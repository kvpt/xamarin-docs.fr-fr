---
title: Principes fondamentaux de chemin d’accès dans SkiaSharp
description: Cet article explore l’objet SkiaSharp SKPath pour la combinaison de lignes et courbes connectées et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: c892adf2f75ec00c4a9ee171ded78f79bb8227e9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291764"
---
# <a name="path-basics-in-skiasharp"></a>Principes fondamentaux de chemin d’accès dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorer l’objet SkiaSharp SKPath pour combiner les lignes et les courbes connectées_

Une des fonctionnalités plus importantes du chemin d’accès de graphiques est la capacité à définir lorsque plusieurs lignes doivent être connectés et lorsque ils ne doivent pas être connectés. La différence peut être significative, comme le montrent les parties supérieures de ces deux triangles :

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

Un chemin d’accès graphique est encapsulé par l’objet [`SKPath`](xref:SkiaSharp.SKPath) . Un chemin d’accès est une collection d’un ou de plusieurs *contourages*. Chaque contour est une collection de lignes droites et de courbes *connectées* . Profils ne sont pas connectés entre eux, mais ils peuvent chevaucher visuellement. Parfois, un contour unique permettre se chevaucher.

Un profil commence généralement par un appel à la méthode de `SKPath`suivante :

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) de commencer un nouveau contour

L’argument de cette méthode est un point unique, que vous pouvez exprimer en tant que valeur `SKPoint` ou en tant que coordonnées X et Y distinctes. L’appel de `MoveTo` établit un point au début du contour et un *point actuel*initial. Vous pouvez appeler les méthodes suivantes pour continuer le contour avec une ligne ou de la courbe à partir du point actuel à un point spécifié dans la méthode, qui devient alors le nouveau point actuel :

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) pour ajouter une ligne droite au tracé
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) pour ajouter un arc, qui est une ligne sur la circonférence d’un cercle ou d’une ellipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) pour ajouter une spline de Bézier cubique
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) pour ajouter une spline de Bézier quadratique
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) pour ajouter une spline de Bézier rationnelle quadratique, qui peut restituer avec précision des sections coniques (ellipses, paraboliqueas et hyperboliques)

Aucun de ces cinq méthodes contient toutes les informations nécessaires pour décrire la ligne ou la courbe. Chacune de ces cinq méthodes fonctionne conjointement avec le point actuel établi par l’appel de méthode qui la précède immédiatement. Par exemple, la méthode `LineTo` ajoute une ligne droite au contour en fonction du point actuel. par conséquent, le paramètre pour `LineTo` n’est qu’un point unique.

La classe `SKPath` définit également des méthodes qui ont les mêmes noms que ces six méthodes, mais avec un `R` au début :

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

Le `R` correspond à *relatif*. Ces méthodes ont la même syntaxe que les méthodes correspondantes sans le `R`, mais sont relatives au point actuel. Ils sont pratiques pour le dessin des parties similaires d’un chemin d’accès dans une méthode que vous appelez plusieurs fois.

Un contour se termine par un autre appel à `MoveTo` ou `RMoveTo`, qui commence un nouveau contour ou un appel à `Close`, qui ferme le contour. La méthode `Close` ajoute automatiquement une ligne droite à partir du point actuel jusqu’au premier point du contour, puis marque le tracé comme fermé, ce qui signifie qu’il sera rendu sans les extrémités du trait.

La différence entre les contours ouverts et fermés est illustrée dans la page de **deux facettes** , qui utilise un objet `SKPath` avec deux conversions pour afficher deux triangles. Le premier contour est ouvert et le second est fermé. Voici la classe [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) :

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

Le premier contour se compose d’un appel à [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) à l’aide de coordonnées X et Y plutôt que d’une valeur de `SKPoint`, suivi de trois appels à [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) pour dessiner les trois côtés du triangle. Le deuxième contour n’a que deux appels à `LineTo`, mais il finit le contour par un appel à [`Close`](xref:SkiaSharp.SKPath.Close), qui ferme le contour. La différence est significative :

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

Comme vous pouvez le voir, le premier contour est évidemment une série de trois lignes connectées, mais la fin ne se connecte pas avec le début. Les deux lignes se chevauchent en haut. Le deuxième contour est évidemment fermé et a été effectué avec un minimum de `LineTo` appels, car la méthode `Close` ajoute automatiquement une dernière ligne pour fermer le contour.

`SKCanvas` définit une seule méthode [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) , qui, dans cette démonstration, est appelée deux fois pour remplir et rayer le tracé. Tous les profils sont remplis, y compris celles qui ne sont pas fermés. Afin de combler les chemins d’accès non fermés, une ligne droite est censée pour exister entre le début et les points de terminaison des profils. Si vous supprimez le dernier `LineTo` du premier contour, ou que vous supprimez le `Close` appel du deuxième contour, chaque contour n’aura que deux côtés, mais sera rempli comme s’il s’agissait d’un triangle.

`SKPath` définit de nombreuses autres méthodes et propriétés. Les méthodes suivantes ajoutent des contours entières pour le chemin d’accès, qui peut être fermé ou pas fermé en fonction de la méthode :

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) pour ajouter une courbe sur la circonférence d’une ellipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) pour ajouter un autre chemin d’accès au chemin d’accès actuel
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) pour ajouter un autre chemin d’accès en sens inverse

N’oubliez pas qu’un objet `SKPath` définit uniquement une géométrie &mdash; une série de points et de connexions. Uniquement lorsqu’un `SKPath` est combiné avec un objet `SKPaint` est le chemin d’accès rendu avec une couleur, une largeur de trait et ainsi de suite particulières. En outre, gardez à l’esprit que l’objet `SKPaint` passé à la méthode `DrawPath` définit des caractéristiques du chemin d’accès entier. Si vous souhaitez dessiner quelque chose nécessitant plusieurs couleurs, vous devez utiliser un chemin d’accès distinct pour chaque couleur.

Tout comme l’apparence du début et de la fin d’une ligne est définie par un embout de trait, l’apparence de la connexion entre deux lignes est définie par une *jointure de traits*. Pour ce faire, définissez la propriété [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) de `SKPaint` sur un membre de l’énumération [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) :

- `Miter` pour une jointure Pointy
- `Round` pour une jointure arrondie
- `Bevel` pour une jointure hachée

La page **jointures de traits** affiche ces trois jointures de trait avec du code similaire à la page de **extrémités de trait** . Il s’agit du gestionnaire d’événements `PaintSurface` dans la classe [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) :

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

[![](paths-images/strokejoins-small.png "Triple screenshot of the Stroke Joins page")](paths-images/strokejoins-large.png#lightbox "Triple screenshot of the Stroke Joins page")

Joint d’angle se compose d’un point aigu où les lignes de se connectent. Lorsque vous participez à deux lignes à un petit angle, la jointure de pointe peut devenir assez longue. Pour éviter les jointures d’angle excessivement longues, la longueur de la jointure Mitre est limitée par la valeur de la propriété [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) de `SKPaint`. Une jointure de pointe qui dépasse cette longueur est coupée en joint biseauté.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
