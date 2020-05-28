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
ms.openlocfilehash: f3a5a581ffb4ca2acf1d4209b8b7a744f0daa5eb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128045"
---
# <a name="path-effects-in-skiasharp"></a>Effets du chemin dans SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez les divers effets de tracés qui permettent d’utiliser les chemins d’accès pour le décontour et le remplissage_

Un *effet de chemin d’accès* est une instance de la [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) classe créée avec l’une des huit méthodes de création statiques définies par la classe. L' `SKPathEffect` objet est ensuite défini sur la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propriété d’un [`SKPaint`](xref:SkiaSharp.SKPaint) objet pour divers effets intéressants, par exemple, en traçant une ligne avec un petit chemin répliqué :

![Exemple de chaîne liée](effects-images/patheffectsample.png)

Les effets de tracés vous permettent d’effectuer les opérations suivantes :

- Trait d’une ligne avec des points et des tirets
- Trait d’une ligne avec un chemin d’accès rempli
- Remplir une zone avec des lignes de hachurage
- Remplir une zone avec un tracé en mosaïque
- Arrondir les angles aigus
- Ajouter une « gigue » aléatoire aux lignes et aux courbes

En outre, vous pouvez combiner deux ou plusieurs effets de tracés.

Cet article montre également comment utiliser la [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) méthode de `SKPaint` pour convertir un chemin dans un autre chemin d’accès en appliquant les propriétés de `SKPaint` , y compris `StrokeWidth` et `PathEffect` . Cela entraîne des techniques intéressantes, telles que l’obtention d’un chemin d’accès qui est un contour d’un autre chemin d’accès. `GetFillPath`est également utile pour la connexion avec des effets de chemin d’accès.

## <a name="dots-and-dashes"></a>Points et tirets

L’utilisation de la [`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) méthode a été décrite dans les [**points et les tirets**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)de l’article. Le premier argument de la méthode est un tableau qui contient un nombre pair de deux valeurs ou plus, en alternant entre les longueurs des tirets et les longueurs d’intervalle entre les tirets :

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Ces valeurs ne sont *pas* relatives à la largeur du trait. Par exemple, si la largeur du trait est 10 et que vous souhaitez une ligne composée de tirets carrés et de trous de carrés, définissez le `intervals` tableau sur {10, 10}. L' `phase` argument indique où commence la ligne dans le motif des tirets. Dans cet exemple, si vous souhaitez que la ligne commence par l’intervalle carré, définissez `phase` sur 10.

Les extrémités des tirets sont affectées par la `StrokeCap` propriété de `SKPaint` . Pour les largeurs de traits larges, il est très courant d’affecter à cette propriété `SKStrokeCap.Round` la valeur pour arrondir les extrémités des tirets. Dans ce cas, les valeurs du `intervals` tableau n’incluent *pas* la longueur supplémentaire résultant de l’arrondi. Cela signifie qu’un point circulaire requiert la spécification d’une largeur de zéro. Pour une largeur de trait de 10, pour créer une ligne avec des points circulaires et des intervalles entre les points du même diamètre, utilisez un `intervals` tableau de {0, 20}.

La page de **texte en pointillé animée** est semblable à la page de **texte avec contour** décrite dans l’article [**intégration de texte et de graphiques**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) , car elle affiche des caractères de texte avec contour en affectant à la `Style` propriété de l’objet la valeur `SKPaint` `SKPaintStyle.Stroke` . En outre, le **texte en pointillé animé** utilise `SKPathEffect.CreateDash` pour donner à ce contour une apparence en pointillés, et le programme anime également l' `phase` argument de la `SKPathEffect.CreateDash` méthode pour que les points semblent se déplacer autour des caractères de texte. Voici la page en mode paysage :

[![Capture d’écran triple de la page de texte en pointillé animée](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

La [`AnimatedDottedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe commence par définir des constantes et substitue également les `OnAppearing` `OnDisappearing` méthodes et pour l’animation :

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

Le `PaintSurface` Gestionnaire commence par créer un `SKPaint` objet pour afficher le texte. La `TextSize` propriété est ajustée en fonction de la largeur de l’écran :

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

Vers la fin de la méthode, la `SKPathEffect.CreateDash` méthode est appelée à l’aide du `dashArray` défini comme un champ et de la `phase` valeur animée. L' `SKPathEffect` instance est définie sur la `PathEffect` propriété de l' `SKPaint` objet pour afficher le texte.

Vous pouvez également définir l' `SKPathEffect` objet sur l' `SKPaint` objet avant de mesurer le texte et le centrer sur la page. Dans ce cas, toutefois, les points et les tirets animés entraînent une variation de la taille du texte rendu et le texte a tendance à vibrer un peu. (Essayez !)

Vous remarquerez également que, comme les points animés entourent les caractères de texte, il y a un certain point dans chaque courbe fermée où les points semblent apparaître et ne pas exister. C’est là que le chemin d’accès qui définit la structure des caractères commence et se termine. Si la longueur du chemin d’accès n’est pas un multiple entier de la longueur du modèle tiret (dans ce cas, 20 pixels), seule une partie de ce modèle peut être contenue à la fin du chemin d’accès.

Il est possible d’ajuster la longueur du modèle des tirets pour l’ajuster à la longueur du chemin d’accès, mais cela nécessite de déterminer la longueur du chemin d’accès, une technique qui est décrite dans les [**informations sur le chemin d’accès et l’énumération**](information.md)de l’article.

Le programme **Morph point/Dash** anime le motif des tirets, de sorte que les tirets semblent être divisés en points, qui sont combinés pour former à nouveau les tirets :

[![Capture d’écran triple de la page matricielle en pointillés](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

La [`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe substitue les `OnAppearing` méthodes et de la `OnDisappearing` même façon que le programme précédent, mais la classe définit l' `SKPaint` objet en tant que champ :

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

Le `PaintSurface` Gestionnaire crée un tracé elliptique en fonction de la taille de la page et exécute une longue section de code qui définit les `dashArray` variables et `phase` . Étant donné que la variable animée est comprise `t` entre 0 et 1, les `if` blocs divisent cette durée en quatre trimestres et, dans chacun de ces trimestres, sont `tsub` également compris entre 0 et 1. Tout au plus, le programme crée le `SKPathEffect` et lui affecte l' `SKPaint` objet pour le dessin.

## <a name="from-path-to-path"></a>Du chemin vers le chemin

La [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) méthode de `SKPaint` convertit un chemin en un autre en fonction des paramètres de l' `SKPaint` objet. Pour voir comment cela fonctionne, remplacez l' `canvas.DrawPath` appel dans le programme précédent par le code suivant :

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

Dans ce nouveau code, l' `GetFillPath` appel convertit `ellipsePath` (qui est simplement un ovale) en `newPath` , qui est ensuite affiché avec `newPaint` . L' `newPaint` objet est créé avec tous les paramètres de propriété par défaut, sauf que la `Style` propriété est définie en fonction de la valeur de retour booléenne de `GetFillPath` .

Les éléments visuels sont identiques, à l’exception de la couleur, qui est définie dans `ellipsePaint` mais pas `newPaint` . Au lieu d’avoir une ellipse simple définie dans `ellipsePath` , `newPath` contient de nombreuses contournements de chemin qui définissent la série de points et de tirets. Il s’agit du résultat de l’application de diverses propriétés de `ellipsePaint` (plus particulièrement,, `StrokeWidth` `StrokeCap` et `PathEffect` ) à `ellipsePath` et de l’ajout du chemin d’accès résultant dans `newPath` . La `GetFillPath` méthode retourne une valeur booléenne indiquant si le chemin d’accès de destination doit être rempli ; dans cet exemple, la valeur de retour est `true` pour remplir le chemin d’accès.

Essayez de modifier le `Style` paramètre `newPaint` dans `SKPaintStyle.Stroke` et vous verrez les contours de tracés individuels délimitées par une ligne de largeur d’un pixel.

## <a name="stroking-with-a-path"></a>Décontour avec un tracé

La [`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) méthode est conceptuellement semblable à `SKPathEffect.CreateDash` , à ceci près que vous spécifiez un chemin d’accès plutôt qu’un modèle de tirets et d’espaces. Ce chemin d’accès est répliqué plusieurs fois pour rayer la ligne ou la courbe.

La syntaxe est :

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

En général, le chemin d’accès que vous transmettez `Create1DPath` est petit et centré autour du point (0, 0). Le `advance` paramètre indique la distance entre les centres du chemin d’accès, car le chemin d’accès est répliqué sur la ligne. Vous définissez généralement cet argument sur la largeur approximative du tracé. L' `phase` argument joue le même rôle que dans la `CreateDash` méthode.

[`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle)A trois membres :

- `Translate`
- `Rotate`
- `Morph`

Le `Translate` membre fait en sorte que le chemin reste dans la même orientation que lorsqu’il est répliqué sur une ligne ou une courbe. Pour `Rotate` , le tracé est pivoté en fonction d’une tangente à la courbe. Le chemin d’accès a son orientation normale pour les lignes horizontales. `Morph`est semblable à `Rotate` , à ceci près que le tracé lui-même est également courbé pour correspondre à la courbure de la ligne tracée.

La page **effet du chemin 1D** illustre ces trois options. Le fichier [**OneDimensionalPathEffectPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) définit un sélecteur contenant trois éléments correspondant aux trois membres de l’énumération :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

Le fichier code-behind [**OneDimensionalPathEffectPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) définit trois `SKPathEffect` objets en tant que champs. Ils sont tous créés à l’aide `SKPathEffect.Create1DPath` de avec des `SKPath` objets créés à l’aide de `SKPath.ParseSvgPathData` . La première est une zone simple, la seconde est une forme en losange et la troisième est un rectangle. Ils sont utilisés pour illustrer les trois styles d’effet :

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

Le `PaintSurface` Gestionnaire crée une courbe de Bézier qui se boucle autour de lui-même, et accède au sélecteur pour déterminer lequel `PathEffect` doit être utilisé pour le rayer. Les trois options, `Translate` , `Rotate` et, `Morph` sont affichées de gauche à droite :

[![Capture d’écran triple de la page d’effet de tracé 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

Le chemin d’accès spécifié dans la `SKPathEffect.Create1DPath` méthode est toujours rempli. Le chemin d’accès spécifié dans la `DrawPath` méthode est toujours rayé si la `SKPaint` propriété de l’objet est `PathEffect` définie sur un effet de tracé 1D. Notez que l' `pathPaint` objet n’a pas de `Style` paramètre, ce qui `Fill` est normalement le chemin par défaut, mais le chemin d’accès est rayé indépendamment.

La zone utilisée dans l' `Translate` exemple est de 20 pixels carrés et l' `advance` argument est défini sur 24. Cette différence provoque un intervalle entre les cases lorsque la ligne est à peu près horizontale ou verticale, mais les zones se chevauchent un peu lorsque la ligne est diagonale, car la diagonale de la zone est de 28,3 pixels.

La forme en losange dans l' `Rotate` exemple est également de 20 pixels de largeur. `advance`A la valeur 20 afin que les points continuent à toucher lorsque le losange pivote avec la courbure de la ligne.

La forme de rectangle dans l' `Morph` exemple est de 50 pixels de largeur avec un `advance` paramètre de 55 pour faire un petit intervalle entre les rectangles, car ils sont tordus autour de la courbe de Bézier.

Si l' `advance` argument est inférieur à la taille du chemin d’accès, les chemins d’accès répliqués peuvent se chevaucher. Cela peut entraîner des effets intéressants. La page **chaîne liée** affiche une série de cercles qui se chevauchent, qui semblent ressembler à une chaîne liée, qui se bloque dans la forme distinctive d’une caténaire :

[![Capture d’écran triple de la page chaîne liée](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Semble très proche et vous verrez que ces derniers ne sont pas réellement des cercles. Chaque lien de la chaîne est deux arcs, dimensionnés et positionnés de sorte qu’ils semblent se connecter avec des liens adjacents.

Une chaîne ou un câble de distribution de poids uniforme se bloque sous la forme d’une caténaire. Un arche créé sous la forme d’une caténaire renversée bénéficie d’une distribution égale de la pression du poids d’une arche. La caténaire a une description mathématique apparemment simple :

`y = a · cosh(x / a)`

L' *cosh* est la fonction cosinus hyperbolique. Pour *x* égal à 0, *cosh* est égal à zéro et *y* est égal à *a*. C’est le centre de la caténaire. À l’instar de la fonction *cosinus* , *cosh* est dit *pair*, ce qui signifie que *Cosh (– x)* est égal à *Cosh (x)* et que les valeurs augmentent pour l’augmentation des arguments positifs ou négatifs. Ces valeurs décrivent les courbes qui forment les côtés de la caténaire.

La recherche de la valeur appropriée d' *un* pour s’adapter à la caténaire aux dimensions de la page du téléphone n’est pas un calcul direct. Si *w* et *h* sont la largeur et la hauteur d’un rectangle, la valeur optimale d' *un* correspond à l’équation suivante :

`cosh(w / 2 / a) = 1 + h / a`

La méthode suivante dans la [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpore cette égalité en faisant référence aux deux expressions à gauche et à droite du signe égal comme `left` et `right` . Pour les petites valeurs d' *un*, `left` est supérieur à `right` ; pour les valeurs élevées d' *un*, `left` est inférieur à `right` . La `while` boucle rétrécit sur une valeur optimale de : *a*

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

L' `SKPath` objet pour les liens est créé dans le constructeur de la classe, et l' `SKPathEffect` objet résultant est ensuite défini sur la `PathEffect` propriété de l' `SKPaint` objet qui est stocké en tant que champ :

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

Le principal travail du `PaintSurface` Gestionnaire consiste à créer un chemin d’accès pour la caténaire proprement dite. Après avoir déterminé la valeur maximale *a* et l’avoir stocké dans la `optA` variable, vous devez également calculer un décalage à partir du haut de la fenêtre. Elle peut ensuite accumuler une collection de `SKPoint` valeurs pour la caténaire, la transformer en tracé et dessiner le tracé avec l’objet créé précédemment `SKPaint` :

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

Ce programme définit le chemin d’accès utilisé dans `Create1DPath` pour avoir son point (0,0) au centre. Cela semble raisonnable, car le point (0,0) du tracé est aligné avec la ligne ou la courbe qu’il Orne. Toutefois, vous pouvez utiliser un point non centré (0, 0) pour certains effets spéciaux.

La page **tapis** roulant crée un tracé ressemblant à un tapis roulant, avec un haut et un bas courbés dimensionnés aux dimensions de la fenêtre. Ce chemin d’accès est rayé avec un `SKPaint` objet simple de 20 pixels de grande largeur et de couleur grise, puis rayé avec un autre objet `SKPaint` avec un `SKPathEffect` objet référençant un chemin d’accès ressemblant à un petit compartiment :

[![Capture d’écran triple de la page du tapis roulant](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

Le point (0,0) du chemin d’accès du compartiment est le descripteur. ainsi, lorsque l' `phase` argument est animé, les compartiments semblent tourner autour du tapis roulant, peut-être en éliminant l’eau en bas et en le vidant en haut.

La [`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implémente une animation avec des substitutions des `OnAppearing` `OnDisappearing` méthodes et. Le chemin d’accès pour le compartiment est défini dans le constructeur de la page :

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

Le code de création de compartiment se termine par deux transformations qui rendent le compartiment un peu plus grand et l’activent de manière latérale. Il est plus facile d’appliquer ces transformations que d’ajuster toutes les coordonnées dans le code précédent.

Le `PaintSurface` Gestionnaire commence par la définition d’un chemin d’accès pour le tapis roulant lui-même. Il s’agit simplement d’une paire de lignes et d’une paire de semi-cercles qui sont dessinés avec une ligne grise foncée de 20 pixels :

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

La logique de dessin du tapis roulant ne fonctionne pas en mode paysage.

Les compartiments doivent être espacés d’environ 200 pixels sur le tapis roulant. Toutefois, le tapis roulant n’est probablement pas un multiple de 200 pixels de long, ce qui signifie que comme l' `phase` argument de `SKPathEffect.Create1DPath` est animé, les compartiments s’affichent dans et hors de l’existence.

Pour cette raison, le programme calcule d’abord une valeur nommée `length` qui est la longueur du tapis roulant. Étant donné que le tapis roulant se compose de lignes droites et de semi-cercles, il s’agit d’un calcul simple. Ensuite, le nombre de compartiments est calculé en divisant `length` par 200. Elle est arrondie à l’entier le plus proche, et ce nombre est ensuite divisé en `length` . Le résultat est un espacement pour un nombre entier de compartiments. L' `phase` argument est simplement une fraction de ce.

## <a name="from-path-to-path-again"></a>De nouveau chemin vers le chemin

En bas du `DrawSurface` gestionnaire dans le **tapis roulant**, commentez l' `canvas.DrawPath` appel et remplacez-le par le code suivant :

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Comme pour l’exemple précédent de `GetFillPath` , vous verrez que les résultats sont les mêmes, à l’exception de la couleur. Après l’exécution de `GetFillPath` , l' `newPath` objet contient plusieurs copies du chemin d’accès du compartiment, chacune placée dans le même endroit que l’animation qui les a positionnée au moment de l’appel.

## <a name="hatching-an-area"></a>Hachurage d’une zone

La [`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) méthode remplit une zone avec des lignes parallèles, souvent appelées *lignes de hachurage*. La méthode a la syntaxe suivante :

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

L' `width` argument spécifie la largeur du trait des lignes de hachurage. Le `matrix` paramètre est une combinaison de la mise à l’échelle et de la rotation facultative. Le facteur d’échelle indique l’incrément de pixel utilisé par skia pour l’espacement des lignes de hachurage. La séparation entre les lignes est le facteur d’échelle moins l' `width` argument. Si le facteur d’échelle est inférieur ou égal à la `width` valeur, il n’y aura pas d’espace entre les lignes de hachurage, et la zone apparaîtra comme étant remplie. Spécifiez la même valeur pour la mise à l’échelle horizontale et verticale.

Par défaut, les lignes de hachurage sont horizontales. Si le `matrix` paramètre contient une rotation, les lignes de hachurage sont pivotées dans le sens des aiguilles d’une montre.

La page **remplissage hachuré** illustre cet effet de tracé. La [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe définit trois effets de tracés en tant que champs, le premier pour les lignes de hachurage horizontales avec une largeur de 3 pixels avec un facteur d’échelle indiquant qu’ils sont espacés de 6 pixels. La séparation entre les lignes est donc de trois pixels. Le deuxième effet de tracé concerne les lignes de hachurage verticales avec une largeur de six pixels espacées de 24 pixels (par conséquent, la séparation est de 18 pixels) et la troisième pour les lignes de hachurage diagonales de 12 pixels de plus de 36 pixels de large.

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Notez la `Multiply` méthode Matrix. Étant donné que les facteurs de mise à l’échelle horizontale et verticale sont identiques, l’ordre de multiplication des matrices de mise à l’échelle et de rotation n’a pas d’importance.

Le `PaintSurface` gestionnaire utilise ces trois effets de tracé avec trois couleurs différentes conjointement avec `fillPaint` pour remplir un rectangle arrondi dimensionné en fonction de la page. La `Style` propriété définie sur `fillPaint` est ignorée ; lorsque l' `SKPaint` objet contient un effet de tracé créé à partir de `SKPathEffect.Create2DLine` , la zone est remplie, quelle que soit la valeur :

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

Si vous examinez attentivement les résultats, vous verrez que les hachures rouge et bleue ne se limitent pas précisément au rectangle arrondi. (Il s’agit apparemment d’une caractéristique du code skia sous-jacent.) Si ce n’est pas satisfaisant, une autre approche est affichée pour les lignes de hachurage diagonale en vert : le rectangle arrondi est utilisé comme tracé de détourage et les lignes de hachurage sont dessinées sur la page entière.

Le `PaintSurface` Gestionnaire se termine par un appel pour simplement rayer le rectangle arrondi, de sorte que vous pouvez voir l’écart avec les lignes de hachurage rouge et bleu :

[![Capture d’écran triple de la page remplissage de hachurage](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

L’écran Android ne ressemble pas vraiment à ce qui suit : la mise à l’échelle de la capture d’écran a entraîné la consolidation des lignes rouges fines et des espaces fins dans des lignes rouges apparemment plus larges et des espaces plus larges.

## <a name="filling-with-a-path"></a>Remplir avec un chemin d’accès

Le [`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) vous permet de remplir une zone avec un tracé qui est répliqué horizontalement et verticalement, en fonction de la disposition en mosaïque de la zone :

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

Les `SKMatrix` facteurs de mise à l’échelle indiquent l’espacement horizontal et vertical du chemin d’accès répliqué. Toutefois, vous ne pouvez pas faire pivoter le tracé à l’aide de cet `matrix` argument ; si vous souhaitez que le tracé pivote, faites pivoter le tracé lui-même à l’aide de la `Transform` méthode définie par `SKPath` .

Le chemin d’accès répliqué est normalement aligné sur les bords gauche et supérieur de l’écran plutôt que sur la zone remplie. Vous pouvez remplacer ce comportement en fournissant des facteurs de translation entre 0 et les facteurs de mise à l’échelle pour spécifier des décalages horizontal et vertical à partir des côtés gauche et supérieur.

La page remplissage de la **vignette du tracé** illustre cet effet de tracé. Le chemin d’accès utilisé pour la disposition en mosaïque de la zone est défini en tant que champ dans la [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. Les coordonnées horizontales et verticales sont comprises entre-40 et 40, ce qui signifie que ce chemin d’accès est de 80 pixels carrés :

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

Dans le `PaintSurface` Gestionnaire, les `SKPathEffect.Create2DPath` appels attribuent à l’espacement horizontal et vertical la valeur 64 pour provoquer le chevauchement des mosaïques carrées de 80 pixels. Heureusement, le chemin d’accès ressemble à une pièce de puzzle, le maillage parfait avec les vignettes adjacentes :

[![Capture d’écran triple de la page de remplissage de vignette du tracé](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

La mise à l’échelle de la capture d’écran d’origine provoque une déformation, en particulier sur l’écran Android.

Notez que ces vignettes apparaissent toujours tout et ne sont jamais tronquées. Sur les deux premières captures d’écran, il n’est même pas évident que la zone remplie est un rectangle arrondi. Si vous souhaitez tronquer ces vignettes à une zone particulière, utilisez un tracé de détourage.

Essayez de définir la `Style` propriété de l' `SKPaint` objet sur `Stroke` , et vous verrez les vignettes individuelles délimitées au lieu de remplir.

Il est également possible de remplir une zone avec un bitmap en mosaïque, comme illustré dans l’article [**SkiaSharp bitmap**](../effects/shaders/bitmap-tiling.md)en mosaïque.

## <a name="rounding-sharp-corners"></a>Arrondir les angles aigus

Le programme **heptagon arrondi** présenté dans les [**trois façons de dessiner un**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) article d’arc utilisait un arc tangent pour courber les points d’une figure sur sept faces. L' **autre page heptagon arrondies** présente une approche beaucoup plus facile qui utilise un effet de chemin créé à partir de la [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) méthode :

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Bien que l’argument unique soit nommé `radius` , vous devez le définir à la moitié du rayon de l’angle souhaité. (Il s’agit d’une caractéristique du code skia sous-jacent.)

Voici le `PaintSurface` Gestionnaire de la [`AnotherRoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

Vous pouvez utiliser cet effet avec un encombrement ou un remplissage en fonction de la `Style` propriété de l' `SKPaint` objet. Ici, il s’exécute :

[![Capture d’écran triple d’une autre page heptagon arrondie](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Vous verrez que ce heptagon arrondi est identique au programme précédent. Si vous avez besoin de plus en plus convaincant que le rayon d’angle est véritablement 100 plutôt que le 50 spécifié dans l' `SKPathEffect.CreateCorner` appel, vous pouvez supprimer les marques de commentaire de l’instruction finale dans le programme et voir un cercle 100-RADIUS superposé sur l’angle.

## <a name="random-jitter"></a>Gigue aléatoire

Parfois, les lignes droites sans failles des graphiques informatiques ne sont pas exactement ce que vous voulez et un peu de randomisation est souhaitable. Dans ce cas, vous souhaiterez essayer la [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) méthode :

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Vous pouvez utiliser cet effet de tracé pour le contour ou le remplissage. Les lignes sont séparées en segments connectés (la longueur approximative spécifiée par `segLength` ) et s’étendent dans différentes directions. L’étendue de l’écart par rapport à la ligne d’origine est spécifiée par `deviation` .

Le dernier argument est une valeur de départ utilisée pour générer la séquence Pseudo-aléatoire utilisée pour l’effet. L’effet d’instabilité sera légèrement différent pour les différentes graines. L’argument a une valeur par défaut de zéro, ce qui signifie que l’effet est le même chaque fois que vous exécutez le programme. Si vous souhaitez une gigue différente chaque fois que l’écran est repeint, vous pouvez définir la `Millisecond` valeur de départ sur la propriété d’une `DataTime.Now` valeur (par exemple).

La page **expérimentation d’instabilité** vous permet d’expérimenter différentes valeurs en traçant un rectangle :

[![Capture d’écran triple de la page JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

Le programme est simple. Le fichier [**JitterExperimentPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) instancie deux `Slider` éléments et un `SKCanvasView` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Le `PaintSurface` Gestionnaire du fichier code-behind [**JitterExperimentPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) est appelé chaque fois qu’une `Slider` valeur change. Elle appelle `SKPathEffect.CreateDiscrete` à l’aide des deux `Slider` valeurs et l’utilise pour rayer un rectangle :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Vous pouvez également utiliser cet effet pour le remplissage, auquel cas le contour de la zone remplie est soumis à ces écarts aléatoires. La page de **texte instabilité** montre l’utilisation de cet effet de tracé pour afficher du texte. La majeure partie du code dans le `PaintSurface` Gestionnaire de la [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe est consacrée au dimensionnement et au centrage du texte :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Ici, il s’exécute en mode paysage :

[![Capture d’écran triple de la page JitterText](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>Mode plan du tracé

Vous avez déjà vu deux petits exemples de la [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) méthode de `SKPaint` , qui existe deux versions :

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Seuls les deux premiers arguments sont requis. La méthode accède au chemin d’accès référencé par l' `src` argument, modifie les données de chemin d’accès en fonction des propriétés de trait dans l' `SKPaint` objet (y compris la `PathEffect` propriété), puis écrit les résultats dans le `dst` chemin d’accès. Le `resScale` paramètre permet de réduire la précision pour créer un chemin de destination plus petit, et l' `cullRect` argument peut éliminer les contours en dehors d’un rectangle.

Une utilisation de base de cette méthode n’implique pas du tout des effets de chemin d’accès : si la propriété de l' `SKPaint` objet a la `Style` valeur `SKPaintStyle.Stroke` et que son jeu n’est *pas* `PathEffect` défini, `GetFillPath` crée un chemin d’accès qui représente un *contour* du chemin d’accès source comme s’il avait été rayé par les propriétés de peinture.

Par exemple, si le `src` chemin d’accès est un cercle simple de rayon 500 et que l' `SKPaint` objet spécifie une largeur de trait de 100, le `dst` tracé devient deux cercles concentriques, l’un avec un rayon de 450 et l’autre avec un rayon de 550. La méthode est appelée `GetFillPath` car le remplissage de ce `dst` chemin d’accès est identique au contour du `src` chemin d’accès. Toutefois, vous pouvez également tracer le tracé `dst` pour voir les contours du tracé.

Le **TAP pour définir le contour du tracé** illustre cela. Les `SKCanvasView` et `TapGestureRecognizer` sont instanciés dans le fichier [**TapToOutlineThePathPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) . Le fichier code-behind [**TapToOutlineThePathPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) définit trois `SKPaint` objets en tant que champs, deux pour le tracé avec des largeurs de trait de 100 et 20 et le troisième pour le remplissage :

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

Si l’écran n’a pas été taraudé, le `PaintSurface` gestionnaire utilise `blueFill` les `redThickStroke` objets et Paint pour restituer un chemin circulaire :

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

Le cercle est rempli et rayé comme prévu :

[![Capture d’écran triple du TAP normal pour définir le contour de la page du tracé](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Lorsque vous appuyez sur l’écran, `outlineThePath` a la valeur `true` , et le `PaintSurface` Gestionnaire crée un nouvel `SKPath` objet et l’utilise comme chemin d’accès de destination dans un appel à `GetFillPath` sur l' `redThickStroke` objet Paint. Ce chemin d’accès de destination est ensuite rempli et rayé avec `redThinStroke` , ce qui donne ce qui suit :

[![Capture d’écran triple du robinet avec contour pour la page du chemin d’accès](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

Les deux cercles rouges indiquent clairement que le tracé circulaire d’origine a été converti en deux contours circulaires.

Cette méthode peut être très utile pour le développement de chemins d’accès à utiliser pour la `SKPathEffect.Create1DPath` méthode. Les chemins d’accès que vous spécifiez dans ces méthodes sont toujours remplis lorsque les chemins d’accès sont répliqués. Si vous ne souhaitez pas que l’intégralité du chemin soit remplie, vous devez définir soigneusement les contours.

Par exemple, dans l’exemple de **chaîne liée** , les liens ont été définis avec une série de quatre arcs, chaque paire étant basée sur deux rayons pour détailler la zone du chemin d’accès à remplir. Il est possible de remplacer le code de la `LinkedChainPage` classe pour le faire un peu différemment.

Tout d’abord, vous souhaiterez redéfinir la `linkRadius` constante :

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

Le `linkPath` est désormais simplement deux arcs basés sur ce rayon unique, avec les angles de début et les angles de balayage souhaités :

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

L' `outlinePath` objet est ensuite le destinataire du contour de `linkPath` lorsqu’il est rayé avec les propriétés spécifiées dans `strokePaint` .

Un autre exemple qui utilise cette technique est le suivant pour le chemin d’accès utilisé dans une méthode.

## <a name="combining-path-effects"></a>Combinaison des effets du tracé

Les deux méthodes de création statiques finales de `SKPathEffect` sont [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) et [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) :

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Ces deux méthodes combinent deux effets de tracé pour créer un effet de tracé composite. La `CreateSum` méthode crée un effet de tracé semblable aux deux effets de tracés appliqués séparément, tandis que `CreateCompose` applique un seul effet de tracé (le), puis `inner` applique `outer` à ce.

Vous avez déjà vu comment la `GetFillPath` méthode de `SKPaint` peut convertir un chemin en un autre chemin d’accès en fonction des `SKPaint` Propriétés (y compris `PathEffect` ). il ne devrait donc pas être *trop* mystérieux comment un `SKPaint` objet peut effectuer cette opération à deux reprises avec les deux effets de tracés spécifiés dans les `CreateSum` `CreateCompose` méthodes ou.

Une utilisation évidente de `CreateSum` consiste à définir un `SKPaint` objet qui remplit un chemin d’accès avec un seul effet de tracé, et insère le tracé avec un autre effet de tracé. Cela est illustré dans l’exemple **chats dans le cadre** , qui affiche un tableau de chats au sein d’une image avec des bords festés :

[![Capture d’écran triple de la page chats dans le cadre](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

La [`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe commence par la définition de plusieurs champs. Vous pouvez reconnaître le premier champ de la [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe de l’article relatif aux [**données du chemin d’accès SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) . Le deuxième tracé est basé sur une ligne et un arc pour le motif feston du cadre :

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath`Peut être utilisé dans la `SKPathEffect.Create2DPath` méthode si la `SKPaint` propriété de l’objet a la `Style` valeur `Stroke` . Toutefois, si le `catPath` est utilisé directement dans ce programme, la partie entière du chat est remplie et les moustaches ne sont même pas visibles. (Essayez !) Il est nécessaire d’obtenir le contour de ce chemin d’accès et d’utiliser ce contour dans la `SKPathEffect.Create2DPath` méthode.

Le constructeur effectue cette tâche. Elle applique d’abord deux transformations à pour `catPath` déplacer le point (0,0) vers le centre et l’ajuster à la taille. `GetFillPath`Obtient tous les plans des contours dans `outlinedCatPath` et cet objet est utilisé dans l' `SKPathEffect.Create2DPath` appel. Les facteurs de mise à l’échelle dans la `SKMatrix` valeur sont légèrement plus grands que les tailles horizontale et verticale du CAT pour fournir un petit tampon entre les vignettes, tandis que les facteurs de traduction étaient dérivés un peu de façon empirique pour qu’un chat complet soit visible dans l’angle supérieur gauche du cadre :

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

Le constructeur appelle ensuite `SKPathEffect.Create1DPath` pour le frame festé. Notez que la largeur du chemin d’accès est de 100 pixels, mais l’avance est de 75 pixels, de sorte que le chemin d’accès répliqué est superposé autour du cadre. La dernière instruction du constructeur appelle `SKPathEffect.CreateSum` pour combiner les deux effets de tracé et définir le résultat sur l' `SKPaint` objet.

Tout ce travail permet au `PaintSurface` Gestionnaire d’être assez simple. Il suffit de définir un rectangle et de le dessiner à l’aide de `framePaint` :

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Les algorithmes situés derrière les effets de chemin d’accès provoquent toujours l’affichage du chemin d’accès complet pour le détourage ou le remplissage, ce qui peut entraîner l’affichage de certains éléments visuels en dehors du rectangle. L' `ClipRect` appel avant l' `DrawRect` appel permet aux visuels d’être beaucoup plus propre. (Essayez sans découpage !)

Il est courant d’utiliser `SKPathEffect.CreateCompose` pour ajouter une gigue à un autre effet de tracé. Vous pouvez certainement expérimenter par vous-même, mais voici un exemple quelque peu différent :

Les **lignes de hachurage en pointillés** remplissent une ellipse avec des lignes de hachurage qui sont en pointillés. La majeure partie du travail de la [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe est effectuée directement dans les définitions de champ. Ces champs définissent un effet tiret et un effet hachuré. Ils sont définis comme `static` parce qu’ils sont ensuite référencés dans un `SKPathEffect.CreateCompose` appel de la `SKPaint` définition :

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Le `PaintSurface` gestionnaire ne doit contenir que la surcharge standard, plus un appel à `DrawOval` :

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

Comme vous l’avez déjà découvert, les lignes de hachures ne sont pas limitées précisément à l’intérieur de la zone, et dans cet exemple, elles commencent toujours à gauche par un tiret entier :

[![Capture d’écran triple de la page lignes de hachures en pointillés](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Maintenant que vous avez vu des effets de tracés allant de simples points et de tirets à des combinaisons étranges, utilisez votre imagination et voyez ce que vous pouvez créer.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
