---
title: Modes de fusion porter-Duff
description: Utilisez les modes de fusion porter-Duff pour composer des scènes en fonction des images source et de destination.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12e3e95b0f87d0e93d157bebe057874430866c2b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560778"
---
# <a name="porter-duff-blend-modes"></a>Modes de fusion porter-Duff

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les modes de fusion porter-Duff sont nommés après Thomas porter et Tom Duff, qui ont développé une algébrique de composition tout en travaillant pour Lucasfilm. Leur [_composition_](https://graphics.pixar.com/library/Compositing/paper.pdf) papier a été publiée dans le numéro de juillet 1984 des images _informatiques_, pages 253 à 259. Ces modes de fusion sont essentiels pour la composition, qui consiste à assembler différentes images dans une scène composite :

![Exemple porter-Duff](porter-duff-images/PorterDuffSample.png "Exemple porter-Duff")

## <a name="porter-duff-concepts"></a>Concepts de porter-Duff

Supposons qu’un rectangle brun occupe les deux tiers gauche et supérieur de votre surface d’affichage :

![Porter-Duff destination](porter-duff-images/PorterDuffDst.png "Porter-Duff destination")

Cette zone est appelée _destination_ ou parfois l' _arrière-plan_ ou le _fond_.

Vous souhaitez dessiner le rectangle suivant, qui est de la même taille que la destination. Le rectangle est transparent, à l’exception d’une zone bleue qui occupe les deux tiers à droite et en bas :

![Porter-Duff source](porter-duff-images/PorterDuffSrc.png "Porter-Duff source")

C’est ce que l’on appelle la _source_ ou parfois le _premier plan_.

Lorsque vous affichez la source sur la destination, voici ce que vous attendez :

![Porter-Duff source sur](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff source sur")

Les pixels transparents de la source permettent l’affichage de l’arrière-plan, tandis que les pixels de la source bleuté masquent l’arrière-plan. C’est le cas normal, et il est appelé dans SkiaSharp comme `SKBlendMode.SrcOver` . Cette valeur est le paramètre par défaut de la `BlendMode` propriété lors de la `SKPaint` première instanciation d’un objet.

Toutefois, il est possible de spécifier un mode de fondu différent pour un effet différent. Si vous spécifiez `SKBlendMode.DstOver` , dans la zone où la source et la destination se croisent, la destination apparaît à la place de la source :

![Porter-Duff de la destination](porter-duff-images/PorterDuffDstOver.png "Porter-Duff de la destination")

Le `SKBlendMode.DstIn` mode de fusion affiche uniquement la zone dans laquelle la destination et la source se croisent à l’aide de la couleur de destination :

![Porter-Duff destination dans](porter-duff-images/PorterDuffDstIn.png "Porter-Duff destination dans")

Le mode de fusion `SKBlendMode.Xor` (exclusif ou) n’affiche rien quand les deux zones se chevauchent :

![Porter-Duff exclusif ou](porter-duff-images/PorterDuffXor.png "Porter-Duff exclusif ou")

Les rectangles de destination et de source colorés divisent efficacement la surface d’affichage en quatre zones uniques qui peuvent être colorées de différentes façons correspondant à la présence des rectangles de destination et sources :

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter-Duff")

Les rectangles situés en haut à droite et en bas à gauche sont toujours vides, car la destination et la source sont transparentes dans ces zones. La couleur de destination occupe la zone supérieure gauche, ce qui signifie que la zone peut être colorée avec la couleur de destination ou pas du tout. De même, la couleur source occupe la zone inférieure droite, de sorte que la zone peut être colorée avec la couleur source ou pas du tout. L’intersection de la destination et de la source au milieu peut être colorée avec la couleur de destination, la couleur source ou pas du tout.

Le nombre total de combinaisons est 2 (pour l’angle supérieur gauche) multiplié par 2 (pour le coin inférieur droit) fois 3 (pour le centre) ou 12. Il s’agit des 12 modes de composition porter-Duff de base.

Jusqu’à la fin de la _composition d’images numériques_ (page 256), porter et Duff ajoutent un 13e mode appelé _plus_ (correspondant au `SKBlendMode.Plus` membre SKIASHARP et le mode _allégé_ W3C (qui ne doit pas être confondu avec le mode _éclairci_ du W3C). Ce `Plus` mode ajoute les couleurs de destination et de source, un processus qui sera décrit plus en détail dans les plus brefs délais.

Skia ajoute un quatorzième mode appelé `Modulate` qui est très semblable à `Plus` , à ceci près que les couleurs de destination et source sont multipliées. Il peut être traité comme un mode de fondu porter-Duff supplémentaire.

Voici les 14 modes porter-Duff tels qu’ils sont définis dans SkiaSharp. Le tableau montre la couleur de chacune des trois zones non vides dans le diagramme ci-dessus :

| Mode       | Destination | Ensembles | Source |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | Source       | X      |
| `Dst`      | X           | Destination  |        |
| `SrcOver`  | X           | Source       | X      |
| `DstOver`  | X           | Destination  | X      |
| `SrcIn`    |             | Source       |        |
| `DstIn`    |             | Destination  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | Source       |        |
| `DstATop`  |             | Destination  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | SUM          | X      |
| `Modulate` |             | Produit      |        | 

Ces modes de fusion sont symétriques. La source et la destination peuvent être échangées et tous les modes sont toujours disponibles.

La Convention d’affectation de noms des modes suit quelques règles simples :

- **Src** ou **DST** seul signifie que seuls les pixels source et de destination sont visibles.
- Le suffixe **sur** indique ce qui est visible dans l’intersection. La source ou la destination est dessinée « sur » l’autre.
- Le suffixe **in** signifie que seule l’intersection est colorée. La sortie est limitée uniquement à la partie de la source ou de la destination qui est « dans » l’autre.
- Le suffixe **out** signifie que l’intersection n’est pas colorée. La sortie est uniquement la partie de la source ou de la destination qui est « out » de l’intersection.
- Le suffixe par- **dessus** est l’Union de **in** et **out**. Il comprend la zone dans laquelle la source ou la destination est « par-dessus » de l’autre.

Notez la différence avec les `Plus` `Modulate` modes et. Ces modes effectuent un type de calcul différent sur les pixels source et de destination. Ils sont décrits plus en détail dans un instant.

La page de **grille porter-Duff** affiche les 14 modes sur un écran sous la forme d’une grille. Chaque mode est une instance distincte de `SKCanvasView` . Pour cette raison, une classe est dérivée de `SKCanvasView` nommé `PorterDuffCanvasView` . Le constructeur statique crée deux bitmaps de la même taille, l’un avec un rectangle brun dans sa zone supérieure gauche et l’autre avec un rectangle bleu :

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

Le constructeur d’instance a un paramètre de type `SKBlendMode` . Elle enregistre ce paramètre dans un champ. 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Le `OnPaintSurface` remplacement dessine les deux bitmaps. Le premier est dessiné normalement :

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

Le deuxième est dessiné avec un `SKPaint` objet dans lequel la `BlendMode` propriété a été définie sur l’argument de constructeur :

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

Le reste de la `OnPaintSurface` substitution dessine un rectangle autour de l’image bitmap pour indiquer leur taille.

La `PorterDuffGridPage` classe crée quatorze instances de `PorterDurffCanvasView` , une pour chaque membre du `blendModes` tableau. L’ordre des `SKBlendModes` membres dans le tableau est légèrement différent de celui de la table pour positionner des modes similaires adjacents. Les 14 instances de `PorterDuffCanvasView` sont organisées avec des étiquettes dans un `Grid` :

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

Voici le résultat :

[![Grille porter-Duff](porter-duff-images/PorterDuffGrid.png "Grille porter-Duff")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Vous souhaiterez vous convaincre que la transparence est essentielle au bon fonctionnement des modes de fusion porter-Duff. La `PorterDuffCanvasView` classe contient un total de trois appels à la `Canvas.Clear` méthode. Tous utilisent la méthode sans paramètre, qui définit tous les pixels comme transparents :

```csharp
canvas.Clear();
```

Essayez de modifier l’un de ces appels afin que les pixels soient définis sur le blanc opaque :

```csharp
canvas.Clear(SKColors.White);
```

Après cette modification, certains modes de fusion semblent fonctionner, contrairement à d’autres. Si vous définissez l’arrière-plan de l’image bitmap source sur blanc, le `SrcOver` mode ne fonctionne pas, car il n’y a pas de pixels transparents dans la bitmap source pour permettre l’affichage de la destination. Si vous définissez l’arrière-plan de l’image bitmap de destination ou le canevas sur blanc, `DstOver` ne fonctionne pas, car la destination n’a pas de pixels transparents.

Il peut être tentant de remplacer les bitmaps de la page de **grille porter-Duff** par des appels plus simples `DrawRect` . Cela fonctionne pour le rectangle de destination, mais pas pour le rectangle source. Le rectangle source doit englober plus que la zone de couleur bleue. Le rectangle source doit inclure une zone transparente qui correspond à la zone de couleur de la destination. Ce sont uniquement les modes de fusion qui fonctionnent.

## <a name="using-mattes-with-porter-duff"></a>Utilisation de Matts avec porter-Duff

La page de **composition du mur brique** illustre un exemple de tâche de composition classique : une image doit être Assemblée à partir de plusieurs éléments, y compris une image bitmap avec un arrière-plan qui doit être éliminé. Voici le **SeatedMonkey.jpg** bitmap avec l’arrière-plan problématique :

![Singe assis](porter-duff-images/SeatedMonkey.jpg "Singe assis")

En préparation de la composition, un _cache_ correspondant a été créé, qui est une autre bitmap noire à l’endroit où vous souhaitez que l’image apparaisse et transparente dans le cas contraire. Ce fichier est nommé **SeatedMonkeyMatte.png** et se trouve parmi les ressources du dossier **Media** dans l’exemple [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

![Cache du singe assis](porter-duff-images/SeatedMonkeyMatte.png "Cache du singe assis")

Il ne s’agit _pas_ d’un cache créé par un spécialiste. De manière optimale, le cache doit inclure des pixels partiellement transparents autour du bord des pixels noirs, ce qui n’est pas le cas de ce cache.

Le fichier XAML de la page de **composition du mur brique** instancie un `SKCanvasView` et un `Button` qui guide l’utilisateur tout au long du processus de composition de l’image finale :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

Le fichier code-behind charge les deux bitmaps dont il a besoin et gère l' `Clicked` événement de `Button` . Pour chaque `Button` clic, le `step` champ est incrémenté et une nouvelle `Text` propriété est définie pour le `Button` . Lorsque `step` atteint la valeur 5, il est rétabli sur 0 :

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

Quand le programme s’exécute pour la première fois, rien n’est visible à l’exception de `Button` :

[![Étape de composition du mur brique 0](porter-duff-images/BrickWallCompositing0.png "Étape de composition du mur brique 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

Le fait d’appuyer sur la touche `Button` une seule fois provoque un `step` incrément de 1, et le `PaintSurface` gestionnaire affiche maintenant **SeatedMonkey.jpg**:

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

Il n’y a pas d' `SKPaint` objet et, par conséquent, aucun mode de fusion. Le bitmap apparaît au bas de l’écran :

[![Étape de composition du mur brique 1](porter-duff-images/BrickWallCompositing1.png "Étape de composition du mur brique 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Appuyez de nouveau sur la touche `Button` et `step` incrémente la valeur 2. Il s’agit de l’étape essentielle de l’affichage du fichier **SeatedMonkeyMatte.png** :

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

Le mode de fusion est, ce qui `SKBlendMode.DstIn` signifie que la destination sera conservée dans les zones correspondant à des zones non transparentes de la source. Le reste du rectangle de destination correspondant à la bitmap d’origine devient transparent :

[![Étape de composition du mur brique 2](porter-duff-images/BrickWallCompositing2.png "Étape de composition du mur brique 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

L’arrière-plan a été supprimé. 

L’étape suivante consiste à dessiner un rectangle qui ressemble à un trottoir sur lequel le singe est assis. L’apparence de ce trottoir est basée sur la composition de deux nuanceurs : un nuanceur de couleur unie et un nuanceur de bruit perl :

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

Étant donné que ce trottoir doit être placé derrière le singe, le mode de fusion est `DstOver` . La destination apparaît uniquement lorsque l’arrière-plan est transparent :

[![Étape de composition du mur brique 3](porter-duff-images/BrickWallCompositing3.png "Étape de composition du mur brique 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

La dernière étape consiste à ajouter un mur de briques. Le programme utilise la vignette de l’image bitmap du mur brique disponible en tant que propriété statique `BrickWallTile` dans la `AlgorithmicBrickWallPage` classe. Une transformation de traduction est ajoutée à l' `SKShader.CreateBitmap` appel pour déplacer les vignettes afin que la ligne inférieure soit une vignette complète :

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Pour plus de commodité, l' `DrawRect` appel affiche ce nuanceur sur la zone de dessin entière, mais le `DstOver` mode limite la sortie à la zone du canevas qui est toujours transparente :

[![Étape de composition du mur brique 4](porter-duff-images/BrickWallCompositing4.png "Étape de composition du mur brique 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Évidemment, il existe d’autres façons de composer cette scène. Il peut être créé en commençant à l’arrière-plan et en progressant jusqu’au premier plan. Toutefois, l’utilisation des modes de fusion vous offre plus de souplesse. En particulier, l’utilisation du cache permet d’exclure l’arrière-plan d’une image bitmap de la scène composée.

Comme vous l’avez appris dans l’article de [découpage avec des chemins d’accès et des régions](../../curves/clipping.md), la `SKCanvas` classe définit trois types de découpage, correspondant aux [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*) [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*) méthodes, et [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) . Les modes de fusion porter-Duff ajoutent un autre type de découpage, ce qui permet de restreindre une image à tout ce que vous pouvez dessiner, y compris les bitmaps. Le cache utilisé dans la **composition du mur de briques** définit essentiellement une zone de découpage.

## <a name="gradient-transparency-and-transitions"></a>Transparence et transitions de dégradé

Les exemples de modes de fusion porter-Duff indiqués plus haut dans cet article comportent toutes les images impliquées dans des pixels opaques et des pixels transparents, mais pas des pixels partiellement transparents. Les fonctions en mode Blend sont également définies pour ces pixels. Le tableau suivant est une définition plus formelle des modes de fusion porter-Duff qui utilise la notation trouvée dans la [**référence skia SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference). (Étant donné que la **référence SkBlendMode** est une référence skia, la syntaxe C++ est utilisée.)

D’un point de vue conceptuel, les composants rouge, vert, bleu et alpha de chaque pixel sont convertis d’octets en nombres à virgule flottante dans la plage comprise entre 0 et 1. Pour le canal alpha, 0 est entièrement transparent et 1 est entièrement opaque

La notation dans le tableau ci-dessous utilise les abréviations suivantes :

- **Da** est le canal alpha de destination
- **DC** est la couleur RVB de destination
- **Sa** est le canal alpha source
- **SC** est la couleur RVB source

Les couleurs RVB sont prémultipliées par la valeur alpha. Par exemple, si **SC** représente le rouge pur mais que **sa** est égal à 0x80, alors la couleur RVB est **(0x80, 0,0)**. Si **sa** est égale à 0, tous les composants RGB sont également nuls.

Le résultat est indiqué entre crochets avec le canal alpha et la couleur RVB séparés par une virgule : **[alpha, couleur]**. Pour la couleur, le calcul est effectué séparément pour les composants rouge, vert et bleu :

| Mode       | Opération |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [Sa, SC]  |
| `Dst`      | [DA, DC]  |
| `SrcOver`  | [Sa + da · (1 – sa), SC + DC · (1 – sa) | 
| `DstOver`  | [Da + sa · (1 – da), DC + SC · (1 – da) |
| `SrcIn`    | Sa DA, SC · Da |
| `DstIn`    | Da Sa, DC · Sa |
| `SrcOut`   | Sa (1 – da), SC · (1 – da)] |
| `DstOut`   | Da (1 – sa), DC · (1 – sa)] |
| `SrcATop`  | [DA, SC · Da + DC · (1 – sa)] |
| `DstATop`  | [Sa, DC · Sa + SC · (1 – da)] |
| `Xor`      | [Sa + da – 2 · Sa DA, SC · (1 – da) + DC · (1 – sa)] |
| `Plus`     | [Sa + da, SC + DC] |
| `Modulate` | Sa DA, SC · Métafichier | 

Ces opérations sont plus faciles à analyser lorsque les fonctions **da** et **sa** sont égales à 0 ou à 1. Par exemple, pour le mode par défaut `SrcOver` , **si sa** est égale à 0, **SC** est également 0 et le résultat est **[DA, DC]**, l’alpha et la couleur de destination. Si **sa** valeur est 1, le résultat est **[sa, SC]**, la source alpha et la couleur, ou **[1, SC]**.

Les `Plus` `Modulate` modes et sont un peu différents des autres dans la mesure où de nouvelles couleurs peuvent résulter de la combinaison de la source et de la destination. Le `Plus` mode peut être interprété soit avec des composants d’octets, soit avec des composants à virgule flottante. Dans la page de **grille porter-Duff** indiquée précédemment, la couleur de destination est **(0xC0, 0x80, 0x00)** et la couleur source est **(0x00, 0x80, 0xC0)**. Chaque paire de composants est ajoutée, mais la somme est ancrée à 0xFF. Le résultat est la couleur **(0xC0, 0xFF, 0xC0)**. Il s’agit de la couleur affichée dans l’intersection.

Pour le `Modulate` mode, les valeurs RVB doivent être converties en virgule flottante. La couleur de destination est **(0,75, 0,5, 0)** et la source est **(0, 0,5, 0,75)**. Les composants RGB sont multipliés ensemble, et le résultat est **(0, 0,25, 0)**. Il s’agit de la couleur affichée dans l’intersection dans la page de **grille porter-Duff** pour ce mode.

La page de **transparence porter-Duff** vous permet d’examiner la manière dont les modes de fusion porter-Duff fonctionnent sur les objets graphiques partiellement transparents. Le fichier XAML comprend un `Picker` avec les modes porter-Duff :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

Le fichier code-behind remplit deux rectangles de la même taille à l’aide d’un dégradé linéaire. Le dégradé de destination se trouve du coin supérieur droit au coin inférieur gauche. Il se trouve dans le coin supérieur droit, mais le Centre commence à se démarquer de manière transparente et est transparent dans le coin inférieur gauche. 

Le rectangle source a un dégradé du haut à gauche vers le bas à droite. L’angle supérieur gauche est bleuté, mais disparaît à nouveau en transparent et est transparent dans le coin inférieur droit. 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Ce programme montre que les modes de fusion porter-Duff peuvent être utilisés avec des objets graphiques autres que des bitmaps. Toutefois, la source doit inclure une zone transparente. C’est le cas ici, car le dégradé remplit le rectangle, mais une partie du dégradé est transparente.

Voici trois exemples :

[![Porter-transparence Duff](porter-duff-images/PorterDuffTransparency.png "Porter-transparence Duff")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configuration de la destination et de la source est très similaire aux diagrammes illustrés dans la page 255 du document original porter-Duff [_composition d’images numériques_](https://graphics.pixar.com/library/Compositing/paper.pdf) , mais cette page montre que les modes de fusion sont corrects pour les zones de transparence partielle.

Vous pouvez utiliser des dégradés transparents pour des effets différents. L’une des possibilités est le masquage, qui est similaire à la technique illustrée dans la section [**dégradés radiaux pour le masquage**](../shaders/circular-gradients.md#radial-gradients-for-masking) de la **page dégradés circulaires SkiaSharp**. La majeure partie de la page **masque de composition** est semblable à celle du programme précédent. Il charge une ressource bitmap et détermine un rectangle dans lequel l’afficher. Un dégradé radial est créé sur la base d’un centre et d’un rayon prédéfinis :

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                        scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

La différence avec ce programme est que le dégradé commence par le noir au centre et se termine par la transparence. Il est affiché sur l’image bitmap avec un mode de fondu `DstIn` , qui affiche la destination uniquement dans les zones de la source qui ne sont pas transparentes.

Après l' `DrawRect` appel, la surface entière du canevas est transparente, à l’exception du cercle défini par le dégradé radial. Un dernier appel est effectué :

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Toutes les zones transparentes de la zone de dessin sont colorées en rose :

[![Masque de composition](porter-duff-images/CompositingMask.png "Masque de composition")](porter-duff-images/CompositingMask-Large.png#lightbox)

Vous pouvez également utiliser les modes porter-Duff et les dégradés partiellement transparents pour les transitions d’une image à l’autre. La page **transitions de dégradé** comprend un `Slider` pour indiquer un niveau de progression de la transition de 0 à 1, et un `Picker` pour choisir le type de transition souhaité :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

Le fichier code-behind charge deux ressources bitmap pour illustrer la transition. Il s’agit des deux mêmes images utilisées dans la page de **résolution des bitmaps** , plus haut dans cet article. Le code définit également une énumération avec trois membres correspondant à trois types de dégradés &mdash; linéaire, radial et Sweep. Ces valeurs sont chargées dans `Picker` :

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

Le fichier code-behind crée trois `SKPaint` objets. L' `paint0` objet n’utilise pas de mode de fusion. Cet objet Paint est utilisé pour dessiner un rectangle avec un dégradé qui passe du noir à transparent, comme indiqué dans le `colors` tableau. Le `positions` tableau est basé sur la position du `Slider` , mais il est ajusté quelque peu. Si le `Slider` est à sa valeur minimale ou maximale, les `progress` valeurs sont 0 ou 1, et l’une des deux bitmaps doit être entièrement visible. Le `positions` tableau doit être défini en conséquence pour ces valeurs.

Si la `progress` valeur est 0, le `positions` tableau contient les valeurs-0,1 et 0. SkiaSharp ajuste cette première valeur pour qu’elle soit égale à 0, ce qui signifie que le dégradé est noir uniquement à 0 et transparent dans le cas contraire. Quand `progress` a la valeur 0,5, le tableau contient les valeurs 0,45 et 0,55. Le dégradé est noir de 0 à 0,45, puis passe à transparent et est entièrement transparent de 0,55 à 1. Lorsque `progress` a la valeur 1, le `positions` tableau est 1 et 1,1, ce qui signifie que le dégradé est noir de 0 à 1.

Les `colors` `position` tableaux et sont utilisés dans les trois méthodes de `SKShader` qui créent un dégradé. Un seul de ces nuanceurs est créé en fonction de la `Picker` sélection : 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

Ce dégradé est affiché dans le rectangle sans mode de fondu. Après cet `DrawRect` appel, la zone de dessin contient simplement un dégradé de noir à transparent. La quantité de noir augmente avec des `Slider` valeurs supérieures.

Dans les quatre dernières instructions du `PaintSurface` Gestionnaire, les deux bitmaps s’affichent. Le `SrcOut` mode de fusion signifie que la première bitmap s’affiche uniquement dans les zones transparentes de l’arrière-plan. Le `DstOver` mode de la deuxième bitmap signifie que la deuxième bitmap s’affiche uniquement dans les zones où la première bitmap n’est pas affichée.

Les captures d’écran suivantes illustrent les trois types de transitions différents, chacun au niveau de la marque 50% :

[![Transitions de dégradé](porter-duff-images/GradientTransitions.png "Transitions de dégradé")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)