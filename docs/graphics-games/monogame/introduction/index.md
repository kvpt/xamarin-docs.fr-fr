---
title: Présentation du développement de jeux avec monojeu
description: Cette procédure pas à pas en plusieurs parties montre comment créer une application 2D simple à l’aide d’un monojeu.  Il couvre les concepts courants de la programmation de jeux, tels que les graphiques, les entrées, les entités de jeu et la physique.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 47ed7fc1b4485864646a17940aceed395a4a8983
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680910"
---
# <a name="introduction-to-game-development-with-monogame"></a>Présentation du développement de jeux avec monojeu

_Cette procédure pas à pas en plusieurs parties montre comment créer une application 2D simple à l’aide d’un monojeu.  Il couvre les concepts courants de la programmation de jeux, tels que les graphiques, les entrées, les entités de jeu et la physique._

Cet article décrit la technologie des API monojeu pour créer des jeux multiplateformes. Pour obtenir la liste complète des plateformes, consultez le [site Web monojeu](http://www.monogame.net/). Ce didacticiel va utiliser C# pour les exemples de code, bien que le monojeu F# soit entièrement opérationnel avec.

Monogame est une API multiplateformes, accélérée sur le matériel, qui fournit des fonctionnalités graphiques, audio, de gestion de l’état du jeu, d’entrée et un pipeline de contenu pour l’importation des ressources. Contrairement à la plupart des moteurs de jeu, le monojeu ne fournit ni n’impose aucune structure de projet ou de modèle.  Bien que cela signifie que les développeurs sont libres d’organiser leur code comme ils le souhaitent, cela signifie également qu’un peu de code d’installation est nécessaire lors du premier démarrage d’un nouveau projet.

La première section de cette procédure pas à pas se concentre sur la configuration d’un projet vide. La dernière section couvre l’écriture de la logique et du contenu de votre jeu, dont la plupart seront multiplateformes.

À la fin de cette procédure pas à pas, nous allons créer un jeu simple où le joueur peut contrôler un personnage animé avec entrée tactile.  Bien qu’il ne s’agisse pas techniquement d’un jeu complet (dans la mesure où il n’a pas de conditions gagnantes ou perdues), il montre de nombreux concepts de développement de jeux et peut servir de base pour de nombreux types de jeux. 

Le résultat de cette procédure pas à pas est le suivant:

![Animation d’un exemple de jeu de caractères à la suite de la souris](images/image1.gif)

## <a name="monogame-and-xna"></a>Monojeu et XNA

La bibliothèque monojeu est conçue pour imiter la bibliothèque XNA de Microsoft dans la syntaxe et les fonctionnalités.  Tous les objets monojeu existent sous l’espace de noms Microsoft. XNA, ce qui permet l’utilisation de la plupart du code XNA en monojeu sans aucune modification. 

Les développeurs familiarisés avec XNA seront déjà familiarisés avec la syntaxe de monojeu, et les développeurs qui recherchent des informations supplémentaires sur l’utilisation du monojeu pourront faire référence à des procédures pas à pas de XNA en ligne existantes, à la documentation des API et aux discussions.


## <a name="walkthrough-parts"></a>Parties de procédure pas à pas

- [Partie 1: création d’un projet monogame multiplateforme](~/graphics-games/monogame/introduction/part1.md)
- [Partie 2: implémentation du WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Liens associés

- [Projet monogame WalkingGame (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
- [Polices XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB polices Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [Android monojeu sur NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS monogame sur NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentation sur les API monojeu](http://www.monogame.net/documentation/?page=main)
