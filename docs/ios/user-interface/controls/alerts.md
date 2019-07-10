---
title: Affichage des alertes dans Xamarin.iOS
description: Ce document décrit comment afficher les alertes dans Xamarin.iOS à l’aide de l’API introduit dans iOS 8 UIAlertController.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 576de9a46fc89fe01c564c05568d4331a32dbd96
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674394"
---
# <a name="displaying-alerts-in-xamarinios"></a>Affichage des alertes dans Xamarin.iOS

À compter d’iOS 8, UIAlertController a terminé UIActionSheet remplacé et UIAlertView qui sont désormais déconseillés.

Contrairement aux classes qui sont des sous-classes de UIView, qu'il est remplacé, UIAlertController est une sous-classe de UIViewController.

Utilisez `UIAlertControllerStyle` pour indiquer le type d’alerte à afficher. Ces types d’alertes sont :

- **UIAlertControllerStyleActionSheet**
    * Préliminaire iOS 8 cela aurait été un UIActionSheet
- **UIAlertControllerStyleAlert**
    * Préliminaire iOS 8 cela aurait été UIAlertView 

Il existe trois étapes nécessaires à prendre lors de la création d’un contrôleur d’alerte :

- Créer et configurer l’alerte avec r :
    * titre
    * message
    * preferredStyle
    
- (Facultatif) Ajouter un champ de texte
- Ajoutez les actions requises
- Présenter le contrôleur d’affichage

L’alerte la plus simple contient un seul bouton, comme indiqué dans cette capture d’écran :

 ![Alerte avec un bouton](alerts-images/alert1.png)

Le code permettant d’afficher une alerte simple est la suivante :

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

Affiche une alerte avec plusieurs options, s’effectue de manière similaire, mais ajouter deux actions. Par exemple, la capture d’écran suivante montre une alerte avec deux boutons :

 ![Avec deux boutons d’alerte](alerts-images/alert2.png)

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

Alertes peuvent également afficher une feuille d’action, semblable à la capture d’écran ci-dessous :

 ![Alerte de feuille d’action](alerts-images/alert3.png)

Boutons sont ajoutés à l’alerte avec le `AddAction` méthode :

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

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
- [Contrôleur d’alerte](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
