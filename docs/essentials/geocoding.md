---
titre : « Xamarin.Essentials : géocodage » Description : « la classe de géocodage dans Xamarin.Essentials fournit des API pour géocoder un placemark en coordonnées de position et des coordonnées géocode inversées en placemark ».
ms. AssetID : 3ADC440C-B000-4708-A2CC-296F5160AF90 auteur : jamesmontemagno ms. Author : Jamont ms. Date : 05/28/2019 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Géocodage

La classe **Geocoding** fournit des API qui permettent de géocoder un repère en coordonnées de position, et d’inverser les coordonnées de géocodage en un repère.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **géocodage**, la configuration suivante spécifique à la plateforme est obligatoire.

# <a name="android"></a>[Android](#tab/android)

Aucune configuration supplémentaire n’est requise.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Une clé API Bing Maps est nécessaire pour utiliser la fonctionnalité de géocodage. Inscrivez-vous pour obtenir un compte [Bing Maps](https://www.bingmapsportal.com/) gratuit. Sous **Mon compte > Mes clés**, créez une clé, puis indiquez les informations nécessaires en fonction de votre type d’application (qui doit être **Application Windows publique (UWP, 8.x et versions antérieures)** pour les applications UWP).

Très tôt dans la vie de votre application, avant d’appeler une méthode de **géocodage**, définissez la clé API (uniquement disponible sur UWP) :

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Utilisation du géocodage

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Obtention des coordonnées de [localisation](xref:Xamarin.Essentials.Location) d’une adresse :

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

L’altitude n’est pas toujours disponible. Dans ce cas, la propriété `Altitude` peut avoir une valeur `null` ou égale à zéro. Si l’altitude est disponible, la valeur est exprimée en mètres au-dessus du niveau de la mer.

## <a name="using-reverse-geocoding"></a>Utilisation du géocodage inversé

Le géocodage inversé est le processus d’obtention de [repères](xref:Xamarin.Essentials.Placemark) pour un ensemble existant de coordonnées :

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>Distance entre deux emplacements

Les [`Location`](xref:Xamarin.Essentials.Location) [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) classes et définissent des méthodes pour calculer la distance entre deux emplacements. Pour obtenir un exemple, consultez l’article [** Xamarin.Essentials : géolocalisation**](geolocation.md#calculate-distance) .

## <a name="api"></a>API

- [Code source de la fonctionnalité de géocodage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentation sur l’API de géocodage](xref:Xamarin.Essentials.Geocoding)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geocoding-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
