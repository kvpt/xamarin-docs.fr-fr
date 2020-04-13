---
title: Lancement de l’application Maps
description: Comment lancer l’application Maps intégrée à partir de votre application Xamarin.Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027080"
---
# <a name="launching-the-maps-application"></a>Lancement de l’application Maps

La façon la plus simple de travailler avec des cartes dans Xamarin.Android est de tirer parti de l’application de cartes intégrées ci-dessous:

[![Exemple de capture d’écran de l’application Google Maps intégrée](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Lorsque vous utilisez l’application de cartes, la carte ne fera pas partie de votre application. Au lieu de cela, votre application lancera l’application de cartes et chargera la carte à l’extérieur. La section suivante examine comment utiliser Xamarin.Android pour lancer des cartes comme celle ci-dessus.

## <a name="creating-the-intent"></a>Création de l’intention

Travailler avec l’application de cartes est aussi facile que la création d’une intention avec une URI appropriée, la mise en place de l’action à ActionView, et d’appeler la méthode StartActivity. Par exemple, le code suivant lance l’application de cartes centrée à une latitude et une longitude données :

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Ce code est tout ce qui est nécessaire pour lancer la carte montrée dans la capture d’écran précédente. En plus de spécifier la latitude et la longitude, le système URI pour les cartes prend en charge plusieurs autres options.

## <a name="geo-uri-scheme"></a>Système Geo URI

Le code ci-dessus a utilisé le schéma géo pour créer un URI. Ce système URI prend en charge plusieurs formats, comme indiqué ci-dessous :

- `geo:latitude,longitude`&ndash; Ouvre l’application de cartes centrée sur un lat/lon. 

- `geo:latitude,longitude?z=zoom`&ndash; Ouvre l’application de cartes centrée sur un lat/lon et zoomée au niveau spécifié. Le niveau de zoom peut varier de 1 à 23: 1 affiche toute la Terre et 23 est le niveau de zoom le plus proche.

- `geo:0,0?q=my+street+address`&ndash; Ouvre l’application de cartes à l’emplacement d’une adresse de rue. 

- `geo:0,0?q=business+near+city`&ndash; Ouvre l’application de cartes et affiche les résultats de recherche annotés. 

Les versions de l’URI qui prennent une requête (à savoir l’adresse de rue ou les termes de recherche) utilisent le service de géocoder de Google pour récupérer l’emplacement qui est ensuite affiché sur la carte. Par exemple, l’URI `geo:0,0?q=coop+Cambridge` donne lieu à la carte indiquée ci-dessous :

[![Exemple de capture d’écran montrant Google Maps avec un terme de recherche](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Pour plus d’informations sur les systèmes géo-URI, voir [Afficher un emplacement sur une carte](https://developer.android.com/guide/components/intents-common.html#Maps).

## <a name="street-view"></a>Vue de rue

En plus du système géo, Android prend également en charge le chargement des vues de la rue à partir d’une intention. Un exemple de l’application street view lancée à partir de Xamarin.Android est montré ci-dessous:

[![Exemple de capture d’écran d’une vue de rue](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Pour lancer une vue de `google.streetview` rue, il suffit d’utiliser le schéma URI, comme le démontre le code suivant :

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

Le système google.streetview URI utilisé ci-dessus prend la forme suivante:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Comme vous pouvez le voir, il existe plusieurs paramètres pris en charge, comme indiqué ci-dessous:

- `lat`&ndash; La latitude de l’emplacement à afficher dans la vue de la rue.

- `lng`&ndash; La longitude de l’emplacement à afficher dans la vue de la rue.

- `pitch`&ndash; Angle de vue de la rue panorama, mesuré à partir du centre dans les degrés où 90 degrés est tout droit vers le bas et -90 degrés est tout droit.

- `yaw`&ndash; Centre de vue du panorama de vue de rue, mesuré dans le sens des aiguilles d’une montre en degrés du nord.

- `zoom`&ndash; Multiplicateur de zoom pour le panorama de vue de rue, où 1.0 zoom normal, 2.0 ' zoomé 2x, 3.0 ' zoomé 4x, etc.

- `mz`&ndash; Le niveau de zoom de carte qui sera utilisé lors de l’utilisation des cartes de la vue de la rue.

Travailler avec l’application de cartes intégrée ou la vue de la rue est un moyen facile d’ajouter rapidement un support de cartographie. Cependant, l’API Maps d’Android offre un contrôle plus fin sur l’expérience de cartographie.
