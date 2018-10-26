---
title: Emplacement de l’utilisateur ne fonctionne ne pas dans iOS 8
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9BE92C99-C9C5-427E-ADE4-789DF258BACE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 8b800d0c5639d4679b5e17c6c6a4689690529b85
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118212"
---
# <a name="user-location-not-working-in-ios-8"></a>Emplacement de l’utilisateur ne fonctionne ne pas dans iOS 8

Dans un éditeur de texte : ouvrez jusqu'à votre fichier Info.plist et ajoutez le code suivant :

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>You are about to use location!</string>

<key>NSLocationAlwaysUsageDescription</key>
<string>This will be called if location is used behind the scenes</string>
```

Et, dans le MainViewController.cs vous devez appeler les éléments suivants :

```csharp
iPhoneLocationManager.RequestWhenInUseAuthorization ();
```

EX :

```cs
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    iPhoneLocationManager.RequestWhenInUseAuthorization ();
    iPhoneLocationManager.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) => {
        UpdateLocation (mainScreen, e.Locations [e.Locations.Length - 1]);
        };
} else {
    // this won't be called on iOS 6 (deprecated)
    iPhoneLocationManager.UpdatedLocation += (object sender, CLLocationUpdatedEventArgs e) => {
        UpdateLocation (mainScreen, e.NewLocation);
        };
}
```
