---
title: 'Xamarin.Essentials: Informations d’affichage de l’appareil'
description: Ce document décrit la classe DeviceDisplay de Xamarin.Essentials, qui fournit les métriques d’écran de l’appareil sur lequel l’application s’exécute.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 9c0eb4f856dbbb05e891a07326f40528a4e99bea
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120099"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informations d’affichage de l’appareil

La classe **DeviceDisplay** fournit des informations sur les métriques d’écran de l’appareil où s’exécute l’application et peut demander d’empêcher l’écran de se mettre en veille quand l’application est en cours d’exécution.

## <a name="get-started"></a>Prise en main

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
