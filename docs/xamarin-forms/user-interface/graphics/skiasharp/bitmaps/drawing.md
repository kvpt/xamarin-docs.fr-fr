---
title: Création et le dessin sur des images bitmap SkiaSharp
description: Découvrez comment créer des bitmaps de SkiaSharp et dessinez sur ces bitmaps en créant un canevas basé sur ces derniers.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 68d6cb1df8557b6055feb81b21ed5513592c71c4
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198142"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Création et le dessin sur des images bitmap SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Vous avez vu comment une application peut charger des bitmaps à partir du Web, à partir des ressources d’application et à partir de la bibliothèque de photos de l’utilisateur. Il est également possible de créer de nouvelles images bitmaps au sein de votre application. L’approche la plus simple implique l’un des constructeurs de [ `SKBitmap` ](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

Le `width` et `height` paramètres sont des entiers et spécifiez les dimensions en pixels de la bitmap. Ce constructeur crée un bitmap de couleur avec quatre octets par pixel : un octet pour le rouge, vert, bleu et les composants alpha (opacité). 

Une fois que vous avez créé une nouvelle image bitmap, vous devez obtenir quelque chose sur la surface de l’image bitmap. Vous le faites généralement de deux manières :

- Dessiner sur l’image bitmap à l’aide de la norme `Canvas` méthodes de dessin.
- Accéder directement les bits de pixel.

Cet article illustre la première approche :

![Exemple de dessin](drawing-images/DrawingSample.png "exemple de dessin")

La deuxième approche est décrite dans l’article [ **l’accès à des Pixels de Bitmap SkiaSharp**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Dessiner sur l’image bitmap

Dessin sur la surface d’une image bitmap est identique à dessiner sur un écran vidéo. Pour dessiner sur un écran vidéo, vous obtenez un `SKCanvas` de l’objet à partir de la `PaintSurface` arguments d’événement. Pour dessiner sur une image bitmap, vous créez un `SKCanvas` à l’aide de l’objet le [ `SKCanvas` ](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) constructeur :

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Lorsque vous avez terminé de dessiner sur l’image bitmap, vous pouvez vous débarrasser du `SKCanvas` objet. Pour cette raison, le `SKCanvas` est généralement appelé un `using` instruction :

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

L’image bitmap peut ensuite être affichée. Ultérieurement, le programme peut créer un nouveau `SKCanvas` objet selon que même bitmap et dessiner dessus davantage.

Le **Hello Bitmap** page dans le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application écrit le texte « Hello, Bitmap ! » sur une image bitmap et affiche alors cette bitmap plusieurs fois.  

Le constructeur de la `HelloBitmapPage` commence par créer un `SKPaint` objet pour afficher du texte. Il détermine les dimensions d’une chaîne de texte et crée une image bitmap avec ces dimensions. Il crée ensuite un `SKCanvas` objet basé sur cette bitmap, appels `Clear`, puis appelle `DrawText`. Il est toujours judicieux d’appeler `Clear` avec une nouvelle image bitmap, car une image bitmap nouvellement créée peut contenir des données aléatoires.

Le constructeur conclut en créant un `SKCanvasView` objet pour afficher l’image bitmap :

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

Le `PaintSurface` Gestionnaire restitue l’image bitmap plusieurs fois en lignes et colonnes de l’affichage. Notez que le `Clear` méthode dans le `PaintSurface` gestionnaire a un argument de `SKColors.Aqua`, quelles couleurs d’arrière-plan de la surface d’affichage :

[![Hello, Bitmap ! ](drawing-images/HelloBitmap.png "Hello, Bitmap !")](drawing-images/HelloBitmap-Large.png#lightbox)

L’apparence de l’arrière-plan cyan révèle que la bitmap est transparente à l’exception du texte.

## <a name="clearing-and-transparency"></a>Compensation et transparence

L’affichage de la **Hello Bitmap** page montre que le programme créé est transparent à l’exception du texte noir. C’est pourquoi la couleur cyan de la surface d’affichage transparaît.

La documentation des `Clear` méthodes de `SKCanvas` les décrit avec l’instruction: «Remplace tous les pixels dans le clip actuel du canevas». L’utilisation du mot «remplace» révèle une caractéristique importante de ces méthodes: Toutes les méthodes de dessin `SKCanvas` de ajoutent des éléments à la surface d’affichage existante. Le `Clear` méthodes _remplacer_ ce qui existe déjà.

`Clear` Il existe dans deux versions différentes : 

- Le [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) méthode avec un `SKColor` paramètre remplace les pixels de la surface d’affichage avec pixels de cette couleur.

- Le [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear) méthode sans paramètres remplace les pixels avec la [ `SKColors.Empty` ](xref:SkiaSharp.SKColors.Empty) couleur, qui est une couleur dans lequel tous les composants (rouge, vert, bleu et alpha) sont définies à zéro. Cette couleur est parfois appelée « noir transparent ».

Appel de `Clear` sans arguments sur une nouvelle image bitmap initialise l’image bitmap entière pour être entièrement transparent. Quoi que ce soit par la suite dessinée sur la bitmap sera généralement opaques ou partiellement opaques.

Voici ce que vous pouvez essayer: Dans la page de l' **image bitmap Hello** , remplacez la `Clear` `bitmapCanvas` méthode appliquée au par celui-ci:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

L’ordre de la `SKColor` paramètres du constructeur est rouge, vert, bleu et alpha, où chaque valeur peut être comprise entre 0 et 255. N’oubliez pas qu’une valeur alpha de 0 est transparente, tandis qu’une valeur alpha de 255 est opaque.

La valeur (255, 0, 0, 128) efface les pixels du bitmap sur rouge pixels avec une opacité de 50 %. Cela signifie que l’arrière-plan de la bitmap est semi-transparent. Arrière-plan rouge semi transparente de l’image bitmap associe l’arrière-plan cyan de la surface d’affichage pour créer un fond gris.

Essayez de définir la couleur du texte en noir transparent en plaçant l’assignation suivante dans le `SKPaint` initialiseur :

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Vous pourriez penser que ce texte transparent créerait des zones entièrement transparentes de l’image bitmap par le biais duquel vous verriez l’arrière-plan cyan de la surface d’affichage. Mais qui sont est pas le cas. Le texte est dessiné en haut de ce qui est déjà sur l’image bitmap. Le texte transparent seront pas visible de tout.

Ne `Draw` méthode rend jamais une bitmap plus transparent. Uniquement `Clear` pour ce faire.

## <a name="bitmap-color-types"></a>Types de couleurs de bitmap

La plus simple `SKBitmap` constructeur vous permet de spécifier un entier pixel largeur et hauteur de la bitmap. Autres `SKBitmap` constructeurs sont plus complexes. Ces constructeurs nécessitent des arguments de deux types d’énumération : [ `SKColorType` ](xref:SkiaSharp.SKColorType) et [ `SKAlphaType` ](xref:SkiaSharp.SKAlphaType). Autres constructeurs utilisent la [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo) structure qui consolide ces données.

Le `SKColorType` énumération a 9 membres. Chacun de ces membres décrit une façon particulière de stocker les pixels du bitmap :

- `Unknown`
- `Alpha8` &mdash; chaque pixel est de 8 bits, représentant une valeur alpha allant de complètement transparent à complètement opaque
- `Rgb565` &mdash; chaque pixel est 16 bits, 5 bits pour le rouge et bleu et 6 pour le vert
- `Argb4444` &mdash; chaque pixel est 16 bits, 4 pour alpha, rouge, vert et bleu
- `Rgba8888` &mdash; chaque pixel est 32 bits, 8 pour le rouge, vert, bleu et alpha
- `Bgra8888` &mdash; chaque pixel est 32 bits, 8 pour bleu, vert, rouge et alpha
- `Index8` &mdash; chaque pixel est de 8 bits et représente un index dans un [`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8` &mdash; chaque pixel est de 8 bits représentant une nuance de gris du noir sur blanc
- `RgbaF16` &mdash; chaque pixel est 64 bits, avec rouge, vert, bleu et alpha dans un format à virgule flottante 16 bits

Les deux formats où chaque pixel est 32 pixels (4 octets) sont souvent appelés _en couleurs_ formats. La plupart de la date des autres formats à partir d’une heure lorsque vidéo affiche eux-mêmes n’étaient pas capables de couleurs. Les images bitmap de couleur limité ont été adaptées à ces écrans et autorisés bitmaps occuper moins d’espace en mémoire. 

De nos jours, les programmeurs presque toujours utilisent des images bitmap en couleurs et nul besoin avec d’autres formats. L’exception est le `RgbaF16` format, ce qui permet la résolution de couleurs supérieure que même les formats de couleur. Toutefois, ce format est utilisé à des usages spécialisés, tels qu’imagerie médicale et ne rend pas beaucoup de sens lorsqu’il est utilisé avec les écrans en couleurs standard.

Cette série d’articles lui-même à limitera le `SKBitmap` utilisés par défaut quand aucun des formats de couleur `SKColorType` membre est spécifié. Ce format par défaut est basé sur la plateforme sous-jacente. Pour les plateformes prises en charge par Xamarin.Forms, le type de couleur par défaut est :

- `Rgba8888` pour iOS et Android
- `Bgra8888` pour la plateforme Windows universelle

La seule différence est l’ordre de 4 octets dans la mémoire, et uniquement, cela devient un problème lorsque vous accédez directement aux bits de pixel. Cela ne deviennent importante jusqu'à ce que vous arriviez à l’article [ **l’accès à des Pixels de Bitmap SkiaSharp**](pixel-bits.md).

Le `SKAlphaType` énumération a quatre membres :

- `Unknown`
- `Opaque` &mdash; la bitmap ne possède pas de transparence
- `Premul` &mdash; composants de couleur sont préalablement multipliés par le composant alpha
- `Unpremul` &mdash; composants de couleur ne sont pas préalablement multipliés par le composant alpha

Voici un pixel de bitmap de rouge de 4 octets avec une transparence de 50 % avec les octets indiqué rouge de l’ordre, vert, bleu, alpha :

0xFF 0x00 0x00 0x80

Lorsqu’une image bitmap qui contient des pixels semi-transparents est rendue sur une surface d’affichage, les composants de couleur de chaque pixel de bitmap doivent être multipliés par la valeur alpha de ce pixel, et les composants de couleur du pixel correspondant de la surface d’affichage doivent être multipliés. par 255 moins la valeur alpha. Les deux pixels peuvent être combinés. L’image bitmap peut être rendue plus rapidement si les composants de couleur dans les pixels du bitmap ont déjà été pre-multipliée par la valeur alpha. Ce même pixel rouge est stocké comme suit dans un format prémultiplié :

0x80 0x00 0x00 0x80

Cette amélioration des performances est la raison pour laquelle `SkiaSharp` bitmaps par défaut sont créés avec un `Premul` format. Mais là encore, il devient nécessaire de savoir ce que lorsque vous y accéder et manipuler des bits de pixel.

## <a name="drawing-on-existing-bitmaps"></a>Dessin sur bitmaps existants

Il n’est pas nécessaire de créer une nouvelle image bitmap pour dessiner dessus. Vous pouvez également dessiner sur une bitmap existante. 

Le **Monkey Moustache** page utilise son constructeur pour charger le **MonkeyFace.png** image. Il crée ensuite un `SKCanvas` objet basé sur cette bitmap et utilise `SKPaint` et `SKPath` objets pour dessiner une moustache dessus :

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Le constructeur conclut en créant un `SKCanvasView` dont `PaintSurface` gestionnaire affiche simplement le résultat :

[![Certain nombre de manipulations Moustache](drawing-images/MonkeyMoustache.png "Moustache certain nombre de manipulations")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copie et la modification des bitmaps

Les méthodes de `SKCanvas` inclure de bitmap que vous pouvez utiliser pour dessiner sur `DrawBitmap`. Cela signifie que vous pouvez dessiner une bitmap sur un autre, généralement de la modification d’une certaine façon.

La façon la plus polyvalente pour modifier une image bitmap est via l’accès aux bits de pixel, un sujet traité dans l’article  **[pixels de bitmap de l’accès à un SkiaSharp](pixel-bits.md)** . Mais il existe plusieurs autres techniques pour modifier les bitmaps qui ne nécessitent pas l’accès aux bits de pixel.

La bitmap suivante incluse avec le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application est 360 pixels de large et 480 pixels en hauteur :

![Mountain Climbers](drawing-images/MountainClimbers.jpg "Mountain Climbers")

Supposons que vous n’avez pas reçu l’autorisation à partir de la monkey sur la gauche pour publier cette photo. Une solution consiste à masquer la face de le monkey à l’aide d’une technique appelée _pixelization_. Les pixels de la face sont remplacées par les blocs de couleur afin que vous ne pouvez pas apporter les fonctionnalités. Les blocs de couleur sont généralement dérivées de l’image d’origine en faisant la moyenne les couleurs des pixels correspondant à ces blocs. Mais vous n’avez pas besoin effectuer cette moyenne vous-même. Cela se produit automatiquement lorsque vous copiez une image bitmap dans une dimension de pixel plus petits. 

Les visages de la gauche monkey occupent environ une zone carrée 72 pixels avec un angle supérieur gauche au niveau du point (112, 238). Nous allons remplacer cette zone carrée 72 pixels avec un tableau 9 x 9 de briques, chacun d’eux est 8 par 8 pixels carrés.

Le **Image Pixelize** page se charge dans cette bitmap et crée d’abord une minuscule 9 pixels carrée image bitmap appelée `faceBitmap`. Il s’agit d’une destination pour la copie simplement les visages de la monkey. Le rectangle de destination est simplement 9-pixels carrés, mais le rectangle source est 72-pixels carrés. Chaque bloc de 8 par 8 de pixels sources est consolidées jusqu'à un pixel en faisant la moyenne les couleurs.

L’étape suivante consiste à copier l’image bitmap d’origine dans un nouveau bitmap de la même taille appelé `pixelizedBitmap`. La petite `faceBitmap` est ensuite copié en cela avec un rectangle de destination carré 72 pixels afin que chaque pixel de `faceBitmap` est développée à 8 fois sa taille :

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap, 
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Le constructeur conclut en créant un `SKCanvasView` pour afficher le résultat :

[![Image de pixelize](drawing-images/PixelizeImage.png "Pixelize d’Image")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>La rotation des bitmaps

Une autre tâche courante fait tourner les bitmaps. Cela est particulièrement utile lors de la récupération des bitmaps à partir d’une bibliothèque de photos iPhone ou iPad. À moins que l’appareil a été maintenu dans une orientation particulière lorsque la photo a été effectuée, l’image est susceptible d’être envers ou sur le côté.

Activation d’une image bitmap envers nécessite la création d’une autre image bitmap la même taille que le premier, puis en définissant une transformation pour faire pivoter de 180 degrés lors de la copie de la première à la seconde. Dans tous les exemples dans cette section, `bitmap` est le `SKBitmap` objet dont vous avez besoin pour effectuer la rotation :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Lors de la rotation de 90 degrés, vous devez créer une image bitmap qui a une taille différente que celle d’origine en échangeant la hauteur et largeur. Par exemple, si la bitmap d’origine est 1200 pixels de large et 800 pixels de haut, la bitmap pivoté est 800 pixels de large et 1200 pixels de large. Définir translation et rotation afin que la bitmap est la rotation autour de son coin supérieur gauche et alors déplacée dans la vue. (N’oubliez pas que le `Translate` et `RotateDegrees` méthodes sont appelées dans l’ordre inverse de la manière qu’elles sont appliquées.) Voici le code pour faire pivoter de 90 degrés dans le sens horaire :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```                        

Et Voici une fonction similaire pour la rotation de 90 degrés vers la gauche :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Ces deux méthodes sont utilisées dans le **Photo Puzzle** pages décrit dans l’article [ **rognage de Bitmaps de SkiaSharp**](cropping.md#tile-division).

Un programme qui permet à l’utilisateur faire pivoter une image bitmap en incréments de 90 degrés doit uniquement implémenter une fonction pour faire pivoter de 90 degrés. L’utilisateur peut ensuite faire pivoter dans n’importe quel incrément de 90 degrés par l’exécution répétée de cette fonction.

Un programme peut également faire pivoter une image bitmap par n’importe quel volume. Une approche simple consiste à modifier la fonction qui fait pivoter de 180 degrés en remplaçant 180 par généralisée `angle` variable :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Toutefois, en règle générale, cette logique sera rogner les angles de l’image bitmap pivotée. Une meilleure approche consiste à calculer la taille de la bitmap pivotée à l’aide de trigonométrie à inclure ces angles. 

Cette trigonométrie est indiqué dans le **Bitmap Rotator** page. Le fichier XAML instancie un `SKCanvasView` et un `Slider` qui peuvent être comprises entre 0 et 360 degrés avec un `Label` indiquant la valeur actuelle :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Le fichier code-behind charge une ressource bitmap et l’enregistre sous la forme d’un champ en lecture seule statique nommé `originalBitmap`. La bitmap affichée dans le `PaintSurface` gestionnaire est `rotatedBitmap`, qui est initialement défini sur `originalBitmap`:

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap = 
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

Le `ValueChanged` Gestionnaire de la `Slider` effectue les opérations qui créent un nouveau `rotatedBitmap` selon l’angle de rotation. La nouvelle largeur et la hauteur sont basées sur absolute values de sinus et le cosinus de la largeur d’origine et la hauteur. Les transformations utilisées pour dessiner la bitmap d’origine sur l’image bitmap pivotée déplacement le centre de bitmap d’origine à l’origine, la faire pivoter le nombre de degrés spécifié puis ensuite traduire ce centre vers le centre de l’image bitmap pivotée. (Le `Translate` et `RotateDegrees` méthodes sont appelées dans l’ordre inverse à la manière dont ils sont appliqués.)

Remarquez l’utilisation de la `Clear` méthode pour rendre l’arrière-plan de `rotatedBitmap` un rose clair. Il s’agit uniquement pour illustrer la taille de `rotatedBitmap` sur l’affichage :

[![Bitmap Rotator](drawing-images/BitmapRotator.png "Bitmap Rotator")](drawing-images/BitmapRotator-Large.png#lightbox)

La bitmap pivotée est suffisamment grande pour inclure la bitmap entière d’origine, mais pas plus important.

## <a name="flipping-bitmaps"></a>Retournement de bitmaps

Une autre opération communément exécutée sur des images bitmap est appelée _retournement_. Conceptuellement, la bitmap est pivotée en trois dimensions autour d’un axe vertical ou l’axe horizontal via le centre de l’image bitmap. Retournement vertical crée une image miroir.

Le **le Flipper Bitmap** page dans le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application montre ces processus. Le fichier XAML contient un `SKCanvasView` et deux boutons de retournement verticalement et horizontalement :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        
        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

Le fichier code-behind implémente ces deux opérations dans le `Clicked` gestionnaires pour les boutons : 

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

Le retournement vertical est accompli par une transformation de mise à l’échelle avec un facteur d’échelle horizontale de &ndash;1. Le centre de mise à l’échelle est au centre vertical de l’image bitmap. Le retournement horizontal est une transformation de mise à l’échelle avec un facteur d’échelle verticale de &ndash;1. 

Comme vous pouvez le voir à partir de la mise en inversé de chemise de la monkey, retournement n’est pas identique à la rotation. Mais, comme illustré dans la capture d’écran UWP sur la droite, retourner à la fois horizontalement et verticalement est identique à la rotation de 180 degrés :

[![Bitmap le Flipper](drawing-images/BitmapFlipper.png "Bitmap le Flipper")](drawing-images/BitmapFlipper-Large.png#lightbox)

Une autre tâche courante qui peut être gérée à l’aide des techniques similaires est rogner une image bitmap à un sous-ensemble rectangulaire. Cette opération est décrite dans l’article suivant [ **rognage de Bitmaps de SkiaSharp**](cropping.md).

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
