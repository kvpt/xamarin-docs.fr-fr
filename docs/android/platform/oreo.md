---
title: Fonctionnalités de Oreo
description: Comment prendre en main Xamarin. Android pour développer des applications pour la dernière version d’Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 56430f8c4988c16a31f9806b0ffb8b6355d6340b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019996"
---
# <a name="oreo-features"></a>Fonctionnalités de Oreo

_Comment prendre en main Xamarin. Android pour développer des applications pour la dernière version d’Android._

[Android 8,0 Oreo](https://developer.android.com/index.html) est la dernière version d’Android disponible à partir de Google. Android Oreo offre de nombreuses nouvelles fonctionnalités intéressantes pour les développeurs Xamarin. Android. Ces fonctionnalités incluent les canaux de notification, les badges de notification, les polices personnalisées dans XML, les polices téléchargeables, le remplissage automatique et les INCRUSTATIONs d’image. Android Oreo comprend de nouvelles API pour ces nouvelles fonctionnalités, et ces API sont disponibles pour les applications Xamarin. Android quand vous utilisez Xamarin. Android 8,0 et versions ultérieures.

[image![Android Oreo Hero](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Cet article est structuré pour vous aider à commencer à développer des applications Xamarin. Android pour Android 8,0 Oreo. Il explique comment installer les mises à jour nécessaires, configurer le kit de développement logiciel (SDK) et créer un émulateur (ou un appareil) à des fins de test. Il fournit également un aperçu des nouvelles fonctionnalités de la Oreo Android 8,0, avec des liens vers des exemples d’applications qui illustrent comment utiliser les fonctionnalités Android Oreo dans les applications Xamarin. Android.

## <a name="requirements"></a>spécifications

Les éléments suivants sont requis pour utiliser les fonctionnalités Android Oreo dans les applications basées sur Xamarin :

- **Visual studio** &ndash; si vous utilisez Windows, la version 15,5 ou une version ultérieure de Visual Studio est requise.  Si vous utilisez un Mac, Visual Studio pour Mac version 7.2.0 est requise.

- **Xamarin. android** &ndash; Xamarin. Android 8,0 ou version ultérieure doit être installé et configuré avec Visual Studio.

- **Android SDK** &ndash; Android SDK 8,0 (API 26) ou version ultérieure doit être installé via le gestionnaire de Android SDK.

## <a name="getting-started"></a>Commencer

Pour commencer à utiliser Android Oreo avec Xamarin. Android, vous devez télécharger et installer les derniers outils et packages du kit de développement logiciel (SDK) avant de pouvoir créer un projet Android Oreo :

1. Mettez à jour vers la dernière version de Visual Studio.

2. Installez les packages et outils **Android 8.0.0 (API 26)** ou version ultérieure via le gestionnaire du kit de développement logiciel (SDK).

3. Créez un nouveau projet Xamarin. Android qui cible Android Oreo (API 26).

4. Configurez un émulateur ou un appareil pour tester des applications Android Oreo.

Chacune de ces étapes est expliquée dans les sections suivantes :

### <a name="update-visual-studio-and-xamarinandroid"></a>Mettre à jour Visual Studio et Xamarin. Android

Pour ajouter la prise en charge d’Android Oreo à Visual Studio, procédez comme suit :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Pour Visual Studio 2019, utilisez le [Gestionnaire du kit de développement logiciel (SDK)](~/android/get-started/installation/android-sdk.md) pour installer le niveau d’API 26,0 ou version ultérieure.

- Si vous utilisez Visual Studio 2017 :

    1. Mettez à jour vers Visual Studio 2017 version 15,7 ou ultérieure (consultez [mise à jour de Visual studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Utilisez le [Gestionnaire du kit SDK](~/android/get-started/installation/android-sdk.md) pour installer le niveau d’API 26,0 ou une version ultérieure.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

- Mettez à jour vers la dernière version stable de Visual Studio pour Mac, comme expliqué dans [mise à jour de Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Pour plus d’informations sur la prise en charge d’Xamarin pour Android Oreo, consultez les [notes de publication de Xamarin. Android 8,0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).

### <a name="install-the-android-sdk"></a>Installer le Android SDK

Pour créer un projet avec Xamarin. Android 8,0, vous devez d’abord utiliser Xamarin Android SDK Manager pour installer la plateforme SDK pour **Android 8,0-Oreo** ou version ultérieure. Vous devez également installer Android SDK Tools 26,0 ou une version ultérieure.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez le gestionnaire du kit de développement logiciel (SDK) (dans Visual Studio, cliquez sur **outils > Android > gestionnaire de Android SDK**).

2. Installez les packages **Android 8,0-Oreo** . Si vous utilisez l’émulateur Android SDK, veillez à inclure les images système **x86** dont vous aurez besoin :

    [![sélection de packages Android 8,0 dans le gestionnaire de Android SDK](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installez **Android SDK Tools 26.0.2** ou version ultérieure, **Android SDK Platform-Tools 26.0.0** ou version ultérieure, et **Android SDK Build-Tools 26.0.0** (ou version ultérieure) :

    [![sélectionnant Android SDK Tools 26 dans le gestionnaire de Android SDK](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez le gestionnaire du kit de développement logiciel (SDK) (dans Visual Studio pour Mac, cliquez sur **outils > gestionnaire SDK**).

2. Installez les packages du kit de développement logiciel (SDK) **Android 8,0-Oreo** . Si vous utilisez l’émulateur Android SDK, veillez à inclure les images système **x86** dont vous aurez besoin :

    [![sélection de packages Android 8,0 dans le gestionnaire du kit de développement logiciel (SDK)](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installez **Android SDK Tools 26.0.2** ou version ultérieure, **Android SDK Platform-Tools 26.0.0** ou version ultérieure, et **Android SDK Build-Tools 26.0.0** (ou version ultérieure) :

    [![sélectionnant Android SDK Tools 26 dans le gestionnaire du kit SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>Démarrer un projet Xamarin. Android

Créez un projet Xamarin. Android. Si vous débutez avec le développement Android avec Xamarin, consultez [Hello, Android](~/android/get-started/hello-android/index.md) pour en savoir plus sur la création de projets Xamarin. Android.

Lorsque vous créez un projet Android, vous devez configurer les paramètres de version pour cibler Android 8,0 ou une version ultérieure. Par exemple, pour cibler votre projet pour Android 8,0, vous devez configurer le niveau d’API Android cible de votre projet sur **android 8,0 (API 26)** . Nous vous recommandons également de définir le niveau de votre infrastructure cible sur l’API 26 ou une version ultérieure. Pour plus d’informations sur la configuration des niveaux de niveau de l’API Android, consultez [Présentation des niveaux d’API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurer un émulateur ou un appareil

Si vous tentez de lancer le gestionnaire AVD basé sur l’interface utilisateur graphique Google par défaut après avoir installé Android SDK Tools 26,0 ou une version ultérieure, vous pouvez recevoir la boîte de dialogue d’erreur suivante, qui vous demande d’utiliser la ligne de commande **avdmanager** du gestionnaire AVD à la place :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Boîte de dialogue d’avertissement du gestionnaire de Émulateur Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Boîte de dialogue d’avertissement du gestionnaire de Émulateur Android](oreo-images/mac/03-avd-warning.png)

-----

Ce message s’affiche, car Google ne fournit plus d’interface utilisateur AVD autonome qui prend en charge l’API 26,0 et les versions ultérieures. Pour Android 8,0 Oreo, vous devez utiliser le gestionnaire de Émulateur Android Xamarin ou l’outil de `avdmanager` de ligne de commande pour créer des appareils virtuels pour Android Oreo.

Pour utiliser la Android Device Manager pour créer et gérer des appareils virtuels, consultez [gestion des appareils virtuels avec le Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Pour créer des appareils virtuels sans la Android Device Manager, suivez les étapes de la section suivante.

#### <a name="creating-virtual-devices-using-avdmanager"></a>Création d’appareils virtuels à l’aide de avdmanager

Pour utiliser **avdmanager** pour créer un appareil virtuel, procédez comme suit :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrez une fenêtre d’invite de commandes et définissez `JAVA_HOME` à l’emplacement du kit de développement logiciel (SDK) Java sur votre ordinateur. Pour une installation Xamarin classique, vous pouvez utiliser la commande suivante :

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Ajoutez l’emplacement du dossier Android SDK `bin` à votre `PATH`.
    Pour une installation Xamarin classique, vous pouvez utiliser la commande suivante :

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Fermez la fenêtre d’invite de commandes et ouvrez une nouvelle fenêtre d’invite de commandes. Créez un nouvel appareil virtuel à l’aide de la commande [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Par exemple, pour créer une AVD nommée **AVD-Oreo-8,0** à l’aide de l’image système x86 pour l’API de niveau 26, utilisez la commande suivante :

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Lorsque vous êtes invité à **créer un profil matériel personnalisé [non]** , vous pouvez entrer **non** et accepter le profil matériel par défaut. Si vous dites **Oui**, **avdmanager** vous invitera à fournir une liste de questions pour la personnalisation du profil matériel.

Une fois que vous avez **avdmanager** pour créer votre appareil virtuel, il est inclus dans le menu déroulant de l’appareil :

[![un nouveau AVD ajouté au menu déroulant de l’appareil](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez une fenêtre de **Terminal** et accédez à l’emplacement du répertoire Android SDK Tools sur votre Mac. Pour une installation Xamarin classique, vous pouvez utiliser la commande suivante :

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Créez un nouvel appareil virtuel à l’aide de la commande [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Par exemple, pour créer une AVD nommée **AVD-Oreo-8,0** à l’aide de l’image système x86 pour l’API de niveau 26, utilisez la commande suivante :

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Lorsque vous êtes invité à **créer un profil matériel personnalisé [non]** , vous pouvez entrer **non** et accepter le profil matériel par défaut. Si vous dites **Oui**, **avdmanager** vous invitera à fournir une liste de questions pour la personnalisation du profil matériel.

Une fois que vous avez utilisé **avdmanager** pour créer votre appareil virtuel, il est inclus dans le menu déroulant de l’appareil :

[![un nouveau AVD ajouté au menu déroulant de l’appareil](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Pour plus d’informations sur la configuration d’un émulateur Android à des fins de test et de débogage, consultez [débogage sur le émulateur Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Si vous utilisez un appareil physique tel qu’une liaison ou un pixel, vous pouvez mettre à jour votre appareil par le biais de mises à jour OTA (Automatic on the air) ou télécharger une image système et flasher votre appareil directement. Pour plus d’informations sur la mise à jour manuelle de votre appareil vers Android Oreo, consultez [images d’usine pour les appareils de passerelle et de pixels](https://developers.google.com/android/images).

## <a name="new-features"></a>Nouvelles fonctionnalités

Android Oreo introduit une variété de nouvelles fonctions et fonctionnalités, telles que les canaux de notification, les badges de notification, les polices personnalisées en XML, les polices téléchargeables, le remplissage automatique et l’image en image. Les sections suivantes mettent en évidence ces fonctionnalités et fournissent des liens pour vous aider à commencer à les utiliser dans votre application.

### <a name="notification-channels"></a>Canaux de notification

Les *canaux de notification* sont des catégories définies par l’application pour les notifications.
Vous pouvez créer un canal de notification pour chaque type de notification que vous devez envoyer et vous pouvez créer des canaux de notification pour refléter les choix effectués par les utilisateurs de votre application. Grâce à la nouvelle fonctionnalité de canaux de notification, vous pouvez fournir aux utilisateurs un contrôle affiné sur différents types de notifications. Par exemple, si vous implémentez une application de messagerie, vous pouvez créer des canaux de notification distincts pour chaque groupe de conversations créé par un utilisateur.

[Canaux de notification](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explique comment créer un canal de notification et l’utiliser pour publier des notifications locales. Pour obtenir un exemple de code réel, consultez l’exemple [canaux](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) . Cet exemple d’application gère deux canaux et définit des options de notification supplémentaires.

### <a name="notification-badges"></a>Badges de notification

Les badges de notification sont des petits points qui apparaissent sur les icônes d’application, comme illustré dans cette capture d’écran :

[![exemples de badges de notification sur les icônes d’application](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Ces points indiquent qu’il existe de nouvelles notifications pour un ou plusieurs canaux de notification dans l’application associée à l’icône de l’application &ndash; il s’agit de notifications indiquant que l’utilisateur n’a pas encore été licencié ou traité. Les utilisateurs peuvent appuyer longuement sur une icône pour en savoir plus sur les notifications associées à un badge de notification, en rejetant ou en agissant sur les notifications à partir du menu long-Press qui appeaars.

Pour plus d’informations sur les badges de notification, consultez la rubrique [badges des notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) pour les développeurs Android.

### <a name="custom-fonts-in-xml"></a>Polices personnalisées dans XML

Android Oreo présente des *polices au format XML*, ce qui vous permet d’incorporer des polices personnalisées en tant que ressources. Les formats de police OpenType ( **. otf**) et TrueType ( **. ttf**) sont pris en charge. Pour ajouter des polices en tant que ressources, procédez comme suit :

1. Créez un dossier **Resources/font** .

2. Copiez vos fichiers de polices (par exemple, fichiers **. ttf** et **. otf** ) dans **ressources/police**. 

3. Si nécessaire, renommez chaque fichier de polices afin qu’il respecte les conventions d’affectation de noms de fichiers Android (c’est-à-dire, utilisez uniquement les minuscules *a-z*, *0-9*et les traits de soulignement dans les noms de fichiers). Par exemple, le fichier de police `Pacifico-Regular.ttf` peut être renommé comme `pacifico.ttf`.

4. Appliquez la police personnalisée à l’aide de l’attribut New `android:fontFamily` dans votre fichier XML de disposition. Par exemple, la déclaration de `TextView` suivante utilise la ressource de police **Pacifico. ttf** ajoutée :

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Vous pouvez également créer un fichier XML de famille de polices qui décrit plusieurs polices, ainsi que des détails de style et de poids. Pour plus d’informations, consultez la rubrique polices pour développeurs Android [dans XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) .

### <a name="downloadable-fonts"></a>Polices téléchargeables

À compter d’Android Oreo, les applications peuvent demander des polices à un fournisseur au lieu de les regrouper dans le APK. Les polices sont téléchargées à partir du réseau uniquement si nécessaire. Cette fonctionnalité réduit la taille du APK, en conservant la mémoire du téléphone et l’utilisation des données cellulaires. Vous pouvez également utiliser cette fonctionnalité sur les versions 14 et ultérieures de l’API Android en installant le package de la bibliothèque de support Android 26.

Lorsque votre application a besoin d’une police, vous créez un objet `FontsRequest` (en spécifiant la police à télécharger), puis vous le transmettez à une méthode `FontsContract` pour télécharger la police. Les étapes suivantes décrivent le processus de téléchargement de polices plus en détail :

1. Instanciez un objet [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) . 

2. Sous-classe et instanciez [FontsContract. FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implémentez la méthode [FontRequestCallback. OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) , qui est utilisée pour gérer l’achèvement de la demande de police.

4. Implémentez la méthode [FontRequestCallback. OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) , qui est utilisée pour informer votre application des erreurs qui se produisent pendant le processus de demande de police.

5. Appelez la méthode [FontsContract. RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) pour récupérer la police du fournisseur de polices. 

Quand vous appelez la méthode `RequestFonts`, elle vérifie d’abord si la police est mise en cache localement (à partir d’un appel précédent à `RequestFont`). S’il n’est pas mis en cache, il appelle le fournisseur de polices, récupère la police de manière asynchrone, puis renvoie les résultats à votre application en appelant votre méthode `OnTypeFaceRetrieved`.

L’exemple de [polices téléchargeables](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) montre comment utiliser la fonctionnalité de polices téléchargeables introduite dans Android Oreo. 

Pour plus d’informations sur le téléchargement des polices, consultez la rubrique [polices téléchargeables](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) pour les développeurs Android.

### <a name="autofill"></a>Remplissage automatique

La nouvelle infrastructure de _remplissage_ automatique dans Android Oreo permet aux utilisateurs de gérer plus facilement des tâches répétitives, telles que la connexion, la création de comptes et les transactions de carte de crédit. Les utilisateurs passent moins de temps à retaper des informations (ce qui peut entraîner des erreurs d’entrée). Pour que votre application puisse fonctionner avec l’infrastructure de remplissage automatique, un service de remplissage automatique doit être activé dans les paramètres système (les utilisateurs peuvent activer ou désactiver le remplissage automatique).

L’exemple [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) illustre l’utilisation de l’infrastructure de remplissage automatique. Il comprend des implémentations d’activités clientes avec des vues qui doivent être renseignées automatiquement et un service qui peut fournir des données de remplissage automatique aux activités du client.

Pour plus d’informations sur la nouvelle fonctionnalité de remplissage automatique et sur l’optimisation de votre application pour la fonction de remplissage automatique, consultez la rubrique [infrastructure de remplissage](https://developer.android.com/guide/topics/text/autofill.html) automatique Android Developer.

### <a name="picture-in-picture-pip"></a>Image dans l’image (PIP)

Android Oreo permet à une activité de démarrer en mode incrustation d’image (PIP), en superposant l’écran d’une autre activité. Cette fonctionnalité est destinée à la lecture vidéo.

Pour spécifier que l’activité de votre application peut utiliser le mode PIP, affectez la valeur true à l’indicateur suivant dans le manifeste Android :

```xml
android:supportsPictureInPicture
```

Pour spécifier la façon dont votre activité doit se comporter lorsqu’elle est en mode PIP, vous utilisez le nouvel objet [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) . `PictureInPictureParams` représente un ensemble de paramètres que vous utilisez pour initialiser et mettre à jour une activité en mode PIP (par exemple, les proportions par défaut de l’activité). Les nouvelles méthodes PIP suivantes ont été ajoutées à `Activity` dans Android Oreo :

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; place l’activité en mode PIP. L’activité est placée dans le coin de l’écran, et le reste de l’écran est rempli avec l’activité précédente qui était à l’écran.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; met à jour les paramètres de configuration PIP de l’activité (par exemple, une modification des proportions).

L’exemple [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) illustre l’utilisation de base du mode incrustation d’image (PIP) pour les appareils mobiles introduits dans Oreo. L’exemple lit une vidéo qui continue sans interruption lors du basculement entre les modes d’affichage ou d’autres activités.

### <a name="other-features"></a>Autres fonctionnalités

Android Oreo contient de nombreuses autres nouvelles fonctionnalités telles que la bibliothèque de prise en charge des emoji, l’API d’emplacement, les limites d’arrière-plan, la large gamme d’applications, les nouveaux codecs audio, les améliorations WebView, la prise en charge améliorée de la navigation au clavier et une nouvelle API AAudio (Pro Audio) pour audio à faible latence haute performance. pour plus d’informations sur ces fonctionnalités, consultez la rubrique sur les [API et les fonctionnalités](https://developer.android.com/about/versions/oreo/android-8.0.html) Android Developer Android Oreo.

## <a name="behavior-changes"></a>Changements de comportement

Android Oreo comprend une série de modifications de comportement du système et de l’API qui peuvent avoir un impact sur les fonctionnalités des applications existantes. Ces modifications sont décrites comme suit.

### <a name="background-execution-limits"></a>Limites d’exécution en arrière-plan

Pour améliorer l’expérience utilisateur, Android Oreo impose des limitations sur les applications qui peuvent être exécutées en arrière-plan. Par exemple, si l’utilisateur regarde une vidéo ou émet un jeu, une application qui s’exécute en arrière-plan peut altérer les performances d’une application qui utilise beaucoup de vidéo au premier plan. En conséquence, Android Oreo place les restrictions suivantes sur les applications qui n’interagissent pas directement avec l’utilisateur :

1. Les **limitations du service d’arrière-plan** &ndash; lorsqu’une application s’exécute en arrière-plan, elle dispose d’une fenêtre de plusieurs minutes dans laquelle elle est toujours autorisée à créer et à utiliser des services. À la fin de cette fenêtre, Android arrête le service en arrière-plan de l’application et le traite comme étant _inactif_.

2. Les **limitations de diffusion** &ndash; Android 7,0 (API 25) ont placé des limitations sur les diffusions qu’une application s’inscrit à recevoir. Android Oreo rend ces limitations plus strictes. Par exemple, les applications Oreo Android ne peuvent plus inscrire de récepteurs de diffusion pour les diffusions implicites dans leurs manifestes.

Pour plus d’informations sur les nouvelles limites d’exécution en arrière-plan, consultez la rubrique [limites d’exécution en arrière-plan](https://developer.android.com/about/versions/oreo/background.html) du développeur Android.

### <a name="breaking-changes"></a>Modifications avec rupture

Les applications qui ciblent Android Oreo ou version ultérieure doivent modifier leurs applications pour prendre en charge les modifications suivantes, le cas échéant :

- Android Oreo déprécie la possibilité de définir la priorité des notifications individuelles. Au lieu de cela, vous définissez un niveau d’importance recommandé lors de la création d’un canal de notification. Le niveau d’importance que vous attribuez à un canal de notification s’applique à tous les messages de notification que vous y publiez.

- Pour les applications ciblant Android Oreo, `PendingIntent.GetService()` ne fonctionne pas en raison de nouvelles limites placées sur les services démarrés en arrière-plan. Si vous ciblez Android Oreo, vous devez utiliser [PendingIntent. GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) à la place.  

## <a name="sample-code"></a>Exemple de code

Plusieurs exemples Xamarin. Android sont disponibles pour vous montrer comment tirer parti des fonctionnalités Android Oreo :

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) montre comment utiliser le nouveau système de canaux de notification introduit dans Android Oreo. Cet exemple gère deux canaux de notification : l’un avec l’importance par défaut et l’autre avec une importance élevée.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) illustre l’utilisation de base du mode incrustation d’image (PIP) pour les appareils mobiles introduits dans Oreo. L’exemple lit une vidéo qui continue sans interruption lors du basculement entre les modes d’affichage ou d’autres activités.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) illustre l’utilisation de l’infrastructure de remplissage automatique. Il comprend des implémentations d’activités clientes avec des vues qui doivent être renseignées automatiquement et un service qui peut fournir des données de remplissage automatique aux activités du client.

- [Polices téléchargeables](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) fournit un exemple d’utilisation de la fonctionnalité de polices téléchargeables décrite précédemment.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) illustre l’utilisation de la bibliothèque de prise en charge EmojiCompat. Vous pouvez utiliser cette bibliothèque pour empêcher votre application d’illustrer des caractères Emoji manquants en tant que caractères « tofu ».

- Les [mises à jour d’emplacement en attente](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) montrent comment utiliser l’API location pour obtenir des mises à jour sur l’emplacement d’un appareil à l’aide d’un `PendingIntent`.

- Le [service de premier plan des mises à jour d’emplacement](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) montre comment utiliser l’API d’emplacement pour obtenir des mises à jour sur l’emplacement d’un appareil à l’aide d’un service de premier plan lié et démarré.

## <a name="video"></a>Vidéo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8,0 développement Oreo avecC#**

## <a name="summary"></a>Récapitulatif

Cet article a présenté Android Oreo et a expliqué comment installer et configurer les outils et packages les plus récents pour le développement Xamarin. Android sur Android Oreo. Il fournit une vue d’ensemble des fonctionnalités clés disponibles dans Android Oreo, avec des liens vers un exemple de code source pour plusieurs nouvelles fonctionnalités. Elle contient des liens vers des rubriques de documentation sur les API et des développeurs Android qui vous aideront à créer des applications pour Android Oreo. Elle a également mis en évidence les modifications de comportement Oreo Android les plus importantes qui pourraient avoir un impact sur les applications existantes.

## <a name="related-links"></a>Liens associés

- [Android 8,0 Oreo](https://developer.android.com/index.html)
