---
title: Curseurs, commutateurs et contrôles segmentés dans Xamarin.iOS
description: Ce document aborde les diapositives, les commutateurs et contrôles segmentés dans Xamarin.iOS, décrivant la façon de les manipuler par programmation et dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 7b66005ff64763d68dc6101985e514fa56cf896d
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827365"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Curseurs, commutateurs et contrôles segmentés dans Xamarin.iOS

<a name="Sliders" />

## <a name="sliders"></a>Curseurs

Le contrôle slider permet une sélection simple d’une valeur numérique dans une plage. Par défaut, le contrôle à une valeur comprise entre 0 et 1, mais ces limites peuvent être personnalisés.

 [![](slider-switch-segmented-controls-images/image25a.png "Curseur")](slider-switch-segmented-controls-images/image25a.png#lightbox)

La capture d’écran suivante montre les propriétés qui sont modifiables dans le concepteur :

 [![](slider-switch-segmented-controls-images/image26a.png "Propriétés de curseur")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Vous pouvez définir ces valeurs dans le code comme indiqué ci-dessous, y compris d’associer un gestionnaire pour afficher la valeur actuellement sélectionnée dans un `UILabel` contrôle :

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

Vous pouvez également personnaliser l’apparence visuelle du curseur par paramètre

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

Le curseur personnalisé ressemble à ceci :

 [![](slider-switch-segmented-controls-images/image27a.png "Curseur personnalisé")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Il existe actuellement un [bogue](https://stackoverflow.com/a/19496179) provoquant la `ThumbTint` s’affichait ne pas en cours d’exécution comme prévu. Vous pouvez ajouter la ligne de code suivante **avant** le code ci-dessus comme solution de contournement. [[Source](https://stackoverflow.com/a/21396794)] :
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> Vous pouvez utiliser n’importe quelle image, tel qu’il sera remplacée, mais assurez-vous qu’il est placé _dans_ le répertoire des ressources et est appelée dans votre code.

<a name="Switch" />

## <a name="switch"></a>Basculer

iOS utilise le `UISwitch` comme entrée d’une valeur booléenne qui peut être représentée par une case sur d’autres plateformes. L’utilisateur peut manipuler le contrôle en déplaçant le *thumb* entre le **activé/désactivé** positions.

 [![](slider-switch-segmented-controls-images/image28a.png "Commutateur")](slider-switch-segmented-controls-images/image28a.png#lightbox)

L’apparence du commutateur peut être personnalisé dans le **panneau Propriétés** du concepteur, qui sera vous permettent de contrôler l’état par défaut, **désactivez-les teinte** couleurs et une **on/off Image**. Ceci est illustré dans l’image ci-dessous :

 [![](slider-switch-segmented-controls-images/image29a.png "Propriétés du commutateur")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Les propriétés du commutateur peuvent également être définies dans le code, par exemple le code suivant affiche un commutateur avec la valeur par défaut `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Contrôles segmentés

Un contrôle segmenté est une façon organisée pour permettre aux utilisateurs d’interagir avec un petit nombre d’options. Il est disposé horizontalement et chaque segment fonctionne comme un bouton distinct. Lorsque vous utilisez le concepteur, le contrôle segmenté peut être trouvé sous **boîte à outils > contrôles**et doit ressembler à l’image suivante :

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Contrôle segmenté")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Une fonctionnalité unique du concepteur permet pour chaque segment être sélectionnés individuellement sur l’aire de conception, comme illustré ci-dessous :

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Contrôle segmenté")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

Ainsi, le panneau Propriétés pour vous permettre de contrôler plus précisément les propriétés de chaque segment. Vous pouvez voir les propriétés modifiables dans la capture d’écran ci-dessous :

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Contrôle segmenté")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Il convient de noter que le Style de contrôle segmenté a été déconseillé dans iOS7 et par conséquent, réglage des options pour ce dans une application iOS7 aura aucun effet.

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
- [Contrôleur d’alerte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
