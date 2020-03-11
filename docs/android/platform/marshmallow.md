---
title: Fonctionnalités de Marshmallow
description: Cet article vous aide à utiliser Xamarin. Android pour développer des applications pour Android 6,0 Marshmallow.
ms.prod: xamarin
ms.assetid: E4D6F183-98D2-460A-9D65-937639A899E0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: fb1ba92be9527d490b3d34bd4c0e454b0a750837
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019992"
---
# <a name="marshmallow-features"></a>Fonctionnalités de Marshmallow

_Cet article vous aide à utiliser Xamarin. Android pour développer des applications pour Android 6,0 Marshmallow._

Cet article fournit une présentation des nouvelles fonctionnalités d’Android 6,0 Marshmallow, explique comment préparer Xamarin. Android pour le développement Android Marshmallow et fournit des liens vers des exemples d’applications qui illustrent comment utiliser les nouveaux Marshmallow Android fonctionnalités des applications Xamarin. Android. 

## <a name="overview"></a>Overview

[Android 6,0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html), est la prochaine version d’Android suivante après Android Lollipop.
Xamarin. Android prend en charge Android Marshmallow et comprend les éléments suivants :

- **API 23/Android 6,0 liaisons** &ndash; Android 6,0 ajoute de nombreuses nouvelles API pour les nouvelles fonctionnalités décrites ci-dessous. Ces API sont disponibles pour les applications Xamarin. Android quand vous ciblez le niveau d’API 23. Pour plus d’informations sur les API Android 6,0, consultez [API android 6,0](https://developer.android.com/preview/api-overview.html). 

[![des images de héros de tablettes et de téléphones exécutant Marshmallow](marshmallow-images/android-m-hero-sml.png)](marshmallow-images/android-m-hero.png#lightbox)

Bien que la version Marshmallow soit principalement axée sur le « polonais et la qualité », elle offre également de nombreuses nouvelles fonctionnalités intéressantes pour les développeurs Xamarin. Android. Voici quelques fonctionnalités : 

- Les **autorisations d’exécution** &ndash; cette amélioration permettent aux utilisateurs d’approuver les autorisations de sécurité au cas par cas au moment de l’exécution. 

- Les **améliorations de l’authentification** &ndash; à partir d’Android Marshmallow, les applications peuvent désormais utiliser des capteurs d’empreintes digitales pour authentifier les utilisateurs, et une nouvelle fonctionnalité *confirmer les informations d’identification* réduit la nécessité d’entrer des mots de passe. 

- La **liaison d’applications** &ndash; cette fonctionnalité permet d’éliminer la nécessité de faire apparaître le sélecteur d' **application** en associant automatiquement les applications aux domaines Web. 

- &ndash; de partage **direct** vous pouvez définir des *cibles de partage direct* qui rendent le partage rapide et intuitif pour les utilisateurs. Cette fonctionnalité permet à utiliseront de partager du contenu avec d’autres applications. 

- Les **interactions vocales** &ndash; cette nouvelle API vous permet de créer des fonctionnalités vocales de conversation dans votre application. 

- le **mode d’affichage 4k** &ndash; dans Android Marshmallow, votre application peut demander une résolution d’affichage 4k sur le matériel qui la prend en charge. 

- Les **nouvelles fonctionnalités Audio** &ndash; à partir de Marshmallow, Android prend désormais en charge le protocole midi. Il fournit également de nouvelles classes pour créer des objets de capture et de lecture audio numériques, et propose de nouveaux hooks d’API pour associer les périphériques audio et d’entrée. 

- Les **nouvelles fonctionnalités vidéo** &ndash; Marshmallow fournissent une nouvelle classe qui aide les applications à effectuer le rendu des flux audio et vidéo synchronisés. Cette classe fournit également la prise en charge de la vitesse de lecture dynamique. 

- **Android for Work** &ndash; Marshmallow inclut des contrôles améliorés pour les appareils d’entreprise à utilisateur unique. Il prend en charge l’installation et la désinstallation silencieuses des applications par le propriétaire de l’appareil, l’acceptation automatique des mises à jour du système, la gestion améliorée des certificats, le suivi de l’utilisation des données, la gestion des autorisations et les notifications d’État du travail. 

- La **bibliothèque de prise en charge** de la conception de matériaux &ndash; la nouvelle bibliothèque de *prise en charge de conception* fournit des composants et des modèles de conception qui facilitent la création d’apparences de conception matérielle dans votre application. 

En outre, de nombreuses mises à jour de la bibliothèque Android de base ont été publiées avec Android M, et ces mises à jour fournissent de nouvelles fonctionnalités pour Android M et les versions antérieures d’Android.

En outre, de nombreuses mises à jour de la bibliothèque Android de base ont été publiées avec Android Marshmallow. ces mises à jour fournissent de nouvelles fonctionnalités pour Android Marshmallow et les versions antérieures d’Android. Cet article explique comment prendre en main la création d’applications avec Android Marshmallow et fournit une vue d’ensemble des nouvelles fonctionnalités de Android 6,0. 

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour utiliser les nouvelles fonctionnalités Android Marshmallow dans les applications basées sur Xamarin : 

- **Xamarin. android** &ndash; Xamarin. Android 5.1.7.12 ou version ultérieure doit être installé et configuré avec Visual Studio ou Xamarin Studio.

- **Visual Studio pour Mac** ou **Visual Studio** &ndash; si vous utilisez Visual Studio pour Mac, la version 5.9.7.22 ou ultérieure est requise. Si vous utilisez Visual Studio, vous devez disposer de la version 3.11.1537 ou d’une version ultérieure des outils Xamarin pour Visual Studio. 

- **Android SDK** &ndash; Android SDK 6,0 (API 23) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

- **Kit de développement Java** &ndash; Xamarin. Android nécessite [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure si vous développez pour l’API de niveau 24 ou supérieur (JDK 1,8 prend également en charge les niveaux d’API antérieurs à 24, y compris Marshmallow). La version 64 bits de JDK 1,8 est requise si vous utilisez des contrôles personnalisés ou le générateur d’aperçu de formulaires.

Vous pouvez continuer à utiliser [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) si vous développez spécifiquement pour l’API de niveau 23 ou antérieur. 

## <a name="getting-started"></a>Mise en route

Pour commencer à utiliser Android Marshmallow avec Xamarin. Android, vous devez télécharger et installer les derniers outils et packages du kit de développement logiciel (SDK) avant de pouvoir créer un projet Android Marshmallow : 

1. Installez les dernières mises à jour Xamarin à partir du canal **stable** . 

2. Installez les outils et packages du kit de développement logiciel (SDK) Android 6,0 Marshmallow.

3. Créez un nouveau projet Xamarin. Android qui cible Android 6,0 Marshmallow (niveau d’API 23). 

4. Configurez un émulateur ou un appareil pour Android Marshmallow.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="install-xamarin-updates"></a>Installer les mises à jour Xamarin

Pour mettre à jour Xamarin afin qu’il comprenne la prise en charge d’Android 6,0 Marshmallow, modifiez le canal de mise à jour en **stable** et installez toutes les mises à jour. Pour plus d’informations sur l’installation des mises à jour à partir du canal de mise à jour, consultez [modifier le canal des mises à jour](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel). 

### <a name="install-the-android-60-sdk"></a>Installer le kit de développement logiciel (SDK) Android 6,0

Pour créer un projet Xamarin. Android pour Android Marshmallow, vous devez d’abord utiliser le gestionnaire de Android SDK pour installer le kit de développement logiciel (SDK) Android 6,0 :

- Démarrez le gestionnaire de Android SDK (dans Visual Studio pour Mac, utilisez **outils > gestionnaire du kit de développement logiciel (SDK**); dans Visual Studio, utilisez les **outils > Android > Android SDK Manager**) et installez les Android SDK Tools les plus récents :

    [![sélectionnant Android SDK outils dans le gestionnaire de Android SDK](marshmallow-images/mnc-preview-tools.png)](marshmallow-images/mnc-preview-tools.png#lightbox)

- Installez également les derniers packages du kit de développement logiciel (SDK) **Android 6,0** :

    [![sélection de packages du kit de développement logiciel (SDK) Android 6,0 dans le gestionnaire de Android SDK](marshmallow-images/mnc-preview-packages.png)](marshmallow-images/mnc-preview-packages.png#lightbox)

Vous devez installer Android SDK Tools révision 24.3.4 ou version ultérieure.
Pour plus d’informations sur l’utilisation du gestionnaire de Android SDK pour installer le kit de développement logiciel (SDK) Android 6,0, consultez [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin. Android

Créez un projet Xamarin. Android. Si vous débutez avec le développement Android avec Xamarin, consultez [Hello, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Android. 

Lorsque vous créez un projet Android, vous devez configurer les paramètres de version pour cibler Android 6,0 MarshMallow. Pour cibler votre projet pour Marshmallow, vous devez configurer votre projet pour l' **API de niveau 23 (prise en charge de Xamarin. Android v 6.0)** . Pour plus d’informations sur la configuration des niveaux de niveau de l’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurer un émulateur ou un appareil

Si vous utilisez un émulateur, démarrez Android AVD Manager et créez un appareil à l’aide des paramètres suivants :

- Appareil : la passerelle 5, 6 ou 9.
- Cible : Android 6,0-API de niveau 23
- ABI : x86

Par exemple, cet appareil virtuel est configuré pour émuler une passerelle 5 :

[![configuration d’une AVD à l’aide d’un appareil de passerelle 5, d’une cible Android 6,0 et d’Intel Atom (x86)](marshmallow-images/android-m-avd.png)](marshmallow-images/android-m-avd.png#lightbox)

Si vous utilisez un appareil physique tel qu’un type de passerelle 5, 6 ou 9, vous pouvez installer une image d’aperçu d’Android Marshmallow. Pour plus d’informations sur la mise à jour de votre appareil vers Android Marshmallow, consultez [images système matérielles](https://developer.android.com/preview/download.html#images).

## <a name="new-features"></a>Nouvelles fonctionnalités

La plupart des modifications introduites dans Android Marshmallow sont axées sur l’amélioration de l’expérience utilisateur Android, l’augmentation des performances et la résolution des bogues. Toutefois, Marshmallow a également introduit de nombreuses modifications apportées aux notions de base de la plateforme Android. Les sections suivantes mettent en évidence ces améliorations et fournissent des liens pour vous aider à commencer à utiliser les nouvelles fonctionnalités Android Marshmallow dans votre application. 

### <a name="runtime-permissions"></a>Autorisations d’exécution

Le système d’autorisations Android a été considérablement optimisé et simplifié depuis Android Lollipop. Dans Android Marshmallow, les utilisateurs accordent des autorisations au cas par cas au moment de l’exécution plutôt qu’au moment de l’installation. Pour prendre en charge cette fonctionnalité sur Android Marshmallow et versions ultérieures, vous concevez votre application pour inviter l’utilisateur à entrer des autorisations au moment de l’exécution (dans le contexte où les autorisations sont nécessaires). Grâce à cette modification, il est plus facile pour les utilisateurs de commencer à utiliser votre application immédiatement, car elle simplifie le processus d’installation et de mise à niveau de votre application. 

Pour plus d’informations sur l’implémentation des autorisations d’exécution dans les applications Xamarin. Android, consultez [demande d’autorisations d’exécution dans Android Marshmallow](https://blog.xamarin.com/requesting-runtime-permissions-in-android-marshmallow/) .
Xamarin fournit également un exemple d’application qui illustre le fonctionnement des autorisations d’exécution dans Android Marshmallow (et versions ultérieures) : [RuntimePermissions](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions).

Cet exemple d’application illustre les éléments suivants :

- Comment vérifier et demander des autorisations au moment de l’exécution.
- Comment déclarer des autorisations pour les appareils Android M.

Pour utiliser cet exemple d’application :

1. Appuyez sur les boutons **caméra** ou **contacts** pour afficher une boîte de dialogue de demande d’autorisations.
2. Accordez l’autorisation d’afficher les fragments de l’appareil photo ou des contacts.

Pour plus d’informations sur les nouvelles fonctionnalités d’autorisations du runtime dans Android Marshmallow, consultez [utilisation des autorisations système](https://developer.android.com/preview/features/runtime-permissions.html).

### <a name="authentication-enhancements"></a>Améliorations de l’authentification

Android Marshmallow comprend deux améliorations d’authentification qui permettent d’éliminer le besoin de mots de passe :

- **L’authentification par empreinte digitale** &ndash; utilise une analyse par empreinte digitale pour authentifier les utilisateurs.

- **Confirmer les informations d’identification** &ndash; authentifie les utilisateurs en fonction de la durée de déverrouillage de l’appareil.

Les liens et les exemples d’applications décrits ci-après peuvent vous aider à vous familiariser avec ces nouvelles fonctionnalités.

#### <a name="fingerprint-authentication"></a>Authentification par empreinte digitale

Sur les appareils qui prennent en charge le matériel de numérisation d’empreintes digitales, vous pouvez utiliser la nouvelle classe `FingerPrintManager` pour authentifier un utilisateur.
Pour plus d’informations sur la fonctionnalité d’authentification par empreinte digitale dans Android Marshmallow, consultez [authentification par empreinte digitale](https://developer.android.com/preview/api-overview.html#fingerprint-authentication).

Xamarin fournit un exemple d’application qui illustre l’utilisation d’empreintes digitales inscrites pour authentifier un utilisateur dans votre application : [FingerprintDialog](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog).

Pour utiliser cet exemple d’application :

1. Appuyez sur le bouton **acheter** pour ouvrir une boîte de dialogue d’authentification par empreinte digitale.
2. Analysez dans votre empreinte digitale inscrite pour vous authentifier.

Notez que cet exemple d’application nécessite un appareil avec un lecteur d’empreintes digitales.
Cette application ne stocke pas votre empreinte digitale (ou votre mot de passe).

#### <a name="voice-interactions"></a>Interactions vocales

La nouvelle fonctionnalité interactions vocales introduite dans Android Marshmallow permet aux utilisateurs de votre application d’utiliser leur voix pour confirmer des actions et d’effectuer une sélection dans une liste d’options. Pour plus d’informations sur les interactions vocales, consultez [vue d’ensemble de l’API d’interaction vocale](https://developers.google.com/voice-actions/interaction/). 

Pour plus d’informations sur l’implémentation des interactions vocales dans les applications Xamarin. Android, consultez [Ajouter une conversation à votre application Android avec des interactions vocales](https://blog.xamarin.com/add-a-conversation-to-your-android-app-with-voice-interactions/) (y compris des exemples de code).
Un exemple d’application est disponible qui illustre l’utilisation de l’API d’interaction vocale dans une application Xamarin. Android : [interactions vocales](https://github.com/jamesmontemagno/MarshmallowSamples/tree/master/VoiceInteractions).

#### <a name="confirm-credential"></a>Confirmer les informations d’identification

À l’aide de la nouvelle fonctionnalité *confirmer les informations d’identification* d’Android Marshmallow, vous pouvez éviter aux utilisateurs de devoir mémoriser et entrer des mots de passe spécifiques à l’application en les authentifiant en fonction de la durée de leur déverrouillage.
Pour ce faire, vous utilisez la nouvelle méthode `SetUserAuthenticationValidityDurationSeconds` de la `KeyGenerator`. Utilisez la méthode `CreateConfirmDeviceCredentialIntent` de l' `KeyGuardManager`pour authentifier à nouveau l’utilisateur à partir de votre application. Pour plus d’informations sur cette nouvelle fonctionnalité dans Android Marshmallow, consultez [confirmer les informations d’identification](https://developer.android.com/preview/api-overview.html#confirm-credential).

Xamarin fournit un exemple d’application qui illustre l’utilisation des informations d’identification de l’appareil (par exemple, le code confidentiel, le modèle ou le mot de passe) dans votre application : [ConfirmCredential](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)

Pour utiliser cet exemple d’application :

1. Configurer un écran de verrouillage sécurisé sur votre appareil (**secure > Security > Screenlock**).
2. Appuyez sur le bouton **acheter** et confirmez les informations d’identification de l’écran de verrouillage sécurisé.

### <a name="chrome-custom-tabs"></a>Onglets personnalisés chrome

Les développeurs d’applications sont en mesure de choisir lorsqu’un utilisateur appuie sur une URL : l’application peut lancer un navigateur ou utiliser un navigateur dans l’application en fonction d’un `WebView`. Les deux options présentent des défis &ndash; le lancement du navigateur est un changement de contexte important qui n’est pas personnalisable, tandis que `WebView`s ne partagent pas d’État avec le navigateur. En outre, l’utilisation de `WebView`peut ajouter une surcharge de maintenance supplémentaire. 

Les *onglets personnalisés chrome* vous permettent d’afficher facilement et de façon élégante des sites Web avec la puissance du chrome sans que vos utilisateurs quittent votre application. Cette fonctionnalité permet à votre application de mieux contrôler l’expérience Web de l’utilisateur. Il rend les transitions entre le contenu natif et le contenu Web plus fluides sans avoir à recourir à un `WebView`. Votre application peut également avoir une incidence sur l’apparence et le fonctionnement de chrome en personnalisant les éléments suivants : 

- Couleur de la barre d’outils

- Animations Enter et Exit

- Actions personnalisées dans la barre d’outils chrome et le menu de dépassement de capacité

- Pre-Start chrome et prérécupération de contenu (pour un chargement plus rapide)

Pour tirer parti de cette fonctionnalité dans votre application Xamarin. Android, téléchargez et installez la [bibliothèque d’onglets personnalisations de la prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.CustomTabs/).
Pour plus d’informations sur cette fonctionnalité, consultez les [onglets personnalisés chrome](https://developer.chrome.com/multidevice/android/customtabs).

### <a name="material-design-support-library"></a>Bibliothèque de support pour la conception de matériaux

Android Lollipop a introduit la [conception matérielle](https://www.google.com/design/spec/material-design/introduction.html) comme un nouveau langage de conception pour actualiser l’expérience Android (pour plus d’informations sur l’utilisation de la conception de matériaux dans les applications Xamarin. Android), consultez le [thème matériel](~/android/user-interface/material-theme.md) . Avec Android Marshmallow, Google a introduit la *bibliothèque de support de conception Android* pour permettre aux développeurs d’applications d’adopter plus facilement l’apparence et la convivialité de la conception matérielle. Cette bibliothèque comprend les composants suivants :

- **CoordinatorLayout** &ndash; le nouveau widget `CoordinatorLayout` est semblable à, mais plus puissant qu’un `FrameLayout`. Vous pouvez utiliser `CoordinatorLayout` comme conteneur pour les vues enfants ou comme une disposition de niveau supérieur, et fournit un attribut `layout_anchor` qui peut être utilisé pour ancrer des vues par rapport à d’autres vues.

- La **réduction des barres d’outils** &ndash; la nouvelle `CollapsingToolbarLayout` est une barre d’application réduite qui est un wrapper pour `Toolbar`. (Notez que la *barre* de l’application est celle qui était précédemment appelée *barre d’action*.)

- **Bouton d’action flottant** &ndash; un bouton rond qui désigne l’action principale sur l’interface de votre application.

- **Les étiquettes flottantes pour la modification de texte** &ndash; utilisent un nouveau widget de `TextInputLayout` (qui encapsule `EditText`) pour afficher une étiquette flottante lorsqu’un indicateur est masqué quand un utilisateur entre du texte.

- Le **mode navigation** &ndash; le nouveau widget `NavigationView` vous aide à utiliser le tiroir de navigation d’une manière plus facile pour les utilisateurs de naviguer.

- **Snackbar** &ndash; le nouveau widget `SnackBar` est un mécanisme de commentaires léger (semblable à un toast) qui affiche un bref message en bas de l’écran, apparaissant au-dessus de tous les autres éléments à l’écran.

- Les **onglets matériau** &ndash; le nouveau widget `TabLayout` fournissent une disposition horizontale pour l’affichage des onglets en tant que moyen d’implémenter la navigation de niveau supérieur dans votre application.

Pour tirer parti de la [bibliothèque de support de conception](https://developer.android.com/tools/support-library/features.html#design) dans votre application Xamarin. Android, téléchargez et installez le package NuGet de conception de la [bibliothèque de prise en charge Xamarin Xamarin](https://www.nuget.org/packages/Xamarin.Android.Support.Design/) .

Pour plus d’informations (y compris des exemples de code) sur l’utilisation de la bibliothèque de prise en charge de la conception de matériaux dans les applications Xamarin. Android, consultez [magnifique design Design Library (en](https://blog.xamarin.com/add-beautiful-material-design-with-the-android-support-design-library/) anglais).
Xamarin fournit un exemple d’application qui présente la nouvelle bibliothèque de conception Android sur Xamarin. Android &ndash; [Cheesesquare](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-cheesesquare).
Cet exemple illustre les fonctionnalités suivantes de la bibliothèque de conception :

- Masquage de la barre d’outils
- Bouton d’action flottante
- Afficher l’ancrage
- NavigationView
- Snackbar

Pour plus d’informations sur la bibliothèque de conception, consultez la [bibliothèque de prise en charge de la conception Android](https://android-developers.googleblog.com/2015/05/android-design-support-library.html) dans le blog du développeur Android.

### <a name="additional-library-updates"></a>Mises à jour de bibliothèque supplémentaires

En plus des marshmallow Android, Google a annoncé des mises à jour associées à plusieurs bibliothèques Android de base. Xamarin fournit la prise en charge de Xamarin. Android pour ces mises à jour par le biais de plusieurs packages NuGet version préliminaire : 

- [Google Play Services](https://www.nuget.org/packages?q=Xamarin+Google+Play+Services) &ndash; la dernière version de Google Play services comprend la nouvelle fonctionnalité d' *invitations d’application* , qui permet aux utilisateurs de partager leur application avec des amis. Pour plus d’informations sur cette fonctionnalité, consultez [étendre la portée de votre application avec les invitations de l’application Google](https://blog.xamarin.com/expand-your-apps-reach-with-googles-app-invites/). 

- Les [bibliothèques de prise en charge Android](https://www.nuget.org/packages?q=xamarin+support+library) &ndash; ces fonctionnalités d’offre packages NuGet qui sont disponibles uniquement pour les API de bibliothèque, tout en fournissant des versions à compatibilité descendante des API d’infrastructure Android. 

- [Bibliothèque Android portable](https://www.nuget.org/packages/Xamarin.Android.Wear) &ndash; ce NuGet comprend des liaisons Google Play services. La dernière version de la bibliothèque portable offre de nouvelles fonctionnalités (y compris une navigation plus facile pour les applications personnalisées) à la plateforme d’usure Android. 

## <a name="summary"></a>Résumé

Cet article a présenté Android Marshmallow et a expliqué comment installer et configurer les outils et packages les plus récents pour le développement Xamarin. Android sur Marshmallow. Il offre également une vue d’ensemble des nouvelles fonctionnalités Android Marshmallow pour le développement Xamarin. Android.

## <a name="related-links"></a>Liens connexes

- [Android 6,0 Marshmallow](https://developer.android.com/about/versions/marshmallow/index.html)
- [Obtient le Android SDK](https://developer.android.com/sdk/index.html#Other)
- [Vue d'ensemble des fonctionnalités](https://developer.android.com/preview/api-overview.html)
- [Notes de publication](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1.99/index.md)
- [RuntimePermissions (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-runtimepermissions)
- [ConfirmCredential (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-confirmcredential)
- [FingerprintDialog (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
