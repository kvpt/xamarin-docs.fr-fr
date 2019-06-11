---
title: 'Installation et configuration de l’usure du système d’exploitation onXamarin.Android '
description: Cet article décrit les étapes d’installation et les détails de configuration requises pour préparer votre ordinateur et les appareils pour le développement Android Wear. À la fin de cet article, vous disposerez d’un travail d’installation de l’usure de Xamarin.Android intégrée à Visual Studio pour Mac et/ou Microsoft Visual Studio, et vous serez prêt à créer votre première application Xamarin.Android usure.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 0bae98a204ba3478834894d6c093259a8b2139b2
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827719"
---
# <a name="setup-and-installation"></a>Installation et configuration

_Cet article décrit les étapes d’installation et les détails de configuration requises pour préparer votre ordinateur et les appareils pour le développement Android Wear. À la fin de cet article, vous disposerez d’un travail d’installation de l’usure de Xamarin.Android intégrée à Visual Studio pour Mac et/ou Microsoft Visual Studio, et vous serez prêt à créer votre première application Xamarin.Android usure._

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour créer des applications basées sur Xamarin Android Wear :

-   **Visual Studio ou Visual Studio pour Mac** &ndash; Visual Studio 2017 Community ou ultérieur est requis.

-   **Xamarin.Android** &ndash; Xamarin.Android 4,17 ou version ultérieure doit être installé et configuré avec Visual Studio ou Visual Studio pour Mac.

-   **Kit de développement logiciel Android** -Kit de développement logiciel Android 5.0.1 (API 21) ou version ultérieure doit être installé via le gestionnaire Android SDK.

-   **Kit de développement Java** &ndash; le développement Xamarin Android nécessite [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) si vous êtes développement pour le niveau d’API 24 ou supérieure (JDK 1.8 prend également en charge les niveaux d’API antérieures à 24).

Vous pouvez continuer à utiliser [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous êtes développement spécifiquement pour le niveau d’API 23 ou une version antérieure.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

## <a name="installation"></a>Installation

Une fois que vous avez installé Xamarin.Android, procédez comme suit afin que vous êtes prêt à générer et tester des applications Android Wear : 

1.  Installez le Kit Android SDK et les outils requis.
2.  Configurer un appareil de test.
3.  Créer votre première application Android Wear.

Ces étapes sont décrites dans les sections suivantes.


### <a name="install-android-sdk-and-tools"></a>Installer le Kit de développement logiciel Android 

Lancer le **Gestionnaire Android SDK**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Comment lancer le gestionnaire Android SDK dans Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Comment lancer le gestionnaire Android SDK dans Visual Studio pour Mac](installation-images/xs/sdk-menu.png)

-----


Assurez-vous d’avoir le Kit de développement logiciel Android suivantes et les outils installés :

* Android SDK Tools v 24.0.0 ou une version ultérieure, et
* Android 4.4W (API20), ou
* Android 5.0.1 (API21) ou une version ultérieure.

Si vous n’avez pas les dernières SDK et outils sont installés, téléchargez les outils du Kit de développement logiciel requis *et* les bits de l’API (vous devrez peut-être faire défiler un peu pour les trouver &ndash; la sélection de l’API est indiquée ci-dessous) : 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Capture d’écran de l’exemple SDK Manager de l’activation d’Android 5.0.1 composants](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Capture d’écran de l’exemple SDK Manager de l’activation d’Android 4.4 et 5.0.1 composants](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuration

Avant de pouvoir utiliser tester votre application, vous devez configurer un émulateur Android Wear ou un appareil Android Wear réel. 


### <a name="android-wear-emulator"></a>Émulateur d’Android Wear

Avant de pouvoir utiliser un émulateur Android Wear, vous devez configurer un Android Wear Android Virtual Device (AVD) à l’aide de la **Gestionnaire d’émulateur Google**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Comment lancer le Gestionnaire d’émulateur Android à partir de Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Comment lancer le Gestionnaire d’émulateur Android à partir de Visual Studio pour Mac](installation-images/xs/emulator-menu.png)

-----

Pour plus d’informations sur la configuration d’un émulateur Android Wear, consultez [déboguer Android Wear sur un émulateur](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Appareil Android Wear

Si vous avez un appareil Android Wear, par exemple un Smartwatch Android Wear, vous pouvez déboguer l’application sur cet appareil au lieu d’utiliser un émulateur. Pour plus d’informations sur le développement avec un appareil Wear, consultez [déboguer sur un appareil Wear](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Créer votre première application Android Wear

Suivez le [Hello, l’usure](~/android/wear/get-started/hello-wear.md) instructions pour créer votre première application watch.


## <a name="packaging-your-app"></a>Empaquetage de votre application

Android wear applications sont toujours distribuées avec une application de téléphone Android Compagnon. 

Lorsque vous ajoutez votre application Android Wear comme une référence à votre application Android principale, il est automatiquement considéré comme un projet Android Wear et génère tous les XML et les métadonnées nécessaires pour vous. En outre, il vérifie que les numéros de version et package correspondent donc vous pouvez facilement expédier vos applications sur Google Play. 

Pour en savoir plus sur l’empaquetage d’applications de l’usure, consultez [fonctionne avec l’empaquetage](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Liens associés

- [SkeletonWear (sample)](https://developer.xamarin.com/samples/monodroid/wear/SkeletonWear/)
