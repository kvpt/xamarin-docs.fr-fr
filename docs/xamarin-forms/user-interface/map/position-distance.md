---
title: Xamarin.FormsPosition et distance de la carte
description: Xamarin.Forms. L’espace de noms Maps contient un struct position qui est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, et d’un struct de distance qui peut éventuellement être utilisé lors du positionnement d’une carte.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2b1613789029d59e46a6d0431bfa9da1a53082e8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138396"
---
# <a name="xamarinforms-map-position-and-distance"></a>Xamarin.FormsPosition et distance de la carte

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

L' [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) espace de noms contient un [`Position`](xref:Xamarin.Forms.Maps.Position) struct qui est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, ainsi [`Distance`](xref:Xamarin.Forms.Maps.Distance) qu’un struct qui peut éventuellement être utilisé lors du positionnement d’un mappage.

## <a name="position"></a>Position

Le [`Position`](xref:Xamarin.Forms.Maps.Position) struct encapsule une position stockée en tant que valeurs de latitude et de longitude. Ce struct définit deux propriétés en lecture seule :

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), de type `double` , qui représente la latitude de la position en degrés décimaux.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), de type `double` , qui représente la longitude de la position en degrés décimaux.

[`Position`](xref:Xamarin.Forms.Maps.Position)les objets sont créés avec le `Position` constructeur, qui requiert des arguments de latitude et de longitude spécifiés en tant que `double` valeurs :

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Lors de la création d’un `Position` objet, la valeur de latitude est ancrée entre-90,0 et 90,0, et la valeur de longitude est ancrée entre-180,0 et 180,0.

> [!NOTE]
> La `GeographyUtils` classe a une `ToRadians` méthode d’extension qui convertit une `double` valeur de degrés en radians, et une `ToDegrees` méthode d’extension qui convertit une `double` valeur de radians en degrés.

## <a name="distance"></a>Distance

Le [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct encapsule une distance stockée sous la forme d’une `double` valeur, qui représente la distance en mètres. Ce struct définit trois propriétés en lecture seule :

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), de type `double` , qui représente la distance en kilomètres qui est comprise entre le `Distance` .
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), de type `double` , qui représente la distance en mètres qui est comprise entre le `Distance` .
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), de type `double` , qui représente la distance en miles qui est comprise entre le `Distance` .

[`Distance`](xref:Xamarin.Forms.Maps.Distance)les objets peuvent être créés avec le `Distance` constructeur, qui requiert un argument de compteurs spécifié en tant que `double` :

```csharp
Distance distance = new Distance(1450.5);
```

[`Distance`](xref:Xamarin.Forms.Maps.Distance)Les objets peuvent également être créés avec les méthodes de fabrique,, [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) et `BetweenPositions` :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
