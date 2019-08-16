---
title: Personnalisation des liaisons
description: Vous pouvez personnaliser une liaison Xamarin. Android en modifiant les métadonnées qui contrôlent le processus de liaison. Ces modifications manuelles sont souvent nécessaires pour résoudre les erreurs de build et pour mettre en forme l’API résultante afin qu' C#elle soit plus cohérente avec/.net. Ces guides expliquent la structure de ces métadonnées, la façon de modifier les métadonnées et l’utilisation de JavaDoc pour récupérer les noms des paramètres de la méthode.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: e29432504f3b8554c387d277004d3cc779aade95
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524643"
---
# <a name="customizing-bindings"></a>Personnalisation des liaisons

_Vous pouvez personnaliser une liaison Xamarin. Android en modifiant les métadonnées qui contrôlent le processus de liaison. Ces modifications manuelles sont souvent nécessaires pour résoudre les erreurs de build et pour mettre en forme l’API résultante afin qu' C#elle soit plus cohérente avec/.net. Ces guides expliquent la structure de ces métadonnées, la façon de modifier les métadonnées et l’utilisation de JavaDoc pour récupérer les noms des paramètres de la méthode._


## <a name="overview"></a>Présentation
 
Xamarin. Android automatise une grande partie du processus de liaison. Toutefois, dans certains cas, une modification manuelle est nécessaire pour résoudre les problèmes suivants:

- Résolution des erreurs de build provoquées par les types manquants, les types obscurcis, les noms dupliqués, les problèmes de visibilité des classes et d’autres situations qui ne peuvent pas être résolues par les outils Xamarin. Android. 

- Modification du mappage utilisé par Xamarin. Android pour lier l’API Android à différents types dans C# (par exemple, de nombreux développeurs préfèrent mapper des `int` constantes Java C# `enum` à des constantes).

- Suppression des types inutilisés qui n’ont pas besoin d’être liés. 

- Ajout de types qui n’ont pas d’équivalent dans l’API Java sous-jacente. 

Vous pouvez apporter une partie ou la totalité de ces modifications en modifiant les métadonnées qui contrôlent le processus de liaison.


## <a name="guides"></a>Repères

Les guides suivants décrivent les métadonnées qui contrôlent le processus de liaison et expliquent comment modifier ces métadonnées pour résoudre ces problèmes:

- Les métadonnées de [liaisons Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fournissent une vue d’ensemble des métadonnées qui passent dans une liaison Java.
    Il décrit les différentes étapes manuelles qui sont parfois nécessaires à la création d’une bibliothèque de liaisons Java. il explique également comment mettre en forme une API exposée par une liaison pour mieux suivre les instructions de conception .NET.

- [Paramètres d’attribution de noms avec Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explique comment récupérer des noms de paramètres dans un projet de liaison Java à l’aide de Javadoc produit à partir du projet Java lié.


 

