---
ms.openlocfilehash: 3130c20d39e0140695eed92ffa4941d6bafe796e
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "67394543"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Entry`](xref:Xamarin.Forms.Entry) pour qu’elle définisse un gestionnaire pour les événements [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) et [`Completed`](xref:Xamarin.Forms.Entry.Completed) :

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Ce code définit l’événement [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) sur un gestionnaire d’événements nommé `OnEntryTextChanged` et l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed) sur un gestionnaire d’événements nommé `OnEntryCompleted`. Les deux gestionnaires d’événements seront créés à l’étape suivante.

1. Dans **l’Explorateur de solutions**, dans le projet **EntryTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnEntryTextChanged` et `OnEntryCompleted` à la classe :

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Lorsque le texte de [`Entry`](xref:Xamarin.Forms.Entry) change, la méthode `OnEntryTextChanged` s’exécute. L’argument `sender` est l’objet `Entry` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Entry`. L’argument [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fournit les anciennes et les nouvelles valeurs de texte, avant et après le changement de texte.

    Lorsque vous finalisez le texte dans [`Entry`](xref:Xamarin.Forms.Entry) avec la touche Retour, la méthode `OnEntryCompleted` s’exécute. L’argument `sender` est l’objet `Entry` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Entry`.

    > [!IMPORTANT]
    > Tout texte saisi dans [`Entry`](xref:Xamarin.Forms.Entry) est stocké dans la propriété [`Text`](xref:Xamarin.Forms.Entry.Text).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une entrée contenant du texte, sur iOS et Android](../images/text-changes.png "Entrée constituée de texte")](../images/text-changes-large.png#lightbox "Entrée constituée de texte")

    Définissez des points d’arrêt dans les deux gestionnaires d’événements et entrez du texte dans [`Entry`](xref:Xamarin.Forms.Entry) pour observer le déclenchement des événements [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) et [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Pour plus d’informations sur les événements [`Entry`](xref:Xamarin.Forms.Entry), consultez la rubrique [Événements et interactivité](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) du guide [Entrée de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Entry`](xref:Xamarin.Forms.Entry) pour qu’elle définisse un gestionnaire pour les événements [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) et [`Completed`](xref:Xamarin.Forms.Entry.Completed) :

    ```xaml
    <Entry Placeholder="Enter text"
           TextChanged="OnEntryTextChanged"
           Completed="OnEntryCompleted" />
    ```

    Ce code définit l’événement [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) sur un gestionnaire d’événements nommé `OnEntryTextChanged` et l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed) sur un gestionnaire d’événements nommé `OnEntryCompleted`. Les deux gestionnaires d’événements seront créés à l’étape suivante.

1. Dans **Panneau Solutions**, dans le projet **EntryTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnEntryTextChanged` et `OnEntryCompleted` à la classe :

    ```csharp
    void OnEntryTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        string text = ((Entry)sender).Text;
    }
    ```

    Lorsque le texte de [`Entry`](xref:Xamarin.Forms.Entry) change, la méthode `OnEntryTextChanged` s’exécute. L’argument `sender` est l’objet `Entry` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Entry`. L’argument [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fournit les anciennes et les nouvelles valeurs de texte, avant et après le changement de texte.

    Lorsque vous finalisez le texte dans [`Entry`](xref:Xamarin.Forms.Entry) avec la touche Retour, la méthode `OnEntryCompleted` s’exécute. L’argument `sender` est l’objet `Entry` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Entry`.

    > [!IMPORTANT]
    > Tout texte saisi dans [`Entry`](xref:Xamarin.Forms.Entry) est stocké dans la propriété [`Text`](xref:Xamarin.Forms.Entry.Text).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une entrée contenant du texte, sur iOS et Android](../images/text-changes.png "Entrée constituée de texte")](../images/text-changes-large.png#lightbox "Entrée constituée de texte")

    Définissez des points d’arrêt dans les deux gestionnaires d’événements et entrez du texte dans [`Entry`](xref:Xamarin.Forms.Entry) pour observer le déclenchement des événements [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) et [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Pour plus d’informations sur les événements [`Entry`](xref:Xamarin.Forms.Entry), consultez la rubrique [Événements et interactivité](~/xamarin-forms/user-interface/text/entry.md#events-and-interactivity) du guide [Entrée de Xamarin.Forms](~/xamarin-forms/user-interface/text/entry.md).
