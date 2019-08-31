---
title: Mosaïque de bitmap SkiaSharp
description: Vignette d’une zone à l’aide de bitmaps répétées horizontalement et verticalement.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: f019b6e031774d7bcdf593015394d0c73c96949b
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198646"
---
# <a name="skiasharp-bitmap-tiling"></a>Mosaïque de bitmap SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

Comme vous l’avez vu dans les deux précédents articles, le [ `SKShader` ](xref:SkiaSharp.SKShader) classe peut créer des dégradés linéaires ou circulaires. Cet article se concentre sur la `SKShader` objet qui utilise une image bitmap à la vignette d’une zone. L’image bitmap peut être répété horizontalement et verticalement, soit dans son orientation d’origine ou vous pouvez également cliquer retournée horizontalement et verticalement. La symétrie permet d’éviter les discontinuités entre les vignettes :

![Exemple de mosaïque bitmap](bitmap-tiling-images/BitmapTilingSample.png "exemple de disposition en mosaïque de Bitmap")

Statiques [ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) méthode qui crée ce nuanceur a un `SKBitmap` paramètre et deux membres de la [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) énumération :

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Les deux paramètres indiquent les modes utilisés pour la disposition en mosaïque horizontale et verticale en mosaïque. Il s’agit du même `SKShaderTileMode` énumération est également utilisée avec les méthodes de dégradé.

Un [ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge inclut un `SKMatrix` argument pour effectuer une transformation sur les images en mosaïque :

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Cet article contient plusieurs exemples d’utilisation de cette matrice de transformation des images bitmap en mosaïque.

## <a name="exploring-the-tile-modes"></a>Explorer les modes de mosaïque

Le premier programme dans le **Bitmap mosaïque** section de la **nuanceurs et autres effets** page de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple montre les effets des deux `SKShaderTileMode` arguments. Le **Modes de retournement de vignette de Bitmap** XAML fichier instancie un `SKCanvasView` et deux `Picker` des affichages qui vous permet de sélectionner un `SKShaderTilerMode` valeur pour la mosaïque horizontale et verticale. Notez qu’un tableau de la `SKShaderTileMode` membres est défini dans la `Resources` section :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

Le constructeur du fichier code-behind se charge dans la ressource bitmap qui montre un monkey assis. Il rogne tout d’abord l’image en utilisant le [ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) méthode de `SKBitmap` afin que la tête et les pieds sont touchent les bords de l’image bitmap. Le constructeur utilise ensuite le [ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) méthode pour créer une autre image bitmap de la moitié. Ces modifications facilitent la bitmap est un peu plus adapté à la disposition en mosaïque :

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Le `PaintSurface` gestionnaire obtient le `SKShaderTileMode` paramètres provenant des deux `Picker` des vues et crée un `SKShader` objet basé sur l’image bitmap et ces deux valeurs. Ce nuanceur est utilisé pour remplir la zone de dessin :

[![Bitmap des Modes de retournement de vignette](bitmap-tiling-images/BitmapTileFlipModes.png "Bitmap des Modes de retournement de vignette")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

L’écran iOS à gauche montre l’effet des valeurs par défaut de `SKShaderTileMode.Clamp`. L’image bitmap se trouve dans le coin supérieur gauche. Sous l’image bitmap, la ligne inférieure de pixels est répétée vers le bas. À droite de l’image bitmap, la colonne la plus à droite de pixels est répétée sur l’ensemble. Le reste de la zone de dessin est de couleur par pixel Marron foncé dans le coin inférieur droit de la bitmap. Il devrait être évident que le `Clamp` option n’est presque jamais utilisée avec la mosaïque bitmap !

L’écran Android dans le centre montre le résultat de `SKShaderTileMode.Repeat` pour les deux arguments. La vignette est répétée horizontalement et verticalement. L’écran de plateforme Windows universelle affiche `SKShaderTileMode.Mirror`. Les vignettes sont répétées, mais également retournées horizontalement et verticalement. L’avantage de cette option est qu’il n’y a aucune discontinuités entre les vignettes.

N’oubliez pas que vous pouvez utiliser différentes options pour la répétition horizontale et verticale. Vous pouvez spécifier `SKShaderTileMode.Mirror` comme deuxième argument à `CreateBitmap` mais `SKShaderTileMode.Repeat` comme troisième argument. Sur chaque ligne, les singes toujours alternez entre l’image normale et l’image miroir, mais aucune les singes sont envers.

## <a name="patterned-backgrounds"></a>Arrière-plans à motifs

Mosaïque de l’image bitmap est couramment utilisée pour créer un arrière-plan répété à partir d’une bitmap relativement faible. L’exemple classique est un mur de brique.

Le **algorithmique mur de brique** page crée une petite image bitmap qui ressemble à une brique entière et les deux moitiés d’une brique séparés par traditionnels. Étant donné que cette brique est utilisée dans l’exemple suivant, il a créé par un constructeur statique et rendues publiques avec une propriété statique :

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

La bitmap résultante est de 70 pixels de large et 60 pixels de haut :

![Vignette de mur de brique algorithmique](bitmap-tiling-images/AlgorithmicBrickWallTile.png "vignette de mur de brique algorithmique")

Le reste de la **algorithmique mur de brique** page crée une `SKShader` objet que cette image est répétée horizontalement et verticalement :

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Voici le résultat :

[![Mur de brique algorithmique](bitmap-tiling-images/AlgorithmicBrickWall.png "mur de brique algorithmique")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Vous préférerez peut-être quelque chose d’un peu plus réaliste. Dans ce cas, vous pouvez prendre une photo d’un mur de brique réelle et rogner puis. Cette image bitmap est de 300 pixels de large et 150 pixels en hauteur :

![Vignette de mur de brique](bitmap-tiling-images/BrickWallTile.jpg "vignette de mur de brique")

Cette image bitmap est utilisée dans le **photographique mur de brique** page :

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Notez que le `SKShaderTileMode` arguments à `CreateBitmap` sont tous deux `Mirror`. Cette option est généralement nécessaire lorsque vous utilisez des vignettes créées à partir d’images du monde réel. Mise en miroir les vignettes évite les discontinuités :

[![Mur de brique photographique](bitmap-tiling-images/PhotographicBrickWall.png "mur de brique photographique")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Un travail est nécessaire pour obtenir une image bitmap appropriée de la vignette. Celle-ci ne fonctionne pas très bien, car la brique plus sombre faire ressortir trop importante. Il s’affiche régulièrement dans les images répétées, révélant le fait que ce mur de brique a été construit à partir d’une bitmap plus petits.

Le **Media** dossier de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple inclut également cette image d’un mur de pierres :

![Pierre mur vignette](bitmap-tiling-images/StoneWallTile.jpg "Pierre mur de vignette")

Toutefois, la bitmap d’origine est un peu trop volumineuse pour une vignette. Il peut être redimensionné, mais la `SKShader.CreateBitmap` méthode pouvez également redimensionner la vignette en appliquant une transformation à celui-ci. Cette option est illustrée dans le **mur de Pierre** page :

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Un `SKMatrix` valeur est créée pour mettre à l’échelle de l’image à la moitié de sa taille d’origine :

[![Pierre mur](bitmap-tiling-images/StoneWall.png "Pierre mur")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

La transformation fonctionne sur la bitmap d’origine utilisée dans le `CreateBitmap` méthode ? Ou il transforme le tableau résultant des vignettes ? 

Un moyen facile de répondre à cette question consiste à inclure une rotation dans le cadre de la transformation :

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Si la transformation est appliquée à la vignette individuelle, puis chaque image répétée de la vignette doit pivoter et le résultat contient parfois des discontinuités nombreux. Mais il est évident à partir de cette capture d’écran que le tableau composite des vignettes est transformé :

[![Pierre mur paysage](bitmap-tiling-images/StoneWallRotated.png "Pierre mur paysage")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

Dans la section [ **vignette alignement**](#tile-alignment), vous verrez un exemple appliqué au nuanceur de transformation de translation.

La version autonome [ **Cat horloge** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock) exemple (ne fait pas partie de **SkiaSharpFormsDemos**) simule un arrière-plan grain de bois à l’aide de la mosaïque de bitmap basée sur cette image bitmap de 240 pixels carrée :

![Grain de bois](bitmap-tiling-images/WoodGrain.png "Grain de bois")

Il s’agit d’une photo d’un plancher bois. Le `SKShaderTileMode.Mirror` option permet de faire apparaître comme une plus grande zone du bois :

[![CAT horloge](bitmap-tiling-images/CatClock.png "Cat horloge")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Alignement de la vignette

Tous les exemples présentés jusqu'à présent ont utilisé le nuanceur créé par `SKShader.CreateBitmap` pour couvrir la totalité du canevas. Dans la plupart des cas, vous allez utiliser la mosaïque bitmap permettant de remplir des zones plus petites ou (plus rarement) pour remplir l’intérieur des lignes épaisses. Voici la vignette de mur de briques photo utilisée pour un rectangle plus petit :

[![Vignette alignement](bitmap-tiling-images/TileAlignment.png "vignette alignement")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Cela peut se présenter correctement, ou peut-être pas. Peut-être que vous êtes dérangé que la mosaïque ne commence pas par une brique complète dans le coin supérieur gauche du rectangle. C’est parce que les nuanceurs sont alignés sur le canevas et pas l’objet de graphique qui ils orner.

La solution est simple. Créer un `SKMatrix` valeur basée sur une transformation de traduction. La transformation déplace le motif en mosaïque au point où vous souhaitez le coin supérieur gauche de la vignette à être alignée. Cette approche est illustrée dans le **vignette alignement** page, qui a créé l’image des vignettes non alignés ci-dessus :

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Le **vignette alignement** page inclut un `TapGestureRecognizer`. Appuyez ou cliquez sur l’écran et les commutateurs de programme pour le `SKShader.CreateBitmap` méthode avec un `SKMatrix` argument. Cette transformation décale le modèle afin que l’angle supérieur gauche contient une brique complète :

[![Vignette alignement appuyé sur](bitmap-tiling-images/TileAlignmentTapped.png "vignette alignement activé par un clic")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

Vous pouvez également utiliser cette technique pour vous assurer que le modèle de bitmap en mosaïque est centré dans la zone il peint. Dans le **vignettes centré** page, le `PaintSurface` Gestionnaire calcule d’abord coordonnées comme s’il va afficher l’image bitmap unique dans le centre de la zone de dessin. Il utilise ensuite ces coordonnées pour créer une transformation de traduction pour `SKShader.CreateBitmap`. Cette transformation décale le modèle entier afin qu’une vignette est centrée :

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

Le `PaintSurface` Gestionnaire conclut en dessinant un cercle dans le centre de la zone de dessin. Bien sûr, une des vignettes est exactement dans le centre du cercle, et les autres sont organisés dans un modèle symétrique :

[![Centré vignettes](bitmap-tiling-images/CenteredTiles.png "centré vignettes")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Une autre approche centrage est en fait un peu plus facile. Plutôt que de construire une transformation de traduction qui place une vignette dans le centre, vous pouvez centrer un coin du modèle en mosaïque. Dans le `SKMatrix.MakeTranslation` appeler, utilisez les arguments pour le centre de la zone de dessin :

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

Le modèle est toujours centrée et symétriques, mais aucune vignette ne se trouve au centre :

[![Centré vignettes autre](bitmap-tiling-images/CenteredTilesAlternate.png "centré vignettes de remplacement")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Simplification de rotation

Parfois, à l’aide d’une transformation de rotation dans le `SKShader.CreateBitmap` méthode peut simplifier la vignette de l’image bitmap. Cela devient évident lorsque vous tentez de définir une vignette pour une frontière de sécurité de liaison de chaîne. Le **ChainLinkTile.cs** fichier crée la vignette indiquée ici (avec un arrière-plan rose pour des raisons de clarté) :

![Vignette de chaîne par lien physique](bitmap-tiling-images/HardChainLinkTile.png "vignette de chaîne par lien physique")

La vignette doit inclure deux liens, afin que le code divise la vignette en quatre quadrants. L’angle supérieur gauche et inférieur droit quadrants sont les mêmes, mais elles ne sont pas terminées. Les fils ont peu crans qui doivent être gérés avec certains dessin supplémentaires dans l’angle supérieur droit et inférieur gauche quadrants. Le fichier qui effectue tout ce travail est 174 lignes longues.

Il s’avère beaucoup plus facile à créer cette vignette :

![Vignette de liaison de chaîne plus facile](bitmap-tiling-images/EasierChainLinkTile.png "vignette de liaison de chaîne plus facile")

Si le nuanceur de vignette de l’image bitmap est pivotée de 90 degrés, les éléments visuels sont presque identiques.

Le code pour créer la vignette plus facile de lien de la chaîne fait partie de la **chaîne-lien vignette** page. Le constructeur détermine une taille de la mosaïque en fonction du type de périphérique que le programme est en cours d’exécution, puis appelle `CreateChainLinkTile`, qui dessine sur l’image bitmap à l’aide des lignes, des chemins d’accès et des nuanceurs de dégradé :

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

À l’exception des fils, la vignette est transparente, ce qui signifie que vous pouvez l’afficher sur autre chose. Le programme se charge dans une des ressources bitmap, affiche ce qu’il remplisse la zone de dessin, puis dessine le nuanceur en haut :

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Notez que le nuanceur est pivoté de 45 degrés ; il est orienté comme une barrière de chaîne-lien réel :

[![Délimitation de chaîne-lien](bitmap-tiling-images/ChainLinkFence.png "délimitation de chaîne-lien")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animation vignettes de bitmap

Vous pouvez animer un modèle de mosaïque de bitmap entière en animant la matrice de transformation. Vous pouvez choisir le modèle pour déplacer horizontalement ou verticalement, ou les deux. Que faire en créant une transformation de traduction basée sur les coordonnées de décalage.

Il est également possible de dessiner sur un petit bitmap, ou à manipuler des bits de pixel de la bitmap à une fréquence de 60 fois par seconde. Ce bitmap peut ensuite être utilisé pour mosaïque et le motif en mosaïque entière peut sembler être animée. 

Le **vignette de Bitmap animées** page illustre cette approche. Une image bitmap est instanciée en tant que champ soit 64-pixels carrés. Le constructeur appelle `DrawBitmap` pour lui donner une apparence initiale. Si le `angle` champ est égal à zéro (comme c’est lorsque la méthode est appelée tout d’abord), puis la bitmap contient deux lignes dépassés un X. Les lignes sont apportées suffisamment long pour atteindre toujours sur le bord de l’image bitmap, quel que soit le `angle` valeur : 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

La surcharge de l’animation se produit dans le `OnAppearing` et `OnDisappearing` remplace. Le `OnTimerTick` méthode anime le `angle` une valeur de 0 degré à 360 degrés toutes les 10 secondes pour faire pivoter la figure X dans l’image bitmap :

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
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
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

En raison de la symétrie de la figure X, il est identique à la rotation de la `angle` valeur comprise entre 0 degrés et 90 degrés chaque 2,5 secondes.

Le `PaintSurface` gestionnaire crée un nuanceur à partir de l’image bitmap et utilise l’objet de peinture pour la totalité du canevas de couleur :

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Le `SKShaderTileMode.Mirror` options garantissent que les branches de X dans chaque bitmap joindre avec un X dans les bitmaps adjacentes pour créer un motif animé globale qui semble beaucoup plus complexe que l’animation simple suggère :

[![Animée Bitmap vignette](bitmap-tiling-images/AnimatedBitmapTile.png "animée de vignette de Bitmap")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)
