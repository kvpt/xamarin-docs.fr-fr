---
title: Xamarin. Forms SearchBar
description: Xamarin. Forms SearchBar est un contrôle d’entrée utilisateur utilisé pour lancer une recherche. Le contrôle SearchBar prend en charge le texte de l’espace réservé, l’entrée de requête, l’exécution et l’annulation. Cet article explique comment utiliser un SearchBar en XAML et du code.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/12/2019
ms.openlocfilehash: 391820cf2e94c1131f4082798ee9efa05d8489b8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739402"
---
# <a name="xamarinforms-searchbar"></a>Xamarin. Forms SearchBar

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) est un contrôle d’entrée utilisateur utilisé pour lancer une recherche. Le `SearchBar` contrôle prend en charge le texte de l’espace réservé, l’entrée de requête, l’exécution de la recherche et l’annulation. La capture d’écran suivante `SearchBar` montre une requête avec les résultats `ListView`affichés dans un:

[ ![Capture d’écran de Searchbar sur iOS et Android](searchbar-images/device-searchbars-cropped.png "Searchbar sur iOS et Android") ] (searchbar-images/device-searchbars.png#lightbox "Searchbar sur iOS et Android")

`SearchBar` Définit les propriétés suivantes:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)est un `Color` qui définit la couleur du bouton Annuler.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)valeur enum qui détermine si la `SearchBar` police est en gras, en italique ou aucune des valeurs. `FontAttributes`
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)est un `string` qui détermine la famille de polices utilisée par `SearchBar`le.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)peut être une `NamedSize` valeur d’énumération `double` ou une valeur qui représente des tailles de police spécifiques sur plusieurs plateformes.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)`TextAlignment` valeur enum qui définit l’alignement horizontal du texte de la requête.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder)est un `string` qui définit le texte de l’espace réservé, par exemple «Search...».
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor)est un `Color` qui définit la couleur du texte de l’espace réservé.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)est un `ICommand` qui permet de lier des actions d’utilisateur, telles que des clics de doigt ou des clics, aux commandes définies sur un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)est un `object` qui spécifie le paramètre qui doit être passé à `SearchCommand`l'.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text)est un `string` qui contient le texte de la `SearchBar`requête dans le.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor)est un `Color` qui définit la couleur du texte de la requête.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui `SearchBar` signifie que peut être personnalisé et être la cible des liaisons de données. La spécification des propriétés de `SearchBar` police sur le est cohérente avec la personnalisation du texte sur d’autres [contrôles de texte Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md). Pour plus d’informations, consultez [polices dans Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Créer un SearchBar

Un `SearchBar` peut être instancié en XAML. Sa propriété `Placeholder` facultative peut être définie pour définir le texte d’indication dans la zone d’entrée de requête. La valeur par défaut de `Placeholder` est une chaîne vide, de sorte qu’aucun espace réservé ne s’affiche s’il n’est pas défini. L’exemple suivant montre comment instancier un `SearchBar` en XAML avec le jeu `Placeholder` de propriétés facultatif:

```xaml
<SearchBar Placeholder="Search items..." />
```

Un `SearchBar` peut également être créé dans le code:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propriétés d’apparence SearchBar

Le `SearchBar` contrôle définit de nombreuses propriétés qui personnalisent l’apparence du contrôle. L’exemple suivant montre comment instancier un `SearchBar` en XAML avec plusieurs propriétés spécifiées:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Ces propriétés peuvent également être spécifiées lors de `SearchBar` la création d’un dans le code:

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

La capture d’écran suivante montre `SearchBar`le résultat obtenu:

[ ![Capture d’écran des Searchbar personnalisés sur iOS et Android](searchbar-images/device-searchbars-styled-cropped.png "personnalisé Searchbar sur iOS et Android") ] (searchbar-images/device-searchbars-styled.png#lightbox "Searchbar personnalisé sur iOS et Android")

## <a name="perform-a-search-with-event-handlers"></a>Effectuer une recherche avec des gestionnaires d’événements

Une recherche peut être exécutée à `SearchBar` l’aide du contrôle en attachant un gestionnaire d’événements à l’un des événements suivants:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)est appelé lorsque l’utilisateur clique sur le bouton de recherche ou appuie sur la touche entrée.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged)est appelé chaque fois que le texte de la zone de requête est modifié.

L’exemple suivant montre un gestionnaire d’événements attaché à `TextChanged` l’événement en XAML et utilise `ListView` un pour afficher les résultats de la recherche:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Un gestionnaire d’événements peut également être attaché à `SearchBar` un créé dans le code:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

Le `TextChanged` gestionnaire d’événements dans le fichier code-behind est le même, que `SearchBar` le soit créé via du code XAML ou:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

L’exemple précédent implique l’existence d’une `DataService` classe avec une `GetSearchResults` méthode capable de retourner des éléments qui correspondent à une requête. La `SearchBar` valeur de `Text` propriété du contrôle est passée à `GetSearchResults` la méthode et le résultat est utilisé pour mettre `ListView` à jour `ItemsSource` la propriété du contrôle. L’effet global est que les résultats de la recherche sont `ListView` affichés dans le contrôle.

L’exemple d’application fournit `DataService` une implémentation de classe qui peut être utilisée pour tester la fonctionnalité de recherche.

## <a name="perform-a-search-using-a-viewmodel"></a>Effectuer une recherche à l’aide d’un ViewModel

Une recherche peut être exécutée sans gestionnaires d’événements en liant `SearchCommand` les `SearchCommandParameter` propriétés et `ICommand` aux implémentations. L’exemple de projet illustre ces implémentations à l’aide du modèle MVVM (Model-View-ViewModel). Pour plus d’informations sur les liaisons de données avec MVVM, consultez [liaisons de données avec MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

Le ViewModel dans l’exemple d’application contient le code suivant:

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> Le ViewModel suppose l’existence d’une `DataService` classe susceptible d’effectuer des recherches. La `DataService` classe, y compris des exemples de données, est disponible dans l’exemple d’application.

Le code XAML suivant montre comment lier un `SearchBar` à l’exemple de ViewModel, avec `ListView` un contrôle affichant les résultats de la recherche:

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

Cet exemple définit le `BindingContext` comme étant une instance de la `SearchViewModel` classe. Il `SearchCommand` lie la `SearchCommandParameter` propriété `PerformSearch` `SearchBar` à dans le ViewModel et lie la `Text` propriété à la propriété. `ICommand` La `ListView.ItemsSource` propriété est liée à la `SearchResults` propriété du ViewModel.

Pour plus d’informations sur `ICommand` l’interface et les liaisons, consultez [liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md) et [l’interface ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Liens connexes

* [Démonstrations SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Contrôles de texte Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Polices dans Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
