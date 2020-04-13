---
title: Caractéristiques Oreo
description: Comment commencer à utiliser Xamarin.Android pour développer des applications pour la dernière version d’Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 56430f8c4988c16a31f9806b0ffb8b6355d6340b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019996"
---
# <a name="oreo-features"></a>Caractéristiques Oreo

_Comment commencer à utiliser Xamarin.Android pour développer des applications pour la dernière version d’Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) est la dernière version d’Android disponible sur Google. Android Oreo offre de nombreuses nouvelles fonctionnalités d’intérêt pour les développeurs Xamarin.Android. Ces fonctionnalités comprennent les canaux de notification, les badges de notification, les polices personnalisées dans XML, les polices téléchargeables, le remplissage automatique et l’image dans l’image (PIP). Android Oreo inclut de nouvelles API pour ces nouvelles fonctionnalités, et ces API sont disponibles pour les applications Xamarin.Android lorsque vous utilisez Xamarin.Android 8.0 et plus tard.

[![Image de héros d’Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Cet article est structuré pour vous aider à démarrer dans le développement des applications Xamarin.Android pour Android 8.0 Oreo. Il explique comment installer les mises à jour nécessaires, configurer le SDK et créer un émulateur (ou un appareil) pour les tests. Il fournit également un aperçu des nouvelles fonctionnalités dans Android 8.0 Oreo, avec des liens vers des applications d’échantillons qui illustrent comment utiliser les fonctionnalités Android Oreo dans les applications Xamarin.Android.

## <a name="requirements"></a>Spécifications

Ce qui suit est nécessaire pour utiliser les fonctionnalités Android Oreo dans les applications basées sur Xamarin:

- **Studio** &ndash; visuel Si vous utilisez Windows, la version 15.5 ou plus tard de Visual Studio est nécessaire.  Si vous utilisez un Mac, Visual Studio pour mac version 7.2.0 est nécessaire.

- **Xamarin.Android** &ndash; Xamarin.Android 8.0 ou plus tard doit être installé et configuré avec Visual Studio.

- **Android SDK** &ndash; Android SDK 8.0 (API 26) ou plus tard doit être installé via le gestionnaire Android SDK.

## <a name="getting-started"></a>Mise en route

Pour commencer à utiliser Android Oreo avec Xamarin.Android, vous devez télécharger et installer les derniers outils et forfaits SDK avant de pouvoir créer un projet Android Oreo :

1. Mise à jour de la dernière version de Visual Studio.

2. Installez **l’Android 8.0.0 (API 26)** ou plus tard des paquets et outils via le SDK Manager.

3. Créez un nouveau projet Xamarin.Android qui cible Android Oreo (API 26).

4. Configurez un émulateur ou un appareil pour tester les applications Android Oreo.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="update-visual-studio-and-xamarinandroid"></a>Mettre à jour Visual Studio et Xamarin.Android

Pour ajouter le support Android Oreo à Visual Studio, faites ce qui suit :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- Pour Visual Studio 2019, utilisez le [SDK Manager](~/android/get-started/installation/android-sdk.md) pour installer le niveau API 26.0 ou plus tard.

- Si vous utilisez Visual Studio 2017 :

    1. Mise à jour sur Visual Studio 2017 version 15.7 ou plus tard (voir [Mise à jour Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Utilisez le [SDK Manager](~/android/get-started/installation/android-sdk.md) pour installer le niveau API 26.0 ou plus tard.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

- Mise à jour de la dernière version stable de Visual Studio pour Mac comme expliqué dans [Updating Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Pour plus d’informations sur le support Xamarin pour Android Oreo, voir les [notes de sortie Xamarin.Android 8.0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).

### <a name="install-the-android-sdk"></a>Installer l’Android SDK

Pour créer un projet avec Xamarin.Android 8.0, vous devez d’abord utiliser le Xamarin Android SDK Manager pour installer la plate-forme SDK pour **Android 8.0 - Oreo** ou plus tard. Vous devez également installer Android SDK Tools 26.0 ou plus tard.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Démarrer le SDK Manager (en Studio Visuel, cliquez sur **Outils > Android > Android SDK Manager**).

2. Installez les forfaits **Android 8.0 - Oreo.** Si vous utilisez l’émulateur Android SDK, assurez-vous d’inclure les images **système x86** dont vous aurez besoin :

    [![Sélection des forfaits Android 8.0 dans le gestionnaire Android SDK](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installez **Android SDK Tools 26.0.2** ou plus tard, **Android SDK Platform-Tools 26.0.0** ou plus tard, et **Android SDK Build-Tools 26.0.0** (ou plus tard):

    [![Sélection d’Android SDK Tools 26 dans le gestionnaire Android SDK](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrer le SDK Manager (en studio visuel pour Mac, cliquez sur **Outils > SDK Manager**).

2. Installez les forfaits **Android 8.0 - Oreo** SDK. Si vous utilisez l’émulateur Android SDK, assurez-vous d’inclure les images **système x86** dont vous aurez besoin :

    [![Sélection des forfaits Android 8.0 dans le SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installez **Android SDK Tools 26.0.2** ou plus tard, **Android SDK Platform-Tools 26.0.0** ou plus tard, et **Android SDK Build-Tools 26.0.0** (ou plus tard):

    [![Sélection d’Android SDK Tools 26 dans le SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin.Android

Créez un nouveau projet Xamarin.Android. Si vous êtes nouveau au développement Android avec Xamarin, voir [Bonjour, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Xamarin.Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de la version pour cibler Android 8.0 ou plus tard. Par exemple, pour cibler votre projet pour Android 8.0, vous devez configurer le niveau d’API Android cible de votre projet à **Android 8.0 (API 26)**. Il est recommandé de définir votre niveau de cadre cible à l’API 26 ou plus tard. Pour en savoir plus sur la configuration des niveaux de niveau d’API Android, voir [Comprendre les niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurer un émulateur ou un appareil

Si vous tentez de lancer le gestionnaire AVD par défaut basé sur Google GUI après avoir installé Android SDK Tools 26.0 ou plus tard, vous pouvez obtenir le dialogue d’erreur suivante, qui vous demande d’utiliser la ligne de commande AVD gestionnaire outil **avdmanager** à la place:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Dialogue d’avertissement d’Android Emulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Dialogue d’avertissement d’Android Emulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Ce message est affiché parce que Google ne fournit plus un gestionnaire DE GUI AVD autonome qui prend en charge API 26.0 et plus tard. Pour Android 8.0 Oreo, vous devez utiliser soit le Xamarin `avdmanager` Android Emulator Manager, soit l’outil de ligne de commande pour créer des appareils virtuels pour Android Oreo.

Pour utiliser le gestionnaire d’appareils Android pour créer et gérer des appareils virtuels, voir [Gérer les appareils virtuels avec le gestionnaire d’appareils Android](~/android/get-started/installation/android-emulator/device-manager.md).
Pour créer des périphériques virtuels sans le gestionnaire d’appareils Android, suivez les étapes de la section suivante.

#### <a name="creating-virtual-devices-using-avdmanager"></a>Création d’appareils virtuels à l’aide d’avdmanager

Pour utiliser **avdmanager** pour créer un nouvel appareil virtuel, suivez ces étapes :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Ouvrez une fenêtre `JAVA_HOME` Command Prompt et définissez à l’emplacement du Java SDK sur votre ordinateur. Pour une installation Xamarin typique, vous pouvez utiliser la commande suivante :

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Ajoutez l’emplacement du dossier `bin` Android SDK à votre `PATH`.
    Pour une installation Xamarin typique, vous pouvez utiliser la commande suivante :

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Fermez la fenêtre Command Prompt et ouvrez une nouvelle fenêtre d’invite de commandement. Créez un nouvel appareil virtuel en utilisant la commande [avdmanager.](https://developer.android.com/studio/command-line/avdmanager.html) Par exemple, pour créer un AVD nommé **AVD-Oreo-8.0** à l’aide de l’image système x86 pour le niveau API 26, utilisez la commande suivante :

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Lorsque vous êtes invité avec **Voulez-vous créer un profil matériel personnalisé [non]** vous pouvez entrer **non** et accepter le profil matériel par défaut. Si vous dites **oui,** **avdmanager** vous invitera avec une liste de questions pour personnaliser le profil matériel.

Après **avoir avdmanager** pour créer votre appareil virtuel, il sera inclus dans le menu de traction de l’appareil:

[![Nouvel AVD ajouté au menu pull-down de l’appareil](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez une fenêtre **Terminal** et changez à l’emplacement du répertoire des outils Android SDK sur votre Mac. Pour une installation Xamarin typique, vous pouvez utiliser la commande suivante :

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Créez un nouvel appareil virtuel en utilisant la commande [avdmanager.](https://developer.android.com/studio/command-line/avdmanager.html) Par exemple, pour créer un AVD nommé **AVD-Oreo-8.0** à l’aide de l’image système x86 pour le niveau API 26, utilisez la commande suivante :

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Lorsque vous êtes invité avec **Voulez-vous créer un profil matériel personnalisé [non]** vous pouvez entrer **non** et accepter le profil matériel par défaut. Si vous dites **oui,** **avdmanager** vous invitera avec une liste de questions pour personnaliser le profil matériel.

Une fois que vous utilisez **avdmanager** pour créer votre appareil virtuel, il sera inclus dans le menu de traction de l’appareil:

[![Nouvel AVD ajouté au menu pull-down de l’appareil](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Pour plus d’informations sur la configuration d’un émulateur Android pour les tests et le débogage, voir [Debugging sur l’émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Si vous utilisez un appareil physique tel qu’un Nexus ou un Pixel, vous pouvez soit mettre à jour votre appareil par des mises à jour automatiques sur l’air (OTA) ou télécharger une image du système et clignoter votre appareil directement. Pour plus d’informations sur la mise à jour manuelle de votre appareil sur Android Oreo, voir [Factory Images pour Nexus et Pixel Devices](https://developers.google.com/android/images).

## <a name="new-features"></a>Nouvelles fonctionnalités

Android Oreo introduit une variété de nouvelles fonctionnalités et fonctionnalités, telles que les canaux de notification, les badges de notification, les polices personnalisées dans XML, les polices téléchargeables, le remplissage automatique et l’image dans l’image. Les sections suivantes mettent en évidence ces fonctionnalités et fournissent des liens pour vous aider à commencer à les utiliser dans votre application.

### <a name="notification-channels"></a>Canaux de notification

*Les canaux de notification* sont des catégories définies par l’application pour les notifications.
Vous pouvez créer un canal de notification pour chaque type de notification que vous devez envoyer, et vous pouvez créer des canaux de notification pour refléter les choix faits par les utilisateurs de votre application. La nouvelle fonction de canaux de notification vous permet de donner aux utilisateurs un contrôle de qualité sur différents types de notifications. Par exemple, si vous implémentez une application de messagerie, vous pouvez créer des canaux de notification distincts pour chaque groupe de conversation créé par un utilisateur.

[Notification Channels](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explique comment créer un canal de notification et l’utiliser pour afficher des notifications locales. Pour un exemple de code réel, consultez l’échantillon [NotificationChannels;](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) cette application d’échantillon gère deux canaux et définit des options de notification supplémentaires.

### <a name="notification-badges"></a>Notification Badges

Les badges de notification sont de petits points qui apparaissent sur les icônes de l’application comme indiqué dans cette capture d’écran:

[![Exemple d’badges de notification sur les icônes de l’application](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Ces points indiquent qu’il existe de nouvelles notifications pour un ou &ndash; plusieurs canaux de notification dans l’application associée à cette icône d’application ce sont des notifications que l’utilisateur n’a pas encore rejetées ou suivies d’un rôle de non- Les utilisateurs peuvent presser longuement sur une icône pour jeter un coup d’œil aux notifications associées à un badge de notification, rejeter ou agir sur les notifications du menu à longue presse qui apparage.

Pour plus d’informations sur les badges de notification, consultez le sujet Android Developer [Notification Badges.](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges)

### <a name="custom-fonts-in-xml"></a>Polices personnalisées dans XML

Android Oreo introduit *Fonts dans XML*, ce qui vous permet d’intégrer des polices personnalisées comme ressources. OpenType (**.otf**) et TrueType (**.ttf**) formats de police sont pris en charge. Pour ajouter des polices comme ressources, faites ce qui suit :

1. Créez un dossier **Ressources/police.**

2. Copiez vos fichiers de police (exemple, **fichiers .ttf** et **.otf)** à **Ressources/police**. 

3. Si nécessaire, renommer chaque fichier de police afin qu’il adhère aux conventions de nommage de fichiers Android (c.-à-d., utiliser seulement lowercase *a-z*, *0-9*, et souligne dans les noms de fichiers). Par exemple, le `Pacifico-Regular.ttf` fichier de police `pacifico.ttf`pourrait être renommé à quelque chose comme .

4. Appliquez la police personnalisée `android:fontFamily` en utilisant le nouvel attribut dans votre mise en page XML. Par exemple, `TextView` la déclaration suivante utilise la ressource supplémentaire de police **pacifico.ttf** :

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Vous pouvez également créer un fichier XML de la famille de polices qui décrit plusieurs polices ainsi que des détails de style et de poids. Pour plus d’informations, consultez le sujet Android Developer [Fonts in XML.](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)

### <a name="downloadable-fonts"></a>Fonts téléchargeables

En commençant par Android Oreo, les applications peuvent demander des polices d’un fournisseur plutôt que de les regrouper dans l’APK. Les polices ne sont téléchargées à partir du réseau que si nécessaire. Cette fonctionnalité réduit la taille APK, la conservation de la mémoire du téléphone et l’utilisation de données cellulaires. Vous pouvez également utiliser cette fonctionnalité sur les versions Android API 14 et plus en installant le paquet Android Support Library 26.

Lorsque votre application a besoin `FontsRequest` d’une police, vous créez un objet (spécifiant la police à télécharger) puis passez-la à une `FontsContract` méthode pour télécharger la police. Les étapes suivantes décrivent plus en détail le processus de téléchargement de polices :

1. Instantané d’un objet [FontRequest.](https://developer.android.com/reference/android/provider/FontRequest.html) 

2. Sous-classe et instantané [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implémentez la méthode [FontRequestCallback.OnTypeFaceRetrieved,](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) qui est utilisée pour gérer l’achèvement de la demande de police.

4. Implémentez la méthode [FontRequestCallback.OnTypeFaceRequestFailed,](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) qui est utilisée pour informer votre application des erreurs qui ont lieu pendant le processus de demande de police.

5. Appelez la méthode [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) pour récupérer la police auprès du fournisseur de polices. 

Lorsque vous `RequestFonts` appelez la méthode, il vérifie d’abord pour voir si `RequestFont`la police est localement mise en cache (d’un appel précédent à ). S’il n’est pas mis en cache, il appelle le fournisseur de police, récupère la police asynchrone, puis transmet les résultats à votre application en invoquant votre `OnTypeFaceRetrieved` méthode.

[L’échantillon Downloadable Fonts](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) montre comment utiliser la fonction Fonts téléchargeables introduite dans Android Oreo. 

Pour plus d’informations sur le téléchargement de polices, consultez le sujet Android Developer [Downloadable Fonts.](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html)

### <a name="autofill"></a>Remplissage automatique

Le nouveau cadre _Autofill_ dans Android Oreo permet aux utilisateurs de gérer plus facilement des tâches répétitives telles que la connexion, la création de compte et les transactions par carte de crédit. Les utilisateurs passent moins de temps à re-taper des informations (ce qui peut conduire à des erreurs d’entrée). Avant que votre application puisse fonctionner avec le cadre d’autofill, un service de remplissage automatique doit être activé dans les paramètres du système (les utilisateurs peuvent activer ou désactiver le remplissage automatique).

[L’échantillon AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) démontre l’utilisation du cadre de remplissage automatique. Il comprend la mise en œuvre d’activités clients avec des vues qui devraient être remplies automatiques, et un service qui peut fournir des données de remplissage automatique aux activités des clients.

Pour plus d’informations sur la nouvelle fonctionnalité Autofill et comment optimiser votre application pour le remplissage automatique, consultez le sujet Android Developer [Autofill Framework.](https://developer.android.com/guide/topics/text/autofill.html)

### <a name="picture-in-picture-pip"></a>Photo dans l’image (PIP)

Android Oreo permet à une activité de se lancer en mode image en image (PIP), superposant l’écran d’une autre activité. Cette fonctionnalité est destinée à la lecture vidéo.

Pour spécifier que l’activité de votre application peut utiliser le mode PIP, définissez le drapeau suivant pour que le drapeau suivant soit vrai dans le manifeste Android :

```xml
android:supportsPictureInPicture
```

Pour spécifier comment votre activité doit se comporter lorsqu’elle est en mode PIP, vous utilisez le nouvel objet [PictureInPictureParams.](https://developer.android.com/reference/android/app/PictureInPictureParams.html) `PictureInPictureParams`représente un ensemble de paramètres que vous utilisez pour initialiser et mettre à jour une activité en mode PIP (par exemple, le ratio d’aspect préféré de l’activité). Les nouvelles méthodes PIP `Activity` suivantes ont été ajoutées à Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; met l’activité en mode PIP. L’activité est placée dans le coin de l’écran, et le reste de l’écran est rempli de l’activité précédente qui était à l’écran.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; met à jour les paramètres de configuration PIP de l’activité (par exemple, un changement de rapport d’aspect).

[L’échantillon PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) montre l’utilisation de base du mode Picture-in-Picture (PiP) pour les appareils portables introduits dans Oreo. L’échantillon joue une vidéo qui se poursuit sans interruption tout en changeant d’avant en arrière entre les modes d’affichage ou d’autres activités.

### <a name="other-features"></a>Autres fonctionnalités

Android Oreo contient de nombreuses autres fonctionnalités telles que la bibliothèque de support Emoji, Location API, limites de fond, couleur large gamme pour les applications, nouveaux codecs audio, améliorations WebView, amélioration améliorée de la navigation du clavier, et un nouvel AAudio (pro audio) API pour haute performance à faible latence audio, Pour plus d’informations sur ces fonctionnalités, voir le développeur Android [Android Oreo Caractéristiques et API](https://developer.android.com/about/versions/oreo/android-8.0.html) sujet.

## <a name="behavior-changes"></a>Modifications de comportement

Android Oreo inclut une variété de changements de comportement système et API qui peuvent avoir un impact sur la fonctionnalité des applications existantes. Ces changements sont décrits comme suit.

### <a name="background-execution-limits"></a>Limites d’exécution de fond

Pour améliorer l’expérience utilisateur, Android Oreo impose des limites à ce que les applications peuvent faire en cours d’exécution en arrière-plan. Par exemple, si l’utilisateur regarde une vidéo ou joue à un jeu, une application en arrière-plan peut nuire aux performances d’une application à forte intensité vidéo au premier plan. En conséquence, Android Oreo impose les restrictions suivantes sur les applications qui ne sont pas directement interagir avec l’utilisateur:

1. &ndash; **Limitations de service d’arrière-plan** Lorsqu’une application s’exécute en arrière-plan, elle dispose d’une fenêtre de plusieurs minutes dans laquelle elle est toujours autorisée à créer et à utiliser des services. À la fin de cette fenêtre, Android arrête le service d’arrière-plan de l’application et le traite comme étant _inactif_.

2. **Restrictions de** &ndash; diffusion Android 7.0 (API 25) a imposé des limitations sur les émissions qu’une application s’inscrit pour recevoir. Android Oreo rend ces limitations plus strictes. Par exemple, les applications Android Oreo ne peuvent plus enregistrer les récepteurs de diffusion pour les diffusions implicites dans leurs manifestes.

Pour plus d’informations sur les nouvelles limites d’exécution de fond, consultez le sujet Android Developer [Background Execution Limits.](https://developer.android.com/about/versions/oreo/background.html)

### <a name="breaking-changes"></a>Dernières modifications

Les applications qui ciblent Android Oreo ou plus doivent modifier leurs applications pour prendre en charge les modifications suivantes, le cas échéant :

- Android Oreo déprécie la possibilité de définir la priorité des notifications individuelles. Au lieu de cela, vous définissez un niveau d’importance recommandé lors de la création d’un canal de notification. Le niveau d’importance que vous attribuez à un canal de notification s’applique à tous les messages de notification que vous lui publiez.

- Pour les applications ciblant `PendingIntent.GetService()` Android Oreo, ne fonctionne pas en raison de nouvelles limites imposées sur les services lancés en arrière-plan. Si vous ciblez Android Oreo, vous devriez utiliser [PendingIntent.GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) à la place.  

## <a name="sample-code"></a>Exemple de code

Plusieurs échantillons Xamarin.Android sont disponibles pour vous montrer comment profiter des fonctionnalités Android Oreo:

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) montre comment utiliser le nouveau système de canaux de notification introduit dans Android Oreo. Cet exemple gère deux canaux de notification : l’un avec l’importance par défaut et l’autre avec une grande importance.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) démontre l’utilisation de base du mode Picture-in-Picture (PiP) pour les appareils portables introduits dans Oreo. L’échantillon joue une vidéo qui se poursuit sans interruption tout en changeant d’avant en arrière entre les modes d’affichage ou d’autres activités.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) démontre l’utilisation du Cadre de remplissage automatique. Il comprend la mise en œuvre d’activités clients avec des vues qui devraient être remplies automatiques, et un service qui peut fournir des données de remplissage automatique aux activités des clients.

- [Downloadable Fonts](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) fournit un exemple de la façon d’utiliser la fonction Downloadable Fonts décrite précédemment.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) démontre l’utilisation de la bibliothèque de soutien EmojiCompat. Vous pouvez utiliser cette bibliothèque pour empêcher votre application de montrer les caractères emoji manquants comme des caractères de « tofu ».

- [Mises à jour de localisation en attente d’intention](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) illustre l’utilisation `PendingIntent`de l’API de localisation pour obtenir des mises à jour sur l’emplacement d’un appareil à l’aide d’un .

- [Location Updates Foreground Service](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) montre comment utiliser l’API de localisation pour obtenir des mises à jour sur l’emplacement d’un appareil à l’aide d’un service de premier plan lié et lancé.

## <a name="video"></a>Vidéo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Développement Android 8.0 Oreo avec C #**

## <a name="summary"></a>Récapitulatif

Cet article a introduit Android Oreo et expliqué comment installer et configurer les derniers outils et paquets pour le développement Xamarin.Android sur Android Oreo. Il a fourni un aperçu des principales fonctionnalités disponibles dans Android Oreo, avec des liens vers le code source exemple pour plusieurs nouvelles fonctionnalités. Il comprenait des liens vers la documentation API et des sujets de développeur Android pour vous aider à démarrer dans la création d’applications pour Android Oreo. Il a également mis en évidence les changements de comportement Android Oreo les plus importants qui pourraient avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens connexes

- [Android 8.0 Oreo](https://developer.android.com/index.html)
