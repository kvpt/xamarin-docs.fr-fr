---
title: Trois types de courbes Bézier
description: Cet article explique comment utiliser SkiaSharp pour restituer des courbes de Bézier cubiques, quadratiques et coniques dans des Xamarin.Forms applications, et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ad548846500ccbacc2a3d117919bfb4df1a1d79
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138682"
---
# <a name="three-types-of-bzier-curves"></a>Trois types de courbes Bézier

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser SkiaSharp pour restituer des courbes de Bézier cubiques, quadratiques et coniques_

La courbe de Bézier est nommée après Pierre Bézier (1910 – 1999), un ingénieur français au niveau de la société automobile Renault, qui a utilisé la courbe pour la conception assistée par ordinateur des corps automobiles.

Les courbes de Bézier sont connues pour être bien adaptées à la conception interactive : elles se comportent bien &mdash; en d’autres termes, il n’y a pas de singularités qui entraînent une courbe infinie ou peu maniable &mdash; . elles sont généralement agréables à l’esthétique :

![Exemple de courbe de Bézier](beziers-images/beziersample.png)

Les contours de caractère des polices basées sur l’ordinateur sont généralement définies avec des courbes de Bézier.

L’article Wikipédia sur la [**courbe Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contient des informations générales utiles. Le terme *courbe Bézier* fait référence à une famille de courbes similaires. SkiaSharp prend en charge trois types de courbes de Bézier, appelés *cubes*, *quadratiques*et *coniques*. Le cône est également appelé *Rational quadratique*.

## <a name="the-cubic-bzier-curve"></a>Courbe de Bézier cubique

Le cubique est le type de courbe de Bézier que la plupart des développeurs pensent lorsque l’objet des courbes de Bézier apparaît.

Vous pouvez ajouter une courbe de Bézier cubique à un `SKPath` objet à l’aide de la [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) méthode avec trois `SKPoint` paramètres, ou la [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) surcharge avec des `x` paramètres et distincts `y` :

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La courbe commence au point actuel du contour. La courbe de Bézier cubique complète est définie par quatre points :

- point de départ : point actuel dans le contour, ou (0,0) si n' `MoveTo` a pas été appelé
- premier point de contrôle : `point1` dans l' `CubicTo` appel
- deuxième point de contrôle : `point2` dans l' `CubicTo` appel
- point de terminaison : `point3` dans l' `CubicTo` appel

La courbe résultante commence au point de départ et se termine au point de terminaison. En général, la courbe ne traverse pas les deux points de contrôle. au lieu de cela, les points de contrôle fonctionnent très bien comme les aimants pour tirer la courbe.

La meilleure façon d’obtenir une idée de la courbe de Bézier cubique est d’expérimenter. C’est l’objectif de la page de **courbe de Bézier** , qui dérive de `InteractivePage` . Le fichier [**BezierCurvePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) instancie le `SKCanvasView` et un `TouchEffect` . Le fichier code-behind [**BezierCurvePage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) crée quatre `TouchPoint` objets dans son constructeur. Le `PaintSurface` Gestionnaire d’événements crée un `SKPath` pour restituer une courbe de Bézier basée sur les quatre `TouchPoint` objets, et dessine des lignes tangentes en pointillés à partir des points de contrôle jusqu’aux points de terminaison :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

Ici, il s’exécute :

[![Capture d’écran triple de la page de courbe de Bézier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Mathématiquement, la courbe est un polynôme cubique. La courbe croise une ligne droite à trois points au maximum. Au point de départ, la courbe est toujours tangente à, et dans la même direction que, une ligne droite à partir du point de départ jusqu’au premier point de contrôle. Au point de terminaison, la courbe est toujours tangente à, et dans la même direction que, une ligne droite à partir du deuxième point de contrôle jusqu’au point de terminaison.

La courbe de Bézier cubique est toujours limitée par un quadrilatère convexe reliant les quatre points. C’est ce que l’on appelle une *coque convexe*. Si les points de contrôle se trouvent sur la ligne droite comprise entre le point de départ et le point de terminaison, la courbe de Bézier est rendue sous la forme d’une ligne droite. Mais la courbe peut également se croiser, comme le montre la troisième capture d’écran.

Un contour de tracé peut contenir plusieurs courbes de Bézier cubiques connectées, mais la connexion entre deux courbes de Bézier cubiques est lisse uniquement si les trois points suivants sont colinéaires (c’est-à-dire qu’ils se trouvent sur une ligne droite) :

- deuxième point de contrôle de la première courbe
- point de terminaison de la première courbe, qui est également le point de départ de la deuxième courbe
- premier point de contrôle de la deuxième courbe

Dans l’article suivant sur les [**données du chemin d’accès SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), vous découvrirez une fonctionnalité qui facilite la définition des courbes de Bézier correctement connectées.

Il est parfois utile de connaître les équations paramétriques sous-jacentes qui rendent une courbe de Bézier cubique. Pour *t* allant de 0 à 1, les équations paramétriques sont les suivantes :

x (t) = (1 – t) ³ x ₀ + 3T (1 – t) ² x ₁ + 3T ² (1 – t) x ₂ + t ³ x ₃

y (t) = (1 – t) ³ y ₀ + 3T (1 – t) ² y ₁ + 3T ² (1 – t) y ₂ + t ³ y ₃

L’exposant le plus élevé de 3 confirme qu’il s’agit de polynômes cubiques. Il est facile de vérifier que lorsque est `t` égal à 0, le point est (x ₀, y ₀), qui est le point de départ, et lorsque est `t` égal à 1, le point est (x ₃, y ₃), qui est le point de terminaison. Près du point de départ (pour les valeurs basses de `t` ), le premier point de contrôle (x ₁, y ₁) a un effet fort, et près du point de terminaison (valeurs élevées de « t ») le deuxième point de contrôle (x ₂, y ₂) a un effet fort.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Approximation de la courbe de Bézier en arcs circulaires

Il est parfois pratique d’utiliser une courbe de Bézier pour restituer un arc de cercle. Une courbe de Bézier cubique peut se rapprocher d’un arc de cercle très bien jusqu’à un quart de cercle, donc quatre courbes de Bézier connectées peuvent définir un cercle entier. Cette approximation est abordée dans deux articles publiés il y a plus de 25 ans :

> Si l’on Dokken, et al, « bonne approximation des cercles par courbure-courbes de Bézier continues », la *conception géométrique assistée par ordinateur 7* (1990), 33-41.

> Michael Goldapp, « approximation des arcs circulaires par des polynômes cubiques », « *Computer-Design Geometry 8* (1991), 227-238.

Le diagramme suivant illustre quatre points étiquetés `pto` ,, `pt1` `pt2` et `pt3` définissant une courbe de Bézier (en rouge) qui se rapproche d’un arc de cercle :

![Approximation d’un arc de cercle avec une courbe de Bézier](beziers-images/bezierarc45.png)

Les lignes des points de début et de fin aux points de contrôle sont tangentes au cercle et à la courbe de Bézier, et elles ont une longueur de *L*. Le premier article mentionné ci-dessus indique que la courbe de *Bézier est la* plus proche d’un arc de cercle lorsque cette longueur est calculée comme suit :

L = 4 × Tan (α/4)/3

L’illustration montre un angle de 45 degrés, donc L est égal à 0,265. Dans le code, cette valeur est multipliée par le rayon souhaité du cercle.

La page **arc de cercle de Bézier** vous permet d’expérimenter la définition d’une courbe de Bézier pour rapprocher un arc de cercle pour des angles allant jusqu’à 180 degrés. Le fichier [**BezierCircularArcPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) instancie le `SKCanvasView` et un `Slider` pour la sélection de l’angle. Le `PaintSurface` Gestionnaire d’événements dans le fichier code-behind [**BezierCircularArgPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) utilise une transformation pour définir le point (0,0) au centre de la zone de dessin. Il dessine un cercle centré sur ce point à des fins de comparaison, puis calcule les deux points de contrôle de la courbe de Bézier :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

Les points de début et de fin ( `point0` et `point3` ) sont calculés en fonction des équations paramétriques normales pour le cercle. Étant donné que le cercle est centré à (0,0), ces points peuvent également être traités comme des vecteurs radials à partir du centre du cercle jusqu’à la circonférence. Les points de contrôle se trouvent sur des lignes tangentes au cercle, donc elles sont perpendiculaires à ces vecteurs radiaux. Un vecteur à angle droit vers un autre est simplement le vecteur d’origine avec les coordonnées X et Y échangées et l’une d’entre elles a été négative.

Voici le programme en cours d’exécution avec différents angles :

[![Capture d’écran triple de la page de l’arc de cercle de Bézier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Examinez attentivement la troisième capture d’écran, et vous verrez que la courbe de Bézier s’écarte notamment d’un demi-cercle lorsque l’angle est de 180 degrés, mais l’écran iOS montre qu’il semble s’ajuster à un quart de cercle juste lorsque l’angle est de 90 degrés.

Le calcul des coordonnées des deux points de contrôle est très simple lorsque le cercle quart est orienté comme suit :

![Approximation d’un cercle quart avec une courbe de Bézier](beziers-images/bezierarc90.png)

Si le rayon du cercle est 100, *L* est 55, et c’est un nombre facile à mémoriser.

La page en forme de **cercle** anime une figure entre un cercle et un carré. Le cercle est approximatif de quatre courbes de Bézier dont les coordonnées sont affichées dans la première colonne de cette définition de tableau dans la [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe :

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

La deuxième colonne contient les coordonnées des quatre courbes de Bézier qui définissent un carré dont la zone est approximativement la même que la zone du cercle. (Le fait de dessiner un carré avec la zone *exacte* comme un cercle donné est le problème géométrique classique qui ne peut pas être résolu par [le cercle](https://en.wikipedia.org/wiki/Squaring_the_circle).) Pour le rendu d’un carré avec des courbes de Bézier, les deux points de contrôle de chaque courbe sont identiques, et ils sont colinéaires avec les points de début et de fin, de sorte que la courbe Bézier est rendue sous la forme d’une ligne droite.

La troisième colonne du tableau correspond aux valeurs interpolées pour une animation. La page définit un minuteur pour 16 millisecondes, et le `PaintSurface` gestionnaire est appelé à ce taux :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

Les points sont interpolés en fonction de la valeur de l’oscillation sinusoïdale de `t` . Les points interpolés sont ensuite utilisés pour construire une série de quatre courbes de Bézier connectées. Voici l’animation en cours d’exécution :

[![Capture d’écran triple de la page de cercle](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Une telle animation serait impossible sans les courbes qui sont suffisamment flexibles pour être rendues à la fois comme arcs circulaires et en lignes droites.

La page **infini de Bézier** tire également parti de la capacité d’une courbe de Bézier à se rapprocher d’un arc de cercle. Voici le `PaintSurface` Gestionnaire de la [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

Il peut être intéressant de tracer ces coordonnées sur le papier du graphique pour voir comment elles sont liées. Le signe infini est centré autour du point (0, 0), et les deux boucles ont des centres de (– 150, 0) et (150, 0) et des rayons de 100. Dans la série de `CubicTo` commandes, vous pouvez voir les coordonnées X des points de contrôle qui prennent les valeurs de – 95 et-205 (ces valeurs sont : 150 plus et moins 55), 205 et 95 (150 plus et moins 55), ainsi que 250 et – 250 pour les côtés droit et gauche. La seule exception est lorsque le signe infini se coupe dans le centre. Dans ce cas, les points de contrôle ont des coordonnées avec une combinaison de 50 et de – 50 pour redresser la courbe près du centre.

Voici le signe infini :

[![Capture d’écran triple de la page d’infini de Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

Il est un peu plus lisse vers le centre que le signe infini rendu par la page d' **infini d’arc** parmi les [**trois façons de dessiner un**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) article en arc.

## <a name="the-quadratic-bzier-curve"></a>Courbe de Bézier quadratique

La courbe de Bézier quadratique n’a qu’un seul point de contrôle, et la courbe est définie par seulement trois points : le point de départ, le point de contrôle et le point de terminaison. Les équations paramétriques sont très similaires à la courbe de Bézier cubique, sauf que l’exposant le plus élevé est 2, de sorte que la courbe est un degré polynomial quadratique :

x (t) = (1 – t) ² x ₀ + 2T (1 – t) x ₁ + t ² x ₂

y (t) = (1 – t) ² y ₀ + 2T (1 – t) y ₁ + t ² y ₂

Pour ajouter une courbe de Bézier quadratique à un tracé, utilisez la [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) méthode ou la [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) surcharge avec des `x` coordonnées et distinctes `y` :

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Les méthodes ajoutent une courbe de la position actuelle à `point2` avec `point1` comme point de contrôle.

Vous pouvez expérimenter des courbes de Bézier quadratiques à l’aide de la page de **courbe quadratique** , qui est très similaire à la page de **courbe de Bézier** , sauf qu’elle ne comporte que trois points tactiles. Voici le `PaintSurface` gestionnaire dans le fichier code-behind [**QuadraticCurve.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

Et ici, il s’exécute :

[![Capture d’écran triple de la page de courbe quadratique](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

Les lignes en pointillés sont tangentes à la courbe au point de départ et au point de terminaison, et se rencontrent au point de contrôle.

La courbe de Bézier quadratique est bonne si vous avez besoin d’une courbe d’une forme générale, mais vous préférez la commodité d’un seul point de contrôle plutôt que deux. La courbe de Bézier quadratique est plus efficace que toute autre courbe. c’est pourquoi elle est utilisée en interne dans skia pour restituer des arcs elliptiques.

Toutefois, la forme d’une courbe de Bézier quadratique n’est pas elliptique, c’est pourquoi plusieurs Béziers quadratiques sont nécessaires pour rapprocher un arc elliptique. La Bézier quadratique est à la place un segment d’un Parabola.

## <a name="the-conic-bzier-curve"></a>Courbe de Bézier conique

La courbe de Bézier conique &mdash; également connue sous le nom de courbe de Bézier quadratique rationnelle &mdash; est un ajout relativement récent à la famille de courbes de Bézier. À l’instar de la courbe de Bézier quadratique, la courbe de Bézier quadratique rationnelle implique un point de départ, un point de terminaison et un point de contrôle. Mais la courbe de Bézier quadratique rationnelle requiert également une valeur de *pondération* . C’est ce qu’on appelle *Rational* quadratique, car les formules paramétriques impliquent des ratios.

Les équations paramétriques pour X et Y sont des ratios qui partagent le même dénominateur. Voici l’équation pour le dénominateur pour *t* allant de 0 à 1 et une valeur de poids de *w*:

d (t) = (1 – t) ² + 2Wt (1 – t) + t ²

En théorie, un Rational quadratique peut impliquer trois valeurs de pondération distinctes, une pour chacun des trois termes, mais celles-ci peuvent être simplifiées en une seule valeur de poids au terme du milieu.

Les équations paramétriques pour les coordonnées X et Y sont similaires aux équations paramétriques de la Bézier quadratique, sauf que le terme central comprend également la valeur de pondération, et que l’expression est divisée par le dénominateur :

x (t) = ((1 – t) ² x ₀ + 2Wt (1 – t) x ₁ + t ² x ₂)) ÷ d (t)

y (t) = ((1 – t) ² y ₀ + 2Wt (1 – t) y ₁ + t ² y ₂)) ÷ d (t)

Les courbes de Bézier quadratiques rationnelles sont également appelées *coniques* , car elles peuvent représenter exactement des segments d’une section conique &mdash; hyperbolique, de paraboliqueas, de ellipses et de cercles.

Pour ajouter une courbe de Bézier quadratique rationnelle à un tracé, utilisez la [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) méthode ou la [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) surcharge avec des `x` coordonnées et distinctes `y` :

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Notez le `weight` paramètre final.

La page **courbe conique** vous permet d’expérimenter ces courbes. La classe `ConicCurvePage` est dérivée de `InteractivePage`. Le fichier [**ConicCurvePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) instancie un `Slider` pour sélectionner une valeur de poids comprise entre – 2 et 2. Le fichier code-behind [**ConicCurvePage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) crée trois `TouchPoint` objets, et le `PaintSurface` Gestionnaire affiche simplement la courbe résultante avec les lignes tangentes aux points de contrôle :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

Ici, il s’exécute :

[![Capture d’écran triple de la page de courbe conique](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Comme vous pouvez le voir, le point de contrôle semble extraire la courbe vers davantage lorsque le poids est plus élevé. Lorsque la pondération est égale à zéro, la courbe devient une ligne droite à partir du point de départ jusqu’au point de terminaison.

En théorie, les pondérations négatives sont autorisées et provoquent le pliage de la courbe à partir du *point de contrôle* . Toutefois, les pondérations de-1 ou ci-dessous entraînent le dénominateur des équations paramétrées comme étant négatifs pour des valeurs particulières de *t*. Pour cette raison, il est probable que les poids négatifs sont ignorés dans les `ConicTo` méthodes. Le programme de **courbe conique** vous permet de définir des pondérations négatives, mais comme vous pouvez le voir en expérimentant, les pondérations négatives ont le même effet qu’un poids de zéro, et entraînent le rendu d’une ligne droite.

Il est très facile de dériver le point de contrôle et le poids pour utiliser la `ConicTo` méthode pour dessiner un arc de cercle jusqu’à un demi-cercle (mais sans l’inclure). Dans le diagramme suivant, les lignes tangentes des points de début et de fin se rencontrent au niveau du point de contrôle.

![Rendu conique d’un arc de cercle](beziers-images/conicarc.png)

Vous pouvez utiliser trigonométrique pour déterminer la distance du point de contrôle à partir du centre du cercle : il s’agit du rayon du cercle divisé par le cosinus de la moitié de l’angle α. Pour dessiner un arc de cercle entre les points de début et de fin, définissez le poids sur le même cosinus de la moitié de l’angle. Notez que si l’angle est de 180 degrés, les lignes de tangente ne sont jamais satisfaites et le poids est égal à zéro. Mais pour des angles inférieurs à 180 degrés, la mathématique fonctionne bien.

La page **arc de cercle conique** illustre cela. Le fichier [**ConicCircularArc. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) instancie un `Slider` pour la sélection de l’angle. Le `PaintSurface` Gestionnaire du fichier code-behind [**ConicCircularArc.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcule le point de contrôle et le poids :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

Comme vous pouvez le voir, il n’y a aucune différence visuelle entre le `ConicTo` chemin d’accès affiché en rouge et le cercle sous-jacent affiché pour référence :

[![Capture d’écran triple de la page de l’arc de cercle conique](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Mais définissez l’angle sur 180 degrés, et la mathématique échoue.

Dans ce cas, cela `ConicTo` ne prend pas en charge les pondérations négatives, car en théorie (en fonction des équations paramétrées), le cercle peut être complété par un autre appel à `ConicTo` avec les mêmes points, mais avec une valeur négative du poids. Cela permet de créer un cercle entier avec seulement deux `ConicTo` courbes en fonction de n’importe quel angle entre (mais sans inclure) zéro degré et 180 degrés.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
