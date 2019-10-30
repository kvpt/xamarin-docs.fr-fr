---
title: Provisionnement automatique pour Xamarin.iOS
description: Après l’installation de Xamarin.iOS, l’étape suivante dans le développement iOS est de provisionner votre appareil iOS. Ce guide décrit l’utilisation de la signature automatique pour demander des certificats et profils de développement.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: c65a8d4cf47fdec582b46370b6a8dbc1ddd55a1e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022783"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Provisionnement automatique pour Xamarin.iOS

_Une fois Xamarin. iOS installé avec succès, l’étape suivante du développement iOS consiste à approvisionner votre appareil iOS. Ce guide explore l’utilisation de la signature automatique pour demander des profils et des certificats de développement._

## <a name="requirements"></a>spécifications

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

- Visual Studio pour Mac 7.3 ou version ultérieure
- Xcode 9 ou supérieure

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Visual Studio 2019
- OU Visual Studio 2017 Version 15.7 (ou supérieure)

Vous devez également avoir un appairage avec un hôte de build Mac qui a les éléments suivants :

- Xcode 10 ou version ultérieure

-----

## <a name="enabling-automatic-signing"></a>Activation de la signature automatique

Avant de commencer le processus de signature automatique, vous devez vérifier qu’un ID Apple est ajouté dans Visual Studio, comme décrit dans le guide de [gestion des comptes Apple](~/cross-platform/macios/apple-account-management.md). Une fois que vous avez ajouté un ID Apple, vous pouvez utiliser toute _équipe_ associée. Ainsi, des certificats, des profils et d’autres ID peuvent être créés par rapport à l’équipe. L’ID d’équipe est également utilisé pour créer un préfixe pour un ID d’application à inclure dans le profil de provisionnement. Apple peut ainsi vérifier que vous êtes bien qui vous prétendez être.

> [!IMPORTANT]
> Avant de commencer, veillez à vous connecter à [iTunes Connect](https://itunesconnect.apple.com/) ou à [appleid.apple.com](https://appleid.apple.com) pour vérifier que vous avez accepté les dernières stratégies de compte Apple. Si vous y êtes invité, effectuez les étapes requises pour accepter tout nouveau contrat du compte provenant d’Apple. Si vous n’acceptez pas l’accord de confidentialité de mai 2018, l’alerte suivante s’affiche quand vous essayez de provisionner votre appareil :
>
> ```
> Unexpected authentication failure. Reason: {
> "authType" : "sa"
> }
> ```
>
> or
>
> ```
> Authentication Service Is Unavailable
> ```

Pour signer automatiquement votre application pour un déploiement sur un appareil iOS, effectuez les opérations suivantes :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez votre projet iOS dans Visual Studio pour Mac.

2. Ouvrez le fichier **Info.plist**.

3. Dans la section **Signature**, sélectionnez **Provisionnement automatique** :

    ![Liste déroulante de sélection de l’équipe](automatic-provisioning-images/image2.png)

4. Sélectionnez votre équipe dans la liste déroulante **Équipe**.

5. Au bout de quelques secondes, un certificat de signature et un profil d’attribution seront créés :

    ![certificat et profil correctement créés](automatic-provisioning-images/image5.png)

    Si la signature automatique échoue, le **bloc de signature automatique** indique la raison de l’erreur.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Appairez Visual Studio 2019 avec un Mac comme décrit dans le guide [Appairer avec un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Propriétés**. Ensuite, accédez à l’onglet **Signature du bundle iOS**.

3. Sélectionnez le schéma **Provisionnement automatique** :

    ![Sélection du schéma Automatique](automatic-provisioning-images/prov4.png)

4. Sélectionnez votre équipe dans la zone de liste modifiable **Équipe** pour démarrer le processus de signature automatique.

    ![Sélection de l’équipe](automatic-provisioning-images/prov3.png)

5. Le processus de signature automatique démarre. Visual Studio essaie ensuite de générer un ID d’application, un profil de provisionnement et une identité de signature pour utiliser ces artefacts pour la signature. Vous pouvez voir le processus de génération dans la sortie de génération :

    ![Sortie de génération montrant la génération d’artefacts](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>Déclenchement du provisionnement automatique

Quand la signature automatique est activée, Visual Studio pour Mac met à jour ces artefacts si nécessaire dans les situations suivantes :

- Un appareil iOS est branché à votre mac
  - La vérification de l’inscription de l’appareil sur le portail des développeurs Apple est automatiquement effectuée. Si l’appareil n’est pas inscrit, il est ajouté et un nouveau profil de provisionnement qui le contient est généré.
- L’ID de bundle de votre application est modifié
  - L’ID de l’application est alors mis à jour. Un nouveau profil de provisionnement qui contient cet ID d’application est créé.
- Une fonctionnalité prise en charge est activée dans le fichier Entitlements.plist.
  - Cette fonctionnalité est ajoutée à l’ID de l’application et un nouveau profil de provisionnement est généré avec l’ID de l’application mis à jour.
  - Certaines fonctionnalités ne sont actuellement pas prises en charge. Pour plus d’informations sur celles qui sont prises en charge, consultez le guide d’[utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>ID d’applications génériques

À partir de Visual Studio pour Mac 7.6, le provisionnement automatique tente de créer et d’utiliser par défaut un ID d’application générique et un profil de provisionnement, au lieu de créer un ID d’application explicite basé sur l’**identificateur de bundle** spécifié dans **Info.plist**. Les ID d’applications génériques réduisent le nombre de profils et d’ID à gérer dans le portail des développeurs Apple.

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
- Push Notifications
- Configuration d’accessoires sans fil

Si votre application utilise l’un de ces droits, Visual Studio pour Mac tente de créer un ID d’application explicite (au lieu d’un ID d’application générique).

> [!NOTE]
> Le provisionnement automatique avec des ID d’applications génériques est disponible uniquement dans Visual Studio pour Mac.

## <a name="related-links"></a>Liens associés

- [Provisionnement libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [Distribution d’une application](~/ios/deploy-test/app-distribution/index.md)
- [Résolution des problèmes](~/ios/deploy-test/troubleshooting.md)
- [Apple - Guide de distribution d’applications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-Simplified-iOS-Provisioning-in-Visual-Studio-with-fastlane/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
