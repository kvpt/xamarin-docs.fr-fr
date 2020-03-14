---
title: Les dégradés circulaires SkiaSharp
description: En savoir plus sur les différents types de dégradés basés sur les cercles.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304176"
---
# <a name="the-skiasharp-circular-gradients"></a>Les dégradés circulaires SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La classe [`SKShader`](xref:SkiaSharp.SKShader) définit des méthodes statiques pour créer quatre types différents de dégradés. L’article [**dégradé linéaire SkiaSharp**](linear-gradient.md) décrit la méthode [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) . Cet article couvre les trois autres types de dégradés, qui sont basées sur les cercles.

La méthode [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) crée un dégradé qui émane du centre d’un cercle :

![Exemple de dégradé radial](circular-gradients-images/RadialGradientSample.png)

La méthode [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) crée un dégradé qui balaye le centre d’un cercle :

![Exemple de dégradé de balayage](circular-gradients-images/SweepGradientSample.png)

Le troisième type de dégradé est assez rare. Elle est appelée dégradé conique à deux points et est définie par la méthode [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) . Le dégradé s’étend à partir d’un cercle à un autre :

![Exemple de dégradé conique](circular-gradients-images/ConicalGradientSample.png)

Si les deux cercles sont de tailles différentes, le dégradé prend la forme d’un cône.

Cet article explore ces dégradés plus en détail.

## <a name="the-radial-gradient"></a>Dégradé radial

La méthode [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) a la syntaxe suivante :

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Une surcharge de [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) comprend également un paramètre de matrice de transformation.

Les deux premiers arguments spécifient le centre d’un cercle et le rayon. Le dégradé commence à ce centre et s’étend vers l’extérieur pour `radius` pixels. Ce qui se passe au-delà de `radius` dépend de l’argument [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) . Le paramètre `colors` est un tableau d’au moins deux couleurs (comme dans les méthodes de dégradé linéaire) et `colorPos` est un tableau d’entiers compris entre 0 et 1. Ces entiers indiquent les positions relatives des couleurs sur cette ligne de `radius`. Vous pouvez définir cet argument pour `null` pour espacer uniformément les couleurs.

Si vous utilisez `CreateRadialGradient` pour remplir un cercle, vous pouvez définir le centre du dégradé au centre du cercle et le rayon du dégradé sur le rayon du cercle. Dans ce cas, l’argument `SKShaderTileMode` n’a aucun effet sur le rendu du dégradé. Toutefois, si la zone remplie par le dégradé est plus grande que le cercle défini par le dégradé, l’argument `SKShaderTileMode` a un effet profond sur ce qui se passe en dehors du cercle.

L’effet de `SKShaderMode` est illustré dans la page **dégradé radial** de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Le fichier XAML de cette page instancie une `Picker` qui vous permet de sélectionner l’un des trois membres de l’énumération `SKShaderTileMode` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

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
</ContentPage>
```

Le fichier code-behind couleurs la totalité du canevas avec un dégradé radial. Le centre du dégradé est défini sur le centre de la zone de dessin, et le rayon est défini sur 100 pixels. Le dégradé se compose de deux couleurs, noir et blancs :

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
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

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Ce code crée un dégradé avec du noir au centre, progressivement sur blanc 100 pixels à partir du centre. Ce qui se passe au-delà de ce rayon dépend de l’argument `SKShaderTileMode` :

[![Dégradé radial](circular-gradients-images/RadialGradient.png "Dégradé radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Dans les trois cas, le dégradé remplit le canevas. Dans l’écran iOS à gauche, le dégradé au-delà de rayon se poursuit avec la dernière couleur, qui est le blanche. C’est le résultat de `SKShaderTileMode.Clamp`. L’écran Android affiche l’effet de `SKShaderTileMode.Repeat`: à 100 pixels du centre, le dégradé recommence avec la première couleur, qui est le noir. Le dégradé est répété à chaque 100 pixels du rayon de. 

L’écran plateforme Windows universelle à droite montre comment `SKShaderTileMode.Mirror` amène les dégradés à des directions alternées. Le dégradé de la première est du noir au centre sur blanc dans un rayon de 100 pixels. L’autre est blanche dans le rayon de 100 pixels sur noir à un rayon de 200 pixels, et le dégradé suivant est annulée à nouveau.

Vous pouvez utiliser plus de deux couleurs dans un dégradé radial. L’exemple **arc de dégradé arc** -en-ciel crée un tableau de huit couleurs correspondant aux couleurs de l’arc-en-ciel et se terminant par le rouge, ainsi qu’un tableau de huit valeurs de position :

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Supposons que la valeur minimale de la largeur et de la hauteur du canevas est 1000, ce qui signifie que la valeur `rainbowWidth` est 250. Les valeurs `outerRadius` et `innerRadius` sont respectivement définies sur 1000 et 750. Ces valeurs sont utilisées pour calculer le tableau `positions` ; les huit valeurs sont comprises entre 0,75 f et 1. La valeur `radius` est utilisée pour le contour du cercle. La valeur de 875 signifie que la largeur du trait de 250 pixels s’étend entre le rayon de 750 pixels et le rayon de 1000 pixels :

[![Dégradé arc arc-en-ciel](circular-gradients-images/RainbowArcGradient.png "Dégradé arc arc-en-ciel")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Si vous avez rempli le canevas entier avec ce dégradé, vous voyez qu’il est rouge dans le rayon interne. Cela est dû au fait que le tableau `positions` ne commence pas par 0. La première couleur est utilisée pour les décalages de 0 à la première valeur de tableau. Le dégradé est également rouge au-delà du rayon externe. C’est le résultat de l' `Clamp` mode vignette. Étant donné que le dégradé est utilisé pour le contour d’une ligne épaisse, ces zones rouges ne sont pas visibles.

## <a name="radial-gradients-for-masking"></a>Dégradés radiaux pour le masquage

Comme des dégradés linéaires, des dégradés radiaux peuvent incorporer des couleurs transparents ou partiellement transparents. Cette fonctionnalité est utile pour un processus appelé _masquage_, qui masque une partie d’une image pour accentuer une autre partie de l’image.

La page **masque de dégradé radial** montre un exemple. Le programme charge les bitmaps de ressources. Les champs `CENTER` et `RADIUS` ont été déterminés à partir d’un examen de la bitmap et font référence à une zone qui doit être mise en surbrillance. Le gestionnaire de `PaintSurface` commence par calculer un rectangle pour afficher la bitmap, puis l’affiche dans ce rectangle :

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Après avoir dessiné la bitmap, un code simple convertit `CENTER` et `RADIUS` en `center` et `radius`, qui font référence à la zone en surbrillance dans la bitmap qui a été mise à l’échelle et décalée pour l’affichage. Ces valeurs sont utilisées pour créer un dégradé radial avec ce centre et le rayon. Les deux couleurs commencent à transparent dans le centre et pour la première 60 % du rayon. Le dégradé fondu puis au blanc :

[![Masque de dégradé radial](circular-gradients-images/RadialGradientMask.png "Masque de dégradé radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Cette approche n’est pas la meilleure façon de masquer une image bitmap. Le problème est que le masque a principalement une couleur de blanc, ce qui a été choisie pour correspondre à l’arrière-plan de la zone de dessin. Si l’arrière-plan est une autre couleur &mdash; ou peut-être un dégradé lui-même &mdash; il ne correspond pas. Une meilleure approche du masquage est illustrée dans l’article [SkiaSharp porter-Duff Blend modes](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Dégradés radiaux pour les surbrillances spéculaires

Quand une lumière frappe une surface arrondie, il reflète la lumière dans plusieurs directions, mais certains de la lumière rebondit directement dans les yeux du spectateur. Cela crée souvent l’apparence d’une zone blanche floue sur la surface appelée _mise en surbrillance spéculaire_.

Dans les graphiques en trois dimensions, les surbrillances spéculaires résultent généralement les algorithmes utilisés pour déterminer les chemins d’accès claires et de trame. Dans les graphiques à deux dimensions, les surbrillances spéculaires sont parfois ajoutées pour suggérer l’apparence d’une surface 3D. Une mise en surbrillance spéculaire peut transformer un cercle rouge plate en une bille rouge round.

La page de **surbrillance spéculaire radial** utilise un dégradé radial pour effectuer précisément cette opération. Le gestionnaire de `PaintSurface` est responsable en calculant un rayon pour le cercle et deux valeurs `SKPoint` &mdash; un `center` et un `offCenter` qui se trouve à mi-chemin entre le centre et le bord supérieur gauche du cercle :

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

L’appel de `CreateRadialGradient` crée un dégradé qui commence à ce point de `offCenter` avec le blanc et se termine par le rouge à une distance de la moitié du rayon. Voici à quoi elle ressemble :

[![Surbrillance spéculaire radial](circular-gradients-images/RadialSpecularHighlight.png "Surbrillance spéculaire radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Si vous regardez de près ce dégradé, vous pouvez décider qu’elle est incorrecte. Le dégradé est centré autour d’un point particulier, et vous pouvez être amené à il s’agissait d’un peu moins symétrique afin de refléter la surface est arrondie. Dans ce cas, vous pouvez préférer la surbrillance spéculaire présentée ci-dessous dans la section [**dégradés coniques pour**](#conical-gradients-for-specular-highlights)les surbrillances spéculaires.

## <a name="the-sweep-gradient"></a>Le dégradé de balayage

La méthode [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) a la syntaxe la plus simple pour toutes les méthodes de création de dégradé :

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Il est simplement un centre, un tableau de couleurs et les positions de couleur. Le dégradé commence à droite du point central et balaye 360 degrés dans le sens horaire autour du centre. Notez qu’il n’y a aucun paramètre `SKShaderTileMode`.

Une surcharge de [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) avec un paramètre de transformation de matrice est également disponible. Vous pouvez appliquer une transformation de rotation du dégradé pour modifier le point de départ. Vous pouvez également appliquer une transformation d’échelle pour modifier la direction à partir de dans le sens horaire pour le sens antihoraire.

La page **dégradé de balayage** utilise un dégradé de balayage pour colorier un cercle avec une largeur de trait de 50 pixels :

[![Dégradé de balayage](circular-gradients-images/SweepGradient.png "Dégradé de balayage")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La classe `SweepGradientPage` définit un tableau de huit couleurs avec différentes valeurs de teinte. Notez que le tableau commence et se termine par rouge (une valeur de teinte de 0 ou 360), qui s’affiche à l’extrême droite dans les captures d’écran :

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

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
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Le programme implémente également un `TapGestureRecognizer` qui active du code à la fin du gestionnaire de `PaintSurface`. Ce code utilise le même dégradé pour remplir la zone de dessin :

[![Dégradé de balayage plein](circular-gradients-images/SweepGradientFull.png "Dégradé de balayage plein")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Ces captures d’écran montrent que les dégradés toute zone est coloré par celui-ci. Si le dégradé de ne pas commencer et se terminer par la même couleur, il y aura une discontinuité à droite du point central.

## <a name="the-two-point-conical-gradient"></a>Deux points conique dégradé

La méthode [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) a la syntaxe suivante :

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Les paramètres commencent par des points centraux et des rayons pour deux cercles, désignés sous le terme de cercle de _début_ et de _fin_ . Les trois paramètres restants sont les mêmes que pour `CreateLinearGradient` et `CreateRadialGradient`. Une surcharge de [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) comprend une transformation de matrice.

Le dégradé commence au cercle de début et se termine au cercle de fin. Le paramètre `SKShaderTileMode` régit ce qui se passe au-delà des deux cercles. Le gradient de conique de deux points est le seul dégradé qui ne remplit pas entièrement une zone. Si les deux cercles ont le même rayon, le dégradé est limité à un rectangle dont la largeur est le même que le diamètre des cercles. Si les deux cercles ont des rayons différents, le dégradé constitue un cône.

Il est probable que vous souhaiteriez faire des essais avec le dégradé conique à deux points, de sorte que la page de **dégradé conique** dérive de `InteractivePage` pour permettre le déplacement de deux points tactiles pour les rayons à deux cercles :

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
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

Le fichier code-behind définit les deux objets `TouchPoint` avec un rayon fixe de 50 et 100 :

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

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
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

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
        }
    }
}
```

Le tableau `colors` est rouge, vert et bleu. Le code vers le bas du gestionnaire de `PaintSurface` dessine les deux points tactiles en tant que cercles noirs afin qu’ils n’obstruent pas le dégradé.

Notez que `DrawRect` appel utilise le dégradé pour colorer la totalité du canevas. Toutefois, en règle générale, une grande partie de la zone de dessin reste non colorée en dégradé. Voici le programme montrant les trois configurations possibles :

[![Dégradé conique](circular-gradients-images/ConicalGradient.png "Dégradé conique")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

L’écran iOS à gauche montre l’effet du paramètre `SKShaderTileMode` de `Clamp`. Le dégradé commence par rouge du bord intérieur du cercle plus petit est opposé le côté le plus proche du cercle deuxième. La valeur de `Clamp` indique également que le rouge continue jusqu’au point du cône. Le dégradé se termine par bleu au bord externe du cercle plus grand qui est le premier cercle le plus proche, mais continue avec bleu au sein de ce cercle et au-delà.

L’écran Android est similaire, mais avec un `SKShaderTileMode` de `Repeat`. Maintenant, il est plus clair que le dégradé commence à l’intérieur du cercle premier et se termine en dehors du cercle deuxième. Le paramètre `Repeat` oblige le dégradé à répéter à nouveau avec le rouge à l’intérieur du cercle plus grand.

L’écran UWP montre ce qui se passe lorsque le plus petit cercle est déplacé entièrement à l’intérieur du cercle plus volumineux. Le dégradé cesse d’être un cône et au lieu de cela remplit la zone entière. L’effet est similaire à dégradé radial, mais il est asymétrique si le plus petit cercle est centré pas exactement dans le cercle plus volumineux.

Vous pouvez doute l’utilité pratique du dégradé quand un cercle est imbriqué dans une autre, mais elle est idéale pour une mise en surbrillance spéculaire.

## <a name="conical-gradients-for-specular-highlights"></a>Dégradés coniques pour les surbrillances spéculaires

Plus haut dans cet article, vous avez vu comment utiliser un dégradé radial pour créer une mise en surbrillance spéculaire. Vous pouvez également utiliser le dégradé conique deux points à cet effet, et vous préférerez peut-être à quoi il ressemble :

[![Surbrillance spéculaire](circular-gradients-images/ConicalSpecularHighlight.png "Surbrillance spéculaire")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

L’apparence asymétrique suggère mieux l’arrondi surface de l’objet. 

Le code de dessin dans la page de **surbrillance spéculaire conique** est le même que la page de **surbrillance spéculaire radial** , à l’exception du nuanceur :

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Les deux cercles ont des centres de `offCenter` et `center`. Le cercle centré sur `center` est associé à un rayon qui englobe la balle entière, mais le cercle centré sur `offCenter` a un rayon d’un seul pixel. Le dégradé commence à ce stade et se termine à la périphérie de la boule efficacement.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
