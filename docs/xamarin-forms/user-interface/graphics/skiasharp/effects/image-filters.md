---
title : « SkiaSharp image filters » Description : « Découvrez comment utiliser le filtre d’images pour créer des flous et des ombres portées. »
ms. Prod : xamarin ms. Technology : xamarin-skiasharp ms. AssetID : 173E7B22-AEC8-4F12-B657-1C0CEE01AD63 auteur : davidbritch ms. Author : dabritch ms. Date : 08/27/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-image-filters"></a>Filtres d’image SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les filtres d’images sont des effets qui fonctionnent sur tous les bits de couleur des pixels qui composent une image. Ils sont plus polyvalents que les filtres de masque, qui fonctionnent uniquement sur le canal alpha comme décrit dans l’article [**filtres de masque SkiaSharp**](mask-filters.md). Pour utiliser un filtre d’image, affectez à la propriété de la valeur d' [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) `SKPaint` un objet de type [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) que vous avez créé en appelant l’une des méthodes statiques de la classe.

La meilleure façon de se familiariser avec les filtres de masque consiste à expérimenter ces méthodes statiques. Vous pouvez utiliser un filtre de masque pour brouiller l’intégralité d’une image bitmap :

![Exemple de flou](image-filters-images/ImageFilterExample.png "Exemple de flou")

Cet article montre également comment utiliser un filtre d’image pour créer une ombre portée et pour les effets de relief et de gravure.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Flou des graphiques vectoriels et des bitmaps

L’effet de flou créé par la [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) méthode statique présente un avantage significatif par rapport aux méthodes de flou de la [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) classe : le filtre d’image peut rendre floue l’intégralité d’une bitmap. La méthode a la syntaxe suivante :

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

La méthode a deux valeurs Sigma &mdash; la première pour l’étendue floue dans la direction horizontale et la seconde pour la direction verticale. Vous pouvez monter en cascade des filtres d’images en spécifiant un autre filtre d’image comme troisième argument facultatif. Un rectangle de rognage peut également être spécifié.

La page **expérience d’image floue** dans le [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) comprend deux `Slider` vues qui vous permettent d’expérimenter la définition de différents niveaux de flou :

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

Le fichier code-behind utilise les deux `Slider` valeurs pour appeler `SKImageFilter.CreateBlur` pour l' `SKPaint` objet utilisé pour afficher à la fois le texte et une bitmap :

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

Les trois captures d’écran affichent différents paramètres pour les `sigmaX` `sigmaY` paramètres et :

[![Expérience d’image floue](image-filters-images/ImageBlurExperiment.png "Expérience d’image floue")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Pour garantir la cohérence du flou parmi les différentes tailles et résolutions d’affichage, définissez `sigmaX` et `sigmaY` sur des valeurs proportionnelles à la taille de pixel rendue de l’image à laquelle le flou est appliqué.

## <a name="drop-shadow"></a>Ombre portée

La [`SKImageFilter.CreateDropShadow`](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) méthode statique crée un `SKImageFilter` objet pour une ombre portée :

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Définissez cet objet sur la `ImageFilter` propriété d’un `SKPaint` objet, et tout ce que vous dessinez avec cet objet aura une ombre portée derrière.

Les `dx` `dy` paramètres et indiquent les décalages horizontal et vertical, en pixels, par rapport à l’objet graphique. La Convention dans les graphiques 2D consiste à supposer qu’une source de lumière provient du coin supérieur gauche, ce qui signifie que ces deux arguments doivent être positifs pour positionner l’ombre ci-dessous et à droite de l’objet graphique.

Les `sigmaX` `sigmaY` paramètres et sont des facteurs d’atténuation pour l’ombre portée.

Le `color` paramètre est la couleur de l’ombre portée. Cette `SKColor` valeur peut inclure la transparence. L’une des possibilités est la valeur `SKColors.Black.WithAlpha(0x80)` de couleur pour assombrir n’importe quel arrière-plan de couleur.

Les deux derniers paramètres sont facultatifs.

Le programme d' **expérimentation fantôme** vous permet d’expérimenter les valeurs de `dx` , `dy` , `sigmaX` et `sigmaY` d’afficher une chaîne de texte avec une ombre portée. Le fichier XAML instancie quatre `Slider` vues pour définir ces valeurs :

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

Le fichier code-behind utilise ces valeurs pour créer une ombre rouge sur une chaîne de texte bleue :

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

[![Expérience fantôme](image-filters-images/DropShadowExperiment.png "Expérience fantôme")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

Les valeurs de décalage négatives dans la capture d’écran plateforme Windows universelle à l’extrême droite font apparaître l’ombre au-dessus et à gauche du texte. Cela suggère une source de lumière dans le coin inférieur droit, qui n’est pas la Convention pour les graphiques informatiques. Mais cela ne semble pas être erroné, peut-être parce que l’ombre est également rendue très floue et semble plus décoratif que la plupart des ombres portées.

## <a name="lighting-effects"></a>Effets d’éclairage

La `SKImageFilter` classe définit six méthodes qui ont des noms et des paramètres similaires, répertoriés ici par ordre de complexité croissante :

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Ces méthodes créent des filtres d’image qui imitent l’effet de différents types de lumière sur des surfaces en trois dimensions. Le filtre d’images qui en résulte éclaire les objets à deux dimensions comme s’ils existaient dans l’espace 3D, ce qui peut entraîner l’affichage de ces objets avec élévation de privilèges ou mise en surbrillance, ou avec une mise en surbrillance spéculaire.

Les `Distant` méthodes légères supposent que la lumière vient d’une distance éloignée. Dans le cadre de l’éclairage des objets, la lumière est supposée pointer dans une direction cohérente dans l’espace 3D, à l’instar du soleil sur une petite zone de la terre. Les `Point` méthodes légères imitent une ampoule placée dans l’espace 3D qui émet de la lumière dans toutes les directions. La `Spot` lumière a à la fois une position et une direction, en grande partie comme une torche.

Les emplacements et les directions dans l’espace 3D sont spécifiés avec les valeurs de la [`SKPoint3`](xref:SkiaSharp.SKPoint3) structure, ce qui est similaire à, `SKPoint` mais avec trois propriétés nommées `X` , `Y` et `Z` .

Le nombre et la complexité des paramètres de ces méthodes rendent l’expérimentation difficile. Pour vous aider à démarrer, la page d' **expérimentation légère distante** vous permet d’expérimenter les paramètres de la `CreateDistantLightDiffuse` méthode :

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

La page n’utilise pas les deux derniers paramètres facultatifs.

Trois `Slider` vues du fichier XAML vous permettent de sélectionner la `Z` coordonnée de la `SKPoint3` valeur, le `surfaceScale` paramètre et le `kd` paramètre, qui est défini dans la documentation de l’API comme « constante d’éclairage diffuse » :

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

Le fichier code-behind obtient ces trois valeurs et les utilise pour créer un filtre d’images pour afficher une chaîne de texte :

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

Le premier argument de `SKImageFilter.CreateDistantLitDiffuse` est la direction de la lumière. Les coordonnées X et Y positives indiquent que la lumière est pointée vers la droite et vers le dessous. Les coordonnées Z positives pointent sur l’écran. Le fichier XAML vous permet de sélectionner des valeurs Z négatives, mais cela ne vous permet de voir ce qui se passe : de manière conceptuelle, les coordonnées Z négatives provoquent le pointage de la lumière hors de l’écran. Pour tout autre valeur négative, l’effet d’éclairage cesse de fonctionner.

L' `surfaceScale` argument peut être compris entre-1 et 1. (Les valeurs les plus élevées ou les plus basses n’ont pas d’effet supplémentaire.) Il s’agit de valeurs relatives dans l’axe Z qui indiquent le déplacement de l’objet graphique (dans ce cas, la chaîne de texte) à partir de la surface de canevas. Utilisez des valeurs négatives pour élever la chaîne de texte au-dessus de la surface du canevas, et les valeurs positives pour la faire glisser dans le canevas.

La `lightConstant` valeur doit être positive. (Le programme autorise des valeurs négatives afin que vous puissiez constater qu’elles entraînent l’arrêt du travail de l’effet.) Des valeurs plus élevées provoquent une lumière plus intense.

Ces facteurs peuvent être équilibrés pour obtenir un effet de relief lorsque `surfaceScale` est négatif (comme avec les captures d’écran iOS et Android) et un effet d’empreinte lorsque `surfaceScale` est positif, comme dans la capture d’écran UWP située à droite :

[![Expérience de lumière distante](image-filters-images/DistantLightExperiment.png "Expérience de lumière distante")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

La capture d’écran Android a une valeur Z égale à 0, ce qui signifie que la lumière pointe uniquement vers le haut et vers la droite. L’arrière-plan n’est pas éclairé et la surface de la chaîne de texte n’est pas éclairée. La lumière affecte uniquement le bord du texte pour un effet très subtil.

Une autre approche du texte en relief et d’empreinte a été illustrée dans l’article [transformation de traduction](../transforms/translate.md): la chaîne de texte est affichée deux fois avec des couleurs différentes qui sont légèrement décalées les unes des autres.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
