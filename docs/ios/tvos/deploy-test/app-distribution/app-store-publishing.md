---
title: Publication sur l’App Store Apple TV
description: Ce document décrit comment publier une application sur le Store d’application Apple TV. Il explique comment configurer, configurer, générer et soumettre une application tvOS générée avec Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 73ee7fc3c28fc7a8476010e8bf7567b3e5ef590d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865091"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publication sur l’App Store Apple TV

Dans l’ordre de distribuer des applications à tous les appareils Apple TV, Apple exige que les applications pour être publié via la *Apple TV App Store*, ce qui l’App Store à l’emplacement d’achat guichet unique pour les applications tvOS. Les développeurs de nombreux types d’applications peuvent en majuscules sur le succès massif de ce point de distribution unique. Le Store d’application Apple TV est une solution clé en main, offrant aux développeurs d’applications de systèmes de distribution et de paiement.

Le processus de soumission d’une application vers le Store d’application Apple TV implique :

1. Création d’un *ID d’application* et sélection de *droits*
2. Création d’un *profil de provisionnement de distribution*
3. À l’aide de ce profil pour générer votre application.
4. Soumission de votre application via *iTunes Connect*.


Dans cet article, nous aborderons toutes les étapes nécessaires pour configurer, générer et soumettre une application pour la distribution de l’Apple TV App Store.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Avant de soumettre une application

Une fois que vous envoyez une application pour la publication vers le Store d’application Apple TV, il passe par un processus de révision par Apple pour vérifier qu’elle respecte les règles d’Apple pour la qualité et de contenu. Si votre application ne respecte pas ces recommandations, Apple la rejettera. Vous devrez alors corriger la non-conformité citée par Apple, puis soumettre à nouveau votre application.
Ainsi, vous avez de plus grandes chances de réussir l’évaluation effectuée par Apple en vous familiarisant avec ces recommandations et en essayant d’adapter votre application à ces dernières. Instructions d’Apple sont disponibles à l’adresse [directives de révision App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) et [préparer votre application soumise la TV Apple](https://developer.apple.com/tvos/submit/).

Voici quelques éléments à surveiller quand vous soumettez une application :

1. Assurez-vous que la description de l’application correspond à la fonctionnalité incluse dans l’application.
2. Vérifiez que l’application ne plante pas dans des conditions d’utilisation normales. Cela inclut l’utilisation sur tous les appareils Apple TV que vous prenez en charge.


Apple gère également une liste de conseils pour la soumission Apple TV App Store. Vous pouvez les lire sur la page décrivant la [distribution d’applications sur l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configuration de votre application dans iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) est une suite d’outils basée sur le web pour, entre autres choses, la gestion de vos applications tvOS sur le Store d’application Apple TV. Votre application Xamarin.tvOS devra être correctement le programme d’installation et configurées dans iTunes Connect avant qu’il peut être envoyée à Apple pour être examinée et au final, être libéré à la vente ou comme une application gratuite dans le Store d’application Apple TV.

Effectuez ce qui suit :

1. Vérifiez que les contrats appropriés sont en place et à jour dans la section **Agreements, Tax, and Banking (Contrats, taxes et informations bancaires)** d’iTunes Connect pour pouvoir publier une application iOS gratuite ou payante.
2. Créer un nouveau **enregistrement iTunes Connect** pour l’application et spécifiez son **surnom** (comme indiqué dans le Store d’application Apple TV).
3. Sélectionnez un **Sale Price (Prix de vente)** ou indiquez que l’application est disponible gratuitement.
4. Fournir un **App Store icône** (grande icône) et des captures d’écran de votre application en action, sur les appareils Apple TV il prend en charge. Consultez notre [utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) guide pour plus d’informations.
5. Fournir un clair, concis **Description** de l’application, notamment ses fonctionnalités et avantages pour l’utilisateur final.
6. Fournir **catégories**, **sous-catégories**, et **mots clés** pour aider l’utilisateur à trouver votre application dans le Store d’application Apple TV.
7. Fournissez les URL de **Contact** et de **Support** sur votre site web (condition imposée par Apple).
8. Valeur de votre application **évaluation**, qui est utilisé par le contrôle parental dans le Store d’application Apple TV.
9. Configurez les technologies facultatives de l’App Store, par exemple **Game Center** et **In-App Purchase (Achat dans l’application)** .

Pour plus d’informations, consultez notre [configurer votre application tvOS dans iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) documentation.

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Préparation à la distribution via l’App Store

Pour publier une application dans le Store d’application Apple TV, vous devez d’abord générer pour la distribution, ce qui implique de nombreuses étapes. Les sections suivantes décrivent toutes les opérations nécessaires pour préparer une application Xamarin.tvOS publication afin qu’il peut être généré et soumettez-le sur le Store d’application Apple TV pour révision et mise en production.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Provisionnement des services d’application

Apple propose une sélection de Services d’Application spéciaux, également appelés droits, ce qui peuvent être activés pour votre application tvOS lorsque vous créez un ID unique pour celle-ci. Si vous utilisez des droits personnalisés ou non, vous devez toujours créer un ID unique pour votre application Xamarin.tvOS avant de pouvoir être publié sur le Store d’application Apple TV.

La création d’un ID d’application et, éventuellement, la sélection de droits impliquent l’exécution des étapes suivantes via le portail web de provisionnement iOS d’Apple :

1. Sélectionnez **approvisionnement** > **développement**.
2. Cliquez sur le bouton **+** , puis indiquez un **Name (Nom)** et un **Bundle ID (ID de bundle)** pour la nouvelle application.
3. Faites défiler vers le bas de l’écran et sélectionnez celles **App Services** qui sera requis par votre application Xamarin.tvOS.
4. Cliquez sur le bouton **Continue** et suivez les instructions affichées à l’écran pour créer l’ID d’application.

Outre les sélectionnant et en configurant les Services d’Application requis lors de la définition de votre ID d’application, vous devez également configurer l’ID d’application et des habilitations dans votre projet Xamarin.tvOS en modifiant les deux le `Info.plist` et `Entitlements.plist` fichiers.

Procédez comme suit dans Visual Studio pour Mac :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Info.plist` pour l’ouvrir et le modifier.
2. Dans le **cible d’Application tvOS** section, entrez un nom pour votre application et entrez le **identificateur de Bundle** que vous avez créé lorsque vous avez défini l’application ID.
3. Enregistrez les changements dans le fichier `Info.plist`.
4. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Entitlements.plist` pour l’ouvrir et le modifier.
5. Sélectionnez et configurez les droits nécessaires pour vous Xamarin.tvOS application afin qu’ils correspondent à la configuration effectuée ci-dessus lorsque vous avez défini l’application ID.
6. Enregistrez les changements dans le fichier `Entitlements.plist`.

Pour obtenir des instructions détaillées, consultez [Provisionnement des services d’application](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices) dans notre documentation. Bien que ce document a été écrit pour iOS, les mêmes étapes sont utilisés pour configurer une application Xamarin.tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Définition des icônes d’applications, Image de lancement et Image d’étagère supérieure

Pour une application tvOS soit acceptée par Apple pour être inclus dans le Store d’application Apple TV, il nécessite des icônes correctes, lancement haut étagère images et pour tous les appareils Apple TV qu’elle s’exécute sur. Vous devez ajouterez les ressources d’Image requis qui sera compilé dans un `Assets.car` de fichiers et inclus dans votre Xamarin.tvOS bundle de l’application avant d’être téléchargée dans iTunes Connect.

Pour obtenir des instructions détaillées, consultez notre [utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) documentation.

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Création et installation d’un profil de distribution

utilise tvOS *profils de provisionnement* pour contrôler la façon dont une build d’application particulière peut être déployée. Il s’agit de fichiers qui contiennent des informations sur le certificat utilisé pour signer une application, sur l’*ID de l’application* et sur l’emplacement d’installation de l’application. Pour des tâches de développement et de distribution ad hoc, le profil de provisionnement inclut également la liste des appareils autorisés sur lesquels vous pouvez déployer l’application. Toutefois, pour la distribution de l’Apple TV App Store, seules les informations de ID certificat et d’application sont inclus, étant le seul mécanisme permettant de distribuer publiquement via le Store d’application Apple TV.

Le provisionnement implique l’exécution des étapes suivantes à partir du portail web de provisionnement iOS d’Apple :

1.  Sélectionnez **Provisioning (Provisionnement)**  > **Distribution**.
2.  Cliquez sur le **+** bouton et sélectionnez le type de profil de Distribution que vous souhaitez créer en tant que **Apple TV App Store**.
3.  Dans la liste déroulante, sélectionnez l’**ID d’application** pour lequel vous souhaitez créer un profil de distribution.
4.  Sélectionnez le certificat nécessaire pour signer l’application.
5.  Entrez un **Name (Nom)** pour le nouveau **Distribution Profile (Profil de distribution)** , puis générez le profil.
6.  Actualiser la liste des profils disponibles dans Xcode.
7.  Sélectionnez la Distribution de configuration du profil dans Visual Studio pour le **App Store** _Configuration de Build_.

Pour obtenir des instructions détaillées, consultez [Création d’un profil de distribution](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) et [Sélection d’un profil de distribution dans un projet Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). Là encore, les deux de ces documents sont spécifiques à iOS, mais la même technique est utilisée pour les applications tvOS.


<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Définition de la configuration de build pour votre application

Par défaut, lorsque vous créez une nouvelle application Xamarin.tvOS, _des Configurations de Build_ sont créées automatiquement pour les deux **déboguer** et **version** déploiement. Avant de procéder à la version finale de votre application que vous enverrez à Apple, il existe quelques modifications que vous devrez apporter à la base de **version** configuration.

Effectuez ce qui suit :

1. Avec le bouton droit sur le **nom_projet** dans le **l’Explorateur de solutions** et sélection **Options** pour les ouvrir pour modification.
2. Si vous ciblez une version spécifique de tvOS, sélectionnez-le sous **Build tvOS** > **iOS Version du SDK**. Pour la version préliminaire de la prise en charge de tvOS, laissez cette valeur est définie sur **par défaut**.
3. Réduit la taille globale de votre application distribuable en éliminant les Classes inutilisées de méthodes, propriétés, etc. dans la plupart des cas doivent être conservés à la valeur par défaut de **lien SDK framework uniquement**. Dans certaines situations, par exemple lorsque certains spécifique 3e bibliothèques tierces, vous devrez peut-être définir cette valeur sur **ne sont pas liées** pour empêcher que nécessaire d’élément en cours de suppression.
4. Pour expédier une application Xamarin.tvOS, vous devez utiliser le compilateur d’optimisation LLVM. Vérifiez que le **utiliser le compilateur d’optimisation LLVM** case est cochée sous le **version** configuration.
5. Apple également nécessaire que les applications tvOS utilisent bitcode. À nouveau sous le **version** configuration, ajoutez `--bitcode=asmonly` à la **arguments mtouch supplémentaires** boîte.
6. Le **fichiers d’image PNG optimiser pour iOS** case à cocher doit être vérifiée, car cela vous aidera à réduire davantage de taille du fichier livrable de votre application.
7. Le débogage doit *pas* être activée car cela rend la build inutilement supérieure.


<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Génération et soumission du fichier distribuable

Avec votre application Xamarin.tvOS correctement configurée, vous êtes maintenant prêt à effectuer la build de distribution finale que vous soumettez à Apple pour révision et mise en production.

#### <a name="build-your-archive"></a>Générer votre archive

1. Sélectionnez la configuration **Mise en production | Appareil** dans Visual Studio pour Mac :

    ![](app-store-publishing-images/buildxs01new.png "Sélectionnez la configuration Release")
2. Dans le menu **Générer**, sélectionnez **Archiver pour publication** :

    [![](app-store-publishing-images/buildxs02new.png "Sélectionner Archiver pour publication")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Une fois l’archive créée, l’affichage **Archives** est présenté :

    [![](app-store-publishing-images/buildxs03new.png "La vue Archives")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Signer et distribuer votre application

Chaque fois que vous générez votre application pour qu’elle soit archivée, l’*affichage Archives* s’ouvre automatiquement, ce qui entraîne l’affichage de tous les projets archivés, regroupés par solution. Par défaut, cet affichage montre uniquement la solution actuelle, ouverte. Pour afficher toutes les solutions ayant des archives, cliquez sur l’option **Afficher toutes les archives**.

Il est recommandé de conserver les archives déployées auprès des clients (déploiements App Store ou Entreprise) pour que les informations de débogage générées puissent être symbolisées plus tard.

Pour signer votre application et préparer sa distribution :

1. Sélectionnez le **signer et distribuer...** , illustré ci-dessous :

    [![](app-store-publishing-images/buildxs04new.png ", Sélectionnez theSign et distribuer...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. Cela entraîne l’ouverture de l’Assistant Publication. Sélectionnez le canal de distribution **App Store** pour créer un paquet, puis ouvrez Application Loader :

    [![](app-store-publishing-images/distribute01.png "Sélectionnez le canal de distribution App Store")](app-store-publishing-images/distribute01.png#lightbox)
3. Dans l’écran profil de provisionnement, sélectionnez votre identité de signature et profil de provisionnement correspondant, ou resigner avec une autre identité :

    [![](app-store-publishing-images/distribute02.png "Sélectionnez l’identité de signature et profil de provisionnement correspondant")](app-store-publishing-images/distribute02.png#lightbox)
4. Vérifiez les détails du paquet, puis cliquez sur **Publier** pour enregistrer votre paquet `.ipa` :

    [![](app-store-publishing-images/distribute03.png "Vérifiez les détails du package")](app-store-publishing-images/distribute03.png#lightbox)
5. Une fois le paquet `.ipa` enregistré, votre application est prête à être chargée sur iTunes Connect via Application Loader :

    [![](app-store-publishing-images/distribute04.png "Chargée sur iTunes Connect via Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

Une fois votre build de distribution créée et archivée, vous êtes prêt à soumettre votre application à iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Soumission de votre application à Apple

Une fois que vous avez effectué la build de distribution, vous êtes prêt à soumettre votre application iOS à Apple pour qu’elle soit évaluée et, le cas échéant, publiée sur l’App Store.


Le workflow d’archivage dans Visual Studio pour Mac s’ouvre automatiquement, le chargeur d’Application une fois que vous avez enregistré le `.ipa`:

1. Sélectionnez *Deliver Your App (Livrer votre application)* , puis cliquez sur le bouton *Choose (Choisir)*  :

    [![](app-store-publishing-images/publishvs01.png "Sélectionner Livrer votre application")](app-store-publishing-images/publishvs01.png#lightbox)

2. Sélectionnez le fichier zip ou IPA créé ci-dessus, puis cliquez sur le bouton **OK**.
3. Application Loader valide le fichier :

    [![](app-store-publishing-images/publishvs02.png "L’écran de validation du chargeur d’Application")](app-store-publishing-images/publishvs02.png#lightbox)
4. Cliquez sur le bouton *Suivant* pour permettre la validation de l’application par l’App Store :

    [![](app-store-publishing-images/publishvs03.png "L’application en cours de validation par rapport à l’App Store")](app-store-publishing-images/publishvs03.png#lightbox)
5. Cliquez sur le bouton **Envoyer** pour envoyer l’application à Apple pour évaluation.
6. Application Loader vous informe une fois le chargement du fichier réussi.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>État dans iTunes Connect

Si vous reconnectez à iTunes Connect et sélectionnez votre application dans la liste des applications disponibles, l’état dans iTunes Connect doit maintenant indiquer qu’il s’agit **en attente de révision** (il peut lire temporairement **télécharger reçu** pendant le traitement) :

[![](app-store-publishing-images/image21.png "L’état dans iTunes connecter affichant en attente pour révision")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes de soumission de votre application Xamarin.tvOS vers le Store d’application Apple TV, veuillez consulter notre [dépannage](~/ios/tvos/troubleshooting.md) guide. Il contient plusieurs problèmes connus que vous pouvez rencontrer et comment les résoudre dans les Xamarin.tvOS.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté un guide pas à pas pour la configuration, la création et la soumission d’une application pour la publication de l’Apple TV App Store. Tout d’abord, il a décrit les étapes nécessaires pour créer et installer un profil de provisionnement de distribution. Ensuite, il a expliqué comment utiliser Visual Studio pour Mac pour créer une build de distribution. Enfin, il vous a montré comment utiliser iTunes Connect et l’outil d’archivage Xcode pour soumettre une application vers le Store d’application Apple TV.


## <a name="related-links"></a>Liens associés

- [Utilisation des icônes et des images](~/ios/tvos/app-fundamentals/icons-images.md)
- [Préparer votre soumission de l’application pour le nouveau Apple TV](https://developer.apple.com/tvos/submit/)
- [Conseils pour la soumission à l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Motifs courants de rejet d’application](https://developer.apple.com/app-store/review/rejections/)
- [Recommandations pour l’évaluation dans l’App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
