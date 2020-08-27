---
title: Introduction aux comportements
description: Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. En effet, vous implémentez les fonctionnalités dans une classe de comportement et les attachez au contrôle comme si elles en faisaient partie. Cet article offre une introduction aux comportements.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37c76a5f325c363a92c2a2c1e597dab28f064cd9
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964608"
---
# <a name="introduction-to-behaviors"></a>Introduction aux comportements

_Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. Au lieu de cela, la fonctionnalité est implémentée dans une classe de comportement et attachée au contrôle comme si elle faisait partie du contrôle lui-même. Cet article fournit une introduction aux comportements._

Avec les comportements, vous pouvez implémenter du code que vous auriez dû normalement écrire sous forme de code-behind, car celui-ci interagit directement avec l’API du contrôle de manière à être attaché directement au contrôle et empaqueté pour être réutilisé dans plusieurs applications. Vous pouvez les utiliser pour fournir une série complète de fonctionnalités aux contrôles, comme :

- Ajout d’un validateur de messagerie à un [`Entry`](xref:Xamarin.Forms.Entry) .
- Créer un contrôle d’évaluation avec un module de reconnaissance de mouvement de pression.
- Contrôler une animation.
- Ajouter un effet à un contrôle.

Les comportements permettent également des scénarios plus avancés. Dans le contexte de *commande*, les comportements constituent une approche utile pour connecter un contrôle à une commande. De plus, ils peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. Par exemple, ils peuvent servir pour appeler une commande en réponse à un déclenchement d’événement.

Xamarin.Forms prend en charge deux styles différents de comportements :

- ** Xamarin.Forms comportements** : classes qui dérivent de [`Behavior`](xref:Xamarin.Forms.Behavior) la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou, où `T` est le type du contrôle auquel le comportement doit s’appliquer. Pour plus d’informations sur les Xamarin.Forms comportements, consultez [ Xamarin.Forms comportements](~/xamarin-forms/app-fundamentals/behaviors/creating.md).
- **Comportements attachés** : `static` classes avec une ou plusieurs propriétés jointes. Pour plus d’informations sur les comportements attachés, consultez [Comportements attachés](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Ce guide se concentre sur Xamarin.Forms les comportements, car il s’agit de l’approche préférée de la construction de comportement.

## <a name="related-links"></a>Liens associés

- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
