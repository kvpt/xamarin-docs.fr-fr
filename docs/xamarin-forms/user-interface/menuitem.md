---
title: Xamarin. Forms, MenuItem
description: La classe MenuItem est utilisée pour créer des éléments de menu pour les menus tels que les menus contextuels d’élément ListView et les menus volants d’application Shell.
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 5bc36f03eac4ced7c19a0053dfea93dbe2ca4497
ms.sourcegitcommit: 850dd7a3ed10eb3f66692e765d3e31438cff0288
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531007"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. Forms, MenuItem

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

La classe Xamarin. Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) définit les éléments de menu des menus tels que `ListView` menus contextuels d’élément et les menus volants de l’application Shell.

Les captures d’écran suivantes montrent des objets `MenuItem` dans un menu contextuel `ListView` sur iOS et Android :

[![« MenuItems sur iOS et Android »](menuitem-images/menuitem-demo-cropped.png "MenuItems sur iOS et Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItems sur iOS et Android full image")

La classe `MenuItem` définit les propriétés suivantes :

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) est un `ICommand` qui permet de lier des actions de l’utilisateur, telles que des clics ou clics, à des commandes définies sur un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) est un `object` qui spécifie le paramètre qui doit être passé au `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) est une valeur `ImageSource` qui définit l’icône d’affichage.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) est une valeur `bool` qui indique si l' `MenuItem` supprime l’élément d’interface associé de la liste.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled) est une valeur `bool` qui détermine si cet objet répond à une entrée utilisateur.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) est une valeur de `string` qui spécifie le texte à afficher.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , de sorte que l’instance `MenuItem` peut être la cible des liaisons de données.

## <a name="create-a-menuitem"></a>Créer un MenuItem

les objets `MenuItem` peuvent être utilisés dans un menu contextuel sur les éléments d’un objet `ListView`. Le modèle le plus courant consiste à créer des objets `MenuItem` dans une instance de `ViewCell`, qui est utilisée comme objet `DataTemplate` pour la `ItemTemplate` `ListView`s. Lorsque l’objet `ListView` est rempli, il crée chaque élément à l’aide de la `DataTemplate`, exposant ainsi les `MenuItem` choix lorsque le menu contextuel est activé pour un élément.

L’exemple suivant montre `MenuItem` instanciation dans le contexte d’un objet `ListView` :

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

Vous pouvez également créer un `MenuItem` dans le code :

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

La classe `MenuItem` expose un événement `Clicked`. Un gestionnaire d’événements peut être joint à cet événement pour réagir à des clics ou des clics sur l’instance `MenuItem` en XAML :

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

Un gestionnaire d’événements peut également être joint dans le code :

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

Les exemples précédents référençaient un gestionnaire d’événements `OnItemClicked`. Le code suivant illustre un exemple d’implémentation :

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

La classe `MenuItem` prend en charge le modèle MVVM (Model-View-ViewModel) via des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) et l’interface `ICommand`. Le code XAML suivant montre `MenuItem` instances liées aux commandes définies sur un ViewModel :

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

Dans l’exemple précédent, deux objets `MenuItem` sont définis avec leurs propriétés `Command` et `CommandParameter` liées aux commandes du ViewModel. Le ViewModel contient les commandes référencées dans le XAML :

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

L’exemple d’application comprend une classe `DataService` utilisée pour obtenir une liste d’éléments pour le remplissage des objets `ListView`. Un ViewModel est instancié, avec les éléments de la classe `DataService` et défini comme `BindingContext` dans le code-behind :

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Icônes MenuItem

> [!WARNING]
> les objets `MenuItem` affichent uniquement les icônes sur Android. Sur les autres plateformes, seul le texte spécifié par la propriété `Text` s’affiche.

 Les icônes sont spécifiées à l’aide de la propriété `IconImageSource`. Si une icône est spécifiée, le texte spécifié par la propriété `Text` ne sera pas affiché. La capture d’écran suivante montre une `MenuItem` avec une icône sur Android :

![« Capture d’écran de l’icône MenuItem sur Android »](menuitem-images/menuitem-android-icon.png "Capture d’écran de l’icône MenuItem sur Android")

Pour plus d’informations sur l’utilisation d’images dans Xamarin. Forms, consultez [images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="cross-platform-context-menu-behavior"></a>Comportement du menu contextuel multiplateforme

Les menus contextuels sont accessibles et affichés différemment sur chaque plateforme.

Sur Android, le menu contextuel est activé par une pression longue sur un élément de liste. Le menu contextuel remplace le titre et la zone de barre de navigation, et les options de `MenuItem` s’affichent sous forme de boutons horizontaux.

![« Capture d’écran du menu contextuel sur Android »](menuitem-images/menuitem-android-icon.png "Capture d’écran du menu contextuel sur Android")

Sur iOS, le menu contextuel est activé par balayage sur un élément de liste. Le menu contextuel s’affiche sur l’élément de liste et `MenuItems` s’affichent sous forme de boutons horizontaux.

![« Capture d’écran du menu contextuel sur iOS »](menuitem-images/menuitem-ios-contextmenu.png "Capture d’écran du menu contextuel sur iOS")

Sur UWP, le menu contextuel est activé en cliquant avec le bouton droit sur un élément de liste. Le menu contextuel s’affiche à côté du curseur sous la forme d’une liste verticale.

![« Capture d’écran du menu contextuel sur UWP »](menuitem-images/menuitem-uwp.png "Capture d’écran du menu contextuel sur UWP")

## <a name="related-links"></a>Liens connexes

* [Démonstrations MenuItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
