---
title: Dégradé linéaire SkiaSharp
description: Découvrez comment tracer des lignes d’ou remplir des zones avec des dégradés composé d’un mélange graduel de deux couleurs.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 43a427b4eca174dce4af47bb43cb1a048a6cfe7c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647688"
---
# <a name="the-skiasharp-linear-gradient"></a>Dégradé linéaire SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe définit un [ `Color` ](xref:SkiaSharp.SKPaint.Color) propriété qui est utilisée pour tracer des lignes ou de remplir des zones avec une couleur unie. Vous pouvez également tracer des lignes d’ou remplir les zones avec _dégradés_, qui sont progressive dégradés de couleurs :

![Exemple de dégradé linéaire](linear-gradient-images/LinearGradientSample.png "échantillon de dégradé linéaire")

Le type de dégradé plus simple est un _linéaire_ dégradé. Le mélange de couleurs se produit sur une ligne (appelée la _trait dégradé_) à partir d’un point à un autre. Les lignes qui sont perpendiculaires à la ligne de dégradé ont la même couleur. Vous créez un dégradé linéaire à l’aide d’une des deux statiques [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) méthodes. La différence entre les deux surcharges est qu’une comprend une matrice de transformation et l’autre n’est pas le cas. 

Ces méthodes retournent un objet de type [ `SKShader` ](xref:SkiaSharp.SKShader) que vous définissez pour le [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) propriété du `SKPaint`. Si le `Shader` propriété n’est pas null, ce paramètre remplace le `Color` propriété. N’importe quelle ligne qui est rayé ou toute zone qui est remplie à l’aide de ce `SKPaint` objet est basé sur le dégradé plutôt que la couleur unie.

> [!NOTE]
> Le `Shader` propriété est ignorée lorsque vous incluez un `SKPaint` de l’objet dans un `DrawBitmap` appeler. Vous pouvez utiliser la `Color` propriété de `SKPaint` pour définir un niveau de transparence pour l’affichage d’une image bitmap (comme décrit dans l’article [SkiaSharp affichage des bitmaps](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), mais vous ne pouvez pas utiliser le `Shader` propriété d’affichage une image bitmap avec une transparence de dégradé. D’autres techniques sont disponibles pour afficher les bitmaps avec des transparences dégradées: Celles-ci sont décrites dans les articles [SkiaSharp](circular-gradients.md#radial-gradients-for-masking) dégradés circulaires et [SkiaSharp modes de fusion et de composition](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Coin à dégradés

Souvent un dégradé linéaire s’étend à partir d’un coin d’un rectangle à un autre. Si le point de départ est le coin supérieur gauche du rectangle, le dégradé peut étendre :

- verticalement vers le bas à gauche
- horizontalement à l’angle supérieur droit
- en diagonale vers le bas à droite

Le dégradé linéaire diagonal est illustré dans la première page dans le **SkiaSharp nuanceurs et autres effets** section de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple. Le **coin à dégradé** page crée une `SKCanvasView` dans son constructeur. Le `PaintSurface` gestionnaire crée un `SKPaint` de l’objet dans un `using` instruction, puis définit un rectangle carré de 300 pixels centré dans la zone de dessin :

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

Le `Shader` propriété du `SKPaint` est affecté le `SKShader` valeur de retour à partir de la méthode statique `SKShader.CreateLinearGradient` (méthode). Les cinq arguments sont les suivantes :

- Le point de départ du dégradé, définies ici pour l’angle supérieur gauche du rectangle
- Le point de terminaison du dégradé, la valeur est ici le coin inférieur droit du rectangle
- Un tableau de deux ou plusieurs couleurs qui contribuent au dégradé
- Un tableau de `float` valeurs indiquant la position relative des couleurs dans la ligne de dégradé
- Un membre de la [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) énumération indiquant le comportement de dégradé au-delà des extrémités de la ligne de dégradé

Une fois que l’objet de dégradé est créé, le `DrawRect` méthode dessine le rectangle carré de 300 pixels à l’aide de la `SKPaint` objet qui inclut le nuanceur. Ici, il s’exécute sur iOS, Android et la plateforme universelle Windows (UWP) :

[![Coin à dégradé](linear-gradient-images/CornerToCornerGradient.png "coin à dégradé")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La ligne de dégradé est définie par les deux points spécifiés en tant que les deux premiers arguments. Notez que ces points sont relatives à la _canevas_ et _pas_ à l’objet de graphique affichée avec le dégradé. La ligne de dégradé, la couleur passe progressivement du rouge en haut à gauche en bleu en bas à droite. N’importe quelle ligne perpendiculaire à la ligne de dégradé a une couleur constante.

Le tableau de `float` valeurs spécifiées en tant que quatrième argument ont une correspondance biunivoque avec le tableau de couleurs. Les valeurs indiquent la position relative le long de la ligne de dégradé où ces couleurs se produisent. Ici, la valeur 0 signifie que `Red` se produit au début de la ligne de dégradé, et 1 signifie que `Blue` se produit à la fin de la ligne. Les nombres doivent être croissant et doivent se trouver dans la plage de 0 à 1. Si elles ne sont pas dans cette plage, ils seront ajustés pour être dans cette plage.

Les deux valeurs dans le tableau peuvent indiquer une valeur autre que 0 et 1. Écrivez ce code :

```csharp
new float[] { 0.25f, 0.75f }
```

Le tout premier trimestre de la ligne de dégradé est maintenant rouge pure et le dernier trimestre est bleu pur. La combinaison de rouge et bleu est limitée à la partie centrale de la ligne de dégradé.

En règle générale, vous voudrez espace ces valeurs de position tout aussi allant de 0 à 1. Si tel est le cas, vous pouvez simplement fournir `null` comme quatrième argument de `CreateLinearGradient`.

Bien que ce dégradé est défini entre deux angles du rectangle 300 pixels carré, il n’est pas limité au remplissage de ce rectangle. Le **coin à dégradé** page inclut du code supplémentaire qui répond aux clics ou de clics de souris sur la page. Le `drawBackground` champ est activé ou désactivé entre `true` et `false` avec chaque appui sur un. Si la valeur est `true`, puis le `PaintSurface` Gestionnaire utilise la même `SKPaint` de l’objet pour remplir la totalité du canevas et puis dessine un rectangle noir indiquant le plus petit rectangle : 

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

[![Intégral dégradé coin à](linear-gradient-images/CornerToCornerGradientFull.png "intégral dégradé coin à")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Notez que le dégradé se répète dans le même modèle au-delà de points définissant la ligne de dégradé. Cette répétition se produit parce que le dernier argument de `CreateLinearGradient` est `SKShaderTileMode.Repeat`. (Vous verrez les autres options sous peu.)

Notez également que les points que vous utilisez pour spécifier la ligne de dégradé ne sont pas uniques. Les lignes qui sont perpendiculaires à la ligne de dégradé ont la même couleur, il y a un nombre infini de dégradé lignes que vous pouvez spécifier pour le même effet. Par exemple, lors du remplissage d’un rectangle avec un dégradé horizontal, vous pouvez spécifier les angles supérieur gauche et le coin supérieur droit, ou les angles inférieur gauche et inférieur droit ou les deux points même avec et parallèlement à ces lignes.

## <a name="interactively-experiment"></a>Tester de manière interactive

Vous pouvez expérimenter de manière interactive avec un dégradé linéaire avec le **Interactive dégradé linéaire** page. Cette page utilise le `InteractivePage` classe introduite dans l’article [ **trois façons de dessiner un arc**](../../curves/arcs.md). `InteractivePage` handles [ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) événements pour gérer une collection de `TouchPoint` les objets que vous pouvez déplacer avec votre doigt ou de la souris.

Attache le fichier XAML le `TouchEffect` à un parent de la `SKCanvasView` et inclut également un `Picker` qui vous permet de sélectionner l’un des trois membres de le [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) énumération :

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

Le constructeur dans le fichier code-behind crée deux `TouchPoint` objets de début et de points de terminaison du dégradé linéaire. Le `PaintSurface` gestionnaire définit un tableau de trois couleurs (pour un dégradé du rouge au vert, bleu) et obtient actuel `SKShaderTileMode` à partir de la `Picker`:

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

Le `PaintSurface` gestionnaire crée le `SKShader` objet à partir de ces informations et l’utilise pour la totalité du canevas de couleur. Le tableau de `float` valeurs est définie sur `null`. Sinon, pour tout aussi espace trois couleurs, vous devez définir ce paramètre dans un tableau avec les valeurs 0, 0,5 et 1.

La majeure partie de la `PaintSurface` gestionnaire est consacré à l’affichage de plusieurs objets : les points tactiles comme des cercles de contour, la ligne de dégradé et les lignes perpendiculaires aux lignes de dégradé sur les points tactiles :

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

Les lignes perpendiculaires coïncident avec le début et la fin du dégradé. Que se passe-t-il au-delà de ces lignes dépend du paramètre de la `SKShaderTileMode` énumération :

[![Dégradé linéaire interactive](linear-gradient-images/InteractiveLinearGradient.png "Interactive dégradé linéaire")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Les trois captures d’écran affichent les résultats des trois valeurs différentes de [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode). La capture d’écran d’iOS affiche `SKShaderTileMode.Clamp`, qui étend simplement les couleurs sur la bordure du dégradé. Le `SKShaderTileMode.Repeat` option dans la capture d’écran Android montre comment le modèle de dégradé est répété. Le `SKShaderTileMode.Mirror` option dans la capture d’écran UWP répète également le modèle, mais le modèle est inversé chaque fois, ce qui entraîne parfois des discontinuités aucune couleur.

## <a name="gradients-on-gradients"></a>Dégradés sur des dégradés

Le `SKShader` classe ne définit aucune propriétés ou méthodes publiques à l’exception de `Dispose`. Le `SKShader` objets créés par ses méthodes statiques sont par conséquent immuables. Même si vous utilisez le même dégradé pour deux objets différents, il est probable que vous souhaitez varier légèrement en fonction du dégradé. Pour ce faire, vous devez créer un nouveau `SKShader` objet.

Le **texte dégradé** page affiche du texte et un arrière-plan de couleur avec des dégradés similaire :

[![Texte de dégradé](linear-gradient-images/GradientText.png "texte dégradé")](linear-gradient-images/GradientText-Large.png#lightbox)

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

Le `Shader` propriété de la `SKPaint` objet est défini en premier pour afficher un dégradé pour couvrir l’arrière-plan. Les points de dégradé sont définis sur les angles supérieur gauche et inférieur droit de la zone de dessin.

Le code définit le `TextSize` propriété de la `SKPaint` afin que le texte est affiché à 90 % de la largeur de la zone de dessin de l’objet. Les limites de texte sont utilisées pour calculer `xText` et `yText` valeurs à passer à la `DrawText` méthode centrer le texte.

Toutefois, le dégradé de pointe pour la deuxième `CreateLinearGradient` appel doit faire référence à l’angle supérieur gauche et inférieur droit du texte par rapport à la zone de dessin lorsqu’elle est affichée. Cela s’effectue par le passage du `textBounds` rectangle par le même `xText` et `yText` valeurs :

```csharp
textBounds.Offset(xText, yText);
```

Les angles supérieur gauche et inférieur droit du rectangle peuvent maintenant être utilisés pour définir le démarrage et les points de terminaison du dégradé.

## <a name="animating-a-gradient"></a>Animer un dégradé

Il existe plusieurs façons d’animer un dégradé. Une approche consiste à animer le début et points de terminaison. Le **Animation dégradé** page déplace les deux points dans un cercle est centré sur la zone de dessin. Le rayon de ce cercle est la moitié de la largeur ou hauteur de la zone de dessin, plus petite étant retenue. Le début et points de terminaison sont à l’opposé de ce cercle et le dégradé passe du blanc au noir avec une `Mirror` mode mosaïque :

[![Animation dégradée](linear-gradient-images/GradientAnimation.png "Animation dégradée")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

Le constructeur crée le `SKCanvasView`. Le `OnAppearing` et `OnDisappearing` méthodes gèrent la logique d’animation :

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

Le `OnTimerTick` méthode calcule un `angle` valeur qui est animée de 0 à 2π de toutes les 3 secondes. 

Voici une façon de calculer les deux points de dégradé. Un `SKPoint` valeur nommée `vector` est calculée pour l’étendre à partir du centre de la zone de dessin à un point sur le rayon du cercle. La direction de ce vecteur est basée sur les valeurs de sinus et le cosinus de l’angle. Les deux points de dégradé opposés sont ensuite calculés: Un point est calculé en soustrayant ce vecteur du point central, et un autre point est calculé en ajoutant le vecteur au point central:

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

Une approche un peu différente nécessite moins de code. Cette approche utilise le [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge de méthode avec une transformation de matrice comme dernier argument. Cette approche est la version dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple :

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

Si la largeur de la zone de dessin est inférieure à la hauteur, les deux points de dégradé sont définis sur (0, 0) et (`info.Width`, 0). La transformation de rotation passé comme dernier argument à `CreateLinearGradient` pivote efficacement ces deux points autour du centre de l’écran.

Notez que si l’angle est 0, il n’existe aucune rotation, et les deux points de dégradé sont les angles supérieur gauche et le coin supérieur droit de la zone de dessin. Ces points ne sont pas les mêmes points de dégradé calculées comme indiqué dans le précédent `CreateLinearGradient` appeler. Mais ces points sont _parallèles_ à la ligne de dégradé horizontale qui coupe le centre de la zone de dessin, et ils sont le résultat dans un dégradé identiques.

**Dégradé arc-en-ciel**

Le **dégradé arc-en-ciel** page Dessine un arc-en-ciel à partir de l’angle supérieur gauche de la zone de dessin vers le coin inférieur droit. Mais ce dégradé arc-en-ciel n’est pas comme un véritable arc-en-ciel. Il est directement plutôt que les courbes, mais il est basé sur huit couleurs TSL (teinte-saturation-luminosité) qui sont déterminés en parcourant les valeurs de teinte comprise entre 0 et 360 :

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Que le code fait partie de la `PaintSurface` gestionnaire comme indiqué ci-dessous. Le gestionnaire commence en créant un chemin d’accès qui définit un polygone six côtés qui s’étend à partir de l’angle supérieur gauche de la zone de dessin à l’angle inférieur droit :

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

Les deux points de dégradé dans `CreateLinearGradient` la méthode sont basés sur deux des points qui définissent ce chemin d’accès: Les deux points sont proches de l’angle supérieur gauche. La première est sur le bord supérieur de la zone de dessin et la seconde sur le bord gauche de la zone de dessin. Voici le résultat :

[![Dégradé arc-en-ciel défectueux](linear-gradient-images/RainbowGradientFaulty.png "dégradé arc-en-ciel défectueux")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Il s’agit d’une image intéressante, mais il n’est pas tout à fait l’intention. Le problème est que lorsque vous créez un dégradé linéaire, les lignes de constante de couleur sont perpendiculaires à la ligne de dégradé. La ligne de dégradé est basée sur les points où la figure touche les côtés supérieure et gauche, et cette ligne est généralement pas perpendiculaire sur les bords de la figure qui étendent vers la bas à droite. Cette approche fonctionnerait uniquement si la zone de dessin ont été carré.

Pour créer un dégradé arc-en-ciel appropriée, la ligne de dégradé doit être perpendiculaire à la périphérie de l’arc-en-ciel. C’est un calcul plus complexe. Un vecteur qui est parallèle au côté long de la figure doit être défini. Le vecteur est pivotée de 90 degrés afin qu’il soit perpendiculaire à ce côté. Il est rallongé puis à la largeur de la figure en multipliant par `rainbowWidth`. Les deux points de dégradé sont calculés sur un point sur le côté de la figure, et qui pointent ainsi que le vecteur. Voici le code qui s’affiche dans le **dégradé arc-en-ciel** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple :

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

[![Dégradé arc-en-ciel](linear-gradient-images/RainbowGradient.png "arc-en-ciel dégradé")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Couleurs de l’infini**

Un dégradé arc-en-ciel est également utilisé dans le **infini couleurs** page. Cette page Dessine un signe de l’infini à l’aide d’un objet de chemin d’accès indiqué dans l’article [ **trois Types de courbes de Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). L’image est ensuite colorée avec un dégradé d’arc-en-ciel animée balaye en permanence sur l’image.

Le constructeur crée le `SKPath` objet décrivant le signe de l’infini. Une fois le chemin d’accès est créée, le constructeur peut également obtenir les limites rectangulaires de chemin d’accès. Elle calcule ensuite une valeur appelée `gradientCycleLength`. Si un dégradé est basé sur les angles supérieur gauche et inférieur droit de la `pathBounds` rectangle, cela `gradientCycleLength` valeur est la largeur horizontale totale du modèle dégradé :

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

Le constructeur crée également le `colors` tableau pour l’arc-en-ciel et le `SKCanvasView` objet.

Remplacements de la `OnAppearing` et `OnDisappearing` méthodes effectuent la surcharge de l’animation. Le `OnTimerTick` méthode anime le `offset` champ comprise entre 0 et `gradientCycleLength` toutes les deux secondes :

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

Enfin, le `PaintSurface` Gestionnaire restitue le signe de l’infini. Étant donné que le chemin d’accès contient les coordonnées de positifs et négatifs entourant un point central de (0, 0), un `Translate` transformation sur le canevas est utilisée pour décaler il au centre. La transformation de traduction est suivie d’un `Scale` transformation qui applique un facteur d’échelle qui rend le signe de l’infini aussi grand que possible tout en restant toujours au sein de 95 % de la largeur et hauteur de la zone de dessin. 

Notez que le `STROKE_WIDTH` (constante) est ajoutée à la largeur et la hauteur du chemin du rectangle englobant. Le chemin d’accès sera être tracé avec une ligne de cette largeur, donc la taille de la taille de rendu de l’infini est augmentée par ce demi-chasse sur les quatre côtés :

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

Examinez les points passés en tant que les deux premiers arguments de `SKShader.CreateLinearGradient`. Ces points sont basés sur le chemin d’accès d’origine rectangle englobant. Le premier point est (&ndash;250, &ndash;100) et le second (250, 100). Interne à SkiaSharp, ces points sont soumis à la transformation en cours de la zone de dessin donc ils s’intègrent correctement avec le signe de l’infini affichée.

Sans le dernier argument de `CreateLinearGradient`, vous voyez un dégradé arc-en-ciel qui s’étend de l’angle supérieur gauche du signe de l’infini le coin inférieur droit. (En fait, le dégradé s’étend à partir du coin supérieur gauche à l’angle inférieur droit du rectangle englobant. Le signe de rendu de l’infini est supérieur du rectangle englobant de moitié le `STROKE_WIDTH` valeur sur tous les côtés. Étant donné que le gradient est rouge au début et à la fin, et le dégradé est créé avec `SKShaderTileMode.Repeat`, la différence n’est pas visible.)

Avec ce dernier argument `CreateLinearGradient`, le modèle de dégradé balaye en permanence sur l’image :

[![Couleurs de l’infini](linear-gradient-images/InfinityColors.png "les couleurs de l’infini")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>La transparence et des dégradés

Les couleurs qui contribuent à un dégradé peuvent incorporer la transparence. Au lieu d’un dégradé fondu d’une couleur à l’autre, le dégradé peut disparaisse en fondu une couleur transparent. 

Vous pouvez utiliser cette technique pour certains effets intéressants. Un des exemples classiques montre un objet graphique avec ses réflexion :

[![Dégradé de réflexion](linear-gradient-images/ReflectionGradient.png "dégradé de réflexion")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Le texte est envers est coloré avec un dégradé est transparent à complètement transparent en bas en haut à 50 %. Ces niveaux de transparence est associés à des valeurs alpha de 0 x 80 et 0.

Le `PaintSurface` gestionnaire dans le **réflexion dégradé** page met à l’échelle la taille du texte à 90 % de la largeur de la zone de dessin. Elle calcule ensuite `xText` et `yText` valeurs pour positionner le texte sera centré horizontalement, mais si vous étiez assis sur une ligne de base correspondant au centre vertical de la page :

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

Ceux `xText` et `yText` les valeurs sont les mêmes valeurs que celui utilisés pour afficher le texte reflété dans le `DrawText` appeler au bas de la `PaintSurface` gestionnaire. Juste avant que le code, cependant, vous verrez un appel à la `Scale` méthode de `SKCanvas`. Cela `Scale` méthode met à l’échelle horizontalement par 1 (qui ne fait rien) mais verticalement par &ndash;1, ce qui est effectivement renverse tout envers. Le centre de rotation est défini sur le point (0, `yText`), où `yText` est au centre vertical de la zone de dessin, calculée à l’origine comme `info.Height` divisé par 2.

N’oubliez pas que Skia utilise le dégradé pour colorer des objets graphiques avant les transformations de la zone de dessin. Une fois que le texte unreflected est dessiné, la `textBounds` rectangle est décalée afin qu’elle corresponde au texte affiché :

```csharp
textBounds.Offset(xText, yText);
```

Le `CreateLinearGradient` appel définit un dégradé du haut de ce rectangle vers le bas. Le gradient est une bleu complètement transparent (`paint.Color.WithAlpha(0)`) à un bleu transparent de 50 % (`paint.Color.WithAlpha(0x80)`). La transformation de la zone de dessin inverse le texte envers, afin que le bleu transparent de 50 % commence à la ligne de base et devienne transparent en haut du texte.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
