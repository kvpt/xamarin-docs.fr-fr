---
title: Appareils multicœurs et Xamarin.Android
description: Android peut s’exécuter sur plusieurs architectures d’ordinateur différentes. Ce document décrit les différentes architectures d’UC qui peuvent être employées pour une application Xamarin.Android. Ce document explique également comment les applications Android sont empaquetées pour prendre en charge des architectures d’UC différentes. Nous présenterons l’interface binaire d’application (ABI), et fournirons des conseils concernant les ABI à utiliser dans une application de Xamarin.Android.
ms.prod: xamarin
ms.assetid: D812883C-A14A-E74B-0F72-E50071E96328
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: e27e73ac2c5164fa3431c8892b21a71c32fcd8ef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76724013"
---
# <a name="multi-core-devices--xamarinandroid"></a>Appareils multicœurs et Xamarin.Android

_Android peut fonctionner sur plusieurs architectures informatiques différentes. Ce document traite des différentes architectures CPU qui peuvent être utilisées pour une application Xamarin.Android. Ce document expliquera également comment les applications Android sont emballées pour prendre en charge différentes architectures CPU. L’interface binaire d’application (ABI) sera introduite, et des conseils seront fournis concernant les ATI à utiliser dans une application Xamarin.Android._

## <a name="overview"></a>Vue d’ensemble

Android permet la création de « binaires FAT », un seul fichier `.apk` qui contient une version compilée de l’application pour chaque architecture de processeur. Cela est accompli en associant chaque partie du code machine avec une *Interface binaire d’application*. L’ABI sert à contrôler le code exécuté sur un appareil spécifique. Par exemple, pour une application Android qui s’exécute sur un appareil x86, il est nécessaire d’inclure la prise en charge des ABI x86 lors de la compilation de l’application.

Plus précisément, chaque application Android prend en charge au moins une *interface binaire d’application intégrée* (EABI). Les EABI sont des conventions spécifiques à des programmes logiciels intégrés. Une EABI classique décrira des choses telles que :

- Le jeu d'instructions UC.

- Le mode Endian des écritures et lectures mémoire au moment de l’exécution.

- Le format binaire des fichiers objet et bibliothèques du programme, ainsi que le type de contenu autorisé ou pris en charge dans ces fichiers et des bibliothèques.

- Les différentes conventions utilisées pour passer des données entre le code d’application et le système (par exemple : comment les registres et/ou de la pile sont utilisés lorsque les fonctions sont appelées, les contraintes d’alignement, etc.)

- Les contraintes d’alignement et de taille pour les types enum, les structures, les champs et les tableaux.

- La liste des symboles de fonction disponibles pour votre code machine au moment de l’exécution, généralement à partir d’un ensemble très spécifique de bibliothèques.

### <a name="armeabi-and-thread-safety"></a>armeabi et sécurité des threads

L’interface binaire d’application sera abordée en détail ci-dessous, mais il est important de se rappeler que le runtime `armeabi` utilisé par Xamarin.Android *n’est pas thread-safe*. Si une application prenant en charge `armeabi` est déployée sur un appareil `armeabi-v7a`, de nombreuses exceptions étranges et inexplicables se produiront.

En raison d’un bogue dans Android 4.0.0 4.0.1, 4.0.2 et 4.0.3, les bibliothèques natives sont récupérées à partir du répertoire `armeabi` même s’il existe un répertoire `armeabi-v7a` et que l’appareil est un appareil `armeabi-v7a`.

> [!NOTE]
> Xamarin.Android vérifie que les fichiers `.so` sont ajoutés à l’APK dans l’ordre approprié. Ce bogue ne devrait pas poser problème pour les utilisateurs de Xamarin.Android.

### <a name="abi-descriptions"></a>Descriptions des ABI

Chaque ABI prise en charge par Android est identifiée par un nom unique.

#### <a name="armeabi"></a>armeabi

Il s’agit du nom d’une EABI pour les UC ARM qui prennent en charge au moins le jeu d’instructions ARMv5TE. Android suit l’ABI little-endian ARM GNU/Linux. Cette ABI ne prend pas en charge les calculs en virgule flottante avec accélération matérielle. Toutes les opérations de virgule flottante sont effectuées par les fonctions d’assistance provenant de la bibliothèque statique `libgcc.a` du compilateur. Les appareils SMP ne sont pas pris en charge par `armeabi`.

> [!IMPORTANT]
> Le code `armeabi` de Xamarin.Android n’est pas thread-safe et ne doit pas être utilisé sur des appareils `armeabi-v7a` multiprocesseurs (décrits ci-dessous). L’utilisation de code `armeabi` sur les appareils `armeabi-v7a` à un seul cœur est sûre.

#### <a name="armeabi-v7a"></a>armeabi-v7a

Il s’agit d’un autre jeu d’instructions d’UC ARM qui étend l’EABI `armeabi` décrite ci-dessus. L’EABI `armeabi-v7a` prend en charge les opérations à virgule flottante avec accélération matérielle et les appareils multiprocesseur (SMP). Une application qui utilise l’EABI `armeabi-v7a` devrait apporter des améliorations des performances sur une application qui utilise `armeabi`.

> [!NOTE]
> Le code machine `armeabi-v7a` ne fonctionnera pas sur les appareils ARMv5.

#### <a name="arm64-v8a"></a>arm64-v8a

Il s’agit d’un jeu d’instructions de 64 bits basé sur l’architecture d’UC ARMv8. Cette architecture est utilisée dans le *Nexus 9*.
Xamarin.Android 5.1 a inauguré la prise en charge de cette architecture (pour plus d’informations, consultez [Prise en charge du runtime 64 bits](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support)).

#### <a name="x86"></a>x86

C’est le nom d’une ABI pour les processeurs qui prennent en charge le jeu d’instructions couramment nommé *x86* ou *IA-32*. Cette ABI correspond à des instructions pour le jeu d’instructions Pentium Pro, y compris les jeux d’instructions MMX, SSE, SSE2 et SSE3. Elle n’inclut pas toutes d’autres extensions de jeu d’instructions IA-32 facultatives telles que :

- L’instruction MOVBE.
- L’extension SSE3 supplémentaire (SSSE3).
- Toute variante de SSE4.

> [!NOTE]
> Google TV, bien qu’il s’exécute sur x86, n’est pas pris en charge par Android NDK.

#### <a name="x86_64"></a>x86_64

C’est le nom d’une ABI pour les processeurs qui prennent en charge le jeu d’instructions x86 64 bits couramment nommé *x64* ou *AMD64*. Xamarin.Android 5.1 a inauguré la prise en charge de cette architecture (pour plus d’informations, consultez [Prise en charge du runtime 64 bits](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support)).

#### <a name="apk-file-format"></a>Format de fichier APK

Le format de fichier Package d’application Android contient tout le code, les ressources et les certificats nécessaires pour une application Android. Il s’agit d’un fichier `.zip`, mais il utilise l’extension de nom de fichier `.apk`. Quand il est développé, le contenu d’un `.apk` créé par Xamarin.Android peut être consulté comme dans la capture d’écran ci-dessous :

[![Contenu du .apk](multicore-devices-images/00.png)](multicore-devices-images/00.png#lightbox)

Une description rapide du contenu du fichier `.apk` :

- **AndroidManifest.xml** &ndash; C’est le `AndroidManifest.xml` fichier, en format binaire XML.

- **classes.dex** &ndash; Cela contient le code `dex` d’application, compilé dans le format de fichier qui est utilisé par le VM Android runtime.

- **resources.arsc** &ndash; Ce fichier contient toutes les ressources précompilées pour l’application.

- **LIB** &ndash; Ce répertoire contient le code compilé pour chaque ABI. Il contient un sous-dossier pour chaque ABI qui a été décrite dans la section précédente. Dans la capture d’écran ci-dessus, le fichier `.apk` en question a des bibliothèques natives pour `armeabi-v7a` et `x86`.

- **META-INF** &ndash; Ce répertoire (si présent) est utilisé pour stocker des informations de signature, un package et des données de configuration d’extension.

- **res** &ndash; Ce répertoire contient les ressources qui n’ont pas été compilées dans `resources.arsc`.

> [!NOTE]
> Le fichier `libmonodroid.so` est la bibliothèque native nécessaire pour toutes les applications Xamarin.Android.

#### <a name="android-device-abi-support"></a>Prise en charge des ABI par les appareils Android

Chaque appareil Android prend en charge l’exécution de code natif dans jusqu'à deux ABI :

- **Le "primaire" ABI** &ndash; Cela correspond au code machine utilisé dans l’image du système.

- **Une ABI « secondaire »** &ndash; Il s’agit d’un message ABI facultatif qui est également pris en charge par l’image système.

Par exemple, un appareil ARMv5TE typique n’a qu’une ABI principale de `armeabi`, tandis qu’un appareil ARMv7 spécifiera une ABI principale de `armeabi-v7a` et une secondaire de `armeabi`. Un appareil x86 typique spécifie uniquement une ABI principale de `x86`.

### <a name="android-native-library-installation"></a>Installation de la bibliothèque native Android

Au moment de l’installation du package, les bibliothèques natives dans les `.apk` sont extraites dans le répertoire des bibliothèques natives de l’application, généralement `/data/data/<package-name>/lib`, et sont ensuite appelées `$APP/lib`.

Le comportement d’installation des bibliothèques natives Android varie considérablement entre les versions d’Android.

#### <a name="installing-native-libraries-pre-android-40"></a>Installation des bibliothèques natives : Android avant la version 4.0

Les versions antérieures à Android 4.0 Ice Cream Sandwich extraient uniquement les bibliothèques natives à partir d’une *ABI unique* dans le `.apk`. Les applications Android de cette période tentent d’abord d’extraire toutes les bibliothèques natives pour l’ABI principale, et si aucune de ces bibliothèques n’existe, Android extrait ensuite toutes les bibliothèques natives pour l’ABI secondaire. Aucune « fusion » n’est effectuée.

Par exemple, prenons le cas où une application est installée sur un appareil `armeabi-v7a`. Le fichier `.apk,` qui prend en charge `armeabi` et `armeabi-v7a`, a les répertoires `lib` et fichiers d’ABI suivants :

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Après l’installation, le répertoire de bibliothèques natives contiendra :

```shell
$APP/lib/libtwo.so # from the armeabi-v7a directory in the apk
```

En d’autres termes, aucun `libone.so` n’est installé. Cela entraîne des problèmes, car `libone.so` n’est pas présent pour que l’application le charge au moment de l’exécution. Ce comportement, bien qu’inattendu, a été enregistré comme un bogue et reclassifié sous « [Fonctionne comme prévu](https://code.google.com/p/android/issues/detail?id=9089). »

Par conséquent, lorsque vous ciblez des versions d’Android antérieures à 4.0, il est nécessaire de fournir *toutes* les bibliothèques natives pour *chaque* ABI que l’application prendra en charge. Autrement dit, `.apk` doit contenir :

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libone.so
lib/armeabi-v7a/libtwo.so
```

#### <a name="installing-native-libraries-android-40-ndash-android-403"></a>Installation des bibliothèques natives : Android 4.0 &ndash; Android 4.0.3

Android 4.0 Ice Cream Sandwich modifie la logique d’extraction. Il énumère toutes les bibliothèques natives, vérifie si le nom de base du fichier a déjà été extrait. Puis, si les deux conditions suivantes sont remplies, la bibliothèque est extraite :

- Elle n’a pas encore été extraite.

- L’ABI de la bibliothèque native correspond à l’ABI principale ou secondaire de la cible.

La validation de ces conditions permet un comportement de « fusion ». Autrement dit, si nous avons un `.apk` avec le contenu suivant :

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Alors après l’installation, le répertoire de bibliothèques natives contient :

```shell
$APP/lib/libone.so
$APP/lib/libtwo.so
```

Malheureusement, ce comportement repose sur l’ordre, comme décrit dans le document suivant - [Problème 24321 : Galaxy Nexus 4.0.2 utilise le code natif armeabi lorsque armeabi et armeabi-v7a sont tous deux inclus dans l’apk](https://code.google.com/p/android/issues/detail?id=25321).

Les bibliothèques natives sont traitées « dans l’ordre » (celui indiqué par, par exemple, unzip) et le *première correspondance* est extraite. Étant donné que le fichier `.apk` contient les versions `armeabi` et `armeabi-v7a` de `libtwo.so` et que `armeabi` est répertorié en premier, c’est la version `armeabi` qui est extraite, et *pas*`armeabi-v7a` :

```shell
$APP/lib/libone.so # armeabi
$APP/lib/libtwo.so # armeabi, NOT armeabi-v7a!
```

En outre, même si les deux ABI `armeabi` et `armeabi-v7a` sont spécifiées (comme décrit ci-dessous dans la section *Déclaration des ABI prises en charge*), Xamarin.Android crée l’élément suivant.
`csproj`:

```xml
<AndroidSupportedAbis>armeabi,armeabi-v7a</AndroidSupportedAbis>
```

Par conséquent, `armeabi` `libmonodroid.so` le sera trouvé `.apk`d’abord dans le , et `armeabi` `libmonodroid.so` le `armeabi-v7a` `libmonodroid.so` sera celui qui est extrait, même si le est présent et optimisé pour la cible. Cela peut également entraîner des erreurs d’exécution obscures, car `armeabi` n’est pas SMP-safe.

##### <a name="installing-native-libraries-android-404-and-later"></a>Installation des bibliothèques natives : Android 4.0.4 et versions ultérieures

Android 4.0.4 modifie la logique d’extraction : il énumère toutes les bibliothèques natives, lit le nom de base du fichier, puis extrait la version ABI principale (le cas échéant), ou l’ABI secondaire (le cas échéant). Cela permet un comportement de « fusion ». Autrement dit, si nous avons un `.apk` avec le contenu suivant :

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Alors après l’installation, le répertoire de bibliothèques natives contient :

```shell
$APP/lib/libone.so # from armeabi
$APP/lib/libtwo.so # from armeabi-v7a
```

### <a name="xamarinandroid-and-abis"></a>Xamarin.Android et les ABI

Xamarin.Android prend en charge les architectures _64 bits_ suivantes :

- `arm64-v8a`
- `x86_64`

> [!NOTE]
> Depuis août 2018, les nouvelles applications doivent cibler l’API de niveau 26, et à partir d’août 2019, les applications devront [fournir des versions 64 bits](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) en plus des versions 32 bits.

Xamarin.Android prend en charge les architectures 32 bits suivantes :

- `armeabi` ^
- `armeabi-v7a`
- `x86`

> [!NOTE]
> **^** En date de [Xamarin.Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` n’est plus pris en charge.

Xamarin.Android ne fournit pas actuellement la prise en charge de `mips`.

### <a name="declaring-supported-abis"></a>Déclaration des ABI prises en charge

Par défaut, Xamarin.Android utilise `armeabi-v7a` pour les versions de **Production**, et `armeabi-v7a` et `x86` pour les versions de **Débogage**. Vous pouvez définir la prise en charge d’ABI différentes via les Options de projet pour un projet Xamarin.Android. Dans Visual Studio, cela peut être défini dans le page **Options Android** des **Propriétés** du projet, sous l’onglet **Avancé**, comme indiqué dans la capture d’écran suivante :

![Propriétés avancées des options Android](multicore-devices-images/vs-abi-selections.png)

Dans Visual Studio pour Mac, les architectures prises en charge peuvent être sélectionnées dans la page **Build Android** des **Options du projet**, sous l’onglet **Avancé**, comme indiqué dans la capture d’écran suivante :

[![Android Build ATI soutenu](multicore-devices-images/xs-abi-selections-sml.png)](multicore-devices-images/xs-abi-selections.png#lightbox)

Il existe certaines situations où il peut être nécessaire de déclarer une prise en charge supplémentaire d’ABI, par exemple lorsque :

- Vous déployez l’application sur un appareil `x86`.

- Vous déployez l’application sur un appareil `armeabi-v7a` pour garantir la sécurité des threads.

## <a name="summary"></a>Récapitulatif

Ce document décrit les différentes architectures d’UC qui peuvent être employées pour une application Android. Il présentait l’interface binaire d’application et comment elle est utilisée par Android pour prendre en charge différentes architectures d’UC.
Il expliquait ensuite comment spécifier la prise en charge des ABI dans une application Xamarin.Android et soulignait les problèmes qui surviennent lors de l’utilisation des applications Xamarin.Android uniquement destinées à `armeabi` sur un appareil `armeabi-v7a`.

## <a name="related-links"></a>Liens connexes

- [Kit de développement natif (NDK) Android](https://developer.android.com/tools/sdk/ndk/index.html)
- [Problème 9089 :Nexus One - AUCUNE bibliothèque native n’est chargée à partir d’armeabi s’il existe au moins une bibliothèque armeabi-v7a](https://code.google.com/p/android/issues/detail?id=9089)
- [Problème 24321 : Galaxy Nexus 4.0.2 utilise le code natif armeabi lorsque armeabi et armeabi-v7a sont tous deux inclus dans l’apk](https://code.google.com/p/android/issues/detail?id=25321)
