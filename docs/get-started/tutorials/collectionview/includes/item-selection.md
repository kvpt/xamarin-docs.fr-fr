---
ms.openlocfilehash: 41c1c8ae97c62a3eb2a73681b215e7687d0e473c
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98559065"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`CollectionView`](xref:Xamarin.Forms.CollectionView) pour qu’elle définisse la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) sur `Single` et un gestionnaire pour l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) :

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    Ce code définit la sélection d’un seul élément dans la [`CollectionView`](xref:Xamarin.Forms.CollectionView) et définit l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) sur un gestionnaire d’événements nommé `OnSelectionChanged`. Le gestionnaire d’événements sera créé à l’étape suivante.

1. Dans l’**Explorateur de solutions**, dans le projet **CollectionViewTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez le gestionnaire d’événements `OnSelectionChanged` à la classe :

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    Quand un élément de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) est sélectionné, l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) est déclenché, ce qui exécute la méthode `OnSelectionChanged`. L’argument `sender` de la méthode est l’objet `CollectionView` chargé de déclencher l’événement, qui peut être utilisé pour accéder à l’objet `CollectionView`. L’argument [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) de la méthode `OnSelectionChanged` fournit l’élément sélectionné.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une CollectionView qui répond à la sélection d’un élément, sur iOS et Android](../images/item-selection.png "Sélection d’éléments d’une CollectionView")](../images/item-selection-large.png#lightbox "Sélection d’éléments d’une CollectionView")

    Définissez un point d’arrêt dans le gestionnaire d’événements `OnSelectionChanged` et sélectionnez un élément dans la [`CollectionView`](xref:Xamarin.Forms.CollectionView). Examinez la valeur de la variable `selectedItem` pour vérifier qu’elle contient les données de l’élément sélectionné.

    Pour plus d’informations sur la sélection d’éléments, consultez [Sélection dans une CollectionView Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/selection.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`CollectionView`](xref:Xamarin.Forms.CollectionView) pour qu’elle définisse la propriété [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) sur `Single` et un gestionnaire pour l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) :

    ```xaml
    <CollectionView ItemsSource="{Binding Monkeys}"
                    SelectionMode="Single"
                    SelectionChanged="OnSelectionChanged" />
    ```

    Ce code définit la sélection d’un seul élément dans la [`CollectionView`](xref:Xamarin.Forms.CollectionView) et définit l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) sur un gestionnaire d’événements nommé `OnSelectionChanged`. Le gestionnaire d’événements sera créé à l’étape suivante.

1. Dans le **Panneau Solutions**, dans le projet **CollectionViewTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez le gestionnaire d’événements `OnSelectionChanged` à la classe :

    ```csharp
    void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        Monkey selectedItem = e.CurrentSelection[0] as Monkey;
    }
    ```

    Quand un élément de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) est sélectionné, l’événement [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) est déclenché, ce qui exécute la méthode `OnSelectionChanged`. L’argument `sender` de la méthode est l’objet `CollectionView` chargé de déclencher l’événement, qui peut être utilisé pour accéder à l’objet `CollectionView`. L’argument [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) de la méthode `OnSelectionChanged` fournit l’élément sélectionné.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une CollectionView qui répond à la sélection d’un élément, sur iOS et Android](../images/item-selection.png "Sélection d’éléments d’une CollectionView")](../images/item-selection-large.png#lightbox "Sélection d’éléments d’une CollectionView")

    Définissez un point d’arrêt dans le gestionnaire d’événements `OnSelectionChanged` et sélectionnez un élément dans la [`CollectionView`](xref:Xamarin.Forms.CollectionView). Examinez la valeur de la variable `selectedItem` pour vérifier qu’elle contient les données de l’élément sélectionné.

    Pour plus d’informations sur la sélection d’éléments, consultez [Sélection dans une CollectionView Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/selection.md).
