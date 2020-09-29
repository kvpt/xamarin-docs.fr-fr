---
title: 'Xamarin.Essentials: Géolocalisation'
description: Ce document décrit la classe de géolocalisation dans Xamarin.Essentials , qui fournit des API permettant de récupérer les coordonnées de géolocalisation actuelles de l’appareil.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 03/13/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4a671be5f65e0e35c89f4acec17f406a214b9fa9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434620"
---
# <a name="no-locxamarinessentials-geolocation"></a>Xamarin.Essentials: Géolocalisation

La classe **Geolocation** fournit des API permettant de récupérer les coordonnées de géolocalisation de l’appareil.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **géolocalisation**, la configuration suivante spécifique à la plateforme est obligatoire :

# <a name="android"></a>[Android](#tab/android)

Les autorisations de localisation approximative et de localisation précise sont obligatoires, et doivent être configurées dans le projet Android. De plus, si votre application cible Android 5.0 (niveau d’API 21) ou une version ultérieure, vous devez déclarer que votre application utilise les fonctionnalités matérielles dans le fichier manifeste. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

Ou mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés**, puis ajoutez ce qui suit dans le nœud **manifeste** :

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous **Manifeste Android**, recherchez la zone **Autorisations nécessaires**, puis cochez les autorisations **ACCESS_COARSE_LOCATION** et **ACCESS_FINE_LOCATION**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

Le fichier **Info.plist** de votre application doit contenir la clé `NSLocationWhenInUseUsageDescription` pour pouvoir accéder à la localisation de l’appareil.

Ouvrez l’éditeur plist, ajoutez la propriété **Confidentialité - Description de l’utilisation de la localisation en cas d’activité**, puis indiquez une valeur à afficher pour l’utilisateur.

Ou modifiez le fichier et ajoutez manuellement les éléments suivants et mettez à jour la logique :

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Fill in a reason why your app needs access to location.</string>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Vous devez définir l’autorisation `Location` pour l’application. Pour ce faire, ouvrez **Package.appxmanifest**, sélectionnez l’onglet **Fonctionnalités** et cochez **Localisation**.

-----

## <a name="using-geolocation"></a>Utilisation de la géolocalisation

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Si nécessaire, l’API de géolocalisation demande également des autorisations à l’utilisateur.

Vous pouvez obtenir la dernière [localisation](xref:Xamarin.Essentials.Location) connue de l’appareil en appelant la méthode `GetLastKnownLocationAsync`. Bien que cela soit souvent plus rapide que d’effectuer une requête complète, cela peut aussi être moins précis et retourner `null` si aucun emplacement mis en cache n’existe.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

L’altitude n’est pas toujours disponible. Dans ce cas, la propriété `Altitude` peut avoir une valeur `null` ou égale à zéro. Si l’altitude est disponible, la valeur est exprimée en mètres au-dessus du niveau de la mer.

Pour interroger les coordonnées relatives à la [localisation](xref:Xamarin.Essentials.Location) de l’appareil actuel, vous pouvez utiliser `GetLocationAsync`. Il est préférable de passer un `GeolocationRequest` et un `CancellationToken` complets, car il peut s’écouler un certain temps avant d’obtenir la localisation de l’appareil.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Précision de la géolocalisation

Le tableau suivant indique la précision en fonction de la plateforme :

### <a name="lowest"></a>Minimale

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1 000 - 5 000 |

### <a name="low"></a>Faible

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 500 |
| iOS | 1 000 |
| UWP | 300 - 3 000 |

### <a name="medium-default"></a>Moyenne (par défaut)

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30 - 500 |

### <a name="high"></a>Importante

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | <= 10 |

### <a name="best"></a>La meilleure

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | <= 10 |

<a name="calculate-distance"></a>

## <a name="detecting-mock-locations"></a>Détection des emplacements fictifs
Certains appareils peuvent retourner un emplacement fictif du fournisseur ou par une application qui fournit des emplacements fictifs. Vous pouvez détecter ce cas à l’aide `IsFromMockProvider` de l’un des [`Location`](xref:Xamarin.Essentials.Location) .

```csharp
var request = new GeolocationRequest(GeolocationAccuracy.Medium);
var location = await Geolocation.GetLocationAsync(request);

if (location != null)
{
    if(location.IsFromMockProvider)
    {
        // location is from a mock provider
    }
}
```

## <a name="distance-between-two-locations"></a>Distance entre deux emplacements

Les [`Location`](xref:Xamarin.Essentials.Location) [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) classes et définissent des `CalculateDistance` méthodes qui vous permettent de calculer la distance entre deux emplacements géographiques. Cette distance calculée ne prend pas en compte les routes et autres chemins. Elle constitue simplement la distance la plus courte entre deux points à la surface de la Terre, également appelée _distance orthodromique_ ou, familièrement, distance « à vol d’oiseau ».

Voici un exemple :

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

Le constructeur `Location` a des arguments de latitude et de longitude dans cet ordre. Les valeurs de latitude positives sont au nord de l’équateur, et les valeurs de longitude positives sont à l’est du premier méridien. Utilisez le dernier argument pour `CalculateDistance` afin de spécifier des miles ou des kilomètres. La classe `UnitConverters` définit également les méthodes `KilometersToMiles` et `MilesToKilometers` pour effectuer la conversion entre les deux unités.

## <a name="platform-differences"></a>Différences entre les plateformes

L’altitude est calculée différemment sur chaque plateforme.

# <a name="android"></a>[Android](#tab/android)

Sur Android, l' [altitude](https://developer.android.com/reference/android/location/Location#getAltitude()), si elle est disponible, est retournée en mètres au-dessus de la référence WGS 84 ellipsoïde. Si cet emplacement n’a pas d’altitude, 0,0 est retourné.

# <a name="ios"></a>[iOS](#tab/ios)

Sur iOS, l' [altitude](https://developer.apple.com/documentation/corelocation/cllocation/1423820-altitude) est mesurée en mètres. Les valeurs positives indiquent des altitudes au-dessus du niveau de la mer, tandis que les valeurs négatives indiquent des altitudes inférieures au niveau de la mer.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sur UWP, l’altitude est retournée en mètres. Pour plus d’informations, consultez la documentation de [AltitudeReferenceSystem](/uwp/api/windows.devices.geolocation.geopoint.altitudereferencesystem#Windows_Devices_Geolocation_Geopoint_AltitudeReferenceSystem) .

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité de géolocalisation](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Geolocation)
- [Documentation sur l’API de géolocalisation](xref:Xamarin.Essentials.Geolocation)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geolocation-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]