---
title: Outils & de la finesse d’objectif
description: Ce document fournit une vue d’ensemble des outils inclus avec la finesse objective et les arguments de ligne de commande à utiliser avec eux.
ms.prod: xamarin
ms.assetid: A84E209B-8932-4CC1-BAD1-7FD51F798A97
author: asb3993
ms.author: amburns
ms.date: 10/05/2015
ms.openlocfilehash: ddfe0f99991808214a6006c9504d267179adf2ab
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521853"
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

La finesse d’objectif offre les outils suivants:

|Tool|Description|
|--- |--- |
|**xcode**|Fournit des informations sur l’installation de Xcode actuelle et les versions des kits de développement logiciel (SDK) iOS et Mac disponibles. Nous utiliserons ces informations ultérieurement lors de la génération de nos liaisons.|
|**pod**|Recherche, configure, installe (dans un répertoire local) et lie les bibliothèques [CocoaPod](https://cocoapods.org/) objective-C disponibles dans le référentiel de spécifications maître. Cet outil évalue le CocoaPod installé pour déduire automatiquement l’entrée correcte à passer à `bind` l’outil ci-dessous. Nouveauté dans 3,0!|
|**bind**|Analyse les fichiers d’en-`*.h`tête () de la bibliothèque objective-C dans les fichiers [ApiDefinition.cs et StructsAndEnums.cs](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) initiaux.|
|**update**|Vérifie si des versions plus récentes de la netteté objective sont téléchargées et lance le programme d’installation, le cas échéant.|
|**Verify-docs**|Affiche des informations détaillées `[Verify]` sur les attributs.|
|**docs**|Accède à ce document dans votre navigateur Web par défaut.|

Pour obtenir de l’aide sur un outil précis, entrez le nom de l’outil et l' `-help` option. Par exemple, `sharpie xcode -help` retourne la sortie suivante:

```
$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]

Options:
  -h, -help        Show detailed help
  -v, -verbose     Be verbose with output

Xcode Options:
  -sdks            List all available Xcode SDKs. Pass -verbose for more details.
```

Avant de pouvoir démarrer le processus de liaison, nous devons obtenir des informations sur nos kits de développement logiciel (SDK) actuellement installés en `sharpie xcode -sdks`entrant la commande suivante dans le terminal. Votre sortie peut varier selon la ou les versions de Xcode que vous avez installées. `Xcode*.app` La`/Applications` finesse d’objectif recherche les kits de développement logiciel installés dans les sous-répertoires:

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

À partir de ce qui précède, nous pouvons voir `iphoneos9.1` que le kit de développement logiciel (SDK `arm64` ) est installé sur notre ordinateur et qu’il prend en charge l’architecture. Nous utiliserons cette valeur pour tous les exemples de cette section. Une fois ces informations en place, nous sommes prêts à analyser les fichiers d’en-tête de bibliothèque objective `StructsAndEnums.cs` -C dans le premier `ApiDefinition.cs` et pour le projet de liaison.
