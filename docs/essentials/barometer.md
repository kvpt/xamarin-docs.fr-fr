---
title: 'Xamarin.Essentials: Baromètre'
description: La classe Baromètre dans Xamarin.Essentials vous permet d’analyser le capteur du baromètre de l’appareil, qui mesure la pression.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5a496fc06732be1cf104cfbaffa8ff4b68c8f564
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976435"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: Baromètre

La classe **Baromètre** vous permet d’analyser le capteur du baromètre de l’appareil, qui mesure la pression.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-barometer"></a>Utilisation de Baromètre

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité Baromètre appelle les méthodes `Start` et `Stop` qui permettent d’écouter les changements de lecture de la pression du baromètre en hectopascals. Toutes les variations peuvent être écoutées via l’événement `ReadingChanged`. Voici un exemple d’utilisation :

```csharp

public class BarometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public BarometerTest()
    {
        // Register for reading changes.
        Barometer.ReadingChanged += Barometer_ReadingChanged;
    }

    void Barometer_ReadingChanged(object sender, BarometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Pressure
        Console.WriteLine($"Reading: Pressure: {data.PressureInHectopascals} hectopascals");
    }

    public void ToggleBarometer()
    {
        try
        {
            if (Barometer.IsMonitoring)
              Barometer.Stop();
            else
              Barometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Aucun détail d’implémentation spécifique à la plateforme.

# <a name="iostabios"></a>[iOS](#tab/ios)

Cette API utilise [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) pour surveiller les changements de pression, qui est une fonctionnalité matérielle ajoutée à l’iPhone 6 et aux appareils les plus récents. Un `FeatureNotSupportedException` sera levé sur les appareils qui ne prennent pas en charge l’altimètre.

`SensorSpeed` n’est pas utilisé, car il n’est pas pris en charge sur iOS.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Aucun détail d’implémentation spécifique à la plateforme.

-----

## <a name="api"></a>API

- [Code source de Baromètre](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Documentation de l’API Baromètre](xref:Xamarin.Essentials.Barometer)
