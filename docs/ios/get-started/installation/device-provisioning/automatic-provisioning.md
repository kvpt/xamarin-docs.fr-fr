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
ms.sourcegitcommit: 997f7b6a1a1bc50b98c3ca5bbc75d6875ba2ae9a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79510677"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisionnement automatique pour Xamarin.iOS

_Une fois Xamarin. iOS installé avec succès, l’étape suivante du développement iOS consiste à approvisionner votre appareil iOS. Ce guide explore l’utilisation de l’approvisionnement automatique pour demander des profils et des certificats de développement._

## <a name="requirements"></a>Spécifications

L’approvisionnement automatique est disponible sur Visual Studio pour Mac, Visual Studio 2019 et Visual Studio 2017 (version 15,7 et ultérieures). 

> [!NOTE]
> Vous devez également avoir un compte de développeur Apple payant pour utiliser cette fonctionnalité. Vous trouverez plus d’informations sur les comptes de développeurs Apple dans le Guide de [provisionnement des appareils](~/ios/get-started/installation/device-provisioning/index.md) .
> Si vous n’avez pas de compte de développeur Apple payant, consultez le Guide [approvisionnement gratuit pour Xamarin. iOS] (~/iOS/Get-Started/installation/Device-Provisioning/Free-Provisioning.MD).

> [!NOTE]
> Avant de commencer, veillez à accepter d’abord tous les contrats de licence dans le [portail des développeurs Apple](https://developer.apple.com/account/) ou dans l' [App Store Connect](https://appstoreconnect.apple.com/).


## <a name="enable-automatic-provisioning"></a>Activer l’approvisionnement automatique

Avant de démarrer le processus de signature automatique, vous devez vous assurer que vous disposez d’un ID Apple ajouté dans Visual Studio, comme décrit dans le Guide de [gestion des comptes Apple](~/cross-platform/macios/apple-account-management.md) . 

Une fois que vous avez ajouté un ID Apple, vous pouvez utiliser toute _équipe_ associée. Ainsi, des certificats, des profils et d’autres ID peuvent être créés par rapport à l’équipe. L’ID d’équipe est également utilisé pour créer un préfixe pour un ID d’application qui sera inclus dans le profil de provisionnement. Apple peut ainsi vérifier que vous êtes bien qui vous prétendez être.

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
> Si vous utilisez Visual Studio 2017 ou Visual Studio 2019 (version 16,4 et antérieure), vous devez être [associé à un hôte de build Mac avant de](~/ios/get-started/installation/windows/connecting-to-mac/index.md) continuer.

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet iOS et sélectionnez **Propriétés**. Ensuite, accédez à l’onglet **signature d’offre groupée iOS** :

    ![Capture d’écran de la page de signature de Bundle dans les propriétés iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Sélectionnez le schéma d' **approvisionnement automatique** .

3. Sélectionnez votre équipe dans le menu déroulant de l' **équipe** pour démarrer le processus de signature automatique. Visual Studio indique si le processus s’est terminé correctement :

    ![Capture d’écran de la page de signature de Bundle mettant en surbrillance le message « l’approvisionnement automatique s’est terminé correctement ».](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Exécuter l’approvisionnement automatique

Lorsque l’approvisionnement automatique est activé, Visual Studio réexécute le processus si nécessaire lorsque l’un des événements suivants se produit :

- Un appareil iOS est branché à votre mac
  - La vérification de l’inscription de l’appareil sur le portail des développeurs Apple est automatiquement effectuée. Si ce n’est pas le cas, il l’ajoute et génère un nouveau profil de provisionnement qui le contient.
- L’ID de bundle de votre application est modifié
  - L’ID de l’application est alors mis à jour. Un nouveau profil de provisionnement qui contient cet ID d’application est créé.
- Une fonctionnalité prise en charge est activée dans le fichier Entitlements.plist.
  - Cette fonctionnalité est ajoutée à l’ID de l’application et un nouveau profil de provisionnement est généré avec l’ID de l’application mis à jour.
  - Certaines fonctionnalités ne sont actuellement pas prises en charge. Pour plus d’informations sur celles qui sont prises en charge, consultez le guide d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>ID d’applications génériques

Dans Visual Studio pour Mac et Visual Studio 2019 (version 16,5 ou ultérieure), le provisionnement automatique tente par défaut de créer et d’utiliser un ID d’application générique et un profil de provisionnement au lieu d’un ID d’application explicite basé sur l' **identificateur de Bundle** spécifié dans **info. plist**. Les ID d’applications génériques réduisent le nombre de profils et d’ID à gérer dans le portail des développeurs Apple.

Dans certains cas, les droits d’une application nécessitent un ID d’application explicite. Les droits suivants ne prennent pas en charge les ID d’applications génériques :

- App Groups
- Associated Domains
- Apple Pay
- Centre de jeux
- HealthKit
- HomeKit
- Zone réactive
- Achat dans l’application
- Multipath
- NFC
- Personal VPN
- Notifications Push
- Wireless Accessory Configuration

Si votre application utilise l’une de ces habilitations, Visual Studio tente de créer un ID d’application explicite (au lieu d’un caractère générique).

## <a name="troubleshoot"></a>Dépanner 

- L’approbation d’un nouveau compte de développeur Apple peut prendre plusieurs heures. Vous ne pouvez pas activer l’approvisionnement automatique tant que le compte n’a pas été approuvé.
- Si le processus d’approvisionnement automatique échoue avec le message d’erreur `Authentication Service Is Unavailable`, connectez-vous à l' [App Store Connect](https://appstoreconnect.apple.com/) ou à [appleid.Apple.com](https://appleid.apple.com) pour vérifier que vous avez accepté les derniers contrats de service.
- Si vous recevez le message d’erreur `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, assurez-vous que l’équipe sélectionnée a une appartenance active au programme pour développeurs Apple. Pour utiliser un compte de développeur Apple payant, consultez le guide [d’approvisionnement gratuit pour les applications Xamarin. iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md) .

## <a name="related-links"></a>Liens connexes

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’une application](~/ios/deploy-test/app-distribution/index.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
