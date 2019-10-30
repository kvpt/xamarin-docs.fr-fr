---
title: Débogage sur l’émulateur Android
description: Ce guide explique comment lancer et déboguer des applications dans Visual Studio à l’aide de l’émulateur Android.
ms.prod: xamarin
ms.assetid: AEA165A4-D81A-411B-91DF-2DED2EED27B5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 8ca13b4f9c961b8bb206d065ce3cf641a8662160
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028085"
---
# <a name="debugging-on-the-android-emulator"></a>Débogage sur l’émulateur Android

_Dans ce guide, vous allez découvrir comment lancer un appareil virtuel dans l’émulateur Android pour déboguer et tester votre application._

## <a name="overview"></a>Vue d'ensemble

L’émulateur Android (installé avec la charge de travail **Développement mobile avec .NET**) peut être exécuté dans diverses configurations pour simuler différents appareils Android. Chacune de ces configurations est créée en tant qu’_appareil virtuel_. Dans ce guide, vous allez découvrir comment lancer l’émulateur à partir de Visual Studio et exécuter votre application dans un appareil virtuel. Pour plus d’informations sur la configuration de l’émulateur Android et la création de nouveaux appareils virtuels, consultez [Configuration de l’émulateur Android](~/android/get-started/installation/android-emulator/index.md).

## <a name="using-a-pre-configured-virtual-device"></a>Utilisation d’un appareil virtuel préconfiguré

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio inclut des appareils virtuels préconfigurés qui s’affichent dans le menu déroulant des appareils. Par exemple, dans la capture d’écran suivante de Visual Studio 2017, plusieurs appareils virtuels préconfigurés sont disponibles :

- **VisualStudio\_android-23\_arm\_phone**

- **VisualStudio\_android-23\_arm\_tablet**

- **VisualStudio\_android-23\_x86\_phone** 

- **VisualStudio\_android-23\_x86\_tablet** 

[![Appareils virtuels](debug-on-emulator-images/win/01-virtual-devices-sml.png)](debug-on-emulator-images/win/01-virtual-devices.png#lightbox)

En règle générale, vous sélectionnez l’appareil virtuel **VisualStudio\_android-23\_x86\_phone** pour tester et déboguer une application pour téléphone. Si l’un de ces appareils virtuels préconfigurés répond à vos besoins (autrement dit, correspond au niveau d’API cible de votre application), passez directement à la section [Lancement de l’émulateur](#launching) pour commencer à exécuter votre application dans l’émulateur. (Si vous n’êtes pas encore familiarisé avec les niveaux d’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).)

Si votre projet Xamarin.Android utilise un niveau Framework cible qui n’est pas compatible avec les appareils virtuels disponibles, le menu déroulant liste les appareils virtuels inutilisables sous **Appareils non pris en charge**. Par exemple, dans le projet suivant, le Framework cible est défini sur **Android 7.1 Nougat (API 25)** , qui n’est pas compatible avec les appareils virtuels **Android 6.0** listés dans cet exemple :

[![Appareil virtuel incompatible](debug-on-emulator-images/win/02-incompatible-level-sml.png)](debug-on-emulator-images/win/02-incompatible-level.png#lightbox)

Vous pouvez cliquer sur **Modifier la cible Android minimale** pour modifier la version d’Android minimale du projet afin qu’elle corresponde au niveau d’API des appareils virtuels disponibles. Vous pouvez également utiliser [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) pour créer des appareils virtuels qui prennent en charge votre niveau d’API cible.
Avant de pouvoir configurer les appareils virtuels pour un nouveau niveau d’API, vous devez commencer par installer les images système correspondantes pour ce niveau d’API (consultez [Configuration du kit Android SDK pour Xamarin.Android](~/android/get-started/installation/android-sdk.md)).

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Visual Studio pour Mac inclut des appareils virtuels préconfigurés qui s’affichent dans le menu déroulant des appareils. Par exemple, dans la capture d’écran suivante, deux appareils virtuels préconfigurés sont disponibles :

- **Android\_Accelerated\_x86**

- **Android\_ARMv7a**

[![Appareils virtuels](debug-on-emulator-images/mac/01-virtual-devices-sml.png)](debug-on-emulator-images/mac/01-virtual-devices.png#lightbox)

En règle générale, vous sélectionnez l’appareil virtuel **Android\_Accelerated\_x86** pour tester et déboguer une application pour téléphone. Si cet appareil virtuel préconfiguré répond à vos besoins (autrement dit, correspond au niveau d’API cible de votre application), passez directement à [Lancement de l’émulateur](#launching) pour commencer à exécuter votre application dans l’émulateur. (Si vous n’êtes pas encore familiarisé avec les niveaux d’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).)

-----

## <a name="editing-virtual-devices"></a>Modification d’appareils virtuels

Pour modifier des appareils virtuels (ou en créer de nouveaux), vous devez utiliser [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

<a name="launching" />

## <a name="launching-the-emulator"></a>Lancement de l'émulateur

En haut de la fenêtre Visual Studio figure un menu déroulant avec lequel vous pouvez sélectionner le mode **Debug** ou **Release**. Si vous choisissez **Debug**, le débogueur est attaché au processus d’application en cours d’exécution dans l’émulateur après le démarrage de l’application. Si vous choisissez **Release**, le débogueur est désactivé (mais vous pouvez toujours exécuter l’application et utiliser des instructions de journal pour le débogage). Après avoir choisi un appareil virtuel dans le menu déroulant des appareils, sélectionnez le mode **Debug** ou **Release**, puis cliquez sur le bouton Lire pour exécuter l’application :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Modes Debug et Mise en production, bouton Lire](debug-on-emulator-images/win/17-debug-release-sml.png)](debug-on-emulator-images/win/17-debug-release.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Modes Debug et Mise en production, bouton Lire](debug-on-emulator-images/mac/16-debug-release-sml.png)](debug-on-emulator-images/mac/16-debug-release.png#lightbox)

-----

Après le démarrage de l’émulateur, Xamarin.Android déploie l’application sur l’émulateur. L’émulateur exécute l’application avec l’image d’appareil virtuel configurée. Une capture d’écran de l’émulateur Android est affichée ci-dessous. Dans cet exemple, l’émulateur exécute une application vide appelée **MyApp** :

![Émulateur exécutant une application vide](debug-on-emulator-images/emulator-running.png)

Vous pouvez laisser l’émulateur s’exécuter ; il n’est pas nécessaire de le fermer et d’attendre qu’il redémarre chaque fois que l’application est lancée. La première fois qu’une application Xamarin.Android est exécutée dans l’émulateur, le runtime partagé Xamarin.Android pour le niveau d’API ciblé est installé, suivi de l’application. L’installation du runtime peut prendre un certain temps, soyez donc patient. L’installation du runtime est réalisée uniquement lors du premier déploiement de l’application Xamarin.Android sur l’émulateur . Les déploiements suivants sont plus rapides, car seule l’application est copiée sur l’émulateur.

<a name="quick-boot" />

## <a name="quick-boot"></a>Quick Boot

Les versions les plus récentes de l’émulateur Android incluent une fonctionnalité appelée _Quick Boot_ qui lance l’émulateur en quelques secondes seulement. Quand vous fermez l’émulateur, il prend un instantané de l’état de l’appareil virtuel afin de pouvoir le restaurer rapidement à partir de cet état lors de son redémarrage.
Pour accéder à cette fonctionnalité, vous avez besoin des éléments suivants :

- Émulateur Android version 27.0.2 ou ultérieure
- Android SDK Tools version 26.1.1 ou ultérieure

Quand les versions de l’émulateur et des outils SDK répertoriées ci-dessus sont installées, la fonctionnalité Quick Boot est activée par défaut. 

Le premier démarrage à froid de l’appareil virtuel a lieu sans amélioration de la vitesse, car aucun instantané n’a encore été créé :

![Capture d’écran du démarrage à froid](debug-on-emulator-images/cold-boot.png)

Quand vous quittez l’émulateur, Quick Boot enregistre l’état de l’émulateur dans un instantané :

![Enregistrement de l’état lors de l’arrêt](debug-on-emulator-images/saving-state.png)

Les démarrages ultérieurs de l’appareil virtuel sont beaucoup plus rapides, car l’émulateur restaure simplement l’état à partir duquel vous l’avez fermé.

![Chargement de l’état lors du redémarrage](debug-on-emulator-images/loading-state.png)

## <a name="troubleshooting"></a>Résolution des problèmes

Pour des conseils et des solutions de contournement aux problèmes courants d’émulateur, consultez [Résolution des problèmes d’émulateur Android](~/android/get-started/installation/android-emulator/troubleshooting.md).

## <a name="summary"></a>Récapitulatif

Ce guide a expliqué le processus de configuration de l’émulateur Android pour exécuter et tester des applications Xamarin.Android. Il a décrit les étapes de lancement de l’émulateur à l’aide d’appareils virtuels préconfigurés et les étapes de déploiement d’une application dans l’émulateur à partir de Visual Studio. 

Pour plus d’informations sur l’utilisation de l’émulateur Android, consultez les rubriques suivantes relatives au développement Android :

- [Navigation dans l’écran](https://developer.android.com/studio/run/emulator.html#navigate)

- [Réaliser des tâches de base dans l’émulateur](https://developer.android.com/studio/run/emulator.html#tasks)

- [Utilisation des contrôles étendus, des paramètres et de l’aide](https://developer.android.com/studio/run/emulator.html#extended)

- [Exécuter l’émulateur avec Quick Boot](https://developer.android.com/studio/run/emulator#quickboot)
