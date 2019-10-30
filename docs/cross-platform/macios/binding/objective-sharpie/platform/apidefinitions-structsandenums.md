---
title: Fichiers ApiDefinitions & StructsAndEnums
description: Ce document décrit les fichiers ApiDefinitions.cs et StructsAndEnums.cs générés par. Ces fichiers sont ensuite utilisés pour accéder au code Objective-C C#à partir de.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 870733554f3f69b7a0cd9b35c1b89e24c62b264d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016201"
---
# <a name="apidefinitions--structsandenums-files"></a>Fichiers ApiDefinitions & StructsAndEnums

En cas de réussite de l’exécution d’objective Sharp, les fichiers `Binding/ApiDefinitions.cs` et `Binding/StructsAndEnums.cs` sont générés.
Ces deux fichiers sont ajoutés à un projet de liaison dans Visual Studio pour Mac ou passés directement aux outils `btouch` ou `bmac` pour produire la liaison finale.

Dans *certains* cas, ces fichiers générés peuvent être tout ce dont vous avez besoin, mais plus souvent, le développeur doit modifier manuellement ces fichiers générés pour résoudre les problèmes qui n’ont pas pu être gérés automatiquement par l’outil (tels que ceux signalés par un [`Verify` attribut](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Voici quelques-unes des étapes suivantes :

- **Modification des noms**: parfois, vous souhaiterez peut-être ajuster les noms des méthodes et des classes pour qu’elles correspondent aux règles de conception de .NET Framework.
- **Méthodes ou propriétés**: les heuristiques utilisées par la netteté objective sélectionnent parfois une méthode qui doit être transformée en propriété. À ce stade, vous pouvez décider s’il s’agit ou non du comportement prévu.
- **Événements de raccordement**: vous pouvez lier vos classes à vos classes déléguées et générer automatiquement des événements pour ceux-ci.
- **Notifications de raccordement**: il n’est pas possible d’extraire le contrat d’API des notifications à partir des fichiers d’en-tête purs. cela nécessitera un trajet vers la documentation de l’API. Si vous souhaitez recevoir des notifications fortement typées, vous devrez mettre à jour le résultat.
- Réalisation d' **API**: à ce stade, vous pouvez choisir de fournir des constructeurs supplémentaires, d’ajouter des méthodes (pour C# permettre la syntaxe d’initialisation sur construction), de surcharger des opérateurs et d’implémenter vos propres interfaces sur le fichier de définitions supplémentaire.

Consultez la description de la [liaison d’une API](~/cross-platform/macios/binding/objective-c-libraries.md) pour voir comment ces fichiers s’inscrivent dans le processus de liaison, comme indiqué dans le diagramme ci-dessous :

![](apidefinitions-structsandenums-images/binding-flowchart.png "The binding process is shown in this diagram")

Pour plus d’informations sur le contenu de ces fichiers, reportez-vous à la référence relative aux [types de liaison](~/cross-platform/macios/binding/binding-types-reference.md) .
