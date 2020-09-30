---
title: Animation de base dans SkiaSharp
description: Cet article explique comment animer vos SkiaSharp Graphics dans les Xamarin.Forms applications et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3052220b914b09f18490846bbd2558bbf07e4d3a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562260"
---
# <a name="basic-animation-in-skiasharp"></a>Animation de base dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment animer vos graphiques SkiaSharp_

Vous pouvez animer des graphiques SkiaSharp dans Xamarin.Forms en provoquant l' `PaintSurface` appel périodique de la méthode, à chaque fois que les graphiques dessineront un peu différemment. Voici une animation présentée plus loin dans cet article avec des cercles concentriques qui s’étendent apparemment à partir du Centre :

![Plusieurs cercles concentriques ont apparemment été étendus à partir du centre](animation-images/animationexample.png)

La page d' **ellipse Pulsating** dans le programme [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) anime les deux axes d’une ellipse afin qu’elle semble être Pulsating, et vous pouvez même contrôler le taux de cette pulsation. Le fichier [**PulsatingEllipsePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) instancie un Xamarin.Forms `Slider` et un `Label` pour afficher la valeur actuelle du curseur. Il s’agit d’une méthode courante pour intégrer un `SKCanvasView` avec d’autres Xamarin.Forms vues :

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

Le fichier code-behind instancie un `Stopwatch` objet qui sert de point de vue haute précision. La `OnAppearing` substitution affecte au `pageIsActive` champ la valeur `true` et appelle une méthode nommée `AnimationLoop` . Le `OnDisappearing` remplacement définit ce `pageIsActive` champ sur `false` :

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

La `AnimationLoop` méthode démarre `Stopwatch` , puis effectue une boucle alors que `pageIsActive` est `true` . Il s’agit essentiellement d’une « boucle infinie » tandis que la page est active, mais elle n’entraîne pas le blocage du programme, car la boucle se termine par un appel à `Task.Delay` avec l' `await` opérateur, qui permet à d’autres parties du programme de fonctionner. L’argument pour `Task.Delay` provoque son achèvement après 1/30 secondes. Cela définit la fréquence d’images de l’animation.

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

La `while` boucle commence par obtenir une durée de cycle à partir du `Slider` . Il s’agit d’une durée en secondes, par exemple 5. La deuxième instruction calcule la valeur de `t` pour l' *heure*. Pour un `cycleTime` de 5, `t` augmente de 0 à 1 toutes les 5 secondes. L’argument de la `Math.Sin` fonction dans la deuxième instruction est compris entre 0 et 2π toutes les 5 secondes. La `Math.Sin` fonction retourne une valeur comprise entre 0 et 1 à 0, puis &ndash; 1 et 0 toutes les 5 secondes, mais avec des valeurs qui changent plus lentement lorsque la valeur est proche de 1 ou-1. La valeur 1 est ajoutée afin que les valeurs soient toujours positives, puis divisées par 2. par conséquent, les valeurs vont de 1/2 à 1 à 1/2 à 0 à 1/2, mais plus lentement lorsque la valeur est égale à 1 et 0. Elle est stockée dans le `scale` champ et `SKCanvasView` est invalidée.

La `PaintSurface` méthode utilise cette `scale` valeur pour calculer les deux axes de l’ellipse :

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

La méthode calcule un rayon maximal en fonction de la taille de la zone d’affichage et d’un rayon minimal basé sur le rayon maximal. La `scale` valeur est animée entre 0 et 1 et revient à 0. par conséquent, la méthode utilise celle-ci pour calculer un `xRadius` et les `yRadius` plages entre `minRadius` et `maxRadius` . Ces valeurs sont utilisées pour dessiner et remplir une ellipse :

[![Capture d’écran triple de la page d’ellipse Pulsating](animation-images/pulsatingellipse-small.png)](animation-images/pulsatingellipse-large.png#lightbox "Capture d’écran triple de la page d’ellipse Pulsating")

Notez que l' `SKPaint` objet est créé dans un `using` bloc. Comme de nombreuses classes SkiaSharp `SKPaint` dérivent de, `SKObject` qui dérive de `SKNativeObject` , qui implémente l' [`IDisposable`](xref:System.IDisposable) interface. `SKPaint` Substitue la `Dispose` méthode pour libérer des ressources non managées.

 `SKPaint`Le fait de placer dans un `using` Bloc garantit que `Dispose` est appelé à la fin du bloc pour libérer ces ressources non managées. Cela se produit quand la mémoire utilisée par l' `SKPaint` objet est libérée par le garbage collector .net, mais dans le code d’animation, il est préférable d’être proactif pour libérer de la mémoire de manière plus ordonnée.

 Dans ce cas particulier, une meilleure solution consisterait à créer deux `SKPaint` objets une fois et à les enregistrer sous forme de champs.

C’est ce que fait l’animation des **cercles de développement** . La [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe commence par la définition de plusieurs champs, y compris un `SKPaint` objet :

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

Ce programme utilise une approche différente de l’animation basée sur la Xamarin.Forms `Device.StartTimer` méthode. Le `t` champ est animé de 0 à 1 toutes les `cycleTime` millisecondes :

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

Le `PaintSurface` Gestionnaire dessine cinq cercles concentriques avec des rayons animés. Si la `baseRadius` variable est calculée comme 100, tandis que `t` est animée de 0 à 1, les rayons des cinq cercles augmentent de 0 à 100, 100 à 200, 200 à 300, 300 à 400 et 400 à 500. Pour la plupart des cercles `strokeWidth` , le est 50, mais pour le premier cercle, le s' `strokeWidth` anime de 0 à 50. Pour la plupart des cercles, la couleur est bleue, mais pour le dernier cercle, la couleur est animée du bleu au transparent. Notez le quatrième argument du `SKColor` constructeur qui spécifie l’opacité :

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

Le résultat est que l’image a le même résultat lorsque est `t` égal à 0 comme lorsque `t` est égal à 1 et que les cercles semblent continuer à s’étendre indéfiniment :

[![Capture d’écran triple de la page agrandissement des cercles](animation-images/expandingcircles-small.png)](animation-images/expandingcircles-large.png#lightbox "Capture d’écran triple de la page agrandissement des cercles")

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)