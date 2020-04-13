---
title: 'Xamarin.Essentials : informations sur l’appareil'
description: Ce document décrit la classe DeviceInfo de Xamarin.Essentials, qui fournit des informations sur l’appareil où l’application s’exécute.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1cab4ea8ea3f98def4830e101783db1554efa69c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78295415"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials : informations sur l’appareil

La classe **DeviceInfo** fournit des informations sur l’appareil où l’application s’exécute.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>Utilisation de DeviceInfo

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Les informations suivantes sont exposées via l’API :

```csharp
// Device Model (SMG-950U, iPhone10,6)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platforms"></a>Plateformes

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform)corrélise à une chaîne constante qui cartographie le système d’exploitation. Vous pouvez vérifier les valeurs avec le struct `DevicePlatform` :

- **DevicePlatform.iOS** – iOS
- **DevicePlatform.Android** – Android
- **DevicePlatform.UWP** – UWP
- **DevicePlatform.Unknown** – Inconnu

## <a name="idioms"></a>Idiomes

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom)corrèle une chaîne constante sur laquelle les cartes sont alignées sur le type d’appareil sur lequel l’application s’exécute. Vous pouvez vérifier les valeurs avec le struct `DeviceIdiom` :

- **DeviceIdiom.Phone** – Téléphone
- **DeviceIdiom.Tablet** – Tablette
- **DeviceIdiom.Desktop** – Ordinateur de bureau
- **DeviceIdiom.TV** – TV
- **DeviceIdiom.Watch** – Espion
- **DeviceIdiom.Unknown** – Inconnu

## <a name="device-type"></a>Type d’appareil

`DeviceInfo.DeviceType` est mis en corrélation avec une énumération pour déterminer si l’application s’exécute sur un appareil physique ou virtuel. Un appareil virtuel est un simulateur ou un émulateur.

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="ios"></a>[iOS](#tab/ios)

iOS n’expose pas une API pour les développeurs d’obtenir le modèle de l’appareil iOS spécifique. Au lieu de cela un identifiant matériel est retourné comme _l’iPhone10,6_ qui se réfère à l’iPhone X. Une cartographie de ces identificateurs ne sont pas fournis par Apple, mais peut être trouvé sur ces (sources non officielles) [Le Wiki iPhone](https://www.theiphonewiki.com/wiki/Models) et [Get iOS Model](https://github.com/dannycabrera/Get-iOS-Model).

--------------

## <a name="api"></a>API

- [Code source de DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentation sur l’API de DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
