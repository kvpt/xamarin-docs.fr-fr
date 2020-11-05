---
title: Ajout de données à la collection d’éléments d’un sélecteur
description: La vue sélecteur est un contrôle permettant de sélectionner un élément de texte dans une liste de données. Cet article explique comment remplir un sélecteur de données en l’ajoutant à la collection Items et comment répondre à la sélection d’éléments par l’utilisateur.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8904f3392e935b3d8aa08d87d208bdb46ac37b30
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368048"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Ajout de données à la collection d’éléments d’un sélecteur

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_La vue sélecteur est un contrôle permettant de sélectionner un élément de texte dans une liste de données. Cet article explique comment remplir un sélecteur de données en l’ajoutant à la collection Items et comment répondre à la sélection d’éléments par l’utilisateur._

## <a name="populating-a-picker-with-data"></a>Remplissage d’un sélecteur avec des données

Avant Xamarin.Forms la série 2.3.4, le processus de remplissage d’un [`Picker`](xref:Xamarin.Forms.Picker) avec des données consistait à ajouter les données à afficher à la collection en lecture seule [`Items`](xref:Xamarin.Forms.Picker.Items) , qui est de type `IList<string>` . Chaque élément de la collection doit être de type `string` . Les éléments peuvent être ajoutés en XAML en initialisant la `Items` propriété avec une liste d' `x:String` éléments :

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

Le code C# équivalent est illustré ci-dessous :

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

En plus d’ajouter des données à l’aide de la `Items.Add` méthode, les données peuvent également être insérées dans la collection à l’aide de la `Items.Insert` méthode.

## <a name="responding-to-item-selection"></a>Réponse à la sélection d’élément

[`Picker`](xref:Xamarin.Forms.Picker)Prend en charge la sélection d’un élément à la fois. Lorsqu’un utilisateur sélectionne un élément, l' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement se déclenche et la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriété est mise à jour avec un entier représentant l’index de l’élément sélectionné dans la liste. La `SelectedIndex` propriété est un nombre de base zéro indiquant l’élément que l’utilisateur a sélectionné. Si aucun élément n’est sélectionné, ce qui est le cas lorsque `Picker` est créé et initialisé pour la première fois, est `SelectedIndex` -1.

> [!NOTE]
> Le comportement de sélection d’éléments dans un [`Picker`](xref:Xamarin.Forms.Picker) peut être personnalisé sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez contrôle de la [sélection des éléments du sélecteur](~/xamarin-forms/platform/ios/picker-selection.md).

L’exemple de code suivant montre la `OnPickerSelectedIndexChanged` méthode du gestionnaire d’événements, qui est exécutée lorsque l' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement se déclenche :

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

Cette méthode obtient la valeur de la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriété et utilise la valeur pour récupérer l’élément sélectionné dans la [`Items`](xref:Xamarin.Forms.Picker.Items) collection. Étant donné que chaque élément de la `Items` collection est un `string` , il peut être affiché par un [`Label`](xref:Xamarin.Forms.Label) sans nécessiter un cast.

> [!NOTE]
> Un [`Picker`](xref:Xamarin.Forms.Picker) peut être initialisé pour afficher un élément spécifique en définissant la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriété. Toutefois, la `SelectedIndex` propriété doit être définie après l’initialisation de la [`Items`](xref:Xamarin.Forms.Picker.Items) collection.

## <a name="related-links"></a>Liens connexes

- [Démonstration du sélecteur (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Sélecteur](xref:Xamarin.Forms.Picker)