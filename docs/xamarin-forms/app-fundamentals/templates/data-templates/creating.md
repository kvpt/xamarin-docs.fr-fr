---
title: Création d’un DataTemplate Xamarin.Forms
description: Vous pouvez créer des modèles de données inline, dans un ResourceDictionary, à partir d’un type personnalisé ou à partir du type de cellule Xamarin.Forms approprié. Cet article explore chaque technique.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 399f411acd497b9d55ca81f670556430fe5f5503
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771284"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Création d’un DataTemplate Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Les modèles de données peuvent être créés en ligne, dans un ResourceDictionary, ou à partir d’un type personnalisé ou approprié Xamarin.Forms type de cellule. Cet article explore chaque technique._

Un scénario d’utilisation courant pour un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est [`ListView`](xref:Xamarin.Forms.ListView)d’afficher des données à partir d’une collection d’objets dans un . L’apparence des données pour [`ListView`](xref:Xamarin.Forms.ListView) chaque cellule dans [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) le peut [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)être géré en fixant la propriété à un . Plusieurs techniques permettent d’y parvenir :

- [Création d’un DataTemplate inline](#inline).
- [Création d’un DataTemplate avec un type](#type).
- [Création d’un DataTemplate comme ressource](#resource).

Indépendamment de la technique utilisée, le résultat est que [`ListView`](xref:Xamarin.Forms.ListView) l’apparition [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)de chaque cellule dans le est défini par un , comme indiqué dans les captures d’écran suivantes:

![](creating-images/data-template-appearance.png "ListView with a DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Création d’un DataTemplate inline

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété peut être réglée [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à une ligne . Vous devez utiliser un modèle inline, c’est-à-dire un modèle placé en tant qu’enfant direct d’une propriété de contrôle appropriée, s’il n’est pas nécessaire de réutiliser le modèle de données ailleurs. Les éléments spécifiés dans `DataTemplate` définissent l’apparence de chaque cellule, comme indiqué dans l’exemple de code XAML suivant :

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

L’enfant d’une ligne [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) doit être de [`Cell`](xref:Xamarin.Forms.Cell)type . Cet exemple [`ViewCell`](xref:Xamarin.Forms.ViewCell)utilise un , `Cell`qui dérive de . Mise en `ViewCell` page à [`Grid`](xref:Xamarin.Forms.Grid)l’intérieur de la est gérée ici par un . Les `Grid` deux [`Label`](xref:Xamarin.Forms.Label) instances qui [`Text`](xref:Xamarin.Forms.Label.Text) lient leurs propriétés aux propriétés appropriées de chaque `Person` objet de la collection.

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

Dans C, l’inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est créée à l’aide `Func` d’une surcharge de constructeurs qui spécifie un argument.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Création d’un DataTemplate avec un type

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété peut également [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) être réglée à un qui est créé à partir d’un type de cellule. L’avantage de cette approche est que l’apparence définie par le type de cellule peut être réutilisée par plusieurs modèles de données dans l’application. Le code XAML suivant montre un exemple de cette approche :

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

Ici, [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) la propriété est [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définie à un qui est créé à partir d’un type personnalisé qui définit l’apparence de la cellule. Le type personnalisé doit [`ViewCell`](xref:Xamarin.Forms.ViewCell)dériver du type, comme indiqué dans l’exemple de code suivant :

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

Dans [`ViewCell`](xref:Xamarin.Forms.ViewCell)la , mise en [`Grid`](xref:Xamarin.Forms.Grid)page est gérée ici par un . Les `Grid` deux [`Label`](xref:Xamarin.Forms.Label) instances qui [`Text`](xref:Xamarin.Forms.Label.Text) lient leurs propriétés aux propriétés appropriées de chaque `Person` objet de la collection.

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

Dans C, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) le est créé à l’aide d’une surcharge de constructeur qui spécifie le type de cellule comme un argument. Le type de cellule [`ViewCell`](xref:Xamarin.Forms.ViewCell)doit dériver du type, comme le montre l’exemple de code suivant :

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
> Notez que Xamarin.Forms inclut également les types de [`ListView`](xref:Xamarin.Forms.ListView) cellules qui peuvent être utilisés pour afficher des données simples dans les cellules. Pour plus d’informations, consultez [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Création d’un DataTemplate en tant que ressource

Les modèles de données peuvent également être [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)créés en tant qu’objets réutilisables dans un . Pour ce faire, vous affectez à chaque déclaration un attribut `x:Key` unique, qui lui fournit une clé descriptive dans `ResourceDictionary`, comme indiqué dans l’exemple de code XAML suivant :

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

Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est attribué [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) à la `StaticResource` propriété en utilisant l’extension de balisage. Notez que `DataTemplate` si le est [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)défini dans la page, il peut également être défini au niveau de contrôle ou au niveau de l’application.

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

L’est [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ajouté [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) l’utilisation de `Key` la méthode, qui `DataTemplate` spécifie une chaîne qui est utilisé pour référencer le lors de sa récupération.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment créer des modèles de données, en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)ligne, à partir d’un type personnalisé, ou dans un . Vous devez utiliser un modèle inline si vous n’avez pas besoin de réutiliser le modèle de données ailleurs. Vous pouvez aussi réutiliser un modèle de données en le définissant en tant que type personnalisé, ou en tant que ressource au niveau du contrôle, de la page ou de l’application.

## <a name="related-links"></a>Liens connexes

- [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
