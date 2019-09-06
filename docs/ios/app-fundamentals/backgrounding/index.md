---
title: Arrière-plan dans Xamarin. iOS
description: Le traitement en arrière-plan ou l’arrière-plan est le processus qui permet aux applications d’effectuer des tâches en arrière-plan pendant qu’une autre application s’exécute au premier plan. Ce guide constitue une introduction au traitement en arrière-plan dans iOS.
ms.prod: xamarin
ms.assetid: F377440C-C5D9-4267-85D8-2C816E3A0300
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 161fda52002e8bb757db23c9b2a20a6befd132f5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289337"
---
# <a name="backgrounding-in-xamarinios"></a>Arrière-plan dans Xamarin. iOS

_Le traitement en arrière-plan ou l’arrière-plan est le processus qui permet aux applications d’effectuer des tâches en arrière-plan pendant qu’une autre application s’exécute au premier plan. Ce guide constitue une introduction au traitement en arrière-plan dans iOS._

L’arrière-plan dans les applications mobiles est fondamentalement différent du concept traditionnel du multitâche sur le bureau. Les ordinateurs de bureau disposent d’une variété de ressources disponibles pour une application, notamment l’espace de l’écran, la puissance et la mémoire. Les applications peuvent s’exécuter côte à côte et rester performantes et utilisables. Sur un appareil mobile, les ressources sont bien plus limitées. Il est difficile d’afficher plus d’une application sur un petit écran, et l’exécution de plusieurs applications à pleine vitesse draine la batterie. L’arrière-plan est un compromis constant entre donner aux applications les ressources nécessaires à l’exécution des tâches en arrière-plan dont elles ont besoin pour fonctionner correctement, et maintenir la réactivité de l’application de premier plan et de l’appareil. IOS et Android présentent des dispositions pour l’arrière-plan, mais ils le gèrent de manière très différente.

Dans iOS, l’arrière-plan est reconnu comme un état d’application et les applications sont déplacées dans l’état d’arrière-plan en fonction du comportement de l’application et de l’utilisateur. iOS offre également plusieurs options pour le câblage d’une application à exécuter en arrière-plan, y compris pour demander le temps nécessaire à l’exécution d’une tâche importante, le fonctionnement d’un type d’application d’arrière-plan connue et l’actualisation du contenu d’une application à l’adresse désignée. intervalles.

Dans ce guide et les procédures pas à pas connexes, nous allons apprendre à effectuer des tâches d’application en arrière-plan. Nous allons aborder les concepts clés et les meilleures pratiques, puis créer une application réelle qui reçoit des mises à jour d’emplacement en arrière-plan.

## <a name="contents"></a>Sommaire

1. [Introduction au backgrounding dans iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md)
1. [Démo du cycle de vie d’une application](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md)
1. [Techniques de backgrounding iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md)
1. [Procédures pas à pas : Backgrounding dans iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
1. [Conseils de backgrounding iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-guidance.md)

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons présenté les différentes façons de procéder au traitement en arrière-plan dans iOS. Nous avons abordé les États de l’application iOS et examiné le rôle joué par l’arrière-plan dans le cycle de vie de l’application iOS. En outre, nous avons appris comment nous pourrions inscrire des tâches individuelles ou des applications entières pour qu’elles fonctionnent en arrière-plan dans iOS. Enfin, nous avons renforcé notre compréhension de l’arrière-plan sur iOS en créant des applications qui effectuent des mises à jour en arrière-plan.



## <a name="related-links"></a>Liens associés

- [Arrière-plan sur Android](~/android/app-fundamentals/services/index.md)
- [LifecycleDemo (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
- [Emplacement (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Transfert en arrière-plan simple (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
- [Exécution en arrière-plan iOS](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html)
