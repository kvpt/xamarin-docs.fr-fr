---
title: La transformation d’inclinaison
description: Cet article explique comment la transformation d’inclinaison peut créer des objets graphiques inclinées dans SkiaSharp et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: 0592f80b7d7352463ba22d7d371cc1b18ac3e0be
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655581"
---
# <a name="the-skew-transform"></a>La transformation d’inclinaison

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment la transformation d’inclinaison peut créer des objets graphiques inclinées dans SkiaSharp_

Dans SkiaSharp, la transformation d’inclinaison montre comment faire pivoter des objets graphiques, tels que l’ombre dans cette image :

![](skew-images/skewexample.png "Un exemple de l’inclinaison à partir du programme de décalage de texte de clichés instantanés")

Le décalage transforme un rectangle en un parallélogramme, mais une ellipse décalée est toujours une ellipse.

Bien que Xamarin.Forms définit des propriétés pour la traduction, la mise à l’échelle et les rotations, il n’existe aucune propriété correspondante, dans Xamarin.Forms de skew.

Le [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) méthode `SKCanvas` accepte deux arguments pour le décalage horizontal et vertical d’inclinaison :

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Une seconde [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) méthode combine ces arguments dans un seul `SKPoint` valeur :

```csharp
public void Skew (SKPoint skew)
```

Toutefois, il est peu probable que vous allez utiliser une de ces deux méthodes de manière isolée.

Le **incliner faire des essais** page permet de vous faire des essais avec décalage des valeurs comprises entre -10 et 10. Une chaîne de texte est positionnée dans le coin supérieur gauche de la page, avec un décalage valeurs obtenues à partir de deux `Slider` éléments. Voici le `PaintSurface` gestionnaire dans le [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe :

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

Valeurs de la `xSkew` argument shift bas du texte pour les valeurs positives droite ou gauche pour les valeurs négatives. Valeurs de `ySkew` décalage vers la droite du texte vers le bas pour les valeurs positives ou pour les valeurs négatives :

[![](skew-images/skewexperiment-small.png "Capture d’écran triple de la page d’expérience d’inclinaison")](skew-images/skewexperiment-large.png#lightbox "Triple capture d’écran de la page d’expérience d’inclinaison")

Si le `xSkew` valeur est la valeur négative de la `ySkew` valeur, le résultat correspond à la rotation, mais également à l’échelle un peu comme l’affichage UWP indique.

Les formules de transformation sont les suivantes :

x' = x + xSkew mise en y

y' = ySkew du x + y

Par exemple, pour un nombre positif `xSkew` valeur transformée `x'` valeur augmente en tant que `y` augmente. C’est ce qui provoque l’inclinaison.

Si un pixels de triangle 200 large et 100 pixels de haut est positionné à son angle supérieur gauche au niveau du point (0, 0) et est rendu avec un `xSkew` valeur de 1.5, les résultats du parallélogramme suivants :

![](skew-images/skeweffect.png "L’effet de la transformation d’inclinaison sur un rectangle")

Les coordonnées du bord inférieur ont `y` valeurs de 100, donc il est décalés 150 pixels vers la droite.

Pour les valeurs non nulles de `xSkew` ou `ySkew`, seul le point (0, 0) reste le même. Ce point peut être considéré comme le centre de l’inclinaison. Si vous avez besoin au centre de l’inclinaison pour être autre chose (qui est généralement le cas), il existe aucune `Skew` méthode qui fournit à qui. Vous devrez combiner explicitement `Translate` appelle avec le `Skew` appeler. Pour centrer la déformation à `px` et `py`, effectuez les appels suivants :

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Les formules de la transformation composite sont :

x' = x + xSkew mise en (py y –)

y' = ySkew du (x – px) + y

Si `ySkew` est égal à zéro, puis le `px` valeur n’est pas utilisée. La valeur n’est pas pertinente et même pour `ySkew` et `py`.

Vous vous sentirez plus à l’aise en spécifiant un décalage comme un angle d’inclinaison, telles que l’angle α dans ce diagramme :

![](skew-images/skewangleeffect.png "L’effet de la transformation d’inclinaison sur un rectangle avec un angle d’inclinaison indiqué")

Le rapport entre le passage de 150 pixels à la verticale de 100 pixels est la tangente de cet angle, dans cet exemple et de 56,3 degrés.

Le fichier XAML de la **expérience de Angle d’inclinaison** page est similaire à la **Angle d’inclinaison** page, sauf que le `Slider` éléments comprise entre – 90 degrés et 90 degrés. Le [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) fichier code-behind Centre le texte sur la page et utilise `Translate` pour définir un centre de l’inclinaison au centre de la page. Short `SkewDegrees` méthode en bas du code convertit les angles pour incliner des valeurs :

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

L’approche un angle positifs ou négatifs de 90 degrés, la tangente est l’infini proche, mais les angles jusqu'à environ 80 degrés ou ainsi sont utilisables :

[![](skew-images/skewangleexperiment-small.png "Capture d’écran triple de la page de l’expérience de Angle d’inclinaison")](skew-images/skewangleexperiment-large.png#lightbox "Triple capture d’écran de la page de l’expérience de Angle d’inclinaison")

Une inclinaison horizontale négatif petite peut simuler oblique ou en italique du texte, comme le **texte Oblique** montre de page. Le [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe montre comment procéder :

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

Le `TextAlign` propriété du `SKPaint` est défini sur `Center`. Sans les transformations, le `DrawText` appeler avec les coordonnées de (0, 0) positionner le texte au centre horizontal de la ligne de base dans l’angle supérieur gauche. Le `SkewDegrees` incline le texte horizontalement 20 degrés par rapport à la ligne de base. Le `Translate` appel déplace au centre horizontal de la ligne de base du texte au centre de la zone de dessin :

[![](skew-images/obliquetext-small.png "Capture d’écran triple de la page de texte Oblique")](skew-images/obliquetext-large.png#lightbox "Triple capture d’écran de la page de texte Oblique")

Le **incliner le texte ombré** page montre comment utiliser une combinaison d’une échelle de décalage et verticale de 45 degrés pour rendre une ombre du texte qui fait pivoter en dehors du texte. Voici la partie pertinente de la `PaintSurface` gestionnaire :

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

L’ombre est affiché en premier, puis le texte :

[![](skew-images/skewshadowtext1-small.png "Capture d’écran triple de la page de texte de clichés instantanés incliner")](skew-images/skewshadowtext1-large.png#lightbox "Triple capture d’écran de la page de texte de clichés instantanés d’inclinaison")

Coordonnée verticale est passé à la `DrawText` méthode indique la position du texte par rapport à la ligne de base. Qui est la coordonnée verticale même utilisée pour le centre de l’inclinaison. Cette technique ne fonctionne pas si la chaîne de texte contient hampes inférieures. Par exemple, le mot « étrange » de « Fantôme » et Voici le résultat :

[![](skew-images/skewshadowtext2-small.png "Capture d’écran triple de la page de texte de clichés instantanés incliner avec un autre mot descendants")](skew-images/skewshadowtext2-large.png#lightbox "Triple capture d’écran de la page de texte de clichés instantanés incliner avec un autre mot descendants")

Les clichés instantanés et le texte sont toujours alignées sur la ligne de base, mais l’effet est bien incorrect. Pour corriger ce problème, vous devez obtenir les limites de texte :

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

Le `Translate` appels doivent être ajustés par la hauteur de la hampe inférieure :

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

L’ombre s’étend maintenant en bas de ces descendants :

[![](skew-images/skewshadowtext3-small.png "Capture d’écran triple de la page de texte de clichés instantanés incliner avec ajustements pour hampes")](skew-images/skewshadowtext3-large.png#lightbox "Triple capture d’écran de la page de texte de clichés instantanés incliner avec ajustements pour hampes")


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
