---
title: SkiaSharp le bruit et la composition
description: Générez des nuanceurs de bruit Perl et associez-les à d’autres nuanceurs.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1a9a8b8dc31369b5774935a2e8fca5cf17faa24b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371246"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp le bruit et la composition

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les graphiques vectoriels simples ont tendance à paraître innaturels. Les lignes droites, les courbes lissées et les couleurs unies ne sont pas similaires aux imperfections des objets réels. En travaillant sur les graphiques générés par ordinateur pour le film _Tron_ 1982, le chercheur en informatique Ken Perl a commencé à développer des algorithmes qui utilisaient des processus aléatoires pour obtenir des textures plus réalistes. Dans 1997, Ken Perl a remporté un prix d’Académie pour la réalisation technique. Son travail est connu sous le nom de « Perl » et est pris en charge dans SkiaSharp. Voici un exemple :

![Exemple de bruit perl](noise-images/NoiseSample.png "Exemple de bruit perl")

Comme vous pouvez le voir, chaque pixel n’est pas une valeur de couleur aléatoire. La continuité du pixel au pixel génère des formes aléatoires.

La prise en charge du bruit perl dans skia est basée sur une spécification W3C pour CSS et SVG. La section 8,20 du [**module d’effets de filtre niveau 1**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) comprend les algorithmes de bruit perl sous-jacents en code C.

## <a name="exploring-perlin-noise"></a>Exploration du bruit perl

La [`SKShader`](xref:SkiaSharp.SKShader) classe définit deux méthodes statiques différentes pour générer le bruit perl :  [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) et [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*) . Les paramètres sont identiques :

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Les deux méthodes existent également dans les versions surchargées avec un `SKPointI` paramètre supplémentaire. La section Présentation de la [**mosaïque de Perl**](#tiling-perlin-noise) décrit ces surcharges.

Les deux `baseFrequency` arguments sont des valeurs positives définies dans la documentation SkiaSharp comme allant de 0 à 1, mais elles peuvent également être définies sur des valeurs plus élevées. Plus la valeur est élevée, plus la modification de l’image aléatoire est importante dans les directions horizontale et verticale.

La `numOctaves` valeur est un entier égal ou supérieur à 1. Il se réfère à un facteur d’itération dans les algorithmes. Chaque octave supplémentaire apporte un effet qui correspond à la moitié de l’octave précédente, de sorte que l’effet diminue avec des valeurs d’octave supérieures.

Le `seed` paramètre est le point de départ pour le générateur de nombres aléatoires. Bien qu’elle soit spécifiée comme valeur à virgule flottante, la fraction est tronquée avant d’être utilisée, et 0 est le même que 1.

La page de **bruit perl** dans l’exemple [ **SkiaSharpFormsDemos** )](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) vous permet de faire des essais avec différentes valeurs des `baseFrequency` `numOctaves` arguments et. Voici le fichier XAML :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Il utilise deux `Slider` vues pour les deux `baseFrequency` arguments. Pour étendre la plage des valeurs inférieures, les curseurs sont logarithmiques. Le fichier code-behind calcule les arguments des `SKShader` méthodes à partir des puissances des `Slider` valeurs. Les `Label` vues affichent les valeurs calculées :

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Une `Slider` valeur de 1 correspond à 0,001, une `Slider` valeur OS 2 correspond à 0,01, une valeur `Slider` de 3 correspond à 0,1 et une `Slider` valeur de 4 correspond à 1.

Voici le fichier code-behind qui comprend ce code :

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Voici le programme s’exécutant sur des appareils iOS, Android et plateforme Windows universelle (UWP). Le bruit fractal est affiché dans la moitié supérieure du canevas. Le bruit de turbulence se trouve dans la moitié inférieure :

[![Bruit perl](noise-images/PerlinNoise.png "Bruit perl")](noise-images/PerlinNoise-Large.png#lightbox)

Les mêmes arguments produisent toujours le même modèle qui commence à l’angle supérieur gauche. Cette cohérence est évidente lorsque vous ajustez la largeur et la hauteur de la fenêtre UWP. Lorsque Windows 10 redessine l’écran, le motif dans la moitié supérieure du canevas reste le même.

Le modèle de bruit intègre différents degrés de transparence. La transparence devient évidente si vous définissez une couleur dans l' `canvas.Clear()` appel. Cette couleur est visible dans le modèle. Vous verrez également cet effet dans la section [**combinaison de plusieurs nuanceurs**](#combining-multiple-shaders).

Ces modèles de bruit Perl sont rarement utilisés par eux-mêmes. Souvent, ils sont soumis à des modes de fusion et des filtres de couleur décrits dans des articles ultérieurs.

## <a name="tiling-perlin-noise"></a>Mosaïque du bruit perl

Les deux méthodes statiques `SKShader` pour créer un bruit perl existent également dans les versions de surcharge. Les [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) surcharges et ont un `SKPointI` paramètre supplémentaire :

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

La [`SKPointI`](xref:SkiaSharp.SKPointI) structure est la version entière de la [`SKPoint`](xref:SkiaSharp.SKPoint) structure familière. `SKPointI` définit `X` les `Y` Propriétés et de type `int` plutôt que `float` .

Ces méthodes créent un modèle répétitif de la taille spécifiée. Dans chaque vignette, le bord droit est le même que le bord gauche, et le bord supérieur est le même que le bord inférieur. Cette caractéristique est illustrée dans la page de **bruit perl en mosaïque** . Le fichier XAML est similaire à l’exemple précédent, mais il n’a qu’une `Stepper` vue permettant de modifier l' `seed` argument :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Le fichier code-behind définit une constante pour la taille de la vignette. Le `PaintSurface` Gestionnaire crée une image bitmap de cette taille et un `SKCanvas` pour dessiner dans cette bitmap. La `SKShader.CreatePerlinNoiseTurbulence` méthode crée un nuanceur avec cette taille de mosaïque. Ce nuanceur est dessiné sur l’image bitmap :

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

Une fois la bitmap créée, un autre `SKPaint` objet est utilisé pour créer un modèle de bitmap en mosaïque en appelant `SKShader.CreateBitmap` . Notez les deux arguments `SKShaderTileMode.Repeat` suivants :

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Ce nuanceur est utilisé pour couvrir le canevas. Enfin, un autre `SKPaint` objet est utilisé pour rayer un rectangle qui indique la taille de la bitmap d’origine.

Seul le `seed` paramètre peut être sélectionné à partir de l’interface utilisateur. Si le même `seed` modèle est utilisé sur chaque plateforme, ils affichent le même modèle. `seed`Des valeurs différentes produisent des modèles différents :

[![Bruit perl en mosaïque](noise-images/TiledPerlinNoise.png "Bruit perl en mosaïque")](noise-images/TiledPerlinNoise-Large.png#lightbox)

Le motif carré de 200 pixels dans le coin supérieur gauche se déforment de façon transparente dans les autres vignettes.

## <a name="combining-multiple-shaders"></a>Combinaison de plusieurs nuanceurs

La `SKShader` classe comprend une [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) méthode qui crée un nuanceur avec une couleur unie spécifiée. Ce nuanceur n’est pas très utile en soi, car vous pouvez simplement définir cette couleur sur la `Color` propriété de l' `SKPaint` objet et définir la `Shader` propriété sur la valeur null.

Cette `CreateColor` méthode devient utile dans une autre méthode qui `SKShader` définit. Cette méthode est [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)) , qui combine deux nuanceurs. Voici la syntaxe :

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

Le `srcShader` (nuanceur source) est effectivement dessiné par-dessus le `dstShader` (nuanceur de destination). Si le nuanceur source est une couleur unie ou un dégradé sans transparence, le nuanceur de destination est entièrement masqué.

Un nuanceur de bruit perl contient une transparence. Si ce nuanceur est la source, le nuanceur de destination s’affiche à travers les zones transparentes.

La page de **bruit perl composée** contient un fichier XAML quasiment identique à la première page de **bruit perl** . Le fichier code-behind est également similaire. Toutefois, la page de bruit d’origine de **perl** définit la `Shader` propriété de `SKPaint` sur le nuanceur retourné par les `CreatePerlinNoiseFractalNoise` méthodes et statiques `CreatePerlinNoiseTurbulence` . Cette page de **bruit perl composée** appelle `CreateCompose` un nuanceur combiné. La destination est un nuanceur bleu fixe créé à l’aide de `CreateColor` . La source est un nuanceur de bruit perl :

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Le nuanceur bruit fractal se trouve en haut ; le nuanceur de turbulences se trouve en bas :

[![Bruit perl composé](noise-images/ComposedPerlinNoise.png "Bruit perl composé")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Notez la quantité de bleu que ces nuanceurs sont celles affichées par la page de **bruit perl** . La différence illustre la quantité de transparence dans les nuanceurs de bruit.

Il y a également une surcharge de la [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) méthode :

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

Le dernier paramètre est un membre de l' `SKBlendMode` énumération, une énumération avec 29 membres, abordée dans la série d’articles suivante sur la [**composition de SkiaSharp et les modes de fusion**](../blend-modes/index.md).

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)