---
title: Modes de fusion SkiaSharp
description: Utilisez les modes de fusion pour définir ce qui se produit lorsque les objets graphiques sont empilés les uns sur les autres.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 829d764f03dd77c6126c2f4bced750ae570a3bc6
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83425698"
---
# <a name="skiasharp-blend-modes"></a>Modes de fusion SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Ces articles se concentrent sur la [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) propriété de [`SKPaint`](xref:SkiaSharp.SKPaint) . La `BlendMode` propriété est de type [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) , une énumération avec 29 membres.

La `BlendMode` propriété détermine ce qui se passe lorsqu’un objet graphique (souvent appelé _source_) est rendu sur des objets graphiques existants (appelé la _destination_). Normalement, nous pensons que le nouvel objet graphique masque les objets en dessous. Mais cela se produit uniquement parce que le mode de fusion par défaut est, ce qui `SKBlendMode.SrcOver` signifie que la source est dessinée _sur_ la destination. Les 28 autres membres de `SKBlendMode` provoquent d’autres effets. Dans la programmation graphique, la technique consistant à combiner des objets graphiques de différentes manières est appelée _composition_.

## <a name="the-skblendmodes-enumeration"></a>Énumération SKBlendModes

Les modes de fusion SkiaSharp correspondent étroitement à ceux décrits dans la spécification du W3C sur la [**composition et le niveau de fusion 1**](https://www.w3.org/TR/compositing-1/) . La [**vue d’ensemble**](https://skia.org/user/api/SkBlendMode_Overview) de skia SkBlendMode fournit également des informations générales utiles. Pour une présentation générale des modes de fusion, l’article [**modes de fusion**](https://en.wikipedia.org/wiki/Blend_modes) de Wikipédia est un bon début. Les modes de fusion étant pris en charge dans Adobe Photoshop, il existe de nombreuses informations en ligne supplémentaires sur les modes de fusion dans ce contexte.

Les 29 membres de l' `SKBlendMode` énumération peuvent être divisés en trois catégories :

| Porter-Duff | Quoiqu'    | Non séparable |
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

Les noms de ces trois catégories prendront plus de sens dans les discussions qui suivent. L’ordre dans lequel les membres sont répertoriés ici est le même que dans la définition de l' `SKBlendMode` énumération. Les 13 membres de l’énumération de la première colonne ont les valeurs entières comprises entre 0 et 12. La deuxième colonne sont des membres de l’énumération qui correspondent aux entiers de 13 à 24, et les membres de la troisième colonne ont des valeurs de 25 à 28.

Ces _modes de fusion sont décrits dans le_ même ordre que dans le document du W3C sur la **composition et le niveau de fusion** , mais il existe quelques différences : le `Src` mode est appelé « _copie_ » dans le document du W3C et `Plus` est plus _léger_. Le document W3C définit un mode de fusion _normal_ qui n’est pas inclus dans `SKBlendModes` , car il est identique à `SrcOver` . Le `Modulate` mode de fusion (en haut de la première colonne) n’est pas inclus dans le document du W3C et la discussion du `Multiply` mode précède `Screen` .

Étant donné que le `Modulate` mode de fusion est propre à skia, il est abordé comme un mode porter-Duff supplémentaire et en mode séparable.

## <a name="the-importance-of-transparency"></a>L’importance de la transparence

Historiquement, la composition a été développée conjointement avec le concept de _canal alpha_. Dans une surface d’affichage telle que l' `SKCanvas` objet et une image bitmap de couleur entière, chaque pixel se compose de 4 octets : 1 octet pour chaque composant rouge, vert et bleu, et d’un octet supplémentaire pour la transparence. Ce composant alpha est 0 pour la transparence totale et 0xFF pour une opacité complète, avec différents niveaux de transparence entre ces valeurs.

La plupart des modes de fusion dépendent de la transparence. En général, lorsqu’un `SKCanvas` est obtenu pour la première fois dans un `PaintSurface` Gestionnaire, ou lorsqu’un `SKCanvas` est créé pour dessiner sur une bitmap, la première étape est cet appel :

```csharp
canvas.Clear();
```

Cette méthode remplace tous les pixels du canevas par des pixels noirs transparents, équivalant à `new SKColor(0, 0, 0, 0)` ou à l’entier 0x00000000. Tous les octets de tous les pixels sont initialisés à zéro.

La surface de dessin d’un `SKCanvas` qui est obtenue dans un `PaintSurface` gestionnaire peut sembler avoir un arrière-plan blanc, mais c’est uniquement parce que le `SKCanvasView` lui-même a un arrière-plan transparent et que la page a un arrière-plan blanc. Vous pouvez démontrer ce fait à vous-même en affectant à la propriété Xamarin. Forms la valeur d' `BackgroundColor` `SKCanvasView` une couleur Xamarin. Forms :

```csharp
canvasView.BackgroundColor = Color.Red;
```

Ou, dans une classe qui dérive de `ContentPage` , vous pouvez définir la couleur d’arrière-plan de la page :

```csharp
BackgroundColor = Color.Red;
```

Cet arrière-plan rouge s’affiche derrière vos graphiques SkiaSharp, car la zone de dessin SkiaSharp elle-même est transparente.

L’article [**SkiaSharp transparent**](../../basics/transparency.md) a montré des techniques de base dans l’utilisation de la transparence pour réorganiser plusieurs graphiques dans une image composite. Les modes de fusion vont au-delà de cela, mais la transparence reste cruciale pour les modes de fusion.

## <a name="skiasharp-porter-duff-blend-modes"></a>[SkiaSharp porter-Duff Blend, modes](porter-duff.md)

Utilisez les modes de fusion porter-Duff pour composer des scènes en fonction des images source et de destination.

## <a name="skiasharp-separable-blend-modes"></a>[Modes de fusion séparable SkiaSharp](separable.md)

Utilisez les modes de fusion séparable pour modifier les couleurs rouge, vert et bleu.

## <a name="skiasharp-non-separable-blend-modes"></a>[SkiaSharp modes de fusion non séparables](non-separable.md)

Utilisez les modes de fusion non séparables pour modifier la teinte, la saturation ou la luminosité.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
