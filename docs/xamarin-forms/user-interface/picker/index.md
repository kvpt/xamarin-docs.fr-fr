---
title: Sélecteur Xamarin. Forms
description: Le sélecteur Xamarin. Forms affiche une liste succincte d’éléments, à partir desquels l’utilisateur peut sélectionner un élément. Cet article explique comment utiliser la classe Picker pour sélectionner un élément de texte dans une liste de données.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: c8246f8316a2a579bc890935dc4f9beecccb8dcc
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696002"
---
# <a name="xamarinforms-picker"></a>Sélecteur Xamarin. Forms

_La vue sélecteur est un contrôle permettant de sélectionner un élément de texte dans une liste de données._

L' [`Picker`](xref:Xamarin.Forms.Picker) Xamarin. Forms affiche une brève liste d’éléments, à partir de laquelle l’utilisateur peut sélectionner un élément. `Picker` définit les propriétés suivantes :

- [`Title`](xref:Xamarin.Forms.Picker.Title) de type `string`, qui a par défaut la valeur `null`.
- `TitleColor` de type [`Color`](xref:Xamarin.Forms.Color), la couleur utilisée pour afficher le texte de `Title`.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) de type `IList`, liste source des éléments à afficher, dont la valeur par défaut est `null`.
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) de type `int`, index de l’élément sélectionné, qui a comme valeur par défaut-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) de type `object`, l’élément sélectionné, qui a par défaut la valeur `null`.
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) de type [`Color`](xref:Xamarin.Forms.Color), la couleur utilisée pour afficher le texte, qui a comme valeur par défaut [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), qui a par défaut la valeur [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) de type `string`, qui a par défaut la valeur `null`.
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) de type `double`, dont la valeur par défaut est-1,0.
- `CharacterSpacing`, de type `double`, est l’espacement entre les caractères de l’élément affiché par le `Picker`.

Toutes les propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’ils peuvent être stylisés, et les propriétés peuvent être des cibles de liaisons de données. Les propriétés [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) et [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) ont un mode de liaison par défaut [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), ce qui signifie qu’elles peuvent être des cibles de liaisons de données dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

Un [`Picker`](xref:Xamarin.Forms.Picker) n’affiche aucune donnée lorsqu’il est affiché pour la première fois. Au lieu de cela, la valeur de sa propriété [`Title`](xref:Xamarin.Forms.Picker.Title) est affichée sous la forme d’un espace réservé sur les plateformes iOS et Android :

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

Lorsque l' [`Picker`](xref:Xamarin.Forms.Picker) obtient le focus, ses données sont affichées et l’utilisateur peut sélectionner un élément :

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

Le [`Picker`](xref:Xamarin.Forms.Picker) déclenche un événement [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) lorsque l’utilisateur sélectionne un élément. Après la sélection, l’élément sélectionné est affiché par l' `Picker` :

![](images/picker-after-selection.png "Picker after Selection")

Il existe deux techniques pour remplir un [`Picker`](xref:Xamarin.Forms.Picker) avec des données :

- Définition de la propriété [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) sur les données à afficher. Il s'agit de la technique recommandée. Pour plus d’informations, consultez [définition de la propriété ItemsSource d’un sélecteur](populating-itemssource.md).
- Ajout des données à afficher dans la collection [`Items`](xref:Xamarin.Forms.Picker.Items) . Cette technique était le processus d’origine pour le remplissage d’un [`Picker`](xref:Xamarin.Forms.Picker) avec des données. Pour plus d’informations, consultez [Ajout de données à la collection d’éléments d’un sélecteur](populating-items.md).

## <a name="related-links"></a>Liens connexes

- [Sélecteur](xref:Xamarin.Forms.Picker)
