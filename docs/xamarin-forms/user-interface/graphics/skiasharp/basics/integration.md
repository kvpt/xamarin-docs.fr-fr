---
title: Intégrer àXamarin.Forms
description: Cet article explique comment créer des graphiques SkiaSharp qui répondent à des éléments tactiles et Xamarin.Forms illustre cette procédure avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 60f53fb00786b183f36f7cfefa6af9062bb682db
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938162"
---
# <a name="integrating-with-xamarinforms"></a>Intégrer àXamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Créer des graphiques SkiaSharp qui répondent à des Xamarin.Forms éléments tactiles_

Les graphiques SkiaSharp peuvent s’intégrer au reste de de Xamarin.Forms plusieurs façons. Vous pouvez combiner un canevas SkiaSharp et des Xamarin.Forms éléments sur la même page, et même positionner Xamarin.Forms des éléments sur une zone de dessin SkiaSharp :

![Sélection d’une couleur avec des curseurs](integration-images/integrationexample.png)

Une autre approche de la création de graphiques SkiaSharp interactifs dans Xamarin.Forms est d’utiliser Touch.
La deuxième page du programme [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) est autorisée à **appuyer sur Activer/désactiver le remplissage**. Il dessine un cercle simple de deux manières &mdash; sans remplissage et avec un remplissage &mdash; activé par un TAP. La [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe montre comment vous pouvez modifier les graphiques SkiaSharp en réponse à une entrée de l’utilisateur.

Pour cette page, la `SKCanvasView` classe est instanciée dans le fichier [TapToggleFill. Xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) , qui définit également un Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) sur la vue :

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

Notez la `skia` déclaration d’espace de noms XML.

Le `Tapped` Gestionnaire de l' `TapGestureRecognizer` objet bascule simplement la valeur d’un champ booléen et appelle la [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) méthode de `SKCanvasView` :

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

L’appel de `InvalidateSurface` génère effectivement un appel au `PaintSurface` Gestionnaire, qui utilise le `showFill` champ pour remplir ou ne pas remplir le cercle :

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

La `StrokeWidth` propriété a été définie sur 50 pour accentuer la différence. Vous pouvez également voir la largeur de ligne entière en dessinant l’intérieur en premier, puis le contour. Par défaut, les figures graphiques qui sont dessinées ultérieurement dans le `PaintSurface` Gestionnaire d’événements masquent celles qui sont dessinées plus tôt dans le gestionnaire.

La page **Explorer la couleur** montre comment vous pouvez également intégrer des graphiques SkiaSharp à d’autres Xamarin.Forms éléments, et illustre également la différence entre deux méthodes alternatives pour définir des couleurs dans SkiaSharp. La [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) méthode statique crée une `SKColor` valeur en fonction du modèle teinte-saturation-Lighting :

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

La [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) méthode statique crée une `SKColor` valeur basée sur le modèle de valeur de teinte-saturation-valeur :

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

Dans les deux cas, l’argument est compris `h` entre 0 et 360. Les `s` `l` arguments, et sont `v` compris entre 0 et 100. L' `a` argument (alpha ou Opacity) est compris entre 0 et 255.

Le fichier [**ColorExplorePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) crée deux `SKCanvasView` objets `StackLayout` côte à côte avec des `Slider` vues et `Label` qui permettent à l’utilisateur de sélectionner des valeurs de couleur TSL et HSV :

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

Les deux `SKCanvasView` éléments se trouvent dans une cellule unique `Grid` avec un `Label` assis en haut pour afficher la valeur de couleur RVB résultante.

Le fichier code-behind [**ColorExplorePage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) est relativement simple. Le `ValueChanged` Gestionnaire partagé pour les trois `Slider` éléments invalide simplement les deux `SKCanvasView` éléments. Les `PaintSurface` gestionnaires effacent la zone de dessin avec la couleur indiquée par les `Slider` éléments, et définissent également le `Label` bord sur les `SKCanvasView` éléments :

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

Dans les modèles de couleurs TSL et HSV, la valeur de teinte est comprise entre 0 et 360 et indique la teinte dominante de la couleur. Il s’agit des couleurs traditionnelles de l’arc-en-ciel : rouge, orange, jaune, vert, bleu, Indigo, violet et arrière dans un cercle rouge.

Dans le modèle TSL, une valeur 0 pour la clarté est toujours noire, et une valeur 100 est toujours blanche. Lorsque la valeur de saturation est 0, les valeurs de luminosité comprises entre 0 et 100 sont des nuances de gris. L’amélioration de la saturation augmente la couleur. Les couleurs pures (qui sont des valeurs RVB avec un composant égal à 255, une autre valeur égale à 0 et la troisième comprise entre 0 et 255) se produisent lorsque la saturation est 100 et la luminosité est de 50.

Dans le modèle HSV, les couleurs pures sont générées lorsque la saturation et la valeur sont 100. Quand la valeur est 0, quels que soient les autres paramètres, la couleur est noire. Les ombres grises se produisent lorsque la saturation est égale à 0 et les plages de valeurs comprises entre 0 et 100.

Mais la meilleure façon d’avoir une idée pour les deux modèles consiste à les expérimenter vous-même :

[![Capture d’écran triple de la page explorer les couleurs](integration-images/colorexplore-large.png)](integration-images/colorexplore-small.png#lightbox "Capture d’écran triple de la page explorer les couleurs")

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
