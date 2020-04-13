---
title: Mouvements Xamarin.Forms
description: Ce guide explique comment les modules de reconnaissance de mouvement Xamarin.Forms peuvent être utilisés pour détecter l’interaction utilisateur avec des vues dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61184569"
---
# <a name="xamarinforms-gestures"></a>Mouvements Xamarin.Forms

_Modules de reconnaissance de mouvement Xamarin.Forms pouvant être utilisés pour détecter l’interaction utilisateur avec des vues dans une application Xamarin.Forms._

La classe Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) supporte le robinet, pince, panoramique et balaye les gestes sur [`View`](xref:Xamarin.Forms.View) les instances.

## <a name="adding-a-tap-gesture-recognizer"></a>[Ajout d’un module de reconnaissance des appuis](tap.md)

Un geste de robinet est utilisé pour [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) la détection du robinet et est reconnu avec la classe.

## <a name="adding-a-pinch-gesture-recognizer"></a>[Ajout d’un module de reconnaissance des pincements](pinch.md)

Un geste de pincement est utilisé pour [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) effectuer le zoom interactif et est reconnu avec la classe.

## <a name="adding-a-pan-gesture-recognizer"></a>[Ajout d’un module de reconnaissance des mouvements panoramiques](pan.md)

Un geste panoramique est utilisé pour détecter le mouvement des doigts autour de l’écran et appliquer ce mouvement au contenu, et est reconnu avec la [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="adding-a-swipe-gesture-recognizer"></a>[Ajout d’un module de reconnaissance des mouvements de balayage](swipe.md)

Un mouvement de balayage se produit quand un doigt est déplacé verticalement ou horizontalement sur l’écran. Il est souvent utilisé pour lancer l’exploration du contenu. Les gestes de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) balayage sont reconnus avec la classe.
