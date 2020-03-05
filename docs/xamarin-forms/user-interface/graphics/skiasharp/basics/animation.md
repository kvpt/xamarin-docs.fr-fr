---
title: Animation de base dans SkiaSharp
description: Cet article explique comment animer vos graphiques de SkiaSharp dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 80de16a0cf9b601ac3795085b638b9d62812f4d9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292074"
---
# <a name="basic-animation-in-skiasharp"></a>Animation de base dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment animer vos graphiques SkiaSharp_

Vous pouvez animer des graphiques SkiaSharp dans Xamarin. Forms en provoquant l’appel périodique de la méthode `PaintSurface`, à chaque fois que les graphiques dessineront un peu différemment. Voici une animation présentée plus loin dans cet article avec des cercles concentriques apparemment développez à partir du centre :

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

La page d' **ellipse Pulsating** dans le programme [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) anime les deux axes d’une ellipse afin qu’elle semble être Pulsating, et vous pouvez même contrôler le taux de cette pulsation. Le fichier [**PulsatingEllipsePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) instancie un `Slider` Xamarin. Forms et un `Label` pour afficher la valeur actuelle du curseur. Il s’agit d’une méthode courante pour intégrer un `SKCanvasView` à d’autres vues Xamarin. Forms :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Le fichier code-behind instancie un objet `Stopwatch` pour servir d’horloge haute précision. La `OnAppearing` remplacement définit le champ `pageIsActive` sur `true` et appelle une méthode nommée `AnimationLoop`. Le `OnDisappearing` remplacement définit ce champ `pageIsActive` sur `false`:

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

La méthode `AnimationLoop` démarre le `Stopwatch` puis effectue une boucle lorsque `pageIsActive` est `true`. Il s’agit essentiellement d’une « boucle infinie », tandis que la page est active, mais elle n’entraîne pas le blocage du programme, car la boucle se termine par un appel à `Task.Delay` avec l’opérateur `await`, qui permet à d’autres parties de la fonction du programme. L’argument à `Task.Delay` provoque son achèvement après 1/30 secondes. Cela définit la fréquence d’images de l’animation.

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

La boucle `while` commence par obtenir une durée de cycle à partir du `Slider`. Il s’agit d’une durée en secondes, par exemple, 5. La deuxième instruction calcule une valeur de `t` pour l' *heure*. Pour une `cycleTime` de 5, `t` augmente de 0 à 1 toutes les 5 secondes. L’argument de la fonction `Math.Sin` dans la deuxième instruction est compris entre 0 et 2π toutes les 5 secondes. La fonction `Math.Sin` retourne une valeur comprise entre 0 et 1 renvoyée à 0, puis &ndash;1 et 0 toutes les 5 secondes, mais avec des valeurs qui changent plus lentement lorsque la valeur est proche de 1 ou-1. La valeur 1 est ajoutée afin que les valeurs sont toujours positives, et puis elle est divisée par 2, donc les valeurs comprises entre ½ et 1 par ½ à 0 par ½, mais plus lent lorsque la valeur est d’environ 1 et 0. Elle est stockée dans le champ `scale` et la `SKCanvasView` est invalidée.

La méthode `PaintSurface` utilise cette `scale` valeur pour calculer les deux axes de l’ellipse :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

La méthode calcule un rayon maximum en fonction de la taille de la zone d’affichage et un rayon minimal, selon le rayon maximal. La valeur de `scale` est animée entre 0 et 1, et de nouveau à 0. par conséquent, la méthode l’utilise pour calculer une `xRadius` et `yRadius` qui s’étend entre `minRadius` et `maxRadius`. Ces valeurs sont utilisées pour dessiner et remplir une ellipse :

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

Notez que l’objet `SKPaint` est créé dans un bloc `using`. Comme de nombreuses classes SkiaSharp `SKPaint` dérive de `SKObject`, qui dérive de `SKNativeObject`, qui implémente l’interface [`IDisposable`](xref:System.IDisposable) . `SKPaint` substitue la méthode `Dispose` pour libérer des ressources non managées.

 Le fait de placer des `SKPaint` dans un bloc `using` garantit que `Dispose` est appelé à la fin du bloc pour libérer ces ressources non managées. Cela se produit quand la mémoire utilisée par l’objet `SKPaint` est libérée par le garbage collector .NET, mais dans le code d’animation, il est préférable d’être proactif pour libérer de la mémoire de manière plus ordonnée.

 Dans ce cas particulier, une meilleure solution consisterait à créer deux objets `SKPaint` une fois et à les enregistrer sous forme de champs.

C’est ce que fait l’animation des **cercles de développement** . La classe [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) commence par la définition de plusieurs champs, notamment un objet `SKPaint` :

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

Ce programme utilise une approche différente de l’animation basée sur la méthode Xamarin. Forms `Device.StartTimer`. Le champ `t` est animé de 0 à 1 chaque `cycleTime` millisecondes :

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
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

Le gestionnaire de `PaintSurface` dessine cinq cercles concentriques avec des rayons animés. Si la variable `baseRadius` est calculée comme 100, alors que `t` est animée de 0 à 1, les rayons des cinq cercles augmentent de 0 à 100, 100 à 200, 200 à 300, 300 à 400 et 400 à 500. Pour la plupart des cercles, le `strokeWidth` est 50, mais pour le premier cercle, le `strokeWidth` s’anime de 0 à 50. Pour la plupart des cercles, la couleur est bleue, mais pour le cercle dernier, la couleur est animée du bleu transparent. Notez le quatrième argument du constructeur `SKColor` qui spécifie l’opacité :

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

Le résultat est que l’image a la même valeur lorsque `t` est égal à 0 que lorsque `t` est égal à 1 et que les cercles semblent continuer à s’étendre indéfiniment :

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
