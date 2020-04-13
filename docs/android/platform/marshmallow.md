---
title: Caractéristiques de la guimauve
description: Cet article vous aide à commencer à utiliser Xamarin.Android pour développer des applications pour Android 6.0 Marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: fb1ba92be9527d490b3d34bd4c0e454b0a750837
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019992"
---
# <a name="marshmallow-features"></a>Caractéristiques de la guimauve

_Cet article vous aide à commencer à utiliser Xamarin.Android pour développer des applications pour Android 6.0 Marshmallow._

Cet article fournit un aperçu des nouvelles fonctionnalités dans Android 6.0 Marshmallow, explique comment préparer Xamarin.Android pour le développement Android Marshmallow, et fournit des liens vers des applications d’échantillons qui illustrent comment faire usage de nouvelles fonctionnalités Android Marshmallow dans les applications Xamarin.Android. 

## <a name="overview"></a>Vue d’ensemble

[Android 6.0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), est la prochaine version android majeur après Android Lollipop.
Xamarin.Android prend en charge Android Marshmallow et comprend:

- **API 23/Android 6.0 Bindings** &ndash; Android 6.0 ajoute de nombreuses nouvelles API pour les nouvelles fonctionnalités décrites ci-dessous; ces API sont disponibles pour les applications Xamarin.Android lorsque vous ciblez le niveau 23 de l’API. Pour plus d’informations sur les API Android 6.0, voir [API Android 6.0](https://developer.android.com/preview/api-overview.html). 

[![Images de héros des tablettes et des téléphones exécutant la guimauve](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Bien que la version Marshmallow est principalement axée sur "polish and quality", il fournit également de nombreuses nouvelles fonctionnalités d’intérêt pour les développeurs Xamarin.Android. Voici quelques fonctionnalités : 

- **Autorisations** &ndash; d’exécution Cette amélioration permet aux utilisateurs d’approuver les autorisations de sécurité au cas par cas au moment de l’exécution. 

- **Améliorations d’authentification** &ndash; À partir de Android Marshmallow, les applications peuvent désormais utiliser des capteurs d’empreintes digitales pour authentifier les utilisateurs, et une nouvelle fonctionnalité *d’identification de confirmation* minimise la nécessité d’entrer des mots de passe. 

- **App Linking** &ndash; Cette fonctionnalité permet d’éliminer la nécessité de faire apparaître l’App **Chooser** en associant automatiquement les applications à des domaines Web. 

- **Partage** &ndash; direct Vous pouvez définir des *objectifs de partage direct* qui rendent le partage rapide et intuitif pour les utilisateurs ; cette fonctionnalité permet aux utilisateurs de partager du contenu avec d’autres applications. 

- **Interactions vocales** &ndash; Cette nouvelle API vous permet de créer des fonctions vocales conversationnelles dans votre application. 

- &ndash; **Mode d’affichage 4K** Dans Android Marshmallow, votre application peut demander une résolution d’affichage 4K sur le matériel qui le prend en charge. 

- **Nouvelles fonctionnalités** &ndash; audio À partir de Marshmallow, Android prend désormais en charge le protocole MIDI. Il fournit également de nouvelles classes pour créer des objets de capture audio et de lecture numériques, et il offre de nouveaux crochets API pour associer des appareils audio et d’entrée. 

- **New Video Features** &ndash; Marshmallow fournit une nouvelle classe qui aide les applications à rendre les flux audio et vidéo synchronisés; cette classe fournit également un soutien pour le taux de lecture dynamique. 

- **Android for Work** &ndash; Marshmallow comprend des contrôles améliorés pour les appareils à usage unique appartenant à l’entreprise. Il prend en charge l’installation silencieuse et le désinstallation des applications par le propriétaire de l’appareil, l’acceptation automatique des mises à jour du système, l’amélioration de la gestion des certificats, le suivi de l’utilisation des données, la gestion des autorisations et les notifications d’état de travail. 

- **Bibliothèque** &ndash; de soutien à la conception des matériaux La nouvelle *bibliothèque de soutien à la conception* fournit des composants de conception et des modèles qui vous facilitent la construction de l’aspect et de la sensation de conception de matériaux dans votre application. 

En outre, de nombreuses mises à jour de base de la bibliothèque Android ont été publiés avec Android M, et ces mises à jour fournissent de nouvelles fonctionnalités pour Android M et les versions antérieures d’Android.

En outre, de nombreuses mises à jour de base de la bibliothèque Android ont été publiés avec Android Marshmallow, et ces mises à jour fournissent de nouvelles fonctionnalités à la fois pour Android Marshmallow et les versions antérieures d’Android. Cet article explique comment commencer à construire des applications avec Android Marshmallow, et il fournit un aperçu des faits saillants nouvelle fonctionnalité dans Android 6.0. 

## <a name="requirements"></a>Spécifications

Ce qui suit est nécessaire pour utiliser les nouvelles fonctionnalités Android Marshmallow dans les applications basées sur Xamarin: 

- **Xamarin.Android** &ndash; Xamarin.Android 5.1.7.12 ou plus tard doit être installé et configuré avec Visual Studio ou Xamarin Studio.

- **Studio visuel pour Mac** ou **Visual Studio** &ndash; Si vous utilisez Visual Studio pour Mac, la version 5.9.7.22 ou plus tard est nécessaire. Si vous utilisez Visual Studio, la version 3.11.1537 ou plus tard des outils Xamarin pour Visual Studio est requise. 

- **Android SDK** &ndash; Android SDK 6.0 (API 23) ou plus tard doit être installé via le gestionnaire Android SDK.

- **Java Developer Kit** &ndash; Xamarin.Android nécessite [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou plus tard si vous développez pour le niveau API 24 ou plus (JDK 1.8 prend également en charge les niveaux d’API plus tôt que 24, y compris la guimauve). La version 64 bits de JDK 1.8 est requise si vous utilisez des contrôles personnalisés ou l’aperçu des formulaires.

Vous pouvez continuer à utiliser [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez spécifiquement pour le niveau API 23 ou plus tôt. 

## <a name="getting-started"></a>Mise en route

Pour commencer à utiliser Android Marshmallow avec Xamarin.Android, vous devez télécharger et installer les derniers outils et forfaits SDK avant de pouvoir créer un projet Android Marshmallow : 

1. Installez les dernières mises à jour Xamarin du canal **Stable.** 

2. Installez les paquets et outils Android 6.0 Marshmallow SDK.

3. Créez un nouveau projet Xamarin.Android qui cible android 6.0 Marshmallow (niveau API 23). 

4. Configurer un émulateur ou un appareil pour Android Marshmallow.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="install-xamarin-updates"></a>Installer les mises à jour Xamarin

Pour mettre à jour Xamarin afin qu’il inclut la prise en charge pour Android 6.0 Marshmallow, changer le canal de mise à jour à **Stable** et installer toutes les mises à jour. Pour plus d’informations sur l’installation des mises à jour à partir du canal de mises à jour, voir [Changer le canal de mises à jour](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Installer l’Android 6.0 SDK

Pour créer un projet Xamarin.Android pour Android Marshmallow, vous devez d’abord utiliser le gestionnaire Android SDK pour installer l’Android 6.0 SDK:

- Démarrer l’Android SDK Manager (en studio visuel pour Mac, utiliser **Tools > SDK Manager**; dans Visual Studio, utiliser Des outils > Android > Android **SDK Manager**) et installer les derniers outils Android SDK:

    [![Sélection d’outils Android SDK dans le gestionnaire Android SDK](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Aussi, installer les derniers paquets **Android 6.0** SDK:

    [![Sélection des forfaits Android 6.0 SDK dans le android SDK Manager](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Vous devez installer Android SDK Tools révision 24.3.4 ou plus tard.
Pour plus d’informations sur l’utilisation de l’Android SDK Manager pour installer l’Android 6.0 SDK, voir [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin.Android

Créez un nouveau projet Xamarin.Android. Si vous êtes nouveau au développement Android avec Xamarin, voir [Bonjour, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Android. 

Lorsque vous créez un projet Android, vous devez configurer les paramètres de la version pour cibler Android 6.0 MarshMallow. Pour cibler votre projet pour Marshmallow, vous devez configurer votre projet pour **le niveau API 23 (Xamarin.Android v6.0 Support)**. Pour en savoir plus sur la configuration des niveaux de niveau d’API Android, voir [Comprendre les niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurer un émulateur ou un appareil

Si vous utilisez un émulateur, démarrez le gestionnaire AVD Android et créez un nouvel appareil en utilisant les paramètres suivants :

- Appareil: Nexus 5, 6, ou 9.
- Cible: Android 6.0 - API Niveau 23
- ABI: x86

Par exemple, cet appareil virtuel est configuré pour imiter un Nexus 5 :

[![Configurer un AVD à l’aide de l’appareil Nexus 5, Android 6.0 Target et Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Si vous utilisez un appareil physique tel qu’un Nexus 5, 6 ou 9, vous pouvez installer une image de prévisualisation de La guimauve Android. Pour plus d’informations sur la mise à jour de votre appareil sur Android Marshmallow, voir [Hardware System Images](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Nouvelles fonctionnalités

Bon nombre des changements introduits dans Android Marshmallow sont axés sur l’amélioration de l’expérience utilisateur Android, l’augmentation des performances, et la fixation des bogues. Cependant, Marshmallow a également introduit quelques changements importants aux fondamentaux de la plate-forme Android. Les sections suivantes mettent en évidence ces améliorations et fournissent des liens pour vous aider à commencer à utiliser les nouvelles fonctionnalités Android Marshmallow dans votre application. 

### <a name="runtime-permissions"></a>Autorisations d’exécution

Le système d’autorisations Android a été considérablement optimisé et simplifié depuis Android Lollipop. Dans Android Marshmallow, les utilisateurs accordent des autorisations au cas par cas au moment de l’exécution plutôt qu’au moment de l’installation. Pour prendre en charge cette fonctionnalité sur Android Marshmallow et plus tard, vous concevez votre application pour inciter l’utilisateur à obtenir des autorisations à l’heure d’exécution (dans le contexte de l’endroit où les autorisations sont nécessaires). Cette modification permet aux utilisateurs de commencer immédiatement à utiliser votre application, car elle simplifie le processus d’installation et de mise à niveau de votre application. 

Voir [demander des autorisations de temps d’exécution dans Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) pour plus de détails (y compris des exemples de code) sur la mise en œuvre des autorisations de temps dans les applications Xamarin.Android.
Xamarin fournit également une application d’échantillon qui illustre comment les autorisations de temps d’exécution fonctionnent dans Android Marshmallow (et plus tard) : [RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

Cette application d’échantillon démontre ce qui suit :

- Comment vérifier et demander des autorisations au moment de l’exécution.
- Comment déclarer les autorisations pour les appareils Android M.

Pour utiliser cet exemple d’application :

1. Appuyez sur les boutons **Caméra** ou **Contacts** pour afficher un dialogue de demande d’autorisation.
2. Accorder la permission de voir des fragments de caméra ou de contacts.

Pour plus d’informations sur les nouvelles fonctionnalités d’autorisations de temps d’exécution dans Android Marshmallow, voir [Travailler avec des autorisations système](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Améliorations d’authentification

Android Marshmallow comprend deux améliorations d’authentification qui aident à éliminer le besoin de mots de passe:

- **L’authentification des empreintes digitales** &ndash; utilise un balayage des empreintes digitales pour authentifier les utilisateurs.

- **Confirmez les utilisateurs d’authentification d’informations** &ndash; sur la durée du déverrouillage de l’appareil.

Les liens et les exemples d’applications décrits ensuite peuvent vous aider à vous familiariser avec ces nouvelles fonctionnalités.

#### <a name="fingerprint-authentication"></a>Authentification par empreinte digitale

Sur les appareils qui prennent en charge `FingerPrintManager` le matériel de numérisation des empreintes digitales, vous pouvez utiliser la nouvelle classe pour authentifier un utilisateur.
Pour plus d’informations sur la fonction d’authentification des empreintes digitales dans Android Marshmallow, voir [Fingerprint Authentication](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin fournit une application d’échantillon qui illustre comment utiliser les empreintes digitales enregistrées pour authentifier un utilisateur dans votre application: [FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Pour utiliser cet exemple d’application :

1. Touchez le bouton **Achat** pour ouvrir un dialogue d’authentification des empreintes digitales.
2. Scannez votre empreinte digitale enregistrée pour vous authentifier.

Notez que cette application d’échantillon nécessite un appareil avec un lecteur d’empreintes digitales.
Cette application ne stocke pas votre empreinte digitale (ou votre mot de passe).

#### <a name="voice-interactions"></a>Interactions vocales

La nouvelle fonction Interactions vocales introduite dans Android Marshmallow permet aux utilisateurs de votre application d’utiliser leur voix pour confirmer les actions et sélectionner parmi une liste d’options. Pour plus d’informations sur les interactions vocales, voir [Aperçu de l’API d’interaction vocale](https://developers.google.com/voice-actions/interaction/). 

Voir [Ajouter une conversation à votre application Android avec des interactions vocales](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) pour plus de détails (y compris des exemples de code) sur la mise en œuvre des interactions vocales dans les applications Xamarin.Android.
Une application d’échantillon est disponible qui illustre comment utiliser l’API d’interaction vocale dans une application Xamarin.Android : [Interactions vocales](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Confirmer l’accréditation

En utilisant la nouvelle fonctionnalité *d’identification* de confirmation d’Android Marshmallow, vous pouvez libérer les utilisateurs d’avoir à se souvenir et entrer des mots de passe spécifiques à l’application en les authentifiant en fonction de combien de temps leur appareil a été déverrouillé.
Pour ce faire, vous `SetUserAuthenticationValidityDurationSeconds` utilisez `KeyGenerator`la nouvelle méthode de la . Utilisez `KeyGuardManager`la `CreateConfirmDeviceCredentialIntent` méthode 's pour réentesser l’utilisateur à partir de votre application. Pour plus d’informations sur cette nouvelle fonctionnalité dans Android Marshmallow, voir [Confirm Credential](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin fournit une application d’exemple qui illustre comment utiliser les informations d’identification de l’appareil (tels que le NIP, le modèle ou le mot de passe) dans votre application : [ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Pour utiliser cet exemple d’application :

1. Configurez un écran de verrouillage sécurisé sur votre appareil (**Secure > Security > Screenlock**).
2. Appuyez sur le bouton **Achat** et confirmez les informations d’identification sécurisées de l’écran de verrouillage.

### <a name="chrome-custom-tabs"></a>Onglets personnalisés Chrome

Les développeurs d’applications ont un choix lorsqu’un utilisateur tape sur une URL : `WebView`l’application peut soit lancer un navigateur, soit utiliser un navigateur intégré en fonction d’un . Les deux &ndash; options présentent des défis de lancement du navigateur est `WebView`un commutateur de contexte lourd qui n’est pas personnalisable, tandis que s ne partagent pas l’état avec le navigateur. En outre, `WebView`l’utilisation de s peut ajouter des frais généraux d’entretien supplémentaires. 

*Chrome Custom Tabs* vous permet d’afficher facilement et élégamment des sites Web avec la puissance de Chrome sans que vos utilisateurs quittent votre application. Cette fonctionnalité donne à votre application plus de contrôle sur l’expérience Web de l’utilisateur ; il rend les transitions entre le contenu natif `WebView`et web plus transparente sans avoir à recourir à un . Votre application peut également affecter l’apparence et les sensations de Chrome en personnalisant ce qui suit : 

- Couleur de barre d’outils

- Entrez et sortez des animations

- Actions personnalisées dans la barre d’outils Chrome et le menu de débordement

- Chrome pré-démarrage et contenu pré-fetch (pour une charge plus rapide)

Pour profiter de cette fonctionnalité dans votre application Xamarin.Android, téléchargez et installez la [bibliothèque Android Support Custom Tabs](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/)Library .
Pour plus d’informations sur cette fonctionnalité, voir [Chrome Custom Tabs](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Bibliothèque de soutien à la conception des matériaux

Android Lollipop a introduit [Material Design](https://www.google.com/design/spec/material-design/introduction.html) comme un nouveau langage de conception pour rafraîchir l’expérience Android (voir [Material Theme](~/android/user-interface/material-theme.md) pour des informations sur l’utilisation de la conception de matériel dans les applications Xamarin.Android). Avec Android Marshmallow, Google a présenté la *bibliothèque de soutien* à la conception Android pour faciliter pour les développeurs d’applications d’adopter le look et la sensation de conception de matériel. Cette bibliothèque comprend les éléments suivants :

- **CoordinatorLayout** &ndash; Le `CoordinatorLayout` nouveau widget est similaire `FrameLayout`mais plus puissant qu’un . Vous pouvez `CoordinatorLayout` utiliser comme un conteneur pour les vues d’enfant `layout_anchor` ou comme une mise en page de haut niveau, et il fournit un attribut qui peut être utilisé pour ancrer les vues par rapport à d’autres vues.

- **Effondrer Toolbars** &ndash; Le `CollapsingToolbarLayout` nouveau est une barre d’application `Toolbar`qui s’effondre qui est un emballage pour . (Notez que la *barre d’application* est ce qu’on appelait autrefois la *barre d’action*.)

- **Bouton d’action flottant** &ndash; Un bouton rond qui désigne l’action principale sur l’interface de votre application.

- **Les étiquettes flottantes pour l’édition de texte** &ndash; utilise un nouveau `TextInputLayout` widget (qui s’enroule `EditText`) pour afficher une étiquette flottante lorsqu’un indice est caché lorsqu’un utilisateur entre le texte.

- **Vue de** &ndash; `NavigationView` navigation Le nouveau widget vous aide à utiliser le tiroir de navigation d’une manière qui est plus facile pour les utilisateurs de naviguer.

- **Snackbar** &ndash; Le `SnackBar` nouveau widget est un mécanisme de rétroaction léger (semblable à un toast) qui affiche un bref message au bas de l’écran, apparaissant au-dessus de tous les autres éléments sur l’écran.

- **Onglets matériels** &ndash; Le nouveau `TabLayout` widget fournit une disposition horizontale pour afficher les onglets comme moyen d’implémenter la navigation de haut niveau dans votre application.

Pour profiter de la [bibliothèque de soutien au design](https://developer.android.com/tools/support-library/features.html#design) dans votre application Xamarin.Android, téléchargez et installez le forfait Xamarin [Xamarin Support Library Design](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) NuGet.

Voir [Beautiful Material Design avec l’Android Support Design Library](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) pour plus de détails (y compris des exemples de code) sur l’utilisation de la Material Design Support Library dans les applications Xamarin.Android.
Xamarin fournit un exemple d’application qui démos la &ndash; nouvelle bibliothèque Android Design sur Xamarin.Android [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare).
Cet échantillon démontre les caractéristiques suivantes de la bibliothèque de conception :

- Barre d’outils s’effondrer
- Bouton d’action flottant
- Afficher l’ancrage
- NavigationView
- Snackbar Snackbar

Pour plus d’informations sur la bibliothèque de conception, voir [Android Design Support Library](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) dans le blog du développeur Android.

### <a name="additional-library-updates"></a>Mises à jour supplémentaires de la bibliothèque

En plus d’Android Marshmallow, Google a annoncé des mises à jour connexes à plusieurs bibliothèques Android de base. Xamarin fournit le support Xamarin.Android pour ces mises à jour grâce à plusieurs paquets NuGet de prévisualisation : 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; La dernière version de Google Play Services inclut la nouvelle fonctionnalité *App Invites,* qui permet aux utilisateurs de partager leur application avec des amis. Pour plus d’informations sur cette fonctionnalité, voir [Expand Your App’s Reach with Google’s App Invites](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- [Bibliothèques](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; de support Android Ces NuGets offrent des fonctionnalités qui ne sont disponibles que pour les API de bibliothèque tout en fournissant des versions rétro-compatibles des API de cadre Android. 

- [Android Wearable Library](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; ce NuGet comprend des liaisons Google Play Services. La dernière version de la bibliothèque portable apporte de nouvelles fonctionnalités (y compris la navigation plus facile pour les applications personnalisées) à la plate-forme Android Wear. 

## <a name="summary"></a>Récapitulatif

Cet article a introduit Android Marshmallow et expliqué comment installer et configurer les derniers outils et paquets pour le développement Xamarin.Android sur Marshmallow. Il a également fourni un aperçu des nouvelles fonctionnalités Android Marshmallow les plus excitantes pour le développement Xamarin.Android.

## <a name="related-links"></a>Liens connexes

- [Android 6.0 Guimauve](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obtenez l’Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Aperçu des fonctionnalités](https://developer.android.com/preview/api-overview.html)
- [Notes de publication](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
