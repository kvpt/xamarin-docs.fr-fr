---
title: Animation de bitmaps SkiaSharp
description: Découvrez comment effectuer une animation bitmap en affichant séquentiellement une série de bitmaps et en restituant des fichiers GIF animés.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 763f44c26d653aa32429b2aa764989e18e8b8078
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139969"
---
# <a name="animating-skiasharp-bitmaps"></a>Animation de bitmaps SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les applications qui animent les graphiques SkiaSharp appellent généralement `InvalidateSurface` sur `SKCanvasView` une vitesse fixe, souvent toutes les 16 millisecondes. L’invalidation de la surface déclenche un appel au `PaintSurface` Gestionnaire pour redessiner l’affichage. À mesure que les éléments visuels sont redessinés 60 fois par seconde, ils semblent être facilement animés.

Toutefois, si les graphiques sont trop complexes pour être rendus en 16 millisecondes, l’animation peut devenir instable. Le programmeur peut choisir de réduire la fréquence d’actualisation à 30 fois ou 15 fois par seconde, mais parfois même cela ne suffit pas. Parfois, les graphiques sont tellement complexes qu’ils ne peuvent tout simplement pas être rendus en temps réel.

Une solution consiste à préparer l’animation au préalable en affichant les images individuelles de l’animation sur une série de bitmaps. Pour afficher l’animation, il suffit d’afficher ces bitmaps séquentiellement 60 fois par seconde.

Bien entendu, il s’agit potentiellement d’un grand nombre de bitmaps, mais c’est la manière dont les vidéos animées 3D Big-budget sont créées. Les graphiques 3D sont beaucoup trop complexes pour être affichés en temps réel. Un temps de traitement important est nécessaire pour afficher chaque frame. Ce que vous voyez quand vous regardez le film est essentiellement une série de bitmaps.

Vous pouvez effectuer une opération similaire dans SkiaSharp. Cet article présente deux types d’animations d’image bitmap. Le premier exemple est une animation de l’ensemble Mandelbrot :

![Animation de l’exemple](animating-images/AnimatingSample.png "Animation de l’exemple")

Le deuxième exemple montre comment utiliser SkiaSharp pour afficher un fichier GIF animé.

## <a name="bitmap-animation"></a>Animation bitmap

L’ensemble de Mandelbrot est visuellement fascinant, mais computionally long. (Pour une description de l’ensemble de Mandelbrot et des mathématiques utilisés ici, consultez le [chapitre 20 de _création d’Mobile Apps avec Xamarin. Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) à partir de la page 666. La description suivante part du principe que les connaissances en arrière-plan.)

L’exemple d' [**animation Mandelbrot**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima) utilise l’animation bitmap pour simuler un zoom continu d’un point fixe dans l’ensemble de Mandelbrot. Le zoom avant est suivi par un zoom arrière, puis le cycle se répète indéfiniment ou jusqu’à ce que vous terminiez le programme.

Le programme prépare cette animation en créant jusqu’à 50 bitmaps qu’il stocke dans le stockage local de l’application. Chaque bitmap englobe la moitié de la largeur et de la hauteur du plan complexe comme bitmap précédente. (Dans le programme, ces bitmaps sont dites pour représenter des _niveaux de zoom_intégraux.) Les bitmaps sont ensuite affichés dans l’ordre. La mise à l’échelle de chaque bitmap est animée pour fournir une progression fluide d’une image bitmap à une autre.

À l’instar du dernier programme décrit dans le chapitre 20 de la _création d’Mobile Apps avec Xamarin. Forms_, le calcul de l’ensemble de Mandelbrot dans l' **animation Mandelbrot** est une méthode asynchrone avec huit paramètres. Les paramètres incluent un point central complexe, ainsi qu’une largeur et une hauteur du plan complexe entourant ce point central. Les trois paramètres suivants sont la largeur et la hauteur en pixels de l’image bitmap à créer, ainsi qu’un nombre maximal d’itérations pour le calcul récursif. Le `progress` paramètre est utilisé pour afficher la progression de ce calcul. Le `cancelToken` paramètre n’est pas utilisé dans ce programme :

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

La méthode retourne un objet de type `BitmapInfo` qui fournit des informations pour la création d’une image bitmap :

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

Le fichier XAML d' **animation Mandelbrot** comprend deux `Label` vues, un `ProgressBar` et un, ainsi que `Button` les `SKCanvasView` éléments suivants :

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Le fichier code-behind commence par définir trois constantes cruciales et un tableau de bitmaps :

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

À un moment donné, vous souhaiterez probablement remplacer la `COUNT` valeur par 50 pour voir la plage complète de l’animation. Les valeurs supérieures à 50 ne sont pas utiles. Autour d’un niveau de zoom de 48, la résolution des nombres à virgule flottante double précision devient insuffisante pour le calcul du jeu de Mandelbrot. Ce problème est abordé à la page 684 de la _création d’Mobile Apps avec Xamarin. Forms_.

La `center` valeur est très importante. Il s’agit de l’objectif du zoom d’animation. Les trois valeurs du fichier sont celles utilisées dans les trois captures d’écran finales du chapitre 20 de la _création d’Mobile Apps avec Xamarin. Forms_ à la page 684, mais vous pouvez expérimenter le programme dans ce chapitre pour trouver l’une de vos propres valeurs.

L’exemple d' **animation Mandelbrot** stocke ces `COUNT` bitmaps dans le stockage d’applications local. 50 les bitmaps requièrent plus de 20 mégaoctets de stockage sur votre appareil. vous pouvez donc être amené à connaître la quantité de stockage que ces bitmaps occupent et, à un moment donné, vous souhaiterez peut-être les supprimer. C’est l’objectif de ces deux méthodes au bas de la `MainPage` classe :

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

Vous pouvez supprimer les bitmaps dans le stockage local pendant que le programme anime les mêmes bitmaps, car le programme les conserve en mémoire. La prochaine fois que vous exécuterez le programme, il devra recréer les bitmaps.

Les bitmaps stockées dans le stockage d’application local incorporent la `center` valeur dans leurs noms de fichiers. par conséquent, si vous modifiez le `center` paramètre, les bitmaps existantes ne seront pas remplacées dans le stockage et continueront à occuper de l’espace.

Voici les méthodes utilisées `MainPage` par pour construire les noms de fichiers, ainsi qu’une `MakePixel` méthode permettant de définir une valeur en pixels basée sur les composants de couleur :

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

Le `zoomLevel` paramètre est compris `FilePath` entre 0 et la `COUNT` constante moins 1.

Le `MainPage` constructeur appelle la `LoadAndStartAnimation` méthode :

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

La `LoadAndStartAnimation` méthode est chargée d’accéder au stockage local de l’application pour charger les bitmaps qui peuvent avoir été créées lors de l’exécution précédente du programme. Il parcourt les `zoomLevel` valeurs de 0 à `COUNT` . Si le fichier existe, il le charge dans le `bitmaps` tableau. Dans le cas contraire, il doit créer une image bitmap pour les `center` valeurs et spécifiques `zoomLevel` en appelant `Mandelbrot.CalculateAsync` . Cette méthode obtient le nombre d’itérations pour chaque pixel, que cette méthode convertit en couleurs :

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

Notez que le programme stocke ces bitmaps dans le stockage d’applications local plutôt que dans la bibliothèque de photos de l’appareil. La bibliothèque .NET Standard 2,0 permet d’utiliser les `File.OpenRead` méthodes et familières `File.WriteAllBytes` pour cette tâche.

Une fois que toutes les bitmaps ont été créées ou chargées en mémoire, la méthode démarre un `Stopwatch` objet et appelle `Device.StartTimer` . La `OnTimerTick` méthode est appelée toutes les 16 millisecondes.

`OnTimerTick`calcule une `time` valeur en millisecondes comprise entre 0 et 6000 fois, ce qui répartit `COUNT` six secondes pour l’affichage de chaque bitmap. La `progress` valeur utilise la `Math.Sin` valeur pour créer une animation sinusoïdale qui sera plus lente au début du cycle, et plus lente à la fin, car elle inverse le sens.

La valeur est comprise `progress` entre 0 et `COUNT` . Cela signifie que la partie entière de `progress` est un index dans le `bitmaps` tableau, tandis que la partie fractionnaire de `progress` indique un niveau de zoom pour cette bitmap particulière. Ces valeurs sont stockées dans les `bitmapIndex` `bitmapProgress` champs et, et sont affichées par `Label` et `Slider` dans le fichier XAML. Le `SKCanvasView` est invalidé pour mettre à jour l’affichage de la bitmap :

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

Enfin, le `PaintSurface` Gestionnaire du `SKCanvasView` calcule un rectangle de destination pour afficher le bitmap le plus grand possible tout en conservant les proportions. Un rectangle source est basé sur la `bitmapProgress` valeur. La `fraction` valeur calculée ici est comprise entre 0 lorsque `bitmapProgress` est égal à 0 pour afficher la bitmap entière, à 0,25 quand `bitmapProgress` est 1 pour afficher la moitié de la largeur et de la hauteur de la bitmap, en zoomant efficacement :

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

Voici le programme en cours d’exécution :

[![Animation Mandelbrot](animating-images/MandelbrotAnimation.png "Animation Mandelbrot")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Animation GIF

La spécification GIF (Graphics Interchange Format) comprend une fonctionnalité qui permet à un seul fichier GIF de contenir plusieurs images séquentielles d’une scène qui peuvent être affichées successivement, souvent dans une boucle. Ces fichiers sont appelés _GIF animés_. Les navigateurs Web peuvent lire des images GIF animées, et SkiaSharp permet à une application d’extraire les frames d’un fichier GIF animé et de les afficher de façon séquentielle.

L’exemple [SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comprend une ressource GIF animée nommée **Newtons_cradle_animation_book_2.gif** créée par DemonDeLuxe et téléchargée à partir de la page du [berceau de Newton](https://en.wikipedia.org/wiki/Newton%27s_cradle) dans Wikipédia. La page **GIF animée** inclut un fichier XAML qui fournit ces informations et instancie un `SKCanvasView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Le fichier code-behind n’est pas généralisé pour lire un fichier GIF animé. Il ignore certaines des informations disponibles, en particulier un nombre de répétitions, et lit simplement le GIF animé dans une boucle.

L’utilisation de SkisSharp pour extraire les frames d’un fichier GIF animé ne semble pas être documentée n’importe où, donc la description du code qui suit est plus détaillée que d’habitude :

Le décodage du fichier GIF animé se produit dans le constructeur de la page et requiert que l' `Stream` objet référençant la bitmap soit utilisé pour créer un `SKManagedStream` objet, puis un [`SKCodec`](xref:SkiaSharp.SKCodec) objet. La [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount) propriété indique le nombre de frames qui composent l’animation.

Ces frames étant finalement enregistrés sous forme de bitmaps individuelles, le constructeur utilise `FrameCount` pour allouer un tableau de type `SKBitmap` ainsi que deux `int` tableaux pour la durée de chaque frame et (pour faciliter la logique d’animation) les durées accumulées.

La [`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo) propriété de la `SKCodec` classe est un tableau de [`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo) valeurs, une pour chaque frame, mais la seule chose que ce programme effectue à partir de cette structure est le [`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) du frame en millisecondes.

`SKCodec`définit une propriété nommée [`Info`](xref:SkiaSharp.SKCodec.Info) de type [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) , mais cette `SKImageInfo` valeur indique (au moins pour cette image) que le type de couleur est, ce qui `SKColorType.Index8` signifie que chaque pixel est un index dans un type de couleur. Pour éviter de vous soucier des tables de couleurs, le programme utilise les [`Width`](xref:SkiaSharp.SKImageInfo.Width) [`Height`](xref:SkiaSharp.SKImageInfo.Height) informations de et de cette structure pour construire sa propre valeur de couleur entière `ImageInfo` . Chaque `SKBitmap` est créé à partir de ce.

La `GetPixels` méthode de `SKBitmap` retourne un qui `IntPtr` référence les bits de pixel de cette bitmap. Ces bits de pixel n’ont pas encore été définis. Qui `IntPtr` est passé à l’une des [`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) méthodes de `SKCodec` . Cette méthode copie le frame du fichier GIF dans l’espace mémoire référencé par le `IntPtr` . Le [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions) constructeur indique le numéro de frame :

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

En dépit de la `IntPtr` valeur, aucun `unsafe` code n’est nécessaire, car le `IntPtr` n’est jamais converti en valeur de pointeur C#.

Une fois que chaque trame a été extraite, le constructeur totalise les durées de tous les frames, puis initialise un autre tableau avec les durées accumulées.

Le reste du fichier code-behind est dédié à l’animation. La `Device.StartTimer` méthode est utilisée pour démarrer un minuteur, et le `OnTimerTick` rappel utilise un `Stopwatch` objet pour déterminer le temps écoulé en millisecondes. L’itération dans le tableau des durées accumulées est suffisante pour trouver le frame actuel :

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
    ···
    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

Chaque fois que la `currentframe` variable change, le `SKCanvasView` est invalidé et le nouveau frame est affiché :

[![GIF animé](animating-images/AnimatedGif.png "GIF animé")](animating-images/AnimatedGif-Large.png#lightbox)

Bien entendu, vous souhaiterez exécuter le programme vous-même pour voir l’animation.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Animation Mandelbrot (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
