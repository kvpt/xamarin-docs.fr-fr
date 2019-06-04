---
title: Marge et remplissage
description: La marge et marge intérieure des propriétés contrôlent le comportement de disposition quand un élément est affiché dans l’interface utilisateur. Cet article illustre la différence entre les deux propriétés et comment les définir.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61370943"
---
# <a name="margin-and-padding"></a>Marge et remplissage

_La marge et marge intérieure des propriétés contrôlent le comportement de disposition quand un élément est affiché dans l’interface utilisateur. Cet article illustre la différence entre les deux propriétés et comment les définir._

## <a name="overview"></a>Vue d'ensemble

Marge et marge intérieure sont les concepts de mise en page :

- Le [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriété représente la distance entre un élément et ses éléments adjacents et est utilisée pour contrôler la position de rendu de l’élément et la position de rendu de ses voisins. `Margin` valeurs peuvent être spécifiées sur [disposition](~/xamarin-forms/user-interface/controls/layouts.md) et [vue](~/xamarin-forms/user-interface/controls/views.md) classes.
- Le [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriété représente la distance entre un élément et ses éléments enfants et est utilisée pour séparer le contrôle à partir de son propre contenu. `Padding` valeurs peuvent être spécifiées sur [disposition](~/xamarin-forms/user-interface/controls/layouts.md) classes.

Le diagramme suivant illustre les deux concepts :

[![](margin-and-padding-images/margins-and-padding-sml.png "Marges et remplissage Concepts")](margin-and-padding-images/margins-and-padding.png#lightbox "marges et remplissage Concepts")

Notez que [ `Margin` ](xref:Xamarin.Forms.View.Margin) les valeurs sont additifs. Par conséquent, si deux éléments adjacents spécifient une marge de 20 pixels, la distance entre les éléments sera 40 pixels. En outre, les marges et marges intérieures sont additifs lorsque les deux sont appliquées, dans la mesure la distance entre un élément et tout son contenu sera la marge plus la marge intérieure.

## <a name="specifying-a-thickness"></a>Qui spécifie une épaisseur

Le [ `Margin` ](xref:Xamarin.Forms.View.Margin) et [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriétés sont tous deux de type [ `Thickness` ](xref:Xamarin.Forms.Thickness). Trois options sont possibles lorsque vous créez un `Thickness` structure :

- Créer une structure [ `Thickness` ](xref:Xamarin.Forms.Thickness) définie par une seule valeur uniforme. La valeur unique est appliquée aux côtés gauche, haut, droite et bas de l’élément.
- Créer une structure [ `Thickness` ](xref:Xamarin.Forms.Thickness) définie par les valeurs horizontales et verticales. La valeur horizontale est appliquée symétriquement pour les côtés gauche et droit de l’élément, avec la valeur verticale qui est appliquée symétriquement sur les côtés supérieur et inférieur de l’élément.
- Créer une structure [ `Thickness` ](xref:Xamarin.Forms.Thickness) définie par quatre valeurs distinctes qui sont appliquées aux côtés gauche, haut, droit et bas de l’élément.

L’exemple de code XAML suivant montre toutes les trois possibilités :

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` valeurs peuvent être négatifs, ce qui fait coïncider généralement ou que vous dépasse le contenu.

## <a name="summary"></a>Récapitulatif

Cet article ne présente la différence entre la [ `Margin` ](xref:Xamarin.Forms.View.Margin) et [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriétés et comment les définir. Les propriétés contrôlent le comportement de disposition quand un élément est affiché dans l’interface utilisateur.


## <a name="related-links"></a>Liens associés

- [Margin](xref:Xamarin.Forms.View.Margin)
- [Padding](xref:Xamarin.Forms.Layout.Padding)
- [Épaisseur](xref:Xamarin.Forms.Thickness)
