---
title: Fonctionnalités Xamarin.Forms pour appareils double écran
description: Ce guide explique comment Xamarin.Forms peut facilement adapter des applications à des appareils double écran.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1f648297a608c592d90f2c70494ae4496fe73c0f
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145513"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms pour appareils double écran

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) et Surface Neo (Windows 10X) introduisent de nouveaux modèles pour les applications tactiles. Xamarin.Forms fournit `TwoPaneView` et `DualScreenInfo` pour vous permettre de tirer pleinement parti de ces nouvelles expériences.

## <a name="dual-screen-patternsdesign-patternsmd"></a>[Modèles double écran](design-patterns.md)

Si vous envisagez la meilleure façon d’utiliser plusieurs écrans sur un appareil double écran, reportez-vous à notre aide sur les modèles pour trouver la solution la mieux adaptée à l’interface de votre application.

## <a name="twopaneviewtwopaneviewmd"></a>[TwoPaneView](twopaneview.md)

La classe Xamarin.Forms `TwoPaneView`, inspirée par le contrôle UWP du même nom, introduit une disposition optimisée pour les appareils double écran sur plusieurs plateformes.

## <a name="dualscreeninfodual-screen-infomd"></a>[DualScreenInfo](dual-screen-info.md)

Pour tirer le meilleur parti des appareils double écran, il peut être utile de savoir sur quel volet se trouve votre vue, sa taille, la posture de l’appareil, l’angle de la charnière, etc. `DualScreenInfo` fournit ces informations.

## <a name="dualscreenhelperdual-screen-helpermd"></a>[DualScreenHelper](dual-screen-helper.md)
`DualScreenHelper` vous permet de vérifier si la plateforme prend en charge l’ouverture d’une nouvelle fenêtre en mode Image dans l’image. Pour Neo, cela vous permet d’ouvrir une fenêtre qui s’affiche dans le Wonderbar quand l’appareil est en mode composition.