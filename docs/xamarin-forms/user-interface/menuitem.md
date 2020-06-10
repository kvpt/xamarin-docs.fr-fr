---
title : " Xamarin.Forms MenuItem" Description : "la classe MenuItem est utilisée pour créer des éléments de menu pour les menus tels que les menus contextuels d’élément ListView et les menus volants d’application Shell."
ms. Prod : xamarin ms. assetId : 62655C21-6053-466D-A7F4-DE2BE36538F5 ms. Technology : xamarin-Forms Author : profexorgeek ms. Author : jusjohns ms. Date : 08/01/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-menuitem"></a>Xamarin.FormsMenuItem

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

La Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe définit les éléments de menu des menus tels que les menus `ListView` contextuels d’élément et les menus volants d’application Shell.

Les captures d’écran suivantes montrent des `MenuItem` objets dans un `ListView` menu contextuel sur iOS et Android :

[![« MenuItems sur iOS et Android »](menuitem-images/menuitem-demo-cropped.png "MenuItems sur iOS et Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItems sur iOS et Android image complète")

La `MenuItem` classe définit les propriétés suivantes :

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)est un `ICommand` qui permet de lier des actions d’utilisateur, telles que des clics de doigt ou des clics, aux commandes définies sur un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)est un `object` qui spécifie le paramètre qui doit être passé à l' `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)`ImageSource`valeur qui définit l’icône d’affichage.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)est une `bool` valeur qui indique si l' `MenuItem` élément d’interface utilisateur associé est supprimé de la liste.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)`bool`valeur qui indique si cet objet répond à l’entrée d’utilisateur.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)`string`valeur qui spécifie le texte affiché.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, de sorte que l' `MenuItem` instance peut être la cible des liaisons de données.

## <a name="create-a-menuitem"></a>Créer un MenuItem

`MenuItem`les objets peuvent être utilisés dans un menu contextuel sur les `ListView` éléments d’un objet. Le modèle le plus courant consiste à créer des `MenuItem` objets dans une `ViewCell` instance, qui est utilisée comme `DataTemplate` objet pour `ListView` les `ItemTemplate` . Lorsque l' `ListView` objet est rempli, il crée chaque élément à l’aide de `DataTemplate` , en exposant les `MenuItem` choix lorsque le menu contextuel est activé pour un élément.

L’exemple suivant illustre l' `MenuItem` instanciation dans le contexte d’un `ListView` objet :

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Un `MenuItem` peut également être créé dans le code :

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>Définir le comportement MenuItem avec les événements

La classe `MenuItem` expose un événement `Clicked`. Un gestionnaire d’événements peut être joint à cet événement pour réagir à des clics ou des clics sur l' `MenuItem` instance en XAML :

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

Un gestionnaire d’événements peut également être joint dans le code :

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

Les exemples précédents référençaient un `OnItemClicked` Gestionnaire d’événements. Le code suivant illustre un exemple d’implémentation :

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>Définir le comportement MenuItem avec MVVM

La `MenuItem` classe prend en charge le modèle MVVM (Model-View-ViewModel) par le biais d' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets et de l' `ICommand` interface. Le code XAML suivant montre des `MenuItem` instances liées aux commandes définies sur un ViewModel :

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

Dans l’exemple précédent, deux `MenuItem` objets sont définis avec leurs `Command` `CommandParameter` Propriétés et liées à des commandes sur le ViewModel. Le ViewModel contient les commandes référencées dans le XAML :

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

L’exemple d’application comprend une `DataService` classe utilisée pour obtenir une liste d’éléments pour le remplissage des `ListView` objets. Un ViewModel est instancié, avec les éléments de la `DataService` classe et défini en tant que `BindingContext` dans le code-behind :

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Icônes MenuItem

> [!WARNING]
> `MenuItem`les objets affichent uniquement les icônes sur Android. Sur les autres plateformes, seul le texte spécifié par la `Text` propriété s’affiche.

 Les icônes sont spécifiées à l’aide de la `IconImageSource` propriété. Si une icône est spécifiée, le texte spécifié par la `Text` propriété ne sera pas affiché. La capture d’écran suivante montre un `MenuItem` avec une icône sur Android :

![« Capture d’écran de l’icône MenuItem sur Android »](menuitem-images/menuitem-android-icon.png "Capture d’écran de l’icône MenuItem sur Android")

Pour plus d’informations sur l’utilisation d’images dans Xamarin.Forms , consultez [images dans Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>Activer ou désactiver un MenuItem au moment de l’exécution

Pour activer la désactivation d’un `MenuItem` lors de l’exécution, liez sa `Command` propriété à une `ICommand` implémentation et assurez-vous qu’un `canExecute` délégué active et désactive le `ICommand` en fonction des besoins.

> [!IMPORTANT]
> Ne liez pas la `IsEnabled` propriété à une autre propriété lorsque vous utilisez la `Command` propriété pour activer ou désactiver `MenuItem` .

L’exemple suivant montre un `MenuItem` dont la `Command` propriété est liée à un `ICommand` nommé `MyCommand` :

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

L' `ICommand` implémentation requiert un `canExecute` délégué qui retourne la valeur d’une `bool` propriété pour activer et désactiver le `MenuItem` :

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public ToolbarItemViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsToolbarItemEnabled);
    }
}
```

Dans cet exemple, `MenuItem` est désactivé jusqu’à ce que la `IsMenuItemEnabled` propriété soit définie. Lorsque cela se produit, la `Command.ChangeCanExecute` méthode est appelée, ce qui entraîne la `canExecute` `MyCommand` réévaluation du délégué.

## <a name="cross-platform-context-menu-behavior"></a>Comportement du menu contextuel multiplateforme

Les menus contextuels sont accessibles et affichés différemment sur chaque plateforme.

Sur Android, le menu contextuel est activé par une pression longue sur un élément de liste. Le menu contextuel remplace le titre et la zone de barre de navigation, et les `MenuItem` options sont affichées sous forme de boutons horizontaux.

![« Capture d’écran du menu contextuel sur Android »](menuitem-images/menuitem-android-icon.png "Capture d’écran du menu contextuel sur Android")

Sur iOS, le menu contextuel est activé par balayage sur un élément de liste. Le menu contextuel s’affiche sur l’élément de liste et s' `MenuItems` affiche sous forme de boutons horizontaux.

![« Capture d’écran du menu contextuel sur iOS »](menuitem-images/menuitem-ios-contextmenu.png "Capture d’écran du menu contextuel sur iOS")

Sur UWP, le menu contextuel est activé en cliquant avec le bouton droit sur un élément de liste. Le menu contextuel s’affiche à côté du curseur sous la forme d’une liste verticale.

![« Capture d’écran du menu contextuel sur UWP »](menuitem-images/menuitem-uwp.png "Capture d’écran du menu contextuel sur UWP")

## <a name="related-links"></a>Liens connexes

* [Démonstrations MenuItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Images dansXamarin.Forms](~/xamarin-forms/user-interface/images.md)
