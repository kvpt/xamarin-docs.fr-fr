---
title: Chemins d’accès et des lignes de SkiaSharp
description: Cet article explique comment utiliser SkiaSharp pour dessiner des lignes et des tracés graphiques dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 316A15FE-383D-4D06-8641-BAC7EE7474CA
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 14b92bb576679dee3408c5805c6b698addd9bd8a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657444"
---
# <a name="skiasharp-lines-and-paths"></a>Chemins d’accès et des lignes de SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Permet de dessiner des lignes et des graphismes chemins d’accès SkiaSharp_

Le [section précédente](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md) a démontré que le SkiaSharp `SKCanvas` classe inclut plusieurs méthodes pour dessiner des cercles, ellipses, rectangles, rectangles arrondis, texte et des bitmaps. Cette section et les sections suivantes couvrent les différentes classes liées à la création et le rendu *tracés graphiques*.

Le chemin d’accès de graphiques est l’approche la plus généralisée pour dessiner des lignes et des courbes dans SkiaSharp. Cette section traite de l’utilisation un [ `SKPath` ](xref:SkiaSharp.SKPath) objet pour dessiner des lignes droites et d’utiliser une collection de lignes droites minuscules (appelé un *polyligne*) pour dessiner des courbes que vous pouvez définir de façon algorithmique. Une section ultérieure sur [ **SkiaSharp courbes et chemins d’accès** ](../curves/index.md) présente les différentes sortes de courbes pris en charge par `SKPath`.

Tous les exemples de programmes dans cette section apparaissent sous l’en-tête **lignes et chemins d’accès** dans la page d’accueil de la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programme, puis, dans le [ **Chemins** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths) dossier de cette solution.

## <a name="lines-and-stroke-capslinesmd"></a>[Lignes et embouts de trait](lines.md)

Découvrez comment utiliser SkiaSharp pour dessiner des lignes avec des embouts de trait différentes.

## <a name="path-basicspathsmd"></a>[Principes de base du chemin d’accès](paths.md)

Explorez le SkiaSharp `SKPath` objet pour la combinaison de lignes et des courbes.

## <a name="the-path-fill-typesfill-typesmd"></a>[Les types de remplissage de chemin d’accès](fill-types.md)

Découvrez les différents effets possibles avec les types de remplissage de chemin d’accès SkiaSharp.

## <a name="polylines-and-parametric-equationspolylinesmd"></a>[Polylignes et équations paramétriques](polylines.md)

SkiaSharp permet d’afficher n’importe quelle ligne, que vous pouvez définir avec des équations paramétriques.

## <a name="dots-and-dashesdotsmd"></a>[Points et tirets](dots.md)

Maîtrisez les complexités de dessiner des lignes en pointillés et dans SkiaSharp.

## <a name="finger-paintingfinger-paintmd"></a>[Peinture au doigt](finger-paint.md)

Utilisez vos doigts pour peindre sur le canevas.


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
