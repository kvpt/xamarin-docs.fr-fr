---
title: Points et tirets dans SkiaSharp
description: Cet article explique en détail comment maîtriser les complexités de dessin des lignes en pointillés et dans SkiaSharp et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 2d02e79ff51468572250d1a7ce7c6d3da103c03a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770526"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Points et tirets dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Maîtrisez les complexités de dessiner des lignes en pointillés et dans SkiaSharp_

SkiaSharp vous permet de dessiner des lignes qui ne sont pas correctes, mais au lieu de cela sont composés de points et de tirets :

![](dots-images/dottedlinesample.png "Ligne en pointillés")

Cela avec une *effet*, qui est une instance de la [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) classe que vous définissez pour le [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propriété de `SKPaint`. Vous pouvez créer un chemin d’accès (ou les combiner chemin effets) en utilisant l’une des méthodes de création statiques définies par `SKPathEffect`. (`SKPathEffect` est un des six effets pris en charge par SkiaSharp ; les autres sont décrits dans la section [ **SkiaSharp effet**](../effects/index.md).)

Pour dessiner des lignes en pointillés ou en pointillés, utilisez le [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) méthode statique. Il existe deux arguments : C’est tout d’abord un `float` tableau de valeurs qui indiquent les longueurs des points et des tirets et la longueur des espaces entre eux. Ce tableau doit avoir un nombre pair d’éléments, et il doit y avoir au moins deux éléments. (Il peut être zéro élément dans le tableau mais qui se traduit par un trait plein.) S’il existe deux éléments, la première est la longueur d’un point ou un tiret et le second est la longueur de l’espacement avant le prochain point ou tiret. S’il existe plus de deux éléments, ils se trouvent dans cet ordre : tiret de longueur, longueur de l’intervalle, longueur du tiret, longueur de l’intervalle et ainsi de suite.

En règle générale, vous souhaitez rendre les longueurs de dash et gap un multiple de la largeur du trait. Si la largeur du trait est 10 pixels, par exemple, puis le tableau {10, 10} Dessine une ligne en pointillés où les points et les espaces sont la même longueur que l’épaisseur du trait.

Toutefois, le `StrokeCap` définition de la `SKPaint` objet affecte également ces points et tirets. Comme vous le verrez bientôt, qui a un impact sur les éléments de ce tableau.

Séparée par des points et de lignes en pointillés sont décrites sur le **points et tirets** page. Le [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) fichier instancie deux `Picker` vues, une pour ce qui vous permet de sélectionner une extrémité de trait et le second pour sélectionner un tableau de tiret :

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

 Les trois premiers éléments dans le `dashArrayPicker` supposent que la largeur du trait est de 10 pixels. Le {10, 10} tableau est pour une ligne en pointillés, {30, 10} est pour une ligne en pointillés et {10, 10, 30, 10} est pour une ligne de point-tiret. (Les trois autres seront abordées sous peu.)

Le [ `DotsAndDashesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) fichier code-behind contient le `PaintSurface` Gestionnaire d’événements et deux routines d’assistance pour accéder à la `Picker` vues :

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

[![](dots-images/dotsanddashes-small.png "Capture d’écran triple de la page de points et de tirets")](dots-images/dotsanddashes-large.png#lightbox "Triple capture d’écran de la page de points et de tirets")

Toutefois, l’écran Android est également supposé pour faire une ligne en pointillés à l’aide du tableau {10, 10}, mais au lieu de cela, la ligne est pleine. Que s'est-il passé ? Le problème est que l’écran Android a également un paramètre d’embouts de trait de `Square`. Cela permet d’étendre tous les tirets par le trait demi-chasse, provoquant la remplir les écarts.

Pour contourner ce problème lors de l’utilisation d’une extrémité de trait de `Square` ou `Round`, vous devez les longueurs de tiret dans le tableau par la longueur du trait (parfois, ce qui entraîne une longueur de tiret de 0) et d’augmenter les longueurs d’écart par la longueur du trait. Voici comment les trois derniers tirets tableaux dans les `Picker` dans le fichier XAML ont été calculés :

- {10, 10} est {0, 20} pour une ligne en pointillés
- {30, 10} devient {20, 20} pour une ligne en pointillés
- {10, 10, 30, 10} est {0, 20, 20, 20} pour une ligne en pointillés et

Limite de l’écran affiche UWP qui séparée par des points et tirets de ligne pour un trait de `Round`. Le `Round` extrémité de trait permet souvent la meilleure apparence des points et des tirets dans les lignes épaisses.

Jusqu'à présent, aucune mention n’a été effectuée du deuxième paramètre à la `SKPathEffect.CreateDash` (méthode). Ce paramètre est nommé `phase` et il fait référence à un offset dans le modèle de point-tiret au début de la ligne. Par exemple, si le tableau dash est {10, 10} et le `phase` est 10, la ligne commence par un écart plutôt que d’un point.

Une application intéressante de la `phase` paramètre figure dans une animation. Le **spirale animée** page est similaire à la **Archimède spirale** page, à ceci près que le [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) classe anime le `phase` à l’aide du paramètre le Xamarin.Forms `Device.Timer` méthode :

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

[![](dots-images/animatedspiral-small.png "Triple capture d’écran de la page de spirale animée")](dots-images/animatedspiral-large.png#lightbox "Triple capture d’écran de la page de spirale animée")

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
