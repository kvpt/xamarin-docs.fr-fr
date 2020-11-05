---
title: Points et tirets dans SkiaSharp
description: Cet article explore comment maîtriser les subtilités de dessin de lignes en pointillés et en pointillés dans SkiaSharp, et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 56a652f6f8ff2c4e9780d72117241d79f71210b5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367385"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Points et tirets dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Maîtrisez les subtilités de dessin de lignes en pointillés et en pointillés dans SkiaSharp_

SkiaSharp vous permet de dessiner des lignes qui ne sont pas solides, mais qui sont composées de points et de tirets :

![Ligne en pointillés](dots-images/dottedlinesample.png)

Pour ce faire, il s’agit d’un *effet de chemin d’accès* , qui est une instance de la [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) classe que vous affectez à la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propriété de `SKPaint` . Vous pouvez créer un effet de tracé (ou combiner des effets de tracés) à l’aide de l’une des méthodes de création statique définies par `SKPathEffect` . ( `SKPathEffect` est l’un des six effets pris en charge par SkiaSharp ; les autres sont décrits dans la section [**SkiaSharp Effect**](../effects/index.md).)

Pour dessiner des lignes en pointillés ou en pointillés, vous utilisez la [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) méthode statique. Il existe deux arguments : le premier est un tableau de `float` valeurs qui indiquent les longueurs des points et des tirets et la longueur des espaces entre eux. Ce tableau doit avoir un nombre pair d’éléments, et il doit y avoir au moins deux éléments. (Il peut y avoir zéro élément dans le tableau, mais il en résulte une ligne pleine.) S’il y a deux éléments, le premier est la longueur d’un point ou d’un tiret, et le deuxième est la longueur de l’intervalle avant le point ou le tiret suivant. S’il y a plus de deux éléments, ils sont dans cet ordre : la longueur du tiret, la longueur de l’intervalle, la longueur du tiret, la longueur de l’espace, etc.

En règle générale, vous souhaiterez faire en sorte que les longueurs de tiret et d’intervalle forment un multiple de la largeur du trait. Par exemple, si la largeur du trait est de 10 pixels, le tableau {10, 10} dessinera une ligne en pointillés où les points et les intervalles auront la même longueur que l’épaisseur du trait.

Toutefois, le `StrokeCap` paramètre de l' `SKPaint` objet affecte également ces points et tirets. Comme vous le verrez bientôt, cela a un impact sur les éléments de ce tableau.

Les lignes en pointillés et en pointillés sont illustrées sur la page **points et tirets** . Le fichier [**DotsAndDashesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml) instancie deux `Picker` vues, une pour vous permettre de sélectionner un embout de trait et le second pour sélectionner un tableau de tirets :

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

 Les trois premiers éléments de la `dashArrayPicker` partent du principe que la largeur du trait est de 10 pixels. Le tableau {10, 10} correspond à une ligne en pointillés, {30, 10} correspond à une ligne en pointillés, tandis que {10, 10, 30, 10} correspond à une ligne de points et de tirets. (Les trois autres seront abordés prochainement.)

Le [`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs) fichier code-behind contient le `PaintSurface` Gestionnaire d’événements et quelques routines d’assistance pour accéder aux `Picker` vues :

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

Dans les captures d’écran suivantes, l’écran iOS à l’extrême gauche affiche une ligne en pointillés :

[![Capture d’écran triple de la page des points et des tirets](dots-images/dotsanddashes-small.png)](dots-images/dotsanddashes-large.png#lightbox "Capture d’écran triple de la page des points et des tirets")

Toutefois, l’écran Android est également supposé afficher une ligne en pointillés à l’aide du tableau {10, 10}, mais la ligne est pleine. Que s’est-il passé ? Le problème est que la valeur de l’écran Android est également définie sur `Square` . Cela étend tous les tirets de la moitié de la largeur du trait, provoquant ainsi le remplissage des espaces vides.

Pour contourner ce problème lors de l’utilisation d’une extrémité de trait de `Square` ou `Round` , vous devez réduire les longueurs de tiret dans le tableau en fonction de la longueur du trait (ce qui entraîne parfois une longueur de tiret de 0) et augmenter les longueurs d’intervalle par la longueur du trait. Voici comment les trois tableaux de tirets finaux dans le `Picker` fichier XAML ont été calculés :

- {10, 10} devient {0, 20} pour une ligne en pointillés
- {30, 10} devient {20, 20} pour une ligne en pointillés
- {10, 10, 30, 10} devient {0, 20, 20, 20} pour une ligne en pointillés et en pointillés

L’écran UWP affiche une ligne en pointillés et en pointillés pour une extrémité de trait de `Round` . L' `Round` extrémité du trait donne souvent la meilleure apparence des points et des tirets en traits épais.

Jusqu’à présent, aucune mention n’a été faite du deuxième paramètre de la `SKPathEffect.CreateDash` méthode. Ce paramètre est nommé `phase` et il fait référence à un décalage dans le modèle de point et de tiret pour le début de la ligne. Par exemple, si le tableau de tirets est {10, 10} et que `phase` est 10, la ligne commence par un intervalle plutôt que par un point.

Une application intéressante du `phase` paramètre se trouve dans une animation. La page en **spirale animée** est semblable à la page en **spirale ARCHIMEDEAN** , à ceci près que la [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) classe anime le `phase` paramètre à l’aide de la Xamarin.Forms `Device.Timer` méthode :

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

Bien entendu, vous devrez exécuter le programme pour voir l’animation :

[![Capture d’écran triple de la page en spirale animée](dots-images/animatedspiral-small.png)](dots-images/animatedspiral-large.png#lightbox "Capture d’écran triple de la page en spirale animée")

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)