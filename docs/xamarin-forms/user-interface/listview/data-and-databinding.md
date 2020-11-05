---
title: Sources de données ListView
description: Cet article explique comment remplir le Xamarin.Forms ListView avec des données et comment utiliser la liaison de données avec un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e133c684009c116b9cf70a3ccef78484b5515ddb
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365797"
---
# <a name="listview-data-sources"></a>Sources de données ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) est utilisé pour afficher des listes de données. Cet article explique comment remplir un `ListView` avec des données et comment lier des données à l’élément sélectionné.

## <a name="itemssource"></a>ItemsSource

Un [`ListView`](xref:Xamarin.Forms.ListView) est rempli avec des données à l’aide de la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété, qui peut accepter n’importe quelle collection implémentant `IEnumerable` . La façon la plus simple de remplir un `ListView` implique d’utiliser un tableau de chaînes :

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

![ListView affichant la liste des chaînes](data-and-databinding-images/itemssource-simple.png)

Cette approche remplira `ListView` avec une liste de chaînes. Par défaut, appellera `ListView` `ToString` et affichera le résultat dans un `TextCell` pour chaque ligne. Pour personnaliser la façon dont les données sont affichées, consultez apparence de la [cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Étant donné que `ItemsSource` a été envoyé à un tableau, le contenu n’est pas mis à jour à mesure que la liste ou le tableau sous-jacent est modifié. Si vous souhaitez que le ListView soit automatiquement mis à jour à mesure que des éléments sont ajoutés, supprimés et modifiés dans la liste sous-jacente, vous devez utiliser un `ObservableCollection` . [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) est défini dans `System.Collections.ObjectModel` et est tout à fait similaire `List` , à ceci près qu’il peut notifier `ListView` les modifications :

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Liaison de données

La liaison de données est le « collage » qui lie les propriétés d’un objet d’interface utilisateur aux propriétés d’un objet CLR, par exemple une classe dans votre ViewModel. La liaison de données est utile, car elle simplifie le développement d’interfaces utilisateur en remplaçant un code réutilisable de façon ennuyeuse.

La liaison de données fonctionne en conservant la synchronisation des objets lorsque leurs valeurs liées sont modifiées. Au lieu d’avoir à écrire des gestionnaires d’événements pour chaque modification de la valeur d’un contrôle, vous établissez la liaison et activez la liaison dans votre ViewModel.

Pour plus d’informations sur la liaison de données, consultez [principes de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) de la liaison de données, qui est la quatrième partie de la série d’Articles de base sur [ Xamarin.Forms XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Lier des cellules

Les propriétés des cellules (et des enfants de cellules) peuvent être liées aux propriétés d’objets dans le `ItemsSource` . Par exemple, un `ListView` peut être utilisé pour présenter une liste d’employés.

La classe Employee :

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Un `ObservableCollection<Employee>` est créé, défini en tant que `ListView` `ItemsSource` , et la liste est remplie avec des données :

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
> Si un `ListView` est mis à jour en réponse aux modifications dans son sous-jacent `ObservableCollection` , un ne `ListView` sera pas mis à jour si une `ObservableCollection` instance différente est assignée à la référence d’origine `ObservableCollection` (par exemple, `employees = otherObservableCollection;` ).

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

Cet exemple XAML définit un `ContentPage` qui contient un `ListView` . La source de données du contrôle `ListView` est définie via l’attribut `ItemsSource`. La disposition de chaque ligne dans l’attribut `ItemsSource` est définie dans l’élément `ListView.ItemTemplate`. Voici les captures d’écran suivantes :

![ListView utilisant la liaison de données](data-and-databinding-images/bound-data.png)

> [!WARNING]
> `ObservableCollection` n'est pas sûr (thread safe). La modification de `ObservableCollection` entraîne des mises à jour de l’interface utilisateur sur le thread qui a effectué les modifications. Si le thread n’est pas le thread d’interface utilisateur principal, une exception est levée.

### <a name="binding-selecteditem"></a>Liaison de SelectedItem

Il est souvent préférable d’effectuer une liaison à l’élément sélectionné d’un `ListView` , plutôt que d’utiliser un gestionnaire d’événements pour répondre aux modifications. Pour effectuer cette opération en XAML, liez la `SelectedItem` propriété :

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

En supposant `listView` `ItemsSource` que est une liste de chaînes, `SomeLabel` sa `Text` propriété sera liée à `SelectedItem` .

## <a name="related-links"></a>Liens associés

- [Liaison bidirectionnelle (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)