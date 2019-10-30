---
title: Lancement de l’application Maps
description: Comment lancer l’application Maps intégrée à partir de votre application Xamarin. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027080"
---
# <a name="launching-the-maps-application"></a>Lancement de l’application Maps

La façon la plus simple de travailler avec des mappages dans Xamarin. Android est de tirer parti de l’application Maps intégrée illustrée ci-dessous :

[![exemple de capture d’écran de l’application Google Maps intégrée](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Lorsque vous utilisez l’application Maps, la carte ne fait pas partie de votre application. Au lieu de cela, votre application lance l’application Maps et charge la carte en externe. La section suivante explique comment utiliser Xamarin. Android pour lancer des cartes comme celle ci-dessus.

## <a name="creating-the-intent"></a>Création de l’intention

L’utilisation de l’application Maps est aussi simple que la création d’une intention avec un URI approprié, la définition de l’action sur ActionView et l’appel de la méthode StartActivity. Par exemple, le code suivant lance l’application Maps centrée sur une latitude et une longitude données :

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Ce code est tout ce qui est nécessaire pour lancer la carte illustrée dans la capture d’écran précédente. En plus de spécifier la latitude et la longitude, le schéma d’URI pour les mappages prend en charge plusieurs autres options.

## <a name="geo-uri-scheme"></a>Schéma géo-URI

Le code ci-dessus utilisait le schéma géo pour créer un URI. Ce modèle d’URI prend en charge plusieurs formats, comme indiqué ci-dessous :

- `geo:latitude,longitude` &ndash; ouvre l’application Maps centrée sur un LAT/LON. 

- `geo:latitude,longitude?z=zoom` &ndash; ouvre l’application Maps centrée sur une table lat/lon et effectue un zoom sur le niveau spécifié. Le niveau de zoom peut être compris entre 1 et 23:1. affiche la totalité de la terre et 23 le niveau de zoom le plus proche.

- `geo:0,0?q=my+street+address` &ndash; ouvre l’application Maps à l’emplacement d’une adresse postale. 

- `geo:0,0?q=business+near+city` &ndash; ouvre l’application Maps et affiche les résultats de la recherche annotés. 

Les versions de l’URI qui acceptent une requête (à savoir l’adresse postale ou les termes de recherche) utilisent le service de géocodeur de Google pour récupérer l’emplacement qui est ensuite affiché sur la carte. Par exemple, l’URI `geo:0,0?q=coop+Cambridge` résultat dans le mappage indiqué ci-dessous :

[![exemple de capture d’écran montrant Google Maps avec un terme de recherche](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Pour plus d’informations sur les schémas d’URI géo, consultez [afficher un emplacement sur une carte](https://developer.android.com/guide/components/intents-common.html#Maps).

## <a name="street-view"></a>Affichage de la rue

En plus de la géo-schéma, Android prend également en charge le chargement de vues de rue à partir d’une intention. Vous trouverez ci-dessous un exemple de l’application de la vue Street lancée à partir de Xamarin. Android :

[![exemple de capture d’écran d’une vue de la rue](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Pour lancer un affichage de la rue, utilisez simplement le schéma d’URI `google.streetview`, comme illustré dans le code suivant :

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Le schéma d’URI Google. Streetview utilisé ci-dessus prend la forme suivante :

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Comme vous pouvez le voir, plusieurs paramètres sont pris en charge, comme indiqué ci-dessous :

- `lat` &ndash; la latitude de l’emplacement à afficher dans la vue Street.

- `lng` &ndash; la longitude de l’emplacement à afficher dans la vue Street.

- `pitch` &ndash; angle de la vue de la rue, mesuré à partir du centre en degrés, où 90 degrés est vers le haut et-90 degrés vers le haut.

- `yaw` &ndash; Centre d’affichage du panorama de la rue, mesuré dans le sens des aiguilles d’une montre, en degrés, du Nord.

- `zoom` &ndash; multiplicateur de zoom pour la vue de la rue Panorama, où 1,0 = zoom normal, 2,0 = facteur de zoom 2x, 3,0 = zoom 4x, etc.

- `mz` &ndash; le niveau de zoom de la carte qui sera utilisé pour accéder à l’application Maps à partir de la vue Street.

L’utilisation de l’application Maps intégrée ou de la vue Street est un moyen simple d’ajouter rapidement la prise en charge du mappage. Toutefois, l’API Maps d’Android offre un contrôle plus précis de l’expérience de mappage.
