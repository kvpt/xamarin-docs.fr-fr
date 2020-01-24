---
ms.openlocfilehash: b1a041f1a2baae9b06de023f6eae9c6598b80061
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "72678717"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une application Xamarin.Forms vide nommée **GridTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **GridTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **l’Explorateur de solutions**, dans le projet **GridTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`Label`](xref:Xamarin.Forms.Label) dans [`Grid`](xref:Xamarin.Forms.Grid). Par défaut, l’élément `Grid` positionne ses vues enfants dans un emplacement unique. Par conséquent, lorsqu’il contient plusieurs enfants, un élément `Grid` doit spécifier des colonnes et des lignes. Nous aborderons ce sujet dans l’exercice suivant. En outre, la propriété [`Margin`](xref:Xamarin.Forms.View.Margin) indique la position de rendu de l’élément `Grid` dans [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Outre la propriété [`Margin`](xref:Xamarin.Forms.View.Margin), la propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) peut également être définie sur [`Grid`](xref:Xamarin.Forms.Grid). La valeur de la propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) spécifie la distance entre les limites de la `Grid` et ses enfants. Pour plus d’informations, consultez la page [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une étiquette dans une grille, sur iOS et Android](../images/create-grid.png "Grille contenant une étiquette")](../images/create-grid-large.png#lightbox "Grille contenant une étiquette")

    Pour plus d’informations sur l’élément [`Grid`](xref:Xamarin.Forms.Grid), consultez la page [Grille Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une application Xamarin.Forms vide nommée **GridTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **GridTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **Panneau Solutions**, dans le projet **GridTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`Label`](xref:Xamarin.Forms.Label) dans [`Grid`](xref:Xamarin.Forms.Grid). Par défaut, l’élément `Grid` positionne ses vues enfants dans un emplacement unique. Par conséquent, lorsqu’il contient plusieurs enfants, un élément `Grid` doit spécifier des colonnes et des lignes. Nous aborderons ce sujet dans l’exercice suivant. En outre, la propriété [`Margin`](xref:Xamarin.Forms.View.Margin) indique la position de rendu de l’élément `Grid` dans [`ContentPage`](xref:Xamarin.Forms.ContentPage).

    > [!NOTE]
    > Outre la propriété [`Margin`](xref:Xamarin.Forms.View.Margin), la propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) peut également être définie sur [`Grid`](xref:Xamarin.Forms.Grid). La valeur de propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) spécifie la distance entre les vues dans l’élément `Grid`. Pour plus d’informations, consultez la page [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une étiquette dans une grille, sur iOS et Android](../images/create-grid.png "Grille contenant une étiquette")](../images/create-grid-large.png#lightbox "Grille contenant une étiquette")

    Pour plus d’informations sur l’élément [`Grid`](xref:Xamarin.Forms.Grid), consultez la page [Grille Xamarin.Forms](~/xamarin-forms/user-interface/layouts/grid.md).
