---
titre : « Xamarin.Essentials convertisseurs d’unités » Description : « la classe UnitConverters dans Xamarin.Essentials fournit plusieurs convertisseurs d’unités pour aider les développeurs lors de l’utilisation de Xamarin.Essentials . »
ms. AssetID : 35DE2704-E730-4337-9476-66CD53376943 Author : jamesmontemagno ms. Custom : vidéo ms. Author : Jamont ms. Date : 01/06/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Convertisseurs d’unités

La classe **UnitConverters** fournit plusieurs convertisseurs d’unités pour aider les développeurs à utiliser Xamarin.Essentials .

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Utilisation de convertisseurs d’unités

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Tous les convertisseurs d’unités sont disponibles à l’aide de la `UnitConverters` classe statique dans Xamarin.Essentials . Par exemple, vous pouvez facilement convertir des Fahrenheit en Celsius.

```csharp
var celsius = UnitConverters.FahrenheitToCelsius(32.0);
```

Voici la liste des conversions disponibles :

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
- MetersToInternationalFeet
- InternationalFeetToMeters
- DegreesToRadians
- RadiansToDegrees
- DegreesPerSecondToRadiansPerSecond
- RadiansPerSecondToDegreesPerSecond
- DegreesPerSecondToHertz
- RadiansPerSecondToHertz
- HertzToDegreesPerSecond
- HertzToRadiansPerSecond
- KilopascalsToHectopascals
- HectopascalsToKilopascals
- KilopascalsToPascals
- HectopascalsToPascals
- AtmospheresToPascals
- PascalsToAtmospheres
- CoordinatesToMiles
- CoordinatesToKilometers
- KilogramsToPounds
- PoundsToKilograms
- StonesToPounds
- PoundsToStones

## <a name="api"></a>API

- [Code source des convertisseurs d’unités](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentation sur les API de convertisseurs d’unités](xref:Xamarin.Essentials.UnitConverters)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Unit-Conversion-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
