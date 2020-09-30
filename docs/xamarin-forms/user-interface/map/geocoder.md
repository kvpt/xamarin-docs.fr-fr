---
title: Xamarin.Forms Géocodage de la carte
description: Cet article explique comment géocoder et inverser les données de la carte géocode à l’aide du Xamarin.Forms . Classe de géocodeur Maps.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6a63e952d7466dc4b7f2d3e8cfc39d6c63e4daf
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559972"
---
# <a name="no-locxamarinforms-map-geocoding"></a>Xamarin.Forms Géocodage de la carte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

L' [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) espace de noms fournit une [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe, qui effectue une conversion entre des adresses de chaîne et des coordonnées de latitude et de longitude stockées dans des [`Position`](xref:Xamarin.Forms.Maps.Position) objets. Pour plus d’informations sur la [`Position`](xref:Xamarin.Forms.Maps.Position) structure, consultez [mapper la position et distance](position-distance.md).

> [!NOTE]
> Une autre API de géocodage est disponibles dans Xamarin.Essentials . L' Xamarin.Essentials `Geocoding` API offre des données d’adresse structurées lors du géocodage des adresses, par opposition aux chaînes retournées par cette API. Pour plus d’informations, consultez [ Xamarin.Essentials géocodage](~/essentials/geocoding.md).

## <a name="geocode-an-address"></a>Géocoder une adresse

Une adresse postale peut être géocodée en coordonnées de latitude et de longitude en créant une [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instance et en appelant la [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) méthode sur l' `Geocoder` instance :

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

La [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) méthode prend un `string` argument qui représente l’adresse et retourne de manière asynchrone une collection d' [`Position`](xref:Xamarin.Forms.Maps.Position) objets qui pourraient représenter l’adresse.

## <a name="reverse-geocode-an-address"></a>Inverser géocoder une adresse

Les coordonnées de latitude et de longitude peuvent être inversées géocodées en une adresse postale en créant une [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) instance et en appelant la [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) méthode sur l' `Geocoder` instance :

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

La [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) méthode prend un [`Position`](xref:Xamarin.Forms.Maps.Position) argument composé de coordonnées de latitude et de longitude, et retourne de manière asynchrone une collection de chaînes qui représentent les adresses à proximité de la position.

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms Position et distance de la carte](position-distance.md)
- [API géocoder](xref:Xamarin.Forms.Maps.Geocoder)