---
title: Xamarin.Formsmouvements
description: Ce guide explique comment Xamarin.Forms les détecteurs de mouvement peuvent être utilisés pour détecter l’interaction de l’utilisateur avec les vues dans une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/04/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7528afd0971cf06eb69df4ed7c08c3fd6dcc9e22
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917830"
---
# <a name="no-locxamarinforms-gestures"></a>Xamarin.Formsmouvements

_Les détecteurs de mouvement peuvent être utilisés pour détecter l’interaction de l’utilisateur avec des vues dans une Xamarin.Forms application._

La Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) classe prend en charge le TAP, le pincement, le panoramique, le balayage et les mouvements de glisser-déplacer sur les [`View`](xref:Xamarin.Forms.View) instances.

## <a name="add-a-tap-gesture-recognizer"></a>[Ajouter un module de reconnaissance de mouvement TAP](tap.md)

Un mouvement TAP est utilisé pour la détection de TAP et est reconnu avec la [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) classe.

## <a name="add-a-pinch-gesture-recognizer"></a>[Ajouter un module de reconnaissance de mouvement de pincement](pinch.md)

Un mouvement de pincement est utilisé pour effectuer un zoom interactif et est reconnu avec la [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) classe.

## <a name="add-a-pan-gesture-recognizer"></a>[Ajouter un module de reconnaissance de mouvement de panoramique](pan.md)

Un mouvement panoramique est utilisé pour détecter le mouvement des doigts à l’écran et pour appliquer ce mouvement au contenu, et est reconnu avec la [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="add-a-swipe-gesture-recognizer"></a>[Ajouter un module de reconnaissance de mouvement de balayage](swipe.md)

Un mouvement de balayage se produit quand un doigt est déplacé verticalement ou horizontalement sur l’écran. Il est souvent utilisé pour lancer l’exploration du contenu. Les mouvements de balayage sont reconnus avec la [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe.

## <a name="add-a-drag-and-drop-gesture-recognizer"></a>[Ajouter un module de reconnaissance de mouvement glisser-déplacer](drag-and-drop.md)

Un mouvement de glisser-déplacer permet de faire glisser des éléments et leurs packages de données associés à partir d’un emplacement à l’écran à un autre emplacement à l’aide d’un mouvement continu. Les mouvements de glissement sont reconnus avec la `DragGestureRecognizer` classe et les mouvements de suppression sont reconnus avec la `DropGestureRecognizer` classe.
