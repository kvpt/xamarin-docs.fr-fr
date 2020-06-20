---
title: 'Xamarin.Essentials: Gyroscope'
description: La classe gyroscope dans vous Xamarin.Essentials permet de surveiller le capteur de gyroscope de l’appareil, qui mesure la rotation autour des trois axes principaux de l’appareil.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a085ac5bd18e660339443ea33fe552dd86259cb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802294"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Gyroscope

La classe **Gyroscope** vous permet de superviser le capteur gyroscopique de l’appareil, qui mesure la rotation autour des trois axes principaux de l’appareil.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Utilisation du gyroscope

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité de gyroscope appelle les méthodes `Start` et `Stop` pour écouter les changements relatifs au gyroscope. Tous les changements sont renvoyés via l’événement `ReadingChanged` en rad/s. Voici un exemple d’utilisation :

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z reported in rad/s
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
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

## <a name="api"></a>API

- [Code source de la fonctionnalité de gyroscope](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Gyroscope)
- [Documentation sur l’API de gyroscope](xref:Xamarin.Essentials.Gyroscope)
