---
title: Affichage des alertes dans Xamarin. iOS
description: Ce document explique comment afficher des alertes dans Xamarin. iOS à l’aide des API UIAlertController introduites dans iOS 8.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b8ffeb996783d7a3b1a385265b14f37f36126e8f
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119062"
---
# <a name="displaying-alerts-in-xamarinios"></a>Affichage des alertes dans Xamarin. iOS

À compter d’iOS 8, UIAlertController a terminé le remplacement de UIActionSheet et UIAlertView tous deux qui sont désormais dépréciés.

Contrairement aux classes qu’il a remplacées, qui sont des sous-classes de UIView, UIAlertController est une sous-classe de UIViewController.

Utilisez `UIAlertControllerStyle` pour indiquer le type d’alerte à afficher. Ces types d’alertes sont les suivants:

- **UIAlertControllerStyleActionSheet**
  - Pré-iOS 8 cela aurait été un UIActionSheet
- **UIAlertControllerStyleAlert**
  - Pré-iOS 8 cela aurait été UIAlertView 

Il existe trois étapes nécessaires à suivre lors de la création d’un contrôleur d’alerte:

- Créez et configurez l’alerte avec:
  - title
  - message
  - preferredStyle

- Facultatif Ajouter un champ de texte
- Ajouter les actions requises
- Présenter le contrôleur d’affichage

L’alerte la plus simple contient un seul bouton, comme illustré dans cette capture d’écran:

 ![Alerte avec un bouton](alerts-images/alert1.png)

Le code permettant d’afficher une alerte simple est le suivant:

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

L’affichage d’une alerte avec plusieurs options s’effectue de la même manière, mais ajoute deux actions. Par exemple, la capture d’écran suivante montre une alerte avec deux boutons:

 ![Alerte avec deux boutons](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

Les alertes peuvent également afficher une feuille d’action, similaire à la capture d’écran ci-dessous:

 ![Alerte de la feuille d’action](alerts-images/alert3.png)

Les boutons sont ajoutés à l’alerte avec `AddAction` la méthode:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Contrôleur d’alerte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
