---
title: Unités indépendantes de l’appareil et pixels
description: Cet article explore les différences entre les coordonnées et les coordonnées SkiaSharp Xamarin.Forms et les illustre avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0c1ae9e05b6671d45d8df485a89cfc0dea86632d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937304"
---
# <a name="pixels-and-device-independent-units"></a>Unités indépendantes de l’appareil et pixels

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Explorez les différences Xamarin.Forms entre les coordonnées et coordonnées SkiaSharp_

Cet article explore les différences dans le système de coordonnées utilisé dans SkiaSharp et Xamarin.Forms . Vous pouvez obtenir des informations pour effectuer une conversion entre les deux systèmes de coordonnées et dessiner des graphiques qui remplissent une zone particulière :

![Ovale qui remplit l’écran](pixels-images/screenfillexample.png)

Si vous avez programmé dans pendant Xamarin.Forms un certain temps, vous pouvez avoir une idée des Xamarin.Forms coordonnées et des tailles. Les cercles dessinés dans les deux articles précédents peuvent paraître un peu petit pour vous.

Ces cercles *sont* petits par rapport à la Xamarin.Forms taille. Par défaut, SkiaSharp dessine en unités de pixels tout en Xamarin.Forms base les coordonnées et les tailles sur une unité indépendante du périphérique établie par la plateforme sous-jacente. (Vous trouverez plus d’informations sur le Xamarin.Forms système de coordonnées dans le [Chapitre 5. Gestion des tailles](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) du livre création d' *Mobile Apps avec Xamarin.Forms *.)

La page dans le programme [**SkewSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) intitulée **taille** de la surface utilise la sortie de texte SkiaSharp pour afficher la taille de la surface d’affichage à partir de trois sources différentes :

- Les Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) Propriétés normales et [`Height`](xref:Xamarin.Forms.VisualElement.Height) de l' `SKCanvasView` objet.
- [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)Propriété de l' `SKCanvasView` objet.
- La [`Size`](xref:SkiaSharp.SKImageInfo.Size) propriété de la `SKImageInfo` valeur, qui est cohérente avec `Width` les `Height` Propriétés et utilisées dans les deux pages précédentes.

La [`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe montre comment afficher ces valeurs. Le constructeur enregistre l' `SKCanvasView` objet sous la forme d’un champ. il est donc accessible dans le `PaintSurface` Gestionnaire d’événements :

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas`comprend six `DrawText` méthodes différentes, mais cette [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) méthode est la plus simple :

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Vous spécifiez la chaîne de texte, les coordonnées X et Y où le texte doit commencer et un `SKPaint` objet. La coordonnée X spécifie le positionnement du côté gauche du texte, mais regarde : la coordonnée Y spécifie la position de la *ligne de base* du texte. Si vous avez déjà écrit à la main du papier à courbes, la ligne de base est la ligne sur laquelle les caractères se trouvent et les hampes inférieures (telles que celles sur les lettres g, p, q et y).

L' `SKPaint` objet vous permet de spécifier la couleur du texte, la famille de polices et la taille du texte. Par défaut, la [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) valeur de la propriété est 12, ce qui se traduit par un texte minuscule sur les appareils haute résolution tels que les téléphones. Dans toutes les applications, à l’exception des plus simples, vous aurez également besoin d’informations sur la taille du texte que vous affichez. La `SKPaint` classe définit une [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) propriété et plusieurs [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) méthodes, mais pour des besoins moins fantaisistes, la [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) propriété fournit une valeur recommandée pour l’espacement des lignes de texte successives.

Le `PaintSurface` gestionnaire suivant crée un `SKPaint` objet pour un `TextSize` de 40 pixels, qui est la hauteur verticale souhaitée du texte à partir du haut des hampes supérieures jusqu’au bas des descendants. La `FontSpacing` valeur `SKPaint` retournée par l’objet est un peu plus grande que cela, environ 47 pixels.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

La méthode commence la première ligne de texte avec une coordonnée X de 20 (pour une petite marge à gauche) et une coordonnée Y de `fontSpacing` , ce qui est un peu plus grand que ce qui est nécessaire pour afficher la hauteur complète de la première ligne de texte en haut de la surface d’affichage. Après chaque appel à `DrawText` , la coordonnée Y est augmentée d’un ou de deux incréments de `fontSpacing` .

Voici le programme en cours d’exécution :

[![Capture d’écran triple de la page de taille de surface](pixels-images/surfacesize-small.png)](pixels-images/surfacesize-large.png#lightbox "Capture d’écran triple de la page de taille de surface")

Comme vous pouvez le voir, la `CanvasSize` propriété de `SKCanvasView` et la `Size` propriété de la `SKImageInfo` valeur sont cohérentes dans le rapport des dimensions en pixels. Les `Height` `Width` Propriétés et de `SKCanvasView` sont des Xamarin.Forms Propriétés et signalent la taille de la vue dans les unités indépendantes du périphérique définies par la plateforme.

Le simulateur iOS sept sur la gauche a deux pixels par unité indépendante du périphérique et le lien Android 5 au Centre a trois pixels par unité. C’est la raison pour laquelle le cercle simple présenté précédemment a des tailles différentes sur différentes plateformes.

Si vous préférez travailler entièrement sur des unités indépendantes des appareils, vous pouvez le faire en affectant `IgnorePixelScaling` à la propriété de la `SKCanvasView` valeur `true` . Toutefois, vous n’aimez peut-être pas les résultats. SkiaSharp restitue les graphiques sur une surface d’appareil plus petite, avec une taille de pixel égale à la taille de la vue dans les unités indépendantes du périphérique. (Par exemple, SkiaSharp utilise une surface d’affichage de 360 x 512 pixels sur le point de vue 5.) Elle augmente ensuite la taille de cette image, ce qui se traduit par des dentelés de bitmaps perceptibles.

Pour conserver la même résolution d’image, une meilleure solution consiste à écrire vos propres fonctions simples pour effectuer une conversion entre les deux systèmes de coordonnées.

En plus de la `DrawCircle` méthode, `SKCanvas` définit également deux `DrawOval` méthodes qui dessinent une ellipse. Une ellipse est définie par deux rayons plutôt qu’un seul rayon. Il s’agit du *rayon majeur* et du *rayon mineur*. La `DrawOval` méthode dessine une ellipse avec les deux rayons parallèles aux axes X et Y. (Si vous avez besoin de dessiner une ellipse avec des axes qui ne sont pas parallèles aux axes X et Y, vous pouvez utiliser une transformation de rotation comme indiqué dans l’article [**transformation de rotation**](../transforms/rotate.md) ou graphique, comme indiqué dans l’article [**trois façons de dessiner un arc**](../curves/arcs.md)). Cette surcharge de la [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) méthode nomme les deux paramètres de rayon `rx` et `ry` pour indiquer qu’ils sont parallèles aux axes X et Y :

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

Est-il possible de dessiner une ellipse qui remplit la surface d’affichage ? La page remplissage de l' **ellipse** montre comment. Le `PaintSurface` Gestionnaire d’événements de la classe [**EllipseFillPage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) soustrait la moitié de la largeur du trait des `xRadius` `yRadius` valeurs et pour qu’elle corresponde à l’ensemble de l’ellipse et à son contour dans la surface d’affichage :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

Ici, il s’exécute :

[![Capture d’écran triple de la page de taille de surface](pixels-images/ellipsefill-small.png)](pixels-images/ellipsefill-large.png#lightbox "Capture d’écran triple de la page de taille de surface")

L’autre [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode possède un [`SKRect`](xref:SkiaSharp.SKRect) argument, qui est un rectangle défini en termes de coordonnées X et Y de son coin supérieur gauche et du coin inférieur droit. L’ovale remplit ce rectangle, ce qui suggère qu’il est possible de l’utiliser dans la page de **remplissage d’ellipse** comme suit :

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Toutefois, cela tronque tous les bords du contour de l’ellipse sur les quatre côtés. Vous devez ajuster tous les `SKRect` arguments de constructeur en fonction du `strokeWidth` pour rendre ce travail correct :

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
