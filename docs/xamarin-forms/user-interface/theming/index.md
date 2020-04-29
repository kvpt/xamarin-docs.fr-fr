---
title: Ajout d’un thème à une application Xamarin.Forms
description: Les applications Xamarin. Forms prennent en charge les thèmes en créant un ResourceDictionary pour chaque thème, puis en chargeant les ressources avec l’extension de balisage DynamicResource.
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: 5988437b40ac875b8b59f9af0f25d4b5c60ded97
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517131"
---
# <a name="theming-a-xamarinforms-application"></a>Thèmes d’une application Xamarin. Forms

## <a name="theme-an-application"></a>[Thème d’une application](theming.md)

Ils peuvent être implémentés dans les applications Xamarin. Forms en [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) créant un pour chaque thème, puis en chargeant les ressources `DynamicResource` avec l’extension de balisage.

## <a name="respond-to-system-theme-changes"></a>[Répondre aux modifications du thème du système](system-theme-changes.md)

Les appareils incluent généralement des thèmes clairs et foncés, qui font chacun référence à un large éventail de préférences d’apparence qui peuvent être définies au niveau du système d’exploitation. Les applications doivent respecter ces thèmes système et répondre immédiatement lorsque le thème du système change.
