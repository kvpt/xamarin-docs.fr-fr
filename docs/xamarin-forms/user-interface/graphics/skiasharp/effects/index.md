---
title: Effets SkiaSharp
description: Découvrez comment modifier l’affichage normal des graphiques avec des dégradés, la mosaïque de bitmaps, les modes de fusion, le flou et d’autres effets.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e63d92cde5925561dae4217c3c46af2c2c59f795
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563989"
---
# <a name="skiasharp-effects"></a>Effets SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKPaint`](xref:SkiaSharp.SKPaint) classe SkiaSharp définit six propriétés qui peuvent être classées sous le terme général des _effets_. Il s’agit des propriétés qui modifient l’affichage normal des graphiques d’une certaine façon. Les effets SkiaSharp se répartissent en six catégories :

## <a name="path-effects"></a>[Effets de tracé](../curves/effects.md)

Affectez à la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) propriété de la valeur `SKPaint` un objet de type [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) pour afficher les lignes en pointillés, ou pour rayer ou remplir une zone avec un modèle créé à partir de tracés. L’effet du chemin d’accès a été abordé plus haut dans cette série dans les [**effets du chemin d’accès de l’article dans SkiaSharp**](../curves/effects.md).

## <a name="shaders"></a>[Nuanceurs](shaders/index.md)

Affectez [`Shader`](xref:SkiaSharp.SKPaint.Shader) à la propriété de la valeur d' `SKPaint` un objet de type [`SKShader`](xref:SkiaSharp.SKShader) pour afficher des dégradés linéaires ou circulaires, des bitmaps en mosaïque et des modèles de bruit perl.

## <a name="blend-modes"></a>[Modes de fusion](blend-modes/index.md)

Affectez [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) à la propriété de la valeur d' `SKPaint` un membre de l' [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) énumération pour régir ce qui se produit lorsqu’un graphique source est affiché sur une destination. SkiaSharp prend en charge tous les modes de fusion et de composition CSS, y compris les modes porter-Duff, les modes de fusion séparable et les modes de fusion non séparables.

## <a name="mask-filters"></a>[Filtres de masque](mask-filters.md)

Affectez [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) à la propriété de la valeur `SKPaint` un objet de type [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) pour les flous et d’autres effets alpha.

## <a name="image-filters"></a>[Filtres d’image](image-filters.md)

Affectez [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) à la propriété de la valeur `SKPaint` un objet de type pour brouiller les [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) bitmaps et créer des ombres portées, des reliefs ou des effets de gravure.

## <a name="color-filters"></a>[Filtres de couleur](color-filters.md)

Affectez [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) à la propriété de la valeur d' `SKPaint` un objet de type [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) pour modifier les couleurs à l’aide de tables ou de transformations de matrice.

Tous les exemples de code de ces articles se trouvent dans le [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos). Dans la page d’hébergement, sélectionnez **SkiaSharp Effects**.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)