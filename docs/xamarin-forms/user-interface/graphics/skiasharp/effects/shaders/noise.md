---
title: SkiaSharp bruit et la composition
description: Générer des nuanceurs de bruit de Perlin et combiner des autres nuanceurs.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: dea7f5e51a864922d56f7b65d19b21a889cbc650
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656159"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp bruit et la composition

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Graphismes vectoriels simple ont tendance à être non naturelles. Les lignes droites, des courbes lissées et des couleurs unies ne se présenter comme les imperfections des objets du monde réel. Lorsque vous travaillez sur les graphiques générés par l’ordinateur pour le film 1982 _Tron_, authentique scientifique Ken Perlin a commencé à développer des algorithmes qui permettant de donner ces images textures plus réalistes processus aléatoire. En 1997, Ken Perlin a gagné une récompense Academy pour une prime en technique. Son travail est venu à être connu comme un bruit de Perlin, et il est pris en charge dans SkiaSharp. Voici un exemple :

![Échantillon de bruit de Perlin](noise-images/NoiseSample.png "échantillon de bruit de Perlin")

Comme vous pouvez le voir, chaque pixel n’est pas une valeur de couleur aléatoire. La continuité des activités à partir de pixel au pixel entraîne des formes aléatoire.

La prise en charge de bruit de Perlin dans Skia est basé sur une spécification W3C pour CSS et SVG. 8\.20 de section de [ **1 de niveau Module filtre effets** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) inclut les algorithmes de bruit de Perlin sous-jacente dans le code C.

## <a name="exploring-perlin-noise"></a>Exploration de bruit de Perlin

Le [ `SKShader` ](xref:SkiaSharp.SKShader) classe définit deux méthodes statiques pour la génération de bruit de Perlin : [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) et [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*). Les paramètres sont identiques :

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Les deux méthodes existent également dans les versions surchargées avec un autre `SKPointI` paramètre. La section [ **bruit de Perlin de mosaïque** ](#tiling-perlin-noise) traite ces surcharges.

Les deux `baseFrequency` arguments sont des valeurs positives définis dans la documentation SkiaSharp comprises entre 0 et 1, mais elles peuvent être définies ainsi des valeurs plus élevées. La valeur est élevée, plus la modification de l’image aléatoire dans le sens horizontal et vertical.

Le `numOctaves` valeur est un entier de 1 ou supérieure. Il est lié à un facteur d’itération dans les algorithmes. Chaque octave supplémentaire contribue à un effet qui représente la moitié de l’octave précédente, afin de l’effet diminue avec la hausse octave.

Le `seed` paramètre est le point de départ pour le Générateur de nombres aléatoires. Bien qu’il est spécifié en tant que valeur à virgule flottante, la fraction est tronquée avant qu’il est utilisé, et 0 est identique à 1.

Le **bruit de Perlin** page dans le [ **SkiaSharpFormsDemos**)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple permet de vous faire des essais avec différentes valeurs de la `baseFrequency` et `numOctaves` arguments. Voici le fichier XAML :

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

Il utilise deux `Slider` vues pour les deux `baseFrequency` arguments. Pour développer la plage des valeurs inférieure, les curseurs sont logarithmiques. Le fichier code-behind calcule les arguments à la `SKShader`méthodes à partir de la puissance de la `Slider` valeurs. Le `Label` vues affichent les valeurs calculées :

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Un `Slider` la valeur 1 correspond à 0,001, un `Slider` valeur du système d’exploitation 2 correspond à 0,01, un `Slider` valeurs 3 correspond à 0,1 et un `Slider` valeur 4 correspond à 1.

Voici le fichier code-behind qui inclut ce code :

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

Voici le programme en cours d’exécution sur iOS, Android et Universal Windows Platform (UWP) des appareils. Le bruit fractal est indiqué dans la partie supérieure de la zone de dessin. Le bruit turbulences est dans la partie inférieure moitié :

[![Bruit de Perlin](noise-images/PerlinNoise.png "bruit de Perlin")](noise-images/PerlinNoise-Large.png#lightbox)

Les mêmes arguments produisent toujours le même modèle que celui qui commence dans l’angle supérieur gauche. Cette cohérence est évidente lorsque vous ajustez la largeur et la hauteur de la fenêtre UWP. Comme Windows 10 redessine l’écran, le modèle dans la partie supérieure du canevas reste la même.

Le motif de bruit intègre divers degrés de transparence. La transparence devient évidente si vous définissez une couleur dans le `canvas.Clear()` appeler. Cette couleur devient visible dans le modèle. Vous verrez également cet effet dans la section [ **combinant plusieurs nuanceurs**](#combining-multiple-shaders).

Ces modèles de bruit de Perlin sont rarement utilisées par eux-mêmes. Ils sont souvent soumis pour fusionner les modes et les filtres de couleur décrits dans les articles suivants.

## <a name="tiling-perlin-noise"></a>Bruit de Perlin de disposition en mosaïque

Deux statiques `SKShader` méthodes pour la création de bruit de Perlin existent également dans les versions de surcharge. Le [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) et [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) surcharges ont supplémentaires `SKPointI` paramètre :

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

Le [ `SKPointI` ](xref:SkiaSharp.SKPointI) structure est la version de l’entier de la version classique [ `SKPoint` ](xref:SkiaSharp.SKPoint) structure. `SKPointI` définit `X` et `Y` propriétés de type `int` plutôt que `float`.

Ces méthodes créent un motif répétitif de la taille spécifiée. Dans chaque vignette, le bord droit est le même que le bord gauche et le bord supérieur est le même que le bord inférieur. Cette caractéristique est illustrée dans le **en mosaïque de bruit de Perlin** page. Le fichier XAML est similaire à l’exemple précédent, mais il ne comporte qu’un `Stepper` vue pour modifier le `seed` argument :

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

Le fichier code-behind définit une constante pour la taille de mosaïque. Le `PaintSurface` gestionnaire crée une image bitmap de cette taille et un `SKCanvas` pour le dessin dans cette image bitmap. Le `SKShader.CreatePerlinNoiseTurbulence` méthode crée un nuanceur avec cette taille de vignette. Ce nuanceur est dessiné sur l’image bitmap :

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

Une fois que l’image bitmap a été créé, un autre `SKPaint` objet est utilisé pour créer un modèle en mosaïque bitmap en appelant `SKShader.CreateBitmap`. Notez que les deux arguments de `SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Ce nuanceur est utilisé pour couvrir la zone de dessin. Enfin, un autre `SKPaint` objet est utilisé pour tracer un rectangle indiquant la taille de la bitmap d’origine.

Uniquement la `seed` paramètre est sélectionnable à partir de l’interface utilisateur. Si le même `seed` modèle est utilisé sur chaque plateforme, il affiche le même modèle. Différents `seed` valeurs provoquent des modèles différents :

[![Mosaïque de bruit de Perlin](noise-images/TiledPerlinNoise.png "affichée en mosaïque de bruit de Perlin")](noise-images/TiledPerlinNoise-Large.png#lightbox)

Le modèle de carré de 200 pixels dans le coin supérieur gauche circulent en toute transparence dans les autres vignettes.

## <a name="combining-multiple-shaders"></a>Combinaison de plusieurs des nuanceurs

Le `SKShader` classe inclut un [ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*) méthode qui crée un nuanceur avec une couleur unie spécifiée. Ce nuanceur n’est pas très utile en soi, car vous pouvez simplement affecter cette couleur la `Color` propriété de la `SKPaint` de l’objet et définir le `Shader` propriété sur null.

Cela `CreateColor` méthode devient utile dans une autre méthode qui `SKShader` définit. Cette méthode est [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)), qui combine les deux nuanceurs. Voici la syntaxe :

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

Le `srcShader` (nuanceur source) est effectivement dessiné en haut de la `dstShader` (nuanceur de destination). Si le nuanceur de source est une couleur unie ou un dégradé sans transparence, le nuanceur de destination est complètement masqué.

Un nuanceur de bruit de Perlin contient la transparence. Si ce nuanceur est la source, le nuanceur de destination s’affichera à travers les zones transparentes.

Le **composé de bruit de Perlin** page a un fichier XAML qui est presque identique à la première **bruit de Perlin** page. Le fichier code-behind est également similaire. Mais la version d’origine **bruit de Perlin** page définit le `Shader` propriété du `SKPaint` au nuanceur retourné à partir de la méthode statique `CreatePerlinNoiseFractalNoise` et `CreatePerlinNoiseTurbulence` méthodes. Cela **composé de bruit de Perlin** page appelle `CreateCompose` pour un nuanceur de combinaison. La destination est un nuanceur bleu solid créé à l’aide de `CreateColor`. La source est un nuanceur de bruit de Perlin :

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

Le nuanceur de bruit fractal est situé en haut ; le nuanceur de turbulences est en bas :

[![Composé de bruit de Perlin](noise-images/ComposedPerlinNoise.png "composé de bruit de Perlin")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Notez comment beaucoup plus bleues ces nuanceurs sont ceux qui sont affichés par le **bruit de Perlin** page. La différence illustre la quantité de transparence dans les nuanceurs de bruit.

Il existe également une surcharge de la [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) méthode :

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

Le paramètre final est un membre de la `SKBlendMode` énumération, une énumération avec les 29 membres qui est décrite dans la prochaine série d’articles sur [ **SkiaSharp composition et modes de fusion**](../blend-modes/index.md).

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
