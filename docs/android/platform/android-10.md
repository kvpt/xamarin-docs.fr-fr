---
title: Fonctions Android 10
description: Comment commencer à développer des applications pour Android 10 en utilisant Xamarin.Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: c19c9e5bd279824ea2d3e4e9f88857388f786a2c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73612271"
---
# <a name="android-10-with-xamarin"></a>Android 10 avec Xamarin

_Comment commencer à développer des applications pour Android 10 en utilisant Xamarin.Android._

Android 10 est maintenant disponible sur Google. Un certain nombre de nouvelles fonctionnalités et API sont disponibles dans cette version, et beaucoup d’entre eux sont nécessaires pour tirer parti de nouvelles capacités matérielles dans les derniers appareils Android.

![Android 10 Logo](~/android/platform/android-10-images/android10_black.png)

Cet article est structuré pour vous aider à démarrer dans le développement des applications Xamarin.Android pour Android 10. Il explique comment installer les mises à jour nécessaires, configurer le SDK et préparer un émulateur ou un appareil pour les tests. Il fournit également un aperçu des nouvelles fonctionnalités dans Android 10 et fournit un exemple de code source qui illustre comment utiliser certaines des fonctionnalités clés Android 10.

Xamarin.Android 10.0 fournit un support pour Android 10. Pour plus d’informations sur le support Xamarin.Android pour Android 10, voir les [notes de sortie Xamarin.Android 10.0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Spécifications

La liste suivante est nécessaire pour utiliser les fonctionnalités Android 10 dans les applications basées sur Xamarin :

- **Visual Studio** - Visual Studio 2019 est recommandé. Mise à jour Windows de Visual Studio 2019 version 16.3 ou plus tard. Sur macOS, mise à jour sur Visual Studio 2019 pour mac version 8.3 ou plus tard.
- **Xamarin.Android** - Xamarin.Android 10.0 ou plus tard doit être installé avec Visual Studio (Xamarin.Android est automatiquement installé dans le cadre du développement mobile avec la charge de travail **.NET** sur Windows et installé dans le cadre du **studio visuel pour Mac Installer**)
- **Java Developer Kit** - Xamarin.Android 10.0 développement nécessite JDK 8. La distribution de Microsoft de l’OpenJDK est automatiquement installée dans le cadre de Visual Studio.
- **Android SDK** - Android SDK API 29 doit être installé via le gestionnaire Android SDK.

## <a name="get-started"></a>Prise en main

Pour commencer à développer des applications Android 10 avec Xamarin.Android, vous devez télécharger et installer les derniers outils et forfaits SDK avant de pouvoir créer votre premier projet Android 10 :

1. **Visual Studio 2019 est recommandé**. Mise à jour de Visual Studio 2019 version 16.3 ou plus tard. Si vous utilisez Visual Studio pour Mac 2019, mise à jour sur Visual Studio 2019 pour mac version 8.3 ou plus tard.
2. Installez des forfaits et des outils **Android 10 (API 29)** via le SDK Manager.
    - Android 10 (API 29) Plateforme SDK
    - Android 10 (API 29) Image système
    - Android SDK Build-Tools 29.0.0
    - Android SDK Platform-Tools 29.0.0
    - Emulateur Android 29.0.0
3. Créez un nouveau projet Xamarin.Android qui cible Android 10.0.
4. Configurer un émulateur ou un appareil pour tester les applications Android 10.

Chacune de ces étapes est expliquée ci-dessous :

### <a name="update-visual-studio"></a>Mettre à jour Visual Studio 2017

Visual Studio 2019 est recommandé pour la construction d’applications Android 10 en utilisant Xamarin.

Si vous utilisez Visual Studio 2019, mettez à jour sur Visual Studio 2019 version 16.3 ou plus tard (pour les instructions, voir [Update Visual Studio 2019 à la version la plus récente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). Sur macOS, mise à jour de Visual Studio 2019 pour Mac 8.3 ou plus tard (pour les instructions, voir [Update Visual Studio 2019 pour Mac à la version la plus récente](https://docs.microsoft.com/visualstudio/mac/update)).

### <a name="install-the-android-sdk"></a>Installer l’Android SDK

Pour créer un projet avec Xamarin.Android 10.0, vous devez d’abord utiliser le gestionnaire Android SDK pour installer la plate-forme SDK pour **Android 10 (niveau API 29)**.

1. Démarrez le SDK Manager. Dans Visual Studio, cliquez sur **Tools > Android > Android SDK Manager.** Dans Visual Studio pour Mac, cliquez sur **Tools > SDK Manager.**
2. Dans le coin inférieur droit, cliquez sur l’icône de l’engrenage et **sélectionnez Repository > Google (non pris en compte) :**

    ![Sélection de référentiel Android SDK Manager](~/android/platform/android-10-images/sdkrepository.png)

3. Installez les forfaits **Android 10 SDK Platform,** qui sont répertoriés comme **Android SDK Platform 29** dans l’onglet **Plates-formes** (pour plus d’informations sur l’utilisation du gestionnaire SDK, voir [android SDK configuration](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)):

    ![Onglet Android SDK Manager Platform](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Créer un projet Xamarin.Android

Créez un nouveau projet Xamarin.Android. Si vous êtes nouveau au développement Android avec Xamarin, voir [Bonjour, Android](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index) pour en savoir plus sur la création de projets Xamarin.Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de la version pour cibler Android 10.0 ou plus tard. Par exemple, pour cibler votre projet pour Android 10, vous devez configurer le niveau d’API Android cible de votre projet à **Android 10.0 (API 29)**. Cela inclut à la fois votre **version cadre cible** et la version **SDK Android cible** à API 29 ou plus tard. Pour plus d’informations sur la configuration des niveaux d’API Android, voir [Comprendre les niveaux d’API Android.](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)

![Cadre cible Xamarin.Android](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configurer un appareil ou un émulateur

Si vous utilisez un appareil physique tel qu’un Pixel, vous pouvez `System`  >  `System update`  >  télécharger la mise à jour Android 10 en allant dans les `Check for update` paramètres de votre téléphone. Si vous préférez clignoter votre appareil, veuillez consulter les instructions sur le clignotement d’une [image d’usine](https://developers.google.com/android/images) ou [d’une image OTA](https://developers.google.com/android/ota) à votre appareil.

Si vous utilisez un émulateur, créez un appareil virtuel pour le niveau 29 de l’API et sélectionnez une image à base de x86. Pour plus d’informations sur l’utilisation du gestionnaire d’appareils Android pour créer et gérer des appareils virtuels, voir [Gérer les périphériques virtuels avec le gestionnaire d’appareils Android.](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager) Pour plus d’informations sur l’utilisation de l’émulateur Android pour les tests et le débogage, voir [Debugging sur l’émulateur Android.](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>Nouvelles fonctionnalités

Android 10 présente une variété de nouvelles fonctionnalités. Certaines de ces nouvelles fonctionnalités sont destinées à tirer parti de nouvelles capacités matérielles offertes par les derniers appareils Android, tandis que d’autres sont conçus pour améliorer davantage l’expérience utilisateur Android:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Améliorez votre application avec les fonctionnalités Android 10 et les API

Ensuite, lorsque vous êtes prêt, plongez dans Android 10 et découvrez les [nouvelles fonctionnalités et les API](https://developer.android.com/preview/api-overview.html) que vous pouvez utiliser. Voici quelques-unes des principales fonctionnalités pour commencer avec.

Ces fonctionnalités sont recommandées pour chaque application :

- **Dark Theme:** Assurer une expérience cohérente pour les utilisateurs qui permettent un thème sombre à l’échelle du système en ajoutant un [thème](https://developer.android.com/preview/features/darktheme) sombre ou en permettant [Force Dark](https://developer.android.com/preview/features/darktheme#force_dark).

![Thème foncé](~/android/platform/android-10-images/darktheme.png)

- **Soutenez [la navigation](https://developer.android.com/preview/features/gesturalnav)** gestuelle dans votre application en allant de bord en bord et en vous assurant que vos gestes personnalisés sont complémentaires aux gestes de navigation du système.

![Navigation gestuelle](~/android/platform/android-10-images/gesturenavigation.png)

- **Optimisez les pliables :** Offrez des expériences transparentes et bord à bord sur les appareils innovants d’aujourd’hui [en optimisant les pliables.](https://developer.android.com/preview/features/foldables)

![Pliable](~/android/platform/android-10-images/foldable.png)

Ces fonctionnalités sont recommandées si cela est pertinent pour votre application :

- **Notifications plus interactives :** Si vos notifications incluent des messages, activez [les réponses et les actions suggérées dans](https://developer.android.com/preview/features#smart-suggestions) les notifications pour engager les utilisateurs et les laisser agir instantanément.
- **Meilleure biométrie:** Si vous utilisez l’auth biométrique, passez à [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), le moyen préféré de prendre en charge l’empreinte digitale auth sur les appareils modernes.
- **Enregistrement enrichi :** Pour prendre en charge le sous-titrage ou l’enregistrement de gameplay, activez [la capture audio de lecture.](https://developer.android.com/preview/features/playback-capture) C’est un excellent moyen d’atteindre plus d’utilisateurs et de rendre votre application plus accessible.
- **Meilleurs codecs:** Pour les applications multimédias, essayez [AV1](https://en.wikipedia.org/wiki/AV1) pour le streaming vidéo et [HDR10 pour](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) la vidéo à haute plage dynamique. Pour la diffusion de la parole et de la musique, vous pouvez utiliser l’encodage [Opus,](http://opus-codec.org/) et pour les musiciens, une [API](https://developer.android.com/preview/features/midi) MIDI native est disponible.
- **Meilleures API de réseautage :** Si votre application gère les appareils IoT via Wi-Fi, essayez les nouvelles [API](https://developer.android.com/preview/features#peer2peer) de connexion réseau pour des fonctions comme la configuration, le téléchargement ou l’impression.

Ce ne sont que quelques-unes des nombreuses nouvelles fonctionnalités et API dans Android 10. Pour les voir tous, visitez le [site Android 10 pour les développeurs](https://developer.android.com/about/versions/10/highlights).

## <a name="behavior-changes"></a>Changements de comportement

Lorsque la version Android cible est réglée au niveau 29 de l’API, il existe plusieurs changements de plate-forme qui peuvent affecter le comportement de votre application, même si vous n’implémentez pas les nouvelles fonctionnalités décrites ci-dessus. La liste suivante est un bref résumé de ces changements :

- [Pour assurer la stabilité et la compatibilité de l’application, la plate-forme Android limite désormais les interfaces non SDK que votre application peut utiliser dans Android 10](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions).
- [La mémoire partagée a changé](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory).
- [Android runtime & AOT correctness](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat).
- [Les autorisations pour les `USE_FULL_SCREEN_INTENT`intentions à écran complet doivent demander ](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Prise en charge des pliables](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Récapitulatif

Cet article a introduit Android 10 et expliqué comment installer et configurer les derniers outils et paquets pour le développement Xamarin.Android avec Android 10. Il a fourni un aperçu des principales fonctionnalités disponibles dans Android 10. Il comprenait des liens vers la documentation API et des sujets de développeur Android pour vous aider à démarrer dans la création d’applications pour Android 10. Il a également mis en évidence les changements de comportement Android 10 les plus importants qui pourraient avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Android 10](https://developer.android.com/about/versions/10)
