---
title: Création de liaisons avec une netteté objective
description: Cette section fournit une introduction à l’outil de ligne de commande Xamarin, utilisé pour automatiser le processus de création d’une liaison à une bibliothèque objective-C.
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 4d6ab6cf48c5c365a4d8d05ef108a4d3a5d16134
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016191"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Création de liaisons avec une netteté objective

_Cette section fournit une introduction à l’outil de ligne de commande Xamarin, utilisé pour automatiser le processus de création d’une liaison à une bibliothèque objective-C._

- [Vue d’ensemble](#overview) & [historique](#history)
- [Bien démarrer](get-started.md)
- [Outils et commandes](tools.md)
- [Fonctionnalités](platform/index.md)
- [Exemples](examples/index.md)
- [Exécuter la procédure pas à pas](~/ios/platform/binding-objective-c/walkthrough.md)
- [Historique des versions](releases.md)

## <a name="overview"></a>Vue d'ensemble

La finesse d’objectif est un outil en ligne de commande qui permet de démarrer le premier passage d’une liaison.
Il fonctionne en analysant les fichiers d’en-tête d’une bibliothèque native pour mapper l’API publique dans la [définition de liaison](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un processus qui a été précédemment effectué manuellement).

Objective Sharp utilise des fichiers d’en-tête d’analyse Clang, de sorte que la liaison est aussi exacte et complète que possible. Cela peut réduire de manière considérable le temps et les efforts nécessaires pour produire une liaison de qualité.

> [!IMPORTANT]
> La finesse d’objectif est un outil destiné aux développeurs Xamarin expérimentés ayant une connaissance approfondie de Objective-C (et de l’extension C). Avant de tenter de lier une bibliothèque objective-C, vous devez avoir une connaissance approfondie de la façon de générer la bibliothèque native sur la ligne de commande (et de bien comprendre le fonctionnement de la bibliothèque native).

## <a name="history"></a>Historique

Nous avons fait des évolutions et utilisons l’objectif de la finesse en interne au Xamarin des trois dernières années. En guise de preuve de la puissance de la finesse objective, les API introduites dans Xamarin. iOS et Xamarin. Mac depuis iOS 8, Mac OS X 10,10 et Watchos 2,0 ont été entièrement amorcées avec la finesse objective. Xamarin s’appuie fortement sur la netteté objective en interne pour la création de ses propres produits.

Toutefois, objective Sharp est un outil très avancé qui nécessite une connaissance approfondie de Objective-C et C, de l’utilisation du compilateur Clang sur la ligne de commande, et généralement de la façon dont les bibliothèques natives sont rassemblées. En raison de cette grande barre, nous avons pensé qu’un assistant d’interface utilisateur graphique définissait des attentes erronées et, par conséquent, la finesse d’objectif est actuellement uniquement disponible en tant qu’outil en ligne de commande.

## <a name="related-links"></a>Liens associés

- [Téléchargement d’objective](https://aka.ms/objective-sharpie)
- [Procédure pas à pas : liaison d’une bibliothèque objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Liaison de bibliothèques Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Détails de la liaison](~/cross-platform/macios/binding/overview.md)
- [Guide de référence des types de liaison](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin pour les développeurs Objective-C](~/ios/get-started/objective-c-developers/index.md)
