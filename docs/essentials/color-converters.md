---
title: Convertisseurs de couleurs Xamarin.Essentials
description: La classe ColorConverters dans Xamarin.Essentials fournit plusieurs méthodes d’assistance et méthodes d’extension à utiliser avec System.Drawing.Color.
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: aab0857381d6805503824d89bf47e9d4a6f3ab79
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120120"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: Convertisseurs de couleurs

La classe **ColorConverters** dans Xamarin.Essentials fournit plusieurs méthodes d’assistance pour System.Drawing.Color.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Utilisation de convertisseurs de couleurs

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Quand vous travaillez avec `System.Drawing.Color`, vous pouvez utiliser les convertisseurs intégrés de Xamarin.Forms pour créer une couleur Hsl, Hex ou UInt.

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverers.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Utilisation d’extensions de couleurs

Les méthodes d’extension sur `System.Drawing.Color` vous permettent d’appliquer différentes propriétés :

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Il existe plusieurs autres méthodes d’extension, notamment :

- ToUInt
- MultiplyAlpha
- WithHue
- WithAlpha
- WithSaturation
- WithLuminosity


## <a name="using-platform-extensions"></a>Utilisation d’extensions de plateforme

De plus, vous pouvez convertir System.Drawing.Color en structure de couleurs spécifique de la plateforme. Ces méthodes peuvent uniquement être appelées à partir de projets iOS, Android et UWP.

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);
 
// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```


```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);
 
// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

La méthode `ToSystemColor` s’applique à Android.Graphics.Color, UIKit.UIColor et Windows.UI.Color.


## <a name="api"></a>API

- [Code source des convertisseurs de couleurs](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentation sur les API de convertisseurs de couleurs](xref:Xamarin.Essentials.ColorConverters)
- [Code source des extensions de couleurs](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentation sur les API d’extensions de couleurs](xref:Xamarin.Essentials.ColorExtensions)
