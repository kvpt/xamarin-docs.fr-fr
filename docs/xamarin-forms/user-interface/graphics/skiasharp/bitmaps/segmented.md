---
title: Affichage segmenté des bitmaps SkiaSharp
description: Affichez un bitmap SkiaSharp pour que certaines zones soient étirées et que certaines ne le soient pas.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a39433a0bad518055542adb190f4f441675ddfb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556371"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Affichage segmenté des bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

L' `SKCanvas` objet SkiaSharp définit une méthode nommée `DrawBitmapNinePatch` et deux méthodes nommées `DrawBitmapLattice` qui sont très similaires. Ces deux méthodes restituent une bitmap à la taille d’un rectangle de destination, mais au lieu d’étirer la bitmap uniformément, elles affichent des parties de la bitmap dans ses dimensions en pixels et étirent d’autres parties de la bitmap afin qu’elles s’ajustent au rectangle :

![Exemples segmentés](segmented-images/SegmentedSample.png "Exemple segmenté")

Ces méthodes sont généralement utilisées pour le rendu des bitmaps qui font partie d’objets d’interface utilisateur tels que des boutons. Lorsque vous concevez un bouton, en général vous souhaitez que la taille d’un bouton soit basée sur le contenu du bouton, mais vous souhaitez probablement que la bordure du bouton soit de la même largeur, quel que soit le contenu du bouton. Il s’agit d’une application idéale de `DrawBitmapNinePatch` .

`DrawBitmapNinePatch` est un cas particulier de `DrawBitmapLattice` , mais il s’agit de la plus simple des deux méthodes à utiliser et à comprendre.

## <a name="the-nine-patch-display"></a>Affichage des neuf correctifs 

D’un point de vue conceptuel, [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divise une image bitmap en neuf rectangles :

![Neuf correctif logiciel](segmented-images/NinePatch.png "Neuf correctif logiciel")

Les rectangles aux quatre coins s’affichent dans leur taille en pixels. Comme les flèches indiquent, les autres zones sur les bords de la bitmap sont étirées horizontalement ou verticalement vers la zone du rectangle de destination. Le rectangle au centre est étiré horizontalement et verticalement. 

S’il n’y a pas assez d’espace dans le rectangle de destination pour afficher les dimensions de pixel même les quatre coins, elles sont réduites à la taille disponible et rien ne s’affiche, mais les quatre angles sont affichés.

Pour diviser une image bitmap en ces neuf rectangles, il est nécessaire de spécifier le rectangle au centre uniquement. Il s’agit de la syntaxe de la `DrawBitmapNinePatch` méthode :

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Le rectangle central est relatif à l’image bitmap. Il s’agit d’une `SKRectI` valeur (la version entière de `SKRect` ) et toutes les coordonnées et tailles sont exprimées en unités de pixels. Le rectangle de destination est relatif à la surface d’affichage. L'argument `paint` est obligatoire.

La page d' **affichage des neuf patchs** de l’exemple [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) utilise tout d’abord un constructeur statique pour créer une propriété statique publique de type `SKBitmap` :

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

Deux autres pages de cet article utilisent cette même bitmap. La bitmap est un carré de 500 pixels et se compose d’un tableau de 25 cercles, tous de la même taille, chacun occupant une zone carrée de 100 pixels :

![Quadrillage de cercle](segmented-images/CircleGrid.png "Quadrillage de cercle")

Le constructeur d’instance du programme crée un `SKCanvasView` avec un `PaintSurface` gestionnaire qui utilise `DrawBitmapNinePatch` pour afficher la bitmap étirée sur sa surface d’affichage entière :

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

Le `centerRect` rectangle englobe le tableau central de 16 cercles. Les cercles des angles s’affichent dans leurs dimensions en pixels, et tout le reste est étiré en conséquence :

[![Neuf-affichage des correctifs](segmented-images/NinePatchDisplay.png "Neuf-affichage des correctifs")](segmented-images/NinePatchDisplay-Large.png#lightbox)

La page UWP se trouve à 500 pixels de largeur et affiche donc les lignes du haut et du bas sous la forme d’une série de cercles de même taille. Dans le cas contraire, tous les cercles qui ne se trouvent pas dans les angles sont étirés pour former des ellipses.

Pour un affichage étrange des objets composés d’une combinaison de cercles et de ellipses, essayez de définir le rectangle central afin qu’il chevauche les lignes et les colonnes de cercles :

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>Affichage de treillis

Les deux `DrawBitmapLattice` méthodes sont similaires à `DrawBitmapNinePatch` , mais elles sont généralisées pour un nombre quelconque de divisions horizontales ou verticales. Ces divisions sont définies par des tableaux d’entiers correspondant aux pixels. 

La [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode avec des paramètres pour ces tableaux d’entiers ne semble pas fonctionner. La [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode avec un paramètre de type `SKLattice` fonctionne, et c’est celle qui est utilisée dans les exemples ci-dessous.

La [`SKLattice`](xref:SkiaSharp.SKLattice) structure définit quatre propriétés :

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), un tableau d’entiers
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), un tableau d’entiers
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), un tableau de `SKLatticeFlags` , un type énumération
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) de type `Nullable<SKRectI>` pour spécifier un rectangle source facultatif dans la bitmap

Le `XDivs` tableau divise la largeur de la bitmap en bandes verticales. La première bande s’étend du pixel 0 à gauche à `XDivs[0]` . Cette bande est rendue dans sa largeur en pixels. La seconde bande s’étend de `XDivs[0]` à `XDivs[1]` , et est étirée. La troisième bande s’étend de `XDivs[1]` à `XDivs[2]` et est rendue dans sa largeur en pixels. La dernière bande s’étend du dernier élément du tableau au bord droit de l’image bitmap. Si le tableau a un nombre pair d’éléments, il est affiché dans sa largeur en pixels. Dans le cas contraire, il est étiré. Le nombre total de bandes verticales est supérieur au nombre d’éléments contenus dans le tableau.

Le `YDivs` tableau est similaire. Elle divise la hauteur du tableau en bandes horizontales.

Ensemble, le `XDivs` `YDivs` tableau et divise le bitmap en rectangles. Le nombre de rectangles est égal au produit du nombre de bandes horizontales et du nombre de bandes verticales.

Selon la documentation de skia, le `Flags` tableau contient un élément pour chaque rectangle, d’abord la ligne supérieure des rectangles, puis la deuxième ligne, et ainsi de suite. Le `Flags` tableau est de type [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) , une énumération avec les membres suivants :

- `Default` avec la valeur 0
- `Transparent` avec la valeur 1

Toutefois, ces indicateurs ne semblent pas fonctionner comme ils le devraient, et il est préférable de les ignorer. Mais n’affectez pas la valeur `Flags` à la propriété `null` . Affectez-lui un tableau de `SKLatticeFlags` valeurs suffisamment grand pour englober le nombre total de rectangles.

La page **treillis neuf patch** utilise `DrawBitmapLattice` pour imiter `DrawBitmapNinePatch` . Elle utilise la même bitmap créée dans `NinePatchDisplayPage` :

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

Les `XDivs` Propriétés et `YDivs` sont toutes deux définies sur des tableaux de deux entiers, divisant la bitmap en trois bandes horizontales et verticales : du pixel 0 au pixel 100 (rendu dans la taille de pixel), du pixel 100 au pixel 400 (étiré) et du pixel 400 au pixel 500 (taille de pixel). Ensemble, `XDivs` et `YDivs` définissent un total de 9 rectangles, qui est la taille du `Flags` tableau. La création du tableau suffit pour créer un tableau de `SKLatticeFlags.Default` valeurs.

L’affichage est identique à celui du programme précédent :

[![Treillis neuf-correctif](segmented-images/LatticeNinePatch.png "Treillis neuf-correctif")](segmented-images/LatticeNinePatch-Large.png#lightbox)

La page d' **affichage de treillis** divise la bitmap en 16 rectangles :

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

Les `XDivs` `YDivs` tableaux et sont légèrement différents, ce qui a pour effet que l’affichage n’est pas aussi symétrique que les exemples précédents :

[![Affichage de treillis](segmented-images/LatticeDisplay.png "Affichage de treillis")](segmented-images/LatticeDisplay-Large.png#lightbox)

Dans les images iOS et Android sur la gauche, seuls les petits cercles sont rendus dans leur taille en pixels. Tout le reste est étiré.

La page d' **affichage** de réseau généralise la création du `Flags` tableau, ce qui vous permet d’expérimenter `XDivs` et `YDivs` plus facilement. En particulier, vous souhaiterez voir ce qui se passe lorsque vous définissez le premier élément du `XDivs` `YDivs` tableau ou sur 0. 

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)