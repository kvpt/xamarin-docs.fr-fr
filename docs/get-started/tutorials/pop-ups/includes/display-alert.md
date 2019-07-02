---
ms.openlocfilehash: 875f00b379879aa131d37018f89e475170e5320e
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277229"
---
Xamarin.Forms possède une fenêtre contextuelle modale, connue sous le nom d’alerte, pour alerter l’utilisateur ou lui poser des questions simples. Dans cet exercice, vous allez utiliser la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*), à partir de la classe [`Page`](xref:Xamarin.Forms.Page), pour afficher une alerte à l’utilisateur et lui poser une question simple.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une application Xamarin.Forms vide nommée **PopupsTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **PopupsTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **l’Explorateur de solutions**, dans le projet **PopupsTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend deux objets [`Button`](xref:Xamarin.Forms.Button) dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Les propriétés [`Button.Text`](xref:Xamarin.Forms.Button.Text) spécifient le texte qui apparaît dans chaque `Button` et les événements [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sont définis sur les gestionnaires d’événements qui seront créés à l’étape suivante.

1. Dans **l’Explorateur de solutions**, dans le projet **PopupsTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnDisplayAlertButtonClicked` et `OnDisplayAlertQuestionButtonClicked` à la classe :

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Lorsque vous appuyez sur un [`Button`](xref:Xamarin.Forms.Button), la méthode de gestionnaire d’événements correspondante est exécutée. La méthode `OnDisplayAlertButtonClicked` appelle la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) pour afficher une alerte modale avec un seul bouton d’annulation. Si l’alerte est ignorée, l’utilisateur peut continuer d’interagir avec l’application.

    La méthode `OnDisplayAlertQuestionButtonClicked` appelle une surcharge de la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) pour afficher une alerte modale avec un bouton d’acceptation et un bouton d’annulation. Lorsque l’utilisateur sélectionne l’un de ces boutons, son choix est retourné sous la forme d’un `boolean`.

    > [!IMPORTANT]
    > La méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) est asynchrone et est toujours assortie d’un délai d’attente avec le mot clé `await`.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Ensuite, appuyez sur le premier [`Button`](xref:Xamarin.Forms.Button) :

    [![Capture d’écran d’une alerte sur iOS et Android](../images/alert.png "Alerte")](../images/alert-large.png#lightbox "Alerte")

    Ignorez l’alerte, puis appuyez sur le second [`Button`](xref:Xamarin.Forms.Button) :

    [![Capture d’écran d’une alerte qui pose une question, sur iOS et Android](../images/alert-question.png "Alerte qui pose une question")](../images/alert-question-large.png#lightbox "Alerte qui pose une question")

    Notez que, après avoir sélectionné une réponse à la question, la réponse s’affiche dans la fenêtre **Sortie** de Visual Studio.

    Pour plus d’informations sur l’affichage des alertes, voir [Afficher une alerte](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert) dans le guide [Afficher les fenêtres contextuelles](~/xamarin-forms/user-interface/pop-ups.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une application Xamarin.Forms vide nommée **PopupsTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **PopupsTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **Panneau Solutions**, dans le projet **PopupsTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend deux objets [`Button`](xref:Xamarin.Forms.Button) dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). Les propriétés [`Button.Text`](xref:Xamarin.Forms.Button.Text) spécifient le texte qui apparaît dans chaque `Button` et les événements [`Clicked`](xref:Xamarin.Forms.Button.Clicked) sont définis sur les gestionnaires d’événements qui seront créés à l’étape suivante.

1. Dans **Panneau Solutions**, dans le projet **PopupsTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez les gestionnaires d’événements `OnDisplayAlertButtonClicked` et `OnDisplayAlertQuestionButtonClicked` à la classe :

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    Lorsque vous appuyez sur un [`Button`](xref:Xamarin.Forms.Button), la méthode de gestionnaire d’événements correspondante est exécutée. La méthode `OnDisplayAlertButtonClicked` appelle la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) pour afficher une alerte modale avec un seul bouton d’annulation. Si l’alerte est ignorée, l’utilisateur peut continuer d’interagir avec l’application.

    La méthode `OnDisplayAlertQuestionButtonClicked` appelle une surcharge de la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) pour afficher une alerte modale avec un bouton d’acceptation et un bouton d’annulation. Lorsque l’utilisateur sélectionne l’un de ces boutons, son choix est retourné sous la forme d’un `boolean`.

    > [!IMPORTANT]
    > La méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) est asynchrone et est toujours assortie d’un délai d’attente avec le mot clé `await`.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Ensuite, appuyez sur le premier [`Button`](xref:Xamarin.Forms.Button) :

    [![Capture d’écran d’une alerte sur iOS et Android](../images/alert.png "Alerte")](../images/alert-large.png#lightbox "Alerte")

    Ignorez l’alerte, puis appuyez sur le second [`Button`](xref:Xamarin.Forms.Button) :

    [![Capture d’écran d’une alerte qui pose une question, sur iOS et Android](../images/alert-question.png "Alerte qui pose une question")](../images/alert-question-large.png#lightbox "Alerte qui pose une question")

    Notez que, après avoir sélectionné une réponse à la question, la réponse s’affiche dans la fenêtre **Sortie de l’application** de Visual Studio pour Mac.

    Pour plus d’informations sur l’affichage des alertes, voir [Afficher une alerte](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert) dans le guide [Afficher les fenêtres contextuelles](~/xamarin-forms/user-interface/pop-ups.md).
