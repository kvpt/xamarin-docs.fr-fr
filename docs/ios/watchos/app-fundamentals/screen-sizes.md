---
title: Utilisation des tailles d’écran Watchos dans Xamarin
description: Ce document explique comment utiliser différentes tailles d’écran Watchos. Il décrit le concepteur d’interface Watchos, le simulateur Watchos et les ressources d’image.
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: aeaa1bb1273bc062e0ac76eaa09722827f15797f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028392"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>Utilisation des tailles d’écran Watchos dans Xamarin

Apple Watch est disponible dans deux tailles d’écran :

- **38mm**
  - 136 x 170 pixels logiques (272 x 340 pixels physiques)

- **Watch 42mm**
  - 156 x 195 pixels logiques (312 x 390 pixels physiques).

Vous devez prendre en compte la taille de l’écran lors de la conception et du test de vos applications.

## <a name="watchos-interface-designer"></a>Concepteur d’interface Watchos

Par défaut, le concepteur de Visual Studio pour Mac affiche les contrôleurs d’interface Watch à **tout Apple Watch**.

![](screen-sizes-images/screen-any-sml.png "The Designer displays watch interface controllers at Any Apple Watch")

Utilisez le menu taille pour modifier et afficher un aperçu de votre table de montage séquentiel à l’une des tailles d’écran disponibles : **38mm** ou **Watch 42mm**:

![](screen-sizes-images/screen-menu-sml.png "Selecting the 38mm or 42mm size")

La taille d’écran la plus grande peut parfois afficher du contenu qui serait tronqué/masqué sur l’écran le plus petit.
Veillez à tester les deux tailles.

### <a name="interface-design"></a>Conception d'interfaces

Votre application doit afficher le même contenu à l’écran, quelle que soit sa taille, et doit développer ou réduire les éléments de manière appropriée. Dans le concepteur d’Visual Studio pour Mac, dans l’inspecteur d’attribut, vous devez utiliser **par rapport au conteneur** ou **taille pour ajuster le contenu** à la préférence aux tailles fixes.

![](screen-sizes-images/sizeattributepanel-sml.png "Use Relative to Container or Size to Fit Content in preference to fixed sizes")

Étant donné que l’écran Watch est entouré d’un cadre noir, il n’est pas recommandé de fournir un remplissage autour de votre interface. Laissez les éléments reposer sur le bord de l’écran et laissez le cadre former une bordure naturelle autour de l’application.

## <a name="watchos-simulator"></a>Simulateur Watchos

Lors du test sur le simulateur, vous pouvez facilement basculer entre les deux tailles d’écran à l’aide du menu **matériel > périphérique** .

![](screen-sizes-images/simulator.png "The simulator can switch between the two screen sizes using the Hardware Device menu")

## <a name="image-resources"></a>Ressources d’image

Vous devez utiliser plusieurs ressources d’images si une seule ressource ne semble pas correcte à des tailles différentes. Les catalogues de ressources d’images permettent de spécifier des bitmaps distinctes pour chaque taille :

![](screen-sizes-images/images-xcassets.png "Image asset catalog editor")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

Vous pouvez également utiliser du code pour déterminer la taille de l’écran et charger des images différentes :

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

En savoir plus sur l’utilisation du [contrôle image](~/ios/watchos/user-interface/image.md).

## <a name="related-links"></a>Liens associés

- [Introduction à watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
