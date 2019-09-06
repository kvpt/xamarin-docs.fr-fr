---
title: Exemple concret utilisant CocoaPods
description: Ce document montre comment utiliser la finesse objective pour générer automatiquement les C# définitions de liaison à partir d’un CocoaPod.
ms.prod: xamarin
ms.assetid: 233B781D-5841-4250-9F63-0585231D2112
author: conceptdev
ms.author: crdun
ms.date: 03/28/2018
ms.openlocfilehash: 0f730b1c0a0deacdb84c198cfe4af47308a268cc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290032"
---
# <a name="real-world-example-using-cocoapods"></a>Exemple concret utilisant CocoaPods

> [!NOTE]
> Cet exemple utilise [AFNetworking CocoaPod](https://cocoapods.org/pods/AFNetworking).

Nouveauté de la version 3,0, objective sharpen prend en charge la liaison de CocoaPods et`sharpie pod`comprend même une commande () pour faciliter le téléchargement, la configuration et la création de CocoaPods très simples. Vous devez vous [familiariser avec CocoaPods](https://cocoapods.org) en général avant d’utiliser cette fonctionnalité.

## <a name="creating-a-binding-for-a-cocoapod"></a>Création d’une liaison pour un CocoaPod

La `sharpie pod` commande a une option globale et deux sous-commandes :

```bash
$ sharpie pod -help
usage: sharpie pod [OPTIONS] COMMAND [COMMAND_OPTIONS]

Pod Options:
  -d, -dir DIR     Use DIR as the CocoaPods binding directory,
                   defaulting to the current directory

Available Commands:
  init         Initialize a new Xamarin C# CocoaPods binding project
  bind         Bind an existing Xamarin C# CocoaPods project
```

La `init` sous-commande offre également une aide utile :

```bash
$ sharpie pod init -help
usage: sharpie pod init [INIT_OPTIONS] TARGET_SDK POD_SPEC_NAMES

Init Options:
  -f, -force       Initialize a new Podfile and run actions against
                   it even if one already exists
```

Plusieurs noms de CocoaPod et sous-spécifications peuvent être fournis `init`à.

```bash
$ sharpie pod init ios AFNetworking
** Setting up CocoaPods master repo ...
   (this may take a while the first time)
** Searching for requested CocoaPods ...
** Working directory:
**   - Writing Podfile ...
**   - Installing CocoaPods ...
**     (running `pod install --no-integrate --no-repo-update`)
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.6.0)
Generating Pods project
Sending stats
** 🍻 Success! You can now use other `sharpie podn`  commands.
```

Une fois votre CocoaPod configuré, vous pouvez maintenant créer la liaison :

```bash
$ sharpie pod bind
```

Cela entraînera la génération, puis l’évaluation et l’analyse du projet XCode CocoaPod. Un grand nombre de sorties de console seront générées, mais la définition de liaison doit se produire à la fin :

```bash
(... lots of build output ...)

Parsing 19 header files...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Done.
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir généré les fichiers **ApiDefinitions.cs** et **StructsAndEnums.cs** , consultez la documentation suivante pour générer un assembly à utiliser dans vos applications :

- [Vue d’ensemble de la liaison objective-C](~/cross-platform/macios/binding/overview.md)
- [Liaison des bibliothèques objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Procédure pas à pas : Liaison d’une bibliothèque objective-C iOS](~/ios/platform/binding-objective-c/walkthrough.md)
