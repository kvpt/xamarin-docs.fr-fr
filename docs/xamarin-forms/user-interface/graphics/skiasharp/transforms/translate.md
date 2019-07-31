---
title: La transformation de traduction
description: Cet article examine comment utiliser la transformation de traduction pour décaler des graphiques de SkiaSharp dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: b4fae33f7de8c6022b2298c462ea510ec8ed623f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657204"
---
# <a name="the-translate-transform"></a>La transformation de traduction

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser la transformation de traduction pour décaler des graphiques de SkiaSharp_

Le type le plus simple de transformation dans SkiaSharp est la *traduire* ou *traduction* transformer. Cette transformation déplace des objets graphiques dans le sens horizontal et vertical. Dans un sens, la traduction est la transformation plus inutile, car vous pouvez généralement obtenir le même effet en modifiant simplement les coordonnées que vous utilisez dans la fonction de dessin. Lors du rendu d’un chemin d’accès, toutefois, toutes les coordonnées sont encapsulées dans le chemin d’accès, par conséquent, il est beaucoup plus facile d’appliquer la conversion pour décaler le chemin d’accès complet.

Traduction est également utile pour l’animation et pour les effets de texte simple :

![](translate-images/translateexample.png "Ombre du texte, de gravure et de relief avec la traduction")

Le [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) méthode dans `SKCanvas` a deux paramètres qui provoquent des objets graphiques dessinés par la suite être décalés horizontalement et verticalement :

```csharp
public void Translate (Single dx, Single dy)
```

Ces arguments peuvent être négatifs. Une seconde [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) méthode combine les valeurs de translation de deux dans un seul `SKPoint` valeur :

```csharp
public void Translate (SKPoint point)
```

Le **accumulées traduire** page de la [ **SkiaSharpForms** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple de programme illustre que plusieurs appels de la `Translate` méthode sont cumulatifs. Le [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) classe affiche 20 versions du même rectangle, chacun d’eux offset du rectangle précédent juste assez afin qu’ils étirement le long de la diagonale. Voici le `PaintSurface` Gestionnaire d’événements :

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

[![](translate-images/accumulatedtranslate-small.png "Triple capture d’écran de la page accumulées traduire")](translate-images/accumulatedtranslate-large.png#lightbox "Triple capture d’écran de la page accumulées traduire")

Si les facteurs de translation accumulées sont `dx` et `dy`, et le point que vous spécifiez dans une fonction de dessin est (`x`, `y`), puis l’objet graphique est restitué au point (`x'`, `y'`), où :

x' = x + dx

y' = y + dy

Ceux-ci sont connus comme les *transformer formules* pour la traduction. Les valeurs par défaut de `dx` et `dy` pour un nouveau `SKCanvas` ont pour valeur 0.

Il est courant d’utiliser la transformation de traduction pour les effets d’ombre et des techniques similaires, comme le **traduire les effets de texte** montre de page. Voici la partie pertinente de la `PaintSurface` gestionnaire dans le [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) classe :

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

Dans chacun des trois exemples, `Translate` est appelée pour afficher le texte pour la décaler à partir de l’emplacement indiqué par le `x` et `y` variables. Ensuite, le texte s’affiche à nouveau dans une autre couleur sans aucune incidence de traduction :

[![](translate-images/translatetexteffects-small.png "Capture d’écran triple de la page de traduire les effets de texte")](translate-images/translatetexteffects-large.png#lightbox "Triple capture d’écran de la page de traduire les effets de texte")

Chacun des trois exemples montre une autre manière d’annuler la `Translate` appeler :

Le premier exemple appelle simplement `Translate` là encore, mais avec des valeurs négatives. Étant donné que le `Translate` appels sont cumulatifs, cette séquence d’appels restaure simplement la traduction total pour les valeurs par défaut de zéro.

Le deuxième exemple appelle [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix). Par conséquent, toutes les transformations revenir à leur état par défaut.

Le troisième exemple enregistre l’état de la `SKCanvas` objet avec un appel à [ `Save` ](xref:SkiaSharp.SKCanvas.Save) puis restaure l’état avec un appel à [ `Restore` ](xref:SkiaSharp.SKCanvas.Restore). Il s’agit de la façon la plus polyvalente pour manipuler des transformations pour une série d’opérations de dessin. Ces `Save` fonctions `Restore` et appellent une fonction comme une pile: Vous pouvez appeler `Save` plusieurs fois, puis appeler `Restore` en séquence inverse pour revenir aux États précédents. Le `Save` méthode retourne un entier, et vous pouvez passer cet entier à [ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*) efficacement appeler `Restore` plusieurs fois. Le [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount) propriété retourne le nombre d’états actuellement enregistrés sur la pile.

Vous pouvez également utiliser le [ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore) classe permettant de restaurer l’état de la zone de dessin. Le constructeur de cette classe est destiné à être appelée un `using` instruction ; la zone de dessin état est restauré automatiquement à la fin de la `using` bloc. 

Toutefois, vous n’avez pas à vous soucier des transformations conservant à partir d’un seul appel de la `PaintSurface` gestionnaire à la suivante. Chaque nouvel appel à `PaintSurface` offre une nouvelle `SKCanvas` objet avec des transformations par défaut.

Une autre utilisation courante de la `Translate` transformation est pour le rendu d’un objet visuel qui a été initialement créé à l’aide de coordonnées qui sont pratiques pour le dessin. Par exemple, vous souhaiterez peut-être spécifier les coordonnées d’une horloge analogique avec un centre au point (0, 0). Vous pouvez ensuite utiliser des transformations pour afficher l’horloge où vous le souhaitez. Cette technique est illustrée dans le [**Hendecagram tableau**] page. Le [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) classe commence par créer un `SKPath` objet pour une étoile 11-référencée. Le `HendecagramPath` objet est défini comme public, statique et en lecture seule afin qu’il est accessible à partir d’autres programmes de démonstration. Il est créé dans un constructeur statique :

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

Si le centre de l’étoile est le point (0, 0), tous les points de l’étoile sont sur un cercle entourant ce point. Chaque point est une combinaison de valeurs sinus et le cosinus d’un angle qui augmente de 5/11ths de 360 degrés. (Il est également possible de créer une étoile pointes 11 en augmentant l’angle par 2/11, / 11ths 3 ou 4/11 du cercle.) Le rayon du cercle est défini comme 100.

Ce chemin d’accès est rendu sans les transformations, le centre est placé dans l’angle supérieur gauche de la `SKCanvas`et uniquement un trimestre sera visible. Le `PaintSurface` Gestionnaire de `HendecagramPage` utilise à la place `Translate` pour placer la zone de dessin avec plusieurs copies de l’étoile, chacun d’eux colorée au hasard :

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

[![](translate-images/hendecagramarray-small.png "Capture d’écran triple de la page de tableau de Hendecagram")](translate-images/hendecagramarray-large.png#lightbox "Triple capture d’écran de la page de tableau de Hendecagram")

Animations impliquent souvent des transformations. Le **Hendecagram Animation** page déplace l’étoile 11-référencée dans un cercle. Le [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) classe commence par certains champs et les remplacements de la `OnAppearing` et `OnDisappearing` méthodes pour démarrer et arrêter un minuteur de Xamarin.Forms :

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

Le `angle` champ est animé de degrés de 0 à 360 degrés toutes les 5 secondes. Le `PaintSurface` Gestionnaire utilise la `angle` propriété de deux manières : pour spécifier la teinte de la couleur dans le `SKColor.FromHsl` (méthode) et en tant qu’argument à la `Math.Sin` et `Math.Cos` méthodes pour régir l’emplacement de l’étoile :

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

Le `PaintSurface` appels du gestionnaire le `Translate` méthode à deux reprises, tout d’abord pour convertir vers le centre de la zone de dessin, puis à traduire en la circonférence d’un cercle centrée autour de (0, 0). Le rayon du cercle est défini pour être aussi grand que possible tout en conservant l’étoile dans les limites de la page :

[![](translate-images/hendecagramanimation-small.png "Capture d’écran triple de la page de l’Animation Hendecagram")](translate-images/hendecagramanimation-large.png#lightbox "Triple capture d’écran de la page de l’Animation Hendecagram")

Notez que l’étoile conserve l’orientation même comme il tourne autour du centre de la page. Il ne pivote pas du tout. Est un outil idéal pour une transformation de rotation.


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
