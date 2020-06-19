---
title: 'Xamarin.Essentials: Informations d’affichage de l’appareil'
description: Ce document décrit la classe DeviceDisplay dans Xamarin.Essentials , qui fournit des mesures d’écran pour l’appareil sur lequel l’application s’exécute.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 867e6bd1828d4158f70226dbaad678f9d6037bb0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802399"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informations d’affichage de l’appareil

La classe **DeviceDisplay** fournit des informations sur les métriques d’écran de l’appareil où s’exécute l’application et peut demander d’empêcher l’écran de se mettre en veille quand l’application est en cours d’exécution.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Utilisation de DeviceDisplay

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>Informations principales sur l’écran

En plus des informations de base relatives à l’appareil, la classe **DeviceDisplay** contient des informations sur l’écran et l’orientation de l’appareil.

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

La classe **DeviceDisplay** expose également un événement auquel il est possible de s’abonner, et qui se déclenche chaque fois qu’une métrique d’écran change :

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

La classe **DeviceDisplay** expose une propriété `bool` appelée `KeepScreenOn` qui peut être définie pour tenter d’empêcher la désactivation ou le verrouillage de l’écran de l’appareil.

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

Aucune différence.

# <a name="ios"></a>[iOS](#tab/ios)

- L’accès à `DeviceDisplay` doit être effectué sur le thread d’IU, sinon une exception est levée. Vous pouvez utiliser la [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) méthode pour exécuter ce code sur le thread d’interface utilisateur.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence.

--------------

## <a name="api"></a>API

- [Code source de DeviceDisplay](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/DeviceDisplay)
- [Documentation sur l’API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Display-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
