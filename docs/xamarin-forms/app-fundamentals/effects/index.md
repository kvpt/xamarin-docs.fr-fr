---
title: Effets Xamarin.Forms
description: Les effets permettent de personnaliser les contrôles natifs de chaque plateforme sans avoir à implémenter de convertisseur personnalisé.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 2de1d1dd065a01bb457ebf03acdc0c01529abf7b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73083839"
---
# <a name="xamarinforms-effects"></a>Effets Xamarin.Forms

_Les interfaces utilisateur Xamarin.Forms sont rendues à l’aide des commandes natives de la plate-forme cible, ce qui permet aux applications Xamarin.Forms de conserver l’apparence et la sensation appropriées pour chaque plate-forme. Les effets permettent de personnaliser les commandes natives sur chaque plate-forme sans avoir à recourir à une implémentation de rendu personnalisé._

## <a name="introduction-to-effects"></a>[Présentation des effets](introduction.md)

Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style. Cet article présente les effets, décrit la limite entre les effets et les convertisseurs personnalisés, et décrit la classe `PlatformEffect`.

## <a name="creating-an-effect"></a>[Création d’un effet](creating.md)

Les effets simplifient la personnalisation d’un contrôle. Cet article démontre comment créer un effet qui [`Entry`](xref:Xamarin.Forms.Entry) modifie la couleur de fond du contrôle lorsque le contrôle gagne la mise au point.

## <a name="passing-parameters-to-an-effect"></a>[Passage de paramètres à un effet](passing-parameters/index.md)

La configuration d’un effet avec des paramètres permet de le réutiliser. Ces articles décrivent l’utilisation de propriétés pour passer des paramètres à un effet et changer un paramètre au moment de l’exécution.

## <a name="invoking-events-from-an-effect"></a>[Appel d’événements à partir d’un effet](touch-tracking.md)

Les effets peuvent appeler des événements. Cet article explique comment créer un événement qui implémente le suivi du doigt multipoint de bas niveau et signale à une application les appuis tactiles, les déplacements et les libérations.

## <a name="reusable-roundeffect"></a>[RoundEffect réutilisable](reusable-roundeffect.md)

RoundEffect est un effet réutilisable qui peut être appliqué à n’importe quel contrôle provenant de VisualElement pour rendre le contrôle en tant que cercle. Cet effet peut être utilisé pour créer des images circulaires, des boutons circulaires ou d’autres commandes circulaires.
