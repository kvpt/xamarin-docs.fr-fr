---
title: Manipulations tactiles
description: Cet article explique comment utiliser les transformations de matrice pour implémenter le glissement, le pincement et la rotation tactiles, et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5738f376e2a814ef1af455178c2e68135184402
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140164"
---
# <a name="touch-manipulations"></a>Manipulations tactiles

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utiliser des transformations de matrice pour implémenter le glissement, le pincement et la rotation tactiles_

Dans les environnements tactiles multiples tels que ceux des appareils mobiles, les utilisateurs utilisent souvent leurs doigts pour manipuler des objets à l’écran. Les mouvements courants tels qu’un glisser-doigt et un pincement à deux doigts peuvent déplacer et mettre à l’échelle des objets, voire les faire pivoter. Ces mouvements sont généralement implémentés à l’aide de matrices de transformation, et cet article vous explique comment procéder.

![](touch-images/touchmanipulationsexample.png "A bitmap subjected to translation, scaling, and rotation")

Tous les exemples présentés ici utilisent l' Xamarin.Forms effet de suivi tactile présenté dans l’article [**appel d’événements à partir d’effets**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Glisser-déplacer

L’une des applications les plus importantes des transformations de matrice est le traitement tactile. Une [`SKMatrix`](xref:SkiaSharp.SKMatrix) valeur unique peut consolider une série d’opérations tactiles. 

Pour le glissement d’un seul doigt, la `SKMatrix` valeur effectue une traduction. Cela est illustré dans la page **déplacement de bitmap** . Le fichier XAML instancie un `SKCanvasView` dans un Xamarin.Forms `Grid` . Un `TouchEffect` objet a été ajouté à la `Effects` collection de ce qui `Grid` suit :

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

En théorie, l' `TouchEffect` objet peut être ajouté directement à la `Effects` collection du `SKCanvasView` , mais cela ne fonctionne pas sur toutes les plateformes. Étant donné que `SKCanvasView` est de la même taille que le `Grid` dans cette configuration, l’attacher au `Grid` fonctionne également.

Le fichier code-behind se charge dans une ressource bitmap dans son constructeur et l’affiche dans le `PaintSurface` Gestionnaire :

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

Sans code supplémentaire, la `SKMatrix` valeur est toujours la matrice d’identification, et elle n’aurait aucun effet sur l’affichage de la bitmap. L’objectif du `OnTouchEffectAction` Gestionnaire défini dans le fichier XAML est de modifier la valeur de la matrice pour refléter les manipulations tactiles.

Le `OnTouchEffectAction` Gestionnaire commence par convertir la Xamarin.Forms `Point` valeur en valeur SkiaSharp `SKPoint` . Il s’agit d’une question simple de mise à l’échelle basée sur les `Width` `Height` Propriétés et de `SKCanvasView` (qui sont des unités indépendantes du périphérique) et de la `CanvasSize` propriété, qui est en unités de pixels :

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

Lorsqu’un doigt touche d’abord l’écran, un événement de type `TouchActionType.Pressed` est déclenché. La première tâche consiste à déterminer si le doigt touche le bitmap. Une telle tâche est souvent appelée _test d’atteinte_. Dans ce cas, le test de positionnement peut être effectué en créant une `SKRect` valeur correspondant à la bitmap, en lui appliquant la transformation de matrice avec, puis en `MapRect` déterminant si le point tactile est à l’intérieur du rectangle transformé.

Si tel est le cas, le `touchId` champ est défini sur l’id tactile et la position du doigt est enregistrée.

Pour l' `TouchActionType.Moved` événement, les facteurs de translation de la `SKMatrix` valeur sont ajustés en fonction de la position actuelle du doigt et de la nouvelle position du doigt. Cette nouvelle position est enregistrée pour la prochaine fois, et la `SKCanvasView` est invalidée.

À mesure que vous expérimentez ce programme, prenez note que vous ne pouvez faire glisser le bitmap que lorsque votre doigt touche une zone où la bitmap est affichée. Même si cette restriction n’est pas très importante pour ce programme, elle devient cruciale lors de la manipulation de plusieurs bitmaps.

## <a name="pinching-and-scaling"></a>Pincement et mise à l’échelle

Que voulez-vous faire lorsque deux doigts touchent le bitmap ? Si les deux doigts se déplacent en parallèle, vous souhaiterez probablement que l’image bitmap se déplace avec les doigts. Si les deux doigts effectuent une opération de pincement ou d’étirement, vous souhaiterez peut-être faire pivoter l’image bitmap (pour la voir dans la section suivante) ou la mettre à l’échelle. Lors de la mise à l’échelle d’une image bitmap, il est plus judicieux que les deux doigts restent dans les mêmes positions par rapport à l’image bitmap et que l’image bitmap soit mise à l’échelle en conséquence.

Gérer deux doigts à la fois semble compliqué, mais gardez à l’esprit que le `TouchAction` Gestionnaire reçoit uniquement des informations sur un doigt à la fois. Si deux doigts manipulent l’image bitmap, alors, pour chaque événement, un doigt a changé de position, mais l’autre n’a pas changé. Dans le code de la page de **mise à l’échelle des bitmaps** ci-dessous, le doigt qui n’a pas changé de position est appelé le point _pivot_ parce que la transformation est relative à ce point.

L’une des différences entre ce programme et le programme précédent est que vous devez enregistrer plusieurs ID tactiles. Un dictionnaire est utilisé à cet effet, où l’ID tactile est la clé de dictionnaire et la valeur de dictionnaire est la position actuelle de ce doigt :

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

La gestion de l' `Pressed` action est presque identique à celle du programme précédent, à ceci près que l’ID et le point tactile sont ajoutés au dictionnaire. Les `Released` `Cancelled` actions et suppriment l’entrée de dictionnaire.

Toutefois, la gestion de l' `Moved` action est plus complexe. S’il n’y a qu’un seul doigt impliqué, le traitement est quasiment identique à celui du programme précédent. Pour deux ou plusieurs doigts, le programme doit également obtenir des informations du dictionnaire qui impliquent le doigt qui ne bouge pas. Pour ce faire, il copie les clés de dictionnaire dans un tableau, puis compare la première clé à l’ID du doigt déplacé. Cela permet au programme d’obtenir le point pivot correspondant au doigt qui ne bouge pas.

Ensuite, le programme calcule deux vecteurs de la nouvelle position du doigt par rapport au point pivot, et l’ancienne position du doigt par rapport au point pivot. Les ratios de ces vecteurs sont des facteurs de mise à l’échelle. Étant donné que la division par zéro est une possibilité, les valeurs infinies ou les valeurs NaN (non numériques) doivent être vérifiées. Si tout est correct, une transformation de mise à l’échelle est concaténée avec la `SKMatrix` valeur enregistrée en tant que champ.

À mesure que vous expérimentez cette page, vous remarquerez que vous pouvez faire glisser l’image bitmap avec un ou deux doigts ou la mettre à l’échelle avec deux doigts. La mise à l’échelle est _anisotrope_, ce qui signifie que la mise à l’échelle peut être différente dans les directions horizontale et verticale. Cela déforme les proportions, mais vous permet également d’inverser l’image bitmap pour créer une image miroir. Vous pouvez également découvrir que vous pouvez réduire la bitmap à une dimension zéro et la faire disparaître. Dans le code de production, vous souhaiterez vous prémunir contre cela.

## <a name="two-finger-rotation"></a>Rotation à deux doigts

La page **faire pivoter la bitmap** vous permet d’utiliser deux doigts pour la rotation ou la mise à l’échelle isotrope. La bitmap conserve toujours ses proportions correctes. L’utilisation de deux doigts pour la rotation et la mise à l’échelle anisotrope ne fonctionne pas très bien, car le déplacement des doigts est très similaire pour les deux tâches.

La première grande différence dans ce programme est la logique de test de positionnement. Les programmes précédents ont utilisé la `Contains` méthode de `SKRect` pour déterminer si le point tactile se trouve dans le rectangle transformé qui correspond à l’image bitmap. Toutefois, lorsque l’utilisateur manipule la bitmap, l’image bitmap peut être pivotée et `SKRect` ne peut pas représenter correctement un rectangle pivoté. Vous vous inquiétez peut-être que la logique de test de positionnement doit implémenter une géométrie analytique plutôt complexe dans ce cas.

Toutefois, un raccourci est disponible : déterminer si un point se trouve dans les limites d’un rectangle transformé est identique à déterminer si un point transformé inverse se trouve dans les limites du rectangle non transformé. C’est un calcul beaucoup plus facile, et la logique peut continuer à utiliser la `Contains` méthode pratique :

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

La logique de l' `Moved` événement démarre comme le programme précédent. Deux vecteurs nommés `oldVector` et `newVector` sont calculés en fonction du point précédent et du point actuel du doigt mobile et du point pivot du doigt qui ne bouge pas. Toutefois, les angles de ces vecteurs sont déterminés, et la différence est l’angle de rotation.

La mise à l’échelle peut également être impliquée, de sorte que l’ancien vecteur est pivoté en fonction de l’angle de rotation. La grandeur relative des deux vecteurs est désormais le facteur d’échelle. Notez que la même `scale` valeur est utilisée pour la mise à l’échelle horizontale et verticale afin que la mise à l’échelle soit isotrope. Le `matrix` champ est ajusté à la fois par la matrice de rotation et par une matrice de mise à l’échelle.

Si votre application doit implémenter le traitement tactile pour une seule bitmap (ou un autre objet), vous pouvez adapter le code à partir de ces trois exemples pour votre propre application. Toutefois, si vous avez besoin d’implémenter le traitement tactile pour plusieurs bitmaps, vous souhaiterez probablement encapsuler ces opérations tactiles dans d’autres classes.

## <a name="encapsulating-the-touch-operations"></a>Encapsulation des opérations tactiles

La page **manipulation tactile** illustre la manipulation tactile d’une seule bitmap, mais à l’aide de plusieurs autres fichiers qui encapsulent une grande partie de la logique indiquée ci-dessus. Le premier de ces fichiers est l' [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) énumération, qui indique les différents types de manipulations tactiles implémentés par le code que vous voyez :

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

`PanOnly`glissement d’un doigt qui est implémenté avec la traduction. Toutes les options suivantes incluent également le panoramique, mais impliquent deux doigts : `IsotropicScale` est une opération de pincement qui se traduit par une mise à l’échelle de l’objet de manière égale dans les directions horizontale et verticale. `AnisotropicScale`autorise une mise à l’échelle inégale.

L' `ScaleRotate` option est pour la mise à l’échelle et la rotation à deux doigts. La mise à l’échelle est isotrope. Comme mentionné précédemment, l’implémentation de la rotation à deux doigts avec une mise à l’échelle anisotrope est problématique, car les mouvements des doigts sont fondamentalement les mêmes.

L' `ScaleDualRotate` option ajoute une rotation d’un doigt. Lorsqu’un seul doigt fait glisser l’objet, l’objet déplacé est d’abord pivoté autour de son centre afin que le centre de l’objet soit aligné avec le vecteur de glissement.

Le fichier [**TouchManipulationPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) comprend un `Picker` avec les membres de l' `TouchManipulationMode` énumération :

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

Le fichier code-behind [**TouchManipulationPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) contient un `bitmap` champ, mais il n’est pas de type `SKBitmap` . Le type est `TouchManipulationBitmap` (une classe que vous verrez bientôt) :

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

Le constructeur instancie un `TouchManipulationBitmap` objet, en passant au constructeur `SKBitmap` obtenu à partir d’une ressource incorporée. Le constructeur conclut en affectant `Mode` à la propriété de la `TouchManager` propriété de l' `TouchManipulationBitmap` objet un membre de l' `TouchManipulationMode` énumération.

Le `SelectedIndexChanged` Gestionnaire pour le `Picker` définit également cette `Mode` propriété :

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

Le `TouchAction` Gestionnaire du `TouchEffect` instancié dans le fichier XAML appelle deux méthodes dans les `TouchManipulationBitmap` éléments nommés `HitTest` et `ProcessTouchEvent` :

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

Si la `HitTest` méthode retourne `true` &mdash; , ce qui signifie qu’un doigt a touché l’écran dans la zone occupée par la bitmap &mdash; , l’id tactile est ajouté à la `TouchIds` collection. Cet ID représente la séquence d’événements tactiles pour ce doigt jusqu’à ce que le doigt s’affiche à l’écran. Si plusieurs doigts touchent l’image bitmap, la `touchIds` collection contient un id tactile pour chaque doigt.

Le `TouchAction` Gestionnaire appelle également la `ProcessTouchEvent` classe dans `TouchManipulationBitmap` . C’est là qu’intervient certains (mais pas tous) du traitement réel des fonctions tactiles.

La [`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe est une classe wrapper pour `SKBitmap` qui contient le code permettant de restituer les événements bitmap et traitement tactile. Il fonctionne conjointement avec du code plus généralisé dans une `TouchManipulationManager` classe (que vous verrez bientôt).

Le `TouchManipulationBitmap` constructeur enregistre `SKBitmap` et instancie deux propriétés, la `TouchManager` propriété de type `TouchManipulationManager` et la `Matrix` propriété de type `SKMatrix` :

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

Cette `Matrix` propriété est la transformation accumulée résultant de l’activité tactile. Comme vous le verrez, chaque événement tactile est résolu en une matrice, qui est ensuite concaténée avec la `SKMatrix` valeur stockée par la `Matrix` propriété.

L' `TouchManipulationBitmap` objet se dessine lui-même dans sa `Paint` méthode. L’argument est un `SKCanvas` objet. `SKCanvas`Une transformation est peut-être déjà appliquée à cette `Paint` méthode. la méthode concatène donc la `Matrix` propriété associée à la bitmap à la transformation existante, puis restaure la zone de dessin une fois celle-ci terminée :

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

La `HitTest` méthode retourne `true` si l’utilisateur touche l’écran à un point situé dans les limites de l’image bitmap. Cela utilise la logique indiquée plus haut dans la page de rotation de la **bitmap** :

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

La deuxième méthode publique dans `TouchManipulationBitmap` est `ProcessTouchEvent` . Lorsque cette méthode est appelée, il a déjà été établi que l’événement tactile appartient à cette image bitmap particulière. La méthode gère un dictionnaire d' [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) objets, qui est simplement le point précédent et le nouveau point de chaque doigt :

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Voici le dictionnaire et la `ProcessTouchEvent` méthode elle-même :

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

Dans les `Moved` `Released` événements et, la méthode appelle `Manipulate` . À ce moment-là, `touchDictionary` contient un ou plusieurs `TouchManipulationInfo` objets. Si `touchDictionary` contient un élément, il est probable que les `PreviousPoint` valeurs et `NewPoint` sont inégales et représentent le mouvement d’un doigt. Si plusieurs doigts touchent l’image bitmap, le dictionnaire contient plus d’un élément, mais un seul de ces éléments a des `PreviousPoint` valeurs et différentes `NewPoint` . Toutes les Rest ont des `PreviousPoint` valeurs et identiques `NewPoint` .

Cela est important : la `Manipulate` méthode peut supposer qu’elle traite le mouvement d’un seul doigt. Au moment de cet appel, aucun autre doigt ne bouge, et s’ils se déplacent vraiment (comme c’est probablement le cas), ces mouvements sont traités dans les appels futurs à `Manipulate` .

La `Manipulate` méthode copie d’abord le dictionnaire dans un tableau pour plus de commodité. Elle ignore les deux premières entrées. Si plus de deux doigts tentent de manipuler le bitmap, les autres sont ignorés. `Manipulate`est le membre final de `TouchManipulationBitmap` :

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

Si un doigt manipule la bitmap, `Manipulate` appelle la `OneFingerManipulate` méthode de l' `TouchManipulationManager` objet. Pour deux doigts, il appelle `TwoFingerManipulate` . Les arguments de ces méthodes sont les mêmes : les `prevPoint` `newPoint` arguments et représentent le doigt qui bouge. Toutefois, l' `pivotPoint` argument est différent pour les deux appels :

Pour la manipulation d’un seul doigt, `pivotPoint` est le centre de l’image bitmap. Cela permet d’autoriser la rotation d’un seul doigt. Pour la manipulation en deux doigts, l’événement indique le déplacement d’un seul doigt, afin que le `pivotPoint` soit le doigt qui ne bouge pas.

Dans les deux cas, `TouchManipulationManager` retourne une `SKMatrix` valeur, que la méthode concatène avec la `Matrix` propriété actuelle que `TouchManipulationPage` utilise pour restituer la bitmap.

`TouchManipulationManager`est généralisé et n’utilise pas d’autres fichiers à l’exception de `TouchManipulationMode` . Vous serez peut-être en mesure d’utiliser cette classe sans modification dans vos propres applications. Elle définit une seule propriété de type `TouchManipulationMode` :

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

Toutefois, vous souhaiterez probablement éviter l' `AnisotropicScale` option. C’est très facile avec cette option pour manipuler le bitmap afin que l’un des facteurs de mise à l’échelle devienne zéro. Cela rend la bitmap disparaître de la vue, jamais à retourner. Si vous avez véritablement besoin de la mise à l’échelle anisotrope, vous souhaiterez améliorer la logique pour éviter des résultats indésirables.

`TouchManipulationManager`utilise des vecteurs, mais étant donné qu’il n’y a aucune `SKVector` structure dans SkiaSharp, `SKPoint` est utilisé à la place. `SKPoint`prend en charge l’opérateur de soustraction et le résultat peut être traité comme un vecteur. La seule logique spécifique au vecteur qui devait être ajoutée est un `Magnitude` calcul :

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

Chaque fois que la rotation a été sélectionnée, les méthodes de manipulation à un seul doigt et à deux doigts gèrent d’abord la rotation. Si une rotation est détectée, le composant de rotation est effectivement supprimé. Ce qui reste est interprété comme un panoramique et une mise à l’échelle.

Voici la `OneFingerManipulate` méthode. Si la rotation à un doigt n’a pas été activée, la logique est simple. &mdash; elle utilise simplement le point précédent et le nouveau point pour construire un vecteur nommé `delta` qui correspond précisément à la traduction. Avec la rotation d’un doigt activée, la méthode utilise des angles du point pivot (le centre de la bitmap) jusqu’au point précédent et un nouveau point pour construire une matrice de rotation :

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

Dans la `TwoFingerManipulate` méthode, le point pivot est la position du doigt qui ne bouge pas dans cet événement tactile particulier. La rotation est très similaire à la rotation à un seul doigt, puis le vecteur nommé `oldVector` (basé sur le point précédent) est ajusté pour la rotation. Le déplacement restant est interprété comme une mise à l’échelle :

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

Vous remarquerez qu’il n’existe aucune traduction explicite dans cette méthode. Toutefois, les `MakeRotation` méthodes et `MakeScale` sont basées sur le point pivot, et cela comprend une traduction implicite. Si vous utilisez deux doigts sur le bitmap et que vous les faites glisser dans la même direction, vous `TouchManipulation` obtiendrez une série d’événements tactiles alternant entre les deux doigts. À mesure que chaque doigt se déplace par rapport à l’autre, les résultats de mise à l’échelle ou de rotation, mais il est inversé par le mouvement de l’autre doigt, et le résultat est la traduction.

La seule partie restante de la page de **manipulation tactile** est le `PaintSurface` gestionnaire dans le `TouchManipulationPage` fichier code-behind. Cela appelle la `Paint` méthode de `TouchManipulationBitmap` , qui applique la matrice représentant l’activité tactile accumulée :

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

Le `PaintSurface` Gestionnaire se termine en affichant un `MatrixDisplay` objet montrant la matrice tactile accumulée :

[![](touch-images/touchmanipulation-small.png "Triple screenshot of the Touch Manipulation page")](touch-images/touchmanipulation-large.png#lightbox "Triple screenshot of the Touch Manipulation page")

## <a name="manipulating-multiple-bitmaps"></a>Manipulation de plusieurs bitmaps

L’un des avantages de l’isolation du code de traitement tactile dans les classes telles que `TouchManipulationBitmap` et `TouchManipulationManager` est la possibilité de réutiliser ces classes dans un programme qui permet à l’utilisateur de manipuler plusieurs bitmaps.

La page de **vue nuage d’images** montre comment procéder. Au lieu de définir un champ de type `TouchManipulationBitmap` , la [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe définit un `List` des objets bitmap :

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

Le constructeur charge dans toutes les bitmaps disponibles en tant que ressources incorporées, puis les ajoute à `bitmapCollection` . Notez que la `Matrix` propriété est initialisée sur chaque `TouchManipulationBitmap` objet. par conséquent, les angles supérieurs gauches de chaque bitmap sont décalés de 100 pixels.

La `BitmapScatterView` page doit également gérer des événements tactiles pour plusieurs bitmaps. Au lieu de définir un `List` des ID tactiles des objets actuellement manipulés `TouchManipulationBitmap` , ce programme nécessite un dictionnaire :

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

Notez comment la logique parcourt `Pressed` le `bitmapCollection` en sens inverse. Les bitmaps se chevauchent souvent les unes des autres. Les bitmaps plus loin dans la collection sont visuellement situées au-dessus des bitmaps précédemment dans la collection. S’il y a plusieurs bitmaps sous le doigt qui appuie sur l’écran, le premier doit être celui qui est manipulé par ce doigt.

Notez également que la `Pressed` logique déplace cette bitmap à la fin de la collection afin qu’elle se déplace visuellement vers le haut de la pile d’autres bitmaps.

Dans les `Moved` `Released` événements et, le `TouchAction` Gestionnaire appelle la `ProcessingTouchEvent` méthode comme `TouchManipulationBitmap` le programme précédent.

Enfin, le `PaintSurface` Gestionnaire appelle la `Paint` méthode de chaque `TouchManipulationBitmap` objet :

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

Le code parcourt la collection et affiche le tas des bitmaps du début à la fin de la collection :

[![](touch-images/bitmapscatterview-small.png "Triple screenshot of the Bitmap Scatter View page")](touch-images/bitmapscatterview-large.png#lightbox "Triple screenshot of the Bitmap Scatter View page")

## <a name="single-finger-scaling"></a>Mise à l’échelle à un seul doigt

Une opération de mise à l’échelle requiert généralement un geste de pincement à deux doigts. Toutefois, il est possible d’implémenter la mise à l’échelle avec un seul doigt en faisant passer le doigt des angles d’une image bitmap.

Cela est illustré dans la page de mise à l' **échelle des angles d’un seul doigt** . Étant donné que cet exemple utilise un type de mise à l’échelle légèrement différent de celui implémenté dans la `TouchManipulationManager` classe, il n’utilise pas cette classe ou la `TouchManipulationBitmap` classe. Au lieu de cela, toutes les logiques tactiles se trouvent dans le fichier code-behind. Il s’agit d’une logique plus simple que d’habitude, car elle ne suit qu’un seul doigt à la fois et ignore simplement les doigts secondaires qui pourraient toucher l’écran.

La page [**SingleFingerCornerScale. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) instancie la `SKCanvasView` classe et crée un `TouchEffect` objet pour le suivi des événements tactiles :

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

Le fichier [**SingleFingerCornerScalePage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) charge une ressource bitmap à partir du répertoire **multimédia** et l’affiche à l’aide d’un `SKMatrix` objet défini en tant que champ :

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

Cet `SKMatrix` objet est modifié par la logique tactile indiquée ci-dessous.

Le reste du fichier code-behind est le `TouchEffect` Gestionnaire d’événements. Il commence par convertir l’emplacement actuel du doigt en `SKPoint` valeur. Pour le `Pressed` type d’action, le gestionnaire vérifie qu’aucun autre doigt ne touche l’écran et que le doigt se trouve dans les limites de l’image bitmap.

La partie essentielle du code est une `if` instruction impliquant deux appels à la `Math.Pow` méthode. Cette mathématique vérifie si l’emplacement du doigt se trouve en dehors d’une ellipse qui remplit l’image bitmap. Si c’est le cas, il s’agit d’une opération de mise à l’échelle. Le doigt est proche de l’un des angles de la bitmap et un point pivot est déterminé comme étant l’angle opposé. Si le doigt se trouve dans cette ellipse, il s’agit d’une opération de panoramique normale :

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

Le `Moved` type d’action calcule une matrice correspondant à l’activité Touch à partir du moment où le doigt a appuyé sur l’écran jusqu’à présent. Elle concatène cette matrice avec la matrice en vigueur au moment où le doigt a appuyé sur la bitmap. L’opération de mise à l’échelle est toujours relative à l’angle opposé à celui touché par le doigt.

Pour les bitmaps de petite taille ou les images oblongues, une ellipse intérieure peut occuper la majeure partie de l’image bitmap et conserver des zones minuscules aux angles pour la mise à l’échelle de l’image bitmap. Vous préférerez peut-être une approche quelque peu différente, auquel cas vous pouvez remplacer la totalité du `if` bloc qui définit `isScaling` `true` par ce code :

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

Ce code divise efficacement la zone de l’image bitmap en une forme en losange intérieur et quatre triangles aux angles. Cela permet d’obtenir des zones plus larges aux angles pour saisir et mettre à l’échelle l’image bitmap.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
