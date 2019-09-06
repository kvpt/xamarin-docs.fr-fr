---
title: Déploiement sans fil pour les applications Xamarin.iOS et tvOS
description: Ce document décrit comment déployer sans fil une application Xamarin.iOS sur un appareil iOS à partir de Visual Studio pour Mac ou Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 5AB4C5A9-4FBB-4DCB-BD72-0022D5439E65
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: afa87cf98a2f9caee49c510674fe774a46e81107
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284351"
---
# <a name="wireless-deployment-for-xamarinios-and-tvos-apps"></a>Déploiement sans fil pour les applications Xamarin.iOS et tvOS

Une part importante du workflow du développeur consiste au déploiement sur un appareil. Xcode 9 a introduit une option de déploiement sur un appareil iOS ou Apple TV via un réseau, ce qui évite d’avoir à raccorder les appareils à chaque fois que vous désirez déployer et déboguer votre application. Cette fonctionnalité a été introduite dans les versions Visual Studio pour Mac 7.4 et Visual Studio 15.6.

Ce guide détaille comment coupler et déployer sur un appareil sur le réseau.

## <a name="requirements"></a>Configuration requise

La fonctionnalité de déploiement sans fil est disponible à la fois dans Visual Studio pour Mac et dans Visual Studio.

Pour utiliser le déploiement sans fil, vous devez disposer des éléments suivants :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

- macOS 10.12.4
- La dernière version de Visual Studio pour Mac
- Xcode 9.0 ou version ultérieure
- Un appareil avec iOS 11.0 ou tvOS 11.0 et version ultérieure

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- La dernière version de Visual Studio
- Un appareil avec iOS 11.0 ou tvOS 11.0 et version ultérieure

Sur votre hôte de build Mac, les composants suivants doivent être installés :

- macOS 10.12.4
- Visual Studio pour Mac
- Xcode 9.0 ou version ultérieure

-----

## <a name="connecting-a-device"></a>Connexion d’un appareil

Pour déployer et déboguer sans fil sur votre appareil, vous devez coupler votre appareil iOS ou TV Apple à Xcode sur votre Mac. Une fois couplé, vous pouvez le sélectionner dans la liste cible des appareils dans Visual Studio. 

Le processus de couplage suivant ne doit se produire qu’une seule fois par appareil. Xcode conserve les paramètres de connexion.

<a name="pair" />

### <a name="pairing-an-ios-device-with-xcode"></a>Couplage d’un appareil iOS à Xcode

1. Ouvrez Xcode et allez à **Fenêtre > Appareils et simulateurs**.
2. Branchez votre appareil iOS sur votre Mac à l’aide d’un câble Lightning. Vous devrez peut-être sélectionner **Faire confiance à cet ordinateur** sur votre appareil.
3. Sélectionnez votre appareil, puis cochez la case **Se connecter via le réseau** pour coupler votre appareil :  ![Fenêtre Appareil et simulateur affichant l’option Se connecter via le réseau](wireless-deployment-images/image2.png)

### <a name="pairing-an-apple-tv-with-xcode"></a>Couplage d’un Apple TV avec Xcode

1. Vérifiez que votre Mac et Apple TV sont connectés au même réseau.

2. Ouvrez Xcode et allez à **Fenêtre > Appareils et simulateurs**.

3. Sur Apple TV, allez à **Paramètres > Distants et appareils > Application distante et appareil**.

4. Sélectionnez l’Apple TV dans la zone **découvert** dans Xcode et entrez le code de vérification affiché sur l’écran de l’Apple TV.

5. Cliquez sur le bouton **Connecter**. Une fois le couplage réussi, une icône de connexion réseau s’affiche en regard de l’Apple TV.

## <a name="deploy-to-a-device"></a>Déployer l’application sur un appareil

Quand un appareil est connecté sans fil et prêt à être utilisé pour le déploiement, il s’affiche dans la liste cible des appareils, comme si l’appareil était connecté via USB.

Pour tester sur un appareil physique, l’appareil doit être [configuré](~/ios/get-started/installation/device-provisioning/index.md). Veillez à effectuer cette opération avant de tenter de déployer sur un appareil. 

Pour déployer sur un appareil iOS ou tvOS, procédez comme suit :

1. Assurez-vous que votre machine de déploiement et votre appareil cible se trouvent sur le même réseau sans fil. 

2. Sélectionnez votre appareil dans la liste des appareils cibles et exécuter l’application.

3. Si votre appareil est verrouillé, vous êtes invité à déverrouiller votre appareil. Une fois l’appareil déverrouillé, votre application est déployée sur l’appareil.

Le débogage sans fil est automatiquement activé après le déploiement sans fil, afin de pouvoir utiliser les points d’arrêt précédemment définis et continuer le débogage du workflow comme d’habitude.

## <a name="troubleshooting"></a>Résolution des problèmes

1. Assurez-vous toujours que votre appareil iOS ou Apple TV sont connectés au même réseau que votre Mac.

2. Si l’appareil n’apparaît pas dans Visual Studio, vérifiez la fenêtre **Appareils et simulateurs** de Xcode. 

    - Si Xcode n’affiche **pas** votre appareil comme étant connecté, essayez de [coupler](#pair) à nouveau votre appareil.

    - Si Xcode n’affiche pas l’appareil comme étant connecté, essayez de redémarrer Visual Studio et votre appareil.

3. Si vous ne le n'avez pas encore fait, vous devrez [configurer](~/ios/get-started/installation/device-provisioning/index.md) votre appareil.

4. Si vous rencontrez des problèmes liés à cette fonction qui ne peut pas être résolue par les étapes précédentes, veuillez soumettre un problème dans [Communauté de développeurs](https://developercommunity.visualstudio.com/spaces/41/index.html).

## <a name="related-links"></a>Liens associés

- [Coupler un appareil sans fil avec Xcode](https://help.apple.com/xcode/mac/9.0/index.html?localePath=en.lproj#/devbc48d1bad)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Debug-to-iOS-Devices-Over-Wi-Fi/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
