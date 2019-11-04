---
title: Carte Xamarin. Forms
description: Le contrôle de carte affiche un mappage et requiert le package NuGet Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 013e126b76de08442327707cd0502f207826dad8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425600"
---
# <a name="xamarinforms-map"></a>Carte Xamarin. Forms

## <a name="initialization-and-configurationsetupmd"></a>[Initialisation et configuration](setup.md)

Le package NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) est requis pour utiliser la fonctionnalité Maps dans une application. En outre, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour être accordé à l’application.

## <a name="map-controlmapmd"></a>[Map Control](map.md)

Le contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) est une vue multiplateforme permettant d’afficher et d’annoter des cartes. Il utilise le contrôle de carte natif pour chaque plateforme, en fournissant une expérience de cartes rapide et familière aux utilisateurs.

## <a name="position-and-distanceposition-distancemd"></a>[Position et distance](position-distance.md)

Le struct [`Position`](xref:Xamarin.Forms.Maps.Position) est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, et du struct [`Distance`](xref:Xamarin.Forms.Maps.Distance) qui peut éventuellement être utilisé lors du positionnement d’un mappage.

## <a name="pinspinsmd"></a>[Épingles](pins.md)

Le contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) permet aux emplacements d’être marqués avec des objets [`Pin`](xref:Xamarin.Forms.Maps.Pin) . Une `Pin` est un marqueur de carte qui ouvre une fenêtre d’informations lorsqu’elle est exploitée.

## <a name="polygons-and-polylinespolygonsmd"></a>[Polygones et polylignes](polygons.md)

les éléments `Polygon` et `Polyline` vous permettent de mettre en surbrillance des zones spécifiques sur une carte. Une `Polygon` est une forme entièrement incluse qui peut avoir un trait et une couleur de remplissage. Une `Polyline` est une ligne qui ne délimite pas complètement une zone.

## <a name="geocodinggeocodermd"></a>[Géocodage](geocoder.md)

La classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) convertit entre des adresses de chaîne et des coordonnées de latitude et de longitude stockées dans des objets [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="launch-the-native-map-appnative-map-appmd"></a>[Lancer l’application Map Native](native-map-app.md)

L’application Map native sur chaque plateforme peut être lancée à partir d’une application Xamarin. Forms par la classe Xamarin. Essentials `Launcher`.
