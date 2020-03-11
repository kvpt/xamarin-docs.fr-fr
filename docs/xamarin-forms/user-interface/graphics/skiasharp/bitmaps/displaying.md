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
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915833"
---
# <a name="displaying-skiasharp-bitmaps"></a>Afficher des bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le sujet des bitmaps SkiaSharp a été introduit dans l’article **[concepts de base de la bitmap dans SkiaSharp](../basics/bitmaps.md)** . Cet article vous a montré les trois façons de bitmaps de charge et de trois façons d’afficher des bitmaps. Cet article passe en revue les techniques permettant de charger des bitmaps et va plus loin dans l’utilisation des méthodes `DrawBitmap` de `SKCanvas`.

![Affichage de l’exemple](displaying-images/DisplayingSample.png "Affichage de l’exemple")

Les méthodes `DrawBitmapLattice` et `DrawBitmapNinePatch` sont présentées dans l’article **[affichage segmenté des bitmaps SkiaSharp](segmented.md)** .

Les exemples de cette page proviennent de l’application **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . Dans la page d’hébergement de cette application, choisissez **SkiaSharp bitmaps**, puis accédez à la section **affichage des bitmaps** .

## <a name="loading-a-bitmap"></a>Le chargement d’une image bitmap

Une image bitmap utilisée par une application de SkiaSharp généralement provient d’une des trois sources différentes :

- Depuis Internet
- À partir d’une ressource incorporée dans le fichier exécutable
- À partir de la bibliothèque de photos de l’utilisateur

Il est également possible pour une application SkiaSharp créer une nouvelle image bitmap, puis dessiner dessus ou définir les bits du bitmap par algorithme. Ces techniques sont décrites dans les articles **[création et dessin sur les bitmaps SkiaSharp](drawing.md)** et **[accès aux pixels de la bitmap SkiaSharp](pixel-bits.md)** .

Dans les trois exemples de code suivants illustrant le chargement d’une image bitmap, la classe est supposée contenir un champ de type `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Comme l’indique l’article **[concepts de base de la bitmap dans SkiaSharp](../basics/bitmaps.md)** , la meilleure façon de charger une image bitmap sur Internet consiste à utiliser la classe [`HttpClient`](xref:System.Net.Http.HttpClient) . Une seule instance de la classe peut être définie en tant que champ :

```csharp
HttpClient httpClient = new HttpClient();
```

Quand vous utilisez des `HttpClient` avec des applications iOS et Android, vous pouvez définir les propriétés du projet, comme décrit dans les documents sur le **[protocole TLS (Transport Layer Security) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Le code qui utilise `HttpClient` implique souvent l’opérateur `await`, donc il doit résider dans une méthode `async` :

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

Notez que l’objet `Stream` obtenu à partir d' `GetStreamAsync` est copié dans un `MemoryStream`. Android n’autorise pas le traitement des `Stream` de `HttpClient` par le thread principal, sauf dans les méthodes asynchrones. 

L' [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) effectue beaucoup de travail : l’objet `Stream` qui lui est passé fait référence à un bloc de mémoire contenant l’intégralité d’une bitmap dans l’un des formats de fichier bitmap courants, généralement JPEG, png ou gif. La méthode `Decode` doit déterminer le format, puis décoder le fichier bitmap au format de bitmap interne de SkiaSharp.

Une fois que votre code a appelé `SKBitmap.Decode`, il risque d’invalider le `CanvasView` afin que le gestionnaire de `PaintSurface` puisse afficher l’image bitmap nouvellement chargée.

En incluant l’image bitmap en tant que ressource incorporée dans la bibliothèque .NET Standard la deuxième façon de charger une image bitmap est référencée par les projets de plateforme individuels. Un ID de ressource est passé à la méthode [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) . Cet ID de ressource comprend le nom de l’assembly, le nom du dossier et le nom de la ressource séparée par des points :

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

Une troisième approche à l’obtention d’une image bitmap est à partir de la bibliothèque d’images de l’utilisateur. Le code suivant utilise un service de dépendance inclus dans l’application **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . La bibliothèque de .NET Standard **SkiaSharpFormsDemo** inclut l’interface `IPhotoLibrary`, tandis que chacun des projets de plateforme contient une classe `PhotoLibrary` qui implémente cette interface.

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

En règle générale, ce code invalide également la `CanvasView` afin que le gestionnaire de `PaintSurface` puisse afficher la nouvelle bitmap.

La classe `SKBitmap` définit plusieurs propriétés utiles, notamment [`Width`](xref:SkiaSharp.SKBitmap.Width) et [`Height`](xref:SkiaSharp.SKBitmap.Height), qui révèlent les dimensions de pixel de la bitmap, ainsi que de nombreuses méthodes, notamment les méthodes permettant de créer des bitmaps, de les copier et d’exposer les bits de pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Affichage dans les dimensions en pixels

La classe [`Canvas`](xref:SkiaSharp.SKCanvas) SkiaSharp définit quatre méthodes `DrawBitmap`. Ces méthodes permettent de bitmaps à afficher deux fondamentalement différentes manières : 

- La spécification d’une valeur `SKPoint` (ou de valeurs `x` et `y` distinctes) affiche la bitmap dans ses dimensions en pixels. Les pixels de l’image bitmap sont mappés directement aux pixels de l’affichage vidéo.
- Si vous spécifiez un rectangle, l’image bitmap pour l’étendre à la taille et la forme du rectangle. 

Vous affichez une bitmap dans ses dimensions en pixels à l’aide de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) avec un paramètre `SKPoint` ou [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) avec des paramètres `x` et `y` séparés :

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Ces deux méthodes sont fonctionnellement identiques. Le point spécifié indique l’emplacement de l’angle supérieur gauche de la bitmap par rapport à la zone de dessin. Étant donné que la résolution de pixel des appareils mobiles est très élevée, plus petits bitmaps s’affichent généralement assez petits sur ces appareils.

Le paramètre facultatif `SKPaint` vous permet d’afficher la bitmap à l’aide de la transparence. Pour ce faire, créez un objet `SKPaint` et affectez à la propriété `Color` une valeur de `SKColor` avec un canal alpha inférieur à 1. Par exemple :

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

Les 0 x 80 passé comme dernier argument indique la transparence de 50 %. Vous pouvez également définir un canal alpha sur l’une des couleurs prédéfinies :

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Toutefois, la couleur elle-même est sans importance. Seul le canal alpha est examiné lorsque vous utilisez l’objet `SKPaint` dans un appel `DrawBitmap`.

L’objet `SKPaint` joue également un rôle lors de l’affichage de bitmaps à l’aide de modes de fusion ou d’effets de filtre. Celles-ci sont illustrées dans les articles [SkiaSharp la composition et les modes de fusion](../effects/blend-modes/index.md) et les filtres d' [image SkiaSharp](../effects/image-filters.md).

La page **dimensions en pixels** de l’exemple de programme **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** affiche une ressource bitmap de 320 pixels de large par 240 pixels de haut :

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

Le gestionnaire de `PaintSurface` Centre la bitmap en calculant les valeurs de `x` et de `y` en fonction des dimensions de pixel de la surface d’affichage et des dimensions en pixels de l’image bitmap :

[![Dimensions de pixel](displaying-images/PixelDimensions.png "Dimensions de pixel")](displaying-images/PixelDimensions-Large.png#lightbox)

Si l’application souhaite afficher l’image bitmap dans son coin supérieur gauche, il serait simplement transmettre les coordonnées de (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Une méthode de chargement des images bitmap de ressource

De nombreux exemples mis en ligne devrez charger des ressources de la bitmap. La classe `BitmapExtensions` statique de la solution **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** contient une méthode pour vous aider :

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

Notez le paramètre `Type`. Il peut s’agir de l’objet `Type` associé à n’importe quel type dans l’assembly qui stocke la ressource bitmap.

Cette méthode `LoadBitmapResource` sera utilisée dans tous les exemples suivants qui requièrent des ressources bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>Étirer pour remplir un rectangle

La classe `SKCanvas` définit également une méthode [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) qui restitue l’image bitmap dans un rectangle, et une autre méthode [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) qui restitue un sous-ensemble rectangulaire de l’image bitmap dans un rectangle :

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

Dans les deux cas, la bitmap est étirée pour remplir le rectangle nommé `dest`. Dans la deuxième méthode, le rectangle de `source` vous permet de sélectionner un sous-ensemble de l’image bitmap. Le rectangle de `dest` est relatif au périphérique de sortie ; le rectangle de `source` est relatif à l’image bitmap.

La page **remplissage de rectangle** illustre la première de ces deux méthodes en affichant la même bitmap utilisée dans l’exemple précédent dans un rectangle de la même taille que la zone de dessin : 

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

Notez l’utilisation de la nouvelle méthode `BitmapExtensions.LoadBitmapResource` pour définir le champ `SKBitmap`. Le rectangle de destination est obtenu à partir de la propriété [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) de `SKImageInfo`, qui décrit la taille de la surface d’affichage :

[![Rectangle de remplissage](displaying-images/FillRectangle.png "Rectangle de remplissage")](displaying-images/FillRectangle-Large.png#lightbox)

Ce n’est généralement _pas_ ce que vous voulez. L’image est déformée par étirée différemment dans le sens horizontal et vertical. Lorsque vous affichez une image bitmap dans un élément autre que sa taille en pixels, vous souhaitez généralement conserver les proportions d’origine de la bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Étirement tout en conservant les proportions

L’étirement d’une bitmap tout en conservant les proportions est un processus également appelé _mise à l’échelle uniforme_. Ce terme suggère une approche algorithmique. Une solution possible est illustrée dans la page de **mise à l’échelle uniforme** :

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

Le gestionnaire de `PaintSurface` calcule un facteur de `scale` qui est la valeur minimale du rapport entre la largeur et la hauteur de l’affichage et la largeur et la hauteur de la bitmap. Les valeurs `x` et `y` peuvent ensuite être calculées pour centrer le bitmap mis à l’échelle dans la largeur et la hauteur de l’affichage. Le rectangle de destination a un coin supérieur gauche de `x` et `y` et un coin inférieur droit de ces valeurs plus la largeur et la hauteur mises à l’échelle de l’image bitmap :

[![Mise à l’échelle uniforme](displaying-images/UniformScaling.png "Mise à l’échelle uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Activer le téléphone sur le côté pour voir l’image bitmap étirée pour cette zone :

[![Paysage de mise à l’échelle uniforme](displaying-images/UniformScaling-Landscape.png "Paysage de mise à l’échelle uniforme")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

L’avantage de cette `scale` facteur devient évident lorsque vous souhaitez implémenter un algorithme légèrement différent. Supposons que vous souhaitez conserver les proportions de la bitmap, mais également remplir le rectangle de destination. Le seul moyen possible est de rogner une partie de l’image, mais vous pouvez implémenter cet algorithme simplement en modifiant `Math.Min` pour `Math.Max` dans le code ci-dessus. Voici le résultat : 

[![Alternative à la mise à l’échelle uniforme](displaying-images/UniformScaling-Alternative.png "Alternative à la mise à l’échelle uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Les proportions de la bitmap est conservée mais zones sur la gauche et droite de la bitmap sont rognées.

## <a name="a-versatile-bitmap-display-function"></a>Une fonction d’affichage polyvalent bitmap

Environnements de programmation XAML (par exemple, UWP et Xamarin.Forms) ont une fonctionnalité permettant de développer ou réduire la taille des images bitmap tout en conservant ses proportions. Bien que SkiaSharp n’inclut pas cette fonctionnalité, vous pouvez l’implémenter vous-même. La classe `BitmapExtensions` incluse dans l’application [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) montre comment procéder. La classe définit deux nouvelles méthodes de `DrawBitmap` qui effectuent le calcul des proportions. Ces nouvelles méthodes sont des méthodes d’extension de `SKCanvas`.

Les nouvelles méthodes `DrawBitmap` incluent un paramètre de type `BitmapStretch`, une énumération définie dans le fichier **BitmapExtensions.cs** :

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

Les membres `None`, `Fill`, `Uniform`et `UniformToFill` sont les mêmes que ceux de l’énumération UWP [`Stretch`](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) . L’énumération Xamarin. Forms similaire [`Aspect`](xref:Xamarin.Forms.Aspect) définit les membres `Fill`, `AspectFit`et `AspectFill`.

La page de **mise à l’échelle uniforme** présentée ci-dessus Centre l’image bitmap dans le rectangle, mais vous pouvez avoir besoin d’autres options, telles que le positionnement de l’image bitmap à gauche ou à droite du rectangle, ou le haut ou le bas. C’est l’objectif de l’énumération `BitmapAlignment` :

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Les paramètres d’alignement n’ont aucun effet lorsqu’ils sont utilisés avec `BitmapStretch.Fill`.

La première fonction d’extension `DrawBitmap` contient un rectangle de destination mais aucun rectangle source. Les valeurs par défaut sont définies de sorte que si vous souhaitez que la bitmap soit centrée, vous devez uniquement spécifier un membre `BitmapStretch` :

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

L’objectif principal de cette méthode est de calculer un facteur d’échelle nommé `scale` qui est ensuite appliqué à la largeur et à la hauteur de la bitmap lors de l’appel de la méthode `CalculateDisplayRect`. Il s’agit de la méthode qui calcule un rectangle d’affichage de l’image bitmap en fonction de l’alignement horizontal et vertical :

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

La classe `BitmapExtensions` contient une méthode `DrawBitmap` supplémentaire avec un rectangle source pour spécifier un sous-ensemble de l’image bitmap. Cette méthode est similaire à la première, à ceci près que le facteur d’échelle est calculé en fonction du rectangle de `source`, puis appliqué au rectangle `source` dans l’appel à `CalculateDisplayRect`:

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

La première de ces deux nouvelles méthodes de `DrawBitmap` est présentée dans la page **modes de mise à l’échelle** . Le fichier XAML contient trois éléments `Picker` qui vous permettent de sélectionner les membres du `BitmapStretch` et de `BitmapAlignment` énumérations :

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

Le fichier code-behind invalide simplement le `CanvasView` lorsqu’un élément `Picker` a changé. Le gestionnaire de `PaintSurface` accède aux trois vues `Picker` pour l’appel de la méthode d’extension `DrawBitmap` :

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

La page du **sous-ensemble rectangle** a pratiquement le même fichier XAML que les **modes de mise à l’échelle**, mais le fichier code-behind définit un sous-ensemble rectangulaire de la bitmap donnée par le champ `SOURCE` : 

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

[![Sous-ensemble rectangle](displaying-images/RectangleSubset.png "Sous-ensemble rectangle")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
