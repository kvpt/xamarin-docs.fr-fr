---
title: Points et tirets dans SkiaSharp
description: Cet article explique en détail comment maîtriser les complexités de dessin des lignes en pointillés et dans SkiaSharp et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 229f60cbb96058454a1c634e53a7bb00ec725bcf
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291575"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Points et tirets dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Maîtrisez les subtilités de dessin de lignes en pointillés et en pointillés dans SkiaSharp_

SkiaSharp vous permet de dessiner des lignes qui ne sont pas correctes, mais au lieu de cela sont composés de points et de tirets :

![](dots-images/dottedlinesample.png "Dotted line")

Pour ce faire, utilisez un *effet de chemin d’accès*, qui est une instance de la classe [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) que vous affectez à la propriété [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) de `SKPaint`. Vous pouvez créer un effet de tracé (ou combiner des effets de tracés) à l’aide de l’une des méthodes de création statiques définies par `SKPathEffect`. (`SKPathEffect` est l’un des six effets pris en charge par SkiaSharp ; les autres sont décrits dans la section [**SkiaSharp Effect**](../effects/index.md).)

Pour dessiner des lignes en pointillés ou des tirets, utilisez la méthode statique [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) . Il existe deux arguments : le premier est un tableau de valeurs `float` qui indiquent les longueurs des points et des tirets et la longueur des espaces entre eux. Ce tableau doit avoir un nombre pair d’éléments, et il doit y avoir au moins deux éléments. (Il peut y avoir zéro élément dans le tableau, mais il en résulte une ligne pleine.) S’il y a deux éléments, le premier est la longueur d’un point ou d’un tiret, et le deuxième est la longueur de l’intervalle avant le point ou le tiret suivant. S’il existe plus de deux éléments, ils se trouvent dans cet ordre : tiret de longueur, longueur de l’intervalle, longueur du tiret, longueur de l’intervalle et ainsi de suite.

En règle générale, vous souhaitez rendre les longueurs de dash et gap un multiple de la largeur du trait. Si la largeur du trait est 10 pixels, par exemple, puis le tableau {10, 10} Dessine une ligne en pointillés où les points et les espaces sont la même longueur que l’épaisseur du trait.

Toutefois, le paramètre `StrokeCap` de l’objet `SKPaint` affecte également ces points et tirets. Comme vous le verrez bientôt, qui a un impact sur les éléments de ce tableau.

Les lignes en pointillés et en pointillés sont illustrées sur la page **points et tirets** . Le fichier [**DotsAndDashesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml) instancie deux vues de `Picker`, une pour vous permettre de sélectionner un embout de trait et le second pour sélectionner un tableau de tirets :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 Les trois premiers éléments de la `dashArrayPicker` supposent que la largeur du trait est de 10 pixels. Le {10, 10} tableau est pour une ligne en pointillés, {30, 10} est pour une ligne en pointillés et {10, 10, 30, 10} est pour une ligne de point-tiret. (Les trois autres seront abordées sous peu.)

Le fichier code-behind [`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs) contient le gestionnaire d’événements `PaintSurface` et quelques routines d’assistance pour accéder aux vues de `Picker` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

Dans les captures d’écran suivante, l’écran iOS à l’extrême gauche montre une ligne en pointillés :

[![](dots-images/dotsanddashes-small.png "Triple screenshot of the Dots and Dashes page")](dots-images/dotsanddashes-large.png#lightbox "Triple screenshot of the Dots and Dashes page")

Toutefois, l’écran Android est également supposé pour faire une ligne en pointillés à l’aide du tableau {10, 10}, mais au lieu de cela, la ligne est pleine. Que s’est-il passé ? Le problème est que l’écran Android a également un paramètre d’épaisseur de trait `Square`. Cela permet d’étendre tous les tirets par le trait demi-chasse, provoquant la remplir les écarts.

Pour contourner ce problème lors de l’utilisation d’un embout de trait d' `Square` ou de `Round`, vous devez réduire les longueurs de tiret dans le tableau en fonction de la longueur du trait (parfois, une longueur de tiret de 0) et augmenter les longueurs d’intervalle par la longueur du trait. C’est ainsi que sont calculées les trois tableaux de tirets finaux dans le `Picker` du fichier XAML :

- {10, 10} est {0, 20} pour une ligne en pointillés
- {30, 10} devient {20, 20} pour une ligne en pointillés
- {10, 10, 30, 10} est {0, 20, 20, 20} pour une ligne en pointillés et

L’écran UWP affiche une ligne en pointillés et en pointillés pour une limite de `Round`. La limite de `Round` trait affiche souvent la meilleure apparence des points et des tirets en traits épais.

Jusqu’à présent, aucune mention n’a été faite du deuxième paramètre de la méthode `SKPathEffect.CreateDash`. Ce paramètre est nommé `phase` et fait référence à un décalage dans le modèle de point et de tiret pour le début de la ligne. Par exemple, si le tableau de tirets est {10, 10} et que la `phase` est 10, la ligne commence par un intervalle et non par un point.

Une application intéressante du paramètre `phase` se trouve dans une animation. La page en **spirale animée** est semblable à la page en **spirale ARCHIMEDEAN** , à la différence que la classe [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) anime le paramètre `phase` à l’aide de la méthode de `Device.Timer` Xamarin. Forms :

```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

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
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

Bien sûr, vous devrez exécuter le programme pour voir l’animation :

[![](dots-images/animatedspiral-small.png "Triple screenshot of the Animated Spiral page")](dots-images/animatedspiral-large.png#lightbox "Triple screenshot of the Animated Spiral page")

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
