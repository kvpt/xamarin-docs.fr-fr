---
title: Contrôles de l’interface utilisateur Watchos dans Xamarin
description: Ce document décrit les différents contrôles qui peuvent être utilisés dans les interfaces utilisateur espionneos. Il fournit une description des étiquettes, boutons, commutateurs, curseurs, images, séparateurs, cartes, etc.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2016
ms.openlocfilehash: 3d47b8d524d8f798ab9c0a654fde8246b9fbcc36
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437153"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>Contrôles de l’interface utilisateur Watchos dans Xamarin

L’exemple [**WatchKitCatalog**](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) illustre différents contrôles Watchos. Le Storyboard de l’application est affiché ici (cliquez pour effectuer un zoom) :

[![Exemple de disposition Watchos](images/storyboard-sml.png)](images/storyboard.png#lightbox)

Les noms de programmation de tous les contrôles ont pour préfixe `WKInterface` (par exemple, `WKInterfaceLabel`, `WKInterfaceButton`).

|Control|Description|Capture d'écran|
|---|---|---|
|Étiquette|Utilisez `SetText` et d’autres propriétés pour contrôler l’apparence du texte dans un contrôle Label. `NSAttributedString` est également pris en charge.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![Capture d’écran des étiquettes](Images/label.png)|
|Bouton|Créez et définissez des propriétés dans le Storyboard. Ctrl + glisser pour ajouter un `Action` pour implémenter un gestionnaire lorsque l’utilisateur clique dessus.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![Capture d’écran du bouton](Images/button.png)|
|Commutateur|Utilisez `SetOn` pour contrôler l’état du commutateur.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![Capture d’écran du commutateur](Images/switch.png)|
|Curseur|De nombreux styles différents sont possibles.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![Capture d’écran du curseur](Images/slider.png)|
|Image|Utilisez `myImage.SetImage("MyWatchImage")` pour charger des images sur la montre ou `WKInterfaceDevice.CurrentDevice.AddCachedImage` pour les mettre en cache pour une utilisation répétée sur la montre.<br />[Documentation sur le contrôle image](~/ios/watchos/user-interface/image.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![Capture d’écran d’image](Images/image.png)|
|Séparateur|Utilisez des séparateurs pour créer des interfaces utilisateur attrayantes Watch.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![Capture d’écran séparateur](Images/separator.png)|
|Mappage|L’image de la carte est affichée statiquement sur la montre, mais vous pouvez contrôler de nombreux aspects de son apparence, y compris l’ajout de codes confidentiels.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![Capture d’écran de la carte](Images/map.png)|
|Film & InlineMove|Les films peuvent s’ouvrir eux-mêmes ou en ligne<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![Capture d’écran film](Images/movie.png)|
|Group|Utilisez des groupes pour faciliter la création d’interfaces utilisateur attrayantes Watch.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![Capture d’écran du groupe](Images/group.png)|
|Table de charge de travail|Version simplifiée des tables sur iOS. Implémentez `DidSelectRow` pour répondre à la sélection de l’utilisateur (ou utilisez un Segue).<br />[Documentation sur le contrôle de table](~/ios/watchos/user-interface/table.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![Capture d’écran du tableau](Images/table.png)|
|Appareil|`WKInterfaceDevice.CurrentDevice` comprend des propriétés telles que `ScreenBounds` , `ScreenScale` et `PreferredContentSizeCategory` .<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![Capture d’écran de l’appareil](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Définissez le menu force-Press dans le Storyboard et implémentez les actions pour chaque bouton dans le code.<br />[Documentation sur le contrôle de menu (Force Touch)](~/ios/watchos/user-interface/menu.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![Capture d’écran du menu](Images/controller.png)|
|Entrée de texte|Utilisez `PresentTextInputController` et l' `WKTextInputMode` énumération.<br />[Documentation d’entrée de texte](~/ios/watchos/user-interface/text-input.md)<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![Capture d’écran entrée de texte](Images/textinput.png)|
|Digital Crown|La Digital Crown peut être utilisée pour piloter un sélecteur, ou sa rotation peut être suivie dans le code.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![Capture d’écran des couronnes numériques](Images/digital-crown.png)|
|Mouvements|Quatre types de reconnaissance de mouvement peuvent être ajoutés à une scène : TAP, balayer, Pan et LongPress.<br />[Code du catalogue](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![Capture d’écran des mouvements](Images/gestures.png)|

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Informations de référence sur l’API Watch Kit](xref:WatchKit)