---
title: Animation dansXamarin.Forms
description: Xamarin.Formscomprend sa propre infrastructure d’animation qui est simple pour la création d’animations simples, tout en étant suffisamment polyvalente pour créer des animations complexes.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 88a671c4d28d62a5f73e90a7b2fa9c45b7dbe8b1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128997"
---
# <a name="animation-in-xamarinforms"></a>Animation dansXamarin.Forms

_Xamarin. Forms comprend sa propre infrastructure d’animation qui est simple pour créer des animations simples, tout en étant suffisamment polyvalente pour créer des animations complexes._

Les Xamarin.Forms classes d’animation ciblent différentes propriétés d’éléments visuels, avec une animation type qui change progressivement une propriété d’une valeur à une autre sur une période donnée. Notez qu’il n’existe aucune interface XAML pour les Xamarin.Forms classes d’animation. Toutefois, les animations peuvent être encapsulées dans des [comportements](~/xamarin-forms/app-fundamentals/behaviors/index.md) , puis référencées à partir de XAML.

## <a name="simple-animations"></a>[Animations simples](simple.md)

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fournit des méthodes d’extension qui peuvent être utilisées pour construire des animations simples qui font pivoter, mettre à l’échelle, traduire et estomper des [`VisualElement`](xref:Xamarin.Forms.VisualElement) instances. Cet article explique comment créer et annuler des animations à l’aide de la `ViewExtensions` classe.

## <a name="easing-functions"></a>[Fonctions d'accélération](easing.md)

Xamarin.Formscomprend une [`Easing`](xref:Xamarin.Forms.Easing) classe qui vous permet de spécifier une fonction de transfert qui contrôle la vitesse à laquelle les animations augmentent ou ralentissent lorsqu’elles sont en cours d’exécution. Cet article montre comment utiliser les fonctions d’accélération prédéfinies et comment créer des fonctions d’accélération personnalisées.

## <a name="custom-animations"></a>[Animations personnalisées](custom.md)

La [`Animation`](xref:Xamarin.Forms.Animation) classe est le bloc de construction de toutes les Xamarin.Forms animations, avec les méthodes d’extension dans la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe qui crée un ou plusieurs `Animation` objets. Cet article explique comment utiliser la `Animation` classe pour créer et annuler des animations, synchroniser plusieurs animations et créer des animations personnalisées qui animent des propriétés qui ne sont pas animées par les méthodes d’animation existantes.
