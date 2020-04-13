---
title: Android 9 Pie
description: Comment commencer à développer des applications pour Android 9 Pie en utilisant Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019886"
---
# <a name="android-pie-features"></a>Fonctions Android Pie

_Comment commencer à développer des applications pour Android 9 Pie en utilisant Xamarin.Android._

[Android 9 Pie](https://developer.android.com/about/versions/pie/) est maintenant disponible sur Google. Un certain nombre de nouvelles fonctionnalités et API sont disponibles dans cette version, et beaucoup d’entre eux sont nécessaires pour tirer parti de nouvelles capacités matérielles dans les derniers appareils Android.

![Image de héros d’Android Pie](pie-images/01-android-p-logo.png)

Cet article est structuré pour vous aider à démarrer dans le développement des applications Xamarin.Android pour Android Pie. Il explique comment installer les mises à jour nécessaires, configurer le SDK et préparer un émulateur ou un appareil pour les tests. Il fournit également un aperçu des nouvelles fonctionnalités dans Android Pie et fournit un exemple de code source qui illustre comment utiliser certaines des fonctionnalités clés Android Pie.

Xamarin.Android 9.0 fournit un support pour Android Pie. Pour plus d’informations sur le support Xamarin.Android pour Android Pie, voir les notes de sortie [Android P Developer Preview 3.](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)

## <a name="requirements"></a>Spécifications

La liste suivante est nécessaire pour utiliser les fonctionnalités Android Pie dans les applications basées sur Xamarin :

- **Visual Studio** &ndash; Visual Studio 2019 est recommandé.
    Si vous utilisez Visual Studio 2017, sur windows update to Visual Studio 2017 version 15.8 ou plus tard. Sur macOS, mise à jour sur Visual Studio 2017 pour mac version 7.6 ou plus tard.

- **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 ou plus tard doit être installé avec Visual Studio (Xamarin.Android est automatiquement installé dans le cadre du développement mobile avec la charge de travail **.NET).**

- **Java Developer Kit** &ndash; Xamarin Android 9.0 développement nécessite [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (ou vous pouvez essayer l’aperçu de la distribution de Microsoft de l’OpenJDK ). [OpenJDK](~/android/get-started/installation/openjdk.md) JDK8 est automatiquement installé dans le cadre du développement mobile avec une charge de travail **.NET.**

- **Android SDK** &ndash; Android SDK API 28 ou plus tard doit être installé via le gestionnaire Android SDK.

## <a name="getting-started"></a>Prise en main

Pour commencer à développer des applications Android Pie avec Xamarin.Android, vous devez télécharger et installer les derniers outils et forfaits SDK avant de pouvoir créer votre premier projet Android Pie :

1. Visual Studio 2019 est recommandé. Si vous utilisez Visual Studio 2017, mettez à jour sur [Visual Studio 2017 version 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou plus tard. Si vous utilisez Visual Studio pour Mac, mise à jour vers [Visual Studio 2017 pour mac version 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou plus tard.

2. Installez des forfaits et des outils **Android Pie (API 28)** via le SDK Manager.

3. Créez un nouveau projet Xamarin.Android qui cible **Android 9.0**.

4. Configurez un émulateur ou un appareil pour tester les applications Android Pie.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="update-visual-studio"></a>Mettre à jour Visual Studio 2017

Visual Studio 2019 est recommandé pour la construction d’applications Android Pie en utilisant Xamarin.

Si vous utilisez Visual Studio 2017, mettez à jour sur Visual Studio 2017 version 15.8 ou plus tard (pour les instructions, voir [Update Visual Studio 2017 à la version la plus récente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). Sur macOS, mise à jour sur Visual Studio 2017 pour Mac 7.6 ou plus tard (pour les instructions, voir [Setup and Install Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Installer l’Android SDK

Pour créer un projet avec Xamarin.Android 9.0, vous devez d’abord utiliser le gestionnaire Android SDK pour installer la plate-forme SDK pour **Android Pie (niveau API 28)** ou plus tard.

1. Démarrez le SDK Manager. Dans Visual Studio, cliquez sur **Tools > Android > Android SDK Manager**. Dans Visual Studio pour Mac, cliquez sur **Tools > SDK Manager**.

2. Dans le coin inférieur droit, cliquez sur l’icône de l’engrenage et **sélectionnez Repository > Google (Non-supporté)**:

    [![Définir le dépôt à Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Installez les forfaits **Android Pie** SDK, qui sont répertoriés comme **Android SDK Platform 28** dans l’onglet **Plates-formes** (pour plus d’informations sur l’utilisation du gestionnaire SDK, voir [Android SDK Setup](~/android/get-started/installation/android-sdk.md)):

    [![Installation de forfaits Android Pie](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Si vous utilisez un émulateur, créez un appareil virtuel qui prend en charge **le niveau 28 de l’API**. Pour plus d’informations sur la création d’appareils virtuels, voir [Gérer les appareils virtuels avec le gestionnaire d’appareils Android](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin.Android

Créez un nouveau projet Xamarin.Android. Si vous êtes nouveau au développement Android avec Xamarin, voir [Bonjour, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Xamarin.Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de la version pour cibler Android 9.0 ou plus tard. Par exemple, pour cibler votre projet pour Android Pie, vous devez configurer le niveau d’API Android cible de votre projet à **Android 9.0** (API 28). Il est recommandé de définir votre niveau de cadre cible à l’API 28 ou plus tard. Pour en savoir plus sur la configuration des niveaux d’API Android, voir [Comprendre les niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-a-device-or-emulator"></a>Configurer un appareil ou un émulateur

Si vous utilisez un appareil physique tel qu’un Nexus ou un Pixel, vous pouvez mettre à jour votre appareil sur Android Pie en suivant les instructions dans [Factory Images pour Nexus et Pixel Devices](https://developers.google.com/android/images).

Si vous utilisez un émulateur, créez un appareil virtuel pour le niveau 28 de l’API et sélectionnez une image à base de x86. Pour plus d’informations sur l’utilisation du gestionnaire d’appareils Android pour créer et gérer des appareils virtuels, voir [Gérer les périphériques virtuels avec le gestionnaire d’appareils Android](~/android/get-started/installation/android-emulator/device-manager.md).
Pour plus d’informations sur l’utilisation de l’émulateur Android pour les tests et le débogage, voir [Debugging sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="new-features"></a>Nouvelles fonctionnalités

Android Pie présente une variété de nouvelles fonctionnalités. Certaines de ces nouvelles fonctionnalités sont destinées à tirer parti de nouvelles capacités matérielles offertes par les derniers appareils Android, tandis que d’autres sont conçus pour améliorer davantage l’expérience utilisateur Android:

- **Le support** &ndash; Display Cutout fournit des API pour trouver l’emplacement et la forme de la _découpe_ en haut de l’écran sur les nouveaux appareils Android.

- **Notification Enhancements** &ndash; Les messages de notification `Person` peuvent désormais afficher des images, et une nouvelle classe est utilisée pour simplifier les participants à la conversation.

- **Support de** &ndash; la plate-forme de positionnement intérieur pour le protocole WiFi Round-Trip-Time, qui permet aux applications d’utiliser des appareils WiFi pour la navigation dans les paramètres intérieurs.

- **Support multi-caméra** &ndash; Offre la possibilité d’accéder simultanément aux flux à partir de plusieurs caméras physiques (comme des caméras à double avant et à double dossier).

Les sections suivantes mettent en évidence ces fonctionnalités et fournissent de brefs exemples de code pour vous aider à commencer à les utiliser dans votre application.

### <a name="display-cutout-support"></a>Support de découpe d’affichage

De nombreux appareils Android plus récents avec des écrans bord à bord ont un *Display Cutout* (ou "encoche") en haut de l’écran pour l’appareil photo et le haut-parleur.
La capture d’écran suivante fournit un exemple d’émulateur d’une découpe :

[![Un émulateur Android simulant une découpe](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Pour gérer la façon dont la fenêtre de votre application affiche son contenu sur les appareils avec une découpe d’affichage, Android Pie a ajouté un nouvel attribut de mise en page de fenêtre [LayoutInDisplayCutoutMode.](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) Cet attribut peut être défini à l’une des valeurs suivantes :

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; La fenêtre n’est jamais autorisée à se chevaucher avec la zone de découpe.

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; La fenêtre est autorisée à s’étendre dans la zone de découpe, mais seulement sur les bords courts de l’écran. 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; La fenêtre est autorisée à s’étendre dans la zone de découpe si la découpe est contenue dans une barre du système.

Par exemple, pour empêcher la fenêtre de l’application de se chevaucher avec la zone de découpe, définissez le mode de découpe de mise en page pour *ne jamais*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Les exemples suivants fournissent des exemples de ces modes de découpe. La première capture d’écran sur la gauche est de l’application en mode non-plein écran. Dans la capture d’écran centrale, `LayoutInDisplayCutoutMode` l’application va plein écran avec défini à `LayoutInDisplayCutoutModeShortEdges`. Notez que le fond blanc de l’application s’étend dans la zone de découpe d’affichage :

[![Exemple de modes de découpe d’affichage dans l’émulateur](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Dans la capture d’écran `LayoutInDisplayCutoutMode` finale (ci-dessus sur la droite), est réglé avant `LayoutInDisplayCutoutModeShortNever` qu’il ne passe à plein écran.
Notez que le fond blanc de l’application n’est pas autorisé à s’étendre dans la zone de découpe d’affichage.

Si vous avez besoin d’informations plus détaillées sur la zone de découpe sur l’appareil, vous pouvez utiliser la nouvelle classe [DisplayCutout.](https://developer.android.com/reference/android/view/DisplayCutout.html) `DisplayCutout`représente la zone de l’écran qui ne peut pas être utilisée pour afficher du contenu. Vous pouvez utiliser ces informations pour récupérer l’emplacement et la forme de la découpe afin que votre application ne tente pas d’afficher du contenu dans cette zone non fonctionnelle.

Pour plus d’informations sur les nouvelles fonctionnalités de découpe dans Android P, voir [Le support de découpe Display](https://developer.android.com/about/versions/pie/android-9.0#cutout).

### <a name="notifications-enhancements"></a>Améliorations des notifications

Android Pie introduit les améliorations suivantes pour améliorer l’expérience de messagerie:

- Les canaux de notification (introduits dans [Android Oreo](~/android/platform/oreo.md)) favorisent désormais le blocage des groupes de canaux.

- Le système de notification comporte trois nouvelles catégories Do-Not-Disturb (en priorisant les alarmes, les sons du système et les sources médiatiques). En outre, il existe sept nouveaux modes Do-Not-Disturb qui peuvent être utilisés pour supprimer les interruptions visuelles (comme les badges, les feux de notification, les apparences de barre d’état, et le lancement d’activités en plein écran).

- Une nouvelle classe [de personnes](https://developer.android.com/reference/android/app/Person.html) a été ajoutée pour représenter l’expéditeur d’un message. L’utilisation de cette classe permet d’optimiser le rendu de chaque notification en identifiant les personnes impliquées dans une conversation (y compris leurs avatars et ITI).

- Les notifications peuvent désormais afficher des images. 

L’exemple suivant illustre comment utiliser les nouvelles API pour générer une notification contenant une image. Dans les captures d’écran suivantes, une notification texte est affichée et est suivie d’une notification avec une image intégrée. Lorsque les notifications sont étendues (comme on le voit à droite), le texte de la première notification est affiché et l’image intégrée dans la deuxième notification est élargie :

[![Exemple de notification avec image](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

L’exemple suivant illustre comment inclure une image dans une notification Android `Person` Pie, et il démontre l’utilisation de la nouvelle classe:

1. Créez `Person` un objet qui représente l’expéditeur. Par exemple, le nom et l’icône `fromPerson`de l’expéditeur sont inclus dans :

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Créez `Notification.MessagingStyle.Message` un qui contient l’image à envoyer, en passant l’image à la nouvelle méthode [Notification.MessagingStyle.Message.SetData.](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)
   Par exemple :

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Ajoutez le message `Notification.MessagingStyle` à un objet. Par exemple :

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Branchez ce style dans le constructeur de notifications. Par exemple :

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. Publier la notification. Par exemple :

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Pour plus d’informations sur la création de notifications, voir [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

### <a name="indoor-positioning"></a>Positionnement intérieur

Android Pie fournit un support pour IEEE 802.11mc (également connu sous le nom _WiFi Round-Trip-Time_ ou _WiFi RTT_), ce qui permet aux applications de détecter la distance à un ou plusieurs points d’accès Wi-Fi. En utilisant ces informations, il est possible pour votre application de profiter du *positionnement intérieur* avec une précision d’un à deux mètres. Sur les appareils Android qui fournissent une prise en charge matérielle pour IEEE 801.11mc, votre application peut offrir des fonctionnalités de navigation telles que le contrôle géolocalis des appareils intelligents ou des instructions tour à tour à travers un magasin:

[![Exemple de navigation intérieure à l’aide de WiFi RTT](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

La nouvelle classe [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) et plusieurs classes d’aide fournissent les moyens de mesurer la distance par rapport aux appareils Wi-Fi. Pour plus d’informations sur les API de positionnement intérieur introduits dans Android P, voir [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).

### <a name="multi-camera-support"></a>Soutien multi-caméra

De nombreux appareils Android plus récents ont deux fronts et / ou deux-arrières caméras qui sont utiles pour des fonctionnalités telles que la vision stéréo, des effets visuels améliorés, et une capacité de zoom améliorée. Android P introduit une nouvelle API [Multi-Camera](https://developer.android.com/about/versions/pie/android-9.0#camera) qui permet à votre application d’utiliser un *appareil photo logique* (ou logique *multi-caméra*) qui est soutenu par deux caméras physiques ou plus.
Pour déterminer si l’appareil prend en charge une caméra multi logique, vous pouvez regarder les capacités de chaque appareil photo sur l’appareil pour voir si elle prend en charge [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android Pie comprend également une nouvelle classe [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) qui peut être utilisée pour aider à réduire les retards lors de la capture initiale et éliminer la nécessité de démarrer et de démarrer le flux de la caméra.

Pour plus d’informations sur le support Multi-Camera dans Android P, voir [support multi-caméra et mises à jour de la caméra](https://developer.android.com/about/versions/pie/android-9.0#camera).

### <a name="other-features"></a>Autres fonctionnalités

En outre, Android Pie prend en charge plusieurs autres nouvelles fonctionnalités:

- La nouvelle classe [AnimatedImageDrawable,](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) qui peut être utilisée pour dessiner et afficher des images animées.

- Une nouvelle classe [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) qui remplace `BitmapFactory`. `ImageDecoder`peut être utilisé pour `AnimatedImageDrawable`décoder un .

- Prise en charge des images hdR (High Dynamic Range) et HEIF (High Efficiency Image File Format).

- Le [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) a été amélioré pour gérer plus intelligemment les emplois liés au réseau. La nouvelle méthode [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) de la classe [JobParameters renvoie](https://developer.android.com/reference/android/app/job/JobParameters) le meilleur réseau pour effectuer toutes les demandes de réseau pour un emploi donné.

Pour plus d’informations sur les dernières fonctionnalités Android Pie, voir [android 9 fonctionnalités et API](https://developer.android.com/about/versions/pie/android-9.0).

## <a name="behavior-changes"></a>Changements de comportement

Lorsque la version Android cible est réglée au niveau 28 de l’API, il existe plusieurs changements de plate-forme qui peuvent affecter le comportement de votre application, même si vous n’implémentez pas les nouvelles fonctionnalités décrites ci-dessus. La liste suivante est un bref résumé de ces changements :

- Les applications doivent maintenant demander l’autorisation de premier plan avant d’utiliser les services de premier plan.

- Si votre application a plus d’un processus, elle ne peut pas partager un seul répertoire de données [WebView](xref:Android.Webkit.WebView) entre les processus.

- L’accès direct à l’annuaire de données d’une autre application par voie n’est plus autorisé.

Pour plus d’informations sur les changements de comportement pour les applications ciblant Android P, voir [Behavior Changes](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).

## <a name="sample-code"></a>Exemple de code

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) est une application d’échantillon Xamarin.Android pour Android Pie qui démontre comment `Person` définir les modes de découpe d’affichage, comment utiliser la nouvelle classe, et comment envoyer une notification qui inclut une image.

## <a name="summary"></a>Récapitulatif

Cet article a introduit Android Pie et expliqué comment installer et configurer les derniers outils et paquets pour le développement Xamarin.Android avec Android Pie. Il a fourni un aperçu des principales fonctionnalités disponibles dans Android Pie, avec le code source par exemple pour plusieurs de ces fonctionnalités.
Il comprenait des liens vers la documentation API et des sujets de développeur Android pour vous aider à démarrer dans la création d’applications pour Android Pie. Il a également mis en évidence les changements de comportement Android Pie les plus importants qui pourraient avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
