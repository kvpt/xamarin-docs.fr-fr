---
title: Fichiers ApiDefinitions & StructsAndEnums
description: Ce document décrit les fichiers ApiDefinitions.cs et StructsAndEnums.cs générés par. Ces fichiers sont ensuite utilisés pour accéder au code Objective-C à partir de C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 8d4a05745d8d2ec6e05abd519aef4b9827655e06
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930427"
---
# <a name="apidefinitions--structsandenums-files"></a>Fichiers ApiDefinitions & StructsAndEnums

En cas de réussite de l’exécution d’objective Sharp, `Binding/ApiDefinitions.cs` les fichiers et sont générés `Binding/StructsAndEnums.cs` .
Ces deux fichiers sont ajoutés à un projet de liaison dans Visual Studio pour Mac ou passés directement aux `btouch` `bmac` outils ou pour produire la liaison finale.

Dans *certains* cas, ces fichiers générés peuvent être tout ce dont vous avez besoin, mais plus souvent, le développeur doit modifier manuellement ces fichiers générés pour résoudre les problèmes qui n’ont pas pu être gérés automatiquement par l’outil (tels que ceux signalés par un [ `Verify` attribut](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Voici quelques-unes des étapes suivantes :

- **Modification des noms**: parfois, vous souhaiterez peut-être ajuster les noms des méthodes et des classes pour qu’elles correspondent aux règles de conception de .NET Framework.
- **Méthodes ou propriétés**: les heuristiques utilisées par la netteté objective sélectionnent parfois une méthode qui doit être transformée en propriété. À ce stade, vous pouvez décider s’il s’agit ou non du comportement prévu.
- **Événements de raccordement**: vous pouvez lier vos classes à vos classes déléguées et générer automatiquement des événements pour ceux-ci.
- **Notifications de raccordement**: il n’est pas possible d’extraire le contrat d’API des notifications à partir des fichiers d’en-tête purs. cela nécessitera un trajet vers la documentation de l’API. Si vous souhaitez recevoir des notifications fortement typées, vous devrez mettre à jour le résultat.
- Réalisation d' **API**: à ce stade, vous pouvez choisir de fournir des constructeurs supplémentaires, d’ajouter des méthodes (pour permettre la syntaxe d’initialisation en C#), de surcharger des opérateurs et d’implémenter vos propres interfaces sur le fichier de définitions supplémentaire.

Consultez la description de la [liaison d’une API](~/cross-platform/macios/binding/objective-c-libraries.md) pour voir comment ces fichiers s’inscrivent dans le processus de liaison, comme indiqué dans le diagramme ci-dessous :

![Le processus de liaison est illustré dans ce diagramme](apidefinitions-structsandenums-images/binding-flowchart.png)

Pour plus d’informations sur le contenu de ces fichiers, reportez-vous à la référence relative aux [types de liaison](~/cross-platform/macios/binding/binding-types-reference.md) .
