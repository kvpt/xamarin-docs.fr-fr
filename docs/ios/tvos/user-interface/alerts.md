---
title: Utilisation des alertes tvOS dans Xamarin
description: Ce document décrit comment utiliser les alertes tvOS dans Xamarin. Il aborde l’affichage d’une alerte, l’ajout de champs de texte et d’une classe d’assistance.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: de7c8918ff500cb2353214fd84eaa4c97713493e
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227265"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Utilisation des alertes tvOS dans Xamarin

_Cet article traite de l’utilisation de UIAlertController pour afficher un message d’alerte à l’utilisateur dans Xamarin. tvOS._

Si vous avez besoin d’attirer l’attention de l’utilisateur tvOS ou de demander l’autorisation d’effectuer une action destructrice (telle que la suppression d’un fichier), vous pouvez présenter `UIAlertViewController`un message d’alerte à l’aide des éléments suivants:

[![](alerts-images/alert01.png "Exemple de UIAlertViewController")](alerts-images/alert01.png#lightbox)

En plus d’afficher un message, vous pouvez ajouter des boutons et des champs de texte à une alerte pour permettre à l’utilisateur de répondre aux actions et de fournir des commentaires.

<a name="About-Alerts" />

## <a name="about-alerts"></a>À propos des alertes

Comme indiqué ci-dessus, les alertes sont utilisées pour attirer l’attention de l’utilisateur et l’informer de l’état de votre application ou demander des commentaires. Les alertes doivent présenter un titre, mais elles peuvent éventuellement avoir un message et un ou plusieurs boutons ou champs de texte.

[![](alerts-images/alert04.png "Un exemple d’alerte")](alerts-images/alert04.png#lightbox)

Apple propose les suggestions suivantes pour l’utilisation des alertes:

- **Utiliser des alertes** avec modération-les alertes perturbent le workflow de l’utilisateur avec l’application et interrompent l’expérience utilisateur. ne doivent donc être utilisées que dans des situations importantes, telles que les notifications d’erreur, les achats dans l’application et les actions destructrices.
- **Fournit des choix utiles** : si l’alerte présente des options à l’utilisateur, vous devez vous assurer que chaque option présente des informations critiques et fournit des actions utiles à l’utilisateur.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Titres et messages d’alerte

Apple propose les suggestions suivantes pour présenter le titre d’une alerte et un message facultatif:

- **Utiliser des titres** à plusieurs mots: le titre d’une alerte doit faire apparaître clairement le point de la situation tout en restant simple. Un titre de mot unique fournit rarement des informations suffisantes.
- **Utilisez des titres descriptifs qui ne nécessitent pas de message** , dans la mesure du possible, pensez à rendre le titre de l’alerte suffisamment descriptif pour que le texte de message facultatif ne soit pas obligatoire.
- **Faites en sorte que le message soit une phrase brève et complète** : si le message facultatif est nécessaire pour faire passer le point de l’alerte, maintenez-le aussi simple que possible et faites-en une phrase complète avec une mise en majuscule et une ponctuation appropriées.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Boutons d’alerte

Apple propose la suggestion suivante pour l’ajout de boutons à une alerte:

- **Limiter à deux boutons** : dans la mesure du possible, limitez l’alerte à deux boutons au maximum. Les alertes à bouton unique fournissent des informations, mais aucune action. Les alertes à deux boutons offrent un choix simple: oui/non.
- **Utilisez des noms de bouton succincts et logiques** : un simple à deux titres de bouton Word qui décrivent clairement l’action du bouton. Pour plus d’informations, consultez notre documentation [sur les boutons utilisation de](~/ios/tvos/user-interface/buttons.md) .
- **Marquez clairement les boutons destructifs** : pour les boutons qui exécutent une action destructrice (telle que la suppression d’un `UIAlertActionStyle.Destructive` fichier), marquez-les clairement avec le style.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Affichage d’une alerte

Pour afficher une alerte, vous devez créer une instance du `UIAlertViewController` et la configurer en ajoutant des actions (boutons) et en sélectionnant le style de l’alerte. Par exemple, le code suivant affiche une alerte OK/Annuler:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...

var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

Jetons un coup d’œil à ce code en détail. Tout d’abord, nous créons une nouvelle alerte avec le titre et le message donnés:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Ensuite, pour chaque bouton que vous souhaitez afficher dans l’alerte, nous créons une action qui définit le titre du bouton, son style et l’action que vous souhaitez effectuer si le bouton est enfoncé:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ =>
    // Do something when the button is pressed
    ...
);
```

L' `UIAlertActionStyle` énumération vous permet de définir le style du bouton comme suit:

- **Par défaut** : le bouton est le bouton par défaut sélectionné lors de l’affichage de l’alerte.
- **Annuler** : le bouton est le bouton Annuler de l’alerte.
- **Destructif** -met en surbrillance le bouton comme une action destructrice, telle que la suppression d’un fichier. Actuellement, tvOS restitue le bouton destructif avec un arrière-plan rouge.

La `AddAction` méthode ajoute l’action donnée `UIAlertViewController` au et, enfin, `PresentViewController (alertController, true, null)` la méthode affiche l’alerte donnée à l’utilisateur.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Ajout de champs de texte

Outre l’ajout d’actions (boutons) à l’alerte, vous pouvez ajouter des champs de texte à l’alerte pour permettre à l’utilisateur de renseigner des informations telles que des ID utilisateur et des mots de passe:

[![](alerts-images/alert02.png "Champ de texte dans une alerte")](alerts-images/alert02.png#lightbox)

Si l’utilisateur sélectionne le champ de texte, le clavier standard tvOS est affiché, ce qui lui permet d’entrer une valeur pour le champ:

[![](alerts-images/alert03.png "Entrée de texte")](alerts-images/alert03.png#lightbox)

Le code suivant affiche une alerte OK/Annuler avec un champ de texte unique pour l’entrée d’une valeur:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

La `AddTextField` méthode ajoute un nouveau champ de texte à l’alerte que vous pouvez ensuite configurer en définissant des propriétés telles que le texte de l’espace réservé (le texte qui apparaît lorsque le champ est vide), la valeur de texte par défaut et le type de clavier. Par exemple :

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Pour que nous puissions agir ultérieurement sur la valeur du champ de texte, nous enregistrons également une copie de à l’aide du code suivant:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

Une fois que l’utilisateur a entré une valeur dans le champ de texte, nous `field` pouvons utiliser la variable pour accéder à cette valeur.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Classe d’assistance du contrôleur d’affichage des alertes

Étant donné que l’affichage d’alertes simples et `UIAlertViewController` courantes à l’aide de peut entraîner un certain nombre de code en double, vous pouvez utiliser une classe d’assistance pour réduire la quantité de code répétitif. Par exemple :

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

À l’aide de cette classe, l’affichage et la réponse aux alertes simples peuvent être effectués comme suit:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article traite de l’utilisation `UIAlertController` de pour afficher un message d’alerte à l’utilisateur dans Xamarin. tvOS. Tout d’abord, il a montré comment afficher une alerte simple et ajouter des boutons. Ensuite, il a montré comment ajouter des champs de texte à une alerte. Enfin, il a montré comment utiliser une classe d’assistance pour réduire la quantité de code répétitif nécessaire à l’affichage d’une alerte.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
