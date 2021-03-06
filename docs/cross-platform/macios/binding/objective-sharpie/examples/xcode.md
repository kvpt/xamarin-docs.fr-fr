---
title: Exemple concret utilisant un projet XCode
description: Ce document explique comment utiliser un projet XCode comme entrée directe pour objective Sharp, simplifiant ainsi le processus de création C# de liaisons au code Objective-C.
ms.prod: xamarin
ms.assetid: 168AA64C-E181-4937-A1F2-AD095B9A36F2
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 16191b7ee5bae353a438340d6099d39a296bffd0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016227"
---
# <a name="real-world-example-using-an-xcode-project"></a>Exemple concret utilisant un projet XCode

**Cet exemple utilise la [bibliothèque pop de Facebook](https://github.com/facebook/pop).**

Nouveauté de la version 3,0, objective Sharp prend en charge les projets Xcode comme entrée. Ces projets spécifient les fichiers d’en-tête corrects et les indicateurs de compilateur nécessaires pour compiler la bibliothèque native et, par conséquent, doivent également être liés. La finesse d’objectif est de sélectionner la première _cible_ et sa configuration par défaut d’un projet s’il n’est pas invité à le faire autrement.

Avant que objective ne tente d’analyser le projet et les fichiers d’en-tête, il doit le générer. Les projets comportent souvent des phases de génération qui structurent correctement les fichiers d’en-tête pour la consommation externe et l’intégration. il est donc préférable de toujours générer le projet complet avant de tenter de le lier.

```
$ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   (more git clone output)

$ cd pop
$ sharpie bind pop.xcodeproj -sdk iphoneos9.0
```
