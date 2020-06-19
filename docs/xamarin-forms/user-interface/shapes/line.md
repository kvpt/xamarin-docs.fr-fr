---
title: 'Xamarin.FormsFormes : ligne'
description: La Xamarin.Forms classe Line peut être utilisée pour dessiner des lignes.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a69c505fe83618f06bafe6a49b8b5ce84aef096b
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101292"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.FormsFormes : ligne

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Line` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des lignes. Pour plus d’informations sur les propriétés que la `Line` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Line` définit les propriétés suivantes :

- `X1`, de type double, indique la coordonnée x du point de départ de la ligne. La valeur par défaut de cette propriété est 0,0.
- `X2`, de type double, indique la coordonnée x du point de terminaison de la ligne. La valeur par défaut de cette propriété est 0,0.
- `Y1`, de type double, indique la coordonnée y du point de départ de la ligne. La valeur par défaut de cette propriété est 0,0.
- `Y2`, de type double, indique la coordonnée y du point de terminaison de la ligne. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

## <a name="create-a-line"></a>Créer une ligne

L’exemple de code XAML suivant montre comment dessiner une ligne :

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeThickness="4"
      HeightRequest="120"
      WidthRequest="120" />
```

> [!NOTE]
> La définition `Fill` de la propriété d’un n' `Line` a aucun effet, car une ligne n’a pas d’intérieur.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
