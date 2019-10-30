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
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027040"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quels pilotes USB sont nécessaires pour déboguer Android sur Windows ?

## <a name="finding-usb-drivers"></a>Recherche de pilotes USB

Pour déboguer sur un appareil Android lors du développement dans Windows ; vous devez installer un pilote USB compatible. Le gestionnaire de Android SDK inclut par défaut le « pilote USB Google », qui ajoute la prise en charge des appareils de lien comme décrit ici : [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

D’autres appareils nécessitent des pilotes USB spécifiquement publiés par le fabricant de l’appareil. Certains liens pour les fabricants les plus courants sont inclus dans ce guide : [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternatives

En fonction du manfacturer, il peut être difficile de détecter le pilote USB exact requis. Certaines alternatives pour tester des applications Android développées dans Windows, notamment l’utilisation d’un émulateur Android ou l’utilisation de services de test externes. Certaines interfaces incluent :

- Les services de test-Cloud de [App Center](https://docs.microsoft.com/appcenter/test-cloud/) s’exécutent sur des centaines d’appareils Android réels.

- [Émulateur Visual Studio pour Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md)
