---
title: Modèles de données Xamarin.Forms
description: Un DataTemplate permet de spécifier l’apparence des données sur les contrôles pris en charge et il est généralement lié aux données à afficher.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 6cb828f59505159c972d40905f22995055f207df
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646855"
---
# <a name="xamarinforms-data-templates"></a>Modèles de données Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Un DataTemplate permet de spécifier l’apparence des données sur les contrôles pris en charge et il est généralement lié aux données à afficher._

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

Les modèles de données Xamarin.Forms permettent de définir la présentation des données sur les contrôles pris en charge. Cet article présente les modèles de données et explique pourquoi ils sont nécessaires.

## <a name="creating-a-datatemplatecreatingmd"></a>[Création d’un DataTemplate](creating.md)

Les modèles de données peuvent être créés inline, dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), ou à partir d’un type personnalisé ou d’un type de cellule Xamarin.Forms approprié. Vous devez utiliser un modèle inline si vous n’avez pas besoin de réutiliser le modèle de données ailleurs. Vous pouvez aussi réutiliser un modèle de données en le définissant comme un type personnalisé, ou comme une ressource au niveau du contrôle, de la page ou de l’application.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Création d’un DataTemplateSelector](selector.md)

Un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) peut être utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) au moment de l’exécution selon la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs instances de `DataTemplate` au même type d’objet, pour personnaliser l’apparence d’objets en particulier. Cet article montre comment créer et utiliser un `DataTemplateSelector`.


## <a name="related-links"></a>Liens associés

- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
