---
title: Lignes et extrémités de trait
description: Cet article explique comment utiliser SkiaSharp pour dessiner des lignes avec des embouts de trait différents dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 724a79e618321f97257718bf56dd1fdd18f73563
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545605"
---
# <a name="lines-and-stroke-caps"></a>Lignes et extrémités de trait

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser SkiaSharp pour dessiner des lignes avec des embouts de trait différents_

Dans SkiaSharp, le rendu d’une seule ligne est très différent de rendu d’une série de lignes droites connectées. Même lors du dessin des lignes uniques, toutefois, il est souvent nécessaire de donner les lignes d’un trait particulier. Comme ces lignes deviennent plus large, l’apparence de la fin des lignes devient aussi important. L’apparence de la fin de la ligne est appelé le *extrémité de trait*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Pour dessiner des lignes uniques, `SKCanvas` définit une simple [ `DrawLine` ](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) méthode dont les arguments indiquent le début et fin des coordonnées de la ligne avec un `SKPaint` objet :

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Par défaut, le [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth) propriété de nouvellement instancié `SKPaint` objet est 0, ce qui a le même effet en tant que valeur de 1 lors du rendu d’une ligne d’un pixel d’épaisseur. Cela semble très léger sur les appareils haute résolution tels que les téléphones, vous devez donc probablement définir le `StrokeWidth` d’une valeur supérieure. Mais une fois que vous démarrez le traçage des lignes d’une épaisseur dimensionnable, qui déclenche un autre problème : comment doivent le démarre et les extrémités de ces lignes épaisses être restituées ?

L’apparence du démarre et la fin des lignes est appelé un *embout de ligne* ou Skia, un *extrémité de trait*. Le mot « limite » dans ce contexte fait référence à un type de hat &mdash; quelque chose qui se trouve sur la fin de la ligne. Vous définissez le [ `StrokeCap` ](xref:SkiaSharp.SKPaint.StrokeCap) propriété de la `SKPaint` objet à un des membres suivants de la [ `SKStrokeCap` ](xref:SkiaSharp.SKStrokeCap) énumération :

- `Butt` (la valeur par défaut)
- `Square`
- `Round`

Ces désinstallations sont illustrées mieux avec un exemple de programme. Le **SkiaSharp lignes et chemins d’accès** section de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programme commence par une page intitulée **embouts de trait** basé sur le [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) classe. Cette page définit un `PaintSurface` Gestionnaire d’événements qui effectue une itération sur les trois membres de le `SKStrokeCap` énumération, affichant les deux le nom du membre de l’énumération et dessiner une ligne à l’aide de cette extrémité de trait :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

Pour chaque membre de la `SKStrokeCap` énumération, le gestionnaire dessine deux lignes, une avec une épaisseur de trait de 50 pixels et une autre ligne positionné en haut d’une épaisseur de trait de deux pixels. Cette deuxième ligne est destinée à illustrer géométrique début et fin de la ligne indépendante de l’épaisseur du trait et une extrémité de trait :

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Comme vous pouvez le voir, le `Square` et `Round` embouts de trait développer de manière efficace la longueur de la ligne par moitié la largeur de trait au début de la ligne et à nouveau à la fin. Cette extension devient importante lorsqu’il est nécessaire déterminer les dimensions d’un objet rendu graphique.

Le `SKCanvas` classe inclut également une autre méthode pour dessiner plusieurs lignes qui est un peu étrange :

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Le `points` paramètre est un tableau de `SKPoint` valeurs et `mode` est un membre de la [ `SKPointMode` ](xref:SkiaSharp.SKPointMode) énumération, qui a trois membres :

- `Points` Pour afficher des points
- `Lines` Pour vous connecter à chaque paire de points
- `Polygon` Pour vous connecter à tous les points consécutifs

Le **plusieurs lignes** page illustre cette méthode. Le [ **MultipleLinesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) fichier instancie deux `Picker` vues qui vous permettent de sélectionner un membre de la `SKPointMode` énumération et un membre de la `SKStrokeCap` énumération :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

Notez que les déclarations d’espace de noms SkiaSharp sont un peu différentes, car le `SkiaSharp` espace de noms est nécessaire pour référencer les membres de la `SKPointMode` et `SKStrokeCap` énumérations. Le `SelectedIndexChanged` gestionnaire pour les deux `Picker` vues invalide simplement le `SKCanvasView` objet :

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Ce gestionnaire doit vérifier l’existence de la `SKCanvasView` , car le Gestionnaire d’événements est le premier objet appelé lorsque le `SelectedIndex` propriété de la `Picker` est définie sur 0 dans le fichier XAML, et qui se produit avant le `SKCanvasView` a été instancié.

Le `PaintSurface` gestionnaire obtient les deux valeurs d’énumération à partir de la `Picker` vues :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

Les captures d’écran d’afficher diverses `Picker` sélections :

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

L’iPhone à gauche montre comment la `SKPointMode.Points` provoque de membre d’énumération `DrawPoints` pour chacun des points de restituer le `SKPoint` tableau sous la forme d’un carré si l’embout de ligne est `Butt` ou `Square`. Cercles sont rendus si l’embout de ligne est `Round`.

La capture d’écran Android affiche le résultat du `SKPointMode.Lines`. La méthode `DrawPoints` dessine une ligne entre chaque paire de valeurs `SKPoint`, à l’aide de l’extrémité de ligne spécifiée, dans ce cas `Round`.

Quand vous utilisez à la place `SKPointMode.Polygon`, une ligne est dessinée entre les points successifs du tableau, mais si vous regardez très étroitement, vous verrez que ces lignes ne sont pas connectées. Chacune de ces lignes distinctes démarre et se termine par l’embout de ligne spécifié. Si vous sélectionnez le `Round` Cap, les lignes peuvent sembler être connecté, mais elles sont vraiment pas connectées.

Si les lignes sont connectés ou non connectés est un aspect essentiel de l’utilisation de chemins d’accès de graphiques.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
