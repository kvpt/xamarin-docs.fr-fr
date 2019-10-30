---
title: Vue d'ensemble de l'architecture
description: Ce document décrit l’architecture de Xamarin Workbooks, en examinant la façon dont l’agent interactif et le client interactif fonctionnent ensemble.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 7b3f2613e315bc05fedfb5b2fa70d11c2874ba65
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029620"
---
# <a name="architecture-overview"></a>Vue d'ensemble de l'architecture

Xamarin Workbooks comporte deux composants principaux qui doivent fonctionner conjointement : _agent_ et _client_.

## <a name="interactive-agent"></a>Agent interactif

Le composant agent est un petit assembly spécifique à une plateforme qui s’exécute dans le contexte d’une application .NET.

Xamarin Workbooks fournit des applications « vides » prédéfinies pour plusieurs plateformes, telles qu’iOS, Android, Mac et WPF. Ces applications hébergent explicitement l’agent.

Pendant l’inspection en temps réel (Xamarin Inspector), l’agent est injecté via le débogueur IDE dans une application existante dans le cadre du flux de travail de développement normal & le débogage.

## <a name="interactive-client"></a>Client interactif

Le client est un interpréteur de commandes natif (cacao sur Mac, WPF sur Windows) qui héberge une surface de navigateur Web pour la présentation de l’interface de classeur/REPL. Du point de vue du SDK, toutes les intégrations de clients sont implémentées en JavaScript et CSS.

Le client est responsable (via Roslyn) de la compilation du code source en petits assemblys et de son envoi à l’agent connecté pour exécution. Les résultats de l’exécution sont renvoyés au client à des fins de rendu. Chaque cellule d’un classeur produit un assembly qui fait référence à l’assembly de la cellule précédente.

Étant donné qu’un agent peut s’exécuter sur n’importe quel type de plateforme .NET et qu’il a accès à quoi que ce soit dans l’application en cours d’exécution, il convient de veiller à sérialiser les résultats de manière indépendante de la plateforme.
