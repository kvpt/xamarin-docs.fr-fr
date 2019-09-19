---
title: Bien démarrer avec Objective-C
description: Ce document décrit la prise en main de l’intégration de .NET avec Objective-C. Il aborde les conditions requises, l’installation de l’incorporation .NET à partir de NuGet et les plateformes prises en charge.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: b9c97e871791b633c65e9d374edfe446874567e9
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106099"
---
# <a name="getting-started-with-objective-c"></a>Bien démarrer avec Objective-C

Il s’agit de la page de prise en main pour objective-C, qui couvre les notions de base de toutes les plateformes prises en charge.

## <a name="requirements"></a>Configuration requise

Pour utiliser l’incorporation .NET avec Objective-C, vous avez besoin d’un Mac exécutant :

- macOS 10,12 (Sierra) ou version ultérieure
- Xcode 8.3.2 ou version ultérieure
- [Mono 5,0](https://www.mono-project.com/download/)

Vous pouvez installer [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) pour modifier et compiler votre C# code.

> [!NOTE]
>
> - Les versions antérieures de macOS, Xcode et mono _peuvent_ fonctionner, mais elles ne sont pas testées et ne sont pas prises en charge
> - La génération de code peut être effectuée sur Windows, mais il est uniquement possible de la compiler sur un ordinateur Mac sur lequel Xcode est installé.

## <a name="installing-net-embedding-from-nuget"></a>Installation de l’incorporation .NET à partir de NuGet

Suivez ces [instructions](~/tools/dotnet-embedding/get-started/install/install.md) pour installer et configurer l’incorporation .net pour votre projet.

Un exemple d’appel de commande est listé dans les guides de prise en main [MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) et [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) .

## <a name="platforms"></a>Plateformes

Objective-C est un langage le plus couramment utilisé pour écrire des applications pour macOS, iOS, tvOS et Watchos. l’incorporation .NET prend en charge toutes ces plateformes. L’utilisation de chaque plateforme implique des [différences clés et celles-ci sont expliquées ici](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[La création d’une application MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) est plus facile, car elle n’implique pas autant d’étapes supplémentaires, telles que la configuration de l’identité, des profils continuez, des simulateurs et des appareils. Il est recommandé de commencer par le document macOS avant celui d’iOS.

### <a name="ios--tvos"></a>iOS/tvOS

Assurez-vous que vous êtes déjà configuré pour développer des applications iOS avant d’essayer d’en créer un à l’aide de l’incorporation .NET. Les [instructions suivantes](~/tools/dotnet-embedding/get-started/objective-c/ios.md) supposent que vous avez déjà créé et déployé une application iOS à partir de votre ordinateur.

La prise en charge de tvOS est analogue à la façon dont iOS fonctionne, en utilisant simplement des projets tvOS dans les IDE (Visual Studio et Xcode) au lieu de projets iOS.

> [!NOTE]
> La prise en charge de Watchos sera disponible dans une version ultérieure et sera très similaire à iOS/tvOS.

## <a name="further-reading"></a>Informations supplémentaires

- [Fonctionnalités d’incorporation .NET spécifiques à Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
- [Meilleures pratiques pour objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
- [Limitations de l’incorporation .NET](~/tools/dotnet-embedding/limitations.md)
- [Contribution au projet open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Codes d’erreur et descriptions](~/tools/dotnet-embedding/errors.md)
- [Plateformes cibles](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Liens connexes

- [Exemple météo (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
