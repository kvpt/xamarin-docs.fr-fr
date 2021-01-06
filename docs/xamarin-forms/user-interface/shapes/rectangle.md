---
title: 'Xamarin.Forms Formes : rectangle'
description: La Xamarin.Forms classe Rectangle peut être utilisée pour dessiner des rectangles.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d70d850f0c51abfc75d15d6cefd6dc429de8c8a
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939964"
---
# <a name="no-locxamarinforms-shapes-rectangle"></a>Xamarin.Forms Formes : rectangle

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Rectangle` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des rectangles et des carrés. Pour plus d’informations sur les propriétés que la `Rectangle` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Rectangle` définit les propriétés suivantes :

- `RadiusX`, de type `double` , qui est le rayon de l’axe x utilisé pour arrondir les angles du rectangle. La valeur par défaut de cette propriété est 0,0.
- `RadiusY`, de type `double` , qui est le rayon de l’axe y utilisé pour arrondir les angles du rectangle. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `Rectangle` classe affecte `Aspect` à la propriété, héritée de la `Shape` classe, la valeur `Stretch.Fill` . Pour plus d’informations sur la `Aspect` propriété, consultez [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-a-rectangle"></a>Créer un rectangle

Pour dessiner un rectangle, créez un `Rectangle` objet et définissez ses `WidthRequest` `HeightRequest` Propriétés et. Pour peindre l’intérieur du rectangle, affectez la valeur `Fill` à sa propriété [`Color`](xref:Xamarin.Forms.Color) . Pour attribuer un contour au rectangle, affectez `Stroke` à sa propriété la valeur a [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propriété spécifie l’épaisseur du contour du rectangle.

Pour affecter des angles arrondis au rectangle, définissez ses `RadiusX` `RadiusY` Propriétés et. Ces propriétés définissent les rayons axe x et axe y utilisés pour arrondir les angles du rectangle.

Pour dessiner un carré, rendez `WidthRequest` les `HeightRequest` Propriétés et de l' `Rectangle` objet égales.

L’exemple de code XAML suivant montre comment dessiner un rectangle rempli :

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

Dans cet exemple, un rectangle rempli en rouge avec les dimensions 150x50 (unités indépendantes du périphérique) est dessiné :

![Rectangle rempli](rectangle-images/filled.png "Rectangle rempli")

L’exemple de code XAML suivant montre comment dessiner un rectangle rempli, avec des angles arrondis :

```xaml
<Rectangle Fill="Blue"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10"
           WidthRequest="200"
           HeightRequest="100"
           HorizontalOptions="Start" />
```

Dans cet exemple, un rectangle plein bleu avec des angles arrondis est dessiné :

![Rectangle avec coins arrondis](rectangle-images/rounded.png "Rectangle avec coins arrondis")

Pour plus d’informations sur le dessin d’un rectangle en pointillés, consultez [dessiner des formes en pointillés](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
