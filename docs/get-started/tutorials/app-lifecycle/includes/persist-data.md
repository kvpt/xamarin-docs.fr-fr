---
ms.openlocfilehash: 5d9d5e4eb757d6afd1c13cb4851edd23feaa6e65
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135136"
---
La sous-classe [`Application`](xref:Xamarin.Forms.Application) dispose d’un dictionnaire statique [`Properties`](xref:Xamarin.Forms.Application.Properties) qui peut être utilisé pour stocker des données tout au long des changements d’état du cycle de vie. Ce dictionnaire utilise une clé `string` et stocke une valeur `object`. Le dictionnaire est automatiquement enregistré sur l’appareil et il est rempli à nouveau lorsque l’application est redémarrée.

> [!IMPORTANT]
> Le dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties) peut sérialiser uniquement les types primitifs de stockage.

Dans cet exercice, vous modifierez l’application pour conserver le texte d’un élément [`Entry`](xref:Xamarin.Forms.Entry) lors de la mise en arrière-plan et restaurer le texte vers l’élément `Entry` lorsque l’application est redémarrée.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans l’**Explorateur de solutions**, dans le projet **AppLifecycleTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **App.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Ce code définit une propriété `DisplayText` et une constante `displayText`. Lorsque l’application est mise en arrière-plan ou arrêtée, la substitution de méthode `OnSleep` ajoute la valeur de propriété `DisplayText` au dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties), par rapport à une clé `displayText`. Puis, lorsque l’application démarre, si le dictionnaire `Properties` contient bien la clé `displayText`, la valeur de la clé est restaurée vers la propriété `DisplayText`.

    > [!IMPORTANT]
    > Pour éviter toute erreur inattendue, vérifiez toujours que le dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties) contient la clé avant d’y accéder.

    Il n’est pas nécessaire de restaurer les données à partir du dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties) dans la surcharge de méthode `OnResume`. En effet, lorsqu’une application est mise en arrière-plan, son état est toujours conservé en mémoire.

1. Dans **l’Explorateur de solutions**, dans le projet **AppLifecycleTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`Entry`](xref:Xamarin.Forms.Entry) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propriété [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) spécifie le texte d’espace réservé affiché lorsque l’élément `Entry` est affiché en premier, et un gestionnaire d’événements nommé `OnEntryCompleted` est inscrit auprès de l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed). En outre, l’élément `Entry` dispose d’un nom spécifié avec l’attribut `x:Name`. Ainsi, le fichier code-behind peut accéder à l’objet `Entry` en utilisant le nom qui y est affecté.

1. Dans **l’Explorateur de solutions**, dans le projet **AppLifecycleTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez une substitution pour la méthode `OnAppearing`, et le gestionnaire d’événements `OnEntryCompleted` à la classe :

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    La méthode `OnAppearing` récupère la valeur de la propriété `App.DisplayText` et la définit comme valeur de propriété [`Text`](xref:Xamarin.Forms.InputView.Text) de l’élément [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > Le remplacement de la méthode `OnAppearing` est exécuté une fois que [`ContentPage`](xref:Xamarin.Forms.ContentPage) est mis en page, juste avant qu’il soit visible. Il représente donc un emplacement idéal pour définir le contenu des affichages Xamarin.Forms.

    Lorsque le texte est finalisé dans l’élément [`Entry`](xref:Xamarin.Forms.Entry), avec la touche de retour, la méthode `OnEntryCompleted` s’exécute et le texte `Entry` est stocké dans la propriété `App.DisplayText`.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix.

    Entrez du texte dans l’élément [`Entry`](xref:Xamarin.Forms.Entry) et appuyez sur la touche de retour. Ensuite, mettez l’application en arrière-plan en appuyant sur le bouton d’accueil pour appeler la méthode `OnSleep`.

    Pour terminer, lancez l’application à partir de Visual Studio et le texte qui a été entré précédemment dans l’élément [`Entry`](xref:Xamarin.Forms.Entry) sera restauré :

    [![Capture d’écran d’une entrée dont la propriété Text est conservée après les changements d’état du cycle de vie, sur iOS et Android](../images/persist-data.png "Entrée dont la propriété Text est conservée après les changements d’état du cycle de vie")](../images/persist-data-large.png#lightbox "Entrée dont la propriété Text est conservée après les changements d’état du cycle de vie")

    Pour plus d’informations sur la conservation des données dans le dictionnaire de propriétés, consultez la page [Dictionnaire de propriétés](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) dans le guide [Classe App Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **Panneau Solutions**, dans le projet **AppLifecycleTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **App.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace AppLifecycleTutorial
    {
        public partial class App : Application
        {
            const string displayText = "displayText";

            public string DisplayText { get; set; }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                Console.WriteLine("OnStart");

                if (Properties.ContainsKey(displayText))
                {
                    DisplayText = (string)Properties[displayText];
                }
            }

            protected override void OnSleep()
            {
                Console.WriteLine("OnSleep");
                Properties[displayText] = DisplayText;
            }

            protected override void OnResume()
            {
                Console.WriteLine("OnResume");
            }
        }
    }
    ```

    Ce code définit une propriété `DisplayText` et une constante `displayText`. Lorsque l’application est mise en arrière-plan ou arrêtée, la substitution de méthode `OnSleep` ajoute la valeur de propriété `DisplayText` au dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties), par rapport à une clé `displayText`. Puis, lorsque l’application démarre, si le dictionnaire `Properties` contient bien la clé `displayText`, la valeur de la clé est restaurée vers la propriété `DisplayText`.

    > [!IMPORTANT]
    > Pour éviter toute erreur inattendue, vérifiez toujours que le dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties) contient la clé avant d’y accéder.

    Il n’est pas nécessaire de restaurer les données à partir du dictionnaire [`Properties`](xref:Xamarin.Forms.Application.Properties) dans la surcharge de méthode `OnResume`. En effet, lorsqu’une application est mise en arrière-plan, son état est toujours conservé en mémoire.

1. Dans **Panneau Solutions**, dans le projet **AppLifecycleTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="AppLifecycleTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry x:Name="entry"
                   Placeholder="Enter text here"
                   Completed="OnEntryCompleted" />
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`Entry`](xref:Xamarin.Forms.Entry) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propriété [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) spécifie le texte d’espace réservé affiché lorsque l’élément `Entry` est affiché en premier, et un gestionnaire d’événements nommé `OnEntryCompleted` est inscrit auprès de l’événement [`Completed`](xref:Xamarin.Forms.Entry.Completed). En outre, l’élément `Entry` dispose d’un nom spécifié avec l’attribut `x:Name`. Ainsi, le fichier code-behind peut accéder à l’objet `Entry` en utilisant le nom qui y est affecté.

1. Dans **Panneau Solutions**, dans le projet **AppLifecycleTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Puis, dans **MainPage.xaml.cs**, ajoutez une substitution pour la méthode `OnAppearing`, et le gestionnaire d’événements `OnEntryCompleted` à la classe :

    ```csharp
    protected override void OnAppearing()
    {
        base.OnAppearing();

        entry.Text = (Application.Current as App).DisplayText;
    }

    void OnEntryCompleted(object sender, EventArgs e)
    {
        (Application.Current as App).DisplayText = entry.Text;
    }
    ```

    La méthode `OnAppearing` récupère la valeur de la propriété `App.DisplayText` et la définit comme valeur de propriété [`Text`](xref:Xamarin.Forms.InputView.Text) de l’élément [`Entry`](xref:Xamarin.Forms.Entry).

    > [!NOTE]
    > Le remplacement de la méthode `OnAppearing` est exécuté une fois que [`ContentPage`](xref:Xamarin.Forms.ContentPage) est mis en page, juste avant qu’il soit visible. Il représente donc un emplacement idéal pour définir le contenu des affichages Xamarin.Forms.

    Lorsque le texte est finalisé dans l’élément [`Entry`](xref:Xamarin.Forms.Entry), avec la touche de retour, la méthode `OnEntryCompleted` s’exécute et le texte `Entry` est stocké dans la propriété `App.DisplayText`.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix.

    Entrez du texte dans l’élément [`Entry`](xref:Xamarin.Forms.Entry) et appuyez sur la touche de retour. Ensuite, mettez l’application en arrière-plan en appuyant sur le bouton d’accueil pour appeler la méthode `OnSleep`.

    Pour terminer, lancez l’application à partir de Visual Studio pour Mac et le texte qui a été entré précédemment dans l’élément [`Entry`](xref:Xamarin.Forms.Entry) sera restauré :

    [![Capture d’écran d’une entrée dont la propriété Text est conservée après les changements d’état du cycle de vie, sur iOS et Android](../images/persist-data.png "Entrée dont la propriété Text est conservée après les changements d’état du cycle de vie")](../images/persist-data-large.png#lightbox "Entrée dont la propriété Text est conservée après les changements d’état du cycle de vie")

    Pour plus d’informations sur la conservation des données dans le dictionnaire de propriétés, consultez la page [Dictionnaire de propriétés](~/xamarin-forms/app-fundamentals/application-class.md#properties-dictionary) dans le guide [Classe App Xamarin.Forms](~/xamarin-forms/app-fundamentals/application-class.md).
