---
title: Xamarin.FormsEffets
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
ms.openlocfilehash: a6206d2c561df74a01b7d7408e8d542f1e2189d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139332"
---
# <a name="xamarinforms-effects"></a>Xamarin.FormsEffets

_Les interfaces utilisateur Xamarin. Forms sont rendues à l’aide des contrôles natifs de la plateforme cible, ce qui permet Xamarin.Forms aux applications de conserver l’apparence et la convivialité appropriées pour chaque plateforme. Les effets permettent de personnaliser les contrôles natifs sur chaque plateforme sans avoir à recourir à une implémentation de convertisseur personnalisée._

## <a name="introduction-to-effects"></a>[Présentation des effets](introduction.md)

Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style. Cet article présente les effets, décrit la limite entre les effets et les convertisseurs personnalisés, et décrit la classe `PlatformEffect`.

## <a name="creating-an-effect"></a>[Création d’un effet](creating.md)

Les effets simplifient la personnalisation d’un contrôle. Cet article montre comment créer un effet qui modifie la couleur d’arrière-plan du [`Entry`](xref:Xamarin.Forms.Entry) contrôle lorsque le contrôle obtient le focus.

## <a name="passing-parameters-to-an-effect"></a>[Passage de paramètres à un effet](passing-parameters/index.md)

La configuration d’un effet avec des paramètres permet de le réutiliser. Ces articles décrivent l’utilisation de propriétés pour passer des paramètres à un effet et changer un paramètre au moment de l’exécution.

## <a name="invoking-events-from-an-effect"></a>[Appel d’événements à partir d’un effet](touch-tracking.md)

Les effets peuvent appeler des événements. Cet article explique comment créer un événement qui implémente le suivi du doigt multipoint de bas niveau et signale à une application les appuis tactiles, les déplacements et les libérations.

## <a name="reusable-roundeffect"></a>[RoundEffect réutilisable](reusable-roundeffect.md)

RoundEffect est un effet réutilisable qui peut être appliqué à n’importe quel contrôle dérivant de VisualElement pour afficher le contrôle sous forme de cercle. Cet effet peut être utilisé pour créer des images circulaires, des boutons circulaires ou d’autres contrôles circulaires.
