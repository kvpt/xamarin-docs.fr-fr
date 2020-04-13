---
title: Accès au fichier avec Xamarin.Android
description: Ce guide expliquera l’accès aux fichiers dans Xamarin.Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304407"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Stockage de fichiers et accès avec Xamarin.Android

Une exigence commune pour les &ndash; applications Android est de manipuler des fichiers d’enregistrement d’images, le téléchargement de documents ou l’exportation de données à partager avec d’autres programmes. Android (qui est basé sur Linux) prend en charge cela en fournissant de l’espace pour le stockage de fichiers. Android regroupe le système de fichiers en deux types de stockage différents :

* **Stockage** &ndash; interne il s’agit d’une partie du système de fichiers qui ne peut être consulté que par l’application ou le système d’exploitation.
* **Stockage** &ndash; externe il s’agit d’une partition pour le stockage de fichiers qui est accessible par toutes les applications, l’utilisateur, et peut-être d’autres appareils. Sur certains appareils, le stockage externe peut être amovible (comme une carte SD).

Ces groupes sont conceptuels seulement, et ne se réfèrent pas nécessairement à une seule partition ou répertoire sur l’appareil. Un appareil Android fournira toujours la partition pour le stockage interne et le stockage externe. Il est possible que certains appareils puissent avoir plusieurs partitions qui sont considérées comme un stockage externe. Indépendamment de la partition, les API pour lire, écrire ou créer des fichiers sont les mêmes. Il existe deux ensembles d’API qu’une application Xamarin.Android peut utiliser pour l’accès au fichier :

1. **Les API .NET (fournis par Mono et enveloppés par Xamarin.Android)** &ndash; Il s’agit notamment des [aides système de fichiers](~/essentials/file-system-helpers.md?context=xamarin/android) fournies par [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android). Les API .NET offrent la meilleure compatibilité multiplateforme et, à ce titre, l’accent de ce guide sera mis sur ces API.
1. **Les API d’accès aux fichiers Java (fournis par Java et enveloppés par Xamarin.Android)** &ndash; Java fournit ses propres API pour la lecture et l’écriture de fichiers. Ce sont une alternative tout à fait acceptable aux API .NET, mais sont spécifiques à Android et ne sont pas adaptés pour les applications qui sont destinés à être multi-plateforme.

La lecture et l’écriture de fichiers est presque identique dans Xamarin.Android comme il est à toute autre application .NET. L’application Xamarin.Android détermine le chemin vers le fichier qui sera manipulé, puis utilise des idiomes .NET standard pour l’accès aux fichiers. Étant donné que les chemins réels vers le stockage interne et externe peuvent varier d’un appareil à l’autre ou de la version Android à la version Android, il n’est pas recommandé de coder dur le chemin vers les fichiers. Utilisez plutôt les API Xamarin.Android pour déterminer le chemin vers les fichiers. De cette façon, les API .NET pour la lecture et l’écriture de fichiers expose les API Android natifs qui aideront à déterminer le chemin vers les fichiers sur le stockage interne et externe.

Avant de discuter des API impliquées dans l’accès aux fichiers, il est important de comprendre certains détails entourant le stockage interne et externe. Cette question sera discutée dans la section suivante.

## <a name="internal-vs-external-storage"></a>Stockage interne vs externe

Conceptuellement, le stockage interne &ndash; et le stockage externe sont très similaires, ils sont les deux endroits où une application Xamarin.Android peut enregistrer des fichiers. Cette similitude peut être source de confusion pour les développeurs qui ne sont pas familiers avec Android car il n’est pas clair quand une application doit utiliser le stockage interne vs stockage externe.

Le stockage interne se réfère à la mémoire non volatile qu’Android attribue au système d’exploitation, aux APK et aux applications individuelles. Cet espace n’est pas accessible sauf par le système d’exploitation ou les applications. Android attribuera un répertoire dans la partition de stockage interne pour chaque application. Lorsque l’application n’est pas bloquée, tous les fichiers qui sont conservés sur le stockage interne dans cet annuaire seront également supprimés. Le stockage interne est le mieux adapté pour les fichiers qui ne sont accessibles à l’application et qui ne seront pas partagés avec d’autres applications ou auront très peu de valeur une fois que l’application est désinstallée. Sur Android 6.0 ou plus, les fichiers sur le stockage interne peuvent être automatiquement sauvegardés par Google en utilisant la [fonction Auto Backup dans Android 6.0](https://developer.android.com/guide/topics/data/autobackup). Le stockage interne présente les inconvénients suivants :

* Les fichiers ne peuvent pas être partagés.
* Les fichiers seront supprimés lorsque l’application sera désinstallée.
* L’espace disponible sur le stockage interne peut-être limité.

Le stockage externe désigne le stockage de fichiers qui n’est pas le stockage interne et qui n’est pas exclusivement accessible à une application. Le but principal du stockage externe est de fournir un endroit pour mettre des fichiers qui sont censés être partagés entre les applications ou qui sont trop grands pour s’adapter sur le stockage interne. L’avantage du stockage externe est qu’il a généralement beaucoup plus d’espace pour les fichiers que le stockage interne. Cependant, le stockage externe n’est pas toujours garanti d’être présent sur un appareil et peut nécessiter une autorisation spéciale de l’utilisateur pour y accéder.

> [!NOTE]
> Pour les appareils qui prennent en charge plusieurs utilisateurs, Android fournira à chaque utilisateur son propre répertoire sur le stockage interne et externe. Ce répertoire est inaccessible aux autres utilisateurs de l’appareil. Cette séparation est invisible pour les applications tant qu’elles ne codent pas les chemins de code dur vers les fichiers sur le stockage interne ou externe.

En règle générale, les applications Xamarin.Android devraient préférer enregistrer leurs fichiers sur le stockage interne quand il est raisonnable, et compter sur le stockage externe lorsque les fichiers doivent être partagés avec d’autres applications, sont très grandes, ou doivent être conservés, même si l’application est non bloquée. Par exemple, un fichier de configuration est le mieux adapté pour un stockage interne car il n’a aucune importance, sauf pour l’application qui le crée.  En revanche, les photos sont un bon candidat pour le stockage externe. Ils peuvent être très grands et dans de nombreux cas, l’utilisateur peut vouloir les partager ou y accéder, même si l’application est désinstallée.

Ce guide mettra l’accent sur le stockage interne. S’il vous plaît voir le guide [stockage externe](~/android/platform/files/external-storage.md) pour plus de détails sur l’utilisation du stockage externe dans une application Xamarin.Android.

## <a name="working-with-internal-storage"></a>Travailler avec le stockage interne

L’annuaire de stockage interne pour une application est déterminé par le `Android.Content.Context.FilesDir` système d’exploitation, et est exposé aux applications Android par la propriété. Cela rendra `Java.IO.File` un objet représentant l’annuaire qu’Android a dédié exclusivement pour l’application.  Par exemple, une application avec le nom de paquet **com.companyname** l’annuaire de stockage interne peut être:

```bash
/data/user/0/com.companyname/files
```

Ce document se référera à l’annuaire de stockage interne comme _INTERNAL\_STORAGE_.

> [!IMPORTANT]
> Le chemin exact vers l’annuaire de stockage interne peut varier d’un appareil à l’autre et entre les versions d’Android. Pour cette raison, les applications ne doivent pas coder dur le chemin vers l’annuaire de `System.Environment.GetFolderPath()`stockage de fichiers internes, et à la place utiliser les API Xamarin.Android, tels que .

Pour maximiser le partage de code, les applications Xamarin.Android (ou Xamarin.Forms apps ciblant Xamarin.Android) devraient utiliser la [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) méthode. Dans Xamarin.Android, cette méthode va retourner une chaîne pour `Android.Content.Context.FilesDir`un répertoire qui est le même endroit que . Cette méthode prend un `System.Environment.SpecialFolder`enum, qui est utilisé pour identifier un ensemble de constantes énumérées qui représentent les chemins des dossiers spéciaux utilisés par le système d’exploitation. Toutes les `System.Environment.SpecialFolder` valeurs ne seront pas cartographiant à un répertoire valide sur Xamarin.Android. Le tableau suivant décrit le chemin à prévoir `System.Environment.SpecialFolder`pour une valeur donnée de :

| System.Environment.SpecialFolder | Path  |
|----------------------|---|
| `ApplicationData` | **_INTERNAL\_STORAGE_/.config** |
| `Desktop` | **_STOCKAGE\_INTERNE_/Bureau** |
| `LocalApplicationData` | **_INTERNAL\_STORAGE_/.local/share** |
| `MyDocuments` | **_STOCKAGE\_INTERNE_** |
| `MyMusic` | **_INTERNAL\_STORAGE_/Musique** |
| `MyPictures` | **_INTERNAL\_STORAGE_/Photos** |
| `MyVideos` | **_INTERNAL\_STORAGE_/Vidéos** |
| `Personal` | **_STOCKAGE\_INTERNE_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Lecture ou rédaction de fichiers sur le stockage interne

Les [API C pour l’écriture](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) d’un fichier sont suffisantes; tout ce qui est nécessaire est d’obtenir le chemin vers le fichier qui est dans l’annuaire alloué à la demande. Il est fortement recommandé que les versions async des API .NET soient utilisées pour minimiser les problèmes qui peuvent être associés à l’accès au fichier bloquant le thread principal.

Cet extrait de code est un exemple d’écriture d’un integer à un fichier texte UTF-8 à l’annuaire de stockage interne d’une application:

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

L’extrait de code suivant fournit une façon de lire une valeur d’intégrage qui a été stockée dans un fichier texte :

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Utilisation de Xamarin.Essentials &ndash; File System Helpers

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) est un ensemble d’API pour écrire du code compatible multiplateforme. [L’aide système de fichiers](~/essentials/file-system-helpers.md?context=xamarin/android) est une classe qui contient une série d’aides pour simplifier la localisation des répertoires de cache et de données de l’application. Cet extrait de code fournit un exemple de la façon de trouver l’annuaire de stockage interne et le répertoire de cache pour une application :

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Cacher des fichiers de la`MediaStore`

Il `MediaStore` s’agit d’un composant Android qui recueille des métadonnées sur les fichiers multimédias (vidéos, musique, images) sur un appareil Android. Son but est de simplifier le partage de ces fichiers sur toutes les applications Android sur l’appareil.

Les fichiers privés ne seront pas présentés comme des médias partageables. Par exemple, si une application enregistre une image à son stockage externe privé,`MediaStore`alors ce fichier ne sera pas capté par le scanner multimédia ().

Les dossiers publics `MediaStore`seront repris par . Annuaires qui ont un nom de fichier d’ente zéro **. NOMEDIA** ne sera pas `MediaStore`numérisé par .

## <a name="related-links"></a>Liens connexes

* [Stockage externe](~/android/platform/files/external-storage.md)
* [Enregistrer les fichiers sur le stockage de l’appareil](https://developer.android.com/training/data-storage/files)
* [Xamarin.Essentials File System Helpers](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Sauvegarde des données utilisateur avec Auto Backup](https://developer.android.com/guide/topics/data/autobackup)
* [Stockage adoptable](https://source.android.com/devices/storage/adoptable)
