---
title: Xamarin.Forms Modèles de données
description: Un DataTemplate permet de spécifier l’apparence des données sur les contrôles pris en charge et il est généralement lié aux données à afficher.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 682ed3b0d0f54617a0238dcc1bbf413e1ee0156a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373105"
---
# <a name="no-locxamarinforms-data-templates"></a>Xamarin.Forms Modèles de données

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Un DataTemplate permet de spécifier l’apparence des données sur les contrôles pris en charge et il est généralement lié aux données à afficher._

## <a name="introduction"></a>[Introduction](introduction.md)

Xamarin.Forms les modèles de données permettent de définir la présentation des données sur les contrôles pris en charge. Cet article présente les modèles de données et explique pourquoi ils sont nécessaires.

## <a name="creating-a-datatemplate"></a>[Création d’un DataTemplate](creating.md)

Les modèles de données peuvent être créés en ligne, dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , ou à partir d’un type personnalisé ou d’un Xamarin.Forms type de cellule approprié. Vous devez utiliser un modèle inline si vous n’avez pas besoin de réutiliser le modèle de données ailleurs. Vous pouvez aussi réutiliser un modèle de données en le définissant en tant que type personnalisé, ou en tant que ressource au niveau du contrôle, de la page ou de l’application.

## <a name="creating-a-datatemplateselector"></a>[Création d’un DataTemplateSelector](selector.md)

Un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) peut être utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) au moment de l’exécution en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs instances de `DataTemplate` au même type d’objet, pour personnaliser l’apparence d’objets en particulier. Cet article montre comment créer et utiliser un `DataTemplateSelector`.

## <a name="related-links"></a>Liens associés

- [Modèles de données (exemple)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)