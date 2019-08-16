---
title: Liaison de frameworks natifs
description: Ce document explique comment utiliser l’option Framework de l’infrastructure d’objectif pour créer une liaison à une bibliothèque distribuée en tant qu’infrastructure.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: cb6c39b2110161b3f839b8adc03701007f09cc4d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521882"
---
# <a name="binding-native-frameworks"></a>Liaison de frameworks natifs

Parfois, une bibliothèque native est distribuée en tant qu' [infrastructure](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). La fonction sharpship objective offre une fonctionnalité pratique pour la liaison des frameworks correctement définis par le biais de l' `-framework` option.

Par exemple, la liaison de l' [infrastructure Adobe Creative SDK](https://creativesdk.adobe.com/downloads.html) pour iOS est simple:

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

Dans certains cas, une infrastructure spécifie un fichier **info. plist** qui indique le kit de développement logiciel (SDK) dans lequel l’infrastructure doit être compilée. Si ces informations existent et qu’aucune `-sdk` option explicite n’est passée, objective Sharp le déduire du Framework **info. plist** (la `DTSDKName` clé ou une combinaison des `DTPlatformName` touches et `DTPlatformVersion` ).

L' `-framework` option n’autorise pas la transmission des fichiers d’en-tête explicites. Le fichier d’en-tête du parapluie est choisi par Convention en fonction du nom de l’infrastructure. Si un en-tête de parapluie est introuvable, objective Sharp ne tente pas de lier l’infrastructure et vous devez effectuer manuellement la liaison en fournissant les fichiers d’en-tête de parapluie corrects à analyser, ainsi que tous les arguments d’infrastructure pour Clang ( `-F`commeoption de chemin de recherche de Framework).

En coulisses, le `-framework` fait de spécifier n’est qu’un raccourci. Les arguments de liaison suivants sont identiques au `-framework` raccourci ci-dessus.
L’importance particulière est le `-F .` chemin de recherche du Framework fourni à Clang (Notez l’espace et le point, qui sont nécessaires dans le cadre de la commande).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
