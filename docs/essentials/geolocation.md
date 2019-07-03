---
title: 'Xamarin.Essentials: Géolocalisation'
description: Ce document décrit la classe Geolocation de Xamarin.Essentials, qui fournit des API permettant de récupérer les coordonnées de géolocalisation de l’appareil.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4ac6344165730bc8c348c16fe8f3a932d4ac3548
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268617"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Géolocalisation

La classe **Geolocation** fournit des API permettant de récupérer les coordonnées de géolocalisation de l’appareil.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **géolocalisation**, la configuration suivante spécifique à la plateforme est obligatoire :

# <a name="androidtabandroid"></a>[Android](#tab/android)

Les autorisations de localisation approximative et de localisation précise sont obligatoires, et doivent être configurées dans le projet Android. De plus, si votre application cible Android 5.0 (niveau d’API 21) ou une version ultérieure, vous devez déclarer que votre application utilise les fonctionnalités matérielles dans le fichier manifeste. Vous pouvez le faire de plusieurs façons, comme indiqué ci-dessous :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés**, puis ajoutez :

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

# <a name="iostabios"></a>[iOS](#tab/ios)

Le fichier **Info.plist** de votre application doit contenir la clé `NSLocationWhenInUseUsageDescription` pour pouvoir accéder à la localisation de l’appareil.

Ouvrez l’éditeur plist, ajoutez la propriété **Confidentialité - Description de l’utilisation de la localisation en cas d’activité**, puis indiquez une valeur à afficher pour l’utilisateur.

Ou modifiez le fichier et ajoutez manuellement les éléments suivants et mettez à jour la logique :

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Fill in a reason why your app needs access to location.</string>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Vous devez définir l’autorisation `Location` pour l’application. Pour ce faire, ouvrez **Package.appxmanifest**, sélectionnez l’onglet **Fonctionnalités** et cochez **Localisation**.

-----

## <a name="using-geolocation"></a>Utilisation de la géolocalisation

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Si nécessaire, l’API de géolocalisation demande également des autorisations à l’utilisateur.

Vous pouvez obtenir la dernière [localisation](xref:Xamarin.Essentials.Location) connue de l’appareil en appelant la méthode `GetLastKnownLocationAsync`. Bien que cela soit souvent plus rapide que d’effectuer une requête complète, cela peut aussi être moins précis.

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
| iOS | 3 000 |
| UWP | 1 000 - 5 000 |

### <a name="low"></a>Faible

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3 000 |

### <a name="medium-default"></a>Moyenne (par défaut)

| Plateforme | Distance (en mètres) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30 - 500 |

### <a name="high"></a>Haute

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

<a name="calculate-distance" />

## <a name="detecting-mock-locations"></a>Détection des emplacements fictifs
Certains appareils peuvent retourner un emplacement fictif du fournisseur ou par une application qui fournit des emplacements fictifs. Vous pouvez le détecter à l’aide de `IsFromMockProvider` sur tout [`Location`](xref:Xamarin.Essentials.Location).

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

Les classes [`Location`](xref:Xamarin.Essentials.Location) et [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) définissent les méthodes `CalculateDistance` qui vous permettent de calculer la distance entre deux emplacements géographiques. Cette distance calculée ne prend pas en compte les routes et autres chemins. Elle constitue simplement la distance la plus courte entre deux points à la surface de la Terre, également appelée _distance orthodromique_ ou, familièrement, distance « à vol d’oiseau ».

Voici un exemple :

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

Le constructeur `Location` a des arguments de latitude et de longitude dans cet ordre. Les valeurs de latitude positives sont au nord de l’équateur, et les valeurs de longitude positives sont à l’est du premier méridien. Utilisez le dernier argument pour `CalculateDistance` afin de spécifier des miles ou des kilomètres. La classe `UnitConverters` définit également les méthodes `KilometersToMiles` et `MilesToKilometers` pour effectuer la conversion entre les deux unités.

## <a name="api"></a>API

- [Code source de la fonctionnalité de géolocalisation](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentation sur l’API de géolocalisation](xref:Xamarin.Essentials.Geolocation)
