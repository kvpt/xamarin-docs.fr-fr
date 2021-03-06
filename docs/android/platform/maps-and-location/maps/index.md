---
title: Utilisation de Google Maps et emplacement avec Xamarin. Android
description: Cet article explique comment utiliser Maps et location avec Xamarin. Android. Il aborde tout ce qui est de tirer parti de l’application Maps intégrée à l’utilisation de l’API Android de Google Maps v2 directement.
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 11feb8e3e10e12bfb2c647182ec4ba953ab34e24
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456897"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Utilisation de Google Maps et emplacement avec Xamarin. Android

_Cet article explique comment utiliser Maps et location avec Xamarin. Android. Il aborde tout ce qui est de tirer parti de l’application Maps intégrée à l’utilisation de l’API Android de Google Maps v2 directement._

## <a name="maps-overview"></a>Vue d’ensemble des mappages

Les technologies de mappage sont un complément omniprésent des appareils mobiles. Les ordinateurs de bureau et les ordinateurs portables n’ont pas tendance à avoir une reconnaissance d’emplacement intégrée. En revanche, les appareils mobiles utilisent ces applications pour localiser les appareils et pour afficher les informations relatives à la modification de l’emplacement. Android offre une technologie puissante et intégrée qui affiche les données d’emplacement sur les cartes à l’aide de périphériques de localisation qui peuvent être disponibles sur l’appareil. Cet article couvre un large éventail d’offres des applications Maps sous Xamarin. Android, notamment : 

- Utilisation de l’application Maps intégrée pour ajouter rapidement des fonctionnalités de mappage.
- Utilisation de l’API Maps pour contrôler l’affichage d’une carte.
- À l’aide de diverses techniques pour ajouter des superpositions graphiques.

Les rubriques de cette section couvrent un large éventail de fonctionnalités de mappage.
Tout d’abord, ils expliquent comment tirer parti de l’application Maps intégrée d’Android et comment afficher une vue panoramique de la rue d’un emplacement. Ils décrivent ensuite comment utiliser l’API Maps pour incorporer les fonctionnalités de mappage directement dans une application, en couvrant à la fois la façon de contrôler la position et l’affichage d’une carte, ainsi que l’ajout de superpositions graphiques.

## <a name="related-links"></a>Liens associés

- [MapsAndLocationDemo_v3 (exemple)](/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Liste des intentions : appel des applications Google sur des appareils Android](https://developer.android.com/guide/appendix/g-app-intents.html)
- [Emplacement et mappages](https://developer.android.com/guide/topics/location/index.html)