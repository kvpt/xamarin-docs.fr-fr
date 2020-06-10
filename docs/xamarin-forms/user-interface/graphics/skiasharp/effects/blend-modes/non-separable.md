---
titre : « description des modes de fusion non séparables » : utilisez les modes de fusion non séparables pour modifier la teinte, la saturation ou la luminosité.
ms. Prod : xamarin ms. Technology : xamarin-skiasharp ms. AssetID : 97FA2730-87C0-4914-8C9F-C64A02CF9EEF auteur : davidbritch ms. Author : dabritch ms. Date : 08/23/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="the-non-separable-blend-modes"></a>Modes de fusion non séparables

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu dans l’article [**SkiaSharp séparable Blend modes**](separable.md), les modes de fusion séparable effectuent des opérations sur les canaux rouge, vert et bleu séparément. Les modes de fusion non séparables ne le font pas. En opérant sur les niveaux de couleur teinte, saturation et luminosité, les modes de fusion non séparables peuvent altérer les couleurs de manière intéressante :

![Exemple non séparable](non-separable-images/NonSeparableSample.png "Exemple non séparable")

## <a name="the-hue-saturation-luminosity-model"></a>Modèle de teinte-saturation-luminosité

Pour comprendre les modes de fusion non séparables, il est nécessaire de traiter les pixels de destination et source en tant que couleurs dans le modèle de teinte-saturation-luminosité. (La luminosité est également appelée clarté.)

Le modèle TSL Color a été abordé dans l’article [**intégration Xamarin.Forms de à**](../../basics/integration.md) et un exemple de programme de cet article permet d’expérimenter les couleurs TSL. Vous pouvez créer une `SKColor` valeur à l’aide des valeurs de teinte, de saturation et de luminosité avec la [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl*) méthode statique.

La teinte représente la longueur d’onde dominante de la couleur. Les valeurs de teinte sont comprises entre 0 et 360 et traversent les primaires additive et soustrait : le rouge est la valeur 0, le jaune est 60, le vert est 120, le cyan est 180, le bleu est 240, le magenta est 300 et le cycle revient au rouge à 360.

S’il n’existe pas de couleur dominante &mdash; , par exemple, la couleur est du blanc ou du noir ou un ombrage gris, &mdash; la teinte est non définie et est généralement définie sur 0. 

Les valeurs de saturation peuvent être comprises entre 0 et 100 et indiquent la pureté de la couleur. Une valeur de saturation de 100 est la couleur la plus pure, tandis que les valeurs inférieures à 100 provoquent une coloration plus grise. Une valeur de saturation de 0 donne un ombrage de gris.

La valeur de luminosité (ou de luminosité) indique la luminosité de la couleur. Une valeur de luminosité de 0 est noire, quels que soient les autres paramètres. De même, une valeur de luminosité de 100 est blanche. 

La valeur TSL (0, 100, 50) est la valeur RVB (FF, 00, 00), qui est le rouge pur. La valeur TSL (180, 100, 50) est la valeur RVB (00, FF, FF), cyan pur. À mesure que la saturation est réduite, le composant de couleur dominant diminue et les autres composants sont augmentés. Avec un niveau de saturation de 0, tous les composants sont identiques et la couleur est une ombre grise. Réduisez la luminosité pour atteindre le noir ; Augmentez la luminosité pour atteindre le blanc.

## <a name="the-blend-modes-in-detail"></a>Modes de fusion en détail

À l’instar des autres modes de fusion, les quatre modes de fusion non séparables impliquent une destination (qui est souvent une image bitmap) et une source, qui est souvent une couleur unique ou un dégradé. Les modes de fusion combinent les valeurs de teinte, de saturation et de luminosité à partir de la destination et de la source :

| Mode de fusion   | Composants de la source | Composants de la destination |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | Teinte                    | Saturation et luminosité   |
| `Saturation` | Saturation             | Teinte et luminosité          |
| `Color`      | Teinte et saturation     | Luminance                  | 
| `Luminosity` | Luminance             | Teinte et saturation          | 

Consultez la spécification W3C sur la [**composition et le niveau de fusion 1**](https://www.w3.org/TR/compositing-1/) pour les algorithmes.

La page **modes de fusion non séparables** contient un `Picker` pour sélectionner l’un de ces modes de fondu et trois `Slider` vues pour sélectionner une couleur TSL :

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

Pour économiser de l’espace, les trois `Slider` vues ne sont pas identifiées dans l’interface utilisateur du programme. Vous devez vous souvenir que la commande est teinte, saturation et luminosité. Deux `Label` affichages au bas de la page affichent les valeurs de couleur TSL et RVB.

Le fichier code-behind charge l’une des ressources bitmap, l’affiche aussi grand que possible sur le canevas, puis couvre le canevas avec un rectangle. La couleur du rectangle est basée sur les trois `Slider` vues et le mode de fusion est celui sélectionné dans le `Picker` :

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

Notez que le programme n’affiche pas la valeur de couleur TSL sélectionnée par les trois curseurs. Au lieu de cela, il crée une valeur de couleur à partir de ces curseurs, puis utilise la [`ToHsl`](xref:SkiaSharp.SKColor.ToHsl*) méthode pour obtenir les valeurs de teinte, de saturation et de luminosité. Cela est dû au fait que la `FromHsl` méthode convertit une couleur TSL en couleur RVB, qui est stockée en interne dans la `SKColor` structure. La `ToHsl` méthode convertit de RVB en TSL, mais le résultat n’est pas toujours la valeur d’origine. 

Par exemple, `FromHsl` convertit la valeur TSL (180, 50, 0) en couleur RVB (0, 0, 0), car `Luminosity` est égal à zéro. La `ToHsl` méthode convertit la couleur RVB (0, 0, 0) en couleur TSL (0, 0, 0), car les valeurs de teinte et de saturation ne sont pas pertinentes. Lors de l’utilisation de ce programme, il est préférable de voir la représentation de la couleur TSL que le programme utilise au lieu de celle que vous avez spécifiée avec les curseurs.

Le `SKBlendModes.Hue` mode de fusion utilise le niveau de teinte de la source tout en conservant les niveaux de saturation et de luminosité de la destination. Lorsque vous testez ce mode de fondu, les curseurs de saturation et de luminosité doivent être définis sur une valeur autre que 0 ou 100, car dans ce cas, la teinte n’est pas définie de manière unique.

[![Modes de fusion non séparables-nuance](non-separable-images/NonSeparableBlendModes-Hue.png "Modes de fusion non séparables-nuance")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

Lorsque vous utilisez la fonction définir le curseur sur 0 (comme avec la capture d’écran iOS à gauche), tout devient rougeâtre. Mais cela ne signifie pas que l’image est entièrement absente du vert et du bleu. Évidemment, les nuances de gris sont toujours présentes dans le résultat. Par exemple, la couleur RVB (40, 40, C0) est équivalente à la couleur TSL (240, 50, 50). La teinte est bleue, mais la valeur de saturation de 50 indique qu’il y a également des composants rouge et vert. Si la teinte est définie sur 0 avec `SKBlendModes.Hue` , la couleur TSL est (0, 50, 50), qui est la couleur RVB (C0, 40, 40). Il existe toujours des composants bleus et verts, mais le composant dominant est désormais rouge.

Le `SKBlendModes.Saturation` mode de fusion associe le niveau de saturation de la source à la teinte et à la luminosité de la destination. À l’instar de la teinte, la saturation n’est pas bien définie si la luminosité est 0 ou 100. En théorie, tout paramètre de luminosité entre ces deux extrêmes doit fonctionner. Toutefois, le paramètre de luminosité semble affecter le résultat plus qu’il ne le devrait. Définissez la luminosité sur 50, et vous pouvez voir comment vous pouvez définir le niveau de saturation de l’image :

[![Modes de fusion non séparables-saturation](non-separable-images/NonSeparableBlendModes-Saturation.png "Modes de fusion non séparables-saturation")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

Vous pouvez utiliser ce mode de fondu pour augmenter la saturation des couleurs d’une image terne, ou vous pouvez réduire la saturation à zéro (comme dans la capture d’écran iOS à gauche) pour une image résultante composée uniquement de nuances de gris.

Le `SKBlendModes.Color` mode de fusion conserve la luminosité de la destination, mais utilise la teinte et la saturation de la source. Là encore, cela implique que tout paramètre du curseur Luminosité situé entre les extrêmes devrait fonctionner. 

[![Modes de fusion non séparables-couleur](non-separable-images/NonSeparableBlendModes-Color.png "Modes de fusion non séparables-couleur")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

Vous verrez bientôt une application de ce mode de fondu.

Enfin, le `SKBlendModes.Luminosity` mode de fusion est l’opposé de `SKBlendModes.Color` . Elle conserve la teinte et la saturation de la destination, mais utilise la luminosité de la source. Le `Luminosity` mode de fusion est le plus mystérieux du lot : les curseurs teinte et saturation affectent l’image, mais même avec une luminosité moyenne, l’image n’est pas distincte :

[![Modes de fusion non séparables-luminosité](non-separable-images/NonSeparableBlendModes-Luminosity.png "Modes de fusion non séparables-luminosité")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

En théorie, l’augmentation ou la diminution de la luminosité d’une image doit le rendre plus clair ou plus sombre. Il est intéressant de le faire, l' [exemple de luminosité de la **référence SkBlendMode** skia](https://skia.org/user/api/SkBlendMode_Reference#Luminosity) est assez similaire.

En règle générale, il est préférable d’utiliser l’un des modes de fusion non séparables avec une source constituée d’une seule couleur appliquée à l’image de destination entière. L’effet est tout simplement trop grand. Vous souhaiterez limiter l’effet à une partie de l’image. Dans ce cas, la source incorporera probablement transparancy, ou peut-être la source sera-t-elle limitée à un graphique plus petit.

## <a name="a-matte-for-a-separable-mode"></a>Cache pour un mode séparable

Voici l’une des bitmaps incluses comme ressource dans l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Le nom de fichier est **Banana.jpg**:

![Singe de la banane](non-separable-images/Banana.jpg "Singe de la banane")

Il est possible de créer un cache qui englobe uniquement la banane. Il s’agit également d’une ressource dans l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Le nom de fichier est **BananaMatte.png**:

![Cache de bananes](non-separable-images/BananaMatte.png "Cache de bananes")

Outre la forme noir, le reste de la bitmap est transparent.

La page de **banane bleue** utilise ce cache pour modifier la teinte et la saturation de la banane que le singe détient, mais pour ne rien faire d’autre dans l’image. 

Dans la `BlueBananaPage` classe suivante, la **Banana.jpg** bitmap est chargée en tant que champ. Le constructeur charge l’image bitmap **BananaMatte.png** en tant qu' `matteBitmap` objet, mais ne conserve pas cet objet au-delà du constructeur. Au lieu de cela, une troisième bitmap nommée `blueBananaBitmap` est créée. `matteBitmap`Est dessiné sur `blueBananaBitmap` suivi d’un `SKPaint` avec son `Color` défini sur bleu et son `BlendMode` défini sur `SKBlendMode.SrcIn` . Le `blueBananaBitmap` reste surtout transparent, mais avec une image bleue pure solide de la banane :

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

Le `PaintSurface` Gestionnaire dessine l’image bitmap avec le singe contenant la banane. Ce code est suivi de l’affichage de `blueBananaBitmap` avec `SKBlendMode.Color` . Sur la surface de la banane, la teinte et la saturation de chaque pixel sont remplacées par le bleu fixe, qui correspond à une valeur de teinte de 240 et une valeur de saturation de 100. Toutefois, la luminosité reste la même, ce qui signifie que la banane continue à avoir une texture réaliste malgré sa nouvelle couleur :

[![Banane bleue](non-separable-images/BlueBanana.png "Banane bleue")](non-separable-images/BlueBanana-Large.png#lightbox)

Essayez de changer le mode de fusion en `SKBlendMode.Saturation` . La banane reste jaune, mais il s’agit d’un jaune plus intense. Dans une application réelle, cela peut être un effet plus souhaitable que le virage du bleu de banane.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
