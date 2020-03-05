---
title: La transformation de traduction
description: Cet article examine comment utiliser la transformation de traduction pour décaler des graphiques de SkiaSharp dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f1efd7610b32e6a3903d34fc2f8b5a6e20c9da8a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292141"
---
# <a name="the-translate-transform"></a>La transformation de traduction

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser la transformation de traduction pour déplacer des graphiques SkiaSharp_

Le type de *transformation le plus* simple dans SkiaSharp est la transformation de translation ou de *traduction* . Cette transformation déplace des objets graphiques dans le sens horizontal et vertical. Dans un sens, la traduction est la transformation plus inutile, car vous pouvez généralement obtenir le même effet en modifiant simplement les coordonnées que vous utilisez dans la fonction de dessin. Lors du rendu d’un chemin d’accès, toutefois, toutes les coordonnées sont encapsulées dans le chemin d’accès, par conséquent, il est beaucoup plus facile d’appliquer la conversion pour décaler le chemin d’accès complet.

Traduction est également utile pour l’animation et pour les effets de texte simple :

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

La méthode [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) dans `SKCanvas` a deux paramètres qui entraînent le décalage horizontal et vertical des objets graphiques dessinés par la suite :

```csharp
public void Translate (Single dx, Single dy)
```

Ces arguments peuvent être négatifs. Une deuxième [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) méthode combine les deux valeurs de traduction dans une valeur de `SKPoint` unique :

```csharp
public void Translate (SKPoint point)
```

La page de **traduction accumulée** de l’exemple de programme [**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) montre que plusieurs appels de la méthode `Translate` sont cumulatifs. La classe [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) affiche 20 versions du même rectangle, chaque décalage par rapport au rectangle précédent suffit pour qu’elles s’étendent le long de la diagonale. Voici le `PaintSurface` gestionnaire d’événements :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

Les rectangles successives segmentée bas de la page :

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

Si les facteurs de traduction accumulés sont `dx` et `dy`, et que le point que vous spécifiez dans une fonction de dessin est (`x`, `y`), l’objet graphique est rendu au point (`x'`, `y'`), où :

x' = x + dx

y' = y + dy

Il s’agit des *formules de transformation* pour la traduction. Les valeurs par défaut de `dx` et `dy` pour une nouvelle `SKCanvas` sont 0.

Il est courant d’utiliser la transformation de traduction pour les effets d’ombre et les techniques similaires, comme le montre la page **traduire les effets de texte** . Voici la partie pertinente du gestionnaire de `PaintSurface` dans la classe [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) :

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

Dans chacun des trois exemples, `Translate` est appelé pour afficher le texte afin de l’offset de l’emplacement donné par les variables `x` et `y`. Ensuite, le texte s’affiche à nouveau dans une autre couleur sans aucune incidence de traduction :

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

Chacun des trois exemples illustre une façon différente de nier l’appel de `Translate` :

Le premier exemple appelle simplement `Translate`, mais avec des valeurs négatives. Étant donné que les appels `Translate` sont cumulatifs, cette séquence d’appels rétablit simplement la traduction totale sur les valeurs par défaut de zéro.

Le deuxième exemple appelle [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix). Par conséquent, toutes les transformations revenir à leur état par défaut.

Le troisième exemple enregistre l’état de l’objet `SKCanvas` avec un appel à [`Save`](xref:SkiaSharp.SKCanvas.Save) puis restaure l’État à l’aide d’un appel à [`Restore`](xref:SkiaSharp.SKCanvas.Restore). Il s’agit de la façon la plus polyvalente pour manipuler des transformations pour une série d’opérations de dessin. Ces `Save` et `Restore` appellent une fonction comme une pile : vous pouvez appeler plusieurs fois `Save`, puis appeler `Restore` dans l’ordre inverse pour revenir aux États précédents. La méthode `Save` retourne un entier, et vous pouvez passer cet entier à [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) pour appeler efficacement `Restore` plusieurs fois. La propriété [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) retourne le nombre d’États actuellement enregistrés sur la pile.

Vous pouvez également utiliser la classe [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) pour restaurer l’état de la zone de dessin. Le constructeur de cette classe est destiné à être appelé dans une instruction `using` ; l’état de la zone de dessin est restauré automatiquement à la fin du bloc `using`.

Toutefois, vous n’avez pas à vous soucier des transformations effectuant un appel du gestionnaire de `PaintSurface` à la suivante. Chaque nouvel appel à `PaintSurface` remet un nouvel objet `SKCanvas` avec les transformations par défaut.

Une autre utilisation courante de la transformation de `Translate` est le rendu d’un objet visuel qui a été créé à l’origine à l’aide de coordonnées qui sont pratiques pour le dessin. Par exemple, vous souhaiterez peut-être spécifier les coordonnées d’une horloge analogique avec un centre au point (0, 0). Vous pouvez ensuite utiliser des transformations pour afficher l’horloge où vous le souhaitez. Cette technique est illustrée dans la page [**Hendecagram Array**]. La classe [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs) commence par créer un objet `SKPath` pour une étoile à 11 branches. L’objet `HendecagramPath` est défini comme public, statique et en lecture seule afin d’être accessible à partir d’autres programmes de démonstration. Il est créé dans un constructeur statique :

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

Si le centre de l’étoile est le point (0, 0), tous les points de l’étoile sont sur un cercle entourant ce point. Chaque point est une combinaison de valeurs sinus et le cosinus d’un angle qui augmente de 5/11ths de 360 degrés. (Il est également possible de créer une étoile à 11 branches en accroissant l’angle de 2/11, 3/11 ou 4/11 du cercle.) Le rayon de ce cercle est défini sur 100.

Si ce chemin d’accès est rendu sans transformations, le centre est positionné dans le coin supérieur gauche de la `SKCanvas`et seul un quart de celui-ci est visible. Le gestionnaire de `PaintSurface` de `HendecagramPage` utilise à la place `Translate` pour juxtaposer la zone de dessin avec plusieurs copies de l’étoile, chacune d’elles étant colorée de façon aléatoire :

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

Voici le résultat :

[![](translate-images/hendecagramarray-small.png "Triple screenshot of the Hendecagram Array page")](translate-images/hendecagramarray-large.png#lightbox "Triple screenshot of the Hendecagram Array page")

Animations impliquent souvent des transformations. La page d' **animation Hendecagram** déplace l’étoile à 11 branches autour d’un cercle. La classe [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) commence par certains champs et remplace les méthodes `OnAppearing` et `OnDisappearing` pour démarrer et arrêter un minuteur Xamarin. Forms :

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

Le champ `angle` est animé de 0 degré à 360 degrés toutes les 5 secondes. Le gestionnaire de `PaintSurface` utilise la propriété `angle` de deux manières : pour spécifier la teinte de la couleur dans la méthode `SKColor.FromHsl`, et en tant qu’argument de la `Math.Sin` et `Math.Cos` méthodes pour régir l’emplacement de l’étoile :

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

Le gestionnaire de `PaintSurface` appelle la méthode `Translate` deux fois, d’abord pour se déplacer au centre du canevas, puis vers la circonférence d’un cercle centré autour de (0,0). Le rayon du cercle est défini pour être aussi grand que possible tout en conservant l’étoile dans les limites de la page :

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

Notez que l’étoile conserve l’orientation même comme il tourne autour du centre de la page. Il ne pivote pas du tout. Est un outil idéal pour une transformation de rotation.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
