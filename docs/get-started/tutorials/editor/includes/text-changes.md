---
ms.openlocfilehash: f1671f4f40e3eb44465431d39994cf5e623665a1
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919294"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Editor`](xref:Xamarin.Forms.Editor) pour qu’elle définisse un gestionnaire pour les événements [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) et [`Completed`](xref:Xamarin.Forms.Editor.Completed) :

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Ce code définit l’événement [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) sur un gestionnaire d’événements nommé `OnEditorTextChanged` et l’événement [`Completed`](xref:Xamarin.Forms.Editor.Completed) sur un gestionnaire d’événements nommé `OnEditorCompleted`. Les deux gestionnaires d’événements seront créés à l’étape suivante.

1. Dans **l’Explorateur de solutions**, dans le projet **EditorTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnEditorTextChanged` et `OnEditorCompleted` à la classe :

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Lorsque le texte de [`Editor`](xref:Xamarin.Forms.Editor) change, la méthode `OnEditorTextChanged` s’exécute. L’argument `sender` est l’objet `Editor` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Editor`. L’argument [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fournit les anciennes et les nouvelles valeurs de texte, avant et après le changement de texte.

    Lorsque la modification est terminée, la méthode `OnEditorCompleted` s’exécute. Pour y parvenir, vous devez décentrer [`Editor`](xref:Xamarin.Forms.Editor) ou appuyer sur le bouton « Terminé » sur iOS. L’argument `sender` est l’objet `Editor` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Editor`.

    > [!IMPORTANT]
    > Tout texte saisi dans [`Editor`](xref:Xamarin.Forms.Editor) est stocké dans la propriété [`Text`](xref:Xamarin.Forms.InputView.Text).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’un éditeur contenant du texte, sur iOS et Android](../images/text-changes.png "Éditeur avec du texte")](../images/text-changes-large.png#lightbox "Éditeur avec du texte")

    Définissez des points d’arrêt dans les deux gestionnaires d’événements, entrez du texte dans [`Editor`](xref:Xamarin.Forms.Editor) et observez le déclenchement de l’événement [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged). Décentrez `Editor` pour observer le déclenchement de l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Pour plus d’informations sur les événements [`Editor`](xref:Xamarin.Forms.Editor), consultez la rubrique [Événements et interactivité](~/xamarin-forms/user-interface/text/editor.md#events-and-interactivity) du guide [Éditeur Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Editor`](xref:Xamarin.Forms.Editor) pour qu’elle définisse un gestionnaire pour les événements [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) et [`Completed`](xref:Xamarin.Forms.Editor.Completed) :

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            HeightRequest="200"
            TextChanged="OnEditorTextChanged"
            Completed="OnEditorCompleted" />
    ```

    Ce code définit l’événement [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) sur un gestionnaire d’événements nommé `OnEditorTextChanged` et l’événement [`Completed`](xref:Xamarin.Forms.Editor.Completed) sur un gestionnaire d’événements nommé `OnEditorCompleted`. Les deux gestionnaires d’événements seront créés à l’étape suivante.

1. Dans **Panneau Solutions**, dans le projet **EditorTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnEditorTextChanged` et `OnEditorCompleted` à la classe :

    ```csharp
    void OnEditorTextChanged(object sender, TextChangedEventArgs e)
    {
        string oldText = e.OldTextValue;
        string newText = e.NewTextValue;
    }

    void OnEditorCompleted(object sender, EventArgs e)
    {
        string text = ((Editor)sender).Text;
    }
    ```

    Lorsque le texte de [`Editor`](xref:Xamarin.Forms.Editor) change, la méthode `OnEditorTextChanged` s’exécute. L’argument `sender` est l’objet `Editor` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Editor`. L’argument [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) fournit les anciennes et les nouvelles valeurs de texte, avant et après le changement de texte.

    Lorsque la modification est terminée, la méthode `OnEditorCompleted` s’exécute. Pour y parvenir, vous devez décentrer [`Editor`](xref:Xamarin.Forms.Editor) ou appuyer sur le bouton « Terminé » sur iOS. L’argument `sender` est l’objet `Editor` chargé de déclencher l’événement `TextChanged` ; il peut être utilisé pour accéder à l’objet `Editor`.

    > [!IMPORTANT]
    > Tout texte saisi dans [`Editor`](xref:Xamarin.Forms.Editor) est stocké dans la propriété [`Text`](xref:Xamarin.Forms.InputView.Text).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’un éditeur contenant du texte, sur iOS et Android](../images/text-changes.png "Éditeur avec du texte")](../images/text-changes-large.png#lightbox "Éditeur avec du texte")

    Définissez des points d’arrêt dans les deux gestionnaires d’événements, entrez du texte dans [`Editor`](xref:Xamarin.Forms.Editor) et observez le déclenchement de l’événement [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged). Décentrez `Editor` pour observer le déclenchement de l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed).

    Pour plus d’informations sur les événements [`Editor`](xref:Xamarin.Forms.Editor), consultez la rubrique [Événements et interactivité](~/xamarin-forms/user-interface/text/editor.md#events-and-interactivity) du guide [Éditeur Xamarin.Forms](~/xamarin-forms/user-interface/text/editor.md).
