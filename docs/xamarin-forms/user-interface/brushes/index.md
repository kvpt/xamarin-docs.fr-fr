---
title: Xamarin.FormsBrosse
description: La Xamarin.Forms classe Brush est une classe abstraite qui peint une zone avec sa sortie.
ms.prod: xamarin
ms.assetid: 44420FC2-304C-4175-8654-76769F79A813
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f1f56103b20eac84ce6106c0955acebf974cfe3
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918970"
---
# <a name="no-locxamarinforms-brushes"></a>Xamarin.FormsBrosse

![API d’aperçu](~/media/shared/preview.png "Cette API est actuellement en préversion.")

Un pinceau vous permet de peindre une zone, telle que l’arrière-plan d’un contrôle, à l’aide de différentes approches. La prise en charge des pinceaux dans Xamarin.Forms est disponible dans l' `Xamarin.Forms` espace de noms sur iOS, Android, MacOS, le plateforme Windows universelle (UWP) et le Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> La prise en charge des pinceaux dans Xamarin.Forms est actuellement expérimentale et ne peut être utilisée qu’en définissant l' `Brush_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).

La `Brush` classe est une classe abstraite qui peint une zone avec sa sortie. Les classes qui dérivent de `Brush` décrivent différentes façons de peindre une zone. La liste suivante décrit les différents types de pinceau disponibles dans Xamarin.Forms :

- `SolidColorBrush`, qui peint une zone avec une couleur unie. Pour plus d’informations, consultez [ Xamarin.Forms pinceaux : couleurs unies](solidcolor.md).
- `LinearGradientBrush`, qui peint une zone avec un dégradé linéaire. Pour plus d’informations, consultez [ Xamarin.Forms pinceaux : dégradés linéaires](lineargradient.md).
- `RadialGradientBrush`, qui peint une zone avec un dégradé radial. Pour plus d’informations, consultez [ Xamarin.Forms pinceaux : dégradés radiaux](radialgradient.md).

Les instances de ces types de pinceau peuvent être assignées aux `Stroke` `Fill` Propriétés et d’un `Shape` et de la `Background` propriété d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!NOTE]
> La `VisualElement.Background` propriété permet d’utiliser des pinceaux comme arrière-plan dans n’importe quel contrôle.

La `Brush` classe a également une `IsNullOrEmpty` méthode qui retourne une `bool` valeur qui indique si le pinceau est défini ou non.
