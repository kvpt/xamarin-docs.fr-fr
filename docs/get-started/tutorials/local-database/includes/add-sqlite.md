---
ms.openlocfilehash: c174a52e413db255c0d6cfedf22b89be85f03f75
ms.sourcegitcommit: 2105091f2eeb7844b19ae94708a6ab07e3e79bce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84200330"
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
    > - **Créé par :** Frank A. Krueger (praeclarum)
    > - **ID :** sqlite-net-pcl
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
