---
title: Options de disposition dansXamarin.Forms
description: Chaque Xamarin.Forms vue a des propriétés HorizontalOptions et VerticalOptions, de type LayoutOptions. Cet article explique l’effet de chaque valeur LayoutOptions sur l’alignement et l’expansion d’une vue.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6fa03cf5c18e21ce5ca9e7ea907f50c8de6f3e6c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573259"
---
# <a name="layout-options-in-xamarinforms"></a>Options de disposition dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_Chaque Xamarin.Forms vue a des propriétés HorizontalOptions et VerticalOptions, de type LayoutOptions. Cet article explique l’effet de chaque valeur LayoutOptions sur l’alignement et l’expansion d’une vue._

## <a name="overview"></a>Vue d’ensemble

La [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) structure encapsule deux préférences de disposition :

- **Alignment** : alignement préféré de la vue, qui détermine sa position et sa taille dans sa disposition parente.
- **Expansion** : utilisée uniquement par un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , et indique si la vue doit utiliser de l’espace supplémentaire, si elle est disponible.

Ces préférences de disposition peuvent être appliquées à un [`View`](xref:Xamarin.Forms.View) , par rapport à son parent, en affectant [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) à la propriété ou [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de l' `View` un des champs publics de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) structure. Les champs publics sont les suivants :

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

Les `Start` champs,, `Center` `End` et `Fill` sont utilisés pour définir l’alignement de la vue dans la disposition parente :

- Pour l’alignement horizontal, [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) positionne le [`View`](xref:Xamarin.Forms.View) sur le côté gauche de la disposition parente et, pour l’alignement vertical, il positionne le `View` en haut de la disposition parente.
- Pour l’alignement horizontal et vertical, [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) Centre horizontalement ou verticalement [`View`](xref:Xamarin.Forms.View) .
- Pour l’alignement horizontal, [`End`](xref:Xamarin.Forms.LayoutOptions.End) positionne le [`View`](xref:Xamarin.Forms.View) sur le côté droit de la disposition parente et, pour l’alignement vertical, il positionne le `View` en bas de la disposition parente.
- Pour l’alignement horizontal, [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) garantit que le [`View`](xref:Xamarin.Forms.View) remplit la largeur de la disposition parente et, pour l’alignement vertical, il garantit que le `View` remplit la hauteur de la disposition parente.

Les `StartAndExpand` valeurs,, `CenterAndExpand` `EndAndExpand` et `FillAndExpand` sont utilisées pour définir la préférence d’alignement, et si la vue occupera plus d’espace si elle est disponible dans le parent [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> La valeur par défaut des propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) d’une vue est [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

## <a name="alignment"></a>Alignement

L’alignement contrôle la manière dont une vue est positionnée dans sa disposition parente lorsque la disposition parente contient de l’espace inutilisé (autrement dit, la disposition parente est supérieure à la taille combinée de tous ses enfants).

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) respecte uniquement les `Start` `Center` champs,, `End` et `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) sur les vues enfants qui se trouvent dans la direction opposée à l' `StackLayout` orientation. Par conséquent, les vues enfants d’un orienté verticale `StackLayout` peuvent définir leurs [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés sur l’un `Start` des `Center` champs,, `End` ou `Fill` . De même, les vues enfants d’un orienté horizontal `StackLayout` peuvent définir leurs [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés sur l’un des `Start` `Center` champs,, `End` ou `Fill` .

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) ne respecte pas les `Start` `Center` champs,, `End` et `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) sur les vues enfants qui se trouvent dans la même direction que l' `StackLayout` orientation. Par conséquent, un orienté verticale `StackLayout` ignore les `Start` `Center` champs,, `End` ou `Fill` s’ils sont définis sur les [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés des vues enfants. De même, un orienté horizontal `StackLayout` ignore les `Start` champs, `Center` , `End` ou `Fill` s’ils sont définis sur les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés des vues enfants.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)remplace généralement les demandes de taille spécifiées à l’aide des [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriétés et.

L’exemple de code XAML suivant montre une orientation verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) où chaque enfant [`Label`](xref:Xamarin.Forms.Label) définit sa [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propriété sur l’un des quatre champs d’alignement de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) structure :

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

Le code C# équivalent est illustré ci-dessous :

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

Le code donne la mise en page illustrée dans les captures d’écran suivantes :

[![](layout-options-images/alignment.png "Alignment Layout Options")](layout-options-images/alignment-large.png#lightbox "Alignment Layout Options")

## <a name="expansion"></a>Expansion

L’expansion contrôle si une vue occupera plus d’espace, si elle est disponible, dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Si le `StackLayout` contient un espace inutilisé (c’est-à-dire qu’il `StackLayout` est plus grand que la taille combinée de tous ses enfants), l’espace inutilisé est partagé de manière égale par toutes les vues enfants qui demandent une expansion en affectant [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) à leurs propriétés ou un [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) champ qui utilise le `AndExpand` suffixe. Notez que lorsque tout l’espace du `StackLayout` est utilisé, les options d’expansion n’ont aucun effet.

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) peut uniquement développer les vues enfants dans le sens de son orientation. Par conséquent, une orientation verticale `StackLayout` peut développer des vues enfants qui définissent leurs [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés sur l’un des `StartAndExpand` `CenterAndExpand` champs,, `EndAndExpand` ou `FillAndExpand` , si le contient de l' `StackLayout` espace inutilisé. De même, un orienté horizontal `StackLayout` peut développer des vues enfants qui définissent leurs [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés sur l’un des `StartAndExpand` `CenterAndExpand` champs,, `EndAndExpand` ou `FillAndExpand` , si le contient de l' `StackLayout` espace inutilisé.

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) ne peut pas développer les vues enfants dans la direction opposée à son orientation. Par conséquent, sur une orientation verticale `StackLayout` , l’affectation de la valeur à la [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propriété sur une vue enfant [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) a le même effet que l’affectation de la valeur à la propriété [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) .

> [!NOTE]
> Notez que l’activation de l’expansion ne modifie pas la taille d’une vue, sauf si elle utilise [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) .

L’exemple de code XAML suivant montre une orientation verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) où chaque enfant [`Label`](xref:Xamarin.Forms.Label) définit sa [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriété sur l’un des quatre champs d’expansion de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) structure :

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

Le code C# équivalent est illustré ci-dessous :

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

Le code donne la mise en page illustrée dans les captures d’écran suivantes :

[![](layout-options-images/expansion.png "Expansion Layout Options")](layout-options-images/expansion-large.png#lightbox "Expansion Layout Options")

Chaque [`Label`](xref:Xamarin.Forms.Label) occupe la même quantité d’espace dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) . En revanche, seule la dernière instance `Label`, qui définit sa propriété [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) a une taille différente. En outre, chaque `Label` est séparé par un petit rouge [`BoxView`](xref:Xamarin.Forms.BoxView) , ce qui permet `Label` d’afficher facilement l’espace occupé par le.

## <a name="summary"></a>Résumé

Cet article a expliqué l’effet de chaque [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) valeur de structure sur l’alignement et l’expansion d’une vue, par rapport à son parent. Les `Start` champs,, `Center` `End` et `Fill` sont utilisés pour définir l’alignement de la vue dans la disposition parente, et `StartAndExpand` les `CenterAndExpand` champs,, `EndAndExpand` et `FillAndExpand` sont utilisés pour définir la préférence d’alignement et pour déterminer si la vue occupera plus d’espace, si elle est disponible, dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

## <a name="related-links"></a>Liens connexes

- [LayoutOptions (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
