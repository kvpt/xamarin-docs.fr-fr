---
title: Convertisseurs de couleurs Xamarin.Essentials
description: La classe ColorConverters dans Xamarin.Essentials fournit plusieurs méthodes d’assistance et méthodes d’extension à utiliser avec System.Drawing.Color.
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
ms.openlocfilehash: 159add7ee83f3c65d791fc49ee3a85ddaaabae1d
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150139"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin. Essentials : convertisseurs de couleurs

La classe **ColorConverters** dans Xamarin.Essentials fournit plusieurs méthodes d’assistance pour System.Drawing.Color.

## <a name="get-started"></a>Prendre en main

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
var blueUInt = ColorConverters.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Utilisation d’extensions de couleurs

Les méthodes d’extension sur `System.Drawing.Color` vous permettent d’appliquer différentes propriétés :

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Il existe plusieurs autres méthodes d’extension, notamment :

- GetComplementary
- MultiplyAlpha
- ToUInt
- WithAlpha
- WithHue
- WithLuminosity
- WithSaturation

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

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Color-Converters-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
