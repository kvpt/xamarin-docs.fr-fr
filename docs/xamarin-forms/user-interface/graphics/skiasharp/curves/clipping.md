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
ms.openlocfilehash: a4bb6c30ada13691146d00d2094df8f13ca453b9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140255"
---
# <a name="clipping-with-paths-and-regions"></a>Détourage avec tracés et régions

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utiliser des chemins d’accès pour découper des graphiques dans des zones spécifiques et pour créer des régions_

Il est parfois nécessaire de limiter le rendu des graphiques à une zone particulière. C’est ce qu’on appelle le *découpage*. Vous pouvez utiliser le découpage pour des effets spéciaux, tels que l’image d’un singe vu par un Keyhole :

![Singe à travers un Keyhole](clipping-images/clippingsample.png)

La *zone de découpage* correspond à la zone de l’écran dans laquelle les graphiques sont rendus. Tout ce qui est affiché en dehors de la zone de découpage n’est pas rendu. La zone de découpage est généralement définie par un rectangle ou un [`SKPath`](xref:SkiaSharp.SKPath) objet, mais vous pouvez également définir une zone de découpage à l’aide d’un [`SKRegion`](xref:SkiaSharp.SKRegion) objet. Ces deux types d’objets semblent tout d’abord liés, car vous pouvez créer une région à partir d’un chemin d’accès. Toutefois, vous ne pouvez pas créer un chemin d’accès à partir d’une région et ils sont très différents en interne : un chemin d’accès comprend une série de lignes et de courbes, tandis qu’une région est définie par une série de lignes de numérisation horizontales.

L’image ci-dessus a été créée par le **singe via** la page Keyhole. La [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe définit un chemin d’accès à l’aide de données SVG et utilise le constructeur pour charger une image bitmap à partir des ressources du programme :

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Bien que l' `keyholePath` objet décrive le contour d’un Keyhole, les coordonnées sont complètement arbitraires et reflètent ce qui était pratique lorsque les données de chemin d’accès ont été imaginées. Pour cette raison, le `PaintSurface` Gestionnaire obtient les limites de ce chemin d’accès et appelle `Translate` et `Scale` déplace le chemin d’accès au centre de l’écran et le rend presque aussi haut que l’écran :

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

Toutefois, le chemin d’accès n’est pas rendu. Au lieu de cela, après les transformations, le chemin d’accès est utilisé pour définir une zone de découpage à l’aide de l’instruction suivante :

```csharp
canvas.ClipPath(keyholePath);
```

Le `PaintSurface` Gestionnaire réinitialise ensuite les transformations avec un appel à `ResetMatrix` et dessine l’image bitmap pour étendre à la hauteur complète de l’écran. Ce code suppose que l’image bitmap est carré, ce qui correspond à cette image bitmap particulière. La bitmap est rendue uniquement dans la zone définie par le chemin d’accès de détourage :

[![Capture d’écran triple du singe via la page Keyhole](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

Le chemin d’accès de détourage est soumis aux transformations en vigueur lorsque la `ClipPath` méthode est appelée, et non aux transformations en vigueur lorsqu’un objet graphique (tel qu’une bitmap) est affiché. Le chemin d’accès de détourage fait partie de l’état du canevas qui est enregistré avec la `Save` méthode et restauré avec la `Restore` méthode.

## <a name="combining-clipping-paths"></a>Combinaison de chemins d’accès de détourage

Strictement parlant, la zone de découpage n’est pas « définie » par la `ClipPath` méthode. Au lieu de cela, il est combiné avec le chemin d’accès de détourage existant, qui commence comme un rectangle dont la taille est égale à la zone de dessin. Vous pouvez obtenir les limites rectangulaires de la zone de découpage à l’aide de la [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) propriété ou de la [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) propriété. La `ClipBounds` propriété retourne une `SKRect` valeur qui reflète toutes les transformations qui peuvent être en vigueur. La `ClipDeviceBounds` propriété retourne une `RectI` valeur. Il s’agit d’un rectangle avec des dimensions entières, qui décrit la zone de découpage dans les dimensions de pixel réelles.

Tout appel pour `ClipPath` réduit la zone de découpage en combinant la zone de découpage avec une nouvelle zone. La syntaxe complète de la [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) méthode est la suivante :

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Il existe également une [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) méthode qui combine la zone de découpage avec un rectangle :

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Par défaut, la zone de découpage résultante est une intersection de la zone de découpage existante et du `SKPath` ou `SKRect` spécifié dans la `ClipPath` `ClipRect` méthode ou. Cela est illustré dans la page de **clip intersection des quatre cercles** . Le `PaintSurface` Gestionnaire de la [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe réutilise le même `SKPath` objet pour créer quatre cercles qui se chevauchent, chacun d’entre eux réduisant la zone de découpage par des appels successifs à `ClipPath` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

Ce qui reste est l’intersection de ces quatre cercles :

[![Capture d’écran triple de la page de clip Intersect de quatre cercles](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

L' [`SKClipOperation`](xref:SkiaSharp.SKClipOperation) énumération n’a que deux membres :

- `Difference`supprime le chemin d’accès ou le rectangle spécifié de la zone de découpage existante.

- `Intersect`croise le chemin d’accès ou le rectangle spécifié avec la zone de découpage existante

Si vous remplacez les quatre `SKClipOperation.Intersect` arguments de la `FourCircleIntersectClipPage` classe par `SKClipOperation.Difference` , vous verrez ce qui suit :

[![Capture d’écran triple de la page de clip intersection à quatre cercles avec opération de différence](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

Quatre cercles se chevauchant ont été supprimés de la zone de découpage.

La page **opérations de découpage** illustre la différence entre ces deux opérations avec juste une paire de cercles. Le premier cercle situé à gauche est ajouté à la zone de découpage avec l’opération de découpage par défaut de `Intersect` , tandis que le deuxième cercle à droite est ajouté à la zone de découpage avec l’opération de clip indiquée par l’étiquette de texte :

[![Capture d’écran triple de la page opérations de clip](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

La [`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe définit deux `SKPaint` objets en tant que champs, puis divise l’écran en deux zones rectangulaires. Ces zones diffèrent selon que le téléphone est en mode portrait ou paysage. La `DisplayClipOp` classe affiche ensuite le texte et appelle `ClipPath` avec les deux tracés circulaires pour illustrer chaque opération de séquence :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

L’appel de `DrawPaint` normalement entraîne le remplissage de l’intégralité de la zone de dessin avec cet `SKPaint` objet, mais dans ce cas, la méthode peint simplement dans la zone de découpage.

## <a name="exploring-regions"></a>Exploration des régions

Vous pouvez également définir une zone de découpage en termes d’un [`SKRegion`](xref:SkiaSharp.SKRegion) objet.

Un objet nouvellement créé `SKRegion` décrit une zone vide. En général, le premier appel sur l’objet est [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) afin que la région décrive une zone rectangulaire. Le paramètre de `SetRect` est une `SKRectI` valeur &mdash; un rectangle avec des coordonnées entières, car il spécifie le rectangle en pixels. Vous pouvez ensuite appeler [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) avec un `SKPath` objet. Cela crée une région qui est identique à l’intérieur du tracé, mais qui est découpée vers la région rectangulaire initiale.

La région peut également être modifiée en appelant l’une des [`Op`](xref:SkiaSharp.SKRegion.Op*) surcharges de la méthode, telle que celle-ci :

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

L' [`SKRegionOperation`](xref:SkiaSharp.SKRegionOperation) énumération est semblable à, `SKClipOperation` mais elle a plus de membres :

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

La région sur laquelle vous effectuez l' `Op` appel est combinée avec la région spécifiée comme paramètre basé sur le `SKRegionOperation` membre. Lorsque vous avez finalement une région adaptée au découpage, vous pouvez la définir comme zone de découpage du canevas à l’aide [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) de la méthode de `SKCanvas` :

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

La capture d’écran suivante montre les zones de découpage en fonction des opérations de six régions. Le cercle gauche est la région sur laquelle la `Op` méthode est appelée, et le cercle droit est la région transmise à la `Op` méthode :

[![Capture d’écran triple de la page opérations de la région](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

Existe-t-il toutes les possibilités de combiner ces deux cercles ? Considérez l’image résultante comme une combinaison de trois composants, qui sont visibles dans les `Difference` `Intersect` opérations, et `ReverseDifference` . Le nombre total de combinaisons est deux à la troisième puissance, ou huit. Les deux qui manquent sont la région d’origine (qui résulte de ne pas appeler du `Op` tout) et une région entièrement vide.

Il est plus difficile d’utiliser des régions pour le découpage, car vous devez d’abord créer un chemin d’accès, puis une région à partir de ce chemin d’accès, puis combiner plusieurs régions. La structure globale de la page d’opérations de la **région** est très similaire à celle des **opérations de découpage** , mais la [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divise l’écran en six zones et montre le travail supplémentaire requis pour utiliser les régions pour ce travail :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

Voici une grande différence entre la `ClipPath` méthode et la `ClipRegion` méthode :

> [!IMPORTANT]
> Contrairement `ClipPath` à la méthode, la `ClipRegion` méthode n’est pas affectée par les transformations.

Pour comprendre le raisonnement de cette différence, il est utile de comprendre ce qu’est une région. Si vous avez pensé à la façon dont les opérations sur les éléments ou les régions peuvent être implémentées en interne, cela peut sembler très compliqué. Plusieurs chemins d’accès potentiellement très complexes sont combinés et le contour du chemin d’accès résultant est probablement un cauchemar algorithmique.

Cette tâche est considérablement simplifiée si chaque chemin d’accès est réduit à une série de lignes de balayage horizontales, telles que celles des téléviseurs à tubes à vide vieux. Chaque ligne de numérisation est simplement une ligne horizontale avec un point de départ et un point de terminaison. Par exemple, un cercle avec un rayon de 10 pixels peut être décomposé en 20 lignes de balayage horizontales, chacune commençant à la partie gauche du cercle et se terminant à la partie droite. La combinaison de deux cercles avec toute opération de région devient très simple, car il s’agit simplement d’examiner les coordonnées de début et de fin de chaque paire de lignes de numérisation correspondantes.

Voici ce qu’est une région : une série de lignes de balayage horizontales qui définissent une zone.

Toutefois, lorsqu’une zone est réduite à une série de lignes de numérisation, ces lignes de numérisation sont basées sur une dimension de pixel particulière. Strictement parlant, la région n’est pas un objet graphique vectoriel. Il est plus proche de la nature d’une image bitmap monochrome compressée qu’à un tracé. Par conséquent, les régions ne peuvent pas être mises à l’échelle ou pivotées sans perdre de fidélité. pour cette raison, elles ne sont pas transformées lorsqu’elles sont utilisées pour les zones de découpage.

Toutefois, vous pouvez appliquer des transformations à des régions à des fins de peinture. Le programme **Region Paint** démontre vivement la nature interne des régions. La [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe crée un `SKRegion` objet basé sur un `SKPath` cercle de rayon de 10 unités. Une transformation développe ensuite ce cercle pour remplir la page :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

L' `DrawRegion` appel remplit la région en orange, tandis que l' `DrawPath` appel insère le tracé d’origine en bleu pour comparaison :

[![Capture d’écran triple de la page Paint de la région](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

La région est clairement une série de coordonnées discrètes.

Si vous n’avez pas besoin d’utiliser des transformations en relation avec vos zones de découpage, vous pouvez utiliser des régions pour le découpage, comme le montre la page de **trèfle à quatre feuilles** . La [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe construit une région composite à partir de quatre régions circulaires, définit cette région composite comme zone de découpage, puis dessine une série de 360 lignes droites à partir du centre de la page :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

Il n’est pas vraiment similaire à un trèfle à quatre feuilles, mais il s’agit d’une image qui serait autrement difficile à afficher sans découpage :

[![Capture d’écran triple de la page de trèfle à quatre feuilles](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
