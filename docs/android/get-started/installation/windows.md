---
title: Installation sur un système Windows
description: Ce guide décrit les étapes d’installation de Xamarin.Android pour Visual Studio sur un système Windows, et explique comment configurer Xamarin.Android pour créer votre première application Xamarin.Android.
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 315cb8e58b0b9f29c06e4d0af2b8cf9adffb06e3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758529"
---
# <a name="windows-installation"></a>Installation sur un système Windows

_Ce guide décrit les étapes d’installation de Xamarin.Android pour Visual Studio sur un système Windows, et explique comment configurer Xamarin.Android pour créer votre première application Xamarin.Android._

## <a name="overview"></a>Présentation

Étant donné que Xamarin est désormais inclus gratuitement dans toutes les éditions de Visual Studio et ne nécessite pas de licence supplémentaire, vous pouvez utiliser le programme d’installation de Visual Studio pour télécharger et installer les outils Xamarin.Android
(l’installation manuelle et la gestion des licences qui étaient nécessaires pour les versions antérieures de Xamarin.Android ne le sont plus pour cette version). Dans ce guide, vous apprendrez à :

- Configurer des emplacements personnalisés pour le Java Development Kit, le kit Android SDK et le kit Android NDK

- Lancer le Gestionnaire Android SDK pour télécharger et installer les composants supplémentaires du kit Android SDK

- Préparer un appareil ou un émulateur Android en vue d’un débogage ou d’un test

- Créer votre premier projet d’application Xamarin.Android

À la fin de ce guide, Xamarin.Android aura été intégré à Visual Studio, et vous serez prêt à créer votre première application Xamarin.Android.

## <a name="installation"></a>Installation

Pour plus d’informations sur l’installation de Xamarin en vue de son utilisation avec Visual Studio sur un système Windows, consultez le guide [Installation sur un système Windows](~/get-started/installation/windows.md).

## <a name="configuration"></a>Configuration

Xamarin.Android utilise le JDK Java et Android SDK pour créer des applications. Pendant l’installation, le programme d’installation de Visual Studio place ces outils dans leur emplacement par défaut, et configure l’environnement de développement avec la configuration de chemin appropriée. Vous pouvez afficher et modifier ces emplacements en cliquant sur **Outils > Options > Xamarin > Paramètres Android** :

![Capture d’écran de la boîte de dialogue Paramètres Xamarin Android](windows-images/07-settings.png)

Pour la plupart des utilisateurs, ces emplacements par défaut fonctionnent sans nécessiter de modifications. Toutefois, vous pouvez souhaiter configurer des emplacements personnalisés pour ces outils dans Visual Studio (par exemple, si vous avez installé le JDK Java, Android SDK ou le NDK dans un autre emplacement). Cliquez sur **Modifier** en regard du chemin que vous souhaitez modifier, puis accédez au nouvel emplacement.

Xamarin.Android utilise [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), qui est nécessaire si vous développez des applications pour le niveau d’API 24 et niveaux supérieurs (JDK 8 prend également en charge les niveaux d’API antérieurs au niveau 24). Vous pouvez continuer d’utiliser [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez des applications spécifiquement pour le niveau d’API 23 ou des niveaux antérieurs.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

### <a name="android-sdk-manager"></a>Android SDK Manager

Android utilise plusieurs paramètres de niveau d’API Android pour déterminer la compatibilité de votre application avec les différentes versions Android (pour plus d’informations sur les niveaux d’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md)).
Selon le ou les niveaux d’API Android à cibler, vous devrez peut-être télécharger et installer d’autres composants Android SDK. De plus, vous aurez peut-être à installer les outils facultatifs et les images d’émulateur fournis par Android SDK. Pour cela, utilisez le **Gestionnaire Android SDK**. Lancez le **Gestionnaire Android SDK** en cliquant sur **Outils > Android > Gestionnaire Android SDK** :

[![Comment lancer le Gestionnaire Android SDK](windows-images/08-sdk-manager-sml.png)](windows-images/08-sdk-manager.png#lightbox)

Par défaut, Visual Studio installe le Gestionnaire Android SDK de Google :

![Capture d’écran du Gestionnaire Android SDK de Google](windows-images/09-google-sdk-manager.png)

Vous pouvez utiliser le Gestionnaire Android SDK de Google Android pour installer le paquet Android SDK Tools jusqu’à la version 25.2.3. Toutefois, si vous devez utiliser une version ultérieure du paquet Android SDK Tools, installez le plug-in Gestionnaire Android SDK Xamarin pour Visual Studio (disponible sur Visual Studio Marketplace). Cette installation est nécessaire, car la version autonome du Gestionnaire Android SDK de Google est dépréciée depuis la version 25.2.3 du paquet Android SDK Tools. 

Pour plus d’informations sur l’utilisation du Gestionnaire Android SDK Xamarin, consultez [Configuration du kit Android SDK](~/android/get-started/installation/android-sdk.md).

### <a name="android-emulator"></a>Émulateur Android

[L’émulateur Android](https://developer.android.com/studio/run/emulator) peut être très utile pour développer et tester une application Xamarin.Android. C’est le cas, par exemple, quand un appareil physique comme une tablette n’est disponible pendant le développement ou quand un développeur veut exécuter des tests d’intégration sur son ordinateur avant de valider le code.

L’émulation d’un appareil Android sur un ordinateur implique les composants suivants :

- **L’émulateur Google Android** : Il s’agit d’un émulateur basé sur [QEMU](https://www.qemu.org/) qui crée un appareil virtuel s’exécutant sur la station de travail du développeur.
- **Une image d’émulateur** : Une _image d’émulateur_ est un modèle ou une spécification du matériel et du système d’exploitation à virtualiser. Par exemple, une image d’émulateur identifie la configuration matérielle requise d’un appareil Nexus 5X exécutant Android 7.0 avec Google Play Services installé. Une autre image d’émulateur peut être une tablette 10 pouces exécutant Android 6.0.
- **Un appareil virtuel Android (AVD)** : Un _appareil virtuel Android_ est un appareil Android émulé créé à partir d’une image d’émulateur. Quand vous exécutez et testez des applications Android, Xamarin.Android démarre l’émulateur Android qui démarre un AVD spécifique, installe l’APK, puis exécute l’application.

Vous pouvez considérablement améliorer les performances en cas de développement sur des ordinateurs x86 en utilisant des images d’émulateur spéciales optimisées pour l’architecture x86 ainsi que l’une des deux technologies de virtualisation :

1. Microsoft Hyper-V &ndash; Disponible sur les ordinateurs exécutant la mise à jour d’avril 2018 de Windows 10
2. Intel HAXM (Gestionnaire d’exécution avec accélération matérielle) &ndash; Disponible sur les ordinateurs x86 exécutant OS X, macOS ou d’anciennes versions de Windows

Pour plus d’informations sur l’émulateur Android, Hyper-V et HAXM, consultez le guide [Accélération matérielle de l’émulateur Android](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

> [!NOTE]
> Sur les versions de Windows antérieures à la mise à jour d’avril 2018 de Windows 10, HAXM n’est pas compatible avec Hyper-V. Dans ce scénario, vous devez [désactiver Hyper-V](~/android/get-started/installation/android-emulator/troubleshooting.md#disable-hyperv) ou utiliser des images d’émulateur plus lentes qui n’ont pas les optimisations x86.

<a name="device" />

### <a name="android-device"></a>Appareil Android

Si vous disposez d’un appareil physique Android pour vos tests, c’est le moment de le configurer pour le développement. Consultez [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md) pour configurer votre appareil Android pour le développement. Ensuite, connectez-le à votre ordinateur pour exécuter et déboguer des applications Xamarin.Android.

## <a name="create-an-application"></a>Créer une application

Maintenant que vous avez installé Xamarin.Android, vous pouvez lancer Visual Studio pour créer un projet. Cliquez sur **Fichier > Nouveau > Projet** pour commencer à créer votre application :

![Comment créer un projet](windows-images/10-new-project.png)

Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Android** sous **Modèles**, puis cliquez sur **Application Android** dans le volet droit. Entrez un nom pour votre application (dans la capture d’écran ci-dessous, l’application se nomme **MyApp**), puis cliquez sur **OK** :

[![Capture d’écran de la boîte de dialogue Nouveau projet - Création d’une application Android vide](windows-images/11-first-app-sml.w157.png)](windows-images/11-first-app.w157.png#lightbox)

C’est tout ! Vous êtes maintenant prêt à utiliser Xamarin.Android pour créer des applications Android !

## <a name="summary"></a>Récapitulatif

Dans cet article, vous avez appris à configurer et à installer la plateforme Xamarin.Android sur un système Windows. Vous avez également vu comment configurer des emplacements d’installation personnalisés pour le JDK Java et Android SDK dans Visual Studio (facultatif), comment lancer le Gestionnaire Android SDK pour installer des composants Android SDK supplémentaires, comment configurer un émulateur et un appareil Android, et comment créer votre première application.

Dans la prochaine étape, vous allez étudier les didacticiels [Hello, Android](~/android/get-started/hello-android/index.md) pour apprendre à créer une application Xamarin.Android.

## <a name="related-links"></a>Liens associés

- [Télécharger Visual Studio](https://visualstudio.microsoft.com/vs/)
- [Installation de Visual Studio Tools pour Xamarin](~/get-started/installation/windows.md)
- [Configuration système requise](~/cross-platform/get-started/requirements.md)
- [Configuration du kit Android SDK](~/android/get-started/installation/android-sdk.md)
- [Configuration de l’émulateur Android](~/android/get-started/installation/android-emulator/index.md)
- [Configurer un appareil pour le développement](~/android/get-started/installation/set-up-device-for-development.md)
- [Exécuter des applications sur l’émulateur Android](https://developer.android.com/studio/run/emulator#Requirements)
