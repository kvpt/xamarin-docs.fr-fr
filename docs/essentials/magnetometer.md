---
title: 'Xamarin.Essentials: Magnétomètre'
description: La classe magnétomètre dans vous Xamarin.Essentials permet de surveiller le capteur magnétomètre de l’appareil, qui indique l’orientation du périphérique par rapport au champ magnétique de la terre.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b01bc1fd9c65186952635c5f472b1ac6beb0c9bd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802287"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnétomètre

La classe **Magnetometer** vous permet de superviser le capteur magnétométrique de l’appareil, qui indique l’orientation de l’appareil par rapport au champ magnétique terrestre.

## <a name="get-started"></a>Bien démarrer

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

Toutes les données sont retournées dans μT (microteslas).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Code source de la fonctionnalité de magnétomètre](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Magnetometer)
- [Documentation sur l’API de magnétomètre](xref:Xamarin.Essentials.Magnetometer)
