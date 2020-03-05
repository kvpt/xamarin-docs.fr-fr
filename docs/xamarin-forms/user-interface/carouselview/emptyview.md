---
title: Xamarin. Forms CarouselView EmptyView
description: Dans CarouselView, vous pouvez spécifier une vue vide qui fournit des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.
ms.prod: xamarin
ms.assetid: C6DEE1A9-63FC-4889-BC77-F401D5D7DF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2019
ms.openlocfilehash: 8359e5f5008205237d602d7d364ebea376b57cf0
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "78291704"
---
# <a name="xamarinforms-carouselview-emptyview"></a>Xamarin. Forms CarouselView EmptyView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes qui peuvent être utilisées pour fournir des commentaires à l’utilisateur lorsqu’il n’y a pas de données à afficher :

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), de type `object`, la chaîne, la liaison ou la vue qui s’affiche lorsque la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est `null`ou lorsque la collection spécifiée par la propriété `ItemsSource` est `null` ou vide. La valeur par défaut est `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), le modèle à utiliser pour mettre en forme le `EmptyView`spécifié. La valeur par défaut est `null`.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Les principaux scénarios d’utilisation de la propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) affichent les commentaires des utilisateurs lorsqu’une opération de filtrage sur un [`CarouselView`](xref:Xamarin.Forms.CarouselView) ne produit pas de données et affiche les commentaires des utilisateurs pendant que les données sont récupérées à partir d’un service Web.

> [!NOTE]
> La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) peut être définie sur une vue qui comprend du contenu interactif, si nécessaire.

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Afficher une chaîne lorsque les données ne sont pas disponibles

La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) peut être définie sur une chaîne, qui s’affiche lorsque la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est `null`ou lorsque la collection spécifiée par la propriété `ItemsSource` est `null` ou vide. Le code XAML suivant illustre un exemple de ce scénario :

```xaml
<CarouselView ItemsSource="{Binding EmptyMonkeys}"
              EmptyView="No items to display." />
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    EmptyView = "No items to display."
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

Le résultat est que, étant donné que la collection liée aux données est `null`, la chaîne définie comme [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valeur de la propriété est affichée.

## <a name="display-views-when-data-is-unavailable"></a>Afficher les affichages quand les données ne sont pas disponibles

La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) peut être définie sur une vue, qui sera affichée lorsque la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est `null`ou lorsque la collection spécifiée par la propriété `ItemsSource` est `null` ou vide. Il peut s’agir d’une vue unique ou d’une vue contenant plusieurs vues enfants. L’exemple de code XAML suivant illustre la propriété `EmptyView` définie sur une vue contenant plusieurs vues enfants :

```xaml
<StackLayout Margin="20">
    <SearchBar SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CarouselView.EmptyView>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Le code C# équivalent est :

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute la `FilterCommand`, la collection affichée par l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la propriété [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Si l’opération de filtrage ne produit aucune donnée, le [`StackLayout`](xref:Xamarin.Forms.StackLayout) défini comme valeur de la propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) est affiché.

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Afficher un type personnalisé basé sur un modèle lorsque les données ne sont pas disponibles

La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) peut avoir la valeur d’un type personnalisé, dont le modèle s’affiche lorsque la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est `null`ou lorsque la collection spécifiée par la propriété `ItemsSource` est `null` ou vide. La propriété [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) peut avoir la valeur d’une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence du `EmptyView`. Le code XAML suivant illustre un exemple de ce scénario :

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <controls:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CarouselView.EmptyView>
        <CarouselView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CarouselView.EmptyViewTemplate>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Le code C# équivalent est :

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

Le type de `FilterData` définit une propriété `Filter` et un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)correspondant :

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) est définie sur un objet `FilterData`, et les données de la propriété `Filter` sont liées à la propriété [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute la `FilterCommand`, la collection affichée par l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la propriété `Filter`. Si l’opération de filtrage ne produit aucune donnée, le [`Label`](xref:Xamarin.Forms.Label) défini dans la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui est défini comme valeur de la propriété [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) , est affiché.

> [!NOTE]
> Lorsque vous affichez un type personnalisé basé sur un modèle lorsque les données ne sont pas disponibles, la propriété [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) peut être définie sur une vue qui contient plusieurs vues enfants.

## <a name="choose-an-emptyview-at-runtime"></a>Choisir un EmptyView au moment de l’exécution

Les vues qui s’affichent sous la forme d’un [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) lorsque les données ne sont pas disponibles, peuvent être définies en tant qu’objets [`ContentView`](xref:Xamarin.Forms.ContentView) dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). La propriété `EmptyView` peut ensuite être définie sur un `ContentView`spécifique, en fonction de la logique métier, au moment de l’exécution. L’exemple de code XAML suivant montre un exemple de ce scénario :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:viewmodels="clr-namespace:CarouselViewDemos.ViewModels"
             x:Class="CarouselViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.BindingContext>
        <viewmodels:MonkeysViewModel />
    </ContentPage.BindingContext>
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <SearchBar SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CarouselView x:Name="carouselView"
                      ItemsSource="{Binding Monkeys}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Ce code XAML définit deux objets [`ContentView`](xref:Xamarin.Forms.ContentView) dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)au niveau de la page, avec l’objet [`Switch`](xref:Xamarin.Forms.Switch) contrôlant l’objet `ContentView` qui sera défini comme valeur de la propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Lorsque le [`Switch`](xref:Xamarin.Forms.Switch) est basculé, le gestionnaire d’événements `OnEmptyViewSwitchToggled` exécute la méthode `ToggleEmptyView` :

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

La méthode `ToggleEmptyView` définit la propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) de l’objet `carouselView` sur l’un des deux objets [`ContentView`](xref:Xamarin.Forms.ContentView) stockés dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), en fonction de la valeur de la propriété [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) . Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute la `FilterCommand`, la collection affichée par l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la propriété [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Si l’opération de filtrage ne génère pas de données, l’objet `ContentView` défini comme `EmptyView` propriété est affiché.

Pour plus d’informations sur les dictionnaires de ressources, consultez [dictionnaires de ressources Xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Choisir un EmptyViewTemplate au moment de l’exécution

L’apparence de l' [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) peut être choisie au moment de l’exécution, en fonction de sa valeur, en affectant à la propriété [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) la valeur d’un objet [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <CarouselView ItemsSource="{Binding Monkeys}"
                      EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                      EmptyViewTemplate="{StaticResource SearchSelector}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Le code C# équivalent est :

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView()
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) est définie sur la propriété [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) et la propriété [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) est définie sur un objet `SearchTermDataTemplateSelector`.

Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute la `FilterCommand`, la collection affichée par l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la propriété [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) . Si l’opération de filtrage ne produit aucune donnée, le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) choisi par l’objet `SearchTermDataTemplateSelector` est défini en tant que propriété [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) et affiché.

L’exemple suivant illustre la classe `SearchTermDataTemplateSelector` :

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

La classe `SearchTermTemplateSelector` définit les propriétés de `DefaultTemplate` et de `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui sont définies sur des modèles de données différents. La substitution `OnSelectTemplate` retourne `DefaultTemplate`, qui affiche un message à l’utilisateur, lorsque la requête de recherche n’est pas égale à « xamarin ». Lorsque la requête de recherche est égale à « xamarin », le `OnSelectTemplate` override retourne `OtherTemplate`, qui affiche un message de base à l’utilisateur.

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dictionnaires de ressources Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Créer un DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
