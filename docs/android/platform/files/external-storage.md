---
title: Accès au fichier sur le stockage externe avec Xamarin.Android
description: Ce guide discutera de l’accès aux fichiers sur le stockage externe dans Xamarin.Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020483"
---
# <a name="external-storage"></a>Stockage externe

Le stockage externe désigne le stockage de fichiers qui n’est pas sur le stockage interne et qui n’est pas exclusivement accessible à l’application responsable du fichier. Le but principal du stockage externe est de fournir un endroit pour mettre des fichiers qui sont censés être partagés entre les applications ou qui sont trop grands pour s’adapter sur le stockage interne.

Historiquement parlant, le stockage externe se référait à une partition de disque sur des supports amovibles tels qu’une carte SD (était également connu sous le nom _de stockage portable_). Cette distinction n’est plus aussi pertinente que les appareils Android ont évolué et de nombreux appareils Android ne prennent plus en charge le stockage amovible. Au lieu de cela, certains appareils alloueront une partie de leur mémoire interne non volatile qui Android pour effectuer la même fonction support amovible. Ceci est connu sous le nom de stockage _imité_ et est toujours considéré comme un stockage externe. Alternativement, certains appareils Android peuvent avoir plusieurs cloisons de stockage externes. Par exemple, une tablette Android (en plus de son stockage interne) peut avoir imité le stockage et une ou plusieurs fentes pour une carte SD. Toutes ces partitions sont traitées par Android comme un stockage externe.

Sur les appareils qui ont plusieurs utilisateurs, chaque utilisateur aura un répertoire dédié sur la partition de stockage externe primaire pour leur stockage externe. Les applications fonctionnant comme un utilisateur n’auront pas accès aux fichiers d’un autre utilisateur sur l’appareil. Les fichiers pour tous les utilisateurs sont encore lisibles dans le monde et écrivant dans le monde entier; cependant, Android va sandbox chaque profil d’utilisateur des autres.

La lecture et l’écriture de fichiers est presque identique dans Xamarin.Android comme il est à toute autre application .NET. L’application Xamarin.Android détermine le chemin vers le fichier qui sera manipulé, puis utilise des idiomes .NET standard pour l’accès aux fichiers. Étant donné que les chemins réels vers le stockage interne et externe peuvent varier d’un appareil à l’autre ou de la version Android à la version Android, il n’est pas recommandé de coder dur le chemin vers les fichiers. Au lieu de cela, Xamarin.Android expose les API Android natifs qui aideront à déterminer le chemin vers les fichiers sur le stockage interne et externe.

Ce guide discutera des concepts et des API dans Android qui sont spécifiques au stockage externe.

## <a name="public-and-private-files-on-external-storage"></a>Fichiers publics et privés sur le stockage externe

Il existe deux types différents de fichiers qu’une application peut conserver sur le stockage externe :

* **Fichiers privés** &ndash; Les fichiers privés sont des fichiers spécifiques à votre application (mais qui sont toujours lisibles dans le monde entier et qui sont réalisables dans le monde). Android s’attend à ce que les fichiers privés soient stockés dans un répertoire spécifique sur le stockage externe. Même si les fichiers sont appelés «privés», ils sont toujours visibles et accessibles par d’autres applications sur l’appareil, ils ne sont pas accordés une protection spéciale par Android.

* **Dossiers** &ndash; publics Ce sont des dossiers qui ne sont pas considérés comme spécifiques à l’application et qui sont censés être librement partagés.

Les différences entre ces fichiers sont principalement conceptuelles. Les fichiers privés sont privés en ce sens qu’ils sont considérés comme faisant partie de l’application, tandis que les fichiers publics sont tous les autres fichiers qui existent sur le stockage externe. Android fournit deux API différentes pour résoudre les voies vers les fichiers privés et publics, mais sinon les mêmes API .NET sont utilisés pour lire et écrire à ces fichiers. Ce sont les mêmes API qui sont discutés dans la section sur [la lecture et l’écriture](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>Fichiers externes privés

Les fichiers externes privés sont considérés comme spécifiques à une application (semblable aux fichiers internes) mais sont conservés sur le stockage externe pour un certain nombre de raisons (comme être trop grand pour le stockage interne). Semblable aux fichiers internes, ces fichiers seront supprimés lorsque l’application n’est pas bloquée par l’utilisateur.

L’emplacement principal pour les fichiers externes `Android.Content.Context.GetExternalFilesDir(string type)`privés est trouvé en appelant la méthode . Cette méthode retournera un `Java.IO.File` objet qui représente l’annuaire de stockage externe privé pour l’application. Passer `null` à cette méthode retournera le chemin vers l’annuaire de stockage de l’utilisateur pour l’application. Par exemple, pour une application `com.companyname.app`avec le nom du paquet, l’annuaire « racine » des fichiers externes privés serait :

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Ce document fera référence à l’annuaire de stockage pour les fichiers privés sur le stockage externe comme _PRIVATE\_EXTERNAL\_STORAGE_.

Le paramètre pour `GetExternalFilesDir()` est une chaîne qui spécifie un répertoire _d’application_. Il s’agit d’un répertoire destiné à fournir un emplacement standard pour une organisation logique des fichiers. Les valeurs de cordes sont `Android.OS.Environment` disponibles à travers des constantes sur la classe :

| `Android.OS.Environment` | Répertoire |
|-|-|
| AnnuaireAlarms | **_PRIVATE\_\_EXTERNAL STORAGE_/Alarmes** |
| DirectoryDcim (en) | **_STOCKAGE\_\_EXTERNE PRIVÉ_/DCIM** |
| DirectoryDownloads (en) | **_PRIVATE\_\_EXTERNAL STORAGE_/Télécharger** |
| DirectoryDocuments | **_PRIVATE\_\_EXTERNAL STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_\_EXTERNAL STORAGE_/Films** |
| DirectoryMusic | **_PRIVATE\_\_EXTERNAL STORAGE_/Musique** |
| DirectoryNotifications | **_PRIVATE\_\_EXTERNAL STORAGE_/Notifications** |
| DirectoryPodcasts (en anglais seulement) | **_PRIVATE\_\_EXTERNAL STORAGE_/Podcasts** |
| Annuaires | **_PRIVATE\_\_EXTERNAL STORAGE_/Sonneries** |
| DirectoryPictures | **_PRIVATE\_\_EXTERNAL STORAGE_/Photos** |

Pour les appareils qui ont plusieurs partitions de stockage externes, chaque partition aura un répertoire destiné aux fichiers privés. La `Android.Content.Context.GetExternalFilesDirs(string type)` méthode retournera `Java.IO.Files`un tableau de . Chaque objet représentera un répertoire privé spécifique à l’application sur tous les périphériques de stockage partagés/externes où l’application peut placer les fichiers qu’il possède.

> [!IMPORTANT]
> Le chemin exact vers l’annuaire de stockage externe privé peut varier d’un appareil à l’autre et entre les versions d’Android. Pour cette raison, les applications ne doivent pas coder dur le chemin vers ce `Android.Content.Context.GetExternalFilesDir()`répertoire, et à la place utiliser les API Xamarin.Android, tels que .

### <a name="public-external-files"></a>Fichiers externes publics

Les fichiers publics sont des fichiers qui existent sur le stockage externe qui ne sont pas stockés dans l’annuaire qu’Android alloue pour les fichiers privés. Les fichiers publics ne seront pas supprimés lorsque l’application n’est pas bloquée. Les applications Android doivent être autorisées avant de pouvoir lire ou écrire des fichiers publics. Il est possible pour les fichiers publics d’exister n’importe où sur le stockage `Android.OS.Environment.ExternalStorageDirectory`externe, mais par convention Android s’attend à ce que les fichiers publics existent dans l’annuaire identifié par la propriété . Cette propriété retournera un `Java.IO.File` objet qui représente le répertoire de stockage externe principal. À titre `Android.OS.Environment.ExternalStorageDirectory` d’exemple, peut se référer à l’annuaire suivant :

```bash
/storage/emulated/0/
```

Ce document fera référence à l’annuaire de stockage pour les fichiers publics sur le stockage externe comme _PUBLIC\_EXTERNAL\_STORAGE_.

Android prend également en charge le concept d’annuaires d’applications sur _PUBLIC\_EXTERNAL\_STORAGE_. Ces répertoires sont exactement les mêmes `PRIVATE_EXTERNAL_STORAGE` que les répertoires d’applications pour et sont décrits dans le tableau dans la section précédente. La `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` méthode retournera un `Java.IO.File` objet qui correspond à un répertoire de demande publique. Le `directoryType` paramètre est un `null`paramètre obligatoire et ne peut pas être .

Par exemple, `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` l’appel rendra une chaîne qui ressemblera à :

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> Le chemin exact vers l’annuaire de stockage externe public peut varier d’un appareil à l’autre et entre les versions d’Android. Pour cette raison, les applications ne doivent pas coder dur le chemin vers ce `Android.OS.Environment.ExternalStorageDirectory`répertoire, et à la place utiliser les API Xamarin.Android, tels que .

## <a name="working-with-external-storage"></a>Travailler avec le stockage externe

Une fois qu’une application Xamarin.Android a obtenu le chemin complet vers un fichier, il devrait utiliser l’un des API .NET standard pour créer, lire, écrire ou supprimer des fichiers. Cela maximise la quantité de code compatible plate-forme croisée pour une application. Toutefois, avant de tenter d’accéder à un fichier, une application Xamarin.Android doit s’assurer qu’il est possible d’accéder à ce fichier.

1. **Vérifier** &ndash; le stockage externe Selon la nature du stockage externe, il est possible qu’il ne soit pas monté et utilisable par l’application. Toutes les applications doivent vérifier l’état du stockage externe avant de tenter de l’utiliser.
2. Effectuer une &ndash; **vérification d’autorisation de temps d’exécution** Une application Android doit demander la permission de l’utilisateur afin d’accéder au stockage externe. Cela signifie qu’une demande d’autorisation de temps d’exécution doit être effectuée avant tout accès au fichier. Le guide [Permissions In Xamarin.Android](~/android/app-fundamentals/permissions.md) contient plus de détails sur les autorisations Android.

Chacune de ces deux tâches sera discutée ci-dessous.

### <a name="verifying-that-external-storage-is-available"></a>Vérifier que le stockage externe est disponible

La première étape avant d’écrire au stockage externe est de vérifier qu’il est lisible ou réprésasible. La `Android.OS.Environment.ExternalStorageState` propriété contient une chaîne qui identifie l’état du stockage externe. Cette propriété rendra une chaîne qui représente l’État. Ce tableau est une `ExternalStorageState` liste des valeurs `Environment.ExternalStorageState`qui pourraient être retournées par :

| ExternalStorageState (en) | Description  |
|----------------------|---|
| MédiasBadRemoval      | Les médias ont été brusquement retirés sans être correctement démontés. |
| MediaChecking (en)        | Les médias sont présents mais font l’objet d’une vérification du disque.  |
| Média-Éjectage        | Les médias sont en train d’être démontés et éjectés.  |
| MediaMounted (en)         | Les médias sont montés et peuvent être lus ou écrits.  |
| MediaMountedReadOnly | Les médias sont montés mais ne peuvent être lus qu’à partir. |
| MediaNofs (en)            | Les médias sont présents mais ne contiennent pas de système de fichiers adapté à Android. |
| MediaRemoved (en)         | Il n’y a pas de médias présents. |
| MediaShared (en)          | Les médias sont présents, mais ne sont pas montés. Il est partagé via USB avec un autre appareil.|
| MediaUnknown (en)         | L’état des médias n’est pas reconnu par Android. |
| MédiasUnmontables     | Les médias sont présents mais ne peuvent pas être montés par Android. |
| MédiasUnmounted       | Les médias sont présents mais ne sont pas montés. |

La plupart des applications Android n’auront besoin que de vérifier si le stockage externe est monté. L’extrait de code suivant montre comment vérifier que le stockage externe est monté pour l’accès à la lecture ou l’écriture :

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Autorisations de stockage externes

Android considère l’accès au stockage externe comme une _autorisation dangereuse,_ ce qui exige généralement de l’utilisateur d’accorder sa permission d’accéder à la ressource. L’utilisateur peut révoquer cette autorisation à tout moment.  Cela signifie qu’une demande d’autorisation de temps d’exécution doit être effectuée avant tout accès au fichier. Les applications sont automatiquement autorisées à lire et à écrire leurs propres fichiers privés. Il est possible pour les applications de lire et d’écrire les fichiers privés qui appartiennent à d’autres applications après avoir [obtenu l’autorisation](~/android/app-fundamentals/permissions.md) de l’utilisateur.

Toutes les applications Android doivent déclarer l’une des deux autorisations de stockage externe dans **l’AndroidManifest.xml** . Pour identifier les autorisations, l’un des deux `uses-permission` éléments suivants doit être ajouté à **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Si l’utilisateur `WRITE_EXTERNAL_STORAGE` `READ_EXTERNAL_STORAGE` accorde , alors est également implicitement accordée. Il n’est pas nécessaire de demander les deux autorisations dans **AndroidManifest.xml**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Les autorisations peuvent également être ajoutées à l’aide de l’onglet **Android Manifest** des **propriétés**de la solution :

![Solution Explorer - Autorisations requises pour Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Les autorisations peuvent également être ajoutées à l’aide de l’onglet **Android Manifest** des **propriétés**de la solution :

[![Solution Pad - Autorisations requises pour Visual Studio pour Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

En règle générale, toutes les autorisations dangereuses doivent être approuvées par l’utilisateur. Les autorisations de stockage externe sont une anomalie en ce qu’il ya des exceptions à cette règle, en fonction de la version d’Android que l’application est en cours d’exécution:

![Flowchart des contrôles externes d’autorisation de stockage](./images/external-permission-check-flowchart.png)

Pour plus d’informations sur l’exécution des demandes d’autorisation de temps d’exécution, s’il vous plaît consulter le guide [Autorisations in Xamarin.Android](~/android/app-fundamentals/permissions.md). **L’échantillon monodroïde** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) démontre également une façon d’effectuer des vérifications d’autorisation de temps d’exécution.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Accorder et révoquer les autorisations avec la BAD

Dans le cadre du développement d’une application Android, il peut être nécessaire d’accorder et de révoquer les autorisations de tester les différents flux de travail impliqués dans les contrôles d’autorisation de temps d’exécution. Il est possible de le faire à l’invitation de commande à l’aide de la BAD. Les extraits de ligne de commande suivants démontrent comment accorder ou révoquer les autorisations à l’aide d’ADB pour une application Android dont le nom de paquet est **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Suppression des fichiers

N’importe lequel des API C standard peuvent être utilisés [`System.IO.File.Delete`](xref:System.IO.File.Delete*)pour supprimer un fichier du stockage externe, tel que . Il est également possible d’utiliser les API Java au détriment de la portabilité du code. Par exemple :

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Liens connexes

* [Xamarin.Android Local Files échantillon sur **monodroid-échantillons**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permissions dans Xamarin.Android](~/android/app-fundamentals/permissions.md)
