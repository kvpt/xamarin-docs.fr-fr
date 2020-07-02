---
title: Liaison de bibliothèques iOS
description: Ce document explique comment créer des liaisons C# vers du code Objective-C, ce qui permet de consommer des bibliothèques natives et des CocoaPods dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1e0342a41587b7479381ad763790227aba2ef414
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853088"
---
# <a name="binding-ios-libraries"></a>Liaison de bibliothèques iOS

> [!IMPORTANT]
> Nous étudions actuellement l’utilisation de la liaison personnalisée sur la plateforme Xamarin. Veuillez suivre [**ce questionnaire**](https://www.surveymonkey.com/r/KKBHNLT) pour informer les futurs efforts de développement.

Suivez ces liens pour en savoir plus sur la liaison de bibliothèques objective-C et CocoaPods pour Xamarin. iOS et Xamarin. Mac :

- [**Vue d’ensemble**](~/cross-platform/macios/binding/overview.md) -
   Décrit le fonctionnement de la liaison.
- [**Liaison des bibliothèques**](~/cross-platform/macios/binding/objective-c-libraries.md) -
   objective-C Instructions sur la façon de lier des bibliothèques objective-C pour une utilisation dans des projets Xamarin.
- Guide de référence des [**définitions de types**](~/cross-platform/macios/binding/binding-types-reference.md) -
   Décrit tous les attributs disponibles pour lier des auteurs afin de piloter le processus de génération de liaison.

## <a name="objective-sharpie"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

La finesse d’objectif est un outil en ligne de commande qui permet de démarrer le premier passage d’une liaison.
Il fonctionne en analysant les fichiers d’en-tête d’une bibliothèque native pour mapper l’API publique dans la [définition de liaison](~/cross-platform/macios/binding/objective-c-libraries.md) (un processus qui est autrement effectué manuellement). La finesse objective ne crée pas de liaison seule, mais elle peut vous aider à démarrer.

L’objectif de Sharpy 3,0 a introduit la possibilité de lier directement les Cocoapods !

## <a name="walkthrough---binding-an-ios-objective-c-library"></a>[Procédure pas à pas : liaison d’une bibliothèque objective-C iOS](walkthrough.md)

Cette page fournit une procédure pas à pas de création d’un projet de liaison iOS à l’aide du projet open source [**InfColorPicker**](https://github.com/InfinitApps/InfColorPicker) objective-C, par exemple. La bibliothèque **InfColorPicker** fournit un contrôleur d’affichage réutilisable qui permet à l’utilisateur de sélectionner une couleur en fonction de sa représentation TSL, ce qui rend la sélection des couleurs plus conviviale.
La finesse d’objectif est utilisée pour faciliter le processus de liaison.

## <a name="video"></a>Vidéo

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**Liaisons iOS dans une vidéo en C/C++**

## <a name="related-links"></a>Liens associés

- [Liaison Objective-C](~/cross-platform/macios/binding/index.md)
- [Liaison Mac](~/mac/platform/binding.md)
