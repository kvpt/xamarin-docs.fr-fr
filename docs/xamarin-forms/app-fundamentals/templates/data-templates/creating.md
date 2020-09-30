---
title: Création d’un Xamarin.Forms DataTemplate
description: Les modèles de données peuvent être créés en ligne, dans un ResourceDictionary, ou à partir d’un type personnalisé ou d’un Xamarin.Forms type de cellule approprié. Cet article explore chaque technique.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f4ca18a34dc66a81fd30ce4ae2b60e31df8cffb5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562611"
---
# <a name="creating-a-no-locxamarinforms-datatemplate"></a>Création d’un Xamarin.Forms DataTemplate

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Les modèles de données peuvent être créés en ligne, dans un ResourceDictionary, ou à partir d’un type personnalisé ou d’un Xamarin.Forms type de cellule approprié. Cet article explore chaque technique._

Un scénario d’utilisation courante pour un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) affiche des données à partir d’une collection d’objets dans un [`ListView`](xref:Xamarin.Forms.ListView) . L’apparence des données pour chaque cellule du [`ListView`](xref:Xamarin.Forms.ListView) peut être gérée en affectant à la propriété la valeur [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Plusieurs techniques permettent d’y parvenir :

- [Création d’un DataTemplate inline](#creating-an-inline-datatemplate).
- [Création d’un DataTemplate avec un type](#creating-a-datatemplate-with-a-type).
- [Création d’un DataTemplate en tant que ressource](#creating-a-datatemplate-as-a-resource).

Quelle que soit la technique utilisée, le résultat est que l’apparence de chaque cellule du [`ListView`](xref:Xamarin.Forms.ListView) est définie par un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , comme illustré dans les captures d’écran suivantes :

![ListView avec un DataTemplate](creating-images/data-template-appearance.png)

## <a name="creating-an-inline-datatemplate"></a>Création d’un DataTemplate inline

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété peut être définie sur inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Vous devez utiliser un modèle inline, c’est-à-dire un modèle placé en tant qu’enfant direct d’une propriété de contrôle appropriée, s’il n’est pas nécessaire de réutiliser le modèle de données ailleurs. Les éléments spécifiés dans `DataTemplate` définissent l’apparence de chaque cellule, comme indiqué dans l’exemple de code XAML suivant :

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

L’enfant d’un inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) doit être de type, ou dériver de [`Cell`](xref:Xamarin.Forms.Cell) . Cet exemple utilise un [`ViewCell`](xref:Xamarin.Forms.ViewCell) , qui dérive de `Cell` . La disposition à l’intérieur du `ViewCell` est gérée ici par un [`Grid`](xref:Xamarin.Forms.Grid) . Le `Grid` contient trois [`Label`](xref:Xamarin.Forms.Label) instances qui lient leurs [`Text`](xref:Xamarin.Forms.Label.Text) Propriétés aux propriétés appropriées de chaque `Person` objet dans la collection.

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

En C#, la fonction inline [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est créée à l’aide d’une surcharge de constructeur qui spécifie un `Func` argument.

## <a name="creating-a-datatemplate-with-a-type"></a>Création d’un DataTemplate avec un type

La [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété peut également être définie sur une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui est créée à partir d’un type de cellule. L’avantage de cette approche est que l’apparence définie par le type de cellule peut être réutilisée par plusieurs modèles de données dans l’application. Le code XAML suivant montre un exemple de cette approche :

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

Ici, la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété a la valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui est créée à partir d’un type personnalisé qui définit l’apparence de la cellule. Le type personnalisé doit dériver du type [`ViewCell`](xref:Xamarin.Forms.ViewCell) , comme illustré dans l’exemple de code suivant :

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

Dans [`ViewCell`](xref:Xamarin.Forms.ViewCell) , la disposition est gérée ici par un [`Grid`](xref:Xamarin.Forms.Grid) . Le `Grid` contient trois [`Label`](xref:Xamarin.Forms.Label) instances qui lient leurs [`Text`](xref:Xamarin.Forms.Label.Text) Propriétés aux propriétés appropriées de chaque `Person` objet dans la collection.

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

En C#, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est créé à l’aide d’une surcharge de constructeur qui spécifie le type de cellule comme argument. Le type de cellule doit dériver du type [`ViewCell`](xref:Xamarin.Forms.ViewCell) , comme illustré dans l’exemple de code suivant :

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
> Notez que Xamarin.Forms comprend également des types de cellule qui peuvent être utilisés pour afficher des données simples dans des [`ListView`](xref:Xamarin.Forms.ListView) cellules. Pour plus d’informations, consultez [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

## <a name="creating-a-datatemplate-as-a-resource"></a>Création d’un DataTemplate en tant que ressource

Les modèles de données peuvent également être créés en tant qu’objets réutilisables dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Pour ce faire, vous affectez à chaque déclaration un attribut `x:Key` unique, qui lui fournit une clé descriptive dans `ResourceDictionary`, comme indiqué dans l’exemple de code XAML suivant :

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

Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est assigné à la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) propriété à l’aide de l' `StaticResource` extension de balisage. Notez que si `DataTemplate` est défini dans le de la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , il peut également être défini au niveau du contrôle ou de l’application.

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

Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) est ajouté au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) à l’aide de la [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) méthode, qui spécifie une `Key` chaîne utilisée pour référencer le `DataTemplate` lors de sa récupération.

## <a name="summary"></a>Résumé

Cet article a expliqué comment créer des modèles de données, inline, à partir d’un type personnalisé ou dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Vous devez utiliser un modèle inline si vous n’avez pas besoin de réutiliser le modèle de données ailleurs. Vous pouvez aussi réutiliser un modèle de données en le définissant en tant que type personnalisé, ou en tant que ressource au niveau du contrôle, de la page ou de l’application.

## <a name="related-links"></a>Liens associés

- [Apparence de cellule](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Modèles de données (exemple)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)