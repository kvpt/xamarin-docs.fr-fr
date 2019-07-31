---
title: Les dégradés circulaires SkiaSharp
description: En savoir plus sur les différents types de dégradés basés sur les cercles.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: df45908af9e2d13dcaeff732e8e8b1b49c523934
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647659"
---
# <a name="the-skiasharp-circular-gradients"></a>Les dégradés circulaires SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le [ `SKShader` ](xref:SkiaSharp.SKShader) classe définit des méthodes statiques pour créer quatre types de dégradés. Le [ **dégradé linéaire SkiaSharp** ](linear-gradient.md) article aborde le [ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) (méthode). Cet article couvre les trois autres types de dégradés, qui sont basées sur les cercles.

Le [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*) méthode crée un dégradé qui émane du centre d’un cercle :

![Exemple de dégradé radial](circular-gradients-images/RadialGradientSample.png)

Le [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*) méthode crée un dégradé balaye autour du centre d’un cercle :

![Exemple de dégradé de balayage](circular-gradients-images/SweepGradientSample.png)

Le troisième type de dégradé est assez rare. Il est appelé le dégradé conique deux points et est définie par le [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) (méthode). Le dégradé s’étend à partir d’un cercle à un autre :

![Exemple de dégradé conique](circular-gradients-images/ConicalGradientSample.png)

Si les deux cercles sont de tailles différentes, le dégradé prend la forme d’un cône.

Cet article explore ces dégradés plus en détail.

## <a name="the-radial-gradient"></a>Dégradé radial

Le [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) méthode présente la syntaxe suivante :

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Un [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge inclut également un paramètre de matrice de transformation.

Les deux premiers arguments spécifient le centre d’un cercle et le rayon. Le dégradé commence à ce centre et s’étend vers l’extérieur pour `radius` pixels. Que se passe-t-il au-delà `radius` varie selon le [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) argument. Le `colors` paramètre est un tableau de deux ou plusieurs couleurs (comme dans les méthodes de dégradé linéaires) et `colorPos` est un tableau d’entiers dans la plage de 0 à 1. Ces entiers indiquent la position relative des couleurs le long qui `radius` ligne. Vous pouvez définir cet argument sur `null` pour tout aussi espacer les couleurs.

Si vous utilisez `CreateRadialGradient` pour remplir un cercle, vous pouvez définir le centre du dégradé pour le centre du cercle et le rayon du dégradé pour le rayon du cercle. Dans ce cas, le `SKShaderTileMode` argument n’a aucun effet sur le rendu du dégradé. Mais si la zone remplie par le dégradé est supérieure à cercle défini par le dégradé, puis le `SKShaderTileMode` argument a un impact important sur ce qui se passe en dehors du cercle.

L’effet de `SKShaderMode` est illustrée dans le **dégradé Radial** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple. Le fichier XAML pour cette page instancie un `Picker` qui vous permet de sélectionner l’un des trois membres de le `SKShaderTileMode` énumération :

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

Ce code crée un dégradé avec du noir au centre, progressivement sur blanc 100 pixels à partir du centre. Que se passe-t-il au-delà de ce rayon varie selon le `SKShaderTileMode` argument :

[![Dégradé radial](circular-gradients-images/RadialGradient.png "dégradé Radial")](circular-gradients-images/RadialGradient-Large.png#lightbox)

Dans les trois cas, le dégradé remplit le canevas. Dans l’écran iOS à gauche, le dégradé au-delà de rayon se poursuit avec la dernière couleur, qui est le blanche. C’est le résultat de `SKShaderTileMode.Clamp`. L’écran Android affiche l’effet de `SKShaderTileMode.Repeat`: À 100 pixels du centre, le dégradé recommence avec la première couleur, qui est le noir. Le dégradé est répété à chaque 100 pixels du rayon de. 

L’écran de la plateforme Windows universelle à droite montre comment `SKShaderTileMode.Mirror` provoque des dégradés de l’autre sens. Le dégradé de la première est du noir au centre sur blanc dans un rayon de 100 pixels. L’autre est blanche dans le rayon de 100 pixels sur noir à un rayon de 200 pixels, et le dégradé suivant est annulée à nouveau.

Vous pouvez utiliser plus de deux couleurs dans un dégradé radial. Le **arc-en-ciel Arc dégradé** exemple crée un tableau de huit couleurs correspondant aux couleurs de l’arc-en-ciel et se terminant par red et également un tableau de huit valeurs de position :

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

Supposons que la valeur minimale de la largeur et hauteur de la zone de dessin est 1 000, ce qui signifie que le `rainbowWidth` valeur est 250. Le `outerRadius` et `innerRadius` ont la valeur 1000 et 750, respectivement. Ces valeurs sont utilisées pour calculer le `positions` tableau ; la plage de huit valeurs de 0.75f à 1. Le `radius` valeur est utilisée pour le contour du cercle. La valeur de 875 signifie que la largeur du trait de 250 pixels s’étend entre le rayon de 750 pixels et le rayon de 1000 pixels :

[![Arc-en-ciel Arc dégradé](circular-gradients-images/RainbowArcGradient.png "arc-en-ciel Arc dégradé")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Si vous avez rempli le canevas entier avec ce dégradé, vous voyez qu’il est rouge dans le rayon interne. Il s’agit, car le `positions` tableau ne commence pas par 0. La première couleur est utilisée pour les décalages de 0 à la première valeur de tableau. Le dégradé est également rouge au-delà du rayon externe. C’est le résultat de la `Clamp` mode mosaïque. Étant donné que le dégradé est utilisé pour le contour d’une ligne épaisse, ces zones rouges ne sont pas visibles.

## <a name="radial-gradients-for-masking"></a>Dégradés radiaux pour le masquage

Comme des dégradés linéaires, des dégradés radiaux peuvent incorporer des couleurs transparents ou partiellement transparents. Cette fonctionnalité est utile pour un processus appelé _masquage_, qui masque la partie d’une image d’accentuer une autre partie de l’image.

Le **masque dégradé Radial** page montre un exemple. Le programme charge les bitmaps de ressources. Le `CENTER` et `RADIUS` champs ont été déterminées à partir d’un examen de l’image bitmap et référencer une zone qui doit être mis en surbrillance. Le `PaintSurface` gestionnaire commence en calculant un rectangle pour afficher l’image bitmap et puis l’affiche dans ce rectangle :

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

Après le dessin de l’image bitmap, convertit un code simple `CENTER` et `RADIUS` à `center` et `radius`, qui font référence à la zone en surbrillance dans la bitmap qui a été mis à l’échelle et décalée pour l’affichage. Ces valeurs sont utilisées pour créer un dégradé radial avec ce centre et le rayon. Les deux couleurs commencent à transparent dans le centre et pour la première 60 % du rayon. Le dégradé fondu puis au blanc :

[![Masque de dégradé radial](circular-gradients-images/RadialGradientMask.png "masque dégradé Radial")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Cette approche n’est pas la meilleure façon de masquer une image bitmap. Le problème est que le masque a principalement une couleur de blanc, ce qui a été choisie pour correspondre à l’arrière-plan de la zone de dessin. Si l’arrière-plan est une autre couleur &mdash; ou peut-être un dégradé lui-même &mdash; ne correspondent pas. Une meilleure approche de masquage est indiquée dans l’article [SkiaSharp Porter-Duff les modes de fusion](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Dégradés radiaux pour les surbrillances spéculaires

Quand une lumière frappe une surface arrondie, il reflète la lumière dans plusieurs directions, mais certains de la lumière rebondit directement dans les yeux du spectateur. Cela crée souvent l’apparence d’une zone blanche floue sur l’aire d’appelé un _surbrillance spéculaire_.

Dans les graphiques en trois dimensions, les surbrillances spéculaires résultent généralement les algorithmes utilisés pour déterminer les chemins d’accès claires et de trame. Dans les graphiques à deux dimensions, les surbrillances spéculaires sont parfois ajoutées pour suggérer l’apparence d’une surface 3D. Une mise en surbrillance spéculaire peut transformer un cercle rouge plate en une bille rouge round.

Le **surbrillance spéculaire radiale** page utilise un dégradé radial pour le faire avec précision. Le `PaintSurface` êtres gestionnaire en calculant un rayon pour le cercle et les deux `SKPoint` valeurs &mdash; un `center` et un `offCenter` qui se trouve à mi-chemin entre le centre et le bord supérieur gauche du cercle :

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

Le `CreateRadialGradient` appel crée un dégradé qui commence à ce `offCenter` point avec blanc et se termine par rouge à une distance de moitié le rayon. Voici à quoi elle ressemble :

[![Mise en surbrillance spéculaire radiale](circular-gradients-images/RadialSpecularHighlight.png "surbrillance spéculaire radiale")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Si vous regardez de près ce dégradé, vous pouvez décider qu’elle est incorrecte. Le dégradé est centré autour d’un point particulier, et vous pouvez être amené à il s’agissait d’un peu moins symétrique afin de refléter la surface est arrondie. Dans ce cas, vous pouvez préférer la surbrillance spéculaire ci-dessous dans la section [ **coniques dégradés pour les surbrillances spéculaires**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>Le dégradé de balayage

Le [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) méthode présente la syntaxe la plus simple de toutes les méthodes de création de dégradé :

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Il est simplement un centre, un tableau de couleurs et les positions de couleur. Le dégradé commence à droite du point central et balaye 360 degrés dans le sens horaire autour du centre. Notez qu’il y a aucune `SKShaderTileMode` paramètre.

Un [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) surcharge avec un paramètre de transformation de matrice est également disponible. Vous pouvez appliquer une transformation de rotation du dégradé pour modifier le point de départ. Vous pouvez également appliquer une transformation d’échelle pour modifier la direction à partir de dans le sens horaire pour le sens antihoraire.

Le **balayage dégradé** page utilise un dégradé de balayage pour un cercle avec une épaisseur de contour de 50 pixels de couleur :

[![Dégradé de balayage](circular-gradients-images/SweepGradient.png "dégradé de balayage")](circular-gradients-images/SweepGradient-Large.png#lightbox)

Le `SweepGradientPage` classe définit un tableau de huit couleurs avec des valeurs de teinte différents. Notez que le tableau commence et se termine par rouge (une valeur de teinte de 0 ou 360), qui s’affiche à l’extrême droite dans les captures d’écran :

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

Le programme implémente également un `TapGestureRecognizer` qui permet à du code à la fin de la `PaintSurface` gestionnaire. Ce code utilise le même dégradé pour remplir la zone de dessin :

[![Lors des nettoyages intégral dégradé](circular-gradients-images/SweepGradientFull.png "intégral dégradé de balayage")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Ces captures d’écran montrent que les dégradés toute zone est coloré par celui-ci. Si le dégradé de ne pas commencer et se terminer par la même couleur, il y aura une discontinuité à droite du point central.

## <a name="the-two-point-conical-gradient"></a>Deux points conique dégradé

Le [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) méthode présente la syntaxe suivante :

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

Les paramètres commencent par les points centraux et les rayons pour deux cercles, appelés le _Démarrer_ cercle et _fin_ cercle. Les trois paramètres restants sont les mêmes que pour `CreateLinearGradient` et `CreateRadialGradient`. Un [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) surcharge inclut une matrice de transformation.

Le dégradé commence au cercle de début et se termine au cercle de fin. Le `SKShaderTileMode` paramètre régit que se passe-t-il au-delà de deux cercles. Le gradient de conique de deux points est le seul dégradé qui ne remplit pas entièrement une zone. Si les deux cercles ont le même rayon, le dégradé est limité à un rectangle dont la largeur est le même que le diamètre des cercles. Si les deux cercles ont des rayons différents, le dégradé constitue un cône.

Il est probable que vous souhaitez faire des essais avec deux points conique dégradé, donc la **conique dégradé** dérive de la page `InteractivePage` pour autoriser les deux points tactiles à être déplacées pour les deux cercle rayons :

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

Le fichier code-behind définit les deux `TouchPoint` objets avec des rayons fixes de 50 et 100 :

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

Le `colors` tableau est rouge, vert et bleu. Le code vers le bas de la `PaintSurface` gestionnaire dessine les points deux tactiles comme noir cercles afin qu’ils n’à l’encontre du dégradé.

Notez que `DrawRect` appel utilise du dégradé de couleur de la totalité du canevas. Toutefois, en règle générale, une grande partie de la zone de dessin reste non colorée en dégradé. Voici le programme montrant les trois configurations possibles :

[![Dégradé conique](circular-gradients-images/ConicalGradient.png "conique dégradé")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

L’écran iOS à gauche montre l’effet de la `SKShaderTileMode` paramètre `Clamp`. Le dégradé commence par rouge du bord intérieur du cercle plus petit est opposé le côté le plus proche du cercle deuxième. Le `Clamp` valeur provoque également rouge pour continuer au point du cône. Le dégradé se termine par bleu au bord externe du cercle plus grand qui est le premier cercle le plus proche, mais continue avec bleu au sein de ce cercle et au-delà.

L’écran Android est similaire, mais avec un `SKShaderTileMode` de `Repeat`. Maintenant, il est plus clair que le dégradé commence à l’intérieur du cercle premier et se termine en dehors du cercle deuxième. Le `Repeat` paramètre entraîne répéter rouge à l’intérieur du plus grand cercle de dégradé.

L’écran UWP montre ce qui se passe lorsque le plus petit cercle est déplacé entièrement à l’intérieur du cercle plus volumineux. Le dégradé cesse d’être un cône et au lieu de cela remplit la zone entière. L’effet est similaire à dégradé radial, mais il est asymétrique si le plus petit cercle est centré pas exactement dans le cercle plus volumineux.

Vous pouvez doute l’utilité pratique du dégradé quand un cercle est imbriqué dans une autre, mais elle est idéale pour une mise en surbrillance spéculaire.

## <a name="conical-gradients-for-specular-highlights"></a>Dégradés coniques pour les surbrillances spéculaires

Plus haut dans cet article, vous avez vu comment utiliser un dégradé radial pour créer une mise en surbrillance spéculaire. Vous pouvez également utiliser le dégradé conique deux points à cet effet, et vous préférerez peut-être à quoi il ressemble :

[![Mise en surbrillance spéculaire conique](circular-gradients-images/ConicalSpecularHighlight.png "conique surbrillance spéculaire")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

L’apparence asymétrique suggère mieux l’arrondi surface de l’objet. 

Le code de dessin dans le **surbrillance spéculaire conique** page est le même que le **surbrillance spéculaire radiale** page à l’exception du nuanceur :

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

Les deux cercles disposent de centres de `offCenter` et `center`. Le cercle est centré à `center` est associé à un rayon qui englobe la balle entière, mais le cercle centré à `offCenter` a un rayon d’un pixel. Le dégradé commence à ce stade et se termine à la périphérie de la boule efficacement.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
