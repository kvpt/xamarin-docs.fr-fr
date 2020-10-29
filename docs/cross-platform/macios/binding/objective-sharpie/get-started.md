---
title: Prise en main avec une grande finesse objective
description: Ce document fournit une vue d’ensemble de haut niveau de la netteté objective, l’outil utilisé pour automatiser la création de liaisons C# au code Objective-C.
ms.prod: xamarin
ms.assetid: 577512BF-1A90-41E5-89DE-9E056C478678
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: 5bee8df72290cab3ed6d5c23fef6c2ae2f1a2559
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928514"
---
# <a name="getting-started-with-objective-sharpie"></a>Prise en main avec une grande finesse objective

> [!IMPORTANT]
> La finesse d’objectif est un outil destiné aux développeurs Xamarin expérimentés ayant une connaissance approfondie de Objective-C (et de l’extension C). Avant de tenter de lier une bibliothèque objective-C, vous devez avoir une connaissance approfondie de la façon de générer la bibliothèque native sur la ligne de commande (et de bien comprendre le fonctionnement de la bibliothèque native).

<a name="installing"></a>

## <a name="installing-objective-sharpie"></a>Installation de la finesse objective

La finesse d’objectif est actuellement un outil en ligne de commande autonome pour Mac OS X 10,10 et versions ultérieures, et n’est _pas un produit Xamarin entièrement pris en charge_ . Elle doit être utilisée uniquement par les développeurs avancés pour faciliter la création d’un projet de liaison à une bibliothèque objective-C tierce.

Vous pouvez télécharger objective Sharp en tant que programme d’installation standard du package OS X.
Exécutez le programme d’installation et suivez toutes les invites à l’écran de l’Assistant Installation de :

- **Version actuelle : 3,4**
  - [Télécharger la dernière version Release](https://aka.ms/objective-sharpie)
  - [Annonce du Forum](https://forums.xamarin.com/discussion/104800/objective-sharpie-3-4)

> [!TIP]
> Utilisez la `sharpie update` commande pour mettre à jour vers la version la plus récente.

## <a name="basic-walkthrough"></a>Procédure pas à pas de base

La finesse d’objectif est un outil en ligne de commande fourni par Xamarin qui facilite la création des définitions requises pour lier une bibliothèque objective-C tierce à C#.
Même en cas d’utilisation de la netteté objective, *le développeur doit* modifier les fichiers générés une fois que l’outil a terminé son achèvement pour résoudre les problèmes qui n’ont pas pu être gérés automatiquement par l’outil.

Dans la mesure du possible, objective Sharp annote les API avec lesquelles il n’y a aucun doute sur la façon de lier correctement (de nombreuses constructions dans le code natif sont ambiguës).
Ces annotations s’affichent en tant qu' [ `[Verify]` attributs](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md).

La sortie de l’ensemble d’objectifs est une paire de fichiers- [ `ApiDefinition.cs` et `StructsAndEnums.cs` ](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) , qui peut être utilisée pour créer un projet de liaison qui se compile en une bibliothèque que vous pouvez utiliser dans les applications Xamarin.

> [!IMPORTANT]
> L’utilisation d’une règle importante est une règle **majeure** pour une utilisation correcte : vous devez absolument lui transmettre les arguments de ligne de commande corrects du compilateur Clang afin de garantir une analyse appropriée. Cela est dû au fait que la phase d’analyse de la finesse de l’objectif est simplement un outil [implémenté sur l’API Clang libtoolal](https://clang.llvm.org/docs/LibTooling.html).

Cela signifie qu’objective Sharp possède toute la puissance de Clang (le compilateur C/Objective-C/C++ qui compile réellement la bibliothèque Native que vous liez) et toutes ses connaissances internes des fichiers d’en-tête pour la liaison.
Au lieu de traduire l' [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree) analysé en code objet, objective Sharp traduit l’AST en une liaison C# « Echafaudage » adaptée à l’entrée dans les outils de `bmac` `btouch` liaison et Xamarin.

En cas d’erreur de forte netteté pour l’analyse, cela signifie que Clang a rencontré une erreur pendant sa phase d’analyse en tentant de construire l’AST, et que vous devez déterminer pourquoi.

**Nouveau!** les tentatives de la version 3,0 prennent en charge la prise en charge directe des projets Xcode. Si une bibliothèque native a un projet XCode valide, objective Sharp peut évaluer le projet pour une cible et une configuration spécifiées afin de déduire les fichiers d’en-tête d’entrée et les indicateurs de compilateur nécessaires.

Si aucun projet XCode n’est disponible, vous devrez vous familiariser avec le projet en déposant les fichiers d’en-tête d’entrée corrects, les chemins de recherche de fichiers d’en-tête et d’autres indicateurs de compilateur nécessaires. Il est important de se rendre compte que les indicateurs de compilateur utilisés pour générer la bibliothèque Native sont les mêmes que ceux qui doivent être passés à la finesse objective. Il s’agit d’un processus plus manuel, qui nécessite un peu de familiarité avec la compilation du code natif sur la ligne de commande avec le chaîne d’outils Clang.

**Nouveau!** la version 3,0 introduit également un outil permettant de lier facilement des [CocoaPods](https://cocoapods.org) via la `sharpie pod` commande.
Si la bibliothèque qui vous intéresse est disponible sous la forme d’un CocoaPod, nous vous recommandons de commencer par tenter de lier le CocoaPod à la finesse objective (par opposition à une tentative de liaison directe avec la source).
