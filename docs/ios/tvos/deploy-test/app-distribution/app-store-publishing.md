---
title: Publication sur l’App Store Apple TV
description: Ce document explique comment publier une application sur l’App Store d’Apple TV. Il explique comment configurer, approvisionner, générer et soumettre une application tvOS créée avec Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 4dface536504b0a79d376ab0979443a5ed19e901
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769250"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publication sur l’App Store Apple TV

Pour distribuer des applications à tous les appareils TV Apple, Apple requiert la publication d’applications via le magasin d’applications *Apple TV*, ce qui fait que l’application stocke l’emplacement d’achat unique pour les applications tvOS. Les développeurs de nombreux types d’applications peuvent être capitalisés sur la réussite massive de ce point de distribution unique. Apple TV App Store est une solution clé en main, qui offre aux développeurs d’applications des systèmes de distribution et de paiement.

Le processus d’envoi d’une application à Apple TV App Store implique les opérations suivantes :

1. Création d’un *ID d’application* et sélection de *droits*
2. Création d’un *profil de provisionnement de distribution*
3. Utilisation de ce profil pour générer votre application.
4. Envoi de votre application via *iTunes Connect*.

Dans cet article, nous allons aborder toutes les étapes nécessaires à l’approvisionnement, à la création et à l’envoi d’une application pour la distribution Apple TV App Store.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Avant de soumettre une application

Une fois que vous avez envoyé une application en vue de sa publication dans l’App Store d’Apple TV, elle passe par Apple pour s’assurer qu’elle respecte les instructions de qualité et de contenu d’Apple. Si votre application ne respecte pas ces recommandations, Apple la rejettera. Vous devrez alors corriger la non-conformité citée par Apple, puis soumettre à nouveau votre application.
Ainsi, vous avez de plus grandes chances de réussir l’évaluation effectuée par Apple en vous familiarisant avec ces recommandations et en essayant d’adapter votre application à ces dernières. Les instructions d’Apple sont disponibles dans les instructions de révision de l' [App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) et [préparent la soumission de votre application pour la nouvelle présentation Apple TV](https://developer.apple.com/tvos/submit/).

Voici quelques éléments à surveiller quand vous soumettez une application :

1. Assurez-vous que la description de l’application correspond à la fonctionnalité incluse dans l’application.
2. Vérifiez que l’application ne plante pas dans des conditions d’utilisation normales. Cela comprend l’utilisation sur chaque appareil TV Apple que vous prenez en charge.

Apple gère également une liste des conseils d’envoi d’Apple TV App Store. Vous pouvez les lire sur la page décrivant la [distribution d’applications sur l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configuration de votre application dans iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) est une suite d’outils Web pour, entre autres, la gestion de vos applications tvOS sur le magasin d’applications Apple TV. Votre application Xamarin. tvOS doit être correctement configurée et configurée dans iTunes Connect avant de pouvoir être envoyée à Apple pour examen et, en fin de contrat, être mise en vente ou en tant qu’application gratuite dans le magasin d’applications Apple TV.

Effectuez ce qui suit :

1. Vérifiez que les contrats appropriés sont en place et à jour dans la section **Agreements, Tax, and Banking (Contrats, taxes et informations bancaires)** d’iTunes Connect pour pouvoir publier une application iOS gratuite ou payante.
2. Créez un **enregistrement iTunes Connect** pour l’application et spécifiez son **nom complet** (comme indiqué dans le magasin d’applications Apple TV).
3. Sélectionnez un **Sale Price (Prix de vente)** ou indiquez que l’application est disponible gratuitement.
4. Fournissez une **icône App Store** (grande icône) et des captures d’écran de votre application en action sur les périphériques TV Apple qu’elle prend en charge. Pour plus d’informations, consultez notre guide d' [utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md) .
5. Fournissez une **Description** claire et succincte de l’application, notamment ses fonctionnalités et avantages pour l’utilisateur final.
6. Fournissez des **catégories**, des **sous-catégories**et des **Mots clés** pour aider l’utilisateur à trouver votre application dans le magasin d’applications Apple TV.
7. Fournissez les URL de **Contact** et de **Support** sur votre site web (condition imposée par Apple).
8. Définissez l' **évaluation**de votre application, qui est utilisée par les contrôles parentaux sur le magasin d’applications Apple TV.
9. Configurez les technologies facultatives de l’App Store, par exemple **Game Center** et **In-App Purchase (Achat dans l’application)** .

Pour plus d’informations, consultez notre documentation relative [à la configuration de votre application tvOS dans iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) .

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Préparation à la distribution via l’App Store

Pour publier une application dans l’App Store d’Apple TV, vous devez d’abord la créer en vue de sa distribution, ce qui implique de nombreuses étapes. Les sections suivantes traitent de tous les éléments nécessaires à la préparation d’une application Xamarin. tvOS pour la publication afin qu’elle puisse être générée et envoyée à l’App Store d’Apple TV à des fins de révision et de publication.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisionnement des services d’application

Apple fournit une sélection de Services d’application spéciales, également appelées droits, qui peuvent être activées pour votre application tvOS quand vous en créez un ID unique. Que vous utilisiez ou non des droits personnalisés, vous devrez toujours créer un ID unique pour votre application Xamarin. tvOS avant de la publier sur l’App Store d’Apple TV.

La création d’un ID d’application et, éventuellement, la sélection de droits impliquent l’exécution des étapes suivantes via le portail web de provisionnement iOS d’Apple :

1. Sélectionnez le**développement**de l' **approvisionnement** > .
2. Cliquez sur le bouton **+** , puis indiquez un **Name (Nom)** et un **Bundle ID (ID de bundle)** pour la nouvelle application.
3. Faites défiler l’écran vers le bas et sélectionnez les **app services** qui seront nécessaires à votre application Xamarin. tvOS.
4. Cliquez sur le bouton **Continue** et suivez les instructions affichées à l’écran pour créer l’ID d’application.

En plus de sélectionner et de configurer le services d’application requis lors de la définition de votre ID d’application, vous devez également configurer l’ID d’application et les droits dans votre projet Xamarin. tvOS en `Info.plist` modifiant `Entitlements.plist` les fichiers et.

Procédez comme suit dans Visual Studio pour Mac :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Info.plist` pour l’ouvrir et le modifier.
2. Dans la section **cible d’application tvOS** , indiquez un nom pour votre application et entrez l' **identificateur de Bundle** que vous avez créé lorsque vous avez défini l’ID de l’application.
3. Enregistrez les changements dans le fichier `Info.plist`.
4. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Entitlements.plist` pour l’ouvrir et le modifier.
5. Sélectionnez et configurez les droits requis pour votre application Xamarin. tvOS afin qu’ils correspondent à la configuration que vous avez effectuée ci-dessus lorsque vous avez défini l’ID de l’application.
6. Enregistrez les changements dans le fichier `Entitlements.plist`.

Pour obtenir des instructions détaillées, consultez [Provisionnement des services d’application](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices) dans notre documentation. Même si ce document a été écrit pour iOS, les mêmes étapes sont utilisées pour approvisionner une application Xamarin. tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Définition des icônes d’applications, de lancement d’image et d’image de haut rayon

Pour qu’une application tvOS soit acceptée par Apple pour l’inclure dans le magasin d’applications Apple TV, elle requiert des icônes, des images de lancement et des images de rayon supérieures pour tous les périphériques TV Apple sur lesquels elle s’exécute. Vous devez ajouter les ressources d’image requises qui seront compilées dans un `Assets.car` fichier et incluses dans le bundle de votre application Xamarin. tvOS avant qu’elles ne soient chargées dans iTunes Connect.

Pour obtenir des instructions détaillées, consultez notre documentation [sur l’utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Création et installation d’un profil de distribution

tvOS utilise les *profils de provisionnement* pour contrôler la façon dont une build d’application particulière peut être déployée. Il s’agit de fichiers qui contiennent des informations sur le certificat utilisé pour signer une application, sur l’*ID de l’application* et sur l’emplacement d’installation de l’application. Pour des tâches de développement et de distribution ad hoc, le profil de provisionnement inclut également la liste des appareils autorisés sur lesquels vous pouvez déployer l’application. Toutefois, pour la distribution d’Apple TV App Store, seules les informations sur les certificats et les ID d’application sont incluses, puisque le seul mécanisme de distribution publique est d’Apple TV App Store.

Le provisionnement implique l’exécution des étapes suivantes à partir du portail web de provisionnement iOS d’Apple :

1. Sélectionnez **Provisioning (Provisionnement)**  > **Distribution**.
2. Cliquez sur **+** le bouton et sélectionnez le type de profil de distribution que vous souhaitez créer comme **Apple TV App Store**.
3. Dans la liste déroulante, sélectionnez l’**ID d’application** pour lequel vous souhaitez créer un profil de distribution.
4. Sélectionnez le certificat requis pour signer l’application.
5. Entrez un **Name (Nom)** pour le nouveau **Distribution Profile (Profil de distribution)** , puis générez le profil.
6. Actualisez la liste des profils disponibles dans Xcode.
7. Sélectionnez le profil de provisionnement de distribution dans Visual Studio pour la _configuration de build_de l' **App Store** .

Pour obtenir des instructions détaillées, consultez [Création d’un profil de distribution](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) et [Sélection d’un profil de distribution dans un projet Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Là encore, ces deux documents sont spécifiques à iOS, mais la même technique est utilisée pour les applications tvOS.

<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Définition de la configuration de build pour votre application

Par défaut, lorsque vous créez une nouvelle application Xamarin. tvOS, les _configurations de build_ sont créées automatiquement pour le déploiement **Debug** et **Release** . Avant d’effectuer la version finale de votre application que vous allez soumettre à Apple, vous devez apporter quelques modifications à la configuration de la **version** de base.

Effectuez ce qui suit :

1. Cliquez avec le bouton droit sur le **nom du projet** dans les **options** **Explorateur de solutions** et sélection pour les ouvrir et les modifier.
2. Si vous ciblez une version spécifique de tvOS, sélectionnez-la sous **tvOS Build** > **IOS SDK version**. Pour la version préliminaire de la prise en charge de tvOS, laissez cette valeur définie sur **par défaut**.
3. La liaison réduit la taille globale de la distribution de votre application en supprimant les méthodes, les propriétés, les classes, etc., et dans la plupart des cas, il est préférable de laisser la valeur par défaut de **Link Framework SDK uniquement**. Dans certaines situations, par exemple lors de l’utilisation de bibliothèques tierces spécifiques, vous pouvez être contraint de définir cette valeur sur **ne pas lier** pour empêcher la suppression de l’élément nécessaire.
4. Pour livrer une application Xamarin. tvOS, vous devez utiliser le compilateur d’optimisation LLVM. Vérifiez que la case à cocher **utiliser le compilateur d’optimisation LLVM** est activée dans la configuration **Release** .
5. Apple exige également que les applications tvOS utilisent bitcode. Là encore, dans la configuration Release `--bitcode=asmonly` , ajoutez à la zone **arguments mTouch supplémentaires** .
6. La case à cocher **optimiser les fichiers image png pour iOS** doit être vérifiée, car cela vous aidera à réduire davantage la taille du livrable de votre application.
7. Le débogage ne doit *pas* être activé, car il rend la build inutilement plus grande.

<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Génération et soumission du fichier distribuable

Une fois votre application Xamarin. tvOS correctement configurée, vous êtes maintenant prêt à effectuer la build de distribution finale que vous enverrez à Apple pour la révision et la publication.

#### <a name="build-your-archive"></a>Générer votre archive

1. Sélectionnez la configuration **Mise en production | Appareil** dans Visual Studio pour Mac :

    ![](app-store-publishing-images/buildxs01new.png "Sélectionner la configuration Release")
2. Dans le menu **Générer**, sélectionnez **Archiver pour publication** :

    [![](app-store-publishing-images/buildxs02new.png "Sélectionner Archiver pour publication")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Une fois l’archive créée, l’affichage **Archives** est présenté :

    [![](app-store-publishing-images/buildxs03new.png "Vue Archives")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Signer et distribuer votre application

Chaque fois que vous générez votre application pour qu’elle soit archivée, l’*affichage Archives* s’ouvre automatiquement, ce qui entraîne l’affichage de tous les projets archivés, regroupés par solution. Par défaut, cet affichage montre uniquement la solution actuelle, ouverte. Pour afficher toutes les solutions ayant des archives, cliquez sur l’option **Afficher toutes les archives**.

Il est recommandé de conserver les archives déployées auprès des clients (déploiements App Store ou Entreprise) pour que les informations de débogage générées puissent être symbolisées plus tard.

Pour signer votre application et préparer sa distribution :

1. Sélectionnez **signer et distribuer...** , comme illustré ci-dessous :

    [![](app-store-publishing-images/buildxs04new.png ", Sélectionnez theSign et distribuer...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Cela entraîne l’ouverture de l’Assistant Publication. Sélectionnez le canal de distribution **App Store** pour créer un paquet, puis ouvrez Application Loader :

    [![](app-store-publishing-images/distribute01.png "Sélectionner le canal de distribution de l’App Store")](app-store-publishing-images/distribute01.png#lightbox)
3. Dans l’écran Profil de provisionnement, sélectionnez votre identité de signature et le profil de configuration correspondant, ou resignez avec une autre identité :

    [![](app-store-publishing-images/distribute02.png "Sélectionner l’identité de signature et le profil de provisionnement correspondant")](app-store-publishing-images/distribute02.png#lightbox)
4. Vérifiez les détails du paquet, puis cliquez sur **Publier** pour enregistrer votre paquet `.ipa` :

    [![](app-store-publishing-images/distribute03.png "Vérifier les détails du package")](app-store-publishing-images/distribute03.png#lightbox)
5. Une fois le paquet `.ipa` enregistré, votre application est prête à être chargée sur iTunes Connect via Application Loader :

    [![](app-store-publishing-images/distribute04.png "Téléchargé dans iTunes Connect via le chargeur d’application")](app-store-publishing-images/distribute04.png#lightbox)

Une fois votre build de distribution créée et archivée, vous êtes prêt à soumettre votre application à iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Soumission de votre application à Apple

Une fois que vous avez effectué la build de distribution, vous êtes prêt à soumettre votre application iOS à Apple pour qu’elle soit évaluée et, le cas échéant, publiée sur l’App Store.

Dans Visual Studio pour Mac, le flux de travail d’archivage ouvre automatiquement application Loader, une fois `.ipa`que vous avez enregistré :

1. Sélectionnez *Deliver Your App (Livrer votre application)* , puis cliquez sur le bouton *Choose (Choisir)*  :

    [![](app-store-publishing-images/publishvs01.png "Sélectionner Livrer votre application")](app-store-publishing-images/publishvs01.png#lightbox)

2. Sélectionnez le fichier zip ou IPA créé ci-dessus, puis cliquez sur le bouton **OK**.
3. Application Loader valide le fichier :

    [![](app-store-publishing-images/publishvs02.png "Écran de validation du chargeur d’application")](app-store-publishing-images/publishvs02.png#lightbox)
4. Cliquez sur le bouton *Suivant* pour permettre la validation de l’application par l’App Store :

    [![](app-store-publishing-images/publishvs03.png "L’application qui est validée par rapport à l’App Store")](app-store-publishing-images/publishvs03.png#lightbox)
5. Cliquez sur le bouton **Envoyer** pour envoyer l’application à Apple pour évaluation.
6. Application Loader vous informe une fois le chargement du fichier réussi.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>État dans iTunes Connect

Si vous vous reconnectez à iTunes Connect et que vous sélectionnez votre application dans la liste des applications disponibles, l’État dans iTunes Connect doit maintenant indiquer qu’elle est en **attente de révision** (elle peut lire temporairement le **Téléchargement reçu** pendant son traitement) :

[![](app-store-publishing-images/image21.png "État dans iTunes Connect, affichage en attente de la révision")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de l’envoi de votre application Xamarin. tvOS à Apple TV App Store, consultez notre guide de [Dépannage](~/ios/tvos/troubleshooting.md) . Il contient plusieurs problèmes connus que vous pouvez rencontrer et comment les résoudre dans Xamarin. tvOS.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté un guide pas à pas pour la configuration, la création et l’envoi d’une application pour la publication Apple TV App Store. Tout d’abord, il a décrit les étapes nécessaires pour créer et installer un profil de provisionnement de distribution. Ensuite, il explique comment utiliser Visual Studio pour Mac pour créer une build de distribution. Enfin, il vous a montré comment utiliser iTunes Connect et l’outil d’archivage Xcode pour soumettre une application à Apple TV App Store.

## <a name="related-links"></a>Liens associés

- [Utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md)
- [Préparer la soumission de votre application pour la nouvelle TV Apple](https://developer.apple.com/tvos/submit/)
- [Conseils pour la soumission à l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Motifs courants de rejet d’application](https://developer.apple.com/app-store/review/rejections/)
- [Recommandations pour l’évaluation dans l’App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
