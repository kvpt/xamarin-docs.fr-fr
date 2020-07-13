---
ms.openlocfilehash: eda3fa134d318ca787432cf5c5a2115b93c3614e
ms.sourcegitcommit: 898ba8e5140ae32a7df7e07c056aff65f6fe4260
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86277919"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une application Xamarin.Forms vide nommée **LocalDatabaseTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **LocalDatabaseTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **l’Explorateur de solutions**, sélectionnez le projet **LocalDatabaseTutorial**, cliquez avec le bouton droit et sélectionnez **Gérer les packages NuGet...**  :

    ![Capture d’écran de l’élément de menu « Gérer les packages NuGet » sélectionné](../images/vs/add-nuget-packages.png "Élément de menu Ajouter des packages NuGet")

1. Dans le **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Installer** pour l’ajouter au projet :

    ![Capture d’écran du package NuGet SQLite.NET dans le Gestionnaire de package NuGet](../images/vs/add-package.png "Package NuGet SQLite.NET")

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **ID :** sqlite-net-pcl
    > - **Author(s) :** SQLite-net
    > - **Propriétaire (s) :** praeclarum
    > - **URL du projet :** https://github.com/praeclarum/sqlite-net
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une application Xamarin.Forms vide nommée **LocalDatabaseTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **LocalDatabaseTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **Panneau Solutions**, sélectionnez le projet **LocalDatabaseTutorial**, cliquez avec le bouton droit et sélectionnez **Gérer les packages NuGet...**  :

    ![Capture d’écran de l’élément de menu « Ajouter des packages NuGet » sélectionné](../images/vsmac/add-nuget-packages.png "Élément de menu Ajouter des packages NuGet")

1. Dans la fenêtre **Ajouter des packages**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Ajouter un package** pour l’ajouter au projet :

    ![Capture d’écran du package NuGet SQLite.NET dans le Gestionnaire de package NuGet](../images/vsmac/add-package.png "Package NuGet SQLite.NET")

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Author :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.
