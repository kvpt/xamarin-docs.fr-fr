---
title: 'Xamarin.Forms Pinceaux : dégradés radiaux'
description: La Xamarin.Forms classe RadialGradientBrush peint une zone avec un dégradé radial.
ms.prod: xamarin
ms.assetid: 099BA530-3B38-4005-9B19-A0EB4D4DEEFC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5bba3fd223d5777036cb0e31030aad22d46ed864
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940042"
---
# <a name="no-locxamarinforms-brushes-radial-gradients"></a>Xamarin.Forms Pinceaux : dégradés radiaux

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

La `RadialGradientBrush` classe dérive de la `GradientBrush` classe et peint une zone avec un dégradé radial, qui fusionne deux couleurs ou plus sur un cercle. `GradientStop` les objets sont utilisés pour spécifier les couleurs du dégradé et leurs positions. Pour plus d’informations sur les `GradientStop` objets, consultez [ Xamarin.Forms pinceaux : dégradés](gradient.md).

La `RadialGradientBrush` classe définit les propriétés suivantes :

- `Center`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point central du cercle pour le dégradé radial. La valeur par défaut de cette propriété est (0.5, 0.5).
- `Radius`, de type `double` , qui représente le rayon du cercle pour le dégradé radial. La valeur par défaut de cette propriété est 0,5.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `RadialGradientBrush` classe a également une `IsEmpty` méthode qui retourne un `bool` qui indique si des objets ont été assignés au pinceau `GradientStop` .

> [!NOTE]
> Les dégradés radiaux peuvent également être créés avec la `radial-gradient()` fonction CSS.

## <a name="create-a-radialgradientbrush"></a>Créer un RadialGradientBrush

Les points de dégradé d’un pinceau de dégradé radial sont positionnés le long d’un axe de dégradé défini par un cercle. L’axe du dégradé rayonne à partir du centre du cercle jusqu’à sa circonférence. La position et la taille du cercle peuvent être modifiées à l’aide des `Center` Propriétés et du pinceau `Radius` . Le cercle définit le point de terminaison du dégradé. Par conséquent, un point de dégradé à 1,0 définit la couleur à la circonférence du cercle. Un point de dégradé à 0,0 définit la couleur au centre du cercle.

Pour créer un dégradé radial, créez un `RadialGradientBrush` objet et définissez ses `Center` `Radius` Propriétés et. Ensuite, ajoutez deux objets ou plus `GradientStop` à la `RadialGradientBrush.GradientStops` collection, qui spécifient les couleurs du dégradé et leurs positions.

L’exemple de code XAML suivant montre un `RadialGradientBrush` défini en tant que `Background` d’un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml    
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- Center defaults to (0.5,0.5)
             Radius defaults to (0.5) -->
        <RadialGradientBrush>
            <GradientStop Color="Red"
                          Offset="0.1" />
            <GradientStop Color="DarkBlue"
                          Offset="1.0" />
        </RadialGradientBrush>
    </Frame.Background>
</Frame>
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `RadialGradientBrush` qui interpole du rouge au bleu foncé. Le centre du dégradé radial est positionné au centre du `Frame` :

![Cadre peint avec un RadialGradientBrush centré](radialgradient-images/center.png)

L’exemple de code XAML suivant déplace le centre du dégradé radial vers l’angle supérieur gauche de [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="0.0,0.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `RadialGradientBrush` qui interpole du rouge au bleu foncé. Le centre du dégradé radial est positionné en haut à gauche de `Frame` :

![Cadre peint avec un RadialGradientBrush en haut à gauche](radialgradient-images/top-left.png)

L’exemple de code XAML suivant déplace le centre du dégradé radial vers le coin inférieur droit de [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="1.0,1.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>            
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `RadialGradientBrush` qui interpole du rouge au bleu foncé. Le centre du dégradé radial est positionné dans le coin inférieur droit de `Frame` :

![Cadre peint avec un RadialGradientBrush en bas à droite](radialgradient-images/bottom-right.png)

## <a name="related-links"></a>Liens connexes

- [BrushesDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms Pinceaux : dégradés](gradient.md)
