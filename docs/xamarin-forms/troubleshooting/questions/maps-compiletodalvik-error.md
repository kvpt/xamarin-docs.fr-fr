---
title: Pourquoi mon projet Android Xamarin. Forms. Maps échoue-t-il avec COMPILETODALVIK erreur inattendue de niveau supérieur?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: efa21d3547167996e1a7dcc533caa00e0b1262e6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529032"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Pourquoi mon projet Android Xamarin. Forms. Maps échoue-t-il avec COMPILETODALVIK erreur inattendue de niveau supérieur?

Cette erreur peut se produire dans le bloc d’erreurs de Visual Studio pour Mac ou dans la fenêtre sortie de la génération de Visual Studio. dans les projets Android utilisant Xamarin. Forms. Maps.

Cela est généralement résolu en accroissant la taille du tas Java pour votre projet Xamarin. Android. Pour augmenter la taille du tas, procédez comme suit:

## <a name="visual-studio"></a>Visual Studio

1. Cliquez avec le bouton droit sur le projet Android & ouvrir les options du projet.
2. Accéder aux **options Android-> avancé**
3. Dans la zone de texte taille du tas Java, entrez 1G.
4. Regénérez le projet.

![Capture d’écran des options de projet Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Options de génération Android dans Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. Cliquez avec le bouton droit sur le projet Android & ouvrir les options du projet.
2. Accédez à **Build-> Android Build-> Advanced**
3. Dans la zone de texte taille du tas Java, entrez 1G.
4. Regénérez le projet.  

![Capture d’écran des options du projet Visual Studio pour Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Options de génération Android dans Visual Studio pour Mac")

