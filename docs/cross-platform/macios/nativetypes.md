---
title: Types natifs pour iOS et macOS
description: Ce document décrit comment le API unifiée de Xamarin mappe les types .NET aux types natifs 32 bits et 64 bits, selon les besoins en fonction de l’architecture cible de la compilation.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: 1168dfe0f2120e87c57b46011caba5e7a8a0c020
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015481"
---
# <a name="native-types-for-ios-and-macos"></a>Types natifs pour iOS et macOS

Les API Mac et iOS utilisent des types de données spécifiques à l’architecture qui sont toujours 32 bits sur les plateformes 32 bits et 64 bits sur les plateformes 64 bits.

Par exemple, Objective-C mappe le type de données `NSInteger` pour `int32_t` sur les systèmes 32 bits et pour `int64_t` sur des systèmes bits 64.

Pour correspondre à ce comportement, sur notre API unifiée, nous remplaçons les utilisations précédentes de `int` (qui dans .NET est défini comme toujours `System.Int32`) à un nouveau type de données : `System.nint`. Vous pouvez considérer « n » comme « native », donc le type d’entier natif de la plateforme.

Avec ces nouveaux types de données, le même code source est compilé pour les architectures 32 bits et 64 bits, en fonction de vos indicateurs de compilation.

## <a name="new-data-types"></a>Nouveaux types de données

Le tableau suivant présente les modifications apportées à nos types de données pour qu’elles correspondent à ce nouveau monde 32 bits :

|Type natif|type de stockage 32 bits|type de stockage 64 bits|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Nous avons choisi ces noms pour permettre C# à votre code de s’afficher plus ou moins de la même façon qu’aujourd’hui.

### <a name="implicit-and-explicit-conversions"></a>Conversions implicites et explicites

La conception des nouveaux types de données est conçue pour permettre à un C# fichier source unique d’utiliser naturellement le stockage 32 ou 64 bits, en fonction de la plateforme hôte et des paramètres de compilation.

Cela nous a demandé de concevoir un ensemble de conversions implicites et explicites vers et à partir des types de données spécifiques à la plateforme vers les types de données intégraux et à virgule flottante .NET.

Les opérateurs de conversion implicite sont fournis lorsqu’il n’existe aucune possibilité de perte de données (les valeurs de 32 bits sont stockées sur un espace de 64 bits).

Les opérateurs de conversions explicites sont fournis lorsqu’il existe un risque de perte de données (la valeur de 64 bits est stockée sur un emplacement de stockage 32 ou potentiellement 32).

`int`, `uint` et `float` sont tous implicitement convertibles en `nint`, `nuint` et `nfloat`, car 32 bits peuvent toujours être contenus dans 32 ou 64 bits.

`nint`, `nuint` et `nfloat` sont tous implicitement convertibles en `long`, `ulong` et `double` en tant que valeurs de bit 32 ou 64 sont toujours adaptées au stockage 64 bits.

Vous devez utiliser des conversions explicites de `nint`, `nuint` et `nfloat` dans `int`, `uint` et `float` puisque les types natifs peuvent contenir 64 bits de stockage.

Vous devez utiliser des conversions explicites de `long`, `ulong` et `double` dans `nint`, `nuint` et `nfloat` puisque les types natifs peuvent uniquement contenir 32 bits de stockage.

## <a name="coregraphics-types"></a>Types CoreGraphics

Les types de données point, size et rectangle utilisés avec CoreGraphics utilisent des bits 32 ou 64 selon l’appareil sur lequel ils s’exécutent.  Lors de la liaison initiale des API iOS et Mac, nous avons utilisé des structures de données existantes qui correspondent aux tailles de la plateforme hôte (les types de données dans `System.Drawing`).

Lors du passage à **Unified**, vous devez remplacer les instances de `System.Drawing` par leurs équivalents `CoreGraphics`, comme indiqué dans le tableau suivant :

|Ancien type dans System. Drawing|Nouveau type de données CoreGraphics|Description|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Contient les informations de rectangle à virgule flottante.|
|`SizeF`|`CGSize`|Contient des informations sur la taille de la virgule flottante (largeur, hauteur)|
|`PointF`|`CGPoint`|Contient une virgule flottante, des informations sur les points (X, Y)|

Les anciens types de données utilisaient des valeurs float pour stocker les éléments des structures de données, tandis que le nouveau utilise `System.nfloat`.

## <a name="related-links"></a>Liens associés

- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
