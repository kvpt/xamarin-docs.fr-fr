---
title: 'Xamarin.Forms Formes : polyligne'
description: La Xamarin.Forms classe Polyline peut être utilisée pour dessiner une série de lignes droites connectées.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2c80a82c50c34d45184e8f6359e8940b697e9823
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375055"
---
# <a name="no-locxamarinforms-shapes-polyline"></a>Xamarin.Forms Formes : polyligne

![API de la version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polyline` classe dérive de la `Shape` classe et peut être utilisée pour dessiner une série de lignes droites connectées. Une polyligne est similaire à un polygone, sauf que le dernier point d’une polyligne n’est pas connecté au premier point. Pour plus d’informations sur les propriétés que la `Polyline` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Polyline` définit les propriétés suivantes :

- `Points`, de type `PointCollection` , qui est une collection de `Point` structures qui décrivent les points de vertex de la polyligne.
- `FillRule`, de type `FillRule` , qui spécifie la façon dont les zones d’intersection dans la polyligne sont combinées. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Le `PointsCollection` type est un `ObservableCollection` d' [`Point`](xref:Xamarin.Forms.Point) objets. La `Point` structure définit `X` les `Y` Propriétés et, de type `double` , qui représentent une paire de coordonnées x et y dans un espace 2D. Par conséquent, la `Points` propriété doit être définie sur une liste de paires de coordonnées x et y qui décrivent les points de vertex de polylignes, délimitées par une virgule unique et/ou un ou plusieurs espaces. Par exemple, « 40, 10 70, 80 » et « 40 10, 70 80 » sont tous deux valides.

Pour plus d’informations sur l' `FillRule` énumération, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).

## <a name="create-a-polyline"></a>Créer une polyligne

Pour dessiner une polyligne, créez un `Polyline` objet et affectez `Points` à sa propriété les sommets d’une forme. Pour attribuer un contour à la polyligne, affectez à sa propriété la valeur `Stroke` a [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propriété spécifie l’épaisseur du contour de la polyligne.

> [!IMPORTANT]
> Si vous affectez `Fill` à la propriété d’un la valeur `Polyline` [`Color`](xref:Xamarin.Forms.Color) , l’espace intérieur de la polyligne est peint, même si le point de départ et le point de terminaison ne se croisent pas.

L’exemple de code XAML suivant montre comment dessiner une polyligne :

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="1" />
```

Dans cet exemple, une polyligne rouge est dessinée :

![Polyligne](polyline-images/stroke.png "Polyligne")

L’exemple de code XAML suivant montre comment dessiner une polyligne en pointillés :

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

Dans cet exemple, la polyligne est en pointillés :

![Ligne en pointillés](polyline-images/dashed.png "Ligne en pointillés")

Pour plus d’informations sur le dessin d’une polyligne en pointillés, consultez [dessiner des formes en pointillés](index.md#draw-dashed-shapes).

L’exemple de code XAML suivant montre une polyligne qui utilise la règle de remplissage par défaut :

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

Dans cet exemple, le comportement de remplissage de la polyligne est déterminé à l’aide de la `EvenOdd` règle de remplissage.

![Ligne EvenOdd](polyline-images/evenodd.png "EvenOdd polyine")

L’exemple de code XAML suivant montre une polyligne qui utilise la `Nonzero` règle de remplissage :

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![Polyligne différente de zéro](polyline-images/nonzero.png "Polyligne différente de zéro")

Dans cet exemple, le comportement de remplissage de la polyligne est déterminé à l’aide de la `Nonzero` règle de remplissage.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
- [Xamarin.Forms Formes : règles de remplissage](fillrules.md)