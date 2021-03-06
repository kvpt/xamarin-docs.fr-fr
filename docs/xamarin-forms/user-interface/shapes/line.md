---
title: 'Xamarin.Forms Formes : ligne'
description: La Xamarin.Forms classe Line peut être utilisée pour dessiner des lignes.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5d5ecd7804c6cd8321827cd8aef98245124beca0
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939109"
---
# <a name="no-locxamarinforms-shapes-line"></a>Xamarin.Forms Formes : ligne

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Line` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des lignes. Pour plus d’informations sur les propriétés que la `Line` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Line` définit les propriétés suivantes :

- `X1`, de type double, indique la coordonnée x du point de départ de la ligne. La valeur par défaut de cette propriété est 0,0.
- `Y1`, de type double, indique la coordonnée y du point de départ de la ligne. La valeur par défaut de cette propriété est 0,0.
- `X2`, de type double, indique la coordonnée x du point de terminaison de la ligne. La valeur par défaut de cette propriété est 0,0.
- `Y2`, de type double, indique la coordonnée y du point de terminaison de la ligne. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour plus d’informations sur le contrôle de la façon dont les extrémités de ligne sont dessinées, consultez [terminaisons de ligne de contrôle](index.md#control-line-ends).

## <a name="create-a-line"></a>Créer une ligne

Pour dessiner une ligne, créez un `Line` objet et définissez ses `X1` `Y1` Propriétés et sur son point de départ, et `X2` ses `Y` Propriétés et sur son point de terminaison. En outre, affectez `Stroke` à sa propriété la valeur a [`Color`](xref:Xamarin.Forms.Color) , car une ligne sans trait est invisible.

> [!NOTE]
> La définition `Fill` de la propriété d’un n' `Line` a aucun effet, car une ligne n’a pas d’intérieur.

L’exemple de code XAML suivant montre comment dessiner une ligne :

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red" />
```

Dans cet exemple, une ligne diagonale rouge est dessinée de (40, 0) à (0120) :

![Ligne](line-images/line.png "Ligne")

Étant donné que les `X1` `Y1` Propriétés,, `X2` et `Y2` ont des valeurs par défaut égales à 0, il est possible de dessiner des lignes avec une syntaxe minimale :

```xaml
<Line Stroke="Red"
      X2="200" />
```

Dans cet exemple, une ligne horizontale de 200 unités indépendantes du périphérique est définie. Étant donné que les autres propriétés ont la valeur 0 par défaut, une ligne est dessinée de (0,0) à (200, 0).

L’exemple de code XAML suivant montre comment dessiner une ligne en pointillés :

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeDashArray="1,1"
      StrokeDashOffset="6" />
```

Dans cet exemple, un trait Diagonal en pointillés bleus foncé est dessiné de (40, 0) à (0120) :

![Ligne en pointillés](line-images/dashed-line.png "Ligne en pointillés")

Pour plus d’informations sur le dessin d’une ligne en pointillés, consultez [dessiner des formes en pointillés](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
