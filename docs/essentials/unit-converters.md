---
title: Convertisseurs d’unités Xamarin.Essentials
description: La classe UnitConverters dans Xamarin.Essentials fournit plusieurs convertisseurs d’unités pour aider les développeurs à utiliser Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: be560a156647274932265597ae5b83f22255d061
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120137"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Convertisseurs d’unités

La classe **UnitConverters** fournit plusieurs convertisseurs d’unités pour aider les développeurs à utiliser Xamarin.Essentials.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Utilisation de convertisseurs d’unités

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Tous les convertisseurs d’unités sont disponibles en utilisant la classe `UnitConverters` statique dans Xamarin.Essentials. Par exemple, vous pouvez facilement convertir des Fahrenheit en Celsius.

```csharp
var celcius = UnitConverters.FahrenheitToCelsius(32.0);
```

Voici la liste des conversions disponibles :

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
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

## <a name="api"></a>API

- [Code source des convertisseurs d’unités](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentation sur les API de convertisseurs d’unités](xref:Xamarin.Essentials.UnitConverters)
