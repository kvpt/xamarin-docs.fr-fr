---
title: Transformations non affines
description: Cet article explique comment créer une perspective et effets conique avec la troisième colonne de la matrice de transformation et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: f526be2156c4d74cf695b6b11706e3bbb7f355bb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642992"
---
# <a name="non-affine-transforms"></a>Transformations non affines

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Créer des perspectives et des effets conique avec la troisième colonne de la matrice de transformation_

Translation, mise à l’échelle, la rotation et l’inclinaison sont toutes classées en tant que *affine* transforme. Transformations affines conservent les lignes parallèles. Si deux lignes sont parallèles avant la transformation, ils restent parallèles après la transformation. Rectangles sont toujours transformées en Parallélogrammes.

Toutefois, SkiaSharp est également capable de transformations non affines, qui ont la possibilité de transformer un rectangle en n’importe quel quadrilatère convexe :

![](non-affine-images/nonaffinetransformexample.png "Une image bitmap transformée en un quadrilatère convexe")

Un quadrilatère convexe est un graphique de quatre côtés avec des angles intérieurs toujours inférieur à 180 degrés et côtés qui ne traversent les uns des autres.

Non affines transforme les résultats lorsque la valeur de la troisième ligne de la matrice de transformation des valeurs autres que 0, 0 et 1. La version complète `SKMatrix` multiplication est :

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Les formules de transformation résultantes sont :

x' = ScaleX·x + SkewX·y + TransX

y' = SkewY·x + ScaleY·y + TransY

z' = Persp0·x + Persp1·y + Persp2

La règle fondamentale de l’utilisation d’une matrice 3 x 3 pour les transformations à deux dimensions est tout le reste sur le plan où Z est égal à 1. À moins que `Persp0` et `Persp1` sont 0, et `Persp2` est égal à 1, la transformation a déplacé les coordonnées Z désactiver ce plan.

Pour restaurer une transformation à deux dimensions, les coordonnées doivent être déplacées vers ce plan. Une autre étape est requise. Le x », y', et z » valeurs doivent être divisés en z » :

x » = x' / z'

y » = y' / z'

z » = z' / z' = 1

Ils sont appelés *coordonnées homogènes* et elles ont été développées par mathématicien août Ferdinand Möbius, beaucoup plus répandues pour son curiosité topologique, la bande Möbius.

Si z » est égal à 0, les résultats de la division en coordonnées infinies. En fait, une des motivations de Möbius pour le développement des coordonnées homogènes était la possibilité pour représenter les valeurs infinies avec nombres finis.

Lors de l’affichage des graphiques, toutefois, vous souhaitez éviter quelque chose avec les coordonnées qui transforment les valeurs infinies rendu. Ces coordonnées ne sont pas être restituées. Tous les éléments à proximité de ces coordonnées seront très volumineux et probablement pas visuellement cohérent.

Dans cette équation, vous ne souhaitez pas la valeur de z' devenant zéro :

z' = Persp0·x + Persp1·y + Persp2

Par conséquent, ces valeurs ont certaines restrictions pratiques : 

Le `Persp2` cellule peut être zéro ou non nul. Si `Persp2` est zéro, z » est égale à zéro pour le point (0, 0), et qui n’est généralement pas souhaitable, car ce point est très courant dans les graphiques à deux dimensions. Si `Persp2` n’est pas égal à zéro, il y a aucune perte de généralité si `Persp2` est fixée à 1. Par exemple, si vous déterminez que `Persp2` doit être 5, puis vous pouvez simplement diviser ce qui rend toutes les cellules de la matrice à 5, `Persp2` égaux à 1, et le résultat sera le même.

Pour ces raisons, `Persp2` est souvent fixée à 1, qui est la même valeur dans la matrice d’identité.

En règle générale, `Persp0` et `Persp1` sont de petits nombres. Par exemple, supposons que vous commencez avec une matrice d’identité mais le jeu `Persp0` à 0,01 :

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Les formules de transformation sont :

x' = x / (0.01·x + 1)

y' = y / (0.01·x + 1)

Maintenant utiliser cette transformation pour rendre un carré 100 pixels positionné à l’origine. Voici comment les quatre coins sont transformés :

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Si x est 100, alors le z' dénominateur est 2, donc les coordonnées x et y sont réduit de moitié. Le côté droit de la zone devient plus court que le côté gauche :

![](non-affine-images/nonaffinetransform.png "Une zone soumise à une transformation non affines")

Le `Persp` partie de ces noms de cellule fait référence à « perspective », car la réduction suggère que la zone est inclinée maintenant avec le côté droit plus éloigné de la visionneuse.

Le **Perspective Test** page vous permet de faire des essais avec des valeurs de `Persp0` et `Pers1` pour avoir une idée pour leur fonctionnement. Des valeurs raisonnables de ces cellules de matrice sont, de sorte que le `Slider` dans la plateforme Windows universelle ne peut pas gérer correctement les. Pour prendre en compte le problème UWP, les deux `Slider` éléments dans le [ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) doivent être initialisés à la plage de -1 à 1 :

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

Les gestionnaires d’événements pour les curseurs dans le [ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) fichier code-behind divise les valeurs par 100 afin qu’ils être comprise entre –0.01 et 0,01. En outre, le constructeur de charge dans une image bitmap :

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

Le `PaintSurface` Gestionnaire calcule un `SKMatrix` valeur nommée `perspectiveMatrix` selon les valeurs de ces deux curseurs divisés par 100. Cela est combiné avec deux traduire des transformations qui placer au centre de cette transformation dans le centre de l’image bitmap :

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

[![](non-affine-images/testperspective-small.png "Capture d’écran triple de la page de la Perspective Test")](non-affine-images/testperspective-large.png#lightbox "Triple capture d’écran de la page de la Perspective Test")

Lorsque vous testez les curseurs, vous trouverez que les valeurs au-delà 0.0066 ou ci-dessous –0.0066 entraînent l’image deviennent subitement détaché et incohérents. L’image bitmap en cours de transformation est carré de 300 pixels. Il est transformé par rapport à son centre, de sorte que les coordonnées de l’image bitmap allant de –150 à 150. N’oubliez pas que la valeur de z » est :

z' = Persp0·x + Persp1·y + 1

Si `Persp0` ou `Persp1` est supérieure à 0.0066 ou ci-dessous –0.0066, puis il y a toujours des coordonnées de la bitmap qui entraîne un z » la valeur zéro. Qui entraîne la division par zéro et le rendu devient n’importe quoi. Lorsque vous utilisez des transformations non affines, que vous souhaitez éviter de rendu quoi que ce soit avec les coordonnées qui provoquent la division par zéro.

En règle générale, vous ne les définissant `Persp0` et `Persp1` de manière isolée. Il est souvent nécessaire de définir d’autres cellules dans la matrice pour obtenir certains types de transformations non affines.

Une telle transformation non affine est un *transformation conique*. Ce type de transformation non affine conserve les dimensions générales d’un rectangle mais EFFILE côté « un » :

![](non-affine-images/tapertransform.png "Une zone soumise à une transformation conique")

Le [ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe effectue un calcul généralisé d’une transformation non affine en fonction de ces paramètres :

- la taille rectangulaire de l’image en cours de transformation,
- énumération qui indique le côté du rectangle qui EFFILE,
- une autre énumération qui indique la façon dont il EFFILE, et
- étendue de la rétrécissement.

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

Cette classe est utilisée dans le **transformer conique** page. Le fichier XAML instancie deux `Picker` éléments à sélectionner les valeurs d’énumération et un `Slider` pour le choix de la fraction conique. Le [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) Gestionnaire combine la transformation conique avec deux translation à rendre la transformation par rapport à l’angle supérieur gauche de l’image bitmap :

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

Voici quelques exemples :

[![](non-affine-images/tapertransform-small.png "Capture d’écran triple de la page Transformer conique")](non-affine-images/tapertransform-large.png#lightbox "Triple capture d’écran de la page Transformer conique")

Un autre type de transformations non affines généralisés est rotation 3D, ce qui est présentée dans l’article suivant, [ **Rotations 3D**](3d-rotation.md).

La transformation non affine peut transformer un rectangle en n’importe quel quadrilatère convexe. Cela est illustré par la **afficher la matrice Non affines** page. Il est très similaire à la **afficher la matrice Affine** page à partir de la [ **matrice transforme** ](matrix.md) article, à ceci près qu’il ait une quatrième `TouchPoint` objet à manipuler la quatrième angle de l’image bitmap :

[![](non-affine-images/shownonaffinematrix-small.png "Capture d’écran triple de la page Afficher la matrice Non affines")](non-affine-images/shownonaffinematrix-large.png#lightbox "Triple capture d’écran de la page Afficher la matrice Non affines")

Tant que vous n’essayez pas d’apporter un angle de l’intérieur d’un des angles de l’image bitmap supérieur à 180 degrés ou deux côtés coupent, le programme calcule correctement la transformation à l’aide de cette méthode à partir de la [ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe :

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

Pour faciliter le calcul, cette méthode obtient la transformation totale en tant que produit de trois transformations distincts, qui sont symbolisé ici avec les flèches indiquant comment ces transformations modifient les quatre coins de l’image bitmap :

(0, 0) → → (0, 0) → (0, 0) (x 0, y0) (en haut à gauche)

(0, H) → (0, 1) → (0, 1) → (x1, y1) (en bas à gauche)

(P, 0) → → (1, 0) → (1, 0) (x 2, y2) (en haut à droite)

(W, H) → → (1, 1) → (a, b) (x 3, y3) (en bas à droite)

Les coordonnées finales à droite sont les quatre points associés avec les points quatre tactiles. Il s’agit des finales coordonnées des angles de l’image bitmap.

L et H représentent la largeur et la hauteur de la bitmap. La première transformation `S` simplement mettre à l’échelle la bitmap à un carré de 1 pixel. La deuxième transformation est la transformation non affine `N`, et le troisième est la transformation affine `A`. Cette transformation affine étant basée sur trois points, il a comme précédemment affine [ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) (méthode) et n’implique pas la quatrième ligne avec le (a, b) point.

Le `a` et `b` afin que la troisième transformation est affine ou pas les valeurs sont calculées. Le code obtient l’inverse de la transformation affine et qui utilise ensuite pour mapper le coin inférieur droit. C’est le point (a, b).

Une autre utilisation des transformations non affines est d’imiter les graphiques en trois dimensions. Dans le prochain article, [ **Rotations 3D** ](3d-rotation.md) vous allez apprendre à faire pivoter un graphique à deux dimensions dans l’espace 3D.


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
