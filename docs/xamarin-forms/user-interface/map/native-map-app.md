---
title: Lancer l’application Map native à partir deXamarin.Forms
description: L’application Maps natives sur chaque plateforme peut être lancée à partir d’une Xamarin.Forms application par la Xamarin.Essentials classe Launcher.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c135d5dd02bba5102f5a93132f079526c84865d5
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129335"
---
# <a name="launch-the-native-map-app-from-xamarinforms"></a>Lancer l’application Map native à partir deXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

L’application Map native sur chaque plateforme peut être lancée à partir d’une Xamarin.Forms application par la Xamarin.Essentials `Launcher` classe. Cette classe permet à une application d’ouvrir une autre application par le biais de son modèle d’URI personnalisé. La fonctionnalité de lancement peut être appelée à l’aide de la `OpenAsync` méthode, en passant un `string` `Uri` argument ou qui représente le schéma d’URL personnalisé à ouvrir. Pour plus d’informations sur Xamarin.Essentials , consultez [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms) .

> [!NOTE]
> Une alternative à l’utilisation de la Xamarin.Essentials `Launcher` classe consiste à utiliser sa `Map` classe. Pour plus d’informations, consultez [ Xamarin.Essentials : Map](~/essentials/maps.md?context=xamarin/xamarin-forms).

L’application Maps sur chaque plateforme utilise un schéma d’URI personnalisé unique. Pour plus d’informations sur le modèle d’URI Maps sur iOS, consultez [mappage des liens](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html) sur Developer.Apple.com. Pour plus d’informations sur le schéma d’URI Maps sur Android, consultez [Maps Guide du développeur](https://developer.android.com/guide/components/intents-common.html#Maps) et [Google Maps intentions pour Android](https://developers.google.com/maps/documentation/urls/android-intents) sur Developers.Android.com. Pour plus d’informations sur le schéma d’URI Maps sur la plateforme Windows universelle (UWP), consultez [lancer l’application Windows Maps](/windows/uwp/launch-resume/launch-maps-app).

## <a name="launch-the-map-app-at-a-specific-location"></a>Lancer l’application de mappage à un emplacement spécifique

Vous pouvez ouvrir un emplacement dans l’application cartes natives en ajoutant des paramètres de requête appropriés au schéma d’URI personnalisé pour chaque application de mappage :

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // open the maps app directly
    await Launcher.OpenAsync("geo:0,0?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?where=394 Pacific Ave San Francisco CA");
}
```

Cet exemple de code entraîne le lancement de l’application de mappage native sur chaque plateforme, avec la carte centrée sur un code confidentiel représentant l’emplacement spécifié :

[![Capture d’écran de l’application Map native, sur iOS et Android](native-map-app-images/location.png "Application Map Native")](native-map-app-images/location-large.png#lightbox "Application Map Native")

## <a name="launch-the-map-app-with-directions"></a>Lancer l’application Map avec des instructions

L’application Maps natives peut être lancée en affichant des instructions, en ajoutant des paramètres de requête appropriés au schéma d’URI personnalisé pour chaque application de mappage :

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?daddr=San+Francisco,+CA&saddr=cupertino");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // opens the 'task chooser' so the user can pick Maps, Chrome or other mapping app
    await Launcher.OpenAsync("http://maps.google.com/?daddr=San+Francisco,+CA&saddr=Mountain+View");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?rtp=adr.394 Pacific Ave San Francisco CA~adr.One Microsoft Way Redmond WA 98052");
}
```

Cet exemple de code entraîne le lancement de l’application de mappage native sur chaque plateforme, avec la carte centrée sur un itinéraire entre les emplacements spécifiés :

[![Capture d’écran de l’itinéraire de l’application Map native sur iOS et Android](native-map-app-images/directions.png "Directions de l’application de mappage Native")](native-map-app-images/directions-large.png#lightbox "Directions de l’application de mappage Native")

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [Liens de carte](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Guide du développeur Maps](https://developer.android.com/guide/components/intents-common.html#Maps)
- [Intentions Google Maps pour Android](https://developers.google.com/maps/documentation/)
- [Lancer l’application Cartes Windows](/windows/uwp/launch-resume/launch-maps-app)
