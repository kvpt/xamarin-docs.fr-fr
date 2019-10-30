---
title: Outils & de la finesse d’objectif
description: Ce document fournit une vue d’ensemble des outils inclus avec la finesse objective et les arguments de ligne de commande à utiliser avec eux.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: davidortinau
ms.author: daortin
ms.date: 10/05/2015
ms.openlocfilehash: 2179154aa6dc78a8b0b6b418d780e7996f8e557d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015925"
---
# <a name="objective-sharpie-tools--commands"></a>Outils & de la finesse d’objectif

_Vue d’ensemble des outils inclus avec la netteté objective et des arguments de ligne de commande pour les utiliser._

Une fois l' [installation](~/cross-platform/macios/binding/objective-sharpie/get-started.md)de la netteté objective terminée, ouvrez un terminal et familiarisez-vous avec les *commandes* :

```
$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, -help                Show detailed help
  -v, -version             Show version information

Telemetry Options:
  -tlm-about               Show a detailed overview of what usage and binding
                             information will be submitted to Xamarin by
                             default when using Objective Sharpie.
  -tlm-do-not-submit       Do not submit any usage or binding information to
                             Xamarin. Run 'sharpie -tml-about' for more
                             information.
  -tlm-do-not-identify     Do not submit Xamarin account information when
                             submitting usage or binding information to Xamarin
                             for analysis. Binding attempts and usage data will
                             be submitted anonymously if this option is
                             specified.

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

La finesse d’objectif offre les outils suivants :

|Outil|Description|
|--- |--- |
|**Xcode**|Fournit des informations sur l’installation de Xcode actuelle et les versions des kits de développement logiciel (SDK) iOS et Mac disponibles. Nous utiliserons ces informations ultérieurement lors de la génération de nos liaisons.|
|**Power**|Recherche, configure, installe (dans un répertoire local) et lie les bibliothèques [CocoaPod](https://cocoapods.org/) objective-C disponibles dans le référentiel de spécifications maître. Cet outil évalue le CocoaPod installé pour déduire automatiquement l’entrée correcte à passer à l’outil `bind` ci-dessous. Nouveauté dans 3,0 !|
|**bind**|Analyse les fichiers d’en-tête (`*.h`) de la bibliothèque objective-C dans les fichiers [ApiDefinition.cs et StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) initiaux.|
|**update**|Vérifie si des versions plus récentes de la netteté objective sont téléchargées et lance le programme d’installation, le cas échéant.|
|**Verify-docs**|Affiche des informations détaillées sur les attributs de `[Verify]`.|
|**docs**|Accède à ce document dans votre navigateur Web par défaut.|

Pour obtenir de l’aide sur un outil précis, entrez le nom de l’outil et l’option `-help`. Par exemple, `sharpie xcode -help` retourne la sortie suivante :

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

Avant de pouvoir démarrer le processus de liaison, nous devons obtenir des informations sur nos kits de développement logiciel (SDK) actuellement installés en entrant la commande suivante dans le `sharpie xcode -sdks`terminal. Votre sortie peut varier selon la ou les versions de Xcode que vous avez installées. La précision objective recherche les kits de développement logiciel installés dans n’importe quel `Xcode*.app` dans le répertoire `/Applications` :

```
$ sharpie xcode -sdks
sdk: appletvos9.0    arch: arm64
sdk: iphoneos9.1     arch: arm64   armv7
sdk: iphoneos9.0     arch: arm64   armv7
sdk: iphoneos8.4     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: macosx10.10     arch: x86_64  i386
sdk: watchos2.0      arch: armv7
```

À partir de ce qui précède, nous pouvons voir que le kit de développement logiciel (SDK) `iphoneos9.1` est installé sur notre ordinateur et qu’il a `arm64` prise en charge de l’architecture. Nous utiliserons cette valeur pour tous les exemples de cette section. Une fois ces informations en place, nous sommes prêts à analyser les fichiers d’en-tête de bibliothèque objective-C dans le `ApiDefinition.cs` initial et `StructsAndEnums.cs` pour le projet de liaison.
