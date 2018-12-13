---
title: Introduction aux comportements
description: Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. En effet, vous implémentez les fonctionnalités dans une classe de comportement et les attachez au contrôle comme si elles en faisaient partie. Cet article offre une introduction aux comportements.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 176f41d4b7349af2cf7cc49de8ba0789ad2f8c11
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995812"
---
# <a name="introduction-to-behaviors"></a>Introduction aux comportements

_Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. En effet, vous implémentez les fonctionnalités dans une classe de comportement et les attachez au contrôle comme si elles en faisaient partie. Cet article offre une introduction aux comportements._

Avec les comportements, vous pouvez implémenter du code que vous auriez dû normalement écrire sous forme de code-behind, car celui-ci interagit directement avec l’API du contrôle de manière à être attaché directement au contrôle et empaqueté pour être réutilisé dans plusieurs applications. Vous pouvez les utiliser pour fournir une série complète de fonctionnalités aux contrôles, comme :

- Ajouter un validateur d’e-mail à une [`Entry`](xref:Xamarin.Forms.Entry).
- Créer un contrôle d’évaluation avec un module de reconnaissance de mouvement de pression.
- Contrôler une animation.
- Ajouter un effet à un contrôle.

Les comportements permettent également des scénarios plus avancés. Dans le contexte de *commande*, les comportements constituent une approche utile pour connecter un contrôle à une commande. De plus, ils peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. Par exemple, ils peuvent servir pour appeler une commande en réponse à un déclenchement d’événement.

Xamarin.Forms prend en charge deux styles différents de comportements :

- **Comportements Xamarin.Forms** – classes qui dérivent de la classe [ `Behavior` ](xref:Xamarin.Forms.Behavior) ou [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1), où `T` est le type du contrôle auquel le comportement doit s’appliquer. Pour plus d’informations sur les comportements Xamarin.Forms, consultez [Comportements Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) et [Comportements réutilisables](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Comportements attachés** – `static` classes avec une ou plusieurs propriétés attachées. Pour plus d’informations sur les comportements attachés, consultez [Comportements attachés](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Ce guide se concentre sur les comportements Xamarin.Forms, car ils constituent l’approche recommandée pour la construction de comportements.



## <a name="related-links"></a>Liens associés

- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
