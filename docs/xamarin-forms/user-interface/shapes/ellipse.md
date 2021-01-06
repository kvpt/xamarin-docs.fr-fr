---
title: 'Xamarin.Forms Formes : ellipse'
description: La Xamarin.Forms classe ellipse peut être utilisée pour dessiner des ellipses et des cercles.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b4a905371b8bda93616fee0677cbd0932d8699e5
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939769"
---
# <a name="no-locxamarinforms-shapes-ellipse"></a>Xamarin.Forms Formes : ellipse

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Ellipse` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des ellipses et des cercles. Pour plus d’informations sur les propriétés que la `Ellipse` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

La `Ellipse` classe affecte `Aspect` à la propriété, héritée de la `Shape` classe, la valeur `Stretch.Fill` . Pour plus d’informations sur la `Aspect` propriété, consultez [Stretch Shapes](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Créer une ellipse

Pour dessiner une ellipse, créez un `Ellipse` objet et définissez ses `WidthRequest` `HeightRequest` Propriétés et. Pour peindre l’intérieur de l’ellipse, affectez `Fill` à sa propriété la valeur a [`Color`](xref:Xamarin.Forms.Color) . Pour attribuer un contour à l’ellipse, affectez la valeur `Stroke` à sa propriété [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propriété spécifie l’épaisseur du contour de l’ellipse.

Pour dessiner un cercle, rendez `WidthRequest` les `HeightRequest` Propriétés et de l' `Ellipse` objet égales.

L’exemple de code XAML suivant montre comment dessiner une Ellipse remplie :

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

Dans cet exemple, une Ellipse remplie en rouge avec les dimensions 150x50 (unités indépendantes du périphérique) est dessinée :

![Ellipse remplie](ellipse-images/filled.png "Ellipse remplie")

L’exemple de code XAML suivant montre comment dessiner un cercle :

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

Dans cet exemple, un cercle rouge avec les dimensions 150 x 150 (unités indépendantes du périphérique) est dessiné :

![Circle](ellipse-images/circle.png "Circle")

Pour plus d’informations sur le dessin d’une ellipse en pointillés, consultez [dessiner des formes en pointillés](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
