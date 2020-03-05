---
title: Lignes et extrémités de trait
description: Cet article explique comment utiliser SkiaSharp pour dessiner des lignes avec des embouts de trait différents dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 9aaecb8c63ff28111097dce81954f523b4c7731b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292824"
---
# <a name="lines-and-stroke-caps"></a>Lignes et extrémités de trait

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser SkiaSharp pour dessiner des lignes avec des épaisseurs de trait différentes_

Dans SkiaSharp, le rendu d’une seule ligne est très différent de rendu d’une série de lignes droites connectées. Même lors du dessin des lignes uniques, toutefois, il est souvent nécessaire de donner les lignes d’un trait particulier. Comme ces lignes deviennent plus large, l’apparence de la fin des lignes devient aussi important. L’apparence de la fin de la ligne est appelée *extrémité du trait*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Pour dessiner des lignes uniques, `SKCanvas` définit une méthode de [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) simple dont les arguments indiquent les coordonnées de début et de fin de la ligne avec un objet `SKPaint` :

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Par défaut, la propriété [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) d’un objet `SKPaint` récemment instancié est 0, ce qui a le même effet qu’une valeur de 1 pour le rendu d’une ligne d’un pixel dans l’épaisseur. Cela semble très léger sur les appareils haute résolution tels que les téléphones. vous souhaiterez probablement définir la `StrokeWidth` sur une valeur plus élevée. Mais une fois que vous démarrez le traçage des lignes d’une épaisseur dimensionnable, qui déclenche un autre problème : comment doivent le démarre et les extrémités de ces lignes épaisses être restituées ?

L’apparence du début et de la fin des lignes est appelée un *embout de ligne* ou, dans skia, une extrémité de *trait*. Dans ce contexte, le mot « Cap » fait référence à un type de chapeau &mdash; un point qui se trouve à la fin de la ligne. Vous définissez la propriété [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) de l’objet `SKPaint` sur l’un des membres suivants de l’énumération [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) :

- `Butt` (valeur par défaut)
- `Square`
- `Round`

Ces désinstallations sont illustrées mieux avec un exemple de programme. La section **lignes et chemins d’accès SkiaSharp** du programme [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) commence par une page intitulée **traits** d’accès en fonction de la classe [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) . Cette page définit un gestionnaire d’événements `PaintSurface` qui parcourt les trois membres de l’énumération `SKStrokeCap`, en affichant à la fois le nom du membre de l’énumération et en dessinant une ligne à l’aide de cette extrémité de trait :

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

Pour chaque membre de l’énumération `SKStrokeCap`, le gestionnaire dessine deux lignes, une avec une épaisseur de trait de 50 pixels et une autre ligne positionnée en haut avec une épaisseur de trait de deux pixels. Cette deuxième ligne est destinée à illustrer géométrique début et fin de la ligne indépendante de l’épaisseur du trait et une extrémité de trait :

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Comme vous pouvez le voir, les `Square` et `Round` épaisseur de trait étendent efficacement la longueur de la ligne de la moitié de la largeur du trait au début de la ligne et à nouveau à la fin. Cette extension devient importante lorsqu’il est nécessaire déterminer les dimensions d’un objet rendu graphique.

La classe `SKCanvas` comprend également une autre méthode pour dessiner plusieurs lignes qui sont quelque peu propres :

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Le paramètre `points` est un tableau de valeurs `SKPoint` et `mode` est un membre de l’énumération [`SKPointMode`](xref:SkiaSharp.SKPointMode) , qui a trois membres :

- `Points` pour le rendu des points individuels
- `Lines` pour connecter chaque paire de points
- `Polygon` pour connecter tous les points consécutifs

La page **lignes multiples** illustre cette méthode. Le fichier [**MultipleLinesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) instancie deux vues de `Picker` qui vous permettent de sélectionner un membre de l’énumération `SKPointMode` et un membre de l’énumération `SKStrokeCap` :

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

Notez que les déclarations d’espace de noms SkiaSharp sont un peu différentes, car l’espace de noms `SkiaSharp` est nécessaire pour référencer les membres du `SKPointMode` et des énumérations de `SKStrokeCap`. Le gestionnaire de `SelectedIndexChanged` pour les deux affichages `Picker` invalide simplement l’objet `SKCanvasView` :

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Ce gestionnaire doit vérifier l’existence de l’objet `SKCanvasView`, car le gestionnaire d’événements est appelé en premier lorsque la propriété `SelectedIndex` de l' `Picker` a la valeur 0 dans le fichier XAML, et cela se produit avant l’instanciation de l' `SKCanvasView`.

Le gestionnaire de `PaintSurface` obtient les deux valeurs d’énumération des vues `Picker` :

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

Les captures d’écran montrent une variété de `Picker` sélections :

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

L’iPhone à gauche montre comment le membre de l’énumération `SKPointMode.Points` provoque le rendu par `DrawPoints` de chacun des points du tableau `SKPoint` sous la forme d’un carré si l’extrémité de ligne est `Butt` ou `Square`. Les cercles sont affichés si l’extrémité de ligne est `Round`.

La capture d’écran Android affiche le résultat du `SKPointMode.Lines`. La méthode `DrawPoints` dessine une ligne entre chaque paire de valeurs `SKPoint`, à l’aide de l’extrémité de ligne spécifiée, dans ce cas `Round`.

Quand vous utilisez à la place `SKPointMode.Polygon`, une ligne est dessinée entre les points successifs du tableau, mais si vous regardez très étroitement, vous verrez que ces lignes ne sont pas connectées. Chacune de ces lignes distinctes démarre et se termine par l’embout de ligne spécifié. Si vous sélectionnez la `Round` majuscules, les lignes peuvent sembler être connectées, mais elles ne sont pas connectées.

Si les lignes sont connectés ou non connectés est un aspect essentiel de l’utilisation de chemins d’accès de graphiques.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
