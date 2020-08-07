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
ms.openlocfilehash: 52043bd507d35a1ebe2628c13c14429c604569c9
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918483"
---
# <a name="no-locxamarinforms-shapes-polygon"></a>Xamarin.FormsFormes : polygone

![API de la version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polygon` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des polygones, qui sont des séries de lignes connectées qui forment des formes fermées. Pour plus d’informations sur les propriétés que la `Polygon` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Polygon` définit les propriétés suivantes :

- `Points`, de type `PointCollection` , qui est une collection de [`Point`](xref:Xamarin.Forms.Point) structures qui décrivent les points de vertex du polygone.
- `FillRule`, de type `FillRule` , qui spécifie la façon dont le remplissage intérieur de la forme est déterminé. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Le `PointsCollection` type est un `ObservableCollection` d' [`Point`](xref:Xamarin.Forms.Point) objets. La `Point` structure définit `X` les `Y` Propriétés et, de type `double` , qui représentent une paire de coordonnées x et y dans un espace 2D. Par conséquent, la `Points` propriété doit être définie sur une liste de paires de coordonnées x et y qui décrivent les points de vertex du polygone, délimitée par une virgule unique et/ou un ou plusieurs espaces. Par exemple, « 40, 10 70, 80 » et « 40 10, 70 80 » sont tous deux valides.

Pour plus d’informations sur l' `FillRule` énumération, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).

## <a name="create-a-polygon"></a>Créer un polygone

Pour dessiner un polygone, créez un `Polygon` objet et affectez `Points` à sa propriété les sommets d’une forme. Une ligne est automatiquement dessinée qui connecte le premier et le dernier point. Pour peindre l’intérieur du polygone, affectez la valeur `Fill` à sa propriété [`Color`](xref:Xamarin.Forms.Color) . Pour attribuer un contour au polygone, affectez la valeur `Stroke` à sa propriété [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propriété spécifie l’épaisseur du contour du polygone.

L’exemple de code XAML suivant montre comment dessiner un polygone plein :

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

Dans cet exemple, un polygone plein qui représente un triangle est dessiné :

![Polygone plein](polygon-images/filled.png "Polygone plein")

L’exemple de code XAML suivant montre comment dessiner un polygone en pointillés :

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

Dans cet exemple, le contour du polygone est discontinu :

![Polygone avec tirets](polygon-images/dashed.png "Polygone avec tirets")

Pour plus d’informations sur le dessin d’un polygone en pointillés, consultez [dessiner des formes en pointillés](index.md#draw-dashed-shapes).

L’exemple de code XAML suivant illustre un polygone qui utilise la règle de remplissage par défaut :

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

Dans cet exemple, le comportement de remplissage de chaque polygone est déterminé à l’aide de la `EvenOdd` règle de remplissage.

![Polygone EvenOdd](polygon-images/evenodd.png "Polygone EvenOdd")

L’exemple de code XAML suivant illustre un polygone qui utilise la `Nonzero` règle de remplissage :

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![Polygone différent de zéro](polygon-images/nonzero.png "Polygone différent de zéro")

Dans cet exemple, le comportement de remplissage de chaque polygone est déterminé à l’aide de la `Nonzero` règle de remplissage.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsFormes](index.md)
- [Xamarin.FormsFormes : règles de remplissage](fillrules.md)
