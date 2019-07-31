---
title: Intégration avec Xamarin.Forms
description: Cet article explique comment créer des graphiques de SkiaSharp qui répondent à toucher et éléments de Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: 586cf0eaa7b0f38af61c9f7d619b30a39023a19e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652960"
---
# <a name="integrating-with-xamarinforms"></a>Intégration avec Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Créer des graphiques de SkiaSharp qui répondent aux tactiles et des éléments de Xamarin.Forms_

Graphique de SkiaSharp peut intégrer avec le reste de Xamarin.Forms de plusieurs façons. Vous pouvez combiner une zone de dessin SkiaSharp et éléments de Xamarin.Forms sur la même page et même de positionner des éléments de Xamarin.Forms sur une zone de dessin SkiaSharp :

![](integration-images/integrationexample.png "Sélection d’une couleur avec les curseurs")

Une autre approche pour la création de graphiques interactifs de SkiaSharp dans Xamarin.Forms est via tactile.
La deuxième page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programme est autorisée à utiliser **appuyez sur le remplissage de bascule**. Il dessine un cercle simple de deux façons &mdash; sans remplissage et avec un remplissage &mdash; activé ou désactivé par un clic. Le [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe montre comment vous pouvez modifier les graphiques de SkiaSharp en réponse à une entrée utilisateur.

Pour cette page, le `SKCanvasView` classe est instanciée dans le [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) fichier, qui définit également un Xamarin.Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) sur la vue :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

Notez que le `skia` déclaration d’espace de noms XML.

Le `Tapped` gestionnaire pour le `TapGestureRecognizer` objet bascule simplement la valeur d’un champ booléen et appelle le [ `InvalidateSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) méthode de `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

L’appel à `InvalidateSurface` efficacement génère un appel à la `PaintSurface` gestionnaire, qui utilise le `showFill` champ à remplir ou pas de remplissage du cercle :

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
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

Le `StrokeWidth` propriété a été définie à 50 pour accentuer la différence. Vous pouvez également voir la largeur de la ligne entière en dessinant l’intérieur tout d’abord, puis sur le contour. Par défaut, les graphiques chiffres qui sont dessinées ultérieurement dans le `PaintSurface` Gestionnaire d’événements masquer celles du plus haut dans le gestionnaire.

Le **couleur Explorer** page illustre comment vous pouvez également intégrer des graphiques de SkiaSharp avec d’autres éléments Xamarin.Forms et également la différence entre les deux autres méthodes pour définir les couleurs dans SkiaSharp. La méthode statique [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) méthode crée un `SKColor` valeur basée sur le modèle de Teinte-Saturation-Luminosité :

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

La méthode statique [ `SKColor.FromHsv` ](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) méthode crée un `SKColor` valeur basée sur le modèle de valeur de Teinte-Saturation similaire :

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

Dans les deux cas, le `h` plages argument comprise entre 0 et 360. Le `s`, `l`, et `v` arguments comprises entre 0 et 100. Le `a` (alpha ou l’opacité) plages argument comprise entre 0 et 255.

Le [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) fichier crée deux `SKCanvasView` des objets dans un `StackLayout` côte à côte avec `Slider` et `Label` vues permettant aux utilisateurs de sélectionner TSL et TSL valeurs de couleur :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Les deux `SKCanvasView` sont des éléments dans une seule cellule `Grid` avec un `Label` appuie dessus pour afficher la valeur de couleur RGB résultante.

Le [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) fichier code-behind est relativement simple. Le partagé `ValueChanged` gestionnaire pour les trois `Slider` éléments invalide simplement les deux `SKCanvasView` éléments. Le `PaintSurface` gestionnaires d’effacer la zone de dessin avec la couleur indiquée par le `Slider` éléments et également définir le `Label` assis en haut de la `SKCanvasView` éléments :

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

Dans les modèles de couleurs TSL et de TSL, la valeur de teinte comprise entre 0 et 360 et indique la teinte dominante de la couleur. Voici les couleurs traditionnelles de l’arc-en-ciel : rouge, orange, jaune, vert, bleu, indigo, violet et arrière dans un cercle rouge.

Dans le modèle TSL, une valeur 0 pour la luminosité est toujours noir, et une valeur de 100 est toujours blanc. Lorsque la valeur de Saturation est 0, les valeurs de luminosité comprise entre 0 et 100 sont des nuances de gris. Augmentez la Saturation ajoute plus de couleur. Couleurs pures (qui sont des valeurs RVB avec un élément égal à 255, un autre égal à 0 et le troisième comprise entre 0 et 255) se produisent lors de la Saturation est 100 et la luminosité est 50.

Dans le modèle TSL, couleurs pures entraînent lors de la Saturation et la valeur sont 100. Lorsque la valeur est 0, indépendamment des autres paramètres, la couleur est noir. Nuances de gris se produisent lorsque la Saturation est 0 et la valeur est comprise entre 0 et 100.

Mais la meilleure façon de faire une idée des deux modèles consiste à les tester vous-même :

[![](integration-images/colorexplore-large.png "Triple capture d’écran de la page couleur Explorer")](integration-images/colorexplore-small.png#lightbox "Triple capture d’écran de la page à Explorer de couleur")


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
