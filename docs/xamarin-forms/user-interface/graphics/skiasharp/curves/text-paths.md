---
title: Chemins d’accès et du texte dans SkiaSharp
description: Cet article explore l’intersection des chemins d’accès SkiaSharp et du texte et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
ms.openlocfilehash: d38391f3fd0f02dda8bfd92fce650c557bda0153
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645212"
---
# <a name="paths-and-text-in-skiasharp"></a>Chemins d’accès et du texte dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorez l’intersection des chemins d’accès et du texte_

Dans les systèmes graphiques modernes, les polices de texte sont des collections de contours de caractère, généralement définis par des courbes de Bézier quadratiques. Par conséquent, de nombreux systèmes graphiques modernes incluent une fonctionnalité permettant de convertir les caractères de texte en un chemin d’accès de graphiques.

Vous avez déjà vu que vous pouvez tracer des contours des caractères de texte mais aussi les remplir. Cela vous permet d’afficher ces contours de caractère avec une largeur de trait particulier et même un effet de chemin d’accès comme décrit dans la [ **effets de tracé** ](effects.md) article. Mais il est également possible de convertir une chaîne de caractères en une `SKPath` objet. Cela signifie que les contours du texte peuvent être utilisés pour le découpage avec plusieurs techniques qui ont été décrites dans le [ **détourage avec chemins d’accès et régions** ](clipping.md) article.

Outre l’utilisation d’un effet de chemin d’accès pour tracer un contour de caractère, vous pouvez également créer des effets de chemin d’accès qui sont basées sur un chemin d’accès qui est dérivé d’une chaîne de caractères, et vous pouvez même combiner les deux effets :

![](text-paths-images/pathsandtextsample.png "Effet de texte")

Dans l’article précédent sur [ **les effets de chemin d’accès**](effects.md), vous avez vu comment le [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) méthode de `SKPaint` peuvent obtenir une description d’un chemin de tracé. Vous pouvez également utiliser cette méthode avec les chemins d’accès dérivés de contours de caractère.

Enfin, cet article présente une autre intersection de chemins d’accès et de texte: La [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) méthode de `SKCanvas` vous permet d’afficher une chaîne de texte afin que la ligne de base du texte suive un tracé courbé.

## <a name="text-to-path-conversion"></a>Texte à la Conversion de chemin d’accès

Le [ `GetTextPath` ](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) méthode de `SKPaint` convertit une chaîne de caractères en une `SKPath` objet :

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Le `x` et `y` arguments indiquent le point de départ de la ligne de base du côté gauche du texte. Ils jouent le même rôle ici en tant que le `DrawText` méthode de `SKCanvas`. Dans le chemin d’accès, la ligne de base du côté gauche du texte aura les coordonnées (x, y).

Le `GetTextPath` méthode est excessif si vous souhaitez simplement remplir ou entourer le chemin d’accès résultant. Vecteur normal `DrawText` méthode vous permet de faire. Le `GetTextPath` méthode est plus utile pour d’autres tâches impliquant des chemins d’accès.

Une de ces tâches est le découpage. Le **découpage texte** page crée un tracé de détourage basé sur les contours des caractères du mot « CODE ». Ce chemin d’accès est étendu selon la taille de la page pour découper une image bitmap qui contient une image de la **texte découpage** code source :

[![](text-paths-images/clippingtext-small.png "Capture d’écran triple de la page de texte de découpage")](text-paths-images/clippingtext-large.png#lightbox "Triple capture d’écran de la page de texte de découpage")

Le [ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) constructeur de classe de charge de la bitmap qui est stockée sous la forme d’une ressource incorporée dans le **Media** dossier de la solution :

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

Le `PaintSurface` gestionnaire commence par créer un `SKPaint` objet adapté au texte. Le `Typeface` propriété est définie, ainsi que les `TextSize`, bien que pour cette application particulière le `TextSize` propriété est purement arbitraire. Notez également est aucun `Style` paramètre.

Le `TextSize` et `Style` les paramètres de propriété ne sont pas nécessaires, car cela `SKPaint` objet est utilisé uniquement pour le `GetTextPath` appeler à l’aide de la chaîne de texte « CODE ». Le Gestionnaire de mesure puis la résultante `SKPath` de l’objet et applique trois transformations pour centrer et mettre à l’échelle à la taille de la page. Le chemin d’accès peut être défini comme le tracé de détourage :

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

Une fois que le tracé de détourage est défini, l’image bitmap peut être affichée, et il est découpée selon les contours des caractères. Remarquez l’utilisation de la [ `AspectFill` ](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) méthode de `SKRect` qui calcule un rectangle pour remplir la page tout en conservant les proportions.

Le **effet de texte** page convertit un caractère unique pour un chemin d’accès pour créer un effet de chemin d’accès 1D. Un peinture objet avec cet effet de chemin d’accès est ensuite utilisé pour tracer le contour d’une version supérieure de ce même caractère :

[![](text-paths-images/textpatheffect-small.png "Capture d’écran triple de la page de l’effet de chemin d’accès de texte")](text-paths-images/textpatheffect-large.png#lightbox "Triple capture d’écran de la page de l’effet de chemin d’accès de texte")

Quantité de travail dans le [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe se produit dans les champs et le constructeur. Les deux `SKPaint` objets définis en tant que champs sont utilisés à deux fins différentes: Le premier (nommé `textPathPaint`) est utilisé pour convertir l’esperluette avec un `TextSize` de 50 en chemin d’accès pour l’effet 1D Path. Le second (`textPaint`) est utilisé pour afficher la version la plus grande de l’esperluette avec effet de ce chemin d’accès. Pour cette raison, le `Style` de cette deuxième peinture objet est défini sur `Stroke`, mais le `StrokeWidth` propriété n’est pas définie, car cette propriété n’est pas nécessaire lors de l’utilisation d’un effet 1D :

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

Le constructeur utilise d’abord la `textPathPaint` objet pour mesurer l’esperluette avec un `TextSize` de 50. Les valeurs négatives des coordonnées du centre du rectangle sont ensuite transmis à la `GetTextPath` méthode pour convertir le texte à un chemin d’accès. Le chemin d’accès résultant a le (0, 0) point dans le centre du caractère, ce qui est idéal pour un effet 1D.

Vous pouvez penser que le `SKPathEffect` objet créé à la fin du constructeur peut être définie sur le `PathEffect` propriété de `textPaint` plutôt qu’enregistré en tant que champ. Mais ce n’est pas désactivé out fonctionne très bien, car il déformée les résultats de la `MeasureText` appeler dans le `PaintSurface` gestionnaire :

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

Que `MeasureText` appel est utilisé pour centrer le caractère sur la page. Pour éviter les problèmes, le `PathEffect` propriété est définie sur l’objet paint après que le texte a été mesuré, mais avant son affichage.

## <a name="outlines-of-character-outlines"></a>Contours des contours de caractère

Normalement le [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) méthode `SKPaint` convertit un chemin d’accès à un autre en appliquant des propriétés de la peinture, plus particulièrement la largeur et le chemin d’accès effet de contour. Lorsqu’il est utilisé sans effets de chemin d’accès, `GetFillPath` crée en réalité un chemin d’accès qui décrit un autre chemin d’accès. Cela est illustré dans le **appuyez sur contour, le chemin d’accès** page dans le [ **effets de tracé** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) article.

Vous pouvez également appeler `GetFillPath` sur le chemin d’accès retourné à partir de `GetTextPath` mais dans un premier temps vous ne serez pas tout à fait certain quoi cela ressemble.

Le **contours de caractère contour** page illustre cette technique. Tout le code approprié est dans le `PaintSurface` Gestionnaire de la [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

Le constructeur commence par créer un `SKPaint` objet nommé `textPaint` avec un `TextSize` propriété basée sur la taille de la page. Il est converti en un chemin d’accès à l’aide de la `GetTextPath` (méthode). Les arguments de coordonnées à `GetTextPath` center efficacement le chemin d’accès à l’écran :

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

Le `PaintSurface` gestionnaire crée ensuite un nouveau chemin d’accès nommé `outlinePath`. Cela devient le chemin d’accès de destination dans l’appel à `GetFillPath`. Le `StrokeWidth` propriété des 25 causes `outlinePath` pour décrire le contour d’un chemin d’accès de 25 pixels de large traçant les caractères de texte. Ce chemin d’accès est ensuite affichée en rouge avec une largeur de trait de 5 :

[![](text-paths-images/characteroutlineoutlines-small.png "Capture d’écran triple de la page des contours de caractère contour")](text-paths-images/characteroutlineoutlines-large.png#lightbox "Triple capture d’écran de la page des contours de contour de caractère")

Examinez attentivement et vous verrez des chevauchements et où la structure du chemin d’accès permet à un angle aigu. Il s’agit d’artefacts normales de ce processus.

## <a name="text-along-a-path"></a>Texte sur un tracé

Texte s’affiche normalement sur une ligne de base horizontale. Texte peut être pivoté pour exécuter verticalement ou en diagonale, mais la ligne de base est toujours une ligne droite.

Il est parfois, cependant, lorsque vous souhaitez que le texte à exécuter une courbe. C’est l’objectif de la [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) méthode de `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

Le texte spécifié dans le premier argument est effectué pour s’exécuter sur le chemin spécifié comme deuxième argument. Vous pouvez commencer le texte à un offset à partir du début du chemin d’accès avec le `hOffset` argument. Normalement, le chemin d’accès forme la ligne de base du texte: Les jambages de texte sont situés d’un côté du chemin d’accès et les descendants du texte sont sur l’autre. Mais vous pouvez décaler la ligne de base du chemin d’accès avec le `vOffset` argument.

Cette méthode n’a aucune fonctionnalité de fournir des conseils sur la configuration de la `TextSize` propriété du `SKPaint` pour rendre le texte dimensionné parfaitement pour exécuter depuis le début du chemin d’accès à la fin. Parfois, vous vous rendiez compte cette taille de texte sur votre propre. Parfois, vous devez utiliser les fonctions de chemin d’accès de la mesure à décrire dans l’article suivant sur [ **les informations de chemin et énumération**](information.md).

Le **texte circulaire** programme encapsule le texte autour d’un cercle. Il est facile de déterminer la circonférence d’un cercle, il est facile de la taille du texte afin de correspondre exactement. Le `PaintSurface` Gestionnaire de la [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcule un rayon d’un cercle en fonction de la taille de la page. Cercle devient `circularPath`:

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

Le `TextSize` propriété du `textPaint` est ensuite ajustée afin que la largeur du texte correspond à la circonférence du cercle :

[![](text-paths-images/circulartext-small.png "Capture d’écran triple de la page de texte circulaire")](text-paths-images/circulartext-large.png#lightbox "Triple capture d’écran de la page de texte circulaire")

Le texte lui-même a été choisi comme étant un peu circulaire: Le mot «Circle» est à la fois le sujet de la phrase et l’objet d’une phrase de préposition.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
