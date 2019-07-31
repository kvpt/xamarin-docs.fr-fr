---
title: Options de disposition dans Xamarin.Forms
description: Chaque vue Xamarin.Forms a les propriétés HorizontalOptions et VerticalOptions, de type LayoutOptions. Cet article explique l’effet de chaque valeur LayoutOptions sur l’alignement et l’expansion d’une vue.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 2e4fa5f1fb96077b0237dbeac9074006e761bc09
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655302"
---
# <a name="layout-options-in-xamarinforms"></a>Options de disposition dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_Chaque vue Xamarin.Forms a les propriétés HorizontalOptions et VerticalOptions, de type LayoutOptions. Cet article explique l’effet de chaque valeur LayoutOptions sur l’alignement et l’expansion d’une vue._

## <a name="overview"></a>Vue d'ensemble

Le [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) structure encapsule les préférences de mise en page deux :

- **Alignement** – la vue préférée de l’alignement, qui détermine sa position et la taille au sein de sa disposition du parent.
- **Expansion** : utilisés uniquement par un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)et indique si la vue doit utiliser un espace supplémentaire, s’il est disponible.

Ces préférences de mise en page peuvent être appliquées à un [ `View` ](xref:Xamarin.Forms.View), par rapport à son parent, en définissant le [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) ou [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriété de la `View` à un des champs publics à partir de la [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) structure. Les champs publics sont les suivantes :

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

Le `Start`, `Center`, `End`, et `Fill` champs sont utilisés pour définir l’alignement de la vue dans la disposition du parent :

- Pour l’alignement horizontal, [ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start) positions le [ `View` ](xref:Xamarin.Forms.View) sur le côté gauche de la disposition du parent et pour l’alignement vertical, il positionne le `View` en haut de la disposition du parent.
- Pour l’alignement horizontal et vertical, [ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center) centres de horizontalement ou verticalement le [ `View` ](xref:Xamarin.Forms.View).
- Pour l’alignement horizontal, [ `End` ](xref:Xamarin.Forms.LayoutOptions.End) positions le [ `View` ](xref:Xamarin.Forms.View) sur le côté droit de la disposition du parent et pour l’alignement vertical, il positionne le `View` en bas de la disposition du parent.
- Pour l’alignement horizontal, [ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill) garantit que le [ `View` ](xref:Xamarin.Forms.View) remplit la largeur de la disposition du parent et pour l’alignement vertical, il garantit que le `View` remplit le hauteur de la disposition du parent.

Le `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, et `FillAndExpand` valeurs sont utilisées pour définir la préférence d’alignement, et indique si la vue occupent davantage d’espace s’il est disponible dans le parent [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> La valeur par défaut de l’affichage [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) et [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriétés est [ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill).

<a name="alignment" />

## <a name="alignment"></a>Alignement

Alignement contrôle la façon dont une vue est positionnée dans sa présentation parent lorsque la mise en page parent contient l’espace inutilisé (autrement dit, la disposition du parent est supérieure à la taille combinée de tous ses enfants).

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) prend uniquement en compte la `Start`, `Center`, `End`, et `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) champs sur les vues enfants qui se trouvent dans la direction opposée pour le `StackLayout` orientation. Par conséquent, des vues enfant au sein d’un orienté verticalement `StackLayout` peuvent définir leurs [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) à une des propriétés de la `Start`, `Center`, `End`, ou `Fill` champs. De même, des vues enfant au sein d’un orienté horizontalement `StackLayout` pouvez définir leurs [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) à une des propriétés de la `Start`, `Center`, `End`, ou `Fill` champs.

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) ne respecte pas la `Start`, `Center`, `End`, et `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) champs sur les vues enfants qui se trouvent dans la même direction que le `StackLayout` orientation. Par conséquent, un orienté verticalement `StackLayout` ignore le `Start`, `Center`, `End`, ou `Fill` champs s’ils sont définis sur le [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriétés de vues enfants. De même, un orienté horizontalement `StackLayout` ignore le `Start`, `Center`, `End`, ou `Fill` champs s’ils sont définis sur le [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriétés de vues enfants.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) généralement remplacements de taille spécifiés à l’aide de demandes le [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) et [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) propriétés.

L’exemple de code XAML suivant montre un orienté verticalement [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) où chaque enfant [ `Label` ](xref:Xamarin.Forms.Label) définit son [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriété à un des champs à partir de quatre alignement le [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) structure :

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

Le code c# équivalent est indiqué ci-dessous :

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

Le code génère la disposition indiquée dans les captures d’écran suivante :

[![](layout-options-images/alignment.png "Options de disposition alignement")](layout-options-images/alignment-large.png#lightbox "Options de mise en page d’alignement")

<a name="expansion" />

## <a name="expansion"></a>Expansion

Contrôle l’expansion si une vue occupe davantage d’espace, s’il est disponible, au sein d’un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Si le `StackLayout` contient l’espace inutilisé (autrement dit, le `StackLayout` est supérieure à la taille combinée de tous ses enfants), l’espace inutilisé est équitablement partagé par toutes les vues enfants qui demande d’extension en définissant leurs [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)ou [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriétés à un [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) champ qui utilise le `AndExpand` suffixe. Notez que lorsque tout l’espace dans le `StackLayout` est utilisé, les options d’expansion n’ont aucun effet.

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) pouvez uniquement développer les vues enfants dans la direction de son orientation. Par conséquent, un orienté verticalement `StackLayout` pouvez développer les vues enfants que définir leurs [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) à une des propriétés de la `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, ou `FillAndExpand` champs, si le `StackLayout` contient l’espace inutilisé. De même, un orienté horizontalement `StackLayout` pouvez développer les vues enfants que définir leurs [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) à une des propriétés de la `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, ou `FillAndExpand` champs, si le `StackLayout` contient l’espace inutilisé.

Un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) Impossible de développer les vues enfants dans le sens inverse son orientation. Par conséquent, sur orienté verticalement `StackLayout`, ce qui affecte le [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriété sur une vue enfant à [ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) a le même effet que la propriété [ `Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> Notez que l’activation d’extension ne change pas la taille d’une vue, sauf si elle utilise [ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand).

L’exemple de code XAML suivant montre un orienté verticalement [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) où chaque enfant [ `Label` ](xref:Xamarin.Forms.Label) définit son [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriété à un des champs d’extension de quatre à partir de la [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) structure :

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

Le code c# équivalent est indiqué ci-dessous :

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

Le code génère la disposition indiquée dans les captures d’écran suivante :

[![](layout-options-images/expansion.png "Options de mise en page d’extension")](layout-options-images/expansion-large.png#lightbox "Options de mise en page d’extension")

Chaque [ `Label` ](xref:Xamarin.Forms.Label) occupe la même quantité d’espace dans le [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Toutefois, uniquement la dernière `Label`, qui définit son [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriété [ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) a une taille différente. En outre, chaque `Label` est séparé par un petit que [ `BoxView` ](xref:Xamarin.Forms.BoxView), ce qui permet de l’espace le `Label` occupe pour être affichés facilement.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué l’effet que chaque [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) valeur de la structure a sur l’alignement et l’expansion d’une vue, par rapport à son parent. Le `Start`, `Center`, `End`, et `Fill` champs sont utilisés pour définir l’alignement de la vue dans la disposition du parent et le `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, et `FillAndExpand` champs servent à définir la préférence d’alignement et pour déterminer si la vue occupe davantage d’espace, s’il est disponible, au sein d’un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).



## <a name="related-links"></a>Liens associés

- [LayoutOptions (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
