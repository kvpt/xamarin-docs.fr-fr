---
title: Xamarin.EssentialsConvertisseurs d’unités
description: La classe UnitConverters dans Xamarin.Essentials fournit plusieurs convertisseurs d’unités pour aider les développeurs à utiliser Xamarin.Essentials .
ms.assetid: ''
author: ''
ms.custom: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: deff6997ff921e6048ccb2497a0747c770501a04
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137135"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Convertisseurs d’unités

La classe **UnitConverters** fournit plusieurs convertisseurs d’unités pour aider les développeurs à utiliser Xamarin.Essentials .

## <a name="get-started"></a>Prise en main

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
