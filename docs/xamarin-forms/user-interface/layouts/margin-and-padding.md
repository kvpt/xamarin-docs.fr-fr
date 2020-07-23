---
title: Marge et remplissage
description: Les propriétés de marge et de marge intérieure contrôlent le comportement de disposition lorsqu’un élément est rendu dans l’interface utilisateur. Cet article explique la différence entre les deux propriétés et comment les définir.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b8ba915f3bc701b6a100d206dc791f9572fa594b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936615"
---
# <a name="margin-and-padding"></a>Marge et remplissage

_Les propriétés de marge et de marge intérieure contrôlent le comportement de disposition lorsqu’un élément est rendu dans l’interface utilisateur. Cet article explique la différence entre les deux propriétés et comment les définir._

## <a name="overview"></a>Vue d’ensemble

Les marges et le remplissage sont des concepts de disposition associés :

- La [`Margin`](xref:Xamarin.Forms.View.Margin) propriété représente la distance entre un élément et ses éléments adjacents, et est utilisée pour contrôler la position de rendu de l’élément et la position de rendu de ses voisins. `Margin`les valeurs peuvent être spécifiées dans les classes [Layout](~/xamarin-forms/user-interface/controls/layouts.md) et [View](~/xamarin-forms/user-interface/controls/views.md) .
- La [`Padding`](xref:Xamarin.Forms.Layout.Padding) propriété représente la distance entre un élément et ses éléments enfants, et est utilisée pour séparer le contrôle de son propre contenu. `Padding`les valeurs peuvent être spécifiées dans les classes de [disposition](~/xamarin-forms/user-interface/controls/layouts.md) .

Le diagramme suivant illustre les deux concepts :

[![Marges et concepts de remplissage](margin-and-padding-images/margins-and-padding-sml.png)](margin-and-padding-images/margins-and-padding.png#lightbox "Marges et concepts de remplissage")

Notez que [`Margin`](xref:Xamarin.Forms.View.Margin) les valeurs sont additives. Par conséquent, si deux éléments adjacents spécifient une marge de 20 pixels, la distance entre les éléments sera de 40 pixels. En outre, la marge et le remplissage sont additifs quand les deux sont appliqués, en ce sens que la distance entre un élément et tout contenu est la marge plus le remplissage.

## <a name="specifying-a-thickness"></a>Spécification d’une épaisseur

Les [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Layout.Padding) Propriétés et sont toutes deux de type [`Thickness`](xref:Xamarin.Forms.Thickness) . Il existe trois possibilités pour créer une `Thickness` structure :

- Crée une [`Thickness`](xref:Xamarin.Forms.Thickness) structure définie par une valeur uniforme unique. La valeur unique est appliquée aux côtés gauche, supérieur, droit et inférieur de l’élément.
- Crée une [`Thickness`](xref:Xamarin.Forms.Thickness) structure définie par des valeurs horizontales et verticales. La valeur horizontale est appliquée symétriquement aux côtés gauche et droit de l’élément, la valeur verticale étant appliquée symétriquement aux côtés supérieur et inférieur de l’élément.
- Crée une [`Thickness`](xref:Xamarin.Forms.Thickness) structure définie par quatre valeurs distinctes qui sont appliquées aux côtés gauche, supérieur, droit et inférieur de l’élément.

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
> `Thickness`les valeurs peuvent être négatives, ce qui en général découpe ou dessine le contenu.

## <a name="summary"></a>Résumé

Cet article a montré la différence entre [`Margin`](xref:Xamarin.Forms.View.Margin) les [`Padding`](xref:Xamarin.Forms.Layout.Padding) Propriétés et et comment les définir. Les propriétés contrôlent le comportement de disposition lorsqu’un élément est affiché dans l’interface utilisateur.

## <a name="related-links"></a>Liens associés

- [Margin](xref:Xamarin.Forms.View.Margin)
- [Remplissage](xref:Xamarin.Forms.Layout.Padding)
- [Thickness](xref:Xamarin.Forms.Thickness)
