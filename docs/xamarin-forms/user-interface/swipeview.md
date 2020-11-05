---
title: Xamarin.Forms SwipeView
description: Le Xamarin.Forms SwipeView est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c9ebee7914d8d2ccfb284910f31d87999b22701f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369647"
---
# <a name="no-locxamarinforms-swipeview"></a>Xamarin.Forms SwipeView

![API de la version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

Le `SwipeView` est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage :

[![Capture d’écran des éléments de balayage SwipeView dans un CollectionView, sur iOS et Android](swipeview-images/swipeview-collectionview.png "SwipeView les éléments de balayage")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView les éléments de balayage")

`SwipeView` est disponible dans Xamarin.Forms 4,4. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre classe `AppDelegate` sur iOS, à votre `MainActivity` classe sur Android, ou à votre `App` classe sur UWP, avant d’appeler `Forms.Init` :

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` définit les propriétés suivantes :

- `LeftItems`, de type `SwipeItems` , qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du côté gauche.
- `RightItems`, de type `SwipeItems` , qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du côté droit.
- `TopItems`, de type `SwipeItems` , qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du haut vers le haut.
- `BottomItems`, de type `SwipeItems` , qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du bas vers le haut.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

En outre, le `SwipeView` hérite de la [`Content`](xref:Xamarin.Forms.ContentView.Content) propriété de la [`ContentView`](xref:Xamarin.Forms.ContentView) classe. La `Content` propriété est la propriété de contenu de la `SwipeView` classe et n’a donc pas besoin d’être explicitement définie.

La `SwipeView` classe définit également quatre événements :

- `SwipeStarted` est déclenché au démarrage d’un balayage. L' `SwipeStartedEventArgs` objet qui accompagne cet événement a une `SwipeDirection` propriété, de type `SwipeDirection` .
- `SwipeChanging` est déclenché lors du déplacement du balayage. L' `SwipeChangingEventArgs` objet qui accompagne cet événement a une `SwipeDirection` propriété, de type `SwipeDirection` et une `Offset` propriété de type `double` .
- `SwipeEnded` est déclenché lorsqu’un balayage se termine. L' `SwipeEndedEventArgs` objet qui accompagne cet événement a une `SwipeDirection` propriété, de type `SwipeDirection` .
- `CloseRequested` est déclenché lorsque les éléments de balayage sont fermés.

En outre, `SwipeView` comprend `Open` les `Close` méthodes et, qui ouvrent et ferment respectivement les éléments de balayage.

> [!NOTE]
> `SwipeView` a un spécifique à une plateforme sur iOS et Android, qui contrôle la transition utilisée lors de l’ouverture d’un `SwipeView` . Pour plus d’informations, consultez [mode de transition par balayage SwipeView sur iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) et [mode de transition par balayage SwipeView sur Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Créer un SwipeView

Un `SwipeView` doit définir le contenu autour duquel le `SwipeView` habille, ainsi que les éléments de balayage révélés par le mouvement de balayage. Les éléments de balayage sont un ou plusieurs `SwipeItem` objets placés dans l’une des quatre `SwipeView` Collections directionnelles `LeftItems` ,, `RightItems` `TopItems` ou `BottomItems` .

L’exemple suivant montre comment instancier un `SwipeView` en XAML :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

Le code C# équivalent est :

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

Dans cet exemple, le `SwipeView` contenu est un [`Grid`](xref:Xamarin.Forms.Grid) qui contient un [`Label`](xref:Xamarin.Forms.Label) :

[![Capture d’écran du contenu SwipeView, sur iOS et Android](swipeview-images/swipeview-content.png "Contenu SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenu SwipeView")

Les éléments de balayage sont utilisés pour effectuer des actions sur le `SwipeView` contenu et sont révélés lorsque le contrôle est extrait du côté gauche :

[![Capture d’écran des éléments de balayage SwipeView, sur iOS et Android](swipeview-images/swipeview-swipeitems.png "SwipeView les éléments de balayage")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView les éléments de balayage")

Par défaut, un élément balayer est exécuté lorsqu’il est exploité par l’utilisateur. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [mode de balayage](#swipe-mode).

Une fois qu’un élément balayer a été exécuté, les éléments de balayage sont masqués et le `SwipeView` contenu s’affiche à nouveau. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [comportement de balayage](#swipe-behavior).

> [!NOTE]
> Le contenu de balayage et les éléments de balayage peuvent être placés inline ou définis en tant que ressources.

## <a name="swipe-items"></a>Balayer les éléments

Les `LeftItems` Collections,, `RightItems` `TopItems` et `BottomItems` sont toutes de type `SwipeItems` . La `SwipeItems` classe définit les propriétés suivantes :

- `Mode`, de type `SwipeMode` , qui indique l’effet d’une interaction de balayage. Pour plus d’informations sur le mode de balayage, consultez [mode balayage](#swipe-mode).
- `SwipeBehaviorOnInvoked`, de type `SwipeBehaviorOnInvoked` , qui indique comment un `SwipeView` se comporte après l’appel d’un élément de balayage. Pour plus d’informations sur le comportement de balayage, consultez [comportement de balayage](#swipe-behavior).

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Chaque élément balayer est défini en tant qu' `SwipeItem` objet placé dans l’une des quatre `SwipeItems` Collections directionnelles. La `SwipeItem` classe dérive de la [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe et ajoute les membres suivants :

- `BackgroundColor`Propriété de type `Color` qui définit la couleur d’arrière-plan de l’élément balayer. Cette propriété est stockée par une propriété pouvant être liée.
- `Invoked`Événement qui est déclenché lors de l’exécution de l’élément de balayage.

> [!IMPORTANT]
> La [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe définit plusieurs propriétés, notamment `Command` , `CommandParameter` , `IconImageSource` et `Text` . Ces propriétés peuvent être définies sur un `SwipeItem` objet pour définir son apparence, et pour définir un `ICommand` qui s’exécute lorsque l’élément balayer est appelé. Pour plus d’informations, consultez [ Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

L’exemple suivant montre deux `SwipeItem` objets dans la `LeftItems` collection d’un objet `SwipeView` :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

L’apparence de chaque `SwipeItem` est définie par une combinaison des `Text` Propriétés, `IconImageSource` et `BackgroundColor` :

[![Capture d’écran des éléments de balayage SwipeView, sur iOS et Android](swipeview-images/swipeview-swipeitems.png "SwipeView les éléments de balayage")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView les éléments de balayage")

Lorsqu’un `SwipeItem` est frappé, son `Invoked` événement se déclenche et est géré par son gestionnaire d’événements inscrit. La `Command` propriété peut également être définie sur une `ICommand` implémentation qui sera exécutée lorsque le `SwipeItem` sera appelé.

> [!NOTE]
> Lorsque l’apparence d’un `SwipeItem` est définie uniquement à l’aide des `Text` `IconImageSource` Propriétés ou, le contenu est toujours centré.

En plus de définir des éléments de balayage en tant qu' `SwipeItem` objets, il est également possible de définir des vues d’éléments de balayage personnalisées. Pour plus d’informations, consultez [éléments de balayage personnalisés](#custom-swipe-items).

## <a name="swipe-direction"></a>Sens de balayage

`SwipeView` prend en charge quatre directions de balayage différentes, la direction de balayage étant définie par la `SwipeItems` collection directionnelle à laquelle les `SwipeItem` objets sont ajoutés. Chaque sens de balayage peut contenir ses propres éléments de balayage. Par exemple, l’exemple suivant montre un `SwipeView` dont les éléments de balayage dépendent de la direction de balayage :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

Dans cet exemple, le `SwipeView` contenu peut être balayé vers la droite ou la gauche. Le balayage vers la droite affiche l’élément de balayage **supprimer** , tandis que le balayage vers la gauche affiche les éléments de balayage du **favori** et du **partage** .

> [!WARNING]
> Une seule instance d’une `SwipeItems` collection directionnelle peut être définie à la fois sur un `SwipeView` . Par conséquent, vous ne pouvez pas avoir deux `LeftItems` définitions sur un `SwipeView` .

Les `SwipeStarted` `SwipeChanging` événements, et `SwipeEnded` signalent la direction de balayage via la `SwipeDirection` propriété dans les arguments d’événement. Cette propriété est de type `SwipeDirection` , qui est une énumération composée de quatre membres :

- `Right` indique qu’un balayage à droite s’est produit.
- `Left` indique qu’un balayage à gauche s’est produit.
- `Up` indique qu’un balayage vers le haut s’est produit.
- `Down` indique qu’un balayage vers le bas s’est produit.

## <a name="swipe-mode"></a>Mode balayage

La `SwipeItems` classe a une `Mode` propriété, qui indique l’effet d’une interaction de balayage. Cette propriété doit être définie sur l’un des `SwipeMode` membres de l’énumération :

- `Reveal` indique qu’un balayage révèle les éléments de balayage. C’est la valeur par défaut de la propriété `SwipeItems.Mode`.
- `Execute` indique qu’un balayage exécute les éléments de balayage.

En mode d’affichage, l’utilisateur effectue un balayage `SwipeView` pour ouvrir un menu constitué d’un ou de plusieurs éléments de balayage et doit appuyer explicitement sur un élément balayer pour l’exécuter. Après l’exécution de l’élément de balayage, les éléments de balayage sont fermés et le `SwipeView` contenu est à nouveau affiché. En mode exécution, l’utilisateur effectue un balayage `SwipeView` pour ouvrir un menu constitué d’un ou plusieurs éléments de balayage, qui sont ensuite exécutés automatiquement. Après l’exécution, les éléments de balayage sont fermés et le `SwipeView` contenu s’affiche à nouveau.

L’exemple suivant montre un `SwipeView` configuré pour utiliser le mode d’exécution :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

Dans cet exemple, le `SwipeView` contenu peut être balayé directement pour révéler l’élément balayer qui est exécuté immédiatement. Après l’exécution, le `SwipeView` contenu s’affiche à nouveau.

## <a name="swipe-behavior"></a>Comportement de balayage

La `SwipeItems` classe a une `SwipeBehaviorOnInvoked` propriété, qui indique comment un `SwipeView` se comporte après l’appel d’un élément de balayage. Cette propriété doit être définie sur l’un des `SwipeBehaviorOnInvoked` membres de l’énumération :

- `Auto` indique que dans le mode d’affichage `SwipeView` , le se ferme après l’appel d’un élément de balayage, et en mode exécution, le `SwipeView` reste ouvert après l’appel d’un élément de balayage. C’est la valeur par défaut de la propriété `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close` indique que le `SwipeView` se ferme après l’appel d’un élément de balayage.
- `RemainOpen` indique que le `SwipeView` reste ouvert après l’appel d’un élément de balayage.

L’exemple suivant montre un `SwipeView` configuré pour rester ouvert après l’appel d’un élément de balayage :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>Éléments de balayage personnalisés

Vous pouvez définir des éléments de balayage personnalisés avec le `SwipeItemView` type. La `SwipeItemView` classe dérive de la [`ContentView`](xref:Xamarin.Forms.ContentView) classe et ajoute les propriétés suivantes :

- `Command`, de type `ICommand` , qui est exécuté lorsqu’un élément balayer est frappé.
- `CommandParameter`, de type `object` : paramètre passé à la commande `Command`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La `SwipeItemView` classe définit également un `Invoked` événement qui est déclenché lorsque l’élément est frappé, après l' `Command` exécution de.

L’exemple suivant montre un `SwipeItemView` objet dans la `LeftItems` collection d’un objet `SwipeView` :

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

Dans cet exemple, le `SwipeItemView` comprend un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui contient un [`Entry`](xref:Xamarin.Forms.Entry) et un [`Label`](xref:Xamarin.Forms.Label) . Une fois que l’utilisateur a entré l’entrée dans le `Entry` , le reste du `SwipeViewItem` peut être taraudé et exécuter le `ICommand` défini par la `SwipeItemView.Command` propriété.

## <a name="open-and-close-a-swipeview-programmatically"></a>Ouvrir et fermer un SwipeView par programmation

`SwipeView` comprend `Open` les `Close` méthodes et, qui ouvrent et ferment respectivement les éléments de balayage.

La `Open` méthode requiert un `OpenSwipeItem` argument pour spécifier la direction `SwipeView` à partir de laquelle le sera ouvert. L' `OpenSwipeItem` énumération a quatre membres :

- `LeftItems`, qui indique que le `SwipeView` sera ouvert à partir de la gauche, pour afficher les éléments de balayage dans la `LeftItems` collection.
- `TopItems`, qui indique que le `SwipeView` sera ouvert à partir du haut, pour afficher les éléments de balayage dans la `TopItems` collection.
- `RightItems`, qui indique que le `SwipeView` sera ouvert à partir de la droite pour afficher les éléments de balayage dans `RightItems` la collection.
- `BottomItems`, qui indique que le `SwipeView` sera ouvert à partir du bas pour afficher les éléments de balayage dans la `BottomItems` collection.

À partir d’un `SwipeView` nommé `swipeView` , l’exemple suivant montre comment ouvrir un `SwipeView` pour afficher les éléments de balayage dans la `LeftItems` collection :

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

La `swipeView` méthode peut ensuite être fermée à l’aide de la `Close` méthode :

```csharp
swipeView.Close();
```

> [!NOTE]
> Lorsque la `Close` méthode est appelée, l' `CloseRequested` événement est déclenché.

## <a name="disable-a-swipeview"></a>Désactiver un SwipeView

Une application peut entrer dans un État dans lequel le balayage d’un élément de contenu n’est pas une opération valide. Dans ce cas, le `SwipeView` peut être désactivé en affectant `IsEnabled` à sa propriété la valeur `false` . Cela empêchera les utilisateurs d’être en mesure de balayer le contenu pour révéler des éléments balayés.

En outre, lorsque vous définissez la `Command` propriété d’un ou d’un `SwipeItem` `SwipeItemView` , le `CanExecute` délégué du `ICommand` peut être spécifié pour activer ou désactiver l’élément balayer.

## <a name="related-links"></a>Liens connexes

- [SwipeView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)