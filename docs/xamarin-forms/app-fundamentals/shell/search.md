---
title : " Xamarin.Forms Description de l’interpréteur de commandes" : "les Xamarin.Forms applications Shell peuvent utiliser la fonctionnalité de recherche intégrée qui est fournie par une zone de recherche qui peut être ajoutée en haut de chaque page."
ms. Prod : xamarin ms. AssetID : F8F9471D-6771-4D23-96C0-2B79473A06D4 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 12/18/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-shell-search"></a>Xamarin.FormsRecherche de Shell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.FormsL’interpréteur de commandes comprend une fonctionnalité de recherche intégrée qui est fournie par la `SearchHandler` classe. Une fonction de recherche peut être ajoutée à une page en définissant la propriété jointe `Shell.SearchHandler` sur un objet `SearchHandler` sous-classé. Une zone de recherche est alors ajoutée en haut de la page :

[![Capture d’écran d’un shell SearchHandler, sur iOS et Android](search-images/searchhandler.png "Shell SearchHandler")](search-images/searchhandler-large.png#lightbox "Shell SearchHandler")

Lorsque vous saisissez une requête dans la zone de recherche, la propriété `Query` est mise à jour et la méthode `OnQueryChanged` est exécutée à chaque mise à jour. Cette méthode peut être substituée pour remplir la zone des suggestions de recherche avec des données :

[![Capture d’écran des résultats de la recherche dans un shell SearchHandler, sur iOS et Android](search-images/search-suggestions.png "Résultats de la recherche dans le shell SearchHandler")](search-images/search-suggestions-large.png#lightbox "Résultats de la recherche dans le shell SearchHandler")

Ensuite, lorsqu’un résultat est sélectionné dans la zone des suggestions de recherche, la méthode `OnItemSelected` est exécutée. Cette méthode peut être substituée pour réagir de façon appropriée, par exemple, en accédant à une page de détail.

## <a name="create-a-searchhandler"></a>Créer un SearchHandler

Une fonctionnalité de recherche peut être ajoutée à une application Shell en sous-classant la classe `SearchHandler` et en remplaçant les méthodes `OnQueryChanged` et `OnItemSelected` :

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

La substitution `OnQueryChanged` a deux arguments : `oldValue`, qui contient la requête de recherche précédente, et `newValue`, qui contient la requête de recherche actuelle. La zone des suggestions de recherche peut être mise à jour en définissant la propriété `SearchHandler.ItemsSource` sur une collection `IEnumerable` contenant les éléments qui correspondent à la requête de recherche actuelle.

Lorsqu’un résultat de recherche est sélectionné par l’utilisateur, la substitution `OnItemSelected` est exécutée et la propriété `SelectedItem` est définie. Dans cet exemple, la méthode accède à une autre page qui affiche des données concernant l’élément `Animal` sélectionné. Pour plus d’informations sur la navigation, consultez navigation dans le [ Xamarin.Forms Shell](navigation.md).

> [!NOTE]
> Des propriétés `SearchHandler` supplémentaires peuvent être définies pour contrôler l’apparence de la zone de recherche.

## <a name="consume-a-searchhandler"></a>Consommer un SearchHandler

L’élément `SearchHandler` sous-classé peut être consommé en définissant la propriété jointe `Shell.SearchHandler` sur un objet du type sous-classé :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Le code C# équivalent est :

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

La méthode `MonkeySearchHandler.OnQueryChanged` retourne un `List` des objets `Animal`. La propriété `DisplayMemberName` est définie sur la propriété `Name` de chaque objet `Animal`, et par conséquent, les données affichées dans la zone de suggestions correspondront à chaque nom d’animal.

La propriété `ShowsResults` est définie sur `true`, de sorte que les suggestions de recherche sont affichées lorsque l’utilisateur saisit une requête de recherche :

[![Capture d’écran des résultats de la recherche dans un shell SearchHandler, sur iOS et Android](search-images/search-results.png "Résultats de la recherche dans le shell SearchHandler")](search-images/search-results-large.png#lightbox "Résultats de la recherche dans le shell SearchHandler")

À chaque changement de la requête de recherche, la zone de suggestions est mise à jour :

[![Capture d’écran des résultats de la recherche dans un shell SearchHandler, sur iOS et Android](search-images/search-results-change.png "Résultats de la recherche dans le shell SearchHandler")](search-images/search-results-change-large.png#lightbox "Résultats de la recherche dans le shell SearchHandler")

Lorsqu’un résultat de recherche est sélectionné, la page `MonkeyDetailPage` s’affiche, et les données relatives au singe sélectionné apparaissent :

[![Capture d’écran des détails du singe, sur iOS et Android](search-images/detailpage.png "Détails du singe")](search-images/detailpage-large.png#lightbox "Détails du singe")

## <a name="define-search-results-item-appearance"></a>Définir l’apparence d’un élément des résultats de la recherche

Outre l’affichage `string` des données dans les résultats de la recherche, l’apparence de chaque élément de résultat de recherche peut être définie en affectant `SearchHandler.ItemTemplate` à la propriété [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Le code C# équivalent est :

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Les éléments spécifiés dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque élément dans la zone des suggestions. Dans cet exemple, la disposition dans le `DataTemplate` est gérée par un [`Grid`](xref:Xamarin.Forms.Grid) . Le `Grid` contient un [`Image`](xref:Xamarin.Forms.Image) objet, et un [`Label`](xref:Xamarin.Forms.Label) objet, qui sont tous deux liés à des propriétés de chaque `Monkey` objet.

Les captures d’écran suivantes montrent le résultat de la création d’un modèle pour chaque élément de la zone de suggestions :

[![Capture d’écran des résultats de recherche basés sur un modèle dans un shell SearchHandler, sur iOS et Android](search-images/search-results-template.png "Résultats de recherche basés sur un modèle de Shell SearchHandler")](search-images/search-results-template-large.png#lightbox "Résultats de recherche basés sur un modèle de Shell SearchHandler")

Pour plus d’informations sur les modèles de données, consultez [ Xamarin.Forms modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="search-box-visibility"></a>Visibilité de la zone de recherche

Lorsqu’un élément `SearchHandler` est ajouté en haut d’une page, par défaut, la zone de recherche est visible et complètement développée. Toutefois, vous pouvez changer ce comportement en définissant la propriété `SearchHandler.SearchBoxVisibility` sur un des membres d’énumération `SearchBoxVisibility` :

- `Hidden` : la zone de recherche n’est pas visible ou accessible.
- `Collapsible` : la zone de recherche est masquée jusqu’à ce que l’utilisateur effectue une action pour l’afficher. Sur iOS, la zone de recherche est révélée en rebondissant verticalement le contenu de la page et, sur Android, la zone de recherche est affichée en appuyant sur l’icône de point d’interrogation.
- `Expanded` : la zone de recherche est visible et complètement développée. C’est la valeur par défaut de la propriété `SearchHandler.SearchBoxVisibility`.

> [!IMPORTANT]
> Sur iOS, une zone de recherche réductible requiert iOS 11 ou une version ultérieure.

L’exemple suivant montre comment masquer la zone de recherche :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="search-box-focus"></a>Focus de la zone de recherche

Lorsque vous appuyez dans une zone de recherche, vous appelez le clavier à l’écran et la zone de recherche obtient le focus d’entrée. Vous pouvez également procéder par programmation en appelant la méthode `Focus` qui tente de définir le focus d’entrée sur la zone de recherche et retourne `true` en cas de réussite. Lorsqu’une zone de recherche obtient le focus, l’événement `Focus` est déclenché et la méthode `OnFocused` substituable est appelée.

Quand une zone de recherche dispose d’un focus d’entrée, vous pouvez masquer le clavier à l’écran en appuyant sur un autre endroit de l’écran. La zone de recherche perd alors le focus d’entrée. Vous pouvez également procéder par programmation en appelant la méthode `Unfocus`. Lorsqu’une zone de recherche perd le focus, l’événement `Unfocused` est déclenché et la méthode `OnUnfocus` substituable est appelée.

L’état de focus d’une zone de recherche peut être récupéré via la propriété `IsFocused` qui retourne `true` si un élément `SearchHandler` dispose actuellement du focus d’entrée.

## <a name="searchhandler-appearance"></a>Apparence de SearchHandler

La classe `SearchHandler` définit les propriétés suivantes destinées à modifier son apparence :

- `BackgroundColor`, de type `Color` : couleur d’arrière-plan pour le texte de la zone recherche.
- `CancelButtonColor`, de type `Color` : couleur du bouton Annuler.
- `CharacterSpacing`, de type `double` , est l’espacement entre les caractères du `SearchHandler` texte.
- `FontAttributes`, de type `FontAttributes` : indique si le texte de la zone de recherche est en italique ou en gras.
- `FontFamily`, de type `string` : famille de polices utilisée pour le texte de la zone de recherche.
- `FontSize`, de type `double` : taille du texte de la zone de recherche.
- `HorizontalTextAlignment`, de type `TextAlignment` : alignement horizontal du texte de la zone de recherche.
- `PlaceholderColor`, de type `Color` : couleur pour l’espace réservé du texte de la zone de recherche.
- `TextColor`, de type `Color` : couleur pour le texte de la zone de recherche.
- `VerticalTextAlignment`, de type `TextAlignment` , est l’alignement vertical du texte de la zone de recherche.

## <a name="searchhandler-keyboard"></a>Clavier SearchHandler

Le clavier présenté lorsque les utilisateurs interagissent avec un `SearchHandler` peut être défini par programmation via la `Keyboard` propriété, à l’une des propriétés suivantes à partir de la [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe :

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat): utilisé pour le texte et les emplacements où les Emoji sont utiles.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default): clavier par défaut.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)– utilisé lors de la saisie des adresses e-mail.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric): utilisé lors de l’entrée de nombres.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain): utilisé lors de l’entrée de texte, sans aucun [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) spécifié.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)– utilisé lors de l’entrée de numéros de téléphone.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text): utilisé lors de la saisie de texte.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url): permet d’entrer des chemins de fichier & des adresses Web.

Cela peut être accompli en XAML de la façon suivante :

```xaml
<SearchHandler Keyboard="Email" />
```

Le code C# équivalent est :

```csharp
SearchHandler searchHandler = new SearchHandler { Keyboard = Keyboard.Email };
```

La [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe a également une [`Create`](xref:Xamarin.Forms.Keyboard.Create*) méthode de fabrique qui peut être utilisée pour personnaliser un clavier en spécifiant le comportement de mise en majuscules, de vérification orthographique et de suggestion. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)les valeurs d’énumération sont spécifiées comme arguments de la méthode, avec un personnalisé `Keyboard` qui est retourné. L’énumération `KeyboardFlags` contient les valeurs suivantes :

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None): aucune fonctionnalité n’est ajoutée au clavier.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence): indique que la première lettre du premier mot de chaque phrase entrée est automatiquement mise en majuscules.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck): indique que l’orthographe est effectuée sur le texte entré.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions): indique que les saisies semi-automatiques de mot seront proposées dans le texte entré.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord): indique que la première lettre de chaque mot sera automatiquement mise en majuscules.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter): indique que chaque caractère sera automatiquement mis en majuscules.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone): indique qu’aucune mise en majuscule automatique n’aura lieu.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All): indique que l’orthographe, la saisie semi-automatique des mots et la mise en majuscule des phrases se produisent dans le texte entré.

L’exemple de code XAML suivant montre comment personnaliser la valeur par défaut [`Keyboard`](xref:Xamarin.Forms.Keyboard) pour proposer des saisies semi-automatiques de mots et mettre en majuscules chaque caractère entré :

```xaml
<SearchHandler Placeholder="Enter search terms">
    <SearchHandler.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </SearchHandler.Keyboard>
</SearchHandler>
```

Le code C# équivalent est :

```csharp
SearchHandler searchHandler = new SearchHandler { Placeholder = "Enter search terms" };
searchHandler.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="searchhandler-reference"></a>Référence SearchHandler

La classe `SearchHandler` définit les propriétés suivantes, qui contrôlent son apparence et son comportement :

- `BackgroundColor`, de type `Color` : couleur d’arrière-plan pour le texte de la zone recherche.
- `CancelButtonColor`, de type `Color` : couleur du bouton Annuler.
- `ClearIcon`, de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , l’icône affichée pour effacer le contenu de la zone de recherche.
- `ClearIconHelpText`, de type `string` : texte d’aide accessible pour l’icône d’effacement.
- `ClearIconName`, de type `string` : nom de l’icône d’effacement pour une utilisation avec des lecteurs d’écran.
- `ClearPlaceholderCommand`, de type `ICommand` : exécutée lorsque vous activez l’option `ClearPlaceholderIcon`.
- `ClearPlaceholderCommandParameter`, de type `object` : paramètre passé à la commande `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, de type `bool` : détermine si la commande `ClearPlaceholderCommand` peut être exécutée. La valeur par défaut est `true`.
- `ClearPlaceholderHelpText`, de type `string` : texte d’aide accessible pour l’icône de l’espace réservé pour l’effacement.
- `ClearPlaceholderIcon`, de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , l’icône d’espace réservé Clear s’affiche lorsque la zone de recherche est vide.
- `ClearPlaceholderName`, de type `string` : nom de l’icône de l’espace réservé pour l’effacement, à utiliser avec des lecteurs d’écran.
- `Command`, de type `ICommand` : exécutée lorsque la requête de recherche est confirmée.
- `CommandParameter`, de type `object` : paramètre passé à la commande `Command`.
- `DisplayMemberName`, de type `string` : représente le nom ou le chemin d’accès de la propriété, affiché pour chaque élément de données de la collection `ItemsSource`.
- `FontAttributes`, de type `FontAttributes` : indique si le texte de la zone de recherche est en italique ou en gras.
- `FontFamily`, de type `string` : famille de polices utilisée pour le texte de la zone de recherche.
- `FontSize`, de type `double` : taille du texte de la zone de recherche.
- `HorizontalTextAlignment`, de type `TextAlignment` : alignement horizontal du texte de la zone de recherche.
- `IsFocused`, de type `bool` : indique si un `SearchHandler` dispose actuellement d’un focus d’entrée.
- `IsSearchEnabled`, de type `bool` : représente l’état activé de la zone de recherche. La valeur par défaut est `true`.
- `ItemsSource`, de type `IEnumerable` : spécifie la collection d’éléments à afficher dans la zone de suggestion, et dont la valeur par défaut est `null`.
- `ItemTemplate`, de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , spécifie le modèle à appliquer à chaque élément de la collection d’éléments à afficher dans la zone de suggestion.
- `Keyboard`, de type `Keyboard` : clavier pour le `SearchHandler`.
- `Placeholder`, de type `string` : texte à afficher lorsque la zone de recherche est vide.
- `PlaceholderColor`, de type `Color` : couleur pour l’espace réservé du texte de la zone de recherche.
- `Query`, de type `string` : texte saisi par l’utilisateur dans la zone de recherche.
- `QueryIcon`, de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , l’icône utilisée pour indiquer à l’utilisateur que la recherche est disponible.
- `QueryIconHelpText`, de type `string` : texte d’aide accessible pour l’icône de la requête.
- `QueryIconName`, de type `string` : nom de l’icône de la requête à utiliser avec des lecteurs d’écran.
- `SearchBoxVisibility`, de type `SearchBoxVisibility` : visibilité de la zone de recherche. Par défaut, la zone de recherche est visible et complètement développée.
- `SelectedItem`, de type `object` : élément sélectionné dans les résultats de la recherche. Cette propriété est en lecture seule et sa la valeur par défaut est `null`.
- `ShowsResults`, de type `bool` : indique si les résultats de la recherche doivent être attendus dans la zone de suggestion, lors de la saisie du texte. La valeur par défaut est `false`.
- `TextColor`, de type `Color` : couleur pour le texte de la zone de recherche.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

En outre, la classe `SearchHandler` fournit les méthodes substituables suivantes :

- `OnClearPlaceholderClicked` : appelée chaque fois que l’option `ClearPlaceholderIcon` est activée.
- `OnItemSelected` : appelée chaque fois qu’un résultat de recherche est sélectionné par l’utilisateur.
- `OnFocused` : appelée quand un `SearchHandler` obtient un focus d’entrée.
- `OnQueryChanged` : appelée lorsque les propriétés `Query` changent.
- `OnQueryConfirmed` : appelée chaque fois que l’utilisateur appuie sur entrée ou confirme sa requête dans la zone de recherche.
- `OnUnfocus` : appelée quand un `SearchHandler` perd un focus d’entrée.

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsNavigation dans le shell](navigation.md)
