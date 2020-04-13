---
title: Xamarin Hot Restart
description: Ce document décrit comment configurer et utiliser Xamarin Hot Restart pour déboguer une application iOS.
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
ms.openlocfilehash: cc5efffd4c3646fbff9cdb1ad1a30ec614cb4921
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79429565"
---
# <a name="xamarin-hot-restart-preview"></a>Xamarin Hot Restart (préversion)

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

Xamarin Hot Restart vous permet de tester rapidement les modifications apportées à votre application pendant le développement, notamment les modifications de code multifichiers, les ressources et les références. Il envoie les nouvelles modifications au bundle d’applications existant sur la cible de débogage, ce qui entraîne un cycle de génération et de déploiement beaucoup plus rapide.

> [!IMPORTANT]
> Xamarin Hot Restart est actuellement disponible dans Visual Studio 2019 version 16.5 stable et prend en charge les applications iOS en utilisant Xamarin.Forms. La prise en charge de Visual Studio pour Mac et des applications non-Xamarin.Forms est dans la feuille de route.

## <a name="requirements"></a>Spécifications

- Visual Studio 2019 version 16.5
- iTunes (64 bits)
- Compte de développeur d’Apple et inscription payée [de programme de développeur d’Apple](https://developer.apple.com/programs)


## <a name="initial-setup"></a>Configuration initiale

> [!NOTE]
> Xamarin Hot Restart est désactivé par défaut pendant qu’il est en prévisualisation. Vous pouvez l’activer sous **Outils > Options > Environnement > Les fonctionnalités d’aperçu > Activer Xamarin Hot Restart**.

1. Assurez-vous que le projet iOS est défini comme le projet de démarrage et la configuration de construction est réglé sur **Debug-iPhone**.

   1. S’il s’agit d’un projet existant, accédez à **Générer > Gestionnaire de configuration...** et vérifiez que **Déployer** est activé pour le projet iOS.

2. Sélectionnez et cliquez sur **Appareil local** dans la barre d’outils pour lancer l’Assistant d’installation :

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Si iTunes n’est pas installé, cliquez sur **Télécharger iTunes** pour télécharger le programme d’installation. Cliquez sur **Suivant** quand l'installation d’iTunes est terminée.

4. Connectez un appareil iOS à votre ordinateur. Si un appareil était déjà branché, débranchez-le. Le nom de l’appareil apparaît dans l’Assistant une fois qu’il est détecté. Cliquez sur **Suivant**.

5. Entrez les informations d’identification de votre compte de développeur Apple, puis cliquez sur **Suivant**.

6. Sélectionnez une équipe de développement dans le menu déroulant afin d’activer le [provisionnement automatique](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) dans le projet. Cliquez sur **Terminer**.

> [!NOTE]
> Il est recommandé d’utiliser le provisionnement automatique afin que d’autres appareils iOS puissent être facilement configurés pour le déploiement. Toutefois, vous pouvez le désactiver et continuer à utiliser le provisionnement manuel si les bons profils de provisionnement sont présents.

## <a name="use-xamarin-hot-restart"></a>Utiliser Xamarin Hot Restart
Après la configuration initiale, votre appareil connecté apparaît dans le menu déroulant de la cible de débogage. Pour déboguer votre application, sélectionnez votre appareil dans le menu déroulant et cliquez sur le bouton **Exécuter**. Vous pouvez voir un message dans Visual Studio vous demandant de lancer manuellement l’application sur l’appareil pour démarrer la session de débogage.

Vous pouvez apporter des modifications à vos fichiers de code pendant le débogage, puis appuyer sur le bouton **Redémarrer** dans la barre d’outils de débogage ou utiliser **Ctrl+Maj+F5** pour redémarrer la session de débogage avec les nouvelles modifications appliquées :

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

Vous pouvez également `HOTRESTART` utiliser le symbole préprocesseur pour empêcher certains codes de s’exécuter lors du débogage avec Xamarin Hot Restart.

## <a name="limitations"></a>Limites

- Seuls les applications iOS générées avec Xamarin.Forms et les appareils iOS sont actuellement pris en charge.
- Seuls les appareils iOS 64 bits sont pris en charge. En date d’iOS 11, Apple n’autorise plus l’exécution d’applications iOS sur l’architecture 32 bits (appareils plus tôt que l’iPhone 5s).
- Les fichiers Storyboard et XIB ne sont pas pris en charge et l’application peut planter si elle tente de les charger au moment de l’exécution. Utilisez `HOTRESTART` le symbole du préprocesseur pour empêcher ce code de s’exécuter.
- Les bibliothèques et les cadres iOS statiques ne sont pas pris en charge et vous pouvez voir des erreurs ou des plantages en temps d’exécution si votre application tente de les charger. Utilisez `HOTRESTART` le symbole du préprocesseur pour empêcher ce code de s’exécuter. Les bibliothèques iOS dynamiques sont prises en charge.
- Vous ne pouvez pas utiliser Xamarin Hot Restart pour créer des bundles d’applications en vue d’une publication. Vous aurez toujours d’un ordinateur Mac pour la compilation, la signature et le déploiement complets de votre application en production.

## <a name="troubleshoot"></a>Dépanner

- L’Assistant d’installation ne détecte pas iTunes si celui-ci n’a pas été installé via le Microsoft Store. Vous devrez d’abord désinstaller cette version, puis télécharger le [programme d’installation à partir d’Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- Un problème connu est que l’activation de builds spécifiques à l’appareil empêche l’application d’entrer en mode débogage. Pour contourner ce problème, désactivez cette option sous **Propriétés > Build iOS** et réessayez le débogage. Ceci fera l’objet d’un correctif dans une version future.
- Si l’application est déjà présente sur l’appareil, la tentative de déploiement avec Hot Restart peut échouer avec une erreur `AMDeviceStartHouseArrestService`. La solution de contournement consiste à désinstaller l’application de l’appareil, puis à la redéployer.
- L’entrée d’un identifiant Apple qui ne fait pas `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID`partie du programme de développement d’Apple peut entraîner l’erreur suivante : . Vous devez avoir un compte Apple Developer valide pour utiliser Xamarin Hot Restart sur les appareils iOS. 

Pour signaler d’autres problèmes, utilisez l’outil de commentaires dans [Aide > Envoyer des commentaires > Signaler un problème](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
