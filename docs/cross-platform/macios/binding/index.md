---
title: Liaison Objective-C
description: Ce document fournit des liens vers les différents guides qui expliquent comment créer C# liaisons au code Objective-C, permettant aux développeurs de consommer des bibliothèques prêts à l’emploi dans les applications Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 923ec41443feff67d851a0c2f3cba1b948aed580
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864411"
---
# <a name="binding-objective-c"></a>Liaison Objective-C

Cette section comprend un grand nombre de documents qui couvrent la création de liaisons aux bibliothèques Objective-C, afin qu’ils peuvent être appelées depuis C# applications créées avec Xamarin.iOS ou Xamarin.Mac.

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Vue d’ensemble](~/cross-platform/macios/binding/overview.md)

Ce document contient certains éléments internes de la façon dont une liaison a lieu. Il est un document avec des informations techniques avancées.

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Liaison de bibliothèques Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Ce document décrit le processus utilisé pour créer C# liaisons de l’API de Objective-C et comment les idiomes en Objective-C sont mappées aux idiomes utilisés dans .NET.
Si vous liez simplement les API C, vous devez utiliser le mécanisme de .NET standard pour ce faire, l’infrastructure de P/Invoke.

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guide de référence de définition de liaison](~/cross-platform/macios/binding/binding-types-reference.md)

Il s’agit du guide de référence qui décrit tous les attributs disponibles pour les auteurs de liaison pour piloter le processus de génération de liaison.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objectif Sharpie est un outil de ligne de commande pour aider à démarrer la première passe d’une liaison. Il fonctionne en analysant les fichiers d’en-tête d’une bibliothèque native pour mapper l’API publique dans le [définition de liaison](~/cross-platform/macios/binding/objective-c-libraries.md) (un processus qui peut également être effectué manuellement).

## <a name="ios"></a>iOS

Le [page de liaison iOS](~/ios/platform/binding-objective-c/index.md) revient à ces ressources communes de la liaison, en outre les exemples ci-dessous.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Procédure pas à pas : Liaison d’une bibliothèque Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Cet article fournit une procédure de création d’un projet de liaison à l’aide de l’open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) projet Objective-C comme exemple. La bibliothèque InfColorPicker fournit un contrôleur d’affichage réutilisable permettant aux utilisateurs de sélectionner une couleur en fonction de sa représentation sous forme de TSL, effectuer une sélection de couleur plus conviviales. Objectif Sharpie permet de faciliter le processus de liaison.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Exemples de liaisons](https://github.com/mono/monotouch-bindings)

Une collection de liaisons de fournisseurs tiers qui peut être utilisé comme référence lors de la création de nouveaux projets de liaison.

## <a name="mac"></a>Mac

Historiquement [liaison de Mac](~/mac/platform/binding.md) a été un processus très manuel. Il existe actuellement un [aperçu téléchargeable](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) de prise en charge du projet de liaison Mac pour une future version de Visual Studio pour Mac.

## <a name="related-links"></a>Liens associés

- [iOS de liaison](~/ios/platform/binding-objective-c/index.md)
- [Liaison du Mac](~/mac/platform/binding.md)
