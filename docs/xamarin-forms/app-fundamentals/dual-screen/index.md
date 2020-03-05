---
title: Xamarin.Forms pour appareils double écran
description: Ce guide explique comment créer des applications Xamarin.Forms pour des appareils double écran.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 344b6293090ffa4281ea6351f7f176a5be37e5bd
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165555"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms pour appareils double écran

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) et Surface Neo (Windows 10X) introduisent de nouveaux modèles pour les applications tactiles. Xamarin.Forms inclut les classes `TwoPaneView` et `DualScreenInfo`, que vous pouvez utiliser pour développer des applications pour ces appareils.

## <a name="dual-screen-design-patterns"></a>[Modèles de conception à deux écrans](design-patterns.md)

Si vous vous demandez quelle est la meilleure façon d’utiliser plusieurs écrans sur un appareil double écran, reportez-vous à cette aide sur les modèles pour trouver la solution la mieux adaptée à l’interface de votre application.

## <a name="dual-screen-layout"></a>[Disposition sur deux écrans](twopaneview.md)

La classe `TwoPaneView` de Xamarin.Forms, inspirée par le contrôle UWP du même nom, est une disposition multiplateforme optimisée pour les appareils double écran.

## <a name="dual-screen-device-capabilities"></a>[Fonctionnalités de l’appareil à deux écrans](dual-screen-info.md)

La classe `DualScreenInfo` vous permet de déterminer le volet où se trouve votre vue, sa taille, la position de l’appareil, l’angle de la charnière, etc.

## <a name="dual-screen-platform-helpers"></a>[Applications auxiliaires de plateforme à deux écrans](dual-screen-helper.md)

La classe `DualScreenHelper` vous permet de vérifier si la plateforme prend en charge l’ouverture d’une nouvelle fenêtre en mode Image dans l’image. Pour Neo, ceci vous permet d’ouvrir une fenêtre qui s’affiche dans le Wonderbar quand l’appareil est en mode composition.

## <a name="dual-screen-triggers"></a>[Déclencheurs à deux écrans](triggers.md)

L’espace de noms [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) comprend deux déclencheurs d’État qui déclenchent une modification de [`VisualState`](xref:Xamarin.Forms.VisualState) lorsque le mode d’affichage de la disposition attachée, ou fenêtre, change.
