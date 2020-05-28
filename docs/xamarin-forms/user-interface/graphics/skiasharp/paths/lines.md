---
title: ''
description: Cet article explique comment utiliser SkiaSharp pour dessiner des lignes avec différentes épaisseurs de trait dans les Xamarin.Forms applications et illustre cela avec un exemple de code.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87b97ad913e08c42d16bbf055f168c07b9bd60e8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137202"
---
# <a name="lines-and-stroke-caps"></a>Lignes et extrémités de trait

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment utiliser SkiaSharp pour dessiner des lignes avec des épaisseurs de trait différentes_

Dans SkiaSharp, le rendu d’une seule ligne est très différent du rendu d’une série de lignes droites connectées. Toutefois, même lors du dessin de lignes uniques, il est souvent nécessaire de fournir aux lignes une largeur de trait particulière. À mesure que ces lignes deviennent larges, l’apparence des extrémités des lignes devient également importante. L’apparence de la fin de la ligne est appelée *extrémité du trait*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Pour dessiner des lignes uniques, `SKCanvas` définit une [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) méthode simple dont les arguments indiquent les coordonnées de début et de fin de la ligne avec un `SKPaint` objet :

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Par défaut, la [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) propriété d’un objet nouvellement instancié `SKPaint` est 0, ce qui a le même effet qu’une valeur de 1 pour le rendu d’une ligne d’un pixel dans l’épaisseur. Cela semble très léger sur les appareils haute résolution tels que les téléphones, donc vous souhaiterez probablement définir `StrokeWidth` sur une valeur plus élevée. Toutefois, une fois que vous avez commencé à dessiner des lignes d’épaisseur dimensionnable, cela pose un autre problème : comment les début et fin de ces traits épais doivent-ils être rendus ?

L’apparence du début et de la fin des lignes est appelée un *embout de ligne* ou, dans skia, une extrémité de *trait*. Dans ce contexte, le mot « Cap » fait référence à un type de chapeau &mdash; qui se trouve à la fin de la ligne. Vous définissez la [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) propriété de l' `SKPaint` objet sur l’un des membres suivants de l' [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) énumération :

- `Butt`(valeur par défaut)
- `Square`
- `Round`

Ils sont mieux illustrés par un exemple de programme. La section **lignes et chemins d’accès SkiaSharp** du programme [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) commence par une page intitulée **traits** d’accès en fonction de la [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) classe. Cette page définit un `PaintSurface` Gestionnaire d’événements qui parcourt les trois membres de l' `SKStrokeCap` énumération, en affichant à la fois le nom du membre de l’énumération et en dessinant une ligne à l’aide de cette extrémité de trait :

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

Pour chaque membre de l' `SKStrokeCap` énumération, le gestionnaire dessine deux lignes, l’une avec une épaisseur de trait de 50 pixels et une autre sur la partie supérieure avec une épaisseur de trait de deux pixels. Cette deuxième ligne a pour but d’illustrer le début et la fin géométriques de la ligne indépendamment de l’épaisseur de la ligne et d’un embout de trait :

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Comme vous pouvez le voir, `Square` les `Round` embouts de trait et étendent efficacement la longueur de la ligne de la moitié de la largeur du trait au début de la ligne et à nouveau à la fin. Cette extension devient importante quand il est nécessaire de déterminer les dimensions d’un objet graphique rendu.

La `SKCanvas` classe comprend également une autre méthode pour dessiner plusieurs lignes qui sont quelque peu propres :

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Le `points` paramètre est un tableau de `SKPoint` valeurs et `mode` est membre de l' [`SKPointMode`](xref:SkiaSharp.SKPointMode) énumération, qui a trois membres :

- `Points`pour afficher les points individuels
- `Lines`pour connecter chaque paire de points
- `Polygon`pour connecter tous les points consécutifs

La page **lignes multiples** illustre cette méthode. Le fichier [**MultipleLinesPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) instancie deux `Picker` vues qui vous permettent de sélectionner un membre de l' `SKPointMode` énumération et un membre de l' `SKStrokeCap` énumération :

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

Notez que les déclarations d’espace de noms SkiaSharp sont un peu différentes, car l' `SkiaSharp` espace de noms est nécessaire pour référencer les membres des `SKPointMode` `SKStrokeCap` énumérations et. Le `SelectedIndexChanged` Gestionnaire des deux `Picker` vues invalide simplement l' `SKCanvasView` objet :

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Ce gestionnaire doit vérifier l’existence de l' `SKCanvasView` objet, car le gestionnaire d’événements est appelé en premier lorsque la `SelectedIndex` propriété du `Picker` a la valeur 0 dans le fichier XAML, et cela se produit avant l' `SKCanvasView` instanciation de.

Le `PaintSurface` Gestionnaire obtient les deux valeurs d’énumération à partir des `Picker` vues :

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

Les captures d’écran présentent diverses `Picker` sélections :

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

L’iPhone à gauche montre comment le membre de l' `SKPointMode.Points` énumération provoque le `DrawPoints` rendu de chacun des points du `SKPoint` tableau sous la forme d’un carré si l’extrémité de ligne est `Butt` ou `Square` . Des cercles sont affichés si l’extrémité de ligne est `Round` .

La capture d’écran Android affiche le résultat de `SKPointMode.Lines` . La `DrawPoints` méthode dessine une ligne entre chaque paire de `SKPoint` valeurs, à l’aide de l’extrémité de ligne spécifiée, dans ce cas `Round` .

Quand vous utilisez `SKPointMode.Polygon` à la place, une ligne est dessinée entre les points successifs du tableau, mais si vous regardez très attentivement, vous verrez que ces lignes ne sont pas connectées. Chacune de ces lignes distinctes commence et se termine par l’extrémité de ligne spécifiée. Si vous sélectionnez les `Round` majuscules, les lignes peuvent sembler être connectées, mais elles ne sont pas connectées.

Le fait que les lignes soient connectées ou non connectées est un aspect crucial de l’utilisation des chemins d’accès aux graphiques.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
