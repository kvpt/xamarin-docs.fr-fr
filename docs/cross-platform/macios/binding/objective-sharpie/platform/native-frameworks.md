---
title: Liaison de frameworks natifs
description: Ce document explique comment utiliser l’option Framework de l’infrastructure d’objectif pour créer une liaison à une bibliothèque distribuée en tant qu’infrastructure.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 78c489518833705432610e83453c3c04bf1cca53
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016093"
---
# <a name="binding-native-frameworks"></a>Liaison de frameworks natifs

Parfois, une bibliothèque native est distribuée en tant qu' [infrastructure](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). La fonction Sharpship objective offre une fonctionnalité pratique pour lier des frameworks correctement définis à l’aide de l’option `-framework`.

Par exemple, la liaison de l' [infrastructure Adobe Creative SDK](https://creativesdk.adobe.com/downloads.html) pour iOS est simple :

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

Dans certains cas, une infrastructure spécifie un fichier **info. plist** qui indique le kit de développement logiciel (SDK) dans lequel l’infrastructure doit être compilée. Si ces informations existent et qu’aucune option de `-sdk` explicite n’est transmise, objective Sharp le déduire du Framework **info. plist** (la clé `DTSDKName` ou une combinaison des clés `DTPlatformName` et `DTPlatformVersion`).

L’option `-framework` ne permet pas de passer des fichiers d’en-tête explicites. Le fichier d’en-tête du parapluie est choisi par Convention en fonction du nom de l’infrastructure. Si un en-tête de parapluie est introuvable, objective Sharp ne tente pas de lier l’infrastructure et vous devez effectuer manuellement la liaison en fournissant les fichiers d’en-tête de parapluie corrects à analyser, ainsi que tous les arguments d’infrastructure pour Clang (comme le `-F` option de chemin de recherche de Framework).

En coulisses, la spécification de `-framework` est simplement un raccourci. Les arguments de liaison suivants sont identiques à ceux de `-framework` ci-dessus.
L’importance particulière est le chemin de recherche `-F .` Framework fourni à Clang (Notez l’espace et le point, qui sont nécessaires dans le cadre de la commande).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
