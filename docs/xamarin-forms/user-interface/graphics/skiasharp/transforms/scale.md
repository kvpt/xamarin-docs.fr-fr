---
title: La transformation d’échelle
description: Thhis article explore la transformation d’échelle SkiaSharp mise à l’échelle des objets à différentes tailles et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 2e9259bed6ad0ae5a926cb75ea74c1f379897220
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649286"
---
# <a name="the-scale-transform"></a>La transformation d’échelle

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrir la transformation d’échelle pour la mise à l’échelle des objets à différentes tailles de SkiaSharp_

Comme vous l’avez vu dans [ **le traduire transformer** ](translate.md) article, la transformation de traduction peut déplacer un objet graphique à partir d’un emplacement vers un autre. En revanche, la transformation d’échelle modifie la taille de l’objet de graphique :

![](scale-images/scaleexample.png "Un mot en hauteur mise à l’échelle la taille")

La transformation d’échelle entraîne également souvent les coordonnées des graphiques déplacer que ceux-ci sont rendus plus volumineux.

Vous avez vu précédemment deux formules de transformation qui décrivent les effets de facteurs de translation de `dx` et `dy`:

x' = x + dx

y' = y + dy

Mettre à l’échelle des facteurs de `sx` et `sy` sont des multiples au lieu d’addition :

x' = mise en sx x

y' = sy du y

Les valeurs par défaut des facteurs de translation sont 0 ; les valeurs par défaut les facteurs d’échelle sont 1.

Le `SKCanvas` classe définit quatre `Scale` méthodes. La première [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single)) méthode est pour tenir compte des cas où vous souhaitez que le même horizontal et vertical mise à l’échelle :

```csharp
public void Scale (Single s)
```

Il s’agit *isotropes* mise à l’échelle &mdash; mise à l’échelle qui est le même dans les deux sens. Mise à l’échelle isotropes conserve les proportions de l’objet.

La seconde [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) méthode vous permet de spécifier des valeurs différentes pour la mise à l’échelle horizontale et verticale :

```csharp
public void Scale (Single sx, Single sy)
```

Il en résulte *ANISOTROPIQUE* mise à l’échelle.
La troisième [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) méthode combine les deux facteurs d’échelle dans un seul `SKPoint` valeur :

```csharp
public void Scale (SKPoint size)
```

La quatrième `Scale` méthode sera bientôt décrite.

Le **mise à l’échelle base** page montre le `Scale` (méthode). Le [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) fichier contient deux `Slider` les éléments qui vous permettent de sélectionner des facteurs de mise à l’échelle horizontale et verticale comprise entre 0 et 10. Le [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) fichier code-behind utilise ces valeurs pour appeler `Scale` avant d’afficher un rectangle arrondi rayée avec une ligne en pointillés et dimensionnée en fonction du texte dans l’angle supérieur gauche angle de la zone :

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

Vous vous demandez peut-être: Comment les facteurs de mise à l’échelle affectent-ils la `MeasureText` valeur retournée par la méthode de? `SKPaint` La réponse est la suivante: Pas du tout. `Scale` est une méthode de `SKCanvas`. Il n’affecte pas tout ce que vous le faites avec un `SKPaint` jusqu'à ce que cet objet vous permet de restituer un élément de la zone de dessin de l’objet.

Comme vous pouvez le voir, tous les éléments dessinés après le `Scale` appeler augmente proportionnellement :

[![](scale-images/basicscale-small.png "Capture d’écran triple de la page de mise à l’échelle base")](scale-images/basicscale-large.png#lightbox "Triple capture d’écran de la page de base à l’échelle")

Le texte, la largeur de la ligne en pointillés, la longueur des tirets dans cette ligne, l’arrondi des angles et la marge de 10 pixels entre les bords gauche et supérieure du canevas et le rectangle à coins arrondis sont toutes susceptibles d’être les mêmes facteurs d’échelle.

> [!IMPORTANT]
> La plateforme Windows universelle ne rend pas correctement le texte à l’échelle anisotropicly.

ANISOTROPIQUE causes de mise à l’échelle la largeur du trait pour devenir différents pour les lignes aligné sur les axes horizontales et verticales. (Ceci est également évident à partir de la première image dans cette page.) Si vous ne souhaitez pas la largeur du trait pour être affectées par les facteurs d’échelle, affectez-lui la valeur 0 et il sera toujours un pixel de large quel que soit le `Scale` paramètre.

Mise à l’échelle est relatif à l’angle supérieur gauche de la zone de dessin. Cela peut être exactement ce que vous voulez, mais il ne peut pas être. Supposons que vous souhaitez placer le texte et le rectangle vers un autre emplacement sur le canevas et que vous souhaitez mettre à l’échelle par rapport à son centre. Dans ce cas, vous pouvez utiliser la quatrième version de la [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) (méthode), qui inclut deux paramètres supplémentaires pour spécifier le centre de mise à l’échelle :

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Le `px` et `py` paramètres définissent un point qui est parfois appelé le *centre de mise à l’échelle* mais dans le SkiaSharp documentation est appelée un *point pivot*. Il s’agit d’un point par rapport à l’angle supérieur gauche de la zone de dessin qui n’est pas affecté par la mise à l’échelle. Mise à l’échelle tous les se produit par rapport à ce centre.

Le [ **mise à l’échelle centré** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) page montre comment cela fonctionne. Le `PaintSurface` gestionnaire est similaire à la **mise à l’échelle base** du programme, sauf que le `margin` la valeur est calculée pour centrer le texte horizontalement, ce qui implique que le programme fonctionne mieux en mode portrait :

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

Le coin supérieur gauche du rectangle arrondi est positionné `margin` pixels à partir de la gauche du canevas et `margin` pixels à partir du haut. Les deux derniers arguments à la `Scale` méthode sont définies sur ces valeurs ainsi que la largeur et la hauteur du texte, qui est également la largeur et hauteur du rectangle arrondi. Cela signifie que la mise à l’échelle toutes les est par rapport au centre du rectangle :

[![](scale-images/centeredscale-small.png "Capture d’écran triple de la page de mise à l’échelle centré")](scale-images/centeredscale-large.png#lightbox "Triple capture d’écran de la page de mise à l’échelle centré")

Le `Slider` éléments dans ce programme dispose d’une plage de &ndash;10 à 10. Comme vous pouvez le voir, les valeurs négatives vertical mise à l’échelle (par exemple, dans le Android écran dans le centre) entraînent des objets faire pivoter autour de l’axe horizontal qui passe par le centre de mise à l’échelle. Les valeurs négatives horizontal mise à l’échelle (par exemple, comme dans l’écran UWP sur la droite) entraînent des objets faire pivoter autour de l’axe vertical qui transitent dans le centre de mise à l’échelle.

La version de la [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) méthode avec les points pivot est un raccourci pour une série de trois `Translate` et `Scale` appels. Vous pouvez souhaiter voir comment cela fonctionne en remplaçant le `Scale` méthode dans le **mise à l’échelle centré** page par le code suivant :

```csharp
canvas.Translate(-px, -py);
```

Voici les valeurs négatives des coordonnées du point pivot.

Réexécutez le programme. Vous verrez que le rectangle et le texte sont décalés afin que le centre est dans le coin supérieur gauche de la zone de dessin. Vous pouvez le voir à peine. Les curseurs ne fonctionnent pas bien sûr, car il est désormais le programme n’augmente pas du tout.

Ajoutez maintenant le basic `Scale` appeler (sans un centre de mise à l’échelle) *avant* qui `Translate` appeler :

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si vous êtes familiarisé avec cet exercice dans d’autres graphiques programmer des systèmes, vous pouvez penser que qui est incorrect, mais il n’est pas. Skia gère les appels successifs de transformation un peu différemment à partir de ce que vous connaissez peut-être.

Avec le successives `Scale` et `Translate` appels, le centre du rectangle arrondi est toujours dans le coin supérieur gauche, mais vous pouvez désormais mettre à l’échelle par rapport à l’angle supérieur gauche du canevas, ce qui correspond également au centre du rectangle arrondi.

À présent, avant cela `Scale` appeler, ajoutez un autre `Translate` appeler avec les valeurs de centrage :

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

Lorsque vous combinez `Translate` et `Scale` appels, l’ordre est important. Si le `Translate` vient après le `Scale`, les facteurs de translation sont efficacement à l’échelle les facteurs d’échelle. Si le `Translate` vient avant le `Scale`, les facteurs de traduction ne sont pas à l’échelle. Ce processus devient un peu plus clair (bien que plus mathématiques) lorsque l’objet de matrices de transformation est introduite.

Le `SKPath` classe définit en lecture seule [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) propriété qui retourne un `SKRect` à définir l’étendue des coordonnées dans le chemin d’accès. Par exemple, lorsque le `Bounds` propriété est obtenue à partir du chemin hendecagram créé précédemment, le `Left` et `Top` propriétés du rectangle sont d’environ -100, le `Right` et `Bottom` sont des propriétés environ 100 et le `Width` et `Height` propriétés sont environ 200. (La plupart des valeurs réelles est une peu moins, car les points des étoiles sont définis par un cercle avec un rayon de 100, mais seul le point supérieur est parallèle avec les axes horizontales ou verticales).

La disponibilité de ces informations implique qu’il doit être possible de dériver de mise à l’échelle et de traduire les facteurs de mise à l’échelle d’un chemin d’accès à la taille de la zone de dessin. Le [ **mise à l’échelle ANISOTROPIQUE** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) page illustre cela avec une étoile 11-référencée. Un *ANISOTROPIQUE* mise à l’échelle signifie qu’il n’est pas égale dans le sens horizontal et vertical, ce qui signifie que l’étoile ne conserve ses proportions d’origine. Voici le code approprié le `PaintSurface` gestionnaire :

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

Le `pathBounds` rectangle est obtenu au début de ce code et ensuite utilisé avec la largeur et la hauteur de la zone de dessin dans le `Scale` appeler. Qu’appel par lui-même sera mise à l’échelle les coordonnées du chemin d’accès lorsqu’il est restitué par le `DrawPath` appel mais l’étoile sera centré dans le coin supérieur droit de la zone de dessin. Il doit être décalés vers le bas et vers la gauche. Il s’agit du travail de le `Translate` appeler. Ces deux propriétés de `pathBounds` étant environ – 100, les facteurs de translation sont environ 100. Étant donné que le `Translate` appel est après le `Scale` appeler, ces valeurs sont efficacement à l’échelle les facteurs d’échelle, pour pouvoir les déplacer vers le centre de la zone de dessin au centre de l’étoile :

[![](scale-images/anisotropicscaling-small.png "Capture d’écran triple de la page de mise à l’échelle ANISOTROPIQUE")](scale-images/anisotropicscaling-large.png#lightbox "Triple capture d’écran de la page de mise à l’échelle ANISOTROPIQUE")

Une autre façon de penser `Scale` aux appels et `Translate` consiste à déterminer l’effet en séquence inverse: L' `Translate` appel décale le chemin d’accès pour qu’il devienne complètement visible mais orienté dans le coin supérieur gauche du canevas. Le `Scale` méthode rend ensuite cette étoile supérieure par rapport à l’angle supérieur gauche.

En fait, il apparaît que l’étoile est légèrement supérieure à la zone de dessin. Le problème est la largeur du trait. Le `Bounds` propriété du `SKPath` indique les dimensions des coordonnées encodées dans le chemin d’accès, et qui est utilisé par le programme à l’échelle. Lorsque le chemin d’accès est affiché avec une largeur de trait particulier, le chemin d’accès affiché est supérieure à la zone de dessin.

Pour résoudre ce problème, vous devez compenser pour ce faire. Une approche facile dans ce programme consiste à ajouter le droit d’instruction suivantes avant du `Scale` appeler :

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Cela augmente la `pathBounds` rectangle en 1,5 unités sur les quatre côtés. Il s’agit d’une solution raisonnable uniquement lorsque la jointure de trait est arrondie. Un joint d’angle peut être plus longue et difficile à calculer.

Vous pouvez également utiliser une technique similaire avec du texte, comme le **texte ANISOTROPIQUE** montre de page. Voici la partie pertinente de la `PaintSurface` gestionnaire à partir de la [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe :

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

Il est logique similaire, et le texte s’étend à la taille de la page selon le rectangle de limites de texte retourné à partir de `MeasureText` (qui est un peu plus le texte) :

[![](scale-images/anisotropictext-small.png "Capture d’écran triple de la page de Test ANISOTROPIQUE")](scale-images/anisotropictext-large.png#lightbox "Triple capture d’écran de la page de Test ANISOTROPIQUE")

Si vous avez besoin de conserver les proportions des objets graphiques, vous souhaitez utiliser la mise à l’échelle isotropes. Le **mise à l’échelle isotropes** page montre cela pour l’étoile 11-référencée. Conceptuellement, les étapes pour l’affichage d’un objet graphique dans le centre de la page avec la mise à l’échelle isotropes sont :

- Traduire le centre de l’objet graphique à l’angle supérieur gauche.
- Mettre à l’échelle de l’objet basé sur la valeur minimale des dimensions de page horizontaux et verticaux divisée par les dimensions du graphique d’objet.
- Traduire le centre de l’objet mis à l’échelle vers le centre de la page.

Le [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) effectue ces étapes dans l’ordre inverse avant d’afficher l’étoile :

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

[![](scale-images/isotropicscaling-small.png "Capture d’écran triple de la page de mise à l’échelle isotropes")](scale-images/isotropicscaling-large.png#lightbox "Triple capture d’écran de la page de mise à l’échelle isotropes")


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
