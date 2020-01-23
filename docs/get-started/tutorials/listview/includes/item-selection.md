---
ms.openlocfilehash: 2185fa243d2bccea046be5c91a2b1e9ed365edfe
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "74062879"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`ListView`](xref:Xamarin.Forms.ListView) pour qu’elle définisse les gestionnaires pour les événements [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) et [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) :

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    Ce code définit l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) sur un gestionnaire d’événements nommé `OnListViewItemSelected` et l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) sur un gestionnaire d’événements nommé `OnListViewItemTapped`. Les deux gestionnaires d’événements seront créés à l’étape suivante.

1. Dans **l’Explorateur de solutions**, dans le projet **ListViewTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnListViewItemSelected` et `OnListViewItemTapped` à la classe :

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    Lorsque l’utilisateur appuie sur un élément de la [`ListView`](xref:Xamarin.Forms.ListView), les événements [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) et [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) sont déclenchés, ce qui lance l’exécution des méthodes `OnListViewItemSelected` et `OnListItemTapped`, respectivement. L’argument `sender` des deux méthodes est l’objet `ListView` chargé de déclencher l’événement ; il peut être utilisé pour accéder à l’objet `ListView`. L’argument [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) dans la méthode `OnListViewItemSelected` fournit l’élément sélectionné et l’argument [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) dans la méthode `OnListViewItemTapped` fournit l’élément sur lequel l’utilisateur a appuyé.

    > [!IMPORTANT]
    > L’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) est déclenché uniquement lorsqu’un nouvel élément est sélectionné dans le [`ListView`](xref:Xamarin.Forms.ListView). Par conséquent, si vous appuyez deux fois sur le même élément, cela déclenchera deux événements [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), mais un seul événement `ItemSelected`.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une ListView qui répond à l’action d’un utilisateur qui sélectionne ou appuie sur un élément, sous iOS et Android](../images/item-selection.png "Sélection d’élément ListView")](../images/item-selection-large.png#lightbox "Sélection d’élément ListView")

    Définissez des points d’arrêt dans les deux gestionnaires d’événements et appuyez sur les éléments dans la [`ListView`](xref:Xamarin.Forms.ListView). Notez que l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se déclenche lorsqu’un nouvel élément est sélectionné dans la [`ListView`](xref:Xamarin.Forms.ListView), tandis que l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se déclenche chaque fois que l’utilisateur appuie sur un élément.

    Pour plus d’informations sur la sélection d’éléments par un clic ou par une pression, consultez la rubrique [Sélection par clic ou par pression](~/xamarin-forms/user-interface/listview/interactivity.md#selection-and-taps) dans le guide [Interactivité de ListView](~/xamarin-forms/user-interface/listview/interactivity.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`ListView`](xref:Xamarin.Forms.ListView) pour qu’elle définisse les gestionnaires pour les événements [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) et [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) :

    ```xaml
    <ListView ItemsSource="{Binding Monkeys}"
              ItemSelected="OnListViewItemSelected"
              ItemTapped="OnListViewItemTapped" />
    ```

    Ce code définit l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) sur un gestionnaire d’événements nommé `OnListViewItemSelected` et l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) sur un gestionnaire d’événements nommé `OnListViewItemTapped`. Les deux gestionnaires d’événements seront créés à l’étape suivante.

1. Dans le **Panneau Solutions**, dans le projet **ListViewTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnListViewItemSelected` et `OnListViewItemTapped` à la classe :

    ```csharp
    void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        Monkey selectedItem = e.SelectedItem as Monkey;
    }

    void OnListViewItemTapped(object sender, ItemTappedEventArgs e)
    {
        Monkey tappedItem = e.Item as Monkey;
    }
    ```

    Lorsque l’utilisateur appuie sur un élément de la [`ListView`](xref:Xamarin.Forms.ListView), les événements [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) et [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) sont déclenchés, ce qui lance l’exécution des méthodes `OnListViewItemSelected` et `OnListItemTapped`, respectivement. L’argument `sender` des deux méthodes est l’objet `ListView` chargé de déclencher l’événement ; il peut être utilisé pour accéder à l’objet `ListView`. L’argument [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) dans la méthode `OnListViewItemSelected` fournit l’élément sélectionné et l’argument [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) dans la méthode `OnListViewItemTapped` fournit l’élément sur lequel l’utilisateur a appuyé.

    > [!IMPORTANT]
    > L’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) est déclenché uniquement lorsqu’un nouvel élément est sélectionné dans le [`ListView`](xref:Xamarin.Forms.ListView). Par conséquent, si vous appuyez deux fois sur le même élément, cela déclenchera deux événements [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped), mais un seul événement `ItemSelected`.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une ListView qui répond à l’action d’un utilisateur qui sélectionne ou appuie sur un élément, sous iOS et Android](../images/item-selection.png "Sélection d’élément ListView")](../images/item-selection-large.png#lightbox "Sélection d’élément ListView")

    Définissez des points d’arrêt dans les deux gestionnaires d’événements et appuyez sur les éléments dans la [`ListView`](xref:Xamarin.Forms.ListView). Notez que l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) se déclenche lorsqu’un nouvel élément est sélectionné dans la [`ListView`](xref:Xamarin.Forms.ListView), tandis que l’événement [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) se déclenche chaque fois que l’utilisateur appuie sur un élément.

    Pour plus d’informations sur la sélection d’éléments par un clic ou par une pression, consultez la rubrique [Sélection par clic ou par pression](~/xamarin-forms/user-interface/listview/interactivity.md#selection-and-taps)
