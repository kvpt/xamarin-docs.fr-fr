---
title: Sources de données de ListView
description: Cet article explique comment remplir le ListView Xamarin.Forms avec des données et comment utiliser la liaison de données avec un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
ms.openlocfilehash: e51f0bd011750b030c0a11b9b89a2c2473f2a9ed
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247585"
---
# <a name="listview-data-sources"></a>Sources de données de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Une [`ListView`](xref:Xamarin.Forms.ListView) Xamarin. Forms est utilisée pour afficher des listes de données. Cet article explique comment remplir un `ListView` avec des données et comment lier des données à l’élément sélectionné.

## <a name="itemssource"></a>ItemsSource

Une [`ListView`](xref:Xamarin.Forms.ListView) est remplie avec des données à l’aide de la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) , qui peut accepter n’importe quel regroupement implémentant `IEnumerable`. La façon la plus simple de remplir un `ListView` consiste à utiliser un tableau de chaînes :

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

![](data-and-databinding-images/itemssource-simple.png "ListView Displaying List of Strings")

Cette approche remplit le `ListView` avec une liste de chaînes. Par défaut, `ListView` appellera `ToString` et affichera le résultat dans une `TextCell` pour chaque ligne. Pour personnaliser la façon dont les données sont affichées, consultez apparence de la [cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Étant donné que `ItemsSource` a été envoyé à un tableau, le contenu n’est pas mis à jour à mesure que la liste ou le tableau sous-jacent est modifié. Si vous souhaitez que le ListView soit automatiquement mis à jour à mesure que des éléments sont ajoutés, supprimés et modifiés dans la liste sous-jacente, vous devez utiliser un `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) est défini dans `System.Collections.ObjectModel` et est tout comme `List`, à ceci près qu’il peut notifier `ListView` de toute modification :

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Liaison de données

La liaison de données est le « collage » qui lie les propriétés d’un objet d’interface utilisateur aux propriétés d’un objet CLR, par exemple une classe dans votre ViewModel. Liaison de données est utile, car elle simplifie le développement d’interfaces utilisateur en remplaçant un grand nombre de code fastidieux.

Liaison de données fonctionne en synchronisation des objets comme modifier leurs valeurs liées. Au lieu d’avoir à écrire des gestionnaires d’événements pour chaque modification de la valeur d’un contrôle, vous établissez la liaison et activez la liaison dans votre ViewModel.

Pour plus d’informations sur la liaison de données, consultez [principes de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) de la liaison de données, qui est la quatrième partie de la série d’Articles de base sur [XAML Xamarin. Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Liaison de cellules

Les propriétés des cellules (et des enfants de cellules) peuvent être liées aux propriétés des objets dans la `ItemsSource`. Par exemple, un `ListView` peut être utilisé pour présenter une liste d’employés.

La classe employee :

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Une `ObservableCollection<Employee>` est créée, définie comme `ListView` `ItemsSource`, et la liste est remplie avec des données :

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
    EmployeeView.ItemsSource = employees;

    // ObservableCollection allows items to be added after ItemsSource
    // is set and the UI will react to changes
    employees.Add(new Employee{ DisplayName="Rob Finnerty"});
    employees.Add(new Employee{ DisplayName="Bill Wrestler"});
    employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
    employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
    employees.Add(new Employee{ DisplayName="Sheri Spruce"});
    employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

> [!WARNING]
> Bien qu’un `ListView` soit mis à jour en réponse aux modifications de son `ObservableCollection`sous-jacent, un `ListView` n’est pas mis à jour si une instance de `ObservableCollection` différente est assignée à la référence de `ObservableCollection` d’origine (par exemple, `employees = otherObservableCollection;`).

L’extrait de code suivant illustre un `ListView` lié à une liste d’employés :

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

Cet exemple XAML définit une `ContentPage` qui contient un `ListView`. La source de données de l' `ListView` est définie via l’attribut `ItemsSource`. La disposition de chaque ligne dans le `ItemsSource` est définie dans l’élément `ListView.ItemTemplate`. Voici les captures d’écran suivantes :

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` n’est pas thread-safe. La modification d’une `ObservableCollection` entraîne des mises à jour de l’interface utilisateur sur le thread qui a effectué les modifications. Si le thread n’est pas le thread d’interface utilisateur principal, une exception est levée.

### <a name="binding-selecteditem"></a>Liaison SelectedItem

Il est souvent préférable d’effectuer une liaison à l’élément sélectionné d’un `ListView`, plutôt que d’utiliser un gestionnaire d’événements pour répondre aux modifications. Pour effectuer cette opération en XAML, liez la propriété `SelectedItem` :

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

En supposant que la `ItemsSource` de `listView`est une liste de chaînes, `SomeLabel` la propriété `Text` sera liée à la `SelectedItem`.

## <a name="related-links"></a>Liens connexes

- [Liaison bidirectionnelle (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
