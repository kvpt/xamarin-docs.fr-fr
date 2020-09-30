---
title: Dégradés circulaires SkiaSharp
description: En savoir plus sur les différents types de dégradés basés sur des cercles.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ec84ac906ac146f37ba5b161a898582ce483bc95
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556670"
---
# <a name="the-skiasharp-circular-gradients"></a>Dégradés circulaires SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKShader`](xref:SkiaSharp.SKShader) classe définit des méthodes statiques pour créer quatre types différents de dégradés. L’article sur le [**gradient linéaire SkiaSharp**](linear-gradient.md) décrit la [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) méthode. Cet article couvre les trois autres types de dégradés, qui sont tous basés sur des cercles.

La [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) méthode crée un dégradé qui émane du centre d’un cercle :

![Exemple de dégradé radial](circular-gradients-images/RadialGradientSample.png)

La [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) méthode crée un dégradé qui balaye le centre d’un cercle :

![Exemple de dégradé de balayage](circular-gradients-images/SweepGradientSample.png)

Le troisième type de dégradé est assez rare. Elle est appelée dégradé conique à deux points et est définie par la [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) méthode. Le dégradé s’étend d’un cercle à un autre :

![Échantillon de gradient conique](circular-gradients-images/ConicalGradientSample.png)

Si les deux cercles sont de tailles différentes, le dégradé prend la forme d’un cône.

Cet article explore ces dégradés plus en détail.

## <a name="the-radial-gradient"></a>Dégradé radial

La [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) méthode a la syntaxe suivante :

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Une [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge comprend également un paramètre de matrice de transformation.

Les deux premiers arguments spécifient le centre d’un cercle et un rayon. Le dégradé commence à ce centre et s’étend vers l’extérieur pour les `radius` pixels. Ce qui se passe au-delà `radius` de dépend de l' [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) argument. Le `colors` paramètre est un tableau d’au moins deux couleurs (comme dans les méthodes de dégradé linéaire) et `colorPos` est un tableau d’entiers compris entre 0 et 1. Ces entiers indiquent les positions relatives des couleurs sur cette `radius` ligne. Vous pouvez définir cet argument sur `null` pour espacer uniformément les couleurs.

Si vous utilisez `CreateRadialGradient` pour remplir un cercle, vous pouvez définir le centre du dégradé au centre du cercle et le rayon du dégradé sur le rayon du cercle. Dans ce cas, l' `SKShaderTileMode` argument n’a aucun effet sur le rendu du dégradé. Toutefois, si la zone remplie par le dégradé est plus grande que le cercle défini par le dégradé, l' `SKShaderTileMode` argument a un effet profond sur ce qui se passe en dehors du cercle.

L’effet de `SKShaderMode` est illustré dans la page **dégradé radial** de l’exemple [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Le fichier XAML de cette page instancie un `Picker` qui vous permet de sélectionner l’un des trois membres de l' `SKShaderTileMode` énumération :

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

Le fichier code-behind colore l’intégralité du canevas avec un dégradé radial. Le centre du dégradé est défini au centre du canevas et le rayon est défini sur 100 pixels. Le dégradé se compose uniquement de deux couleurs, le noir et le blanc :

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

Ce code crée un dégradé noir au centre, en dégradant progressivement sur 100 pixels du centre. Ce qui se passe au-delà de ce rayon dépend de l' `SKShaderTileMode` argument :

[![Dégradé radial](circular-gradients-images/RadialGradient.png "Dégradé radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Dans les trois cas, le dégradé remplit le canevas. Sur l’écran iOS situé à gauche, le dégradé qui se trouve au-delà du rayon se poursuit avec la dernière couleur, qui est blanche. C’est le résultat de `SKShaderTileMode.Clamp` . L’écran Android montre l’effet de `SKShaderTileMode.Repeat` : à 100 pixels du centre, le dégradé recommence avec la première couleur, qui est le noir. Le dégradé se répète tous les 100 pixels du rayon. 

L’écran de plateforme Windows universelle à droite montre comment `SKShaderTileMode.Mirror` les dégradés sont à l’autre sens. Le premier dégradé est le noir au centre et le blanc à un rayon de 100 pixels. Le suivant est blanc du rayon de 100 pixels au noir au niveau d’un rayon de 200 pixels, et le dégradé suivant est à nouveau inversé.

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

Supposons que la valeur minimale de la largeur et de la hauteur du canevas est 1000, ce qui signifie que la `rainbowWidth` valeur est 250. Les `outerRadius` `innerRadius` valeurs et sont définies sur 1000 et 750, respectivement. Ces valeurs sont utilisées pour calculer le `positions` tableau ; les huit valeurs sont comprises entre 0,75 f et 1. La `radius` valeur est utilisée pour le contour du cercle. La valeur de 875 signifie que la largeur du trait de 250 pixels s’étend entre le rayon de 750 pixels et le rayon de 1000 pixels :

[![Dégradé arc arc-en-ciel](circular-gradients-images/RainbowArcGradient.png "Dégradé arc arc-en-ciel")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Si vous avez rempli le canevas entier avec ce dégradé, vous verrez qu’il est rouge dans le rayon interne. Cela est dû au fait que le `positions` tableau ne commence pas par 0. La première couleur est utilisée pour les décalages de 0 à la première valeur de tableau. Le dégradé est également rouge au-delà du rayon externe. C’est le résultat du `Clamp` mode Tile. Étant donné que le dégradé est utilisé pour le contour d’une ligne épaisse, ces zones rouges ne sont pas visibles.

## <a name="radial-gradients-for-masking"></a>Dégradés radiaux pour le masquage

Comme les dégradés linéaires, les dégradés radiaux peuvent incorporer des couleurs transparentes ou partiellement transparentes. Cette fonctionnalité est utile pour un processus appelé _masquage_, qui masque une partie d’une image pour accentuer une autre partie de l’image.

La page **masque de dégradé radial** montre un exemple. Le programme charge l’une des bitmaps de ressource. Les `CENTER` `RADIUS` champs et ont été déterminés à partir d’un examen de la bitmap et font référence à une zone qui doit être mise en surbrillance. Le `PaintSurface` Gestionnaire commence par calculer un rectangle pour afficher la bitmap, puis l’affiche dans ce rectangle :

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

Après avoir dessiné la bitmap, un code simple est converti en `CENTER` et `RADIUS` en `center` et `radius` , qui fait référence à la zone en surbrillance dans la bitmap qui a été mise à l’échelle et décalée pour l’affichage. Ces valeurs sont utilisées pour créer un dégradé radial avec ce centre et ce rayon. Les deux couleurs commencent en transparence au centre et pour les 60 premiers% du rayon. Le dégradé disparaît alors en blanc :

[![Masque de dégradé radial](circular-gradients-images/RadialGradientMask.png "Masque de dégradé radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Cette approche n’est pas la meilleure façon de masquer une image bitmap. Le problème est que le masque a principalement une couleur blanche, qui a été choisie pour correspondre à l’arrière-plan de la zone de dessin. Si l’arrière-plan est une autre couleur &mdash; ou peut-être un dégradé lui-même &mdash; ne correspond pas. Une meilleure approche du masquage est illustrée dans l’article [SkiaSharp porter-Duff Blend modes](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Dégradés radiaux pour les surbrillances spéculaires

Quand une lumière est entourée d’une surface arrondie, elle reflète la lumière dans de nombreuses directions, mais une partie de la lumière rebondit directement dans l’oeil du spectateur. Cela crée souvent l’apparence d’une zone blanche floue sur la surface appelée _mise en surbrillance spéculaire_.

Dans les graphiques en trois dimensions, les surbrillances spéculaires résultent souvent des algorithmes utilisés pour déterminer les chemins de lumière et l’ombrage. Dans les graphiques à deux dimensions, des surbrillances spéculaires sont parfois ajoutées pour suggérer l’apparence d’une surface 3D. Une surbrillance spéculaire peut transformer un cercle rouge en une boule rouge ronde.

La page de **surbrillance spéculaire radial** utilise un dégradé radial pour effectuer précisément cette opération. `PaintSurface`Gestionnaire en calculant un rayon pour le cercle, et deux `SKPoint` valeurs &mdash; a `center` et un `offCenter` qui se trouve à mi-chemin entre le centre et le bord supérieur gauche du cercle :

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

L' `CreateRadialGradient` appel crée un dégradé qui commence à ce `offCenter` point avec un blanc et se termine par le rouge à une distance égale à la moitié du rayon. Voici à quoi il ressemble :

[![Surbrillance spéculaire radial](circular-gradients-images/RadialSpecularHighlight.png "Surbrillance spéculaire radial")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Si vous examinez attentivement ce dégradé, vous pouvez décider qu’il est défectueux. Le dégradé est centré autour d’un point particulier et vous souhaiterez peut-être un peu moins symétrique pour refléter la surface arrondie. Dans ce cas, vous pouvez préférer la surbrillance spéculaire présentée ci-dessous dans la section [**dégradés coniques pour**](#conical-gradients-for-specular-highlights)les surbrillances spéculaires.

## <a name="the-sweep-gradient"></a>Dégradé de balayage

La [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) méthode a la syntaxe la plus simple de toutes les méthodes de création de dégradé :

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Il s’agit simplement d’un centre, d’un tableau de couleurs et de positions de couleur. Le dégradé commence à droite du point central et balaye 360 degrés dans le sens des aiguilles d’une montre autour du centre. Notez qu’il n’y a aucun `SKShaderTileMode` paramètre.

Une [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) surcharge avec un paramètre de transformation de matrice est également disponible. Vous pouvez appliquer une transformation de rotation au dégradé pour modifier le point de départ. Vous pouvez également appliquer une transformation d’échelle pour modifier la direction dans le sens inverse des aiguilles d’une montre.

La page **dégradé de balayage** utilise un dégradé de balayage pour colorier un cercle avec une largeur de trait de 50 pixels :

[![Dégradé de balayage](circular-gradients-images/SweepGradient.png "Dégradé de balayage")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La `SweepGradientPage` classe définit un tableau de huit couleurs avec différentes valeurs de teinte. Notez que le tableau commence et se termine par le rouge (une valeur de teinte de 0 ou 360), qui apparaît à l’extrême droite dans les captures d’écran :

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

Le programme implémente également un `TapGestureRecognizer` qui active du code à la fin du `PaintSurface` Gestionnaire. Ce code utilise le même dégradé pour remplir le canevas :

[![Dégradé de balayage plein](circular-gradients-images/SweepGradientFull.png "Dégradé de balayage plein")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Ces captures d’écran montrent que le dégradé remplit la zone qui y est colorée. Si le dégradé ne commence pas et se termine par la même couleur, il y aura une discontinuité à droite du point central.

## <a name="the-two-point-conical-gradient"></a>Dégradé conique à deux points

La [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) méthode a la syntaxe suivante :

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Les paramètres commencent par des points centraux et des rayons pour deux cercles, désignés sous le terme de cercle de _début_ et de _fin_ . Les trois paramètres restants sont les mêmes que pour `CreateLinearGradient` et `CreateRadialGradient` . Une [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge comprend une transformation de matrice.

Le dégradé commence au cercle de début et se termine à la fin du cercle. Le `SKShaderTileMode` paramètre régit ce qui se passe au-delà des deux cercles. Le dégradé conique à deux points est le seul dégradé qui ne remplit pas entièrement une zone. Si les deux cercles ont le même rayon, le dégradé est limité à un rectangle dont la largeur est identique au diamètre des cercles. Si les deux cercles ont des rayons différents, le dégradé forme un cône.

Il est probable que vous souhaiteriez faire des essais avec le dégradé conique à deux points, de sorte que la page de **dégradé conique** dérive de `InteractivePage` pour permettre le déplacement de deux points tactiles pour les deux rayons de cercle :

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

Le fichier code-behind définit les deux `TouchPoint` objets avec un rayon fixe de 50 et 100 :

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

Le `colors` tableau est rouge, vert et bleu. Le code vers le bas du `PaintSurface` Gestionnaire dessine les deux points tactiles en tant que cercles noirs afin qu’ils n’obstruent pas le dégradé.

Notez que l' `DrawRect` appel utilise le dégradé pour colorer la totalité du canevas. Dans le cas général, toutefois, une grande partie de la zone de dessin reste non colorée par le dégradé. Voici le programme qui présente trois configurations possibles :

[![Dégradé conique](circular-gradients-images/ConicalGradient.png "Dégradé conique")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

L’écran iOS à gauche montre l’effet du `SKShaderTileMode` paramètre de `Clamp` . Le dégradé commence par le rouge à l’intérieur du bord du petit cercle qui est le côté le plus proche du second cercle. La `Clamp` valeur indique également que le rouge continue jusqu’au point du cône. Le dégradé se termine par le bleu au bord extérieur du plus grand cercle qui est le plus proche du premier cercle, mais continue en bleu dans ce cercle et au-delà.

L’écran Android est similaire, mais avec un `SKShaderTileMode` de `Repeat` . Maintenant, il est plus clair que le dégradé commence à l’intérieur du premier cercle et se termine en dehors du deuxième cercle. Le `Repeat` paramètre fait en sorte que le dégradé se répète à nouveau avec le rouge à l’intérieur du cercle de plus grande taille.

L’écran UWP montre ce qui se produit lorsque le petit cercle est déplacé entièrement à l’intérieur du plus grand cercle. Le dégradé s’arrête sur un cône et remplit à la place la zone entière. L’effet est similaire au dégradé radial, mais il est asymétrique si le petit cercle n’est pas exactement centré dans le plus grand cercle.

Vous risquez de douter l’utilité pratique du dégradé lorsqu’un cercle est imbriqué dans un autre, mais il est idéal pour une mise en surbrillance spéculaire.

## <a name="conical-gradients-for-specular-highlights"></a>Dégradés coniques pour les surbrillances spéculaires

Plus haut dans cet article, vous avez appris à utiliser un dégradé radial pour créer une mise en surbrillance spéculaire. Vous pouvez également utiliser le dégradé conique à deux points à cet effet, et vous préférerez peut-être en quoi il ressemble :

[![Surbrillance spéculaire](circular-gradients-images/ConicalSpecularHighlight.png "Surbrillance spéculaire")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

L’apparence asymétrique suggère mieux la surface arrondie de l’objet. 

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

Les deux cercles ont des centres de `offCenter` et `center` . Le cercle centré sur `center` est associé à un rayon qui englobe la balle entière, mais le cercle centré sur `offCenter` a un rayon d’un seul pixel. Le dégradé commence effectivement à ce point et se termine à la périphérie de la balle.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)