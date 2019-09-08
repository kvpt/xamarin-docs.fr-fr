---
title: Unités indépendantes de l’appareil et pixels
description: Cet article explore les différences entre les coordonnées de SkiaSharp et Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: d6011175a735eb81f83a023f7d32fccd6feadd47
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759471"
---
# <a name="pixels-and-device-independent-units"></a>Unités indépendantes de l’appareil et pixels

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez les différences entre les coordonnées de SkiaSharp et Xamarin.Forms_

Cet article explore les différences dans le système de coordonnées utilisé dans SkiaSharp et Xamarin.Forms. Vous pouvez obtenir des informations pour effectuer une conversion entre les deux systèmes de coordonnées et également dessiner des graphiques qui remplissent une zone particulière :

![](pixels-images/screenfillexample.png "Un ovale qui remplit l’écran")

Si vous avez programmé dans Xamarin.Forms pendant un certain temps, vous pouvez avoir une idée pour Xamarin.Forms coordonnées et tailles. Les cercles dessinées dans les deux précédents articles peuvent sembler un peu petites pour vous.

Ces cercles *sont* faible par rapport de tailles de Xamarin.Forms. Par défaut, SkiaSharp Dessine en unités de pixels tandis que les coordonnées et tailles des bases Xamarin.Forms sur une unité indépendante du périphérique établie par la plateforme sous-jacente. (Vous trouverez plus d’informations sur le système de coordonnées de Xamarin.Forms dans [chapitre 5. Gestion des tailles](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) du livre *création d’applications mobiles avec Xamarin.Forms*.)

La page dans le [ **SkewSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programme intitulée **taille de la Surface** utilise la sortie de texte SkiaSharp pour afficher la taille de la surface d’affichage à partir de trois sources différentes :

- Xamarin.Forms normal [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) et [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propriétés de la `SKCanvasView` objet.
- Le [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize) propriété de la `SKCanvasView` objet.
- Le [ `Size` ](xref:SkiaSharp.SKImageInfo.Size) propriété de la `SKImageInfo` valeur, qui est cohérente avec la `Width` et `Height` propriétés utilisées dans les deux pages précédentes.

Le [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe montre comment afficher ces valeurs. Le constructeur enregistre le `SKCanvasView` objet sous la forme d’un champ et sont accessibles dans le `PaintSurface` Gestionnaire d’événements :

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

`SKCanvas` inclut six différents `DrawText` méthodes, mais cela [ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) méthode est la plus simple :

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Si vous spécifiez la chaîne de texte, les coordonnées X et Y où le texte doit commencer et un `SKPaint` objet. La coordonnée X spécifie le positionnement du côté gauche du texte, mais observez les éléments suivants : La coordonnée Y spécifie la position de la *ligne de base* du texte. Si vous avez déjà écrit à la main sur papier, la ligne de base est la ligne sur le site de caractères, et en dessous descendent lesquelles jambages (telles que celles sur les lettres g, p, q et y).

Le `SKPaint` objet vous permet de spécifier la couleur du texte, la famille de polices et la taille du texte. Par défaut, le [ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize) propriété a la valeur 12, ce qui entraîne un texte minuscule sur les appareils tels que les téléphones haute résolution. Dans des déploiements autres que les applications la plus simple, vous devez également des informations sur la taille du texte que vous affichez. Le `SKPaint` classe définit un [ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics) propriété et plusieurs [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) méthodes, mais moins fantaisistes aux besoins de, la [ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing) propriété attribue une valeur recommandée pour l’espacement des lignes successives de texte.

Ce qui suit `PaintSurface` gestionnaire crée un `SKPaint` de l’objet pour un `TextSize` de 40 pixels, qui est la hauteur souhaitée verticale du texte à partir du haut des jambages supérieurs vers le bas des jambages. Le `FontSpacing` valeur qui le `SKPaint` retourne d’objet est un peu plus grande que celle, environ 47 pixels.

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

La méthode commence à la première ligne de texte avec une coordonnée X de 20 (pour une petite marge à gauche) et une coordonnée Y de `fontSpacing`, qui est un peu plus que ce qui est nécessaire pour afficher la hauteur totale de la première ligne de texte en haut de la surface d’affichage. Après chaque appel à `DrawText`, la coordonnée Y est augmentée par incréments d’une ou deux de `fontSpacing`.

Voici le programme en cours d’exécution :

[![](pixels-images/surfacesize-small.png "Capture d’écran triple de la page de la taille de la Surface")](pixels-images/surfacesize-large.png#lightbox "Triple capture d’écran de la page de la taille de la Surface")

Comme vous pouvez le voir, le `CanvasSize` propriété de la `SKCanvasView` et le `Size` propriété de la `SKImageInfo` valeur sont cohérents dans les dimensions en pixels de création de rapports. Le `Height` et `Width` propriétés de la `SKCanvasView` sont des propriétés de Xamarin.Forms et indiquent la taille de la vue dans les unités indépendantes du périphérique définies par la plateforme.

Le simulateur de sept iOS sur la gauche a deux pixels par unité indépendante du périphérique, et le Android Nexus 5 dans le centre a trois pixels par unité. C’est pourquoi le cercle simple illustré précédemment présente des tailles différentes sur différentes plateformes.

Si vous préférez travailler entièrement en unités indépendantes du périphérique, vous pouvez le faire en définissant le `IgnorePixelScaling` propriété de la `SKCanvasView` à `true`. Toutefois, vous n’aimez pas les résultats. SkiaSharp restitue les graphiques sur une surface de périphérique plus petits, avec une taille de pixel égale à la taille de la vue en unités indépendantes du périphérique. (Par exemple, SkiaSharp utiliserait une surface d’affichage de 360 x 512 pixels sur le Nexus 5.) Il augmente ensuite cette image en taille, ce qui entraîne une bitmap notable crénelage.

Pour conserver la même résolution d’image, une meilleure solution consiste à écrire vos propres fonctions simples à effectuer une conversion entre les deux systèmes de coordonnées.

Outre le `DrawCircle` (méthode), `SKCanvas` définit également deux `DrawOval` méthodes dessiner une ellipse. Une ellipse est définie par deux rayons plutôt que dans un rayon unique. Ceux-ci sont connus comme les *radius principale* et *petit rayon*. Le `DrawOval` méthode dessine une ellipse avec les deux rayons parallèles aux axes X et Y. (Si vous avez besoin dessiner une ellipse avec les axes qui ne sont pas parallèles aux axes X et Y, vous pouvez utiliser une transformation de rotation comme indiqué dans l’article [ **le Rotate Transform** ](../transforms/rotate.md) ou un chemin d’accès de graphiques comme indiqué dans le article [ **trois moyens pour dessiner un Arc**](../curves/arcs.md)). Cette surcharge de la [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) les paramètres de deux rayons les noms de méthode `rx` et `ry` pour indiquer qu’ils sont parallèles aux axes X et Y :

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

Est-il possible de dessiner une ellipse qui remplit la surface d’affichage ? Le **Ellipse remplir** page montre comment. Le `PaintSurface` Gestionnaire d’événements dans le [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) classe soustrait la moitié de la largeur du trait à partir de la `xRadius` et `yRadius` valeurs en fonction de l’ensemble ellipse et son décrit dans la surface d’affichage :

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

[![](pixels-images/ellipsefill-small.png "Capture d’écran triple de la page de la taille de la Surface")](pixels-images/ellipsefill-large.png#lightbox "Triple capture d’écran de la page de la taille de la Surface")

L’autre [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode a un [ `SKRect` ](xref:SkiaSharp.SKRect) argument, qui est un rectangle défini en termes des coordonnées X et Y de son coin supérieur gauche et le coin inférieur droit. L’ovale remplit ce rectangle, ce qui suggère qu’il est parfois possible d’utiliser dans le **Ellipse remplir** page comme celle-ci :

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Toutefois, qui tronque tous les bords du contour de l’ellipse sur les quatre côtés. Vous devez ajuster toutes le `SKRect` les arguments de constructeur basés sur le `strokeWidth` pour y parvenir à droite :

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
