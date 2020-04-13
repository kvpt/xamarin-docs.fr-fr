---
title: Debug Xamarin.Android apps on Devices and Emulators
description: Guide pratique pour tester et déboguer votre application Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75556481"
---
# <a name="debug-xamarinandroid-apps"></a>Applications Debug Xamarin.Android

Cette section explique comment déboguer une application Xamarin.Android sur des appareils ou émulateurs.

## <a name="debugging-overview"></a>Vue d'ensemble du débogage

Le développement d’applications Android nécessite l’exécution de l’application, que ce soit sur du matériel physique ou un émulateur. L’utilisation de matériel est la meilleure approche, mais elle n’est pas toujours la plus pratique. Il est souvent plus simple et plus rentable de simuler/émuler le matériel Android à l’aide de l’un des émulateurs décrits ci-dessous.

### <a name="debugging-on-the-android-emulator"></a>[Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md)

Cet article explique comment lancer l’émulateur Android à partir de Visual Studio et exécuter votre application dans un appareil virtuel.

### <a name="debugging-on-a-device"></a>[Débogage sur un appareil](~/android/deploy-test/debugging/debug-on-device.md)

Cet article montre comment configurer un appareil Android physique de sorte que l’application Xamarin.Android peut être déployée à elle directement à partir de Visual Studio ou Visual Studio pour Mac.

### <a name="android-debug-log"></a>[Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md)

L’une des astuces couramment utilisées par les développeurs pour déboguer leurs applications est d’utiliser `Console.WriteLine`. Toutefois, sur une plateforme mobile comme Android, il n’y a pas de console. Les appareils Android proposent un journal que vous aurez probablement besoin d’utiliser pendant l’écriture d’applications. Ceci est parfois appelé **logcat** en raison de la commande tapée pour le récupérer. Cet article décrit comment utiliser **logcat**.
