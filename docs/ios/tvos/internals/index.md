---
title: tvOS dans Xamarin – Internals
description: Documents décrivant le fonctionnement interne de tvOS sur Xamarin, qui est basé sur Xamarin. iOS. Le contenu des liens traite des assemblys, des frameworks cibles et des concepts iOS associés.
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 4712b7b75e735da047d7f44f7c6c47f42b9ad7a8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030660"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS dans Xamarin – Internals 

## <a name="assembliesiostvosinternalsassembliesmd"></a>[Assemblys](~/ios/tvos/internals/assemblies.md)

Liste des assemblys pris en charge par Xamarin pour vos applications Xamarin. tvOS.

## <a name="target-frameworksiostvosinternalsframeworksmd"></a>[Frameworks cibles](~/ios/tvos/internals/frameworks.md)

Cet article décrit les types de frameworks cibles (bibliothèques de classes de base) qui sont disponibles dans Xamarin. tvOS et les implications de la sélection d’une cible spécifique pour votre application Xamarin. tvOS.

## <a name="related-ios-articles"></a>Articles relatifs à iOS

Les articles suivants sont spécifiques à iOS, mais s’appliquent à tvOS (étant donné que tvOS 9 est un sous-ensemble d’iOS 9).

### <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[API unifiée](~/cross-platform/macios/unified/index.md)

Présente les nouvelles API unifiées qui permettent le partage de code plus simple entre les codes base Apple TV et iOS, ainsi que la prise en charge des API 64 bits et de la compilation 64 bits.  

### <a name="api-designiosinternalsapi-designindexmd"></a>[Conception d’API](~/ios/internals/api-design/index.md)

Explique les principes de conception sous-jacents à la liaison d’API.

### <a name="limitationsiosinternalslimitationsmd"></a>[Limitations](~/ios/internals/limitations.md)

Cette section présente les pièges et les limitations à prendre en compte en ce qui concerne Xamarin. iOS, dont la plupart sont applicables à Xamarin. tvOS.

### <a name="linkeriosdeploy-testlinkermd"></a>[Linker](~/ios/deploy-test/linker.md)

Explique comment fonctionne l’éditeur de liens pour garantir le plus petit package d’application possible, ainsi que la manière de modifier ses paramètres et son utilisation.

### <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[Localisation et internationalisation](~/ios/app-fundamentals/localization/index.md)

Ce guide couvre l’ajout d’encodages à une application Xamarin. iOS pour prendre en charge l’internationalisation.

### <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

Notes et informations sur mtouch.exe, l’outil de ligne de commande qui génère une application utilisable par iOS à partir d’un projet.

### <a name="linking-native-librariesiosplatformnative-interopmd"></a>[Liaison de bibliothèques natives](~/ios/platform/native-interop.md)

Xamarin. iOS prend en charge la liaison avec les bibliothèques C natives et les bibliothèques objective-C. Ce document explique comment lier vos bibliothèques C natives à votre projet Xamarin. iOS. Pour plus d’informations sur la façon de procéder de la même façon pour les bibliothèques objective-C, consultez la&nbsp; [types objective-c de liaison](~/ios/platform/binding-objective-c/index.md)&nbsp;document.

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Sélecteurs objective-C](~/ios/internals/objective-c-selectors.md)

Remarques et utilisation pour appeler directement des sélecteurs objective-C (méthodes).

### <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

Informations et instructions sur l’utilisation de System. Data pour accéder au système de base de données SQLite intégré.

### <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Thread](~/ios/app-fundamentals/threading.md)

Remarques sur l’utilisation des threads dans les applications Xamarin. iOS.

### <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[Génération de code XIB](~/ios/internals/xib-code-generation.md)

Comment Visual Studio pour Mac s’intègre à la Interface Builder de Xcode pour vous permettre d’utiliser Interface Builder pour concevoir l’interface utilisateur.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
