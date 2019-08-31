---
title: Modes de fusion de porter-Duff
description: Utilisez les modes de blend Duff de Porter pour composer des scènes basées sur des images source et de destination.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c15dd4606a75cc3cdffbad71f15299568157213a
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199871"
---
# <a name="porter-duff-blend-modes"></a>Modes de fusion de porter-Duff

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les modes de blend Duff de Porter sont nommés d’après les Thomas Porter et Tom Duff, qui a développé un algèbre de la composition lors de l’utilisation pour cinématographique Lucasfilm. Leur livre [ _Images numériques de composition_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) a été publié dans le numéro de juillet 1984 de _infographie_, pages de 253 à 259. Ces modes de fusion sont essentielles pour la composition, qui est rassemblant les différentes images dans une scène composite :

![Exemple de porter-Duff](porter-duff-images/PorterDuffSample.png "Duff de Porter exemple")

## <a name="porter-duff-concepts"></a>Concepts de porter-Duff

Supposons qu’un rectangle brunâtre occupe la gauche et supérieure deux-tiers de votre surface d’affichage :

![Destination de porter-Duff](porter-duff-images/PorterDuffDst.png "Duff de Porter Destination")

Cette zone est appelée le _destination_ ou parfois le _arrière-plan_ ou _toile de fond_.

Vous souhaitez dessiner le rectangle suivant, qui est la même taille de la destination. Le rectangle est transparent à l’exception d’une zone bleue qui occupe la droite et bas deux tiers :

![Source de porter-Duff](porter-duff-images/PorterDuffSrc.png "Duff de Porter Source")

Il s’agit du _source_ ou parfois le _premier plan_.

Lorsque vous affichez la source sur la destination, voici ce que vous attendez :

![Source Duff de porter sur](porter-duff-images/PorterDuffSrcOver.png "Source Duff de Porter sur")

Les pixels transparents de la source autorise l’arrière-plan à transparaître, tandis que les pixels source bleutée masquent l’arrière-plan. C’est le cas normal, et il s’agit de SkiaSharp comme `SKBlendMode.SrcOver`. Que valeur est le paramètre par défaut de la `BlendMode` propriété lorsqu’un `SKPaint` objet est instancié.

Toutefois, il est possible de spécifier un mode de fondu différents pour un effet différent. Si vous spécifiez `SKBlendMode.DstOver`, puis dans la zone où se croisent la source et la destination, la destination s’affiche au lieu de la source :

![Destination Duff de porter sur](porter-duff-images/PorterDuffDstOver.png "Destination Duff de Porter sur")

Le `SKBlendMode.DstIn` blend mode affiche uniquement la zone où la source et destination croisent à l’aide de la couleur de destination :

![Destination de porter-Duff dans](porter-duff-images/PorterDuffDstIn.png "Destination Duff de Porter dans")

Le mode de fusion de `SKBlendMode.Xor` (OR exclusif) provoque rien à apparaître où les deux domaines se chevauchent :

![Porter-Duff exclusif ou](porter-duff-images/PorterDuffXor.png "Duff de Porter exclusif ou")

Les rectangles de couleur source et de destination divisent de manière efficace la surface d’affichage en quatre domaines uniques qui peuvent être en couleur de différentes manières correspondant à la présence des rectangles source et de destination :

![Porter-Duff](porter-duff-images/PorterDuff.png "Duff de Porter")

Les rectangles de l’angle supérieur droit et inférieur gauche sont toujours vides car la destination et la source sont transparents dans ces domaines. La couleur de destination occupe la zone supérieure gauche, afin que la zone peut soit être en couleur avec la couleur de destination ou pas du tout. De même, la couleur source occupe la zone à droite, afin que la zone peut être colorée avec la couleur de la source ou pas du tout. L’intersection de la source au milieu et de destination peut être en couleur avec la couleur de destination, la couleur de la source, ou pas du tout.

Le nombre total de combinaisons est 2 (pour l’angle supérieur gauche) heures 2 (pour l’angle inférieur droit) heures 3 (pour le centre), ou 12. Voici les modes de composition Porter-Duff base 12.

Vers la fin de _Images numériques de composition_ (page 256), Porter et Duff ajoutent un mode 13 appelé _plus_ (correspondant à la SkiaSharp `SKBlendMode.Plus` membre et le W3C _plus clair_  mode (qui ne doit ne pas être confondu avec le W3C _éclaircir_ mode.) Cela `Plus` mode ajoute les couleurs source et de destination, un processus qui sera décrite plus en détail sous peu.

Skia ajoute un mode 14 appelé `Modulate` qui est très similaire à `Plus` , à ceci près que les couleurs source et de destination sont multipliés. Il peut être traité comme un mode de fondu Porter-Duff supplémentaire.

Voici les modes de Porter-Duff 14 tel que défini dans SkiaSharp. Le tableau montre comment ils couleur chacun des trois zones non vide dans le diagramme ci-dessus :

| Mode       | Destination | Intersection | Source |
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
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | Produit      |        | 

Ces modes de fusion sont symétriques. La source et destination peuvent être échangés et tous les modes sont toujours disponibles.

La convention d’affectation de noms des modes suit quelques règles simples :

- **Src** ou **Dst** par lui-même signifie que seuls les pixels de la source ou la destination sont visibles.
- Le **sur** suffixe indique ce qui est visible dans l’intersection. La source ou la destination est dessinée « plutôt que « l’autre.
- Le **dans** suffixe signifie que seule l’intersection est colorée. La sortie est limitée uniquement la partie de la source ou la destination est « in » l’autre.
- Le **Out** suffixe signifie que l’intersection n’est pas colorée. La sortie est uniquement la partie de la source ou la destination est « out » de l’intersection.
- Le **située en haut de** suffixe est l’union de **dans** et **Out**. Il inclut la zone où la source ou la destination est « au-dessus » de l’autre.

Notez la différence avec le `Plus` et `Modulate` modes. Ces modes effectuent un autre type de calcul sur les pixels de la source et de destination. Ils sont décrits plus en détail sous peu.

Le **Duff de Porter grille** page affiche tous les modes de 14 sur un seul écran sous la forme d’une grille. Chaque mode est une instance distincte de `SKCanvasView`. Pour cette raison, une classe est dérivée de `SKCanvasView` nommé `PorterDuffCanvasView`. Le constructeur statique crée deux bitmaps de la même taille, un avec un rectangle brunâtre dans sa partie supérieure gauche et l’autre avec un rectangle bleutée :

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

Le constructeur d’instance a un paramètre de type `SKBlendMode`. Elle enregistre ce paramètre dans un champ. 

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

Le `OnPaintSurface` remplacement dessine les bitmaps de deux. La première est dessinée normalement :

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

La seconde est dessinée avec un `SKPaint` objet où le `BlendMode` propriété a été définie pour l’argument du constructeur :

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

Le reste de la `OnPaintSurface` remplacement Dessine un rectangle autour de l’image bitmap pour indiquer leur taille.

Le `PorterDuffGridPage` classe crée des instances de quatorze de `PorterDurffCanvasView`, un pour chaque membre de le `blendModes` tableau. L’ordre de la `SKBlendModes` membres dans le tableau est un peu différente de la table afin de positionner les modes similaire adjacents. Les instances de 14 de `PorterDuffCanvasView` sont organisées en même temps que les étiquettes dans un `Grid`:

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

[![Grille de porter-Duff](porter-duff-images/PorterDuffGrid.png "Duff de Porter grille")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

Vous voudrez crainte que la transparence est essentielle au bon fonctionnement des modes de blend Duff de Porter. Le `PorterDuffCanvasView` classe contient un total de trois appels vers le `Canvas.Clear` (méthode). Tous les utilisent la méthode sans paramètre, qui définit tous les pixels transparents :

```csharp
canvas.Clear();
```

Essayez de modifier un de ces appels afin que les pixels sont définies sur blanc opaque :

```csharp
canvas.Clear(SKColors.White);
```

Après cette modification, certains modes blend seront semble fonctionner, mais d’autres pas. Si vous définissez l’arrière-plan de la bitmap source à blanc, puis le `SrcOver` mode ne fonctionne pas car il n’existe aucun pixels transparents dans le bitmap source pour permettre à la destination de transparaître. Si vous définissez l’arrière-plan de la bitmap de destination ou la zone de dessin à blanc, puis `DstOver` ne fonctionne pas, car la destination n’a pas les pixels transparents.

Il peut être tentant de remplacer les images bitmap dans le **Duff de Porter grille** page plus simple `DrawRect` appels. Qui fonctionne pour le rectangle de destination, mais pas pour le rectangle source. Le rectangle source doit englober plus que la zone de couleur bleue. Le rectangle source doit inclure une zone transparente qui correspond à la zone de couleur de la destination. Alors seulement seront ils intégreront modes travail.

## <a name="using-mattes-with-porter-duff"></a>À l’aide de caches avec Porter-Duff

La page de **composition du mur brique** illustre un exemple de tâche de composition classique: Une image doit être Assemblée à partir de plusieurs éléments, y compris une image bitmap avec un arrière-plan qui doit être éliminé. Voici le **SeatedMonkey.jpg** bitmap avec l’arrière-plan problématique :

![Assis Monkey](porter-duff-images/SeatedMonkey.jpg "assis Monkey")

En vue de la composition, correspondante _cache_ a été créé, qui est une autre image bitmap noir où vous souhaitez que l’image s’affichent et transparente dans le cas contraire. Ce fichier se nomme **SeatedMonkeyMatte.png** et est entre les ressources dans le **Media** dossier dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple :

![Assis Monkey cache](porter-duff-images/SeatedMonkeyMatte.png "assis Monkey cache")

Il s’agit de _pas_ un cache de manière experte créée. Idéalement, le cache doit inclure des pixels partiellement transparents autour du bord des pixels noirs, et n’est pas le cas de ce cache.

Le fichier XAML pour le **mur de briques composition** page instancie un `SKCanvasView` et un `Button` qui guide l’utilisateur dans le processus de composition de l’image finale :

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

Le fichier code-behind charge les deux bitmaps dont il a besoin et gère le `Clicked` événements de la `Button`. Pour chaque `Button` cliquez sur, la `step` champ est incrémenté et un nouvel `Text` propriété est définie pour le `Button`. Lorsque `step` atteint 5, il est rétabli à 0 :

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

Lorsque le programme s’exécute tout d’abord, rien n’est visible, sauf le `Button`:

[![Étape de la composition de mur de briques 0](porter-duff-images/BrickWallCompositing0.png "mur de briques composition étape 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

En appuyant sur la `Button` provoque une fois `step` d’incrémentation de 1 et le `PaintSurface` gestionnaire affiche maintenant **SeatedMonkey.jpg**:

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

Il existe aucune `SKPaint` objet et, par conséquent, aucun mode de fusion. L’image bitmap apparaît au bas de l’écran :

[![Étape de la composition de mur de briques 1](porter-duff-images/BrickWallCompositing1.png "mur de briques composition étape 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

Appuyez sur la `Button` à nouveau et `step` incréments à 2. Ceci est l’étape essentielle d’affichage de la **SeatedMonkeyMatte.png** fichier :

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

Le mode de fusion est `SKBlendMode.DstIn`, ce qui signifie que la destination est conservée dans les zones correspondant aux zones non transparentes de la source. Le reste du rectangle de destination correspondant à la bitmap d’origine devienne transparent :

[![Étape de la composition de mur de briques 2](porter-duff-images/BrickWallCompositing2.png "mur de briques composition étape 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

L’arrière-plan a été supprimée. 

L’étape suivante consiste à dessiner un rectangle qui ressemble à un trottoir le monkey s’appuie sur. L’apparence de cette trottoir est basée sur une composition de deux nuanceurs : un nuanceur de couleur unie et un nuanceur de bruit de Perlin :

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

Étant donné que cette trottoir doit être derrière le monkey, le mode de fusion est `DstOver`. La destination s’affiche uniquement lorsque l’arrière-plan est transparent :

[![Étape de la composition de mur de briques 3](porter-duff-images/BrickWallCompositing3.png "mur de briques composition étape 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

L’étape finale consiste à ajouter un mur de brique. Le programme utilise la vignette de bitmap de mur de briques disponible en tant que la propriété statique `BrickWallTile` dans la `AlgorithmicBrickWallPage` classe. Une transformation de traduction est ajoutée à la `SKShader.CreateBitmap` appel à décaler les vignettes afin que la ligne inférieure est une vignette complète :

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

Pour plus de commodité, le `DrawRect` appel affiche ce nuanceur sur la totalité du canevas, mais la `DstOver` mode limite la sortie à la zone de la zone de dessin est toujours transparent uniquement :

[![Étape de la composition de mur de briques 4](porter-duff-images/BrickWallCompositing4.png "mur de briques composition étape 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

Évidemment, il existe plusieurs manières pour composer cette scène. Il pourrait être construit en commençant à l’arrière-plan et progresse au premier plan. Mais l’utilisation des modes blend offre plus de souplesse. En particulier, l’utilisation du cache permet à l’arrière-plan d’une image bitmap à exclure de la scène composée.

Comme vous l’avez appris dans l’article [détourage avec chemins d’accès et régions](../../curves/clipping.md), le `SKCanvas` classe définit trois types de découpage, correspondant à la [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*), [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*), et [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*) méthodes. Les modes de blend Porter-Duff ajouter un autre type de découpage, qui permet de restreindre une image à tout ce que vous pouvez dessiner, y compris les bitmaps. Le cache utilisé dans **mur de briques composition** définit essentiellement une zone de découpage.

## <a name="gradient-transparency-and-transitions"></a>Transitions et transparence d’un dégradé

Les exemples de la Porter-Duff blend modes indiqués plus haut dans cet article tous impliquaient images consistait en pixels opaques et transparents pixels, mais pas partiellement transparents pixels. Les fonctions de mode de fusion sont définies pour ces pixels également. Le tableau suivant est une définition plus formelle des modes de blend Porter-Duff qui utilise la notation trouvée dans le Skia [ **SkBlendMode référence**](https://skia.org/user/api/SkBlendMode_Reference). (Étant donné que **SkBlendMode référence** est une référence Skia, syntaxe C++ est utilisé.)

Conceptuellement, les composants rouges, verts, bleu et alphabétiques de chaque pixel sont convertis à partir d’octets pour les nombres à virgule flottante dans la plage de 0 à 1. Pour le canal alpha, 0 est entièrement transparent et 1 est entièrement opaque

La notation dans le tableau ci-dessous utilise les abréviations suivantes :

- **DA** est le canal alpha de destination
- **Contrôleur de domaine** est la couleur RVB de destination
- **Sa** est le canal alpha de source
- **SC** est la couleur RVB de la source

Les couleurs RVB sont préalablement multipliées par la valeur alpha. Par exemple, si **Sc** représente rouge pure mais **Sa** est 0 x 80, est la couleur RVB **(0 x 80, 0, 0)** . Si **Sa** est 0, tous les composants RVB sont également égales à zéro.

Le résultat est indiqué entre crochets avec le canal alpha et la couleur RVB séparés par une virgule : **[couleur alpha,]** . Pour la couleur, le calcul est effectué séparément pour les composants rouges, vert et bleus :

| Mode       | Opération |
| ---------- | --------- |
| `Clear`    | [0, 0]    |
| `Src`      | [Sa, Sc]  |
| `Dst`      | [Da, contrôleur de domaine]  |
| `SrcOver`  | [Sa + Da· (1 – Sa), Sc + Dc· (1 – Sa) | 
| `DstOver`  | [+ Da Sa· (1 – Da), contrôleur de domaine + Sc· (1 – Da) |
| `SrcIn`    | [Sa· DA, Sc· DA] |
| `DstIn`    | [Da· Sa, Dc· Sa] |
| `SrcOut`   | [Sa· (1 – Da), Sc· (1 – Da)] |
| `DstOut`   | [Da· (1 – Sa), Dc· (1 – Sa)] |
| `SrcATop`  | [Da, Sc· DA + Dc· (1 – Sa)] |
| `DstATop`  | [Sa, Dc· Sa + Sc· (1 – Da)] |
| `Xor`      | [Sa + Da et – 2· Sa· DA, Sc· (1 – Da) + Dc· (1 – Sa)] |
| `Plus`     | [Sa + Da, Sc + Dc] |
| `Modulate` | [Sa· DA, Sc· Contrôleur de domaine] | 

Ces opérations sont plus faciles à analyser lorsque **Da** et **Sa** sont 0 ou 1. Par exemple, pour la valeur par défaut `SrcOver` mode, si **Sa** est 0, puis **Sc** est 0 et le résultat est également **[Da, contrôleur de domaine]** , l’alpha de destination et la couleur. Si **Sa** est 1, le résultat est **[Sa, Sc]** , l’alpha de la source et la couleur, ou **[1, Sc]** .

Le `Plus` et `Modulate` modes sont un peu différents des autres dans la mesure nouvelles couleurs peuvent être dû à la combinaison de la source et la destination. Le `Plus` mode peut être interprété soit avec les composants de l’octet ou à virgule flottante. Dans le **Duff de Porter grille** page présentée précédemment, la couleur de destination est **(0xC0, 0 x 80, 0 x 00)** et la couleur de la source est **(0 x 00, 0 x 80, 0xC0)** . Chaque paire de composants est ajouté, mais la somme est ancrée à 0xFF. Le résultat est la couleur **(0xC0, 0xFF, 0xC0)** . Qui est la couleur indiquée par l’intersection.

Pour le `Modulate` mode, les valeurs RVB doivent être converties à virgule flottante. La couleur de destination est **(0,75, 0,5, 0)** et la source est **(0, 0,5, 0,75)** . Les composants RVB sont chacun multipliés, et le résultat est **(0, 0,25, 0)** . Qui est la couleur indiquée par l’intersection de la **Duff de Porter grille** page pour ce mode.

Le **Duff de Porter transparence** page vous permet d’examiner comment les modes de blend de Porter-Duff fonctionnent sur des objets graphiques qui sont partiellement transparents. Le fichier XAML inclut un `Picker` avec les modes de Porter-Duff :

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

Le fichier code-behind remplit deux rectangles de la même taille à l’aide d’un dégradé linéaire. Le gradient de la destination est à partir de l’angle supérieur droit à l’angle inférieur gauche. Il est brunâtre dans le coin supérieur droit, mais ensuite vers le centre commence Fondu transparent et est transparent dans l’angle inférieur gauche. 

Le rectangle source a un dégradé du haut à gauche vers le coin inférieur droit. L’angle supérieur gauche est bleutée mais à nouveau Fondu transparent et est transparent dans le coin inférieur droit. 

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

Ce programme montre que les modes de blend Porter-Duff peuvent être utilisés avec des objets graphiques autres que les bitmaps. Toutefois, la source doit inclure une zone transparente. Cela est le cas ici, car le dégradé remplit le rectangle, mais la partie du dégradé est transparent.

Voici trois exemples :

[![La transparence de porter-Duff](porter-duff-images/PorterDuffTransparency.png "Duff de Porter transparence")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

La configuration de la source et de destination est très similaire aux diagrammes indiqués dans la page 255 du Porter d’origine-Duff [ _Images numériques de composition_ ](https://graphics.pixar.com/library/Compositing/paper.pdf) papier, mais cette page montre que le modes de fusion sont se comportant bien pour les zones de la transparence partielle.

Vous pouvez utiliser des dégradés transparents pour certains des effets différents. L’une des possibilités sont de masquage, ce qui revient à la technique décrite dans le [ **radiaux pour le masquage** ](../shaders/circular-gradients.md#radial-gradients-for-masking) section de la **page de dégradés circulaire SkiaSharp**. Quantité de la **composition masque** page est similaire à ce programme antérieures. Il charge une ressource bitmap et détermine un rectangle dans lequel pour l’afficher. Un dégradé radial est créé à partir d’un prédéfinie centre et le rayon :

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

La différence avec ce programme est que le dégradé commence par noir dans le centre et se termine par la transparence. Il est affiché sur l’image bitmap avec un mode de fusion de `DstIn`, qui indique la destination uniquement dans les domaines de la source qui ne sont pas transparents.

Après le `DrawRect` appel, l’intégralité de la surface de la zone de dessin est transparent à l’exception du cercle défini par le dégradé radial. Un appel final est effectué :

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

Toutes les zones transparentes du canevas sont colorées en roses :

[![Masque de composition](porter-duff-images/CompositingMask.png "masque de composition")](porter-duff-images/CompositingMask-Large.png#lightbox)

Vous pouvez également utiliser les modes de Porter-Duff et des dégradés partiellement transparents pour les transitions à partir d’une image à un autre. Le **Transitions dégradé** page inclut un `Slider` pour indiquer un niveau de progression lors de la transition à partir de 0 à 1 et un `Picker` à choisir le type de transition souhaitée :

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

Le fichier code-behind est chargé deux ressources bitmap pour illustrer la transition. Voici les deux images mêmes utilisés dans le **Bitmap disparaissent** page précédemment dans cet article. Le code définit également une énumération avec trois membres correspondant aux trois types de dégradés &mdash; linéaire et radial et balayage. Ces valeurs sont chargées dans le `Picker`:

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

Le fichier code-behind crée trois `SKPaint` objets. Le `paint0` objet n’utilise pas un mode de fusion. Cet objet de peinture est utilisé pour dessiner un rectangle avec un dégradé qui passe du noir au transparent comme indiqué dans le `colors` tableau. Le `positions` tableau repose sur la position de la `Slider`, mais quelque peu ajustée. Si le `Slider` est à son minimum ou un maximum, le `progress` les valeurs sont 0 ou 1, et les bitmaps deux doit être complètement visible. Le `positions` tableau doit être défini en conséquence pour ces valeurs.

Si le `progress` valeur est 0, puis le `positions` contient les valeurs 0 et -0,1. SkiaSharp ajustera sinon cette première valeur pour être égal à 0, ce qui signifie que le dégradé est noir uniquement à 0 et transparent. Lorsque `progress` est égale à 0,5, puis le tableau contient les valeurs 0,45 et 0,55. Le dégradé est noir à partir de 0 à 0,45, puis passe à transparent et est entièrement transparent à partir de 0,55 pour 1. Lorsque `progress` est 1, le `positions` tableau est 1 et 1.1, ce qui signifie que le dégradé est noir à partir de 0 à 1.

Le `colors` et `position` tableaux sont tous deux utilisés dans les trois méthodes de `SKShader` qui créent un dégradé. Uniquement une de ces nuanceurs est créée selon le `Picker` sélection : 

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

Ce gradient est affiché dans le rectangle sans un mode de fusion. Après cela `DrawRect` appel, la zone de dessin contient simplement un dégradé du noir transparent. La quantité de noir augmente avec une version ultérieure `Slider` valeurs.

Dans les quatre instructions finales de la `PaintSurface` gestionnaire, les deux images sont affichés. Le `SrcOut` blend mode signifie que la première bitmap s’affiche uniquement dans les zones transparentes de l’arrière-plan. Le `DstOver` mode pour la deuxième bitmap signifie que la deuxième bitmap s’affiche uniquement dans ces domaines où la bitmap première n’est pas affichée.

Les captures d’écran suivantes montrent les trois types de transitions différents, chacun à la marque de 50 % :

[![Transitions dégradées](porter-duff-images/GradientTransitions.png "Transitions dégradées")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
