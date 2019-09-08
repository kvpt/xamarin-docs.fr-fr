---
title: Manipulations tactiles
description: Cet article explique comment utiliser des transformations de matrice pour implémenter en faisant glisser des fonctions tactiles, de pincement et de rotation et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
ms.openlocfilehash: 4e51c1a201ce91b2b376b802f0f45e8aad4b712f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772979"
---
# <a name="touch-manipulations"></a>Manipulations tactiles

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Matrice d’utiliser les transformations à implémenter en faisant glisser des fonctions tactiles, de pincement et de rotation_

Dans les environnements de l’interaction tactile multipoint, telles que celles sur les appareils mobiles, les utilisateurs utilisent souvent leurs doigts pour manipuler des objets sur l’écran. Des mouvements courants tels que d’une opération de glissement d’un doigt et un pincement de deux doigts peuvent déplacer et mettre à l’échelle des objets ou même les faire pivoter. Ces mouvements sont généralement implémentées à l’aide de matrices de transformation, et cet article vous montre comment procéder.

![](touch-images/touchmanipulationsexample.png "Une image bitmap soumise à translation, de mise à l’échelle et rotation")

Tous les exemples ci-après utilisent l’effet de suivi de tactile Xamarin.Forms présentée dans l’article [ **appel des événements à partir des effets**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Déplacement et la traduction

Une des applications plus importants des transformations de matrice est le traitement de tactile. Un seul [ `SKMatrix` ](xref:SkiaSharp.SKMatrix) valeur permettre consolider une série d’opérations de contact. 

Pour faire glisser un seul doigt, le `SKMatrix` valeur effectue la traduction. Cela est illustré dans le **Bitmap en faisant glisser** page. Le fichier XAML instancie un `SKCanvasView` dans un Xamarin.Forms `Grid`. Un `TouchEffect` objet a été ajouté à la `Effects` collection de qui `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.BitmapDraggingPage"
             Title="Bitmap Dragging">
    
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

En théorie, le `TouchEffect` objet peut être ajouté directement à la `Effects` collection de la `SKCanvasView`, mais qui ne fonctionne pas sur toutes les plateformes. Étant donné que le `SKCanvasView` est la même taille que le `Grid` dans cette configuration, attachement à la `Grid` fonctionne aussi bien.

Le fichier code-behind se charge dans une ressource bitmap dans son constructeur et l’affiche dans le `PaintSurface` gestionnaire :

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    // Bitmap and matrix for display
    SKBitmap bitmap;
    SKMatrix matrix = SKMatrix.MakeIdentity();
    ···

    public BitmapDraggingPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, new SKPoint());
    }
}
```

Sans aucun code supplémentaire, le `SKMatrix` valeur est toujours la matrice d’identité, et il n’a aucun effet sur l’affichage de l’image bitmap. L’objectif de la `OnTouchEffectAction` gestionnaire défini dans le fichier XAML est de modifier la valeur de la matrice afin de refléter les manipulations tactiles.

Le `OnTouchEffectAction` gestionnaire commence par convertir la Xamarin.Forms `Point` valeur dans un SkiaSharp `SKPoint` valeur. Il s’agit simplement de mise à l’échelle selon la `Width` et `Height` propriétés de `SKCanvasView` (qui sont des unités indépendantes du périphérique) et le `CanvasSize` propriété, qui est exprimé en unités de pixels :

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    ···
    // Touch information
    long touchId = -1;
    SKPoint previousPoint;
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point = 
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point))
                {
                    touchId = args.Id;
                    previousPoint = point;
                }
                break;

            case TouchActionType.Moved:
                if (touchId == args.Id)
                {
                    // Adjust the matrix for the new position
                    matrix.TransX += point.X - previousPoint.X;
                    matrix.TransY += point.Y - previousPoint.Y;
                    previousPoint = point;
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = -1;
                break;
        }
    }
    ···
}
```

Lorsqu’un doigt touche en premier l’écran, un événement de type `TouchActionType.Pressed` est déclenché. La première tâche consiste à déterminer si le doigt touche l’image bitmap. Cette tâche est souvent appelée _le test de positionnement_. Dans ce cas, le test de positionnement est possible en créant un `SKRect` valeur correspondant à l’image bitmap, en appliquant la transformation de matrice à l’aide de `MapRect`et puis de déterminer si le point tactile est à l’intérieur du rectangle transformé.

Si tel est le cas, le `touchId` champ est défini sur l’ID tactile et la position du doigt est enregistrée.

Pour le `TouchActionType.Moved` événement, les facteurs de translation de la `SKMatrix` valeur sont ajustées en fonction de la position actuelle du doigt et la nouvelle position du doigt. Que la nouvelle position est enregistrée pour la prochaine fois et le `SKCanvasView` est invalidée.

Comme vous faire des essais avec ce programme, notez que vous pouvez uniquement faire glisser l’image bitmap lorsque votre doigt touche une zone où l’image bitmap est affichée. Bien que cette restriction n’est pas très importante pour ce programme, il devient essentiel lors de la manipulation des bitmaps plusieurs.

## <a name="pinching-and-scaling"></a>Pincement et mise à l’échelle

Que voulez-vous se produire lorsque deux doigts touch la bitmap ? Si les deux doigts déplacent en parallèle, vous souhaitez probablement la bitmap à déplacer, ainsi que les doigts. Si les deux doigts effectuer un pincement ou étirement l’opération, vous souhaiterez la bitmap à rotation (abordée dans la section suivante) ou la mise à l’échelle. Lors de la mise à l’échelle une image bitmap, plus pratique pour deux doigts rester dans les mêmes positions par rapport à l’image bitmap et pour la bitmap à mettre à l’échelle en conséquence.

Gestion à la fois des deux doigts semble compliquée, mais n’oubliez pas que le `TouchAction` gestionnaire reçoit uniquement les informations sur un doigt à la fois. Si deux doigts manipulent l’image bitmap, puis un doigt a changé de position pour chaque événement, mais l’autre n’a pas changé. Dans le **mise à l’échelle d’une Bitmap** page le code ci-dessous, le doigt qui n’a pas changé position est appelé le _pivot_ point étant la transformation par rapport à ce point.

Une différence entre ce programme et le programme précédent est que plusieurs ID doivent être enregistrés. Un dictionnaire est utilisé à cet effet, où l’ID tactile est la clé de dictionnaire et la valeur de dictionnaire est la position actuelle de ce doigt :

```csharp
public partial class BitmapScalingPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point) && !touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Add(args.Id, point);
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger scale and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index of non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points involved in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Scaling factors are ratios of those
                        float scaleX = newVector.X / oldVector.X;
                        float scaleY = newVector.Y / oldVector.Y;

                        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
                            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
                        {
                            // If something bad hasn't happened, calculate a scale and translation matrix
                            SKMatrix scaleMatrix = 
                                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);

                            SKMatrix.PostConcat(ref matrix, scaleMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }
    ···
}
```

La gestion de la `Pressed` action est presque identique à celui de l’ID de programme, sauf que le précédent et le point de contact sont ajoutées au dictionnaire. Le `Released` et `Cancelled` actions supprimer l’entrée de dictionnaire.

La gestion de la `Moved` action est plus complexe, toutefois. S’il existe un seul doigt impliquées, le traitement est très semblable à celle du programme précédent. Pour deux ou plusieurs doigts, le programme doit également obtenir des informations à partir du dictionnaire impliquant le doigt ne bouge pas. Pour ce faire copier les clés de dictionnaire dans un tableau, puis en comparant la première clé avec l’ID du doigt en cours de déplacement. Qui permet au programme obtenir le point pivot correspondant au doigt qui ne bouge pas.

Ensuite, le programme calcule deux vecteurs de la nouvelle position du doigt par rapport au point pivot et l’ancienne position du doigt par rapport au point pivot. Les proportions des ces vecteurs sont des facteurs de mise à l’échelle. Étant donné que la division par zéro est possible, ceux-ci doivent vérifier les valeurs infinies ou NaN (pas un nombre). Si tout va bien, une transformation de mise à l’échelle est concaténée avec la `SKMatrix` enregistré sous la forme d’un champ de valeur.

Lorsque vous testez cette page, vous remarquerez que vous pouvez faire glisser l’image bitmap avec un ou deux doigts, ou mettre à l’échelle avec deux doigts. La mise à l’échelle est _ANISOTROPIQUE_, ce qui signifie que la mise à l’échelle peut être différent dans le sens horizontal et vertical. Cela déforme le rapport hauteur / largeur, mais vous permet également de faire pivoter la bitmap pour rendre une image miroir. Vous pouvez également découvrir que vous pouvez réduire la bitmap à une dimension de zéro, et il disparaît. Dans le code de production, vous souhaitez protéger contre cela.

## <a name="two-finger-rotation"></a>Rotation de deux doigts

Le **pivoter Bitmap** page vous permet d’utiliser deux doigts pour la rotation ou de mise à l’échelle isotropes. L’image bitmap conserve toujours ses proportions correcte. À l’aide de deux doigts pour la rotation et la mise à l’échelle ANISOTROPIQUE ne fonctionne pas très bien, car le mouvement des doigts est très similaire pour les deux tâches.

La première grande différence dans ce programme est la logique de test d’atteinte. Les programmes précédentes utilisés le `Contains` méthode de `SKRect` pour déterminer si le point tactile est dans le rectangle transformé qui correspond à l’image bitmap. Mais comme l’utilisateur manipule la bitmap, l’image bitmap peut être pivoté, et `SKRect` ne peut pas représenter correctement un rectangle pivoté. Vous pouvez la peur que la logique de test de positionnement doit implémenter plutôt complexe géométrie analytique dans ce cas.

Toutefois, un raccourci est disponible : Déterminer si un point se trouve dans les limites d’un rectangle transformé revient à déterminer si un point transformé inverse se trouve dans les limites du rectangle non transformé. C’est un calcul plus facile de quantité et la logique peut continuer à utiliser le pratique `Contains` méthode :

```csharp
public partial class BitmapRotationPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!touchDictionary.ContainsKey(args.Id))
                {
                    // Invert the matrix
                    if (matrix.TryInvert(out SKMatrix inverseMatrix))
                    {
                        // Transform the point using the inverted matrix
                        SKPoint transformedPoint = inverseMatrix.MapPoint(point);

                        // Check if it's in the untransformed bitmap rectangle
                        SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);

                        if (rect.Contains(transformedPoint))
                        {
                            touchDictionary.Add(args.Id, point);
                        }
                    }
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger rotate, scale, and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Find angles from pivot point to touch points
                        float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                        float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                        // Calculate rotation matrix
                        float angle = newAngle - oldAngle;
                        SKMatrix touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                        // Effectively rotate the old vector
                        float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                        oldVector.X = magnitudeRatio * newVector.X;
                        oldVector.Y = magnitudeRatio * newVector.Y;

                        // Isotropic scaling!
                        float scale = Magnitude(newVector) / Magnitude(oldVector);

                        if (!float.IsNaN(scale) && !float.IsInfinity(scale))
                        {
                            SKMatrix.PostConcat(ref touchMatrix,
                                SKMatrix.MakeScale(scale, scale, pivotPoint.X, pivotPoint.Y));

                            SKMatrix.PostConcat(ref matrix, touchMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }

    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
    ···
}
```

La logique pour le `Moved` événement démarre comme le programme précédent. Deux vecteurs nommés `oldVector` et `newVector` sont calculés en fonction du précédent et le point actuel du doigt en mouvement et le point pivot du doigt statique. Mais ensuite les angles de ces vecteurs sont déterminées, et la différence est l’angle de rotation.

Mise à l’échelle peut-être également être impliqué, donc le vecteur ancien est pivoté en fonction de l’angle de rotation. L’importance relative de deux vecteurs est désormais le facteur d’échelle. Notez que le même `scale` valeur est utilisée pour horizontal et la mise à l’échelle verticale afin que la mise à l’échelle est Isotrope. Le `matrix` champ est ajusté par la matrice de rotation et une matrice de mise à l’échelle.

Si votre application doit implémenter tactile de traitement pour une seule bitmap (ou d’un autre objet), vous pouvez adapter le code à partir de ces trois exemples pour votre propre application. Mais si vous devez implémenter tactile de traitement pour les bitmaps plusieurs, vous souhaiterez probablement encapsuler ces touch des opérations dans d’autres classes.

## <a name="encapsulating-the-touch-operations"></a>Qui encapsule les opérations tactile

Le **Manipulation tactile** page montre la manipulation tactile d’une seule bitmap, mais à l’aide de plusieurs autres fichiers qui encapsulent une grande partie de la logique ci-dessus. Le premier de ces fichiers est le [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) énumération, qui indique les différents types de manipulation tactile implémentée par le code que vous verrez :

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` est une opération de glissement d’un doigt qui est implémentée avec la traduction. Toutes les options suivantes également inclure le panoramique, mais impliquent deux doigts : `IsotropicScale` est une opération de pincement qui résulte dans l’objet de mise à l’échelle de manière égale dans le sens horizontal et vertical. `AnisotropicScale` permet la mise à l’échelle inégaux.

Le `ScaleRotate` option est de mise à l’échelle des deux doigts et de rotation. Mise à l’échelle est Isotrope. Comme mentionné précédemment, la mise en œuvre de rotation de deux doigts avec mise à l’échelle ANISOTROPIQUE est problématique, car les doigt mouvements sont essentiellement les mêmes.

Le `ScaleDualRotate` option ajoute la rotation d’un doigt. Quand un seul doigt fait glisser l’objet, l’objet glissé est tout d’abord pivoter autour de son centre afin que le centre de l’objet est alignée avec le vecteur de déplacement.

Le [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) fichier inclut un `Picker` avec les membres de la `TouchManipulationMode` énumération :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos.Transforms"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:TouchManipulationMode}">
                    <x:Static Member="local:TouchManipulationMode.None" />
                    <x:Static Member="local:TouchManipulationMode.PanOnly" />
                    <x:Static Member="local:TouchManipulationMode.IsotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.AnisotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.ScaleRotate" />
                    <x:Static Member="local:TouchManipulationMode.ScaleDualRotate" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>
        
        <Grid BackgroundColor="White"
              Grid.Row="1">
            
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

Vers le bas est un `SKCanvasView` et un `TouchEffect` attaché à la cellule unique `Grid` qui l’englobe.

Le [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) fichier code-behind a un `bitmap` champ, mais il n’est pas de type `SKBitmap`. Le type est `TouchManipulationBitmap` (une classe que vous le verrez bientôt) :

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

Le constructeur instancie un `TouchManipulationBitmap` objet, en passant au constructeur un `SKBitmap` obtenu à partir d’une ressource incorporée. Le constructeur se termine en définissant le `Mode` propriété de la `TouchManager` propriété de la `TouchManipulationBitmap` objet à un membre de la `TouchManipulationMode` énumération.

Le `SelectedIndexChanged` gestionnaire pour le `Picker` cela définit également `Mode` propriété :

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            bitmap.TouchManager.Mode = (TouchManipulationMode)picker.SelectedItem;
        }
    }
    ...
}
```

Le `TouchAction` Gestionnaire de la `TouchEffect` instancié dans les appels de fichier XAML, deux méthodes dans `TouchManipulationBitmap` nommé `HitTest` et `ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Si le `HitTest` retourne de la méthode `true` &mdash; ce qui signifie qu’un doigt a touché l’écran dans la zone occupée par l’image bitmap &mdash; l’ID tactile est ajouté à la `TouchIds` collection. Cet ID représente la séquence d’événements tactiles pour ce doigt jusqu'à ce que le doigt se soulève à partir de l’écran. Si plusieurs doigts touch à l’image bitmap, puis le `touchIds` collection contient un ID tactile pour chaque doigt.

Le `TouchAction` gestionnaire appelle également la `ProcessTouchEvent` classe dans `TouchManipulationBitmap`. C’est là où certains (mais pas toutes) des fonctions tactiles véritable traitement se produit.

Le [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe est une classe wrapper pour `SKBitmap` qui contient le code pour restituer l’image bitmap et traiter les événements tactiles. Il fonctionne conjointement avec la plus généralisée du code dans un `TouchManipulationManager` classe (qui vous le verrez bientôt).

Le `TouchManipulationBitmap` constructeur enregistre le `SKBitmap` et instancie les deux propriétés, la `TouchManager` propriété de type `TouchManipulationManager` et `Matrix` propriété de type `SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

Cela `Matrix` propriété est la transformation accumulée résultant de toutes les activités tactile. Comme vous le verrez, chaque événement tactile est résolu en une matrice, qui est ensuite concaténée avec le `SKMatrix` valeur stockée par le `Matrix` propriété.

Le `TouchManipulationBitmap` objet qui se dessine lui-même son `Paint` (méthode). L’argument est un `SKCanvas` objet. Cela `SKCanvas` disposez peut-être déjà d’une transformation appliquée, donc la `Paint` méthode concatène le `Matrix` propriété associé à la bitmap à la transformation existante et restaure la zone de dessin lorsqu’il a terminé :

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

Le `HitTest` retourne de la méthode `true` si l’utilisateur touche l’écran à un point dans les limites de la bitmap. Cet exemple utilise la logique indiquée précédemment dans le **Bitmap Rotation** page :

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

La deuxième méthode publique dans `TouchManipulationBitmap` est `ProcessTouchEvent`. Lorsque cette méthode est appelée, il a déjà été établi que l’événement tactile appartienne à cette image bitmap particulier. La méthode gère un dictionnaire de [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objets, qui est simplement le point précédent et le nouveau point de chaque doigt :

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Voici le dictionnaire et le `ProcessTouchEvent` méthode proprement dite :

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

Dans le `Moved` et `Released` événements, les appels de méthode `Manipulate`. À cette occasion, le `touchDictionary` contient un ou plusieurs `TouchManipulationInfo` objets. Si le `touchDictionary` contient un élément, il est probable que le `PreviousPoint` et `NewPoint` valeurs sont inégales et représentent le mouvement d’un doigt. Si plusieurs doigts touchent l’image bitmap, le dictionnaire contient plusieurs éléments, mais qu’un seul de ces éléments a différents `PreviousPoint` et `NewPoint` valeurs. Les autres ont égal `PreviousPoint` et `NewPoint` valeurs.

C'est important: La `Manipulate` méthode peut supposer qu’elle traite le mouvement d’un seul doigt. Au moment de cet appel, aucune des autres les doigts sont mobile, et si elles sont vraiment déplacez (comme c’est probablement), ces mouvements sont traités dans les appels suivants à `Manipulate`.

Le `Manipulate` méthode copie tout d’abord le dictionnaire dans un tableau pour des raisons pratiques. Il ignore tout élément autre que les deux premières entrées. Si plus de deux doigts tente de manipuler l’image bitmap, les autres sont ignorés. `Manipulate` est le dernier membre de `TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

Si un doigt manipule l’image bitmap, `Manipulate` appelle le `OneFingerManipulate` méthode de la `TouchManipulationManager` objet. Pour deux doigts, il appelle `TwoFingerManipulate`. Les arguments de ces méthodes sont les mêmes : la `prevPoint` et `newPoint` arguments représentent le doigt se déplace. Mais le `pivotPoint` argument est différent pour les deux appels :

Pour la manipulation d’un doigt, la `pivotPoint` correspond au centre de l’image bitmap. Cela consiste à autoriser pour la rotation d’un doigt. Pour la manipulation des deux doigts, l’événement indique le déplacement d’un seul doigt, afin que le `pivotPoint` est le doigt ne bouge pas.

Dans les deux cas, `TouchManipulationManager` retourne un `SKMatrix` valeur, la méthode concatène avec actuel `Matrix` propriété qui `TouchManipulationPage` utilise pour restituer l’image bitmap.

`TouchManipulationManager` est généralisé et n’utilise aucun autre fichier à l’exception `TouchManipulationMode`. Vous pouvez être en mesure d’utiliser cette classe sans modification dans vos propres applications. Il définit une propriété unique de type `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

Toutefois, vous souhaiterez probablement éviter le `AnisotropicScale` option. Il est très facile avec cette option pour manipuler le bitmap afin qu’un des facteurs d’échelle devient égal à zéro. Ainsi, l’image bitmap disparaissent de la vue, jamais à retourner. Si vous avez véritablement besoin ANISOTROPIQUE de mise à l’échelle, vous souhaitez améliorer la logique pour éviter des résultats indésirables.

`TouchManipulationManager` utilise des vecteurs, mais dans la mesure où il existe aucune `SKVector` structure dans SkiaSharp, `SKPoint` est utilisé à la place. `SKPoint` prend en charge que l’opérateur de soustraction et le résultat peuvent être traité comme un vecteur. La logique spécifique uniquement vecteur qui doit être ajoutés est un `Magnitude` calcul :

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

Chaque fois que la rotation a été sélectionnée, les méthodes de manipulation d’un doigt et deux doigts gérer tout d’abord la rotation. Si la rotation est détectée, le composant de rotation est alors effectivement supprimé. Ce qui reste est interprété comme panoramique et de mise à l’échelle.

Voici le `OneFingerManipulate` (méthode). Si un doigt rotation n’a pas été activée, la logique est simple &mdash; il utilise simplement le point précédent et le point pour construire un vecteur nommé `delta` qui correspond précisément à la traduction. Avec une rotation d’un doigt est activée, la méthode utilise angles à partir du point de pivot (au centre de l’image bitmap) pour le point précédent et le point pour construire une matrice de rotation :

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

Dans le `TwoFingerManipulate` (méthode), le point pivot est la position du doigt qui ne bouge pas dans cet événement tactile donnée. La rotation est très similaire à la rotation d’un doigt, et ensuite le vecteur nommé `oldVector` (basé sur le point précédent) est ajustée pour la rotation. Le mouvement restant est interprété en tant que mise à l’échelle :

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

Vous remarquerez qu’il n’existe aucune conversion explicite dans cette méthode. Toutefois, à la fois le `MakeRotation` et `MakeScale` méthodes sont basées sur le point pivot, et qui inclut la conversion implicite. Si vous utilisez deux doigts sur l’image bitmap et les faisant glisser dans la même direction, `TouchManipulation` obtiendra une série d’événements tactiles alternant entre deux doigts. Comme chaque doigt par rapport à l’autres, résultats mise à l’échelle ou la rotation, mais elle est invalidée par le déplacement de l’autre doigt, et le résultat est la traduction.

La seule partie restante de la **Manipulation tactile** page est la `PaintSurface` gestionnaire dans le `TouchManipulationPage` fichier code-behind. Ce code appelle la `Paint` méthode de la `TouchManipulationBitmap`, auquel s’applique la matrice représentant l’activité cumulée tactile :

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

Le `PaintSurface` gestionnaire se termine en affichant un `MatrixDisplay` objet en affichant la matrice cumulée tactile :

[![](touch-images/touchmanipulation-small.png "Capture d’écran triple de la page de la Manipulation tactile")](touch-images/touchmanipulation-large.png#lightbox "Triple capture d’écran de la page de la Manipulation tactile")

## <a name="manipulating-multiple-bitmaps"></a>Manipulation des Bitmaps plusieurs

Un des avantages de l’isolation du code de traitement de touch dans les classes telles que `TouchManipulationBitmap` et `TouchManipulationManager` est la possibilité de réutiliser ces classes dans un programme qui permet à l’utilisateur de manipuler plusieurs images bitmap.

Le **Bitmap à nuages de points vue** page montre comment procéder. Au lieu de la définition d’un champ de type `TouchManipulationBitmap`, le [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe définit un `List` des objets bitmap :

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

Le constructeur se charge dans toutes les images disponibles en tant que ressources incorporées et les ajoute à la `bitmapCollection`. Notez que le `Matrix` propriété est initialisée sur chaque `TouchManipulationBitmap` de l’objet, donc les angles supérieur gauche de chaque bitmap sont décalés de 100 pixels.

Le `BitmapScatterView` page doit également gérer les événements tactiles pour les bitmaps de plusieurs. Au lieu de définir un `List` de touch ID des actuellement manipulées `TouchManipulationBitmap` objets, ce programme nécessite un dictionnaire :

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Notez comment la `Pressed` logique effectue une itération sur les `bitmapCollection` dans l’ordre inverse. Les bitmaps souvent chevauchent. Les bitmaps plus loin dans la collection concordent visuellement les bitmaps plus haut dans la collection. S’il existe plusieurs images bitmap sous le doigt appuie sur l’écran, celui qui est au premier plan doit être celui qui est manipulé par ce doigt.

Notez également que le `Pressed` logique déplace ce bitmap à la fin de la collection afin qu’il se déplace visuellement en haut de l’accumulation d’autres images bitmap.

Dans le `Moved` et `Released` événements, le `TouchAction` appels du gestionnaire le `ProcessingTouchEvent` méthode dans `TouchManipulationBitmap` comme programme précédent.

Enfin, le `PaintSurface` appels du gestionnaire le `Paint` (méthode) de chaque `TouchManipulationBitmap` objet :

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

Le code effectue une itération sur la collection et affiche l’accumulation de bitmaps à partir du début de la collection à la fin :

[![](touch-images/bitmapscatterview-small.png "Capture d’écran triple de la page de vue de nuage de points de Bitmap")](touch-images/bitmapscatterview-large.png#lightbox "Triple capture d’écran de la page de vue de nuage de points de Bitmap")

## <a name="single-finger-scaling"></a>Mise à l’échelle d’un seul doigt

Une opération de mise à l’échelle nécessite généralement un mouvement de pincement à l’aide de deux doigts. Toutefois, il est possible d’implémenter la mise à l’échelle avec un seul doigt en demandant le doigt à déplacer les angles d’une image bitmap.

Cela est illustré dans le **unique à l’échelle de coin doigt** page. Étant donné que cet exemple utilise un type de mise à l’échelle à un peu différent qui implémentée dans le `TouchManipulationManager` (classe), il n’utilise pas cette classe ou la `TouchManipulationBitmap` classe. Au lieu de cela, toute la logique de tactile est dans le fichier code-behind. Il s’agit d’une logique un peu plus simple que d’habitude, car il effectue le suivi à la fois qu’un seul doigt et ignore simplement les doigts secondaire qui peuvent être toucher l’écran.

Le [ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) page instancie le `SKCanvasView` classe et crée un `TouchEffect` objet de suivi des événements tactiles :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Le [ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) fichier charge une ressource bitmap à partir de la **Media** directory et affiche à l’aide un `SKMatrix` objet défini en tant qu’un champ :

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

Cela `SKMatrix` objet est modifié par la logique de touch indiquée ci-dessous.

Le reste du fichier code-behind est le `TouchEffect` Gestionnaire d’événements. Il commence par convertir l’emplacement actuel du doigt pour un `SKPoint` valeur. Pour le `Pressed` type d’action, le gestionnaire vérifie qu’aucun autre doigt ne touche l’écran, et qu’il se trouve dans les limites de la bitmap.

La partie essentielle du code est un `if` instruction impliquant deux appels à la `Math.Pow` (méthode). Cette mathématiques vérifie si le doigt se trouve en dehors d’une ellipse qui remplit la bitmap. Dans ce cas, puis qui est une opération de mise à l’échelle. Il se trouve près d’un des angles de l’image bitmap et un point pivot est déterminé qui est l’angle opposé. Si le doigt se trouve au sein de cette ellipse, il est une opération de panoramique régulière :

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

Le `Moved` type d’action calcule une matrice correspondant à l’activité tactile à partir du moment que le doigt utilisateur appuie sur l’écran jusqu'à cette heure. Il concatène cette matrice avec la matrice en vigueur au moment où que le doigt enfoncé tout d’abord l’image bitmap. L’opération de mise à l’échelle est toujours relatif au coin opposé à celui qui le doigt couvertes.

Pour les bitmaps de petite taille ou allongées, une ellipse intérieure peut occupent la majeure partie de l’image bitmap et laissez les zones minuscules dans les angles à l’échelle de l’image bitmap. Vous préférerez peut-être une approche un peu différente, auquel cas vous pouvez remplacer cet ensemble `if` bloc définit `isScaling` à `true` avec ce code :

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

Ce code divise efficacement la zone de l’image bitmap en forme de losange intérieurs et quatre triangles dans les angles. Ainsi, beaucoup plus grande zones dans les angles pour récupérer et mettre à l’échelle de l’image bitmap.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
