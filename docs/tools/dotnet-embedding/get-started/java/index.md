---
title: Prise en main de Java
description: Ce document explique comment commencer à utiliser l’incorporation .NET avec Java. Il aborde la configuration système requise, l’installation et les plateformes prises en charge.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 09ea33724c2b1184654ce7768ea1cb2525b62c28
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007376"
---
# <a name="getting-started-with-java"></a>Prise en main de Java

Il s’agit de la page de prise en main de Java, qui couvre les principes de base de toutes les plateformes prises en charge.

## <a name="requirements"></a>spécifications

Pour utiliser l’incorporation .NET avec Java, vous avez besoin des éléments suivants :

* Java 1,8 ou version ultérieure
* [Mono 5,0](https://www.mono-project.com/download/)

Pour Mac :

* Xcode 8.3.2 ou version ultérieure

Pour Windows :

* Visual Studio 2017 avec C++ prise en charge
* SDK Windows 10

Pour Android :

* [Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou version ultérieure
* [Android Studio 3. x](https://developer.android.com/studio/index.html) avec Java 1,8

Vous pouvez utiliser [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) pour modifier et compiler votre C# code.

> [!NOTE]
> Les versions antérieures de Xcode, Visual Studio, Xamarin. Android, Android Studio et mono _peuvent_ fonctionner, mais elles ne sont pas testées et ne sont pas prises en charge.

## <a name="installation"></a>Installation

L’incorporation .NET est actuellement disponible sur [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

**Embeddinator-4000. exe** est placé dans le répertoire **packages/Embeddinator-4000/Tools** .

En outre, vous pouvez créer une incorporation .NET à partir de la source, consulter notre [référentiel git](https://github.com/mono/Embeddinator-4000/) et le document [contributeur](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) pour obtenir des instructions.

## <a name="platforms"></a>Plateformes

Java est actuellement dans un état d’aperçu pour macOS, Windows et Android.

La plateforme est sélectionnée en passant l' `--platform=<platform>` argument de ligne de commande à l’outil d’incorporation .NET. Actuellement `macOS`, `Windows`et `Android` sont pris en charge.

### <a name="macos-and-windows"></a>macOS et Windows

Pour le développement, vous devez être en mesure d’utiliser n’importe quel IDE Java prenant en charge Java 1,8. Vous pouvez même utiliser Android Studio pour cela si vous le souhaitez, [voir ici](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Vous pouvez utiliser la sortie du fichier JAR comme vous le feriez pour n’importe quel fichier jar Java standard.

### <a name="android"></a>Android

Assurez-vous que vous êtes déjà configuré pour développer des applications Android avant d’essayer d’en créer un à l’aide de l’incorporation .NET. Les [instructions suivantes](~/tools/dotnet-embedding/get-started/java/android.md) supposent que vous avez déjà créé et déployé une application Android à partir de votre ordinateur.

Android Studio est recommandé pour le développement, mais d’autres IDE devraient fonctionner tant qu’il existe une prise en charge du [format de fichier AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Compléments de lecture

* [Prise en main sur Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Rappels sur Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Recherche Android préliminaire](~/tools/dotnet-embedding/android/index.md)
* [Limitations de l’incorporation .NET](~/tools/dotnet-embedding/limitations.md)
* [Contribution au projet open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codes d’erreur et descriptions](~/tools/dotnet-embedding/errors.md)
