---
title: 'Xamarin.FormsFormes : rectangle'
description: La Xamarin.Forms classe Rectangle peut être utilisée pour dessiner des rectangles.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da9649a4abb2cb65930d98576eda81739b711886
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101375"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.FormsFormes : rectangle

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Rectangle` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des rectangles. Pour plus d’informations sur les propriétés que la `Rectangle` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Rectangle` définit les propriétés suivantes :

- `RadiusX`, de type `double` , qui est le rayon de l’axe x utilisé pour arrondir les angles du rectangle. La valeur par défaut de cette propriété est 0,0.
- `RadiusY`, de type `double` , qui est le rayon de l’axe y utilisé pour arrondir les angles du rectangle. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `Rectangle` classe affecte `Aspect` à la propriété, héritée de la `Shape` classe, la valeur `Stretch.Fill` .

## <a name="create-a-rectangle"></a>Créer un rectangle

L’exemple de code XAML suivant montre comment dessiner un rectangle rempli, avec des angles arrondis :

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           RadiusX="12"
           RadiusY="24"           
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
