---
title: GDB
ms.prod: xamarin
ms.assetid: CD0BE462-FA38-4881-B481-82AD05B3B8FE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 0599b2374addf461e59948a1926de06e6e1e746a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754054"
---
# <a name="gdb"></a>GDB

## <a name="overview"></a>Présentation

Xamarin.Android 4.10 a introduit la prise en charge partielle de l’utilisation de `gdb` à l’aide de la cible MSBuild `_Gdb`. 

> [!NOTE]
> La prise en charge de `gdb` nécessite l’installation d’Android NDK.

Il y a trois manières d’utiliser `gdb` :

1. [Versions de débogage avec déploiement rapide activé](#Debug_Builds_with_Fast_Deployment).
1. [Versions de débogage avec déploiement rapide désactivé](#Debug_Builds_without_Fast_Deployment).
1. [Versions Release](#Release_Builds).

En cas de problème, veuillez consulter la section [Dépannage](#Troubleshooting).

<a name="Debug_Builds_with_Fast_Deployment" />

### <a name="debug-builds-with-fast-deployment"></a>Versions de débogage avec déploiement rapide

Lors de la création du déploiement d’une version de débogage avec déploiement rapide activé, `gdb` peut être joint à l’aide de la cible MSBuild `_Gdb`.

Tout d’abord, installez l’application. Vous pouvez le faire via l’IDE, ou via la ligne de commande :

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:Install *.csproj
```

Ensuite, exécutez la cible `_Gdb`. À la fin de l’exécution, une ligne de commande `gdb` s’affichera :

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
...
```

La `_Gdb` cible lancera une activité de lanceur arbitraire déclarée dans votre fichier `AndroidManifest.xml`. Pour spécifier explicitement les activités à exécuter, utilisez la propriété MSBuild `RunActivity`. Le démarrage des services et autres constructions Android ne sont pas pris en charge pour l’instant.

La cible `_Gdb` créera un répertoire `gdb-symbols` où elle copiera le contenu des répertoires `/system/lib` et `$APPDIR/lib` de votre cible.

> [!NOTE]
> Le contenu du répertoire `gdb-symbols` est lié à la cible Android sur laquelle vous avez déployé et ne sera pas automatiquement remplacé si vous changez la cible. (Considérez qu’il s’agit d’un bogue.) Si vous modifiez les appareils Android cibles, vous devrez supprimer manuellement ce répertoire.

Pour finir, copiez la commande `gdb` générée et exécutez-la dans votre interpréteur de commandes :

```bash
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) bt
#0  0x40082e84 in nanosleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#1  0x4008ffe6 in sleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#2  0x74e46240 in ?? ()
#3  0x74e46240 in ?? ()
(gdb) c
```

<a name="Debug_Builds_without_Fast_Deployment" />

## <a name="debug-builds-without-fast-deployment"></a>Versions de débogage sans déploiement rapide

Les versions de débogage *avec déploiement rapide de* fonctionnent en copiant le programme `gdbserver` du NDK d’Android dans le répertoire `.__override__` de déploiement rapide. Lorsque le déploiement rapide est désactivé, il est possible que ce répertoire n’existe pas.

Il y a deux manières d’y remédier :

- Définissez la propriété système `debug.mono.log` afin que le répertoire `.__override__` soit créé.
- Incluez `gdbserver` dans votre `.apk`.

### <a name="setting-the-debugmonolog-system-property"></a>Définition de la propriété système `debug.mono.log`

Pour définir la propriété système `debug.mono.log`, utilisez la commande `adb` :

```bash
$ adb shell setprop debug.mono.log gc
```

Une fois que la propriété système a été définie, exécutez la cible `_Gdb` et la commande imprimé `gdb`, comme avec les versions de débogage à configuration de déploiement rapide :

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

### <a name="including-gdbserver-in-your-app"></a>Inclusion de `gdbserver` dans votre application

Pour inclure `gdbserver` dans votre application :

1. Recherchez `gdbserver` au sein de votre NDK Android (il devrait se trouver dans **$ANDROID\_NDK\_PATH/prebuilt/android-arm/gdbserver/gdbserver**), puis copiez-le dans votre répertoire de projet.

2. Renommez `gdbserver` **libs/armeabi-v7a/libgdbserver.so**.

3. Ajoutez **libs/armeabi-v7a/libgdbserver.so** à votre projet avec une **action de génération** de `AndroidNativeLibrary`.

4. Régénérez et réinstallez votre application.

Une fois que l’application a été réinstallée, exécutez la cible `_Gdb` et la commande imprimé `gdb`, comme avec les versions de débogage à configuration de déploiement rapide :

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

<a name="Release_Builds" />

## <a name="release-builds"></a>Versions release

`gdb` prend en charge trois opérations :

1. L’autorisation `INTERNET`.
2. Le débogage d’application activé.
3. Un `gdbserver` accessible.

L’autorisation `INTERNET` est activée par défaut dans les applications de débogage. Si elle n’est pas déjà présente dans votre application, vous pouvez l’ajouter en modifiant **Properties/AndroidManifest.xml**, ou en modifiant les [Propriétés du projet](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest).

Le débogage de l’application peut être activé soit en définissant la propriété d’attribut personnalisé [ApplicationAttribute.Debugging](xref:Android.App.ApplicationAttribute.Debuggable) sur `true`, soit en modifiant **Properties/AndroidManifest.xml** et en définissant l’attribut `//application/@android:debuggable` sur `true` :

```xml
<application android:label="Example.Name.Here" android:debuggable="true">
```

Un `gdbserver` accessible peut être fourni en se référant à la section [Versions de débogage sans déploiement rapide ](#Debug_Builds_without_Fast_Deployment).

Une ombre au tableau : la cible MSBuild `_Gdb` va tuer toutes les instances d’application préalablement exécutées. Ceci ne fonctionnera pas sur les cibles v4.0 préalables à Android.

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="mono_pmip-doesnt-work"></a>`mono_pmip` ne fonctionne pas

La fonction `mono_pmip` (utile pour que [obtienne des frames de pile managées ](https://www.mono-project.com/docs/debug+profile/debug/#debugging-with-gdb)) est exportée à partir de `libmonosgen-2.0.so`, qui n’est actuellement pas déroulé par la cible `_Gdb`. (Ceci fera l’objet d’un correctif dans une version future.)

Pour activer des fonctions d’appel situées dans `libmonosgen-2.0.so`, copiez-les de l’appareil cible vers le répertoire `gdb-symbols` :

```bash
$ adb pull /data/data/Mono.Android.DebugRuntime/lib/libmonosgen-2.0.so Project/gdb-symbols
```

redémarrez ensuite votre session de débogage.

### <a name="bus-error-10-when-running-the-gdb-command"></a>Erreur de bus : 10 lors de l’exécution de la commande `gdb`

Lorsque la commande `gdb` échoue avec `"Bus error: 10"`, redémarrez l’appareil Android.

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
Bus error: 10
$
```

### <a name="no-stack-trace-after-attach"></a>Aucune arborescence des appels de procédure après que le répertoire a été joint

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
(gdb) bt
No stack.
```

C’est généralement le signe que le contenu du répertoire `gdb-symbols` n’est pas synchronisé avec votre cible Android. (Avez-vous modifié votre cible Android ?)

Supprimez le répertoire `gdb-symbols` et réessayez.
