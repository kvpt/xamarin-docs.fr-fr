---
title: Création et dessin sur des bitmaps SkiaSharp
description: Découvrez comment créer des bitmaps SkiaSharp, puis dessiner sur ces bitmaps en créant une zone de dessin basée sur ces bitmaps.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3ca546f69dd8c4995747ad352c54e9ba184b2425
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373495"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Création et dessin sur des bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Vous avez vu comment une application peut charger des bitmaps à partir du Web, à partir de ressources d’application et de la bibliothèque de photos de l’utilisateur. Il est également possible de créer des bitmaps dans votre application. L’approche la plus simple implique l’un des constructeurs de [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) :

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

Les `width` `height` paramètres et sont des entiers et spécifient les dimensions en pixels de l’image bitmap. Ce constructeur crée une image bitmap de couleur entière avec quatre octets par pixel : un octet pour chaque composant rouge, vert, bleu et alpha (opacité).

Une fois que vous avez créé une nouvelle image bitmap, vous devez obtenir une information sur la surface de l’image bitmap. Vous procédez généralement de l’une des deux manières suivantes :

- Dessinez sur l’image bitmap à l’aide des `Canvas` méthodes de dessin standard.
- Accédez directement aux bits de pixel.

Cet article illustre la première approche :

![Exemple de dessin](drawing-images/DrawingSample.png "Exemple de dessin")

La deuxième approche est décrite dans l’article [**accès aux pixels de la bitmap SkiaSharp**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Dessin sur la bitmap

Le dessin sur la surface d’une image bitmap est identique au dessin sur un affichage vidéo. Pour dessiner sur un affichage vidéo, vous obtenez un `SKCanvas` objet à partir des `PaintSurface` arguments d’événement. Pour dessiner sur une image bitmap, vous créez un `SKCanvas` objet à l’aide du [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) constructeur :

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Lorsque vous avez terminé de dessiner sur la bitmap, vous pouvez supprimer l' `SKCanvas` objet. Pour cette raison, le `SKCanvas` constructeur est généralement appelé dans une `using` instruction :

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

La bitmap peut ensuite être affichée. À un moment ultérieur, le programme peut créer un nouvel `SKCanvas` objet basé sur ce même bitmap et en dessiner d’autres.

La page **image bitmap Hello** de l’application **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** écrit le texte « Hello, bitmap ! » sur une image bitmap, puis affiche cette bitmap plusieurs fois.

Le constructeur du `HelloBitmapPage` commence par créer un `SKPaint` objet pour afficher du texte. Il détermine les dimensions d’une chaîne de texte et crée une image bitmap avec ces dimensions. Il crée ensuite un `SKCanvas` objet en fonction de cette image bitmap, appelle `Clear` , puis appelle `DrawText` . Il est toujours judicieux d’appeler `Clear` avec une nouvelle bitmap, car une bitmap nouvellement créée peut contenir des données aléatoires.

Le constructeur conclut en créant un `SKCanvasView` objet pour afficher la bitmap :

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

Le `PaintSurface` Gestionnaire restitue la bitmap plusieurs fois dans les lignes et les colonnes de l’affichage. Notez que la `Clear` méthode dans le `PaintSurface` gestionnaire a un argument de `SKColors.Aqua` , qui colore l’arrière-plan de la surface d’affichage :

[![Bonjour, bitmap !](drawing-images/HelloBitmap.png "Bonjour, bitmap !")](drawing-images/HelloBitmap-Large.png#lightbox)

L’apparence de l’arrière-plan cyan révèle que la bitmap est transparente, à l’exception du texte.

## <a name="clearing-and-transparency"></a>Effacement et transparence

L’affichage de la page de la **bitmap Hello** montre que la bitmap créée par le programme est transparente, à l’exception du texte noir. C’est la raison pour laquelle la couleur cyan de la surface d’affichage s’affiche.

La documentation des `Clear` méthodes de les `SKCanvas` décrit avec l’instruction : « remplace tous les pixels dans le clip actuel du canevas ». L’utilisation du mot « remplace » révèle une caractéristique importante de ces méthodes : toutes les méthodes de dessin de `SKCanvas` Ajouter un élément à la surface d’affichage existante. Les `Clear` méthodes _remplacent_ ce qui existe déjà.

`Clear` existe dans deux versions différentes :

- La [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) méthode avec un `SKColor` paramètre remplace les pixels de la surface d’affichage par des pixels de cette couleur.

- La [`Clear`](xref:SkiaSharp.SKCanvas.Clear) méthode sans paramètre remplace les pixels par la [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) couleur, qui est une couleur dans laquelle tous les composants (rouge, vert, bleu et alpha) sont définis sur zéro. Cette couleur est parfois appelée « noir transparent ».

`Clear`L’appel de sans arguments sur une nouvelle bitmap initialise la totalité de la bitmap en toute transparence. Tout ce qui est ensuite dessiné sur la bitmap est généralement opaque ou partiellement opaque.

Voici ce que vous pouvez essayer : dans la page de la **bitmap Hello** , remplacez la `Clear` méthode appliquée à l’par celle-ci `bitmapCanvas` :

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

L’ordre des `SKColor` paramètres du constructeur est rouge, vert, bleu et alpha, où chaque valeur peut être comprise entre 0 et 255. N’oubliez pas qu’une valeur alpha de 0 est transparente, tandis qu’une valeur alpha de 255 est opaque.

La valeur (255, 0, 0, 128) efface les pixels de la bitmap en pixels rouges avec une opacité de 50%. Cela signifie que l’arrière-plan de la bitmap est semi-transparent. L’arrière-plan rouge semi-transparent de l’image bitmap est combiné avec l’arrière-plan cyan de la surface d’affichage pour créer un arrière-plan gris.

Essayez de définir la couleur du texte sur transparent Black en plaçant l’assignation suivante dans l' `SKPaint` initialiseur :

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Vous pensez peut-être que ce texte transparent créera des zones entièrement transparentes de la bitmap dans laquelle vous auriez pu voir l’arrière-plan cyan de la surface d’affichage. Mais ce n’est pas le cas. Le texte est dessiné sur ce qui se trouve déjà sur l’image bitmap. Le texte transparent n’est pas visible du tout.

Aucune `Draw` méthode ne rend jamais une bitmap plus transparente. `Clear`Cela est possible uniquement.

## <a name="bitmap-color-types"></a>Types de couleurs bitmap

Le constructeur le plus simple `SKBitmap` vous permet de spécifier une largeur et une hauteur en pixels entières pour l’image bitmap. Les autres `SKBitmap` constructeurs sont plus complexes. Ces constructeurs requièrent des arguments de deux types d’énumération : [`SKColorType`](xref:SkiaSharp.SKColorType) et [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) . D’autres constructeurs utilisent la [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) structure, qui consolide ces informations.

L' `SKColorType` énumération a 9 membres. Chacun de ces membres décrit un moyen particulier de stocker les pixels de l’image bitmap :

- `Unknown`
- `Alpha8`&mdash;chaque pixel est de 8 bits, ce qui représente une valeur alpha de complètement transparent à entièrement opaque.
- `Rgb565`&mdash;chaque pixel est de 16 bits, 5 bits pour le rouge et le bleu, et 6 pour le vert
- `Argb4444`&mdash;chaque pixel est 16 bits, 4 pour alpha, rouge, vert et bleu.
- `Rgba8888`&mdash;chaque pixel est 32 bits, 8 pour les rouge, vert, bleu et alpha.
- `Bgra8888`&mdash;chaque pixel est 32 bits, 8 pour le bleu, le vert, le rouge et l’alpha.
- `Index8`&mdash;chaque pixel est de 8 bits et représente un index dans un[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;chaque pixel est de 8 bits représentant une ombre grise du noir au blanc
- `RgbaF16`&mdash;chaque pixel est 64 bits, avec rouge, vert, bleu et alpha dans un format à virgule flottante 16 bits.

Les deux formats dans lesquels chaque pixel est de 32 pixels (4 octets) sont souvent appelés des formats _de couleur complète_ . La plupart des autres formats date à partir d’une heure où les vidéos s’affichent elles-mêmes ne sont pas en couleur complète. Les bitmaps de couleur limitée étaient appropriées pour ces affichages et les bitmaps autorisées à occuper moins d’espace en mémoire.

Ces jours-ci, les programmeurs utilisent presque toujours des bitmaps de couleur entière et ne sont pas dérangés avec d’autres formats. L’exception est le `RgbaF16` format, qui permet une résolution de couleur supérieure à celle des formats de couleur complète. Toutefois, ce format est utilisé à des fins spécifiques, telles que l’imagerie médicale, et n’est pas très utile en cas d’utilisation avec des affichages couleur standard.

Cette série d’articles se limite aux formats de `SKBitmap` couleurs utilisés par défaut quand aucun `SKColorType` membre n’est spécifié. Ce format par défaut est basé sur la plateforme sous-jacente. Pour les plateformes prises en charge par Xamarin.Forms , le type de couleur par défaut est :

- `Rgba8888` pour iOS et Android
- `Bgra8888` pour la série UWP

La seule différence est l’ordre des 4 octets dans la mémoire, ce qui devient un problème uniquement lorsque vous accédez directement aux bits de pixel. Cela ne devient pas important tant que vous n’accédez pas à l’article sur l' [**accès aux pixels de la bitmap SkiaSharp**](pixel-bits.md).

L' `SKAlphaType` énumération a quatre membres :

- `Unknown`
- `Opaque`&mdash;la bitmap n’a aucune transparence
- `Premul`&mdash;les composants de couleur sont prémultipliés par le composant alpha.
- `Unpremul`&mdash;les composants de couleur ne sont pas prémultipliés par le composant alpha

Voici un pixel de bitmap rouge de 4 octets avec une transparence de 50%, avec les octets affichés dans l’ordre rouge, vert, bleu, alpha :

0xFF 0x00 0x00 0x80

Lorsqu’une image bitmap contenant des pixels semi-transparents est rendue sur une surface d’affichage, les composants de couleur de chaque pixel de la bitmap doivent être multipliés par la valeur alpha de ce pixel, et les composants de couleur du pixel correspondant de la surface d’affichage doivent être multipliés par 255 moins la valeur alpha. Les deux pixels peuvent ensuite être combinés. La bitmap peut être rendue plus rapide si les composants de couleur des pixels de la bitmap ont déjà été prémulitpliedés par la valeur alpha. Ce même pixel rouge serait stocké comme ceci dans un format pré-multiplié :

0x80 0x00 0x00 0x80

Cette amélioration des performances est la raison pour laquelle `SkiaSharp` les bitmaps par défaut sont créées avec un `Premul` format. Là encore, il devient nécessaire de le connaître uniquement lorsque vous accédez et manipulez des bits de pixel.

## <a name="drawing-on-existing-bitmaps"></a>Dessin sur des bitmaps existantes

Il n’est pas nécessaire de créer une image bitmap à dessiner dessus. Vous pouvez également dessiner sur une image bitmap existante.

La page **singe moustache** utilise son constructeur pour charger l’image **MonkeyFace.png** . Il crée ensuite un `SKCanvas` objet en fonction de cette image bitmap, et utilise `SKPaint` les `SKPath` objets et pour dessiner un moustache sur celui-ci :

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

Le constructeur conclut en créant un `SKCanvasView` dont `PaintSurface` le gestionnaire affiche simplement le résultat :

[![Singe moustache](drawing-images/MonkeyMoustache.png "Singe moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copie et modification des bitmaps

Les méthodes de `SKCanvas` que vous pouvez utiliser pour dessiner sur une bitmap incluent `DrawBitmap` . Cela signifie que vous pouvez dessiner une image bitmap sur une autre, généralement en la modifiant de quelque manière que ce soit.

La façon la plus polyvalente de modifier une bitmap consiste à accéder aux bits de pixel réels, un sujet abordé dans l’article **[accès aux pixels de la bitmap SkiaSharp](pixel-bits.md)**. Toutefois, il existe de nombreuses autres techniques permettant de modifier les bitmaps qui ne nécessitent pas l’accès aux bits de pixel.

La bitmap suivante incluse avec l’application **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** est 360 pixels de largeur et 480 pixels en hauteur :

![Alpiners](drawing-images/MountainClimbers.jpg "Alpiners")

Supposez que vous n’avez pas reçu l’autorisation du singe sur la gauche pour publier cette photographie. Une solution consiste à obscurcir le visage du singe à l’aide d’une technique appelée « _pixelisation_ ». Les pixels de la face sont remplacés par des blocs de couleur, de sorte que vous ne pouvez pas créer les fonctionnalités. Les blocs de couleur sont généralement dérivés de l’image d’origine en calculant la moyenne des couleurs des pixels correspondant à ces blocs. Mais vous n’avez pas besoin d’effectuer cette moyenne vous-même. Cela se produit automatiquement lorsque vous copiez une image bitmap dans une dimension de pixels plus petite.

Le visage du singe gauche occupe approximativement une zone carrée de 72 pixels avec un angle supérieur gauche au point (112, 238). Remplaçons cette zone carrée de 72 pixels par un tableau de 9 par 9 de blocs colorés, chacun d’entre eux étant de 8 x 8 pixels carrés.

La page de pixels de l' **image** se charge dans cette bitmap et crée d’abord une petite bitmap carrée de 9 pixels appelée `faceBitmap` . Il s’agit d’une destination pour copier uniquement le visage du singe. Le rectangle de destination correspond à un carré de 9 pixels, mais le rectangle source est un carré de 72 pixels. Chaque bloc de pixels source 8 par 8 est consolidé en un seul pixel en calculant la moyenne des couleurs.

L’étape suivante consiste à copier l’image bitmap d’origine dans une nouvelle image bitmap de la même taille appelée `pixelizedBitmap` . Le minuscule `faceBitmap` est ensuite copié en plus de celui-ci avec un rectangle de destination carré de 72 pixels, de sorte que chaque pixel de `faceBitmap` est étendu à 8 fois sa taille :

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

[![Image de pixellisation](drawing-images/PixelizeImage.png "Image de pixellisation")](drawing-images/PixelizeImage-Large.png#lightbox)

## <a name="rotating-bitmaps"></a>Rotation des bitmaps

Une autre tâche courante est la rotation des bitmaps. Cela s’avère particulièrement utile lors de la récupération de bitmaps à partir d’une bibliothèque de photos iPhone ou iPad. À moins que l’appareil ne soit maintenu dans une orientation particulière lorsque la photo a été prise, l’image est susceptible d’être renversée ou latéralement.

L’activation d’une image bitmap à l’envers nécessite la création d’une autre bitmap de la même taille que la première, puis la définition d’une transformation pour faire pivoter de 180 degrés lors de la copie du premier vers le second. Dans tous les exemples de cette section, `bitmap` est l' `SKBitmap` objet que vous devez faire pivoter :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

En cas de rotation de 90 degrés, vous devez créer une image bitmap d’une taille différente de celle de l’original en remplaçant la hauteur et la largeur. Par exemple, si la bitmap d’origine est 1200 pixels de large et 800 pixels de haut, le bitmap pivoté est de 800 pixels de large et de 1200 pixels de large. Définissez la translation et la rotation de sorte que l’image bitmap pivote autour de son coin supérieur gauche, puis déplacée dans la vue. (Gardez à l’esprit que `Translate` les `RotateDegrees` méthodes et sont appelées dans l’ordre inverse de la façon dont elles sont appliquées.) Voici le code permettant de tourner de 90 degrés dans le sens des aiguilles d’une montre :

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

Et voici une fonction similaire pour faire pivoter le compteur de 90 degrés dans le sens inverse des aiguilles d’une montre :

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

Ces deux méthodes sont utilisées dans les pages de **Puzzle photo** décrites dans l’article [**découpage de bitmaps SkiaSharp**](cropping.md#cropping-skiasharp-bitmaps).

Un programme qui permet à l’utilisateur de faire pivoter une image bitmap par incréments de 90 degrés n’a besoin d’implémenter qu’une seule fonction pour la rotation de 90 degrés. L’utilisateur peut alors faire pivoter à l’aide d’un incrément de 90 degrés en répétant l’exécution de cette fonction.

Un programme peut également faire pivoter une image bitmap d’une quantité quelconque. Une approche simple consiste à modifier la fonction qui pivote de 180 degrés en remplaçant 180 par une variable généralisée `angle` :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Toutefois, dans le cas général, cette logique rogne les angles de l’image bitmap pivotée. Une meilleure approche consiste à calculer la taille de l’image bitmap pivotée à l’aide de trigonométrique pour inclure ces angles.

Cette trigonométrie est indiquée dans la page **rotation du bitmap** . Le fichier XAML instancie un `SKCanvasView` et un `Slider` qui peut être compris entre 0 et 360 degrés avec un qui `Label` indique la valeur actuelle :

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

Le fichier code-behind charge une ressource bitmap et l’enregistre sous la forme d’un champ statique en lecture seule nommé `originalBitmap` . La bitmap affichée dans le `PaintSurface` gestionnaire est `rotatedBitmap` , qui a initialement la valeur `originalBitmap` :

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

Le `ValueChanged` Gestionnaire du `Slider` effectue les opérations qui créent un nouveau `rotatedBitmap` en fonction de l’angle de rotation. La nouvelle largeur et la hauteur sont basées sur des valeurs absolues de sinus et de cosinus des largeurs et hauteurs d’origine. Les transformations utilisées pour dessiner l’image bitmap d’origine sur l’image bitmap pivotée déplacent le centre de la bitmap d’origine dans l’origine, le font pivoter du nombre de degrés spécifié, puis traduisent ce centre au centre de l’image bitmap pivotée. (Les `Translate` `RotateDegrees` méthodes et sont appelées dans l’ordre inverse par rapport à la façon dont elles sont appliquées.)

Notez l’utilisation de la `Clear` méthode pour rendre l’arrière-plan d' `rotatedBitmap` un rose clair. Il s’agit uniquement d’illustrer la taille de `rotatedBitmap` sur l’affichage :

[![Rotateur bitmap](drawing-images/BitmapRotator.png "Rotateur bitmap")](drawing-images/BitmapRotator-Large.png#lightbox)

Le bitmap pivoté est suffisamment grand pour inclure l’intégralité de la bitmap d’origine, mais pas plus grand.

## <a name="flipping-bitmaps"></a>Retourner des bitmaps

Une autre opération couramment effectuée sur les bitmaps est appelée _retournement_. D’un point de vue conceptuel, la bitmap est pivotée en trois dimensions autour d’un axe vertical ou d’un axe horizontal au centre de l’image bitmap. Le retournement vertical crée une image miroir.

La page du **flipper bitmap** de l’application **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** illustre ces processus. Le fichier XAML contient un `SKCanvasView` et deux boutons pour le basculement vertical et horizontal :

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

Le fichier code-behind implémente ces deux opérations dans les `Clicked` gestionnaires pour les boutons :

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

Le retournement vertical est effectué par une transformation de mise à l’échelle avec un facteur d’échelle horizontale de &ndash; 1. Le centre de mise à l’échelle est le centre vertical de l’image bitmap. Le retournement horizontal est une transformation de mise à l’échelle avec un facteur d’échelle verticale de &ndash; 1.

Comme vous pouvez le voir à partir de la lettre inversée sur la chemise du singe, le retournement n’est pas identique à la rotation. Mais comme le montre la capture d’écran UWP sur la droite, le fait de retourner à la fois horizontalement et verticalement est identique à la rotation de 180 degrés :

[![Flipper bitmap](drawing-images/BitmapFlipper.png "Flipper bitmap")](drawing-images/BitmapFlipper-Large.png#lightbox)

Une autre tâche courante qui peut être gérée à l’aide de techniques similaires consiste à découper une image bitmap en un sous-ensemble rectangulaire. Cela est décrit dans l’article suivant, [**découpage des bitmaps SkiaSharp**](cropping.md).

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)