---
title: Position et distance de la carte Xamarin. Forms
description: L’espace de noms Xamarin. Forms. Maps contient un struct position qui est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, ainsi qu’un struct de distance qui peut éventuellement être utilisé lors du positionnement d’une carte.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: a68eab7bb3e6da764f5a35af4461d6af2be50ebe
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426152"
---
# <a name="xamarinforms-map-position-and-distance"></a>Position et distance de la carte Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

L’espace de noms [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) contient un struct [`Position`](xref:Xamarin.Forms.Maps.Position) qui est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, ainsi qu’un struct [`Distance`](xref:Xamarin.Forms.Maps.Distance) qui peut éventuellement être utilisé lors du positionnement d’un mappage.

## <a name="position"></a>Position

Le struct [`Position`](xref:Xamarin.Forms.Maps.Position) encapsule une position stockée en tant que valeurs de latitude et de longitude. Ce struct définit deux propriétés en lecture seule :

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), de type `double`, qui représente la latitude de la position en degrés décimaux.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), de type `double`, qui représente la longitude de la position en degrés décimaux.

les objets [`Position`](xref:Xamarin.Forms.Maps.Position) sont créés avec le constructeur `Position`, qui requiert des arguments de latitude et de longitude spécifiés comme valeurs de `double` :

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

> [!NOTE]
> Lors de la création d’un objet `Position`, la valeur de latitude est ancrée entre-90,0 et 90,0, et la valeur de longitude est ancrée entre-180,0 et 180,0.

## <a name="distance"></a>Distance

Le struct [`Distance`](xref:Xamarin.Forms.Maps.Distance) encapsule une distance stockée sous la forme d’une valeur de `double`, qui représente la distance en mètres. Ce struct définit trois propriétés en lecture seule :

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), de type `double`, qui représente la distance en kilomètres qui est comprise entre les `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), de type `double`, qui représente la distance en mètres qui est comprise entre les `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), de type `double`, qui représente la distance en miles qui est comprise entre le `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance) objets peuvent être créés avec le constructeur `Distance`, qui requiert un argument de compteurs spécifié comme `double`:

```csharp
Distance distance = new Distance(1450.5);
```

Vous pouvez également créer [`Distance`](xref:Xamarin.Forms.Maps.Distance) objets avec les méthodes de fabrique [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*), [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)et [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
