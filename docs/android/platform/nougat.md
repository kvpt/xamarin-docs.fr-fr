---
title: Fonctionnalités de nougat
description: Comment prendre en main Xamarin. Android pour développer des applications pour Android nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: a28368e0fa4574fbb92a43dbd650a127008f5d06
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643465"
---
# <a name="nougat-features"></a>Fonctionnalités de nougat

_Comment prendre en main Xamarin. Android pour développer des applications pour Android nougat._

Cet article fournit une présentation des fonctionnalités introduites dans Android nougat, explique comment préparer Xamarin. Android pour le développement Android nougat et fournit des liens vers des exemples d’applications qui illustrent comment utiliser les fonctionnalités d’Android nougat dans Applications Xamarin. Android.


## <a name="overview"></a>Vue d'ensemble

[Android nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) est le suivi de Google vers Android 6,0 Marshmallow. Xamarin. Android prend en charge les **liaisons Android 7. x** dans Xamarin Android 7,0 et versions ultérieures. Android nougat ajoute de nombreuses nouvelles API pour les fonctionnalités nougat décrites ci-dessous. Ces API sont disponibles pour les applications Xamarin. Android quand vous utilisez Xamarin. Android 7,0.

[![Images héros de tablettes et de téléphones Android exécutant Android nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Pour plus d’informations sur les API Android 7. x, consultez [android 7,1 pour les développeurs](https://developer.android.com/preview/api-overview.html).
Pour obtenir la liste des problèmes connus de Xamarin. Android 7,0, consultez les [notes de publication](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

Android nougat fournit de nombreuses nouvelles fonctionnalités intéressantes pour les développeurs Xamarin. Android. Ces fonctions incluent :

-   **Prise en charge de plusieurs fenêtres** &ndash; Cette amélioration permet aux utilisateurs d’ouvrir deux applications à la fois sur l’écran.

-   **Améliorations** des notifications Le système de notifications remanié dans Android nougat comprend une fonctionnalité de *réponse directe* qui permet aux utilisateurs de répondre rapidement aux messages texte directement à partir de l’interface utilisateur de notification. &ndash; En outre, si votre application crée des notifications pour les messages reçus, la nouvelle fonctionnalité de notifications regroupées peut regrouper les notifications sous la forme d’un groupe unique lorsque plusieurs messages sont reçus.

-   **Économiseur de données** &ndash; Cette fonctionnalité est un nouveau service système qui permet de réduire l’utilisation des données cellulaires par les applications. elle permet aux utilisateurs de contrôler le mode d’utilisation des données cellulaires par les applications.

En outre, Android nougat offre de nombreuses autres améliorations intéressantes pour les développeurs d’applications, telles qu’une nouvelle fonctionnalité de configuration de la sécurité réseau, Doze sur Go, l’attestation de clé, de nouvelles API de paramètres rapides, la prise en charge de plusieurs paramètres régionaux, les API ICU4J, les améliorations de WebView, accès aux fonctionnalités du langage Java 8, à l’accès à l’annuaire étendu, à une API pointeur personnalisée, à la prise en charge de la plateforme VR, aux fichiers virtuels et aux optimisations de traitement en arrière-plan.

Cet article explique comment prendre en main la création d’applications avec Android nougat pour essayer les nouvelles fonctionnalités et planifier la migration ou le travail de fonctionnalité pour cibler la nouvelle plateforme Android nougat.


## <a name="requirements"></a>Spécifications

Les éléments suivants sont requis pour utiliser les nouvelles fonctionnalités Android nougat dans les applications basées sur Xamarin:

-   **Visual Studio ou Visual Studio pour Mac** &ndash; Si vous utilisez Visual Studio, la version 4.2.0.628 ou ultérieure de Visual Studio Tools pour Xamarin est requise. Si vous utilisez Visual Studio pour Mac, la version 6.1.0 ou ultérieure de Visual Studio pour Mac est requise.

-   **Xamarin. Android** &ndash; Xamarin. Android 7,0 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac.

-   **Android SDK** -Android SDK 7,0 (API 24) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

-   **Kit de développement Java** Le développement Xamarin Android 7,0 requiert [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure si vous développez pour l’API de niveau 24 ou supérieur (JDK 8 prend également en charge les niveaux d’API antérieurs à 24). &ndash; La version 64 bits de JDK 8 est requise si vous utilisez des contrôles personnalisés ou le générateur d’aperçu de formulaires.

> [!IMPORTANT]
> Xamarin.Android ne prend pas en charge JDK 9.

Notez que les applications doivent être reconstruites avec Xamarin C6SR4 ou version ultérieure pour fonctionner de manière fiable avec Android nougat. Comme Android nougat peut être lié uniquement aux [bibliothèques natives fournies par NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), les applications existantes qui utilisent des bibliothèques telles que **mono. Data. sqlite. dll** peuvent se bloquer lors de leur exécution sur Android nougat s’ils ne sont pas correctement reconstruits.



## <a name="getting-started"></a>Prise en main

Pour commencer à utiliser Android nougat avec Xamarin. Android, vous devez télécharger et installer les derniers outils et packages du kit de développement logiciel (SDK) avant de pouvoir créer un projet Android nougat:

1.  Installez les dernières mises à jour Xamarin. Android à partir du Xamarin.

2.  Installez les packages et outils **Android 7,0 (API 24)** ou version ultérieure.

3.  Créez un nouveau projet Xamarin. Android qui cible Android nougat.

4.  Configurez un émulateur ou un appareil pour Android nougat.

Chacune de ces étapes est expliquée dans les sections suivantes:


### <a name="install-xamarin-updates"></a>Installer les mises à jour Xamarin

Pour ajouter la prise en charge Xamarin pour Android nougat, modifiez le canal des mises à jour dans Visual Studio ou Visual Studio pour Mac sur le canal stable et appliquez les dernières mises à jour. Si vous avez également besoin de fonctionnalités actuellement disponibles uniquement dans le canal alpha ou bêta, vous pouvez basculer vers le canal alpha ou bêta (les canaux alpha et bêta fournissent également la prise en charge d’Android 7. x). Pour plus d’informations sur la façon de modifier le canal des mises à jour (releases), consultez [modification du canal des mises à jour](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).



### <a name="install-the-android-sdk"></a>Installer le Android SDK

Pour créer un projet avec Xamarin Android 7,0, vous devez d’abord utiliser le gestionnaire de Android SDK pour installer le **Kit de développement logiciel (SDK) Android N (API 24)** ou version ultérieure. Vous devez également installer la dernière **Android SDK Tools**:

1.  Démarrez le gestionnaire de Android SDK (dans Visual Studio pour Mac, utilisez les **Outils > Ouvrir&hellip;Android SDK Manager**; dans Visual Studio, utilisez **Outils > Android > Android SDK gestionnaire**).

2.  Installez **Android 7,0 (API 24)** ou version ultérieure:

    [![Sélection de packages Android 7,0 dans le gestionnaire de Android SDK](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Installez les derniers outils de Android SDK:

    [![Sélection des derniers outils de Android SDK dans le gestionnaire de Android SDK](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Vous devez installer Android SDK Tools révision 25.2.2 ou ultérieure, Android SDK outils de plateforme 24.0.3 ou version ultérieure et Android SDK outils de génération 24.0.2 ou version ultérieure.

4.  Vérifiez que l' **emplacement du kit de développement Java** est configuré pour JDK 1,8:

    [![Configuration du chemin JDK 8 sous outils options](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Pour afficher ce paramètre dans Visual Studio, cliquez sur **outils > Options > Xamarin > paramètres Android**. Dans Visual Studio pour Mac, cliquez sur **préférences > projets > emplacements du kit de développement logiciel (SDK) > Android**.



### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin. Android

Créez un projet Xamarin. Android. Si vous débutez avec le développement Android avec Xamarin, consultez [Hello, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Xamarin. Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de version pour cibler Android 7,0 ou une version ultérieure. Par exemple, pour cibler votre projet pour Android 7,0, vous devez configurer le niveau d’API Android cible de votre projet sur **android 7,0 (API 24-nougat)** . Nous vous recommandons de définir le niveau de votre infrastructure cible sur API 24 ou version ultérieure. Pour plus d’informations sur la configuration des niveaux de niveau de l’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> Actuellement, vous devez définir la **version minimale d’Android** sur **Android 7,0 (API 24-nougat)** pour déployer votre application sur des appareils ou émulateurs Android nougat.



### <a name="configure-an-emulator-or-device"></a>Configurer un émulateur ou un appareil

Si vous utilisez un émulateur, démarrez Android AVD Manager et créez un appareil à l’aide des paramètres suivants:

-   Périphérique : La réversion 5X, le joueur 6, le 6H00 de la réversion, le joueur de la, la réversion 9 ou le C pixel.
-   Cible : Android 7,0-niveau d’API 24
-   Abi: x86 ou x86\_64

Par exemple, cet appareil virtuel est configuré pour émuler une passerelle 6:

[![Configuration d’une AVD à l’aide d’un appareil de passerelle 6, d’une cible Android 7,0 et d’un processeur/ABI Intel Atom x86](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Si vous utilisez un appareil physique tel qu’une liaison 5X, 6 ou 9, vous pouvez mettre à jour votre appareil par le biais de mises à jour OTA (Automatic on the air) ou télécharger une image système et flasher votre appareil directement. Pour plus d’informations sur la mise à jour manuelle de votre appareil vers Android nougat, consultez [images OTA pour les appareils de passerelle](https://developers.google.com/android/nexus/ota).

Notez que les appareils de la réversion 5 ne sont pas pris en charge par Android nougat.



## <a name="new-features"></a>Nouvelles fonctionnalités

Android nougat introduit une variété de nouvelles fonctionnalités et capacités, telles que la prise en charge de plusieurs fenêtres, les améliorations des notifications et l’économiseur de données. Les sections suivantes mettent en évidence ces fonctionnalités et fournissent des liens pour vous aider à commencer à les utiliser dans votre application.



### <a name="multi-window-mode"></a>Mode à plusieurs fenêtres

Le mode à plusieurs fenêtres permet aux utilisateurs d’ouvrir deux applications à la fois avec la prise en charge de l’intertâche totale. Ces applications peuvent s’exécuter côte à côte (paysage) ou un-au-dessus (portrait) en mode fractionné.
Les utilisateurs peuvent faire glisser un séparateur entre les applications pour les redimensionner et ils peuvent couper et coller le contenu entre les applications. Lorsque deux applications sont présentées en mode multi-fenêtre, l’activité sélectionnée continue de s’exécuter pendant que l’activité non sélectionnée est en pause, mais toujours visible. Le mode de plusieurs fenêtres ne modifie pas le cycle de vie des activités Android.

[![Exemples d’applications s’exécutant en mode à fenêtres multiples dans le portrait et le paysage](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Vous pouvez configurer la façon dont les activités de votre application Xamarin. Android prennent en charge le mode multi-fenêtre. Par exemple, vous pouvez configurer des attributs qui définissent la taille minimale et la hauteur et la largeur par défaut de votre application en mode multi-fenêtre. Vous pouvez utiliser la nouvelle `Activity.IsInMultiWindowMode` propriété pour déterminer si votre activité est en mode multi-fenêtre. Exemple :

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

L'exemple d’application [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) C# comprend du code qui montre comment tirer parti de plusieurs interfaces utilisateur Windows avec votre application.

Pour plus d’informations sur le mode à plusieurs fenêtres, consultez [prise en charge de plusieurs fenêtres](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notifications améliorées

Android nougat introduit un système de notification remanié. Il propose une nouvelle fonctionnalité de réponse directe qui permet aux utilisateurs de répondre rapidement aux notifications pour les messages texte entrants directement dans l’interface utilisateur de notification. À compter d’Android 7,0, les messages de notification peuvent être regroupés sous la forme d’un groupe unique lorsque plusieurs messages sont reçus. En outre, les développeurs peuvent personnaliser les vues de notification, tirer parti des décorations système dans les notifications et tirer parti des nouveaux modèles de notification lors de la génération de notifications.


#### <a name="direct-reply"></a>Réponse directe

Lorsqu’un utilisateur reçoit une notification pour un message entrant, Android nougat permet de répondre au message dans la notification (au lieu d’ouvrir l’application de messagerie pour envoyer une réponse).
Cette fonctionnalité de réponse en ligne permet aux utilisateurs de répondre rapidement à un SMS ou à un message texte directement dans l’interface de notification:

[![Capture d’écran d’une notification avec un champ de réponse directe en ligne](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Pour prendre en charge cette fonctionnalité dans votre application, vous devez ajouter des *actions de réponse en ligne* à votre application via un objet [RemoteInput](xref:Android.App.RemoteInput) afin que les utilisateurs puissent répondre directement par le biais du texte de l’interface utilisateur de notification.
Par exemple, le code suivant génère un `RemoteInput` pour la réception de l’entrée de texte, génère une intention d’action en attente pour l’action de réponse et crée une action d’entrée distante activée:

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

Cette action est ajoutée à la notification:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

L’exemple d’application de [service de messagerie](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) contient C# du code qui montre comment étendre `RemoteInput` des notifications avec un objet. Pour plus d’informations sur l’ajout d’actions de réponse en ligne à votre application pour Android 7,0 ou version ultérieure, consultez la rubrique [réponse aux notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) Android.


#### <a name="bundled-notifications"></a>Notifications groupées

Android nougat peut regrouper des messages de notification (par exemple, une rubrique de message) et afficher le groupe plutôt qu’un message distinct.
Grâce à cette fonctionnalité de notifications groupées, les utilisateurs peuvent ignorer ou archiver un groupe de notifications en une seule action. L’utilisateur peut faire défiler pour développer le groupe de notifications pour afficher chaque notification en détail:

[![Capture d’écran d’exemples de notifications groupées](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Pour prendre en charge les notifications groupées, votre application peut utiliser la méthode [Builder. SetGroup](xref:Android.App.Notification.Builder.SetGroup*) pour regrouper des notifications similaires. Pour plus d’informations sur les groupes de notification groupés dans Android N, consultez la rubrique notifications de [regroupement](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) Android.


#### <a name="custom-views"></a>Vues personnalisées

Android nougat vous permet de créer des vues de notification personnalisées avec des en-têtes de notification système, des actions et des dispositions pouvant être développées. Pour plus d’informations sur les vues de notification personnalisées dans Android nougat, consultez la rubrique [améliorations](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) des notifications Android.



### <a name="data-saver"></a>Économiseur de données

À compter d’Android nougat, les utilisateurs peuvent activer un nouveau paramètre d’économiseur de *données* qui bloque l’utilisation des données en arrière-plan. Ce paramètre indique également à votre application d’utiliser moins de données au premier plan dans la mesure du possible. Le [connectivitymanager](xref:Android.Net.ConnectivityManager) a été étendu dans Android nougat afin que votre application puisse vérifier si l’utilisateur a activé l’économiseur de données afin que votre application puisse faire un effort pour limiter son utilisation des données lorsque l’économiseur de données est activé.

Pour plus d’informations sur la nouvelle fonctionnalité de l’économiseur de données dans Android nougat, consultez la rubrique Optimisation de l' [utilisation des données réseau](https://developer.android.com/training/basics/network-ops/data-saver.html) pour Android.



### <a name="app-shortcuts"></a>Raccourcis d’application

Android 7,1 a introduit une fonctionnalité de *raccourcis d’application* qui permet aux utilisateurs de démarrer rapidement des tâches courantes ou recommandées avec votre application.
Pour activer le menu des raccourcis, l’utilisateur appuie longuement sur l’icône de l’application pour une deuxième &ndash; ou plusieurs fois, le menu s’affiche avec un mouvement vibratoire rapide.
Si vous relâchez l’appui, le menu est conservé:

[![Exemple d’écran du menu contextuel d’une application de messagerie](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Cette fonctionnalité est disponible uniquement au niveau de l’API 25 ou version ultérieure.
Pour plus d’informations sur la nouvelle fonctionnalité de raccourcis d’application dans Android 7,1, consultez la rubrique [raccourcis vers les applications](https://developer.android.com/guide/topics/ui/shortcuts.html) Android.


### <a name="sample-code"></a>Exemple de code

Plusieurs exemples Xamarin. Android sont disponibles pour vous montrer comment tirer parti des fonctionnalités Android nougat:

-   [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) illustre l’utilisation de l’API à plusieurs fenêtres disponible dans Android nougat. Vous pouvez basculer l’exemple d’application en mode multi-Windows pour voir comment il affecte le cycle de vie et le comportement de l’application.

-   Le [service de messagerie](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) est un service simple qui envoie des `NotificationCompatManager`notifications à l’aide du. Elle étend également la notification avec un `RemoteInput` objet pour permettre aux appareils nougat Android de répondre via le texte directement à partir de la notification sans avoir à ouvrir une application.

-   Les [notifications actives](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) montrent comment utiliser `NotificationManager` l’API pour vous indiquer le nombre de notifications affichées actuellement par votre application.

-   [Accès à l’annuaire étendu](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Montre comment utiliser l’API d’accès à l’annuaire étendu pour accéder facilement à des répertoires spécifiques. Cela constitue une alternative à la nécessité de définir `READ_EXTERNAL_STORAGE` des `WRITE_EXTERNAL_STORAGE` autorisations ou dans votre manifeste.

-   [Démarrage direct](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Montre comment stocker des données dans un stockage chiffré par appareil qui est toujours disponible pendant le démarrage de l’appareil, avant et après les informations d’identification de l’utilisateur (code confidentiel/modèle/mot de passe).


## <a name="summary"></a>Récapitulatif

Cet article a présenté Android nougat et a expliqué comment installer et configurer les outils et packages les plus récents pour le développement Xamarin. Android sur Android nougat. Il fournit également une vue d’ensemble des fonctionnalités clés disponibles dans Android nougat, avec des liens vers un exemple de code source pour vous aider à commencer à créer des applications pour Android nougat.


## <a name="related-links"></a>Liens associés

- [Android 7,1 pour les développeurs](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notes de publication d’Xamarin Android 7,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
