---
title: Position et distance de la carte Xamarin. Forms
description: L’espace de noms Xamarin. Forms. Maps contient un struct position qui est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, ainsi qu’un struct de distance qui peut éventuellement être utilisé lors du positionnement d’une carte.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: 567e1b5620378f0c1b64d17c56c8fb451f18abc3
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517448"
---
# <a name="xamarinforms-map-position-and-distance"></a>Position et distance de la carte Xamarin. Forms

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

L' [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) espace de noms [`Position`](xref:Xamarin.Forms.Maps.Position) contient un struct qui est généralement utilisé lors du positionnement d’un mappage et de [`Distance`](xref:Xamarin.Forms.Maps.Distance) ses codes confidentiels, ainsi qu’un struct qui peut éventuellement être utilisé lors du positionnement d’un mappage.

## <a name="position"></a>Position

Le [`Position`](xref:Xamarin.Forms.Maps.Position) struct encapsule une position stockée en tant que valeurs de latitude et de longitude. Ce struct définit deux propriétés en lecture seule :

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), de type `double`, qui représente la latitude de la position en degrés décimaux.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), de type `double`, qui représente la longitude de la position en degrés décimaux.

[`Position`](xref:Xamarin.Forms.Maps.Position)les objets sont créés avec `Position` le constructeur, qui requiert des arguments de latitude et `double` de longitude spécifiés en tant que valeurs :

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Lors de la `Position` création d’un objet, la valeur de latitude est ancrée entre-90,0 et 90,0, et la valeur de longitude est ancrée entre-180,0 et 180,0.

> [!NOTE]
> La `GeographyUtils` classe a une `ToRadians` méthode d’extension qui convertit une `double` valeur de degrés en radians, `ToDegrees` et une méthode d’extension qui `double` convertit une valeur de radians en degrés.

## <a name="distance"></a>Distance

Le [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct encapsule une distance stockée sous `double` la forme d’une valeur, qui représente la distance en mètres. Ce struct définit trois propriétés en lecture seule :

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), de type `double`, qui représente la distance en kilomètres qui est comprise entre le `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), de type `double`, qui représente la distance en mètres qui est comprise entre le `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), de type `double`, qui représente la distance en miles qui est comprise entre le `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance)les objets peuvent être créés avec `Distance` le constructeur, qui requiert un argument de compteurs spécifié `double`en tant que :

```csharp
Distance distance = new Distance(1450.5);
```

[`Distance`](xref:Xamarin.Forms.Maps.Distance) Les objets [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*)peuvent également être créés avec les [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*)méthodes de fabrique,, `BetweenPositions` et :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
