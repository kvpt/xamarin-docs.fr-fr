---
title: Les modes de fusion non séparables
description: Utiliser les modes de fusion non séparables de modifier la teinte, saturation ou luminosité.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9054539b08da89c0f7d8a93150866fb1b41e63f1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642783"
---
# <a name="the-non-separable-blend-modes"></a>Les modes de fusion non séparables

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu dans l’article [ **SkiaSharp séparable blend modes**](separable.md), les modes de blend séparables effectuer des opérations sur les canaux rouges, vert et bleus séparément. Les modes de fusion non séparables ne peuvent pas. En opérant sur les niveaux de teinte, Saturation et de luminosité de couleur, les modes de fusion non séparables peuvent modifier les couleurs de façons intéressantes :

![Exemple non séparables](non-separable-images/NonSeparableSample.png "exemple Non séparables")

## <a name="the-hue-saturation-luminosity-model"></a>Le modèle de Teinte-Saturation-Luminosité

Pour comprendre les modes de fusion non séparables, il est nécessaire traiter les pixels de la source et de destination comme des couleurs dans le modèle de Teinte-Saturation-Luminosité. (La luminosité est également appelée luminosité.)

Le modèle de couleurs TSL a été abordé dans l’article [ **l’intégration avec Xamarin.Forms** ](../../basics/integration.md) et un exemple de programme dans cet article permet de l’expérimentation avec les couleurs TSL. Vous pouvez créer un `SKColor` valeur à l’aide des valeurs de teinte, Saturation et de luminosité avec la ligne statique [ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*) (méthode).

La teinte représente la longueur d’onde dominante de la couleur. Les valeurs de teinte sont comprises entre 0 et 360 et défilent vers l’additif et les primaires de soustraction: Le rouge est la valeur 0, le jaune est 60, le vert est 120, le cyan est 180, le bleu est 240, le magenta est 300 et le cycle revient au rouge à 360.

S’il n’existe aucune couleur dominante &mdash; , par exemple, la couleur est blanc ou noir ou un dégradé de gris &mdash; ensuite la teinte est non définie et généralement la valeur 0. 

Les valeurs de Saturation peuvent être comprise entre 0 et 100 et indiquer la pureté de la couleur. Une valeur de Saturation de 100 est la couleur plus pure tandis que les valeurs inférieures à 100 provoque la couleur à devenir plus grayish. Une valeur de Saturation de 0 entraîne une nuance de gris.

La valeur de luminosité (ou luminosité) indique la couleur de la luminosité est. Une valeur de luminosité de 0 est le noire indépendamment des autres paramètres. De même, une valeur de luminosité de 100 est blanche. 

La valeur TSL (0, 100, 50) est la valeur RVB (FF, 00, 00), qui est rouge pure. La valeur TSL (180, 100, 50) est la valeur RVB (00, FF, FF), cyan pur. Comme la Saturation est réduite, le composant de couleur dominante est réduit et les autres composants sont augmentées. À un niveau de Saturation de 0, tous les composants sont identiques et la couleur est une nuance de gris. Diminuer la luminosité pour accéder à noir ; Augmentez la luminosité pour accéder au blanc.

## <a name="the-blend-modes-in-detail"></a>Les modes de fusion en détail

Comme les autres modes de fusion, les quatre modes de lissage non séparables impliquent une destination (c'est-à-dire le plus souvent une image bitmap) et une source, qui est souvent une couleur unique ou un dégradé. Les modes de blend combinent les valeurs de teinte, Saturation et de luminosité à partir de la source et de la destination :

| Mode de fusion   | Composants à partir de la source | Composants de destination |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Teinte                    | Saturation et luminosité   |
| `Saturation` | Saturation             | Teinte et la luminosité          |
| `Color`      | Teinte et la Saturation     | Luminosité                  | 
| `Luminosity` | Luminosité             | Teinte et la Saturation          | 

Consultez le W3C [ **composition et fusion de niveau 1** ](https://www.w3.org/TR/compositing-1/) spécification pour les algorithmes.

Le **Non séparables Modes Blend** page contient un `Picker` pour sélectionner l’une de ces modes et trois blend `Slider` vues pour sélectionner une couleur TSL :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Pour économiser de l’espace, les trois `Slider` vues ne sont pas identifiées dans l’interface utilisateur du programme. Vous aurez besoin de se rappeler que l’ordre est teinte, Saturation et luminosité. Deux `Label` vues au bas de la page affichent les valeurs de couleur RVB et TSL.

Le fichier code-behind charge une des ressources bitmap, qui affiche aussi grand que possible sur le canevas et couvre ensuite la zone de dessin avec un rectangle. La couleur du rectangle est basée sur les trois `Slider` vues et le mode de fusion est celui sélectionné dans le `Picker`:

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Notez que le programme n’affiche pas la valeur de la couleur TSL comme déterminé par les curseurs de trois. Au lieu de cela, il crée une valeur de couleur à partir de ces curseurs et utilise ensuite le [ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*) méthode pour obtenir les valeurs de teinte, Saturation et de luminosité. Il s’agit, car le `FromHsl` méthode convertit une couleur TSL une couleur RVB, qui est stockée en interne dans le `SKColor` structure. Le `ToHsl` méthode convertit RVB TSL, mais le résultat ne sera pas toujours la valeur d’origine. 

Par exemple, `FromHsl` convertit la valeur TSL (180, 50, 0) pour la couleur RGB (0, 0, 0), car le `Luminosity` est égal à zéro. Le `ToHsl` méthode convertit la couleur RGB (0, 0, 0) à la couleur TSL (0, 0, 0), car les valeurs de teinte et la Saturation ne soient pas pertinentes. Lorsque vous utilisez ce programme, il est préférable que vous voyez la représentation sous forme de la couleur TSL qui utilise le programme au lieu de celui que vous avez spécifié avec les curseurs.

Le `SKBlendModes.Hue` mode de fusion utilise le niveau de la teinte de la source de tout en conservant les niveaux de Saturation et de luminosité de la destination. Lorsque vous testez ce mode blend, les curseurs de saturation et de luminosité doivent être définies sur un élément autre que 0 ou 100, car dans ce cas, la teinte n'est pas définie de manière unique.

[![Modes de fusion non séparables - Hue](non-separable-images/NonSeparableBlendModes-Hue.png "Modes Non séparables Blend - Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Lorsque vous utilisez positionnez le curseur sur 0 (comme avec la capture d’écran d’iOS à gauche), tout devient tirent. Mais cela ne signifie pas que l’image est entièrement absent de vert et bleu. Évidemment, il existe des nuances de gris toujours présents dans le résultat. Par exemple, la couleur RVB (40, 40, C0) est équivalente à la couleur TSL (240, 50, 50). La teinte est bleu, mais la valeur de saturation de 50 indique qu’il existe également des composants rouges et vertes. Si la teinte est définie à 0 avec `SKBlendModes.Hue`, la couleur TSL est (0, 50, 50), qui est la couleur RVB (C0, 40, 40). Il existe toujours bleues et vertes de composants, mais maintenant le composant dominant est rouge.

Le `SKBlendModes.Saturation` blend mode combine le niveau de Saturation de la source avec la teinte et la luminosité de la destination. Comme la teinte, Saturation n’est pas bien définie, si la luminosité est 0 ou 100. En théorie, n’importe quel paramètre de luminosité entre ces deux extrêmes devrait fonctionner. Toutefois, le paramètre de luminosité semble affectent le résultat plus qu’elle ne devrait. Définissez la luminosité à 50, et vous pouvez voir comment vous pouvez définir le niveau de Saturation de l’image :

[![Modes de fusion non séparables - Saturation](non-separable-images/NonSeparableBlendModes-Saturation.png "Modes Non séparables Blend - Saturation")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Vous pouvez utiliser ce mode blend pour augmenter la Saturation d’une image ennuyeux de couleur, ou vous pouvez réduire la Saturation à zéro (comme dans la capture d’écran d’iOS à gauche) pour une image résultante, composée de nuances de gris uniquement.

Le `SKBlendModes.Color` blend mode conserve la luminosité de la destination, mais utilise la teinte et la Saturation de la source. Là encore, cela implique que tout paramètre du curseur de la luminosité quelque part entre les extrêmes doit fonctionner. 

[![Modes de fusion non séparables - couleur](non-separable-images/NonSeparableBlendModes-Color.png "Modes Non séparables Blend - couleur")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Vous verrez bientôt une application de ce mode blend.

Enfin, le `SKBlendModes.Luminosity` mode de fusion est l’opposé de `SKBlendModes.Color`. Il conserve la teinte et la Saturation de la destination, mais utilise la luminosité de la source. Le `Luminosity` mode de fusion est le plus mystérieux du lot: Les curseurs teinte et saturation affectent l’image, mais même avec une luminosité moyenne, l’image n’est pas distincte:

[![Modes de fusion non séparables - luminosité](non-separable-images/NonSeparableBlendModes-Luminosity.png "Modes Non séparables Blend - luminosité")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

En théorie, augmentant ou en diminuant la luminosité d’une image doit rendre plus clair ou sombre. Chose intéressante, la [exemple luminosité dans le Skia **SkBlendMode référence** ](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) est assez semblable.

Il n’est généralement pas le cas où vous souhaitez utiliser un des modes non séparables blend avec une source qui se compose d’une seule couleur à appliquer à l’image entière de destination. L’effet est simplement trop grand. Vous souhaitez limiter l’effet à une partie de l’image. Dans ce cas, la source va intégrer probablement la transparence, ou peut-être la source sera limitée à un graphique plus petit.

## <a name="a-matte-for-a-separable-mode"></a>Un cache pour un mode séparable

Voici les bitmaps inclus en tant que ressource dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple. Le nom de fichier est **Banana.jpg**:

![Banana Monkey](non-separable-images/Banana.jpg "Banana Monkey")

Il est possible de créer un cache qui englobe simplement le banana. Il s’agit également d’une ressource dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple. Le nom de fichier est **BananaMatte.png**:

![Cache de banana](non-separable-images/BananaMatte.png "Banana cache")

À part la forme banana noir, le reste de l’image bitmap est transparent.

Le **Banana bleu** page utilise ce cache pour modifier la teinte et la Saturation banane qui maintient le monkey, mais modifier rien d’autre dans l’image. 

Dans l’exemple suivant `BlueBananaPage` (classe), le **Banana.jpg** bitmap est chargée en tant que champ. Les charges de constructeur le **BananaMatte.png** bitmap en tant que le `matteBitmap` objet, mais il ne conserve pas au-delà du constructeur de cet objet. Au lieu de cela, une image bitmap tiers nommé `blueBananaBitmap` est créé. Le `matteBitmap` est dessinée sur `blueBananaBitmap` suivie d’une `SKPaint` avec son `Color` bleu et son `BlendMode` défini sur `SKBlendMode.SrcIn`. Le `blueBananaBitmap` restent en grande partie transparent, mais avec une image de bleu pure solide la banane :

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
        }

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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

Le `PaintSurface` gestionnaire dessine l’image bitmap avec le monkey contenant le banana. Ce code est suivi par l’affichage de `blueBananaBitmap` avec `SKBlendMode.Color`. Sur la surface de la banane, teinte et la Saturation de chaque pixel est remplacé par le bleu solid, qui correspond à une valeur de teinte de 240 et une valeur de saturation de 100. La luminosité, cependant, reste le même, ce qui signifie que le banana continue à avoir une texture réaliste en dépit de sa nouvelle couleur :

[![Bleu Banana](non-separable-images/BlueBanana.png "bleu Banana")](non-separable-images/BlueBanana-Large.png#lightbox)

Essayez de modifier le mode de fusion à `SKBlendMode.Saturation`. Le banana reste jaune, mais il est un jaune plus intense. Dans une application réelle, cela peut être un effet plus souhaitable que l’activation de la banana bleu.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
