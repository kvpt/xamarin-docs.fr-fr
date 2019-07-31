---
title: Animer des bitmaps de SkiaSharp
description: Découvrez comment effectuer des animation d’image bitmap en séquentiellement affichant une série de bitmaps, fichiers GIF animés de rendu.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 69f77ef7959a53fa46210d7e6e68b9666692423b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653095"
---
# <a name="animating-skiasharp-bitmaps"></a>Animer des bitmaps de SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les applications qui animent SkiaSharp graphics généralement appeler `InvalidateSurface` sur la `SKCanvasView` à un taux fixe, la fréquence à laquelle toutes les 16 millisecondes. Invalidation de la surface déclenche un appel à la `PaintSurface` gestionnaire pour redessiner l’affichage. Comme les éléments visuels sont redessinées 60 fois par seconde, ils semblent être animées sans heurts.

Toutefois, si les graphiques sont trop complexes pour être restitué dans 16 millisecondes, l’animation peut devenir instable. Le programmeur peut choisir de réduire la fréquence d’actualisation à 30 fois ou 15 fois par seconde, mais parfois même qui n’est pas suffisant. Parfois, les graphiques sont si complexes qu’ils simplement ne peut pas être restitués en temps réel.

Une solution consiste à préparer pour l’animation au préalable par les trames individuelles de l’animation sur une série de bitmaps de rendu. Pour afficher l’animation, il est uniquement nécessaire afficher ces bitmaps séquentiellement 60 fois par seconde.

Bien sûr, qui est potentiellement un grand nombre d’images bitmap, mais qui est comment gros budget 3D films animés sont effectuées. Les graphiques 3D sont beaucoup trop complexes pour être rendu en temps réel. Beaucoup de temps de traitement est nécessaire pour afficher chaque frame. Ce que vous voyez lorsque vous regardez la vidéo est essentiellement une série de bitmaps.

Vous pouvez faire quelque chose de similaire dans SkiaSharp. Cet article montre les deux types d’animation d’image bitmap. Le premier exemple est une animation de l’ensemble de Mandelbrot :

![Animation exemple](animating-images/AnimatingSample.png "animation exemple")

Le deuxième exemple montre comment utiliser SkiaSharp pour restituer une image GIF animée.

## <a name="bitmap-animation"></a>Animation d’image bitmap

L’ensemble de Mandelbrot est visuellement fascinant mais computionally longue. (Pour une discussion sur l’ensemble de Mandelbrot et les calculs mathématiques utilisée ici, consultez [chapitre 20 de _création d’applications mobiles avec Xamarin.Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) de début de page 666. La description suivante suppose que ces connaissances en arrière-plan.)

Le [ **Mandelbrot Animation** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima) exemple utilise l’animation d’image bitmap pour simuler un zoom continu d’un point fixe dans l’ensemble de Mandelbrot. Un zoom avant est suivi d’un zoom arrière, et ensuite le cycle se répète indéfiniment ou jusqu'à ce que vous mettre fin au programme.

Le programme prépare pour cette animation en créant jusqu'à 50 bitmaps qu’elle stocke dans le stockage local des applications. Chaque bitmap englobe la moitié de la largeur et hauteur du plan complexe en tant que l’image bitmap précédente. (Dans le programme, ces bitmaps sont dits pour représenter le type intégral _niveaux de zoom_.) Les bitmaps sont ensuite affichés dans la séquence. La mise à l’échelle de chaque image bitmap est animée pour fournir une progression smooth à partir d’une bitmap vers un autre.

Comme le programme final décrit au chapitre 20 de _création d’applications mobiles avec Xamarin.Forms_, le calcul de l’ensemble de Mandelbrot dans **Mandelbrot Animation** est une méthode asynchrone avec huit paramètres. Les paramètres incluent un point central complexes et la largeur et la hauteur du plan complexe qui entourent ce point central. Les trois paramètres sont la largeur en pixels et hauteur de la bitmap doit être créé et un nombre maximal d’itérations pour le calcul récursive. Le `progress` paramètre est utilisé pour afficher la progression de ce calcul. Le `cancelToken` paramètre n’est pas utilisé dans ce programme :

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

Le **Mandelbrot Animation** les fichiers XAML inclut deux `Label` vues, un `ProgressBar`et un `Button` ainsi que le `SKCanvasView`:

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

Le fichier code-behind commence par définir les trois constantes cruciales et un tableau de bitmaps :

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

À un moment donné, vous souhaiterez probablement modifier le `COUNT` valeur 50 à voir la gamme complète de l’animation. Les valeurs supérieures à 50 ne sont pas utiles. Autour d’un niveau de zoom de 48 ou ainsi, la résolution de nombres à virgule flottante double précision devient insuffisante pour le calcul de l’ensemble de Mandelbrot. Ce problème est abordé dans la page 684 de _création d’applications mobiles avec Xamarin.Forms_.

Le `center` est très importante. Il s’agit de l’objectif de zoom de l’animation. Les trois valeurs dans le fichier sont ceux utilisés dans les trois captures d’écran finales dans le chapitre 20 de _création d’applications mobiles avec Xamarin.Forms_ sur page 684, mais vous pouvez expérimenter avec le programme dans ce chapitre afin de proposer une de vos propres valeurs.

Le **Mandelbrot Animation** exemple stocke ces `COUNT` bitmaps dans le stockage de l’application locale. Cinquante bitmaps nécessitent plus de 20 mégaoctets de stockage sur votre appareil, vous pouvez donc connaître ces bitmaps occupent la quantité de stockage, et à un moment donné vous pouvez souhaiter supprimer toutes les. C’est l’objectif de ces deux méthodes au bas de la `MainPage` classe :

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

Vous pouvez supprimer les bitmaps dans le stockage local tandis que le programme est animée ces bitmaps de mêmes, car le programme les conserve en mémoire. Mais la prochaine fois que vous exécutez le programme, il doit recréer les bitmaps.

Incorporer les bitmaps stockés dans le stockage de l’application locale le `center` valeur dans leurs noms de fichiers, donc si vous modifiez le `center` définition, les bitmaps existants ne seront pas remplacées dans le stockage et continue d’occuper l’espace.

Voici les méthodes qui `MainPage` utilise pour construire les noms de fichiers, ainsi qu’un `MakePixel` méthode de définition d’une valeur de pixel basée sur les composants de couleur :

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

Le `zoomLevel` paramètre `FilePath` est comprise entre 0 et le `COUNT` constante moins 1.

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

Le `LoadAndStartAnimation` méthode est chargée de l’accès au stockage local d’application pour charger toutes les images bitmap qui peuvent avoir été créés lorsque le programme a été exécuté précédemment. Il effectue une itération sur `zoomLevel` valeurs allant de 0 à `COUNT`. Si le fichier existe, il charge dans le `bitmaps` tableau. Sinon, il doit créer une image bitmap pour le particulier `center` et `zoomLevel` valeurs en appelant `Mandelbrot.CalculateAsync`. Cette méthode obtient le nombre d’itérations pour chaque pixel, cette méthode convertit en couleurs :

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

Notez que le programme stocke ces bitmaps dans le stockage de l’application locale, plutôt que dans la bibliothèque de photos de l’appareil. La bibliothèque .NET Standard 2.0 permet à l’aide de la version classique `File.OpenRead` et `File.WriteAllBytes` méthodes pour cette tâche.

Une fois toutes les bitmaps ont été créés ou chargées en mémoire, la méthode démarre une `Stopwatch` objet et appelle `Device.StartTimer`. Le `OnTimerTick` méthode est appelée toutes les 16 millisecondes.

`OnTimerTick` calcule un `time` valeur en millisecondes, comprise entre 0 et 6000 fois `COUNT`, qui répartit les six secondes pour l’affichage de chaque image bitmap. Le `progress` valeur utilise le `Math.Sin` valeur pour créer une animation sinusoïdale sera plus lente au début du cycle, et plus lent à la fin, tel qu’il inverse la direction.

Le `progress` valeur comprise entre 0 et `COUNT`. Cela signifie que la partie entière de `progress` est un index dans le `bitmaps` tableau, lors de la partie fractionnaire des `progress` indique un niveau de zoom de cette bitmap particulière. Ces valeurs sont stockées dans le `bitmapIndex` et `bitmapProgress` champs et sont affichés par le `Label` et `Slider` dans le fichier XAML. Le `SKCanvasView` est invalidé pour mettre à jour l’affichage de l’image bitmap :

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

Enfin, le `PaintSurface` Gestionnaire de la `SKCanvasView` calcule un rectangle de destination pour afficher la bitmap aussi grande que possible tout en conservant les proportions. Un rectangle source est basé sur la `bitmapProgress` valeur. Le `fraction` valeur calculée ici est comprise entre 0 lorsque `bitmapProgress` a la valeur 0 pour afficher l’image bitmap entière, à 0,25 when `bitmapProgress` est 1 pour afficher la moitié de la largeur et hauteur de l’image bitmap, en fait un zoom avant :

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

[![Animation de Mandelbrot](animating-images/MandelbrotAnimation.png "Mandelbrot Animation")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Animation d’image GIF

La spécification de Format GIF (Graphics Interchange) inclut une fonctionnalité qui permet à un seul fichier GIF contenir plusieurs images séquentielles d’une scène qui peut être affiché à la suite, souvent dans une boucle. Ces fichiers sont appelés _animés_. Navigateurs Web peuvent lire des fichiers GIF animés et SkiaSharp permet à une application pour extraire les images à partir d’un fichier GIF animé et de les afficher de manière séquentielle.

Le [SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple inclut une ressource d’image GIF animée appelée **Newtons_cradle_animation_book_2.gif** créé par DemonDeLuxe et téléchargé à partir du [station d’accueil de Newton ](https://en.wikipedia.org/wiki/Newton%27s_cradle) page Wikipédia. Le **GIF animé** page inclut un fichier XAML qui fournit cette information et instancie un `SKCanvasView`:

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

Le fichier code-behind n’est pas généralisé pour lire un fichier GIF animé. Il ignore certaines informations qui sont disponibles, en particulier un nombre de répétitions et simplement joué par l’image GIF animée dans une boucle.

L’utilisation de SkisSharp pour extraire les images d’un fichier GIF animé ne semble pas être documenté n’importe où, par conséquent, la description du code qui suit est plus détaillée que d’habitude :

Le décodage du fichier GIF animé se produit dans le constructeur de la page et exige que le `Stream` objet faisant référence à l’image bitmap servir à créer un `SKManagedStream` objet, puis un [ `SKCodec` ](xref:SkiaSharp.SKCodec) objet. Le [ `FrameCount` ](xref:SkiaSharp.SKCodec.FrameCount) propriété indique le nombre d’images qui composent l’animation.

Ces images sont finalement enregistrés comme les bitmaps individuels, afin du constructeur utilise `FrameCount` pour allouer un tableau de type `SKBitmap` ainsi que deux `int` tableaux l’accumulées pendant la durée de chaque image et (pour faciliter la logique d’animation) durées.

Le [ `FrameInfo` ](xref:SkiaSharp.SKCodec.FrameInfo) propriété du `SKCodec` classe est un tableau de [ `SKCodecFrameInfo` ](xref:SkiaSharp.SKCodecFrameInfo) valeurs, un pour chaque trame, mais la seule chose que ce programme extrait à partir de cette structure est la [ `Duration` ](xref:SkiaSharp.SKCodecFrameInfo.Duration) de l’image en millisecondes.

`SKCodec` définit une propriété nommée [ `Info` ](xref:SkiaSharp.SKCodec.Info) de type [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo), mais que `SKImageInfo` indique de valeur (au moins pour cette image) que le type de couleur est `SKColorType.Index8`, ce qui signifie que chaque pixel est un index dans un type de couleur. Pour éviter de déranger avec des tables de couleurs, le programme utilise le [ `Width` ](xref:SkiaSharp.SKImageInfo.Width) et [ `Height` ](xref:SkiaSharp.SKImageInfo.Height) plus d’informations à partir de cette structure pour la construction est propre en couleurs `ImageInfo` valeur. Chaque `SKBitmap` est créé à partir de là.

Le `GetPixels` méthode de `SKBitmap` retourne un `IntPtr` référençant les bits de pixel de bitmap. Ces bits de pixel n’ont pas encore été définies. Que `IntPtr` est passée à une de le [ `GetPixels` ](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) méthodes de `SKCodec`. Cette méthode copie le frame à partir du fichier GIF dans l’espace de mémoire référencé par le `IntPtr`. Le [ `SKCodecOptions` ](xref:SkiaSharp.SKCodecOptions) constructeur indique le nombre d’images :

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

Malgré la `IntPtr` valeur, non `unsafe` code est nécessaire, car le `IntPtr` n’est jamais convertie en une valeur de pointeur C#.

Une fois que chaque image a été extrait, le constructeur les durées de tous les cadres de totaux et initialise ensuite un autre tableau avec les durées cumulées.

Le reste du fichier code-behind est dédié à l’animation. Le `Device.StartTimer` méthode est utilisée pour démarrer un minuteur qui va et le `OnTimerTick` rappel utilise un `Stopwatch` objet afin de déterminer le temps écoulé en millisecondes. Une boucle dans le tableau de durées cumulée est suffisante pour trouver la trame actuelle :

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

Chaque fois que le `currentframe` les modifications de variable, le `SKCanvasView` est invalidé et le nouveau frame est affiché :

[![GIF animé](animating-images/AnimatedGif.png "image GIF animée")](animating-images/AnimatedGif-Large.png#lightbox)

Bien sûr, vous souhaitez exécuter le programme vous-même pour voir l’animation.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Animation de Mandelbrot (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
