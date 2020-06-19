---
title: 'Xamarin.FormsFormes : ellipse'
description: La Xamarin.Forms classe ellipse peut être utilisée pour dessiner des ellipses et des cercles.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 142deccbcd29548e2d72b7144a01322f9909d98f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101284"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsFormes : ellipse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Ellipse` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des ellipses et des cercles. Pour plus d’informations sur les propriétés que la `Ellipse` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

La `Ellipse` classe affecte `Aspect` à la propriété, héritée de la `Shape` classe, la valeur `Stretch.Fill` .

## <a name="create-an-ellipse"></a>Créer une ellipse

L’exemple de code XAML suivant montre comment dessiner une Ellipse remplie :

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

L’exemple de code XAML suivant montre comment dessiner un cercle :

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
