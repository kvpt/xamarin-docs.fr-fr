---
title: Xamarin Hot Restart
description: Ce document décrit comment configurer et utiliser Xamarin Hot Restart pour déboguer une application iOS.
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b441e5fd5ef045bf90244b4b69f868fe858e002d
ms.sourcegitcommit: ba052b0990499d8191bcb25291c6ccd8d1ff26fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92493310"
---
# <a name="xamarin-hot-restart-preview"></a>Xamarin Hot Restart (préversion)

![Fonctionnalité d’évaluation](~/media/shared/preview.png)

Xamarin Hot Restart vous permet de tester rapidement les modifications apportées à votre application pendant le développement, notamment les modifications de code multifichiers, les ressources et les références. Il envoie les nouvelles modifications au bundle d’applications existant sur la cible de débogage, ce qui entraîne un cycle de génération et de déploiement beaucoup plus rapide.

> [!IMPORTANT]
> Le redémarrage à chaud Xamarin est actuellement disponible dans Visual Studio 2019 version 16,5 stable et prend en charge les applications iOS à l’aide de Xamarin.Forms . La prise en charge des Visual Studio pour Mac et des applications non basées Xamarin.Forms sur le plan.

## <a name="requirements"></a>Spécifications

- Visual Studio 2019 version 16,5
- iTunes (64 bits)
- Compte de développeur Apple et inscription du [programme de développement Apple](https://developer.apple.com/programs) payant


## <a name="initial-setup"></a>Configuration initiale

> [!NOTE]
> Le redémarrage à chaud Xamarin est désactivé par défaut lorsqu’il est en version préliminaire. Vous pouvez l’activer sous **outils > Options > environnement > fonctionnalités préliminaires > activer le redémarrage à chaud Xamarin**.

1. Vérifiez que le projet iOS est défini comme projet de démarrage et que la configuration de build est définie sur **Déboguer | iPhone**.

   1. S’il s’agit d’un projet existant, accédez à **Générer > Gestionnaire de configuration...** et vérifiez que **Déployer** est activé pour le projet iOS.

2. Sélectionnez et cliquez sur **Appareil local** dans la barre d’outils pour lancer l’Assistant d’installation :

    [![Capture d’écran de la barre d’outils Visual Studio avec l’appareil local défini comme cible de débogage.](hot-restart-images/toolbar.png)](hot-restart-images/toolbar.png)

3. Si iTunes n’est pas installé, cliquez sur **Télécharger iTunes** pour télécharger le programme d’installation. Cliquez sur **Suivant** quand l'installation d’iTunes est terminée.

4. Connectez un appareil iOS à votre ordinateur. Si un appareil est déjà branché, débranchez-le, puis reconnectez-le. Le nom de l’appareil apparaît dans l’Assistant une fois qu’il est détecté. Cliquez sur **Suivant**.

5. Entrez les informations d’identification de votre compte de développeur Apple, puis cliquez sur **Suivant**.

6. Sélectionnez une équipe de développement dans le menu déroulant afin d’activer le [provisionnement automatique](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) dans le projet. Cliquez sur **Terminer**.

> [!NOTE]
> Il est recommandé d’utiliser le provisionnement automatique afin que d’autres appareils iOS puissent être facilement configurés pour le déploiement. Toutefois, vous pouvez le désactiver et continuer à utiliser le provisionnement manuel si les bons profils de provisionnement sont présents.

## <a name="use-xamarin-hot-restart"></a>Utiliser Xamarin Hot Restart
Après la configuration initiale, votre appareil connecté apparaît dans le menu déroulant de la cible de débogage. Pour déboguer votre application, sélectionnez votre appareil dans le menu déroulant et cliquez sur le bouton **Exécuter**. Vous pouvez voir un message dans Visual Studio vous demandant de lancer manuellement l’application sur l’appareil pour démarrer la session de débogage.

Vous pouvez apporter des modifications à vos fichiers de code pendant le débogage, puis appuyer sur le bouton **Redémarrer** dans la barre d’outils de débogage ou utiliser **Ctrl+Maj+F5** pour redémarrer la session de débogage avec les nouvelles modifications appliquées :

[![Capture d’écran de la barre d’outils de débogage avec le bouton de redémarrage sélectionné.](hot-restart-images/restart.png)](hot-restart-images/toolbar.png)

Vous pouvez également utiliser le `HOTRESTART` symbole de préprocesseur pour empêcher l’exécution de certains codes lors du débogage avec redémarrage à chaud Xamarin.

## <a name="limitations"></a>Limites

- Seules les applications iOS créées avec Xamarin.Forms et les appareils IOS sont actuellement prises en charge.
- Seuls les appareils iOS 64 bits sont pris en charge. Depuis iOS 11, Apple n’autorise plus l’exécution d’applications iOS sur l’architecture 32 bits (appareils antérieurs à iPhone 5 s).
- Les fichiers Storyboard et XIB ne sont pas pris en charge et l’application peut planter si elle tente de les charger au moment de l’exécution. Utilisez le `HOTRESTART` symbole de préprocesseur pour empêcher l’exécution de ce code.
- Les infrastructures et bibliothèques iOS statiques ne sont pas prises en charge et vous pouvez voir des erreurs d’exécution ou des blocages si votre application tente de les charger. Utilisez le `HOTRESTART` symbole de préprocesseur pour empêcher l’exécution de ce code. Les bibliothèques iOS dynamiques sont prises en charge.
- Vous ne pouvez pas utiliser Xamarin Hot Restart pour créer des bundles d’applications en vue d’une publication. Vous aurez toujours d’un ordinateur Mac pour la compilation, la signature et le déploiement complets de votre application en production.
- Les catalogues de biens ne sont actuellement pas pris en charge. Quand vous utilisez le redémarrage à chaud, votre application affiche l’écran d’icône et de lancement par défaut pour les applications Xamarin. Lorsqu’ils sont associés à un Mac ou au développement sur un Mac, vos catalogues de ressources fonctionnent.

## <a name="troubleshoot"></a>Dépanner

- Un problème connu est que l’activation de builds spécifiques à l’appareil empêche l’application d’entrer en mode débogage. Pour contourner ce problème, désactivez cette option sous **Propriétés > Build iOS** et réessayez le débogage. Ceci fera l’objet d’un correctif dans une version future.
- Si l’application est déjà présente sur l’appareil, la tentative de déploiement avec Hot Restart peut échouer avec une erreur `AMDeviceStartHouseArrestService`. La solution de contournement consiste à désinstaller l’application de l’appareil, puis à la redéployer.
- L’entrée d’un ID Apple qui ne fait pas partie du programme de développement Apple peut entraîner l’erreur suivante : `Authentication Error. Xcode 7.3 or later is required to continue developing with your Apple ID` . Vous devez disposer d’un compte de développeur Apple valide pour utiliser le redémarrage à chaud Xamarin sur les appareils iOS. 

Pour signaler d’autres problèmes, utilisez l’outil de commentaires dans [Aide > Envoyer des commentaires > Signaler un problème](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
