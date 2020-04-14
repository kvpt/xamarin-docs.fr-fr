---
ms.openlocfilehash: 247e75435f42a49d5d1ea01a4d0ec3da67866156
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "67277219"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une application Xamarin.Forms vide nommée **appLifecycleTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **AppLifecycleTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans l’**Explorateur de solutions**, dans le projet **AppLifecycleTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **App.xaml.cs**, mettez à jour les substitutions `OnStart`, `OnSleep` et `OnResume` comme suit :

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Ce code met à jour les substitutions de méthodes de cycle de vie des applications, avec des instructions `Console.WriteLine` qui indiquent le moment où chaque méthode a été appelée :

    - La méthode `OnStart` est appelée au démarrage de l’application.
    - La méthode `OnSleep` est appelée lorsque l’application passe en arrière-plan.
    - La méthode `OnResume` est appelée lorsque l’application est relancée depuis l’arrière-plan.

    > [!NOTE]
    > Il n’existe aucune méthode pour arrêter l’application. Dans des circonstances normales, l’arrêt de l’application est effectué à partir de la méthode `OnSleep`.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Lorsque l’application démarre, la méthode `OnStart` est appelée, et le résultat **OnStart** est affiché dans la fenêtre de **sortie** de Visual Studio :

    ```
    [Mono] Found as 'java_interop_jnienv_get_object_array_element'.
    OnStart
    [OpenGLRenderer] HWUI GL Pipeline
    ```

    Lorsque l’application passe en arrière-plan (en appuyant sur le bouton d’accueil iOS ou Android), la méthode `OnSleep` est appelée :

    ```
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    OnSleep
    [Mono] Image addref System.Runtime.Serialization[0x83ee19c0] -> System.Runtime.Serialization.dll[0x83f57b00]: 2
    ```

    Ensuite, lorsque l’application est relancée depuis l’arrière-plan (en appuyant sur l’icône d’application sur iOS ou en appuyant sur le bouton d’aperçu sur Android, puis en sélectionnant l’application AppLifecycleTutorial), la méthode `OnResume` est appelée :

    ```
    Thread finished: <Thread Pool> #5
    OnResume
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    ```

    > [!NOTE]
    > Ces blocs de code illustrent un exemple de sortie lors de l’exécution de l’application sur Android.

    Pour plus d’informations sur le cycle de vie des applications Xamarin.Forms, consultez la page [Cycle de vie des applications Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une application Xamarin.Forms vide nommée **appLifecycleTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **AppLifecycleTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **Panneau Solutions**, dans le projet **AppLifecycleTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **App.xaml.cs**, mettez à jour les substitutions `OnStart`, `OnSleep` et `OnResume` comme suit :

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Ce code met à jour les substitutions de méthodes de cycle de vie des applications, avec des instructions `Console.WriteLine` qui indiquent le moment où chaque méthode a été appelée :

    - La méthode `OnStart` est appelée au démarrage de l’application.
    - La méthode `OnSleep` est appelée lorsque l’application passe en arrière-plan.
    - La méthode `OnResume` est appelée lorsque l’application est relancée depuis l’arrière-plan.

    > [!NOTE]
    > Il n’existe aucune méthode pour arrêter l’application. Dans des circonstances normales, l’arrêt de l’application est effectué à partir de la méthode `OnSleep`.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Lorsque l’application démarre, la méthode `OnStart` est appelée, et le résultat **OnStart** est affiché dans la fenêtre de **sortie d’application** de Visual Studio pour Mac :

    ```
    2019-02-11 12:05:23.164761+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:05:23.165297+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    2019-02-11 12:05:23.685430+0000 AppLifecycleTutorial.iOS[4089:361037] OnStart
    ```

    Lorsque l’application passe en arrière-plan (en appuyant sur le bouton d’accueil iOS ou Android), la méthode `OnSleep` est appelée :

    ```
    2019-02-11 12:06:12.394301+0000 AppLifecycleTutorial.iOS[4089:361037] OnSleep
    Thread started: <Thread Pool> #3
    Thread started: <Thread Pool> #4
    Thread started: <Thread Pool> #5
    Thread started: <Thread Pool> #6
    2019-02-11 12:06:13.056968+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:06:13.057264+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    ```

    Ensuite, lorsque l’application est relancée depuis l’arrière-plan (en appuyant sur l’icône d’application sur iOS ou en appuyant sur le bouton d’aperçu sur Android, puis en sélectionnant l’application AppLifecycleTutorial), la méthode `OnResume` est appelée :

    ```
    2019-02-11 12:07:10.321905+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4130
    2019-02-11 12:07:10.322557+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskCopyDebugDescription: AppLifecycleTuto[4130]/0#-1 LF=0
    2019-02-11 12:07:17.110938+0000 AppLifecycleTutorial.iOS[4130:365695] OnResume
    ```

    > [!NOTE]
    > Ces blocs de code illustrent un exemple de sortie lors de l’exécution de l’application sur iOS.

    Pour plus d’informations sur le cycle de vie des applications Xamarin.Forms, consultez la page [Cycle de vie des applications Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).
