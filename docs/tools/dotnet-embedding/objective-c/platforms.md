---
title: Plateformes objective-C
description: Ce document décrit les différentes plateformes que l’incorporation .NET peut cibler lorsque vous travaillez avec du code Objective-C. Il aborde macOS, iOS, tvOS et Watchos.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: f97b595f129cb1ad1ea56e3ae43b0f0a477fef5a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282730"
---
# <a name="objective-c-platforms"></a>Plateformes objective-C

L’incorporation .NET peut cibler différentes plateformes lors de la génération de code Objective-C :

* macOS
* iOS
* tvOS
* Watchos [non encore implémenté]

La plateforme est sélectionnée en passant l' `--platform=<platform>` argument de ligne de commande à l’incorporation .net.

Lors de la génération pour les plateformes iOS, tvOS et Watchos, l’incorporation .NET crée toujours une infrastructure qui incorpore Xamarin. iOS, puisque Xamarin. iOS contient un grand nombre de code de prise en charge du runtime qui est requis sur ces plateformes.

Toutefois, lors de la création pour la plateforme macOS, il est possible de choisir si l’infrastructure générée doit incorporer Xamarin. Mac. Il est possible de ne pas incorporer Xamarin. Mac si l’assembly lié ne fait pas référence à Xamarin. Mac. dll (directement ou indirectement), et cette `--platform=macOS` option est sélectionnée en passant à l’outil d’incorporation .net.

Si l’assembly lié contient une référence à Xamarin. Mac. dll, il est nécessaire d’incorporer Xamarin. Mac, et en outre, le embeddinator doit connaître le Framework cible à utiliser.

Il existe trois frameworks cibles Xamarin. Mac possibles : `modern` (précédemment appelé `mobile`) `full` et `system` (la différence entre les deux est décrite dans la documentation du [Framework cible][1] de Xamarin. Mac) et chacune est sélectionné en passant `--platform=macOS-modern` `--platform=macOS-full` ou `--platform=macOS-system` à l’outil d’incorporation .net.

[1]: ~/mac/platform/target-framework.md
