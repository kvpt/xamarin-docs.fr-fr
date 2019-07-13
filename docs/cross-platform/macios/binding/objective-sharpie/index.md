---
title: Création de liaisons avec Sharpie objectif
description: Cette section fournit une introduction à l’objectif Sharpie, outil de ligne de commande de Xamarin permettent d’automatiser le processus de création d’une liaison vers une bibliothèque Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: c298d3dc772929a123b6b65b0efe929f023a6563
ms.sourcegitcommit: 7dbfac5f68c55f40dd726aa1b03cff62082fe711
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67866354"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Création de liaisons avec Sharpie objectif

_Cette section fournit une introduction à l’objectif Sharpie, outil de ligne de commande de Xamarin permettent d’automatiser le processus de création d’une liaison vers une bibliothèque Objective-C_

- [Vue d’ensemble](#overview) & [historique](#history)
- [Prise en main](get-started.md)
- [Outils et commandes](tools.md)
- [Fonctionnalités](platform/index.md)
- [Exemples](examples/index.md)
- [Procédure pas à pas complète](~/ios/platform/binding-objective-c/walkthrough.md)
- [Historique des versions](releases.md)

## <a name="overview"></a>Présentation

Objectif Sharpie est un outil de ligne de commande pour aider à démarrer la première passe d’une liaison.
Il fonctionne en analysant les fichiers d’en-tête d’une bibliothèque native pour mapper l’API publique dans le [définition de liaison](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un processus qui précédemment a été effectué manuellement).

Objectif Sharpie utilise Clang analyse en-tête des fichiers, par conséquent, la liaison est aussi exacte et détaillée que possible. Cela peut réduire considérablement le temps et l’effort que nécessaire pour produire une liaison de qualité.

> [!IMPORTANT]
> Objectif Sharpie est un outil pour les développeurs expérimentés de Xamarin possédant des connaissances avancées de Objective-C (et par extension, C). Avant de tenter de lier une bibliothèque Objective-C, vous devez acquérir des connaissances solides de la création de la bibliothèque native sur la ligne de commande (et une bonne compréhension du fonctionne de la bibliothèque native).

## <a name="history"></a>Historique

Nous avons été en constante évolution et à l’aide de l’objectif Sharpie en interne chez Xamarin pour les trois dernières années. Comme une preuve de la puissance de l’objectif Sharpie, API introduit dans Xamarin.iOS et Xamarin.Mac depuis iOS 8, Mac OS X 10.10 et watchOS 2.0 ont été amorcés entièrement avec Sharpie d’objectif. Xamarin s’appuie fortement sur l’objectif Sharpie en interne pour la création de ses propres produits.

Toutefois, objectif Sharpie est un outil très avancé qui nécessite une connaissance approfondie de Objective-C et C, comment utiliser le compilateur clang sur la ligne de commande et des bibliothèques natives généralement comment sont assemblées. En raison de cette barre haut, nous avons pensé qu’ayant une interface graphique utilisateur Assistant définit les attentes incorrects, et par conséquent, objectif Sharpie est actuellement uniquement disponible comme un outil de ligne de commande.

## <a name="related-links"></a>Liens associés

- [Téléchargement de Sharpie objectif](https://aka.ms/objective-sharpie)
- [Procédure pas à pas : Liaison d’une bibliothèque Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Liaison de bibliothèques Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Détails de liaison](~/cross-platform/macios/binding/overview.md)
- [Guide de référence de Types de liaison](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin pour les développeurs Objective-C](~/ios/get-started/objective-c-developers/index.md)

