---
title: Accès aux fichiers avec Xamarin. Android
description: Ce guide explique l’accès aux fichiers dans Xamarin. Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: bf4b0f4ed6ade69808314ac7e7a51270aa3a847e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758895"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Stockage de fichiers et accès avec Xamarin. Android

Une exigence courante pour les applications Android consiste à manipuler &ndash; des fichiers pour enregistrer des images, télécharger des documents ou exporter des données à partager avec d’autres programmes. Android (basé sur Linux) le prend en charge en fournissant de l’espace pour le stockage des fichiers. Android regroupe le système de fichiers en deux types de stockage différents :

* **Stockage interne** &ndash; il s’agit d’une partie du système de fichiers accessible uniquement par l’application ou le système d’exploitation.
* **Stockage externe** &ndash; il s’agit d’une partition pour le stockage des fichiers accessibles par toutes les applications, l’utilisateur et éventuellement d’autres appareils. Sur certains appareils, le stockage externe peut être amovible (par exemple, une carte SD).

Ces regroupements sont conceptuels uniquement et ne font pas nécessairement référence à une partition ou un répertoire unique sur l’appareil. Un appareil Android fournira toujours une partition pour le stockage interne et le stockage externe. Il est possible que certains appareils puissent avoir plusieurs partitions qui sont considérées comme du stockage externe. Quelle que soit la partition, les API pour la lecture, l’écriture ou la création de fichiers sont les mêmes. Il existe deux ensembles d’API qui peuvent être utilisés par une application Xamarin. Android pour accéder aux fichiers :

1. **API .net (fournies par mono et encapsulées par Xamarin. Android)** Celles-ci incluent les [applications auxiliaires de système de fichiers](~/essentials/file-system-helpers.md?context=xamarin/android) fournies par [Xamarin. Essentials.](~/essentials/index.md?context=xamarin/android) &ndash; Les API .NET offrent la meilleure compatibilité multiplateforme et, par conséquent, ce guide se concentre sur ces API.
1. **Les API d’accès au fichier Java natif (fournies par Java et encapsulées par Xamarin. Android)** &ndash; Java fournit ses propres API pour la lecture et l’écriture de fichiers. Il s’agit d’une alternative entièrement acceptable pour les API .NET, mais qui sont spécifiques à Android et ne conviennent pas aux applications qui sont destinées à être multiplateformes.

La lecture et l’écriture dans des fichiers sont presque identiques dans Xamarin. Android, comme c’est le cas pour toute autre application .NET. L’application Xamarin. Android détermine le chemin d’accès au fichier qui sera manipulé, puis utilise les idiomes .NET standard pour l’accès aux fichiers. Étant donné que les chemins d’accès réels au stockage interne et externe peuvent varier d’un appareil à l’appareil ou d’une version Android à une version Android, il n’est pas recommandé de coder en dur le chemin d’accès aux fichiers. Utilisez plutôt les API Xamarin. Android pour déterminer le chemin d’accès aux fichiers. Ainsi, les API .NET pour la lecture et l’écriture de fichiers exposent les API Android natives qui vous aideront à déterminer le chemin d’accès aux fichiers sur le stockage interne et externe.

Avant de discuter des API impliquées dans l’accès aux fichiers, il est important de comprendre certains détails relatifs au stockage interne et externe. Ce sujet sera abordé dans la section suivante.

## <a name="internal-vs-external-storage"></a>Stockage interne et externe

Conceptuellement, le stockage interne et le stockage externe sont &ndash; très similaires à ceux où une application Xamarin. Android peut enregistrer des fichiers. Cette similarité peut prêter à confusion pour les développeurs qui ne sont pas familiarisés avec Android, car il n’est pas évident qu’une application doit utiliser le stockage interne et le stockage externe.

Le stockage interne fait référence à la mémoire non volatile que Android alloue au système d’exploitation, fichiers APK et pour les applications individuelles. Cet espace n’est pas accessible, sauf par le système d’exploitation ou les applications. Android allouera un répertoire dans la partition de stockage interne pour chaque application. Lorsque l’application est désinstallée, tous les fichiers qui sont conservés sur le stockage interne dans ce répertoire sont également supprimés. Le stockage interne est mieux adapté aux fichiers qui ne sont accessibles qu’à l’application et qui ne sont pas partagés avec d’autres applications ou qui n’ont pas une valeur minimale une fois l’application désinstallée. Sur Android 6,0 ou version ultérieure, les fichiers sur le stockage interne peuvent être sauvegardés automatiquement par Google à l’aide [de la fonctionnalité de sauvegarde automatique dans Android 6,0](https://developer.android.com/guide/topics/data/autobackup). Le stockage interne présente les inconvénients suivants :

* Les fichiers ne peuvent pas être partagés.
* Les fichiers seront supprimés lors de la désinstallation de l’application.
* L’espace disponible sur le stockage interne peut être limité.

Le stockage externe fait référence au stockage de fichiers qui n’est pas un stockage interne et n’est pas exclusivement accessible à une application. L’objectif principal du stockage externe est de fournir un emplacement pour placer les fichiers destinés à être partagés entre les applications ou qui sont trop volumineux pour tenir sur le stockage interne. L’avantage du stockage externe est qu’il a généralement plus d’espace pour les fichiers que le stockage interne. Toutefois, le stockage externe n’est pas toujours présent sur un appareil et peut nécessiter une autorisation spéciale de la part de l’utilisateur pour y accéder.

> [!NOTE]
> Pour les appareils qui prennent en charge plusieurs utilisateurs, Android fournit à chaque utilisateur leur propre répertoire sur le stockage interne et externe. Ce répertoire n’est pas accessible aux autres utilisateurs sur l’appareil. Cette séparation est invisible aux applications tant qu’elles ne sont pas codées en dur dans des fichiers sur un stockage interne ou externe.

En règle générale, les applications Xamarin. Android doivent préférer enregistrer leurs fichiers sur le stockage interne lorsque cela est raisonnable et s’appuyer sur un stockage externe lorsque les fichiers doivent être partagés avec d’autres applications, sont très volumineux ou doivent être conservés même si l’application est désinstallée. Par exemple, un fichier de configuration est mieux adapté à un stockage interne, car il n’a aucune importance, sauf pour l’application qui le crée.  En revanche, les photos sont un bon candidat pour le stockage externe. Ils peuvent être très volumineux et, dans de nombreux cas, l’utilisateur peut souhaiter le partager ou y accéder même si l’application est désinstallée.

Ce guide se concentre sur le stockage interne. Pour plus d’informations sur l’utilisation du stockage externe dans une application Xamarin. Android, consultez le guide [stockage externe](~/android/platform/files/external-storage.md) .

## <a name="working-with-internal-storage"></a>Utilisation du stockage interne

Le répertoire de stockage interne pour une application est déterminé par le système d’exploitation et est exposé aux applications Android par `Android.Content.Context.FilesDir` la propriété. Cela renverra un `Java.IO.File` objet représentant le répertoire qu’Android a exclusivement dédié à l’application.  Par exemple, une application avec le nom de package **com. CompanyName** le répertoire de stockage interne peut être :

```bash
/data/user/0/com.companyname/files
```

Ce document fait référence au répertoire de stockage interne en tant que _stockage interne\__ .

> [!IMPORTANT]
> Le chemin d’accès exact au répertoire de stockage interne peut varier d’un appareil à l’autre et entre les versions d’Android. Pour cette raison, les applications ne doivent pas coder en dur le chemin d’accès au répertoire de stockage des fichiers internes et utiliser à la place les `System.Environment.GetFolderPath()`API Xamarin. Android, telles que.

Pour optimiser le partage de code, les applications Xamarin. Android (ou les applications Xamarin. Forms ciblant Xamarin [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) . Android) doivent utiliser la méthode. Dans Xamarin. Android, cette méthode retourne une chaîne pour un répertoire qui est le même emplacement que `Android.Content.Context.FilesDir`. Cette méthode prend une énumération `System.Environment.SpecialFolder`,, qui est utilisée pour identifier un jeu de constantes énumérées qui représentent les chemins d’accès des dossiers spéciaux utilisés par le système d’exploitation. Toutes les `System.Environment.SpecialFolder` valeurs ne sont pas mappées à un répertoire valide sur Xamarin. Android. Le tableau suivant décrit le chemin d’accès qui peut être attendu pour une `System.Environment.SpecialFolder`valeur donnée :

| System.Environment.SpecialFolder | path  |
|----------------------|---|
| `ApplicationData` | **_INTERNAL\_STORAGE_/.config** |
| `Desktop` | **_Stockage\_interne_/Desktop** |
| `LocalApplicationData` | **_Stockage\_interne_/.local/share** |
| `MyDocuments` | **_STOCKAGE\_INTERNE_** |
| `MyMusic` | **_Stockage\_interne_/Music** |
| `MyPictures` | **_Stockage\_interne_/Pictures** |
| `MyVideos` | **_Stockage\_interne_/Videos** |
| `Personal` | **_STOCKAGE\_INTERNE_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Lecture ou écriture dans des fichiers sur le stockage interne

Toutes les [ C# API pour l’écriture](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) dans un fichier sont suffisantes ; le chemin d’accès au fichier qui se trouve dans le répertoire alloué à l’application est tout ce qui est nécessaire. Il est vivement recommandé d’utiliser les versions Async des API .NET pour réduire les problèmes qui peuvent être associés à l’accès aux fichiers bloquant le thread principal.

Cet extrait de code est un exemple d’écriture d’un entier dans un fichier texte UTF-8 dans le répertoire de stockage interne d’une application :

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

L’extrait de code suivant fournit un moyen de lire une valeur entière qui a été stockée dans un fichier texte :

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Utilisation des applications auxiliaires &ndash; du système de fichiers Xamarin. Essentials

[Xamarin. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) est un ensemble d’API permettant d’écrire du code compatible multiplateforme. Les applications [auxiliaires du système de fichiers](~/essentials/file-system-helpers.md?context=xamarin/android) sont une classe qui contient une série d’applications auxiliaires permettant de simplifier la localisation des répertoires de cache et de données de l’application. Cet extrait de code fournit un exemple de la façon de rechercher le répertoire de stockage interne et le répertoire de cache pour une application :

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Masquage des fichiers du`MediaStore`

Le `MediaStore` est un composant Android qui collecte des métadonnées sur les fichiers multimédias (vidéos, musique, images) sur un appareil Android. Son objectif est de simplifier le partage de ces fichiers sur toutes les applications Android sur l’appareil.

Les fichiers privés ne s’affichent pas en tant que média partageable. Par exemple, si une application enregistre une image dans son stockage externe privé, ce fichier n’est pas récupéré par le scanneur multimédia (`MediaStore`).

Les fichiers publics sont récupérés par `MediaStore`. Répertoires qui ont un nom de fichier de zéro octet **. Nomedias** ne sera pas analysé par `MediaStore`.

## <a name="related-links"></a>Liens associés

* [Stockage externe](~/android/platform/files/external-storage.md)
* [Enregistrer des fichiers sur le stockage de l’appareil](https://developer.android.com/training/data-storage/files)
* [Applications d’assistance du système de fichiers Xamarin. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Sauvegarder des données utilisateur avec la sauvegarde automatique](https://developer.android.com/guide/topics/data/autobackup)
* [Stockage à adopter](https://source.android.com/devices/storage/adoptable)
