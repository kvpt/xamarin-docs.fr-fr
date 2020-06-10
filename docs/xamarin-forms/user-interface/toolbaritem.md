---
titre : « Xamarin.Forms ToolbarItem » Description : « la classe ToolbarItem est un type spécial de bouton utilisé dans la barre de navigation d’une application ».
ms. Prod : xamarin ms. assetId : CC737D54-0280-46BD-A2BC-A0FB67DDD6A1 ms. Technology : xamarin-Forms Author : profexorgeek ms. Author : jusjohns ms. Date : 07/29/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-toolbaritem"></a>Xamarin.FormsToolbarItem

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La Xamarin.Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) classe est un type spécial de bouton qui peut être ajouté à la `Page` collection d’un objet `ToolbarItems` . Chaque `ToolbarItem` objet apparaît sous la forme d’un bouton dans la barre de navigation de l’application. Une `ToolbarItem` instance peut avoir une icône et apparaître en tant qu’élément de menu principal ou secondaire. La `ToolbarItem` classe hérite de [`MenuItem`](xref:Xamarin.Forms.MenuItem) .

Les captures d’écran suivantes montrent des `ToolbarItem` objets dans la barre de navigation sur iOS et Android :

![Capture d’écran de démonstration de ToolbarItem sur Android et iOS](toolbaritem-images/toolbaritem-device-screenshot.png "Capture d’écran de démonstration de ToolbarItem sur Android et iOS")

La `ToolbarItem` classe définit les propriétés suivantes :

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)`ToolbarItemOrder`valeur enum qui détermine si l' `ToolbarItem` instance s’affiche dans le menu principal ou secondaire.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)`integer`valeur qui détermine l’ordre d’affichage des éléments dans la `Page` collection d’un objet `ToolbarItems` .

La `ToolbarItem` classe hérite des propriétés suivantes utilisées en général de la `MenuItem` classe :

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)est un `ICommand` qui permet de lier des actions d’utilisateur, telles que des clics de doigt ou des clics, aux commandes définies sur un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)est un `object` qui spécifie le paramètre qui doit être passé à l' `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)est une `ImageSource` valeur qui détermine l’icône d’affichage sur un `ToolbarItem` objet.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)est un `string` qui détermine le texte affiché sur un `ToolbarItem` objet.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’une `ToolbarItem` instance peut être la cible des liaisons de données.

> [!NOTE]
> Une alternative à la création d’une barre d’outils à partir d' [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objets consiste à définir la [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propriété jointe sur une classe de disposition qui contient plusieurs vues. Pour plus d’informations, consultez [affichage des affichages dans la barre de navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Créer un ToolbarItem

Un `ToolbarItem` objet peut être instancié en XAML. Les `Text` `IconImageSource` Propriétés et peuvent être définies pour déterminer le mode d’affichage du bouton dans la barre de navigation. L’exemple suivant montre comment instancier un `ToolbarItem` avec des propriétés communes définies, et l’ajouter à la `ContentPage` `ToolbarItems` collection de :

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Cet exemple génère un `ToolbarItem` objet qui contient du texte, une icône et s’affiche en premier dans la zone de la barre de navigation principale. Un `ToolbarItem` peut également être créé dans le code et ajouté à la `ToolbarItems` collection :

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

Le fichier représenté par le `string` , fourni en tant que `IconImageSource` propriété, doit exister dans chaque projet de plateforme.

> [!NOTE]
> Les ressources d’image sont gérées différemment sur chaque plateforme. Un `ImageSource` peut provenir de sources, notamment d’un fichier local ou d’une ressource incorporée, d’un URI ou d’un flux. Pour plus d’informations sur la définition de la `IconImageSource` propriété et des images dans Xamarin.Forms , consultez [images dans Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Définir le comportement du bouton

La `ToolbarItem` classe hérite `Clicked` de l’événement de la `MenuItem` classe. Un gestionnaire d’événements peut être attaché à l' `Clicked` événement pour réagir à des clics ou des clics sur des `ToolbarItem` instances en XAML :

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Un gestionnaire d’événements peut également être joint dans le code :

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Les exemples précédents référençaient un `OnItemClicked` Gestionnaire d’événements. Le code suivant illustre un exemple d’implémentation :

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`les objets peuvent également utiliser `Command` les `CommandParameter` Propriétés et pour réagir à l’entrée d’utilisateur sans gestionnaires d’événements. Pour plus d’informations sur l' `ICommand` interface et la liaison de données MVVM, consultez le [ Xamarin.Forms comportement de MenuItem MVVM](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>Activer ou désactiver un ToolbarItem au moment de l’exécution

Pour activer la désactivation d’un `ToolbarItem` lors de l’exécution, liez sa `Command` propriété à une `ICommand` implémentation et assurez-vous qu’un `canExecute` délégué active et désactive le `ICommand` en fonction des besoins.

Pour plus d’informations, consultez [activer ou désactiver un MenuItem au moment de l’exécution](menuitem.md#enable-or-disable-a-menuitem-at-runtime).

## <a name="primary-and-secondary-menus"></a>Menus principal et secondaire

L' `ToolbarItemOrder` énumération a des `Default` `Primary` valeurs, et `Secondary` .

Lorsque la `Order` propriété a la valeur `Primary` , l' `ToolbarItem` objet s’affiche dans la barre de navigation principale sur toutes les plateformes. `ToolbarItem`les objets sont classés par ordre de priorité sur le titre de la page, qui sera tronqué pour libérer de l’espace pour les éléments. Les captures d’écran suivantes montrent des `ToolbarItem` objets dans le menu principal sur iOS et Android :

![Capture d’écran du menu principal de ToolbarItem Android et iOS](toolbaritem-images/toolbaritem-primary-menu.png "Capture d’écran du menu principal de ToolbarItem sur Android et iOS")

Lorsque la `Order` propriété a la valeur `Secondary` , le comportement varie d’une plateforme à l’autre. Sur UWP et Android, le `Secondary` menu éléments apparaît sous la forme de trois points qui peuvent être activés ou sur lesquels vous pouvez cliquer pour afficher des éléments dans une liste verticale. Sur iOS, le `Secondary` menu éléments s’affiche sous la barre de navigation sous la forme d’une liste horizontale. Les captures d’écran suivantes montrent un menu secondaire sur iOS et Android :

![Capture d’écran du menu secondaire de ToolbarItem Android et iOS](toolbaritem-images/toolbaritem-secondary-menu.png "Capture d’écran du menu secondaire de ToolbarItem sur Android et iOS")

> [!WARNING]
> Le comportement de l’icône dans `ToolbarItem` les objets dont la propriété a la `Order` valeur `Secondary` est incohérent entre les plateformes. Évitez de définir la `IconImageSource` propriété sur les éléments qui s’affichent dans le menu secondaire.

## <a name="related-links"></a>Liens connexes

* [Démos de ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Images dansXamarin.Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.FormsMenuItem](~/xamarin-forms/user-interface/menuitem.md)
