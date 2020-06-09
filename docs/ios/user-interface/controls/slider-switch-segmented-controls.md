---
title: Curseurs, commutateurs et contrôles segmentés dans Xamarin. iOS
description: Ce document traite des diapositives, des commutateurs et des contrôles segmentés dans Xamarin. iOS, qui décrivent comment les utiliser par programme et dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f7b27c9f1010be313810b4b0f289ef792efd47a3
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568644"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Curseurs, commutateurs et contrôles segmentés dans Xamarin. iOS

<a name="Sliders"></a>

## <a name="sliders"></a>Curseurs

Le contrôle Slider permet une sélection simple d’une valeur numérique dans une plage. La valeur par défaut du contrôle est comprise entre 0 et 1, mais ces limites peuvent être personnalisées.

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

La capture d’écran suivante montre les propriétés qui sont modifiables dans le concepteur :

 [![](slider-switch-segmented-controls-images/image26a.png "Slider Properties")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Vous pouvez définir ces valeurs dans le code comme indiqué ci-dessous, y compris l’installation d’un gestionnaire pour afficher la valeur actuellement sélectionnée dans un `UILabel` contrôle :

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

Vous pouvez également personnaliser l’apparence visuelle du curseur en définissant

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

Le curseur personnalisé ressemble à ceci :

 [![](slider-switch-segmented-controls-images/image27a.png "Custom Slider")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Il existe actuellement un [bogue](https://stackoverflow.com/a/19496179) qui provoque l' `ThumbTint` imprécision du rendu au moment de l’exécution comme prévu. Vous pouvez ajouter la ligne de code suivante **avant** le code ci-dessus comme solution de contournement. [[Source](https://stackoverflow.com/a/21396794)] :
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Vous pouvez utiliser n’importe quelle image, telle qu’elle sera remplacée, mais assurez-vous qu’elle est placée _dans_ le répertoire Resources et qu’elle est appelée dans votre code.

<a name="Switch"></a>

## <a name="switch"></a>Basculer

iOS utilise `UISwitch` comme entrée booléenne qui peut être représentée par un bouton radio sur d’autres plateformes. L’utilisateur peut manipuler le contrôle en déplaçant le *curseur* entre les positions **on/off** .

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

L’apparence du commutateur peut être personnalisée dans le **panneau Propriétés** du concepteur, ce qui vous permet de contrôler l’État par défaut, les couleurs **de teinte d’activation/de désactivation** et une **image d’activation/de désactivation**. Cela est illustré dans l’image ci-dessous :

 [![](slider-switch-segmented-controls-images/image29a.png "Switch Properties")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Les propriétés du commutateur peuvent également être définies dans le code. par exemple, le code ci-dessous affiche un commutateur avec la valeur par défaut `On` :

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls"></a>

## <a name="segmented-controls"></a>Contrôles segmentés

Un contrôle segmenté est un moyen organisé pour permettre aux utilisateurs d’interagir avec un petit nombre d’options. Il est disposé horizontalement et chaque segment fonctionne comme un bouton distinct. Lorsque vous utilisez le concepteur, le contrôle segmenté se trouve sous la **boîte à outils > les contrôles**et doit ressembler à l’image suivante :

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Une fonctionnalité unique du concepteur permet de sélectionner individuellement chaque segment sur l’aire de conception, comme illustré ci-dessous :

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Cela permet à l’Panneau Propriétés d’être utilisé pour contrôler plus précisément les propriétés de chaque segment. Vous pouvez voir les propriétés modifiables dans la capture d’écran ci-dessous :

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Il convient de noter que le style de contrôle segmenté est déconseillé dans iOS7 et, par conséquent, l’ajustement des options pour cela dans une application iOS7 n’aura aucun effet.

## <a name="related-links"></a>Liens connexes

- [Contrôles (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Contrôleur d’alerte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
