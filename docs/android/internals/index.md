---
title: Concepts avancés et éléments internes
description: Architecture sous-jacente derrière Xamarin. Android et sa conception d’API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/21/2018
ms.openlocfilehash: 97382243ac5f767d94a782b895401c1f2f8ae554
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027856"
---
# <a name="advanced-concepts-and-internals"></a>Concepts avancés et éléments internes

_Cette section contient des rubriques qui expliquent l’architecture, la conception d’API et les limitations de Xamarin. Android. En outre, il contient des rubriques qui expliquent son implémentation de garbage collection et les assemblys qui sont disponibles dans Xamarin. Android. Étant donné que Xamarin. Android est [Open source](https://github.com/xamarin/xamarin-android), il est également possible de comprendre le fonctionnement interne de Xamarin. Android en examinant son code source._

## <a name="architectureandroidinternalsarchitecturemd"></a>[Architecture](~/android/internals/architecture.md)

Cet article explique l’architecture sous-jacente d’une application Xamarin. Android. Il explique comment les applications Xamarin. Android s’exécutent à l’intérieur d’un environnement d’exécution mono avec la machine virtuelle du runtime Android et explique ces concepts clés en tant que wrappers Android pouvant être appelés et les wrappers pouvant être appelés. 

## <a name="api-designandroidinternalsapi-designmd"></a>[Conception d’API](~/android/internals/api-design.md)

En plus des bibliothèques de classes de base principales qui font partie de mono, Xamarin. Android est fourni avec des liaisons pour différentes API Android afin de permettre aux développeurs de créer des applications Android natives avec mono.

Au cœur de Xamarin. Android, il existe un moteur d’interopérabilité qui C# fait le pont entre le monde et le monde Java et permet aux développeurs d' C# accéder aux API Java à partir de ou d’autres langages .net.

## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assemblys](~/cross-platform/internals/available-assemblies.md)

Xamarin. Android est fourni avec plusieurs assemblys. Tout comme Silverlight est un sous-ensemble étendu des assemblys .NET de bureau, Xamarin. Android est également un sous-ensemble étendu de plusieurs assemblys .NET Silverlight et Desktop. 
