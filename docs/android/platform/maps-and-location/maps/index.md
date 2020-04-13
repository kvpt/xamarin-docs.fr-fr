---
title: Comment utiliser Google Maps et Location avec Xamarin.Android
description: Cet article traite de la façon d’utiliser les cartes et l’emplacement avec Xamarin.Android. Il couvre tout, de l’exploitation de l’application de cartes intégrées à l’utilisation de google Maps Android API V2 directement.
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: d877f415bb96024bb41edc2be9aec108ae248e88
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020029"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Comment utiliser Google Maps et Location avec Xamarin.Android

_Cet article traite de la façon d’utiliser les cartes et l’emplacement avec Xamarin.Android. Il couvre tout, de l’exploitation de l’application de cartes intégrées à l’utilisation de google Maps Android API V2 directement._

## <a name="maps-overview"></a>Aperçu des cartes

Les technologies de cartographie sont un complément omniprésent aux appareils mobiles. Ordinateurs de bureau et ordinateurs portables n’ont pas tendance à avoir la conscience de localisation intégré. D’autre part, les appareils mobiles utilisent ces applications pour localiser les appareils et afficher des informations de localisation changeantes. Android dispose d’une technologie puissante et intégrée qui affiche les données de localisation sur les cartes à l’aide du matériel de localisation qui peut être disponible sur l’appareil. Cet article couvre un spectre de ce que les applications de cartes sous Xamarin.Android ont à offrir, y compris: 

- Utilisation de l’application de cartes intégrées pour ajouter rapidement des fonctionnalités de cartographie.
- Travailler avec l’API Maps pour contrôler l’affichage d’une carte.
- Utilisation d’une variété de techniques pour ajouter des superpositions graphiques.

Les sujets de cette section couvrent un large éventail de fonctionnalités de cartographie.
Tout d’abord, ils expliquent comment tirer parti de l’application de cartes intégrées d’Android et comment afficher une vue panoramique sur la rue d’un emplacement. Ensuite, ils discutent de la façon d’utiliser l’API Maps pour incorporer des fonctionnalités de cartographie directement dans une application, couvrant à la fois la façon de contrôler la position et l’affichage d’une carte, ainsi que la façon d’ajouter des superpositions graphiques.

## <a name="related-links"></a>Liens connexes

- [MapsAndLocationDemo_v3 (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Liste d’intentions : Invoquer des applications Google sur les appareils Android](https://developer.android.com/guide/appendix/g-app-intents.html)
- [Emplacement et cartes](https://developer.android.com/guide/topics/location/index.html)
