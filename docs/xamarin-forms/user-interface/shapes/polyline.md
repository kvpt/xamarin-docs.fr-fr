---
title: 'Xamarin.FormsFormes : polyligne'
description: La Xamarin.Forms classe Polyline peut être utilisée pour dessiner une série de lignes droites connectées.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b6c884caa7bfb301f949f353f3c6c3445704aa89
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101355"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.FormsFormes : polyligne

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Polyline` classe dérive de la `Shape` classe et peut être utilisée pour dessiner une série de lignes droites connectées. Pour plus d’informations sur les propriétés que la `Polyline` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Polyline` définit les propriétés suivantes :

- `Points`, de type `PointCollection` , qui est une collection de `Point` structures qui décrivent les points de vertex de la polyligne.
- `FillRule`, de type `FillRule` , qui spécifie la façon dont les zones d’intersection dans la polyligne sont combinées. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

## <a name="create-a-polyline"></a>Créer une polyligne

L’exemple de code XAML suivant montre comment dessiner un polygone :

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          HeightRequest="100"
          WidthRequest="500" />
```

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
