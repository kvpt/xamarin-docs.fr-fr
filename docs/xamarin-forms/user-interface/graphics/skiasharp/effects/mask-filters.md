---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 10192e93d2e20f9aa58ca95dd81c07f560193905
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136407"
---
# <a name="skiasharp-mask-filters"></a>Filtres de masque SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les filtres de masque sont des effets qui manipulent la géométrie et le canal alpha des objets graphiques. Pour utiliser un filtre de masque, affectez à la propriété de la valeur d' [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) `SKPaint` un objet de type [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) que vous avez créé en appelant l’une des `SKMaskFilter` méthodes statiques.

La meilleure façon de se familiariser avec les filtres de masque consiste à expérimenter ces méthodes statiques. Le filtre de masque le plus utile crée un flou :

![Exemple de flou](mask-filters-images/MaskFilterExample.png "Exemple de flou")

Il s’agit de la seule fonctionnalité de filtre de masque décrite dans cet article. L’article suivant sur les [**filtres d’images SkiaSharp**](image-filters.md) décrit également un effet de flou que vous pouvez préférer à celui-ci. 

La [`SKMaskFilter.CreateBlur`](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) méthode statique a la syntaxe suivante :

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Les surcharges permettent de spécifier des indicateurs pour l’algorithme utilisé pour créer le flou, et un rectangle pour éviter les flous dans les zones qui seront couvertes par d’autres objets graphiques.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle)est une énumération avec les membres suivants :

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Les effets de ces styles sont présentés dans les exemples ci-dessous. Le `sigma` paramètre spécifie l’étendue du flou. Dans les versions antérieures de skia, l’étendue du flou était indiquée par une valeur de rayon. Si une valeur RADIUS est préférable pour votre application, il existe une méthode statique [`SKMaskFilter.ConvertRadiusToSigma`](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) qui peut effectuer une conversion de l’une à l’autre. La méthode multiplie le rayon par 0,57735 et ajoute 0,5.

La page de l' **expérience masquer le masque** de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) vous permet d’expérimenter les styles de flou et les valeurs Sigma. Le fichier XAML instancie un `Picker` avec les quatre `SKBlurStyle` membres de l’énumération et un `Slider` pour la spécification de la valeur Sigma :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind utilise ces valeurs pour créer un `SKMaskFilter` objet et le définir sur la `MaskFilter` propriété d’un `SKPaint` objet. Cet `SKPaint` objet est utilisé pour dessiner une chaîne de texte et une image bitmap :

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Pink);

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Voici le programme en cours d’exécution sur iOS, Android et le plateforme Windows universelle (UWP) avec le `Normal` style de flou et les `sigma` niveaux plus élevés :

[![Expérience de masque flou-normal](mask-filters-images/MaskBlurExperiment-Normal.png "Expérience de masque flou-normal")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Vous remarquerez que seuls les bords de la bitmap sont affectés par le flou. La `SKMaskFilter` classe n’est pas l’effet correct à utiliser si vous souhaitez brouiller une image bitmap entière. Pour cela, vous souhaiterez utiliser la [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) classe, comme décrit dans l’article suivant sur les [**filtres d’image SkiaSharp**](image-filters.md).

Le texte est plus flou avec les valeurs d’incrément de l' `sigma` argument. Dans l’expérimentation de ce programme, vous remarquerez que pour une `sigma` valeur particulière, le flou est plus extrême sur le bureau Windows 10. Cette différence est due au fait que la densité de pixels est plus faible sur un moniteur d’ordinateur de bureau que sur les périphériques mobiles. par conséquent, la hauteur du texte en pixels est inférieure. La `sigma` valeur est proportionnelle à une étendue de flou en pixels. par conséquent, pour une `sigma` valeur donnée, l’effet est plus extrême sur les affichages de résolution inférieure. Dans une application de production, vous souhaiterez probablement calculer une `sigma` valeur proportionnelle à la taille du graphique. 

Essayez plusieurs valeurs avant de régler sur un niveau de flou qui ressemble le mieux à votre application. Par exemple, dans la page de l' **expérience masquer le masque** , essayez de définir `sigma` comme suit :

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

À présent `Slider` , le n’a aucun effet, mais le degré de flou est cohérent entre les plateformes :

[![Expérience de masque flou-cohérence](mask-filters-images/MaskBlurExperiment-Consistent.png "Expérience de masque flou-cohérence")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Toutes les captures d’écran jusqu’à présent ont montré un flou créé avec le membre de l' `SKBlurStyle.Normal` énumération. Les captures d’écran suivantes montrent les effets `Solid` des `Outer` styles de `Inner` flou, et :

[![Expérience de masque flou](mask-filters-images/MaskBlurExperiment.png "Expérience de masque flou")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

La capture d’écran iOS présente le `Solid` style : les caractères de texte sont toujours présents sous forme de traits noirs pleins et le flou est ajouté à l’extérieur de ces caractères de texte. 

La capture d’écran Android au milieu montre le `Outer` style : les traits de caractère eux-mêmes sont éliminés (comme c’est le cas de la bitmap) et le flou entoure l’espace vide où les caractères de texte sont apparus. 

La capture d’écran UWP sur la droite montre le `Inner` style. Le flou est limité à la zone occupée normalement par les caractères de texte.

L’article [**gradient linéaire SkiaSharp**](shaders/linear-gradient.md#transparency-and-gradients) décrit un programme de **gradient de réflexion** qui utilisait un dégradé linéaire et une transformation pour imiter la réflexion d’une chaîne de texte :

[![Dégradé de réflexion](shaders/linear-gradient-images/ReflectionGradient.png "Dégradé de réflexion")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

La page **réflexion floue** ajoute une instruction unique à ce code :

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

La nouvelle instruction ajoute un filtre de flou pour le texte réfléchi qui est basé sur la taille du texte :

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Ce filtre de flou rend la réflexion plus réaliste :

[![Réflexion floue](mask-filters-images/BlurryReflection.png "Réflexion floue")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
