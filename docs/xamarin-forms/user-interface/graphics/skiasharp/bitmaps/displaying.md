---
title: Afficher des bitmaps SkiaSharp
description: Découvrez comment afficher des bitmaps en pixels dimensionne et étendu pour remplir des rectangles tout en conservant les proportions de SkiaSharp.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 9955b68346c74435a3a141c69d02e1bec5856bd3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759513"
---
# <a name="displaying-skiasharp-bitmaps"></a>Afficher des bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le sujet de bitmaps de SkiaSharp a été introduit dans l’article  **[principes fondamentaux de la Bitmap dans SkiaSharp](../basics/bitmaps.md)** . Cet article vous a montré les trois façons de bitmaps de charge et de trois façons d’afficher des bitmaps. Cet article passe en revue les techniques pour charger des bitmaps et approfondira l’utilisation de la `DrawBitmap` méthodes de `SKCanvas`.

![Affichage exemple](displaying-images/DisplayingSample.png "affichage exemple")

Le `DrawBitmapLattice` et `DrawBitmapNinePatch` méthodes sont décrites dans l’article  **[affichage de bitmaps de SkiaSharp segmenté](segmented.md)** .

Exemples de cette page sont tirés du **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application. Dans la page d’accueil de l’application, choisissez **SkiaSharp Bitmaps**, puis accédez à la **affichage des Bitmaps** section.

## <a name="loading-a-bitmap"></a>Le chargement d’une image bitmap

Une image bitmap utilisée par une application de SkiaSharp généralement provient d’une des trois sources différentes :

- Depuis Internet
- À partir d’une ressource incorporée dans le fichier exécutable
- À partir de la bibliothèque de photos de l’utilisateur

Il est également possible pour une application SkiaSharp créer une nouvelle image bitmap, puis dessiner dessus ou définir les bits du bitmap par algorithme. Ces techniques sont abordées dans les articles **[créer et dessiner sur les SkiaSharp Bitmaps](drawing.md)** et **[l’accès à des Pixels de Bitmap SkiaSharp](pixel-bits.md)** .

Dans les exemples de code de trois suivants du chargement d’une image bitmap, la classe est supposée pour contenir un champ de type `SKBitmap`:

```csharp
SKBitmap bitmap;
```

En tant que l’article **[principes fondamentaux de la Bitmap dans SkiaSharp](../basics/bitmaps.md)** indiqué, la meilleure façon de charger une image bitmap sur Internet est avec la [ `HttpClient` ](xref:System.Net.Http.HttpClient) classe. Une seule instance de la classe peut être définie en tant que champ :

```csharp
HttpClient httpClient = new HttpClient();
```

Lorsque vous utilisez `HttpClient` avec les applications iOS et Android, vous voudrez définir les propriétés du projet comme décrit dans les documents sur  **[sécurité TLS (Transport Layer) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Code qui utilise `HttpClient` implique souvent le `await` opérateur, et il doit résider dans un `async` méthode :

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

Notez que le `Stream` objet obtenu à partir de `GetStreamAsync` est copié dans un `MemoryStream`. Android n’autorise pas la `Stream` de `HttpClient` à traiter par le thread principal, sauf dans les méthodes asynchrones. 

Le [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) effectue beaucoup de travail : L' `Stream` objet qui lui est passé référence un bloc de mémoire contenant l’intégralité d’une bitmap dans l’un des formats de fichier bitmap courants, généralement JPEG, png ou gif. Le `Decode` méthode doit déterminer le format et puis décoder le fichier bitmap au format de bitmap internes de SkiaSharp.

Après votre code appelle `SKBitmap.Decode`, probablement invalidera la `CanvasView` afin que le `PaintSurface` gestionnaire peut afficher l’image bitmap qui vient d’être chargé.

En incluant l’image bitmap en tant que ressource incorporée dans la bibliothèque .NET Standard la deuxième façon de charger une image bitmap est référencée par les projets de plateforme individuels. Une ressource ID est passé à la [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) (méthode). Cet ID de ressource comprend le nom de l’assembly, le nom du dossier et le nom de la ressource séparée par des points :

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

Fichiers bitmap peuvent également être stockées en tant que ressources dans le projet de plateforme individuels pour iOS, Android et la plateforme universelle Windows (UWP). Toutefois, le chargement de ces bitmaps nécessite de code qui se trouve dans le projet de plateforme.

Une troisième approche à l’obtention d’une image bitmap est à partir de la bibliothèque d’images de l’utilisateur. Le code suivant utilise un service de dépendance qui est inclus dans le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application. Le **SkiaSharpFormsDemo** bibliothèque .NET Standard inclut la `IPhotoLibrary` interface, tandis que chacun des projets de plateforme contient un `PhotoLibrary` classe qui implémente cette interface.

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

En règle générale, ce code invalide également la `CanvasView` afin que le `PaintSurface` gestionnaire peut afficher la nouvelle image bitmap.

Le `SKBitmap` classe définit plusieurs propriétés utiles, y compris [ `Width` ](xref:SkiaSharp.SKBitmap.Width) et [ `Height` ](xref:SkiaSharp.SKBitmap.Height), qui révèlent les dimensions en pixels de l’image bitmap, ainsi que de nombreuses méthodes, notamment méthodes pour créer des bitmaps, copiez-les et exposer les bits de pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Affichage dans les dimensions en pixels

Le SkiaSharp [ `Canvas` ](xref:SkiaSharp.SKCanvas) classe définit quatre `DrawBitmap` méthodes. Ces méthodes permettent de bitmaps à afficher deux fondamentalement différentes manières : 

- En spécifiant un `SKPoint` valeur (ou séparément `x` et `y` valeurs) affiche l’image bitmap dans ses dimensions en pixels. Les pixels de l’image bitmap sont mappés directement aux pixels de l’affichage vidéo.
- Si vous spécifiez un rectangle, l’image bitmap pour l’étendre à la taille et la forme du rectangle. 

Vous affichez une image bitmap dans ses dimensions en pixels à l’aide de [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) avec un `SKPoint` paramètre ou [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) avec différentes `x` et `y` paramètres :

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Ces deux méthodes sont fonctionnellement identiques. Le point spécifié indique l’emplacement de l’angle supérieur gauche de la bitmap par rapport à la zone de dessin. Étant donné que la résolution de pixel des appareils mobiles est très élevée, plus petits bitmaps s’affichent généralement assez petits sur ces appareils.

Le paramètre facultatif `SKPaint` paramètre vous permet d’afficher la bitmap à l’aide de la transparence. Pour ce faire, créez un `SKPaint` de l’objet et définir le `Color` à une propriété `SKColor` valeur avec une valeur alpha de canal inférieur à 1. Exemple :

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

Les 0 x 80 passé comme dernier argument indique la transparence de 50 %. Vous pouvez également définir un canal alpha sur l’une des couleurs prédéfinies :

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Toutefois, la couleur elle-même est sans importance. Uniquement le canal alpha est examiné lorsque vous utilisez le `SKPaint` de l’objet dans un `DrawBitmap` appeler.

Le `SKPaint` objet joue également un rôle lors de l’affichage de bitmaps à l’aide de modes de fusion ou de filtrer les effets. Celles-ci sont décrites dans les articles [SkiaSharp composition et modes de fusion](../effects/blend-modes/index.md) et [filtres d’image SkiaSharp](../effects/image-filters.md).

Le **Dimensions en pixels** page dans le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** exemple de programme affiche une ressource bitmap qui est de 320 pixels de large sur 240 pixels de haut :

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Le `PaintSurface` Gestionnaire centres de l’image bitmap en calculant `x` et `y` valeurs basées sur les dimensions en pixels de la surface d’affichage et les dimensions en pixels de l’image bitmap :

[![Dimensions de pixel](displaying-images/PixelDimensions.png "Dimensions en pixels")](displaying-images/PixelDimensions-Large.png#lightbox)

Si l’application souhaite afficher l’image bitmap dans son coin supérieur gauche, il serait simplement transmettre les coordonnées de (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Une méthode de chargement des images bitmap de ressource

De nombreux exemples mis en ligne devrez charger des ressources de la bitmap. La méthode statique `BitmapExtensions` classe dans le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** solution contient une méthode pour vous aider :

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

Notez que le `Type` paramètre. Il peut s’agir du `Type` objet associé à n’importe quel type dans l’assembly qui stocke la ressource bitmap.

Cela `LoadBitmapResource` méthode sera utilisée dans tous les exemples suivants qui nécessitent des ressources de la bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>Étirer pour remplir un rectangle

Le `SKCanvas` classe définit également un [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode qui restitue la bitmap à un rectangle et un autre [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode qui restitue un sous-ensemble rectangulaire de la bitmap à un Rectangle :

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

Dans les deux cas, la bitmap est étirée pour remplir le rectangle nommé `dest`. Dans la deuxième méthode, le `source` rectangle vous permet de sélectionner un sous-ensemble de l’image bitmap. Le `dest` rectangle est relatif à l’appareil de sortie ; le `source` rectangle est relatif à l’image bitmap.

Le **remplir le Rectangle** page montre le premier de ces deux méthodes en affichant la bitmap même utilisé la même taille dans l’exemple précédent dans un rectangle en tant que la zone de dessin : 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Remarquez l’utilisation de la nouvelle `BitmapExtensions.LoadBitmapResource` méthode pour définir le `SKBitmap` champ. Le rectangle de destination est obtenu à partir de la [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) propriété de `SKImageInfo`, qui décrit la taille de la surface d’affichage :

[![Remplir le Rectangle](displaying-images/FillRectangle.png "remplir le Rectangle")](displaying-images/FillRectangle-Large.png#lightbox)

Il s’agit généralement _pas_ ce que vous voulez. L’image est déformée par étirée différemment dans le sens horizontal et vertical. Lorsque vous affichez une image bitmap dans un élément autre que sa taille en pixels, vous souhaitez généralement conserver les proportions d’origine de la bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Étirement tout en conservant les proportions

Étirement d’une image bitmap tout en conservant les proportions est un processus également appelé _mise à l’échelle uniforme_. Ce terme suggère une approche algorithmique. Une solution possible est indiquée dans le **mise à l’échelle uniforme** page :

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

Le `PaintSurface` Gestionnaire calcule un `scale` facteur qui est la valeur minimale du rapport de la largeur d’affichage et la hauteur et la hauteur et la largeur de la bitmap. Le `x` et `y` valeurs peuvent ensuite être calculées pour centrer la bitmap à l’échelle au sein de la largeur d’affichage et la hauteur. Le rectangle de destination a un coin supérieur gauche de `x` et `y` et un coin inférieur droit de ces valeurs ainsi que la mise à l’échelle de la largeur et hauteur de l’image bitmap :

[![Mise à l’échelle uniforme](displaying-images/UniformScaling.png "mise à l’échelle uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Activer le téléphone sur le côté pour voir l’image bitmap étirée pour cette zone :

[![Paysage de la mise à l’échelle de largeurs uniformes](displaying-images/UniformScaling-Landscape.png "paysage de la mise à l’échelle uniforme")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

L’avantage d’utiliser ce `scale` facteur devient évident lorsque vous souhaitez implémenter un algorithme légèrement différent. Supposons que vous souhaitez conserver les proportions de la bitmap, mais également remplir le rectangle de destination. Le seul cela est possible consiste à rogner la partie de l’image, mais vous pouvez implémenter cet algorithme en modifiant simplement `Math.Min` à `Math.Max` dans le code ci-dessus. Voici le résultat : 

[![Alternative de mise à l’échelle uniforme](displaying-images/UniformScaling-Alternative.png "alternative de mise à l’échelle uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Les proportions de la bitmap est conservée mais zones sur la gauche et droite de la bitmap sont rognées.

## <a name="a-versatile-bitmap-display-function"></a>Une fonction d’affichage polyvalent bitmap

Environnements de programmation XAML (par exemple, UWP et Xamarin.Forms) ont une fonctionnalité permettant de développer ou réduire la taille des images bitmap tout en conservant ses proportions. Bien que SkiaSharp n’inclut pas cette fonctionnalité, vous pouvez l’implémenter vous-même. Le `BitmapExtensions` classe inclus dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) application montre comment. La classe définit deux nouvelles `DrawBitmap` méthodes qui effectuent le calcul du rapport hauteur / largeur. Ces nouvelles méthodes sont des méthodes d’extension `SKCanvas`.

La nouvelle `DrawBitmap` méthodes incluent un paramètre de type `BitmapStretch`, une énumération définie dans le **BitmapExtensions.cs** fichier :

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

Le `None`, `Fill`, `Uniform`, et `UniformToFill` membres sont les mêmes que celles de la plateforme Windows universelle [ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) énumération. Xamarin.Forms similaire [ `Aspect` ](xref:Xamarin.Forms.Aspect) énumération définit les membres `Fill`, `AspectFit`, et `AspectFill`.

Le **mise à l’échelle uniforme** page illustrée ci-dessus centres la bitmap dans le rectangle, mais que vous souhaitiez autres options, telles que le positionnement de l’image bitmap sur le côté gauche ou droit du rectangle, ou le haut ou le bas. C’est l’objectif de la `BitmapAlignment` énumération :

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Paramètres d’alignement n’ont aucun effet lorsqu’il est utilisé avec `BitmapStretch.Fill`.

Le premier `DrawBitmap` fonction d’extension contient un rectangle de destination, mais aucun rectangle source. Valeurs par défaut sont définies afin que si vous souhaitez que l’image bitmap centré, il suffit de spécifier un `BitmapStretch` membre :

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

L’objectif principal de cette méthode consiste à calculer un facteur d’échelle nommé `scale` qui est ensuite appliqué à la largeur de la bitmap et la hauteur lors de l’appel le `CalculateDisplayRect` (méthode). Il s’agit de la méthode qui calcule un rectangle d’affichage de l’image bitmap en fonction de l’alignement horizontal et vertical :

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

Le `BitmapExtensions` classe contient un autre `DrawBitmap` méthode avec un rectangle source permettant de spécifier un sous-ensemble de l’image bitmap. Cette méthode est similaire à la première, sauf que le facteur d’échelle est calculé selon la `source` rectangle, puis appliquée à la `source` rectangle dans l’appel à `CalculateDisplayRect`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

La première de ces deux nouvelles `DrawBitmap` méthodes sont illustrées dans le **Modes de mise à l’échelle** page. Le fichier XAML contient trois `Picker` les éléments qui vous permettent de sélectionner les membres de la `BitmapStretch` et `BitmapAlignment` énumérations :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

Le fichier code-behind invalide simplement les `CanvasView` lorsqu’un `Picker` élément a été modifié. Le `PaintSurface` gestionnaire accède à trois `Picker` vues pour appeler le `DrawBitmap` méthode d’extension :

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

Voici certaines combinaisons d’options :

[![Modes de mise à l’échelle](displaying-images/ScalingModes.png "Modes de mise à l’échelle")](displaying-images/ScalingModes-Large.png#lightbox)

Le **Rectangle sous-ensemble** page a pratiquement le même fichier XAML en tant que **Modes de mise à l’échelle**, mais le fichier code-behind définit un sous-ensemble rectangulaire de la bitmap donné par le `SOURCE` champ : 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

Cette source de rectangle isole la tête de la monkey, comme indiqué dans ces captures d’écran :

[![Sous-ensemble de rectangle](displaying-images/RectangleSubset.png "sous-ensemble de Rectangle")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
