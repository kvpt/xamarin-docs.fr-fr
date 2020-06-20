---
title: Xamarin.FormsDouble écran
description: Ce guide explique comment créer Xamarin.Forms des applications pour les appareils à deux écrans.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aeaaeb732adaea45446d6baf833027801abf4d2a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138903"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Formsdouble écran

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) et Surface Neo (Windows 10X) introduisent de nouveaux modèles pour les applications tactiles. Xamarin.Formscomprend `TwoPaneView` les `DualScreenInfo` classes et pour vous permettre de développer des applications pour ces appareils.

## <a name="dual-screen-design-patterns"></a>[Modèles de conception à deux écrans](design-patterns.md)

Si vous vous demandez quelle est la meilleure façon d’utiliser plusieurs écrans sur un appareil double écran, reportez-vous à cette aide sur les modèles pour trouver la solution la mieux adaptée à l’interface de votre application.

## <a name="dual-screen-layout"></a>[Disposition sur double écran](twopaneview.md)

La Xamarin.Forms `TwoPaneView` classe, inspirée par le contrôle UWP du même nom, est une disposition multiplateforme optimisée pour les appareils à deux écrans.

## <a name="dual-screen-device-capabilities"></a>[Fonctionnalités des appareils sur double écran](dual-screen-info.md)

La classe `DualScreenInfo` vous permet de déterminer le volet où se trouve votre vue, sa taille, la position de l’appareil, l’angle de la charnière, etc.

## <a name="dual-screen-platform-helpers"></a>[Assistances aux plateformes sur double écran](dual-screen-helper.md)

La classe `DualScreenHelper` vous permet de vérifier si la plateforme prend en charge l’ouverture d’une nouvelle fenêtre en mode Image dans l’image. Pour Neo, ceci vous permet d’ouvrir une fenêtre qui s’affiche dans le Wonderbar quand l’appareil est en mode composition.

## <a name="dual-screen-triggers"></a>[Déclencheurs sur double écran](triggers.md)

L' [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) espace de noms comprend deux déclencheurs d’État qui déclenchent une [`VisualState`](xref:Xamarin.Forms.VisualState) modification lorsque le mode d’affichage de la disposition attachée, ou fenêtre, change.
