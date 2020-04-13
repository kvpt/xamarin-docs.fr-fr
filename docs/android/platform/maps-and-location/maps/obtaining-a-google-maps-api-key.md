---
title: Obtention d’une clé API Google Maps
description: Comment obtenir une clé API Google Maps pour ajouter des fonctionnalités de cartes à votre application.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 371876d087c7027d4cfe2d2d9ada8b0dbedb5dd5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75488970"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obtention d’une clé API Google Maps

Pour utiliser la fonctionnalité Google Maps dans Android, vous devez vous inscrire à une clé d’API Maps avec Google. Jusqu’à ce que vous fassiez cela, vous verrez simplement une grille vierge au lieu d’une carte dans vos applications. Vous devez obtenir une clé Google Maps Android API v2 - clés de l’ancien Google Maps Android API clé v1 ne fonctionnera pas.

L’obtention d’une clé D’API v2 de cartes implique les étapes suivantes :

1. Récupérez l’empreinte DIGITALE SHA-1 du clavier utilisé pour signer l’application.
2. Créez un projet dans la console d’API Google.
3. Obtenir la clé API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Obtenir votre empreinte digitale de clé de signature

Pour demander une clé d’API Maps de Google, vous devez connaître l’empreinte digitale SHA-1 du clavier qui est utilisé pour signer l’application.
Typiquement, cela signifie que vous devrez déterminer l’empreinte digitale SHA-1 pour le keystore de débogé, puis l’empreinte digitale SHA-1 pour le keystore qui est utilisé pour signer votre demande de libération.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Par défaut, le keystore qui est utilisé pour signer des versions de débogé d’une application Xamarin.Android peut être trouvé à l’endroit suivant:

**C:\\\\\\Utilisateurs [USERNAME]\\AppData Local\\Xamarin\\Mono pour Android\\debug.keystore**

Vous pouvez obtenir des informations sur un magasin de clés en exécutant la commande `keytool` du JDK. Cet outil se trouve généralement dans le répertoire de la poubelle Java :

**C:\\Fichiers\\\\de programme\\Android jdk\\microsoft_dist_openjdk_[VERSION] bin\\keytool.exe**

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Par défaut, le keystore qui est utilisé pour signer des versions de débogé d’une application Xamarin.Android peut être trouvé à l’endroit suivant:

**/Utilisateurs/[USERNAME]/.local/share/Xamarin/Mono pour Android/debug.keystore**

Vous pouvez obtenir des informations sur un magasin de clés en exécutant la commande `keytool` du JDK. Cet outil se trouve généralement dans le répertoire de la poubelle Java :

**/Système/Bibliothèque/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----

Exécuter l’ooutole de clé en utilisant la commande suivante (en utilisant les trajectoires de fichier indiquées ci-dessus) :

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug.keystore Exemple

Pour la clé de débogé par défaut (qui est automatiquement créée pour vous pour le débogage), utilisez cette commande :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Clés de production

Lors du déploiement d’une application sur Google Play, elle doit être [signée avec une clé privée](~/android/deploy-test/signing/index.md).
Il `keytool` faudra exécuter les coordonnées clés privées et l’empreinte digitale SHA-1 qui en résulte pour créer une clé d’API Google Maps de production. N’oubliez pas de mettre à jour le fichier **AndroidManifest.xml** avec la clé d’API Google Maps correcte avant le déploiement.

### <a name="keytool-output"></a>Sortie Keytool

Vous devriez voir quelque chose comme la sortie suivante dans votre fenêtre de console:

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

Vous utiliserez l’empreinte digitale SHA-1 (répertoriée après **SHA1**) plus tard dans ce guide.

## <a name="creating-an-api-project"></a>Création d’un projet API

Une fois que vous avez récupéré l’empreinte digitale SHA-1 du keystore de signature, il est nécessaire de créer un nouveau projet dans la console Google APIs (ou d’ajouter le service Google Maps Android API v2 à un projet existant).

1. Dans un navigateur, naviguez vers [l’API console Google Developers & Services Dashboard](https://console.developers.google.com/apis/dashboard/) et cliquez sur **Sélectionnez un projet**. Cliquez sur un nom de projet ou créez-en un nouveau en cliquant sur **NEW PROJECT**:

   [![Google Developer Console CREATE PROJECT bouton](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Si vous avez créé un nouveau projet, entrez le nom du projet dans le dialogue **du nouveau projet** qui s’affiche. Ce dialogue fabriquera un projet ID unique qui est basé sur le nom de votre projet. Ensuite, cliquez sur le bouton **Créer** comme indiqué dans cet exemple :

   [![Nouveau projet s’appelle XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Après une minute ou deux, le projet est créé et vous êtes amené à la page **tableau de bord** du projet. De là, cliquez sur **ENABLE APIS ET SERVICES**:

   [![Cliquez sur Google Maps Android API dans la section Bibliothèque](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. De la page de la **Bibliothèque API,** cliquez sur **Maps SDK pour Android**. Sur la page suivante, cliquez sur **ENABLE** pour activer le service de ce projet :

   [![En cliquant sur le bouton ENABLE dans la section Tableau de bord](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

À ce stade, le projet API a été créé et Google Maps Android API v2 a été ajouté à elle. Cependant, vous ne pouvez pas utiliser cette API dans votre projet tant que vous n’avez pas créé d’informations d’identification pour cela. La section suivante explique comment créer une clé API et la liste blanche d’une application Xamarin.Android afin qu’elle soit autorisée à utiliser cette clé.

## <a name="obtaining-the-api-key"></a>Obtenir la clé API

Une fois le projet **Google Developer Console** API créé, il est nécessaire de créer une clé Android API. Les applications Xamarin.Android doivent avoir une clé API avant d’avoir accès à Android Map API v2.

1. Dans la page **Maps SDK pour Android** qui s’affiche (après avoir cliqué sur **ENABLE** dans l’étape précédente), allez à **l’onglet Credentials** et cliquez sur le bouton **Créer des informations d’identification** :

   [![Cartes SDK pour le message Android Credentials](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Cliquez sur **la clé API**:

   [![Ajoutez des informations d’identification à votre dialogue de projet](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Une fois ce bouton cliqué, la clé API est générée. Ensuite, il est nécessaire de restreindre cette clé afin que seule votre application peut appeler API avec cette clé. Cliquez **SUR RESTRICT KEY**:

   [![Cliquer sur Restrict Key sur la page Credentials](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Changez le champ de **nom** de **API Key 1** à un nom qui vous aidera à vous rappeler à quoi sert la clé (**XamarinMapsDemoKey** est utilisé dans cet exemple). Ensuite, cliquez sur le bouton radio **des applications Android** :

   [![Sélection d’applications Android sur la page Credentials](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Pour ajouter l’empreinte digitale SHA-1, cliquez sur **le nom et l’empreinte digitale**:

   [![Cliquer Ajouter le nom et l’empreinte digitale](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Entrez le nom du forfait de votre application et saisissez l’empreinte digitale du certificat SHA-1 (obtenue via `keytool` comme expliqué plus tôt dans ce guide). Dans l’exemple suivant, `XamarinMapsDemo` le nom du paquet est inscrit, suivi de l’empreinte digitale du certificat SHA-1 obtenue à partir **de debug.keystore**:

   [![Nom du paquet entré est com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Notez que, pour que votre APK accède à Google Maps, vous devez inclure des empreintes digitales ET des noms d’emballage SHA-1 pour chaque keystore (débbug et version) que vous utilisez pour signer votre APK. Par exemple, si vous utilisez un ordinateur pour débogé et un autre ordinateur pour générer la version APK, vous devez inclure l’empreinte digitale du certificat SHA-1 de la boutique de l’ordinateur de déboise du premier ordinateur et l’empreinte digitale du certificat SHA-1 à partir du clavier de sortie du deuxième ordinateur. Cliquez **sur le nom et l’empreinte digitale de l’emballage** pour ajouter une autre empreinte digitale et le nom de l’emballage, comme indiqué dans cet exemple :

   [![L’ajout d’une autre empreinte digitale crée un autre certificat SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications. Ensuite, vous êtes retourné à la liste de vos clés API. Si vous avez d’autres clés API que vous avez créées plus tôt, elles seront également répertoriées ici. Dans cet exemple, une seule clé API (créée dans les étapes précédentes) est répertoriée :

   [![XamarinMapsDemoKey est indiqué dans la liste des clés de l’API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Connectez le projet à un compte facturable

A partir du 11 juin 2018, la clé API ne fonctionnera pas si le projet n’est pas connecté à un compte facturable (même si le service est toujours gratuit pour les applications mobiles).

1. Cliquez sur le bouton du menu hamburger et sélectionnez la page **Facturation** :

   [![Sélection de la section de facturation du menu hamburger](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Associez le projet à un compte de facturation en cliquant sur **Link, un compte de facturation** suivi par CREATE **BILLING ACCOUNT** sur le popup affiché (si vous n’avez pas de compte, vous serez guidé pour en créer un nouveau:

   [![Projet de lien vers le compte de facturation](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Ajout de la clé à votre projet

Enfin, ajoutez cette clé API au fichier **AndroidManifest.XML** de votre application Xamarin.Android. Dans l’exemple `YOUR_API_KEY` suivant, doit être remplacé par la clé API générée dans les étapes précédentes :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>Liens connexes

- [Console aPI Google](https://code.google.com/apis/console/)
- [La clé API Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
