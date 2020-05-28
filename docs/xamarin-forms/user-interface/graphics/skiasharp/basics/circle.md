---
title: ''
description: Cet article explique les principes de base du dessin SkiaSharp, y compris les canevas et les objets Paint, dans Xamarin.Forms les applications et illustre cela avec un exemple de code.
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fb873102bfb8568b8298a39ea2429fb6c27af175
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137720"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Dessin d’un cercle simple dans SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez les principes de base du dessin SkiaSharp, y compris les canevas et les objets Paint_

Cet article présente les concepts de dessin de graphiques dans Xamarin.Forms à l’aide de SkiaSharp, notamment la création d’un `SKCanvasView` objet pour héberger les graphiques, la gestion de l' `PaintSurface` événement et l’utilisation d’un `SKPaint` objet pour spécifier la couleur et d’autres attributs de dessin.

Le programme [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contient tous les exemples de code de cette série d’articles SkiaSharp. La première page est intitulée **simple Circle** et appelle la classe page [`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) . Ce code montre comment dessiner un cercle au centre de la page avec un rayon de 100 pixels. Le contour du cercle est rouge et l’intérieur du cercle est bleu.

![](circle-images/circleexample.png "A blue circle outlined in red")

La [`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) classe de page dérive de `ContentPage` et contient deux `using` directives pour les espaces de noms SkiaSharp :

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Le constructeur suivant de la classe crée un [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) objet, attache un gestionnaire pour l' [`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) événement et définit l' `SKCanvasView` objet en tant que contenu de la page :

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`Occupe la totalité de la zone de contenu de la page. Vous pouvez également combiner un `SKCanvasView` avec d’autres Xamarin.Forms `View` dérivés, comme vous le verrez dans d’autres exemples.

Le `PaintSurface` Gestionnaire d’événements est l’endroit où vous effectuez tout le dessin. Cette méthode peut être appelée plusieurs fois pendant l’exécution de votre programme. elle doit donc conserver toutes les informations nécessaires pour recréer l’affichage graphique :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

L' [`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs) objet qui accompagne l’événement a deux propriétés :

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info)de type[`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface)de type[`SKSurface`](xref:SkiaSharp.SKSurface)

La `SKImageInfo` structure contient des informations sur la surface de dessin, surtout, sa largeur et sa hauteur en pixels. L' `SKSurface` objet représente la surface de dessin elle-même. Dans ce programme, la surface de dessin est un affichage vidéo, mais dans d’autres programmes, un `SKSurface` objet peut également représenter un bitmap sur lequel vous utilisez SkiaSharp pour dessiner.

La propriété la plus importante de `SKSurface` est [`Canvas`](xref:SkiaSharp.SKSurface.Canvas) de type [`SKCanvas`](xref:SkiaSharp.SKCanvas) . Cette classe est un contexte de dessin graphique que vous utilisez pour effectuer le dessin réel. L' `SKCanvas` objet encapsule un État Graphics, qui comprend les transformations et le découpage de graphiques.

Voici un démarrage classique d’un `PaintSurface` Gestionnaire d’événements :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

La [`Clear`](xref:SkiaSharp.SKCanvas.Clear) méthode efface la zone de dessin avec une couleur transparente. Une surcharge vous permet de spécifier une couleur d’arrière-plan pour la zone de dessin.

L’objectif ici est de dessiner un cercle rouge rempli de bleu. Étant donné que cette image graphique particulière contient deux couleurs différentes, la tâche doit être effectuée en deux étapes. La première étape consiste à dessiner le contour du cercle. Pour spécifier la couleur et d’autres caractéristiques de la ligne, vous créez et initialisez un [`SKPaint`](xref:SkiaSharp.SKPaint) objet :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

La [`Style`](xref:SkiaSharp.SKPaint.Style) propriété indique que vous souhaitez *rayer* une ligne (dans ce cas, le contour du cercle) au lieu de *remplir* l’intérieur. Les trois membres de l' [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle) énumération sont les suivants :

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

Par défaut, il s’agit de `Fill`. Utilisez la troisième option pour rayer la ligne et remplir l’intérieur avec la même couleur.

Affectez [`Color`](xref:SkiaSharp.SKPaint.Color) à la propriété une valeur de type [`SKColor`](xref:SkiaSharp.SKColor) . Une façon d’obtenir une `SKColor` valeur consiste à convertir une Xamarin.Forms `Color` valeur en `SKColor` valeur à l’aide de la méthode d’extension [`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*) . La [`Extensions`](xref:SkiaSharp.Views.Forms.Extensions) classe de l' `SkiaSharp.Views.Forms` espace de noms comprend d’autres méthodes qui effectuent une conversion entre Xamarin.Forms des valeurs et des valeurs SkiaSharp.

La [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) propriété indique l’épaisseur de la ligne. Elle est définie sur 25 pixels.

Vous utilisez cet `SKPaint` objet pour dessiner le cercle :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Les coordonnées sont spécifiées par rapport à l’angle supérieur gauche de la surface d’affichage. Les coordonnées X augmentent à droite et les coordonnées Y augmentent. Dans la discussion sur les graphiques, la notation mathématique (x, y) est souvent utilisée pour désigner un point. Le point (0,0) est l’angle supérieur gauche de la surface d’affichage et est souvent appelé *origine*.

Les deux premiers arguments de `DrawCircle` indiquent les coordonnées X et Y du centre du cercle. Celles-ci sont affectées à la moitié de la largeur et de la hauteur de la surface d’affichage pour placer le centre du cercle au centre de la surface d’affichage. Le troisième argument spécifie le rayon du cercle et le dernier argument est l' `SKPaint` objet.

Pour remplir l’intérieur du cercle, vous pouvez modifier deux propriétés de l' `SKPaint` objet et appeler `DrawCircle` à nouveau. Ce code illustre également une autre façon d’acquérir une `SKColor` valeur à partir de l’un des nombreux champs de la [`SKColors`](xref:SkiaSharp.SKColors) structure :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

Cette fois-ci, l' `DrawCircle` appel remplit le cercle à l’aide des nouvelles propriétés de l' `SKPaint` objet.

Voici le programme s’exécutant sur iOS et Android :

[![](circle-images/simplecircle-small.png "Triple screenshot of the Simple Circle page")](circle-images/simplecircle-large.png#lightbox "Triple screenshot of the Simple Circle page")

Quand vous exécutez le programme vous-même, vous pouvez activer le téléphone ou le simulateur pour voir comment le graphique est redessiné. Chaque fois que le graphique doit être redessiné, le `PaintSurface` Gestionnaire d’événements est appelé à nouveau.

Il est également possible de colorier des objets graphiques avec des dégradés ou des vignettes bitmap. Ces options sont décrites dans la section sur les [**nuanceurs SkiaSharp**](../effects/shaders/index.md).

Un `SKPaint` objet est un peu plus qu’une collection de propriétés de dessin de graphiques. Ces objets sont légers. Vous pouvez réutiliser des `SKPaint` objets comme le fait ce programme, ou vous pouvez créer plusieurs `SKPaint` objets pour différentes combinaisons de propriétés de dessin. Vous pouvez créer et initialiser ces objets en dehors du `PaintSurface` Gestionnaire d’événements, et vous pouvez les enregistrer en tant que champs dans la classe de votre page.

> [!NOTE]
> La `SKPaint` classe définit un [`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) pour activer l’anticrénelage dans le rendu de vos graphiques. L’anticrénelage produit généralement des bords visuellement plus lisses, vous souhaiterez probablement définir cette propriété sur `true` la plupart de vos `SKPaint` objets. À des fins de simplicité, cette propriété n’est _pas_ définie dans la plupart des exemples de pages.

Bien que la largeur du contour du cercle soit définie sur 25 pixels &mdash; ou un quart du rayon du cercle &mdash; , il semble plus mince et il y a une bonne raison : la moitié de la largeur de la ligne est obscurcie par le cercle bleu. Les arguments de la `DrawCircle` méthode définissent les coordonnées géométriques abstraites d’un cercle. L’intérieur bleu est dimensionné à cette dimension sur le pixel le plus proche, mais le contour de 25 pixels est monté sur la moitié du cercle géométrique à l' &mdash; intérieur et à la moitié à l’extérieur.

L’exemple suivant dans l’article [intégration Xamarin.Forms à](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) l’article illustre cela visuellement.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
