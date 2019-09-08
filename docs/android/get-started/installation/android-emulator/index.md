---
title: Configuration de l’émulateur Android
description: Vous pouvez exécuter l’émulateur Android dans diverses configurations pour simuler différents appareils. Ce guide explique comment préparer l’émulateur Android pour tester votre application.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: cb8dc64a8c40496aaf2ea23a834effb58436618a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759085"
---
# <a name="android-emulator-setup"></a>Configuration de l’émulateur Android

_Ce guide explique comment préparer l’émulateur Android pour tester votre application._

## <a name="overview"></a>Présentation

Vous pouvez exécuter l’émulateur Android dans diverses configurations pour simuler différents appareils. Chaque configuration est appelée _appareil virtuel_. Quand vous déployez et testez votre application sur l’émulateur, vous sélectionnez un appareil virtuel préconfiguré ou personnalisé qui simule un appareil physique Android tel qu’un téléphone Nexus ou Pixel.

Les sections ci-dessous décrivent comment accélérer l’émulateur Android pour optimiser les performances, comment utiliser Android Device Manager pour créer et personnaliser des appareils virtuels et comment personnaliser les propriétés de profil d’un appareil virtuel. De plus, une section de dépannage décrit les problèmes courants d’émulateur et les solutions de contournement.

## <a name="sections"></a>Sections

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Accélération matérielle pour les performances de l’émulateur](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Comment préparer votre ordinateur pour optimiser les performances de l’émulateur Android à l’aide de la technologie de virtualisation Hyper-V ou HAXM. Comme l’émulateur Android peut être extrêmement lent sans accélération matérielle, nous vous recommandons d’activer l’accélération matérielle sur votre ordinateur avant d’utiliser l’émulateur.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Gestion des appareils virtuels avec Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Guide pratique pour utiliser Android Device Manager afin de créer et de personnaliser des appareils virtuels.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Modification des propriétés d’appareil virtuel Android](~/android/get-started/installation/android-emulator/device-properties.md)

Guide pratique pour utiliser Android Device Manager afin de modifier les propriétés de profil d’un appareil virtuel.

### <a name="android-emulator-troubleshootingandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Résolution des problèmes de l’émulateur Android](~/android/get-started/installation/android-emulator/troubleshooting.md)

Cet article décrit les problèmes et les messages d’avertissement les plus courants durant l’exécution de l’émulateur Android, puis propose des conseils et des solutions.

Après avoir configuré l’émulateur Android, consultez [Débogage avec l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md) pour plus d’informations sur le lancement de l’émulateur et son utilisation afin de tester et déboguer votre application.

> [!NOTE]
> À compter de la version **26.0.1** d’Android SDK Tools, Google ne prend plus en charge les gestionnaires d’AVD/de SDK, au profit de ses nouveaux outils d’interface de ligne de commande (CLI). En raison de ce changement, les Gestionnaires d’appareils/de SDK Xamarin sont dorénavant utilisés à la place des Gestionnaires d’appareils/de SDK Google pour Android Tools 26.0.1 et versions ultérieures. Pour plus d’informations sur Xamarin SDK Manager, consultez [Installation du kit Android SDK pour Xamarin.Android](~/android/get-started/installation/android-sdk.md).
