---
title: Hello.iOS multi-écran - Démarrage rapide
description: Ce document montre comment étendre l’exemple d’application Phoneword pour ajouter un deuxième écran. Il décrit model-view-controller, la navigation iOS et d’autres concepts clés du développement iOS.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: d72e6230-c9ee-4bee-90ec-877d256821aa
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 1b279b125ce88a37ddb3209cfe689a7fef50a256
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938747"
---
# <a name="hello-ios-multiscreen--quickstart"></a>Hello.iOS multi-écran - Démarrage rapide

Cette partie de la procédure pas à pas permet d’ajouter un deuxième écran à l’application Phoneword pour afficher l’historique des numéros de téléphone appelés avec l’application. L’application finale propose un deuxième écran qui affiche l’historique des appels, comme l’illustre la capture d’écran suivante :

[![L’application finale aura un deuxième écran qui affiche l’historique des appels, comme illustré dans cette capture d’écran.](hello-ios-multiscreen-quickstart-images/00.png)](hello-ios-multiscreen-quickstart-images/00.png#lightbox)

La partie [En profondeur](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md) complémentaire reprend l’application générée et aborde l’architecture, la navigation et d’autres nouveaux concepts iOS rencontrés en chemin.

## <a name="requirements"></a>Spécifications

Ce guide reprend là où le document Hello, iOS s’est arrêté et nécessite d’avoir terminé la partie [Hello, iOS - Démarrage rapide](~/ios/get-started/hello-ios/index.md). Téléchargez la version complète de l’application Phoneword à partir de l’[exemple Hello, iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

::: zone pivot="macos"

## <a name="walkthrough-on-macos"></a>Procédure pas à pas sur macOS

Cette procédure pas à pas permet d’ajouter un écran d’historique des appels à notre application **Phoneword**.

1. Ouvrez l’application **Phoneword** dans Visual Studio pour Mac. Si nécessaire, l’application Phoneword terminée du guide [pas à pas de Hello, iOS](~/ios/get-started/hello-ios/index.md) peut être téléchargée [ici](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios).

2. Ouvrez le fichier **Main.storyboard** à partir du **Panneau Solutions** :

    ![Main.storyboard dans le Concepteur iOS](hello-ios-multiscreen-quickstart-images/02new.png)

3. Faites glisser un **contrôleur de navigation** de la **boîte à outils** vers l’aire de conception (vous devrez peut-être effectuer un zoom arrière pour ajuster ces éléments sur l’aire de conception !) :

    ![Faire glisser un contrôleur de navigation de la boîte à outils vers l’aire de conception](hello-ios-multiscreen-quickstart-images/03new.png)

4. Faites glisser le **segue sans source** (qui est la flèche grise à gauche d’un contrôleur d’affichage unique) vers le contrôleur de **navigation** pour modifier le point de départ de l’application :

    ![Faites glisser le segue sans source vers le contrôleur de navigation pour modifier le point de départ de l’application](hello-ios-multiscreen-quickstart-images/04new.png)

5. Sélectionnez le **contrôleur d’affichage racine** existant en cliquant sur la barre inférieure, puis appuyez sur **supprimer** pour le supprimer de l’aire de conception.
Ensuite, déplacez la scène **Phoneword** à côté du **contrôleur de navigation**:

    ![Déplacer la scène Phoneword à côté du contrôleur de navigation](hello-ios-multiscreen-quickstart-images/05new.png)

6. Définissez **ViewController** en tant que **contrôleur d’affichage racine**du contrôleur de navigation. Appuyez sur la touche **CTRL** et cliquez à l’intérieur du **contrôleur de navigation**. Une ligne bleue doit apparaître. Ensuite, maintenez la touche **CTRL** enfoncée, puis faites glisser le **contrôleur de navigation** vers la scène **Phoneword** et la version finale. Cette opération est appelée _Ctrl+Faire glisser_ :

    ![Faites glisser le contrôleur de navigation vers la scène Phoneword et relâchez](hello-ios-multiscreen-quickstart-images/06.png)

7. Depuis le menu, affectez à la relation la valeur **Racine** :

    ![Définition de la relation sur Racine](hello-ios-multiscreen-quickstart-images/07new.png)

    Le **ViewController** est maintenant le **contrôleur d’affichage racine du contrôleur de navigation :**

    ![Le ViewController est maintenant le contrôleur d’affichage racine des contrôleurs de navigation](hello-ios-multiscreen-quickstart-images/08.png)

8. Double-cliquez sur la barre de **titre** de l’écran **Phoneword** et remplacez le **titre** par **Phoneword** :

    ![](hello-ios-multiscreen-quickstart-images/09.png "Change the Title to 'Phoneword'")

9. Faites glisser un **bouton** à partir de la **boîte à outils** et placez-le sous le **bouton d’appel**. Faites glisser les poignées pour que le nouveau **bouton** ait la même largeur que le **bouton d’appel** :

    ![Définir la même largeur que celle du bouton d’appel pour le nouveau bouton](hello-ios-multiscreen-quickstart-images/10new.png)

10. Dans la **panneau Propriétés**, remplacez le **nom** du bouton par **CallHistoryButton** et remplacez le **titre** par **historique des appels**:

    ![Remplacer le nom du bouton par CallHistoryButton et remplacer le titre par Historique des appels](hello-ios-multiscreen-quickstart-images/11new.png)

11. Créez l’écran **Historique des appels**. À partir de la **boîte à outils**, faites glisser un **contrôleur d’affichage de table** sur l’aire de conception :

    ![Faire glisser un contrôleur d’affichage de table sur l’aire de conception](hello-ios-multiscreen-quickstart-images/12new.png)

12. Ensuite, sélectionnez le **contrôleur d’affichage de table** en cliquant sur la barre noire située au bas de la scène. Dans le **panneau Propriétés**, remplacez la classe **du contrôleur d’affichage de table** par `CallHistoryController` et appuyez sur **entrée**:

    ![Remplacez la classe de contrôleurs d’affichage de table par CallHistoryController](hello-ios-multiscreen-quickstart-images/13new.png)

    Le concepteur iOS génère une classe de support personnalisée appelée `CallHistoryController` pour gérer la hiérarchie de vues de contenu de cet écran. Le fichier **CallHistoryController.cs** s’affiche dans le **Panneau Solutions** :

    ![Le fichier CallHistoryController.cs s’affiche dans le Panneau Solutions](hello-ios-multiscreen-quickstart-images/14new.png)

13. Double-cliquez sur le fichier **CallHistoryController.cs** pour l’ouvrir et remplacez son contenu par le code suivant :
    
    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword_iOS
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<string> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Enregistrez l’application (**⌘ + S**) et générez-la (**⌘ + B**) pour vérifier l’absence d’erreurs.

14. Créez un _segue_ (transition) entre la scène **Phoneword** et la scène **historique des appels** .
  Dans la **scène Phoneword**, sélectionnez le **bouton historique des appels** et appuyez sur CTRL + faire glisser depuis le **bouton** vers la scène **historique des appels** :

    ![Ctrl + faire glisser du bouton vers la scène de l’historique des appels](hello-ios-multiscreen-quickstart-images/15.png)

    À partir de l' **action segue** menu Segue, sélectionnez **Afficher** .

    Le concepteur iOS ajoute un Segue entre les deux scènes :

    ![Segue entre les deux scènes](hello-ios-multiscreen-quickstart-images/17new.png)

15. Ajoutez un **titre** au **contrôleur d’affichage table** en sélectionnant la barre noire au bas de la scène et en remplaçant le **titre du contrôleur d’affichage** par historique des **appels** dans la **panneau Propriétés**:

    ![Remplacez le titre du contrôleur d’affichage par historique des appels dans le Panneau Propriétés](hello-ios-multiscreen-quickstart-images/18new.png)

16. Quand l’application s’exécute, le **bouton d’historique des appels** ouvre l’écran **Historique des appels**, mais la vue de tableau est vide, car il n’existe pas de code permettant de mémoriser et d’afficher les numéros de téléphone.

    Cette application enregistre les numéros de téléphone sous la forme d’une liste de chaînes.

    Ajoutez une `using` directive pour `System.Collections.Generic` en haut de **ViewController**:

    ```csharp
    using System.Collections.Generic;
    ```

17. Modifiez la classe `ViewController` avec le code suivant :

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Quelques éléments méritent votre attention :

    - La variable `translatedNumber` a été déplacée de la méthode `ViewDidLoad` vers une _variable de niveau de classe_.
    - Le code de **CallButton** a été modifié pour permettre l’ajout des numéros composés à la liste des numéros de téléphone via l’appel de `PhoneNumbers.Add(translatedNumber)`.
    - La méthode `PrepareForSegue` a été ajoutée.

    Enregistrez et générez l’application pour vérifier qu'il n'y a pas d'erreurs.

18. Appuyez sur le bouton **Démarrer** pour lancer l’application dans le **simulateur iOS**:

    ![Appuyer sur le bouton Démarrer pour lancer l’application dans le simulateur iOS](hello-ios-multiscreen-quickstart-images/19.png)

Félicitations ! Vous avez terminé votre première application Xamarin.iOS multi-écran.

::: zone-end
::: zone pivot="windows"

## <a name="walkthrough-on-windows"></a>Procédure pas à pas sur Windows

Cette procédure pas à pas permet d’ajouter un écran d’historique des appels à notre application **Phoneword**.

1. Ouvrez l’application **Phoneword** dans Visual Studio. Si nécessaire, téléchargez l’[application Phoneword complète](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) à partir du guide de [procédure pas à pas Hello, iOS](~/ios/get-started/hello-ios/index.md). N’oubliez pas qu’il est nécessaire de se connecter à un [Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) pour utiliser le concepteur iOS et le simulateur iOS.

2. Commencez par modifier l’interface utilisateur. Ouvrez le fichier **Main.storyboard** depuis l’**Explorateur de solutions**, en vérifiant que **Afficher en tant que** a la valeur _iPhone 6_ :

    ![Main.storyboard dans le Concepteur iOS](hello-ios-multiscreen-quickstart-images/image1.png)

3. Faites glisser un **contrôleur de navigation** de la **boîte à outils** vers l’aire de conception :

    ![Faire glisser un contrôleur de navigation de la boîte à outils vers l’aire de conception](hello-ios-multiscreen-quickstart-images/image2.png)

4. Faites glisser le **segue sans source** (c’est-à-dire la flèche grise à gauche de la scène **Phoneword** ) de la scène **Phoneword** vers le **contrôleur de navigation** pour modifier le point de départ de l’application :

    ![Faites glisser le segue sans source vers le contrôleur de navigation pour modifier le point de départ de l’application](hello-ios-multiscreen-quickstart-images/image3.png)

5. Sélectionnez le **contrôleur d’affichage racine** en cliquant sur la barre noire, puis appuyez sur la touche **Suppr** pour la supprimer de l’aire de conception.
  Ensuite, déplacez la scène **Phoneword** à côté du **contrôleur de navigation**:

    ![Déplacer la scène Phoneword à côté du contrôleur de navigation](hello-ios-multiscreen-quickstart-images/image4.png)

6. Définissez **ViewController** en tant que contrôleur d’affichage racine du contrôleur de navigation. Appuyez sur la touche **Ctrl** et cliquez à l’intérieur du **contrôleur de navigation**. Une ligne bleue doit apparaître. Ensuite, maintenez la touche **CTRL** enfoncée, puis faites glisser le **contrôleur de navigation** vers la scène **Phoneword** et la version finale. Cette opération est appelée _Ctrl+Faire glisser_ :

    ![Faites glisser le contrôleur de navigation vers la scène Phoneword et relâchez](hello-ios-multiscreen-quickstart-images/image5.png)

7. Depuis le menu, affectez à la relation la valeur **Racine** :

    ![Affecter à la relation la valeur Racine](hello-ios-multiscreen-quickstart-images/image6.png)

    Le **ViewController** est maintenant le **contrôleur d’affichage racine de notre contrôleur de navigation.**

8. Double-cliquez sur la barre de **titre** de l’écran **Phoneword** et remplacez le **titre** par **Phoneword** :

    ![Remplacez le titre par Phoneword](hello-ios-multiscreen-quickstart-images/image7.png)

9. Faites glisser un **bouton** à partir de la **boîte à outils** et placez-le sous le **bouton d’appel**. Faites glisser les poignées pour que le nouveau **bouton** ait la même largeur que le **bouton d’appel** :

    ![Définir la même largeur que celle du bouton d’appel pour le nouveau bouton](hello-ios-multiscreen-quickstart-images/image8.png)

10. Dans le **volet Propriétés**, remplacez le **nom** du **bouton** par `CallHistoryButton` et remplacez le **titre** par **Historique des appels** :

    ![](hello-ios-multiscreen-quickstart-images/image9.png "Change the Name of the Button to 'CallHistoryButton' and the Title to 'Call History'")

11. Créez l’écran **Historique des appels**. À partir de la **boîte à outils**, faites glisser un **contrôleur d’affichage de table** sur l’aire de conception :

    ![Faire glisser un contrôleur d’affichage de table sur l’aire de conception](hello-ios-multiscreen-quickstart-images/image10.png)

12. Sélectionnez le **contrôleur d’affichage de table** en cliquant sur la barre noire située au bas de la scène. Dans l' **Explorateur de propriétés**, remplacez la classe **du contrôleur d’affichage de table** par `CallHistoryController` et appuyez sur **entrée**:

    ![Remplacez la classe de contrôleurs d’affichage de table par CallHistoryController](hello-ios-multiscreen-quickstart-images/image11.png)

    Le concepteur iOS génère une classe de support personnalisée appelée `CallHistoryController` pour gérer la hiérarchie de vues de contenu de cet écran. Le fichier **CallHistoryController.cs** s’affiche dans l’**Explorateur de solutions** :

    ![Le fichier CallHistoryController.cs s’affiche dans l’Explorateur de solutions](hello-ios-multiscreen-quickstart-images/image12.png)

13. Double-cliquez sur le fichier **CallHistoryController.cs** pour l’ouvrir et remplacez son contenu par le code suivant :

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.Collections.Generic;

    namespace Phoneword
    {
        public partial class CallHistoryController : UITableViewController
        {
            public List<String> PhoneNumbers { get; set; }

            static NSString callHistoryCellId = new NSString ("CallHistoryCell");

            public CallHistoryController (IntPtr handle) : base (handle)
            {
                TableView.RegisterClassForCellReuse (typeof(UITableViewCell), callHistoryCellId);
                TableView.Source = new CallHistoryDataSource (this);
                PhoneNumbers = new List<string> ();
            }

            class CallHistoryDataSource : UITableViewSource
            {
                CallHistoryController controller;

                public CallHistoryDataSource (CallHistoryController controller)
                {
                    this.controller = controller;
                }

                // Returns the number of rows in each section of the table
                public override nint RowsInSection (UITableView tableView, nint section)
                {
                    return controller.PhoneNumbers.Count;
                }

                public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
                {
                    var cell = tableView.DequeueReusableCell (CallHistoryController.callHistoryCellId);

                    int row = indexPath.Row;
                    cell.TextLabel.Text = controller.PhoneNumbers [row];
                    return cell;
                }
            }
        }
    }
    ```

    Enregistrez l’application et générez-la pour vérifier l’absence d’erreurs. Vous pouvez ignorer les avertissements de génération pour le moment.

14. Créez un _segue_ (transition) entre la scène **Phoneword** et la scène **historique des appels** .
  Dans la **scène Phoneword**, sélectionnez le **bouton historique des appels** et **Appuyez sur CTRL + faire glisser** depuis le **bouton** vers la scène **historique des appels** :

    ![Ctrl + faire glisser du bouton vers la scène de l’historique des appels](hello-ios-multiscreen-quickstart-images/image13.png)

    Dans le menu **Action Segue**, sélectionnez **Afficher** :

    ![Sélectionner Afficher comme type de Segue](hello-ios-multiscreen-quickstart-images/image14.png)

    Le concepteur iOS ajoute un segue entre les deux scènes :

    ![Segue entre les deux scènes](hello-ios-multiscreen-quickstart-images/image15.png)

15. Ajoutez un **titre** au **contrôleur d’affichage de table** en sélectionnant la barre noire au bas de la scène et en remplaçant le contrôleur d' **affichage > titre** par **historique des appels** dans l' **Explorateur de propriétés**:

    ![Remplacer le titre du contrôleur d’affichage par historique des appels](hello-ios-multiscreen-quickstart-images/image16.png)

16. Quand l’application s’exécute, le **bouton d’historique des appels** ouvre l’écran **Historique des appels**, mais la vue de tableau est vide, car il n’existe pas de code permettant de mémoriser et d’afficher les numéros de téléphone.

    Cette application enregistre les numéros de téléphone sous la forme d’une liste de chaînes.

    Ajoutez une directive `using` pour `System.Collections.Generic` en haut de **ViewController** :

    ```csharp
    using System.Collections.Generic;
    ```

17. Modifiez la classe `ViewController` avec le code suivant :

    ```csharp
    using System;
    using System.Collections.Generic;
    using Foundation;
    using UIKit;

    namespace Phoneword_iOS
    {
      public partial class ViewController : UIViewController
      {
        string translatedNumber = "";

        public List<string> PhoneNumbers { get; set; }

        protected ViewController(IntPtr handle) : base(handle)
        {
          //initialize list of phone numbers called for Call History screen
          PhoneNumbers = new List<string>();
        }

        public override void ViewDidLoad()
        {
          base.ViewDidLoad();
          // Perform any additional setup after loading the view, typically from a nib.

          TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
            // Convert the phone number with text to a number
            // using PhoneTranslator.cs
            translatedNumber = PhoneTranslator.ToNumber(
              PhoneNumberText.Text);

            // Dismiss the keyboard if text field was tapped
            PhoneNumberText.ResignFirstResponder();

            if (translatedNumber == "")
            {
              CallButton.SetTitle("Call ", UIControlState.Normal);
              CallButton.Enabled = false;
            }
            else
            {
              CallButton.SetTitle("Call " + translatedNumber,
                UIControlState.Normal);
              CallButton.Enabled = true;
            }
          };

          CallButton.TouchUpInside += (object sender, EventArgs e) => {

            //Store the phone number that we're dialing in PhoneNumbers
            PhoneNumbers.Add(translatedNumber);

            // Use URL handler with tel: prefix to invoke Apple's Phone app...
            var url = new NSUrl("tel:" + translatedNumber);

            // otherwise show an alert dialog
            if (!UIApplication.SharedApplication.OpenUrl(url))
            {
              var alert = UIAlertController.Create("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
              alert.AddAction(UIAlertAction.Create("Ok", UIAlertActionStyle.Default, null));
              PresentViewController(alert, true, null);
            }
          };
        }

        public override void PrepareForSegue(UIStoryboardSegue segue, NSObject sender)
        {
          base.PrepareForSegue(segue, sender);

          // set the view controller that’s powering the screen we’re
          // transitioning to

          var callHistoryController = segue.DestinationViewController as CallHistoryController;

          //set the table view controller’s list of phone numbers to the
          // list of dialed phone numbers

          if (callHistoryController != null)
          {
            callHistoryController.PhoneNumbers = PhoneNumbers;
          }
        }
      }
    }
    ```

    Quelques éléments méritent votre attention
    - La variable `translatedNumber` a été déplacée depuis la méthode `ViewDidLoad` vers une _variable de niveau de classe_.
    - Le code de **CallButton** a été modifié pour ajouter les numéros composés à la liste des numéros de téléphone en appelant `PhoneNumbers.Add(translatedNumber)`
    - La méthode `PrepareForSegue` a été ajoutée

    Enregistrez et générez l’application pour vérifier qu'il n'y a pas d'erreurs.

    Enregistrez et générez l’application pour vérifier qu'il n'y a pas d'erreurs.

18. Appuyez sur le bouton **Démarrer** pour lancer l’application dans le **simulateur iOS** :

    ![Premier écran de l’exemple d’application](hello-ios-multiscreen-quickstart-images/19.png)

Félicitations ! Vous avez terminé votre première application Xamarin.iOS multi-écran.

::: zone-end

L’application peut désormais prendre en charge la navigation à l’aide de Segues de Storyboard et dans le code. Le moment est maintenant venu d’éplucher les outils découverts et les compétences acquises tout au long du guide [Hello, iOS multi-écran - En profondeur](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md).

## <a name="related-links"></a>Liens associés

- [Hello, iOS (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Human Interface Guidelines pour iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portail d’approvisionnement iOS](https://developer.apple.com/ios/manage/overview/index.action)
