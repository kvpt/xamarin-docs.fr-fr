---
title: Personnalisation des liaisons
description: Vous pouvez personnaliser une liaison Xamarin.Android en modifiant les métadonnées qui contrôlent le processus de liaison. Ces modifications manuelles sont souvent nécessaires pour résoudre les erreurs de construction et pour façonner l’API résultant afin qu’il soit plus compatible avec C/.NET. Ces guides expliquent la structure de ces métadonnées, comment modifier les métadonnées, et comment utiliser JavaDoc pour récupérer les noms des paramètres de la méthode.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020656"
---
# <a name="customizing-bindings"></a>Personnalisation des liaisons

_Vous pouvez personnaliser une liaison Xamarin.Android en modifiant les métadonnées qui contrôlent le processus de liaison. Ces modifications manuelles sont souvent nécessaires pour résoudre les erreurs de construction et pour façonner l’API résultant afin qu’il soit plus compatible avec C/.NET. Ces guides expliquent la structure de ces métadonnées, comment modifier les métadonnées, et comment utiliser JavaDoc pour récupérer les noms des paramètres de la méthode._

## <a name="overview"></a>Vue d’ensemble

Xamarin.Android automatise une grande partie du processus de liaison; toutefois, dans certains cas, une modification manuelle est nécessaire pour régler les questions suivantes :

- Résoudre les erreurs de construction causées par les types manquants, les types obscurcis, les noms en double, les problèmes de visibilité de classe, et d’autres situations qui ne peuvent pas être résolues par l’outillage Xamarin.Android. 

- Changer la cartographie que Xamarin.Android utilise pour lier l’API Android à différents `int` types de C `enum` (par exemple, de nombreux développeurs préfèrent cartographier les constantes Java à C ' constantes).

- Suppression des types inutilisés qui n’ont pas besoin d’être liés. 

- Ajout de types qui n’ont pas de contrepartie dans l’API Java sous-jacente. 

Vous pouvez effectuer une partie ou la totalité de ces modifications en modifiant les métadonnées qui contrôlent le processus de liaison.

## <a name="guides"></a>Guides

Les guides suivants décrivent les métadonnées qui contrôlent le processus contraignant et expliquent comment modifier ces métadonnées pour résoudre ces problèmes :

- [Java Bindings Metadata](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fournit un aperçu des métadonnées qui entrent dans une liaison Java.
    Il décrit les différentes étapes manuelles qui sont parfois nécessaires pour compléter une bibliothèque de liaison Java, et il explique comment façonner une API exposée par une liaison pour suivre de plus près .NET lignes directrices de conception.

- [Nommer des paramètres avec Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) explique comment récupérer les noms de paramètres dans un projet de liaison Java en utilisant Javadoc produit à partir du projet Java lié.
