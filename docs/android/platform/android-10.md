---
title: Fonctionnalités Android 10
description: Comment commencer à développer des applications pour Android 10 à l’aide de Xamarin. Android.
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: 293fc8e3f8965e3109a0a549897dd23c34c46917
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198462"
---
# <a name="android-10-with-xamarin"></a>Android 10 avec Xamarin

_Comment commencer à développer des applications pour Android 10 à l’aide de Xamarin. Android._

Android 10 est désormais disponible à partir de Google. Un certain nombre de nouvelles fonctionnalités et API sont mises à disposition dans cette version, et beaucoup d’entre elles sont nécessaires pour tirer parti des nouvelles capacités matérielles des appareils Android les plus récents.

![Logo Android 10](~/android/platform/android-10-images/android10_black.png)

Cet article est structuré pour vous aider à commencer à développer des applications Xamarin. Android pour Android 10. Il explique comment installer les mises à jour nécessaires, configurer le kit de développement logiciel (SDK) et préparer un émulateur ou un appareil à des fins de test. Il fournit également un aperçu des nouvelles fonctionnalités d’Android 10 et fournit un exemple de code source illustrant l’utilisation de certaines des principales fonctionnalités Android 10.

Xamarin. Android 10,0 prend en charge Android 10. Pour plus d’informations sur la prise en charge de Xamarin. Android pour Android 10, consultez les [notes de publication de Xamarin. android 10,0](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0).

## <a name="requirements"></a>Configuration requise

La liste suivante est requise pour utiliser les fonctionnalités Android 10 dans les applications basées sur Xamarin :

- **Visual Studio** -visual studio 2019 est recommandé. Sur Windows Update vers Visual Studio 2019 version 16,3 ou ultérieure. Sur macOS, mettez à jour vers Visual Studio 2019 pour Mac version 8,3 ou ultérieure.
- **Xamarin. Android** -Xamarin. Android 10,0 ou version ultérieure doit être installé avec Visual Studio (Xamarin. Android est installé automatiquement dans le cadre du **développement mobile avec** la charge de travail .net sur Windows et installé dans le cadre de **Visual Studio pour Mac installer**)
- **Kit** de développement Java-le développement Xamarin. Android 10,0 requiert JDK 8. La distribution de Microsoft OpenJDK est automatiquement installée dans le cadre de Visual Studio.
- **Android SDK** -Android SDK l’API 29 doit être installée via le gestionnaire de Android SDK.

## <a name="get-started"></a>Prise en main

Pour commencer à développer des applications Android 10 avec Xamarin. Android, vous devez télécharger et installer les derniers outils et packages du kit de développement logiciel (SDK) avant de pouvoir créer votre premier projet Android 10 :

1. **Visual Studio 2019 est recommandé**. Mettez à jour vers Visual Studio 2019 version 16,3 ou ultérieure. Si vous utilisez Visual Studio pour Mac 2019, mettez à jour vers Visual Studio 2019 pour Mac version 8,3 ou ultérieure.
2. Installez les packages et les outils **Android 10 (API 29)** via le gestionnaire du kit de développement logiciel (SDK).
    - Plateforme du kit de développement logiciel (SDK) Android 10 (API 29)
    - Image système Android 10 (API 29)
    - Android SDK Build-outils 29.0.0 +
    - Android SDK Platform-Tools 29.0.0 +
    - Émulateur Android 29.0.0 +
3. Créez un nouveau projet Xamarin. Android qui cible Android 10,0.
4. Configurez un émulateur ou un appareil pour tester les applications Android 10.

Chacune de ces étapes est décrite ci-dessous :

### <a name="update-visual-studio"></a>Mettre à jour Visual Studio 2017

Visual Studio 2019 est recommandé pour créer des applications Android 10 à l’aide de Xamarin.

Si vous utilisez Visual Studio 2019, mettez à jour vers Visual Studio 2019 version 16,3 ou ultérieure (pour obtenir des instructions, consultez [mettre à jour Visual studio 2019 vers la version la plus récente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). Sur macOS, mettez à jour vers Visual Studio 2019 pour Mac 8,3 ou version ultérieure (pour obtenir des instructions, consultez [mettre à jour Visual studio 2019 pour Mac vers la version la plus récente](https://docs.microsoft.com/en-us/visualstudio/mac/update)).

### <a name="install-the-android-sdk"></a>Installer le Android SDK

Pour créer un projet avec Xamarin. Android 10,0, vous devez d’abord utiliser le gestionnaire de Android SDK pour installer la plateforme SDK pour **Android 10 (niveau d’API 29)** .

1. Démarrez le gestionnaire du kit SDK. Dans Visual Studio, cliquez sur **outils > Android > gestionnaire de Android SDK.** Dans Visual Studio pour Mac, cliquez sur **outils > gestionnaire du kit de développement logiciel (SDK).**
2. Dans le coin inférieur droit, cliquez sur l’icône d’engrenage et sélectionnez **dépôt > Google (non pris en charge) :**

    ![Sélection du référentiel du gestionnaire de Android SDK](~/android/platform/android-10-images/sdkrepository.png)

3. Installez les packages de **plateforme Android 10 SDK** , qui sont répertoriés comme **Android SDK plate-forme 29** dans l’onglet **plateformes** (pour plus d’informations sur l’utilisation du gestionnaire du kit de développement logiciel (SDK), consultez [Android SDK configuration](https://docs.microsoft.com/en-us/xamarin/android/get-started/installation/android-sdk)) :

    ![Onglet plateforme de Android SDK Manager](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>Créer un projet Xamarin. Android

Créez un projet Xamarin. Android. Si vous débutez avec le développement Android avec Xamarin, consultez [Hello, Android](https://docs.microsoft.com/en-us/xamarin/android/get-started/hello-android/index) pour en savoir plus sur la création de projets Xamarin. Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de version pour cibler Android 10,0 ou une version ultérieure. Par exemple, pour cibler votre projet pour Android 10, vous devez configurer le niveau d’API Android cible de votre projet sur **android 10,0 (API 29)** . Cela comprend la version **cible de .NET Framework** et la **version de Android SDK cible** vers l’API 29 ou ultérieure. Pour plus d’informations sur la configuration des niveaux d’API Android, consultez [Présentation des niveaux d’API Android.](https://docs.microsoft.com/en-us/xamarin/android/app-fundamentals/android-api-levels)

![Framework cible Xamarin. Android](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>Configurer un appareil ou un émulateur

Si vous utilisez un appareil physique tel qu’un pixel, vous pouvez télécharger la mise à jour Android 10 en accédant `System` au dans les  >   >  `System update` `Check for update` paramètres de votre téléphone. Si vous préférez flasher votre appareil, consultez les instructions sur le clignotement d’une image de [fabrique](https://developers.google.com/android/ota) ou d’une [image OTA](https://developers.google.com/android/ota) sur votre appareil.

Si vous utilisez un émulateur, créez un appareil virtuel pour le niveau d’API 29 et sélectionnez une image x86. Pour plus d’informations sur l’utilisation de la Android Device Manager pour créer et gérer des appareils virtuels, consultez [gestion des appareils virtuels avec le Android Device Manager.](https://docs.microsoft.com/en-us/xamarin/android/get-started/installation/android-emulator/device-manager) Pour plus d’informations sur l’utilisation de l’Émulateur Android à des fins de test et de débogage, consultez [débogage sur le émulateur Android.](https://docs.microsoft.com/en-us/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>Nouvelles fonctionnalités

Android 10 introduit une variété de nouvelles fonctionnalités. Certaines de ces nouvelles fonctionnalités sont destinées à tirer parti des nouvelles capacités matérielles offertes par les derniers appareils Android, tandis que d’autres sont conçues pour améliorer l’expérience des utilisateurs Android :

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>Améliorez votre application avec des fonctionnalités et des API Android 10

Ensuite, lorsque vous êtes prêt, explorez Android 10 et découvrez les [nouvelles fonctionnalités et API](https://developer.android.com/preview/api-overview.html) que vous pouvez utiliser. Voici quelques-unes des principales fonctionnalités de prise en main de.

Ces fonctionnalités sont recommandées pour chaque application :

- **Thème sombre :**  garantissez une expérience cohérente aux utilisateurs qui activent un thème sombre à l’ensemble du système en ajoutant un [thème](https://developer.android.com/preview/features/darktheme) foncé ou en activant la [force sombre](https://developer.android.com/preview/features/darktheme#force_dark).

![Thème foncé](~/android/platform/android-10-images/darktheme.png)

- **Prenez en charge la [navigation](https://developer.android.com/preview/features/gesturalnav)**  Gestural dans votre application en passant de la périphérie à la périphérie et en vous assurant que vos mouvements personnalisés sont complémentaires aux gestes de navigation système.

![Navigation dans les gestes](~/android/platform/android-10-images/gesturenavigation.png)

- **Optimisez pour les appareils pliables :**  offrez des expériences de périphérie vers le bord et des appareils innovateurs à l’heure actuelle en [optimisant les éléments pliables](https://developer.android.com/preview/features/foldables).

![Repli](~/android/platform/android-10-images/foldable.png)

Ces fonctionnalités sont recommandées si elles s’appliquent à votre application :

- **Plus de notifications interactives :**  si vos notifications incluent des messages, activez les [réponses et les actions suggérées dans les notifications](https://developer.android.com/preview/features#smart-suggestions) pour impliquer les utilisateurs et leur permettre de prendre des mesures instantanément.
- **Meilleure biométrie :**  si vous utilisez l’authentification biométrique, passez à [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt), la meilleure façon de prendre en charge l’authentification par empreinte digitale sur les appareils modernes.
- **Enregistrement enrichi :**  pour prendre en charge le sous-titrage ou l’enregistrement de jeu, activez [la capture de lecture audio](https://developer.android.com/preview/features/playback-capture). C’est un excellent moyen d’atteindre davantage d’utilisateurs et rendre votre application plus accessible.
- **Meilleurs codecs :**  pour les applications multimédias, essayez [AV1](https://en.wikipedia.org/wiki/AV1) pour la diffusion vidéo et [HDR10 +](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) pour une vidéo à plage dynamique élevée. Pour la diffusion vocale et musicale, vous pouvez utiliser l’encodage [opus](http://opus-codec.org/) , et pour les musiciens, une  [API midi Native](https://developer.android.com/preview/features/midi)est disponible.
- **Meilleures API réseau :**  si votre application gère des appareils IOT via Wi-Fi, essayez les nouvelles [API](https://developer.android.com/preview/features#peer2peer) de connexion réseau pour les fonctions telles que la configuration, le téléchargement ou l’impression.

Il ne s’agit là que de quelques-unes des nombreuses nouvelles fonctionnalités et API d’Android 10. Pour tous les voir, visitez le [site Android 10 pour les développeurs](https://developer.android.com/about/versions/10/highlights).

## <a name="behavior-changes"></a>Changements de comportement

Lorsque la version d’Android cible est définie sur le niveau d’API 29, plusieurs modifications de plateforme ont une incidence sur le comportement de votre application, même si vous n’implémentez pas les nouvelles fonctionnalités décrites ci-dessus. La liste suivante est un bref résumé de ces modifications :

- [Pour garantir la stabilité et la compatibilité de l’application, la plateforme Android restreint désormais les interfaces qui ne sont pas des kits de développement logiciel (SDK) que votre application peut utiliser dans Android 10](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions).
- La [mémoire partagée a changé](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory).
- Le [Runtime Android & l’exactitude](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat)de l’AOA.
- [Les autorisations pour les intentions fullscreen doivent demander `USE_FULL_SCREEN_INTENT` ](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents).
- [Prise en charge des pliables](https://developer.android.com/about/versions/10/behavior-changes-10#foldables).

## <a name="summary"></a>Récapitulatif

Cet article a présenté Android 10 et a expliqué comment installer et configurer les outils et packages les plus récents pour le développement Xamarin. Android avec Android 10. Il a fourni une vue d’ensemble des fonctionnalités clés disponibles dans Android 10. Elle contient des liens vers des rubriques de documentation sur les API et des développeurs Android qui vous aideront à créer des applications pour Android 10. Elle a également mis en évidence les modifications de comportement Android 10 les plus importantes susceptibles d’avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Android 10](https://developer.android.com/about/versions/10)
