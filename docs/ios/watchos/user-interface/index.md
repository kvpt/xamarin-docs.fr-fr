---
title: Contrôles de l’interface utilisateur Watchos dans Xamarin
description: Ce document décrit les différents contrôles qui peuvent être utilisés dans les interfaces utilisateur espionneos. Il fournit une description des étiquettes, boutons, commutateurs, curseurs, images, séparateurs, cartes, etc.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: 8836eafbbce30586116fd7a7b125da55fe6edf8e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032717"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Contrôles de l’interface utilisateur Watchos dans Xamarin

L’exemple [**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) illustre différents contrôles Watchos. Le Storyboard de l’application est affiché ici (cliquez pour effectuer un zoom) :

[![](images/storyboard-sml.png "Sample watchOS layout")](images/storyboard.png#lightbox)

Les noms de tous les contrôles sont préfixés par des `WKInterface` (par exemple, `WKInterfaceLabel`, `WKInterfaceButton`).

|Contrôle|Description|Capture d'écran|
|---|---|---|
|Etiquette|Utilisez `SetText` et d’autres propriétés pour contrôler l’apparence du texte dans un contrôle Label. `NSAttributedString` est également pris en charge.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Button|Créez et définissez des propriétés dans le Storyboard. Ctrl + glisser pour ajouter une `Action` pour implémenter un gestionnaire lorsque l’utilisateur clique dessus.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Basculer|Utilisez `SetOn` pour contrôler l’état du commutateur.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Curseur|De nombreux styles différents sont possibles.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Image|Utilisez `myImage.SetImage("MyWatchImage")` pour charger des images sur la montre, ou `WKInterfaceDevice.CurrentDevice.AddCachedImage` pour les mettre en cache en vue d’une utilisation répétée sur la montre.<br />[Documentation sur le contrôle image](~/ios/watchos/user-interface/image.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Séparateur|Utilisez des séparateurs pour créer des interfaces utilisateur attrayantes Watch.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Carte|L’image de la carte est affichée statiquement sur la montre, mais vous pouvez contrôler de nombreux aspects de son apparence, y compris l’ajout de codes confidentiels.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Film & InlineMove|Les films peuvent s’ouvrir eux-mêmes ou en ligne<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Regrouper|Utilisez des groupes pour faciliter la création d’interfaces utilisateur attrayantes Watch.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Table|Version simplifiée des tables sur iOS. Implémentez `DidSelectRow` pour répondre à la sélection de l’utilisateur (ou utilisez un Segue).<br />[Documentation sur le contrôle de table](~/ios/watchos/user-interface/table.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Appareil|`WKInterfaceDevice.CurrentDevice` comprend des propriétés telles que `ScreenBounds`, `ScreenScale`et `PreferredContentSizeCategory`.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Définissez le menu force-Press dans le Storyboard et implémentez les actions pour chaque bouton dans le code.<br />[Documentation sur le contrôle de menu (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Entrée de texte|Utilisez `PresentTextInputController` et l’énumération `WKTextInputMode`.<br />[Documentation d’entrée de texte](~/ios/watchos/user-interface/text-input.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Digital Crown|La Digital Crown peut être utilisée pour piloter un sélecteur, ou sa rotation peut être suivie dans le code.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Mouvements|Quatre types de reconnaissance de mouvement peuvent être ajoutés à une scène : TAP, balayer, Pan et LongPress.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Informations de référence sur l’API Watch Kit](xref:WatchKit)
