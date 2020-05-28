---
title: Thèmes d’une Xamarin.Forms application
description: Xamarin.Formsles applications les prennent en charge en créant un ResourceDictionary pour chaque thème, puis en chargeant les ressources avec l’extension de balisage DynamicResource.
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80660ae7d3af0fe5948a5ae4ffdb35d2f9c2a40f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136134"
---
# <a name="theming-a-xamarinforms-application"></a>Thèmes d’une Xamarin.Forms application

## <a name="theme-an-application"></a>[Thème d’une application](theming.md)

Ils peuvent être implémentés dans les Xamarin.Forms applications en créant un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) pour chaque thème, puis en chargeant les ressources avec l' `DynamicResource` extension de balisage.

## <a name="respond-to-system-theme-changes"></a>[Répondre aux modifications du thème du système](system-theme-changes.md)

Les appareils incluent généralement des thèmes clairs et foncés, qui font chacun référence à un large éventail de préférences d’apparence qui peuvent être définies au niveau du système d’exploitation. Les applications doivent respecter ces thèmes système et répondre immédiatement lorsque le thème du système change.
