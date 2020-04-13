---
title: Quels pilotes USB sont nécessaires pour déboguer Android sur Windows ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 21fd8eff64d374e52e64194524a8c096cdf4d90e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027040"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quels pilotes USB sont nécessaires pour déboguer Android sur Windows ?

## <a name="finding-usb-drivers"></a>Trouver des pilotes USB

Débogé d’un appareil Android lors du développement de Windows; vous devez installer un pilote USB compatible. Le gestionnaire Android SDK inclut par défaut le "Google USB Driver", qui ajoute la prise en charge des appareils Nexus tels que décrits ici:[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

D’autres appareils nécessitent des pilotes USB spécifiquement publiés par le fabricant de l’appareil. Certains liens pour les fabricants les plus courants sont inclus dans ce guide :[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Autres solutions

Selon le manfacturer, il peut être difficile de traquer le conducteur USB exact nécessaire. Certaines alternatives pour tester les applications Android développées dans Windows, y compris l’utilisation d’un émulateur Android ou l’utilisation de services de test externes. entres autres :

- [App Center Test](https://docs.microsoft.com/appcenter/test-cloud/) - Les services de test cloud fonctionnent sur des centaines de vrais appareils Android.

- [Visual Studio Emulator pour Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md)
