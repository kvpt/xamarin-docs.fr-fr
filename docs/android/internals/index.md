---
title: Concepts avancés et éléments internes
description: Architecture sous-jacente derrière Xamarin.Android et sa conception d’API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: ba804e9757fb1354c39177e6f7cb9c97ea1b8188
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864135"
---
# <a name="advanced-concepts-and-internals"></a>Concepts avancés et éléments internes

_Cette section contient des rubriques qui expliquent l’architecture, conception d’API et les limitations de Xamarin.Android. En outre, il inclut des rubriques qui expliquent son implémentation de garbage collection et les assemblys qui sont disponibles dans Xamarin.Android. Étant donné que Xamarin.Android est [open source](https://github.com/xamarin/xamarin-android), il est également possible de comprendre le fonctionnement interne de Xamarin.Android en examinant son code source._


## <a name="architectureandroidinternalsarchitecturemd"></a>[Architecture](~/android/internals/architecture.md)

Cet article explique l’architecture sous-jacente derrière une application Xamarin.Android. Il explique comment exécutent des applications Xamarin.Android à l’intérieur d’un environnement d’exécution Mono en même temps qu’avec le runtime Android Machine virtuelle et explique les concepts clés comme Android Callable Wrappers et gérés par. 



## <a name="api-designandroidinternalsapi-designmd"></a>[Conception d’API](~/android/internals/api-design.md)

Outre la base des bibliothèques de classes de Base qui font partie de Mono, Xamarin.Android est livré avec des liaisons pour diverses API Android permettre aux développeurs de créer des applications Android natives avec Mono.

Au cœur de Xamarin.Android est un moteur d’interopérabilité ce monde ponts c# avec le monde de Java et fournit aux développeurs un accès aux API Java à partir de c# ou d’autres langages .NET.



## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblys](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android est livré avec plusieurs assemblys. Tout comme Silverlight est un sous-ensemble étendu des assemblys du .NET de bureau, Xamarin.Android est également un sous-ensemble étendu de plusieurs Silverlight et des assemblys .NET de bureau. 

