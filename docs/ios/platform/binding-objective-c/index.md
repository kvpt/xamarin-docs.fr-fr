---
title: Liaison de bibliothèques iOS
description: Ce document décrit comment créer C# des liaisons au code Objective-C, ce qui permet de consommer des bibliothèques natives et des CocoaPods dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61d1adfc997b34302f1f89f56653af906ca90135
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022225"
---
# <a name="binding-ios-libraries"></a>Liaison de bibliothèques iOS

Suivez ces liens pour en savoir plus sur la liaison de bibliothèques objective-C et CocoaPods pour Xamarin. iOS et Xamarin. Mac :

- [**Vue d’ensemble**](~/cross-platform/macios/binding/overview.md) -
  décrit le fonctionnement de la liaison.
- [**Liaison des bibliothèques objective-c**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  des instructions sur la façon de lier des bibliothèques objective-c pour une utilisation dans des projets Xamarin.
- [**Guide de référence de définition de Type**](~/cross-platform/macios/binding/binding-types-reference.md) -
  décrit tous les attributs disponibles pour lier les auteurs afin de piloter le processus de génération de liaison.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

La finesse d’objectif est un outil en ligne de commande qui permet de démarrer le premier passage d’une liaison.
Il fonctionne en analysant les fichiers d’en-tête d’une bibliothèque native pour mapper l’API publique dans la [définition de liaison](~/cross-platform/macios/binding/objective-c-libraries.md) (un processus qui est autrement effectué manuellement). La finesse objective ne crée pas de liaison seule, mais elle peut vous aider à démarrer.

L’objectif de Sharpy 3,0 a introduit la possibilité de lier directement les Cocoapods !

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Procédure pas à pas : liaison d’une bibliothèque objective-C iOS](walkthrough.md)

Cette page fournit une procédure pas à pas de création d’un projet de liaison iOS à l’aide du projet open source [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) objective-C, par exemple. La bibliothèque **InfColorPicker** fournit un contrôleur d’affichage réutilisable qui permet à l’utilisateur de sélectionner une couleur en fonction de sa représentation TSL, ce qui rend la sélection des couleurs plus conviviale.
La finesse d’objectif est utilisée pour faciliter le processus de liaison.

## <a name="video"></a>Vidéo

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Liaisons iOS dans C/C++ Video**

## <a name="related-links"></a>Liens associés

- [Liaison Objective-C](~/cross-platform/macios/binding/index.md)
- [Liaison Mac](~/mac/platform/binding.md)
