---
title : " Xamarin.Essentials : accéléromètre" Description : "la classe accéléromètre de vous Xamarin.Essentials permet de surveiller le capteur de l’accéléromètre de l’appareil, qui indique l’accélération de l’appareil dans l’espace tridimensionnel."
ms. AssetID : 97883573-F0D9-4854-AC7C-A654814401C5 auteur : jamesmontemagno ms. Author : Jamont ms. Date : 04/02/2019 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: Accéléromètre

La classe **Accelerometer** permet de surveiller le capteur de type accéléromètre de l’appareil, qui indique l’accélération de l’appareil dans un espace en trois dimensions.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-accelerometer"></a>Utilisation de **Accelerometer**

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour écouter les changements d’accélération, il suffit d'appeler les méthodes `Start` et `Stop`. Toutes les variations peuvent être écoutées via l’événement `ReadingChanged`. Voici un exemple d’utilisation :

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

Les lectures de l’accéléromètre sont retournées en G. G est une unité de force gravitationnelle égale à celle exercée par le champ gravitationnelle de la Terre (9,81 m/s^2).

Le système de coordonnées est défini par rapport à l’écran du téléphone dans son orientation par défaut. Les axes ne sont pas intervertis quand l’orientation de l’écran de l’appareil change.

L’axe des X est horizontal et pointe vers la droite, l’axe des Y est vertical et pointe vers le haut et l’axe Z pointe vers l’extérieur de la face avant de l’écran. Dans ce système, les coordonnées derrière l’écran ont des valeurs Z négatives.

Exemples :

- Lorsque l’appareil est à plat sur une table et est poussé par son côté gauche vers la droite, la valeur d’accélération x est un nombre positive.

- Lorsque l’appareil est à plat sur une table, la valeur de l’accélération est +1.00 G ou (+9,81 m/s^2), qui correspond à l’accélération de l’appareil (0 m/s^2) moins la force de gravité (-9,81 m/s^2) et standardisée en G.

- Lorsque l’appareil est à plat sur une table et est poussé vers le ciel avec une accélération de A m/s^2, la valeur de l’accélération est égale à A+9,81, qui correspond à l’accélération de l’appareil (+A m/s^2) moins la force de gravité (-9,81 m/s^2) et normalisée en G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Code source de l’accéléromètre](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentation de l’API Accéléromètre](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Accelerometer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
