---
title: Signature manuelle de l’APK
ms.prod: xamarin
ms.assetid: 08549E1C-7F04-4D20-9E7A-794B9D09FD12
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 5dee7e18b7f9f47493293bc552cd4f44da56ceec
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571426"
---
# <a name="manually-signing-the-apk"></a>Signature manuelle de l’APK

Une fois l’application générée pour sa mise en production, l’APK doit être signé avant distribution afin de pouvoir être exécuté sur un appareil Android. Ce processus est généralement géré par l’environnement IDE. Toutefois, dans certaines situations, il est nécessaire de signer l’APK manuellement au niveau de la ligne de commande. La signature d’un APK met en œuvre les étapes suivantes :

1. **Créer une clé privée** &ndash; Cette étape ne doit être effectuée qu’une seule fois. Une clé privée est nécessaire pour signer numériquement l’APK.
    Une fois la clé privée préparée, cette étape peut être ignorée pour les prochaines builds de mise en production.

2. **Compresser l’APK dans un fichier zipalign** &ndash; *Zipalign* est un processus d’optimisation qui est réalisé sur une application. Il permet à Android d’interagir plus efficacement avec l’APK au moment de l’exécution. Xamarin.Android effectue une vérification au moment de l’exécution et n’autorise pas l’exécution de l’application si l’APK n’a pas été compressé dans un fichier zipalign.

3. **Signer l’APK** &ndash; Cette étape implique l’utilisation de l’utilitaire **apksigner** d’Android SDK et la signature de l’APK avec la clé privée qui a été créée à l’étape précédente. Les applications développées avec des versions des outils de génération du kit Android SDK antérieures à la version 24.0.3 utiliseront l’application **jarsigner** du JDK. Ces deux outils sont décrits plus en détail ci-dessous.

L’ordre des étapes est important et dépend de l’outil utilisé pour signer l’APK. Si vous utilisez **apksigner**, il est important de commencer par compresser l’application dans un fichier **zipalign**, puis de le signer avec **apksigner**.  Si **jarsigner** doit être utilisé pour signer l’APK, il est important de commencer par signer l’APK, puis d’exécuter **zipalign**.

## <a name="prerequisites"></a>Prérequis

Ce guide met l’accent sur l’utilisation de l’utilitaire **apksigner** disponible dans les outils de génération du kit Android SDK v24.0.3 ou version supérieure. Il est supposé qu’un APK a déjà été généré.

Les applications qui sont générées à l’aide d’une version antérieure des outils de génération du kit Android SDK doivent utiliser **jarsigner** comme décrit dans [Signer l’APK avec jarsigner](#Sign_the_APK_with_jarsigner) ci-dessous.

## <a name="create-a-private-keystore"></a>Créer un magasin de clés privé

Un *magasin de clés* est une base de données de certificats de sécurité créée à l’aide du programme [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) du SDK Java. Un magasin de clés est un élément essentiel pour la publication d’une application Xamarin.Android. En effet, Android n’exécutera pas les applications qui n’ont pas été signées numériquement.

Au cours du développement, Xamarin.Android utilise un magasin de clés de débogage pour signer l’application, ce qui permet le déploiement direct de l’application sur l’émulateur ou sur des appareils configurés pour utiliser des applications pouvant être déboguées.
Toutefois, ce magasin de clés n’est pas reconnu comme magasin de clés valide pour les besoins de la distribution d’applications.

Pour cette raison, un magasin de clés privé doit être créé et utilisé pour la signature des applications. Cette étape ne doit être effectuée qu’une seule fois, car la même clé sera utilisée pour la publication des mises à jour et pourra ensuite être utilisée pour signer d’autres applications.

Il est important de protéger ce magasin de clés. S’il est perdu, il ne sera pas possible de publier des mises à jour de l’application sur Google Play.
En cas de perte d’un magasin de clés, la seule solution est de créer un nouveau magasin de clés, de resigner l’APK avec la nouvelle clé, puis de soumettre une nouvelle application. L’ancienne application devra ensuite être supprimée de Google Play. De même, si ce nouveau magasin de clés est compromis ou distribué publiquement, des versions non officielles ou malveillantes d’une application peuvent alors être distribuées.

### <a name="create-a-new-keystore"></a>Créer un magasin de clés

La création d’un magasin de clés requiert l’outil en ligne de commande [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) du SDK Java. L’extrait de code suivant montre comment utiliser **keytool** (remplacez `<my-filename>` par le nom de fichier du magasin de clés et `<key-name>` par le nom de la clé dans le magasin de clés) :

```shell
$ keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA \
          -keysize 2048 -validity 10000
```

La première chose que demande **keytool** est le mot de passe du magasin de clés. Il demande ensuite des informations afin de créer la clé. L’extrait de code suivant est un exemple de création d’une nouvelle clé nommée `publishingdoc` qui sera stockée dans le fichier `xample.keystore` :

```shell
$ keytool -genkeypair -v -keystore xample.keystore -alias publishingdoc -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  Ham Chimpanze
What is the name of your organizational unit?
  [Unknown]:  NASA
What is the name of your organization?
  [Unknown]:  NASA
What is the name of your City or Locality?
  [Unknown]:  Cape Canaveral
What is the name of your State or Province?
  [Unknown]:  Florida
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US
Enter key password for <publishingdoc>
        (RETURN if same as keystore password):
Re-enter new password:
[Storing xample.keystore]
```

Pour afficher la liste des clés qui sont stockées dans un magasin de clés, utilisez **keytool** avec l’option &ndash; `list` :

```shell
$ keytool -list -keystore xample.keystore
```

## <a name="zipalign-the-apk"></a>Compresser l’APK dans un fichier zipalign

Avant de signer un APK avec **apksigner**, il est important de commencer par optimiser le fichier à l’aide de l’outil **zipalign** du SDK Android. **zipalign** va restructurer les ressources d’un APK sur des limites de 4 octets. Cette alignement permet à Android de charger rapidement les ressources de l’APK, augmentant ainsi les performances de l’application et réduisant potentiellement l’utilisation de la mémoire. Xamarin.Android effectuera une vérification au moment de l’exécution pour déterminer si le fichier APK a été compressé dans un fichier zipalign. Si l’APK n’a pas été compressé dans un fichier zipalign, l’application ne fonctionnera pas.

La commande suivante utilise l’APK signé et produit un APK compressé dans un fichier zipalign et signé nommé **helloworld.apk** qui est prêt pour la distribution.

```shell
$ zipalign -f -v 4 mono.samples.helloworld-unsigned.apk helloworld.apk
```

## <a name="sign-the-apk"></a>Signer l’APK

Après avoir compressé l’APK dans un fichier zipalign, il est nécessaire de le signer à l’aide d’un magasin de clés. Cette opération est réalisée avec l’outil **apksigner**, disponible dans le répertoire **build-tools** de la version des outils de génération du kit SDK.  Par exemple, si les outils de génération du kit Android SDK v25.0.3 sont installés, **apksigner** se trouve dans le répertoire suivant :

```bash
$ ls $ANDROID_HOME/build-tools/25.0.3/apksigner
/Users/tom/android-sdk-macosx/build-tools/25.0.3/apksigner*
```

L’extrait de code suivant suppose que l’outil **apksigner** est accessible par la variable d’environnement `PATH`. Il signe un APK à l’aide de l’alias de clé `publishingdoc` contenu dans le fichier **xample.keystore** :

```shell
$ apksigner sign --ks xample.keystore --ks-key-alias publishingdoc mono.samples.helloworld.apk
```

Lorsque cette commande est exécutée, **apksigner** vous demande le mot de passe du magasin de clés si nécessaire.

Consultez la [documentation de Google](https://developer.android.com/studio/command-line/apksigner.html) pour plus d’informations sur l’utilisation de l’outil **apksigner**.

> [!NOTE]
>  D’après [problème 62696222 signalé dans Google Issue Tracker](https://issuetracker.google.com/issues/62696222), **apksigner** est absent du kit Android SDK. La solution consiste à installer les outils de génération du kit Android SDK v25.0.3 et d’utiliser cette version de l’outil **apksigner**.  

<a name="Sign_the_APK_with_jarsigner"></a>

### <a name="sign-the-apk-with-jarsigner"></a>Signer l’APK avec jarsigner

> [!WARNING]
>  Cette section s’applique uniquement s’il est nécessaire de signer l’APK avec l’utilitaire **jarsigner**. Les développeurs sont encouragés à utiliser **apksigner** pour signer l’APK.

Cette technique implique la signature du fichier APK à l’aide de la commande **[jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)** du SDK Java.  L’outil **jarsigner** est fourni par le SDK Java.

L’extrait de code suivant montre comment signer un APK à l’aide de **jarsigner** et de la clé `publishingdoc` qui est contenue dans un fichier de magasin de clés nommé **xample.keystore** :

```shell
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore xample.keystore mono.samples.helloworld.apk publishingdoc
```

> [!NOTE]
>  Lorsque vous utilisez **jarsigner**, il est important de _commencer_ par signer l’APK, puis d’utiliser **zipalign**.  

## <a name="related-links"></a>Liens connexes

- [Signature d’application](https://source.android.com/security/apksigning/)
- [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)
- [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
- [zipalign](https://developer.android.com/studio/command-line/zipalign.html)
- [Outils de génération 26.0.0 - où est passé apksigner ?](https://issuetracker.google.com/issues/62696222)
