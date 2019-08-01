---
title: Création d’un DataTemplate Xamarin.Forms
description: Vous pouvez créer des modèles de données inline, dans un ResourceDictionary, à partir d’un type personnalisé ou à partir du type de cellule Xamarin.Forms approprié. Cet article explore chaque technique.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 1163f264fc54a461d8d95854524439589cdc81f5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646982"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Création d’un DataTemplate Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Vous pouvez créer des modèles de données inline, dans un ResourceDictionary, à partir d’un type personnalisé ou à partir du type de cellule Xamarin.Forms approprié. Cet article explore chaque technique._

Pour [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), le scénario d’usage courant consiste à afficher des données provenant d’une collection d’objets dans un [`ListView`](xref:Xamarin.Forms.ListView). Vous pouvez gérer l’apparence des données de chaque cellule dans [`ListView`](xref:Xamarin.Forms.ListView) en affectant à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Plusieurs techniques permettent d’y parvenir :

- [Création d’un DataTemplate inline](#inline).
- [Création d’un DataTemplate avec un type](#type).
- [Création d’un DataTemplate en tant que ressource](#resource).

Quelle que soit la technique utilisée, l’apparence de chaque cellule du [`ListView`](xref:Xamarin.Forms.ListView) est définie par un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), comme indiqué dans les captures d’écran suivantes :

![](creating-images/data-template-appearance.png "ListView avec un DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Création d’un DataTemplate inline

Vous pouvez affecter à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) inline. Vous devez utiliser un modèle inline, c’est-à-dire un modèle placé en tant qu’enfant direct d’une propriété de contrôle appropriée, s’il n’est pas nécessaire de réutiliser le modèle de données ailleurs. Les éléments spécifiés dans `DataTemplate` définissent l’apparence de chaque cellule, comme indiqué dans l’exemple de code XAML suivant :

```xaml
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
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

L’enfant d’un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) inline doit être de type [`Cell`](xref:Xamarin.Forms.Cell) ou en dériver. Cet exemple utilise un [`ViewCell`](xref:Xamarin.Forms.ViewCell), qui dérive de `Cell`. La disposition dans `ViewCell` est gérée ici par [`Grid`](xref:Xamarin.Forms.Grid). `Grid` contient trois instances de [`Label`](xref:Xamarin.Forms.Label) qui lient leurs propriétés [`Text`](xref:Xamarin.Forms.Label.Text) aux propriétés appropriées de chaque objet `Person` de la collection.

Le code C# équivalent est affiché dans l’exemple de code suivant :

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

En C#, le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) inline est créé à l’aide d’une surcharge de constructeur qui spécifie un argument `Func`.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Création d’un DataTemplate avec un type

Vous pouvez également affecter à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) créé à partir d’un type de cellule. L’avantage de cette approche est que l’apparence définie par le type de cellule peut être réutilisée par plusieurs modèles de données dans l’application. Le code XAML suivant montre un exemple de cette approche :

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
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Ici, la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) a la valeur d’un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) créé à partir d’un type personnalisé qui définit l’apparence de la cellule. Le type personnalisé doit dériver du type [`ViewCell`](xref:Xamarin.Forms.ViewCell), comme indiqué dans l’exemple de code suivant :

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

Dans [`ViewCell`](xref:Xamarin.Forms.ViewCell), la disposition est gérée ici par [`Grid`](xref:Xamarin.Forms.Grid). `Grid` contient trois instances de [`Label`](xref:Xamarin.Forms.Label) qui lient leurs propriétés [`Text`](xref:Xamarin.Forms.Label.Text) aux propriétés appropriées de chaque objet `Person` de la collection.

Le code C# équivalent est indiqué dans l’exemple suivant :

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

En C#, le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est créé à l’aide d’une surcharge de constructeur qui spécifie le type de cellule en tant qu’argument. Le type de cellule doit dériver du type [`ViewCell`](xref:Xamarin.Forms.ViewCell), comme indiqué dans l’exemple de code suivant :

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> Notez que Xamarin.Forms inclut également des types de cellule que vous pouvez utiliser pour afficher des données simples dans des cellules [`ListView`](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Création d’un DataTemplate en tant que ressource

Vous pouvez également créer des modèles de données sous forme d’objets réutilisables dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Pour ce faire, vous affectez à chaque déclaration un attribut `x:Key` unique, qui lui fournit une clé descriptive dans `ResourceDictionary`, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est affecté à la propriété [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) à l’aide de l’extension de balisage `StaticResource`. Notez que même si `DataTemplate` est défini dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la page, il peut également l’être au niveau du contrôle ou de l’application.

L’exemple de code suivant montre la page équivalente en C# :

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est ajouté au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à l’aide de la méthode [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)), qui spécifie une chaîne `Key` permettant de référencer `DataTemplate` durant sa récupération.

## <a name="summary"></a>Récapitulatif

Cet article explique comment créer des modèles de données inline à partir d’un type personnalisé ou dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Vous devez utiliser un modèle inline si vous n’avez pas besoin de réutiliser le modèle de données ailleurs. Vous pouvez aussi réutiliser un modèle de données en le définissant en tant que type personnalisé, ou en tant que ressource au niveau du contrôle, de la page ou de l’application.


## <a name="related-links"></a>Liens associés

- [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
