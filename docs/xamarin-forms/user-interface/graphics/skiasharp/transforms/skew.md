---
title: La transformation d’inclinaison
description: Cet article explique comment la transformation d’inclinaison peut créer des objets graphiques inclinés dans SkiaSharp et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed1bcbed86874362e291fb23ce86dea8992d3408
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563729"
---
# <a name="the-skew-transform"></a>La transformation d’inclinaison

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Voir comment la transformation d’inclinaison peut créer des objets graphiques inclinés dans SkiaSharp_

Dans SkiaSharp, la transformation d’inclinaison incline les objets graphiques, tels que l’ombre dans cette image :

![Exemple de décalage à partir du programme de texte de l’ombre inclinée](skew-images/skewexample.png)

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

[![Capture d’écran triple de la page de l’expérience Skew](skew-images/skewexperiment-small.png)](skew-images/skewexperiment-large.png#lightbox "Capture d’écran triple de la page de l’expérience Skew")

Si la `xSkew` valeur est la valeur négative de la `ySkew` valeur, le résultat est une rotation, mais également un peu mis à l’échelle.

Les formules de transformation sont les suivantes :

x' = x + xSkew · y

y' = ySkew · x + y

Par exemple, pour une `xSkew` valeur positive, la valeur transformée `x'` augmente au fur et à mesure que `y` augmente. C’est ce qui est à l’origine de l’inclinaison.

Si un triangle de 200 pixels de large et de 100 pixels de haut est positionné avec son angle supérieur gauche au point (0,0) et s’il est rendu avec la `xSkew` valeur 1,5, les résultats du parallélogramme suivant sont les suivants :

![Effet de la transformation d’inclinaison sur un rectangle](skew-images/skeweffect.png)

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

![Effet de la transformation d’inclinaison sur un rectangle avec un angle d’inclinaison indiqué](skew-images/skewangleeffect.png)

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

[![Capture d’écran triple de la page d’expérimentation d’angle d’inclinaison](skew-images/skewangleexperiment-small.png)](skew-images/skewangleexperiment-large.png#lightbox "Capture d’écran triple de la page d’expérimentation d’angle d’inclinaison")

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

[![Capture d’écran triple de la page de texte oblique](skew-images/obliquetext-small.png)](skew-images/obliquetext-large.png#lightbox "Capture d’écran triple de la page de texte oblique")

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

[![Capture d’écran triple de la page de texte ombre inclinée](skew-images/skewshadowtext1-small.png)](skew-images/skewshadowtext1-large.png#lightbox "Capture d’écran triple de la page de texte ombre inclinée")

La coordonnée verticale passée à la `DrawText` méthode indique la position du texte par rapport à la ligne de base. Il s’agit de la même coordonnée verticale que celle utilisée pour le centre d’inclinaison. Cette technique ne fonctionne pas si la chaîne de texte contient des descendants. Par exemple, remplacez le mot « excentrique » par « Shadow » et voici le résultat :

[![Capture d’écran triple de la page de texte ombre inclinée avec un autre mot avec des descendants](skew-images/skewshadowtext2-small.png)](skew-images/skewshadowtext2-large.png#lightbox "Capture d’écran triple de la page de texte ombre inclinée avec un autre mot avec des descendants")

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

[![Capture d’écran triple de la page de texte d’ombre inclinée avec ajustements pour les descendants](skew-images/skewshadowtext3-small.png)](skew-images/skewshadowtext3-large.png#lightbox "Capture d’écran triple de la page de texte d’ombre inclinée avec ajustements pour les descendants")

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)