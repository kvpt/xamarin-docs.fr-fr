---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4eea7d500876793357113453493fa2fe2ede6cc4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140012"
---
# <a name="three-ways-to-draw-an-arc"></a>Trois façons de dessiner un arc

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser SkiaSharp pour définir des arcs de trois façons différentes_

Un arc est une courbe sur la circonférence d’une ellipse, telle que les parties arrondies de ce signe infini :

![Signe infini](arcs-images/arcsample.png)

Malgré la simplicité de cette définition, il n’existe aucun moyen de définir une fonction de dessin d’arc qui répond à tous les besoins et, par conséquent, aucun consensus entre les systèmes graphiques de la meilleure façon de dessiner un arc. Pour cette raison, la `SKPath` classe ne se limite pas à une seule approche.

`SKPath`définit une [`AddArc`](xref:SkiaSharp.SKPath.AddArc*) méthode, cinq [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) méthodes différentes et deux méthodes relatives [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*) . Ces méthodes se répartissent en trois catégories, qui représentent trois approches très différentes pour la spécification d’un arc. Celui que vous utilisez dépend des informations disponibles pour définir l’arc et de la façon dont cet arc s’ajuste aux autres graphiques que vous dessinez.

## <a name="the-angle-arc"></a>L’arc d’angle

L’approche à angle angulaire pour dessiner des arcs requiert que vous spécifiiez un rectangle qui délimite une ellipse. L’arc sur la circonférence de cette ellipse est indiqué par des angles à partir du centre de l’ellipse qui indiquent le début de l’arc et sa longueur. Deux méthodes différentes dessinent des arcs angulaires. Il s’agit de la [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) méthode et de la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) méthode :

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Ces méthodes sont identiques aux méthodes Android [`AddArc`](xref:Android.Graphics.Path.AddArc*) et [ `ArcTo` ] XREF : Android. Graphics. Path. ArcTo *). La [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) méthode IOS est similaire, mais elle est limitée aux arcs sur la circonférence d’un cercle plutôt que généralisée à une ellipse.

Les deux méthodes commencent par une `SKRect` valeur qui définit à la fois l’emplacement et la taille d’une ellipse :

![Ovale qui commence un arc d’angle](arcs-images/anglearcoval.png)

L’arc est une partie de la circonférence de cette ellipse.

L' `startAngle` argument est un angle horaire, en degrés, par rapport à une ligne horizontale dessinée à partir du centre de l’ellipse vers la droite. L' `sweepAngle` argument est relatif à `startAngle` . Voici les `startAngle` `sweepAngle` valeurs et 60 degrés et 100 degrés, respectivement :

![Angles qui définissent un arc d’angle](arcs-images/anglearcangles.png)

L’arc commence à l’angle de départ. Sa longueur est régie par l’angle de balayage. L’arc est affiché ici en rouge :

![L’arc d’angle en surbrillance](arcs-images/anglearchighlight.png)

La courbe ajoutée au tracé avec la `AddArc` méthode ou `ArcTo` est simplement la partie de la circonférence de l’ellipse :

![L’arc d’angle par lui-même](arcs-images/anglearc.png)

Les `startAngle` `sweepAngle` arguments ou peuvent être négatifs : l’arc est dans le sens des aiguilles d’une montre pour les valeurs positives de et dans le `sweepAngle` sens inverse des aiguilles d’une montre pour les valeurs négatives.

Toutefois, `AddArc` ne définit *pas* un contour fermé. Si vous appelez `LineTo` après `AddArc` , une ligne est dessinée de la fin de l’arc jusqu’au point dans la `LineTo` méthode, et il en est de même pour `ArcTo` .

`AddArc`démarre automatiquement un nouveau contour et est fonctionnellement équivalent à un appel à `ArcTo` avec un argument final de `true` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Ce dernier argument est appelé `forceMoveTo` et provoque en fait un `MoveTo` appel au début de l’arc. Cela commence un nouveau contour. Ce n’est pas le cas avec un dernier argument `false` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Cette version de `ArcTo` dessine une ligne à partir de la position actuelle jusqu’au début de l’arc. Cela signifie que l’ARC peut être situé au milieu d’un contour plus grand.

La page **arc d’angle** vous permet d’utiliser deux curseurs pour spécifier les angles de début et de balayage. Le fichier XAML instancie deux `Slider` éléments et un `SKCanvasView` . Le `PaintCanvas` Gestionnaire du fichier [**AngleArcPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) dessine l’ovale et l’arc à l’aide de deux `SKPaint` objets définis en tant que champs :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

Comme vous pouvez le voir, l’angle de début et l’angle de balayage peuvent utiliser des valeurs négatives :

[![Capture d’écran triple de la page de l’arc angulaire](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

Cette approche de la génération d’un arc est le plus simple et il est facile de dériver les équations paramétrées qui décrivent l’arc. En connaissant la taille et l’emplacement de l’ellipse, ainsi que les angles de début et de balayage, les points de départ et de fin de l’ARC peuvent être calculés à l’aide de la méthode trigonométrique simple :

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

La `angle` valeur est `startAngle` ou `startAngle + sweepAngle` .

L’utilisation de deux angles pour définir un arc est préférable dans les cas où vous connaissez la longueur angulaire de l’arc que vous souhaitez dessiner, par exemple pour créer un graphique à secteurs. La page de **graphique à secteurs éclatés** illustre cela. La [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) classe utilise une classe interne pour définir des données et des couleurs fabriquées :

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

Le `PaintSurface` Gestionnaire parcourt d’abord les éléments pour calculer un `totalValues` nombre. À partir de cela, il peut déterminer la taille de chaque élément comme la fraction du total et le convertir en angle :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

Un nouvel `SKPath` objet est créé pour chaque secteur. Le chemin d’accès se compose d’une ligne à partir du centre, puis d’un `ArcTo` pour dessiner l’arc et d’une autre ligne vers les résultats du centre de l' `Close` appel. Ce programme affiche les secteurs « éclatés » en les déplaçant tous à partir du centre de 50 pixels. Cette tâche requiert un vecteur dans le sens du point médian de l’angle de balayage pour chaque tranche :

[![Capture d’écran triple de la page de graphique à secteurs éclatés](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

Pour voir à quoi il ressemble sans l’explosion, commentez simplement l' `Translate` appel :

[![Capture d’écran triple de la page de graphique à secteurs éclatés sans explosion](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>Arc tangente

Le deuxième type d’arc pris en charge par `SKPath` est l' *arc tangente*, donc appelé, car l’arc est la circonférence d’un cercle tangent à deux lignes connectées.

Un arc tangente est ajouté à un chemin d’accès avec un appel à la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) méthode avec deux `SKPoint` paramètres, ou la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) surcharge avec des `Single` paramètres distincts pour les points :

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Cette `ArcTo` méthode est similaire à la [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) fonction PostScript (page 532) et à la [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) méthode iOS.

La `ArcTo` méthode implique trois points :

- Le point actuel du contour, ou le point (0, 0) si `MoveTo` n’a pas été appelé
- Le premier argument point de la `ArcTo` méthode, appelé *point d’angle*
- Le deuxième argument point de `ArcTo` , appelé *point de destination*:

![Trois points qui commencent un arc tangent](arcs-images/tangentarcthreepoints.png)

Ces trois points définissent deux lignes connectées :

![Lignes reliant les trois points d’un arc tangent](arcs-images/tangentarcconnectinglines.png)

Si les trois points sont colinéaires &mdash; , s’ils se trouvent sur la même ligne droite, &mdash; aucun arc n’est dessiné.

La `ArcTo` méthode comprend également un `radius` paramètre. Cela définit le rayon d’un cercle :

![Cercle d’un arc tangente](arcs-images/tangentarccircle.png)

L’arc tangente n’est pas généralisé pour une ellipse.

Si les deux lignes se rencontrent à un angle quelconque, ce cercle peut être inséré entre ces lignes pour qu’elles soient tangentes aux deux lignes :

![Cercle de l’arc tangente entre les deux lignes](arcs-images/tangentarctangentcircle.png)

La courbe qui est ajoutée au contour ne touche pas l’un des points spécifiés dans la `ArcTo` méthode. Il se compose d’une ligne droite entre le point actuel et le premier point tangent, et d’un arc qui se termine au deuxième point tangente, indiqué ici en rouge :

![Arc tangente en surbrillance entre les deux lignes](arcs-images/tangentarchighlight.png)

Voici la ligne droite et l’arc qui sont ajoutés au contour :

![Arc tangente en surbrillance entre les deux lignes](arcs-images/tangentarc.png)

Le contour peut être poursuivi à partir du deuxième point tangent.

La page **arc tangente** vous permet d’expérimenter l’arc tangente. Il s’agit de la première des nombreuses pages qui dérivent de [`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs) , qui définit quelques `SKPaint` objets pratiques et effectue le `TouchPoint` traitement :

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

La classe `TangentArcPage` dérive de la classe `InteractivePage`. Le constructeur dans le fichier [**TangentArcPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) est chargé d’instancier et d’initialiser le `touchPoints` tableau, et de définir `baseCanvasView` (dans `InteractivePage` ) l' `SKCanvasView` objet instancié dans le fichier [**TangentArcPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) :

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Le `PaintSurface` gestionnaire utilise la `ArcTo` méthode pour dessiner l’arc en fonction des points tactiles et a `Slider` , mais il calcule également algorithmiquement le cercle sur lequel l’angle est basé :

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
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
}
```

Voici la page de l' **arc tangente** qui s’exécute :

[![Capture d’écran triple de la page de l’arc tangente](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

L’arc tangente est idéal pour créer des angles arrondis, tels qu’un rectangle arrondi. Étant donné que `SKPath` contient déjà une `AddRoundedRect` méthode, la page **heptagon arrondie** montre comment utiliser `ArcTo` pour arrondir les angles d’un polygone à sept faces. (Le code est généralisé pour tout polygone normal.)

Le `PaintSurface` Gestionnaire de la [`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) classe contient une `for` boucle pour calculer les coordonnées des sept sommets du heptagon, et une seconde pour calculer les milieux des sept côtés à partir de ces sommets. Ces points centraux sont ensuite utilisés pour construire le chemin d’accès :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

Voici le programme en cours d’exécution :

[![Capture d’écran triple de la page heptagon arrondie](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>L’arc elliptique

L’arc elliptique est ajouté à un chemin d’accès avec un appel à la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) méthode qui a deux `SKPoint` paramètres, ou la [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) surcharge avec des coordonnées X et Y distinctes :

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

L’arc elliptique est cohérent avec l' [arc elliptique](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) inclus dans Scalable Vector Graphics (SVG) et la [`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) classe plateforme Windows universelle.

Ces `ArcTo` méthodes dessinent un arc entre deux points, qui sont le point actuel du contour et le dernier paramètre de la `ArcTo` méthode (le `xy` paramètre ou les `x` paramètres et distincts `y` ) :

![Deux points définissant un arc elliptique](arcs-images/ellipticalarcpoints.png)

Le premier paramètre point de la `ArcTo` méthode ( `r` , ou `rx` et `ry` ) n’est pas un point du tout, mais spécifie à la place le rayon horizontal et le rayon vertical d’une ellipse.

![Ellipse qui a défini un arc elliptique](arcs-images/ellipticalarcellipse.png)

Le `xAxisRotate` paramètre est le nombre de degrés dans le sens des aiguilles d’une montre pour faire pivoter cette ellipse :

![Ellipse inclinée qui a défini un arc elliptique](arcs-images/ellipticalarctiltedellipse.png)

Si cette ellipse inclinée est ensuite positionnée de façon à ce qu’elle touche les deux points, les points sont reliés par deux arcs différents :

![Premier jeu d’arcs elliptiques](arcs-images/ellipticalarcellipse1.png)

Ces deux arcs peuvent être distingués de deux façons : l’arc supérieur est plus grand que l’arc inférieur, et à mesure que l’arc est dessiné de gauche à droite, l’arc supérieur est dessiné dans le sens des aiguilles d’une montre, tandis que l’arc inférieur est dessiné dans le sens inverse des aiguilles d’une montre.

Il est également possible d’ajuster l’ellipse entre les deux points d’une autre façon :

![Deuxième jeu d’arcs elliptiques](arcs-images/ellipticalarcellipse2.png)

Il y a maintenant un arc plus petit en haut qui est dessiné dans le sens des aiguilles d’une montre et un arc plus grand en bas qui est dessiné dans le sens inverse des aiguilles d’une montre.

Ces deux points peuvent donc être reliés par un arc défini par l’ellipse inclinée d’un total de quatre façons :

![Les quatre arcs elliptiques](arcs-images/ellipticalarccolors.png)

Ces quatre arcs sont distingués par les quatre combinaisons des [`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize) [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) arguments de type d’énumération et à la `ArcTo` méthode :

- rouge : SKPathArcSize. large et SKPathDirection. dans le sens des aiguilles d’une montre
- vert : SKPathArcSize. Small et SKPathDirection. dans le sens des aiguilles d’une montre
- bleu : SKPathArcSize. Small et SKPathDirection. dans le sens inverse
- Magenta : SKPathArcSize. large et SKPathDirection. dans le sens inverse

Si les ellipses inclinées ne sont pas assez grandes pour s’ajuster aux deux points, elles sont uniformément mises à l’échelle jusqu’à ce qu’elles soient suffisamment grandes. Seuls deux arcs uniques connectent les deux points dans ce cas. Elles peuvent être distinguées par le `SKPathDirection` paramètre.

Bien que cette approche de la définition d’un arc soit complexe lors de la première rencontre, c’est la seule approche qui permet de définir un arc avec une ellipse pivotée, et c’est souvent l’approche la plus simple lorsque vous devez intégrer des arcs à d’autres parties du contour.

La page **arc elliptique** vous permet de définir de manière interactive les deux points, ainsi que la taille et la rotation de l’ellipse. La `EllipticalArcPage` classe dérive de `InteractivePage` , et le `PaintSurface` Gestionnaire du fichier code-behind [**EllipticalArcPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) dessine les quatre arcs :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

Ici, il s’exécute :

[![Capture d’écran triple de la page de l’arc elliptique](arcs-images/ellipticalarc-small.png)](arcs-images/ellipticalarc-large.png#lightbox)

La page d' **infini d’arc** utilise l’arc elliptique pour dessiner un signe infini. Le signe infini est basé sur deux cercles avec des rayons de 100 unités séparées par 100 unités :

![Deux cercles](arcs-images/infinitycircles.png)

Deux lignes se croisant sont tangentes aux deux cercles :

![Deux cercles avec des lignes tangentes](arcs-images/infinitycircleslines.png)

Le signe infini est une combinaison de parties de ces cercles et des deux lignes. Pour utiliser l’arc elliptique pour dessiner le signe infini, les coordonnées où les deux lignes sont tangentes aux cercles doivent être déterminées.

Construisez un rectangle droit dans l’un des cercles :

![Deux cercles avec des lignes tangentes et un cercle incorporé](arcs-images/infinitytriangle.png)

Le rayon du cercle est de 100 unités et l’hypoténuse du triangle est de 150 unités, donc l’angle α est l’arc sinus (sinus inverse) de 100 divisé par 150, ou 41,8 degrés. La longueur de l’autre côté du triangle est 150 fois le cosinus de 41,8 degrés, ou 112, qui peut également être calculé par le Pythagorean.

Les coordonnées du point tangente peuvent ensuite être calculées à l’aide des informations suivantes :

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

Les quatre points de tangente sont tous nécessaires pour dessiner un signe infini centré sur le point (0,0) avec des rayons de cercle de 100 :

![Deux cercles avec des lignes tangentes et des coordonnées](arcs-images/infinitycoordinates.png)

Le `PaintSurface` Gestionnaire de la [`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) classe positionne le signe infini de sorte que le point (0,0) soit positionné au centre de la page, et ajuste le chemin d’accès à la taille de l’écran :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
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

Le code utilise la `Bounds` propriété de `SKPath` pour déterminer les dimensions du sinus infini pour l’adapter à la taille du canevas :

[![Capture d’écran triple de la page d’infini d’arc](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

Le résultat semble un peu petit, ce qui suggère que la `Bounds` propriété de `SKPath` signale une taille supérieure à celle du chemin.

En interne, skia se rapproche de l’arc en utilisant plusieurs courbes de Bézier quadratiques. Ces courbes (comme vous le verrez dans la section suivante) contiennent des points de contrôle qui régissent la façon dont la courbe est dessinée, mais qui ne font pas partie de la courbe rendue. La `Bounds` propriété comprend ces points de contrôle.

Pour obtenir un ajustement plus étroit, utilisez la `TightBounds` propriété, qui exclut les points de contrôle. Voici le programme en cours d’exécution en mode paysage et l’utilisation `TightBounds` de la propriété pour obtenir les limites du chemin d’accès :

[![Capture d’écran triple de la page d’infini d’arc avec limites étroites](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

Bien que les connexions entre les arcs et les lignes droites soient mathématiquement lisses, le passage d’un arc à un autre peut sembler un peu brusque. Un meilleur signe infini est présenté dans l’article suivant sur [**trois types de courbes de Bézier**](beziers.md).

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
