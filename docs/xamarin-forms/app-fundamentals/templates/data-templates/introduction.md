---
title: Introduction aux modèles de données Xamarin.Forms
description: Les modèles de données Xamarin.Forms permettent de définir la présentation des données sur les contrôles pris en charge. Cet article présente les modèles de données et explique pourquoi ils sont nécessaires.
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 10bba38de1dc8908ad853d5e4ca2bb845b4ac8c6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771280"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introduction aux modèles de données Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Les modèles de données Xamarin.Forms permettent de définir la présentation des données sur les contrôles pris en charge. Cet article présente les modèles de données et explique pourquoi ils sont nécessaires._

Prenons l’exemple d’un [`ListView`](xref:Xamarin.Forms.ListView) qui affiche une collection d’objets `Person`. L’exemple de code suivant montre la définition de la classe `Person` :

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

La classe `Person` définit les propriétés `Name`, `Age` et `Location`, qui peuvent être définies durant la création d’un objet `Person`. [`ListView`](xref:Xamarin.Forms.ListView) permet d’afficher la collection d’objets `Person`, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

Les éléments sont ajoutés au [`ListView`](xref:Xamarin.Forms.ListView) en XAML via l’initialisation de la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) à partir d’un tableau d’instances de `Person`.

> [!NOTE]
> Notez que l’élément `x:Array` nécessite un attribut `Type` qui indique le type des éléments du tableau.

La page équivalente en C# est illustrée dans l’exemple de code suivant, qui initialise la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) à un `List` d’instances de `Person` :

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

[`ListView`](xref:Xamarin.Forms.ListView) appelle `ToString` au moment d’afficher les objets de la collection. Dans la mesure où il n’existe pas de substitution de `Person.ToString`, `ToString` retourne le nom du type de chaque objet, comme indiqué dans les captures d’écran suivantes :

![](introduction-images/no-data-template.png "ListView sans modèle de données")

L’objet `Person` peut remplacer la méthode `ToString` pour afficher les données significatives, comme indiqué dans l’exemple de code suivant :

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

Ainsi, [`ListView`](xref:Xamarin.Forms.ListView) affiche la valeur de propriété `Person.Name` pour chaque objet de la collection, comme indiqué dans les captures d’écran suivantes :

![](introduction-images/override-tostring.png "ListView avec un modèle de données")

La substitution de `Person.ToString` peut retourner une chaîne mise en forme composée des propriétés `Name`, `Age` et `Location`. Toutefois, cette approche offre uniquement un contrôle limité sur l’apparence de chaque élément de données. Pour plus de souplesse, vous pouvez créer un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence des données.

## <a name="creating-a-datatemplate"></a>Création d’un DataTemplate

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) permet de spécifier l’apparence des données et utilise généralement la liaison de données pour afficher les données. Le scénario d’usage courant consiste à afficher les données provenant d’une collection d’objets dans un [`ListView`](xref:Xamarin.Forms.ListView). Par exemple, quand `ListView` est lié à une collection d’objets `Person`, la propriété `ListView.ItemTemplate` a la valeur d’un `DataTemplate` qui définit l’apparence de chaque objet `Person` dans `ListView`. `DataTemplate` contient des éléments qui se lient aux valeurs de propriété de chaque objet `Person`. Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) peut être utilisé comme valeur pour les propriétés suivantes :

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), hérité par [`ListView`](xref:Xamarin.Forms.ListView)
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), hérité par [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)

> [!NOTE]
> Notez que même si [`TableView`](xref:Xamarin.Forms.TableView) utilise des objets [`Cell`](xref:Xamarin.Forms.Cell), il n’utilise pas [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). En effet, les liaisons de données sont toujours définies directement sur les objets `Cell`.

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) placé en tant qu’enfant direct des propriétés listées ci-dessus est appelé un *modèle inline*. Vous pouvez également définir un `DataTemplate` en tant que ressource au niveau du contrôle, au niveau de la page ou au niveau de l’application. Le fait de choisir où définir [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impacte l’emplacement où il peut être utilisé :

- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau du contrôle peut uniquement être appliqué au contrôle.
- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau de la page peut être appliqué à plusieurs contrôles valides de la page.
- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau de l’application peut être appliqué aux contrôles valides dans toute l’application.

Les modèles de données situés plus bas dans la hiérarchie de vues sont prioritaires par rapport à ceux définis plus haut quand ils partagent des attributs `x:Key`. Par exemple, un modèle de données au niveau de l’application est remplacé par un modèle de données au niveau de la page, et un modèle de données au niveau de la page est remplacé par un modèle de données au niveau du contrôle ou par un modèle de données inline.

## <a name="related-links"></a>Liens associés

- [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
