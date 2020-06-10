---
titre : « Xamarin.Essentials : baromètre » Description : « la classe baromètre dans vous Xamarin.Essentials permet de surveiller le capteur baromètre de l’appareil, qui mesure la pression ».
ms. AssetID : DA4F968A-D988-41F5-8745-1BEE693660A1 auteur : jamesmontemagno ms. Author : Jamont ms. Date : 11/04/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: Baromètre

La classe **Baromètre** vous permet d’analyser le capteur du baromètre de l’appareil, qui mesure la pression.

## <a name="get-started"></a>Prendre en main

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

# <a name="android"></a>[Android](#tab/android)

Aucun détail d’implémentation spécifique à la plateforme.

# <a name="ios"></a>[iOS](#tab/ios)

Cette API utilise [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) pour surveiller les changements de pression, qui est une fonctionnalité matérielle ajoutée à l’iPhone 6 et aux appareils les plus récents. Un `FeatureNotSupportedException` sera levé sur les appareils qui ne prennent pas en charge l’altimètre.

`SensorSpeed` n’est pas utilisé, car il n’est pas pris en charge sur iOS.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucun détail d’implémentation spécifique à la plateforme.

-----

## <a name="api"></a>API

- [Code source de Baromètre](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Documentation de l’API Baromètre](xref:Xamarin.Essentials.Barometer)
