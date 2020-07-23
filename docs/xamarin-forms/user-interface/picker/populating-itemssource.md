---
title: Définition de la propriété ItemsSource d’un sélecteur
description: La vue sélecteur est un contrôle permettant de sélectionner un élément de texte dans une liste de données. Cet article explique comment remplir un sélecteur avec des données en définissant la propriété ItemsSource et comment répondre à la sélection d’élément par l’utilisateur.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a15ca2e11dabb73054f5f0a1dc3f79342d8ce7f5
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938604"
---
# <a name="setting-a-pickers-itemssource-property"></a>Définition de la propriété ItemsSource d’un sélecteur

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_La vue sélecteur est un contrôle permettant de sélectionner un élément de texte dans une liste de données. Cet article explique comment remplir un sélecteur avec des données en définissant la propriété ItemsSource et comment répondre à la sélection d’élément par l’utilisateur._

Xamarin.Forms2.3.4 a amélioré la [`Picker`](xref:Xamarin.Forms.Picker) vue en ajoutant la possibilité de la remplir avec des données en définissant sa [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriété, et de récupérer l’élément sélectionné à partir de la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriété. En outre, la couleur du texte de l’élément sélectionné peut être modifiée en affectant [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) à la propriété [`Color`](xref:Xamarin.Forms.Color) .

## <a name="populating-a-picker-with-data"></a>Remplissage d’un sélecteur avec des données

Une [`Picker`](xref:Xamarin.Forms.Picker) peut être remplie avec des données en définissant sa [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriété sur une `IList` collection. Chaque élément de la collection doit être de type ou dérivé de celui-ci `object` . Les éléments peuvent être ajoutés en XAML en initialisant la `ItemsSource` propriété à partir d’un tableau d’éléments :

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> Notez que l’élément `x:Array` nécessite un attribut `Type` qui indique le type des éléments du tableau.

Le code C# équivalent est illustré ci-dessous :

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Réponse à la sélection d’élément

[`Picker`](xref:Xamarin.Forms.Picker)Prend en charge la sélection d’un élément à la fois. Lorsqu’un utilisateur sélectionne un élément, l' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement se déclenche, la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriété est mise à jour avec un entier représentant l’index de l’élément sélectionné dans la liste, et la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriété est mise à jour en `object` représentant l’élément sélectionné. La [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriété est un nombre de base zéro indiquant l’élément sélectionné par l’utilisateur. Si aucun élément n’est sélectionné, ce qui est le cas lorsque [`Picker`](xref:Xamarin.Forms.Picker) est créé et initialisé pour la première fois, est `SelectedIndex` -1.

> [!NOTE]
> Le comportement de sélection d’éléments dans un [`Picker`](xref:Xamarin.Forms.Picker) peut être personnalisé sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez contrôle de la [sélection des éléments du sélecteur](~/xamarin-forms/platform/ios/picker-selection.md).

L’exemple de code suivant montre comment récupérer la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valeur de la propriété [`Picker`](xref:Xamarin.Forms.Picker) dans le XAML :

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Le code C# équivalent est illustré ci-dessous :

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

En outre, un gestionnaire d’événements peut être exécuté lorsque l' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement se déclenche :

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

Cette méthode obtient la valeur de la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propriété et utilise la valeur pour récupérer l’élément sélectionné dans la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) collection. Cela équivaut d’un point de vue fonctionnel à la récupération de l’élément sélectionné à partir de la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriété. Notez que chaque élément de la `ItemsSource` collection est de type `object` , et doit donc être casté en un à `string` afficher.

> [!NOTE]
> Un [`Picker`](xref:Xamarin.Forms.Picker) peut être initialisé pour afficher un élément spécifique en définissant les [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) Propriétés ou. Toutefois, ces propriétés doivent être définies après l’initialisation de la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) collection.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Remplissage d’un sélecteur avec des données à l’aide de la liaison de données

Un [`Picker`](xref:Xamarin.Forms.Picker) peut également être rempli de données à l’aide d’une liaison de données pour lier sa [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propriété à une `IList` collection. Dans XAML, cela est accompli avec l' [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) extension de balisage :

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

Le code C# équivalent est illustré ci-dessous :

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

Les [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) données de propriété sont liées à la `Monkeys` propriété du modèle de vue connecté, qui retourne une `IList<Monkey>` collection. L’exemple de code suivant montre la `Monkey` classe, qui contient quatre propriétés :

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Lors de la liaison à une liste d’objets, [`Picker`](xref:Xamarin.Forms.Picker) doit être informé de la propriété à afficher à partir de chaque objet. Cela est possible en affectant [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) à la propriété la propriété Required de chaque objet. Dans les exemples de code ci-dessus, `Picker` est défini pour afficher chaque `Monkey.Name` valeur de propriété.

### <a name="responding-to-item-selection"></a>Réponse à la sélection d’élément

La liaison de données peut être utilisée pour définir un objet sur la valeur de la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propriété lorsqu’il est modifié :

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

Le code C# équivalent est illustré ci-dessous :

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

Les [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) données de propriété sont liées à la `SelectedMonkey` propriété du modèle de vue connectée, qui est de type `Monkey` . Par conséquent, lorsque l’utilisateur sélectionne un élément dans le [`Picker`](xref:Xamarin.Forms.Picker) , la `SelectedMonkey` propriété est définie sur l' `Monkey` objet sélectionné. Les `SelectedMonkey` données de l’objet sont affichées dans l’interface utilisateur par [`Label`](xref:Xamarin.Forms.Label) et les [`Image`](xref:Xamarin.Forms.Image) vues :

![Sélection d’élément Picker](populating-itemssource-images/monkeys.png)

> [!NOTE]
> Notez que les [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) Propriétés et prennent toutes les deux en charge les liaisons bidirectionnelles par défaut.

## <a name="related-links"></a>Liens connexes

- [Démonstration du sélecteur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Application singe (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [Sélecteur pouvant être lié (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [API du sélecteur](xref:Xamarin.Forms.Picker)
