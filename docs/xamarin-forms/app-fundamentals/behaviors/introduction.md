---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5bee3baa08387a1ed67384ba4089d63aad08d2c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135770"
---
# <a name="introduction-to-behaviors"></a>Introduction aux comportements

_Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. Au lieu de cela, la fonctionnalité est implémentée dans une classe de comportement et attachée au contrôle comme si elle faisait partie du contrôle lui-même. Cet article fournit une introduction aux comportements._

Avec les comportements, vous pouvez implémenter du code que vous auriez dû normalement écrire sous forme de code-behind, car celui-ci interagit directement avec l’API du contrôle de manière à être attaché directement au contrôle et empaqueté pour être réutilisé dans plusieurs applications. Vous pouvez les utiliser pour fournir une série complète de fonctionnalités aux contrôles, comme :

- Ajout d’un validateur de messagerie à un [`Entry`](xref:Xamarin.Forms.Entry) .
- Créer un contrôle d’évaluation avec un module de reconnaissance de mouvement de pression.
- Contrôler une animation.
- Ajouter un effet à un contrôle.

Les comportements permettent également des scénarios plus avancés. Dans le contexte de *commande*, les comportements constituent une approche utile pour connecter un contrôle à une commande. De plus, ils peuvent être utilisés pour associer des commandes à des contrôles qui n’ont pas été conçus pour interagir avec les commandes. Par exemple, ils peuvent servir pour appeler une commande en réponse à un déclenchement d’événement.

Xamarin.Formsprend en charge deux styles différents de comportements :

- ** Xamarin.Forms comportements** : classes qui dérivent de [`Behavior`](xref:Xamarin.Forms.Behavior) la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou, où `T` est le type du contrôle auquel le comportement doit s’appliquer. Pour plus d’informations sur les Xamarin.Forms comportements, [ Xamarin.Forms ](~/xamarin-forms/app-fundamentals/behaviors/creating.md) consultez comportements et [comportements réutilisables](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Comportements attachés** : `static` classes avec une ou plusieurs propriétés jointes. Pour plus d’informations sur les comportements attachés, consultez [Comportements attachés](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Ce guide se concentre sur Xamarin.Forms les comportements, car il s’agit de l’approche préférée de la construction de comportement.

## <a name="related-links"></a>Liens connexes

- [Comportement](xref:Xamarin.Forms.Behavior)
- [Comportement &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
