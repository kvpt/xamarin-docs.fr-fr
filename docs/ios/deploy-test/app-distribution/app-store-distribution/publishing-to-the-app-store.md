---
title: Publication d’applications Xamarin.iOS sur l’App Store
description: Ce document explique comment configurer, générer et publier une application Xamarin.iOS pour la distribuer dans l’App Store.
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: 9f9504890b12f6a3d8046e98ce537cc0d742f2ed
ms.sourcegitcommit: 159f250ae7535fca7ab3bc4dc7395dc4cc2af93f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278137"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>Publication d’applications Xamarin.iOS sur l’App Store

Pour publier une application dans l’[App Store](https://www.apple.com/ios/app-store/), un développeur d’applications doit d’abord la soumettre (avec des captures d’écran, une description, des icônes et autres informations) à Apple pour vérification. Une fois qu’Apple a approuvé l’application, il l’ajoute sur l’App Store, où les utilisateurs peuvent l’acheter et l’installer directement sur leurs appareils iOS.

Ce guide décrit les étapes à suivre pour préparer une application à sa publication sur l’App Store et l’envoyer à Apple pour vérification. Plus précisément, il décrit les actions suivantes :

> [!div class="checklist"]
>
> - Suivre les instructions de vérification de l’App Store
> - Configurer un ID d’application et des droits
> - Fournir une icône de l’App Store et des icônes de l’application
> - Configurer un profil de provisionnement App Store
> - Mettre à jour la configuration de build **Release**
> - Configurer votre application dans iTunes Connect
> - Générer votre application et la soumettre à Apple

> [!IMPORTANT]
> Apple [a indiqué](https://developer.apple.com/ios/submit/) qu’à partir de mars 2019 toutes les applications et mises à jour envoyées à l’App Store doivent avoir été créées avec le kit SDK d’iOS 12.1 ou ultérieur, inclus dans Xcode 10.1 ou ultérieur.
> Les applications doivent aussi prendre en charge les tailles d’écran de l’iPhone XS et de l’iPad Pro 12,9".

## <a name="app-store-guidelines"></a>Instructions de l’App Store

Avant de soumettre une application pour la publier sur l’App Store, vérifiez qu’elle répond aux standards définis par les [instructions de vérification de l’App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) Apple.
Lorsque vous soumettez une application à l’App Store, Apple la vérifie pour s’assurer qu’elle répond à ces critères. Si ce n’est pas le cas, Apple la rejette et vous devrez résoudre les problèmes mentionnés avant de la soumettre à nouveau.
Par conséquent, il est judicieux de vous familiariser avec les instructions aussitôt que possible dans le processus de développement.

Voici quelques éléments à surveiller quand vous soumettez une application :

1. Assurez-vous que la description de l’application correspond à sa fonction.
2. Vérifiez que l’application ne plante pas dans des conditions d’utilisation normales. Cela inclut son utilisation sur tous les appareils iOS qu’elle prend en charge.

Regardez également les [ressources associées à l’App Store](https://developer.apple.com/app-store/resources/) que fournit Apple.

## <a name="set-up-an-app-id-and-entitlements"></a>Configurer un ID d’application et des droits

Chaque application iOS a un ID d’application unique, qui est associé à un ensemble de services d’application appelé *droits*. Les droits autorisent les applications à effectuer différentes opérations telles que recevoir une notification push, accéder aux fonctionnalités iOS comme HealthKit et plus encore.

Pour créer un ID d’application et sélectionner tous les droits nécessaires, visitez le [portail des développeurs Apple](https://developer.apple.com/account/) et procédez comme suit :

1. Dans la section **Certificates, Identifiers & Profiles** (Certificats, identificateurs et profils), sélectionnez **Identifiers > App IDs** (Identificateurs -> ID d’application).
2. Cliquez sur le bouton **+** , puis indiquez un **Name (Nom)** et un **Bundle ID (ID de bundle)** pour la nouvelle application.
3. Faites défiler l’affichage vers le bas de l’écran, puis sélectionnez les **services d’application** nécessaires pour votre application Xamarin.iOS. Les services d’application sont décrits en détail dans le guide [Utilisation des fonctionnalités dans Xamarin.iOS](~/ios/deploy-test/provisioning/capabilities/index.md).
4. Cliquez sur le bouton **Continue** et suivez les instructions affichées à l’écran pour créer l’ID d’application.

En plus de sélectionner et configurer les services d’application nécessaires quand vous définissez l’ID d’application, vous devez configurer l’ID d’application et les droits dans votre projet Xamarin.iOS en modifiant les fichiers **Info.plist** et  **Entitlements.plist**. Pour plus d’informations, consultez le guide [Utilisation des droits dans Xamarin.iOS](~/ios/deploy-test/provisioning/entitlements.md), qui explique comment créer un fichier **Entitlements.plist** et la signification des différents paramètres de droits qu’il contient.

## <a name="include-an-app-store-icon"></a>Inclure une icône de l’App Store

Lorsque vous soumettez une application à Apple, veillez à ce qu’elle contienne un catalogue de ressources présentant une icône de l’App Store. Pour savoir comment procéder, consultez le guide [Icônes App Store dans Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-store-icon.md).

## <a name="set-the-apps-icons-and-launch-screens"></a>Définir les icônes et les écrans de lancement de l’application

Avec Apple, pour qu’une application iOS soit mise à disposition sur l’App Store, elle doit présenter des icônes et des écrans de lancement adaptés à tous les appareils iOS sur lesquels elle peut s’exécuter. Pour plus d’informations sur la configuration des icônes et des écrans de lancement d’une application, consultez les guides suivants :

- [Icônes d’application dans Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Écrans de lancement pour les applications Xamarin.iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)

## <a name="create-and-install-an-app-store-provisioning-profile"></a>Créer et installer un profil de provisionnement App Store

iOS utilise des *profils de provisionnement* pour contrôler la façon dont une build d’application particulière peut être déployée. Il s’agit de fichiers qui contiennent des informations sur le certificat utilisé pour signer une application, sur l’ID d’application et sur l’emplacement d’installation de l’application. Pour des tâches de développement et de distribution ad hoc, le profil de provisionnement contient également la liste des appareils autorisés sur lesquels vous pouvez déployer l’application. Toutefois, pour la distribution dans l’App Store, seules les informations sur le certificat et l’ID d’application sont incluses, car l’unique système de distribution publique est l’App Store.

Pour créer et installer un profil de provisionnement App Store, procédez comme suit :

1. Connectez-vous au [portail des développeurs Apple](https://developer.apple.com/account/).
2. Dans **Certificates, IDs & Profiles**, sélectionnez **Provisioning Profiles > Distribution**.
3. Cliquez sur le bouton **+** , sélectionnez **App Store** et cliquez sur **Continue**.
4. Sélectionnez l’**ID d’application** de votre application dans la liste et cliquez sur **Continue**.
5. Sélectionnez un certificat de signature et cliquez sur **Continue**.
6. Entrez un **nom de profil** et cliquez sur **Continue** pour générer le profil.
7. Utilisez les outils de [gestion des comptes Apple](~/cross-platform/macios/apple-account-management.md) de Xamarin pour télécharger le profil de provisionnement nouvellement créé sur votre Mac. Si vous êtes sur Mac, vous pouvez également télécharger le profil de provisionnement directement du portail des développeurs Apple et double-cliquer dessus pour l’installer.

Pour des instructions détaillées, consultez [Création d’un profil de distribution](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) et [Sélection d’un profil de distribution dans un projet Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile).

## <a name="update-the-release-build-configuration"></a>Mettre à jour la configuration de build Release

Les nouveaux projets Xamarin.iOS configurent automatiquement les **configurations de build** **Debug** et _Release_. Pour configurer correctement la build **Release**, effectuez ces étapes :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. À partir du **panneau Solutions**, ouvrez **Info.plist**. Sélectionnez **Provisionnement manuel**. Enregistrez et fermez le fichier.
2. Cliquez avec le bouton droit sur le **Nom du projet** dans le **panneau Solutions**, sélectionnez **Options** et accédez à l’onglet **Build iOS**.
3. Définissez **Configuration** sur **Release** et **Plateforme** sur **iPhone**.
4. Pour générer avec un kit SDK iOS spécifique, sélectionnez-le dans la liste **Version du SDK**. Sinon, laissez cette valeur sur **Par défaut**.
5. L’édition des liens réduit la taille globale de votre application en éliminant le code inutilisé. Dans la plupart des cas, le **comportement de l’éditeur de liens** doit être défini sur la valeur par défaut **Lier les SDK Framework uniquement**. Dans certaines situations, par exemple quand vous utilisez des bibliothèques tierces, il peut être nécessaire de définir cette valeur sur **Ne pas lier** pour être sûr que le code nécessaire n’est pas supprimé. Pour plus d’informations, reportez-vous au guide [Édition des liens des applications Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Cochez **Optimiser les images PNG** pour réduire encore davantage la taille de votre application.
7. Vous ne devez _pas_ activer le débogage, car cela grossit inutilement la build.
8. Pour iOS 11, sélectionnez l’une des architectures d’appareil qui prend en charge **ARM64**. Pour plus d’informations sur la génération d’applications pour les appareils iOS 64 bits, consultez la section **Activation de builds 64 bits d’applications Xamarin.iOS** dans la documentation traitant des [considérations sur les plateformes 32/64 bits](~/cross-platform/macios/32-and-64/index.md).
9. Vous pouvez utiliser le compilateur **LLVM** pour générer du code plus court et plus rapide. Toutefois, cette option allonge la durée de compilation.
10. En fonction des besoins de votre application, vous pouvez également ajuster le type de **Garbage Collection** utilisé et configurer une **internationalisation**.

    Après avoir défini les options décrites ci-dessus, vos paramètres de build doivent ressembler à ceci :

    ![Paramètres de build iOS](publishing-to-the-app-store-images/build-m157.png "Paramètres de build iOS")

    Examinez également ce guide sur les [systèmes de build d’iOS](~/ios/deploy-test/ios-build-mechanics.md), qui décrit en détail les paramètres de build.

11. Accédez à l’onglet **Signature du bundle iOS**. Si les options ici ne sont pas modifiables, vérifiez que **Provisionnement manuel** est sélectionné dans le fichier **Info.plist**.
12. Assurez-vous que **Configuration** est défini sur **Release** et que **Plateforme** est défini sur **iPhone**.
13. Définissez **Identité de signature** sur **Distribution (automatique)** .
14. Pour **Profil de provisionnement**, sélectionnez le profil de provisionnement App Store [créé ci-dessus](#create-and-install-an-app-store-provisioning-profile).

    Les options de signature du bundle de votre projet doivent maintenant ressembler à ceci :

    ![Signature du bundle iOS](publishing-to-the-app-store-images/bundleSigning-m157.png "Signature du bundle iOS")

15. Cliquez sur **OK** pour enregistrer les changements apportés aux propriétés du projet.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Vérifiez que Visual Studio 2019 ou Visual Studio 2017 a été [appairé à un hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Cliquez avec le bouton droit sur le **Nom du projet** dans **l’Explorateur de solutions** et sélectionnez **Propriétés**.
3. Accédez à l’onglet **Build iOS**, puis définissez **Configuration** sur **Release** et **Plateforme** sur **iPhone**.
4. Pour générer avec un kit SDK iOS spécifique, sélectionnez-le dans la liste **Version du SDK**. Sinon, laissez cette valeur sur **Par défaut**.
5. L’édition des liens réduit la taille globale de votre application en éliminant le code inutilisé. Dans la plupart des cas, le **comportement de l’éditeur de liens** doit être défini sur la valeur par défaut **Lier les SDK Framework uniquement**. Dans certaines situations, par exemple quand vous utilisez des bibliothèques tierces, il peut être nécessaire de définir cette valeur sur **Ne pas lier** pour être sûr que le code nécessaire n’est pas supprimé. Pour plus d’informations, reportez-vous au guide [Édition des liens des applications Xamarin.iOS](~/ios/deploy-test/linker.md).
6. Cochez **Optimiser les images PNG** pour réduire encore davantage la taille de votre application.
7. Vous ne devez pas activer le débogage, car cela grossit la build inutilement.
8. Pour iOS 11, sélectionnez l’une des architectures d’appareil qui prend en charge **ARM64**. Pour plus d’informations sur la génération d’applications pour les appareils iOS 64 bits, consultez la section **Activation de builds 64 bits d’applications Xamarin.iOS** dans la documentation traitant des [considérations sur les plateformes 32/64 bits](~/cross-platform/macios/32-and-64/index.md).
9. Vous pouvez utiliser le compilateur **LLVM** pour générer du code plus court et plus rapide. Toutefois, cette option allonge la durée de compilation.
10. En fonction des besoins de votre application, vous pouvez également ajuster le type de **Garbage Collection** utilisé et configurer une **internationalisation**.

    Après avoir défini les options décrites ci-dessus, vos paramètres de build doivent ressembler à ceci :

    ![Paramètres de build iOS](publishing-to-the-app-store-images/build-w157.png "Paramètres de build iOS")

    Examinez également ce guide sur les [systèmes de build d’iOS](~/ios/deploy-test/ios-build-mechanics.md), qui décrit en détail les paramètres de build.

11. Accédez à l’onglet **Signature du bundle iOS**. Assurez-vous que **Configuration** est défini sur **Release**, que **Plateforme** est défini sur **iPhone** et que **Provisionnement manuel** est sélectionné.
12. Définissez **Identité de signature** sur **Distribution (automatique)** .
13. Pour **Profil de provisionnement**, sélectionnez le profil de provisionnement App Store [créé ci-dessus](#create-and-install-an-app-store-provisioning-profile).

    Les options de signature du bundle de votre projet doivent maintenant ressembler à ceci :

    ![Paramètres de signature du bundle iOS](publishing-to-the-app-store-images/bundleSigning-w157.png "Paramètres de signature du bundle iOS")

14. Accédez à l’onglet **Options IPA iOS**.
15. Assurez-vous que **Configuration** est défini sur **Release** et que **Plateforme** est défini sur **iPhone**.
16. Cochez la case **Générer une archive IPA (iTunes Package Archive)** . Avec ce paramètre, chaque build **Release** (puisque c’est la configuration sélectionnée) génère un fichier .ipa. Ce fichier peut être soumis à Apple pour publication sur l’App Store.

    > [!NOTE]
    > **Métadonnées iTunes** et **iTunesArtwork** ne sont pas nécessaires pour les publications de l’App Store. Pour plus d’informations, consultez [le fichier iTunesMetadata.plist dans les applications Xamarin.iOS](~/ios/deploy-test/app-distribution/itunesmetadata.md) et [Itunes Artwork](~/ios/app-fundamentals/images-icons/app-icons.md#itunes-artwork).

17. Pour spécifier un nom de fichier .ipa qui diffère du nom de projet Xamarin.iOS, entrez-le dans le champ **Nom du package**.

    ![Paramètres de signature du bundle iOS](publishing-to-the-app-store-images/ipaOptions-w157.png "Paramètres de signature du bundle iOS")

18. Enregistrez la configuration de build et fermez-la.

-----

## <a name="configure-your-app-in-itunes-connect"></a>Configurer votre application dans iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com) est une suite d’outils web qui vous permet de gérer vos applications iOS sur l’App Store. Vous devez configurer correctement votre application Xamarin.iOS dans iTunes Connect avant de pouvoir la soumettre à Apple pour vérification et la publier sur l’App Store.

Pour savoir comment procéder, lisez le guide [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

## <a name="build-and-submit-your-app"></a>Générer et soumettre votre application

Avec vos paramètres de build correctement configurés et iTunes Connect en attente de votre soumission, vous pouvez générer votre application et la soumettre à Apple.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans Visual Studio pour Mac, sélectionnez la configuration de build **Release** et un appareil (pas un simulateur) pour lequel effectuer la génération.

    ![Configuration de la build et sélection de la plateforme](publishing-to-the-app-store-images/chooseConfig-m157.png "Configuration de la build et sélection de la plateforme")

2. Dans le menu **Générer**, sélectionnez **Archiver pour publication**.
3. Une fois l’archive créée, la vue **Archives** s’affiche. Cliquez sur **Signer et distribuer...**  pour ouvrir l’Assistant de publication.


    ![Capture d’écran de l’emplacement du bouton signer et distribuer dans la vue Archives.](publishing-to-the-app-store-images/archives-mac.png "Capture d’écran de l’emplacement du bouton signer et distribuer dans la vue Archives.")

    > [!NOTE]
    > Par défaut, la vue **Archives** montre uniquement les archives de la solution ouverte. Pour voir toutes les solutions ayant des archives, cochez la case **Afficher toutes les archives**. Il est judicieux de conserver les anciennes archives afin que les informations de débogage qu’elles contiennent puissent être utilisées pour générer les symboles de débogage des rapports de plantage si nécessaire.

4. Sélectionnez le canal de distribution **App Store**. Cliquez sur **Suivant**.

5. Sélectionnez **charger** comme destination. Cliquez sur **Suivant**.

6. Dans la fenêtre **Profil de provisionnement**, sélectionnez votre identité de signature, votre application et votre profil de provisionnement. Cliquez sur **Suivant**.

    ![Capture d’écran de la page de l’Assistant Profil d’approvisionnement montrant une identité de signature, une application et une sélection de profil de provisionnement valides.](publishing-to-the-app-store-images/provProfileSelect-mac.png "Capture d’écran de la page de l’Assistant Profil d’approvisionnement avec une identité de signature, une application et un profil de provisionnement valides sélectionnés.")

7. Dans la fenêtre informations de connexion à l' **App Store** , sélectionnez un nom d’utilisateur Apple ID dans le menu et entrez [un mot de passe spécifique à l’application](https://support.apple.com/ht204397). Cliquez sur **Suivant**.

    ![Capture d’écran de la page de l’Assistant informations de connexion de l’App Store avec un nom d’utilisateur Apple ID sélectionné.](publishing-to-the-app-store-images/connectInfo-mac.png "Capture d’écran de la page de l’Assistant informations de connexion de l’App Store avec un nom d’utilisateur Apple ID sélectionné.")

8. Vérifiez les détails de votre package, puis cliquez sur **publier**. Une fois que vous avez sélectionné un emplacement pour enregistrer le fichier. Loi, l’Assistant charge votre application dans l’App Store Connect.

    > [!NOTE]
    > Apple peut rejeter les applications avec **iTunesMetadata.plist** inclus dans le fichier .ipa, ce qui entraîne une erreur comme la suivante :
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > Pour résoudre cette erreur, consultez [ce post sur les forums Xamarin](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Actuellement, Visual Studio 2017 ne prend pas en charge le workflow **Archiver pour publication** qui se trouve dans Visual Studio pour Mac.

1. Vérifiez que Visual Studio 2019 ou Visual Studio 2017 a été [appairé à un hôte de build Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Sélectionnez **Release** dans la liste déroulante **Configurations de solutions** de Visual Studio 2017, puis **iPhone** dans la liste déroulante **Plateformes de solutions**.

    ![Configuration de la build et sélection de la plateforme](publishing-to-the-app-store-images/chooseConfig-w157.png "Configuration de la build et sélection de la plateforme")

3. Générez le projet. Cette opération crée un fichier .ipa.

    > [!NOTE]
    > La section [Mettre à jour la configuration de build Release](#update-the-release-build-configuration) de ce document a permis de configurer les paramètres de build de l’application afin de créer un fichier .ipa pour chaque build **Release**.

4. Pour trouver le fichier .ipa sur l’ordinateur Windows, cliquez avec le bouton droit sur le nom du projet Xamarin.iOS dans l’**Explorateur de solutions** de Visual Studio 2019 ou Visual Studio 2017 et choisissez **Ouvrir le dossier dans l’Explorateur de fichiers**. Ensuite, dans l’**Explorateur de fichiers** Windows qui vient de s’ouvrir, accédez au sous-répertoire **bin/iPhone/Release**. À moins d’avoir [personnalisé l’emplacement de sortie du fichier .ipa](#customize-the-ipa-location), vous devriez le trouver dans ce répertoire.
5. Pour voir le fichier .ipa plutôt sur l’hôte de build Mac, cliquez avec le bouton droit sur le nom du projet Xamarin.iOS dans l’**Explorateur de solutions** de Visual Studio 2019 ou Visual Studio 2017 (sur Windows) et sélectionnez **Afficher le fichier IPA sur le serveur de builds**. La fenêtre du **Finder** s’ouvre sur l’hôte de build Mac avec le fichier .ipa sélectionné.
6. Sur l’hôte de build Mac, ouvrez **Application Loader**. Dans Xcode, sélectionnez **Xcode > Open Developer Tool (Ouvrir l’outil de développement) > Application Loader**.

    > [!NOTE]
    > Pour plus d’informations sur l’outil, consultez la [documentation d’Apple sur Application Loader](https://help.apple.com/itc/apploader/#/apdS673accdb).

7. Connectez-vous à Application Loader (notez que vous devez [créer un mot de passe spécifique à l’application](https://support.apple.com/ht204397) pour votre identifiant Apple).
8. Sélectionnez **Deliver Your App (Livrer votre application)** , puis cliquez sur le bouton **Choose (Choisir)**  :

    ![Sélectionner Distribuer votre app](publishing-to-the-app-store-images/publishvs01.png "Sélectionner Distribuer votre app")

9. Sélectionnez le fichier .ipa créé ci-dessus et cliquez sur **OK**.
10. Application Loader valide le fichier :

    ![Écran de validation](publishing-to-the-app-store-images/publishvs02.png "Écran de validation")

11. Cliquez sur le bouton **Suivant** pour permettre la validation de l’application par l’App Store :

    ![Validation sur l’App Store](publishing-to-the-app-store-images/publishvs03.png "Validation sur l’App Store")

12. Cliquez sur le bouton **Envoyer** pour envoyer l’application à Apple pour évaluation.
13. Application Loader vous informe une fois le chargement du fichier réussi.

    > [!NOTE]
    > Apple peut rejeter les applications avec **iTunesMetadata.plist** inclus dans le fichier .ipa, ce qui entraîne une erreur comme la suivante :
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > Pour résoudre cette erreur, consultez [ce post sur les forums Xamarin](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1).

-----

## <a name="itunes-connect-status"></a>Statut dans iTunes Connect

Pour voir le statut de la soumission de votre application, connectez-vous à iTunes Connect et sélectionnez votre application. Le statut initial doit être **En attente de vérification**, même si vous pouvez lire temporairement **Chargement reçu** pendant son traitement.

![En attente de vérification](publishing-to-the-app-store-images/image21.png "En attente de vérification")

## <a name="tips-and-tricks"></a>Conseils et astuces

### <a name="customize-the-ipa-location"></a>Personnaliser l’emplacement du fichier IPA

Une propriété **MSBuild**, `IpaPackageDir`, permet de personnaliser l’emplacement de sortie du fichier .ipa. Si `IpaPackageDir` est défini sur un emplacement personnalisé, le fichier .ipa se trouve à cet emplacement et non dans le sous-répertoire horodaté par défaut. Cela peut être utile quand vous créez des builds automatisées qui reposent sur un chemin de répertoire spécifique pour fonctionner correctement, par exemple les build d’intégration continue (CI).

Il existe plusieurs façons d’utiliser la nouvelle propriété. Par exemple, pour envoyer le fichier .ipa vers l’ancien répertoire par défaut (comme dans Xamarin.iOS 9.6 et antérieur), vous pouvez affecter à la propriété `IpaPackageDir` la valeur `$(OutputPath)` en utilisant l’une des approches suivantes. Les deux approches sont compatibles avec toutes les builds d’API unifiée Xamarin.iOS, notamment les builds d’IDE et les builds de ligne de commande qui utilisent **msbuild** ou **mdtool** :

- La première option consiste à définir la propriété `IpaPackageDir` dans un élément `<PropertyGroup>` d’un fichier **MSBuild**. Par exemple, vous pouvez ajouter le `<PropertyGroup>` suivant au bas du fichier .csproj du projet d’application iOS (juste avant la balise de fermeture `</Project>`) :

    ```xml
    <PropertyGroup>
      <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- Une meilleure approche consiste à ajouter un élément `<IpaPackageDir>` au bas du `<PropertyGroup>` existant, qui correspond à la configuration utilisée pour générer le fichier .ipa. Cette solution est préférable, car elle permet de préparer le projet pour une compatibilité future avec un paramètre planifié de la page de propriétés de projet dans les Options IPA iOS. Si vous utilisez la configuration `Release|iPhone` pour générer le fichier .ipa, l’ensemble du groupe de propriétés mis à jour peut ressembler à ce qui suit :

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone'">
       <Optimize>true</Optimize>
       <OutputPath>bin\iPhone\Release</OutputPath>
       <ErrorReport>prompt</ErrorReport>
       <WarningLevel>4</WarningLevel>
       <ConsolePause>false</ConsolePause>
       <CodesignKey>iPhone Developer</CodesignKey>
       <MtouchUseSGen>true</MtouchUseSGen>
       <MtouchUseRefCounting>true</MtouchUseRefCounting>
       <MtouchFloat32>true</MtouchFloat32>
       <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
       <MtouchLink>SdkOnly</MtouchLink>
       <MtouchArch>ARMv7, ARM64</MtouchArch>
       <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
       <MtouchTlsProvider>Default</MtouchTlsProvider>
       <BuildIpa>true</BuildIpa>
       <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

Il existe une autre technique pour les builds de ligne de commande **msbuild**. Elle consiste à ajouter un argument de ligne de commande `/p:` pour définir la propriété `IpaPackageDir`. Dans ce cas, notez que **msbuild** ne développe pas les expressions `$()` passées sur la ligne de commande, il est donc impossible d’utiliser la syntaxe `$(OutputPath)`. À la place, vous devez fournir un chemin complet.

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

Ou sur Mac :

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

Une fois votre build de distribution créée et archivée, vous êtes prêt à soumettre votre application à iTunes Connect.

## <a name="summary"></a>Récapitulatif

Cet article a décrit comment configurer, générer et soumettre une application iOS pour la publier sur l’App Store.

## <a name="related-links"></a>Liens connexes

- [Apple Developer Portal (Apple)](https://developer.apple.com/account/)
- [iTunes Connect (Apple)](https://itunesconnect.apple.com)
- [Instructions de vérification de l’App Store (Apple)](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Motifs courants de rejet d’application (Apple)](https://developer.apple.com/app-store/review/rejections/)
- [Utilisation des fonctionnalités dans Xamarin.iOS](~/ios/deploy-test/provisioning/capabilities/index.md)
- [Utilisation des droits dans Xamarin.iOS](~/ios/deploy-test/provisioning/entitlements.md)
- [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Icônes d’application dans Xamarin.iOS](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Écrans de lancement pour les applications Xamarin.iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
- [Documentation d’Application Loader (Apple)](https://help.apple.com/itc/apploader/#/apdS673accdb)
