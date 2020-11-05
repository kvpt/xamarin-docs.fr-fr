---
title: Rotations 3D dans SkiaSharp
description: Cet article explique comment utiliser des transformations non affines pour faire pivoter des objets 2D dans l’espace 3D et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dfc7ec9b029cedf5584590e17565069bbf83ed2a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373170"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotations 3D dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilisez des transformations non affines pour faire pivoter des objets 2D dans l’espace 3D._

Une application courante de transformations non affines simule la rotation d’un objet 2D dans l’espace 3D :

![Chaîne de texte pivotée dans l’espace 3D](3d-rotation-images/3drotationsexample.png)

Ce travail implique l’utilisation de rotations à trois dimensions, puis la dérivation d’une transformation non affine `SKMatrix` qui effectue ces rotations 3D.

Il est difficile de développer cette `SKMatrix` transformation qui fonctionne uniquement dans les deux dimensions. Le travail devient beaucoup plus facile lorsque cette matrice 3 par 3 est dérivée d’une matrice 4 par 4 utilisée dans des graphiques 3D. SkiaSharp comprend la [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) classe à cet effet, mais un arrière-plan dans les graphiques 3D est nécessaire pour comprendre les rotations 3D et la matrice de transformation 4 par 4.

Un système de coordonnées tridimensionnel ajoute un troisième axe appelé Z. conceptuellement, l’axe Z est perpendiculaire à l’écran. Les points de coordonnées dans l’espace 3D sont indiqués par trois nombres : (x, y, z). Dans le système de coordonnées 3D utilisé dans cet article, l’extension de la valeur X est à droite et les valeurs d’incrément de Y s’affichent comme dans deux dimensions. L’amélioration des valeurs Z positives s’affiche à l’écran. L’origine est l’angle supérieur gauche, comme dans les graphiques 2D. Vous pouvez considérer l’écran comme un plan XY avec l’axe Z aux angles droits sur ce plan.

C’est ce que l’on appelle un système de coordonnées de gauche. Si vous pointez le index pour votre main gauche dans la direction des coordonnées X positives (à droite) et votre doigt central dans le sens de l’augmentation des coordonnées Y (vers le bas), votre curseur pointe dans la direction de l’augmentation des coordonnées Z, qui s’étend à partir de l’écran.

Dans les graphiques 3D, les transformations sont basées sur une matrice 4 par 4. Voici la matrice d’identité 4-par-4 :

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Lors de l’utilisation d’une matrice 4 par 4, il est pratique d’identifier les cellules avec leurs numéros de ligne et de colonne :

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Toutefois, la `Matrix44` classe SkiaSharp est un peu différente. La seule façon de définir ou d’extraire des valeurs de cellules individuelles dans `SKMatrix44` est d’utiliser l' [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) indexeur. Les index de ligne et de colonne sont de base zéro plutôt qu’un, et les lignes et les colonnes sont permutées. La cellule M14 dans le diagramme ci-dessus est accessible à l’aide de l’indexeur `[3, 0]` dans un `SKMatrix44` objet.

Dans un système graphique 3D, un point 3D (x, y, z) est converti en matrice 1 par 4 pour la multiplication par la matrice de transformation 4 x 4 :

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Analogues aux transformations 2D qui ont lieu dans trois dimensions, les transformations 3D sont supposées avoir lieu dans quatre dimensions. La quatrième dimension est appelée W et l’espace 3D est supposé exister dans l’espace 4D où les coordonnées W sont égales à 1. Les formules de transformation sont les suivantes :

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

Il est évident que les formules de transformation que les cellules `M11` , `M22` , sont des `M33` facteurs de mise à l’échelle dans les directions x, y et z, et `M41` , et sont des `M42` `M43` facteurs de translation dans les directions x, y et z.

Pour reconvertir ces coordonnées en espace 3D où W est égal à 1, les coordonnées x, y et z sont toutes divisées par w :

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

Cette division par w’fournit une perspective dans l’espace 3D. Si w’est égal à 1, aucune perspective ne se produit.

Les rotations dans l’espace 3D peuvent être assez complexes, mais les rotations les plus simples sont celles autour des axes X, Y et Z. Une rotation d’angle α autour de l’axe X est cette matrice :

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

Les valeurs de X restent les mêmes lorsqu’elles sont soumises à cette transformation. La rotation autour de l’axe Y laisse les valeurs de Y inchangées :

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

La rotation autour de l’axe Z est la même que dans les graphiques 2D :

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

Le sens de la rotation est implicite par la direction du système de coordonnées. Il s’agit d’un système gauche. par conséquent, si vous pointez le curseur de votre main gauche vers des valeurs croissantes pour un axe particulier, vers la droite pour la rotation autour de l’axe X, vers le bas pour la rotation autour de l’axe Y et vers la rotation autour de l’axe Z, la courbe de vos autres doigts indique la direction de rotation pour les angles positifs

`SKMatrix44` a généralisé [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) des méthodes et statiques [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) qui vous permettent de spécifier l’axe autour duquel la rotation se produit :

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Pour la rotation autour de l’axe X, définissez les trois premiers arguments sur 1, 0, 0. Pour la rotation autour de l’axe Y, affectez-lui la valeur 0, 1, 0, et pour la rotation autour de l’axe Z, affectez-lui la valeur 0, 0, 1.

La quatrième colonne du 4-sur-4 est pour la perspective. Le `SKMatrix44` n’a pas de méthode pour créer des transformations de perspective, mais vous pouvez en créer un vous-même en utilisant le code suivant :

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

La raison du nom de l’argument `depth` sera évidente dans un instant. Ce code crée la matrice :

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Les formules de transformation entraînent le calcul suivant de w' :

`w' = –z / depth + 1`

Cela permet de réduire les coordonnées X et Y lorsque les valeurs de Z sont inférieures à zéro (conceptuellement derrière le plan XY) et d’augmenter les coordonnées X et Y pour les valeurs positives de Z. Lorsque la coordonnée Z `depth` est égale à, w est égal à zéro et les coordonnées deviennent infinies. Les systèmes graphiques en trois dimensions sont conçus autour d’une métaphore d’appareil photo, et la `depth` valeur ici représente la distance de la caméra par rapport à l’origine du système de coordonnées. Si un objet graphique a une coordonnée Z qui est une `depth` unité de l’origine, il touche conceptuellement l’objectif de l’appareil photo et devient infiniment grand.

Gardez à l’esprit que vous utiliserez probablement cette `perspectiveMatrix` valeur en combinaison avec les matrices de rotation. Si la rotation d’un objet Graphics a des coordonnées X ou Y supérieures à `depth` , la rotation de cet objet dans l’espace 3D est susceptible d’impliquer des coordonnées Z supérieures à `depth` . Cela doit être évité ! Lorsque `perspectiveMatrix` vous créez, vous souhaitez affecter `depth` à une valeur suffisamment grande pour toutes les coordonnées dans l’objet Graphics, quelle que soit la façon dont elle pivote. Cela garantit qu’il n’y a jamais de division par zéro.

La combinaison de rotations et de perspective 3D nécessite la multiplication des matrices 4 par 4. À cet effet, `SKMatrix44` définit des méthodes de concaténation. Si `A` et `B` sont des `SKMatrix44` objets, le code suivant affecte une valeur égale à a × B :

```csharp
A.PostConcat(B);
```

Lorsqu’une matrice de transformation 4 par 4 est utilisée dans un système graphique 2D, elle est appliquée aux objets 2D. Ces objets sont plats et sont supposés avoir des coordonnées Z de zéro. La multiplication de transformation est un peu plus simple que la transformation présentée précédemment :

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Cette valeur 0 pour z entraîne des formules de transformation qui n’impliquent pas de cellules dans la troisième ligne de la matrice :

x' = M11 · x + M21 · o + M41

y' = M12 · x + M22 · o + M42

z' = M13 · x + M23 · y + M43

w' = M14 · x + M24 · o + M44

En outre, la coordonnée z n’est pas pertinente ici. Lorsqu’un objet 3D est affiché dans un système graphique 2D, il est réduit en un objet à deux dimensions en ignorant les valeurs de coordonnée Z. Les formules de transformation sont en fait simplement ces deux :

`x" = x' / w'`

`y" = y' / w'`

Cela signifie que la troisième ligne *et* la troisième colonne de la matrice 4 par 4 peuvent être ignorées.

Mais si c’est le cas, pourquoi la matrice 4 par 4 est-elle nécessaire en premier lieu ?

Bien que la troisième ligne et la troisième colonne du 4 au 4 ne soient pas pertinentes pour les transformations à deux dimensions, la troisième ligne et la deuxième colonne jouent un rôle avant celui *-* ci lorsque différentes `SKMatrix44` valeurs sont multipliées ensemble. Supposons, par exemple, que vous multipliez la rotation autour de l’axe Y par la transformation perspective :

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

Dans le produit, la cellule `M14` contient maintenant une valeur de perspective. Si vous souhaitez appliquer cette matrice à des objets 2D, la troisième ligne et la colonne sont éliminées pour la convertir en matrice 3 par 3 :

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

À présent, il peut être utilisé pour transformer un point 2D :

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Les formules de transformation sont les suivantes :

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

Tout diviser par z :

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Lorsque des objets 2D pivotent avec un angle positif autour de l’axe Y, les valeurs X positives sont repassées à l’arrière-plan tandis que les valeurs X négatives sont transmises au premier plan. Les valeurs X semblent se rapprocher de l’axe Y (qui est régie par la valeur cosinus), car les coordonnées les plus éloignées de l’axe Y deviennent plus petites ou plus volumineuses lorsqu’elles se déplacent de la visionneuse ou plus près de la visionneuse.

Lorsque vous utilisez `SKMatrix44` , effectuez toutes les opérations de rotation et de perspective 3D en multipliant différentes `SKMatrix44` valeurs. Vous pouvez ensuite extraire une matrice 3D à deux dimensions de la matrice 4 par 4 à l’aide [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) de la propriété de la `SKMatrix44` classe. Cette propriété retourne une `SKMatrix` valeur familière.

La page **rotation 3D** vous permet d’expérimenter la rotation 3D. Le fichier [**Rotation3DPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) instancie quatre curseurs pour définir la rotation autour des axes X, Y et Z, et pour définir une valeur de profondeur :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Notez que le `depthSlider` est initialisé avec une `Minimum` valeur de 250. Cela implique que l’objet 2D pivoté ici possède des coordonnées X et Y limitées à un cercle défini par un rayon de 250 pixels autour de l’origine. Toute rotation de cet objet dans l’espace 3D aura toujours pour résultat des valeurs de coordonnée inférieures à 250.

Le fichier code-behind [**Rotation3DPage.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) se charge dans une bitmap de 300 pixels carrés :

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Si la transformation 3D est centrée sur cette image bitmap, les coordonnées X et Y sont comprises entre-150 et 150, tandis que les angles sont de 212 pixels du centre, tout se trouve dans le rayon de 250 pixels.

Le `PaintSurface` Gestionnaire crée des `SKMatrix44` objets en fonction des curseurs et les multiplie à l’aide de `PostConcat` . La `SKMatrix` valeur extraite de l' `SKMatrix44` objet final est entourée de transformations de translation pour centrer la rotation au centre de l’écran :

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
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

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

Lorsque vous faites des essais avec le quatrième curseur, vous remarquerez que les différents paramètres de profondeur ne déplacent pas l’objet plus loin de la visionneuse, mais modifient plutôt l’étendue de l’effet de perspective :

[![Capture d’écran triple de la page rotation 3D](3d-rotation-images/rotation3d-small.png)](3d-rotation-images/rotation3d-large.png#lightbox "Capture d’écran triple de la page rotation 3D")

La **rotation animée 3D** utilise également `SKMatrix44` pour animer une chaîne de texte dans l’espace 3D. L' `textPaint` objet défini en tant que champ est utilisé dans le constructeur pour déterminer les limites du texte :

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

La `OnAppearing` substitution définit trois Xamarin.Forms `Animation` objets pour animer les `xRotationDegrees` `yRotationDegrees` champs, et `zRotationDegrees` à des taux différents. Notez que les périodes de ces animations sont définies sur des nombres premiers (5 secondes, 7 secondes et 11 secondes), de sorte que la combinaison globale se répète toutes les 385 secondes, ou plus de 10 minutes :

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

Comme dans le programme précédent, le `PaintCanvas` Gestionnaire crée `SKMatrix44` des valeurs pour la rotation et la perspective, et les multiplie :

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Cette rotation 3D est entourée de plusieurs transformations 2D pour déplacer le centre de rotation au centre de l’écran, et pour mettre à l’échelle la taille de la chaîne de texte pour qu’elle soit de la même largeur que l’écran :

[![Capture d’écran triple de la page 3D de rotation animée](3d-rotation-images/animatedrotation3d-small.png)](3d-rotation-images/animatedrotation3d-large.png#lightbox "Capture d’écran triple de la page 3D de rotation animée")

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)