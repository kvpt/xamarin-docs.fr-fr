---
title: Marge et remplissage
description: Les propriétés de marge et de marge intérieure contrôlent le comportement de disposition lorsqu’un élément est rendu dans l’interface utilisateur. Cet article explique la différence entre les deux propriétés et comment les définir.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 66ac81631466131cf1ef44dde39aa768d31b65a1
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70772486"
---
# <a name="margin-and-padding"></a>Marge et remplissage

_Les propriétés de marge et de marge intérieure contrôlent le comportement de disposition lorsqu’un élément est rendu dans l’interface utilisateur. Cet article explique la différence entre les deux propriétés et comment les définir._

## <a name="overview"></a>Vue d'ensemble

Les marges et le remplissage sont des concepts de disposition associés :

- La propriété [`Margin`](xref:Xamarin.Forms.View.Margin) représente la distance entre un élément et ses éléments adjacents, et est utilisée pour contrôler la position de rendu de l’élément et la position de rendu de ses voisins. `Margin` valeurs peuvent être spécifiées dans les classes [Layout](~/xamarin-forms/user-interface/controls/layouts.md) et [View](~/xamarin-forms/user-interface/controls/views.md) .
- La propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) représente la distance entre un élément et ses éléments enfants, et est utilisée pour séparer le contrôle de son propre contenu. `Padding` valeurs peuvent être spécifiées dans les classes de [disposition](~/xamarin-forms/user-interface/controls/layouts.md) .

Le diagramme suivant illustre les deux concepts :

[![](margin-and-padding-images/margins-and-padding-sml.png "Margins and Padding Concepts")](margin-and-padding-images/margins-and-padding.png#lightbox "Margins and Padding Concepts")

Notez que les valeurs de [`Margin`](xref:Xamarin.Forms.View.Margin) sont additives. Par conséquent, si deux éléments adjacents spécifient une marge de 20 pixels, la distance entre les éléments sera de 40 pixels. En outre, la marge et le remplissage sont additifs quand les deux sont appliqués, en ce sens que la distance entre un élément et tout contenu est la marge plus le remplissage.

## <a name="specifying-a-thickness"></a>Spécification d’une épaisseur

Les propriétés [`Margin`](xref:Xamarin.Forms.View.Margin) et [`Padding`](xref:Xamarin.Forms.Layout.Padding) sont toutes deux de type [`Thickness`](xref:Xamarin.Forms.Thickness). Il existe trois possibilités pour créer une structure de `Thickness` :

- Créer une structure [`Thickness`](xref:Xamarin.Forms.Thickness) définie par une valeur uniforme unique. La valeur unique est appliquée aux côtés gauche, supérieur, droit et inférieur de l’élément.
- Créer une structure [`Thickness`](xref:Xamarin.Forms.Thickness) définie par des valeurs horizontales et verticales. La valeur horizontale est appliquée symétriquement aux côtés gauche et droit de l’élément, la valeur verticale étant appliquée symétriquement aux côtés supérieur et inférieur de l’élément.
- Crée une structure [`Thickness`](xref:Xamarin.Forms.Thickness) définie par quatre valeurs distinctes appliquées aux côtés gauche, supérieur, droit et inférieur de l’élément.

L’exemple de code XAML suivant montre les trois possibilités :

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
> les valeurs `Thickness` peuvent être négatives, ce qui en général découpe ou dessine le contenu.

## <a name="summary"></a>Récapitulatif

Cet article a montré la différence entre les propriétés [`Margin`](xref:Xamarin.Forms.View.Margin) et [`Padding`](xref:Xamarin.Forms.Layout.Padding) , et comment les définir. Les propriétés contrôlent le comportement de disposition lorsqu’un élément est affiché dans l’interface utilisateur.

## <a name="related-links"></a>Liens associés

- [Définie](xref:Xamarin.Forms.View.Margin)
- [Remplissage](xref:Xamarin.Forms.Layout.Padding)
- [Épaisseur](xref:Xamarin.Forms.Thickness)
