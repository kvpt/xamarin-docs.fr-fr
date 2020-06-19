---
title: 'Xamarin.FormsFormes : polygone'
description: La Xamarin.Forms classe Polygon peut être utilisée pour dessiner des polygones, qui sont des séries de lignes connectées qui forment des formes fermées.
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2d67a6a4bbea6a4be27f0c0440d9c28ffd5a188f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101299"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsFormes : polygone

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Polygon` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des polygones, qui sont des séries de lignes connectées qui forment des formes fermées. Pour plus d’informations sur les propriétés que la `Polygon` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Polygon` définit les propriétés suivantes :

- `Points`, de type `PointCollection` , qui est une collection de `Point` structures qui décrivent les points de vertex du polygone.
- `FillRule`, de type `FillRule` , qui spécifie la façon dont le remplissage intérieur de la forme est déterminé. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

## <a name="create-a-polygon"></a>Créer un polygone

L’exemple de code XAML suivant montre comment dessiner un polygone :

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         HeightRequest="100"
         WidthRequest="200" />
```

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
