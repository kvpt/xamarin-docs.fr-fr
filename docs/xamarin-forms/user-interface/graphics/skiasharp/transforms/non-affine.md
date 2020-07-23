---
title: Transformations non affines
description: Cet article explique comment créer des effets de perspective et de dépouille avec la troisième colonne de la matrice de transformation et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4c6569acbade7edf64c9aaf54237ebaa342ea54
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936641"
---
# <a name="non-affine-transforms"></a>Transformations non affines

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Créer des effets de perspective et de dépouille avec la troisième colonne de la matrice de transformation_

La traduction, la mise à l’échelle, la rotation et l’inclinaison sont toutes classées comme des transformations *affines* . Les transformations affines préservent les lignes parallèles. Si deux lignes sont parallèles avant la transformation, elles restent parallèles après la transformation. Les rectangles sont toujours transformés en parallélogrammes.

Toutefois, SkiaSharp est également compatible avec les transformations non affines, qui ont la capacité de transformer un rectangle en un quadrangulaires convexe :

![Image bitmap transformée en quadrangulaires convexe](non-affine-images/nonaffinetransformexample.png)

Un quadrilatère convexe est un chiffre à quatre faces avec des angles intérieurs toujours inférieurs à 180 degrés et des côtés qui ne se croisent pas entre eux.

Les transformations non affines sont générées lorsque la troisième ligne de la matrice de transformation est définie sur des valeurs autres que 0, 0 et 1. La `SKMatrix` multiplication complète est :

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Les formules de transformation résultantes sont les suivantes :

x' = ScaleX · x + SkewX · o + TransX

y' = inclinaison · x + ScaleY · o + TransY

z' = Persp0 · x + Persp1 · y + Persp2

La règle fondamentale de l’utilisation d’une matrice 3 par 3 pour les transformations à deux dimensions est que tout reste dans le plan où Z est égal à 1. Sauf si `Persp0` et `Persp1` sont 0 et `Persp2` égal à 1, la transformation a déplacé les coordonnées Z de ce plan.

Pour rétablir une transformation à deux dimensions, les coordonnées doivent être replacées dans ce plan. Une autre étape est nécessaire. Les valeurs x, y et z doivent être divisées par z :

x "= x'/z'

y "= y'/z'

z "= z'/z' = 1

Il s’agit de *coordonnées homogènes* qui ont été développées par Mathematician août Ferdinand Möbius, bien mieux connu pour sa topologie topologique curiosité, la bande Möbius.

Si z est égal à 0, la Division donne des coordonnées infinies. En fait, l’une des motivations Möbius’s pour le développement de coordonnées homogènes était la capacité à représenter des valeurs infinies avec des nombres finis.

Toutefois, lors de l’affichage de graphiques, vous souhaitez éviter d’afficher des éléments avec des coordonnées qui transforment en valeurs infinies. Ces coordonnées ne seront pas rendues. Tout ce qui se trouve à proximité de ces coordonnées sera très volumineux et probablement pas visuellement cohérent.

Dans cette équation, vous ne souhaitez pas que la valeur de z’devienne zéro :

z' = Persp0 · x + Persp1 · y + Persp2

Par conséquent, ces valeurs présentent des restrictions pratiques : 

La `Persp2` cellule peut être égale à zéro ou à une valeur différente de zéro. Si `Persp2` est égal à zéro, z est égal à zéro pour le point (0,0), ce qui n’est généralement pas souhaitable, car ce point est très courant dans les graphiques à deux dimensions. Si `Persp2` n’est pas égal à zéro, il n’y a aucune perte de général si `Persp2` est fixée à 1. Par exemple, si vous déterminez que `Persp2` doit être 5, vous pouvez simplement diviser toutes les cellules de la matrice par 5, ce qui donne `Persp2` égal à 1 et le résultat sera le même.

Pour ces raisons, `Persp2` est souvent fixé à 1, qui est la même valeur dans la matrice d’identité.

En général, `Persp0` et `Persp1` sont de petits nombres. Supposons, par exemple, que vous commenciez par une matrice d’identité, mais que vous définissez `Persp0` sur 0,01 :

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Les formules de transformation sont les suivantes :

x' = x/(0,01 · x + 1)

y' = y/(0,01 · x + 1)

Utilisez à présent cette transformation pour restituer une zone carrée de 100 pixels positionnée à l’origine. Voici comment les quatre angles sont transformés :

(0,0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Lorsque x est égal à 100, alors le dénominateur z est 2, de sorte que les coordonnées x et y sont effectivement divisées en deux. Le côté droit de la zone est plus petit que le côté gauche :

![Zone soumise à une transformation non affine](non-affine-images/nonaffinetransform.png)

La `Persp` partie de ces noms de cellule fait référence à la « perspective », car la réduction suggère que la zone est maintenant inclinée du côté droit de la visionneuse.

La page **perspective de test** vous permet d’expérimenter les valeurs de et d' `Persp0` `Pers1` avoir une idée de leur fonctionnement. Les valeurs raisonnables de ces cellules de matrice sont tellement petites que le `Slider` dans le plateforme Windows universelle ne peut pas les gérer correctement. Pour s’adapter au problème UWP, les deux `Slider` éléments de [**TestPerspective. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) doivent être initialisés pour être compris entre-1 et 1 :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
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
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Les gestionnaires d’événements pour les curseurs dans le [`TestPerspectivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) fichier code-behind divisent les valeurs par 100 afin qu’elles soient comprises entre-0,01 et 0,01. En outre, le constructeur se charge dans une image bitmap :

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

Le `PaintSurface` Gestionnaire calcule une `SKMatrix` valeur nommée `perspectiveMatrix` en fonction des valeurs de ces deux curseurs divisées par 100. Cela est combiné avec deux transformations de translation qui placent le centre de cette transformation au centre de l’image bitmap :

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Voici quelques exemples d’images :

[![Capture d’écran triple de la page de perspective de test](non-affine-images/testperspective-small.png)](non-affine-images/testperspective-large.png#lightbox "Capture d’écran triple de la page de perspective de test")

À mesure que vous faites des essais avec les curseurs, vous constaterez que les valeurs situées au-delà de 0,0066 ou en dessous de 0,0066 entraînent soudainement la rupture et l’incohérence de l’image. La bitmap transformée est de 300 pixels carrés. Elle est transformée par rapport à son centre, donc les coordonnées de la plage de bitmap de – 150 à 150. N’oubliez pas que la valeur de z est :

z' = Persp0 · x + Persp1 · y + 1

Si `Persp0` ou `Persp1` est supérieur à 0,0066 ou inférieur à 0,0066, il y a toujours une coordonnée de l’image bitmap qui produit une valeur z égale à zéro. Cela provoque une division par zéro et le rendu devient un désordre. Lorsque vous utilisez des transformations non affines, vous ne devez pas effectuer de rendu avec des coordonnées qui provoquent une division par zéro.

En règle générale, vous ne devez pas définir `Persp0` et `Persp1` isoler. Il est également souvent nécessaire de définir d’autres cellules de la matrice pour obtenir certains types de transformations non affines.

Une telle transformation non affine est une *transformation conique*. Ce type de transformation non affine conserve les dimensions globales d’un rectangle, mais les bandes d’un côté :

![Zone soumise à une transformation de dépouille](non-affine-images/tapertransform.png)

La [`TaperTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe effectue un calcul généralisé d’une transformation non affine en fonction de ces paramètres :

- taille rectangulaire de l’image transformée,
- énumération qui indique le côté du rectangle que les bandes,
- autre énumération qui indique comment les bandes magnétiques et
- étendue du rétrécissement.

Voici le code :

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

Cette classe est utilisée dans la page **transformation de dépouille** . Le fichier XAML instancie deux `Picker` éléments pour sélectionner les valeurs d’énumération et un `Slider` pour choisir la fraction conique. Le [`PaintSurface`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) Gestionnaire combine la transformation de dépouille avec deux transformations de translation pour effectuer la transformation par rapport à l’angle supérieur gauche de la bitmap :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

Voici quelques exemples :

[![Capture d’écran triple de la page transformation de dépouille](non-affine-images/tapertransform-small.png)](non-affine-images/tapertransform-large.png#lightbox "Capture d’écran triple de la page transformation de dépouille")

Un autre type de transformations non affinées généralisées est la rotation 3D, qui est illustrée dans l’article suivant, [**rotations 3D**](3d-rotation.md).

La transformation non affine peut transformer un rectangle en un quadrangulaires convexe. Cela est illustré par la page **afficher une matrice non affine** . Elle est très similaire à la page **afficher la matrice affine** de l’article [**transformations de matrice**](matrix.md) , à ceci près qu’elle possède un quatrième `TouchPoint` objet pour manipuler le quatrième angle de l’image bitmap :

[![Capture d’écran triple de la page afficher une matrice non affine](non-affine-images/shownonaffinematrix-small.png)](non-affine-images/shownonaffinematrix-large.png#lightbox "Capture d’écran triple de la page afficher une matrice non affine")

Tant que vous ne tentez pas de créer un angle intérieur de l’un des angles de la bitmap supérieure à 180 degrés, ou de faire en sorte que deux côtés se croisent, le programme calcule correctement la transformation à l’aide de cette méthode à partir de la [`ShowNonAffineMatrixPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe :

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

Pour faciliter le calcul, cette méthode obtient la transformation totale en tant que produit de trois transformations distinctes, qui sont symbolisées par des flèches indiquant comment ces transformations modifient les quatre coins de l’image bitmap :

(0,0) → (0, 0) → (0, 0) → (x0, Y0) (en haut à gauche)

(0, H) → (0, 1) → (0, 1) → (x1, Y1) (en bas à gauche)

(W, 0) → (1, 0) → (1,0) → (x2, Y2) (en haut à droite)

(W, H) → (1, 1) → (a, b) → (x3, Y3) (en bas à droite)

Les coordonnées finales à droite sont les quatre points associés aux quatre points tactiles. Il s’agit des coordonnées finales des angles de la bitmap.

W et H représentent la largeur et la hauteur de la bitmap. La première transformation `S` met simplement à l’échelle l’image bitmap sur un carré de 1 pixel. La deuxième transformation est la transformation non affine `N` et la troisième est la transformation affine `A` . Cette transformation affine est basée sur trois points. elle est donc identique à la méthode affine antérieure [`ComputeMatrix`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) et n’implique pas la quatrième ligne avec le point (a, b).

Les `a` `b` valeurs et sont calculées afin que la troisième transformation soit affine. Le code obtient l’inverse de la transformation affine, puis l’utilise pour mapper l’angle inférieur droit. C’est le point (a, b).

Une autre utilisation des transformations non affines consiste à imiter les graphiques en trois dimensions. Dans l’article suivant, [**rotations 3D**](3d-rotation.md) , vous verrez comment faire pivoter un graphique à deux dimensions dans l’espace 3D.

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
