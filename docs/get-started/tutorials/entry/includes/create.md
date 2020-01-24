---
ms.openlocfilehash: 820761111c609f224a6dda14d5853777d22aa259
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "67277212"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une application Xamarin.Forms vide nommée **EntryTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **EntryTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **l’Explorateur de solutions**, dans le projet **EntryTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EntryTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry Placeholder="Enter text" />
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`Entry`](xref:Xamarin.Forms.Entry) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propriété [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) spécifie le texte d’espace réservé qui apparaît lorsque `Entry` s’affiche pour la première fois.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une entrée, sur iOS et Android](../images/create-entry.png "Entrée contenant du texte d’espace réservé")](../images/create-entry-large.png#lightbox "Entrée contenant du texte d’espace réservé")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une application Xamarin.Forms vide nommée **EntryTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **EntryTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **Panneau Solutions**, dans le projet **EntryTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EntryTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Entry Placeholder="Enter text" />
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`Entry`](xref:Xamarin.Forms.Entry) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propriété [`Entry.Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) spécifie le texte d’espace réservé qui apparaît lorsque l’élément `Entry` s’affiche pour la première fois.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une entrée, sur iOS et Android](../images/create-entry.png "Entrée contenant du texte d’espace réservé")](../images/create-entry-large.png#lightbox "Entrée contenant du texte d’espace réservé")
