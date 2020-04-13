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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78165555"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms pour appareils double écran

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) et Surface Neo (Windows 10X) introduisent de nouveaux modèles pour les applications tactiles. Xamarin.Forms inclut les classes `TwoPaneView` et `DualScreenInfo`, que vous pouvez utiliser pour développer des applications pour ces appareils.

## <a name="dual-screen-design-patterns"></a>[Modèles de conception à double écran](design-patterns.md)

Si vous vous demandez quelle est la meilleure façon d’utiliser plusieurs écrans sur un appareil double écran, reportez-vous à cette aide sur les modèles pour trouver la solution la mieux adaptée à l’interface de votre application.

## <a name="dual-screen-layout"></a>[Mise en page à double écran](twopaneview.md)

La classe `TwoPaneView` de Xamarin.Forms, inspirée par le contrôle UWP du même nom, est une disposition multiplateforme optimisée pour les appareils double écran.

## <a name="dual-screen-device-capabilities"></a>[Capacités d’appareils à double écran](dual-screen-info.md)

La classe `DualScreenInfo` vous permet de déterminer le volet où se trouve votre vue, sa taille, la position de l’appareil, l’angle de la charnière, etc.

## <a name="dual-screen-platform-helpers"></a>[Aides plates-formes à double écran](dual-screen-helper.md)

La classe `DualScreenHelper` vous permet de vérifier si la plateforme prend en charge l’ouverture d’une nouvelle fenêtre en mode Image dans l’image. Pour Neo, ceci vous permet d’ouvrir une fenêtre qui s’affiche dans le Wonderbar quand l’appareil est en mode composition.

## <a name="dual-screen-triggers"></a>[Déclencheurs à double écran](triggers.md)

L’espace [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) de nom comprend deux [`VisualState`](xref:Xamarin.Forms.VisualState) déclencheurs d’état qui déclenchent un changement lorsque le mode de vue de la mise en page attachée, ou fenêtre, change.
