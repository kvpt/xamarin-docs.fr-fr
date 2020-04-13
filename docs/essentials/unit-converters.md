---
title: Convertisseurs d’unités Xamarin.Essentials
description: La classe UnitConverters dans Xamarin.Essentials fournit plusieurs convertisseurs d’unités pour aider les développeurs à utiliser Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: c07e0c7d9645c22f0d70c75fd7d8dffdec8cde04
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545030"
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
- KilogrammesToPounds
- LivresToKilograms
- StonesToPounds
- PoundsToStones (en)

## <a name="api"></a>API

- [Code source des convertisseurs d’unités](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentation sur les API de convertisseurs d’unités](xref:Xamarin.Essentials.UnitConverters)
