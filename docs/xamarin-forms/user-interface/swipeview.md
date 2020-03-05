---
title: Xamarin. Forms SwipeView
description: Xamarin. Forms SwipeView est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage.
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 6131287b200846a033e0c476d7039dfd774cab68
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292207"
---
# <a name="xamarinforms-swipeview"></a>Xamarin. Forms SwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

Le `SwipeView` est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel qui sont révélés par un mouvement de balayage :

[![Capture d’écran des éléments de balayage SwipeView dans un CollectionView, sur iOS et Android](swipeview-images/swipeview-collectionview.png "SwipeView les éléments de balayage")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView les éléments de balayage")

`SwipeView` est disponible dans Xamarin. Forms 4,4. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre classe `AppDelegate` sur iOS, à votre classe `MainActivity` sur Android, ou à votre classe `App` sur UWP, avant d’appeler `Forms.Init`:

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` définit les propriétés suivantes :

- `LeftItems`, de type `SwipeItems`, qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du côté gauche.
- `RightItems`, de type `SwipeItems`, qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du côté droit.
- `TopItems`, de type `SwipeItems`, qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du haut vers le haut.
- `BottomItems`, de type `SwipeItems`, qui représente les éléments de balayage qui peuvent être appelés lorsque le contrôle est extrait du bas vers le haut.

Ces propriétés sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et un style.

En outre, le `SwipeView` hérite de la propriété [`Content`](xref:Xamarin.Forms.ContentView.Content) de la classe [`ContentView`](xref:Xamarin.Forms.ContentView) . La propriété `Content` est la propriété de contenu de la classe `SwipeView` et n’a donc pas besoin d’être explicitement définie.

La classe `SwipeView` définit également quatre événements :

- `SwipeStarted` est déclenchée au démarrage d’un balayage. L’objet `SwipeStartedEventArgs` qui accompagne cet événement a une propriété `SwipeDirection`, de type `SwipeDirection`.
- `SwipeChanging` est déclenchée au fur et à mesure que le balayage bouge. L’objet `SwipeChangingEventArgs` qui accompagne cet événement a une propriété `SwipeDirection`, de type `SwipeDirection`et une propriété `Offset` de type `double`.
- `SwipeEnded` est déclenché lorsqu’un balayage se termine. L’objet `SwipeEndedEventArgs` qui accompagne cet événement a une propriété `SwipeDirection`, de type `SwipeDirection`.
- `CloseRequested` est déclenché lorsque les éléments de balayage sont fermés.

De plus, `SwipeView` définit une méthode `Close` qui ferme les éléments de balayage.

> [!NOTE]
> `SwipeView` a un spécifique à une plateforme sur iOS et Android, qui contrôle la transition utilisée lors de l’ouverture d’un `SwipeView`. Pour plus d’informations, consultez [mode de transition par balayage SwipeView sur iOS](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) et [mode de transition par balayage SwipeView sur Android](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md).

## <a name="create-a-swipeview"></a>Créer un SwipeView

Un `SwipeView` doit définir le contenu que le `SwipeView` encapsule et les éléments de balayage révélés par le mouvement de balayage. Les éléments de balayage sont un ou plusieurs objets `SwipeItem` placés dans l’une des quatre collections directionnelles `SwipeView`-`LeftItems`, `RightItems`, `TopItems`ou `BottomItems`.

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

Dans cet exemple, le contenu `SwipeView` est un [`Grid`](xref:Xamarin.Forms.Grid) qui contient une [`Label`](xref:Xamarin.Forms.Label):

[![Capture d’écran du contenu SwipeView, sur iOS et Android](swipeview-images/swipeview-content.png "Contenu SwipeView")](swipeview-images/swipeview-content-large.png#lightbox "Contenu SwipeView")

Les éléments de balayage sont utilisés pour effectuer des actions sur le contenu `SwipeView` et sont révélés lorsque le contrôle est extrait du côté gauche :

[![Capture d’écran des éléments de balayage SwipeView, sur iOS et Android](swipeview-images/swipeview-swipeitems.png "SwipeView les éléments de balayage")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView les éléments de balayage")

Par défaut, un élément balayer est exécuté lorsqu’il est exploité par l’utilisateur. Vous pouvez toutefois modifier ce comportement. Pour plus d’informations, consultez [mode de balayage](#swipe-mode).

Une fois qu’un élément balayer a été exécuté, les éléments de balayage sont masqués et le contenu `SwipeView` s’affiche à nouveau. Vous pouvez toutefois modifier ce comportement. Pour plus d’informations, consultez [comportement de balayage](#swipe-behavior).

> [!NOTE]
> Le contenu de balayage et les éléments de balayage peuvent être placés inline ou définis en tant que ressources.

## <a name="swipe-items"></a>Balayer les éléments

Les collections `LeftItems`, `RightItems`, `TopItems`et `BottomItems` sont toutes des `SwipeItems`de type. La classe `SwipeItems` définit les propriétés suivantes :

- `Mode`, de type `SwipeMode`, qui indique l’effet d’une interaction de balayage. Pour plus d’informations sur le mode de balayage, consultez [mode balayage](#swipe-mode).
- `SwipeBehaviorOnInvoked`, de type `SwipeBehaviorOnInvoked`, qui indique comment un `SwipeView` se comporte après l’appel d’un élément de balayage. Pour plus d’informations sur le comportement de balayage, consultez [comportement de balayage](#swipe-behavior).

Ces propriétés sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et un style.

Chaque élément balayer est défini en tant qu’objet `SwipeItem` placé dans l’une des quatre collections directionnelles `SwipeItems`. La classe `SwipeItem` dérive de la classe [`MenuItem`](xref:Xamarin.Forms.MenuItem) et ajoute les membres suivants :

- Propriété `BackgroundColor`, de type `Color`, qui définit la couleur d’arrière-plan de l’élément balayer. Cette propriété est stockée par une propriété pouvant être liée.
- Événement `Invoked`, qui est déclenché lors de l’exécution de l’élément balayer.

> [!IMPORTANT]
> La classe [`MenuItem`](xref:Xamarin.Forms.MenuItem) définit plusieurs propriétés, notamment `Command`, `CommandParameter`, `IconImageSource`et `Text`. Ces propriétés peuvent être définies sur un objet `SwipeItem` pour définir son apparence, et pour définir un `ICommand` qui s’exécute lorsque l’élément balayer est appelé. Pour plus d’informations, consultez [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md).

L’exemple suivant montre deux objets `SwipeItem` dans la collection `LeftItems` d’une `SwipeView`:

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

L’apparence de chaque `SwipeItem` est définie par une combinaison des propriétés `Text`, `IconImageSource`et `BackgroundColor` :

[![Capture d’écran des éléments de balayage SwipeView, sur iOS et Android](swipeview-images/swipeview-swipeitems.png "SwipeView les éléments de balayage")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView les éléments de balayage")

Lorsqu’un `SwipeItem` est frappé, son événement `Invoked` se déclenche et est géré par son gestionnaire d’événements inscrit. Vous pouvez également définir la propriété `Command` sur une implémentation `ICommand` qui sera exécutée lorsque le `SwipeItem` est appelé.

> [!NOTE]
> Lorsque l’apparence d’un `SwipeItem` est définie uniquement à l’aide des propriétés `Text` ou `IconImageSource`, le contenu est toujours centré.

En plus de définir des éléments de balayage en tant qu’objets `SwipeItem`, il est également possible de définir des vues d’éléments de balayage personnalisées. Pour plus d’informations, consultez [éléments de balayage personnalisés](#custom-swipe-items).

## <a name="swipe-direction"></a>Sens de balayage

`SwipeView` prend en charge quatre directions de balayage différentes, la direction de balayage étant définie par la collection de `SwipeItems` directionnelles, les objets `SwipeItem` sont ajoutés. Chaque sens de balayage peut contenir ses propres éléments de balayage. Par exemple, l’exemple suivant montre une `SwipeView` dont les éléments de balayage dépendent de la direction de balayage :

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

Dans cet exemple, le contenu de `SwipeView` peut être balayé vers la droite ou la gauche. Le balayage vers la droite affiche l’élément de balayage **supprimer** , tandis que le balayage vers la gauche affiche les éléments de balayage du **favori** et du **partage** .

> [!WARNING]
> Une seule instance d’une collection `SwipeItems` directionnelle peut être définie à la fois sur une `SwipeView`. Par conséquent, vous ne pouvez pas avoir deux définitions de `LeftItems` sur une `SwipeView`.

Les événements `SwipeStarted`, `SwipeChanging`et `SwipeEnded` signalent la direction de balayage via la propriété `SwipeDirection` dans les arguments d’événement. Cette propriété est de type `SwipeDirection`, qui est une énumération composée de quatre membres :

- `Right` indique qu’un balayage à droite s’est produit.
- `Left` indique qu’un balayage gauche s’est produit.
- `Up` indique qu’un balayage vers le haut s’est produit.
- `Down` indique qu’un balayage vers le bas s’est produit.

## <a name="swipe-mode"></a>Mode balayage

La classe `SwipeItems` a une propriété `Mode`, qui indique l’effet d’une interaction de balayage. Cette propriété doit être définie sur l’un des membres de l’énumération `SwipeMode` :

- `Reveal` indique qu’un balayage révèle les éléments de balayage. C’est la valeur par défaut de la propriété `SwipeItems.Mode`.
- `Execute` indique qu’un balayage exécute les éléments de balayage.

En mode d’affichage, l’utilisateur passe d’un `SwipeView` pour ouvrir un menu constitué d’un ou de plusieurs éléments de balayage et doit appuyer explicitement sur un élément balayer pour l’exécuter. Une fois que l’élément balayer a été exécuté, les éléments de balayage sont fermés et le contenu `SwipeView` s’affiche à nouveau. En mode exécution, l’utilisateur glisse une `SwipeView` pour ouvrir un menu constitué d’un ou plusieurs éléments de balayage, qui sont ensuite exécutés automatiquement. Après l’exécution, les éléments de balayage sont fermés et le contenu `SwipeView` s’affiche à nouveau.

L’exemple suivant montre une `SwipeView` configurée pour utiliser le mode d’exécution :

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

Dans cet exemple, le contenu de `SwipeView` peut être balayé pour révéler l’élément balayer qui est exécuté immédiatement. Après l’exécution, le contenu de la `SwipeView` s’affiche à nouveau.

## <a name="swipe-behavior"></a>Comportement de balayage

La classe `SwipeItems` a une propriété `SwipeBehaviorOnInvoked`, qui indique comment un `SwipeView` se comporte après l’appel d’un élément de balayage. Cette propriété doit être définie sur l’un des membres de l’énumération `SwipeBehaviorOnInvoked` :

- `Auto` indique qu’en mode d’affichage, le `SwipeView` se ferme après l’appel d’un élément de balayage, et en mode exécution, le `SwipeView` reste ouvert après l’appel d’un élément de balayage. C’est la valeur par défaut de la propriété `SwipeItems.SwipeBehaviorOnInvoked`.
- `Close` indique que le `SwipeView` se ferme après l’appel d’un élément de balayage.
- `RemainOpen` indique que la `SwipeView` reste ouverte après l’appel d’un élément de balayage.

L’exemple suivant montre une `SwipeView` configurée pour rester ouverte après l’appel d’un élément de balayage :

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

Vous pouvez définir des éléments de balayage personnalisés avec le type de `SwipeItemView`. La classe `SwipeItemView` dérive de la classe [`ContentView`](xref:Xamarin.Forms.ContentView) et ajoute les propriétés suivantes :

- `Command`, de type `ICommand`, qui est exécuté lorsqu’un élément balayer est frappé.
- `CommandParameter`, de type `object` : paramètre passé à la commande `Command`.

Ces propriétés sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et un style.

La classe `SwipeItemView` définit également un événement `Invoked` qui est déclenché lorsque l’élément est frappé, après l’exécution de l' `Command`.

L’exemple suivant montre un objet `SwipeItemView` dans la collection `LeftItems` d’une `SwipeView`:

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

Dans cet exemple, le `SwipeItemView` comprend un [`StackLayout`](xref:Xamarin.Forms.StackLayout) contenant un [`Entry`](xref:Xamarin.Forms.Entry) et un [`Label`](xref:Xamarin.Forms.Label). Une fois que l’utilisateur entre dans la `Entry`, le reste du `SwipeViewItem` peut être exploité, ce qui exécute le `ICommand` défini par la propriété `SwipeItemView.Command`.

## <a name="disable-a-swipeview"></a>Désactiver un SwipeView

Une application peut entrer dans un État dans lequel le balayage d’un élément de contenu n’est pas une opération valide. Dans ce cas, l' `SwipeView` peut être désactivée en affectant à sa propriété `IsEnabled` la valeur `false`. Cela empêchera les utilisateurs d’être en mesure de balayer le contenu pour révéler des éléments balayés.

En outre, lors de la définition de la propriété `Command` d’un `SwipeItem` ou d’un `SwipeItemView`, le `CanExecute` délégué du `ICommand` peut être spécifié pour activer ou désactiver l’élément balayer.

## <a name="related-links"></a>Liens connexes

- [SwipeView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
