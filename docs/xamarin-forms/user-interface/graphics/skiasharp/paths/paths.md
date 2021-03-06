---
title: Concepts de base du chemin d’accès dans SkiaSharp
description: Cet article explore l’objet SkiaSharp SKPath pour combiner les lignes et les courbes connectées et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f80660c5875db77c85c39f570e9ae58c6c821eb6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374626"
---
# <a name="path-basics-in-skiasharp"></a>Concepts de base du chemin d’accès dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorer l’objet SkiaSharp SKPath pour combiner les lignes et les courbes connectées_

L’une des fonctionnalités les plus importantes du chemin d’accès aux graphiques est la possibilité de définir quand plusieurs lignes doivent être connectées et quand elles ne doivent pas être connectées. La différence peut être importante, étant donné que le haut de ces deux triangles illustre ce qui suit :

![Deux triangles présentant la différence entre les lignes connectées et déconnectées](paths-images/connectedlinesexample.png)

Un chemin d’accès graphique est encapsulé par l' [`SKPath`](xref:SkiaSharp.SKPath) objet. Un chemin d’accès est une collection d’un ou de plusieurs *contourages*. Chaque contour est une collection de lignes droites et de courbes *connectées* . Les contournements ne sont pas connectés les uns aux autres, mais ils peuvent se chevaucher visuellement. Parfois, un seul contour peut se chevaucher.

Un profil commence généralement par un appel à la méthode suivante `SKPath` :

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) pour commencer un nouveau contour

L’argument de cette méthode est un point unique, que vous pouvez exprimer en tant que `SKPoint` valeur ou en tant que coordonnées X et Y distinctes. L' `MoveTo` appel établit un point au début du contour et un *point actuel* initial. Vous pouvez appeler les méthodes suivantes pour continuer le contour avec une ligne ou une courbe du point actuel jusqu’à un point spécifié dans la méthode, qui devient alors le nouveau point actuel :

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) pour ajouter une ligne droite au tracé
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) pour ajouter un arc, qui est une ligne sur la circonférence d’un cercle ou d’une ellipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) pour ajouter une spline de Bézier cubique
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) pour ajouter une spline de Bézier quadratique
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) pour ajouter une spline de Bézier rationnelle rationnelle, qui peut restituer avec précision des sections coniques (ellipses, paraboliqueas et hyperboliques)

Aucune de ces cinq méthodes ne contient toutes les informations nécessaires pour décrire la ligne ou la courbe. Chacune de ces cinq méthodes fonctionne conjointement avec le point actuel établi par l’appel de méthode qui la précède immédiatement. Par exemple, la `LineTo` méthode ajoute une ligne droite au contour en fonction du point actuel, de sorte que le paramètre de `LineTo` n’est qu’un point unique.

La `SKPath` classe définit également des méthodes qui ont les mêmes noms que ces six méthodes, mais avec un `R` au début :

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`Correspond à *relatif*. Ces méthodes ont la même syntaxe que les méthodes correspondantes sans les, `R` mais sont relatives au point actuel. Ils sont pratiques pour dessiner des parties similaires d’un chemin d’accès dans une méthode que vous appelez plusieurs fois.

Un contour se termine par un autre appel à `MoveTo` ou `RMoveTo` , qui commence un nouveau contour, ou un appel à `Close` , qui ferme le contour. La `Close` méthode ajoute automatiquement une ligne droite à partir du point actuel jusqu’au premier point du contour, puis marque le tracé comme étant fermé, ce qui signifie qu’il sera rendu sans les extrémités du trait.

La différence entre les contours ouverts et fermés est illustrée dans la page de **deux facettes** , qui utilise un `SKPath` objet avec deux conversions pour afficher deux triangles. Le premier contour est ouvert et le second est fermé. Voici la [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) classe :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

Le premier contour se compose d’un appel à [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) l’utilisation de coordonnées X et Y plutôt que d’une `SKPoint` valeur, suivie de trois appels à [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) pour dessiner les trois côtés du triangle. Le deuxième contour n’a que deux appels à `LineTo` , mais il finit le contour par un appel à [`Close`](xref:SkiaSharp.SKPath.Close) , qui ferme le contour. La différence est importante :

[![Capture d’écran triple de la page des contours de deux triangles](paths-images/twotrianglecontours-small.png)](paths-images/twotrianglecontours-large.png#lightbox "Capture d’écran triple de la page des contours de deux triangles")

Comme vous pouvez le voir, le premier contour est évidemment une série de trois lignes connectées, mais la fin ne se connecte pas au début. Les deux lignes se chevauchent en haut. Le deuxième contour est évidemment fermé et a été effectué avec un nombre d’appels inférieur, `LineTo` car la `Close` méthode ajoute automatiquement une dernière ligne pour fermer le contour.

`SKCanvas` définit une seule [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) méthode, qui, dans cette démonstration, est appelée deux fois pour remplir et rayer le tracé. Toutes les contournements sont remplis, même ceux qui ne sont pas fermés. Dans le cadre du remplissage de chemins d’accès non fermés, une ligne droite est supposée exister entre les points de départ et de fin des contournements. Si vous supprimez le dernier `LineTo` du premier contour ou que vous supprimez l' `Close` appel du deuxième contour, chaque contour n’aura que deux côtés, mais sera rempli comme s’il s’agissait d’un triangle.

`SKPath` définit de nombreuses autres méthodes et propriétés. Les méthodes suivantes ajoutent des conversions entières au chemin d’accès, qui peuvent être fermées ou non fermées selon la méthode :

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) pour ajouter une courbe sur la circonférence d’une ellipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) pour ajouter un autre chemin d’accès au chemin d’accès actuel
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) pour ajouter un autre chemin d’accès en sens inverse

N’oubliez pas qu’un `SKPath` objet définit uniquement une géométrie &mdash; une série de points et de connexions. Uniquement lorsqu’un `SKPath` est combiné avec un `SKPaint` objet, le chemin d’accès est rendu avec une couleur, une largeur de trait et ainsi de suite particulières. En outre, gardez à l’esprit que l' `SKPaint` objet passé à la `DrawPath` méthode définit les caractéristiques du chemin d’accès complet. Si vous souhaitez dessiner un texte nécessitant plusieurs couleurs, vous devez utiliser un chemin d’accès distinct pour chaque couleur.

Tout comme l’apparence du début et de la fin d’une ligne est définie par un embout de trait, l’apparence de la connexion entre deux lignes est définie par une *jointure de traits*. Pour ce faire, affectez [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) à la propriété de `SKPaint` la valeur un membre de l' [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) énumération :

- `Miter` pour une jointure Pointy
- `Round` pour une jointure arrondie
- `Bevel` pour une jointure hachée

La page **jointures de traits** affiche ces trois jointures de trait avec du code similaire à la page de **extrémités de trait** . Il s’agit du `PaintSurface` Gestionnaire d’événements dans la [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) classe :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

Voici le programme en cours d’exécution :

[![Capture d’écran triple de la page de jointures de traits](paths-images/strokejoins-small.png)](paths-images/strokejoins-large.png#lightbox "Capture d’écran triple de la page de jointures de traits")

La jointure à onglets se compose d’un point aigu où les lignes se connectent. Lorsque deux lignes sont jointes à un petit angle, la jointure Mitre peut devenir assez longue. Pour éviter les jointures d’angle excessivement longues, la longueur de la jointure Mitre est limitée par la valeur de la [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) propriété de `SKPaint` . Une jointure Mitre qui dépasse cette longueur est découpée pour devenir une jointure biseautée.

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)