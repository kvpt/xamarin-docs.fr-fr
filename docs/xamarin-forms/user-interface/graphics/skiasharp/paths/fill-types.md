---
title: Types de remplissage des tracés
description: Cet article examine les différents effets possibles avec les types de remplissage de chemin d’accès SkiaSharp et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 98081ed1a9aef1260150671d4fd026dd64c20b62
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291987"
---
# <a name="the-path-fill-types"></a>Types de remplissage des tracés

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez les différents effets possibles avec les types de remplissage de chemin SkiaSharp_

Deux profils dans un chemin d’accès peuvent se chevaucher, et les lignes qui composent un contour unique peuvent se chevaucher. Toute zone délimitée peut potentiellement être remplie, mais vous souhaiterez peut-être pas remplir toutes les zones fermées. Voici un exemple :

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

Vous contrôlez un peu cela. L’algorithme de remplissage est régi par la propriété [`SKFillType`](xref:SkiaSharp.SKPath.FillType) de `SKPath`, que vous définissez sur un membre de l’énumération [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) :

- `Winding`, la valeur par défaut
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Les algorithmes enroulement et pair-impair déterminent si n’importe quelle zone délimitée est remplie ou pas remplie avec une ligne hypothétique dessinée à partir de cette zone à l’infini. Cette ligne traverse un ou plusieurs lignes de limite qui composent le chemin d’accès. Avec le mode enroulement, si le nombre de lignes de limite dessiné d’équilibre d’une seule direction le nombre de lignes dessinées dans l’autre sens, puis la zone n’est pas remplie. Sinon, la zone est remplie. L’algorithme pair-impair remplit une zone si le nombre de lignes de la limite est impair.

Avec plusieurs chemins de routine, l’algorithme enroulement remplit souvent toutes les zones délimités d’un chemin d’accès. Généralement, l’algorithme pair-impair produit des résultats plus intéressants.

L’exemple classique est une étoile à cinq branches, comme illustré dans la page **étoile à cinq branches** . Le fichier [**FivePointedStarPage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml) instancie deux vues de `Picker` pour sélectionner le type de remplissage du tracé et si le chemin d’accès est rayé ou rempli, et dans quel ordre :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Le fichier code-behind utilise les deux valeurs `Picker` pour dessiner une étoile à cinq branches :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle),
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Normalement, le type de remplissage du chemin d’accès doit affecter uniquement les remplissages et non les traits, mais les deux modes d' `Inverse` affectent les remplissages et les traits. Pour les remplissages, les deux types de `Inverse` remplissent les zones de manière opposée afin que la zone en dehors de l’étoile soit remplie. Pour les traits, les deux types de `Inverse` colorent tout sauf le trait. À l’aide de ces types de remplissage inverse peut produire des effets impairs, comme le montre la capture d’écran iOS :

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

La capture d’écran Android affiche les effets pair-impair et d’enroulement classiques, mais l’ordre du trait et du remplissage affecte également les résultats.

L’algorithme enroulement est dépendante de la direction que les lignes sont dessinées. Généralement lorsque vous créez un chemin d’accès, vous pouvez contrôler cette direction en tant que vous spécifiez que les lignes sont dessinées à partir d’un point vers un autre. Toutefois, la classe `SKPath` définit également des méthodes comme `AddRect` et `AddCircle` qui dessinent des contours entiers. Pour contrôler la façon dont ces objets sont dessinés, les méthodes incluent un paramètre de type [`SKPathDirection`](xref:SkiaSharp.SKPathDirection), qui a deux membres :

- `Clockwise`
- `CounterClockwise`

Les méthodes de `SKPath` qui incluent un paramètre `SKPathDirection` lui attribuent la valeur par défaut `Clockwise`.

La page cercles qui se **chevauchent** crée un tracé avec quatre cercles qui se chevauchent avec un type de remplissage de tracé pair-impair :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

Il s’agit d’une image intéressante créée avec un minimum de code :

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
