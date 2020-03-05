---
title: Xamarin. Forms, ToolbarItem
description: La classe ToolbarItem est un type spécial de bouton utilisé dans la barre de navigation d’une application.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: afdf9029f836ac8e55b2bb338b31f669af946c12
ms.sourcegitcommit: 6d86aac422d6ce2131930d18ada161d117c8c61b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2020
ms.locfileid: "78291633"
---
# <a name="xamarinforms-toolbaritem"></a>Xamarin. Forms, ToolbarItem

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La classe Xamarin. Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) est un type spécial de bouton qui peut être ajouté à la collection `ToolbarItems` d’un objet `Page`. Chaque objet `ToolbarItem` s’affiche sous la forme d’un bouton dans la barre de navigation de l’application. Une instance `ToolbarItem` peut avoir une icône et apparaître en tant qu’élément de menu principal ou secondaire. La classe `ToolbarItem` hérite de [`MenuItem`](xref:Xamarin.Forms.MenuItem).

Les captures d’écran suivantes montrent des objets `ToolbarItem` dans la barre de navigation sur iOS et Android :

![Capture d’écran de démonstration de ToolbarItem sur Android et iOS](toolbaritem-images/toolbaritem-device-screenshot.png "Capture d’écran de démonstration de ToolbarItem sur Android et iOS")

La classe `ToolbarItem` définit les propriétés suivantes :

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) est une valeur d’énumération `ToolbarItemOrder` qui détermine si l’instance `ToolbarItem` s’affiche dans le menu principal ou secondaire.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority) est une valeur `integer` qui détermine l’ordre d’affichage des éléments dans la collection `ToolbarItems` d’un objet `Page`.

La classe `ToolbarItem` hérite des propriétés utilisées en général suivantes de la classe `MenuItem` :

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) est un `ICommand` qui permet de lier des actions de l’utilisateur, telles que des clics ou clics, à des commandes définies sur un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) est un `object` qui spécifie le paramètre qui doit être passé au `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) est une valeur `ImageSource` qui détermine l’icône d’affichage sur un objet `ToolbarItem`.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) est un `string` qui détermine le texte à afficher sur un objet `ToolbarItem`.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , de sorte qu’une instance `ToolbarItem` peut être la cible des liaisons de données.

> [!NOTE]
> Une alternative à la création d’une barre d’outils à partir d' [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objets consiste à définir la [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propriété jointe à une classe de disposition qui contient plusieurs vues. Pour plus d’informations, consultez [affichage des affichages dans la barre de navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Créer un ToolbarItem

Un objet `ToolbarItem` peut être instancié en XAML. Les propriétés `Text` et `IconImageSource` peuvent être définies pour déterminer le mode d’affichage du bouton dans la barre de navigation. L’exemple suivant montre comment instancier un `ToolbarItem` avec des propriétés communes définies, et l’ajouter à la collection de `ToolbarItems` d’un `ContentPage`:

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Cet exemple génère un objet `ToolbarItem` contenant du texte, une icône et s’affiche en premier dans la zone de la barre de navigation principale. Une `ToolbarItem` peut également être créée dans le code et ajoutée à la collection de `ToolbarItems` :

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

Le fichier représenté par le `string`, fourni comme `IconImageSource` propriété, doit exister dans chaque projet de plateforme.

> [!NOTE]
> Les ressources d’image sont gérées différemment sur chaque plateforme. Une `ImageSource` peut provenir de sources, notamment d’un fichier local ou d’une ressource incorporée, d’un URI ou d’un flux. Pour plus d’informations sur la définition de la propriété `IconImageSource` et des images dans Xamarin. Forms, consultez [images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Définir le comportement du bouton

La classe `ToolbarItem` hérite de l’événement `Clicked` de la classe `MenuItem`. Un gestionnaire d’événements peut être attaché à l’événement `Clicked` pour réagir à des pressions ou des clics sur des instances de `ToolbarItem` en XAML :

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Un gestionnaire d’événements peut également être joint dans le code :

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Les exemples précédents référençaient un gestionnaire d’événements `OnItemClicked`. Le code suivant illustre un exemple d’implémentation :

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

les objets `ToolbarItem` peuvent également utiliser les propriétés `Command` et `CommandParameter` pour réagir aux entrées d’utilisateur sans gestionnaires d’événements. Pour plus d’informations sur l’interface `ICommand` et la liaison de données MVVM, consultez [comportement de Xamarin. Forms MenuItem MVVM](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>Activer ou désactiver un ToolbarItem au moment de l’exécution

Pour activer la désactivation d’un `ToolbarItem` au moment de l’exécution, liez sa propriété `Command` à une implémentation de `ICommand` et assurez-vous qu’un délégué `canExecute` active et désactive le `ICommand`, le cas échéant.

Pour plus d’informations, consultez [activer ou désactiver un MenuItem au moment de l’exécution](menuitem.md#enable-or-disable-a-menuitem-at-runtime).

## <a name="primary-and-secondary-menus"></a>Menus principal et secondaire

L’énumération `ToolbarItemOrder` a des valeurs `Default`, `Primary`et `Secondary`.

Lorsque la propriété `Order` est définie sur `Primary`, l’objet `ToolbarItem` apparaît dans la barre de navigation principale sur toutes les plateformes. `ToolbarItem` objets sont classés par ordre de priorité sur le titre de la page, qui sera tronqué pour libérer de l’espace pour les éléments. Les captures d’écran suivantes montrent `ToolbarItem` objets dans le menu principal sur iOS et Android :

![Capture d’écran du menu principal de ToolbarItem Android et iOS](toolbaritem-images/toolbaritem-primary-menu.png "Capture d’écran du menu principal de ToolbarItem sur Android et iOS")

Lorsque la propriété `Order` est définie sur `Secondary`, le comportement varie d’une plateforme à l’autre. Sur UWP et Android, le menu `Secondary` éléments s’affiche sous la forme de trois points qui peuvent être frappés ou cliqués pour afficher des éléments dans une liste verticale. Sur iOS, le menu `Secondary` éléments s’affiche sous la barre de navigation sous la forme d’une liste horizontale. Les captures d’écran suivantes montrent un menu secondaire sur iOS et Android :

![Capture d’écran du menu secondaire de ToolbarItem Android et iOS](toolbaritem-images/toolbaritem-secondary-menu.png "Capture d’écran du menu secondaire de ToolbarItem sur Android et iOS")

> [!WARNING]
> Comportement des icônes dans `ToolbarItem` objets dont la propriété `Order` a la valeur `Secondary` n’est pas cohérente entre les plateformes. Évitez de définir la propriété `IconImageSource` sur les éléments qui s’affichent dans le menu secondaire.

## <a name="related-links"></a>Liens connexes

* [Démos de ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
