---
title: Mosaïque de bitmaps SkiaSharp
description: Juxtaposer une zone à l’aide de bitmaps répétés horizontalement et verticalement.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c6d139e47974247ce4af6bfa6c32331fcf7c824
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563131"
---
# <a name="skiasharp-bitmap-tiling"></a>Mosaïque de bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

Comme vous l’avez vu dans les deux articles précédents, la [`SKShader`](xref:SkiaSharp.SKShader) classe peut créer des dégradés linéaires ou circulaires. Cet article se concentre sur l' `SKShader` objet qui utilise une image bitmap pour juxtaposer une zone. L’image bitmap peut être répétée horizontalement et verticalement, soit à l’aide de son orientation d’origine, soit à tour de point horizontalement et verticalement. Le retournement évite les discontinuités entre les vignettes :

![Exemple de mosaïque de bitmap](bitmap-tiling-images/BitmapTilingSample.png "Exemple de mosaïque de bitmap")

La [`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) méthode statique qui crée ce nuanceur a un `SKBitmap` paramètre et deux membres de l' [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) énumération :

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

Les deux paramètres indiquent les modes utilisés pour la mosaïque horizontale et la mosaïque verticale. Il s’agit de la même `SKShaderTileMode` énumération qui est également utilisée avec les méthodes de dégradé.

Une [`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge comprend un `SKMatrix` argument pour effectuer une transformation sur les bitmaps en mosaïque :

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Cet article contient plusieurs exemples d’utilisation de cette transformation de matrice avec des bitmaps en mosaïque.

## <a name="exploring-the-tile-modes"></a>Exploration des modes de vignette

Le premier programme de la section **mosaïque d’images** de la page **nuanciers et autres effets** de l’exemple [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) illustre les effets des deux `SKShaderTileMode` arguments. Le fichier XAML de **mode de basculement de vignette de bitmap** instancie un `SKCanvasView` et deux `Picker` vues qui vous permettent de sélectionner une `SKShaderTilerMode` valeur pour la mosaïque horizontale et verticale. Notez qu’un tableau des `SKShaderTileMode` membres est défini dans la `Resources` section :

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

Le constructeur du fichier code-behind charge dans la ressource bitmap qui montre un singe assis. Il commence par rogner l’image à l’aide [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) de la méthode de `SKBitmap` sorte que les têtes et les pieds touchent les bords de l’image bitmap. Le constructeur utilise ensuite la [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) méthode pour créer une autre image bitmap de la moitié de la taille. Ces modifications rendent la bitmap un peu plus adaptée à la mosaïque :

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

Le `PaintSurface` Gestionnaire obtient les `SKShaderTileMode` paramètres à partir des deux `Picker` vues et crée un `SKShader` objet basé sur la bitmap et ces deux valeurs. Ce nuanceur est utilisé pour remplir la zone de dessin :

[![Modes de basculement de vignette de bitmap](bitmap-tiling-images/BitmapTileFlipModes.png "Modes de basculement de vignette de bitmap")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

L’écran iOS à gauche montre l’effet des valeurs par défaut de `SKShaderTileMode.Clamp` . La bitmap se trouve dans le coin supérieur gauche. En dessous de l’image bitmap, la ligne inférieure des pixels est répétée jusqu’à la fin. À droite de l’image bitmap, la colonne la plus à droite des pixels est répétée dans son intégralité. Le reste du canevas est coloré par le pixel Dark Brown dans le coin inférieur droit de la bitmap. Il doit être évident que l' `Clamp` option n’est presque jamais utilisée avec la mosaïque d’images.

L’écran Android du centre affiche le résultat de `SKShaderTileMode.Repeat` pour les deux arguments. La vignette est répétée horizontalement et verticalement. L’écran plateforme Windows universelle s’affiche `SKShaderTileMode.Mirror` . Les vignettes sont répétées, mais peuvent être retournées horizontalement et verticalement. L’avantage de cette option est qu’il n’y a aucune discontinuité entre les vignettes.

Gardez à l’esprit que vous pouvez utiliser différentes options pour la répétition horizontale et verticale. Vous pouvez spécifier `SKShaderTileMode.Mirror` comme deuxième argument de, `CreateBitmap` mais `SKShaderTileMode.Repeat` comme troisième argument. Sur chaque ligne, les singes alternent toujours entre l’image normale et l’image miroir, mais aucun des singes n’est à l’envers.

## <a name="patterned-backgrounds"></a>Arrière-plans à motif

La mosaïque bitmap est couramment utilisée pour créer un arrière-plan à motif à partir d’une image bitmap relativement petite. L’exemple classique est un mur de briques.

La page du **mur de briques algorithmiques** crée une petite bitmap qui ressemble à une brique entière et deux moitiés d’une brique, séparées par un mortier. Étant donné que cette brique est également utilisée dans l’exemple suivant, elle est créée par un constructeur statique et rendue publique avec une propriété statique :

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

La bitmap résultante est 70 pixels de large et 60 pixels de haut :

![Vignette du mur de briques algorithmiques](bitmap-tiling-images/AlgorithmicBrickWallTile.png "Vignette du mur de briques algorithmiques")

Le reste de la page du **mur de briques algorithmiques** crée un `SKShader` objet qui répète cette image horizontalement et verticalement :

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

[![Mur de briques algorithmiques](bitmap-tiling-images/AlgorithmicBrickWall.png "Mur de briques algorithmiques")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Vous préférerez peut-être un peu plus réaliste. Dans ce cas, vous pouvez prendre une photo d’un mur de briques réel, puis la rogner. Cette image bitmap est de 300 pixels de large et de 150 pixels de haut :

![Vignette de mur de briques](bitmap-tiling-images/BrickWallTile.jpg "Vignette de mur de briques")

Cette image bitmap est utilisée dans la page du **mur de briques photographique** :

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

Notez que les `SKShaderTileMode` arguments de `CreateBitmap` sont tous les deux `Mirror` . Cette option est généralement nécessaire lorsque vous utilisez des vignettes créées à partir d’images réelles. La mise en miroir des vignettes évite les discontinuités :

[![Mur de briques photographique](bitmap-tiling-images/PhotographicBrickWall.png "Mur de briques photographique")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Un travail est nécessaire pour obtenir une image bitmap appropriée pour la vignette. Celui-ci ne fonctionne pas très bien, car la brique plus sombre est trop importante. Il apparaît régulièrement dans les images répétées, révélant le fait que ce mur de briques a été construit à partir d’une image bitmap plus petite.

Le dossier **Media** de l’exemple [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comprend également cette image d’un mur en pierre :

![Vignette de mur de Pierre](bitmap-tiling-images/StoneWallTile.jpg "Vignette de mur de Pierre")

Toutefois, le bitmap d’origine est un peu trop grand pour une vignette. Elle peut être redimensionnée, mais la `SKShader.CreateBitmap` méthode peut également redimensionner la vignette en lui appliquant une transformation. Cette option est illustrée dans la page du **mur en pierre** :

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

Une `SKMatrix` valeur est créée pour mettre à l’échelle l’image à la moitié de sa taille d’origine :

[![Mur en pierre](bitmap-tiling-images/StoneWall.png "Mur en pierre")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

La transformation opère-t-elle sur la bitmap d’origine utilisée dans la `CreateBitmap` méthode ? Ou est-ce qu’elle transforme le tableau de vignettes résultant ? 

Un moyen simple de répondre à cette question consiste à inclure une rotation dans le cadre de la transformation :

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Si la transformation est appliquée à la vignette individuelle, chaque image répétée de la vignette doit être pivotée, et le résultat contient de nombreuses discontinuités. Mais il est évident de cette capture d’écran que le tableau composite des vignettes est transformé :

[![Mur en pierres paysage](bitmap-tiling-images/StoneWallRotated.png "Mur en pierres paysage")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

Dans la section [**alignement**](#tile-alignment)de la mosaïque, vous verrez un exemple de transformation de traduction appliquée au nuanceur.

L’exemple d' [**horloge Cat**](/samples/xamarin/xamarin-forms-samples/catclock) autonome (qui ne fait pas partie de **SkiaSharpFormsDemos**) simule un arrière-plan de grain de bois à l’aide d’une mosaïque de bitmap basée sur cette image bitmap carrée de 240 pixels :

![Grain grain](bitmap-tiling-images/WoodGrain.png "Grain grain")

Il s’agit d’une photographie d’un plancher en bois. L' `SKShaderTileMode.Mirror` option lui permet d’apparaître en tant que plus grande surface de bois :

[![Horloge CAT](bitmap-tiling-images/CatClock.png "Horloge CAT")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Alignement des mosaïques

Tous les exemples présentés jusqu’à présent ont utilisé le nuanceur créé par `SKShader.CreateBitmap` pour couvrir la zone de dessin entière. Dans la plupart des cas, vous utiliserez la mosaïque de bitmaps pour déposer des zones plus petites ou (plus rarement) pour remplir l’intérieur des lignes épaisses. Voici la vignette brique briques-Wall utilisée pour un rectangle plus petit :

[![Alignement des mosaïques](bitmap-tiling-images/TileAlignment.png "Alignement des mosaïques")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Cela peut vous sembler très bien, ou peut-être pas. Vous êtes peut-être dérangé que le modèle de mosaïque ne commence pas par une brique pleine dans le coin supérieur gauche du rectangle. Cela est dû au fait que les nuanceurs sont alignés avec le canevas et non l’objet graphique qu’ils ornent.

Le correctif est simple. Créer une `SKMatrix` valeur basée sur une transformation de traduction. La transformation décale efficacement le modèle en mosaïque jusqu’au point où vous souhaitez aligner l’angle supérieur gauche de la vignette. Cette approche est illustrée dans la page **alignement des vignettes** , qui a créé l’image des vignettes non alignées indiquées ci-dessus :

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

La page alignement de la **mosaïque** comprend un `TapGestureRecognizer` . Appuyez ou cliquez sur l’écran, et le programme bascule vers la `SKShader.CreateBitmap` méthode avec un `SKMatrix` argument. Cette transformation décale le modèle de sorte que l’angle supérieur gauche contienne une brique pleine :

[![Alignement des vignettes taraudé](bitmap-tiling-images/TileAlignmentTapped.png "Alignement des vignettes taraudé")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

Vous pouvez également utiliser cette technique pour vous assurer que le modèle de bitmap en mosaïque est centré dans la zone qu’il peint. Dans la page des **vignettes centrées** , le `PaintSurface` Gestionnaire calcule d’abord les coordonnées comme s’il s’agissait d’afficher l’image bitmap unique au centre de la zone de dessin. Il utilise ensuite ces coordonnées pour créer une transformation de traduction pour `SKShader.CreateBitmap` . Cette transformation décale l’intégralité du modèle afin qu’une vignette soit centrée :

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

Le `PaintSurface` Gestionnaire se termine en dessinant un cercle au centre de la zone de dessin. Bien sûr, l’une des vignettes est exactement au centre du cercle et les autres sont organisées selon un modèle symétrique :

[![Vignettes centrées](bitmap-tiling-images/CenteredTiles.png "Vignettes centrées")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Une autre approche de centrage est en fait un peu plus simple. Au lieu de construire une transformation translate qui place une vignette au centre, vous pouvez centrer un angle du modèle en mosaïque. Dans l' `SKMatrix.MakeTranslation` appel, utilisez des arguments pour le centre de la zone de dessin :

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

Le modèle est toujours centré et symétrique, mais aucune vignette n’est au centre :

[![Autres vignettes centrées](bitmap-tiling-images/CenteredTilesAlternate.png "Autres vignettes centrées")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Simplification de la rotation

Parfois, l’utilisation d’une transformation de rotation dans la `SKShader.CreateBitmap` méthode peut simplifier la vignette bitmap. Cela devient évident lorsque vous tentez de définir une vignette pour une clôture de liaison de chaîne. Le fichier **ChainLinkTile.cs** crée la vignette illustrée ici (avec un arrière-plan rose à des fins de clarté) :

![Vignette de liaison de chaîne matérielle](bitmap-tiling-images/HardChainLinkTile.png "Vignette de liaison de chaîne matérielle")

La vignette doit inclure deux liens, de sorte que le code divise la vignette en quatre quadrants. Les quadrants supérieur gauche et inférieur droit sont identiques, mais ils ne sont pas complets. Les câbles ont quelques crans qui doivent être traités avec un dessin supplémentaire dans les quadrants supérieur droit et inférieur gauche. Le fichier qui effectue tout ce travail est 174 lignes.

Il s’avère beaucoup plus facile de créer cette vignette :

![Vignette de liaison de chaîne plus facile](bitmap-tiling-images/EasierChainLinkTile.png "Vignette de liaison de chaîne plus facile")

Si le nuanceur de vignettes bitmap est pivoté de 90 degrés, les visuels sont quasiment identiques.

Le code permettant de créer la vignette de liaison de chaîne plus facile fait partie de la page de **vignette de liaison de chaîne** . Le constructeur détermine une taille de vignette en fonction du type d’appareil sur lequel le programme s’exécute, puis appelle `CreateChainLinkTile` , qui dessine sur le bitmap à l’aide de lignes, de chemins d’accès et de nuanceurs de dégradé :

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

À l’exception des câbles, la vignette est transparente, ce qui signifie que vous pouvez l’afficher au-dessus d’un autre élément. Le programme se charge dans l’une des ressources bitmap, l’affiche pour remplir le canevas, puis dessine le nuanceur en haut :

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

Notez que le nuanceur est pivoté de 45 degrés de manière à ce qu’il soit orienté comme une barrière de liaison de chaîne réelle :

[![Limite de liaison de chaîne](bitmap-tiling-images/ChainLinkFence.png "Limite de liaison de chaîne")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animer des vignettes de bitmap

Vous pouvez animer l’intégralité d’un modèle de mosaïque bitmap en animant la transformation de matrice. Peut-être souhaitez-vous que le modèle se déplace horizontalement ou verticalement, ou les deux à la fois. Pour ce faire, vous pouvez créer une transformation de traduction basée sur les coordonnées de décalage.

Il est également possible de dessiner sur une petite image bitmap ou de manipuler les bits de pixel de la bitmap au taux de 60 fois par seconde. Cette bitmap peut ensuite être utilisée pour la mosaïque, et l’ensemble du modèle en mosaïque peut paraître animé. 

La page de vignette de la **bitmap animée** illustre cette approche. Une image bitmap est instanciée en tant que champ pour atteindre un carré de 64 pixels. Le constructeur appelle `DrawBitmap` pour lui attribuer une apparence initiale. Si le `angle` champ est égal à zéro (comme c’est le cas lorsque la méthode est appelée pour la première fois), le bitmap contient deux lignes croisées comme un X. Les lignes sont suffisamment longues pour atteindre toujours le bord de l’image bitmap, quelle que soit la `angle` valeur : 

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

La surcharge d’animation se produit dans les `OnAppearing` `OnDisappearing` substitutions et. La `OnTimerTick` méthode anime la `angle` valeur de 0 degré à 360 degrés toutes les 10 secondes pour faire pivoter la figure X dans l’image bitmap :

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

En raison de la symétrie de la figure X, cela revient à faire pivoter la `angle` valeur de 0 degré à 90 degrés toutes les 2,5 secondes.

Le `PaintSurface` Gestionnaire crée un nuanceur à partir de l’image bitmap et utilise l’objet Paint pour colorer l’intégralité du canevas :

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

Les `SKShaderTileMode.Mirror` options permettent de s’assurer que les bras de la Croix (x) de chaque bitmap avec le x dans les bitmaps adjacentes pour créer un modèle animé global qui semble bien plus complexe que l’animation simple suggérerait :

[![Vignette image animée](bitmap-tiling-images/AnimatedBitmapTile.png "Vignette image animée")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (exemple)](/samples/xamarin/xamarin-forms-samples/catclock)