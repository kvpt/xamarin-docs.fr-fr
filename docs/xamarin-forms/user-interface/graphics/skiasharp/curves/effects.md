---
title: Effets de chemin d’accès dans SkiaSharp
description: Cet article explique les effets de chemin d’accès SkiaSharp différents chemins d’accès à utiliser pour le contour et en remplissant et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
ms.openlocfilehash: f43c4dac1811a54ee0ceeb70e2b2b1835a5ca030
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228242"
---
# <a name="path-effects-in-skiasharp"></a>Effets de chemin d’accès dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrir les divers effets de chemin d’accès qui autorise des chemins d’accès à utiliser pour le contour et en remplissant_

Un *effet* est une instance de la [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) classe qui est créé avec l’un des huit méthodes de création statiques définies par la classe. Le `SKPathEffect` objet est ensuite défini sur le [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) propriété d’un [ `SKPaint` ](xref:SkiaSharp.SKPaint) objet pour une variété d’effets intéressants, par exemple, traçant une ligne avec un chemin d’accès répliquée petit :

![Exemple de chaîne liée](effects-images/patheffectsample.png)

Effets de chemin d’accès vous permettent de :

- Tracer une ligne avec les points et tirets
- Tracer une ligne avec n’importe quel chemin d’accès rempli
- Remplir une zone de lignes de hachurage
- Remplir une zone avec un chemin d’accès en mosaïque
- Rendre précise des angles arrondis
- Ajouter aléatoire « instabilité » aux lignes et courbes

En outre, vous pouvez combiner deux ou plusieurs effets de chemin d’accès.

Cet article montre également comment utiliser le [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) méthode de `SKPaint` pour convertir un chemin d’accès dans un autre chemin d’accès en appliquant des propriétés de `SKPaint`, y compris `StrokeWidth` et `PathEffect`. Cela entraîne quelques techniques intéressantes, telles que l’obtention d’un chemin d’accès qui est un plan d’un autre chemin d’accès. `GetFillPath` est également utile dans le cadre des effets de chemin d’accès.

## <a name="dots-and-dashes"></a>Points et tirets

L’utilisation de la [ `PathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) méthode a été décrite dans l’article [ **points et tirets**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). Le premier argument de la méthode est un tableau contenant un nombre pair de deux ou plusieurs valeurs, alternant entre les longueurs des tirets et des longueurs des écarts entre les tirets :

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Ces valeurs sont *pas* par rapport à la largeur du trait. Par exemple, si la largeur du trait est 10 et que vous souhaitez une ligne composée de tirets carrés et des espacements carrés, définissez le `intervals` tableau {10, 10}. Le `phase` argument indique où la ligne commence dans le motif des tirets. Dans cet exemple, si vous souhaitez que la ligne pour démarrer avec l’écart carré, définissez `phase` à 10.

Les extrémités des tirets sont affectées par la `StrokeCap` propriété du `SKPaint`. Large largeurs de tracé, il est très courant pour définir cette propriété sur `SKStrokeCap.Round` pour arrondir les extrémités des tirets. Dans ce cas, les valeurs dans le `intervals` tableau *pas* inclut la longueur supplémentaire résultant de l’arrondi. Cela signifie qu’un point circulaire nécessite de spécifier une largeur égale à zéro. Pour une largeur de trait de 10, pour créer une ligne avec points circulaires et les écarts entre les points de la même diamètre, utilisez un `intervals` tableau de {0, 20}.

Le **animée de texte séparée par des points** page est similaire à la **du texte avec contour** page décrite dans l’article [ **l’intégration de texte et les graphiques** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) dans qu’il affiche décrit les caractères de texte en définissant le `Style` propriété de la `SKPaint` objet `SKPaintStyle.Stroke`. En outre, **animée de texte séparée par des points** utilise `SKPathEffect.CreateDash` afin de donner Ceci décrit une apparence en pointillés, et le programme anime également la `phase` argument de la `SKPathEffect.CreateDash` méthode pour rendre les points semblent voyager autour du texte caractères. Voici la page en mode paysage :

[![Capture d’écran triple de la page de texte en pointillé animée](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

Le [ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe commence en définissant certaines constantes et substitue le `OnAppearing` et `OnDisappearing` méthodes pour l’animation :

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

Le `PaintSurface` gestionnaire commence par créer un `SKPaint` objet pour afficher le texte. Le `TextSize` propriété est ajustée en fonction de la largeur de l’écran :

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

Vers la fin de la méthode, le `SKPathEffect.CreateDash` méthode est appelée à l’aide de la `dashArray` qui est défini comme un champ et animée `phase` valeur. Le `SKPathEffect` instance est définie sur le `PathEffect` propriété de la `SKPaint` objet pour afficher le texte.

Vous pouvez également définir le `SKPathEffect` de l’objet à le `SKPaint` objet avant le texte de la mesure et centrant sur la page. Dans ce cas, toutefois, animées points et tirets provoquent une variation de la taille du texte restitué, et le texte a tendance à vibrer un peu. (Essayez-le !)

Vous remarquerez également qu’en tant que le cercle points animés les caractères de texte, il existe un certain point dans chaque courbe fermée où les points semblent pop entrant et sortant d’existence. Il s’agit où le chemin d’accès qui définit le contour de caractère commence et se termine. Si la longueur de chemin d’accès n’est pas un multiple entier de la longueur du motif des tirets (dans ce cas 20 pixels), seule la partie de ce modèle peut contenir à la fin du chemin d’accès.

Il est possible d’ajuster la longueur du motif tiret pour ajuster la longueur du chemin d’accès, mais qui nécessite la détermination de la longueur du chemin d’accès, une technique qui est traitée dans l’article [ **les informations de chemin et énumération** ](information.md).

Le **point / Dash Morph** programme anime le motif des tirets lui-même afin que les tirets semblent diviser en points, qui combinent à nouveau en tirets du formulaire :

[![Capture d’écran triple de la page matricielle en pointillés](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

Le [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) substitue le `OnAppearing` et `OnDisappearing` méthodes tout comme la précédente d’un programme, mais la classe définit la `SKPaint` objet en tant que champ :

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

Le `PaintSurface` gestionnaire crée un chemin d’accès elliptique selon la taille de la page et exécute une longue section de code qui définit le `dashArray` et `phase` variables. En tant que la variable animée `t` comprise entre 0 et 1, le `if` blocs décomposer à ce moment-là dans quatre trimestres et chacun de ces trimestres, `tsub` également comprise entre 0 et 1. À la fin, le programme crée le `SKPathEffect` et lui affecte le `SKPaint` objet pour le dessin.

## <a name="from-path-to-path"></a>À partir du chemin d’accès au chemin d’accès

Le [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) méthode de `SKPaint` transforme un chemin d’accès en un autre en fonction des paramètres de la `SKPaint` objet. Pour voir comment cela fonctionne, remplacez le `canvas.DrawPath` appeler dans le programme précédent par le code suivant :

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

Dans ce nouveau code, le `GetFillPath` appeler convertit le `ellipsePath` (qui est simplement un ovale) dans `newPath`, qui est ensuite affiché avec `newPaint`. Le `newPaint` objet est créé avec tous les paramètres de propriété par défaut, à ceci près que le `Style` propriété est définie selon sur la valeur booléenne retournent la valeur à partir de `GetFillPath`.

Les visuels sont identiques à l’exception de la couleur, qui est définie dans `ellipsePaint` mais pas `newPaint`. Au lieu de l’ellipse simple défini dans `ellipsePath`, `newPath` contient de nombreux profils de chemin d’accès qui définissent la série de points et de tirets. Il s’agit du résultat de l’application de diverses propriétés de `ellipsePaint` (plus précisément, `StrokeWidth`, `StrokeCap`, et `PathEffect`) à `ellipsePath` et en plaçant le chemin d’accès résultant dans `newPath`. Le `GetFillPath` méthode retourne une valeur booléenne indiquant si le chemin d’accès de destination doit être complété ; dans cet exemple, la valeur de retour est `true` pour remplir le chemin d’accès.

Essayez de modifier le `Style` définissant dans `newPaint` à `SKPaintStyle.Stroke` et vous verrez les contours chemin d’accès individuels délimitées par une ligne celui--largeur en pixels.

## <a name="stroking-with-a-path"></a>Contour avec un chemin d’accès

Le [ `SKPathEffect.Create1DPath` ](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) méthode est conceptuellement semblable à `SKPathEffect.CreateDash` , à ceci près que vous spécifiez un chemin d’accès, plutôt qu’un modèle de tirets et d’espaces. Ce chemin d’accès est répliquée plusieurs fois à la ligne de contour ou courbe.

La syntaxe est :

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

En général, le chemin d’accès que vous passez à `Create1DPath` sera petite et centrée autour du point (0, 0). Le `advance` paramètre indique la distance entre les centres de chemin d’accès que le chemin d’accès est répliquée sur la ligne. Vous définissez généralement cet argument pour la largeur approximative de chemin d’accès. Le `phase` joue argument ici le même rôle tel qu’il le fait dans le `CreateDash` (méthode).

Le [ `SKPath1DPathEffectStyle` ](xref:SkiaSharp.SKPath1DPathEffectStyle) a trois membres :

- `Translate`
- `Rotate`
- `Morph`

Le `Translate` membre provoque le chemin d’accès à rester dans la même orientation comme elle est répliquée sur une ligne ou de la courbe. Pour `Rotate`, le chemin d’accès est pivoté selon une tangente à la courbe. Le chemin d’accès a son orientation normale des lignes horizontales. `Morph` est similaire à `Rotate` , à ceci près que le chemin d’accès lui-même est également courbe pour faire correspondre la courbure de la ligne en cours rayée.

Le **effet D 1** page illustre ces trois options. Le [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) fichier définit un sélecteur qui contient trois éléments correspondant aux trois membres de l’énumération :

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

Le [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) fichier code-behind définit trois `SKPathEffect` objets en tant que champs. Ceux-ci sont tous créés à l’aide de `SKPathEffect.Create1DPath` avec `SKPath` objets créés à l’aide de `SKPath.ParseSvgPathData`. La première est une simple zone, le second est une forme de losange et le troisième est un rectangle. Ceux-ci sont utilisés pour illustrer les styles de l’effet de trois :

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

Le `PaintSurface` gestionnaire crée une courbe de Bézier qui effectue une itération sur lui-même et y accède le sélecteur pour déterminer lequel `PathEffect` doit être utilisé pour rayer il. Les trois options : `Translate`, `Rotate`, et `Morph` , sont affichées de gauche à droite :

[![Capture d’écran triple de la page d’effet de tracé 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

Le chemin d’accès spécifié dans le `SKPathEffect.Create1DPath` méthode est toujours remplie. Le chemin d’accès spécifié dans le `DrawPath` méthode est rayée toujours si le `SKPaint` objet possède son `PathEffect` un effet D 1 la valeur de propriété. Notez que le `pathPaint` objet n’a aucun `Style` paramètre, qui est normalement par défaut `Fill`, mais le chemin d’accès est rayé quel que soit.

Utilisé dans la zone de la `Translate` exemple est 20 pixels carrés et le `advance` argument est défini sur 24. Cette différence provoque un écart entre les zones lorsque la ligne est à peu près horizontal ou vertical, mais les zones de se chevauchent un peu quand la ligne est en diagonale, car la diagonale de la zone est 28.3 produits pixels.

La forme de losange dans le `Rotate` s’avère également 20 pixels de large. Le `advance` est définie sur 20 afin que les points de continuent à toucher le losange est pivoté, ainsi que la courbure de la ligne.

La forme de rectangle dans le `Morph` exemple est de 50 pixels de large avec un `advance` définissant de 55 pour rendre un petit intervalle entre les rectangles comme ils sont tordues autour de la courbe de Bézier.

Si le `advance` argument est inférieure à la taille du chemin d’accès, puis les chemins d’accès répliquées peuvent se chevaucher. Cela peut entraîner des effets intéressants. Le **chaîne lié** page affiche une série de cercles qui semblent se présenter comme une chaîne liée, qui se bloque dans la forme distinctive d’un caténaire superposés :

[![Capture d’écran triple de la page chaîne liée](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Recherchez très proches et vous verrez que ce ne sont pas réellement des cercles. Chaque lien de la chaîne est deux arcs, dimensionné et positionné afin qu’elles semblent se connecter avec des liens adjacentes.

Une chaîne ou un câble de répartition uniforme se bloque sous la forme d’une caténaire. Un arch généré sous la forme d’une caténaire inversé bénéficie d’une distribution égale de la pression dans le poids d’un arc. La caténaire comporte une description de mathématique apparemment simple :

`y = a · cosh(x / a)`

Le *cosh* est la fonction cosinus hyperbolique. Pour *x* égal à 0, *cosh* est égal à zéro et *y* est égal à *un*. C’est le centre de la caténaire. Comme le *cosinus* (fonction), *cosh* est dite *même*, ce qui signifie que *cosh(–x)* est égal à *cosh(x)* , et les valeurs augmentent permettant d’accroître les arguments positifs ou négatifs. Ces valeurs décrivent les courbes qui forment les côtés de la caténaire.

Recherche la valeur appropriée de *un* en fonction de la caténaire aux dimensions de la page du téléphone n’est pas un calcul direct. Si *w* et *h* sont la largeur et la hauteur d’un rectangle, la valeur optimale de *un* satisfait l’équation suivante :

`cosh(w / 2 / a) = 1 + h / a`

La méthode suivante dans le [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpore que l’égalité en faisant référence à deux expressions de gauche et droite du signe égal en tant que `left` et `right`. Pour de petites valeurs de *un*, `left` est supérieur à `right`; pour des valeurs élevées de *un*, `left` est inférieure à `right`. Le `while` boucle réduit dans sur une valeur optimale de *un*:

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

Le `SKPath` pour les liens sont créé dans le constructeur de la classe et le résultant de l’objet `SKPathEffect` objet est ensuite défini sur le `PathEffect` propriété de la `SKPaint` objet qui est stocké en tant que champ :

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

La tâche principale de la `PaintSurface` gestionnaire consiste à créer un chemin d’accès pour la caténaire lui-même. Après avoir déterminé l’optimale *un* et en le stockant dans le `optA` variable, il doit également calculer un offset à partir du haut de la fenêtre. Ensuite, il peut accumuler une collection de `SKPoint` les valeurs pour la caténaire, qui transformer en un chemin d’accès et dessiner le chemin d’accès avec l’élément précédemment créé `SKPaint` objet :

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

Ce programme définit le chemin d’accès utilisé dans `Create1DPath` d’avoir son (0, 0) point dans le centre. Cela semble raisonnable, car la (0, 0) point du chemin d’accès est aligné avec la ligne ou courbe possédant l’ornement. Toutefois, vous pouvez utiliser un non-centré (0, 0) point pour certains effets spéciaux.

Le **tapis roulant** page crée un chemin d’accès qui ressemble à un tapis roulant allongées avec une courbe haut et le bas est dimensionné selon les dimensions de la fenêtre. Ce chemin d’accès est rayée avec un simple `SKPaint` objet 20 pixels de large et de couleur grise et puis rayé à nouveau avec un autre `SKPaint` de l’objet avec un `SKPathEffect` objet faisant référence à un chemin d’accès qui ressemble à un compartiment peu :

[![Capture d’écran triple de la page du tapis roulant](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

Le (0, 0) point du chemin d’accès de compartiment est le handle, par conséquent, lorsque le `phase` argument est animé, les compartiments semblent sont axés sur le tapis roulant, peut-être l’eau en bas de ramassage et le vidage de son évolution horizontale en haut.

Le [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implémente l’animation avec les remplacements de la `OnAppearing` et `OnDisappearing` méthodes. Le chemin d’accès pour le compartiment est définie dans le constructeur de la page :

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

Le code de création de compartiments est remplie avec les deux transformations qui rendent le compartiment un peu plus grand, pour l’activer sur le côté. Application de ces transformations était plus facile que d’ajuster toutes les coordonnées dans le code précédent.

Le `PaintSurface` gestionnaire commence par définir un chemin d’accès pour le tapis roulant lui-même. Il s’agit simplement d’une paire de lignes et une paire de cercles point-virgule qui est dessinée avec une ligne gris foncé au niveau de 20 pixels de large :

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

La logique pour dessiner le tapis roulant ne fonctionne pas en mode paysage.

Les compartiments doivent être espacées sur les uns des autres sur le tapis roulant de 200 pixels. Toutefois, le tapis roulant n’est probablement pas un multiple de 200 pixels de longs, ce qui signifie qu’en tant que le `phase` argument de `SKPathEffect.Create1DPath` est animée, compartiments seront affiche dans et hors d’existence.

Pour cette raison, le programme calcule d’abord une valeur nommée `length` qui est la longueur du tapis roulant. Étant donné que le tapis roulant se compose de lignes droites et des cercles, il s’agit d’un calcul simple. Ensuite, le nombre de compartiments est calculé en divisant `length` par 200. Cela est arrondi à l’entier le plus proche, et que le nombre est alors divisé en `length`. Le résultat est un espacement pour un nombre entier de compartiments. Le `phase` argument est simplement une fraction de qui.

## <a name="from-path-to-path-again"></a>À partir du chemin d’accès au nouveau chemin d’accès

En bas de la `DrawSurface` gestionnaire dans **tapis roulant**, commentez la `canvas.DrawPath` appeler et remplacez-le par le code suivant :

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Comme avec l’exemple précédent de `GetFillPath`, vous verrez que les résultats sont identiques à l’exception de la couleur. Après l’exécution de `GetFillPath`, le `newPath` objet contient plusieurs copies du chemin d’accès de compartiment, chacun positionné au même endroit que l’animation les positionné au moment de l’appel.

## <a name="hatching-an-area"></a>Une zone de hachurage

Le [ `SKPathEffect.Create2DLines` ](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) méthode remplit une zone avec des lignes parallèles, souvent appelé *lignes de hachurage*. La méthode a la syntaxe suivante :

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

Le `width` argument spécifie la largeur de trait des lignes de hachurage. Le `matrix` paramètre est une combinaison de rotation de mise à l’échelle et facultatif. Le facteur d’échelle indique l’incrément de pixels Skia utilise pour les lignes de hachurage de l’espace. La séparation entre les lignes est le facteur d’échelle moins le `width` argument. Si le facteur d’échelle est inférieure ou égale à la `width` valeur, il n’y aura aucun espace entre les lignes de hachurage et apparaîtra la zone à remplir. Spécifiez la même valeur pour la mise à l’échelle horizontale et verticale.

Par défaut, les lignes de hachurage sont horizontaux. Si le `matrix` paramètre contient la rotation, les lignes de hachurage pivotent dans le sens horaire.

Le **hachurage de remplissage** page montre l’effet de ce chemin d’accès. Le [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe définit trois effets de chemin d’accès en tant que champs, le premier pour les lignes de hachurage horizontal avec une largeur de 3 pixels avec un facteur de mise à l’échelle indiquant qu’ils sont espacées de 6 pixels uns des autres. La séparation entre les lignes est par conséquent trois pixels. L’effet de la deuxième est pour les lignes de hachurage vertical avec une largeur de six pixels espacement de 24 pixels uns des autres (par conséquent, la séparation est 18 pixels), et le troisième est de 12 pixels larges espacés 36 pixels distinguer les lignes de hachurage diagonal.

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

Notez que la matrice `Multiply` (méthode). Étant donné que les facteurs d’échelle horizontales et verticales sont les mêmes, peu importe l’ordre dans lequel les matrices de mise à l’échelle et rotation sont multipliés.

Le `PaintSurface` Gestionnaire utilise ces effets de chemin d’accès de trois avec trois couleurs différentes en association avec `fillPaint` pour remplir un rectangle à coins arrondis dimensionné en fonction de la page. Le `Style` propriété définie sur `fillPaint` est ignorée ; lorsque la `SKPaint` objet inclut un effet de chemin d’accès créé à partir de `SKPathEffect.Create2DLine`, la zone est remplie quel que soit :

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

Si vous examinez attentivement les résultats, vous verrez que les lignes de hachurage rouge et bleu ne sont pas limités précisément le rectangle à coins arrondis. (Cela est apparemment une caractéristique du code sous-jacent Skia.) Si ce n’est pas satisfaisant, une autre approche est indiquée pour les hachures diagonales en vert: Le rectangle arrondi est utilisé comme tracé de détourage et les lignes de hachurage sont dessinées sur la page entière.

Le `PaintSurface` gestionnaire se termine par un appel à simplement rayer le rectangle à coins arrondis, afin de voir la différence avec les lignes de hachurage rouge et bleu :

[![Capture d’écran triple de la page remplissage de hachurage](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

L’écran Android ne ressemble pas vraiment à ce qui suit: La mise à l’échelle de la capture d’écran a entraîné la consolidation des lignes rouges fines et des espaces fins dans des lignes rouges apparemment plus larges et des espaces plus larges.

## <a name="filling-with-a-path"></a>Remplir avec un chemin d’accès

Le [ `SKPathEffect.Create2DPath` ](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) vous permet de remplir une zone avec un chemin d’accès qui est répliquée horizontalement et verticalement, mosaïque en vigueur de la zone :

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

Le `SKMatrix` facteurs d’échelle indiquent l’espacement horizontal et vertical du chemin d’accès répliquée. Mais vous ne pouvez pas faire pivoter le chemin d’accès à l’aide de ce `matrix` argument ; si vous le chemin d’accès de faire pivoter, faire pivoter le chemin d’accès lui-même à l’aide de la `Transform` méthode définie par `SKPath`.

Le chemin d’accès répliquée est normalement aligné avec les bords gauche et supérieure de l’écran, plutôt que la zone à remplir. Vous pouvez remplacer ce comportement en fournissant des facteurs de translation comprise entre 0 et les facteurs d’échelle pour spécifier les décalages horizontaux et verticaux des bords gauche et supérieure.

Le **chemin Mosaïque remplit** page montre l’effet de ce chemin d’accès. Le chemin d’accès utilisé pour la zone de la mosaïque est défini en tant que champ dans le [ `PathTileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. Les coordonnées horizontales et verticales vont de -40 à 40, ce qui signifie que ce chemin d’accès est 80 pixels carrés :

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

Dans le `PaintSurface` gestionnaire, la `SKPathEffect.Create2DPath` appels définit l’espacement horizontal et vertical à 64 pour provoquer les mosaïques carrées 80-pixel se chevaucher. Heureusement, le chemin d’accès ressemble à une partie du puzzle, maillage bien avec contiguë de vignettes :

[![Capture d’écran triple de la page de remplissage de vignette du tracé](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

La mise à l’échelle à partir de la capture d’écran d’origine entraîne des déformations, en particulier sur l’écran Android.

Notez que ces vignettes toujours apparaissent ensemble et ne sont jamais tronqués. Sur les deux premières captures d’écran, il n’est pas encore évidente que la zone à remplir est un rectangle arrondi. Si vous souhaitez tronquer ces vignettes pour une zone particulière, utilisez un tracé de détourage.

Essayez de définir la `Style` propriété de la `SKPaint` objet `Stroke`, et vous verrez les vignettes individuelles décrites au lieu de rempli.

Il est également possible remplir une zone avec une image bitmap en mosaïque, comme indiqué dans l’article [ **mosaïque de bitmap SkiaSharp**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Arrondi des angles aigus

Le **arrondi de sur un heptagone** programme présenté dans le [ **trois moyens pour dessiner un Arc** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) article utilisé un arc tangent pour les points d’une figure sept recto verso de la courbe. Le **un autre sur un heptagone arrondi** page montre une approche beaucoup plus facile qui utilise un effet de chemin d’accès créé à partir de la [ `SKPathEffect.CreateCorner` ](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) (méthode) :

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Bien que l’argument unique est nommé `radius`, vous devez la définir sur la moitié du rayon de l’angle souhaité. (C’est une caractéristique du code Skia sous-jacent).

Voici le `PaintSurface` gestionnaire dans le [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe :

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

Vous pouvez utiliser cet effet avec contour ou remplissage selon le `Style` propriété de la `SKPaint` objet. Ici, il s’exécute :

[![Capture d’écran triple d’une autre page heptagon arrondie](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Vous verrez que cet sur un heptagone arrondi est identique au programme précédent. Si vous avez besoin de convaincre plus que le rayon de l’angle est véritablement 100 plutôt que les 50 spécifié dans le `SKPathEffect.CreateCorner` appel, vous pouvez ne pas commenter la dernière instruction dans le programme et voir un cercle de rayon de 100 superposées dans l’angle.

## <a name="random-jitter"></a>Instabilité aléatoire

Parfois, les lignes droites exempt de failles de graphiques de l’ordinateur ne sont pas tout à fait ce que vous voulez, et un peu aléatoire est souhaité. Dans ce cas, vous souhaitez essayer le [ `SKPathEffect.CreateDiscrete` ](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) méthode :

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

Vous pouvez utiliser cet effet de chemin d’accès pour le contour ou remplir. Lignes sont divisées en segments connectés, dont la longueur approximative est spécifiée par `segLength` — et étendre dans différentes directions. L’étendue de l’écart par rapport à la ligne d’origine est spécifié par `deviation`.

Le dernier argument est une valeur initiale utilisée pour générer la séquence pseudo-aléatoire utilisée pour l’effet. L’effet d’instabilité aura un aspect un peu différent pour différentes valeurs de départ. L’argument a une valeur par défaut de zéro, ce qui signifie que l’effet est identique à chaque fois que vous exécutez le programme. Si vous souhaitez instabilité différents chaque fois que l’écran est redessiné, vous pouvez définir la valeur de départ le `Millisecond` propriété d’un `DataTime.Now` valeur (par exemple).


Le **instabilité faire des essais** page vous permet de faire des essais avec différentes valeurs dans le contour d’un rectangle :

[![Capture d’écran triple de la page JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

Le programme est simple. Le [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) fichier instancie deux `Slider` éléments et un `SKCanvasView`:

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

Le `PaintSurface` gestionnaire dans le [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) fichier code-behind est appelée chaque fois qu’un `Slider` valeur est modifiée. Il appelle `SKPathEffect.CreateDiscrete` à l’aide de deux `Slider` valeurs et utilise ces informations pour tracer un rectangle :

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

Vous pouvez utiliser cet effet de remplissage, auquel cas le contour de la zone remplie est soumis à ces écarts aléatoire. Le **texte instabilité** page illustre l’utilisation de cet effet de chemin d’accès pour afficher le texte. La plupart du code dans le `PaintSurface` Gestionnaire de la [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe est consacrée au dimensionnement et de centrage du texte :

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

## <a name="path-outlining"></a>Chemin d’accès du mode plan

Vous avez déjà vu deux exemples peu de la [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*) méthode de `SKPaint`, lequel existe deux versions :

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Seuls les deux premiers arguments sont requis. La méthode accède au chemin d’accès référencé par le `src` argument, modifie les données de chemin d’accès basées sur les propriétés de trait dans le `SKPaint` objet (y compris le `PathEffect` propriété), puis écrit les résultats dans le `dst` chemin d’accès. Le `resScale` paramètre permet de réduire la précision pour créer un chemin d’accès de destination plus petits et le `cullRect` argument peut éliminer les contours en dehors d’un rectangle.

Une utilisation de base de cette méthode n’implique pas du tout des effets de chemin d’accès: Si la `SKPaint` `Style` propriété de l’objet a la `SKPaintStyle.Stroke`valeur `PathEffect`etqu' il n’a *pas* son ensemble, créeunchemind’accèsquireprésenteuncontourduchemind’accèssourcecommes’ilavaitétérayé`GetFillPath` par le Propriétés de la peinture.

Par exemple, si le `src` chemin d’accès est un cercle de rayon 500, simple et le `SKPaint` objet spécifie une épaisseur de contour de 100, puis le `dst` chemin d’accès devient deux cercles concentriques, l’autre avec un rayon de 450 et l’autre avec un rayon de 550. La méthode est appelée `GetFillPath` car remplissant ce `dst` chemin d’accès est le même que le contour du `src` chemin d’accès. Mais vous pouvez également tracer le `dst` chemin d’accès pour afficher les contours du chemin d’accès.

Le **appuyez sur contour, le chemin d’accès** illustre cela. Le `SKCanvasView` et `TapGestureRecognizer` sont instanciés dans le [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) fichier. Le [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) fichier code-behind définit trois `SKPaint` objets en tant que champs, deux pour le contour avec rayer les largeurs de 100 et 20 et le troisième de remplissage :

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

Si l’écran n’a pas été appuyé, le `PaintSurface` Gestionnaire utilise la `blueFill` et `redThickStroke` peindre des objets à restituer un chemin circulaire :

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

Le cercle rempli et rayé comme prévu :

[![Capture d’écran triple du TAP normal pour définir le contour de la page du tracé](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Lorsque vous appuyez sur l’écran, `outlineThePath` a la valeur `true`et le `PaintSurface` gestionnaire crée une nouvelle `SKPath` de l’objet et l’utilise comme le chemin d’accès de destination dans un appel à `GetFillPath` sur la `redThickStroke` objet paint. Ce chemin d’accès de destination est ensuite rempli et rayée avec `redThinStroke`, se traduisant par les éléments suivants :

[![Capture d’écran triple du robinet avec contour pour la page du chemin d’accès](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

Les deux cercles rouges indiquent clairement que le chemin d’origine circulaire a été converti en deux profils circulaires.

Cette méthode peut être très utile dans le développement de chemins d’accès à utiliser pour le `SKPathEffect.Create1DPath` (méthode). Les chemins d’accès que vous spécifiez dans ces méthodes sont toujours remplis lorsque les chemins d’accès sont répliquées. Si vous ne souhaitez pas le chemin d’accès complet à remplir, vous devez définir attentivement les contours.

Par exemple, dans le **chaîne lié** exemple, les liens ont été définies avec une série de quatre façons, chaque paire qui étaient basés sur deux rayons décrit la zone du tracé à remplir. Il est possible de remplacer le code dans la `LinkedChainPage` classe faire un peu différemment.

Tout d’abord, vous souhaitez redéfinir le `linkRadius` constante :

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

Le `linkPath` est désormais simplement deux arcs selon ce rayon unique, avec le texte souhaité démarrer angles et balayage angles :

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

Le `outlinePath` objet est ensuite le destinataire du contour de `linkPath` quand elle est rayée avec les propriétés spécifiées dans `strokePaint`.

Un autre exemple à l’aide de cette technique est proche du chemin d’accès utilisé dans une méthode.

## <a name="combining-path-effects"></a>Combinaison des effets de tracé

Les deux méthodes de création statiques finale de `SKPathEffect` sont [ `SKPathEffect.CreateSum` ](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) et [ `SKPathEffect.CreateCompose` ](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Ces deux méthodes combinent deux effets de chemin d’accès pour créer un effet de chemin d’accès composite. Le `CreateSum` méthode crée un effet de chemin d’accès qui est similaire aux effets de chemin de deux accès appliquées séparément, tandis que `CreateCompose` applique un effet de chemin d’accès (la `inner`), puis applique le `outer` à qui.

Vous avez déjà vu comment le `GetFillPath` méthode de `SKPaint` peut convertir un chemin d’accès à un autre chemin d’accès basé sur `SKPaint` propriétés (y compris `PathEffect`) pour qu’il ne doit pas être *trop* mystérieux comment un `SKPaint`objet peut effectuer cette opération à deux reprises avec les effets de deux de chemin d’accès spécifiés dans le `CreateSum` ou `CreateCompose` méthodes.

Une utilisation évidente de `CreateSum` consiste à définir un `SKPaint` objet qui remplit un chemin d’accès avec un effet un chemin d’accès et le chemin d’accès avec un autre effet de chemin d’accès des traits. Cela est illustré dans le **chats dans Frame** exemple, qui affiche un tableau de chats au sein d’un frame avec bords festonnées :

[![Capture d’écran triple de la page chats dans le cadre](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

Le [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe commence par définir plusieurs champs. Vous pouvez reconnaître le premier champ à partir de la [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe à partir de la [ **les données de chemin SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) article. Le deuxième chemin d’accès est basée sur une ligne et d’arc de cercle pour le modèle de crête du cadre :

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

Le `catPath` pourraient être utilisées dans le `SKPathEffect.Create2DPath` méthode si le `SKPaint` objet `Style` propriété est définie sur `Stroke`. Toutefois, si le `catPath` est utilisé directement dans ce programme, puis la tête de la cat entière sera remplie, et valeurs même ne seront pas visibles. (Essayez-le !) Il est nécessaire d’obtenir le contour de ce chemin d’accès et utiliser ce plan dans le `SKPathEffect.Create2DPath` (méthode).

Le constructeur effectue cette tâche. Il applique d’abord deux transformations à `catPath` pour déplacer le (0, 0) pointer vers le centre et à l’échelle la taille. `GetFillPath` Obtient tous les plans des contours de `outlinedCatPath`, et cet objet est utilisé dans le `SKPathEffect.Create2DPath` appeler. La mise à l’échelle en compte la `SKMatrix` valeur sont légèrement supérieur à l’horizontale et la taille verticale de la cat pour fournir un mémoire tampon peu entre les vignettes, tandis que les facteurs de translation ont été dérivées quelque peu empirique afin qu’un chat complète soit visible dans le coin supérieur gauche du cadre :

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

Le constructeur appelle ensuite `SKPathEffect.Create1DPath` pour le frame festonnée. Notez que la largeur du chemin d’accès est 100 pixels, mais l’avance est 75 pixels afin que le chemin d’accès répliquée est superposée autour du cadre. L’instruction finale des appels de constructeur `SKPathEffect.CreateSum` pour combiner les effets de deux chemins et de définir le résultat la `SKPaint` objet.

Tout ce travail permet le `PaintSurface` gestionnaire sont assez simples. Il doit uniquement définir un rectangle et dessiner à l’aide de `framePaint`:

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

Les algorithmes derrière les effets de chemin d’accès provoquent toujours le chemin d’accès complet utilisé pour le remplissage ou de contour soient affichés, ce qui peut entraîner certains éléments visuels apparaissent en dehors du rectangle. Le `ClipRect` appeler avant la `DrawRect` appel permet les visuels soit beaucoup plus propre. (Essayez sans découpage !)

Il est courant d’utiliser `SKPathEffect.CreateCompose` pour ajouter une instabilité à un autre effet de chemin d’accès. Vous pouvez certainement essayer vous-même, mais voici un exemple quelque peu différent :

Le **des lignes de hachurage en pointillés** remplit une ellipse avec les lignes de hachurage qui sont en pointillés. La plupart du travail dans le [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe est effectuée directement dans les définitions de champ. Ces champs définissent un effet de dash et un effet de hachurage. Ils sont définis comme `static` , car ils sont ensuite référencés dans un `SKPathEffect.CreateCompose` appeler dans le `SKPaint` définition :

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

Le `PaintSurface` gestionnaire doit contenir uniquement la surcharge standard plus un seul appel à `DrawOval`:

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

Comme vous l’avez déjà découverts, les lignes de hachurage ne sont pas précisément limités à l’intérieur de la zone, et dans cet exemple, ils commencent toujours à gauche avec un tiret entière :

[![Capture d’écran triple de la page lignes de hachures en pointillés](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Maintenant que vous avez vu les effets de chemin d’accès allant de simples points et tirets à des combinaisons étranges, utilisez votre imagination et voir ce que vous pouvez créer.



## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
