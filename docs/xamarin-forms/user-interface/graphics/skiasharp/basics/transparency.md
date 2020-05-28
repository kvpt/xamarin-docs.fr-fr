---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 735aae1b9d94865bd34450861bd6c57b08c420c2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134717"
---
# <a name="skiasharp-transparency"></a>Transparence SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu, la [`SKPaint`](xref:SkiaSharp.SKPaint) classe comprend une [`Color`](xref:SkiaSharp.SKPaint.Color) propriété de type [`SKColor`](xref:SkiaSharp.SKColor) . `SKColor`comprend un canal alpha, donc tout ce que vous colorez avec une `SKColor` valeur peut être partiellement transparent. 

Certaines transparences ont été démontrées dans l’article [**de base de SkiaSharp**](animation.md) . Cet article va plus loin dans la transparence pour combiner plusieurs objets dans une seule scène, une technique parfois appelée _fusion_. Des techniques de fusion plus avancées sont décrites dans les Articles de la section [**nuanceurs SkiaSharp**](../effects/shaders/index.md) .

Vous pouvez définir le niveau de transparence lorsque vous créez pour la première fois une couleur à l’aide du constructeur à quatre paramètres [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) :

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Une valeur alpha de 0 est entièrement transparente et une valeur alpha de 0xFF est entièrement opaque. Les valeurs comprises entre ces deux extrêmes créent des couleurs partiellement transparentes.

En outre, `SKColor` définit une [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) méthode pratique qui crée une couleur à partir d’une couleur existante, mais avec le niveau alpha spécifié :

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

L’utilisation d’un texte partiellement transparent est illustrée dans la page **code More** code de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Cette page atténue deux chaînes de texte dans et en sortie en incorporant la transparence dans les `SKColor` valeurs :

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

Le `transparency` champ est animé pour varier de 0 à 1, puis de nouveau dans un rythme sinusoïdal. La première chaîne de texte est affichée avec une valeur alpha calculée en soustrayant la `transparency` valeur de 1 :

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

La [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) méthode définit le composant alpha sur une couleur existante, qui est ici `SKColors.Blue` . La deuxième chaîne de texte utilise une valeur alpha calculée à partir de la `transparency` valeur elle-même :

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

L’animation alterne entre les deux mots, mécontentement l’utilisateur à « coder plus » (ou peut demander « plus de code ») :

[![Codez plus de code](transparency-images/CodeMoreCode.png "Codez plus de code")](transparency-images/CodeMoreCode-Large.png#lightbox)

Dans l’article précédent sur les [**concepts de base des bitmaps dans SkiaSharp**](bitmaps.md), vous avez vu comment afficher des bitmaps à l’aide de l’une des [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) méthodes de `SKCanvas` . Toutes les `DrawBitmap` méthodes incluent un `SKPaint` objet en tant que dernier paramètre. Par défaut, ce paramètre a la valeur `null` et vous pouvez l’ignorer. 

Vous pouvez également définir la `Color` propriété de cet `SKPaint` objet pour afficher une image bitmap avec un certain niveau de transparence. La définition d’un niveau de transparence dans la `Color` propriété de `SKPaint` vous permet de faire disparaître et sortant des bitmaps, ou de dissoudre une image bitmap dans une autre. 

La transparence de la bitmap est illustrée dans la page de **résolution de bitmap** . Le fichier XAML instancie un `SKCanvasView` et un `Slider` :

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

Le fichier code-behind charge deux ressources bitmap. Ces bitmaps n’ont pas la même taille, mais elles présentent les mêmes proportions :

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

La `Color` propriété de l' `SKPaint` objet est définie sur deux niveaux alpha complémentaires pour les deux bitmaps. Lors de l’utilisation `SKPaint` de avec des bitmaps, le reste de la valeur n’a pas d’importance `Color` . Tout ce qui est important est le canal alpha. Le code ici appelle simplement la `WithAlpha` méthode sur la valeur par défaut de la `Color` propriété.

Le déplacement des distants `Slider` entre une image bitmap et l’autre :

[![Dérésolution de bitmap](transparency-images/BitmapDissolve.png "Dérésolution de bitmap")](transparency-images/BitmapDissolve-Large.png#lightbox)

Dans les articles précédents, vous avez vu comment utiliser SkiaSharp pour dessiner du texte, des cercles, des ellipses, des rectangles arrondis et des bitmaps. L’étape suivante concerne les [lignes et les chemins SkiaSharp](../paths/index.md) dans lesquels vous allez apprendre à dessiner des lignes connectées dans un tracé graphique.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
