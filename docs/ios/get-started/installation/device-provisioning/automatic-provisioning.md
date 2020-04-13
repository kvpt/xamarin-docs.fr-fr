---
title: Provisionnement automatique pour Xamarin.iOS
description: Après l’installation de Xamarin.iOS, l’étape suivante dans le développement iOS consiste à provisionner votre appareil iOS. Ce guide décrit l’utilisation de la signature automatique pour demander des certificats et profils de développement.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 069c40b74876bea1d3a0c8fca23b3d90c4b91635
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510677"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisionnement automatique pour Xamarin.iOS

_Une fois que Xamarin.iOS a été installé avec succès, la prochaine étape dans le développement d’iOS est de fournir votre appareil iOS. Ce guide explore l’utilisation de l’approvisionnement automatique pour demander des certificats de développement et des profils._

## <a name="requirements"></a>Spécifications

L’approvisionnement automatique est disponible sur Visual Studio pour Mac, Visual Studio 2019 et Visual Studio 2017 (Version 15.7 et plus). 

> [!NOTE]
> Vous devez également avoir un compte Apple Developer payé pour utiliser cette fonctionnalité. Plus d’informations sur les comptes des développeurs Apple sont disponibles dans le guide [de provisionnement des appareils.](~/ios/get-started/installation/device-provisioning/index.md)
> Si vous n’avez pas de compte de développeur Apple payant, consultez le guide [Free Provisioning for Xamarin.iOS] (MD/ios/get-started/installation/device-provisioning/free-provisioning.md).

> [!NOTE]
> Avant de commencer, assurez-vous d’abord d’accepter des accords de licence dans le [portail Apple Developer](https://developer.apple.com/account/) ou App Store [Connect](https://appstoreconnect.apple.com/).


## <a name="enable-automatic-provisioning"></a>Activer l’approvisionnement automatique

Avant de commencer le processus de signature automatique, vous devez vous assurer que vous avez un IDENTIFIANT Apple ajouté dans Visual Studio, tel que décrit dans le guide [Apple Account Management.](~/cross-platform/macios/apple-account-management.md) 

Une fois que vous avez ajouté un ID Apple, vous pouvez utiliser toute _équipe_ associée. Ainsi, des certificats, des profils et d’autres ID peuvent être créés par rapport à l’équipe. L’ID d’équipe est également utilisé pour créer un préfixe pour un ID App qui sera inclus dans le profil de provisionnement. Apple peut ainsi vérifier que vous êtes bien qui vous prétendez être.

Pour signer automatiquement votre application pour un déploiement sur un appareil iOS, effectuez les opérations suivantes :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez votre projet iOS dans Visual Studio pour Mac.

2. Ouvrez le fichier **Info.plist**.

3. Dans la section **Signature**, sélectionnez **Provisionnement automatique** :

    ![Liste déroulante de sélection de l’équipe](automatic-provisioning-images/image2.png)

4. Sélectionnez votre équipe dans la liste déroulante **Équipe**.

5. Au bout de quelques secondes, un certificat de signature et un profil d’attribution seront créés :

    ![certificat et profil correctement créés](automatic-provisioning-images/image5.png)

    Si la signature automatique échoue, le **bloc de signature automatique** indique la raison de l’erreur.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Si vous utilisez Visual Studio 2017 ou Visual Studio 2019 (version 16.4 et plus), vous devrez être [jumelé à un hôte de construction Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) avant de procéder.

1. Dans la **Solution Explorer**, cliquez à droite sur le nom du projet iOS et sélectionnez **les propriétés**. Ensuite, naviguez vers l’onglet **de signature de faisceau d’iOS** :

    ![Capture d’écran de la page de signature du bundle dans les propriétés iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Sélectionnez le système **d’approvisionnement automatique.**

3. Sélectionnez votre équipe du menu Drop down **Team** pour commencer le processus de signature automatique. Visual Studio indiquera si le processus est terminé avec succès :

    ![Capture d’écran de la page de signature du paquet mettant en évidence le message "Fourniture automatique complétée avec succès".](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Exécuter l’approvisionnement automatique

Lorsque l’approvisionnement automatique est activé, Visual Studio réapple le processus si nécessaire lorsque l’une des choses suivantes se produit :

- Un appareil iOS est branché à votre mac
  - La vérification de l’inscription de l’appareil sur le portail des développeurs Apple est automatiquement effectuée. Si ce n’est pas le cas, il l’ajoutera et générera un nouveau profil d’approvisionnement qui le contient.
- L’ID de bundle de votre application est modifié
  - L’ID de l’application est alors mis à jour. Un nouveau profil de provisionnement qui contient cet ID d’application est créé.
- Une fonctionnalité prise en charge est activée dans le fichier Entitlements.plist.
  - Cette fonctionnalité est ajoutée à l’ID de l’application et un nouveau profil de provisionnement est généré avec l’ID de l’application mis à jour.
  - Certaines fonctionnalités ne sont actuellement pas prises en charge. Pour plus d’informations sur celles qui sont prises en charge, consultez le guide d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>ID d’applications génériques

Dans Visual Studio for Mac and Visual Studio 2019 (version 16.5 or plus), l’approvisionnement automatique tentera par défaut de créer et d’utiliser un identifiant d’application wildcard et un profil de provisionnement au lieu d’un identifiant d’application explicite basé sur **l’identifiant bundle** spécifié dans **Info.plist**. Les ID d’applications génériques réduisent le nombre de profils et d’ID à gérer dans le portail des développeurs Apple.

Dans certains cas, les droits d’une application nécessitent un ID d’application explicite. Les droits suivants ne prennent pas en charge les ID d’applications génériques :

- App Groups
- Associated Domains
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- Zone réactive
- Achat dans l’application
- Multipath
- NFC
- VPN personnel
- Notifications Push
- Configuration d’accessoires sans fil

Si votre application utilise l’un de ces droits, Visual Studio tentera de créer un IDENTIFIANT d’application explicite (au lieu d’une wildcard).

## <a name="troubleshoot"></a>Dépanner 

- Il peut prendre plusieurs heures pour qu’un nouveau compte de développeur Apple soit approuvé. Vous ne serez pas en mesure d’activer l’approvisionnement automatique tant que le compte n’aura pas été approuvé.
- Si le processus de provisionnement `Authentication Service Is Unavailable`automatique échoue avec le message d’erreur, connectez-vous à [l’App Store Connect](https://appstoreconnect.apple.com/) ou [appleid.apple.com](https://appleid.apple.com) pour vérifier que vous avez accepté les derniers accords de service.
- Si vous obtenez `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`le message d’erreur, assurez-vous que l’équipe sélectionnée a une adhésion active rémunérée au programme de développeur Apple. Pour utiliser un compte de développeur Apple payant, veuillez consulter le guide [d’approvisionnement gratuit pour les applications Xamarin.iOS.](~/ios/get-started/installation/device-provisioning/free-provisioning.md)

## <a name="related-links"></a>Liens connexes

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’applications](~/ios/deploy-test/app-distribution/index.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
