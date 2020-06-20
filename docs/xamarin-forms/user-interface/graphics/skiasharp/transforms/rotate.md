---
title: La transformation de rotation
description: Cet article explore les effets et les animations possibles avec la transformation de rotation SkiaSharp et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 520c4c3b61049bf17c2c964523714db196da6839
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132182"
---
# <a name="the-rotate-transform"></a>La transformation de rotation

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorez les effets et les animations possibles avec la transformation de rotation SkiaSharp_

Avec la transformation de rotation, les objets graphiques SkiaSharp sautent la contrainte d’alignement avec les axes horizontal et vertical :

![](rotate-images/rotateexample.png "Text rotated around a center")

Pour faire pivoter un objet graphique autour du point (0, 0), SkiaSharp prend en charge à la fois une [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) méthode et une [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) méthode :

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un cercle de 360 degrés est identique à twoπ radians, c’est pourquoi il est facile de procéder à une conversion entre les deux unités. Utilisez celui qui est le plus pratique. Toutes les fonctions trigonométriques de la [`Math`](xref:System.Math) classe .NET utilisent des unités de radians.

La rotation est dans le sens horaire pour l’importance des angles. (Bien que la rotation sur le système de coordonnées cartésien soit le sens dans le sens inverse des aiguilles d’une montre, la rotation dans le sens des aiguilles d’une montre est cohérente avec les coordonnées Y qui s’accroissent dans SkiaSharp. Les angles négatifs et les angles supérieurs à 360 degrés sont autorisés.

Les formules de transformation pour la rotation sont plus complexes que celles pour la translation et l’échelle. Pour un angle de α, les formules de transformation sont les suivantes :

x' = x • cos (α) – y • Sin (α)   

y' = x • Sin (α) + y • cos (α)

La page de **rotation de base** illustre la `RotateDegrees` méthode. Le fichier [**BasicRotate.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) affiche du texte avec sa ligne de base centrée sur la page et le fait pivoter en fonction d’un `Slider` avec une plage de – 360 à 360. Voici la partie pertinente du `PaintSurface` Gestionnaire :

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Étant donné que la rotation est centrée autour de l’angle supérieur gauche de la zone de dessin, pour la plupart des angles définis dans ce programme, le texte est pivoté hors de l’écran :

[![](rotate-images/basicrotate-small.png "Triple screenshot of the Basic Rotate page")](rotate-images/basicrotate-large.png#lightbox "Triple screenshot of the Basic Rotate page")

Très souvent, vous souhaiterez faire pivoter un point centré autour d’un point pivot spécifié à l’aide de ces versions des [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) méthodes et :

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

La page de **rotation centrée** est identique à la **rotation de base** , sauf que la version développée de `RotateDegrees` est utilisée pour définir le centre de rotation sur le même point utilisé pour positionner le texte :

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

À présent, le texte pivote autour du point utilisé pour positionner le texte, qui est le centre horizontal de la ligne de base du texte :

[![](rotate-images/centeredrotate-small.png "Triple screenshot of the Centered Rotate page")](rotate-images/centeredrotate-large.png#lightbox "Triple screenshot of the Centered Rotate page")

Comme avec la version centrée de la `Scale` méthode, la version centrée de l' `RotateDegrees` appel est un raccourci. Voici la méthode :

```csharp
RotateDegrees (degrees, px, py);
```

Cet appel équivaut à ce qui suit :

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Vous découvrirez que vous pouvez parfois combiner `Translate` des appels avec des `Rotate` appels. Par exemple, Voici les `RotateDegrees` appels et `DrawText` dans la page de **rotation centrée** ;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

L' `RotateDegrees` appel équivaut à deux `Translate` appels et à un non centré `RotateDegrees` :

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

L' `DrawText` appel pour afficher du texte à un emplacement particulier est équivalent à un `Translate` appel de cet emplacement suivi par `DrawText` au point (0,0) :

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Les deux appels consécutifs sont `Translate` annulés :

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Conceptuellement, les deux transformations sont appliquées dans l’ordre inverse à la manière dont elles apparaissent dans le code. L' `DrawText` appel affiche le texte dans l’angle supérieur gauche de la zone de dessin. L' `RotateDegrees` appel fait pivoter ce texte par rapport à l’angle supérieur gauche. L' `Translate` appel déplace alors le texte au centre de la zone de dessin.

Il existe généralement plusieurs façons de combiner la rotation et la translation. La page de **texte pivoté** crée l’affichage suivant :

[![](rotate-images/rotatedtext-small.png "Triple screenshot of the Rotated Text page")](rotate-images/rotatedtext-large.png#lightbox "Triple screenshot of the Rotated Text page")

Voici le `PaintSurface` Gestionnaire de la [`RotatedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) classe :

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

Les `xCenter` `yCenter` valeurs et indiquent le centre du canevas. La `yText` valeur est un petit décalage par rapport à ce. Cette valeur est la coordonnée Y nécessaire pour positionner le texte afin qu’il soit véritablement centré sur la page. La `for` boucle définit ensuite une rotation en fonction du centre de la zone de dessin. La rotation est par incréments de 30 degrés. Le texte est dessiné à l’aide de la `yText` valeur. Le nombre d’espaces avant le mot « ROTATE » dans la `text` valeur a été déterminé de façon empirique pour que la connexion entre ces 12 chaînes de texte semble être un dodecagon.

L’une des méthodes permettant de simplifier ce code consiste à incrémenter l’angle de rotation de 30 degrés à chaque fois par la boucle après l' `DrawText` appel. Cela élimine la nécessité d’appeler `Save` et `Restore` . Notez que la `degrees` variable n’est plus utilisée dans le corps du `for` bloc :

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

Il est également possible d’utiliser la forme simple de `RotateDegrees` en faisant précéder la boucle d’un appel à `Translate` pour déplacer tout vers le centre de la zone de dessin :

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Le `yText` calcul modifié n’est plus incorporé `yCenter` . À présent `DrawText` , l’appel Centre le texte verticalement en haut du canevas.

Étant donné que les transformations sont appliquées de manière conceptuelle, contrairement à la façon dont elles apparaissent dans le code, il est souvent possible de commencer par des transformations globales supplémentaires, suivies de plus de transformations locales. Il s’agit souvent du moyen le plus simple pour combiner la rotation et la traduction.

Supposons, par exemple, que vous souhaitiez dessiner un objet graphique qui pivote autour de son centre, comme une planète tournant sur son axe. Mais vous souhaitez également que cet objet tourne autour du centre de l’écran, comme une planète qui tourne autour du soleil.

Pour ce faire, vous pouvez positionner l’objet dans l’angle supérieur gauche de la zone de dessin, puis utiliser une animation pour la faire pivoter autour de cet angle. Ensuite, Traduisez l’objet horizontalement comme un rayon orbital. Appliquez à présent une deuxième rotation animée, également autour de l’origine. Ainsi, l’objet tourne autour de l’angle. À présent, Traduisez au centre de la zone de dessin.

Voici le `PaintSurface` gestionnaire qui contient ces appels de transformation dans l’ordre inverse :

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

Les `revolveDegrees` `rotateDegrees` champs et sont animés. Ce programme utilise une autre technique d’animation basée sur la Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) classe. (Cette classe est décrite dans le [Chapitre 22 de la rubrique *création d’Mobile Apps Xamarin.Forms avec * ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) la `OnAppearing` substitution crée deux `Animation` objets avec des méthodes de rappel, puis appelle `Commit` sur ceux-ci pour une durée d’animation :

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

Le premier `Animation` objet s’anime `revolveDegrees` de 0 degrés à 360 degrés sur 10 secondes. La seconde effectue une animation `rotateDegrees` de 0 degré à 360 degrés toutes les 1 seconde et invalide également la surface pour générer un autre appel au `PaintSurface` Gestionnaire. Le `OnDisappearing` remplacement annule ces deux animations :

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

Le programme d' **horloge analogique** de petite ampleur (appelé, car une horloge analogique plus attrayante est décrite dans un article ultérieur) utilise la rotation pour dessiner les points de minute et d’heure de l’horloge et pour faire pivoter les mains. Le programme dessine l’horloge à l’aide d’un système de coordonnées arbitraire basé sur un cercle centré au point (0,0) avec un rayon de 100. Elle utilise la translation et la mise à l’échelle pour développer et centrer ce cercle sur la page.

Les `Translate` `Scale` appels et s’appliquent globalement à l’horloge, donc ceux-ci sont les premiers à être appelés après l’initialisation des `SKPaint` objets :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}
```

Il y a 60 marques de deux tailles différentes qui doivent être dessinées dans un cercle autour de l’horloge. L' `DrawCircle` appel dessine ce cercle au point (0, – 90), qui est relatif au centre de l’horloge correspond à 12:00. L' `RotateDegrees` appel incrémente l’angle de rotation de 6 degrés après chaque graduation. La `angle` variable est utilisée uniquement pour déterminer si un grand cercle ou un petit cercle est dessiné :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

Enfin, le `PaintSurface` Gestionnaire obtient l’heure actuelle et calcule les degrés de rotation pour les heures, les minutes et les secondes. Chaque main est dessinée dans la position 12:00 afin que l’angle de rotation soit relatif à ce qui suit :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

L’horloge est certainement fonctionnelle bien que les mains soient plutôt brutes :

[![](rotate-images/uglyanalogclock-small.png "Triple screenshot of the Ugly Analog Clock Text page")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

Pour obtenir une horloge plus attrayante, consultez l’article [**données du chemin d’accès SVG dans SkiaSharp**](../curves/path-data.md).

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
