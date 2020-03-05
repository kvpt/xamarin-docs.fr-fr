---
title: La transformation d’échelle
description: Thhis article explore la transformation d’échelle SkiaSharp mise à l’échelle des objets à différentes tailles et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 7dc7a2faabef86aa63b52739edda696efcb54aba
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291576"
---
# <a name="the-scale-transform"></a>La transformation d’échelle

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Détection de la transformation d’échelle SkiaSharp pour la mise à l’échelle d’objets à différentes tailles_

Comme vous l’avez vu dans [**l’article traduire la transformation**](translate.md) , la transformation de traduction peut déplacer un objet graphique d’un emplacement à un autre. En revanche, la transformation d’échelle modifie la taille de l’objet de graphique :

![](scale-images/scaleexample.png "A tall word scaled in size")

La transformation d’échelle entraîne également souvent les coordonnées des graphiques déplacer que ceux-ci sont rendus plus volumineux.

Précédemment, vous avez vu deux formules de transformation qui décrivent les effets des facteurs de traduction de `dx` et `dy`:

x' = x + dx

y' = y + dy

Les facteurs de mise à l’échelle des `sx` et des `sy` sont multiplicatives et non additives :

x' = mise en sx x

y' = sy du y

Les valeurs par défaut des facteurs de translation sont 0 ; les valeurs par défaut les facteurs d’échelle sont 1.

La classe `SKCanvas` définit quatre méthodes `Scale`. La première [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) méthode est pour les cas où vous souhaitez le même facteur d’échelle horizontale et verticale :

```csharp
public void Scale (Single s)
```

C’est ce que l’on appelle la mise à l’échelle *isotrope* &mdash; la mise à l’échelle qui est identique dans les deux sens. Mise à l’échelle isotropes conserve les proportions de l’objet.

La deuxième méthode [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) vous permet de spécifier des valeurs différentes pour la mise à l’échelle horizontale et verticale :

```csharp
public void Scale (Single sx, Single sy)
```

Cela génère une mise à l’échelle *anisotrope* .
La troisième méthode [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) combine les deux facteurs de mise à l’échelle dans une même valeur de `SKPoint` :

```csharp
public void Scale (SKPoint size)
```

La quatrième méthode `Scale` sera décrite rapidement.

La page de mise à l' **échelle de base** illustre la méthode `Scale`. Le fichier [**BasicScalePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contient deux éléments `Slider` qui vous permettent de sélectionner des facteurs de mise à l’échelle horizontale et verticale entre 0 et 10. Le fichier code-behind [**BasicScalePage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) utilise ces valeurs pour appeler `Scale` avant d’afficher un rectangle arrondi avec une ligne en pointillés et de taille pour ajuster un texte dans l’angle supérieur gauche de la zone de dessin :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Vous vous demandez peut-être : comment les facteurs de mise à l’échelle affectent-ils la valeur retournée par la méthode `MeasureText` de `SKPaint`? La réponse est : pas du tout. `Scale` est une méthode de `SKCanvas`. Elle n’affecte pas les opérations que vous effectuez avec un objet `SKPaint` tant que vous n’utilisez pas cet objet pour restituer un élément sur le canevas.

Comme vous pouvez le voir, tout ce qui est dessiné après l’appel de `Scale` augmente proportionnellement :

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

Le texte, la largeur de la ligne en pointillés, la longueur des tirets dans cette ligne, l’arrondi des angles et la marge de 10 pixels entre les bords gauche et supérieure du canevas et le rectangle à coins arrondis sont toutes susceptibles d’être les mêmes facteurs d’échelle.

> [!IMPORTANT]
> La plateforme Windows universelle ne rend pas correctement le texte à l’échelle anisotropicly.

ANISOTROPIQUE causes de mise à l’échelle la largeur du trait pour devenir différents pour les lignes aligné sur les axes horizontales et verticales. (Cela est également évident dans la première image de cette page.) Si vous ne souhaitez pas que la largeur du trait soit affectée par les facteurs de mise à l’échelle, affectez-lui la valeur 0 et il s’agit toujours d’un pixel de largeur, quel que soit le paramètre de `Scale`.

Mise à l’échelle est relatif à l’angle supérieur gauche de la zone de dessin. Cela peut être exactement ce que vous voulez, mais il ne peut pas être. Supposons que vous souhaitez placer le texte et le rectangle vers un autre emplacement sur le canevas et que vous souhaitez mettre à l’échelle par rapport à son centre. Dans ce cas, vous pouvez utiliser la quatrième version de la méthode [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) , qui comprend deux paramètres supplémentaires pour spécifier le centre de mise à l’échelle :

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Les paramètres `px` et `py` définissent un point qui est parfois appelé le *Centre de mise* à l’échelle, mais dans la documentation SkiaSharp, on parle de *point pivot*. Il s’agit d’un point par rapport à l’angle supérieur gauche de la zone de dessin qui n’est pas affecté par la mise à l’échelle. Mise à l’échelle tous les se produit par rapport à ce centre.

La page de mise à l' [**échelle centrée**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) montre comment cela fonctionne. Le gestionnaire de `PaintSurface` est similaire au programme de mise à l' **échelle de base** , sauf que la valeur de `margin` est calculée pour centrer le texte horizontalement, ce qui implique que le programme fonctionne mieux en mode portrait :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

L’angle supérieur gauche du rectangle arrondi est positionné `margin` pixels à partir de la gauche du canevas et `margin` pixels du haut. Les deux derniers arguments de la méthode `Scale` sont définis sur ces valeurs plus la largeur et la hauteur du texte, qui est également la largeur et la hauteur du rectangle à coins arrondis. Cela signifie que la mise à l’échelle toutes les est par rapport au centre du rectangle :

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Les éléments `Slider` dans ce programme ont une plage de &ndash;10 à 10. Comme vous pouvez le voir, les valeurs négatives vertical mise à l’échelle (par exemple, dans le Android écran dans le centre) entraînent des objets faire pivoter autour de l’axe horizontal qui passe par le centre de mise à l’échelle. Les valeurs négatives horizontal mise à l’échelle (par exemple, comme dans l’écran UWP sur la droite) entraînent des objets faire pivoter autour de l’axe vertical qui transitent dans le centre de mise à l’échelle.

La version de la méthode [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) avec des points pivot est un raccourci pour une série de trois appels `Translate` et `Scale`. Vous souhaiterez peut-être voir comment cela fonctionne en remplaçant la méthode `Scale` dans la page de mise à l' **échelle centrée** par les éléments suivants :

```csharp
canvas.Translate(-px, -py);
```

Voici les valeurs négatives des coordonnées du point pivot.

Réexécutez le programme. Vous verrez que le rectangle et le texte sont décalés afin que le centre est dans le coin supérieur gauche de la zone de dessin. Vous pouvez le voir à peine. Les curseurs ne fonctionnent pas bien sûr, car il est désormais le programme n’augmente pas du tout.

Ajoutez à présent l’appel de base `Scale` (sans le centre de mise à l’échelle) *avant* ce `Translate` appel :

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si vous êtes familiarisé avec cet exercice dans d’autres graphiques programmer des systèmes, vous pouvez penser que qui est incorrect, mais il n’est pas. Skia gère les appels successifs de transformation un peu différemment à partir de ce que vous connaissez peut-être.

Avec les appels `Scale` et `Translate` successifs, le centre du rectangle arrondi est toujours dans l’angle supérieur gauche, mais vous pouvez maintenant le mettre à l’échelle par rapport au coin supérieur gauche de la zone de dessin, qui est également le centre du rectangle arrondi.

Maintenant, avant cet appel `Scale`, ajoutez un autre appel `Translate` avec les valeurs de centrage :

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Cela ramène le résultat à l’échelle à la position d’origine. Ces trois appels sont équivalents à :

```csharp
canvas.Scale(sx, sy, px, py);
```

Les transformations individuelles sont compliquent afin que la formule de transformation totale est :

 x' = mise en sx (x – px) + px

 y' = sy du (y – py) + py

N’oubliez pas que les valeurs par défaut de `sx` et `sy` sont 1. Il est facile de crainte que le point pivot (px, py) n’est pas transformé par ces formules. Il reste au même emplacement par rapport à la zone de dessin.

Lorsque vous combinez des appels `Translate` et `Scale`, l’ordre est important. Si le `Translate` vient après l' `Scale`, les facteurs de traduction sont mis à l’échelle de manière efficace par les facteurs de mise à l’échelle. Si le `Translate` précède le `Scale`, les facteurs de traduction ne sont pas mis à l’échelle. Ce processus devient un peu plus clair (bien que plus mathématiques) lorsque l’objet de matrices de transformation est introduite.

La classe `SKPath` définit une propriété [`Bounds`](xref:SkiaSharp.SKPath.Bounds) en lecture seule qui retourne un `SKRect` définissant l’étendue des coordonnées dans le chemin d’accès. Par exemple, lorsque la propriété `Bounds` est obtenue à partir du chemin d’accès hendecagram créé précédemment, les propriétés `Left` et `Top` du rectangle sont approximativement de 100, les propriétés `Right` et `Bottom` sont approximativement 100, et les propriétés `Width` et `Height` sont environ 200. (La plupart des valeurs réelles est une peu moins, car les points des étoiles sont définis par un cercle avec un rayon de 100, mais seul le point supérieur est parallèle avec les axes horizontales ou verticales).

La disponibilité de ces informations implique qu’il doit être possible de dériver de mise à l’échelle et de traduire les facteurs de mise à l’échelle d’un chemin d’accès à la taille de la zone de dessin. La page de [**mise à l’échelle anisotrope**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) illustre cela à l’aide de l’étoile à 11 branches. Une échelle *anisotrope* signifie qu’elle est inégale dans les directions horizontale et verticale, ce qui signifie que l’étoile ne conservera pas ses proportions d’origine. Voici le code approprié dans le gestionnaire de `PaintSurface` :

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

Le rectangle de `pathBounds` est obtenu vers le haut de ce code, puis utilisé ultérieurement avec la largeur et la hauteur de la zone de dessin dans l’appel de `Scale`. Cet appel à lui-même met à l’échelle les coordonnées du chemin d’accès lorsqu’il est rendu par l’appel de `DrawPath`, mais l’étoile est centrée dans l’angle supérieur droit de la zone de dessin. Il doit être décalés vers le bas et vers la gauche. Il s’agit du travail de l’appel de `Translate`. Ces deux propriétés de `pathBounds` sont approximativement de 100, donc les facteurs de traduction sont d’environ 100. Étant donné que l’appel de `Translate` se fait après l’appel de `Scale`, ces valeurs sont mises à l’échelle de manière efficace par les facteurs de mise à l’échelle, de sorte qu’ils déplacent le centre de l’étoile au centre de la zone de dessin :

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Une autre façon de penser aux appels `Scale` et `Translate` consiste à déterminer l’effet en séquence inverse : l’appel `Translate` déplace le chemin d’accès afin qu’il devienne entièrement visible mais orienté dans le coin supérieur gauche du canevas. La méthode `Scale` rend ensuite cette étoile plus grande par rapport à l’angle supérieur gauche.

En fait, il apparaît que l’étoile est légèrement supérieure à la zone de dessin. Le problème est la largeur du trait. La propriété `Bounds` de `SKPath` indique les dimensions des coordonnées encodées dans le tracé, et c’est ce que le programme utilise pour le mettre à l’échelle. Lorsque le chemin d’accès est affiché avec une largeur de trait particulier, le chemin d’accès affiché est supérieure à la zone de dessin.

Pour résoudre ce problème, vous devez compenser pour ce faire. Une approche simple dans ce programme consiste à ajouter l’instruction suivante juste avant l’appel de `Scale` :

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Cela augmente le rectangle de `pathBounds` de 1,5 unités sur les quatre côtés. Il s’agit d’une solution raisonnable uniquement lorsque la jointure de trait est arrondie. Un joint d’angle peut être plus longue et difficile à calculer.

Vous pouvez également utiliser une technique similaire avec du texte, comme le montre la page de **texte anisotrope** . Voici la partie pertinente du gestionnaire de `PaintSurface` de la classe [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) :

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Il s’agit d’une logique similaire, et le texte s’étend sur la taille de la page en fonction du rectangle de limites de texte retourné à partir de `MeasureText` (ce qui est un peu plus grand que le texte réel) :

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Si vous avez besoin de conserver les proportions des objets graphiques, vous souhaitez utiliser la mise à l’échelle isotropes. La page de mise à l' **échelle isotrope** illustre cela pour l’étoile à 11 branches. Conceptuellement, les étapes pour l’affichage d’un objet graphique dans le centre de la page avec la mise à l’échelle isotropes sont :

- Traduire le centre de l’objet graphique à l’angle supérieur gauche.
- Mettre à l’échelle de l’objet basé sur la valeur minimale des dimensions de page horizontaux et verticaux divisée par les dimensions du graphique d’objet.
- Traduire le centre de l’objet mis à l’échelle vers le centre de la page.

La [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) effectue ces étapes dans l’ordre inverse avant d’afficher l’étoile :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

Le code affiche également l’étoile 10 fois de plus, chaque fois que la diminution de la mise à l’échelle du facteur de 10 et progressivement en modifiant la couleur du rouge au bleu :

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
