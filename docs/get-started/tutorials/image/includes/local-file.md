---
ms.openlocfilehash: 93ee0681adcc63fe05b4be88ff67f0aeee3e03ca
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "61384491"
---
Vous pouvez ajouter des fichiers d’image à des projets de plateforme et les référencer à partir d’un code partagé Xamarin.Forms. Cette méthode de distribution d’images est indispensable dans le cas d’images spécifiques à la plateforme, par exemple si vous utilisez différentes résolutions sur différentes plateformes ou des conceptions qui présentent quelques variations mineures.

Dans cet exercice, vous allez modifier la solution **ImageTutorial** pour afficher une image locale, plutôt qu’une image téléchargée à partir d’un URI. L’image locale représente le logo de Xamarin, que vous devez télécharger en cliquant sur le bouton ci-dessous.

> [!div class="nextstepaction"]
> [Download XamarinLogo.png](https://raw.githubusercontent.com/xamarin/xamarin-forms-samples/master/UserInterface/PlatformSpecifics/Droid/Resources/drawable/XamarinLogo.png)

> [!IMPORTANT]
> Pour utiliser une seule image sur l’ensemble des plateformes, *le même nom de fichier doit être utilisé sur toutes les plateformes*. Il doit s’agir d’un nom de ressource Android valide (autrement dit, seuls les lettres minuscules, les chiffres, le trait de soulignement et le point sont autorisés).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **l’Explorateur de solutions**, à l’intérieur du projet **ImageTutorial.iOS**, développez **Catalogues de composants** et double-cliquez sur **Composants** pour l’ouvrir. Sous l’onglet **Assets.xcassets**, cliquez sur le bouton **Plus** et sélectionnez **Ajouter un ensemble d’images** :

    ![Capture d’écran montrant la création d’un ensemble d’images dans le catalogue de composants de Visual Studio](../images/vs/new-image-set.png "Nouvel ensemble d’images du catalogue de composants")

1. Sous l’onglet **Assets.xcassets**, sélectionnez le nouvel ensemble d’images. L’éditeur s’affiche :

    ![Capture d’écran montrant le nouvel ensemble d’images dans le catalogue de composants de Visual Studio](../images/vs/new-image-set-editor.png "Éditeur montrant l’ensemble d’images du catalogue de composants")

1. Faites glisser le fichier **XamarinLogo.png** de votre système de fichiers vers la zone **1 x** dans la catégorie **Universel** :

    ![Capture d’écran de l’ensemble d’images contenant une image dans Visual Studio](../images/vs/image-set-with-image.png "Ensemble d’images contenant une image")

1. Sous l’onglet **Assets.xcassets**, cliquez avec le bouton droit sur le nom du nouvel ensemble d’images et donnez-lui le nom **XamarinLogo** :

    ![Capture d’écran de l’ensemble d’images renommé dans Visual Studio](../images/vs/rename-image-set.png "Ensemble d’images renommé")

    Enregistrez et fermez l’onglet **Assets.xcassets**.

1. Dans **l’Explorateur de solutions**, à l’intérieur du projet **ImageTutorial.Android**, développez le dossier **Ressources**. Faites ensuite glisser le fichier **XamarinLogo.png** de votre système de fichiers vers le dossier **drawable** :

    ![Capture d’écran du fichier image en tant que ressource Android dans Visual Studio](../images/vs/android-resource.png "Fichier image local dans le dossier des ressources Android")

    > [!NOTE]
    > Visual Studio définit automatiquement l’action de génération de l’image sur **AndroidResource**.

1. Dans le projet **ImageTutorial**, sous **MainPage.xaml**, modifiez la déclaration [`Image`](xref:Xamarin.Forms.Editor) pour afficher le fichier **XamarinLogo** local :

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Ce code définit la propriété [`Source`](xref:Xamarin.Forms.Image.Source) sur le fichier local à afficher. La propriété [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) est définie sur 300 unités indépendantes du périphérique sur iOS et sur 250 unités indépendantes du périphérique sur Android. En outre, la propriété [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) indique que l’image est centrée horizontalement.

    > [!NOTE]
    > Pour les images PNG sur iOS, l’extension **.png** peut être omise dans le nom de fichier spécifié dans la propriété [`Source`](xref:Xamarin.Forms.Image.Source). Pour les autres formats d’image, l’extension est requise.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une vue d’image affichant une image locale, sur iOS et Android](../images/local-file.png "Vue d’image montrant une image locale")](../images/local-file-large.png#lightbox "Vue d’image montrant une image locale")

    Pour plus d’informations sur les images locales, consultez la rubrique [Images locales](~/xamarin-forms/user-interface/images.md#local-images) du guide [Images dans Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **Panneau Solutions**, à l’intérieur du projet **ImageTutorial.iOS**, double-cliquez sur le fichier **Assets.xcassets** pour l’ouvrir. Puis, dans **Liste des biens**, cliquez avec le bouton droit et sélectionnez **Nouveau jeu d’images** :

    ![Capture d’écran montrant la création d’un ensemble d’images dans le catalogue de composants de Visual Studio pour Mac](../images/vsmac/new-image-set.png "Nouvel ensemble d’images du catalogue de composants")

1. Dans **Liste des biens**, sélectionnez le nouvel ensemble d’images. L’éditeur s’affiche :

    ![Capture d’écran montrant le nouvel ensemble d’images dans le catalogue de composants de Visual Studio pour Mac](../images/vsmac/new-image-set-editor.png "Éditeur montrant l’ensemble d’images du catalogue de composants")

1. Faites glisser le fichier **XamarinLogo.png** de votre système de fichiers vers la zone **1 x** dans la catégorie **Universel** :

    ![Capture d’écran de l’ensemble d’images contenant une image dans Visual Studio pour Mac](../images/vsmac/image-set-with-image.png "Ensemble d’images contenant une image")

1. Dans **Liste des biens**, double-cliquez sur le nom du nouvel ensemble d’images et donnez-lui le nom **XamarinLogo** :

    ![Capture d’écran de l’ensemble d’images renommé dans Visual Studio pour Mac](../images/vsmac/rename-image-set.png "Ensemble d’images renommé")

1. Dans **Panneau Solutions**, à l’intérieur du projet **ImageTutorial.Android**, développez le dossier **Ressources**. Faites ensuite glisser le fichier **XamarinLogo.png** de votre système de fichiers vers le dossier **drawable**.

1. Dans la boîte de dialogue **Ajouter un fichier à un dossier**, cliquez sur **OK**.

    ![Capture d’écran du fichier image en tant que ressource Android dans Visual Studio pour Mac](../images/vsmac/android-resource.png "Fichier image local dans le dossier des ressources Android")

    > [!NOTE]
    > Visual Studio pour Mac définit automatiquement l’action de génération de l’image sur **AndroidResource**.

1. Dans le projet **ImageTutorial**, sous **MainPage.xaml**, modifiez la déclaration [`Image`](xref:Xamarin.Forms.Editor) pour afficher le fichier **XamarinLogo** local :

    ```xaml
    <Image Source="XamarinLogo"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Ce code définit la propriété [`Source`](xref:Xamarin.Forms.Image.Source) sur le fichier local à afficher. La propriété [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) est définie sur 300 unités indépendantes du périphérique sur iOS et sur 250 unités indépendantes du périphérique sur Android. En outre, la propriété [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) indique que l’image est centrée horizontalement.

    > [!NOTE]
    > Pour les images PNG sur iOS, l’extension **.png** peut être omise dans le nom de fichier spécifié dans la propriété [`Source`](xref:Xamarin.Forms.Image.Source). Pour les autres formats d’image, l’extension est requise.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une vue d’image affichant une image locale, sur iOS et Android](../images/local-file.png "Vue d’image montrant une image locale")](../images/local-file-large.png#lightbox "Vue d’image montrant une image locale")

    Pour plus d’informations sur les images locales, consultez la rubrique [Images locales](~/xamarin-forms/user-interface/images.md#local-images) du guide [Images dans Xamarin.Forms](~/xamarin-forms/user-interface/images.md).
