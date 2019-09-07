---
title: Transparence de SkiaSharp
description: Utiliser la transparence pour combiner plusieurs objets dans une scène unique.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 74335de66e74f6adc7c9488a1b78c31d36d03f14
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759406"
---
# <a name="skiasharp-transparency"></a>Transparence de SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu, le [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe inclut un [ `Color` ](xref:SkiaSharp.SKPaint.Color) propriété de type [ `SKColor` ](xref:SkiaSharp.SKColor). `SKColor` inclut un canal alpha, tout ce que vous la couleur avec une `SKColor` valeur peut être partiellement transparent. 

Certains transparence a été présentée dans le [ **Animation de base dans SkiaSharp** ](animation.md) article. Cet article va un peu plus loin dans la transparence pour combiner plusieurs objets dans une scène unique, une technique parfois appelée _de fusion_. Plusieurs techniques de fusion avancées sont décrits dans les articles de la [ **SkiaSharp nuanceurs** ](../effects/shaders/index.md) section.

Vous pouvez définir le niveau de transparence lorsque vous créez tout d’abord une couleur à l’aide du paramètre de quatre [ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) constructeur :

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Une valeur alpha de 0 est entièrement transparente et une valeur alpha de 0xFF est entièrement opaque. Valeurs comprises entre ces deux extrêmes créent des couleurs qui sont partiellement transparents.

En outre, `SKColor` définit une pratique [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) méthode qui crée une nouvelle couleur à partir d’une couleur existante, mais avec le niveau alpha spécifié :

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

L’utilisation de texte partiellement transparent est illustrée dans le **plus de Code** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple. Cette page fondu deux chaînes de texte dans et en incorporant la transparence dans le `SKColor` valeurs :

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

Le `transparency` champ est animé pour varier entre 0 et 1 et inversement dans un rythme sinusoïdal. La première chaîne de texte s’affiche avec une valeur alpha calculée en soustrayant le `transparency` valeur comprise entre 1 :

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

Le [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) méthode définit le composant alpha d’une couleur existante, qui est ici `SKColors.Blue`. La deuxième chaîne de texte utilise une valeur alpha calculée à partir de la `transparency` valeur lui-même :

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

L’animation alterne entre les deux mots, encourageant l’utilisateur à « code plus » (ou demander probablement « plus de code ») :

[![Code du Code plus](transparency-images/CodeMoreCode.png "Code davantage de Code")](transparency-images/CodeMoreCode-Large.png#lightbox)

Dans l’article précédent sur [ **principes fondamentaux de la Bitmap dans SkiaSharp**](bitmaps.md), vous avez vu comment afficher des bitmaps en utilisant l’une de le [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*) méthodes de `SKCanvas`. Tous les le `DrawBitmap` méthodes incluent un `SKPaint` objet comme dernier paramètre. Par défaut, ce paramètre est défini sur `null` et vous pouvez l’ignorer. 

Vous pouvez également définir le `Color` propriété de ce `SKPaint` objet pour afficher une image bitmap avec un certain degré de transparence. Définition d’un niveau de transparence dans le `Color` propriété du `SKPaint` vous permet de bitmaps en fondu ou disparaissent une bitmap dans un autre. 

Transparence de la bitmap est illustrée dans le **Bitmap disparaissent** page. Le fichier XAML instancie un `SKCanvasView` et un `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind charge deux ressources de bitmap. Ces bitmaps ne sont pas la même taille, mais ils sont les mêmes proportions :

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

Le `Color` propriété de la `SKPaint` objet est défini à deux niveaux alpha complémentaires pour les bitmaps de deux. Lorsque vous utilisez `SKPaint` des images bitmap, peu importe ce que le reste de la `Color` est de valeur. Tout ce qui est important est le canal alpha. Le code ici appelle simplement la `WithAlpha` méthode sur la valeur par défaut de la `Color` propriété.

Déplacer le `Slider` fondus entre un bitmap et l’autre :

[![Bitmap de fondu](transparency-images/BitmapDissolve.png "Bitmap fondu")](transparency-images/BitmapDissolve-Large.png#lightbox)

Dans les articles de plusieurs cours, vous avez vu comment utiliser SkiaSharp pour dessiner le texte, cercles, ellipses, rectangles arrondis et des bitmaps. L’étape suivante consiste [SkiaSharp lignes et chemins d’accès](../paths/index.md) dans lequel vous allez apprendre à dessiner des lignes connectées dans un chemin d’accès de graphiques.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
