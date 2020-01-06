---
title: Limitations de l’incorporation .NET
description: Ce document décrit les limitations de l’incorporation .NET, l’outil qui vous permet de consommer du code .NET dans d’autres langages de programmation.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: a8b63638861e8d44deb4ea72959d7461190f7713
ms.sourcegitcommit: 6266ef043ae0289f174e901f204f2a280a53c071
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545804"
---
# <a name="net-embedding-limitations"></a>Limitations de l’incorporation .NET

Ce document explique les limitations de l’incorporation .NET et, dans la mesure du possible, fournit des solutions de contournement.

## <a name="general"></a>Général

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Utiliser plusieurs bibliothèques incorporées dans un projet

Il n’est pas possible que deux runtimes mono coexistent dans la même application. Cela signifie que vous ne pouvez pas utiliser deux bibliothèques différentes générées par l’incorporation .NET dans la même application.

**Solution de contournement :** Vous pouvez utiliser le générateur pour créer une bibliothèque unique qui comprend plusieurs assemblys (issus de différents projets).

### <a name="subclassing"></a>Sous-classement

L’incorporation .NET facilite l’intégration du runtime mono dans les applications en exposant un ensemble d’API prêtes à l’emploi pour le langage et la plateforme cibles.

Toutefois, il ne s’agit pas d’une intégration bidirectionnelle, par exemple, vous ne pouvez pas sous-classer un type managé et vous attendez à ce que le code managé soit rappelé à l’intérieur de votre code natif, puisque votre code managé n’est pas conscient de ce coexistence.

En fonction de vos besoins, il peut être possible de contourner des parties de cette limitation, par exemple

* votre code managé peut p/Invoke dans votre code natif. Cela nécessite de personnaliser votre code managé pour permettre la personnalisation à partir du code natif.

* Utilisez des produits comme Xamarin. iOS et exposez une bibliothèque managée qui permettrait objective-C (dans ce cas) à la sous-classe des sous-classes NSObject gérées.

## <a name="objective-c-generated-code"></a>Code Objective-C généré

### <a name="nullability"></a>Possibilité de valeurs nulles

Il n’existe aucune métadonnée dans .NET qui nous indique si une référence null est acceptable ou non pour une API. La plupart des API lèvent `ArgumentNullException` si elles ne peuvent pas faire face à un argument `null`. Cela pose problème car la gestion des exceptions objective-C est mieux évitée.

Étant donné que nous ne pouvons pas générer des annotations de possibilité de valeur null dans les fichiers d’en-tête et que vous souhaitez réduire les exceptions managées, nous avons par défaut des arguments non null (`NS_ASSUME_NONNULL_BEGIN`) et ajoutons des annotations de possibilité de valeur NULL lorsque la précision est possible.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Actuellement, l’incorporation .NET ne prend pas en charge bitcode sur iOS, qui est activé pour certains modèles de projet XCode. Cette opération doit être désactivée pour lier correctement les frameworks générés.

* Pour iOS, bitcode est facultatif pour envoyer des applications à Apple AppStore. Xamarin. iOS ne le prend pas en charge pour iOS, car le bitcode généré est « assembly inline ». Cela n’offre aucun avantage sur la plateforme iOS, car elle ne peut pas être optimisée côté serveur, mais rend les fichiers binaires plus volumineux et les temps de génération plus longs.

* Pour tvOS et Watchos, bitcode est requis pour envoyer des applications à Apple AppStore. Xamarin. iOS prend en charge bitcode sur tvOS (comme « assembly inline ») et Watchos (comme « LLVM/IR ») pour satisfaire cette exigence.

* Pour macOS, la prise en charge de bitcode n’est pas requise actuellement, ni prise en charge par Xamarin. Mac.

![Option Bitcode](images/ios-bitcode-option.png)
