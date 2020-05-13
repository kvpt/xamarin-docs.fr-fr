---
title: 'Xamarin.Essentials : boussole'
description: Ce document décrit la classe Compass de Xamarin.Essentials, qui vous permet de superviser le cap du nord magnétique de l’appareil.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 54ce725a319e0222179945ece558338c8a152653
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150126"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials : boussole

La classe **Compass** vous permet de superviser le cap du nord magnétique de l’appareil.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Utilisation de la boussole

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité de boussole appelle les méthodes `Start` et `Stop` pour écouter les changements relatifs à la boussole. Toutes les variations peuvent être écoutées via l’événement `ReadingChanged`. Voici un exemple :

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Android ne fournit pas d’API pour la récupération de l’en-tête Compass. Nous utilisons l’accéléromètre et le magnétomètre pour calculer le cap du nord magnétique, ce qui est recommandé par Google.

Dans de rares cas, vous constaterez peut-être des résultats incohérents, car les capteurs doivent être étalonnés, ce qui implique de bouger votre appareil en formant un 8. Pour ce faire, ouvrez Google Maps, appuyez sur le point correspondant à votre position, puis sélectionnez **Calibrate compass (Étalonner la boussole)**.

L’exécution de plusieurs capteurs à partir de votre application en même temps peut ajuster la vitesse du capteur.

## <a name="low-pass-filter"></a>Filtre passe-bas

En raison du mode de mise à jour et de calcul des valeurs de la boussole Android, il peut s’avérer nécessaire d’affiner ces valeurs. Un _filtre passe-bas_ peut être appliqué, qui calcule la moyenne des valeurs sinusoïdales et cosinus des angles et peut être activée à l’aide de la `Start` surcharge de méthode, qui accepte le `bool applyLowPassFilter` paramètre :

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

Il est appliqué uniquement sur la plateforme Android et le paramètre est ignoré sur iOS et UWP.  Vous trouverez plus d’informations [ici](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Code source de la fonctionnalité de boussole](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentation sur l’API de boussole](xref:Xamarin.Essentials.Compass)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Compass-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
