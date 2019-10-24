---
title: Géocodage des plans Xamarin. Forms
description: Cet article explique comment géocoder et inverser les données de la carte géocode à l’aide de la classe de géocodeur Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: ce1f6751c0381ed41058784fbea3ebedefbdac6d
ms.sourcegitcommit: e4c23187874488ff55794d0e81a9bba30d2c2cd6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72778781"
---
# <a name="xamarinforms-map-geocoding"></a>Géocodage des plans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Xamarin. Forms. Maps fournit la classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) , qui effectue une conversion entre des adresses de chaîne et des coordonnées de latitude et de longitude stockées dans des objets [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="geocode-an-address"></a>Géocoder une adresse

Une adresse postale peut être géocodée en coordonnées de latitude et de longitude en créant une [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instance et en appelant la méthode [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) sur l’instance `Geocoder` :

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

La méthode [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) accepte un argument `string` qui représente l’adresse et retourne de manière asynchrone une collection d’objets [`Position`](xref:Xamarin.Forms.Maps.Position) qui peuvent représenter l’adresse.

## <a name="reverse-geocode-an-address"></a>Inverser géocoder une adresse

Les coordonnées de latitude et de longitude peuvent être inversées géocodées en une adresse postale en créant une [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instance et en appelant la méthode [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) sur l’instance `Geocoder` :

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

La méthode [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) accepte un argument [`Position`](xref:Xamarin.Forms.Maps.Position) composé de coordonnées de latitude et de longitude, et retourne de manière asynchrone une collection de chaînes qui représentent les adresses à proximité de la position.

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [API géocoder](xref:Xamarin.Forms.Maps.Geocoder)
