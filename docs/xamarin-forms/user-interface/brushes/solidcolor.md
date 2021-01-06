---
title: 'Xamarin.Forms Pinceaux : couleurs unies'
description: La Xamarin.Forms classe SolidColorBrush peint une zone avec une couleur unie.
ms.prod: xamarin
ms.assetid: 4225D40A-16C1-40E1-ACBE-23E321E7FDE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3be026df3d6c60ecef81a7409245941f34b8d563
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940016"
---
# <a name="no-locxamarinforms-brushes-solid-colors"></a>Xamarin.Forms Pinceaux : couleurs unies

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

La `SolidColorBrush` classe dérive de la `Brush` classe et est utilisée pour peindre une zone avec une couleur unie. Il existe plusieurs façons de spécifier la couleur d’un `SolidColorBrush` . Par exemple, vous pouvez spécifier sa couleur à l’aide d’une [`Color`](xref:Xamarin.Forms.Color) valeur ou de l’un des objets prédéfinis `SolidColorBrush` fournis par la `Brush` classe.

La `SolidColorBrush` classe définit la `Color` propriété, de type [`Color`](xref:Xamarin.Forms.Color) , qui représente la couleur du pinceau. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

La `SolidColorBrush` classe a également une `IsEmpty` méthode qui retourne un `bool` qui indique si une couleur a été assignée au pinceau.

## <a name="create-a-solidcolorbrush"></a>Créer un SolidColorBrush

Il existe trois techniques principales pour créer un `SolidColorBrush` . Vous pouvez créer un `SolidColorBrush` à partir de [`Color`](xref:Xamarin.Forms.Color) , utiliser un pinceau prédéfini ou créer un à `SolidColorBrush` l’aide de la notation hexadécimale.

### <a name="use-a-predefined-color"></a>Utiliser une couleur prédéfinie

Xamarin.Forms comprend un convertisseur de type qui crée un `SolidColorBrush` à partir d’une [`Color`](xref:Xamarin.Forms.Color) valeur. En XAML, cela permet de `SolidColorBrush` créer un à partir d’une valeur prédéfinie `Color` :

```xaml
<Frame Background="DarkBlue"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un bleu foncé `SolidColorBrush` :

![Image peinte avec une couleur prédéfinie](solidcolor-images/predefined-color.png)

La [`Color`](xref:Xamarin.Forms.Color) valeur peut également être spécifiée à l’aide de la syntaxe de balise de propriété :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
       <Frame.Background>
           <SolidColorBrush Color="DarkBlue" />
       </Frame.Background>
</Frame>
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un `SolidColorBrush` dont la couleur est spécifiée en définissant la `SolidColorBrush.Color` propriété.

### <a name="use-a-predefined-brush"></a>Utiliser un pinceau prédéfini

La `Brush` classe définit un ensemble d’objets couramment utilisés `SolidColorBrush` . L’exemple suivant utilise l’un de ces objets prédéfinis `SolidColorBrush` :

```xaml
<Frame Background="{x:Static Brush.Indigo}"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />       
```

Le code C# équivalent est :

```csharp
Frame frame = new Frame
{
    Background = Brush.Indigo,
    BorderColor = Color.LightGray,
    // ...
};
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec un indigo `SolidColorBrush` :

![Frame peint avec un SolidColorBrush prédéfini](solidcolor-images/predefined-brush.png)

Pour obtenir la liste des objets prédéfinis `SolidColorBrush` fournis par la `Brush` classe, consultez [pinceaux de couleur unie](#solid-color-brushes).

### <a name="use-hexadecimal-notation"></a>Utiliser la notation hexadécimale

`SolidColorBrush` les objets peuvent également être créés à l’aide de la notation hexadécimale. Avec cette approche, une couleur est spécifiée en termes de la quantité de rouge, de vert et de bleu à combiner en une seule couleur. Le format principal pour la spécification d’une couleur à l’aide de la notation hexadécimale est `#rrggbb` , où :

- `rr` nombre hexadécimal à deux chiffres spécifiant la quantité relative de rouge.
- `gg` nombre hexadécimal à deux chiffres spécifiant la quantité relative de vert.
- `bb` nombre hexadécimal à deux chiffres spécifiant la quantité relative de bleu.

En outre, une couleur peut être spécifiée comme `#aarrggbb` où `aa` spécifie la valeur alpha, ou transparence, de la couleur. Cette approche vous permet de créer des couleurs qui sont partiellement transparentes.

L’exemple suivant définit la valeur de couleur d’un `SolidColorBrush` à l’aide de la notation hexadécimale :

```xaml
<Frame Background="#FF9988"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

Dans cet exemple, l’arrière-plan de [`Frame`](xref:Xamarin.Forms.Frame) est peint avec une couleur de saumon `SolidColorBrush` :

![Frame peint avec un SolidColorBrush créé à l’aide de la notation hexadécimale](solidcolor-images/hex.png)

Pour d’autres façons de décrire la couleur, consultez [couleurs dans Xamarin.Forms ](~/xamarin-forms/user-interface/colors.md).

## <a name="solid-color-brushes"></a>Pinceaux de couleur unie

Pour plus de commodité, la `Brush` classe fournit un ensemble d' `SolidColorBrush` objets couramment utilisés, tels que `AliceBlue` et `YellowGreen` . L’illustration suivante montre la couleur de chaque pinceau prédéfini, son nom et sa valeur hexadécimale :

[![Table de couleurs incluant un échantillon de couleur, un nom de couleur et une valeur hexadécimale](solidcolor-images/solidcolorbrushes.png)](solidcolor-images/solidcolorbrushes-large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [BrushesDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Couleurs dans Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)
