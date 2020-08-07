---
title: Xamarin.FormsSearchBar
description: Le Xamarin.Forms Searchbar est un contrôle d’entrée utilisateur utilisé pour lancer une recherche. Le contrôle SearchBar prend en charge le texte de l’espace réservé, l’entrée de requête, l’exécution et l’annulation. Cet article explique comment utiliser un SearchBar en XAML et du code.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a4c782f95db0f8e777494c47e9e668e9af67a2cc
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917771"
---
# <a name="no-locxamarinforms-searchbar"></a>Xamarin.FormsSearchBar

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Le Xamarin.Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) est un contrôle d’entrée utilisateur utilisé pour lancer une recherche. Le `SearchBar` contrôle prend en charge le texte de l’espace réservé, l’entrée de requête, l’exécution de la recherche et l’annulation. La capture d’écran suivante montre une `SearchBar` requête avec les résultats affichés dans un `ListView` :

[![Capture d’écran de SearchBar sur iOS et Android](searchbar-images/device-searchbars-cropped.png "SearchBar sur iOS et Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar sur iOS et Android")

La `SearchBar` classe définit les propriétés suivantes :

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)est un `Color` qui définit la couleur du bouton Annuler.
* `CharacterSpacing`, de type `double` , est l’espacement entre les caractères du `SearchBar` texte.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)`FontAttributes`valeur enum qui détermine si la `SearchBar` police est en gras, en italique ou aucune des valeurs.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)est un `string` qui détermine la famille de polices utilisée par le `SearchBar` .
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)peut être une `NamedSize` valeur d’énumération ou une `double` valeur qui représente des tailles de police spécifiques sur plusieurs plateformes.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)`TextAlignment`valeur enum qui définit l’alignement horizontal du texte de la requête.
* `VerticalTextAlignment``TextAlignment`valeur enum qui définit l’alignement vertical du texte de la requête.
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)est un `string` qui définit le texte de l’espace réservé, par exemple « Search... ».
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)est un `Color` qui définit la couleur du texte de l’espace réservé.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)est un `ICommand` qui permet de lier des actions d’utilisateur, telles que des clics de doigt ou des clics, aux commandes définies sur un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)est un `object` qui spécifie le paramètre qui doit être passé à l' `SearchCommand` .
* [`Text`](xref:Xamarin.Forms.InputView.Text)est un `string` qui contient le texte de la requête dans le `SearchBar` .
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)est un `Color` qui définit la couleur du texte de la requête.
* `TextTransform`est une `TextTransform` valeur qui détermine la casse du `SearchBar` texte.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que `SearchBar` peut être personnalisé et être la cible des liaisons de données. La spécification des propriétés de police sur le `SearchBar` est cohérente avec la personnalisation du texte sur d’autres [ Xamarin.Forms contrôles de texte](~/xamarin-forms/user-interface/text/index.md). Pour plus d’informations, consultez [polices Xamarin.Forms dans ](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Créer un SearchBar

Un `SearchBar` peut être instancié en XAML. Sa `Placeholder` propriété facultative peut être définie pour définir le texte d’indication dans la zone d’entrée de requête. La valeur par défaut de `Placeholder` est une chaîne vide, de sorte qu’aucun espace réservé ne s’affiche s’il n’est pas défini. L’exemple suivant montre comment instancier un `SearchBar` en XAML avec le `Placeholder` jeu de propriétés facultatif :

```xaml
<SearchBar Placeholder="Search items..." />
```

Un `SearchBar` peut également être créé dans le code :

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Propriétés d’apparence SearchBar

Le `SearchBar` contrôle définit de nombreuses propriétés qui personnalisent l’apparence du contrôle. L’exemple suivant montre comment instancier un `SearchBar` en XAML avec plusieurs propriétés spécifiées :

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           TextTransform="Lowercase"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

Ces propriétés peuvent également être spécifiées lors de la création `SearchBar` d’un objet dans le code :

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    TextTransform = TextTransform.Lowercase,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

La capture d’écran suivante montre le `SearchBar` contrôle obtenu :

[![Capture d’écran des SearchBar personnalisés sur iOS et Android](searchbar-images/device-searchbars-styled-cropped.png "SearchBar personnalisé sur iOS et Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personnalisé sur iOS et Android")

> [!NOTE]
> Sur iOS, la `SearchBarRenderer` classe contient une méthode substituable `UpdateCancelButton` . Cette méthode contrôle le moment où le bouton Annuler apparaît et peut être substitué dans un convertisseur personnalisé. Pour plus d’informations sur les convertisseurs personnalisés, consultez [ Xamarin.Forms convertisseurs personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Effectuer une recherche avec des gestionnaires d’événements

Une recherche peut être exécutée à l’aide du `SearchBar` contrôle en attachant un gestionnaire d’événements à l’un des événements suivants :

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)est appelé lorsque l’utilisateur clique sur le bouton de recherche ou appuie sur la touche entrée.
* [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)est appelé chaque fois que le texte de la zone de requête est modifié.

L’exemple suivant montre un gestionnaire d’événements attaché à l' `TextChanged` événement en XAML et utilise un `ListView` pour afficher les résultats de la recherche :

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Un gestionnaire d’événements peut également être attaché à un `SearchBar` créé dans le code :

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

Le `TextChanged` Gestionnaire d’événements dans le fichier code-behind est le même, que `SearchBar` le soit créé via du code XAML ou :

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

L’exemple précédent implique l’existence d’une `DataService` classe avec une `GetSearchResults` méthode capable de retourner des éléments qui correspondent à une requête. La `SearchBar` valeur de `Text` propriété du contrôle est passée à la `GetSearchResults` méthode et le résultat est utilisé pour mettre à jour la `ListView` propriété du contrôle `ItemsSource` . L’effet global est que les résultats de la recherche sont affichés dans le `ListView` contrôle.

L’exemple d’application fournit une `DataService` implémentation de classe qui peut être utilisée pour tester la fonctionnalité de recherche.

## <a name="perform-a-search-using-a-viewmodel"></a>Effectuer une recherche à l’aide d’un ViewModel

Une recherche peut être exécutée sans gestionnaires d’événements en liant `SearchCommand` les `SearchCommandParameter` Propriétés et aux `ICommand` implémentations. L’exemple de projet illustre ces implémentations à l’aide du modèle MVVM (Model-View-ViewModel). Pour plus d’informations sur les liaisons de données avec MVVM, consultez [liaisons de données avec MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

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
> Le ViewModel suppose l’existence d’une `DataService` classe susceptible d’effectuer des recherches. La `DataService` classe, y compris des exemples de données, est disponible dans l’exemple d’application.

Le code XAML suivant montre comment lier un `SearchBar` à l’exemple de ViewModel, avec un `ListView` contrôle affichant les résultats de la recherche :

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
                  ItemsSource="{Binding SearchResults}" />
    </StackLayout>
</ContentPage>
```

Cet exemple définit le `BindingContext` comme étant une instance de la `SearchViewModel` classe. Il lie la `SearchCommand` propriété à `PerformSearch` `ICommand` dans le ViewModel et lie la `SearchBar` `Text` propriété à la `SearchCommandParameter` propriété. La `ListView.ItemsSource` propriété est liée à la `SearchResults` propriété du ViewModel.

Pour plus d’informations sur l' `ICommand` interface et les liaisons, consultez [ Xamarin.Forms liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md) et [interface ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Liens connexes

* [Démonstrations SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Xamarin.FormsContrôles de texte](~/xamarin-forms/user-interface/text/index.md)
* [Polices dansXamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Xamarin.Formsliaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
