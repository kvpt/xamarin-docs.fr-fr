---
title: Caractéristiques de Nougat
description: Comment commencer à utiliser Xamarin.Android pour développer des applications pour Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 6274c75abf229268070d495ced662724f5c16627
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027089"
---
# <a name="nougat-features"></a>Caractéristiques de Nougat

_Comment commencer à utiliser Xamarin.Android pour développer des applications pour Android Nougat._

Cet article fournit un aperçu des fonctionnalités introduites dans Android Nougat, explique comment préparer Xamarin.Android pour le développement Android Nougat, et fournit des liens vers des applications d’échantillons qui illustrent comment utiliser les fonctionnalités Android Nougat dans les applications Xamarin.Android.

## <a name="overview"></a>Vue d’ensemble

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) est le suivi de Google à Android 6.0 Marshmallow. Xamarin.Android fournit un support pour **Android 7.x Liaisons** dans Xamarin Android 7.0 et plus tard. Android Nougat ajoute de nombreuses nouvelles API pour les fonctionnalités Nougat décrites ci-dessous; ces API sont disponibles pour les applications Xamarin.Android lorsque vous utilisez Xamarin.Android 7.0.

[![Images de héros de tablettes Android et téléphones fonctionnant sous Android Nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Pour plus d’informations sur Android 7.x API, voir [Android 7.1 pour les développeurs](https://developer.android.com/preview/api-overview.html).
Pour une liste des numéros connus Xamarin.Android 7.0, s’il vous plaît voir les [notes de sortie](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

Android Nougat fournit de nombreuses nouvelles fonctionnalités d’intérêt pour les développeurs Xamarin.Android. Voici quelques fonctionnalités :

- **Support** &ndash; multi-fenêtres Cette amélioration permet aux utilisateurs d’ouvrir deux applications sur l’écran à la fois.

- **Améliorations de notification** &ndash; Le système de notifications redessiné dans Android Nougat inclut une fonction *de réponse directe* qui permet aux utilisateurs de répondre rapidement aux messages texte directement à partir de l’interface utilisateur de notification. En outre, si votre application crée des notifications pour les messages reçus, la nouvelle fonctionnalité *de notifications groupées* peut regrouper les notifications en un seul groupe lorsque plus d’un message est reçu.

- **Économiseur de** &ndash; données Cette fonctionnalité est un nouveau service système qui aide à réduire l’utilisation de données cellulaires par les applications; il donne aux utilisateurs le contrôle sur la façon dont les applications utilisent les données cellulaires.

En outre, Android Nougat apporte de nombreuses autres améliorations d’intérêt pour les développeurs d’applications tels qu’une nouvelle fonctionnalité de configuration de sécurité réseau, Doze on the Go, l’attestation des clés, de nouvelles API Paramètres rapides, support multi-local, API4J, améliorations WebView, l’accès aux fonctionnalités linguistiques Java 8, l’accès à l’annuaire, un pointeur personnalisé, le support VR plate-forme, les fichiers virtuels et les optimisations de traitement de fond.

Cet article explique comment commencer à construire des applications avec Android Nougat pour essayer les nouvelles fonctionnalités et planifier la migration ou le travail de fonctionnalité pour cibler la nouvelle plate-forme Android Nougat.

## <a name="requirements"></a>Spécifications

Ce qui suit est nécessaire pour utiliser les nouvelles fonctionnalités Android Nougat dans les applications basées sur Xamarin:

- **Studio visuel ou studio visuel pour Mac** &ndash; Si vous utilisez Visual Studio, la version 4.2.0.628 ou plus tard de Visual Studio Tools for Xamarin est nécessaire. Si vous utilisez Visual Studio pour Mac, la version 6.1.0 ou plus tard de Visual Studio pour Mac est requise.

- **Xamarin.Android** &ndash; Xamarin.Android 7.0 ou plus tard doit être installé et configuré avec Visual Studio ou Visual Studio pour Mac.

- **Android SDK** - Android SDK 7.0 (API 24) ou plus tard doit être installé via le gestionnaire Android SDK.

- **Java Developer Kit** &ndash; Xamarin Android 7.0 développement nécessite [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou plus tard si vous développez pour le niveau API 24 ou plus (JDK 8 prend également en charge les niveaux D’API plus tôt que 24). La version 64 bits de JDK 8 est requise si vous utilisez des contrôles personnalisés ou l’aperçu des formulaires.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

Notez que les applications doivent être reconstruites avec Xamarin C6SR4 ou plus tard pour travailler de manière fiable avec Android Nougat. Parce qu’Android Nougat ne peut lier qu’aux [bibliothèques indigènes fournies par NDK, les](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)applications existantes utilisant des bibliothèques telles que **Mono.Data.Sqlite.dll peuvent s’écraser** lorsqu’elles sont en cours d’exécution sur Android Nougat si elles ne sont pas correctement reconstruites.

## <a name="getting-started"></a>Mise en route

Pour commencer à utiliser Android Nougat avec Xamarin.Android, vous devez télécharger et installer les derniers outils et forfaits SDK avant de pouvoir créer un projet Android Nougat :

1. Installez les dernières mises à jour Xamarin.Android du Xamarin.

2. Installez les paquets et outils **Android 7.0 (API 24)** ou plus tard.

3. Créez un nouveau projet Xamarin.Android qui cible Android Nougat.

4. Configurer un émulateur ou un appareil pour Android Nougat.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="install-xamarin-updates"></a>Installer les mises à jour Xamarin

Pour ajouter le support Xamarin pour Android Nougat, changez le canal de mises à jour de Visual Studio ou Visual Studio pour Mac sur le canal Stable et appliquez les dernières mises à jour. Si vous avez également besoin de fonctionnalités qui ne sont actuellement disponibles que dans le canal Alpha ou Beta, vous pouvez passer au canal Alpha ou Beta (les canaux Alpha et Beta fournissent également un support pour Android 7.x). Pour plus d’informations sur la façon de modifier le canal de mises à jour (releases), voir [Changer le canal de mise à jour](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).

### <a name="install-the-android-sdk"></a>Installer l’Android SDK

Pour créer un projet avec Xamarin Android 7.0, vous devez d’abord utiliser le gestionnaire Android SDK pour installer **SDK Platform Android N (API 24)** ou plus tard. Vous devez également installer les derniers **outils Android SDK**:

1. Démarrer l’Android SDK Manager (en studio visuel pour Mac, utiliser **Tools > Open Android SDK Manager&hellip;**; en Visual Studio, utiliser Tools > Android > Android **SDK Manager**).

2. Installer **Android 7.0 (API 24)** ou plus tard:

    [![Sélection des forfaits Android 7.0 dans le gestionnaire Android SDK](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. Installez les derniers outils Android SDK :

    [![Sélection des derniers outils Android SDK dans le android SDK Manager](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Vous devez installer Android SDK Tools révision 25.2.2 ou plus tard, Android SDK Platform outils 24.0.3 ou plus tard, et Android SDK Build outils 24.0.2 ou plus tard.

4. Vérifiez que l’emplacement du **kit de développement Java** est configuré pour JDK 1.8 :

    [![Configurer le chemin JDK 8 sous les options Tools](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Pour voir ce paramètre dans Visual Studio, cliquez sur **Outils > Options > Xamarin > Paramètres Android**. Dans Visual Studio for Mac, cliquez sur **Préférences > Projets > SDK Locations > Android**.

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin.Android

Créez un nouveau projet Xamarin.Android. Si vous êtes nouveau au développement Android avec Xamarin, voir [Bonjour, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Xamarin.Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de la version pour cibler Android 7.0 ou plus tard. Par exemple, pour cibler votre projet pour Android 7.0, vous devez configurer le niveau d’API Android cible de votre projet à **Android 7.0 (API 24 - Nougat)**. Il est recommandé de définir votre niveau de cadre cible à l’API 24 ou plus tard. Pour en savoir plus sur la configuration des niveaux de niveau d’API Android, voir [Comprendre les niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

> [!NOTE]
> Actuellement, vous devez définir la **version Android minimum** sur Android **7.0 (API 24 - Nougat)** pour déployer votre application sur des appareils ou des émulateurs Android Nougat.

### <a name="configure-an-emulator-or-device"></a>Configurer un émulateur ou un appareil

Si vous utilisez un émulateur, démarrez le gestionnaire AVD Android et créez un nouvel appareil en utilisant les paramètres suivants :

- Appareil: Nexus 5X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9, ou Pixel C.
- Cible: Android 7.0 - API Niveau 24
- ABI: x86 ou\_x86 64

Par exemple, cet appareil virtuel est configuré pour imiter un Nexus 6 :

[![Configurer un AVD à l’aide de l’appareil Nexus 6, de la cible Android 7.0 et d’Intel Atom x86 CPU/ABI](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Si vous utilisez un appareil physique tel qu’un Nexus 5X, 6 ou 9, vous pouvez soit mettre à jour votre appareil par des mises à jour automatiques sur l’air (OTA) ou télécharger une image du système et clignoter votre appareil directement. Pour plus d’informations sur la mise à jour manuelle de votre appareil sur Android Nougat, voir [OTA Images pour les périphériques Nexus](https://developers.google.com/android/nexus/ota).

Notez que les appareils Nexus 5 ne sont pas pris en charge par Android Nougat.

## <a name="new-features"></a>Nouvelles fonctionnalités

Android Nougat introduit une variété de nouvelles fonctionnalités et fonctionnalités, telles que le support multi-fenêtres, les améliorations notifications, et Data Saver. Les sections suivantes mettent en évidence ces fonctionnalités et fournissent des liens pour vous aider à commencer à les utiliser dans votre application.

### <a name="multi-window-mode"></a>Mode Multi-Fenêtres

Le mode multi-fenêtres permet aux utilisateurs d’ouvrir deux applications à la fois avec un support multitâche complet. Ces applications peuvent s’exécuter côte à côte (paysage) ou un-au-dessus de l’autre (portrait) en mode écran partagé.
Les utilisateurs peuvent faire glisser un diviseur entre les applications pour les resize, et ils peuvent couper et coller le contenu entre les applications. Lorsque deux applications sont présentées en mode multi-fenêtres, l’activité sélectionnée continue de s’exécuter pendant que l’activité non sélectionnée est interrompue mais toujours visible. Le mode multi-fenêtres ne modifie pas le cycle de vie de l’activité Android.

[![Exemple d’applications fonctionnant en mode multi-fenêtres dans le portrait et le paysage](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Vous pouvez configurer comment les activités de votre application Xamarin.Android prennent en charge le mode multi-fenêtres. Par exemple, vous pouvez configurer des attributs qui fixent la taille minimale et la hauteur et la largeur par défaut de votre application en mode multi-fenêtres. Vous pouvez utiliser `Activity.IsInMultiWindowMode` la nouvelle propriété pour déterminer si votre activité est en mode multi-fenêtres. Par exemple :

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

[L’application d’échantillons MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) comprend le code CMD qui montre comment tirer parti de plusieurs interfaces utilisateur de fenêtre avec votre application.

Pour plus d’informations sur le mode multi-fenêtres, voir le [support Multi-Window](https://developer.android.com/guide/topics/ui/multi-window.html).

### <a name="enhanced-notifications"></a>Notifications améliorées

Android Nougat introduit un système de notification redessiné. Il dispose d’une nouvelle fonctionnalité de réponse directe qui permet aux utilisateurs de répondre rapidement aux notifications pour les messages texte entrants directement dans l’interface utilisateur de notification. En commençant par Android 7.0, les messages de notification peuvent être regroupés en un seul groupe lorsque plus d’un message est reçu. En outre, les développeurs peuvent personnaliser les vues de notification, tirer parti des décorations du système dans les notifications, et profiter de nouveaux modèles de notification lors de la génération de notifications.

#### <a name="direct-reply"></a>Réponse directe

Lorsqu’un utilisateur reçoit une notification pour un message entrant, Android Nougat permet de répondre au message dans la notification (plutôt que d’ouvrir l’application de messagerie pour envoyer une réponse).
Cette fonction de réponse en ligne permet aux utilisateurs de répondre rapidement à un SMS ou à un message texte directement dans l’interface de notification :

[![Capture d’écran d’une notification avec un champ de réponse directe inline](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Pour prendre en charge cette fonctionnalité dans votre application, vous devez ajouter des *actions de réponse en ligne* à votre application via un objet [RemoteInput](xref:Android.App.RemoteInput) afin que les utilisateurs puissent répondre par SMS directement à partir de l’interface utilisateur de notification.
Par exemple, le code `RemoteInput` suivant crée un pour recevoir l’entrée de texte, construit une intention en attente pour l’action de réponse, et crée une action activée par entrée à distance :

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

Cette action est ajoutée à la notification :

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

L’application d’échantillon [de service de messagerie](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) inclut le `RemoteInput` code CMD qui démontre comment étendre les notifications avec un objet. Pour plus d’informations sur l’ajout d’actions de réponse en ligne à votre application pour Android 7.0 ou plus tard, consultez le sujet De [réponse Android aux notifications.](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)

#### <a name="bundled-notifications"></a>Notifications groupées

Android Nougat peut regrouper les messages de notification (par exemple, par sujet de message) et afficher le groupe plutôt que chaque message séparé.
Cette fonction *de notifications groupées* permet aux utilisateurs de rejeter ou d’archiver un groupe de notifications en une seule action. L’utilisateur peut glisser vers le bas pour élargir le faisceau de notifications pour afficher chaque notification en détail :

[![Exemple de capture d’écran de notifications groupées](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Pour prendre en charge les notifications groupées, votre application peut utiliser la méthode [Builder.SetGroup](xref:Android.App.Notification.Builder.SetGroup*) pour regrouper des notifications similaires. Pour plus d’informations sur les groupes de notification groupés dans Android N, consultez le sujet [Des notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) De regroupement Android.

#### <a name="custom-views"></a>Vues personnalisées

Android Nougat vous permet de créer des vues de notification personnalisées avec des en-têtes de notification système, des actions et des mises en page extensibles. Pour plus d’informations sur les vues de notification personnalisée dans Android Nougat, consultez le sujet [Android Notification Enhancements.](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements)

### <a name="data-saver"></a>Saver de données

En commençant par Android Nougat, les utilisateurs peuvent activer un nouveau paramètre *Data Saver* qui bloque l’utilisation des données de fond. Ce paramètre signale également à votre application d’utiliser moins de données au premier plan dans la mesure du possible. Le [ConnectivityManager](xref:Android.Net.ConnectivityManager) a été étendu dans Android Nougat afin que votre application puisse vérifier si l’utilisateur a activé Data Saver afin que votre application puisse faire un effort pour limiter son utilisation de données lorsque Data Saver est activé.

Pour plus d’informations sur la nouvelle fonctionnalité Data Saver dans Android Nougat, consultez le sujet Android [Optimizing Network Data Use.](https://developer.android.com/training/basics/network-ops/data-saver.html)

### <a name="app-shortcuts"></a>App Shortcuts

Android 7.1 a introduit une fonctionnalité *App Shortcuts* qui permet aux utilisateurs de commencer rapidement des tâches courantes ou recommandées avec votre application.
Pour activer le menu des raccourcis, l’utilisateur presse longuement &ndash; l’icône de l’application pendant une seconde ou plus le menu apparaît avec une vibration rapide.
La diffusion de la presse fait rester le menu :

[![Exemple d’écran d’un menu raccourci d’application pour une application de messagerie](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Cette fonctionnalité n’est disponible que niveau API 25 ou plus.
Pour plus d’informations sur la nouvelle fonctionnalité App Shortcuts dans Android 7.1, consultez le sujet Android [App Shortcuts.](https://developer.android.com/guide/topics/ui/shortcuts.html)

### <a name="sample-code"></a>Exemple de code

Plusieurs échantillons Xamarin.Android sont disponibles pour vous montrer comment profiter des fonctionnalités Android Nougat:

- [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) démontre l’utilisation de l’API multi-fenêtres disponible dans Android Nougat. Vous pouvez passer l’application d’échantillon en mode multi-fenêtres pour voir comment elle affecte le cycle de vie et le comportement de l’application.

- [Service de messagerie](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) est un service `NotificationCompatManager`simple qui envoie des notifications en utilisant le . Il étend également la `RemoteInput` notification avec un objet pour permettre aux appareils Android Nougat de répondre par SMS directement à partir de la notification sans avoir à ouvrir une application.

- [Les notifications actives](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) montrent `NotificationManager` comment utiliser l’API pour vous indiquer le nombre de notifications que votre application affiche actuellement.

- [Accès à l’annuaire](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Démontre comment utiliser l’API d’accès à l’annuaire pour accéder facilement à des répertoires spécifiques. Cela sert d’alternative à `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` la définition ou aux autorisations dans votre manifeste.

- [Démarrage direct](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Illustre comment stocker des données dans un stockage crypté qui est toujours disponible pendant que l’appareil est mis en œd de pied avant et après que les informations d’identification de l’utilisateur (PIN/Pattern/Password) soient saisies.

## <a name="summary"></a>Récapitulatif

Cet article a introduit Android Nougat et expliqué comment installer et configurer les derniers outils et paquets pour le développement Xamarin.Android sur Android Nougat. Il a également fourni un aperçu des principales fonctionnalités disponibles dans Android Nougat, avec des liens vers l’exemple de code source pour vous aider à démarrer dans la création d’applications pour Android Nougat.

## <a name="related-links"></a>Liens connexes

- [Android 7.1 Pour les développeurs](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Xamarin Android 7.0 Notes de sortie](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
