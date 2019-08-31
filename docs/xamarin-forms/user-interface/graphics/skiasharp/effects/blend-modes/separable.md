---
title: Les modes de blend séparables
description: Utiliser les modes séparables blend pour modifier les couleurs rouges, vert et bleus.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 621bc872a80cf023bb9428e2d3183e8af611211f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200392"
---
# <a name="the-separable-blend-modes"></a>Les modes de blend séparables

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu dans l’article [ **SkiaSharp Porter-Duff les modes de fusion**](porter-duff.md), en règle générale, les modes de blend Porter-Duff remplissent les opérations de découpage. Les modes de blend séparables sont différents. Les modes séparables de modifier les composants de couleur rouge, vert et bleu individuelles d’une image. Modes de fusion séparables peuvent combiner couleur afin de démontrer que la combinaison de rouge, vert et bleu est effectivement blanche :

![Couleurs primaires](separable-images/SeparableSample.png "couleurs primaires")

## <a name="lighten-and-darken-two-ways"></a>Éclaircir et assombrir deux façons 

Il est courant d’avoir une image bitmap qui est un peu trop sombre ou trop clair. Vous pouvez utiliser les modes de fusion séparables pour éclaircir ou obscurcir l’imabe.  En effet, deux des modes blend séparables dans le [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) énumération sont nommés `Lighten` et `Darken`. 

Ces deux modes sont décrites dans le **éclaircir et obscurcir** page. Le fichier XAML instancie deux `SKCanvasView` objets et deux `Slider` vues :

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

La première `SKCanvasView` et `Slider` montrent `SKBlendMode.Lighten` et illustre la deuxième paire `SKBlendMode.Darken`. Les deux `Slider` vues partagent le même `ValueChanged` gestionnaire et les deux `SKCanvasView` partagent le même `PaintSurface` gestionnaire. Les deux vérification de gestionnaires d’événements objet qui déclenche l’événement :

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

Le `PaintSurface` Gestionnaire calcule un rectangle approprié pour l’image bitmap. Le gestionnaire affiche cette image bitmap, puis affiche un rectangle sur la bitmap à l’aide un `SKPaint` de l’objet avec ses `BlendMode` propriété définie sur `SKBlendMode.Lighten` ou `SKBlendMode.Darken`. Le `Color` propriété dit une nuance de gris selon le `Slider`. Pour le `Lighten` mode, les plages de couleurs du noir à blanc, mais pour le `Darken` mode cela va du blanc sur noir.

Les captures d’écran de gauche à droite affichent toujours plus grands `Slider` valeurs comme l’image supérieure obtient plus clair et l’image du bas obtient plus sombre :

[![Éclaircir et assombrir](separable-images/LightenAndDarken.png "éclaircir et assombrir")](separable-images/LightenAndDarken-Large.png#lightbox)

Ce programme montre le mode d’utilisation normal des modes de fusion séparables: La destination est une image d’un tri, très souvent un bitmap. La source est un rectangle affiché à l’aide un `SKPaint` de l’objet avec ses `BlendMode` définie sur un mode blend séparables de propriété. Le rectangle peut être une couleur unie (comme c’est ici) ou un dégradé. La transparence est _pas_ généralement utilisé avec les modes séparables blend.

Comme vous faire des essais avec ce programme, vous découvrirez que ces modes deux blend ne sont pas éclaircir et assombrir l’image de manière uniforme. Au lieu de cela, le `Slider` semble définir un seuil quelconque. Par exemple, en tant que vous augmentez le `Slider` pour la `Lighten` mode, les zones plus sombre de l’image accéder light tout d’abord tandis que les zones plus claires restent les mêmes.

Pour le `Lighten` mode, si le pixel de destination est la valeur de couleur RVB (récupération d’urgence, le groupe de distribution, la base de données) et le pixel de la source est la couleur (Sr, Sg, Sb), la sortie est (ou, Og, Ob) calculé comme suit :

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

Rouge, vert et bleu séparément, le résultat est supérieur à la source et de destination. Cela a pour effet d’éclaircissement tout d’abord les zones sombres de la destination.

Le `Darken` mode est semblable à ceci près que le résultat est la plus petite entre la source et de destination :

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

Les composants rouges, vert et bleus sont chacun gérés séparément, c’est pourquoi ces modes de fusion sont appelés les _séparables_ les modes de fusion. Pour cette raison, les abréviations **Dc** et **Sc** peut être utilisé pour les couleurs source et de destination, et il est entendu que les calculs s’appliquent séparément à chacun des composants rouges, vert et bleus.

Le tableau suivant affiche tous les modes de blend séparables avec une brève explication de ce qu’ils font. La deuxième colonne indique la couleur de la source qui ne produit aucun résultat :

| Mode de fusion   | Aucune modification | Opération |
| ------------ | --------- | --------- |
| `Plus`       | Noir     | Éclaircit en ajoutant des couleurs: SC + DC |
| `Modulate`   | Blanc     | Assombrit en multipliant les couleurs: SC Métafichier | 
| `Screen`     | Noir     | Complète le produit des compléments: SC + DC &ndash; SC · Métafichier |
| `Overlay`    | Gris      | Inverse de `HardLight` |
| `Darken`     | Blanc     | Minimum de couleurs : min (Sc, contrôleur de domaine) |
| `Lighten`    | Noir     | Nombre maximal de couleurs : max (Sc, contrôleur de domaine) |
| `ColorDodge` | Noir     | Luminosité augmente en fonction de la source de destination |
| `ColorBurn`  | Blanc     | Assombrit selon la source de destination | 
| `HardLight`  | Gris      | Similaire à l’effet de lumière crue |
| `SoftLight`  | Gris      | Similaire à l’effet de spotlight de manière réversible | 
| `Difference` | Noir     | Soustrait le plus sombre du plus clair: ABS (DC &ndash; SC) | 
| `Exclusion`  | Noir     | Semblable à `Difference` mais réduire contraste |
| `Multiply`   | Blanc     | Assombrit en multipliant les couleurs: SC Métafichier |

Vous trouverez des algorithmes plus détaillées dans le W3C [ **composition et fusion de niveau 1** ](https://www.w3.org/TR/compositing-1/) spécification et la Skia [ **SkBlendMode référence** ](https://skia.org/user/api/SkBlendMode_Reference), bien que la notation dans ces deux sources n’est pas le même. N’oubliez pas que `Plus` est souvent considéré comme un mode de lissage Porter-Duff, et `Modulate` ne fait pas partie de la spécification W3C.

Si la source est transparente, puis pour tous les séparables les modes de fusion à l’exception de `Modulate`, le mode de fusion n’a aucun effet. Comme vous l’avez vu précédemment, le `Modulate` blend mode incorpore le canal alpha de la multiplication. Sinon, `Modulate` a le même effet que `Multiply`. 

Notez les deux modes nommés `ColorDodge` et `ColorBurn`. Les mots _fous_ et _graver_ provient de pratiques de photo pointu. Un outil rend un tirage source lumineuse via une valeur négative. Avec aucune lumière, l’impression est blanche. L’impression obtient plus sombre que davantage de lumière tombe sur l’impression pour une longue période de temps. Les constructeurs d’impression utilisé souvent une main ou les petits objets pour bloquer certains de la lumière de tomber sur une certaine partie de l’impression, rendant cette zone plus claire. Il s’agit _à_. À l’inverse, opaque avec un trou dans (ou mains bloque la plupart de la lumière) peut être utilisé pour diriger le plus clair dans une zone particulière pour assombrir appelée _gravure_.

Le **fous et Burn** programme est très similaire à **éclaircir et obscurcir**. Le fichier XAML est structuré identique mais avec des noms d’éléments différents et le fichier code-behind est la même manière tout à fait similaire, mais l’effet de ces modes de deux blend est assez différente :

[![Fous et Burn](separable-images/DodgeAndBurn.png "fous et Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

Pour les petites `Slider` valeurs, le `Lighten` mode éclaircit les zones sombres tout d’abord, tandis que `ColorDodge` éclaircit plus uniformément.

Programmes d’application de traitement d’image permettent souvent d’éclaircissement et la gravure de restreindre à des zones spécifiques, comme dans un pointu. Cela est possible par des dégradés, ou par une image bitmap avec différentes nuances de gris.

## <a name="exploring-the-separable-blend-modes"></a>Explorer les modes séparables blend

Le **séparables Modes Blend** page vous permet d’examiner tous les modes séparables blend. Elle affiche une destination de bitmap et une source de rectangle coloré à l’aide d’un des modes de blend. 

Le fichier XAML définit un `Picker` (pour sélectionner le mode de fusion) et des curseurs de quatre. Les trois premiers curseurs vous permettent de définir les composants rouges, vert et bleus de la source. Le curseur quatrième est destiné à remplacer ces valeurs en définissant une nuance de gris. Les curseurs individuels ne sont pas identifiées, mais les couleurs indiquent leur fonction :

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

Le fichier code-behind charge une des ressources bitmap et dessine à deux reprises, une fois dans la moitié supérieure de la zone de dessin et à nouveau dans la partie inférieure la moitié de la zone de dessin :

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

Au bas de la `PaintSurface` gestionnaire, d’un rectangle est dessiné sur la deuxième bitmap avec le mode de fusion sélectionnée et la couleur sélectionnée. Vous pouvez comparer la bitmap modifiée en bas avec la bitmap d’origine en haut :

[![Modes de fusion séparables](separable-images/SeparableBlendModes.png "Blend séparables Modes")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>Couleurs primaires addition et soustraction

Le **couleurs primaires** page dessine trois cercles se chevauchant de rouge, vert et bleu :

[![Les couleurs primaires additives](separable-images/PrimaryColors-Additive.png "additifs couleurs primaires")](separable-images/PrimaryColors-Additive.png#lightbox)

Il s’agit des couleurs primaires additives. Combinaisons de deux produisent cyan, magenta et jaune et une combinaison des trois est le blanche.

Ces trois cercles sont dessinées avec la `SKBlendMode.Plus` mode, mais vous pouvez également utiliser `Screen`, `Lighten`, ou `Difference` pour le même effet. Voici le programme :

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

Le programme inclut un `TabGestureRecognizer`. Lorsque vous appuyez ou cliquez sur l’écran, le programme utilise `SKBlendMode.Multiply` pour afficher les bases de données primaires soustractifs trois :

[![Les couleurs primaires soustractifs](separable-images/PrimaryColors-Subtractive.png "soustractifs couleurs primaires")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

Le `Darken` mode fonctionne également pour cet même effet.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
