---
title: Principes fondamentaux de chemin d’accès dans SkiaSharp
description: Cet article explore l’objet SkiaSharp SKPath pour la combinaison de lignes et courbes connectées et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 370fec5b9323187f6345d3e6bf9d3e38145cedff
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652786"
---
# <a name="path-basics-in-skiasharp"></a>Principes fondamentaux de chemin d’accès dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorer l’objet SkiaSharp SKPath pour la combinaison de lignes et courbes connectées_

Une des fonctionnalités plus importantes du chemin d’accès de graphiques est la capacité à définir lorsque plusieurs lignes doivent être connectés et lorsque ils ne doivent pas être connectés. La différence peut être significative, comme le montrent les parties supérieures de ces deux triangles :

![](paths-images/connectedlinesexample.png "Deux triangles montrant la différence entre les lignes connectés et déconnectés")

Un chemin d’accès de graphiques est encapsulée par la [ `SKPath` ](xref:SkiaSharp.SKPath) objet. Un chemin d’accès est une collection d’un ou plusieurs *contours*. Chaque contour est une collection de *connecté* lignes droites et courbes. Profils ne sont pas connectés entre eux, mais ils peuvent chevaucher visuellement. Parfois, un contour unique permettre se chevaucher.

Un profil de charge commence généralement par un appel à la méthode suivante de `SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) Pour commencer un nouveau profil

L’argument de cette méthode est un point unique, vous pouvez exprimer sous la forme d’un `SKPoint` valeur ou en tant que distinct X et Y des coordonnées. Le `MoveTo` appel établit un point au début de contour et un initial *point actuel*. Vous pouvez appeler les méthodes suivantes pour continuer le contour avec une ligne ou de la courbe à partir du point actuel à un point spécifié dans la méthode, qui devient alors le nouveau point actuel :

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) Pour ajouter une ligne droite pour le chemin d’accès
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) Pour ajouter un arc, qui est une ligne sur la circonférence d’un cercle ou une ellipse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) Pour ajouter une courbe de Bézier cubique
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) Pour ajouter une courbe de Bézier quadratique
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) Pour ajouter une rational quadratique spline de Bézier, qui peut rendre avec précision les sections conique (points de suspension, paraboles ou et hyperboles)

Aucun de ces cinq méthodes contient toutes les informations nécessaires pour décrire la ligne ou la courbe. Chacune de ces cinq méthodes fonctionne conjointement avec le point actuel établi par l’appel de méthode qui la précède immédiatement. Par exemple, le `LineTo` méthode ajoute une ligne droite pour le contour basé sur le point actuel, par conséquent, le paramètre `LineTo` n'est qu’un seul point.

Le `SKPath` classe définit également les méthodes qui ont les mêmes noms que ces six méthodes, mais avec un `R` au début :

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

Le `R` est l’acronyme *relatif*. Ces méthodes ont la même syntaxe que les méthodes correspondantes sans le `R` mais sont par rapport au point actuel. Ils sont pratiques pour le dessin des parties similaires d’un chemin d’accès dans une méthode que vous appelez plusieurs fois.

Un profil de charge se termine par un autre appel à `MoveTo` ou `RMoveTo`, qui commence un nouveau profil, ou un appel à `Close`, qui ferme le contour. Le `Close` méthode automatiquement ajoute une ligne droite à partir du point actuel et le premier point du contour et marque le chemin d’accès comme étant fermé, ce qui signifie qu’il sera restitué sans les embouts de trait.

La différence entre les contours ouvertes et fermées est illustrée dans le **deux profils de Triangle** page, qui utilise un `SKPath` objet avec les deux profils pour afficher deux triangles. Le premier contour est ouvert et le second est fermé. Voici le [ `TwoTriangleContoursPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) classe :

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

Le premier contour se compose d’un appel à [ `MoveTo` ](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) à l’aide des coordonnées X et Y plutôt qu’un `SKPoint` valeur, suivie de trois appels vers [ `LineTo` ](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) pour dessiner les trois côtés de la triangle. Le deuxième contour a uniquement deux appels à `LineTo` mais il termine le contour avec un appel à [ `Close` ](xref:SkiaSharp.SKPath.Close), qui ferme le contour. La différence est significative :

[![](paths-images/twotrianglecontours-small.png "Capture d’écran triple de la page de deux profils de Triangle")](paths-images/twotrianglecontours-large.png#lightbox "Triple capture d’écran de la page de deux profils de Triangle")

Comme vous pouvez le voir, le premier contour est évidemment une série de trois lignes connectées, mais la fin ne se connecte pas avec le début. Les deux lignes se chevauchent en haut. Le deuxième profil est évidemment fermé et a été effectué avec un de moins `LineTo` appelle, car le `Close` méthode ajoute automatiquement une ligne finale pour fermer le contour.

`SKCanvas` définit qu’un seul [ `DrawPath` ](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) (méthode), qui est appelée deux fois dans cette démonstration pour remplir et tracer le chemin d’accès. Tous les profils sont remplis, y compris celles qui ne sont pas fermés. Afin de combler les chemins d’accès non fermés, une ligne droite est censée pour exister entre le début et les points de terminaison des profils. Si vous supprimez le dernier `LineTo` à partir de la première contour, ou supprimez la `Close` appel à partir de la deuxième contour, chaque contour aura uniquement deux côtés mais n’être rempli comme s’il s’agissait d’un triangle.

`SKPath` définit de nombreuses autres méthodes et propriétés. Les méthodes suivantes ajoutent des contours entières pour le chemin d’accès, qui peut être fermé ou pas fermé en fonction de la méthode :

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) Pour ajouter une courbe sur la circonférence d’une ellipse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) Pour ajouter un autre chemin d’accès pour le chemin d’accès actuel
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) Pour ajouter un autre chemin d’accès dans l’ordre inverse

N’oubliez pas qu’un `SKPath` objet définit uniquement une géométrie &mdash; une série de points et des connexions. Uniquement quand un `SKPath` est combiné avec un `SKPaint` objet est le chemin d’accès rendu avec une couleur particulière, la largeur de trait et ainsi de suite. En outre, n’oubliez pas que le `SKPaint` objet passé à la `DrawPath` méthode définit les caractéristiques de la totalité du chemin. Si vous souhaitez dessiner quelque chose nécessitant plusieurs couleurs, vous devez utiliser un chemin d’accès distinct pour chaque couleur.

Tout comme l’apparence de début et de fin d’une ligne est défini à une extrémité de trait, l’apparence de la connexion entre deux lignes est définie par un *jointure de trait*. Vous spécifiez cela en définissant le [ `StrokeJoin` ](xref:SkiaSharp.SKPaint.StrokeJoin) propriété du `SKPaint` à un membre de la [ `SKStrokeJoin` ](xref:SkiaSharp.SKStrokeJoin) énumération :

- `Miter` pour une jointure après
- `Round` pour une jointure arrondie
- `Bevel` pour une jointure coupées désactivé

Le **Stroke jointures** page montre ces trois rayer jointures avec un code similaire à la **embouts de trait** page. Il s’agit du `PaintSurface` Gestionnaire d’événements dans le [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) classe :

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

[![](paths-images/strokejoins-small.png "Triple capture d’écran de la page Stroke joint")](paths-images/strokejoins-large.png#lightbox "Triple capture d’écran de la page joint de trait")

Joint d’angle se compose d’un point aigu où les lignes de se connectent. Lorsque vous participez à deux lignes à un petit angle, la jointure de pointe peut devenir assez longue. Pour empêcher excessivement longs que les jointures, la longueur de la jointure de pointe est limitée par la valeur de la [ `StrokeMiter` ](xref:SkiaSharp.SKPaint.StrokeMiter) propriété du `SKPaint`. Une jointure de pointe qui dépasse cette longueur est coupée en joint biseauté.


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
