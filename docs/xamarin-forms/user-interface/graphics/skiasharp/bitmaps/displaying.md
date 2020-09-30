---
title: Affichage des bitmaps SkiaSharp
description: Apprenez à afficher les bitmaps SkiaSharp en taille de pixel et à les développer pour remplir les rectangles tout en conservant les proportions.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1427b6f8461c74ded933fe562a7d17221790383a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562312"
---
# <a name="displaying-skiasharp-bitmaps"></a>Affichage des bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le sujet des bitmaps SkiaSharp a été introduit dans l’article **[concepts de base de la bitmap dans SkiaSharp](../basics/bitmaps.md)**. Cet article a montré trois façons de charger des bitmaps et de trois façons d’afficher des bitmaps. Cet article passe en revue les techniques permettant de charger des bitmaps et va plus loin dans l’utilisation des `DrawBitmap` méthodes de `SKCanvas` .

![Affichage de l’exemple](displaying-images/DisplayingSample.png "Affichage de l’exemple")

Les `DrawBitmapLattice` `DrawBitmapNinePatch` méthodes et sont présentées dans l’article **[affichage segmenté des bitmaps SkiaSharp](segmented.md)**.

Les exemples de cette page proviennent de l’application **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . Dans la page d’hébergement de cette application, choisissez **SkiaSharp bitmaps**, puis accédez à la section **affichage des bitmaps** .

## <a name="loading-a-bitmap"></a>Chargement d’une image bitmap

Une image bitmap utilisée par une application SkiaSharp provient généralement de l’une des trois sources suivantes :

- Depuis Internet
- À partir d’une ressource incorporée dans l’exécutable
- À partir de la bibliothèque de photos de l’utilisateur

Il est également possible pour une application SkiaSharp de créer une image bitmap, puis de la dessiner ou de définir les bits de la bitmap par algorithme. Ces techniques sont décrites dans les articles **[création et dessin sur les bitmaps SkiaSharp](drawing.md)** et **[accès aux pixels de la bitmap SkiaSharp](pixel-bits.md)**.

Dans les trois exemples de code suivants illustrant le chargement d’une image bitmap, la classe est supposée contenir un champ de type `SKBitmap` :

```csharp
SKBitmap bitmap;
```

Comme l’indique l’article **[concepts de base de la bitmap dans SkiaSharp](../basics/bitmaps.md)** , la meilleure façon de charger une image bitmap sur Internet est de la [`HttpClient`](xref:System.Net.Http.HttpClient) classe. Une seule instance de la classe peut être définie en tant que champ :

```csharp
HttpClient httpClient = new HttpClient();
```

Lorsque `HttpClient` vous utilisez avec des applications iOS et Android, vous devez définir les propriétés du projet, comme décrit dans les documents sur le **[protocole TLS (Transport Layer Security) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Le code qui utilise `HttpClient` souvent implique l' `await` opérateur, donc il doit résider dans une `async` méthode :

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

Notez que l' `Stream` objet obtenu à partir de `GetStreamAsync` est copié dans un `MemoryStream` . Android n’autorise pas le `Stream` traitement du à partir du `HttpClient` thread principal, sauf dans les méthodes asynchrones. 

Le [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) effectue beaucoup de travail : l' `Stream` objet qui lui est passé fait référence à un bloc de mémoire contenant l’intégralité d’une bitmap dans l’un des formats de fichier bitmap courants, généralement JPEG, png ou gif. La `Decode` méthode doit déterminer le format, puis décoder le fichier bitmap au format de bitmap interne de SkiaSharp.

Une fois que votre code a appelé `SKBitmap.Decode` , il est probable `CanvasView` que le `PaintSurface` gestionnaire peut afficher l’image bitmap nouvellement chargée.

La deuxième façon de charger une image bitmap consiste à inclure la bitmap en tant que ressource incorporée dans la bibliothèque de .NET Standard référencée par les projets de plateforme individuels. Un ID de ressource est passé à la [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) méthode. Cet ID de ressource est constitué du nom de l’assembly, du nom du dossier et du nom de fichier de la ressource, séparés par des points :

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

Les fichiers bitmap peuvent également être stockés en tant que ressources dans le projet de plateforme individuel pour iOS, Android et le plateforme Windows universelle (UWP). Toutefois, le chargement de ces bitmaps requiert du code qui se trouve dans le projet de plateforme.

Une troisième approche pour obtenir une image bitmap provient de la bibliothèque d’images de l’utilisateur. Le code suivant utilise un service de dépendance inclus dans l’application **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . La bibliothèque de .NET Standard **SkiaSharpFormsDemo** inclut l' `IPhotoLibrary` interface, tandis que chacun des projets de plateforme contient une `PhotoLibrary` classe qui implémente cette interface.

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

En règle générale, ce code invalide également le `CanvasView` afin que le `PaintSurface` Gestionnaire puisse afficher la nouvelle bitmap.

La `SKBitmap` classe définit plusieurs propriétés utiles, notamment [`Width`](xref:SkiaSharp.SKBitmap.Width) et [`Height`](xref:SkiaSharp.SKBitmap.Height) , qui révèlent les dimensions de pixel de la bitmap, ainsi que de nombreuses méthodes, y compris les méthodes permettant de créer des bitmaps, de les copier et d’exposer les bits de pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Afficher dans les dimensions en pixels

La [`Canvas`](xref:SkiaSharp.SKCanvas) classe SkiaSharp définit quatre `DrawBitmap` méthodes. Ces méthodes permettent d’afficher les bitmaps de deux manières fondamentalement différentes : 

- La spécification d’une `SKPoint` valeur (ou de `x` valeurs séparées et `y` ) permet d’afficher l’image bitmap dans ses dimensions en pixels. Les pixels de la bitmap sont mappés directement aux pixels de l’affichage vidéo.
- La spécification d’un rectangle entraîne l’étirement de la bitmap à la taille et à la forme du rectangle. 

Vous affichez une bitmap dans ses dimensions en pixels à l’aide [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) de avec un `SKPoint` paramètre ou [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) avec des `x` paramètres et distincts `y` :

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Ces deux méthodes sont fonctionnellement identiques. Le point spécifié indique l’emplacement de l’angle supérieur gauche de l’image bitmap par rapport au canevas. Étant donné que la résolution en pixels des appareils mobiles est tellement élevée, les bitmaps plus petites sont généralement très petites sur ces appareils.

Le `SKPaint` paramètre facultatif vous permet d’afficher la bitmap à l’aide de la transparence. Pour ce faire, créez un `SKPaint` objet et affectez `Color` à la propriété une `SKColor` valeur avec un canal alpha inférieur à 1. Par exemple :

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

Le 0x80 passé comme dernier argument indique une transparence de 50%. Vous pouvez également définir un canal alpha sur l’une des couleurs prédéfinies :

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Toutefois, la couleur elle-même n’est pas pertinente. Seul le canal alpha est examiné lorsque vous utilisez l' `SKPaint` objet dans un `DrawBitmap` appel.

L' `SKPaint` objet joue également un rôle lors de l’affichage de bitmaps à l’aide de modes de fusion ou d’effets de filtre. Celles-ci sont illustrées dans les articles [SkiaSharp la composition et les modes de fusion](../effects/blend-modes/index.md) et les filtres d' [image SkiaSharp](../effects/image-filters.md).

La page **dimensions en pixels** de l’exemple de programme **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** affiche une ressource bitmap de 320 pixels de large par 240 pixels de haut :

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

Le `PaintSurface` Gestionnaire Centre la bitmap en calculant les `x` `y` valeurs et en fonction des dimensions de pixel de la surface d’affichage et des dimensions en pixels de l’image bitmap :

[![Dimensions de pixel](displaying-images/PixelDimensions.png "Dimensions de pixel")](displaying-images/PixelDimensions-Large.png#lightbox)

Si l’application souhaite afficher le bitmap dans son coin supérieur gauche, il lui suffit de passer des coordonnées de (0,0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Méthode de chargement des bitmaps de ressources

La plupart des exemples de la sortie doivent charger des ressources bitmap. La `BitmapExtensions` classe statique de la solution **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** contient une méthode pour vous aider :

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

Notez le `Type` paramètre. Il peut s’agir de l' `Type` objet associé à n’importe quel type dans l’assembly qui stocke la ressource bitmap.

Cette `LoadBitmapResource` méthode sera utilisée dans tous les exemples suivants qui requièrent des ressources bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>Étirement pour remplir un rectangle

La `SKCanvas` classe définit également une [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode qui restitue l’image bitmap dans un rectangle, et une autre [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode qui restitue un sous-ensemble rectangulaire de l’image bitmap dans un rectangle :

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

Dans les deux cas, la bitmap est étirée pour remplir le rectangle nommé `dest` . Dans la deuxième méthode, le `source` rectangle vous permet de sélectionner un sous-ensemble de l’image bitmap. Le `dest` rectangle est relatif à l’appareil de sortie ; le `source` rectangle est relatif à l’image bitmap.

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

Notez l’utilisation de la nouvelle `BitmapExtensions.LoadBitmapResource` méthode pour définir le `SKBitmap` champ. Le rectangle de destination est obtenu à partir de la [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) propriété de `SKImageInfo` , qui décrit la taille de la surface d’affichage :

[![Rectangle de remplissage](displaying-images/FillRectangle.png "Rectangle de remplissage")](displaying-images/FillRectangle-Large.png#lightbox)

Ce n’est généralement _pas_ ce que vous voulez. L’image est déformée en étant étirée différemment dans les directions horizontale et verticale. Lorsque vous affichez une image bitmap dans une autre valeur que sa taille de pixel, vous souhaitez généralement conserver les proportions d’origine de la bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>Extension en conservant les proportions

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

Le `PaintSurface` Gestionnaire calcule un `scale` facteur qui est la valeur minimale du rapport entre la largeur et la hauteur de l’affichage et la largeur et la hauteur de la bitmap. Les `x` `y` valeurs et peuvent ensuite être calculées pour centrer le bitmap mis à l’échelle dans la largeur et la hauteur d’affichage. Le rectangle de destination a un coin supérieur gauche de `x` et `y` un coin inférieur droit de ces valeurs plus la largeur et la hauteur mises à l’échelle de l’image bitmap :

[![Mise à l’échelle uniforme](displaying-images/UniformScaling.png "Mise à l’échelle uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Tournez le téléphone sur le côté pour voir l’image bitmap étirée sur cette zone :

[![Paysage de mise à l’échelle uniforme](displaying-images/UniformScaling-Landscape.png "Paysage de mise à l’échelle uniforme")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

L’avantage de l’utilisation de ce `scale` facteur devient évident lorsque vous souhaitez implémenter un algorithme légèrement différent. Supposons que vous voulez conserver les proportions de la bitmap, mais également remplir le rectangle de destination. Le seul moyen possible est de rogner une partie de l’image, mais vous pouvez implémenter cet algorithme simplement en remplaçant `Math.Min` `Math.Max` dans le code ci-dessus. Voici le résultat : 

[![Alternative à la mise à l’échelle uniforme](displaying-images/UniformScaling-Alternative.png "Alternative à la mise à l’échelle uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Les proportions de la bitmap sont conservées, mais les zones à gauche et à droite de la bitmap sont rognées.

## <a name="a-versatile-bitmap-display-function"></a>Fonction d’affichage bitmap polyvalente

Les environnements de programmation XAML (tels que UWP et Xamarin.Forms ) disposent d’une fonction permettant de développer ou de réduire la taille des bitmaps tout en conservant leurs proportions. Bien que SkiaSharp n’inclue pas cette fonctionnalité, vous pouvez l’implémenter vous-même. La `BitmapExtensions` classe incluse dans l’application [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) montre comment. La classe définit deux nouvelles `DrawBitmap` méthodes qui effectuent le calcul des proportions. Ces nouvelles méthodes sont des méthodes d’extension de `SKCanvas` .

Les nouvelles `DrawBitmap` méthodes incluent un paramètre de type `BitmapStretch` , une énumération définie dans le fichier **BitmapExtensions.cs** :

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

Les `None` membres,, `Fill` `Uniform` et `UniformToFill` sont les mêmes que ceux de l' [`Stretch`](/uwp/api/Windows.UI.Xaml.Media.Stretch) énumération UWP. L' Xamarin.Forms [`Aspect`](xref:Xamarin.Forms.Aspect) énumération similaire définit les membres `Fill` , `AspectFit` et `AspectFill` .

La page de **mise à l’échelle uniforme** présentée ci-dessus Centre l’image bitmap dans le rectangle, mais vous pouvez avoir besoin d’autres options, telles que le positionnement de l’image bitmap à gauche ou à droite du rectangle, ou le haut ou le bas. C’est l’objectif de l' `BitmapAlignment` énumération :

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Les paramètres d’alignement n’ont aucun effet lorsqu’ils sont utilisés avec `BitmapStretch.Fill` .

La première `DrawBitmap` fonction d’extension contient un rectangle de destination mais aucun rectangle source. Les valeurs par défaut sont définies de sorte que si vous souhaitez que la bitmap soit centrée, vous ne devez spécifier qu’un `BitmapStretch` membre :

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

L’objectif principal de cette méthode est de calculer un facteur d’échelle nommé `scale` qui est ensuite appliqué à la largeur et à la hauteur de la bitmap lors de l’appel de la `CalculateDisplayRect` méthode. Il s’agit de la méthode qui calcule un rectangle pour l’affichage de l’image bitmap en fonction de l’alignement horizontal et vertical :

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

La `BitmapExtensions` classe contient une `DrawBitmap` méthode supplémentaire avec un rectangle source pour spécifier un sous-ensemble de l’image bitmap. Cette méthode est similaire à la première, à ceci près que le facteur d’échelle est calculé en fonction du `source` Rectangle, puis appliqué au `source` rectangle dans l’appel à `CalculateDisplayRect` :

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

La première de ces deux nouvelles `DrawBitmap` méthodes est présentée dans la page **modes de mise à l’échelle** . Le fichier XAML contient trois `Picker` éléments qui vous permettent de sélectionner les membres `BitmapStretch` des `BitmapAlignment` énumérations et :

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

Le fichier code-behind invalide simplement le `CanvasView` quand un `Picker` élément a changé. Le `PaintSurface` Gestionnaire accède aux trois `Picker` vues pour l’appel de la `DrawBitmap` méthode d’extension :

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

Voici quelques combinaisons d’options :

[![Modes de mise à l’échelle](displaying-images/ScalingModes.png "Modes de mise à l’échelle")](displaying-images/ScalingModes-Large.png#lightbox)

La page du **sous-ensemble rectangle** a pratiquement le même fichier XAML que les **modes de mise à l’échelle**, mais le fichier code-behind définit un sous-ensemble rectangulaire de la bitmap donnée par le `SOURCE` champ : 

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

Ce rectangle source isole la tête du singe, comme indiqué dans les captures d’écran suivantes :

[![Sous-ensemble rectangle](displaying-images/RectangleSubset.png "Sous-ensemble rectangle")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)