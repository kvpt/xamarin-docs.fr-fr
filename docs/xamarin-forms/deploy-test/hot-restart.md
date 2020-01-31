---
title: Xamarin Hot Restart
description: Ce document décrit comment configurer et utiliser Xamarin Hot Restart pour déboguer une application iOS.
ms.prod: xamarin
ms.assetid: 6BC62A88-9368-41BB-8494-760F2A4805DB
ms.technology: xamarin-forms
author: jimmgarrido
ms.author: jigarrid
ms.date: 01/14/2020
ms.openlocfilehash: fb607ab605322499e42f500e4f3bf08c1c267c23
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76519684"
---
# <a name="xamarin-hot-restart-preview"></a>Xamarin Hot Restart (préversion)

![Fonctionnalité en préversion](~/media/shared/preview.png)

Xamarin Hot Restart vous permet de tester rapidement les modifications apportées à votre application pendant le développement, notamment les modifications de code multifichiers, les ressources et les références. Il envoie les nouvelles modifications au bundle d’applications existant sur la cible de débogage, ce qui entraîne un cycle de génération et de déploiement beaucoup plus rapide.

> [!NOTE]
> Xamarin Hot Restart est actuellement disponible dans Visual Studio 2019 version 16.5 Preview et prend en charge les applications iOS avec Xamarin.Forms. La prise en charge de Visual Studio pour Mac et des applications non-Xamarin.Forms est dans la feuille de route.

## <a name="requirements"></a>Spécifications

- Visual Studio 2019 version 16.5 Preview 2 ou ultérieur
- iTunes (64 bits)
- Compte de développeur Apple


## <a name="initial-setup"></a>Configuration initiale

1. Vérifiez que le projet iOS est défini comme projet de démarrage et que la configuration de build est définie sur **Déboguer | iPhone**.

   1. S’il s’agit d’un projet existant, accédez à **Générer > Gestionnaire de configuration...** et vérifiez que **Déployer** est activé pour le projet iOS.

2. Sélectionnez et cliquez sur **Appareil local** dans la barre d’outils pour lancer l’Assistant d’installation :

    [![](hot-restart-images/toolbar.png "Screenshot of the Visual Studio toolbar with local device set as the debug target.")](hot-restart-images/toolbar.png)

3. Si iTunes n’est pas installé, cliquez sur **Télécharger iTunes** pour télécharger le programme d’installation. Cliquez sur **Suivant** quand l'installation d’iTunes est terminée.

4. Connectez un appareil iOS à votre ordinateur. Le nom de l’appareil apparaît dans l’Assistant une fois qu’il est détecté. Cliquez sur **Suivant**.

5. Entrez les informations d’identification de votre compte de développeur Apple, puis cliquez sur **Suivant**.

6. Sélectionnez une équipe de développement dans le menu déroulant afin d’activer le [provisionnement automatique](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) dans le projet. Cliquez sur **Terminer**.

> [!NOTE]
> Il est recommandé d’utiliser le provisionnement automatique afin que d’autres appareils iOS puissent être facilement configurés pour le déploiement. Toutefois, vous pouvez le désactiver et continuer à utiliser le provisionnement manuel si les bons profils de provisionnement sont présents.

## <a name="use-xamarin-hot-restart"></a>Utiliser Xamarin Hot Restart
Après la configuration initiale, votre appareil connecté apparaît dans le menu déroulant de la cible de débogage. Pour déboguer votre application, sélectionnez votre appareil dans le menu déroulant et cliquez sur le bouton **Exécuter**. Vous devrez peut-être lancer manuellement l’application sur l’appareil pour démarrer la session de débogage.

Vous pouvez apporter des modifications à vos fichiers de code pendant le débogage, puis appuyer sur le bouton **Redémarrer** dans la barre d’outils de débogage ou utiliser **Ctrl+Maj+F5** pour redémarrer la session de débogage avec les nouvelles modifications appliquées :

[![](hot-restart-images/restart.png "Screenshot of the debug toolbar with the restart button highlighted.")](hot-restart-images/toolbar.png)

## <a name="limitations"></a>Limitations
- Seuls les applications iOS générées avec Xamarin.Forms et les appareils iOS sont actuellement pris en charge.
- Les fichiers Storyboard et XIB ne sont pas pris en charge et l’application peut planter si elle tente de les charger au moment de l’exécution. Si vous les utilisez dans votre application, veuillez nous en informer car nous nous intéressons à ce scénario pour plus tard.
- Les frameworks et les bibliothèques iOS statiques ne sont pas pris en charge. Vous pouvez rencontrer des erreurs d’exécution ou des plantages si votre application tente de les charger. Par contre, les bibliothèques iOS dynamiques sont prises en charge.
- Vous ne pouvez pas utiliser Xamarin Hot Restart pour créer des bundles d’applications en vue d’une publication. Vous aurez toujours d’un ordinateur Mac pour la compilation, la signature et le déploiement complets de votre application en production.

## <a name="troubleshoot"></a>Résoudre les problèmes
- L’Assistant d’installation ne détecte pas iTunes si celui-ci n’a pas été installé via le Microsoft Store. Vous devrez d’abord désinstaller cette version, puis télécharger le [programme d’installation à partir d’Apple](https://go.microsoft.com/fwlink/?linkid=2101014).
- Si l’application est déjà présente sur l’appareil, la tentative de déploiement avec Hot Restart peut échouer avec une erreur `AMDeviceStartHouseArrestService`. La solution de contournement consiste à désinstaller l’application de l’appareil, puis à la redéployer.

Pour signaler d’autres problèmes, utilisez l’outil de commentaires dans [Aide > Envoyer des commentaires > Signaler un problème](/visualstudio/ide/feedback-options?view=vs-2019#report-a-problem).
