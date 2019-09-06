---
title: Fichiers ApiDefinitions & StructsAndEnums
description: Ce document décrit les fichiers ApiDefinitions.cs et StructsAndEnums.cs générés par. Ces fichiers sont ensuite utilisés pour accéder au code Objective-C C#à partir de.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 950f9149744cb8aa2abaed60ccefb416405ab110
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290781"
---
# <a name="apidefinitions--structsandenums-files"></a>Fichiers ApiDefinitions & StructsAndEnums

En cas de réussite de l’exécution d’objective `Binding/ApiDefinitions.cs` Sharp `Binding/StructsAndEnums.cs` , les fichiers et sont générés.
Ces deux fichiers sont ajoutés à un projet de liaison dans Visual Studio pour Mac ou passés directement aux `btouch` outils `bmac` ou pour produire la liaison finale.

Dans *certains* cas, ces fichiers générés peuvent être tout ce dont vous avez besoin, mais plus souvent, le développeur doit modifier manuellement ces fichiers générés pour résoudre les problèmes qui n’ont pas pu être gérés automatiquement par l’outil (tels que ceux signalés par un [ `Verify`attribut](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Voici quelques-unes des étapes suivantes :

- **Modification des noms**: Il peut arriver que vous souhaitiez ajuster les noms des méthodes et des classes pour qu’elles correspondent aux règles de conception de .NET Framework.
- **Méthodes ou propriétés**: Les heuristiques utilisées par la netteté objective sélectionnent parfois une méthode qui doit être transformée en propriété. À ce stade, vous pouvez décider s’il s’agit ou non du comportement prévu.
- **Événements de raccordement**: Vous pouvez lier vos classes à vos classes déléguées et générer automatiquement des événements pour ceux-ci.
- **Notifications de raccordement**: Il n’est pas possible d’extraire le contrat d’API des notifications à partir des fichiers d’en-tête purs. cela nécessitera un voyage vers la documentation de l’API. Si vous souhaitez recevoir des notifications fortement typées, vous devrez mettre à jour le résultat.
- L' **API**: À ce stade, vous pouvez choisir de fournir des constructeurs supplémentaires, d’ajouter des méthodes (pour C# permettre la syntaxe d’initialisation sur construction), de surcharger des opérateurs et d’implémenter vos propres interfaces sur le fichier de définitions supplémentaire.

Consultez la description de la [liaison d’une API](~/cross-platform/macios/binding/objective-c-libraries.md) pour voir comment ces fichiers s’inscrivent dans le processus de liaison, comme indiqué dans le diagramme ci-dessous :

![](apidefinitions-structsandenums-images/binding-flowchart.png "Le processus de liaison est illustré dans ce diagramme")

Pour plus d’informations sur le contenu de ces fichiers, reportez-vous à la référence relative aux [types de liaison](~/cross-platform/macios/binding/binding-types-reference.md) .

