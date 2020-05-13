---
title: Bien démarrer avec Xamarin.Essentials
description: Xamarin.Essentials fournit une API inter-plateformes unique qui fonctionne avec n’importe quelle application Android, iOS ou UWP qui est accessible à partir du code partagé, quel que soit le mode de création de l’interface utilisateur.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 05/11/2020
ms.openlocfilehash: 944b01d67fb09f9a21a19fb2ede9eb217d89732a
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149984"
---
# <a name="get-started-with-xamarinessentials"></a>Bien démarrer avec Xamarin.Essentials

Xamarin.Essentials fournit une API inter-plateformes unique qui fonctionne avec n’importe quelle application Android, iOS ou UWP qui est accessible à partir du code partagé, quel que soit le mode de création de l’interface utilisateur. Pour plus d’informations sur les systèmes d’exploitation pris en charge, consultez le [guide de prise en charge des plateformes et des fonctionnalités](platform-feature-support.md).

## <a name="installation"></a>Installation

Xamarin. Essentials est disponible sous la forme d’un package NuGet et est inclus dans chaque nouveau projet dans Visual Studio. Il peut également être ajouté à tout à l’aide de Visual Studio, en suivant les étapes ci-dessous.

1. Téléchargez et installez [Visual Studio](https://visualstudio.microsoft.com/) avec [Visual Studio Tools pour Xamarin](~/get-started/installation/index.md).

2. Ouvrez un projet existant ou créez un projet à l’aide du modèle d’application vide sous **Visual Studio C# ** (Android, iPhone et iPad ou inter-plateformes).

    > [!IMPORTANT]
    > En cas d’ajout à un projet UWP, vérifiez que la build 16299 ou ultérieure est définie dans les propriétés du projet.

3. Ajoutez le package NuGet [**Xamarin. Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) à chaque projet :

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    Dans le panneau de l’Explorateur de solutions, cliquez avec le bouton droit sur le nom de la solution et sélectionnez **Gérer les packages NuGet**. Recherchez **Xamarin.Essentials** et installez le package dans **Tous** les projets, notamment les bibliothèques Android, iOS, UWP et .NET Standard.

    # <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

    Dans le volet Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter > ajouter des packages NuGet...**. Recherchez **Xamarin. Essentials** et installez le package dans **tous les** projets, y compris les bibliothèques Android, iOS et .NET standard.

    -----

4. Ajoutez une référence à Xamarin.Essentials dans n’importe quelle classe C# pour référencer les API.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials nécessite une configuration spécifique à la plateforme :

    # <a name="android"></a>[Android](#tab/android)

    Xamarin. Essentials prend en charge une version Android minimale de 4,4, correspondant au niveau d’API 19, mais la version d’Android cible pour la compilation doit être 9,0 ou 10,0, ce qui correspond au niveau d’API 28 et au niveau 29. (Dans Visual Studio, ces deux versions sont définies dans la boîte de dialogue Propriétés du projet pour le projet Android, sous l’onglet manifeste Android. Dans Visual Studio pour Mac, ils sont définis dans la boîte de dialogue Options du projet pour le projet Android, sous l’onglet application Android.)

    Lors de la compilation par rapport à Android 9,0, Xamarin. Essentials installe la version 28.0.0.3 des bibliothèques Xamarin. Android. support nécessaires. Toutes les autres bibliothèques Xamarin. Android. support requises par votre application doivent également être mises à jour vers la version 28.0.0.3 à l’aide du gestionnaire de package NuGet. Toutes les bibliothèques Xamarin. Android. support utilisées par votre application doivent être identiques et doivent avoir au moins la version 28.0.0.3. Consultez la [page Résolution des problèmes](troubleshooting.md) si vous rencontrez des problèmes d’ajout du package NuGet Xamarin.Essentials ou de mise à jour des packages NuGet dans votre solution.

    À partir de la version 1.5.0 lors de la compilation par rapport à Android 10,0, Xamarin. Essentials installe les bibliothèques de prise en charge AndroidX requises. Lisez la [documentation de AndroidX](https://docs.microsoft.com/xamarin/android/platform/androidx) si vous n’avez pas encore effectué la transition.

    Dans le ou les projets Android `MainLauncher` `Activity` lancés, Xamarin. Essentials doit être initialisé dans la `OnCreate` méthode :

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    Pour gérer les autorisations d’exécution sur Android, Xamarin.Essentials doit recevoir `OnRequestPermissionsResult`. Ajoutez le code suivant à toutes les classes `Activity` :

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="ios"></a>[iOS](#tab/ios)

    Aucune configuration supplémentaire n’est requise.

    # <a name="uwp"></a>[UWP](#tab/uwp)

    Aucune configuration supplémentaire n’est requise.

    -----

6. Suivez les [guides Xamarin.Essentials](index.md) qui vous permettent de copier et de coller des extraits de code pour chaque fonctionnalité.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials - APIs inter-plateformes pour Mobile Apps (vidéo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Autres ressources

Nous recommandons aux développeurs noXamarins de [découvrir la prise en main du développement Xamarin](~/cross-platform/getting-started/index.md).

Consultez le [référentiel GitHub Xamarin.Essentials](https://github.com/xamarin/Essentials) pour afficher le code source actuel, ce qui se prépare, des exemples d’exécution et pour cloner le référentiel. Les contributions de la communauté sont les bienvenues !

Parcourez la [documentation de l’API](xref:Xamarin.Essentials) pour découvrir toutes les fonctionnalités de Xamarin.Essentials.
