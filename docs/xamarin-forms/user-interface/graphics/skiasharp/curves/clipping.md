---
title: Détourage avec tracés et régions
description: Cet article explique comment utiliser des chemins d’accès SkiaSharp aux graphiques de clip à des zones spécifiques et pour créer des zones et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 1daf4822dd7debe98aabd58d42cb6ed29f95b90d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759357"
---
# <a name="clipping-with-paths-and-regions"></a>Détourage avec tracés et régions

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utiliser des chemins d’accès aux images à des zones spécifiques et pour créer des zones_

Il est parfois nécessaire de limiter le rendu des graphiques pour une zone particulière. Il s’agit *découpage*. Vous pouvez utiliser le découpage pour des effets spéciaux, tels que cette image d’un monkey affiché dans un masque :

![Singe à travers un Keyhole](clipping-images/clippingsample.png)

Le *zone de découpage* correspond à la zone de l’écran dans lequel les graphiques sont rendus. Tout ce qui s’affiche en dehors de la zone de découpage n’est pas rendu. La zone de découpage est généralement définie par un rectangle ou un [ `SKPath` ](xref:SkiaSharp.SKPath) objet, mais vous pouvez également définir une zone de découpage à l’aide un [ `SKRegion` ](xref:SkiaSharp.SKRegion) objet. Ces deux types d’objets à paraître connexes, car vous pouvez créer une zone à partir d’un chemin d’accès. Toutefois, vous ne pouvez pas créer un chemin d’accès à partir d’une région et ils sont très différents en interne : Un chemin d’accès comprend une série de lignes et de courbes, tandis qu’une région est définie par une série de lignes de balayage horizontales.

L’image ci-dessus a été créé par le **Monkey via masque** page. Le [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe définit un chemin d’accès à l’aide des données SVG et utilise le constructeur pour charger une bitmap à partir des ressources du programme :

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

Bien que le `keyholePath` objet décrit le contour d’un masque, les coordonnées sont totalement aléatoires et reflètent ce qui s’avère pratique lorsque les données de chemin d’accès a été conçues. Pour cette raison, le `PaintSurface` gestionnaire obtient les limites de ce chemin d’accès et d’appels `Translate` et `Scale` pour déplacer le chemin d’accès au centre de l’écran et s’il presque toute la hauteur de l’écran :

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

Mais le chemin d’accès n’est pas restitué. Au lieu de cela, après les transformations, le chemin d’accès permet de définir une zone de découpage avec cette instruction :

```csharp
canvas.ClipPath(keyholePath);
```

Le `PaintSurface` gestionnaire réinitialise ensuite les transformations avec un appel à `ResetMatrix` et dessine l’image bitmap d’étendre à la hauteur totale de l’écran. Ce code suppose que la bitmap est de carré, c'est-à-dire cette bitmap particulière. La bitmap est restituée uniquement dans la zone définie par le tracé de détourage :

[![Capture d’écran triple du singe via la page Keyhole](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

Le tracé de détourage est soumis aux transformations en vigueur lorsque le `ClipPath` méthode est appelée, et pas pour les transformations en vigueur quand un objet graphique (par exemple, une image bitmap) s’affiche. Le tracé de détourage fait partie de l’état de la zone de dessin qui est enregistré avec le `Save` (méthode) et restauré avec le `Restore` (méthode).

## <a name="combining-clipping-paths"></a>Combiner des tracés de détourage

À proprement parler, la zone de découpage n'est pas « set » le `ClipPath` (méthode). Au lieu de cela, il est combiné avec le tracé de détourage existant, qui est un rectangle de taille égal à la zone de dessin. Vous pouvez obtenir les limites rectangulaires de la zone de découpage à l’aide de la [ `ClipBounds` ](xref:SkiaSharp.SKCanvas.ClipBounds) propriété ou le [ `ClipDeviceBounds` ](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) propriété. Le `ClipBounds` propriété retourne un `SKRect` valeur qui reflète les transformations qui peut-être être appliqués. Le `ClipDeviceBounds` propriété retourne un `RectI` valeur. Ceci est un rectangle avec les dimensions entier et décrit la zone de découpage dans les dimensions de pixel.

Tout appel à `ClipPath` réduit la zone de découpage en associant la zone de découpage avec une nouvelle zone. La syntaxe complète de la [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) méthode est :

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Il existe également un [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) (méthode) qui associe la zone de découpage d’un rectangle :

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Par défaut, la zone de découpage résultant est une intersection de la zone de découpage existant et le `SKPath` ou `SKRect` qui est spécifié dans le `ClipPath` ou `ClipRect` (méthode). Cela est illustré dans le **l’élément se croisent quatre cercles** page. Le `PaintSurface` gestionnaire dans le [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe réutilise le même `SKPath` objet à créer quatre cercles se chevauchant, chacun d’eux permet de réduire la zone de découpage par le biais des appels successifs à `ClipPath`:

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

Le [ `SKClipOperation` ](xref:SkiaSharp.SKClipOperation) énumération possède uniquement deux membres :

- `Difference` Supprime le chemin d’accès spécifié ou le rectangle de la zone de découpage existant

- `Intersect` entre en intersection avec le chemin d’accès spécifié ou le rectangle avec la zone de découpage existant

Si vous remplacez les quatre `SKClipOperation.Intersect` arguments dans le `FourCircleIntersectClipPage` classe avec `SKClipOperation.Difference`, vous verrez les éléments suivants :

[![Capture d’écran triple de la page de clip intersection à quatre cercles avec opération de différence](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

Quatre cercles se chevauchant ont été supprimés à partir de la zone de découpage.

Le **les opérations de découpage** page illustre la différence entre ces deux opérations avec uniquement une paire de cercles. Le premier cercle sur la gauche est ajouté à la zone de découpage avec l’opération de découpage par défaut de `Intersect`, tandis que le deuxième cercle sur la droite est ajouté à la zone de découpage avec l’opération de découpage indiquée par l’étiquette de texte :

[![Capture d’écran triple de la page opérations de clip](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

Le [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe définit deux `SKPaint` objets en tant que champs et puis divise l’écran en deux zones rectangulaires. Ces zones sont différentes selon que le téléphone est en mode portrait ou paysage. Le `DisplayClipOp` classe puis affiche le texte et les appels `ClipPath` avec les chemins d’accès de deux cercle pour illustrer chaque opération de découpage :

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

Appel `DrawPaint` normalement provoque la totalité du canevas à remplir avec qui `SKPaint` objet, mais dans ce cas, la méthode simplement peint au sein de la zone de découpage.

## <a name="exploring-regions"></a>Exploration des régions

Vous pouvez également définir une zone de découpage en termes d’un [ `SKRegion` ](xref:SkiaSharp.SKRegion) objet.

Nouvellement créée `SKRegion` objet décrit une zone vide. Le premier appel sur l’objet est généralement [ `SetRect` ](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) afin que la région décrit une zone rectangulaire. Le paramètre `SetRect` est un `SKRectI` valeur &mdash; un rectangle avec entier coordonne car elle spécifie le rectangle en termes de pixels. Vous pouvez ensuite appeler [ `SetPath` ](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) avec un `SKPath` objet. Cette opération crée une région qui est identique à l’intérieur du chemin d’accès, mais découpé à la région rectangulaire initiale.

La région peut également être modifiée en appelant une de la [ `Op` ](xref:SkiaSharp.SKRegion.Op*) des surcharges de méthode, telle que celle-ci :

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

Le [ `SKRegionOperation` ](xref:SkiaSharp.SKRegionOperation) énumération est similaire à `SKClipOperation` mais elle n’a plus de membres :

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

La région que vous émettiez la `Op` appel sur est associé à la région spécifiée en tant que paramètre selon le `SKRegionOperation` membre. Lorsque vous obtenez finalement une région appropriée pour le découpage, vous pouvez le définir en tant que la zone de découpage de la zone de dessin à l’aide de la [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) méthode de `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

La capture d’écran suivante montre les zones de découpage basés sur les opérations de six région. Le cercle gauche correspond à la région qui le `Op` méthode est appelée sur, et le cercle droite correspond à la région passée à la `Op` méthode :

[![Capture d’écran triple de la page opérations de la région](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

Sont ces toutes les possibilités de la combinaison de ces deux cercles ? Examinez l’image résultante comme une combinaison de trois composants, qui eux-mêmes est visibles dans le `Difference`, `Intersect`, et `ReverseDifference` operations. Le nombre total de combinaisons est deux à la puissance 3, voire huit. Les deux sont manquants sont la région d’origine (qui résulte de l’appel ne pas `Op` du tout) et une région entièrement vide.

Il est plus difficile à utiliser des régions pour le découpage, car vous devez d’abord créer un chemin d’accès, puis une région à partir de ce chemin d’accès et ensuite combiner plusieurs régions. La structure globale de la **opérations de région** page est très similaire à **les opérations de découpage** mais le [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divise l’écran en six domaines et montre le travail supplémentaire nécessaire pour utiliser des régions pour cette tâche :

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

Voici une grande différence entre le `ClipPath` (méthode) et le `ClipRegion` méthode :

> [!IMPORTANT]
> Contrairement à la `ClipPath` (méthode), le `ClipRegion` (méthode) n’est pas affectée par les transformations.

Pour comprendre les fondements de cette différence, il est utile de comprendre quelles une région est. Si vous avez réfléchi sur la façon dont les opérations de découpage ou les opérations de région peuvent être implémentées en interne, il semble probablement très compliqué. Plusieurs chemins d’accès potentiellement très complexes sont combinées, et le contour du chemin d’accès résultant est probablement un cauchemar algorithmique.

Ce travail est considérablement simplifié si chaque chemin d’accès est réduite à une série de lignes de balayage horizontales, telles que celles de l’ancienne tube à vide téléviseurs. Chaque ligne de numérisation est simplement une ligne horizontale avec un point de départ et un point de terminaison. Par exemple, un cercle avec un rayon de 10 pixels peut être décomposé en 20 lignes de balayage horizontal, chacun d’eux commence à la partie gauche du cercle et se termine à la partie droite. Combinaison de deux cercles à toute opération de région devient très simple car il s’agit simplement d’examiner les coordonnées de début et de fin de chaque paire correspondante de lignes de numérisation.

Voici ce qu’est une région : Série de lignes de balayage horizontal qui définissent une zone.

Toutefois, quand une zone est réduite à une série d’analyse les lignes, ces lignes sont basées sur une dimension de pixel particulier d’analyse. À proprement parler, la région n’est pas un objet de graphique vectoriel. Il est plus en détail dans la nature d’une bitmap monochrome compressée à un chemin d’accès. Par conséquent, les régions ne peut pas être mis à l’échelle ou pivotées sans perdre la fidélité et c’est pourquoi qu'ils ne sont pas transformés lorsqu’il est utilisé pour les zones de découpage.

Toutefois, vous pouvez appliquer des transformations aux régions à des fins de peinture. Le **région Paint** programme illustre bien la nature interne des régions. Le [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe crée un `SKRegion` objet basé sur un `SKPath` d’un cercle de rayon de 10 unités. Une transformation étend ensuite ce cercle pour remplir la page :

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

Le `DrawRegion` appel remplit la région en orange, tandis que le `DrawPath` appel strokes le chemin d’accès d’origine en bleu pour la comparaison :

[![Capture d’écran triple de la page Paint de la région](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

La région est clairement une série de coordonnées discrètes.

Si vous n’avez pas besoin d’utiliser des transformations dans le cadre de vos zones de découpage, vous pouvez utiliser des régions pour le découpage, comme le **quatre – sommes très heureux** montre de page. Le [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe construit une région composite à partir de quatre régions circulaires, définit cette région composite en tant que la zone de découpage et puis dessine une série de 360 droites émanant à partir du centre de la page :

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

Il ne ressemble pas vraiment à un sommes très heureux quatre –, mais il est une image qui seraient sinon difficile à effectuer le rendu sans découpage :

[![Capture d’écran triple de la page de trèfle à quatre feuilles](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
