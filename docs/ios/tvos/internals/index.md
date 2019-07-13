---
title: 'tvOS dans Xamarin : éléments internes'
description: Documents décrivant le fonctionnement interne de tvOS sur Xamarin, ce qui est basée sur Xamarin.iOS. Lier le contenu décrit les assemblys, les frameworks cibles et iOS concepts associés.
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 080a565b46dd9f56abee767ab0fe1089c5aa3b06
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865654"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS dans Xamarin : éléments internes 

## <a name="assembliesiostvosinternalsassembliesmd"></a>[Assemblys](~/ios/tvos/internals/assemblies.md)

Liste des assemblys pris en charge par Xamarin pour vos applications Xamarin.tvOS.

## <a name="target-frameworksiostvosinternalsframeworksmd"></a>[Frameworks cibles](~/ios/tvos/internals/frameworks.md)

Cet article couvre les types de Frameworks cibles (bibliothèques de classes de Base) qui sont disponibles dans Xamarin.tvOS et les implications en matière de sélection d’une cible spécifique pour votre application Xamarin.tvOS.

## <a name="related-ios-articles"></a>Articles connexes iOS

Les articles suivants sont spécifiques à iOS mais pertinente à tvOS (puisque tvOS 9 est un sous-ensemble d’iOS 9).

### <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[API unifiée](~/cross-platform/macios/unified/index.md)

Présente les nouvelles API unifiée qui permettent le cas des codebases plus simple partage du code entre Apple TV et iOS, ainsi que la présentation de la prise en charge pour l’API 64 bits et 64 bits.  

### <a name="api-designiosinternalsapi-designindexmd"></a>[Conception d’API](~/ios/internals/api-design/index.md)

Explique les principes de conception derrière la liaison de l’API.

### <a name="limitationsiosinternalslimitationsmd"></a>[Limitations](~/ios/internals/limitations.md)

Cette section illustre les pièges et limitations à connaître ce qui concerne Xamarin.iOS, bon nombre d'entre elles sont applicables à Xamarin.tvOS.

### <a name="linkeriosdeploy-testlinkermd"></a>[Éditeur de liens](~/ios/deploy-test/linker.md)

Explique le fonctionnement de l’éditeur de liens pour garantir le package d’application possibles plus petit, et la manière de modifier ses paramètres et son utilisation.

### <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[Localisation et internationalisation](~/ios/app-fundamentals/localization/index.md)

Ce guide couvre l’ajout des encodages à une application Xamarin.iOS pour prendre en charge de l’internationalisation.

### <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

Notes et informations sur mtouch.exe, l’outil de ligne de commande qui génère une application utilisable par iOS à partir d’un projet.

### <a name="linking-native-librariesiosplatformnative-interopmd"></a>[Liaison de bibliothèques natives](~/ios/platform/native-interop.md)

Xamarin.iOS prend en charge la liaison avec les bibliothèques C natives et les bibliothèques Objective-C. Ce document explique comment lier vos bibliothèques C natifs avec votre projet Xamarin.iOS. Pour plus d’informations sur la même opération pour les bibliothèques Objective-C, consultez le&nbsp; [Objective-C, les Types de liaison](~/ios/platform/binding-objective-c/index.md)&nbsp;document.

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Sélecteurs objective-C](~/ios/internals/objective-c-selectors.md)

Notes de publication et l’utilisation pour appeler directement les sélecteurs Objective-C (méthodes).

### <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

Informations et instructions sur l’utilisation de System.Data pour accéder au système de base de données SQLite intégré.

### <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Thread](~/ios/app-fundamentals/threading.md)

Remarques sur l’utilisation de threads dans les applications Xamarin.iOS.

### <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[Génération de code XIB](~/ios/internals/xib-code-generation.md)

Comment Visual Studio pour Mac s’intègre avec Interface Builder de Xcode pour pouvoir utiliser Interface Builder pour concevoir une interface utilisateur.

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
