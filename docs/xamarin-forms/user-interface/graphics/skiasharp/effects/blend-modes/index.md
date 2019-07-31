---
title: Modes de fusion SkiaSharp
description: Utilisez blend modes pour définir ce qui se passe lorsque les objets graphiques sont empilés les uns sur les autres.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 8071f310e899575699e1d0b925541f2863b00676
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645193"
---
# <a name="skiasharp-blend-modes"></a>Modes de fusion SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Ces articles se concentrent sur la [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) propriété du [ `SKPaint` ](xref:SkiaSharp.SKPaint). Le `BlendMode` propriété est de type [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode), une énumération avec les membres de 29.

Le `BlendMode` propriété détermine ce qui se passe lorsqu’un objet graphique (souvent appelé le _source_) est affiché au-dessus des objets de graphiques existants (appelé le _destination_). En règle générale, nous pensons que le nouvel objet de graphique pour masquer les objets situés en dessous. Mais cela se produit uniquement parce que le mode de fusion par défaut est `SKBlendMode.SrcOver`, ce qui signifie que la source est dessinée _sur_ la destination. Les autres 28 membres de `SKBlendMode` provoquer d’autres effets. Dans la programmation de graphiques, la technique de la combinaison d’objets graphiques de différentes manières est appelée _composition_.

## <a name="the-skblendmodes-enumeration"></a>L’énumération SKBlendModes

Les modes de blend SkiaSharp correspondent étroitement à celles décrites dans le W3C [ **composition et fusion de niveau 1** ](https://www.w3.org/TR/compositing-1/) spécification. Le Skia [ **SkBlendMode référence** ](https://skia.org/user/api/SkBlendMode_Reference) fournit également des informations générales utiles. Pour une introduction générale à mélanger les modes, le [ **les modes de fusion** ](https://en.wikipedia.org/wiki/Blend_modes) article de Wikipedia est un bon début. Modes de fusion sont prises en charge dans Adobe Photoshop, par conséquent, il est beaucoup plus d’informations en ligne sur les modes de fusion dans ce contexte.

Les 29 membres de le `SKBlendMode` énumération peut être divisée en trois catégories :

| Porter-Duff | Séparables    | Non séparables |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

Les noms de ces trois catégories prend plus de sens dans les discussions qui suivent. L’ordre que les membres sont répertoriés ici est le même que dans la définition de la `SKBlendMode` énumération. Les membres de le 13 énumération dans la première colonne ont les valeurs entières 0 à 12. La deuxième colonne sont membres de l’énumération qui correspondent à des entiers 13 à 24, et les membres dans la troisième colonne ont des valeurs de 25 à 28.

Ces modes de fusion sont décrits _dans le_ même ordre que dans le document du W3C sur la **composition et le niveau de fusion** , mais il existe quelques différences: Le `Src` mode est appelé « _copie_ » dans le document du `Plus` W3C et est appelé plus _clair_. Le document du W3C définit un _Normal_ mode blend qui n’est pas inclus dans `SKBlendModes` , car il serait identique `SrcOver`. Le `Modulate` mode blend (en haut de la première colonne) n’est pas inclus dans le document du W3C et la présentation de la `Multiply` mode précède `Screen`.

Étant donné que le `Modulate` blend mode étant spécifique à Skia, elle sera abordée sous la forme d’un mode de Porter-Duff supplémentaire et un mode séparable.

## <a name="the-importance-of-transparency"></a>L’importance de la transparence

Historiquement, la composition a été développée conjointement avec le concept de la _canal alpha_. Dans une surface d’affichage telle que `SKCanvas` l’objet et une image bitmap de couleur entière, chaque pixel se compose de 4 octets: 1 octet pour les composants rouge, vert et bleu, et un octet supplémentaire pour la transparence. Ce composant alpha est 0 pour une transparence intégrale et 0xFF pour l’opacité complète, avec différents niveaux de transparence entre ces valeurs.

La plupart des modes de blend s’appuient sur la transparence. Généralement, lorsque un `SKCanvas` est tout d’abord obtenu dans un `PaintSurface` gestionnaire, ou quand un `SKCanvas` est créé pour dessiner sur une image bitmap, la première étape est cet appel :

```csharp
canvas.Clear();
```

Cette méthode remplace tous les pixels de la zone de dessin avec les pixels noirs transparents, équivalents à `new SKColor(0, 0, 0, 0)` ou l’entier 0 x 00000000. Tous les octets de tous les pixels sont initialisés à zéro.

La surface de dessin d’un `SKCanvas` obtenu dans un `PaintSurface` gestionnaire peut sembler ont un arrière-plan blanc, mais c’est uniquement parce que le `SKCanvasView` lui-même a un arrière-plan transparent, et la page a un arrière-plan blanc. Vous pouvez démontrer cela à vous-même en définissant le Xamarin.Forms `BackgroundColor` propriété du `SKCanvasView` une couleur Xamarin.Forms :

```csharp
canvasView.BackgroundColor = Color.Red;
```

Ou, dans une classe qui dérive de `ContentPage`, vous pouvez définir la couleur d’arrière-plan de page :

```csharp
BackgroundColor = Color.Red;
```

Vous verrez que cet arrière-plan rouge derrière vos graphiques SkiaSharp, car le SkiaSharp canevas proprement dit est transparent.

L’article [ **SkiaSharp transparence** ](../../basics/transparency.md) a montré quelques techniques de base à l’aide de la transparence pour organiser plusieurs graphiques dans une image composite. Les modes de blend aller au-delà de celles, mais la transparence reste cruciale pour les modes de fusion. 

## <a name="skiasharp-porter-duff-blend-modesporter-duffmd"></a>[Modes de fusion SkiaSharp Porter-Duff](porter-duff.md)

Utilisez les modes de blend Duff de Porter pour composer des scènes basées sur des images source et de destination.

## <a name="skiasharp-separable-blend-modesseparablemd"></a>[Modes de blend séparables SkiaSharp](separable.md)

Utiliser les modes séparables blend pour modifier les couleurs rouges, vert et bleus.

## <a name="skiasharp-non-separable-blend-modesnon-separablemd"></a>[Modes de fusion non séparables de SkiaSharp](non-separable.md)

Utiliser les modes de fusion non séparables de modifier la teinte, saturation ou luminosité.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
