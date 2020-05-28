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
ms.openlocfilehash: 207b16f062a5c2137ac5fc3c21775d2486fda57d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135861"
---
# <a name="the-skew-transform"></a>La transformation d’inclinaison

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Voir comment la transformation d’inclinaison peut créer des objets graphiques inclinés dans SkiaSharp_

Dans SkiaSharp, la transformation d’inclinaison incline les objets graphiques, tels que l’ombre dans cette image :

![](skew-images/skewexample.png "An example of skewing from the Skew Shadow Text program")

L’inclinaison transforme un rectangle en parallélogramme, mais une ellipse inclinée est toujours une ellipse.

Bien que Xamarin.Forms définit les propriétés de translation, de mise à l’échelle et de rotations, il n’y a aucune propriété correspondante dans Xamarin.Forms pour skew.

La [`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) méthode de `SKCanvas` accepte deux arguments pour l’inclinaison horizontale et l’inclinaison verticale :

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Une deuxième [`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) méthode combine ces arguments dans une `SKPoint` valeur unique :

```csharp
public void Skew (SKPoint skew)
```

Toutefois, il est peu probable que vous utilisiez l’une de ces deux méthodes de manière isolée.

La page de l' **expérience skew** vous permet d’expérimenter les valeurs d’inclinaison comprises entre-10 et 10. Une chaîne de texte est positionnée dans l’angle supérieur gauche de la page, avec des valeurs d’inclinaison obtenues à partir de deux `Slider` éléments. Voici le `PaintSurface` Gestionnaire de la [`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

Les valeurs de l' `xSkew` argument décalent le bas du texte vers la droite pour les valeurs positives ou vers la gauche pour les valeurs négatives. Valeurs de `ySkew` décalage vers la droite du texte pour les valeurs positives ou vers le haut pour les valeurs négatives :

[![](skew-images/skewexperiment-small.png "Triple screenshot of the Skew Experiment page")](skew-images/skewexperiment-large.png#lightbox "Triple screenshot of the Skew Experiment page")

Si la `xSkew` valeur est la valeur négative de la `ySkew` valeur, le résultat est une rotation, mais également un peu mis à l’échelle.

Les formules de transformation sont les suivantes :

x' = x + xSkew · y

y' = ySkew · x + y

Par exemple, pour une `xSkew` valeur positive, la valeur transformée `x'` augmente au fur et à mesure que `y` augmente. C’est ce qui est à l’origine de l’inclinaison.

Si un triangle de 200 pixels de large et de 100 pixels de haut est positionné avec son angle supérieur gauche au point (0,0) et s’il est rendu avec la `xSkew` valeur 1,5, les résultats du parallélogramme suivant sont les suivants :

![](skew-images/skeweffect.png "The effect of the skew transform on a rectangle")

Les coordonnées du bord inférieur ont `y` des valeurs de 100, donc elles sont décalées de 150 pixels vers la droite.

Pour les valeurs non null de `xSkew` ou `ySkew` , seul le point (0, 0) reste le même. Ce point peut être considéré comme le centre de l’inclinaison. Si vous avez besoin que le centre d’inclinaison soit autre chose (ce qui est généralement le cas), aucune méthode ne le `Skew` fournit. Vous devez combiner explicitement `Translate` les appels avec l' `Skew` appel. Pour centrer l’inclinaison à l’adresse `px` et `py` , effectuez les appels suivants :

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Les formules de transformation composite sont les suivantes :

x' = x + xSkew · (y – PY)

y' = ySkew · (x – PX) + y

Si `ySkew` est égal à zéro, la `px` valeur n’est pas utilisée. La valeur n’est pas pertinente, et de la même façon pour `ySkew` et `py` .

Vous pouvez vous sentir plus à l’aise avec la spécification de l’inclinaison comme un angle d’inclinaison, tel que l’angle α dans ce diagramme :

![](skew-images/skewangleeffect.png "The effect of the skew transform on a rectangle with a skewing angle indicated")

Le rapport entre le décalage de 150 pixels et le décalage vertical de 100 pixels est la tangente de cet angle, dans cet exemple 56,3 degrés.

Le fichier XAML de la page **expérimentation d’angle d’inclinaison** est similaire à la page angle d' **inclinaison** , à ceci près que les `Slider` éléments vont de – 90 degrés à 90 degrés. Le [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) fichier code-behind centre le texte sur la page et utilise `Translate` pour définir un centre d’inclinaison au centre de la page. Une `SkewDegrees` méthode Short en bas du code convertit des angles en valeurs d’inclinaison :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

Comme angle s’approche de 90 degrés positifs ou négatifs, la tangente est proche de l’infini, mais les angles d’environ 80 degrés sont utilisables :

[![](skew-images/skewangleexperiment-small.png "Triple screenshot of the Skew Angle Experiment page")](skew-images/skewangleexperiment-large.png#lightbox "Triple screenshot of the Skew Angle Experiment page")

Une petite inclinaison horizontale négative peut imiter le texte en gras ou en italique, comme le montre la page de **texte oblique** . La [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe montre comment effectuer cette opération :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

La `TextAlign` propriété de `SKPaint` a la valeur `Center` . Sans transformations, l' `DrawText` appel avec des coordonnées de (0,0) positionnerait le texte avec le centre horizontal de la ligne de base dans l’angle supérieur gauche. Le `SkewDegrees` incline le texte horizontalement de 20 degrés par rapport à la ligne de base. L' `Translate` appel déplace le centre horizontal de la ligne de base du texte au centre de la zone de dessin :

[![](skew-images/obliquetext-small.png "Triple screenshot of the Oblique Text page")](skew-images/obliquetext-large.png#lightbox "Triple screenshot of the Oblique Text page")

La page de **texte ombre inclinée** montre comment utiliser une combinaison d’une inclinaison à 45 degrés et d’une échelle verticale pour créer une ombre de texte qui s’éloigne du texte. Voici la partie pertinente du `PaintSurface` Gestionnaire :

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

L’ombre s’affiche en premier, puis le texte :

[![](skew-images/skewshadowtext1-small.png "Triple screenshot of the Skew Shadow Text page")](skew-images/skewshadowtext1-large.png#lightbox "Triple screenshot of the Skew Shadow Text page")

La coordonnée verticale passée à la `DrawText` méthode indique la position du texte par rapport à la ligne de base. Il s’agit de la même coordonnée verticale que celle utilisée pour le centre d’inclinaison. Cette technique ne fonctionne pas si la chaîne de texte contient des descendants. Par exemple, remplacez le mot « excentrique » par « Shadow » et voici le résultat :

[![](skew-images/skewshadowtext2-small.png "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")](skew-images/skewshadowtext2-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")

L’ombre et le texte sont toujours alignés au niveau de la ligne de base, mais l’effet semble incorrect. Pour résoudre ce problème, vous devez obtenir les limites du texte :

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

Les `Translate` appels doivent être ajustés par la hauteur des descendants :

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Désormais, l’ombre s’étend du bas de ces descendants :

[![](skew-images/skewshadowtext3-small.png "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")](skew-images/skewshadowtext3-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
