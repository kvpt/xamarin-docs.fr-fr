---
title: Obtention d’une clé API Google Maps
description: Comment obtenir une clé API Google Maps pour l’ajout de la fonctionnalité Maps à votre application.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 371876d087c7027d4cfe2d2d9ada8b0dbedb5dd5
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488970"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obtention d’une clé API Google Maps

Pour utiliser la fonctionnalité Google Maps dans Android, vous devez vous inscrire à une clé API Maps avec Google. Jusqu’à ce que vous le procédiez, vous ne verrez qu’une grille vide au lieu d’une carte dans vos applications. Vous devez obtenir une clé d’API Android de Google Maps v2 : les clés de l’ancienne clé d’API Android de Google Maps ne fonctionneront pas.

L’obtention d’une clé d’API de cartes v2 implique les étapes suivantes :

1. Récupérez l’empreinte SHA-1 du magasin de clés utilisé pour signer l’application.
2. Créez un projet dans la console des API Google.
3. Obtention de la clé API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Obtention de l’empreinte digitale de votre clé de signature

Pour demander une clé d’API Maps à Google, vous devez connaître l’empreinte numérique SHA-1 du magasin de clés utilisé pour signer l’application.
En général, cela signifie que vous devrez déterminer l’empreinte de l’algorithme SHA-1 pour le magasin de clés de débogage, puis l’empreinte SHA-1 du magasin de clés qui est utilisée pour signer votre application à des fins de publication.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Par défaut, le magasin de clés qui est utilisé pour signer les versions Debug d’une application Xamarin. Android se trouve à l’emplacement suivant :

**C :\\utilisateurs\\[USERNAME]\\AppData\\local\\Xamarin\\mono pour Android\\Debug. keystore**

Vous pouvez obtenir des informations sur un magasin de clés en exécutant la commande `keytool` du JDK. Cet outil se trouve généralement dans le répertoire bin Java :

**C :\\Program Files\\le kit de développement logiciel (Android)\\\\microsoft_dist_openjdk_ [VERSION]\\bin\\keytool. exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Par défaut, le magasin de clés qui est utilisé pour signer les versions Debug d’une application Xamarin. Android se trouve à l’emplacement suivant :

**/Users/[USERNAME]/.local/share/Xamarin/Mono pour Android/Debug. keystore**

Vous pouvez obtenir des informations sur un magasin de clés en exécutant la commande `keytool` du JDK. Cet outil se trouve généralement dans le répertoire bin Java :

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----

Exécutez keytool à l’aide de la commande suivante (à l’aide des chemins d’accès aux fichiers indiqués ci-dessus) :

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Exemple Debug. keystore

Pour la clé de débogage par défaut (qui est automatiquement créée pour le débogage), utilisez la commande suivante :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Clés de production

Lors du déploiement d’une application sur Google Play, elle doit être [signée avec une clé privée](~/android/deploy-test/signing/index.md).
Le `keytool` doit être exécuté avec les détails de la clé privée et l’empreinte de l’algorithme SHA-1 obtenue pour créer une clé API Google Maps de production. N’oubliez pas de mettre à jour le fichier **fichier AndroidManifest. xml** avec la clé d’API Google Maps correcte avant le déploiement.

### <a name="keytool-output"></a>Sortie du keytool

Vous devez voir un résultat semblable à celui-ci dans la fenêtre de console :

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

Vous utiliserez l’empreinte de l’algorithme SHA-1 (indiquée après **SHA1**) plus loin dans ce guide.

## <a name="creating-an-api-project"></a>Création d’un projet d’API

Une fois que vous avez récupéré l’empreinte numérique SHA-1 du magasin de clés de signature, vous devez créer un nouveau projet dans la console des API Google (ou ajouter le service d’API Android de Google Maps à un projet existant).

1. Dans un navigateur, accédez au [tableau de bord & services de l’API de la console de Google Developers](https://console.developers.google.com/apis/dashboard/) , puis cliquez sur **Sélectionner un projet**. Cliquez sur un nom de projet ou créez-en un en cliquant sur **nouveau projet**:

   [![bouton créer un projet de la console de développement Google](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Si vous avez créé un nouveau projet, entrez le nom du projet dans la boîte de dialogue **nouveau projet** qui s’affiche. Cette boîte de dialogue génère un ID de projet unique basé sur le nom de votre projet. Ensuite, cliquez sur le bouton **créer** comme indiqué dans cet exemple :

   [![nouveau projet est nommé XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Après une minute, le projet est créé et vous êtes dirigé vers la page du **tableau de bord** du projet. À partir de là, cliquez sur **activer les API et les services**:

   [![cliquant sur l’API de Google Maps Android dans la section Bibliothèque](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Dans la page **bibliothèque d’API** , cliquez sur **mappages Kit de développement logiciel (SDK) pour Android**. Sur la page suivante, cliquez sur **activer** pour activer le service pour ce projet :

   [![cliquant sur le bouton Activer dans la section tableau de bord](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

À ce stade, le projet d’API a été créé et l’API Android de Google Maps v2 lui a été ajoutée. Toutefois, vous ne pouvez pas utiliser cette API dans votre projet tant que vous n’avez pas créé d’informations d’identification pour celle-ci. La section suivante explique comment créer une clé d’API et de la liste verte une application Xamarin.Android afin qu’il est autorisé à utiliser cette clé.

## <a name="obtaining-the-api-key"></a>Obtention de la clé API

Une fois le projet d’API de la **console de développement Google** créé, il est nécessaire de créer une clé d’API Android. Les applications Xamarin. Android doivent avoir une clé API avant de pouvoir accéder à l’API de carte Android v2.

1. Dans la page **Maps SDK pour Android** qui s’affiche (après avoir cliqué sur **activer** à l’étape précédente), accédez à l’onglet **informations d’identification** , puis cliquez sur le bouton **créer des informations d’identification** :

   [message d’informations d’identification du kit de développement logiciel (SDK) ![Maps pour Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Cliquez sur **clé API**:

   [![boîte de dialogue Ajouter des informations d’identification à votre projet](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Une fois que vous avez cliqué sur ce bouton, la clé API est générée. Ensuite, il est nécessaire de restreindre cette clé afin que seule votre application puisse appeler des API avec cette clé. Cliquez sur **restreindre la clé**:

   [![cliquant sur restreindre la clé dans la page informations d’identification](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Remplacez le champ **nom** de la **clé d’API 1** par un nom qui vous aidera à vous souvenir de l’utilisation de la clé (**XamarinMapsDemoKey** est utilisé dans cet exemple). Ensuite, cliquez sur la case d’option **applications Android** :

   [![sélectionnant applications Android dans la page informations d’identification](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Pour ajouter l’empreinte de l’algorithme SHA-1, cliquez sur **+ Ajouter un nom de package et une empreinte digitale**:

   [![cliquant sur Ajouter un nom de package et une empreinte digitale](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Entrez le nom du package de votre application et entrez l’empreinte de certificat SHA-1 (obtenue via `keytool` comme expliqué précédemment dans ce guide). Dans l’exemple suivant, le nom du package pour `XamarinMapsDemo` est entré, suivi de l’empreinte digitale de certificat SHA-1 obtenue à partir de **Debug. keystore**:

   [![nom de package entré est com. xamarin. docs. Android. map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Notez que, afin que votre APK accéder à Google Maps, vous devez inclure les empreintes digitales de SHA-1 et empaqueter des noms pour chaque magasin de clés (debug et release) que vous utilisez pour signer votre APK. Par exemple, si vous utilisez un ordinateur pour le débogage et un autre ordinateur pour générer le fichier APK de mise en production, vous devez inclure l’empreinte de certificat SHA-1 à partir du magasin de clés de débogage du premier ordinateur et l’empreinte de certificat SHA-1 à partir du magasin de clés de version de le deuxième ordinateur. Cliquez sur **+ Ajouter un nom de package et une empreinte digitale** pour ajouter une autre empreinte digitale et un nom de package, comme indiqué dans cet exemple :

   [![l’ajout d’une autre empreinte digitale crée un autre certificat SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Cliquez sur le bouton **Save (Enregistrer)** pour enregistrer vos changements. Ensuite, vous revenez à la liste de vos clés API. Si vous avez d’autres clés API que vous avez créées précédemment, elles sont également répertoriées ici. Dans cet exemple, une seule clé API (créée dans les étapes précédentes) est listée :

   [![XamarinMapsDemoKey est affiché dans la liste des clés API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Connecter le projet à un compte facturable

À partir du 11 juin 2018, la clé API ne fonctionnera pas si le projet n’est pas connecté à un compte facturable (même si le service est toujours gratuit pour les applications mobiles).

1. Cliquez sur le bouton de menu hamburger et sélectionnez la page **facturation** :

   [![sélectionnant la section facturation du menu hamburger](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Liez le projet à un compte de facturation en cliquant sur **lier un compte de facturation** , puis sur **créer un compte de facturation** dans la fenêtre contextuelle qui s’affiche (si vous n’avez pas de compte, vous serez guidé pour en créer un nouveau) :

   [![lier un projet à un compte de facturation](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Ajout de la clé à votre projet

Enfin, ajoutez cette clé API au fichier **fichier AndroidManifest. xml** de votre application Xamarin. Android. Dans l’exemple suivant, `YOUR_API_KEY` doit être remplacé par la clé API générée lors des étapes précédentes :

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

## <a name="related-links"></a>Liens associés

- [Console des API Google](https://code.google.com/apis/console/)
- [Clé de l’API Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
