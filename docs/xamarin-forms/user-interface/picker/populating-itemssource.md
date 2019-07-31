---
title: Définition de propriété de ItemsSource d’un sélecteur
description: La vue de sélecteur est un contrôle pour la sélection d’un élément de texte à partir d’une liste de données. Cet article explique comment remplir un sélecteur de données en définissant la propriété ItemsSource et comment répondre à la sélection d’éléments par l’utilisateur.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 803cd1babc32e3a42d957c4bac0cc93c4552fb8c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656120"
---
# <a name="setting-a-pickers-itemssource-property"></a>Définition de propriété de ItemsSource d’un sélecteur

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_La vue de sélecteur est un contrôle pour la sélection d’un élément de texte à partir d’une liste de données. Cet article explique comment remplir un sélecteur de données en définissant la propriété ItemsSource et comment répondre à la sélection d’éléments par l’utilisateur._

Xamarin.Forms 2.3.4 a amélioré la [ `Picker` ](xref:Xamarin.Forms.Picker) vue en ajoutant la possibilité de le remplir avec des données en définissant son [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriété et pour récupérer l’élément sélectionné à partir de la [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriété. En outre, la couleur du texte pour l’élément sélectionné peut être modifiée en définissant le [ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor) propriété à un [ `Color` ](xref:Xamarin.Forms.Color).

## <a name="populating-a-picker-with-data"></a>Remplissage d’un sélecteur avec des données

Un [ `Picker` ](xref:Xamarin.Forms.Picker) peut être rempli de données en définissant son [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriété un `IList` collection. Chaque élément dans la collection doit être d’ou dérivé, tapez `object`. Éléments peuvent être ajoutés dans XAML en initialisant le `ItemsSource` propriété à partir d’un tableau d’éléments :

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
> Notez que le `x:Array` élément requiert une `Type` attribut indiquant le type des éléments dans le tableau.

Le code C# équivalent est indiqué ci-dessous :

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

Un [ `Picker` ](xref:Xamarin.Forms.Picker) prend en charge la sélection d’un élément à la fois. Lorsqu’un utilisateur sélectionne un élément, le [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement est déclenché, le [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriété est mise à jour vers un entier représentant l’index de l’élément sélectionné dans la liste et le [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriété est mis à jour vers la `object` représentant l’élément sélectionné. Le [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propriété est un numéro de base zéro indiquant l’élément de l’utilisateur a sélectionné. Si aucun élément n’est sélectionné, ce qui est le cas lorsque le [ `Picker` ](xref:Xamarin.Forms.Picker) est tout d’abord créé et initialisé, `SelectedIndex` sera -1.

> [!NOTE]
> Élément de comportement de sélection dans un [ `Picker` ](xref:Xamarin.Forms.Picker) peuvent être personnalisés sur iOS avec un spécifique à la plateforme. Pour plus d’informations, consultez [sélection d’éléments de contrôle de sélecteur de](~/xamarin-forms/platform/ios/picker-selection.md).

L’exemple de code suivant montre comment récupérer le [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) valeur de propriété à partir de la [ `Picker` ](xref:Xamarin.Forms.Picker) dans XAML :

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

Le code C# équivalent est indiqué ci-dessous :

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

En outre, un gestionnaire d’événements peut être exécutée lorsque la [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) se déclenche des événements :

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

Cette méthode obtient le [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valeur de propriété et utilise la valeur pour récupérer l’élément sélectionné à partir de la [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) collection. Ceci est fonctionnellement équivalent à la récupération de l’élément sélectionné à partir de la [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriété. Notez que chaque élément dans le `ItemsSource` collection est de type `object`et par conséquent, doivent être converties en un `string` pour l’affichage.

> [!NOTE]
> Un [ `Picker` ](xref:Xamarin.Forms.Picker) puisse être initialisé pour afficher un élément spécifique en définissant le [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) ou [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) propriétés. Toutefois, ces propriétés doivent être définies après l’initialisation de la [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) collection.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Remplissage d’un sélecteur avec des données à l’aide de la liaison de données

Un [ `Picker` ](xref:Xamarin.Forms.Picker) peut être également rempli de données à l’aide de la liaison de données à lier son [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) propriété un `IList` collection. Dans XAML, cela est possible avec la [ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension) extension de balisage :

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

Le code C# équivalent est indiqué ci-dessous :

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

Le [ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource) lie les données de propriété pour le `Monkeys` propriété du modèle de vue connectée, qui retourne un `IList<Monkey>` collection. Le code suivant montre l’exemple le `Monkey` (classe), qui contient les quatre propriétés :

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Lors de la liaison à une liste d’objets, le [ `Picker` ](xref:Xamarin.Forms.Picker) doit être informé de la propriété à afficher à partir de chaque objet. Cela s’effectue en définissant le [ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding) propriété à la propriété requise à partir de chaque objet. Dans les exemples de code ci-dessus, le `Picker` est configuré pour afficher chaque `Monkey.Name` valeur de propriété.

### <a name="responding-to-item-selection"></a>Réponse à la sélection d’élément

Liaison de données peut être utilisée pour définir un objet la [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) la valeur de propriété lorsqu’il est modifié :

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

Le code C# équivalent est indiqué ci-dessous :

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

Le [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) lie les données de propriété pour le `SelectedMonkey` propriété du modèle de vue connectée, ce qui est de type `Monkey`. Par conséquent, lorsque l’utilisateur sélectionne un élément dans le [ `Picker` ](xref:Xamarin.Forms.Picker), le `SelectedMonkey` propriété sera définie au `Monkey` objet. Le `SelectedMonkey` données d’objet s’affiche dans l’interface utilisateur par [ `Label` ](xref:Xamarin.Forms.Label) et [ `Image` ](xref:Xamarin.Forms.Image) vues :

![](populating-itemssource-images/monkeys.png "Sélection d’éléments de sélecteur")

> [!NOTE]
> Notez que le [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem) et [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) deux propriétés prennent en charge des liaisons bidirectionnelles par défaut.

## <a name="related-links"></a>Liens connexes

- [Démonstration de sélecteur (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Monkey application (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [Sélecteur peut être lié (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [API du sélecteur](xref:Xamarin.Forms.Picker)
