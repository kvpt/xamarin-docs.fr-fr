---
title: 'Xamarin.Essentials: OrientationSensor'
description: La classe Capteur d’orientation vous permet de surveiller l’orientation d’un appareil dans l’espace tridimensionnel.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5423872da7966bc7e4bb88e278d76b709f114158
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120716"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

La classe **Capteur d’orientation** vous permet de surveiller l’orientation d’un appareil dans l’espace tridimensionnel.

> [!NOTE]
> Cette classe permet de déterminer l’orientation d’un appareil dans un espace en 3D. Si vous avez besoin de déterminer si l’écran de l’appareil vidéo est en mode portrait ou paysage, utilisez la propriété `Orientation` de l’objet `ScreenMetrics` disponible à partir de la classe [`DeviceDisplay`](device-display.md).

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-orientationsensor"></a>Utilisation de Capteur d’orientation

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` est activé en appelant la méthode `Start` pour surveiller les modifications apportées à l’orientation de l’appareil. Il est désactivé en appelant la méthode `Stop`. Toutes les variations peuvent être écoutées via l’événement `ReadingChanged`. Voici un exemple d’utilisation :

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

Les lectures `OrientationSensor` sont retournées sous la forme d’un [`Quaternion`](xref:System.Numerics.Quaternion) qui décrit l’orientation de l’appareil en fonction de deux systèmes de coordonnées 3D :

L’appareil (en général, un téléphone ou une tablette) dispose d’un système de coordonnées 3D avec les axes suivants :

- L’axe X positif pointe vers la droite de l’écran en mode portrait.
- L’axe Y positif pointe vers le haut de l’appareil en mode portrait.
- L’axe Z positif pointe en dehors de l’écran.

Le système de coordonnées 3D de la terre a les axes suivants :

- L’axe X positif est la tangente de la surface de la terre et pointe vers l’est.
- L’axe Y positif est aussi la tangente de la surface de la terre et pointe vers le nord.
- L’axe Z positif est perpendiculaire à la surface de la terre et pointe vers le haut.

`Quaternion` décrit la rotation du système de coordonnées de l’appareil par rapport au système de coordonnées de la terre.

Une valeur `Quaternion` est très étroitement liée à la rotation autour d’un axe. Si un axe de rotation est le vecteur normalisé (a<sub>x</sub>, a<sub>y</sub>, a<sub>z</sub>), et l’angle de rotation est Θ, alors les composants (X, Y, Z, W) du quaternion sont :

(a<sub>x</sub>·sin(Θ/2), a<sub>y</sub>·sin(Θ/2), a<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Il s’agit de systèmes de coordonnées droitiers, donc avec le pouce de la main droit pointée dans le sens positif de l’axe de rotation, la courbe des doigts indique la direction de rotation des angles positifs.

Exemples :

- Lorsque l’appareil se trouve à plat sur une table avec son écran vers le haut, avec le bord supérieur de l’appareil (en mode portrait) pointant vers le nord, les deux systèmes de coordonnées sont alignés. La valeur `Quaternion` représente le quaternion d’identité (0, 0, 0, 1). Toutes les rotations peuvent être analysées par rapport à cette position.

- Lorsque l’appareil se trouve à plat sur une table avec son écran vers le haut, avec le bord supérieur de l’appareil (en mode portrait) pointant vers l’ouest, la valeur `Quaternion` est (0, 0, 0.707, 0.707). L’appareil a été pivoté de 90 degrés autour de l’axe Z de la terre.

- Lorsque l’appareil est maintenu à la verticale afin que le haut (en mode portrait) pointe vers le ciel et que l’arrière de l’appareil soit face au nord, l’appareil a été pivoté de 90 degrés autour de l’axe X. La valeur `Quaternion` est (0.707, 0, 0, 0.707).

- Si l’appareil est positionné de façon à ce que son bord gauche soit sur une table et que le haut de l’appareil pointe vers le nord, l’appareil a été pivoté de &ndash;90 degrés autour de l’axe Y (ou 90 degrés autour de l’axe Y négatif). La valeur `Quaternion` est (0, -0.707, 0, 0.707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Code source de Capteur d’orientation](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentation de l’API Capteur d’orientation](xref:Xamarin.Essentials.OrientationSensor)
