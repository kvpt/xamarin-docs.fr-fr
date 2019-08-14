---
title: Xamarin. Forms, MenuItem
description: La classe MenuItem est utilisée pour créer des éléments de menu pour les menus tels que les menus contextuels d’élément ListView et les menus volants d’application Shell.
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 68560c6cc814f54bb8ba9348bc53334089c36a93
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68984471"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. Forms, MenuItem

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)

La classe Xamarin. [`MenuItem`](xref:Xamarin.Forms.MenuItem) Forms est utilisée pour définir des éléments de menu pour les `ListView` menus tels que les menus contextuels d’élément et les menus volants d’application Shell.

La capture d’écran `MenuItem` suivante montre les `ListView` objets dans un menu contextuel sur iOS et Android:

[ ![«MenuItems on iOS et Android»](menuitem-images/menuitem-demo-cropped.png "MenuItems sur iOS et Android") ] (menuitem-images/menuitem-demo-full.png#lightbox "MenuItems sur iOS et Androidfull image")

La `MenuItem` classe définit les propriétés suivantes:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)est un `ICommand` qui permet de lier des actions d’utilisateur, telles que des clics de doigt ou des clics, aux commandes définies sur un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)est un `object` qui spécifie le paramètre qui doit être passé à `Command`l'.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)`ImageSource` valeur qui définit l’icône d’affichage.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)est une `bool` valeur qui indique si l' `MenuItem` élément d’interface utilisateur associé est supprimé de la liste.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)`bool` valeur qui détermine si cet objet répond à l’entrée d’utilisateur.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)`string` valeur qui spécifie le texte affiché.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, `MenuItem` de sorte que l’instance peut être la cible des liaisons de données.

## <a name="create-a-menuitem"></a>Créer un MenuItem

`MenuItem`les objets peuvent être utilisés dans un menu contextuel `ListView` sur les éléments d’un objet. Le modèle le plus courant consiste à `MenuItem` créer des objets `ViewCell` dans une instance, `DataTemplate` qui est utilisée comme objet pour `ListView`les `ItemTemplate`. Lorsque l' `ListView` objet est rempli `DataTemplate`, il crée chaque élément à l’aide de, en `MenuItem` exposant les choix lorsque le menu contextuel est activé pour un élément.

L’exemple suivant illustre `MenuItem` l’instanciation dans le contexte `ListView` d’un objet:

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

Un `MenuItem` peut également être créé dans le code:

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

La classe `MenuItem` expose un événement `Clicked`. Un gestionnaire d’événements peut être joint à cet événement pour réagir à des clics ou des `MenuItem` clics sur l’instance en XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

Un gestionnaire d’événements peut également être joint dans le code:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

Les exemples précédents référençaient `OnItemClicked` un gestionnaire d’événements. Le code suivant illustre un exemple d’implémentation:

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

La `MenuItem` classe prend en charge le modèle MVVM (Model-View-ViewModel [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ) par le `ICommand` biais d’objets et de l’interface. Le code XAML suivant `MenuItem` montre des instances liées aux commandes définies sur un ViewModel:

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

Dans l’exemple précédent, deux `MenuItem` objets sont définis avec leurs `Command` propriétés `CommandParameter` et liées à des commandes sur le ViewModel. Le ViewModel contient les commandes référencées dans le XAML:

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

L’exemple d’application comprend `DataService` une classe utilisée pour obtenir une liste d’éléments pour le `ListView` remplissage des objets. Un ViewModel est instancié, avec les éléments de la `DataService` classe et défini `BindingContext` en tant que dans le code-behind:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Icônes MenuItem

> [!WARNING]
> `MenuItem`les objets affichent uniquement les icônes sur Android. Sur les autres plateformes, seul le texte spécifié `Text` par la propriété s’affiche.

 Les icônes sont spécifiées `IconImageSource` à l’aide de la propriété. Si une icône est spécifiée, le texte spécifié par la `Text` propriété ne sera pas affiché. La capture d’écran suivante `MenuItem` montre un avec une icône sur Android:

![«Capture d’écran de l’icône MenuItem sur Android»](menuitem-images/menuitem-android-icon.png "Capture d’écran de l’icône MenuItem sur Android")

Pour plus d’informations sur l’utilisation d’images dans Xamarin. Forms, consultez [images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="cross-platform-context-menu-behavior"></a>Comportement du menu contextuel multiplateforme

Les menus contextuels sont accessibles et affichés différemment sur chaque plateforme.

Sur Android, le menu contextuel est activé par une pression longue sur un élément de liste. Le menu contextuel remplace le titre et la zone de `MenuItem` barre de navigation, et les options sont affichées sous forme de boutons horizontaux.

![«Capture d’écran du menu contextuel sur Android»](menuitem-images/menuitem-android-icon.png "Capture d’écran du menu contextuel sur Android")

Sur iOS, le menu contextuel est activé par balayage sur un élément de liste. Le menu contextuel s’affiche sur l’élément de `MenuItems` liste et s’affiche sous forme de boutons horizontaux.

![«Capture d’écran du menu contextuel sur iOS»](menuitem-images/menuitem-ios-contextmenu.png "Capture d’écran du menu contextuel sur iOS")

Sur UWP, le menu contextuel est activé en cliquant avec le bouton droit sur un élément de liste. Le menu contextuel s’affiche à côté du curseur sous la forme d’une liste verticale.

![«Capture d’écran du menu contextuel sur UWP»](menuitem-images/menuitem-uwp.png "Capture d’écran du menu contextuel sur UWP")

## <a name="related-links"></a>Liens connexes

* [Démonstrations MenuItem](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)
* [Images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
