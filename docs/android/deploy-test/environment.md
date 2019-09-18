---
title: Environnement de Xamarin.Android
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 076e6bfd1155c0eba4045af59a599a34b0fca1d5
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021179"
---
# <a name="xamarinandroid-environment"></a>Environnement de Xamarin.Android

## <a name="execution-environment"></a>Environnement d'exécution

*L’environnement d’exécution* correspond à l’ensemble de variables d’environnement et de propriétés système Android qui influencent l’exécution du programme. Les propriétés système Android peuvent être définies avec la commande `adb shell setprop`, tandis que les variables d’environnement peuvent être définies en définissant la propriété systèm `debug.mono.env`  :

```shell
## Enable GREF logging
adb shell setprop debug.mono.log gref

## Set the MONO_LOG_LEVEL and MONO_LOG_MASK environment variables
## so that additional Mono messages will be written to `adb logcat`.
adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
```

les propriétés système Android sont définies pour tous les processus sur l’appareil cible.

À partir de Xamarin.Android 4.6, les propriétés système et les variables d’environnement peuvent être définies ou remplacées pour chaque application en ajoutant un *fichier d’environnement* au projet. Un fichier d’environnement est un fichier texte brut au format Unix avec une [ **action de génération** de `AndroidEnvironment` ](~/android/deploy-test/building-apps/build-process.md).
Le fichier d’environnement contient des lignes avec le format *key=value*.
Les commentaires sont des lignes qui commencent par `#`. Les lignes vides sont ignorées.

Si *clé* commence par une lettre majuscule, *clé* est traité comme une variable d’environnement, et **setenv**(3) est utilisée pour définir la variable d’environnement pour la *valeur* spécifiée lors du démarrage du processus.

Si la *clé* commence par une lettre en minuscules, la *clé* est alors traitée comme une propriété système Android et la *valeur* est la *valeur par défaut* : les propriétés système Android qui contrôlent le comportement d’exécution de Xamarin.Android sont d’abord recherchées à partir du magasin de propriétés système Android et, si aucune valeur n’est trouvée, la valeur spécifiée dans le fichier d’environnement est utilisée. Ceci permet d’autoriser l’utilisation de `adb shell setprop` pour remplacer des valeurs qui proviennent du fichier d’environnement afin d’établir un diagnostic.

## <a name="xamarinandroid-environment-variables"></a>Variables d’environnement Xamarin.Android

Xamarin.Android prend en charge la variable `XA_HTTP_CLIENT_HANDLER_TYPE`, qui peut être définie via `adb shell setprop debug.mono.env` ou via l’action de génération `$(AndroidEnvironment)`.

### `XA_HTTP_CLIENT_HANDLER_TYPE`

Le type qualifié d’assembly qui doit hériter de [HttpMessageHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpmessagehandler?view=xamarinandroid-7.1) est construit à partir du [ `HttpClient()` constructeur par défaut](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.-ctor?view=xamarinandroid-7.1#System_Net_Http_HttpClient__ctor).

Dans Xamarin.Android 6.1, cette variable d’environnement n’est pas définie par défaut, et [HttpClientHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpclienthandler?view=xamarinandroid-7.1) sera utilisée.

Vous pouvez également spécifier la valeur `Xamarin.Android.Net.AndroidClientHandler` pour utiliser [`java.net.URLConnection`](xref:Java.Net.URLConnection)
pour l’accès réseau, ce qui *peut* permettre d’employer le protocole TLS 1.2, quand Android le prend en charge.

Ajouté dans Xamarin.Android 6.1.

## <a name="xamarinandroid-system-properties"></a>Propriétés système de Xamarin.Android

Xamarin.Android prend en charge les propriétés système suivantes, qui peuvent être définies via `adb shell setprop` ou via l’action de génération `$(AndroidEnvironment)`.

- `debug.mono.debug`
- `debug.mono.env`
- `debug.mono.gc`
- `debug.mono.log`
- `debug.mono.max_grefc`
- `debug.mono.profile`
- `debug.mono.runtime_args`
- `debug.mono.trace`
- `debug.mono.wref`
- `XA_HTTP_CLIENT_HANDLER_TYPE`

### `debug.mono.debug`

La valeur de la propriété système `debug.mono.debug` est un entier. Si c’est `1`, comportez-vous « comme si » le processus avait été démarré avec `mono --debug`.
Généralement, des informations de fichier et de ligne dans l’arborescence des appels de procédure, etc., s’affichent sans nécessiter que l’application soit lancée à partir d’un débogueur.

### `debug.mono.env`

Contient une liste des variables d’environnement séparées par un `|`.

### `debug.mono.gc`

La valeur de la propriété système `debug.mono.debug` est un entier.
Si c’est `1`, les informations GC doivent être journalisées.

Ceci équivaut à ce que la propriété système `debug.mono.log` contienne `gc`.

### `debug.mono.log`

Contrôle les informations supplémentaires que Xamarin.Android enregistrera dans `adb logcat`.
Il s’agit d’une chaîne séparée par des virgules (`,`), qui contient l’une des valeurs suivantes :

- `all`: imprimer *tous* les messages. C’est rarement une bonne idée, car cela inclut les messages `lref`.
- `assembly`: imprimer les fichiers `.apk` et les messages analyse d’assembly.
- `gc`: imprimer les messages associés au GC.
- `gref`: imprimer les messages de référence globale JNI.
- `lref`: imprimer les messages de référence locale JNI.
  > [!NOTE]
  > ceci spammera `adb logcat`vraiment* .
  > Dans Xamarin.Android 5.1, ceci créera également un fichier `.__override__/lrefs.txt`, qui peut être *énorme*.
  > Éviter.
- `timing`: imprimer des informations de minutage de méthode. Cette opération créera également les fichiers `.__override__/methods.txt` et `.__override__/counters.txt`.

### `debug.mono.max_grefc`

La valeur de la propriété système `debug.mono.max_grefc` est un entier.
Elle *remplace* le nombre GREF maximal par défaut détecté pour l’appareil cible.

*Remarque :* c’est utilisable uniquement avec `adb shell setprop
debug.mono.max_grefc`, étant donné que la valeur n’est pas disponible à temps avec un fichier **environment.txt**.

### `debug.mono.profile`

La propriété système `debug.mono.profile` active le profileur.
Elle est équivalente à l’option `mono --profile` et utilise les mêmes valeurs. (Pour plus d’informations, consultez la page man [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)).)

### `debug.mono.runtime_args`

La propriété système `debug.mono.runtime_args` contient des options supplémentaires qui devraient être analysées par **mono**.

### `debug.mono.trace`

La propriété système `debug.mono.trace` active le traçage.
Elle est équivalente à l’option `mono --trace` et utilise les mêmes valeurs. (Pour plus d’informations, consultez la page man [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)).)

D’une manière générale, *n’utilisez pas*. L’utilisation du traçage spammera la sortie `adb logcat`, ralentira considérablement et modifiera le comportement du programme (y compris l’ajout de conditions d’erreur).

Cependant, elle permet *parfois* d’effectuer des recherches supplémentaires...

### `debug.mono.wref`

La propriété système `debug.mono.wref` permet de remplacer le mécanisme à faible référence JNI par défaut détecté. Deux valeurs sont prises en charge :

- `jni`: utilisez les références faibles JNI créées par `JNIEnv::NewWeakGlobalRef()` et détruites par `JNIEnv::DeleteWeakGlobalREf()`.
- `java`: Utilisez des références globales JNI `java.lang.WeakReference` qui référencent des instances.

`java` est utilisé, par défaut, jusqu'à l’API-7 et sur l’API-19 (Kit Kat) où ART est activé. (API-8 a ajouté des références `jni` et ART a *rompu* des références `jni`.)

Cette propriété système est utile pour tester et pour certaines formes d’examens.
*En général*, elle ne doit pas être modifiée.

### <a name="xa_http_client_handler_type"></a>XA\_HTTP\_CLIENT\_HANDLER\_TYPE

Tout d’abord introduite dans Xamarin.Android 6.1, cette variable d’environnement déclare l’implémentation de `HttpMessageHandler` par défaut qui sera utilisée par le `HttpClient`. Par défaut, cette variable n’est pas définie, et Xamarin.Android utilisera le `HttpClientHandler`.

```shell
XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
```

> [!NOTE]
> L’appareil Android sous-jacent doit prendre en charge TLS 1.2.
Android 5.0 et les versions ultérieures prennent en charge TLS 1.2

## <a name="example"></a>Exemple

```shell
## Comments are lines which start with '#'
## Blank lines are ignored.

## Enable GREF messages to `adb logcat`
debug.mono.log=gref

## Clear out a Mono environment variable to decrease logging
MONO_LOG_LEVEL=
```

## <a name="related-links"></a>Liens associés

- [Sécurité de la couche de transport](~/cross-platform/app-fundamentals/transport-layer-security.md)
