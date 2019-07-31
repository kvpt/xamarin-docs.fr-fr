---
title: Affichage segmenté de bitmaps de SkiaSharp
description: Afficher une image bitmap SkiaSharp afin qu’une zone est étiré et certaines zones ne sont pas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: ca6c8fafe4352bac83e5ae60b43627d4c7fdc10f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648670"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Affichage segmenté de bitmaps de SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le SkiaSharp `SKCanvas` objet définit une méthode nommée `DrawBitmapNinePatch` et deux méthodes nommées `DrawBitmapLattice` qui sont très similaires. À la fois ces méthodes restituer une image bitmap à la taille d’un rectangle de destination, mais au lieu d’étirement de l’image bitmap uniformément, afficher des parties de l’image bitmap dans ses dimensions en pixels et étirer des autres parties de l’image bitmap afin qu’il tienne le rectangle :

![Segmenté exemples](segmented-images/SegmentedSample.png "segmenté d’exemple")

Ces méthodes sont généralement utilisées pour l’affichage des bitmaps qui font partie des objets d’interface utilisateur tels que des boutons. Lorsque vous concevez un bouton, généralement vous souhaitez que la taille d’un bouton doit être basé sur le contenu du bouton, mais vous voulez probablement la bordure du bouton est la même largeur, quel que soit le contenu du bouton. C’est une application idéale de `DrawBitmapNinePatch`.

`DrawBitmapNinePatch` est un cas spécial de `DrawBitmapLattice` , mais il est plus facile des deux méthodes à utiliser et à comprendre.

## <a name="the-nine-patch-display"></a>L’affichage de neuf-patch 

Conceptuellement, [ `DrawBitmapNinePatch` ](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divise un bitmap en neuf rectangles :

![Correctif neuf](segmented-images/NinePatch.png "correctif neuf")

Les rectangles aux quatre angles sont affichés dans leur taille de pixel. Comme l’indiquent les flèches, les autres zones sur les bords de l’image bitmap sont étirés horizontalement ou verticalement, vers la zone du rectangle de destination. Le rectangle dans le centre est étiré horizontalement et verticalement. 

Si ne comporte pas suffisamment d’espace dans le rectangle de destination pour afficher le même aux quatre coins dans leurs dimensions en pixels, puis elles sont réduites à la taille disponible et rien, mais les quatre coins sont affichés.

Pour diviser un bitmap en ces neuf rectangles, il est uniquement nécessaire de spécifier le rectangle dans le centre. Voici la syntaxe de la `DrawBitmapNinePatch` méthode :

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Le rectangle central est relatif à l’image bitmap. Il s’agit une `SKRectI` valeur (la version de l’entier de `SKRect`) et toutes les coordonnées et tailles sont exprimées en unités de pixels. Le rectangle de destination est par rapport à la surface d’affichage. L’argument `paint` est obligatoire.

Le **affichage correctif neuf** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple utilise d’abord un constructeur statique pour créer une propriété statique publique de type `SKBitmap`:

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

Deux autres pages dans cet article utilisent cette bitmap même. La bitmap est de 500 pixels carrés et compose d’un tableau de 25 cercles, les mêmes taille, chaque occupant une zone carrée 100 pixels :

![Cercle grille](segmented-images/CircleGrid.png "entourer de grille")

Constructeur d’instance du programme crée un `SKCanvasView` avec un `PaintSurface` Gestionnaire utilise `DrawBitmapNinePatch` pour afficher la bitmap étirée pour sa surface d’affichage entière :

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

Le `centerRect` rectangle englobe le tableau central de 16 cercles. Les cercles dans les angles sont affichés dans leurs dimensions en pixels, et tout le reste est étiré en conséquence :

[![Affichage de neuf-Patch](segmented-images/NinePatchDisplay.png "affichage de neuf-Patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

La page UWP se trouve être 500 pixels de large et par conséquent, affiche les lignes supérieures et inférieures sous la forme d’une série de cercles de la même taille. Sinon, tous les cercles qui ne sont pas dans les angles sont étirés pour les points de suspension de formulaire.

Pour obtenir un affichage étrange d’objets composé d’une combinaison des cercles et des ellipses, essayez définissant le rectangle central afin qu’il chevauche des lignes et colonnes de cercles :

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>L’affichage de treillis

Les deux `DrawBitmapLattice` méthodes sont similaires à `DrawBitmapNinePatch`, mais ils sont généralisées pour n’importe quel nombre de divisions horizontales ou verticales. Ces divisions sont définies par les tableaux d’entiers correspondant aux pixels. 

Le [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode avec les paramètres pour ces tableaux d’entiers ne semble pas fonctionner. Le [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode avec un paramètre de type `SKLattice` ne fonctionne pas, et c’est celui utilisé dans les exemples ci-dessous.

Le [ `SKLattice` ](xref:SkiaSharp.SKLattice) structure définit quatre propriétés :

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), un tableau d’entiers
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), un tableau d’entiers
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), un tableau de `SKLatticeFlags`, un type d’énumération
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) de type `Nullable<SKRectI>` pour spécifier un rectangle source facultative dans l’image bitmap

Le `XDivs` tableau divise la largeur de l’image bitmap en bandes verticales. La première bande s’étend du pixel 0 à gauche à `XDivs[0]`. Cette bande est affichée à sa largeur en pixels. La deuxième bande s’étend de `XDivs[0]` à `XDivs[1]`et est étiré. La troisième bande s’étend de `XDivs[1]` à `XDivs[2]` et est affiché dans sa largeur en pixels. La dernière bande s’étend le dernier élément du tableau vers le bord droit de l’image bitmap. Si le tableau a un nombre pair d’éléments, il est affiché dans sa largeur en pixels. Sinon, il est étiré. Le nombre total de bandes verticales est un supérieur au nombre d’éléments dans le tableau.

Le `YDivs` tableau est similaire. Il divise la hauteur du tableau en diagonale horizontal.

Ensemble, le `XDivs` et `YDivs` tableau diviser l’image bitmap en rectangles. Le nombre de rectangles est égal au produit du nombre de bandes horizontales et le nombre de bandes verticales.

Selon la documentation Skia, le `Flags` tableau contient un élément pour chaque rectangle, tout d’abord la ligne supérieure de rectangles, puis la deuxième ligne et ainsi de suite. Le `Flags` tableau est de type [ `SKLatticeFlags` ](xref:SkiaSharp.SKLatticeFlags), une énumération avec les membres suivants :

- `Default` avec la valeur 0
- `Transparent` avec la valeur 1

Toutefois, ces indicateurs ne semblent fonctionner comme ils sont censés, et il est préférable de les ignorer. Mais vous ne définissez pas la `Flags` propriété `null`. Affectez-lui la valeur d’un tableau de `SKLatticeFlags` valeurs suffisamment important pour englober le nombre total de rectangles.

Le **correctif Lattice neuf** page utilise `DrawBitmapLattice` pour imiter `DrawBitmapNinePatch`. Il utilise la même bitmap créée dans `NinePatchDisplayPage`:

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

À la fois le `XDivs` et `YDivs` propriétés sont définies sur des tableaux de deux entiers, diviser l’image bitmap en trois bandes à la fois horizontalement et verticalement : pixel 0 à 100 (rendu dans la taille en pixels) de pixel pixel 100 au pixel 400 (étendu), et pixel 400 au pixel 500 (taille en pixels). Ensemble, `XDivs` et `YDivs` définir un total de 9 rectangles, qui est la taille de la `Flags` tableau. Il vous suffit de créer le tableau est suffisant pour créer un tableau de `SKLatticeFlags.Default` valeurs.

L’affichage est identique au programme précédent :

[![Correctif de neuf en treillis](segmented-images/LatticeNinePatch.png "treillis neuf-Patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

Le **Lattice affichage** page divise l’image bitmap en 16 rectangles :

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

Le `XDivs` et `YDivs` tableaux sont quelque peu différents, à l’origine de l’affichage ne soit ne pas tout à fait comme symétrique que les exemples précédents :

[![Affichage de treillis](segmented-images/LatticeDisplay.png "treillis d’affichage")](segmented-images/LatticeDisplay-Large.png#lightbox)

Dans iOS et Android images sur la gauche, les plus petits cercles sont rendus dans leur taille de pixel. Tout le reste est étiré.

Le **Lattice affichage** page généralise la création de la `Flags` tableau, ce qui vous permet de faire des essais avec `XDivs` et `YDivs` plus facilement. En particulier, vous souhaitez voir ce qui se passe lorsque vous définissez le premier élément de la `XDivs` ou `YDivs` tableau à 0. 

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
