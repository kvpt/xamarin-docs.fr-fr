---
title: 'Xamarin.FormsFormes : ellipse'
description: La Xamarin.Forms classe ellipse peut être utilisée pour dessiner des ellipses et des cercles.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 725f892a667b4e89d55266abcf69e5394b09b6a5
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243803"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsFormes : ellipse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

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

![Cercle](ellipse-images/circle.png "Circle")

Pour plus d’informations sur le dessin d’une ellipse en pointillés, consultez [dessiner des formes en pointillés](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsFormes](index.md)
