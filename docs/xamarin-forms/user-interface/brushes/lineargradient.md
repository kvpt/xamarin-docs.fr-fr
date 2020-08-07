---
title: 'Xamarin.FormsPinceaux : dégradés linéaires'
description: La Xamarin.Forms classe LinearGradientBrush peint une zone avec un dégradé linéaire.
ms.prod: xamarin
ms.assetid: BEA2B3F5-96B0-4E39-88A6-0FAFE95C3DCD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 734ecae4fdafd71f0c88ddc5e4b4ed0c672f2019
ms.sourcegitcommit: 579ec4f2884fa391e5e214a3952cd6004c521eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919630"
---
# <a name="no-locxamarinforms-brushes-linear-gradients"></a>Xamarin.FormsPinceaux : dégradés linéaires

![API d’aperçu](~/media/shared/preview.png "Cette API est actuellement en préversion.")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)

La `LinearGradientBrush` classe dérive de la `GradientBrush` classe et peint une zone avec un dégradé linéaire, qui fusionne deux couleurs ou plus sur une ligne appelée axe du dégradé. `GradientStop`les objets sont utilisés pour spécifier les couleurs du dégradé et leurs positions. Pour plus d’informations sur les `GradientStop` objets, consultez [ Xamarin.Forms pinceaux : dégradés](gradient.md).

La `LinearGradientBrush` classe définit les propriétés suivantes :

- `StartPoint`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente les coordonnées à deux dimensions de départ du dégradé linéaire. La valeur par défaut de cette propriété est (0,0).
- `EndPoint`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente les coordonnées à deux dimensions de fin du dégradé linéaire. La valeur par défaut de cette propriété est (1,1).

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `LinearGradientBrush` classe également comme une `IsEmpty` méthode qui retourne un `bool` qui indique si des objets ont été assignés au pinceau `GradientStop` .

> [!NOTE]
> Les dégradés linéaires peuvent également être créés avec la `linear-gradient()` fonction CSS.

## <a name="create-a-lineargradientbrush"></a>Créer un LinearGradientBrush

Les points de dégradé d’un pinceau de dégradé linéaire sont positionnés le long de l’axe du dégradé. L’orientation et la taille de l’axe du dégradé peuvent être modifiées à l’aide des `StartPoint` Propriétés et du pinceau `EndPoint` . En manipulant ces propriétés, vous pouvez créer des dégradés horizontaux, verticaux et en diagonale, inversez le sens du dégradé, condensez la répartition du dégradé, et bien plus encore.

Les `StartPoint` `EndPoint` Propriétés et sont relatives à la zone qui est peinte. (0,0) représente l’angle supérieur gauche de la zone qui est peinte et (1,1) représente le coin inférieur droit de la zone qui est peinte. Le diagramme suivant illustre l’axe du dégradé d’un pinceau dégradé linéaire Diagonal :

![Frame avec un axe de dégradé Diagonal](lineargradient-images/gradient-axis.png)

Dans ce diagramme, la ligne en pointillés affiche l’axe du dégradé, qui met en surbrillance le chemin d’interpolation du dégradé du point de départ au point de terminaison.

### <a name="create-a-horizontal-linear-gradient"></a>Créer un dégradé linéaire horizontal

Pour créer un dégradé linéaire horizontal, créez un `LinearGradientBrush` objet et affectez à sa valeur `StartPoint` (0, 0) et à la valeur `EndPoint` (1,0). Ensuite, ajoutez deux objets ou plus `GradientStop` à la `LinearGradientBrush.GradientStops` collection, qui spécifient les couleurs du dégradé et leurs positions.

L’exemple de code XAML suivant montre un horizontal `LinearGradientBrush` défini en tant que `Background` d’un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->
        <LinearGradientBrush EndPoint="1,0">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>  
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `LinearGradientBrush` qui interpole horizontalement du jaune au vert :

![Cadre peint avec un LinearGradientBrush horizontal](lineargradient-images/horizontal.png)

### <a name="create-a-vertical-linear-gradient"></a>Créer un dégradé linéaire vertical

Pour créer un dégradé linéaire vertical, créez un `LinearGradientBrush` objet et affectez à sa valeur `StartPoint` (0, 0) et à la valeur `EndPoint` (0, 1). Ensuite, ajoutez deux objets ou plus `GradientStop` à la `LinearGradientBrush.GradientStops` collection, qui spécifient les couleurs du dégradé et leurs positions.

L’exemple de code XAML suivant montre un vertical `LinearGradientBrush` défini en tant que `Background` d’un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->    
        <LinearGradientBrush EndPoint="0,1">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `LinearGradientBrush` qui interpole verticalement du jaune au vert :

![Frame peint avec un LinearGradientBrush vertical](lineargradient-images/vertical.png)

### <a name="create-a-diagonal-linear-gradient"></a>Créer un dégradé linéaire Diagonal

Pour créer un dégradé linéaire Diagonal, créez un `LinearGradientBrush` objet et affectez à sa valeur `StartPoint` (0, 0) et à la valeur `EndPoint` (1,1). Ensuite, ajoutez deux objets ou plus `GradientStop` à la `LinearGradientBrush.GradientStops` collection, qui spécifient les couleurs du dégradé et leurs positions.

L’exemple de code XAML suivant montre une diagonale `LinearGradientBrush` définie en tant que `Background` d’un [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0)      
             Endpoint defaults to (1,1) -->
        <LinearGradientBrush>
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `LinearGradientBrush` qui interpole en diagonale du jaune au vert :

![Cadre peint avec un LinearGradientBrush Diagonal](lineargradient-images/diagonal.png)

## <a name="related-links"></a>Liens connexes

- [BrushesDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)
- [Xamarin.FormsPinceaux : dégradés](gradient.md)
