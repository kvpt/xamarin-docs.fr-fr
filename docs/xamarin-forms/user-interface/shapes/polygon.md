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
ms.openlocfilehash: 1c3164fbd1d7fb3bc3de15558cdd41f4791515eb
ms.sourcegitcommit: 22aca3e7e24038d6f83d68dbfc7435080223b1ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133762"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsFormes : polygone

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polygon` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des polygones, qui sont des séries de lignes connectées qui forment des formes fermées. Pour plus d’informations sur les propriétés que la `Polygon` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Polygon` définit les propriétés suivantes :

- `Points`, de type `PointCollection` , qui est une collection de [`Point`](xref:Xamarin.Forms.Point) structures qui décrivent les points de vertex du polygone.
- `FillRule`, de type `FillRule` , qui spécifie la façon dont le remplissage intérieur de la forme est déterminé. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Le `PointsCollection` type est un `ObservableCollection` d' [`Point`](xref:Xamarin.Forms.Point) objets. La `Point` structure définit `X` les `Y` Propriétés et, de type `double` , qui représentent une paire de coordonnées x et y dans un espace 2D. Par conséquent, la `Points` propriété doit être définie sur une liste de paires de coordonnées x et y qui décrivent les points de vertex du polygone, délimitée par une virgule unique et/ou un ou plusieurs espaces. Par exemple, « 40, 10 70, 80 » et « 40 10, 70 80 » sont tous deux valides.

L’énumération `FillRule` définit les membres suivants :

- `EvenOdd`représente une règle qui détermine si un point se trouve dans la région de remplissage du polygone. Il dessine un rayon du point vers l’infini dans n’importe quelle direction et compte le nombre de segments de chemin d’accès dans la forme que le rayon traverse. Si ce nombre est impair, le point est à l’intérieur de. Si ce nombre est pair, le point est à l’extérieur de.
- `Nonzero`représente une règle qui détermine si un point se trouve dans la région de remplissage du polygone. Il dessine un rayon du point vers l’infini dans n’importe quelle direction, puis examine les emplacements où un segment de la forme coupe le rayon. En commençant par un nombre égal à zéro, ajoutez un chaque fois qu’un segment croise le rayon de gauche à droite et soustrayez un chaque fois qu’un segment croise le rayon de droite à gauche. Comptez ensuite le nombre d’intersections : si le résultat est égal à zéro, le point est à l’extérieur du tracé. Dans le cas contraire, elle est à l’intérieur.

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

Pour plus d’informations sur le dessin d’un polygone en pointillés, consultez [traits en pointillés](index.md#dashed-shapes).

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
