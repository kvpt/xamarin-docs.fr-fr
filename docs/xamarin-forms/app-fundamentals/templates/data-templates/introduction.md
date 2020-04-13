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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771280"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introduction aux modèles de données Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Les modèles de données Xamarin.Forms permettent de définir la présentation des données sur les contrôles pris en charge. Cet article fournit une introduction aux modèles de données, examinant pourquoi ils sont nécessaires._

Considérez [`ListView`](xref:Xamarin.Forms.ListView) un qui `Person` affiche une collection d’objets. L’exemple de code suivant montre la définition de la classe `Person` :

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

La classe `Person` définit les propriétés `Name`, `Age` et `Location`, qui peuvent être définies durant la création d’un objet `Person`. L’est [`ListView`](xref:Xamarin.Forms.ListView) utilisé pour `Person` afficher la collection d’objets, comme indiqué dans l’exemple de code XAML suivant:

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

Les articles sont [`ListView`](xref:Xamarin.Forms.ListView) ajoutés à l’in XAML en initialisant la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété à partir d’un éventail d’instances. `Person`

> [!NOTE]
> Notez que l’élément `x:Array` nécessite un attribut `Type` qui indique le type des éléments du tableau.

La page C équivalente est indiquée dans l’exemple de code suivant, qui initialise la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriété à un `List` des `Person` cas :

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

Les [`ListView`](xref:Xamarin.Forms.ListView) `ToString` appels lors de l’affichage des objets de la collection. Dans la mesure où il n’existe pas de substitution de `Person.ToString`, `ToString` retourne le nom du type de chaque objet, comme indiqué dans les captures d’écran suivantes :

![](introduction-images/no-data-template.png "ListView without a Data Template")

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

Il en [`ListView`](xref:Xamarin.Forms.ListView) résulte `Person.Name` l’affichage de la valeur de propriété pour chaque objet de la collection, comme indiqué dans les captures d’écran suivantes:

![](introduction-images/override-tostring.png "ListView with a Data Template")

La substitution de `Person.ToString` peut retourner une chaîne mise en forme composée des propriétés `Name`, `Age` et `Location`. Toutefois, cette approche offre uniquement un contrôle limité sur l’apparence de chaque élément de données. Pour plus de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) flexibilité, un peut être créé qui définit l’apparence des données.

## <a name="creating-a-datatemplate"></a>Création d’un DataTemplate

A [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est utilisé pour spécifier l’apparence des données, et utilise généralement la liaison de données pour afficher les données. Son scénario d’utilisation commun est lors de [`ListView`](xref:Xamarin.Forms.ListView)l’affichage des données d’une collection d’objets dans un . Par exemple, quand `ListView` est lié à une collection d’objets `Person`, la propriété `ListView.ItemTemplate` a la valeur d’un `DataTemplate` qui définit l’apparence de chaque objet `Person` dans `ListView`. `DataTemplate` contient des éléments qui se lient aux valeurs de propriété de chaque objet `Person`. Pour plus d’informations sur la liaison de données, consultez [Notions de base de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

A [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) peut être utilisé comme valeur pour les propriétés suivantes :

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), qui est [`ListView`](xref:Xamarin.Forms.ListView)hérité par .
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1), qui est [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)hérité [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)par , , et .

> [!NOTE]
> Notez que [`TableView`](xref:Xamarin.Forms.TableView) bien [`Cell`](xref:Xamarin.Forms.Cell) que les faits [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)utilisations d’objets, il n’utilise pas un . En effet, les liaisons de données sont toujours définies directement sur les objets `Cell`.

Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui est placé comme un enfant direct des propriétés énumérées ci-dessus est connu comme un *modèle en ligne*. Vous pouvez également définir un `DataTemplate` en tant que ressource au niveau du contrôle, au niveau de la page ou au niveau de l’application. Choisir où [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définir un impact où il peut être utilisé :

- Un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) défini au niveau de contrôle ne peut être appliqué qu’au contrôle.
- Une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définition au niveau de la page peut être appliquée à plusieurs contrôles valides sur la page.
- Une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définition au niveau de l’application peut être appliquée aux contrôles valides tout au long de l’application.

Les modèles de données situés plus bas dans la hiérarchie de vues sont prioritaires par rapport à ceux définis plus haut quand ils partagent des attributs `x:Key`. Par exemple, un modèle de données au niveau de l’application est remplacé par un modèle de données au niveau de la page, et un modèle de données au niveau de la page est remplacé par un modèle de données au niveau du contrôle ou par un modèle de données inline.

## <a name="related-links"></a>Liens connexes

- [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
