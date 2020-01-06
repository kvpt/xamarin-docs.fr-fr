---
title: Liaison Objective-C
description: Ce document fournit des liens vers divers guides qui décrivent comment C# créer des liaisons au code Objective-C, ce qui permet aux développeurs d’utiliser des bibliothèques prêtes à l’emploi dans les applications Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: b7764d63991ec636043982509319e7097ef2091b
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663249"
---
# <a name="binding-objective-c"></a>Liaison Objective-C

Cette section comprend une variété de documents qui traitent de la création de liaisons aux bibliothèques objective-C, afin qu' C# elles puissent être appelées à partir d’applications créées avec Xamarin. iOS ou Xamarin. Mac.

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Vue d’ensemble](~/cross-platform/macios/binding/overview.md)

Ce document contient certains des éléments internes de la façon dont une liaison a lieu. Il s’agit d’un document avancé avec des informations techniques.

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Liaison de bibliothèques Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Ce document décrit le processus utilisé pour créer C# des liaisons d’API objective-c et comment les idiomes en objective-c sont mappés aux idiomes utilisés dans .net.
Si vous liez uniquement des API C, vous devez utiliser le mécanisme .NET standard pour cela, l’infrastructure P/Invoke.

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guide de référence de la définition de liaison](~/cross-platform/macios/binding/binding-types-reference.md)

Il s’agit du Guide de référence qui décrit tous les attributs disponibles pour lier les auteurs afin de piloter le processus de génération de liaison.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

La finesse d’objectif est un outil en ligne de commande qui permet de démarrer le premier passage d’une liaison. Il fonctionne en analysant les fichiers d’en-tête d’une bibliothèque native pour mapper l’API publique dans la [définition de liaison](~/cross-platform/macios/binding/objective-c-libraries.md) (un processus qui peut également être effectué manuellement).

## <a name="ios"></a>iOS

La [page liaison iOS](~/ios/platform/binding-objective-c/index.md) renvoie à ces ressources de liaison communes, en plus des exemples ci-dessous.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Procédure pas à pas : liaison d’une bibliothèque objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Cet article fournit une procédure pas à pas de création d’un projet de liaison à l’aide du projet open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) objective-C, en guise d’exemple. La bibliothèque InfColorPicker fournit un contrôleur d’affichage réutilisable qui permet à l’utilisateur de sélectionner une couleur en fonction de sa représentation TSL, ce qui rend la sélection des couleurs plus conviviale. La finesse d’objectif est utilisée pour faciliter le processus de liaison.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Exemples de liaison](https://github.com/mono/monotouch-bindings)

Collection de liaisons tierces qui peuvent être utilisées comme référence lors de la création de projets de liaison.

## <a name="mac"></a>Mac

Suivez les instructions de [liaison Mac](~/mac/platform/binding.md) pour lier les bibliothèques MacOS. Vous pouvez créer une nouvelle **bibliothèque de liaisons Mac** à partir de la fenêtre **nouveau projet** :

[boîte de dialogue de projet ![fichier de nouvelles liaisons Mac](images/new-bindings-library-sml.png)](images/new-bindings-library.png#lightbox)

## <a name="related-links"></a>Liens associés

- [Liaison iOS](~/ios/platform/binding-objective-c/index.md)
- [Liaison Mac](~/mac/platform/binding.md)
