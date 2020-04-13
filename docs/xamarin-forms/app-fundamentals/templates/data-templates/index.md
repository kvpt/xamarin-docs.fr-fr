---
title: Modèles de données Xamarin.Forms
description: Un DataTemplate permet de spécifier l’apparence des données sur les contrôles pris en charge et il est généralement lié aux données à afficher.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 5d130a6644af4e5831263c6de137513c021e0b6a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760802"
---
# <a name="xamarinforms-data-templates"></a>Modèles de données Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Un DataTemplate permet de spécifier l’apparence des données sur les contrôles pris en charge et il est généralement lié aux données à afficher._

## <a name="introduction"></a>[Introduction](introduction.md)

Les modèles de données Xamarin.Forms permettent de définir la présentation des données sur les contrôles pris en charge. Cet article présente les modèles de données et explique pourquoi ils sont nécessaires.

## <a name="creating-a-datatemplate"></a>[Création d’un DataTemplate](creating.md)

Les modèles de données peuvent être [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)créés en ligne, dans un, ou à partir d’un type personnalisé ou approprié Xamarin.Forms type de cellule. Vous devez utiliser un modèle inline si vous n’avez pas besoin de réutiliser le modèle de données ailleurs. Vous pouvez aussi réutiliser un modèle de données en le définissant en tant que type personnalisé, ou en tant que ressource au niveau du contrôle, de la page ou de l’application.

## <a name="creating-a-datatemplateselector"></a>[Création d’un DataTemplateSelector](selector.md)

A [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) peut être utilisé [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour choisir un à l’heure d’exécution en fonction de la valeur d’une propriété liée aux données. Cela permet d’appliquer plusieurs instances de `DataTemplate` au même type d’objet, pour personnaliser l’apparence d’objets en particulier. Cet article montre comment créer et utiliser un `DataTemplateSelector`.

## <a name="related-links"></a>Liens connexes

- [Modèles de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
