---
title: Trois types de courbes Bézier
description: Cet article explique comment utiliser SkiaSharp pour restituer des courbes de Bézier cubiques, quadratiques et coniques dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 7802360c95d31f237cf69c69728cffe02ad47e5a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646231"
---
# <a name="three-types-of-bzier-curves"></a>Trois types de courbes Bézier

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorer l’utilisation de SkiaSharp pour restituer des courbes de Bézier cubiques, quadratiques et coniques_

La courbe de Bézier est nommée d’après la Pierre Bézier (1910 : 1999), ingénieur Français chez le constructeur automobile Renault, ce qui a utilisé la courbe pour la conception assistée par ordinateur de corps de la voiture.

Les courbes de Bézier sont connues pour être bien adaptées à la conception interactive: Ils se comportent &mdash; bien, en d’autres termes, il n’y a pas de singularités qui entraînent une courbe &mdash; infinie ou difficile, et elles sont généralement agréables à l’aspect esthétique:

![](beziers-images/beziersample.png "Une courbe de Bézier exemple")

Contours de caractère des polices basées sur l’ordinateur sont généralement définies avec des courbes de Bézier.

L’article de Wikipedia sur [ **courbe de Bézier** ](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contient des informations utiles. Le terme *courbe de Bézier* fait référence à une famille de courbes similaires. SkiaSharp prend en charge trois types de courbes de Bézier, appelés le *cubique*, le *quadratique*et le *conique*. Le conique est également connu sous le *quadratique rational*.

## <a name="the-cubic-bzier-curve"></a>La courbe de Bézier cubique

La courbe cubique est le type de courbe de Bézier considérer la plupart des développeurs lors de l’objet de courbes de Bézier qui s’affiche.

Vous pouvez ajouter une courbe de Bézier cubique à un `SKPath` à l’aide de l’objet le [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) méthode avec trois `SKPoint` paramètres, ou le [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) surcharge avec différentes `x` et `y` paramètres :

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La courbe commence au point actuel du contour. La courbe de Bézier cubique complète est définie par quatre points :

- point de départ : actuel point dans le profil, ou (0, 0) si `MoveTo` n’a pas été appelé
- premier point de contrôle : `point1` dans le `CubicTo` appeler
- deuxième point de contrôle : `point2` dans le `CubicTo` appeler
- point de terminaison : `point3` dans le `CubicTo` appeler

La courbe résultante commence au point de départ et se termine au point de terminaison. La courbe généralement ne passe pas par deux points de contrôle ; à la place les points de contrôle fonctionnent comme des aimants pour extraire la courbe vers eux.

La meilleure façon de faire une idée de la courbe de Bézier cubique est par expérimentation. C’est l’objectif de la **courbe de Bézier** page, qui dérive de `InteractivePage`. Le [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) fichier instancie le `SKCanvasView` et un `TouchEffect`. Le [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) fichier code-behind crée quatre `TouchPoint` objets dans son constructeur. Le `PaintSurface` Gestionnaire d’événements crée un `SKPath` pour restituer une courbe de Bézier selon les quatre `TouchPoint` objets et également dessine des lignes en pointillés de tangente à partir des points de contrôle pour les points de terminaison :

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

[![](beziers-images/beziercurve-small.png "Capture d’écran triple de la page de la courbe de Bézier")](beziers-images/beziercurve-large.png#lightbox "Triple capture d’écran de la page de la courbe de Bézier")

Point de vue mathématique, la courbe est une représentation polynomiale cubique. La courbe entre en intersection avec une ligne droite à trois points au maximum. Sur le point de départ, la courbe est toujours tangent à et, dans la même direction que, une ligne droite à partir du début pointez le premier point de contrôle. Au point de terminaison, la courbe est toujours tangent à et, dans la même direction que, une ligne droite à partir du deuxième contrôle pointez le point de terminaison.

La courbe de Bézier cubique est toujours limitée par un quadrilatère convexe reliant les quatre points. Il s’agit une *forme convexe*. Si les points de contrôle se trouvent sur la ligne droite entre le début et le point de terminaison, puis la courbe de Bézier effectue le rendu en tant qu’une ligne droite. Mais la courbe peut également traverser les lui-même, comme le montre la capture d’écran tiers.

Un contour de chemin d’accès peut contenir plusieurs courbes de Bézier cubiques connectées, mais la connexion entre deux courbes de Bézier cubiques est fluide uniquement si les trois points suivants sont colinéaires (autrement dit, se trouvent sur une ligne droite) :

- le deuxième point de contrôle de la courbe en premier
- le point de terminaison de la première courbe, qui est également le point de départ de la deuxième courbe
- le premier point de contrôle de la deuxième courbe

Dans l’article suivant sur [ **les données de chemin SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), vous allez découvrir une fonctionnalité permettant de faciliter la définition des courbes de Bézier connectées lisses.

Il est parfois utile de connaître les équations paramétriques sous-jacent qui restituent une courbe de Bézier cubique. Pour *t* comprises entre 0 et 1, les équations paramétriques sont les suivantes :

x(t) = (1 – t) ³x₀ + 3 t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3 t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

L’exposant le plus élevé de 3 confirme qu’il s’agit de POLYNÔMES cubiques. Il est facile de vérifier que quand `t` est égale à 0, le point (x₀, y₀), qui est le point de départ et à quel moment `t` est égale à 1, le point (x₃, y₃), qui est le point de terminaison. Près du point de départ (pour des valeurs faibles `t`), le premier point de contrôle (x₁, y₁) a un fort impact et de près le point de terminaison (les valeurs élevées de l ' ») le deuxième point de contrôle (x₂, y₂) a une répercussion importante.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Approximation de courbe de Bézier Arcs de cercle

Il est parfois pratique d’utiliser une courbe de Bézier pour restituer un arc de cercle. Une courbe de Bézier cubique peut se rapprocher un arc de cercle très bien jusqu'à un trimestre cercle, quatre courbes de Bézier connectées peuvent définir un cercle complet. Cette approximation est décrite dans les deux articles publiés il y a plus de 25 ans :

> Tor Dokken, et al., « Approximation satisfaisante de cercles en courbes de Bézier courbure continue, » *ordinateur assistée par géométrique conception 7* (1990), 33-41.

> Michael Goldapp, « Approximation d’Arcs par POLYNÔMES cubiques, » *conception géométrique 8 assistée par ordinateur* (1991), 227-238.

Le diagramme suivant illustre quatre points étiquetés `pto`, `pt1`, `pt2`, et `pt3` définissant une courbe de Bézier (indiquée en rouge) qui est une approximation d’un arc de cercle :

![](beziers-images/bezierarc45.png "Approximation d’un arc de cercle avec une courbe de Bézier")

Les lignes au début et les points de terminaison pour les points de contrôle sont tangente pour le cercle et la courbe de Bézier, et ils ont une longueur de *L*. Le premier article cité plus haut indique que la courbe de Bézier meilleures rapproche un arc de cercle lorsque cette longueur *L* est calculé comme suit :

L = 4 × tan(α / 4) / 3

L’illustration montre un angle de 45 degrés, donc L est égale à 0,265. Dans le code, cette valeur est multipliée par le rayon du cercle souhaité.

Le **Arc de cercle de Bézier** page vous permet de faire des essais avec la définition d’une courbe de Bézier pour estimer un arc de cercle pour les angles allant jusqu'à 180 degrés. Le [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) fichier instancie le `SKCanvasView` et un `Slider` pour la sélection de l’angle. Le `PaintSurface` Gestionnaire d’événements dans le [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) fichier code-behind utilise une transformation pour définir le point (0, 0) au centre de la zone de dessin. Dessine un cercle centré sur ce point de comparaison, elle calcule ensuite les deux points de contrôle de la courbe de Bézier :

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

Le début et points de terminaison (`point0` et `point3`) sont calculées selon les équations paramétriques normales pour le cercle. Étant donné que le cercle est centré à (0, 0), ces points peuvent également être traitées comme des vecteurs radiales à partir du centre du cercle à la circonférence. Les points de contrôle se trouvent sur les lignes de tangente au cercle, afin qu’ils soient perpendiculairement à ces vecteurs radiales. Un vecteur à un angle droit à un autre est simplement le vecteur d’origine avec les coordonnées X et Y échangées et qu’un d’eux apportées négatif.

Voici le programme s’exécute avec des angles différents :

[![](beziers-images/beziercirculararc-small.png "Capture d’écran triple de la page d’Arc de cercle de Bézier")](beziers-images/beziercirculararc-large.png#lightbox "Triple capture d’écran de la page d’Arc de cercle de Bézier")

Examinez attentivement la capture d’écran tiers, et vous verrez que la courbe de Bézier diffère notamment un demi-cercle lors de l’angle est de 180 degrés, mais l’écran iOS montre qu’il semble tenir un cercle trimestre parfaitement lorsque l’angle est de 90 degrés.

Il est facile de calculer les coordonnées de deux points de contrôle lorsque le quart de cercle est orienté à ceci :

![](beziers-images/bezierarc90.png "Approximation d’un quart de cercle avec une courbe de Bézier")

Si le rayon du cercle est de 100, puis *L* est 55, et c’est un nombre facile à mémoriser.

Le **mise au carré le cercle** page anime une figure entre un cercle et un carré. Le cercle est évaluée approximativement par quatre des courbes de Bézier dont les coordonnées sont affichées dans la première colonne de cette définition de tableau dans le [ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe :

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

La deuxième colonne contient les coordonnées de quatre courbes de Bézier qui définissent un carré dont la zone est approximativement identique à l’aire du cercle. (Dessin d’un carré avec la *exacte* zone comme un cercle donné est le problème géométrique insoluble classique de [mise au carré le cercle](https://en.wikipedia.org/wiki/Squaring_the_circle).) Pour le rendu d’un carré avec des courbes de Bézier, deux points de contrôle pour chaque courbe sont les mêmes, et ils sont colinéaires avec les points d’entrée et, donc la courbe de Bézier est restituée sous la forme d’une ligne droite.

La troisième colonne du tableau est pour les valeurs interpolées pour une animation. La page définit un minuteur pour 16 millisecondes et le `PaintSurface` gestionnaire est appelé à ce rythme :

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

Les points sont interpolés selon une valeur sinusoïdal OSCILLANTE de `t`. Les points interpolées sont ensuite utilisés pour construire une série de quatre courbes de Bézier connectées. Voici l’animation en cours d’exécution :

[![](beziers-images/squaringthecircle-small.png "Capture d’écran triple de la Squaring la page de cercle")](beziers-images/squaringthecircle-large.png#lightbox "Triple capture d’écran de la Squaring la page de cercle")

Une animation de ce type serait impossible sans les courbes de façon algorithmique suffisamment flexibles pour être rendus sous forme de lignes droites et à la fois des arcs de cercle.

Le **infini de Bézier** page tire également parti de la capacité d’une courbe de Bézier à estimer un arc de cercle. Voici le `PaintSurface` gestionnaire à partir de la [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe :

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

Il peut être un bon exercice pour tracer ces coordonnées sur le document de graphique pour voir comment elles sont liées. Le signe de l’infini est centré autour du point (0, 0), et les deux boucles disposent de centres de (–150, 0) et (150, 0) et de rayons de 100. Dans la série de `CubicTo` commandes, vous pouvez voir les coordonnées de points de contrôle sur les valeurs de –95 et –205 X (ces valeurs sont –150 plus et moins 55), 205 et 95 (150 plus et moins 55), ainsi que 250 et –250 pour les côtés gauche et droit. La seule exception est lorsque le signe de l’infini dépasse lui-même dans le centre. Dans ce cas, les points de contrôle ont des coordonnées avec une combinaison de 50 et -50 à Aplanissez la courbe près du centre.

Voici le signe de l’infini :

[![](beziers-images/bezierinfinity-small.png "Capture d’écran triple de la page de l’infini Bézier")](beziers-images/bezierinfinity-large.png#lightbox "Triple capture d’écran de la page de l’infini de Bézier")

Il est un peu plus lisse vers le centre que le signe de l’infini restitué par le **Arc infini** page à partir de la [ **trois moyens pour dessiner un Arc** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) article.

## <a name="the-quadratic-bzier-curve"></a>La courbe de Bézier quadratique

La courbe de Bézier quadratique a uniquement un point de contrôle et la courbe est définie par trois points : le point de départ, le point de contrôle et le point de terminaison. Les équations paramétriques sont très similaires à la courbe de Bézier cubique, sauf que l’exposant la plus élevée est 2, par conséquent, la courbe est une représentation polynomiale quadratique :

x(t) = (1 – t) ²x₀ + 2 t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2 t (1 – t) y₁ + t²y₂

Pour ajouter une courbe de Bézier à un chemin d’accès, utilisez le [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) méthode ou le [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) surcharge avec différentes `x` et `y` coordonnées :

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

Les méthodes d’ajoutent d’une courbe à partir de la position actuelle pour `point2` avec `point1` en tant que le point de contrôle.

Vous pouvez expérimenter des courbes de Bézier quadratiques avec le **courbe quadratique** page, qui est très similaire à la **courbe de Bézier** page sauf qu’il possède uniquement trois points tactiles. Voici le `PaintSurface` gestionnaire dans le [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) fichier code-behind :

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

Et ici il est en cours d’exécution :

[![](beziers-images/quadraticcurve-small.png "Capture d’écran triple de la page de la courbe quadratique")](beziers-images/quadraticcurve-large.png#lightbox "Triple capture d’écran de la page de la courbe quadratique")

Les lignes en pointillés sont la tangente de la courbe en le point de départ et le point de terminaison et répondre au niveau du point de contrôle.

La courbe de Bézier quadratique est bonne si vous avez besoin d’une courbe d’une forme en générale, mais vous préférez la commodité de simplement un point de contrôle au lieu de deux. La courbe de Bézier quadratique rend plus efficacement que n’importe quelle autre courbe, c’est pourquoi il est utilisé en interne dans Skia pour restituer des arcs elliptiques.

Toutefois, la forme d’une courbe de Bézier quadratique n’est pas elliptique, c’est pourquoi plusieurs points de Bézier quadratiques sont nécessaires pour estimer un arc elliptique. La courbe de Bézier quadratique est à la place un segment d’un parabole.

## <a name="the-conic-bzier-curve"></a>La courbe de Bézier conique

La courbe de Bézier conique &mdash; également connu sous la rational courbe de Bézier &mdash; est un ajout relativement récent de la famille des courbes de Bézier. Comme la courbe de Bézier quadratique, la courbe de Bézier quadratique rational implique un point de départ, un point de terminaison et un point de contrôle. Mais la courbe de Bézier quadratique rational nécessite également un *poids* valeur. Elle est appelée un *rational* quadratique étant donné que les formules paramétriques impliquent des rapports.

Les équations paramétriques pour X et Y sont des rapports qui partagent le même dénominateur. Voici l’équation pour le dénominateur pour *t* allant de 0 à 1 et une valeur de poids de *w*:

Ät = (1 – t) ² + 2wt(1 – t) + t²

En théorie, un type quadratic rational peut impliquer des trois valeurs de poids distincts, un pour chacune des trois conditions, mais ceux-ci peuvent être simplifiées pour qu’une seule valeur de poids du terme intermédiaire.

Les équations paramétriques des coordonnées X et Y sont similaires pour les équations paramétriques pour la courbe de Bézier quadratique, à ceci près que le terme intermédiaire inclut également la valeur de poids et de l’expression est divisée par le dénominateur :

x(t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ Ät

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ Ät

Courbes de Bézier quadratiques Rational sont également appelés *conics* , car ils peuvent représenter exactement les segments de n’importe quelle section conique &mdash; hyperboles, paraboles ou, des ellipses et des cercles.

Pour ajouter une courbe de Bézier rational à un chemin d’accès, utilisez le [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) méthode ou le [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) surcharge avec différentes `x` et `y` coordonnées :

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Notez que la dernière `weight` paramètre.

Le **courbe conique** page vous permet de faire des essais avec ces courbes. La classe `ConicCurvePage` dérive de la classe `InteractivePage`. Le [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) fichier instancie un `Slider` pour sélectionner une valeur de poids entre – 2 et 2. Le [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) fichier code-behind crée trois `TouchPoint` objets et le `PaintSurface` gestionnaire affiche simplement la courbe résultante avec les lignes de tangente au contrôle points suivants :

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

[![](beziers-images/coniccurve-small.png "Capture d’écran triple de la page de la courbe conique")](beziers-images/coniccurve-large.png#lightbox "Triple capture d’écran de la page de la courbe conique")

Comme vous pouvez le voir, le point de contrôle semble pour extraire la courbe vers elle plus lorsque le poids est supérieur. Lorsque le poids est égal à zéro, la courbe devient une ligne droite entre le point de départ pour le point de terminaison.

En théorie, poids négatifs sont autorisés et provoquer la courbe à plier *disparaître* à partir du point de contrôle. Toutefois, des poids de -1 ou au-dessous de cause le dénominateur dans les équations paramétriques négatif pour des valeurs particulières de *t*. Probablement pour cette raison, les pondérations négatif sont ignorées dans le `ConicTo` méthodes. Le **courbe conique** programme vous permet de définir le poids négatif, mais comme vous pouvez le voir en expérimentant, poids négatif ont le même effet en tant qu’un poids égal à zéro et entraînent une ligne droite doit être restitué.

Il est très facile de dériver le point de contrôle et le poids à utiliser le `ConicTo` pour dessiner un arc de cercle jusqu'à (méthode) (mais non compris) un demi-cercle. Dans le diagramme suivant, les tangentes au début et les points de terminaison répondent au niveau du point de contrôle.

![](beziers-images/conicarc.png "Un rendu de l’arc conique d’un arc de cercle")

Vous pouvez utiliser trigonométrique pour déterminer la distance du point de contrôle à partir du centre du cercle: Il s’agit du rayon du cercle divisé par le cosinus de la moitié de l’angle α. Pour dessiner un arc circulaire entre le début et les points de terminaison, définissez le poids à ce même cosinus de la moitié de l’angle. Notez que si l’angle est de 180 degrés, puis les tangentes ne rencontrez jamais et que le poids est égal à zéro. Mais pour les angles inférieur à 180 degrés, les calculs fonctionnement correctement.

Le **Arc circulaire conique** page illustre cela. Le [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) fichier instancie un `Slider` pour la sélection de l’angle. Le `PaintSurface` gestionnaire dans le [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) fichier code-behind calcule le point de contrôle et le poids :

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

Comme vous pouvez le voir, il n’existe aucune différence visuelle entre les `ConicTo` chemin d’accès indiqué en rouge et le cercle sous-jacent affichés pour référence :

[![](beziers-images/coniccirculararc-small.png "Capture d’écran triple de la page d’Arc de cercle conique")](beziers-images/coniccirculararc-large.png#lightbox "Triple capture d’écran de la page d’Arc de cercle conique")

Mais la valeur est l’angle de 180 degrés et l’échec de mathématiques.

Il est malheureux dans ce cas que `ConicTo` ne prend pas en charge les pondérations négatif, car en théorie (selon les équations paramétriques), le cercle peut être effectué avec un autre appel à `ConicTo` avec les mêmes points, mais une valeur négative du poids. Cela permet la création d’un cercle complet avec seulement deux `ConicTo` courbes selon n’importe quel angle entre (mais sans inclure) zéro degrés et 180 degrés.


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
