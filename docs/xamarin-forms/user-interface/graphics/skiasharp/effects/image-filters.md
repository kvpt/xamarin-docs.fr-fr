---
title: Filtres d’image SkiaSharp
description: Découvrez comment utiliser le filtre d’image pour créer un flou et supprimer des ombres.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 54f6c8b94a1abdfcc3be9a86e179e766c417232b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655383"
---
# <a name="skiasharp-image-filters"></a>Filtres d’image SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les filtres d’image sont des effets qui fonctionnent sur tous les bits de couleur des pixels qui composent une image. Ils sont plus polyvalents que les filtres de masque, qui fonctionnent uniquement sur le canal alpha comme décrit dans l’article [ **filtres de masque de SkiaSharp**](mask-filters.md). Pour utiliser un filtre d’image, définissez la [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) propriété du `SKPaint` à un objet de type [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) que vous avez créé en appelant une des méthodes statiques de la classe.

La meilleure façon de vous familiariser avec les filtres de masque est en expérimentant de ces méthodes statiques. Vous pouvez utiliser un filtre de masque de flou d’une image bitmap entière :

![Exemple de flou](image-filters-images/ImageFilterExample.png "flou exemple")

Cet article montre également à l’aide d’un filtre d’image pour créer un dépôt clichés instantanés et de relief et de la gravure des effets.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Flou des graphiques vectoriels et des bitmaps

L’effet de flou créé par [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) la méthode statique présente un avantage significatif par rapport aux méthodes de [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) flou de la classe: Le filtre d’image peut rendre floue l’intégralité d’une bitmap. La méthode a la syntaxe suivante :

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

La méthode a deux valeurs sigma &mdash; la première pour l’étendue de flou dans la direction horizontale et le second pour la direction verticale. Vous pouvez en cascade les filtres d’image en spécifiant un autre filtre d’image comme troisième argument facultatif. Un rectangle de rognage peut également être spécifié.

Le **Image flou expérimenter** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inclut deux `Slider` vues qui vous permettent de faire des essais avec différents niveaux de flou de paramètre :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind utilise les deux `Slider` valeurs appeler `SKImageFilter.CreateBlur` pour le `SKPaint` objet utilisé pour afficher du texte et une image bitmap :


```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

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

Les trois captures d’écran affichent les différents paramètres pour le `sigmaX` et `sigmaY` paramètres :

[![Expérience de flou de l’image](image-filters-images/ImageBlurExperiment.png "expérience de flou de l’Image")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Pour garantir la cohérence entre les différentes tailles d’affichage et les résolutions de l’effet de flou, définissez `sigmaX` et `sigmaY` aux valeurs qui soient proportionnelles à la taille en pixels de rendu de l’image appliquée à l’effet de flou.

## <a name="drop-shadow"></a>Ombre portée

Le [ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) méthode statique crée une `SKImageFilter` objet pour une ombre portée :

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Cet objet de la valeur la `ImageFilter` propriété d’un `SKPaint` objet et tout ce que vous dessinez avec cet objet a une ombre derrière lui.

Le `dx` et `dy` paramètres indiquent les décalages horizontales et verticales de l’ombre en pixels à partir de l’objet graphique. La convention dans les graphiques 2D est de supposer une source de lumière provenant de la partie supérieure gauche, ce qui implique que ces deux arguments doivent être positives pour positionner l’ombre en dessous et à droite de l’objet graphique.

Le `sigmaX` et `sigmaY` paramètres sont estompent facteurs pour l’ombre portée.

Le `color` paramètre est la couleur de l’ombre. Cela `SKColor` valeur peut inclure la transparence. Une des possibilités est la valeur de couleur `SKColors.Black.WithAlpha(0x80)` pour assombrir toute couleur d’arrière-plan.

Les deux derniers paramètres sont facultatifs.

Le **expérimenter de clichés instantanés Drop** programme vous permet de vous faire des essais avec des valeurs de `dx`, `dy`, `sigmaX`, et `sigmaY` pour afficher une chaîne de texte avec une ombre portée. Le fichier XAML instancie quatre `Slider` vues pour définir ces valeurs :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind utilise ces valeurs pour créer une ombre rouge sur une chaîne de texte en bleu :

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        canvas.Clear();

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Voici le programme en cours d’exécution :

[![Supprimer l’expérience de clichés instantanés](image-filters-images/DropShadowExperiment.png "supprimer l’expérience de clichés instantanés")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Les valeurs de décalage négatif dans la capture d’écran de la plateforme Windows universelle à l’extrême droite provoquent l’ombre à apparaissent au-dessus et à gauche du texte. Cela suggère une source de lumière dans le coin inférieur droit, ce qui n’est pas la convention pour les graphiques de l’ordinateur. Mais il ne semble incorrect de quelque façon, peut-être parce que l’ombre est également très floue et semble plus fins esthétiques que la plupart des ombres.

## <a name="lighting-effects"></a>Effets d’éclairage

Le `SKImageFilter` classe définit six méthodes qui ont des noms similaires et des paramètres, répertoriés par ordre croissant de complexité :

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Ces méthodes créent des filtres d’image qui simulent l’effet de différents types de lumière sur les surfaces en trois dimensions. Le filtre d’image résultante éclaire les objets à deux dimensions comme si elles existaient dans l’espace 3D, ce qui peut entraîner ces objets s’affiche avec élévation de privilèges ou en retrait, ou avec la mise en surbrillance spéculaire.

Le `Distant` claires méthodes supposent que la lumière provient d’une distance lointain. Pour les besoins d’éclairage d’objets, la lumière est supposée à un point dans une direction cohérente dans l’espace 3D, comme le soleil sur une petite zone de la terre. Le `Point` méthodes claires imitent une ampoule positionnée dans l’espace 3D qui émet de la lumière dans toutes les directions. Le `Spot` light a une position et une direction, comme une torche.

Sens dans l’espace 3D et les emplacements sont spécifiés avec des valeurs de la [ `SKPoint3` ](xref:SkiaSharp.SKPoint3) structure, ce qui revient à `SKPoint` mais avec trois propriétés nommées `X`, `Y`, et `Z`.

Le nombre et la complexité des paramètres à ces méthodes compliquent l’expérimentation avec eux. Pour vous aider à démarrer, le **expérimenter de lumière distante** page vous permet de vous faire des essais avec des paramètres pour le `CreateDistantLightDiffuse` méthode :

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

La page n’utilise pas les deux derniers paramètres facultatifs.

Trois `Slider` vues dans le XAML de fichiers permettent de sélectionner le `Z` coordonnée de la `SKPoint3` valeur, le `surfaceScale` paramètre et le `kd` paramètre, qui est défini dans la documentation de l’API en tant que la constante « éclairage diffus » :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind obtient ces trois valeurs et les utilise pour créer un filtre d’image pour afficher une chaîne de texte :

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        canvas.Clear();

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Le premier argument de `SKImageFilter.CreateDistantLitDiffuse` est la direction de la lumière. Positif X et Y coordonnées indiquent que la lumière est pointe vers la droite et vers le bas. Point de coordonnées Z positif dans l’écran. Le fichier XAML vous permet de sélectionner des valeurs Z négatives, mais ce n’est que pour vous permettre de voir ce qui se passe: Conceptuellement, les coordonnées Z négatives provoquent le pointage de la lumière hors de l’écran. Pour tout autres puis petites valeurs négatives, l’effet d’éclairage cesse de fonctionner.

Le `surfaceScale` argument peut varier de -1 à 1. (Les valeurs supérieures ou inférieures n’ont aucun effet.) Il s’agit des valeurs relatives de l’axe Z qui indiquent le déplacement de l’objet de graphique (dans ce cas, il s’agit de la chaîne de texte) à partir de la surface de la zone de dessin. Utilisez les valeurs négatives pour déclencher la chaîne de texte au-dessus de la surface de dessin et les valeurs positives à appuyer sur dans la zone de dessin.

Le `lightConstant` valeur doit être positive. (Le programme permet les valeurs négatives afin de voir qu’ils provoquent l’effet cesser de fonctionner.) Les valeurs plus élevés, plus intense clair.

Ces facteurs peuvent être équilibrées pour obtenir un en relief effet lorsque `surfaceScale` est un nombre négatif (comme avec iOS et Android captures d’écran) et une empreinte effet lorsque `surfaceScale` est un nombre positif, comme avec la capture d’écran UWP à droite :

[![Expérience de lumière distante](image-filters-images/DistantLightExperiment.png "expérience de lumière distante")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

La capture d’écran Android a une valeur Z égale à 0, ce qui signifie que la lumière pointe uniquement vers le bas et vers la droite. L’arrière-plan n’est pas allumé et la surface de la chaîne de texte n’est pas éclairée soit. La lumière affecte uniquement le bord du texte pour un effet très subtil.

Une autre approche du texte en relief et d’empreinte a été illustrée dans l’article [transformation de traduction](../transforms/translate.md): La chaîne de texte s’affiche deux fois avec différentes couleurs qui sont légèrement décalées les unes des autres.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
