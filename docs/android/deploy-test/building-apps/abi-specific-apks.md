---
title: Création de fichiers APK propres à une interface ABI
description: Ce document explique comment générer un fichier APK qui ciblera une interface ABI unique à l’aide de Xamarin.Android.
ms.prod: xamarin
ms.assetid: D21B195B-4530-4EB2-8704-5C4349A2CDD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 8c79075c9e01ef5da73255b152b4cbbd676b4f4e
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674744"
---
# <a name="building-abi-specific-apks"></a>Création de fichiers APK propres à une interface ABI

_Ce document explique comment générer un fichier APK qui ciblera une interface ABI unique à l’aide de Xamarin.Android._



## <a name="overview"></a>Vue d'ensemble

Dans certaines situations, il peut être avantageux pour une application d’avoir plusieurs fichiers APK. Chaque fichier APK est signé avec le même magasin de clés et porte le même nom de package, mais est compilé pour une configuration d’appareil ou Android spécifique. Ce n’est pas l’approche recommandée. Il est beaucoup plus simple d’avoir un fichier APK pouvant prendre en charge plusieurs appareils et configurations. Dans certaines situations, la création de plusieurs fichiers APK peut être utile, par exemple :

-  **Réduire la taille du fichier APK** : Google Play impose une limite de taille de 100 Mo pour les fichiers APK. La création de fichiers APK spécifiques peut réduire la taille du fichier APK, puisqu’il vous suffit d’indiquer un sous-ensemble des ressources et des ressources pour l’application.

-  **Prendre en charge différentes architectures de processeur**  : Si votre application a partagé des bibliothèques pour des processeurs spécifiques, vous ne pouvez distribuer que les bibliothèques partagées pour ce processeur.


Plusieurs fichiers APK peuvent compliquer la distribution – un problème résolu par Google Play. Google Play garantit que le fichier APK correct est remis à un appareil basé sur le code de la version de l’application et sur d’autres métadonnées contenues dans **AndroidManifest.XML**. Pour des détails et restrictions spécifiques sur la façon dont Google Play prend en charge plusieurs fichiers APK pour une application, consultez la [documentation de Google sur la prise en charge de plusieurs fichiers APK](https://developer.android.com/google/play/publishing/multiple-apks.html).

Ce guide explique comment générer un script pour générer plusieurs fichiers APK pour une application Xamarin.Android, chaque fichier APK ciblant une interface ABI spécifique. Il traitera les thèmes suivants :

1.  Créer un *code de version* unique pour le fichier APK.
1.  Créer une version temporaire d’**AndroidManifest.XML** qui sera utilisée pour ce fichier APK.
1.  Générer l’application à l’aide d’**AndroidManifest.XML** à partir de l’étape précédente.
1.  Préparer le fichier APK à la mise en production en le signant en effectuant un alignement zip.


À la fin de ce guide, vous trouverez une procédure pas à pas qui illustre comment générer un script pour ces étapes à l’aide de [Rake](http://martinfowler.com/articles/rake.html).



### <a name="creating-the-version-code-for-the-apk"></a>Création du code de version pour le fichier APK

Pour le code de version, Google recommande un algorithme particulier qui utilise un code de version à sept chiffres (consultez la section *Using a version code scheme* (en anglais) dans [Multiple APK support document](https://developer.android.com/google/play/publishing/multiple-apks.html) (en anglais).
L’extension de ce modèle de code de version à huit chiffres permet d’inclure des informations sur l’interface ABI dans le code de version, afin de garantir que Google Play distribuera le fichier APK correct à un appareil. La liste suivante explique ce format de code de version à huit chiffres (indexée de gauche à droite) :

-   **Index 0** (en rouge dans le diagramme ci-dessous) &ndash; Un nombre entier pour l’interface ABI :
    -   1 &ndash; `armeabi`
    -   2 &ndash; `armeabi-v7a`
    -   6 &ndash; `x86`

-   **Index 1 à 2** (en orange dans le diagramme ci-dessous) &ndash; Le niveau d’API minimal pris en charge par l’application.

-   **Index 3 à 4** (en bleu dans le diagramme ci-dessous) &ndash; Les tailles d’écran prises en charge :
    -   1 &ndash; petit
    -   2 &ndash; normal
    -   3 &ndash; grand
    -   4 &ndash; très grand

-   **Index 5 à 7** (en vert dans le diagramme ci-dessous) &ndash; Un nombre unique pour le code de version. 
    La version est définie par le développeur. Elle doit augmenter pour chaque version publique de l’application.

Le diagramme suivant illustre la position de chaque code décrit dans la liste ci-dessus :

[![Diagramme du format de code de version à huit chiffres, codé par couleur](abi-specific-apks-images/image00.png)](abi-specific-apks-images/image00.png#lightbox)


Google Play garantit que le fichier APK correct est remis à l’appareil en fonction du `versionCode` et de la configuration du fichier APK. Le fichier APK avec le code de version le plus élevé sera remis à l’appareil. Par exemple, une application peut avoir trois fichiers APK avec les codes de version suivants :

-  11413456 : l’interface ABI est `armeabi` ; niveau d’API ciblé 14 ; écrans de petite à grande taille, avec numéro de version 456.
-  21423456 : l’interface ABI est `armeabi-v7a` ; niveau d’API ciblé 14 ; écrans normaux &amp; grands, avec numéro de version 456.
-  61423456 : l’interface ABI est `x86` ; niveau d’API ciblé 14 ; écrans normaux &amp; grands, avec numéro de version 456.

Pour continuer sur cet exemple, imaginez qu’un bogue spécifique à `armeabi-v7a` a été résolu. La version de l’application passe à 457, et un nouveau fichier APK est généré avec le `android:versionCode` défini sur 21423457. Le code de version versionCodes pour les versions `armeabi` et `x86` resterait le même.

Imaginez maintenant que la version x86 reçoive des mises à jour ou des correctifs qui ciblent une interface API plus récente (Niveau d’API 19), menant à la version 500 de l’application. Le nouveau `versionCode` passerait alors à 61923500, tandis qu’armeabi/armeabi-v7a resterait inchangé. À ce stade, les codes de version seraient les suivants :

-  11413456 : l’interface ABI est `armeabi` ; niveau d’API ciblé 14 ; écrans de petite à grande taille, avec nom de version 456.
-  21423457 : l’interface ABI est `armeabi-v7a` ; niveau d’API ciblé 14 ; écrans normaux &amp; petits, avec nom de version 457.
-  61923500 : l’interface ABI est `x86` ; niveau d’API ciblé 19 ; écrans normaux &amp; petits, avec nom de version 500.


La gestion manuelle de ces codes de version peut être une charge considérable pour le développeur. Le processus de calcul du `android:versionCode` correct et la génération des fichiers APK devraient être automatisés.
Un exemple de procédure à suivre est abordé dans la procédure pas à pas à la fin de ce document.


### <a name="create-a-temporary-androidmanifestxml"></a>Créer un fichier AndroidManifest.XML temporaire

Bien que ce ne soit pas impérativement nécessaire, la création d’un fichier **AndroidManifest.XML** temporaire pour chaque interface ABI peut aider à éviter des problèmes susceptibles de survenir en cas de fuite d’informations d’un fichier APK vers l’autre. Par exemple, il est essentiel que l’attribut `android:versionCode` soit unique pour chaque fichier APK.

La manière de procéder dépend du système de scripts impliqué, mais implique généralement de prendre une copie du manifeste Android utilisé pendant le développement, de la modifier, puis d’utiliser ce manifeste modifié pendant le processus de génération.



### <a name="compiling-the-apk"></a>Compilation du fichier APK

La meilleure façon de générer le fichier APK par le biais de l’interface ABI est d’utiliser `xbuild` ou `msbuild`, comme indiqué dans l’exemple de ligne de commande suivant :

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:Package /p:AndroidSupportedAbis=<TARGET_ABI> /p:IntermediateOutputPath=obj.<TARGET_ABI>/ /p:AndroidManifest=<PATH_TO_ANDROIDMANIFEST.XML> /p:OutputPath=bin.<TARGET_ABI> /p:Configuration=Release <CSPROJ FILE>
```

la liste suivante explique chaque paramètre de ligne de commande :

-   `/t:Package` &ndash; crée un fichier APK Android qui est signé en utilisant le magasin de clés de débogage

-   `/p:AndroidSupportedAbis=<TARGET_ABI>` &ndash; il s’agit de l’interface ABI de la cible. Cela doit être l’une des interfaces `armeabi`, `armeabi-v7a` ou `x86`.

-   `/p:IntermediateOutputPath=obj.<TARGET_ABI>/` &ndash; il s’agit du répertoire qui contiendra les fichiers intermédiaires créés dans le cadre de la génération. Si nécessaire, Xamarin.Android créera un répertoire nommé d’après l’interface ABI, par exemple `obj.armeabi-v7a`. Il est recommandé d’utiliser un dossier pour chaque interface ABI, car ceci empêchera des problèmes pouvant entraîner la « fuite » d’une version vers une autre. Notez que cette valeur se termine par un séparateur de répertoire (un `/` dans le cas d’OS X).

-   `/p:AndroidManifest` &ndash; cette propriété spécifie le chemin d’accès au fichier **AndroidManifest.XML** qui sera utilisé lors de la génération.

-   `/p:OutputPath=bin.<TARGET_ABI>` &ndash; il s’agit du répertoire qui va héberger le fichier APK final. Xamarin.Android créera un répertoire nommé d’après l’interface ABI, par exemple `bin.armeabi-v7a`.

-   `/p:Configuration=Release` &ndash; exécuter une version de mise en production du fichier APK. Il n’est pas possible de télécharger des versions de débogage vers Google Play.

-   `<CS_PROJ FILE>` &ndash; il s’agit du chemin d’accès au fichier `.csproj` pour le projet Xamarin.Android.



### <a name="sign-and-zipalign-the-apk"></a>Signer le fichier APK et le compresser dans un fichier Zipalign

Le fichier APK doit être signé avant de pouvoir être distribué via Google Play. Ceci peut se faire à l’aide de l’application `jarsigner`, qui fait partie du Kit du développeur Java. La ligne de commande suivante montre comment utiliser `jarsigner` dans la ligne de commande :

```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <PATH/TO/KEYSTORE> -storepass <PASSWORD> -signedjar <PATH/FOR/SIGNED_JAR> <PATH/FOR/JAR/TO/SIGN> <NAME_OF_KEY_IN_KEYSTORE>
```

toutes les applications Xamarin.Android doivent faire l’objet d’un alignement zip avant de pouvoir être exécutées sur un appareil. Voici le format de ligne de commande à utiliser :

```shell
zipalign -f -v 4 <SIGNED_APK_TO_ZIPALIGN> <PATH/TO/ZIP_ALIGNED.APK>
```


## <a name="automating-apk-creation-with-rake"></a>automatisation de la création d’un fichier APK avec Rake

L’exemple de projet [OneABIPerAPK](https://github.com/xamarin/monodroid-samples/tree/master/OneABIPerAPK) est un projet Android simple qui va montrer comment calculer un numéro de version d’ABI spécifique et générer trois fichiers APK distincts pour chacune des ABI suivants :

-  armeabi
-  armeabi-v7a
-  x86


Le fichier [rakefile](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb) dans l’exemple de projet effectue chacune des étapes décrites dans les sections précédentes :

1. [Créez un code de version android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L30) pour le fichier APK.

1. [Écrivez le code de version android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L55) pour un fichier **AndroidManifest.XML** personnalisé de ce fichier APK.

1. [Compilez une version de mise en production](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L63) du projet Xamarin.Android qui ciblera uniquement l’interface ABI à l’aide du fichier **AndroidManifest.XML** créé à l’étape précédente.

1. [Signez le fichier APK](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L66) avec un magasin de clés de production.

1. Compressez le fichier APK dans un fichier [Zipalign](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L67).


Pour générer tous les fichiers APK de l’application, exécutez la tâche Rake `build` à partir de la ligne de commande :

```shell
$ rake build
==> Building an APK for ABI armeabi with ./Properties/AndroidManifest.xml.armeabi, android:versionCode = 10814120.
==> Building an APK for ABI x86 with ./Properties/AndroidManifest.xml.x86, android:versionCode = 60814120.
==> Building an APK for ABI armeabi-v7a with ./Properties/AndroidManifest.xml.armeabi-v7a, android:versionCode = 20814120.
```

une fois la tâche Rake terminée, il y aura trois dossiers `bin` avec le fichier `xamarin.helloworld.apk`. La capture d’écran suivante montre chacun de ces dossiers avec son contenu :

[![Emplacements des dossiers spécifiques à une plateforme contenant xamarin.helloworld.apk](abi-specific-apks-images/image01.png)](abi-specific-apks-images/image01.png#lightbox)


> [!NOTE]
> Le processus de génération décrit dans ce guide peut être implémenté dans un des nombreux systèmes de build différents. Bien que nous n’ayons pas d’exemple prédéfini, ceci devrait également être possible avec [Powershell](https://technet.microsoft.com/scriptcenter/powershell.aspx) / [psake](https://github.com/psake/psake) ou [fictif](http://fsharp.github.io/FAKE/).


## <a name="summary"></a>Récapitulatif

Ce guide contient quelques suggestions pour la création de fichiers APK Android qui ciblent une interface ABI spécifique. Il présente également un schéma possible pour la création du `android:versionCodes` qui identifiera l’architecture de processeur à laquelle le fichier APK est destiné. La procédure pas à pas inclut un exemple de projet dont le script de build est généré avec Rake.



## <a name="related-links"></a>Liens associés

- [OneABIPerAPK (exemple)](https://developer.xamarin.com/samples/monodroid/OneABIPerAPK/)
- [Publication d’une application](~/android/deploy-test/publishing/index.md)
- [Prise en charge de plusieurs fichiers APK pour Google Play](https://developer.android.com/google/play/publishing/multiple-apks.html)
