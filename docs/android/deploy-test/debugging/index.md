---
title: Déboguer des applications Xamarin. Android sur des appareils et émulateurs
description: Guide pratique pour tester et déboguer votre application Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556481"
---
# <a name="debug-xamarinandroid-apps"></a>Déboguer des applications Xamarin. Android

Cette section explique comment déboguer une application Xamarin.Android sur des appareils ou émulateurs.

## <a name="debugging-overview"></a>Vue d'ensemble du débogage

Le développement d’applications Android nécessite l’exécution de l’application, que ce soit sur du matériel physique ou un émulateur. L’utilisation de matériel est la meilleure approche, mais elle n’est pas toujours la plus pratique. Il est souvent plus simple et plus rentable de simuler/émuler le matériel Android à l’aide de l’un des émulateurs décrits ci-dessous.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Débogage sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md)

Cet article explique comment lancer l’émulateur Android à partir de Visual Studio et exécuter votre application dans un appareil virtuel.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Débogage sur un appareil](~/android/deploy-test/debugging/debug-on-device.md)

Cet article explique comment configurer un appareil Android physique afin que l’application Xamarin. Android puisse être déployée directement à partir de Visual Studio ou Visual Studio pour Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md)

L’une des astuces couramment utilisées par les développeurs pour déboguer leurs applications est d’utiliser `Console.WriteLine`. Toutefois, sur une plateforme mobile comme Android, il n’y a pas de console. Les appareils Android proposent un journal que vous aurez probablement besoin d’utiliser pendant l’écriture d’applications. On l’appelle parfois **logcat** en raison de la commande à taper pour le récupérer. Cet article décrit comment utiliser **logcat**.
