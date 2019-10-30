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
ms.sourcegitcommit: 1242d32b7f072c837005cdee174abe6c0d1d0c68
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73083839"
---
# <a name="xamarinforms-effects"></a>Effets Xamarin.Forms

_Les interfaces utilisateur Xamarin. Forms sont rendues à l’aide des contrôles natifs de la plateforme cible, ce qui permet aux applications Xamarin. Forms de conserver l’apparence et la convivialité appropriées pour chaque plateforme. Les effets permettent de personnaliser les contrôles natifs sur chaque plateforme sans avoir à recourir à une implémentation de convertisseur personnalisée._

## <a name="introduction-to-effectsintroductionmd"></a>[Présentation des effets](introduction.md)

Les effets permettent de personnaliser les contrôles natifs de chaque plateforme et sont généralement utilisés pour les petits changements de style. Cet article présente les effets, décrit la limite entre les effets et les convertisseurs personnalisés, et décrit la classe `PlatformEffect`.

## <a name="creating-an-effectcreatingmd"></a>[Création d’un effet](creating.md)

Les effets simplifient la personnalisation d’un contrôle. Cet article montre comment créer un effet qui change la couleur d’arrière-plan du contrôle [`Entry`](xref:Xamarin.Forms.Entry) quand le contrôle obtient le focus.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Passage de paramètres à un effet](passing-parameters/index.md)

La configuration d’un effet avec des paramètres permet de le réutiliser. Ces articles décrivent l’utilisation de propriétés pour passer des paramètres à un effet et changer un paramètre au moment de l’exécution.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Appel d’événements à partir d’un effet](touch-tracking.md)

Les effets peuvent appeler des événements. Cet article explique comment créer un événement qui implémente le suivi du doigt multipoint de bas niveau et signale à une application les appuis tactiles, les déplacements et les libérations.

## <a name="reusable-roundeffectreusable-roundeffectmd"></a>[RoundEffect réutilisable](reusable-roundeffect.md)

RoundEffect est un effet réutilisable qui peut être appliqué à n’importe quel contrôle dérivant de VisualElement pour afficher le contrôle sous forme de cercle. Cet effet peut être utilisé pour créer des images circulaires, des boutons circulaires ou d’autres contrôles circulaires.
