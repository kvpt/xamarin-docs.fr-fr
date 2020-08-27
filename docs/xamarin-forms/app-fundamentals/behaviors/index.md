---
title: Xamarin.Forms Comportements
description: Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. Les comportements sont écrits dans le code et ajoutés aux contrôles dans le code ou en XAML.
ms.prod: xamarin
ms.assetid: 42E32AD7-8E3B-48B3-B402-E75B758DA913
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d917d7d6421cfae7fc877c81023a835573fa99b1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964621"
---
# <a name="no-locxamarinforms-behaviors"></a>Xamarin.Forms Comportements

_Les comportements vous permettent d’ajouter des fonctionnalités aux contrôles d’interface utilisateur sans avoir à les sous-classer. Les comportements sont écrits en code et ajoutés aux contrôles en XAML ou dans le code._

## <a name="introduction-to-behaviors"></a>[Introduction aux comportements](introduction.md)

Les comportements vous permettent d’implémenter du code au lieu de l’écrire sous forme de code-behind, car il interagit directement avec l’API du contrôle de manière à être joint succinctement au contrôle. Cet article offre une introduction aux comportements.

## <a name="attached-behaviors"></a>[Comportements attachés](attached.md)

Les comportements attachés sont des classes `static` avec une ou plusieurs propriétés attachées. Cet article montre comment créer et utiliser des comportements attachés.

## <a name="no-locxamarinforms-behaviors"></a>[Xamarin.Forms Comportements](creating.md)

Xamarin.Forms les comportements sont créés par dérivation à partir de [`Behavior`](xref:Xamarin.Forms.Behavior) la [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou. Cet article montre comment créer et utiliser des Xamarin.Forms comportements.

## <a name="reusable-effectbehavior"></a>[EffectBehavior réutilisable](effect-behavior.md)

Les comportements sont une approche utile pour ajouter un effet à un contrôle, tout en supprimant le code de gestion de l’effet réutilisable dans les fichiers code-behind. Cet article décrit la création et l’utilisation d’un Xamarin.Forms comportement pour ajouter un effet à un contrôle.
