---
title: 'Xamarin.Essentials : magnétomètre'
description: La classe Magnetometer de Xamarin.Essentials vous permet de superviser le capteur magnétométrique de l’appareil, qui indique l’orientation de l’appareil par rapport au champ magnétique terrestre.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 7682afd26bc09e467c5badbea25c9d478c7bb842
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70226798"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials : magnétomètre

La classe **Magnetometer** vous permet de superviser le capteur magnétométrique de l’appareil, qui indique l’orientation de l’appareil par rapport au champ magnétique terrestre.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>Utilisation du magnétomètre

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité de magnétomètre appelle les méthodes `Start` et `Stop` pour écouter les changements relatifs au magnétomètre. Toutes les variations peuvent être écoutées via l’événement `ReadingChanged`. Voici un exemple d’utilisation :

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Toutes les données sont retournées en T (microteslas).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Code source de la fonctionnalité de magnétomètre](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Documentation sur l’API de magnétomètre](xref:Xamarin.Essentials.Magnetometer)
