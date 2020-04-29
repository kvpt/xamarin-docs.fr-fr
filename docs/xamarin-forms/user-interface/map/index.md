---
title: Carte Xamarin. Forms
description: Le contrôle de carte affiche un mappage et requiert le package NuGet Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: ffd8f7cc31707d09bb3442c180a867d31afcef0f
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517491"
---
# <a name="xamarinforms-map"></a>Carte Xamarin. Forms

## <a name="initialization-and-configuration"></a>[Initialisation et configuration](setup.md)

Le package NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) est requis pour utiliser la fonctionnalité Maps dans une application. En outre, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour être accordé à l’application.

## <a name="map-control"></a>[Map Control](map.md)

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle est une vue multiplateforme permettant d’afficher et d’annoter des cartes. Il utilise le contrôle de carte natif pour chaque plateforme, en fournissant une expérience de cartes rapide et familière aux utilisateurs.

## <a name="position-and-distance"></a>[Position et distance](position-distance.md)

Le [`Position`](xref:Xamarin.Forms.Maps.Position) struct est généralement utilisé lors du positionnement d’un mappage et de ses codes [`Distance`](xref:Xamarin.Forms.Maps.Distance) confidentiels, et du struct qui peut éventuellement être utilisé lors du positionnement d’un mappage.

## <a name="pins"></a>[Broches](pins.md)

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle permet aux emplacements d’être marqués [`Pin`](xref:Xamarin.Forms.Maps.Pin) avec des objets. Un `Pin` est un marqueur de carte qui ouvre une fenêtre d’informations lorsqu’il est appuyé.

## <a name="polygons-polylines-and-circles"></a>[Polygones, polylignes et cercles](polygons.md)

`Polygon`les `Polyline`éléments, `Circle` et vous permettent de mettre en surbrillance des zones spécifiques sur une carte. Un `Polygon` est une forme entièrement incluse qui peut avoir un trait et une couleur de remplissage. Un `Polyline` est une ligne qui ne délimite pas complètement une zone. `Circle` Met en surbrillance une zone circulaire de la carte.

## <a name="geocoding"></a>[Géocodage](geocoder.md)

La [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe effectue une conversion entre des adresses de chaîne et des coordonnées de latitude et [`Position`](xref:Xamarin.Forms.Maps.Position) de longitude stockées dans des objets.

## <a name="launch-the-native-map-app"></a>[Lancer l’application Map Native](native-map-app.md)

L’application Map native sur chaque plateforme peut être lancée à partir d’une application Xamarin. Forms par la `Launcher` classe Xamarin. Essentials.
