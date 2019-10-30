---
title: Déboguer l’usure Android sur un émulateur
description: Ces articles expliquent comment déboguer une application Xamarin. Android usure sur un émulateur.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: ca0a6884c05686bded25a2e515456ab192002a24
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028683"
---
# <a name="debug-android-wear-on-an-emulator"></a>Déboguer l’usure Android sur un émulateur

_Ces articles expliquent comment déboguer une application Xamarin. Android usure sur un émulateur._

## <a name="debug-wear-on-emulator-overview"></a>Présentation du débogage de l’usure sur l’émulateur

Le développement d’applications Android usure requiert l’exécution de l’application, soit sur un matériel physique, soit à l’aide d’un émulateur ou d’un simulateur. L’utilisation de matériel est la meilleure approche, mais elle n’est pas toujours la plus pratique. Dans de nombreux cas, il peut être plus simple et plus économique de simuler/émuler le matériel d’usure Android à l’aide d’un émulateur, comme décrit ci-dessous. Si vous n’êtes pas encore familiarisé avec le processus de déploiement et d’exécution d’applications Android, consultez [Hello, usure](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurer le Émulateur Android

Pour exécuter votre application d’usure sur un émulateur, vous devez installer le Android SDK Émulateur Android et le configurer pour l’usure Android. Pour obtenir des informations générales sur l’installation et la configuration de l’émulateur Android SDK, consultez [émulateur Android Setup](~/android/get-started/installation/android-emulator/index.md).

Lorsque vous créez un appareil virtuel d’usure, sélectionnez un profil d’appareil Android d’usure (par exemple, un **carré d’usure Android**). Pour améliorer les performances, utilisez l’UC **x86** /Abi comme indiqué dans cet exemple :

[![exemple d’usure de la configuration des appareils virtuels](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)

## <a name="launch-the-wear-virtual-device"></a>Lancer l’appareil virtuel usure 

Une fois que vous avez créé un appareil virtuel Android usure, vous pouvez le choisir dans le menu déroulant de l’appareil dans l’IDE avant de commencer le débogage. Si votre appareil virtuel n’est pas disponible dans la liste déroulante des appareils, vérifiez que votre projet est un projet d’application Android d' *usure* (pas un projet d’application Android) et que son niveau d’API cible est défini sur le même niveau d’API que l’appareil virtuel. Exemple :

[![choix d’un système d’usure dans le menu de périphérique Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Après le démarrage de l’émulateur Android, Xamarin. Android déploie l’application usure sur l’émulateur. L’émulateur exécute l’application avec l’image d’appareil virtuel configurée.

Ne soyez pas surpris si vous voyez cela (ou un autre écran interstitiel) au début. Le démarrage de l’émulateur Watch peut prendre un certain temps : 

![L’émulateur Watch affiche une minute...](debug-on-emulator-images/please-wait.png)

Il est possible de laisser l’émulateur s’exécuter ; il n’est pas nécessaire de le fermer et de le redémarrer chaque fois que l’application est exécutée.

## <a name="summary"></a>Récapitulatif

Ce guide explique comment configurer les Émulateur Android pour le développement d’usure et comment lancer un appareil virtuel d’usure pour le débogage.
