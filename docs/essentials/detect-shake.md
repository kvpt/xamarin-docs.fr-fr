---
title: 'Xamarin.Essentials: Détecter le tremblement'
description: La classe accéléromètre de Xamarin.Essentials vous permet de détecter un mouvement de tremblement de l’appareil.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4fd4c7d0e3ffe04ebbb936cff0992272a9085649
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802426"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: Détecter le tremblement

La classe **[accéléromètre](accelerometer.md)** vous permet de surveiller le capteur d’accéléromètre de l’appareil, qui indique l’accélération de l’appareil dans un espace tridimensionnel. De plus, elle vous permet d’enregistrer des événements quand l’utilisateur secoue l’appareil.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Utilisation de la détection de secousses

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour détecter une secousse de l’appareil, vous devez utiliser la fonctionnalité d’accéléromètre en appelant les méthodes `Start` et `Stop` pour écouter les modifications d’accélération et détecter une secousse. Chaque fois qu’une secousse est détectée, un événement `ShakeDetected` se déclenche. Il est recommandé d’utiliser `Game` ou une version plus rapide pour `SensorSpeed`. Voici un exemple d’utilisation :

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>Informations d’implémentation

L’API de détection des secousses utilise des relevés bruts de l’accéléromètre pour calculer l’accélération. Elle utilise un simple mécanisme de file d’attente pour détecter si les 3/4 des événements d’accéléromètre récents se sont produits lors de la dernière demie-seconde. L’accélération se calcule en additionnant le carré des relevés X, Y et Z de l’accéléromètre, puis en le comparant à un seuil spécifique.

## <a name="api"></a>API

- [Code source de l’accéléromètre](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Accelerometer)
- [Documentation de l’API Accéléromètre](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Detect-Shake-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
