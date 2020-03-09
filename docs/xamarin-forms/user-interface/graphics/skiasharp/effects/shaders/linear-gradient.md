---
title: Dégradé linéaire SkiaSharp
description: Découvrez comment tracer des lignes d’ou remplir des zones avec des dégradés composé d’un mélange graduel de deux couleurs.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 290e533e54b2ee150b94d9fb6b0f5119324f9cf0
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78916440"
---
# <a name="the-skiasharp-linear-gradient"></a>Dégradé linéaire SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La classe [`SKPaint`](xref:SkiaSharp.SKPaint) définit une propriété [`Color`](xref:SkiaSharp.SKPaint.Color) utilisée pour rayer des lignes ou des zones de remplissage avec une couleur unie. Vous pouvez également tracer des lignes ou des zones de remplissage avec des _dégradés_, qui sont des fusions progressives de couleurs :

![Exemple de dégradé linéaire](linear-gradient-images/LinearGradientSample.png "Exemple de dégradé linéaire")

Le type de dégradé le plus basique est un dégradé _linéaire_ . Le mélange des couleurs se produit sur une ligne (appelée _ligne de dégradé_) d’un point à un autre. Les lignes qui sont perpendiculaires à la ligne de dégradé ont la même couleur. Vous créez un dégradé linéaire à l’aide de l’une des deux méthodes de [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) statiques. La différence entre les deux surcharges est qu’une comprend une matrice de transformation et l’autre n’est pas le cas. 

Ces méthodes retournent un objet de type [`SKShader`](xref:SkiaSharp.SKShader) que vous affectez à la propriété [`Shader`](xref:SkiaSharp.SKPaint.Shader) de `SKPaint`. Si la propriété `Shader` est non null, elle remplace la propriété `Color`. Toute ligne rayée ou toute zone remplie à l’aide de cet objet `SKPaint` est basée sur le dégradé plutôt que sur la couleur unie.

> [!NOTE]
> La propriété `Shader` est ignorée lorsque vous incluez un objet `SKPaint` dans un appel de `DrawBitmap`. Vous pouvez utiliser la propriété `Color` de `SKPaint` pour définir un niveau de transparence pour l’affichage d’une bitmap (comme décrit dans l’article [affichage des bitmaps SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), mais vous ne pouvez pas utiliser la propriété `Shader` pour afficher une bitmap avec une transparence de dégradé. D’autres techniques sont disponibles pour afficher des bitmaps avec des transparences dégradées : celles-ci sont décrites dans les articles [SkiaSharp dégradés circulaires](circular-gradients.md#radial-gradients-for-masking) et [SkiaSharp modes de composition et de fusion](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Coin à dégradés

Souvent un dégradé linéaire s’étend à partir d’un coin d’un rectangle à un autre. Si le point de départ est le coin supérieur gauche du rectangle, le dégradé peut étendre :

- verticalement vers le bas à gauche
- horizontalement à l’angle supérieur droit
- en diagonale vers le bas à droite

Le dégradé linéaire Diagonal est illustré dans la première page de la section **nuanciers SkiaSharp et autres effets** de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . La page de **dégradé d’angle à angle** crée un `SKCanvasView` dans son constructeur. Le gestionnaire de `PaintSurface` crée un objet `SKPaint` dans une instruction `using`, puis définit un rectangle carré de 300 pixels centré dans la zone de dessin :

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

Le `SKShader` valeur de retour de la méthode statique `SKShader.CreateLinearGradient` est assignée à la propriété `Shader` de `SKPaint`. Les cinq arguments sont les suivantes :

- Le point de départ du dégradé, définies ici pour l’angle supérieur gauche du rectangle
- Le point de terminaison du dégradé, la valeur est ici le coin inférieur droit du rectangle
- Un tableau de deux ou plusieurs couleurs qui contribuent au dégradé
- Tableau de valeurs `float` indiquant la position relative des couleurs dans la ligne de dégradé
- Membre de l’énumération [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) indiquant comment le dégradé se comporte au-delà des extrémités de la ligne de dégradé

Une fois l’objet gradient créé, la méthode `DrawRect` dessine le rectangle carré de 300 pixels à l’aide de l’objet `SKPaint` qui comprend le nuanceur. Ici, il s’exécute sur iOS, Android et la plateforme universelle Windows (UWP) :

[![Dégradé de coin à angle](linear-gradient-images/CornerToCornerGradient.png "Dégradé de coin à angle")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La ligne de dégradé est définie par les deux points spécifiés en tant que les deux premiers arguments. Notez que ces points sont relatifs à la _zone de dessin_ et _non_ à l’objet graphique affiché avec le dégradé. La ligne de dégradé, la couleur passe progressivement du rouge en haut à gauche en bleu en bas à droite. N’importe quelle ligne perpendiculaire à la ligne de dégradé a une couleur constante.

Le tableau de valeurs `float` spécifié comme quatrième argument a une correspondance un-à-un avec le tableau de couleurs. Les valeurs indiquent la position relative le long de la ligne de dégradé où ces couleurs se produisent. Ici, 0 signifie que `Red` se produit au début de la ligne de dégradé, et 1 signifie que `Blue` se trouve à la fin de la ligne. Les nombres doivent être croissant et doivent se trouver dans la plage de 0 à 1. Si elles ne sont pas dans cette plage, ils seront ajustés pour être dans cette plage.

Les deux valeurs dans le tableau peuvent indiquer une valeur autre que 0 et 1. Procédez comme suit :

```csharp
new float[] { 0.25f, 0.75f }
```

Le tout premier trimestre de la ligne de dégradé est maintenant rouge pure et le dernier trimestre est bleu pur. La combinaison de rouge et bleu est limitée à la partie centrale de la ligne de dégradé.

En règle générale, vous voudrez espace ces valeurs de position tout aussi allant de 0 à 1. Si tel est le cas, vous pouvez simplement fournir `null` comme quatrième argument à `CreateLinearGradient`.

Bien que ce dégradé est défini entre deux angles du rectangle 300 pixels carré, il n’est pas limité au remplissage de ce rectangle. La page de **dégradé d’angle à angle** comprend du code supplémentaire qui répond aux pressions ou clics de souris sur la page. Le champ `drawBackground` est basculé entre `true` et `false` à chaque pression. Si la valeur est `true`, le gestionnaire de `PaintSurface` utilise le même objet `SKPaint` pour remplir l’intégralité du canevas, puis dessine un rectangle noir indiquant le plus petit rectangle : 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

Voici ce que vous verrez après avoir appuyé sur l’écran :

[![Dégradé de coin à angle plein](linear-gradient-images/CornerToCornerGradientFull.png "Dégradé de coin à angle plein")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Notez que le dégradé se répète dans le même modèle au-delà de points définissant la ligne de dégradé. Cette répétition se produit parce que le dernier argument de `CreateLinearGradient` est `SKShaderTileMode.Repeat`. (Vous verrez les autres options sous peu.)

Notez également que les points que vous utilisez pour spécifier la ligne de dégradé ne sont pas uniques. Les lignes qui sont perpendiculaires à la ligne de dégradé ont la même couleur, il y a un nombre infini de dégradé lignes que vous pouvez spécifier pour le même effet. Par exemple, lors du remplissage d’un rectangle avec un dégradé horizontal, vous pouvez spécifier les angles supérieur gauche et le coin supérieur droit, ou les angles inférieur gauche et inférieur droit ou les deux points même avec et parallèlement à ces lignes.

## <a name="interactively-experiment"></a>Tester de manière interactive

Vous pouvez faire des essais interactifs avec des dégradés linéaires avec la page **dégradé linéaire interactif** . Cette page utilise la classe `InteractivePage` présentée dans l’article [**trois façons de dessiner un arc**](../../curves/arcs.md). `InteractivePage` gère les événements [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) pour gérer une collection d’objets `TouchPoint` que vous pouvez déplacer avec vos doigts ou la souris.

Le fichier XAML joint le `TouchEffect` à un parent du `SKCanvasView` et comprend également un `Picker` qui vous permet de sélectionner l’un des trois membres de l’énumération [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) :

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

Le constructeur du fichier code-behind crée deux objets `TouchPoint` pour les points de début et de fin du dégradé linéaire. Le gestionnaire de `PaintSurface` définit un tableau de trois couleurs (pour un dégradé du rouge au vert bleu) et obtient le `SKShaderTileMode` actuel à partir du `Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

Le gestionnaire de `PaintSurface` crée l’objet `SKShader` à partir de toutes ces informations et l’utilise pour colorer la zone de dessin entière. Le tableau des valeurs de `float` est défini sur `null`. Sinon, pour tout aussi espace trois couleurs, vous devez définir ce paramètre dans un tableau avec les valeurs 0, 0,5 et 1.

La majeure partie du gestionnaire de `PaintSurface` est consacrée à l’affichage de plusieurs objets : les points tactiles comme cercles de contour, la ligne de dégradé et les lignes perpendiculaires aux lignes de dégradé aux points de contact :

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

La ligne de dégradé qui relie les deux touchpoints est facile à dessiner, mais les lignes perpendiculaires nécessitent plus de travail. La ligne de dégradé est convertie en un vecteur, normalisée pour avoir une longueur d’une unité et ensuite pivoter de 90 degrés. Ce vecteur reçoit une longueur de 200 pixels. Il est utilisé pour dessiner les quatre lignes qui relient les points tactiles soit perpendiculaire à la ligne de dégradé.

Les lignes perpendiculaires coïncident avec le début et la fin du dégradé. Ce qui se passe au-delà de ces lignes dépend du paramètre de l’énumération `SKShaderTileMode` :

[![Dégradé linéaire interactif](linear-gradient-images/InteractiveLinearGradient.png "Dégradé linéaire interactif")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Les trois captures d’écran montrent les résultats des trois différentes valeurs de [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode). La capture d’écran iOS affiche `SKShaderTileMode.Clamp`, qui étend simplement les couleurs sur la bordure du dégradé. L’option `SKShaderTileMode.Repeat` de la capture d’écran Android montre comment le modèle de dégradé est répété. L’option `SKShaderTileMode.Mirror` de la capture d’écran UWP répète également le modèle, mais le modèle est inversé à chaque fois, ce qui n’entraîne aucune discontinuité de la couleur.

## <a name="gradients-on-gradients"></a>Dégradés sur des dégradés

La classe `SKShader` ne définit aucune propriété ou méthode publique, à l’exception de `Dispose`. Les objets `SKShader` qui ont été créés par ses méthodes statiques sont donc immuables. Même si vous utilisez le même dégradé pour deux objets différents, il est probable que vous souhaitez varier légèrement en fonction du dégradé. Pour ce faire, vous devez créer un nouvel objet `SKShader`.

La page de **texte dégradé** affiche du texte et un arrière-plan qui sont tous deux colorés avec des dégradés similaires :

[![Texte dégradé](linear-gradient-images/GradientText.png "Texte dégradé")](linear-gradient-images/GradientText-Large.png#lightbox)

Les seules différences dans les dégradés sont le début et points de terminaison. Du dégradé utilisé pour l’affichage de texte est basé sur deux points sur les angles du rectangle englobant pour le texte. Pour l’arrière-plan, les deux points sont basés sur la totalité du canevas. Voici le code :

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

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
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

La propriété `Shader` de l’objet `SKPaint` est définie en premier pour afficher un dégradé afin de couvrir l’arrière-plan. Les points de dégradé sont définis sur les angles supérieur gauche et inférieur droit de la zone de dessin.

Le code définit la propriété `TextSize` de l’objet `SKPaint` afin que le texte s’affiche à 90% de la largeur de la zone de dessin. Les limites de texte sont utilisées pour calculer `xText` et `yText` valeurs à passer à la méthode `DrawText` pour centrer le texte.

Toutefois, les points de dégradé pour le deuxième `CreateLinearGradient` appel doivent faire référence au coin supérieur gauche et inférieur droit du texte par rapport au canevas lorsqu’il est affiché. Pour ce faire, vous devez déplacer le rectangle `textBounds` des mêmes `xText` et `yText` valeurs :

```csharp
textBounds.Offset(xText, yText);
```

Les angles supérieur gauche et inférieur droit du rectangle peuvent maintenant être utilisés pour définir le démarrage et les points de terminaison du dégradé.

## <a name="animating-a-gradient"></a>Animer un dégradé

Il existe plusieurs façons d’animer un dégradé. Une approche consiste à animer le début et points de terminaison. La page d' **animation de dégradé** déplace les deux points autour d’un cercle centré sur la zone de dessin. Le rayon de ce cercle est la moitié de la largeur ou hauteur de la zone de dessin, plus petite étant retenue. Les points de début et de fin sont opposés l’un de l’autre sur ce cercle et le dégradé passe du blanc au noir avec un mode de vignette `Mirror` :

[![Dégradé, animation](linear-gradient-images/GradientAnimation.png "Dégradé, animation")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

Le constructeur crée le `SKCanvasView`. Les méthodes `OnAppearing` et `OnDisappearing` gèrent la logique d’animation :

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

La méthode `OnTimerTick` calcule une valeur `angle` animée de 0 à 2π toutes les 3 secondes. 

Voici une façon de calculer les deux points de dégradé. Une valeur `SKPoint` nommée `vector` est calculée pour s’étendre du centre du canevas jusqu’à un point sur le rayon du cercle. La direction de ce vecteur est basée sur les valeurs de sinus et le cosinus de l’angle. Les deux points de dégradé opposés sont ensuite calculées : un point est calculé en soustrayant ce vecteur à partir du point center et autre point est calculée en ajoutant le vecteur vers le point central :

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Une approche un peu différente nécessite moins de code. Cette approche utilise la méthode de surcharge [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) avec une transformation de matrice comme dernier argument. Cette approche est la version de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Si la largeur de la zone de dessin est inférieure à la hauteur, les deux points de dégradé sont définis sur (0, 0) et (`info.Width`, 0). La transformation de rotation transmise comme dernier argument à `CreateLinearGradient` fait pivoter ces deux points au centre de l’écran.

Notez que si l’angle est 0, il n’existe aucune rotation, et les deux points de dégradé sont les angles supérieur gauche et le coin supérieur droit de la zone de dessin. Ces points ne sont pas les mêmes points de dégradé calculés comme indiqué dans l’appel de `CreateLinearGradient` précédent. Toutefois, ces points sont _parallèles_ à la ligne de dégradé horizontale qui coupe le centre du canevas, et elles produisent un dégradé identique.

**Dégradé arc-en-ciel**

La page **dégradé arc** -en-ciel dessine un arc-ciel du coin supérieur gauche de la zone de dessin vers le coin inférieur droit. Mais ce dégradé arc-en-ciel n’est pas comme un véritable arc-en-ciel. Il est directement plutôt que les courbes, mais il est basé sur huit couleurs TSL (teinte-saturation-luminosité) qui sont déterminés en parcourant les valeurs de teinte comprise entre 0 et 360 :

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Ce code fait partie du gestionnaire de `PaintSurface` présenté ci-dessous. Le gestionnaire commence en créant un chemin d’accès qui définit un polygone six côtés qui s’étend à partir de l’angle supérieur gauche de la zone de dessin à l’angle inférieur droit :

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

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

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Les deux points de dégradé de la méthode `CreateLinearGradient` sont basés sur deux des points qui définissent ce chemin d’accès : les deux points sont proches de l’angle supérieur gauche. La première est sur le bord supérieur de la zone de dessin et la seconde sur le bord gauche de la zone de dessin. Voici le résultat :

[![Dégradé arc-ciel défectueux](linear-gradient-images/RainbowGradientFaulty.png "Dégradé arc-ciel défectueux")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Il s’agit d’une image intéressante, mais il n’est pas tout à fait l’intention. Le problème est que lorsque vous créez un dégradé linéaire, les lignes de constante de couleur sont perpendiculaires à la ligne de dégradé. La ligne de dégradé est basée sur les points où la figure touche les côtés supérieure et gauche, et cette ligne est généralement pas perpendiculaire sur les bords de la figure qui étendent vers la bas à droite. Cette approche fonctionnerait uniquement si la zone de dessin ont été carré.

Pour créer un dégradé arc-en-ciel appropriée, la ligne de dégradé doit être perpendiculaire à la périphérie de l’arc-en-ciel. C’est un calcul plus complexe. Un vecteur qui est parallèle au côté long de la figure doit être défini. Le vecteur est pivotée de 90 degrés afin qu’il soit perpendiculaire à ce côté. Elle est ensuite allongée pour être la largeur de la figure en multipliant par `rainbowWidth`. Les deux points de dégradé sont calculés sur un point sur le côté de la figure, et qui pointent ainsi que le vecteur. Voici le code qui apparaît dans la page **dégradé arc** -en-ciel de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Désormais les couleurs de l’arc-en-ciel sont alignés sur la figure :

[![Dégradé arc-en-ciel](linear-gradient-images/RainbowGradient.png "Dégradé arc-en-ciel")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Couleurs infinies**

Un dégradé arc-en-ciel est également utilisé dans la page **couleurs infinies** . Cette page dessine un signe infini à l’aide d’un objet Path décrit dans l’article [**trois types de courbes de Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). L’image est ensuite colorée avec un dégradé d’arc-en-ciel animée balaye en permanence sur l’image.

Le constructeur crée l’objet `SKPath` décrivant le signe infini. Une fois le chemin d’accès est créée, le constructeur peut également obtenir les limites rectangulaires de chemin d’accès. Il calcule ensuite une valeur appelée `gradientCycleLength`. Si un dégradé est basé sur les angles supérieur gauche et inférieur droit du rectangle de `pathBounds`, cette `gradientCycleLength` valeur est la largeur totale du dégradé du motif :

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

Le constructeur crée également le tableau `colors` pour l’arc-en-ciel et l’objet `SKCanvasView`.

Les remplacements des méthodes `OnAppearing` et `OnDisappearing` effectuent la surcharge de l’animation. La méthode `OnTimerTick` anime le champ `offset` de 0 à `gradientCycleLength` toutes les deux secondes :

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Enfin, le gestionnaire de `PaintSurface` restitue le signe infini. Étant donné que le chemin d’accès contient des coordonnées négatives et positives entourant un point central de (0,0), une `Translate` transformation sur le canevas est utilisée pour la déplacer vers le centre. La transformation de traduction est suivie d’une transformation de `Scale` qui applique un facteur d’échelle qui rend le signe infini le plus grand possible tout en restant dans 95% de la largeur et de la hauteur de la zone de dessin. 

Notez que la constante `STROKE_WIDTH` est ajoutée à la largeur et à la hauteur du rectangle englobant du tracé. Le chemin d’accès sera être tracé avec une ligne de cette largeur, donc la taille de la taille de rendu de l’infini est augmentée par ce demi-chasse sur les quatre côtés :

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

Examinez les points passés comme les deux premiers arguments de `SKShader.CreateLinearGradient`. Ces points sont basés sur le chemin d’accès d’origine rectangle englobant. Le premier point est (&ndash;250, &ndash;100) et le second est (250, 100). Interne à SkiaSharp, ces points sont soumis à la transformation en cours de la zone de dessin donc ils s’intègrent correctement avec le signe de l’infini affichée.

Si vous n’avez pas le dernier argument de `CreateLinearGradient`, vous verrez un dégradé arc-en-ciel qui s’étend de l’angle supérieur gauche du signe infini vers le coin inférieur droit. (En fait, le dégradé s’étend à partir du coin supérieur gauche à l’angle inférieur droit du rectangle englobant. Le signe infini rendu est supérieur au rectangle englobant par la moitié de la valeur `STROKE_WIDTH` sur tous les côtés. Étant donné que le dégradé est rouge au début et à la fin, et que le dégradé est créé avec `SKShaderTileMode.Repeat`, la différence n’est pas perceptible.)

Avec le dernier argument de `CreateLinearGradient`, le modèle de dégradé balaye en continu l’ensemble de l’image :

[![Couleurs infinies](linear-gradient-images/InfinityColors.png "Couleurs infinies")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>La transparence et des dégradés

Les couleurs qui contribuent à un dégradé peuvent incorporer la transparence. Au lieu d’un dégradé fondu d’une couleur à l’autre, le dégradé peut disparaisse en fondu une couleur transparent. 

Vous pouvez utiliser cette technique pour certains effets intéressants. Un des exemples classiques montre un objet graphique avec ses réflexion :

[![Dégradé de réflexion](linear-gradient-images/ReflectionGradient.png "Dégradé de réflexion")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Le texte est envers est coloré avec un dégradé est transparent à complètement transparent en bas en haut à 50 %. Ces niveaux de transparence est associés à des valeurs alpha de 0 x 80 et 0.

Le gestionnaire de `PaintSurface` dans la page **dégradé de réflexion** met à l’échelle la taille du texte à 90% de la largeur du canevas. Il calcule ensuite les valeurs `xText` et `yText` pour positionner le texte afin qu’il soit centré horizontalement, mais sur une ligne de base correspondant au centre vertical de la page :

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

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

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Ces valeurs `xText` et `yText` sont identiques à celles utilisées pour afficher le texte réfléchi dans l’appel de `DrawText` en bas du gestionnaire de `PaintSurface`. Toutefois, juste avant ce code, vous verrez un appel à la méthode `Scale` de `SKCanvas`. Cette méthode de `Scale` est mise à l’échelle horizontalement de 1 (ce qui ne fait rien) mais verticalement en &ndash;1, ce qui fait tourner tout ce qui est à l’envers. Le centre de rotation est défini sur le point (0, `yText`), où `yText` est le centre vertical du canevas, calculé à l’origine comme `info.Height` divisé par 2.

N’oubliez pas que Skia utilise le dégradé pour colorer des objets graphiques avant les transformations de la zone de dessin. Une fois le texte non réfléchi dessiné, le rectangle de `textBounds` est décalé de manière à ce qu’il corresponde au texte affiché :

```csharp
textBounds.Offset(xText, yText);
```

L’appel de `CreateLinearGradient` définit un dégradé à partir du haut de ce rectangle en bas. Le dégradé est issu d’un bleu complètement transparent (`paint.Color.WithAlpha(0)`) à un bleu transparent de 50% (`paint.Color.WithAlpha(0x80)`). La transformation de la zone de dessin inverse le texte envers, afin que le bleu transparent de 50 % commence à la ligne de base et devienne transparent en haut du texte.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
