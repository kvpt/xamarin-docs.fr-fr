---
title: Compression de fichiers dans Xamarin. iOS
description: Ce document explique comment utiliser l’API libcompression dans Xamarin. iOS. Il traite de la déflatation, de la déflatation et des différents algorithmes pris en charge.
ms.prod: xamarin
ms.assetid: 94D05DAB-01E8-4C62-9CEF-9D6417EEA8EB
ms.technology: xamarin-ios
author: mandel-macaque
ms.author: mandel
ms.date: 03/04/2019
ms.openlocfilehash: bcc63aa4e1926f5502d571bf47c83b0c8ea7e429
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199523"
---
# <a name="file-compression-in-xamarinios"></a>Compression de fichiers dans Xamarin. iOS

Les applications Xamarin ciblant iOS 9,0 ou macOS 10,11 (et versions ultérieures) peuvent utiliser l' _infrastructure de compression_ pour compresser (Encoder) et décompresser les données. Xamarin. iOS fournit ce Framework après l’API Stream. L’infrastructure de compression permet aux développeurs d’interagir avec les données compressées et décompressées comme s’il s’agissait de flux normaux sans avoir à utiliser des rappels ou des délégués.

L’infrastructure de compression assure la prise en charge des algorithmes suivants:

* LZ4
* LZ4 RAW
* Lzfse
* Lzma
* Zlib

L’utilisation de l’infrastructure de compression permet aux développeurs d’effectuer des opérations de compression sans packages NuGet ni bibliothèque tierce. Cela réduit les dépendances externes et garantit que les opérations de compression seront prises en charge sur toutes les plateformes (à condition qu’elles respectent la configuration minimale requise du système d’exploitation).

## <a name="general-file-decompression"></a>Décompression générale des fichiers

L’infrastructure de compression utilise une API de flux dans Xamarin. iOS et Xamarin. Mac. Cette API signifie que pour compresser les données, le développeur peut utiliser les modèles normaux utilisés dans d’autres API d’e/s dans .NET. L’exemple suivant montre comment décompresser des données avec l’infrastructure de compression, qui est similaire à l’API trouvée `System.IO.Compression.DeflateStream` dans l’API:

```csharp
// sample zlib data
static byte [] compressed_data = { 0xf3, 0x48, 0xcd, 0xc9, 0xc9, 0xe7, 0x02, 0x00 };

using (var backing = new MemoryStream (compressed_data)) // compress data to read
using (var decompressing = new CompressionStream (backing, CompressionMode.Decompress, CompressionAlgorithm.Zlib)) // create decompression stream with the correct algorithm
using (var reader = new StreamReader (decompressing))
{
    // perform the required stream operations
    Console.WriteLine (reader.ReadLine ());
}
```

Le `CompressionStream` implémente l' `IDisposable` interface, comme Other `System.IO.Streams`, afin que les développeurs doivent s’assurer que les ressources sont libérées une fois qu’elles ne sont plus nécessaires.

## <a name="general-file-compression"></a>Compression générale des fichiers

L’API de compression permet également aux développeurs de compresser les données après la même API. Les données peuvent être compressées à l’aide de l’un des `CompressionAlgorithm` algorithmes fournis dans l’énumérateur.

```csharp
// sample method that copies the data from the source stream to the destination stream
static void CopyStream (Stream src, Stream dest)
{
    byte[] array = new byte[1024];
    int bytes_read;
    bytes_read = src.Read (array, 0, 1024);
    while (bytes_read != 0) {
        dest.Write (array, 0, bytes_read);
        bytes_read = src.Read (array, 0, 1024);
    }
}

static void CompressExample ()
{
    // create some sample data to compress
    byte [] data = new byte[100000];
    for (int i = 0; i < 100000; i++) {
        data[i] = (byte) i;
    }

    using (var dataStream = new MemoryStream (data)) // stream that contains the data to compress
    using (var backing = new MemoryStream ()) // stream in which the compress data will be written
    using (var compressing = new CompressionStream (backing, CompressionMode.Compress, CompressionAlgorithm.Zlib, true))
    {
        // copy the data to the compressing stream
        CopyStream (dataStream, compressing);
        // at this point, the CompressionStream contains all the data from the dataStream but compressed
        // using the zlib algorithm
    }
```

## <a name="async-support"></a>Prise en charge asynchrone

Le `CompressionStream` prend en charge toutes les opérations asynchrones prises en `System.IO.DeflateStream`charge par, ce qui signifie que les développeurs peuvent utiliser le mot clé Async pour effectuer les opérations de compression/décompression sans bloquer le thread d’interface utilisateur.
