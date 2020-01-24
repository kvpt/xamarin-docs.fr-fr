---
ms.openlocfilehash: 87eb021e6cc571a9a5522697cde2aa11ee991308
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "66193830"
---

Xamarin.Forms dispose d’une fenêtre contextuelle modale (une feuille d’action) servant à guider les utilisateurs dans une tâche. Dans cet exercice, vous utiliserez la méthode [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) à partir de la classe [`Page`](xref:Xamarin.Forms.Page) afin d’afficher une feuille d’action guidant les utilisateurs dans une tâche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, ajoutez une nouvelle déclaration [`Button`](xref:Xamarin.Forms.Button) qui affiche une feuille d’action :

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

     La propriété [`Button.Text`](xref:Xamarin.Forms.Button.Text) spécifie le texte qui apparaît dans l’élément `Button`. De plus, l’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) est défini sur un gestionnaire d’événements nommé `OnDisplayActionSheetButtonClicked`, qui sera créé à l’étape suivante.

1. Dans **l’Explorateur de solutions**, dans le projet **PopupsTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez le gestionnaire d’événements `OnDisplayActionSheetButtonClicked` à la classe :

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Lorsque vous appuyez sur [`Button`](xref:Xamarin.Forms.Button), la méthode `OnDisplayActionSheetButtonClicked` s’exécute. Cette méthode appelle la méthode [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*), afin de présenter un ensemble d’alternatives à l’utilisateur dans le cadre d’une tâche. Lorsque l’utilisateur sélectionne l’une de ces alternatives, son choix est retourné sous la forme de `string`.

    > [!IMPORTANT]
    > La méthode [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) est asynchrone et est toujours assortie d’un délai d’attente avec le mot clé `await`.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Ensuite, appuyez sur l’élément [`Button`](xref:Xamarin.Forms.Button) que vous avez ajouté à [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

    [![Capture d’écran d’une feuille d’action, sur iOS et Android](../images/actionsheet.png "Feuille d’action qui guide les utilisateurs dans une tâche")](../images/actionsheet-large.png#lightbox "Feuille d’action qui guide les utilisateurs dans une tâche")

    Remarquez qu’après avoir sélectionné une alternative dans la boîte de dialogue de la feuille d’action, la sélection est affichée dans la fenêtre **de sortie** de Visual Studio.

    Pour plus d’informations sur l’affichage des feuilles d’action, voir [Guider les utilisateurs dans les tâches](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) dans le guide [Afficher les fenêtres contextuelles](~/xamarin-forms/user-interface/pop-ups.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, ajoutez une nouvelle déclaration [`Button`](xref:Xamarin.Forms.Button) qui affiche une feuille d’action :

    ```xaml
    <Button Text="Display action sheet"
            Clicked="OnDisplayActionSheetButtonClicked" />
    ```

    La propriété [`Button.Text`](xref:Xamarin.Forms.Button.Text) spécifie le texte qui apparaît dans l’élément `Button`. De plus, l’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) est défini sur un gestionnaire d’événements nommé `OnDisplayActionSheetButtonClicked`, qui sera créé à l’étape suivante.

1. Dans **Panneau Solutions**, dans le projet **PopupsTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez le gestionnaire d’événements `OnDisplayActionSheetButtonClicked` à la classe :

    ```csharp
    async void OnDisplayActionSheetButtonClicked(object sender, EventArgs e)
    {
        string action = await DisplayActionSheet("Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
        Console.WriteLine("Action: " + action);
    }
    ```

    Lorsque vous appuyez sur [`Button`](xref:Xamarin.Forms.Button), la méthode `OnDisplayActionSheetButtonClicked` s’exécute. Cette méthode appelle la méthode [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*), afin de présenter un ensemble d’alternatives à l’utilisateur dans le cadre d’une tâche. Lorsque l’utilisateur sélectionne l’une de ces alternatives, son choix est retourné sous la forme de `string`.

    > [!IMPORTANT]
    > La méthode [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) est asynchrone et est toujours assortie d’un délai d’attente avec le mot clé `await`.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Ensuite, appuyez sur l’élément [`Button`](xref:Xamarin.Forms.Button) que vous avez ajouté à [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

    [![Capture d’écran d’une feuille d’action, sur iOS et Android](../images/actionsheet.png "Feuille d’action qui guide les utilisateurs dans une tâche")](../images/actionsheet-large.png#lightbox "Feuille d’action qui guide les utilisateurs dans une tâche")

    Remarquez qu’après avoir sélectionné une alternative dans la boîte de dialogue de la feuille d’action, la sélection est affichée dans la fenêtre **de sortie** de Visual Studio pour Mac.

    Pour plus d’informations sur l’affichage des feuilles d’action, voir [Guider les utilisateurs dans les tâches](~/xamarin-forms/user-interface/pop-ups.md#guide-users-through-tasks) dans le guide [Afficher les fenêtres contextuelles](~/xamarin-forms/user-interface/pop-ups.md).
