---
title: Filtres de masque de SkiaSharp
description: Découvrez comment utiliser le filtre de masque pour créer un flou et autres effets alphabétiques.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 36a8b5c32261d4f508c82feea1e6127574db6a20
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198246"
---
# <a name="skiasharp-mask-filters"></a>Filtres de masque de SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Masquage des filtres sont des effets qui manipulent la géométrie et le canal alpha des objets graphiques. Pour utiliser un filtre de masque, définissez la [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) propriété de `SKPaint` à un objet de type [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) que vous avez créé en appelant une de le `SKMaskFilter` des méthodes statiques.

La meilleure façon de vous familiariser avec les filtres de masque est en expérimentant de ces méthodes statiques. Le filtre de masque plus utiles crée un effet de flou :

![Exemple de flou](mask-filters-images/MaskFilterExample.png "flou exemple")

C’est la seule fonctionnalité de filtre de masque décrite dans cet article. L’article suivant sur [ **filtres d’image SkiaSharp** ](image-filters.md) décrit également un effet de flou, vous préférerez peut-être à celui-ci. 

La méthode statique [ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) méthode présente la syntaxe suivante :

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Surcharges autorise la spécification des indicateurs pour l’algorithme utilisé pour créer l’effet de flou et un rectangle afin d’éviter le flou dans des domaines qui seront couverts par d’autres objets graphiques.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) est une énumération avec les membres suivants :

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Les effets de ces styles sont affichés dans les exemples ci-dessous. Le `sigma` paramètre spécifie l’étendue de l’effet de flou. Dans les versions antérieures de Skia, l’étendue de l’effet de flou était indiqué avec une valeur de rayon. Si une valeur de rayon est préférable pour votre application, il est une variable statique [ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) méthode qui peut être converti à partir d’un à l’autre. La méthode multiplie le rayon par 0.57735 et ajoute 0,5.

Le **Masque flou expérimenter** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple vous permet de faire des essais avec les styles de flou et les valeurs de sigma. Le fichier XAML instancie un `Picker` avec les quatre `SKBlurStyle` membres de l’énumération et une `Slider` pour spécifier la valeur sigma :

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

Le fichier code-behind utilise ces valeurs pour créer un `SKMaskFilter` objet et définissez-le sur le `MaskFilter` propriété d’un `SKPaint` objet. Cela `SKPaint` objet est utilisé pour dessiner une chaîne de texte et une image bitmap :

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

Voici le programme en cours d’exécution sur iOS, Android et la plateforme Windows universelle (UWP) avec le `Normal` style et en augmentant le flou `sigma` niveaux :

[![Masquer l’expérience flou - Normal](mask-filters-images/MaskBlurExperiment-Normal.png "Masque flou expérience - Normal")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Vous remarquerez que seuls les bords de l’image bitmap sont affectés par l’effet de flou. Le `SKMaskFilter` classe n’est pas l’effet correct à utiliser si vous souhaitez une image bitmap entière de flou. Pour ce faire, vous devrez utiliser le [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) classe, comme indiqué dans l’article suivant sur [ **filtres d’image SkiaSharp**](image-filters.md).

Le texte est plus floue avec des valeurs croissantes de la `sigma` argument. Tester avec ce programme, vous remarquerez que pour un particulier `sigma` valeur, le flou est plus extrême sur le bureau Windows 10. Cette différence se produit parce que la densité en pixels est plus faible sur un écran de bureau que sur les appareils mobiles, et par conséquent, la hauteur en pixels est inférieure. Le `sigma` valeur est proportionnelle à une mesure flou, en pixels, c’est le cas pour une donnée `sigma` valeur, l’effet est plus extrême sur les écrans basse résolution. Dans une application de production, vous vous souhaitez probablement calculer un `sigma` valeur est proportionnelle à la taille du graphique. 

Essayez plusieurs valeurs avant de choisir un niveau de flou qui correspond le mieux pour votre application. Par exemple, dans le **Masque flou expérience** page, essayez de définir `sigma` comme suit :

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Maintenant le `Slider` n’a aucun effet, mais le degré de flou est cohérent entre les plateformes :

[![Masquer l’expérience flou - cohérente](mask-filters-images/MaskBlurExperiment-Consistent.png "Masque flou expérience - cohérent")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Les captures d’écran jusqu'à présent ont montré flou créé avec le `SKBlurStyle.Normal` membre d’énumération. Les captures d’écran suivantes montrent les effets de la `Solid`, `Outer`, et `Inner` flou styles :

[![Masquer l’expérience de flou](mask-filters-images/MaskBlurExperiment.png "masquer l’expérience de flou")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

La capture d’écran iOS `Solid` présente le style: Les caractères de texte sont toujours présents sous forme de traits noirs pleins et le flou est ajouté à l’extérieur de ces caractères de texte. 

La capture d’écran Android au milieu montre `Outer` le style: Les traits de caractère eux-mêmes sont éliminés (comme c’est le cas de la bitmap) et le flou entoure l’espace vide où les caractères de texte sont apparus. 

La capture d’écran UWP de droite montre le `Inner` style. L’effet de flou est limitée à la zone normalement occupée par les caractères de texte.

Le [ **dégradé linéaire SkiaSharp** ](shaders/linear-gradient.md#transparency-and-gradients) article décrit un **réflexion dégradé** programme qui a utilisé un dégradé linéaire et une transformation pour imiter une réflexion d’une chaîne de texte :

[![Dégradé de réflexion](shaders/linear-gradient-images/ReflectionGradient.png "dégradé de réflexion")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Le **réflexion floues** page ajoute une instruction unique à ce code :

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

La nouvelle instruction ajoute un filtre de flou pour le texte réfléchi est basé sur la taille du texte :

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Ce filtre de flou provoque la réflexion pour paraître beaucoup plus réalistes :

[![Réflexion floue](mask-filters-images/BlurryReflection.png "réflexion floue")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
