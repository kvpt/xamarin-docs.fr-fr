---
title: Android 9 Pie
description: Comment commencer à développer des applications pour le secteur Android 9 à l’aide de Xamarin. Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019886"
---
# <a name="android-pie-features"></a>Fonctionnalités du graphique Android

_Comment commencer à développer des applications pour le secteur Android 9 à l’aide de Xamarin. Android._

[Android 9 Pie](https://developer.android.com/about/versions/pie/) est désormais disponible à partir de Google. Un certain nombre de nouvelles fonctionnalités et API sont mises à disposition dans cette version, et beaucoup d’entre elles sont nécessaires pour tirer parti des nouvelles capacités matérielles des appareils Android les plus récents.

![Image du héros du graphique Android](pie-images/01-android-p-logo.png)

Cet article est structuré pour vous aider à prendre en main le développement d’applications Xamarin. Android pour Android Pie. Il explique comment installer les mises à jour nécessaires, configurer le kit de développement logiciel (SDK) et préparer un émulateur ou un appareil à des fins de test. Il fournit également un aperçu des nouvelles fonctionnalités du secteur Android et fournit un exemple de code source illustrant l’utilisation de certaines fonctionnalités clés du secteur Android.

Xamarin. Android 9,0 fournit la prise en charge d’Android Pie. Pour plus d’informations sur la prise en charge de Xamarin. Android pour Android Pie, consultez les notes de publication [Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1) .

## <a name="requirements"></a>Configuration requise

La liste suivante est requise pour utiliser les fonctionnalités en secteurs Android dans les applications Xamarin :

- **Visual studio** &ndash; visual studio 2019 est recommandé.
    Si vous utilisez Visual Studio 2017, sur Windows Update pour Visual Studio 2017 version 15,8 ou ultérieure. Sur macOS, mettez à jour vers Visual Studio 2017 pour Mac version 7,6 ou ultérieure.

- **Xamarin. android** &ndash; Xamarin. Android 9.0.0.17 ou version ultérieure doit être installé avec Visual Studio (Xamarin. Android est installé automatiquement dans le cadre du **développement mobile avec** la charge de travail .net).

- Le kit de développement **Java** &ndash; le développement Xamarin Android 9,0 requiert [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (vous pouvez essayer la version préliminaire de la distribution de [openjdk](~/android/get-started/installation/openjdk.md)de Microsoft). JDK8 est automatiquement installé dans le cadre du **développement mobile avec** la charge de travail .net.

- **Android SDK** &ndash; Android SDK l’API 28 ou ultérieure doit être installé via le gestionnaire de Android SDK.

## <a name="getting-started"></a>Prise en main

Pour commencer à développer des applications en secteurs Android avec Xamarin. Android, vous devez télécharger et installer les derniers outils et packages du kit de développement logiciel (SDK) avant de pouvoir créer votre premier projet Android en secteurs :

1. Visual Studio 2019 est recommandé. Si vous utilisez Visual Studio 2017, mettez à jour vers [Visual studio 2017 version 15,8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou ultérieure. Si vous utilisez Visual Studio pour Mac, mettez à jour vers [Visual Studio 2017 pour Mac version 7,6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou ultérieure.

2. Installez les packages et les outils **Android Pie (API 28)** à l’aide du gestionnaire du kit de développement logiciel (SDK).

3. Créez un nouveau projet Xamarin. Android qui cible **Android 9,0**.

4. Configurez un émulateur ou un appareil pour tester des applications en secteurs Android.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="update-visual-studio"></a>Mettre à jour Visual Studio 2017

Visual Studio 2019 est recommandé pour créer des applications en secteurs Android à l’aide de Xamarin.

Si vous utilisez Visual Studio 2017, mettez à jour vers Visual Studio 2017 version 15,8 ou ultérieure (pour obtenir des instructions, consultez [mettre à jour Visual studio 2017 vers la version la plus récente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). Sur macOS, mettez à jour vers Visual Studio 2017 pour Mac 7,6 ou version ultérieure (pour obtenir des instructions, consultez [configuration et installation Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Installer le Android SDK

Pour créer un projet avec Xamarin. Android 9,0, vous devez d’abord utiliser le gestionnaire de Android SDK pour installer la plateforme du kit de développement logiciel **(SDK) pour Android Pie (niveau d’API 28)** ou version ultérieure.

1. Démarrez le gestionnaire du kit SDK. Dans Visual Studio, cliquez sur **outils > Android > gestionnaire de Android SDK**. Dans Visual Studio pour Mac, cliquez sur **outils > gestionnaire du kit de développement logiciel (SDK)** .

2. Dans le coin inférieur droit, cliquez sur l’icône d’engrenage et sélectionnez **dépôt > Google (non pris en charge)** :

    [![de la configuration du référentiel sur Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Installer les packages du kit de développement logiciel (SDK) **Android Pie** , répertoriés comme **Android SDK plateforme 28** dans l’onglet **plateformes** (pour plus d’informations sur l’utilisation du gestionnaire du kit de développement logiciel (SDK), consultez [Android SDK le programme d’installation](~/android/get-started/installation/android-sdk.md)) :

    [![installation de packages Android Pie](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Si vous utilisez un émulateur, créez un appareil virtuel qui prend en charge le **niveau d’API 28**. Pour plus d’informations sur la création d’appareils virtuels, consultez [gestion des appareils virtuels avec l’Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin. Android

Créez un projet Xamarin. Android. Si vous débutez avec le développement Android avec Xamarin, consultez [Hello, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Xamarin. Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de version pour cibler Android 9,0 ou une version ultérieure. Par exemple, pour cibler votre projet pour le secteur Android, vous devez configurer le niveau d’API Android cible de votre projet sur **android 9,0** (API 28). Nous vous recommandons également de définir le niveau de votre infrastructure cible sur l’API 28 ou une version ultérieure. Pour plus d’informations sur la configuration des niveaux d’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-a-device-or-emulator"></a>Configurer un appareil ou un émulateur

Si vous utilisez un appareil physique, tel qu’un périphérique de passerelle ou un pixel, vous pouvez mettre à jour votre appareil vers un graphique Android en suivant les instructions fournies dans [images d’usine pour les appareils de type passerelle et pixel](https://developers.google.com/android/images).

Si vous utilisez un émulateur, créez un appareil virtuel pour le niveau d’API 28 et sélectionnez une image x86. Pour plus d’informations sur l’utilisation de la Android Device Manager pour créer et gérer des appareils virtuels, consultez [gestion des appareils virtuels avec le Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Pour plus d’informations sur l’utilisation de l’émulateur Android à des fins de test et de débogage, consultez [débogage sur le émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="new-features"></a>Nouvelles fonctionnalités

Android Pie introduit une variété de nouvelles fonctionnalités. Certaines de ces nouvelles fonctionnalités sont destinées à tirer parti des nouvelles capacités matérielles offertes par les derniers appareils Android, tandis que d’autres sont conçues pour améliorer l’expérience des utilisateurs Android :

- **Afficher la prise en charge du Découpage** &ndash; fournit des API pour Rechercher l’emplacement et la forme du _découpage_ en haut de l’écran sur les appareils Android plus récents.

- Les **améliorations des notifications** &ndash; messages de notification peuvent désormais afficher des images, et une nouvelle classe de `Person` est utilisée pour simplifier les participants aux conversations.

- Le **positionnement intérieur** &ndash; la prise en charge des plateformes pour le protocole de temps aller-retour WiFi, ce qui permet aux applications d’utiliser des périphériques WiFi pour la navigation dans les paramètres intérieurs.

- La **prise en charge de plusieurs caméras** &ndash; offre la possibilité d’accéder aux flux simultanément à partir de plusieurs caméras physiques (comme les caméras à double avant et à deux arrière-plan).

Les sections suivantes mettent en évidence ces fonctionnalités et fournissent de brefs exemples de code pour vous aider à commencer à les utiliser dans votre application.

### <a name="display-cutout-support"></a>Afficher la prise en charge du découpage

De nombreux appareils Android plus récents avec des écrans de périphérie à bord ont un *découpage d’affichage* (ou « encoche ») en haut de l’écran de l’appareil photo et de l’orateur.
La capture d’écran suivante fournit un exemple d’émulateur d’un découpage :

[![émulateur Android simulant un découpage](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Pour gérer la façon dont votre fenêtre d’application affiche son contenu sur les appareils dotés d’un découpage d’affichage, Android Pie a ajouté un nouvel attribut de disposition de fenêtre [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) . Cet attribut peut être défini sur l’une des valeurs suivantes :

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; la fenêtre n’est jamais autorisée à chevaucher la zone de découpage.

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; la fenêtre est autorisée à s’étendre dans la zone de découpage, mais uniquement sur les bords courts de l’écran. 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; la fenêtre est autorisée à s’étendre dans la zone de découpage si le découpage est contenu dans une barre système.

Par exemple, pour empêcher la fenêtre d’application de se chevaucher avec la zone de découpage, définissez le mode de découpage disposition sur *jamais*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Les exemples suivants fournissent des exemples de ces modes de découpage. La première capture d’écran de gauche correspond à l’application en mode non plein écran. Dans la capture d’écran du centre, l’application s’affiche en plein écran avec `LayoutInDisplayCutoutMode` défini sur `LayoutInDisplayCutoutModeShortEdges`. Notez que l’arrière-plan blanc de l’application s’étend dans la zone de découpage de l’affichage :

[![exemple afficher les modes de découpage dans l’émulateur](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Dans la capture d’écran finale (ci-dessus à droite), `LayoutInDisplayCutoutMode` est défini sur `LayoutInDisplayCutoutModeShortNever` avant de passer en mode plein écran.
Notez que l’arrière-plan blanc de l’application n’est pas autorisé à s’étendre dans la zone de découpage d’affichage.

Si vous avez besoin d’informations plus détaillées sur la zone de découpage sur l’appareil, vous pouvez utiliser la nouvelle classe [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) . `DisplayCutout` représente la zone de l’affichage qui ne peut pas être utilisée pour afficher le contenu. Vous pouvez utiliser ces informations pour récupérer l’emplacement et la forme du découpage afin que votre application n’essaie pas d’afficher le contenu dans cette zone non fonctionnelle.

Pour plus d’informations sur les nouvelles fonctionnalités de découpage dans Android P, consultez [afficher la prise en charge du découpage](https://developer.android.com/about/versions/pie/android-9.0#cutout).

### <a name="notifications-enhancements"></a>Améliorations des notifications

Android Pie introduit les améliorations suivantes pour améliorer l’expérience de messagerie :

- Les canaux de notification (introduits dans [Android Oreo](~/android/platform/oreo.md)) prennent désormais en charge le blocage des groupes de canaux.

- Le système de notification comporte trois nouvelles catégories de do-not-perturbation (hiérarchisation des alarmes, des sons système et des sources multimédias). En outre, il existe sept nouveaux modes de do-not-balancement qui peuvent être utilisés pour supprimer des interruptions visuelles (telles que les badges, les indicateurs de notification, les apparences de la barre d’État et le lancement d’activités plein écran).

- Une nouvelle classe [Person](https://developer.android.com/reference/android/app/Person.html) a été ajoutée pour représenter l’expéditeur d’un message. L’utilisation de cette classe permet d’optimiser le rendu de chaque notification en identifiant les personnes impliquées dans une conversation (y compris leurs avatars et URI).

- Les notifications peuvent maintenant afficher des images. 

L’exemple suivant montre comment utiliser les nouvelles API pour générer une notification qui contient une image. Dans les captures d’écran suivantes, une notification texte est publiée et est suivie d’une notification avec une image incorporée. Lorsque les notifications sont développées (comme vu à droite), le texte de la première notification s’affiche et l’image incorporée dans la deuxième notification est agrandie :

[![exemple de notification avec une image](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

L’exemple suivant illustre l’inclusion d’une image dans une notification de secteur Android et montre l’utilisation de la nouvelle classe `Person` :

1. Créez un objet `Person` qui représente l’expéditeur. Par exemple, le nom et l’icône de l’expéditeur sont inclus dans `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Créez un `Notification.MessagingStyle.Message` qui contient l’image à envoyer, en passant l’image à la nouvelle méthode [notification. MessagingStyle. message. SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) .
   Par exemple :

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Ajoutez le message à un objet `Notification.MessagingStyle`. Par exemple :

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Branchez ce style dans le générateur de notifications. Par exemple :

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. Publiez la notification. Par exemple :

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Pour plus d’informations sur la création de notifications, consultez [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

### <a name="indoor-positioning"></a>Positionnement intérieur

Android Pie assure la prise en charge de la norme IEEE 802.11 MC (également connue sous le nom d' _aller-retour WiFi_ ou _WiFi RTT_), ce qui permet aux applications de détecter la distance à un ou plusieurs points d’accès Wi-Fi. À l’aide de ces informations, votre application peut tirer parti du *positionnement intérieur* avec une précision d’un à deux mètres. Sur les appareils Android qui fournissent une prise en charge matérielle pour IEEE 801.11 MC, votre application peut proposer des fonctionnalités de navigation telles que le contrôle de l’emplacement des appareils intelligents ou les instructions de tour-de-main dans un magasin :

[![exemple de navigation intérieure avec WiFi RTT](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

La nouvelle classe [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) et plusieurs classes d’assistance fournissent les moyens de mesurer la distance aux périphériques Wi-Fi. Pour plus d’informations sur les API de positionnement intérieur introduites dans Android P, consultez [Android .net. WiFi. RTT](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).

### <a name="multi-camera-support"></a>Prise en charge de plusieurs caméras

De nombreux appareils Android plus récents ont des caméras double-frontale et/ou double-arrière qui sont utiles pour des fonctionnalités telles que la vision stéréo, les effets visuels améliorés et une fonction de zoom améliorée. Android P introduit une nouvelle API [multi-caméras](https://developer.android.com/about/versions/pie/android-9.0#camera) qui permet à votre application d’utiliser une *caméra logique* (ou un *multi-Camera logique*) qui est sauvegardée par au moins deux caméras physiques.
Pour déterminer si l’appareil prend en charge une caméra multiple logique, vous pouvez examiner les fonctionnalités de chaque caméra sur l’appareil pour voir s’il prend en charge [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android Pie comprend également une nouvelle classe [configurationsession](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) qui peut être utilisée pour réduire les retards lors de la capture initiale et éliminer le besoin de démarrer et de démarrer le flux de l’appareil photo.

Pour plus d’informations sur la prise en charge de plusieurs caméras dans Android P, consultez [prise en charge de plusieurs caméras et mises à jour de l’appareil photo](https://developer.android.com/about/versions/pie/android-9.0#camera).

### <a name="other-features"></a>Autres fonctionnalités

En outre, Android Pie prend en charge plusieurs autres nouvelles fonctionnalités :

- La nouvelle classe [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) , qui peut être utilisée pour dessiner et afficher des images animées.

- Nouvelle classe [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) qui remplace `BitmapFactory`. `ImageDecoder` peut être utilisé pour décoder une `AnimatedImageDrawable`.

- Prise en charge des images HDR (haute dynamique Range) Video et HEIF (High EFFICACITE image file format).

- Le [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) a été amélioré pour gérer plus intelligemment les tâches liées au réseau. La nouvelle méthode [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) de la classe [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) renvoie le meilleur réseau pour effectuer toutes les demandes réseau pour un travail donné.

Pour plus d’informations sur les fonctionnalités les plus récentes du secteur Android, consultez [fonctionnalités et API Android 9](https://developer.android.com/about/versions/pie/android-9.0).

## <a name="behavior-changes"></a>Changements de comportement

Lorsque la version d’Android cible est définie sur le niveau d’API 28, plusieurs modifications de plateforme peuvent affecter le comportement de votre application, même si vous n’implémentez pas les nouvelles fonctionnalités décrites ci-dessus. La liste suivante est un bref résumé de ces modifications :

- Les applications doivent maintenant demander une autorisation de premier plan avant d’utiliser les services de premier plan.

- Si votre application a plusieurs processus, elle ne peut pas partager un répertoire de données [WebView](xref:Android.Webkit.WebView) unique entre les processus.

- L’accès direct au répertoire de données d’une autre application par chemin d’accès n’est plus autorisé.

Pour plus d’informations sur les changements de comportement pour les applications ciblant Android P, consultez [changements de comportement](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).

## <a name="sample-code"></a>Exemple de code

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) est un exemple d’application Xamarin. Android pour le secteur Android qui montre comment définir des modes de découpage d’affichage, comment utiliser la nouvelle classe `Person` et comment envoyer une notification incluant une image.

## <a name="summary"></a>Résumé

Cet article a présenté le secteur Android et a expliqué comment installer et configurer les outils et packages les plus récents pour le développement Xamarin. Android avec Android Pie. Il a fourni une vue d’ensemble des fonctionnalités clés disponibles dans Android Pie, avec un exemple de code source pour plusieurs de ces fonctionnalités.
Elle contient des liens vers des rubriques de documentation sur les API et des développeurs Android qui vous aideront à créer des applications pour Android Pie. Elle a également mis en évidence les modifications de comportement les plus importantes du secteur Android susceptibles d’avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Secteurs Android 9](https://developer.android.com/about/versions/pie/)
