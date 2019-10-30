---
title: Plateformes objective-C
description: Ce document décrit les différentes plateformes que l’incorporation .NET peut cibler lorsque vous travaillez avec du code Objective-C. Il aborde macOS, iOS, tvOS et Watchos.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: dbc2c40e06f14ec636b4aa4cc11fc4f2d230ee6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006426"
---
# <a name="objective-c-platforms"></a>Plateformes objective-C

L’incorporation .NET peut cibler différentes plateformes lors de la génération de code Objective-C :

* macOS
* iOS
* tvOS
* Watchos [non encore implémenté]

La plateforme est sélectionnée en passant l' `--platform=<platform>` argument de ligne de commande à l’incorporation .NET.

Lors de la génération pour les plateformes iOS, tvOS et Watchos, l’incorporation .NET crée toujours une infrastructure qui incorpore Xamarin. iOS, puisque Xamarin. iOS contient un grand nombre de code de prise en charge du runtime qui est requis sur ces plateformes.

Toutefois, lors de la création pour la plateforme macOS, il est possible de choisir si l’infrastructure générée doit incorporer Xamarin. Mac. Il est possible de ne pas incorporer Xamarin. Mac si l’assembly lié ne fait pas référence à Xamarin. Mac. dll (directement ou indirectement), et cette option est sélectionnée en passant `--platform=macOS` à l’outil d’incorporation .NET.

Si l’assembly lié contient une référence à Xamarin. Mac. dll, il est nécessaire d’incorporer Xamarin. Mac, et en outre, le embeddinator doit connaître le Framework cible à utiliser.

Il existe trois frameworks cibles Xamarin. Mac possibles : `modern` (précédemment appelée `mobile`), `full` et `system` (la différence entre chacune est décrite dans la documentation du [Framework cible][1] de Xamarin. Mac) et chaque est sélectionnée en passant `--platform=macOS-modern`, `--platform=macOS-full` ou `--platform=macOS-system` à l’outil d’incorporation .NET.

[1]: ~/mac/platform/target-framework.md
