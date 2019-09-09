---
title: 'Xamarin.Essentials: Informations d’affichage de l’appareil'
description: La classe DeviceDisplay fournit des informations à propos de l’écran de l’appareil.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 77af173bc3297ac9ccdef22dccbeab054895f772
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756905"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informations d’affichage de l’appareil

La classe **DeviceDisplay** fournit des informations sur les métriques d’écran de l’appareil où s’exécute l’application et peut demander d’empêcher l’écran de se mettre en veille quand l’application est en cours d’exécution.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Utilisation de **DeviceDisplay**

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

La classe **DeviceDisplay** expose également un événement auquel il est possible de s'inscrire et qui se déclenche à chaque fois qu'une information de l'écran est modifiée :

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

Aucune différence.

# <a name="iostabios"></a>[iOS](#tab/ios)

- L’accès à `DeviceDisplay` doit être effectué sur le thread d’IU, sinon une exception est levée. Vous pouvez utiliser la méthode [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) pour exécuter ce code sur le thread d’IU.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Aucune différence.

--------------

## <a name="api"></a>API

- [Code source de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentation sur l’API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
