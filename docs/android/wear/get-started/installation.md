---
title: 'Installation et configuration de l’usure du système d’exploitation onXamarin. Android '
description: Cet article vous guide tout au long des étapes d’installation et des détails de configuration nécessaires à la préparation de votre ordinateur et de vos appareils au développement Android. À la fin de cet article, vous disposerez d’une installation Xamarin. Android d’usure intégrée dans Visual Studio pour Mac et/ou Microsoft Visual Studio, et vous serez prêt à commencer à créer votre première application Xamarin. Android usure.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 1f6fa57bbe47bbc2fe97bbd0df7018fc1cd3897a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522169"
---
# <a name="setup-and-installation"></a>Installation et configuration

_Cet article vous guide tout au long des étapes d’installation et des détails de configuration nécessaires à la préparation de votre ordinateur et de vos appareils au développement Android. À la fin de cet article, vous disposerez d’une installation Xamarin. Android d’usure intégrée dans Visual Studio pour Mac et/ou Microsoft Visual Studio, et vous serez prêt à commencer à créer votre première application Xamarin. Android usure._

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour créer des applications d’usure Android basées sur Xamarin:

- **Visual Studio ou Visual Studio pour Mac** &ndash; La Communauté Visual Studio 2017 ou une version ultérieure est requise.

- **Xamarin. Android** &ndash; Xamarin. Android 4,17 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac.

- **Android SDK** -Android SDK 5.0.1 (API 21) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

- **Kit de développement Java** Le développement Xamarin Android requiert [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) si vous développez pour l’API de niveau 24 ou supérieur (JDK 1,8 prend également en charge les niveaux d’API antérieurs à 24). &ndash;

Vous pouvez continuer à utiliser [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez spécifiquement pour l’API de niveau 23 ou antérieur.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

## <a name="installation"></a>Installation

Après avoir installé Xamarin. Android, procédez comme suit pour être prêt à créer et à tester des applications d’usure Android: 

1. Installez les Android SDK et les outils requis.
2. Configurez un appareil de test.
3. Créez votre première application Android usure.

Ces étapes sont décrites dans les sections suivantes.


### <a name="install-android-sdk-and-tools"></a>Installer Android SDK et les outils 

Lancez le **Gestionnaire de Android SDK**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Comment lancer le gestionnaire de Android SDK dans Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Comment lancer le gestionnaire de Android SDK dans Visual Studio pour Mac](installation-images/xs/sdk-menu.png)

-----


Assurez-vous que les Android SDK et les outils suivants sont installés:

* Android SDK Tools v 24.0.0 ou version ultérieure, et
* Android 4.4 W (API20) ou
* Android 5.0.1 (API21) ou version ultérieure.

Si le kit de développement logiciel (SDK) et les outils les plus récents ne sont pas installés, téléchargez les outils requis du kit de développement logiciel (SDK) &ndash; et les bits d’API (vous devrez peut-être faire défiler un peu pour les trouver dans la sélection de l’API): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Exemple de capture d’écran du gestionnaire SDK de l’activation des composants Android 5.0.1](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Exemple de capture d’écran du gestionnaire SDK de l’activation des composants Android 4,4 et 5.0.1](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuration

Avant de pouvoir utiliser tester votre application, vous devez configurer un émulateur d’usure Android ou un appareil d’usure Android réel. 


### <a name="android-wear-emulator"></a>Émulateur d’usure Android

Avant de pouvoir utiliser un émulateur Android d’usure, vous devez configurer un appareil virtuel Android d’usure à l’aide du **Gestionnaire d’émulateur Google**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Comment lancer le gestionnaire de Émulateur Android à partir de Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Comment lancer le gestionnaire de Émulateur Android à partir de Visual Studio pour Mac](installation-images/xs/emulator-menu.png)

-----

Pour plus d’informations sur la configuration d’un émulateur d’usure Android, consultez déboguer l' [usure Android sur un émulateur](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Appareil Android usure

Si vous avez un appareil Android d’usure comme un SmartWatch Android, vous pouvez déboguer l’application sur cet appareil au lieu d’utiliser un émulateur. Pour plus d’informations sur le développement avec un appareil d’usure, consultez déboguer [sur un appareil d’usure](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Créer votre première application Android usure

Suivez les instructions [Hello, usure](~/android/wear/get-started/hello-wear.md) pour créer votre première application Watch.


## <a name="packaging-your-app"></a>Empaquetage de votre application

Les applications d’usure Android sont toujours distribuées avec une application de téléphone Android auxiliaire. 

Quand vous ajoutez votre application Android usure comme référence à votre application Android principale, il est automatiquement considéré comme un projet Android d’usure et génère toutes les données XML et les métadonnées nécessaires pour vous. En outre, il vérifie que les numéros de package et de version correspondent pour vous permettre d’envoyer facilement vos applications à Google Play. 

Pour en savoir plus sur l’empaquetage d’applications, consultez [utilisation de](~/android/wear/deploy-test/packaging.md)l’empaquetage.


## <a name="related-links"></a>Liens associés

- [SkeletonWear (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)
