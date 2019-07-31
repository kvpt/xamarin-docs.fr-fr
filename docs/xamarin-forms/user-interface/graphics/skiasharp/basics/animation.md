---
title: Animation de base dans SkiaSharp
description: Cet article explique comment animer vos graphiques de SkiaSharp dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: e9af5a78900fce714b214dfd3b3e0e83e17bbce2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648733"
---
# <a name="basic-animation-in-skiasharp"></a>Animation de base dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment animer vos graphiques SkiaSharp_

Vous pouvez animer des graphiques de SkiaSharp dans Xamarin.Forms en provoquant la `PaintSurface` méthode à appeler périodiquement, chaque fois que les graphiques de dessin un peu différemment. Voici une animation présentée plus loin dans cet article avec des cercles concentriques apparemment développez à partir du centre :

![](animation-images/animationexample.png "Plusieurs cercles concentriques apparemment développant à partir du centre")

Le **Ellipse impulsions** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programme anime deux axes d’une ellipse afin qu’il apparaisse à être impulsions, et vous pouvez même contrôler les taux de cette pulsation. Le [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) fichier instancie un Xamarin.Forms `Slider` et un `Label` pour afficher la valeur actuelle du curseur. Il s’agit d’une méthode courante pour intégrer un `SKCanvasView` avec d’autres vues Xamarin.Forms :

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

Le fichier code-behind instancie un `Stopwatch` objet pour servir une horloge de haute précision. Le `OnAppearing` remplacer des jeux le `pageIsActive` champ `true` et appelle une méthode nommée `AnimationLoop`. Le `OnDisappearing` remplacement définit que `pageIsActive` champ `false`:

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

Le `AnimationLoop` méthode démarre le `Stopwatch` et ensuite les boucles lors de la `pageIsActive` est `true`. Il s’agit essentiellement une « boucle infinie » alors que la page est active, mais elle n’entraîne pas le programme se bloque, car la boucle se termine par un appel à `Task.Delay` avec la `await` opérateur, qui vous permet d’autres parties de la fonction de programme. L’argument `Task.Delay` entraînant sa fin après le 1/30 secondes. Cela définit la fréquence d’images de l’animation.

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

Le `while` boucle commence en obtenant un temps de cycle de la `Slider`. Il s’agit d’une durée en secondes, par exemple, 5. La deuxième instruction calcule une valeur de `t` pour *temps*. Pour un `cycleTime` de 5, `t` augmente à partir de 0 à 1, toutes les 5 secondes. L’argument à la `Math.Sin` (fonction) dans la deuxième instruction comprise 0 à 2π toutes les 5 secondes. Le `Math.Sin` fonction retourne une valeur entre 0 et 1 arrière à 0, puis à &ndash;1 et 0 toutes les 5 secondes, mais avec des valeurs qui changent plus lentement lorsque la valeur est proche de 1 ou -1. La valeur 1 est ajoutée afin que les valeurs sont toujours positives, et puis elle est divisée par 2, donc les valeurs comprises entre ½ et 1 par ½ à 0 par ½, mais plus lent lorsque la valeur est d’environ 1 et 0. Ces informations sont stockées dans le `scale` champ et le `SKCanvasView` est invalidée.

Le `PaintSurface` méthode utilise cette `scale` valeur pour calculer les deux axes de l’ellipse :

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

La méthode calcule un rayon maximum en fonction de la taille de la zone d’affichage et un rayon minimal, selon le rayon maximal. Le `scale` valeur est comprise entre 0 et 1 et animée à 0, donc la méthode qui utilise pour calculer un `xRadius` et `yRadius` qui varie entre `minRadius` et `maxRadius`. Ces valeurs sont utilisées pour dessiner et remplir une ellipse :

[![](animation-images/pulsatingellipse-small.png "Capture d’écran triple de la page de l’Ellipse clignotante")](animation-images/pulsatingellipse-large.png#lightbox "Triple capture d’écran de la page de l’Ellipse clignotante")

Notez que le `SKPaint` objet est créé dans un `using` bloc. Comme de nombreuses classes SkiaSharp `SKPaint` dérive `SKObject`, qui dérive à son `SKNativeObject`, qui implémente le [ `IDisposable` ](xref:System.IDisposable) interface. `SKPaint` remplace le `Dispose` méthode pour libérer les ressources non managées.

 Placement de `SKPaint` dans un `using` bloc garantit que `Dispose` est appelée à la fin du bloc pour libérer ces ressources non managées. Cela se produit quand même lorsque la mémoire utilisée par le `SKPaint` objet est libéré par le garbage collector .NET, mais dans le code de l’animation, il est préférable d’être proactif pour libérer de la mémoire de façon plus rationnelle.

 Une meilleure solution dans ce cas particulier serait de créer deux `SKPaint` les objets qu’une seule fois et de les enregistrer en tant que champs.

C’est ce que le **développant des cercles** est de l’animation. Le [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe commence par définir plusieurs champs, y compris un `SKPaint` objet :

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

Ce programme utilise une approche différente pour les animations basées sur le Xamarin.Forms `Device.StartTimer` (méthode). Le `t` champ est animé de 0 à 1 chaque `cycleTime` millisecondes :

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

Le `PaintSurface` gestionnaire dessine cinq cercles concentriques à rayon animée. Si le `baseRadius` variable est calculée en tant que 100, puis en tant que `t` est animé de 0 à 1, les rayons de l’augmentation de cinq cercles à partir de 0 à 100, 100 et 200, 200 à 300, 300 et 400 et 400 et 500. Pour la plupart des cercles le `strokeWidth` est cercle 50, mais pour le premier, le `strokeWidth` s’anime de 0 à 50. Pour la plupart des cercles, la couleur est bleue, mais pour le cercle dernier, la couleur est animée du bleu transparent. Notez le quatrième argument de la `SKColor` constructeur qui spécifie l’opacité :

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

Le résultat est que l’image ressemble identiques lorsque `t` est égal à 0, comme lorsque `t` est égal à 1, et les cercles semblent continuer à développer pour toujours :

[![](animation-images/expandingcircles-small.png "Triple capture d’écran de la page de cercles développant")](animation-images/expandingcircles-large.png#lightbox "Triple capture d’écran de la page développant des cercles")


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
