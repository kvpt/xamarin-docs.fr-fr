---
ms.openlocfilehash: d1f7d209eaaca62a55b768646f51024609057a63
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372929"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Button`](xref:Xamarin.Forms.Button) pour qu’elle définisse un gestionnaire pour l’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) :

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Ce code définit l’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante.

1. Dans **l’Explorateur de solutions**, dans le projet **ButtonTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez le gestionnaire d’événements `OnButtonClicked` à la classe :

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Lorsque vous appuyez sur [`Button`](xref:Xamarin.Forms.Button), la méthode `OnButtonClicked` s’exécute. L’argument `sender` est l’objet `Button` chargé de déclencher l’événement `Clicked` ; il peut être utilisé pour accéder à l’objet `Button`. Ce gestionnaire d’événements met à jour le texte affiché par `Button`.

    > [!NOTE]
    > Outre l’événement `Clicked`, `Button` définit également les événements [`Pressed`](xref:Xamarin.Forms.Button.Pressed) et [`Released`](xref:Xamarin.Forms.Button.Released). Pour plus d’informations, consultez la rubrique [Enfoncer et relâcher le bouton](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) dans le guide [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Cliquez sur [`Button`](xref:Xamarin.Forms.Button). Vous remarquerez que le texte qui s’affiche change :

    [![Capture d’écran du texte d’un bouton qui change après un clic, sur iOS et Android](../images/handle-button-click.png "Gérer un clic de bouton")](../images/handle-button-click-large.png#lightbox "Gérer un clic de bouton")

    Pour plus d’informations sur la gestion des clics sur un bouton, consultez la rubrique [Gestion des clics sur un bouton](~/xamarin-forms/user-interface/button.md#handling-button-clicks) dans le guide [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Button`](xref:Xamarin.Forms.Button) pour qu’elle définisse un gestionnaire pour l’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) :

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    Ce code définit l’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sur un gestionnaire d’événements nommé `OnButtonClicked` qui sera créé à l’étape suivante.

1. Dans **Panneau Solutions**, dans le projet **ButtonTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez le gestionnaire d’événements `OnButtonClicked` à la classe :

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    Lorsque vous appuyez sur [`Button`](xref:Xamarin.Forms.Button), la méthode `OnButtonClicked` s’exécute. L’argument `sender` est l’objet `Button` chargé de déclencher l’événement `Clicked` ; il peut être utilisé pour accéder à l’objet `Button`. Ce gestionnaire d’événements met à jour le texte affiché par `Button`.

    > [!NOTE]
    > Outre l’événement `Clicked`, `Button` définit également les événements [`Pressed`](xref:Xamarin.Forms.Button.Pressed) et [`Released`](xref:Xamarin.Forms.Button.Released). Pour plus d’informations, consultez la rubrique [Enfoncer et relâcher le bouton](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button) dans le guide [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Cliquez sur [`Button`](xref:Xamarin.Forms.Button). Vous remarquerez que le texte qui s’affiche change :

    [![Capture d’écran du texte d’un bouton qui change après un clic, sur iOS et Android](../images/handle-button-click.png "Gérer un clic de bouton")](../images/handle-button-click-large.png#lightbox "Gérer un clic de bouton")

    Pour plus d’informations sur la gestion des clics sur un bouton, consultez la rubrique [Gestion des clics sur un bouton](~/xamarin-forms/user-interface/button.md#handling-button-clicks) dans le guide [Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md).
