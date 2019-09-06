---
title: Limitations de l’incorporation .NET
description: Ce document décrit les limitations de l’incorporation .NET, l’outil qui vous permet de consommer du code .NET dans d’autres langages de programmation.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: cf431d4e3d30ac2ec06bfebc9cebe101411faa1c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292707"
---
# <a name="net-embedding-limitations"></a>Limitations de l’incorporation .NET

Ce document explique les limitations de l’incorporation .NET et, dans la mesure du possible, fournit des solutions de contournement.

## <a name="general"></a>Généralités

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Utiliser plusieurs bibliothèques incorporées dans un projet

Il n’est pas possible que deux runtimes mono coexistent dans la même application. Cela signifie que vous ne pouvez pas utiliser deux bibliothèques différentes générées par l’incorporation .NET dans la même application.

**Solution de contournement :** Vous pouvez utiliser le générateur pour créer une bibliothèque unique qui comprend plusieurs assemblys (issus de différents projets).

### <a name="subclassing"></a>Sous-classement

L’incorporation .NET facilite l’intégration du runtime mono dans les applications en exposant un ensemble d’API prêtes à l’emploi pour le langage et la plateforme cibles.

Toutefois, il ne s’agit pas d’une intégration bidirectionnelle, par exemple, vous ne pouvez pas sous-classer un type managé et vous attendez à ce que le code managé soit rappelé à l’intérieur de votre code natif, puisque votre code managé n’est pas conscient de ce coexistence.

En fonction de vos besoins, il peut être possible de contourner des parties de cette limitation, par exemple

* votre code managé peut p/Invoke dans votre code natif. Cela nécessite de personnaliser votre code managé pour permettre la personnalisation à partir du code natif.

* Utilisez des produits comme Xamarin. iOS et exposez une bibliothèque managée qui permettrait objective-C (dans ce cas) à la sous-classe des sous-classes NSObject gérées.

## <a name="objective-c-generated-code"></a>Code Objective-C généré

### <a name="nullability"></a>Valeur null

Il n’existe aucune métadonnée dans .NET qui nous indique si une référence null est acceptable ou non pour une API. La plupart des API `ArgumentNullException` seront levées si elles ne `null` peuvent pas faire face à un argument. Cela pose problème car la gestion des exceptions objective-C est mieux évitée.

Étant donné que nous ne pouvons pas générer des annotations de possibilité de valeur null dans les fichiers d’en-tête et que vous`NS_ASSUME_NONNULL_BEGIN`souhaitez réduire les exceptions managées, nous avons par défaut des arguments non null () et ajoutons des annotations de valeur null, lorsque la précision est possible.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Actuellement, l’incorporation .NET ne prend pas en charge bitcode sur iOS, qui est activé pour certains modèles de projet XCode. Cette opération doit être désactivée pour lier correctement les frameworks générés.

![Option Bitcode](images/ios-bitcode-option.png)
