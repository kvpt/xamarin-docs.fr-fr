---
title: Intégration de texte et de graphisme
description: Cet article explique comment déterminer la taille de la chaîne de texte rendue pour intégrer les applications Xamarin.Forms de texte avec des graphiques de SkiaSharp et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d23b4dbb97f4f98ff0361bb056e394bb7cbd941a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645643"
---
# <a name="integrating-text-and-graphics"></a>Intégration de texte et de graphisme

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Découvrez comment déterminer la taille de la chaîne de texte rendue à intégrer le texte avec des graphiques de SkiaSharp_

Cet article montre comment mesurer le texte, mettre à l’échelle le texte à une taille particulière et intégrer le texte avec les autres graphiques :

![](text-images/textandgraphicsexample.png "Texte encadré par des rectangles")

Cette image inclut également un rectangle arrondi. Le SkiaSharp `Canvas` classe inclut [ `DrawRect` ](xref:SkiaSharp.SKCanvas.DrawRect*) méthodes pour dessiner un rectangle et [ `DrawRoundRect` ](xref:SkiaSharp.SKCanvas.DrawRoundRect*) méthodes pour dessiner un rectangle avec des angles arrondis. Ces méthodes permettent le rectangle doit être défini comme un `SKRect` valeur ou par d’autres moyens.

Le **texte encadré** page centres une courte chaîne de texte sur la page et les entoure avec un frame composé d’une paire de rectangles à coins arrondis. Le [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe montre comment procéder.

Dans SkiaSharp, vous utilisez la `SKPaint` classe pour définir les attributs de texte et de police, mais vous pouvez également l’utiliser pour obtenir la taille de rendu de texte. Début de ce qui suit `PaintSurface` Gestionnaire d’événements appelle deux différents `MeasureText` méthodes. La première [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) appel a une simple `string` argument et retourne la largeur en pixels du texte basée sur les attributs de police actuelle. Le programme calcule ensuite une nouvelle `TextSize` propriété de la `SKPaint` objet basé sur cette largeur restituée, actuel `TextSize` propriété et la largeur de la zone d’affichage. Ce calcul est destiné à définir `TextSize` afin que la chaîne de texte pour être rendue à 90 % de la largeur de l’écran :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

La seconde [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) appel a un `SKRect` argument, afin qu’il obtient la largeur et la hauteur du texte rendu. Le `Height` propriété de ce `SKRect` valeur dépend de la présence de lettres majuscules, des jambages supérieurs et hampes inférieures dans la chaîne de texte. Différents `Height` valeurs sont indiquées pour le texte chaînes « mom », « cat » et « dog », par exemple.

Le `Left` et `Top` propriétés de la `SKRect` structure indiquent les coordonnées du coin supérieur gauche du texte rendu si le texte est affiché par un `DrawText` appeler avec les positions X et Y de 0. Par exemple, lorsque ce programme est en cours d’exécution sur un simulateur iPhone 7, `TextSize` reçoit la valeur 90.6254 à la suite le calcul suivant le premier appel à `MeasureText`. Le `SKRect` valeur obtenue à partir de la deuxième appel à `MeasureText` a les valeurs de propriété suivantes :

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88 ;

N’oubliez pas que les coordonnées X et Y vous passer à la `DrawText` méthode spécifier le côté gauche du texte à la ligne de base. Le `Top` valeur indique que le texte s’étend des 68 pixels au-dessus de cette ligne de base (en soustrayant 68 informatique à partir de 88) 20 pixels sous la ligne de base. Le `Left` valeur 6 indique que le texte commence six pixels vers la droite de la valeur de X dans le `DrawText` appeler. Ainsi, pour l’espacement normal. Si vous souhaitez afficher le texte correctement dans le coin supérieur gauche de l’affichage, passer les valeurs négatives de ces `Left` et `Top` valeurs comme les coordonnées X et Y de `DrawText`, dans cet exemple, &ndash;6 et 68.

Le `SKRect` structure définit plusieurs propriétés pratiques et des méthodes, dont certaines sont utilisées dans le reste de la `PaintSurface` gestionnaire. Le `MidX` et `MidY` valeurs indiquent les coordonnées du centre du rectangle. (Dans l’exemple iPhone 7, ces valeurs sont 338.4107 et &ndash;24.) Le code suivant utilise ces valeurs pour le calcul le plus simple de coordonnées pour centrer le texte sur l’affichage :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

Le `SKImageInfo` structure d’informations sur définit également un [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) propriété de type `SKRect`, de sorte que vous pouvez également calculer `xText` et `yText` comme suit :

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

Le `PaintSurface` gestionnaire se termine par deux appels à `DrawRoundRect`, qui nécessitent des arguments de `SKRect`. Cela `SKRect` valeur est basée sur le `SKRect` valeur obtenue à partir de la `MeasureText` (méthode), mais il ne peut pas être le même. Tout d’abord, il doit être un peu plus grands afin que le rectangle à coins arrondis ne dessine sur les bords du texte. Deuxièmement, il doit être décalés dans l’espace afin que le `Left` et `Top` valeurs correspondent au coin supérieur gauche où le rectangle doit être positionné. Ces deux tâches sont réalisées par le [ `Offset` ](xref:SkiaSharp.SKRect.Offset*) et [ `Inflate` ](xref:SkiaSharp.SKRect.Inflate*) méthodes définies par `SKRect`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

Ensuite, le reste de la méthode est simple. Il crée un autre `SKPaint` objet pour les bordures et les appels `DrawRoundRect` à deux reprises. Le deuxième appel utilise un rectangle gonflé par un autre 10 pixels. Le premier appel spécifie un rayon de l’angle de 20 pixels. Le second possède un rayon de 30 pixels, afin qu’ils semblent être parallèles :

 [![](text-images/framedtext-small.png "Capture d’écran triple de la page de texte encadré")](text-images/framedtext-large.png#lightbox "Triple capture d’écran de la page de texte encadré")

Vous pouvez activer votre téléphone ou un simulateur sur le côté pour afficher le texte et augmenter la taille de trame.

Si vous avez besoin uniquement du texte sur l’écran de centrer, vous pouvez le faire environ sans le texte de la mesure. Au lieu de cela, définissez la [ `TextAlign` ](xref:SkiaSharp.SKPaint.TextAlign) propriété du `SKPaint` pour le membre d’énumération [ `SKTextAlign.Center` ](xref:SkiaSharp.SKTextAlign). La coordonnée X que vous spécifiez dans le `DrawText` (méthode), puis indique où est positionné au centre horizontal du texte. Si vous passez le milieu de l’écran pour le `DrawText` (méthode), le texte est centré horizontalement et *presque* centré verticalement, car la ligne de base est centré verticalement.

Texte peut être traité beaucoup comme tout autre objet graphique. Une option simple est d’afficher le plan des caractères du texte :

[![](text-images/outlinedtext-small.png "Le triple de capture d’écran de la page du texte avec contour")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Cela s’effectue simplement en modifiant la normale `Style` propriété de la `SKPaint` objet à partir de sa valeur par défaut `SKPaintStyle.Fill` à `SKPaintStyle.Stroke`et en spécifiant une largeur de trait. Le `PaintSurface` Gestionnaire de la **du texte avec contour** page montre comment procéder :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Un autre objet graphique courant est l’image bitmap. C’est un vaste sujet présentée en détail dans la section [ **SkiaSharp Bitmaps**](../bitmaps/index.md), mais le prochain article, [ **principes fondamentaux de la Bitmap dans SkiaSharp**](bitmaps.md), Fournit une présentation plus directe.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
