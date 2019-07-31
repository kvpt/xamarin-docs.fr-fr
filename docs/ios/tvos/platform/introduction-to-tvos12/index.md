---
title: Introduction à tvOS 12
description: Ce document fournit une vue d’ensemble des fonctionnalités nouvelles et mises à jour dans tvOS 12 pour lesquelles la version préliminaire de Xamarin fournit C# actuellement des liaisons.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 16568ca06a7c6be9d23ebb44e9c958b06bbc6bdf
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657352"
---
# <a name="introduction-to-tvos-12"></a>Introduction à tvOS 12

Ce document fournit une vue d’ensemble des nouvelles et mises à jour de tvOS 12.

Pour commencer à créer des applications tvOS 12 avec Xamarin, consultez le Guide de [mise](~/ios/platform/introduction-to-ios12/get-started.md)en route.

## <a name="tvuikit"></a>TVUIKit

tvOS 12 comprend TVUIKit, un ensemble d’API qui permet aux développeurs tvOS d’utiliser des contrôles tvOS courants tels que des affichages de affiches, des boutons de légende, des vues de carte et des affichages monogramme. tvOS 12 introduit également une propriété qui permet aux étiquettes de faire défiler le texte qui est trop long pour être complètement visible.

## <a name="password-autofill"></a>Remplissage automatique du mot de passe

Avec tvOS 12, les utilisateurs peuvent utiliser leurs appareils iOS pour se connecter à une application tvOS en un seul clic. Cette option est activée par une combinaison `UITextContentType` d’utilisation pour spécifier les champs de nom d’utilisateur et de mot de passe, les domaines associés pour établir une relation entre une application iOS et une application tvOS, ainsi que les environnements privilégiés pour sélectionner un élément pour recevoir le focus après un utilisateur fournit un nom d’utilisateur et un mot de passe.

## <a name="focus-engine-enhancements"></a>Améliorations du moteur de focus

tvOS 12 permet à toutes les applications, quel que soit leur mode de rendu, d’interagir avec le moteur de focus. Grâce aux interactions de l’utilisateur avec l’Siri à distance, le moteur de Focus peut être utilisé avec n’importe quelle application pour sélectionner un élément, identifier les modifications de focus possibles et naturellement mettre à jour le focus. Cette option est activée dans les applications personnalisées `IUIFocusItemContainer` par le biais `UIFocusMovementHint` de l’interface `IUIFocusItemScrollableContainer` de UIKit, de la classe, de l’interface et d’autres classes et méthodes associées.

## <a name="vision-framework"></a>Framework de vision

L’infrastructure de vision comprend un détecteur de visage amélioré qui peut détecter les visages dans différentes orientations. En outre, les révisions de la demande peuvent maintenant être utilisées pour sélectionner une révision spécifique de l’algorithme du Framework de vision.

## <a name="natural-language-framework"></a>Infrastructure en langage naturel

L’infrastructure de langage naturel permet aux applications d’effectuer différents types d’analyse de langage. Par exemple, il peut être utilisé pour identifier des parties de la parole et déterminer la langue représentée par un bloc de texte.

## <a name="deprecations"></a>Dépréciations

Avec tvOS 12, Apple a déconseillé OpenGL ES, en [encourageant les développeurs](https://developer.apple.com/tvos/whats-new/) à adopter le métal.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS – développeur Apple (Apple)](https://developer.apple.com/tvos/)
- [Nouveautés de tvOS 12 (Apple) (vidéo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)