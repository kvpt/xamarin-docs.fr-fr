---
title: Xamarin. Forms SearchBar
description: Xamarin. Forms SearchBar est un contrôle d’entrée utilisateur utilisé pour lancer une recherche. Le contrôle SearchBar prend en charge le texte de l’espace réservé, l’entrée de requête, l’exécution et l’annulation. Cet article explique comment utiliser un SearchBar en XAML et du code.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: 8888f1615f250a908930cec9058a54bd6e7fedc2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490075"
---
# <a name="xamarinforms-searchbar"></a>Xamarin. Forms SearchBar

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Xamarin. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) est un contrôle d’entrée utilisateur utilisé pour lancer une recherche. Le contrôle `SearchBar` prend en charge le texte de l’espace réservé, l’entrée de requête, l’exécution de la recherche et l’annulation. La capture d’écran suivante montre une requête `SearchBar` avec les résultats affichés dans une `ListView`:

[![Capture d’écran de SearchBar sur iOS et Android](searchbar-images/device-searchbars-cropped.png "SearchBar sur iOS et Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar sur iOS et Android")

La classe `SearchBar` définit les propriétés suivantes :

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) est un `Color` qui définit la couleur du bouton Annuler.
* `CharacterSpacing`, de type `double`, est l’espacement entre les caractères du texte de `SearchBar`.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes) est une valeur d’énumération `FontAttributes` qui détermine si la police `SearchBar` est en gras, en italique ou aucune.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily) est un `string` qui détermine la famille de polices utilisée par le `SearchBar`.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize) peut être une valeur d’énumération `NamedSize` ou une valeur `double` qui représente des tailles de police spécifiques sur plusieurs plateformes.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment) est une valeur d’énumération `TextAlignment` qui définit l’alignement horizontal du texte de la requête.
* `VerticalTextAlignment` est une valeur d’énumération `TextAlignment` qui définit l’alignement vertical du texte de la requête.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder) est un `string` qui définit le texte de l’espace réservé, par exemple « Search... ».
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor) est un `Color` qui définit la couleur du texte de l’espace réservé.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) est un `ICommand` qui permet de lier des actions de l’utilisateur, telles que des clics ou clics, à des commandes définies sur un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) est un `object` qui spécifie le paramètre qui doit être passé au `SearchCommand`.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text) est un `string` contenant le texte de la requête dans le `SearchBar`.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor) est un `Color` qui définit la couleur du texte de la requête.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que la `SearchBar` peut être personnalisée et être la cible des liaisons de données. La spécification des propriétés de police sur la `SearchBar` est cohérente avec la personnalisation du texte sur d’autres [contrôles de texte Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md). Pour plus d’informations, consultez [polices dans Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Créer un SearchBar

Un `SearchBar` peut être instancié en XAML. Sa propriété `Placeholder` facultative peut être définie pour définir le texte d’indication dans la zone d’entrée de requête. La valeur par défaut de la `Placeholder` est une chaîne vide. aucun espace réservé ne s’affiche s’il n’est pas défini. L’exemple suivant montre comment instancier un `SearchBar` en XAML avec la propriété facultative `Placeholder` définie :

```xaml
<SearchBar Placeholder="Search items..." />
```

Vous pouvez également créer un `SearchBar` dans le code :

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propriétés d’apparence SearchBar

Le contrôle `SearchBar` définit de nombreuses propriétés qui personnalisent l’apparence du contrôle. L’exemple suivant montre comment instancier une `SearchBar` en XAML avec plusieurs propriétés spécifiées :

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Ces propriétés peuvent également être spécifiées lors de la création d’un objet `SearchBar` dans le code :

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

La capture d’écran suivante montre le contrôle de `SearchBar` qui en résulte :

[![Capture d’écran des SearchBar personnalisés sur iOS et Android](searchbar-images/device-searchbars-styled-cropped.png "SearchBar personnalisé sur iOS et Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personnalisé sur iOS et Android")

> [!NOTE]
> Sur iOS, la classe `SearchBarRenderer` contient une méthode de `UpdateCancelButton` substituable. Cette méthode contrôle le moment où le bouton Annuler apparaît et peut être substitué dans un convertisseur personnalisé. Pour plus d’informations sur les convertisseurs personnalisés, consultez [convertisseurs personnalisés Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Effectuer une recherche avec des gestionnaires d’événements

Une recherche peut être exécutée à l’aide du contrôle `SearchBar` en attachant un gestionnaire d’événements à l’un des événements suivants :

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) est appelée quand l’utilisateur clique sur le bouton de recherche ou appuie sur la touche entrée.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) est appelée chaque fois que le texte de la zone de requête est modifié.

L’exemple suivant montre un gestionnaire d’événements attaché à l’événement `TextChanged` en XAML et utilise un `ListView` pour afficher les résultats de la recherche :

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Un gestionnaire d’événements peut également être attaché à un `SearchBar` créé dans le code :

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

Le gestionnaire d’événements `TextChanged` dans le fichier code-behind est le même, que le `SearchBar` soit créé via du code XAML ou :

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

L’exemple précédent implique l’existence d’une classe `DataService` avec une méthode `GetSearchResults` capable de retourner des éléments qui correspondent à une requête. La valeur de la propriété `Text` du contrôle `SearchBar` est passée à la méthode `GetSearchResults` et le résultat est utilisé pour mettre à jour la propriété `ItemsSource` du contrôle `ListView`. L’effet global est que les résultats de la recherche sont affichés dans le contrôle `ListView`.

L’exemple d’application fournit une implémentation de classe `DataService` qui peut être utilisée pour tester la fonctionnalité de recherche.

## <a name="perform-a-search-using-a-viewmodel"></a>Effectuer une recherche à l’aide d’un ViewModel

Une recherche peut être exécutée sans gestionnaires d’événements en liant les propriétés `SearchCommand` et `SearchCommandParameter` aux implémentations de `ICommand`. L’exemple de projet illustre ces implémentations à l’aide du modèle MVVM (Model-View-ViewModel). Pour plus d’informations sur les liaisons de données avec MVVM, consultez [liaisons de données avec MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

Le ViewModel dans l’exemple d’application contient le code suivant :

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
> Le ViewModel part du principe que l’existence d’une classe `DataService` est susceptible d’effectuer des recherches. La classe `DataService`, y compris des exemples de données, est disponible dans l’exemple d’application.

Le code XAML suivant montre comment lier un `SearchBar` à l’exemple de ViewModel, avec un contrôle `ListView` affichant les résultats de la recherche :

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

Cet exemple définit le `BindingContext` comme étant une instance de la classe `SearchViewModel`. Elle lie la propriété `SearchCommand` à la `PerformSearch` `ICommand` dans le ViewModel et lie la propriété `SearchBar` `Text` à la propriété `SearchCommandParameter`. La propriété `ListView.ItemsSource` est liée à la propriété `SearchResults` du ViewModel.

Pour plus d’informations sur l’interface de `ICommand` et les liaisons, consultez [liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md) et [interface ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Liens connexes

* [Démonstrations SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Contrôles de texte Xamarin. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Polices dans Xamarin. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
