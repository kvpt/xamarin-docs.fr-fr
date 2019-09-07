---
title: Accès aux fichiers sur le stockage externe avec Xamarin. Android
description: Ce guide aborde l’accès aux fichiers sur le stockage externe dans Xamarin. Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: e30f726cfb783fc47bc09f7590a523eb0e487105
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756517"
---
# <a name="external-storage"></a>Stockage externe

Le stockage externe fait référence au stockage de fichiers qui ne se trouve pas sur le stockage interne et qui n’est pas exclusivement accessible à l’application responsable du fichier. L’objectif principal du stockage externe est de fournir un emplacement pour placer les fichiers destinés à être partagés entre les applications ou qui sont trop volumineux pour tenir sur le stockage interne.

Historiquement parlant, le stockage externe faisait appel à une partition de disque sur un support amovible, tel qu’une carte SD (également appelée _stockage portable_). Cette distinction n’est plus pertinente, car les appareils Android ont évolué et de nombreux appareils Android ne prennent plus en charge le stockage amovible. Au lieu de cela, certains appareils allouent une partie de leur mémoire non volatile interne qu’Android doit exécuter le même support amovible de fonction. C’est ce qu’on appelle le stockage _émulé_ et est toujours considéré comme un stockage externe. Certains appareils Android peuvent également avoir plusieurs partitions de stockage externe. Par exemple, une tablette Android (en plus de son stockage interne) peut avoir un espace de stockage émulé et un ou plusieurs emplacements pour une carte SD. Toutes ces partitions sont traitées par Android comme stockage externe.

Sur les appareils qui ont plusieurs utilisateurs, chaque utilisateur aura un répertoire dédié sur la partition de stockage externe principale pour son stockage externe. Les applications qui s’exécutent en tant qu’utilisateur n’ont pas accès aux fichiers d’un autre utilisateur sur l’appareil. Les fichiers de tous les utilisateurs sont toujours lisibles et accessibles dans le monde entier. Toutefois, Android met en sandbox chaque profil utilisateur des autres.

La lecture et l’écriture dans des fichiers sont presque identiques dans Xamarin. Android, comme c’est le cas pour toute autre application .NET. L’application Xamarin. Android détermine le chemin d’accès au fichier qui sera manipulé, puis utilise les idiomes .NET standard pour l’accès aux fichiers. Étant donné que les chemins d’accès réels au stockage interne et externe peuvent varier d’un appareil à l’appareil ou d’une version Android à une version Android, il n’est pas recommandé de coder en dur le chemin d’accès aux fichiers. Au lieu de cela, Xamarin. Android expose les API Android natives qui vous aideront à déterminer le chemin d’accès aux fichiers sur le stockage interne et externe.

Ce guide aborde les concepts et les API d’Android qui sont spécifiques au stockage externe.

## <a name="public-and-private-files-on-external-storage"></a>Fichiers publics et privés sur un stockage externe

Une application peut conserver deux types de fichiers différents sur un stockage externe :

* **Fichiers privés les** fichiers privéssontdesfichiersquisontspécifiquesàvotreapplication(maissonttoujourslisiblesdanslemondeentieretaccessiblesaumonde).&ndash; Android s’attend à ce que les fichiers privés soient stockés dans un répertoire spécifique sur le stockage externe. Bien que les fichiers soient appelés « privés », ils sont toujours visibles et accessibles par d’autres applications sur l’appareil, mais ils ne bénéficient pas d’une protection spéciale par Android.

* Fichiers&ndash; publics il s’agit de fichiers qui ne sont pas considérés comme spécifiques à l’application et qui sont destinés à être partagés librement.

Les différences entre ces fichiers sont principalement conceptuelles. Les fichiers privés sont privés dans le sens où ils sont considérés comme faisant partie de l’application, tandis que les fichiers publics sont tous les autres fichiers qui existent sur le stockage externe. Android fournit deux API différentes pour résoudre les chemins d’accès aux fichiers privés et publics, mais dans le cas contraire, les mêmes API .NET sont utilisées pour lire et écrire dans ces fichiers. Il s’agit des mêmes API que celles décrites dans la section relative à la [lecture et](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)à l’écriture.

### <a name="private-external-files"></a>Fichiers externes privés

Les fichiers externes privés sont considérés comme spécifiques à une application (similaire aux fichiers internes), mais ils sont conservés sur le stockage externe pour plusieurs raisons (par exemple, trop volumineuses pour le stockage interne). À l’instar des fichiers internes, ces fichiers sont supprimés lorsque l’application est désinstallée par l’utilisateur.

L’emplacement principal des fichiers externes privés est trouvé en appelant la méthode `Android.Content.Context.GetExternalFilesDir(string type)`. Cette méthode retourne un `Java.IO.File` objet qui représente le répertoire de stockage externe privé de l’application. Le `null` passage à cette méthode retourne le chemin d’accès au répertoire de stockage de l’utilisateur pour l’application. Par exemple, pour une application avec le nom `com.companyname.app`du package, le répertoire « racine » des fichiers externes privés serait :

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Ce document fait référence au répertoire de stockage des fichiers privés sur le stockage externe en tant que _\_stockage externe\_privé_.

Le paramètre pour `GetExternalFilesDir()` est une chaîne qui spécifie un _répertoire d’application_. Il s’agit d’un répertoire destiné à fournir un emplacement standard pour une organisation logique de fichiers. Les valeurs de chaîne sont disponibles via des constantes `Android.OS.Environment` de la classe :

| `Android.OS.Environment` | Répertoire |
|-|-|
| DirectoryAlarms | **_Stockage\_externe\_privé_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_Stockage\_externe\_privé_/Download** |
| DirectoryDocuments | **_Stockage\_externe\_privé_/documents** |
| DirectoryMovies | **_Stockage\_externe\_privé_/movies** |
| DirectoryMusic | **_Stockage\_externe\_privé_/Music** |
| DirectoryNotifications | **_Stockage\_externe\_privé_/notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_Stockage\_externe\_privé_/Ringtones** |
| DirectoryPictures | **_Stockage\_externe\_privé_/Pictures** |

Pour les appareils qui ont plusieurs partitions de stockage externe, chaque partition aura un répertoire destiné aux fichiers privés. La méthode `Android.Content.Context.GetExternalFilesDirs(string type)` retourne un tableau de `Java.IO.Files`. Chaque objet représente un répertoire privé propre à l’application sur tous les périphériques de stockage partagés/externes, où l’application peut placer les fichiers qu’elle possède.

> [!IMPORTANT]
> Le chemin d’accès exact vers le répertoire de stockage privé privé peut varier d’un appareil à l’autre et entre les versions d’Android. Pour cette raison, les applications ne doivent pas coder en dur le chemin d’accès à ce répertoire et utiliser à la place les API `Android.Content.Context.GetExternalFilesDir()`Xamarin. Android, telles que.

### <a name="public-external-files"></a>Fichiers externes publics

Les fichiers publics sont des fichiers qui existent sur un stockage externe qui ne sont pas stockés dans l’annuaire alloué par Android pour les fichiers privés. Les fichiers publics ne sont pas supprimés lorsque l’application est désinstallée. Les applications Android doivent disposer d’une autorisation pour pouvoir lire ou écrire des fichiers publics. Il est possible que les fichiers publics existent n’importe où sur le stockage externe, mais par convention Android s’attend à ce que les fichiers publics existent dans `Android.OS.Environment.ExternalStorageDirectory`le répertoire identifié par la propriété. Cette propriété retourne un `Java.IO.File` objet qui représente le répertoire principal de stockage externe. Par exemple, `Android.OS.Environment.ExternalStorageDirectory` peut faire référence au répertoire suivant :

```bash
/storage/emulated/0/
```

Ce document fait référence au répertoire de stockage des fichiers publics sur le stockage externe en tant que _\_stockage externe\_public_.

Android prend également en charge le concept de répertoires d’application sur le _\_stockage externe\_public_. Ces répertoires sont exactement les mêmes que les répertoires `_PRIVATE\_EXTERNAL\_STORAGE_` d’application pour et sont décrits dans le tableau de la section précédente. La méthode `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` retourne un `Java.IO.File` objet qui correspond à un répertoire d’application public. Le `directoryType` paramètre est un paramètre obligatoire et ne peut `null`pas être.

Par exemple, l' `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` appel de retourne une chaîne qui ressemble à ce qui suit :

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> Le chemin d’accès exact au répertoire de stockage externe public peut varier d’un appareil à l’autre et entre les versions d’Android. Pour cette raison, les applications ne doivent pas coder en dur le chemin d’accès à ce répertoire et utiliser à la place les API `Android.OS.Environment.ExternalStorageDirectory`Xamarin. Android, telles que.

## <a name="working-with-external-storage"></a>Utilisation du stockage externe

Une fois qu’une application Xamarin. Android a obtenu le chemin d’accès complet à un fichier, elle doit utiliser l’une des API .NET standard pour créer, lire, écrire ou supprimer des fichiers. Cela optimise la quantité de code compatible multiplateforme pour une application. Toutefois, avant de tenter d’accéder à un fichier, une application Xamarin. Android doit s’assurer qu’il est possible d’accéder à ce fichier.

1. **Vérifier le stockage externe** &ndash; Selon la nature du stockage externe, il est possible qu’il ne soit pas monté et utilisable par l’application. Toutes les applications doivent vérifier l’état du stockage externe avant de tenter de l’utiliser.
2. **Effectuer une vérification d’autorisation Runtime** &ndash; Une application Android doit demander l’autorisation à l’utilisateur afin d’accéder au stockage externe. Cela signifie qu’une demande d’autorisation au moment de l’exécution doit être exécutée avant tout accès aux fichiers. Les autorisations du guide [dans Xamarin. Android](~/android/app-fundamentals/permissions.md) contiennent plus de détails sur les autorisations Android.

Chacune de ces deux tâches est décrite ci-dessous.

### <a name="verifying-that-external-storage-is-available"></a>Vérification de la disponibilité du stockage externe

La première étape avant d’écrire dans un stockage externe consiste à vérifier qu’elle est accessible en lecture ou en écriture. La `Android.OS.Environment.ExternalStorageState` propriété contient une chaîne qui identifie l’état du stockage externe. Cette propriété retourne une chaîne qui représente l’État. Cette table est une liste des `ExternalStorageState` valeurs qui peuvent être retournées par : `Environment.ExternalStorageState`

| ExternalStorageState | Description  |
|----------------------|---|
| MediaBadRemoval      | Le support a été brusquement supprimé sans avoir été correctement démonté. |
| MediaChecking        | Le média est présent mais subit une vérification du disque.  |
| MediaEjecting        | Le média est en cours de démontage et d’éjection.  |
| MediaMounted         | Le média est monté et peut être lu ou écrit.  |
| MediaMountedReadOnly | Le média est monté mais ne peut être lu qu’à partir de. |
| MediaNofs            | Le support est présent mais ne contient pas de système de fichiers adapté à Android. |
| MediaRemoved         | Aucun support n’est présent. |
| MediaShared          | Le média est présent, mais il n’est pas monté. Il est partagé via USB avec un autre appareil.|
| MediaUnknown         | L’état du support n’est pas reconnu par Android. |
| MediaUnmountable     | Le support est présent mais ne peut pas être monté par Android. |
| MediaUnmounted       | Le média est présent mais n’est pas monté. |

La plupart des applications Android auront uniquement besoin de vérifier si le stockage externe est monté. L’extrait de code suivant montre comment vérifier que le stockage externe est monté pour un accès en lecture seule ou un accès en lecture-écriture :

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Autorisations de stockage externe

Android considère que l’accès au stockage externe est une _autorisation dangereuse_, ce qui oblige généralement l’utilisateur à accorder son autorisation d’accès à la ressource. L’utilisateur peut révoquer cette autorisation à tout moment.  Cela signifie qu’une demande d’autorisation au moment de l’exécution doit être exécutée avant tout accès aux fichiers. Les autorisations sont automatiquement accordées aux applications pour lire et écrire leurs propres fichiers privés. Il est possible pour les applications de lire et d’écrire les fichiers privés qui appartiennent à d’autres applications après avoir [reçu l’autorisation](~/android/app-fundamentals/permissions.md) de l’utilisateur.

Toutes les applications Android doivent déclarer l’une des deux autorisations pour le stockage externe dans le **fichier AndroidManifest. xml** . Pour identifier les autorisations, l’un des deux `uses-permission` éléments suivants doit être ajouté à **fichier AndroidManifest. xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Si l’utilisateur accorde `WRITE_EXTERNAL_STORAGE` `READ_EXTERNAL_STORAGE` , est également accordé implicitement. Il n’est pas nécessaire de demander les deux autorisations dans **fichier AndroidManifest. xml**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Les autorisations peuvent également être ajoutées à l’aide de l’onglet **manifeste Android** des propriétés de la **solution**:

![Explorateur de solutions-autorisations requises pour Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Les autorisations peuvent également être ajoutées à l’aide de l’onglet **manifeste Android** du panneau Propriétés de la **solution**:

[![Panneau Solutions-autorisations requises pour Visual Studio pour Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

En règle générale, toutes les autorisations dangereuses doivent être approuvées par l’utilisateur. Les autorisations pour le stockage externe sont une anomalie en ce qu’il existe des exceptions à cette règle, en fonction de la version d’Android exécutée par l’application :

![Organigramme des vérifications d’autorisations de stockage externe](./images/external-permission-check-flowchart.png)

Pour plus d’informations sur l’exécution des demandes d’autorisation d’exécution, consultez les autorisations du guide [dans Xamarin. Android](~/android/app-fundamentals/permissions.md). L’exemple de [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) **monodroid-Sample** illustre également une manière d’effectuer des vérifications d’autorisations Runtime.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Octroi et révocation d’autorisations avec ADB

Au cours du développement d’une application Android, il peut être nécessaire d’accorder et de révoquer des autorisations pour tester les différents flux de travail impliqués dans les vérifications d’autorisations d’exécution. Vous pouvez le faire à partir de l’invite de commandes à l’aide de ADB. Les extraits de ligne de commande suivants montrent comment accorder ou révoquer des autorisations à l’aide de ADB pour une application Android dont le nom de package est **com. CompanyName. app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Suppression de fichiers

Toutes les API standard C# peuvent être utilisées pour supprimer un fichier du stockage externe, par exemple [`System.IO.File.Delete`](xref:System.IO.File.Delete*). Il est également possible d’utiliser les API Java au détriment de la portabilité du code. Exemple :

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Liens associés

* [Exemple de fichiers locaux Xamarin. Android sur **monodroid-Samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Autorisations dans Xamarin. Android](~/android/app-fundamentals/permissions.md)
