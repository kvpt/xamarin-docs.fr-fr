---
ms.openlocfilehash: cef0b8f56639e7bc8571ab01b820dfd54b074472
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277228"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une application Xamarin.Forms vide nommée **WebServiceTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **WebServiceTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **l’Explorateur de solutions**, sélectionnez le projet **WebServiceTutorial**, cliquez avec le bouton droit et sélectionnez **Gérer les packages NuGet...**  :

    ![Capture d’écran de l’élément de menu Ajouter des Packages NuGet sélectionné](../images/vs/add-nuget-packages.png "Élément de menu Ajouter des Packages NuGet")

1. Dans le **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, recherchez et sélectionnez le package **Newtonsoft.Json**, puis cliquez sur le bouton **Installer** pour l’ajouter au projet :

    ![Capture d’écran du package NuGet Newtonsoft.Json dans le Gestionnaire de package NuGet](../images/vs/add-package.png "Package NuGet Newtonsoft.Json")

    Ce package sera utilisé pour incorporer la désérialisation JSON dans l’application.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une application Xamarin.Forms vide nommée **WebServiceTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **WebServiceTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **Panneau Solutions**, sélectionnez le projet **WebServiceTutorial**, cliquez avec le bouton droit et sélectionnez **Gérer les packages NuGet...**  :

    ![Capture d’écran de l’élément de menu Ajouter des Packages NuGet sélectionné](../images/vsmac/add-nuget-packages.png "Élément de menu Ajouter des Packages NuGet")

1. Dans la fenêtre **Ajouter des packages**, recherchez et sélectionnez le package NuGet **Newtonsoft.Json**, puis cliquez sur le bouton **Ajouter un package** pour l’ajouter au projet :

    ![Capture d’écran du package NuGet Newtonsoft.Json dans le Gestionnaire de package NuGet](../images/vsmac/add-package.png "Package NuGet Newtonsoft.Json")

    Ce package sera utilisé pour incorporer la désérialisation JSON dans l’application.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.
