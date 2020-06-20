---
title: Chemins et texte dans SkiaSharp
description: Cet article explore l’intersection des chemins d’accès et du texte SkiaSharp et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b0cbb7d26a2aea02a3255fc75947c20a3d803b86
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131896"
---
# <a name="paths-and-text-in-skiasharp"></a>Chemins et texte dans SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorer l’intersection des chemins et du texte_

Dans les systèmes graphiques modernes, les polices de texte sont des collections de contours de caractère, généralement définies par des courbes de Bézier quadratiques. Par conséquent, de nombreux systèmes graphiques modernes incluent une fonctionnalité permettant de convertir des caractères de texte en tracé graphique.

Vous avez déjà vu que vous pouvez rayer les contours des caractères de texte et les remplir. Cela vous permet d’afficher ces contours de caractères avec une largeur de trait particulière et même un effet de tracé comme décrit dans l’article [**effets de tracés**](effects.md) . Toutefois, il est également possible de convertir une chaîne de caractères en un `SKPath` objet. Cela signifie que les contours de texte peuvent être utilisés pour le découpage avec les techniques décrites dans l’article [**découpage avec les chemins d’accès et les régions**](clipping.md) .

Outre l’utilisation d’un effet de tracé pour rayer un contour de caractères, vous pouvez également créer des effets de tracés basés sur un chemin d’accès dérivé d’une chaîne de caractères, et vous pouvez même combiner les deux effets :

![](text-paths-images/pathsandtextsample.png "Text Path Effect")

Dans l’article précédent sur les [**effets de tracé**](effects.md), vous avez vu comment la [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) méthode de `SKPaint` peut obtenir un contour d’un tracé rayé. Vous pouvez également utiliser cette méthode avec les chemins dérivés des contours de caractère.

Enfin, cet article présente une autre intersection de chemins et de texte : la [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) méthode de `SKCanvas` vous permet d’afficher une chaîne de texte afin que la ligne de base du texte suive un tracé courbé.

## <a name="text-to-path-conversion"></a>Conversion de texte en tracé

La [`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) méthode de `SKPaint` convertit une chaîne de caractères en `SKPath` objet :

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Les `x` `y` arguments et indiquent le point de départ de la ligne de base du côté gauche du texte. Ils jouent le même rôle que dans la `DrawText` méthode de `SKCanvas` . Dans le chemin d’accès, la ligne de base du côté gauche du texte aura les coordonnées (x, y).

La `GetTextPath` méthode est excessive si vous souhaitez simplement remplir ou rayer le chemin d’accès résultant. La `DrawText` méthode normale vous permet de le faire. La `GetTextPath` méthode est plus utile pour d’autres tâches impliquant des chemins d’accès.

L’une de ces tâches est le découpage. La page de **texte de découpage** crée un tracé de détourage en fonction des contours de caractère du mot « code ». Ce chemin d’accès est étiré sur la taille de la page pour découper une bitmap qui contient une image du code source du **texte de découpage** :

[![](text-paths-images/clippingtext-small.png "Triple screenshot of the Clipping Text page")](text-paths-images/clippingtext-large.png#lightbox "Triple screenshot of the Clipping Text page")

Le [`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) constructeur de classe charge l’image bitmap qui est stockée en tant que ressource incorporée dans le dossier **Media** de la solution :

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Le `PaintSurface` Gestionnaire commence par créer un `SKPaint` objet approprié pour le texte. La `Typeface` propriété est définie et `TextSize` , bien que pour cette application particulière, la `TextSize` propriété est purement arbitraire. Notez également qu’il n’y a aucun `Style` paramètre.

Les `TextSize` paramètres de propriété et ne `Style` sont pas nécessaires, car cet `SKPaint` objet est utilisé uniquement pour l' `GetTextPath` appel à l’aide de la chaîne de texte « Code ». Le gestionnaire mesure ensuite l' `SKPath` objet résultant et applique trois transformations pour le centrer et l’adapter à la taille de la page. Le chemin d’accès peut ensuite être défini en tant que chemin d’accès de détourage :

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

Une fois le tracé de détourage défini, l’image bitmap peut être affichée et elle est découpée en contours de caractère. Notez l’utilisation de la [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) méthode de `SKRect` qui calcule un rectangle pour remplir la page tout en préservant les proportions.

La page **effet de tracé de texte** convertit un caractère perluète unique en tracé pour créer un effet de tracé 1D. Un objet Paint avec cet effet de tracé est ensuite utilisé pour rayer le contour d’une version plus grande de ce même caractère :

[![](text-paths-images/textpatheffect-small.png "Triple screenshot of the Text Path Effect page")](text-paths-images/textpatheffect-large.png#lightbox "Triple screenshot of the Text Path Effect page")

Une grande partie du travail de la [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe se produit dans les champs et le constructeur. Les deux `SKPaint` objets définis en tant que champs sont utilisés à deux fins différentes : le premier (nommé `textPathPaint` ) est utilisé pour convertir l’esperluette avec un `TextSize` de 50 en chemin d’accès pour l’effet 1D Path. Le deuxième ( `textPaint` ) est utilisé pour afficher la plus grande version de l’esperluette avec cet effet de tracé. Pour cette raison, le `Style` de ce second objet Paint a la valeur `Stroke` , mais la `StrokeWidth` propriété n’est pas définie, car cette propriété n’est pas nécessaire lors de l’utilisation d’un effet 1D Path :

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

Le constructeur utilise d’abord l' `textPathPaint` objet pour mesurer la esperluette avec un `TextSize` de 50. Les négatifs des coordonnées du centre de ce rectangle sont ensuite passés à la `GetTextPath` méthode pour convertir le texte en tracé. Le chemin d’accès résultant a le point (0,0) au centre du caractère, ce qui est idéal pour un effet de tracé 1D.

Vous pouvez penser que l' `SKPathEffect` objet créé à la fin du constructeur peut être défini sur la `PathEffect` propriété de `textPaint` plutôt que sur enregistré en tant que champ. Mais cela ne fonctionne pas très bien parce qu’il a faussé les résultats de l' `MeasureText` appel dans le `PaintSurface` Gestionnaire :

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

Cet `MeasureText` appel est utilisé pour centrer le caractère sur la page. Pour éviter les problèmes, la `PathEffect` propriété est définie sur l’objet Paint une fois que le texte a été mesuré mais avant son affichage.

## <a name="outlines-of-character-outlines"></a>Contours des contours de caractère

Normalement [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) , la méthode de `SKPaint` convertit un chemin d’accès à un autre en appliquant des propriétés de peinture, notamment la largeur du trait et l’effet du tracé. En cas d’utilisation sans effet de tracé, `GetFillPath` crée un tracé qui contourne un autre chemin d’accès. Cela a été démontré en appuyant sur la page du **chemin** dans l’article sur les [**effets de chemin**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) .

Vous pouvez également appeler `GetFillPath` sur le chemin d’accès retourné à partir de `GetTextPath` , mais dans un premier temps, vous n’êtes peut-être pas entièrement sûr de ce à quoi cela ressemble.

La page des **contours de contour de caractères** illustre la technique. Tout le code pertinent se trouve dans le `PaintSurface` Gestionnaire de la [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

Le constructeur commence par créer un `SKPaint` objet nommé `textPaint` avec une `TextSize` propriété en fonction de la taille de la page. Elle est convertie en un chemin d’accès à l’aide de la `GetTextPath` méthode. Les arguments de coordonnée pour `GetTextPath` centrer efficacement le tracé à l’écran :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

Le `PaintSurface` Gestionnaire crée alors un nouveau chemin d’accès nommé `outlinePath` . Cela devient le chemin d’accès de destination dans l’appel à `GetFillPath` . La `StrokeWidth` propriété de 25 provoque `outlinePath` la description de la structure d’un chemin d’accès à 25 pixels, en traçant les caractères de texte. Ce chemin d’accès est ensuite affiché en rouge avec une largeur de trait de 5 :

[![](text-paths-images/characteroutlineoutlines-small.png "Triple screenshot of the Character Outline Outlines page")](text-paths-images/characteroutlineoutlines-large.png#lightbox "Triple screenshot of the Character Outline Outlines page")

Examinez attentivement et vous verrez des chevauchements où la structure du tracé fait un angle aigu. Il s’agit d’artefacts normaux de ce processus.

## <a name="text-along-a-path"></a>Texte le long d’un tracé

Le texte est normalement affiché sur une ligne de base horizontale. Le texte peut être pivoté pour s’exécuter verticalement ou en diagonale, mais la ligne de base est toujours une ligne droite.

Il existe cependant des cas où vous souhaitez que le texte s’exécute le long d’une courbe. C’est l’objectif de la [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) méthode de `SKCanvas` :

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

Le texte spécifié dans le premier argument est effectué pour s’exécuter le long du chemin d’accès spécifié comme deuxième argument. Vous pouvez commencer le texte à un offset à partir du début du chemin d’accès avec l' `hOffset` argument. Normalement, le chemin d’accès forme la ligne de base du texte : les hampes ascendantes du texte se trouvent sur un côté du chemin et les descendants du texte sont sur l’autre. Toutefois, vous pouvez décaler la ligne de base du texte à partir du chemin d’accès avec l' `vOffset` argument.

Cette méthode n’a pas de fonctionnalité pour fournir des conseils sur la définition `TextSize` de la propriété de `SKPaint` afin que le texte soit parfaitement dimensionné pour s’exécuter à partir du début du chemin d’accès jusqu’à la fin. Parfois, vous pouvez déterminer la taille du texte. Dans d’autres cas, vous devrez utiliser les fonctions de mesure du chemin d’accès pour les décrire dans le prochain article sur les [**informations de chemin d’accès et l’énumération**](information.md).

Le programme de **texte circulaire** renvoie le texte autour d’un cercle. Il est facile de déterminer la circonférence d’un cercle. il est donc facile de dimensionner le texte pour qu’il s’ajuste exactement. Le `PaintSurface` Gestionnaire de la [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcule le rayon d’un cercle en fonction de la taille de la page. Ce cercle devient `circularPath` :

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

La `TextSize` propriété de `textPaint` est ensuite ajustée de sorte que la largeur du texte corresponde à la circonférence du cercle :

[![](text-paths-images/circulartext-small.png "Triple screenshot of the Circular Text page")](text-paths-images/circulartext-large.png#lightbox "Triple screenshot of the Circular Text page")

Le texte lui-même a été choisi comme étant un peu circulaire : le mot « Circle » est à la fois le sujet de la phrase et l’objet d’une phrase de préposition.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
