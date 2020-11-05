---
title: Modes de fusion séparable
description: Utilisez les modes de fusion séparable pour modifier les couleurs rouge, vert et bleu.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3480c2fe2ef94a2a1beee9a924a59cd90d3a42b3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370804"
---
# <a name="the-separable-blend-modes"></a>Modes de fusion séparable

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu dans l’article [**SkiaSharp Porter-Duff modes de fusion**](porter-duff.md), les modes Porter-Duff Blend effectuent généralement des opérations de découpage. Les modes de fusion séparable sont différents. Les modes séparables modifient les composants de couleur rouge, vert et bleu d’une image. Les modes de fusion séparable peuvent mélanger la couleur pour démontrer que la combinaison de rouge, de vert et de bleu est effectivement blanche :

![Couleurs primaires](separable-images/SeparableSample.png "Couleurs primaires")

## <a name="lighten-and-darken-two-ways"></a>Éclaircir et assombrir deux méthodes 

Il est courant d’avoir une bitmap qui est quelque peu trop sombre ou trop claire. Vous pouvez utiliser les modes de fusion séparable pour éclaircir ou assombrir le imabe.  En effet, deux des modes de fusion séparables de l' [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) énumération sont nommés `Lighten` et `Darken` . 

Ces deux modes sont illustrés dans la page **Eclaircir et obscurcir** . Le fichier XAML instancie deux `SKCanvasView` objets et deux `Slider` vues :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

Le premier `SKCanvasView` et `Slider` le présentateur `SKBlendMode.Lighten` et la deuxième paire illustrent `SKBlendMode.Darken` . Les deux `Slider` vues partagent le même `ValueChanged` Gestionnaire, et les deux `SKCanvasView` partagent le même `PaintSurface` Gestionnaire. Les deux gestionnaires d’événements vérifient l’objet qui déclenche l’événement :

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

Le `PaintSurface` Gestionnaire calcule un rectangle approprié pour l’image bitmap. Le gestionnaire affiche cette bitmap, puis affiche un rectangle sur la bitmap à l’aide d’un `SKPaint` objet dont la `BlendMode` propriété a la valeur `SKBlendMode.Lighten` ou `SKBlendMode.Darken` . La `Color` propriété est une ombre grise basée sur `Slider` . Pour le `Lighten` mode, la couleur est comprise entre le noir et le blanc, mais pour le mode, elle est comprise `Darken` entre le blanc et le noir.

Les captures d’écran de gauche à droite affichent des valeurs de plus en plus grandes `Slider` lorsque l’image supérieure devient plus claire et l’image inférieure est plus sombre :

[![Eclaircir et obscurcir](separable-images/LightenAndDarken.png "Eclaircir et obscurcir")](separable-images/LightenAndDarken-Large.png#lightbox)

Ce programme montre le mode d’utilisation normal des modes de fusion séparable : la destination est une image d’un tri, très souvent une image bitmap. La source est un rectangle affiché à l’aide d’un `SKPaint` objet dont la propriété a la `BlendMode` valeur d’un mode de fusion séparable. Le rectangle peut être une couleur unie (telle qu’elle est ici) ou un dégradé. La transparence n’est généralement _pas_ utilisée avec les modes de fusion séparable.

À mesure que vous expérimentez ce programme, vous découvrirez que ces deux modes de fusion n’éclaircissent pas et assombrissent uniformément l’image. Au lieu de cela, `Slider` semble définir un seuil d’un certain type. Par exemple, quand vous augmentez `Slider` pour le `Lighten` mode, les zones plus sombres de l’image sont d’abord claires, tandis que les zones plus claires restent les mêmes.

Pour le `Lighten` mode, si le pixel de destination est la valeur de couleur RVB (Dr, DG, DB) et que le pixel source est la couleur (SR, SG, SB), la sortie est (ou, og, OB) calculée comme suit :

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

Pour les couleurs rouge, vert et bleu séparément, le résultat est le plus élevé de la destination et de la source. Cela a pour effet d’éclaircir d’abord les zones sombres de la destination.

Le `Darken` mode est similaire, à ceci près que le résultat est le plus petit de la destination et de la source :

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

Les composants rouge, vert et bleu sont gérés séparément, ce qui explique pourquoi ces modes de fusion sont appelés modes de fusion _séparables_ . Pour cette raison, les abréviations **DC** et **SC** peuvent être utilisées pour les couleurs de destination et source, et il est entendu que les calculs s’appliquent séparément à chacun des composants rouge, vert et bleu.

Le tableau suivant présente tous les modes de fusion séparables avec une brève explication de ce qu’ils font. La deuxième colonne indique la couleur source qui ne produit aucune modification :

| Mode de fusion   | Aucun changement | Opération |
| ------------ | --------- | --------- |
| `Plus`       | Noir     | Éclaircit en ajoutant des couleurs : SC + DC |
| `Modulate`   | White     | Assombrit en multipliant les couleurs : SC · Métafichier | 
| `Screen`     | Noir     | Complète le produit de compléments : SC + DC &ndash; SC · Métafichier |
| `Overlay`    | Gris      | Inverse de `HardLight` |
| `Darken`     | White     | Nombre minimal de couleurs : min (SC, DC) |
| `Lighten`    | Noir     | Nombre maximal de couleurs : Max (SC, DC) |
| `ColorDodge` | Noir     | Éclaircit la destination en fonction de la source |
| `ColorBurn`  | White     | Assombrit la destination en fonction de la source | 
| `HardLight`  | Gris      | Similaire à l’effet de la lumière crue |
| `SoftLight`  | Gris      | Similaire à l’effet de la lumière douce | 
| `Difference` | Noir     | Soustrait le plus sombre du plus clair : ABS (DC &ndash; SC) | 
| `Exclusion`  | Noir     | Semblable à, `Difference` mais contraste inférieur |
| `Multiply`   | White     | Assombrit en multipliant les couleurs : SC · Métafichier |

Vous trouverez des algorithmes plus détaillés dans la spécification du W3C sur la [**composition et la fusion de niveau 1**](https://www.w3.org/TR/compositing-1/) et la [**référence skia SkBlendMode**](https://skia.org/user/api/SkBlendMode_Reference), bien que la notation dans ces deux sources ne soit pas la même. Gardez à l’esprit que `Plus` est généralement considéré comme un Porter-Duff mode Blend et qu’il ne fait `Modulate` pas partie de la spécification du W3C.

Si la source est transparente, pour tous les modes de fusion séparable à l’exception de `Modulate` , le mode de fusion n’a aucun effet. Comme vous l’avez vu précédemment, le `Modulate` mode fondu incorpore le canal alpha dans la multiplication. Sinon, `Modulate` a le même effet que `Multiply` . 

Notez les deux modes nommés `ColorDodge` et `ColorBurn` . Les mots « _densité_ et _gravure_ » proviennent de pratiques darkroom. Un agrandisseur effectue une impression photographique en illumine la lumière sur une valeur négative. Sans lumière, l’impression est blanche. L’impression est plus sombre, car la lumière est plus claire sur l’impression pendant une période plus longue. Les décideurs utilisent souvent une main ou un petit objet pour empêcher une partie de la lumière de tomber dans une certaine partie de l’impression, ce qui rend cette zone plus claire. C’est ce que l’on appelle _Dodging_. À l’inverse, une matière opaque avec un trou dans celle-ci (ou mains bloquant la plus grande partie de la lumière) peut être utilisée pour orienter plus clair dans un endroit particulier afin de l’assombrir, appelée _gravure_.

Le programme de **densité et de vieillissement** est très similaire à la **Eclaircir et à l’assombrissement**. Le fichier XAML est structuré de la même façon mais avec des noms d’éléments différents, et le fichier code-behind est également assez similaire, mais l’effet de ces deux modes de fusion est assez différent :

[![Densité et densité](separable-images/DodgeAndBurn.png "Densité et densité")](separable-images/DodgeAndBurn-Large.png#lightbox)

Pour `Slider` les petites valeurs, le `Lighten` mode éclaircit d’abord les zones sombres, tout en s' `ColorDodge` éclaircit de manière plus uniforme.

Les programmes d’application de traitement d’images permettent souvent aux Dodging et à la gravure de se limiter à des zones spécifiques, comme dans un darkroom. Cela peut être accompli par les dégradés ou par une image bitmap avec des nuances de gris variables.

## <a name="exploring-the-separable-blend-modes"></a>Exploration des modes de fusion séparable

La page **modes de fusion séparable** vous permet d’examiner tous les modes de fusion séparables. Elle affiche une destination bitmap et une source rectangle colorée à l’aide de l’un des modes de fusion. 

Le fichier XAML définit un `Picker` (pour sélectionner le mode de fusion) et quatre curseurs. Les trois premiers curseurs vous permettent de définir les composants rouge, vert et bleu de la source. Le quatrième curseur est destiné à remplacer ces valeurs en définissant une ombre grise. Les curseurs individuels ne sont pas identifiés, mais les couleurs indiquent leur fonction :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Le fichier code-behind charge l’une des ressources bitmap et le dessine deux fois, une fois dans la moitié supérieure du canevas et à nouveau dans la moitié inférieure de la zone de dessin :

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Vers le bas du `PaintSurface` Gestionnaire, un rectangle est dessiné sur la deuxième bitmap avec le mode de fusion sélectionné et la couleur sélectionnée. Vous pouvez comparer le bitmap modifié en bas avec l’image bitmap d’origine en haut :

[![Modes de fusion séparables](separable-images/SeparableBlendModes.png "Modes de fusion séparables")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Couleurs principales additives et soustraire

La page **couleurs primaires** dessine trois cercles qui se chevauchent, le rouge, le vert et le bleu :

[![Couleurs primaires additives](separable-images/PrimaryColors-Additive.png "Couleurs primaires additives")](separable-images/PrimaryColors-Additive.png#lightbox)

Il s’agit des couleurs primaires additives. Les combinaisons de deux produisent des couleurs Cyan, magenta et jaune, et une combinaison des trois est blanche.

Ces trois cercles sont dessinés avec le `SKBlendMode.Plus` mode, mais vous pouvez également utiliser `Screen` , `Lighten` ou `Difference` pour le même effet. Voici le programme :

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

Le programme comprend un `TabGestureRecognizer` . Lorsque vous appuyez ou cliquez sur l’écran, le programme utilise `SKBlendMode.Multiply` pour afficher les trois primaires rétractés :

[![Couleurs primaires de soustraction](separable-images/PrimaryColors-Subtractive.png "Couleurs primaires de soustraction")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

Le `Darken` mode fonctionne également pour ce même effet.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)