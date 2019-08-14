---
title: 'Xamarin.Essentials: Assistances de système de fichiers'
description: La classe FileSystem de Xamarin.Essentials contient une série de composants d’assistance qui permettent de rechercher les répertoires de cache et de données de l’application, ainsi que d’ouvrir des fichiers contenus dans le paquet d’application.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5b155e4976a67bda36e66d2ca3565c9237fde3c6
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738863"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Assistances de système de fichiers

La classe **FileSystem** contient une série de composants d’assistance qui permettent de rechercher les répertoires de cache et de données de l’application, ainsi que d’ouvrir des fichiers contenus dans le paquet d’application.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-file-system-helpers"></a>Utilisation des composants d’assistance de système de fichiers

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Permet au répertoire de l’application de stocker les **données du cache**. Vous pouvez utiliser les données de cache pour toutes les données à conserver plus longtemps que les données temporaires, car le système d’exploitation dicte le moment où le stockage est effacé.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Permet d’obtenir le répertoire de niveau supérieur de l’application pour tous les fichiers qui ne sont pas des fichiers de données utilisateur. Ces fichiers sont sauvegardés avec le framework de synchronisation du système d’exploitation. Consultez les spécificités d’implémentation en fonction de la plateforme, ci-dessous.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Permet d’ouvrir un fichier en bundle dans le paquet d’application :

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** - Retourne le [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) du contexte actuel.
- **AppDataDirectory** - Retourne le [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) du contexte actuel, qui est sauvegardé via la [sauvegarde automatique](https://developer.android.com/guide/topics/data/autobackup.html) à partir du niveau d’API 23.

Ajoutez un fichier au dossier **Composants** du projet Android, et marquez l’action de génération en tant qu’**AndroidAsset** pour pouvoir l’utiliser avec `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** - Retourne le répertoire [Library/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).
- **AppDataDirectory** - Retourne le répertoire [Library](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) sauvegardé par iTunes et iCloud.

Ajoutez un fichier au dossier **Ressources** du projet iOS, et marquez l’action de génération en tant que **BundledResource** pour pouvoir l’utiliser avec `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** - Retourne le répertoire [LocalCacheFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder).
- **AppDataDirectory** - Retourne le répertoire [LocalFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) sauvegardé dans le cloud.

Ajoutez un fichier à la racine du projet UWP, et marquez l’action de génération en tant que **Content** pour pouvoir l’utiliser avec `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Code source de la fonctionnalité des composants d’assistance de système de fichiers](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentation sur l’API de système de fichiers](xref:Xamarin.Essentials.FileSystem)
