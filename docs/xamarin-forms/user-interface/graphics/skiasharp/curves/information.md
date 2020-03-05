---
title: Informations et énumération de tracés
description: Cet article explique comment obtenir des informations sur les chemins d’accès SkiaSharp et énumérer le contenu et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 6f4f4e6253c14d86e2057f13d6232a07a83b4d26
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "78291765"
---
# <a name="path-information-and-enumeration"></a>Informations et énumération de tracés

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtenir des informations sur les chemins et énumérer le contenu_

La classe [`SKPath`](xref:SkiaSharp.SKPath) définit plusieurs propriétés et méthodes qui vous permettent d’obtenir des informations sur le chemin d’accès. Les propriétés [`Bounds`](xref:SkiaSharp.SKPath.Bounds) et [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) (et les méthodes associées) obtiennent les dimensions métriques d’un chemin d’accès. La méthode [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) vous permet de déterminer si un point particulier se trouve dans un chemin d’accès.

Il est parfois utile de déterminer la longueur totale de toutes les lignes et courbes qui forment un chemin d’accès. Le calcul de cette longueur n’est pas une tâche algorithmique, de sorte qu’une classe entière nommée [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) lui est consacrée.

Il est parfois utile d’obtenir les opérations de dessin et la points qui composent un chemin d’accès. Dans un premier temps, cette fonctionnalité peut sembler inutile : Si votre programme a créé le chemin d’accès, le programme sait déjà le contenu. Toutefois, vous avez vu que les chemins d’accès peuvent également être créés par des [effets de tracés](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) et en convertissant des [chaînes de texte en chemins d'](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)accès. Vous pouvez également obtenir les dessin opérations et les points qui composent ces chemins d’accès. Il est possible d’appliquer une transformation algorithmique à tous les points, par exemple, pour placer le texte autour d’un hémisphère :

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

## <a name="getting-the-path-length"></a>En obtenant la longueur de chemin d’accès

Dans les [**chemins d’accès et le texte**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) de l’article, vous avez vu comment utiliser la méthode [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) pour dessiner une chaîne de texte dont la ligne de base suit le cours d’un tracé. Mais que se passe-t-il si vous souhaitez la taille du texte de manière à s’ajuster avec précision le chemin d’accès ? Il est facile de dessiner du texte autour d’un cercle, car la circonférence d’un cercle est simple à calculer. Mais la circonférence d’une ellipse ou la longueur d’une courbe de Bézier n’est pas si simple.

La classe [`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure) peut vous aider. Le [constructeur](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) accepte un argument `SKPath`, et la propriété [`Length`](xref:SkiaSharp.SKPathMeasure.Length) révèle sa longueur.

Cette classe est illustrée dans l’exemple de **longueur de chemin d’accès** , qui est basé sur la page de **courbe de Bézier** . Le fichier [**PathLengthPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) dérive de `InteractivePage` et comprend une interface tactile :

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

Le fichier code-behind [**PathLengthPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) vous permet de déplacer quatre points tactiles pour définir les points de terminaison et les points de contrôle d’une courbe de Bézier cubique. Trois champs définissent une chaîne de texte, un objet `SKPaint` et une largeur calculée du texte :

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

Le champ `baseTextWidth` est la largeur du texte en fonction d’un paramètre `TextSize` de 10.

Le gestionnaire de `PaintSurface` dessine la courbe de Bézier, puis redimensionne le texte en fonction de la longueur maximale :

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

La propriété `Length` de l’objet `SKPathMeasure` nouvellement créé obtient la longueur du chemin d’accès. La longueur du chemin d’accès est divisée par la valeur `baseTextWidth` (qui est la largeur du texte en fonction d’une taille de texte de 10), puis multipliée par la taille du texte de base de 10. Le résultat est une nouvelle taille de texte pour l’affichage du texte le long de ce chemin d’accès :

[![](information-images/pathlength-small.png "Triple screenshot of the Path Length page")](information-images/pathlength-large.png#lightbox "Triple screenshot of the Path Length page")

Plus la courbe de Bézier est plus ou moins longtemps, vous pouvez voir la taille du texte changent.

## <a name="traversing-the-path"></a>Parcourir le chemin d’accès

`SKPathMeasure` pouvez faire plus que mesurer simplement la longueur du chemin d’accès. Pour toute valeur comprise entre zéro et la longueur du chemin d’accès, un objet `SKPathMeasure` peut obtenir la position sur le tracé et la tangente à la courbe path à ce stade. La tangente est disponible sous forme de vecteur sous la forme d’un objet `SKPoint` ou sous forme de rotation encapsulée dans un objet `SKMatrix`. Voici les méthodes de `SKPathMeasure` qui obtiennent ces informations de manière variée et flexible :

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Les membres de l’énumération [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) sont :

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

La page à **demi-tube Unicycle** anime un chiffre en forme de bâton sur un unicycle qui semble passer en arrière et en arrière sur une courbe de Bézier cubique :

[![](information-images/unicyclehalfpipe-small.png "Triple screenshot of the Unicycle Half-Pipe page")](information-images/unicyclehalfpipe-large.png#lightbox "Triple screenshot of the Unicycle Half-Pipe page")

L’objet `SKPaint` utilisé pour le détourage du demi-canal et du Unicycle est défini en tant que champ dans la classe `UnicycleHalfPipePage`. Également défini est l’objet `SKPath` pour Unicycle :

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

La classe contient les substitutions standard des méthodes `OnAppearing` et `OnDisappearing` pour l’animation. Le gestionnaire de `PaintSurface` crée le chemin d’accès pour le demi-canal, puis le dessine. Un objet `SKPathMeasure` est ensuite créé sur la base de ce chemin d’accès :

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

Le gestionnaire de `PaintSurface` calcule une valeur de `t` qui va de 0 à 1 toutes les cinq secondes. Il utilise ensuite la fonction `Math.Cos` pour le convertir en une valeur de `t` qui est comprise entre 0 et 1 et de retour à 0, où 0 correspond au Unicycle au début en haut à gauche, tandis que 1 correspond au Unicycle en haut à droite. La fonction cosinus provoque la vitesse à être plus lente en haut du canal et le plus rapide en bas.

Notez que cette valeur de `t` doit être multipliée par la longueur du chemin d’accès pour le premier argument de `GetMatrix`. La matrice est ensuite appliquée à l’objet `SKCanvas` pour dessiner le chemin Unicycle.

## <a name="enumerating-the-path"></a>Le chemin d’accès de l’énumération des

Deux classes incorporées de `SKPath` vous permettent d’énumérer le contenu d’un chemin d’accès. Ces classes sont [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) et [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator). Les deux classes sont très similaires, mais `SKPath.Iterator` peuvent éliminer des éléments dans le chemin d’accès avec une longueur nulle, ou se rapprocher d’une longueur nulle. Le `RawIterator` est utilisé dans l’exemple ci-dessous.

Vous pouvez obtenir un objet de type `SKPath.RawIterator` en appelant la méthode [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) de `SKPath`. L’énumération via le chemin d’accès s’effectue en appelant à plusieurs reprises la méthode [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) . Transmettez-le à un tableau de quatre valeurs `SKPoint` :

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

La méthode `Next` retourne un membre du type énumération [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) . Ces valeurs indiquent la commande de dessin spécifique dans le chemin d’accès. Le nombre de points valides inséré dans le tableau dépend de ce verbe :

- `Move` avec un point unique
- `Line` avec deux points
- `Cubic` avec quatre points
- `Quad` avec trois points
- `Conic` avec trois points (et également appeler la méthode [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) pour le poids)
- `Close` avec un point
- `Done`

Le verbe de `Done` indique que l’énumération de chemin est terminée.

Notez qu’il n’y a pas de verbes de `Arc`. Cela indique que tous les arcs sont convertis en courbes de Bézier lors de l’ajout pour le chemin d’accès.

Certaines des informations contenues dans le tableau `SKPoint` sont redondantes. Par exemple, si un verbe `Move` est suivi d’un verbe `Line`, le premier des deux points qui accompagnent le `Line` est le même que le point de `Move`. Dans la pratique, cette redondance est très utile. Lorsque vous recevez un verbe de `Cubic`, il est accompagné des quatre points qui définissent la courbe de Bézier cubique. Vous n’avez pas besoin de conserver la position actuelle, établie par le verbe précédent.

Toutefois, le verbe problématique est `Close`. Cette commande dessine une ligne droite à partir de la position actuelle jusqu’au début du contour établi précédemment par la commande `Move`. Dans l’idéal, le verbe `Close` doit fournir ces deux points plutôt qu’un seul point. Ce qui est pire, c’est que le point accompagnant le verbe `Close` est toujours (0,0). Lorsque vous énumérez un chemin d’accès, vous devrez probablement conserver le point de `Move` et la position actuelle.

## <a name="enumerating-flattening-and-malforming"></a>L’énumération, mise à plat et Malforming

Il est parfois souhaitable d’appliquer une algorithmique transformer un chemin d’accès au malform d’une certaine façon :

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

La plupart de ces lettres sont constitués de lignes droites, mais ces lignes droites ont apparemment été détournés en courbes. Comment est-ce possible ?

La clé est que les lignes droites d’origine sont répartis en une série de lignes droites plus petits. Ces droites individuelles plus petites peuvent ensuite être manipulées de différentes façons pour former une courbe.

Pour faciliter ce processus, l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contient une classe [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) statique avec une méthode `Interpolate` qui décompose une ligne droite en plusieurs lignes courtes qui ne représentent qu’une seule unité de longueur. En outre, la classe contient plusieurs méthodes qui convertissent les trois types de courbes de Bézier en une série de petits traits qui se rapprochent de la courbe. (Les formules paramétriques ont été présentées dans l’article [**trois types de courbes de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Ce processus est appelé _aplatissement_ de la courbe :

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Toutes ces méthodes sont référencées à partir de la méthode d’extension `CloneWithTransform` également incluses dans cette classe et illustrées ci-dessous. Cette méthode clone un chemin d’accès en énumérant les commandes de chemin d’accès et en créant un nouveau chemin d’accès en fonction des données. Toutefois, le nouveau chemin d’accès se compose uniquement d' `MoveTo` et d’appels de `LineTo`. Toutes les lignes droites et courbes sont réduites à une série de lignes minuscules.

Lors de l’appel de `CloneWithTransform`, vous transmettez à la méthode un `Func<SKPoint, SKPoint>`, qui est une fonction avec un paramètre `SKPaint` qui retourne une valeur `SKPoint`. Cette fonction est appelée pour chaque point d’appliquer une transformation algorithmique personnalisée :

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

Étant donné que le chemin d’accès cloné est réduite à des lignes droites minuscules, la fonction de transformation a la possibilité de conversion de lignes droites en courbes.

Notez que la méthode conserve le premier point de chaque contour dans la variable appelée `firstPoint` et la position actuelle après chaque commande de dessin dans la variable `lastPoint`. Ces variables sont nécessaires pour construire la ligne de fermeture finale lorsqu’un verbe de `Close` est rencontré.

L’exemple **GlobularText** utilise cette méthode d’extension pour habiller en apparence le texte autour d’un hémisphère dans un effet 3D :

[![](information-images/globulartext-small.png "Triple screenshot of the Globular Text page")](information-images/globulartext-large.png#lightbox "Triple screenshot of the Globular Text page")

Le constructeur de classe [`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) effectue cette transformation. Il crée un objet `SKPaint` pour le texte, puis obtient un objet `SKPath` à partir de la méthode `GetTextPath`. Il s’agit du chemin d’accès passé à la méthode d’extension `CloneWithTransform`, ainsi qu’à une fonction de transformation :

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

La fonction Transform calcule d’abord deux valeurs nommées `longitude` et `latitude` qui vont de – π/2 en haut et à gauche du texte, à π/2 à droite et en bas du texte. La plage de ces valeurs n’est pas satisfaisante visuellement, afin qu’ils sont réduits en multipliant par la valeur 0,75. (Essayez le code sans ces ajustements. Le texte devient trop obscur aux pôles Nord et sud et trop étroit en face des côtés.) Ces coordonnées sphériques tridimensionnelles sont converties en `x` à deux dimensions et `y` coordonnées par formules standard.

Le nouveau chemin d’accès est stocké en tant que champ. Le gestionnaire de `PaintSurface` doit ensuite simplement centrer et mettre à l’échelle le chemin d’accès pour l’afficher à l’écran :

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

Il s’agit d’une technique très polyvalente. Si le tableau d’effets de tracés décrit dans l’article [**Effects Path**](effects.md) n’englobe pas tout ce que vous avez pensé à inclure, c’est un moyen de combler les lacunes.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
