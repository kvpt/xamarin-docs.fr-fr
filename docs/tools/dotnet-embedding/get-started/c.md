---
title: Bien démarrer avec C
description: Ce document explique comment utiliser l’incorporation .NET pour incorporer du code .NET dans une application C. Il explique comment utiliser l’incorporation .NET dans Visual Studio 2019 et Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: davidortinau
ms.author: daortin
ms.date: 04/19/2018
ms.openlocfilehash: 9660eccd3e14a6c4ecad901064650fe1e25458f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029781"
---
# <a name="getting-started-with-c"></a>Bien démarrer avec C

## <a name="requirements"></a>spécifications

Pour utiliser l’incorporation .NET avec C, vous avez besoin d’un ordinateur Mac ou Windows exécutant :

### <a name="macos"></a>macOS

* macOS 10,12 (Sierra) ou version ultérieure
* Xcode 8.3.2 ou version ultérieure
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>Windows

* Windows 7, 8, 10 ou version ultérieure
* Visual Studio 2015 ou version ultérieure

## <a name="installing-net-embedding-from-nuget"></a>Installation de l’incorporation .NET à partir de NuGet

Suivez ces [instructions](~/tools/dotnet-embedding/get-started/install/install.md) pour installer et configurer l’incorporation .net pour votre projet.

L’appel de commande que vous devez configurer doit ressembler à (éventuellement avec des chemins et des numéros de version différents) :

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Génération

### <a name="output-files"></a>Fichiers de sortie

Si tout se passe bien, la sortie suivante s’affiche :

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

Étant donné que l’indicateur de `--compile` a été transmis à l’outil, l’incorporation .NET doit également avoir compilé les fichiers de sortie dans une bibliothèque partagée, que vous pouvez trouver en regard des fichiers générés, d’un fichier **libmanaged. dylib** sur MacOS et de **Managed. dll** sur Windows.

Pour utiliser la bibliothèque partagée, vous pouvez inclure le fichier d’en-tête c **Managed. h** , qui fournit les déclarations C correspondant aux API de bibliothèque managée respectives et le lien avec la bibliothèque partagée compilée mentionnée précédemment.

## <a name="further-reading"></a>informations supplémentaires

* [Limitations de l’incorporation .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribution au projet open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codes d’erreur et descriptions](~/tools/dotnet-embedding/errors.md)
