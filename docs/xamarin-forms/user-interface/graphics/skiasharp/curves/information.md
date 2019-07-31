---
title: Informations et énumération de tracés
description: Cet article explique comment obtenir des informations sur les chemins d’accès SkiaSharp et énumérer le contenu et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 05d5003d349ae11a1ec6a1b6f3d66b2f68ffad8a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652867"
---
# <a name="path-information-and-enumeration"></a>Informations et énumération de tracés

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Obtenir des informations sur les chemins d’accès et d’énumérer le contenu_

Le [ `SKPath` ](xref:SkiaSharp.SKPath) classe définit plusieurs propriétés et méthodes qui vous permettent d’obtenir des informations sur le chemin d’accès. Le [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) et [ `TightBounds` ](xref:SkiaSharp.SKPath.TightBounds) propriétés (et les méthodes associées) obtenir les dimensions SURMONTANT d’un chemin d’accès. Le [ `Contains` ](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) méthode vous permet de déterminer si un point donné se trouve dans un chemin d’accès.

Il est parfois utile de déterminer la longueur totale de toutes les lignes et courbes qui forment un chemin d’accès. Calcul de cette longueur n’est pas une tâche par algorithme simple, donc une classe entière nommée [ `PathMeasure` ](xref:SkiaSharp.SKPathMeasure) est consacrée à celle-ci.

Il est parfois utile d’obtenir les opérations de dessin et la points qui composent un chemin d’accès. Dans un premier temps, cette fonctionnalité peut paraître inutile: Si votre programme a créé le chemin d’accès, le programme connaît déjà le contenu. Toutefois, vous avez vu que les chemins d’accès peuvent également être créées en [effets de tracé](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) et en convertissant [des chaînes de texte en tracés](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). Vous pouvez également obtenir les dessin opérations et les points qui composent ces chemins d’accès. Il est possible d’appliquer une transformation algorithmique à tous les points, par exemple, pour placer le texte autour d’un hémisphère :

![](information-images/pathenumerationsample.png "Texte encapsulé dans un hémisphère")

## <a name="getting-the-path-length"></a>En obtenant la longueur de chemin d’accès

Dans l’article [ **chemins d’accès et le texte** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) vous avez vu comment utiliser le [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) méthode pour dessiner une chaîne de texte dont baseline suit au cours d’un chemin d’accès. Mais que se passe-t-il si vous souhaitez la taille du texte de manière à s’ajuster avec précision le chemin d’accès ? Il est facile de dessiner du texte autour d’un cercle, car la circonférence d’un cercle est simple à calculer. Mais la circonférence d’une ellipse ou la longueur d’une courbe de Bézier n’est pas si simple.

Le [ `SKPathMeasure` ](xref:SkiaSharp.SKPathMeasure) classe peut aider. Le [constructeur](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) accepte un `SKPath` argument et le [ `Length` ](xref:SkiaSharp.SKPathMeasure.Length) propriété révèle sa longueur.

Cette classe est illustrée dans le **longueur de chemin d’accès** exemple, qui est basée sur le **courbe de Bézier** page. Le [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) fichier dérive `InteractivePage` et inclut une interface tactile :

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

Le [ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) fichier code-behind vous permet de déplacer les quatre points tactiles pour définir les points de terminaison et de points de contrôle de courbe de Bézier cubique. Trois champs définissent une chaîne de texte, un `SKPaint` objet et une largeur calculée du texte :

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

Le `baseTextWidth` champ correspond à la largeur du texte selon un `TextSize` définissant de 10.

Le `PaintSurface` gestionnaire dessine la courbe de Bézier et puis dimensionne le texte pour ajuster le long de sa longueur totale :

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

Le `Length` propriété du nouveau `SKPathMeasure` Obtient la longueur du chemin d’accès. La longueur de chemin d’accès est divisée par le `baseTextWidth` valeur (qui est la largeur du texte en fonction de la taille du texte de 10) et ensuite multipliée par la taille du texte de base 10. Le résultat est une nouvelle taille de texte pour l’affichage du texte le long de ce chemin d’accès :

[![](information-images/pathlength-small.png "Capture d’écran triple de la page de longueur de chemin d’accès")](information-images/pathlength-large.png#lightbox "Triple capture d’écran de la page de longueur de chemin d’accès")

Plus la courbe de Bézier est plus ou moins longtemps, vous pouvez voir la taille du texte changent.

## <a name="traversing-the-path"></a>Parcourir le chemin d’accès

`SKPathMeasure` faire plus que mesure la longueur du chemin d’accès. Pour toute valeur comprise entre zéro et la longueur du chemin d’accès, un `SKPathMeasure` objet peut obtenir la position sur le chemin d’accès et la tangente de la courbe de chemin d’accès à ce stade. La tangente est disponible sous forme de vecteur sous la forme d’un `SKPoint` de l’objet, ou comme une rotation encapsulé dans un `SKMatrix` objet. Voici les méthodes de `SKPathMeasure` qui obtenir ces informations de manières variées et flexible :

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Les membres de la [ `SKPathMeasureMatrixFlags` ](xref:SkiaSharp.SKPathMeasureMatrixFlags) énumération sont :

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

Le **MONOCYCLE moitié-canal** page anime un dessin minimaliste sur un MONOCYCLE semble aller en arrière le long d’une courbe de Bézier cubique :

[![](information-images/unicyclehalfpipe-small.png "Triple capture d’écran de la page d’un canal moitié MONOCYCLE")](information-images/unicyclehalfpipe-large.png#lightbox "Triple capture d’écran de la page de canal MONOCYCLE moitié")

L' `SKPaint` objet utilisé pour le détourage du demi-canal et du Unicycle est défini en tant que champ `UnicycleHalfPipePage` dans la classe. Également défini est le `SKPath` objet pour le MONOCYCLE :

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

La classe contient les remplacements standards de la `OnAppearing` et `OnDisappearing` méthodes pour l’animation. Le `PaintSurface` gestionnaire crée le chemin d’accès pour le canal de moitié et puis le dessine. Un `SKPathMeasure` objet est ensuite créé en fonction de ce chemin d’accès :

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

Le `PaintSurface` Gestionnaire calcule une valeur de `t` qui va de 0 à 1 toutes les cinq secondes. Il utilise ensuite le `Math.Cos` fonction à convertir en valeur `t` qui varie de 0 à 1 et 0, où 0 correspond à la MONOCYCLE au début en haut à gauche, 1 correspond à la MONOCYCLE en haut à droite. La fonction cosinus provoque la vitesse à être plus lente en haut du canal et le plus rapide en bas.

Notez que cette valeur de `t` doit être multiplié par la longueur de chemin d’accès pour le premier argument à `GetMatrix`. La matrice est ensuite appliquée à la `SKCanvas` objet pour le chemin d’accès MONOCYCLE de dessin.

## <a name="enumerating-the-path"></a>Le chemin d’accès de l’énumération des

Deux intégrés des classes de `SKPath` vous permettent d’énumérer le contenu du chemin d’accès. Ces classes sont [ `SKPath.Iterator` ](xref:SkiaSharp.SKPath.Iterator) et [ `SKPath.RawIterator` ](xref:SkiaSharp.SKPath.RawIterator). Les deux classes sont très similaires, mais `SKPath.Iterator` peuvent éliminer les éléments dans le chemin d’accès avec une longueur nulle ou proche d’une longueur nulle. Le `RawIterator` est utilisé dans l’exemple ci-dessous.

Vous pouvez obtenir un objet de type `SKPath.RawIterator` en appelant le [ `CreateRawIterator` ](xref:SkiaSharp.SKPath.CreateRawIterator) méthode de `SKPath`. L’énumération dans le chemin d’accès s’effectue en appelant à plusieurs reprises la [ `Next` ](xref:SkiaSharp.SKPath.RawIterator.Next*) (méthode). Lui passer un tableau de quatre `SKPoint` valeurs :

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

Le `Next` méthode retourne un membre de la [ `SKPathVerb` ](xref:SkiaSharp.SKPathVerb) type énumération. Ces valeurs indiquent la commande de dessin spécifique dans le chemin d’accès. Le nombre de points valides inséré dans le tableau dépend de ce verbe :

- `Move` avec un point unique
- `Line` avec deux points
- `Cubic` avec quatre points
- `Quad` avec trois points
- `Conic` avec trois points (et également appeler le [ `ConicWeight` ](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) méthode pour le poids)
- `Close` avec un point
- `Done`

Le `Done` verbe indique que l’énumération de chemin d’accès est terminée.

Notez qu’il existe aucune `Arc` verbes. Cela indique que tous les arcs sont convertis en courbes de Bézier lors de l’ajout pour le chemin d’accès.

Certaines informations dans le `SKPoint` tableau est redondant. Par exemple, si un `Move` verbe est suivi d’un `Line` verbe, puis le premier des deux points qui accompagnent le `Line` est le même que le `Move` point. Dans la pratique, cette redondance est très utile. Si vous obtenez un `Cubic` verbe, elle est accompagnée de toutes les quatre points qui définissent la courbe de Bézier cubique. Vous n’avez pas besoin de conserver la position actuelle, établie par le verbe précédent.

Le verbe problématique, cependant, est `Close`. Cette commande Dessine une ligne droite à partir de la position actuelle vers le début du contour établi précédemment par le `Move` commande. Dans l’idéal, le `Close` verbe doit fournir ces deux points au lieu de simplement un point. Pire encore est que le point qui accompagne le `Close` verbe est toujours (0, 0). Lorsque vous énumérez via un chemin d’accès, vous devrez probablement conserver le `Move` point et la position actuelle.

## <a name="enumerating-flattening-and-malforming"></a>L’énumération, mise à plat et Malforming

Il est parfois souhaitable d’appliquer une algorithmique transformer un chemin d’accès au malform d’une certaine façon :

![](information-images/pathenumerationsample.png "Texte encapsulé dans un hémisphère")

La plupart de ces lettres sont constitués de lignes droites, mais ces lignes droites ont apparemment été détournés en courbes. Comment est-ce possible ?

La clé est que les lignes droites d’origine sont répartis en une série de lignes droites plus petits. Ces droites individuelles plus petites peuvent ensuite être manipulées de différentes façons pour former une courbe.

Pour faciliter ce processus, le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple contient statique [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) classe avec un `Interpolate` méthode décompose un ligne droite dans nombreuses lignes courtes qui sont une seule unité de longueur. En outre, la classe contient plusieurs méthodes qui convertissent les trois types de courbes de Bézier en une série de petits traits qui se rapprochent de la courbe. (Les formules paramétriques ont été présentés dans l’article [ **trois Types de courbes de Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Ce processus est appelé _APLANISSEMENT_ la courbe :

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
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Toutes ces méthodes sont référencés à partir de la méthode d’extension `CloneWithTransform` également inclus dans cette classe et indiqué ci-dessous. Cette méthode clone un chemin d’accès en énumérant les commandes de chemin d’accès et en créant un nouveau chemin d’accès en fonction des données. Toutefois, le nouveau chemin d’accès se compose uniquement de `MoveTo` et `LineTo` appels. Toutes les lignes droites et courbes sont réduites à une série de lignes minuscules.

Lors de l’appel `CloneWithTransform`, vous passez à la méthode un `Func<SKPoint, SKPoint>`, qui est une fonction avec un `SKPaint` paramètre qui retourne un `SKPoint` valeur. Cette fonction est appelée pour chaque point d’appliquer une transformation algorithmique personnalisée :

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

Notez que la méthode conserve le premier point de chaque contour dans la variable appelée `firstPoint` et la position actuelle après chaque dessin de commande dans la variable `lastPoint`. Ces variables sont nécessaires à la construction de la dernière fermeture de ligne quand un `Close` verbe est rencontré.

Le **GlobularText** exemple utilise cette méthode d’extension pour apparemment habiller un hémisphère dans un effet 3D :

[![](information-images/globulartext-small.png "Capture d’écran triple de la page de texte globulaire")](information-images/globulartext-large.png#lightbox "Triple capture d’écran de la page de texte globulaire")

Le [ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) constructeur de classe effectue cette transformation. Il crée un `SKPaint` pour le texte de l’objet et obtient ensuite un `SKPath` de l’objet à partir de la `GetTextPath` (méthode). Il s’agit du chemin d’accès passé à la `CloneWithTransform` méthode d’extension, ainsi que d’une fonction de transformation :

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

La fonction de transformation calcule d’abord deux valeurs nommées `longitude` et `latitude` comprises entre – π/2 en haut et gauche du texte, π/2 à droite et bas du texte. La plage de ces valeurs n’est pas satisfaisante visuellement, afin qu’ils sont réduits en multipliant par la valeur 0,75. (Essayez le code sans ces ajustements. Le texte devient trop obscurs au nord et Sud pôles et trop léger sur les côtés.) Ces coordonnées sphériques en trois dimensions sont converties à deux dimensions `x` et `y` coordonnées par les formules standards.

Le nouveau chemin d’accès est stocké en tant que champ. Le `PaintSurface` Gestionnaire puis doit simplement mettre à l’échelle le chemin d’accès pour l’afficher sur l’écran et de centrage :

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

Il s’agit d’une technique très polyvalente. Si le tableau des effets de chemin d’accès décrites dans le [ **effets de tracé** ](effects.md) article n’englobe pas tout à fait quelque chose vous pensé doit être inclus, il s’agit d’un moyen de remplir les écarts.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
