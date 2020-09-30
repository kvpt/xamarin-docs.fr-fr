---
title: Transformations de matrice dans SkiaSharp
description: Cet article explore plus en détail les transformations SkiaSharp avec la matrice de transformation polyvalente et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: davidbritch
ms.author: dabritch
ms.date: 04/12/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 911365b6293fecd3bf309f3e61d9b232d90b7a13
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556553"
---
# <a name="matrix-transforms-in-skiasharp"></a>Transformations de matrice dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorez plus en profondeur dans les transformations SkiaSharp avec la matrice de transformation polyvalente_

Toutes les transformations appliquées à l' `SKCanvas` objet sont consolidées dans une seule instance de la [`SKMatrix`](xref:SkiaSharp.SKMatrix) structure. Il s’agit d’une matrice de transformation 3 par 3 standard similaire à celle de tous les systèmes graphiques 2D modernes.

Comme vous l’avez vu, vous pouvez utiliser les transformations dans SkiaSharp sans connaître la matrice de transformation, mais la matrice de transformation est importante d’un point de vue théorique, et il est essentiel d’utiliser les transformations pour modifier les chemins ou pour gérer les entrées tactiles complexes, qui sont toutes deux démontrées dans cet article et la suivante.

![Image bitmap soumise à une transformation affine](matrix-images/matrixtransformexample.png)

La matrice de transformation actuelle appliquée au `SKCanvas` est disponible à tout moment en accédant à la propriété en lecture seule [`TotalMatrix`](xref:SkiaSharp.SKCanvas.TotalMatrix) . Vous pouvez définir une nouvelle matrice de transformation à l’aide de la [`SetMatrix`](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) méthode, et vous pouvez restaurer cette matrice de transformation aux valeurs par défaut en appelant [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) .

Le seul autre `SKCanvas` membre qui fonctionne directement avec la transformation de matrice du canevas est [`Concat`](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) qui concatène deux matrices en les multipliant.

La matrice de transformation par défaut est la matrice d’identité et est constituée de 1 dans les cellules diagonales et de 0 dans tout le monde :

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

Vous pouvez créer une matrice d’identité à l’aide de la  [`SKMatrix.MakeIdentity`](xref:SkiaSharp.SKMatrix.MakeIdentity) méthode statique :

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

Le `SKMatrix` constructeur par défaut ne retourne *pas* de matrice d’identité. Elle retourne une matrice avec toutes les cellules définies sur zéro. N’utilisez pas le `SKMatrix` constructeur, sauf si vous envisagez de définir ces cellules manuellement.

Quand SkiaSharp effectue le rendu d’un objet graphique, chaque point (x, y) est effectivement converti en matrice 1 par 3 avec 1 dans la troisième colonne :

<pre>
| x  y  1 |
</pre>

Cette matrice 1-par-3 représente un point tridimensionnel dont la coordonnée Z est définie sur 1. Il y a des raisons mathématiques (abordées plus loin), pourquoi une transformation de matrice à deux dimensions requiert un travail en trois dimensions. Vous pouvez considérer cette matrice 1-par-3 comme représentant un point dans un système de coordonnées 3D, mais toujours sur le plan 2D où Z est égal à 1.

Cette matrice 1-par-3 est ensuite multipliée par la matrice de transformation, et le résultat est le point affiché sur le canevas :

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

À l’aide de la multiplication de matrices standard, les points convertis sont les suivants :

`x' = x`

`y' = y`

`z' = 1`

Il s’agit de la transformation par défaut.

Lorsque la `Translate` méthode est appelée sur l' `SKCanvas` objet, les `tx` `ty` arguments et de la `Translate` méthode deviennent les deux premières cellules de la troisième ligne de la matrice de transformation :

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

La multiplication se présente désormais comme suit :

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Voici les formules de transformation :

`x' = x + tx`

`y' = y + ty`

Les facteurs de mise à l’échelle ont une valeur par défaut de 1. Quand vous appelez la `Scale` méthode sur un nouvel `SKCanvas` objet, la matrice de transformation résultante contient les `sx` `sy` arguments et dans les cellules diagonales :

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Les formules de transformation sont les suivantes :

`x' = sx · x`

`y' = sy · y`

La matrice de transformation après `Skew` l’appel de contient les deux arguments dans les cellules de la matrice adjacentes aux facteurs de mise à l’échelle :

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Les formules de transformation sont les suivantes :

`x' = x + xSkew · y`

`y' = ySkew · x + y`

Pour un appel à `RotateDegrees` ou `RotateRadians` pour un angle de α, la matrice de transformation se présente comme suit :

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Voici les formules de transformation :

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

Lorsque α est égal à 0 degré, il s’agit de la matrice d’identité. Lorsque α est de 180 degrés, la matrice de transformation se présente comme suit :

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Une rotation de 180 degrés équivaut à retourner un objet horizontalement et verticalement, ce qui est également possible en définissant des facteurs d’échelle de-1.

Tous ces types de transformations sont classés en tant que transformations *affines* . Les transformations affines n’impliquent jamais la troisième colonne de la matrice, qui conserve les valeurs par défaut 0, 0 et 1. L’article [**transformations non affines**](non-affine.md) traite des transformations non affines.

## <a name="matrix-multiplication"></a>Multiplication de matrices

L’un des avantages importants de l’utilisation de la matrice de transformation est que les transformations composites peuvent être obtenues par multiplication de matrice, qui est souvent appelée *concaténation*dans la documentation SkiaSharp. La plupart des méthodes liées aux transformations dans `SKCanvas` font référence à « pré-concaténation » ou « pre-Concat ». Cela fait référence à l’ordre de multiplication, ce qui est important, car la multiplication de matrice n’est pas commutative.

Par exemple, la documentation de la [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) méthode indique qu’il « CONVERSE la matrice actuelle avec la traduction spécifiée », tandis que la documentation de la [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) méthode indique qu’il s’agit d’un conchat de la matrice actuelle avec l’échelle spécifiée.

Cela signifie que la transformation spécifiée par l’appel de méthode est le multiplicateur (l’opérande gauche) et la matrice de transformation actuelle est le multiplicande (l’opérande de droite).

Supposons que `Translate` est appelé suivi de `Scale` :

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

La `Scale` transformation est multipliée par la `Translate` transformation pour la matrice de transformation composite :

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` peut être appelé avant `Translate` ce qui suit :

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

Dans ce cas, l’ordre de la multiplication est inversé et les facteurs de mise à l’échelle sont effectivement appliqués aux facteurs de traduction :

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Voici la `Scale` méthode avec un point pivot :

```csharp
canvas.Scale(sx, sy, px, py);
```

Cela équivaut aux appels de translation et de mise à l’échelle suivants :

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Les trois matrices de transformation sont multipliées dans l’ordre inverse de la façon dont les méthodes apparaissent dans le code :

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>Structure SKMatrix

La `SKMatrix` structure définit neuf propriétés de lecture/écriture de type `float` correspondant aux neuf cellules de la matrice de transformation :

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` définit également une propriété nommée [`Values`](xref:SkiaSharp.SKMatrix.Values) de type `float[]` . Cette propriété peut être utilisée pour définir ou obtenir les neuf valeurs en une seule capture dans l’ordre,,,,,, `ScaleX` `SkewX` `TransX` `SkewY` `ScaleY` `TransY` `Persp0` , `Persp1` et `Persp2` .

Les `Persp0` `Persp1` cellules, et `Persp2` sont présentées dans l’article [**transformations non affines**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Si ces cellules ont leurs valeurs par défaut 0, 0 et 1, la transformation est multipliée par un point de coordonnée comme suit :

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

Il s’agit de la transformation affine à deux dimensions complète. La transformation affine conserve les lignes parallèles, ce qui signifie qu’un rectangle n’est jamais transformé en autre chose qu’un parallélogramme.

La `SKMatrix` structure définit plusieurs méthodes statiques pour créer des `SKMatrix` valeurs. Toutes les valeurs de retour `SKMatrix` :

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single)) avec un point pivot
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single)) pour un angle en radians
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single)) pour un angle en radians avec un point pivot
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single)) avec un point pivot
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix` définit également plusieurs méthodes statiques qui concatènent deux matrices, ce qui signifie qu’elles sont multipliées. Ces méthodes sont nommées [`Concat`](xref:SkiaSharp.SKMatrix.Concat*) , [`PostConcat`](xref:SkiaSharp.SKMatrix.PostConcat*) , et [`PreConcat`](xref:SkiaSharp.SKMatrix.PreConcat*) , et il y a deux versions de chacune. Ces méthodes n’ont aucune valeur de retour ; au lieu de cela, elles référencent des `SKMatrix` valeurs existantes via des `ref` arguments. Dans l’exemple suivant, `A` , `B` et `R` (pour « result ») sont toutes des `SKMatrix` valeurs.

Les deux `Concat` méthodes sont appelées comme suit :

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Celles-ci effectuent la multiplication suivante :

`R = B × A`

Les autres méthodes n’ont que deux paramètres. Le premier paramètre est modifié, et au retour de l’appel de la méthode, contient le produit des deux matrices. Les deux `PostConcat` méthodes sont appelées comme suit :

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Ces appels effectuent l’opération suivante :

`A = A × B`

Les deux `PreConcat` méthodes sont similaires :

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Ces appels effectuent l’opération suivante :

`A = B × A`

Les versions de ces méthodes avec tous les `ref` arguments sont légèrement plus efficaces pour appeler les implémentations sous-jacentes, mais il peut être difficile de lire votre code et de supposer que tout ce qui a un `ref` argument est modifié par la méthode. En outre, il est souvent commode de passer un argument qui est le résultat de l’une des `Make` méthodes, par exemple :

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Cela crée la matrice suivante :

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Il s’agit de la transformation d’échelle multipliée par la transformation de translation. Dans ce cas particulier, la `SKMatrix` structure fournit un raccourci avec une méthode nommée [`SetScaleTranslate`](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)) :

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

C’est l’une des rares fois où il est possible d’utiliser le constructeur en toute sécurité `SKMatrix` . La `SetScaleTranslate` méthode définit les neuf cellules de la matrice. Il est également possible d’utiliser le `SKMatrix` constructeur avec les `Rotate` méthodes et statiques `RotateDegrees` :

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Ces méthodes ne concatènent *pas* une transformation de rotation en une transformation existante. Les méthodes définissent toutes les cellules de la matrice. Elles sont fonctionnellement identiques aux `MakeRotation` méthodes et `MakeRotationDegrees` , à ceci près qu’elles n’instancient pas la `SKMatrix` valeur.

Supposons que vous avez un `SKPath` objet que vous souhaitez afficher, mais que vous préférez avoir une orientation différente, ou un autre point central. Vous pouvez modifier toutes les coordonnées de ce chemin d’accès en appelant la [`Transform`](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) méthode de `SKPath` avec un `SKMatrix` argument. La page **transformation du chemin d’accès** montre comment procéder. La [`PathTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) classe référence l' `HendecagramPath` objet dans un champ, mais utilise son constructeur pour appliquer une transformation à ce chemin d’accès :

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

L' `HendecagramPath` objet a un centre à (0,0) et les 11 points de l’étoile étendent vers l’extérieur de ce centre de 100 unités dans toutes les directions. Cela signifie que le chemin d’accès a des coordonnées positives et négatives. La page **transformation du chemin d’accès** préfère travailler avec une étoile trois fois plus grande et avec toutes les coordonnées positives. En outre, il ne doit pas faire pointer un point de l’étoile vers le haut. Elle souhaite à la place faire pointer un point de l’étoile vers le droite. (Étant donné que l’étoile a 11 points, elle ne peut pas avoir les deux.) Cela nécessite de faire pivoter l’étoile de 360 degrés divisée par 22.

Le constructeur génère un `SKMatrix` objet à partir de trois transformations distinctes à l’aide de la `PostConcat` méthode avec le modèle suivant, où A, B et C sont des instances de `SKMatrix` :

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Il s’agit d’une série de multiplications successives, le résultat est le suivant :

`A × B × C`

Les multiplications consécutives aident à comprendre ce que fait chaque transformation. La transformation d’échelle augmente la taille des coordonnées du chemin d’accès d’un facteur de 3, donc les coordonnées sont comprises entre – 300 et 300. La transformation de rotation fait pivoter l’étoile autour de son origine. La transformation translate le décale ensuite de 300 pixels vers la droite et vers le dessous, de sorte que toutes les coordonnées deviennent positives.

D’autres séquences produisent la même matrice. Voici un autre :

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Cela fait pivoter d’abord le chemin d’accès autour de son centre, puis le convertit 100 pixels vers la droite et vers le dessous, de sorte que toutes les coordonnées sont positives. La taille de l’étoile est ensuite augmentée par rapport à son nouveau coin supérieur gauche, qui est le point (0,0).

Le `PaintSurface` gestionnaire peut simplement afficher ce chemin d’accès :

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

Il apparaît dans l’angle supérieur gauche de la zone de dessin :

[![Capture d’écran triple de la page transformer le chemin](matrix-images/pathtransform-small.png)](matrix-images/pathtransform-large.png#lightbox "Capture d’écran triple de la page transformer le chemin")

Le constructeur de ce programme applique la matrice au chemin d’accès avec l’appel suivant :

```csharp
transformedPath.Transform(matrix);
```

Le chemin d’accès ne conserve *pas* cette matrice en tant que propriété. Au lieu de cela, il applique la transformation à toutes les coordonnées du chemin d’accès. Si `Transform` est à nouveau appelé, la transformation est de nouveau appliquée et la seule façon de revenir en arrière consiste à appliquer une autre matrice qui annule la transformation. Heureusement, la `SKMatrix` structure définit une [`TryInvert`](xref:SkiaSharp.SKMatrix.TryInvert*) méthode qui obtient la matrice qui inverse une matrice donnée :

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

La méthode est appelée, `TryInverse` car toutes les matrices ne sont pas réversibles, mais il n’est pas possible d’utiliser une matrice non réversible pour une transformation Graphics.

Vous pouvez également appliquer une transformation de matrice à une `SKPoint` valeur, à un tableau de points, à un `SKRect` , ou même simplement à un nombre unique au sein de votre programme. La `SKMatrix` structure prend en charge ces opérations avec une collection de méthodes qui commencent par le mot `Map` , comme celles-ci :

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Si vous utilisez cette dernière méthode, gardez à l’esprit que la structure ne peut `SKRect` pas représenter un rectangle pivoté. La méthode est uniquement utile pour une `SKMatrix` valeur représentant la traduction et la mise à l’échelle.

## <a name="interactive-experimentation"></a>Expérimentation interactive

Une façon d’obtenir une idée de la transformation affine consiste à déplacer de manière interactive trois angles d’une image bitmap sur l’écran et à voir les résultats de la transformation. Il s’agit de l’idée sous-jacente de la page **afficher la matrice affine** . Cette page requiert deux autres classes qui sont également utilisées dans d’autres démonstrations :

La [`TouchPoint`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) classe affiche un cercle translucide qui peut être glissé à l’écran. `TouchPoint` requiert `SKCanvasView` l’attachement d’un élément ou d’un élément parent d’un `SKCanvasView` [`TouchEffect`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) . Affectez à la propriété `Capture` la valeur `true`. Dans le `TouchAction` Gestionnaire d’événements, le programme doit appeler la `ProcessTouchEvent` méthode dans `TouchPoint` pour `TouchPoint` chaque instance. La méthode retourne `true` si l’événement tactile a entraîné le déplacement du point tactile. En outre, le `PaintSurface` gestionnaire doit appeler la `Paint` méthode dans chaque `TouchPoint` instance, en lui passant l' `SKCanvas` objet.

`TouchPoint` illustre une façon courante qu’un visuel SkiaSharp puisse être encapsulé dans une classe distincte. La classe peut définir des propriétés pour spécifier les caractéristiques du visuel, et une méthode nommée `Paint` avec un `SKCanvas` argument peut le rendre.

La `Center` propriété de `TouchPoint` indique l’emplacement de l’objet. Cette propriété peut être définie pour initialiser l’emplacement. la propriété change lorsque l’utilisateur fait glisser le cercle autour du canevas.

La **page afficher les matrices affine** requiert également la [`MatrixDisplay`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) classe. Cette classe affiche les cellules d’un `SKMatrix` objet. Il a deux méthodes publiques : `Measure` pour obtenir les dimensions de la matrice rendue et `Paint` pour l’afficher. La classe contient une `MatrixPaint` propriété de type `SKPaint` qui peut être remplacée pour une couleur ou une taille de police différente.

Le fichier [**ShowAffineMatrixPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) instancie `SKCanvasView` et attache un `TouchEffect` . Le fichier code-behind [**ShowAffineMatrixPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) crée trois `TouchPoint` objets, puis les définit sur des positions correspondant aux trois angles d’une image bitmap qu’il charge à partir d’une ressource incorporée :

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

Une matrice affine est définie de manière unique par trois points. Les trois `TouchPoint` objets correspondent aux angles supérieur gauche, supérieur droit et inférieur gauche de l’image bitmap. Étant donné qu’une matrice affine est uniquement en mesure de transformer un rectangle en parallélogramme, le quatrième point est implicite par les trois autres. Le constructeur se termine par un appel à `ComputeMatrix` , qui calcule les cellules d’un `SKMatrix` objet à partir de ces trois points.

Le `TouchAction` Gestionnaire appelle la `ProcessTouchEvent` méthode de chaque `TouchPoint` . La `scale` valeur convertit des Xamarin.Forms coordonnées en pixels :

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Si un `TouchPoint` a été déplacé, la méthode appelle `ComputeMatrix` à nouveau et invalide la surface.

La `ComputeMatrix` méthode détermine la matrice impliquée par ces trois points. La matrice appelée `A` transforme un rectangle carré d’un pixel en un parallélogramme basé sur les trois points, tandis que la transformation d’échelle appelée `S` met à l’échelle l’image bitmap sur un rectangle carré d’un pixel. La matrice composite est `S` × `A` :

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
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

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Enfin, la `PaintSurface` méthode restitue la bitmap en fonction de cette matrice, affiche la matrice au bas de l’écran et restitue les points tactiles aux trois angles de l’image bitmap :

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

L’écran iOS ci-dessous montre l’image bitmap lorsque la page est chargée pour la première fois, tandis que les deux autres écrans l’affichent après une manipulation :

[![Capture d’écran triple de la page afficher la matrice affine](matrix-images/showaffinematrix-small.png)](matrix-images/showaffinematrix-large.png#lightbox "Capture d’écran triple de la page afficher la matrice affine")

Bien qu’il semble que les points tactiles fassent glisser les angles de l’image bitmap, il ne s’agit que d’une illusion. La matrice calculée à partir des points tactile transforme le bitmap afin que les angles coïncident avec les points tactiles.

Il est plus naturel pour les utilisateurs de déplacer, redimensionner et faire pivoter des bitmaps en faisant glisser les angles, mais en utilisant un ou deux doigts directement sur l’objet pour faire glisser, pincer et faire pivoter. Ce sujet est abordé dans la [**manipulation tactile**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)de l’article suivant.

## <a name="the-reason-for-the-3-by-3-matrix"></a>Motif de la matrice 3 par 3

Il est possible qu’un système graphique à deux dimensions nécessite uniquement une matrice de transformation 2 par 2 :

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Cela fonctionne pour la mise à l’échelle, la rotation et même l’inclinaison, mais il n’est pas compatible avec les transformations les plus basiques, à savoir la traduction.

Le problème est que la matrice 2 par 2 représente une transformation *linéaire* en deux dimensions. Une transformation linéaire conserve certaines opérations arithmétiques de base, mais l’une des implications est qu’une transformation linéaire ne modifie jamais le point (0,0). Une transformation linéaire rend la traduction impossible.

Dans trois dimensions, une matrice de transformation linéaire se présente comme suit :

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

La cellule nommée `SkewXY` signifie que la valeur correspond à la coordonnée x en fonction des valeurs de Y ; la cellule `SkewXZ` signifie que la valeur correspond à la coordonnée x en fonction des valeurs de Z ; et que les valeurs s’inclinent de la même façon pour les autres `Skew` cellules.

Il est possible de limiter cette matrice de transformation 3D à un plan à deux dimensions en affectant à la valeur `SkewZX` `SkewZY` 0, et `ScaleZ` à 1 :

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Si les graphiques à deux dimensions sont entièrement dessinés sur le plan dans l’espace 3D où Z est égal à 1, la multiplication de la transformation ressemble à ceci :

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Tout reste sur le plan à deux dimensions où Z est égal à 1, mais `SkewXZ` les `SkewYZ` cellules et deviennent effectivement des facteurs de traduction à deux dimensions.

Voici comment une transformation linéaire en trois dimensions sert de transformation non linéaire à deux dimensions. (Par analogie, les transformations dans les graphiques 3D sont basées sur une matrice 4 par 4.)

La `SKMatrix` structure dans SkiaSharp définit les propriétés de cette troisième ligne :

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Les valeurs non null de `Persp0` et `Persp1` entraînent des transformations qui déplacent des objets à partir du plan à deux dimensions où Z est égal à 1. Ce qui se passe lorsque ces objets sont déplacés vers ce plan est traité dans l’article sur les [**transformations non affines**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)