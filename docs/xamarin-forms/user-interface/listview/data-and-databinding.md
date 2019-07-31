---
title: Sources de données de ListView
description: Cet article explique comment remplir le ListView Xamarin.Forms avec des données et comment utiliser la liaison de données avec un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2018
ms.openlocfilehash: 9855255464b32b99d78d7a1cdb24acce22d01648
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654756"
---
# <a name="listview-data-sources"></a>Sources de données de ListView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Un [ `ListView` ](xref:Xamarin.Forms.ListView) est utilisé pour afficher des listes de données. Nous apprendrons sur l’alimentation d’un ListView avec des données et comment nous pouvons lier à l’élément sélectionné.

## <a name="itemssource"></a>ItemsSource

Un [ `ListView` ](xref:Xamarin.Forms.ListView) est rempli avec les données à l’aide du [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété, qui peut accepter toute collection implémentant `IEnumerable`. La façon la plus simple pour remplir un `ListView` implique l’utilisation d’un tableau de chaînes :

```xaml
<ListView>
      <ListView.ItemsSource>
          <x:Array Type="{x:Type x:String}">
            <x:String>mono</x:String>
            <x:String>monodroid</x:String>
            <x:String>monotouch</x:String>
            <x:String>monorail</x:String>
            <x:String>monodevelop</x:String>
            <x:String>monotone</x:String>
            <x:String>monopoly</x:String>
            <x:String>monomodal</x:String>
            <x:String>mononucleosis</x:String>
          </x:Array>
      </ListView.ItemsSource>
</ListView>
```

Le code C# équivalent est :

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]
{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};
```

![](data-and-databinding-images/itemssource-simple.png "Liste d’affichage ListView de chaînes")

L’approche ci-dessus remplira la `ListView` avec une liste de chaînes. Par défaut, `ListView` appellera `ToString` et afficher le résultat dans un `TextCell` pour chaque ligne. Pour personnaliser l’affichage des données, consultez [apparence de la cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Étant donné que `ItemsSource` a été envoyé à un tableau, le contenu ne met pas à jour en tant que les modifications de liste ou un tableau sous-jacent. Si vous souhaitez le ListView à mettre à jour automatiquement comme éléments sont ajoutées, supprimées et modifiées dans la liste sous-jacente, vous devez utiliser un `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) est défini dans `System.Collections.ObjectModel` et est comparable `List`, sauf qu’il peut informer `ListView` de toutes les modifications :

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>Liaison de données
Liaison de données est « glue » qui lie les propriétés d’un objet d’interface utilisateur pour les propriétés d’un objet CLR, tel qu’une classe dans votre ViewModel. Liaison de données est utile, car elle simplifie le développement d’interfaces utilisateur en remplaçant un grand nombre de code fastidieux.

Liaison de données fonctionne en synchronisation des objets comme modifier leurs valeurs liées. Au lieu de devoir écrire des gestionnaires d’événements pour chaque modification de valeur d’un contrôle, vous établissez la liaison et activez la liaison dans votre ViewModel.

Pour plus d’informations sur la liaison de données, consultez [notions de base de données liaison](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) qui est la quatrième partie de la [articles de la série principes de base XAML Xamarin.Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Liaison de cellules
Propriétés de cellules (et des cellules enfants) peuvent être liées aux propriétés d’objets dans le `ItemsSource`. Par exemple, un `ListView` peut être utilisé pour présenter une liste d’employés.

La classe employee :

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Un `ObservableCollection<Employee>` est créé et défini `ListView`comme étant `ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
  //defined in XAML to follow
  EmployeeView.ItemsSource = employees;
  ...
}
```

La liste est remplie avec des données :

```csharp
public EmployeeListPage()
{
  ...
  employees.Add(new Employee{ DisplayName="Rob Finnerty"});
  employees.Add(new Employee{ DisplayName="Bill Wrestler"});
  employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
  employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
  employees.Add(new Employee{ DisplayName="Sheri Spruce"});
  employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

L’extrait suivant montre un `ListView` lié à une liste d’employés :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="EmployeeView"
            ItemsSource="{Binding Employees}">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Cet exemple XAML définit un `ContentPage` qui contient un `ListView`. La source de données de la `ListView` est définie via la `ItemsSource` attribut. La disposition de chaque ligne `ItemsSource` du est définie dans l' `ListView.ItemTemplate` élément. Voici les captures d’écran suivantes:

![](data-and-databinding-images/bound-data.png "ListView à l’aide de la liaison de données")

### <a name="binding-selecteditem"></a>Liaison SelectedItem

Fréquence à laquelle vous souhaitez lier à l’élément sélectionné d’un `ListView`, plutôt que d’utiliser un gestionnaire d’événements pour répondre aux modifications. Pour ce faire, dans XAML, vous devez lier le `SelectedItem` propriété :

```xaml
<ListView x:Name="listView"
 SelectedItem="{Binding Source={x:Reference SomeLabel},
 Path=Text}">
 …
</ListView>
```

En supposant que `listView`de `ItemsSource` est une liste de chaînes, `SomeLabel` aura sa propriété de texte liée à la `SelectedItem`.

## <a name="related-links"></a>Liens associés

- [Liaison bidirectionnelle (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
