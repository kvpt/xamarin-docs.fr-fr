---
title: Xamarin.FormsCarouselView EmptyView
description: Dans CarouselView, vous pouvez spécifier une vue vide qui fournit des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.
ms.prod: xamarin
ms.assetid: C6DEE1A9-63FC-4889-BC77-F401D5D7DF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0dd1f4a15d9a8c1315cd3f0f71b641c82bebe004
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918655"
---
# <a name="no-locxamarinforms-carouselview-emptyview"></a>Xamarin.FormsCarouselView EmptyView

![API de la version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)définit les propriétés suivantes qui peuvent être utilisées pour fournir des commentaires de l’utilisateur lorsqu’il n’y a pas de données à afficher :

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), de type `object` , la chaîne, la liaison ou la vue qui s’affiche lorsque la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété a la valeur `null` ou est vide. La valeur par défaut est `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , le modèle à utiliser pour mettre en forme le spécifié `EmptyView` . La valeur par défaut est `null`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Les scénarios d’utilisation principaux pour la définition de la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriété affichent les commentaires des utilisateurs lorsqu’une opération de filtrage sur un [`CarouselView`](xref:Xamarin.Forms.CarouselView) ne produit pas de données et affiche les commentaires des utilisateurs pendant que les données sont récupérées à partir d’un service Web.

> [!NOTE]
> La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriété peut être définie sur une vue qui comprend du contenu interactif, si nécessaire.

Pour plus d’informations sur les modèles de données, consultez [ Xamarin.Forms modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Afficher une chaîne lorsque les données ne sont pas disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriété peut être définie sur une chaîne, qui est affichée lorsque la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété a la valeur ou est `null` vide. Le code XAML suivant illustre un exemple de ce scénario :

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

Le résultat est que, étant donné que la collection liée aux données est `null` , la chaîne définie en tant que [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valeur de propriété est affichée.

## <a name="display-views-when-data-is-unavailable"></a>Afficher les affichages quand les données ne sont pas disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriété peut être définie sur une vue, qui sera affichée lorsque la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. Il peut s’agir d’une vue unique ou d’une vue contenant plusieurs vues enfants. L’exemple de code XAML suivant montre `EmptyView` que la propriété est définie sur une vue qui contient plusieurs vues enfants :

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

Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute `FilterCommand` , la collection affichée par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriété. Si l’opération de filtrage ne génère pas de données, le [`StackLayout`](xref:Xamarin.Forms.StackLayout) jeu en tant que [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valeur de propriété est affiché.

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Afficher un type personnalisé basé sur un modèle lorsque les données ne sont pas disponibles

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriété peut être définie sur un type personnalisé, dont le modèle est affiché lorsque la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété a la valeur `null` , ou lorsque la collection spécifiée par la `ItemsSource` propriété est `null` ou est vide. La [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriété peut être définie sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence du `EmptyView` . Le code XAML suivant illustre un exemple de ce scénario :

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

Le `FilterData` type définit une `Filter` propriété et un correspondant [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) :

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

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) propriété est définie sur un `FilterData` objet et les `Filter` données de propriété sont liées à la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriété. Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute `FilterCommand` , la collection affichée par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la `Filter` propriété. Si l’opération de filtrage ne produit aucune donnée, le [`Label`](xref:Xamarin.Forms.Label) défini dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui est défini comme [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) valeur de propriété, est affiché.

> [!NOTE]
> Lorsque vous affichez un type personnalisé basé sur un modèle lorsque les données ne sont pas disponibles, la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriété peut être définie sur une vue qui contient plusieurs vues enfants.

## <a name="choose-an-emptyview-at-runtime"></a>Choisir un EmptyView au moment de l’exécution

Les vues qui s’affichent sous la forme d’un [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) lorsque les données ne sont pas disponibles, peuvent être définies en tant qu' [`ContentView`](xref:Xamarin.Forms.ContentView) objets dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . La `EmptyView` propriété peut ensuite être définie sur un spécifique `ContentView` , en fonction de la logique métier, au moment de l’exécution. L’exemple de code XAML suivant montre un exemple de ce scénario :

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

Ce code XAML définit deux [`ContentView`](xref:Xamarin.Forms.ContentView) objets au niveau de la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , l' [`Switch`](xref:Xamarin.Forms.Switch) objet contrôlant l’objet qui sera `ContentView` défini comme [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valeur de propriété. Quand [`Switch`](xref:Xamarin.Forms.Switch) est basculé, le `OnEmptyViewSwitchToggled` Gestionnaire d’événements exécute la `ToggleEmptyView` méthode :

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

La `ToggleEmptyView` méthode affecte [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) à la propriété de l' `carouselView` objet l’un des deux [`ContentView`](xref:Xamarin.Forms.ContentView) objets stockés dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , en fonction de la valeur de la [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) propriété. Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute `FilterCommand` , la collection affichée par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriété. Si l’opération de filtrage ne génère pas de données, l' `ContentView` objet défini en tant que `EmptyView` propriété est affiché.

Pour plus d’informations sur les dictionnaires de ressources, consultez [ Xamarin.Forms dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Choisir un EmptyViewTemplate au moment de l’exécution

L’apparence du [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) peut être choisie au moment de l’exécution, en fonction de sa valeur, en affectant [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) à la propriété un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objet :

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

La propriété [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) a la valeur de [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) la propriété, et la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriété a la valeur d’un `SearchTermDataTemplateSelector` objet.

Lorsque le [`SearchBar`](xref:Xamarin.Forms.SearchBar) exécute `FilterCommand` , la collection affichée par le [`CarouselView`](xref:Xamarin.Forms.CarouselView) est filtrée pour le terme de recherche stocké dans la [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) propriété. Si l’opération de filtrage ne produit aucune donnée, le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) choisi par l' `SearchTermDataTemplateSelector` objet est défini en tant que [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) propriété et affiché.

L’exemple suivant illustre la `SearchTermDataTemplateSelector` classe :

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

La `SearchTermTemplateSelector` classe définit `DefaultTemplate` et les `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriétés qui sont définies sur des modèles de données différents. La `OnSelectTemplate` substitution retourne `DefaultTemplate` , qui affiche un message à l’utilisateur, lorsque la requête de recherche n’est pas égale à « xamarin ». Lorsque la requête de recherche est égale à « xamarin », la `OnSelectTemplate` substitution retourne `OtherTemplate` , ce qui affiche un message de base à l’utilisateur.

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.FormsModèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.FormsDictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Créer un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
