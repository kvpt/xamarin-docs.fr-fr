---
title: 'Xamarin.Essentials : gyroscope'
description: La classe Gyroscope de Xamarin.Essentials vous permet de superviser le capteur gyroscopique de l’appareil, qui mesure la rotation autour des trois axes principaux de l’appareil.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1e19585e238d66568364be7ccdbdb52d22b04066
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259487"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials : gyroscope

La classe **Gyroscope** vous permet de superviser le capteur gyroscopique de l’appareil, qui mesure la rotation autour des trois axes principaux de l’appareil.

## <a name="get-started"></a>Prise en main

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

- [Code source de la fonctionnalité de gyroscope](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentation sur l’API de gyroscope](xref:Xamarin.Essentials.Gyroscope)
