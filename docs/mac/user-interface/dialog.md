---
title: Boîtes de dialogue dans Xamarin. Mac
description: Cet article traite de l’utilisation des boîtes de dialogue et des fenêtres modales dans une application Xamarin. Mac. Il décrit la création de fenêtres modales dans Xcode et Interface Builder, l’utilisation de boîtes de dialogue standard et l’interaction C# avec ces contrôles dans le code.
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: a50445307156fc051edbab7abaea6b7bd21aa1fd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032839"
---
# <a name="dialogs-in-xamarinmac"></a>Boîtes de dialogue dans Xamarin. Mac

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes boîtes de dialogue et fenêtres modales qu’un développeur travaillant dans *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos fenêtres modales (ou éventuellement les créer directement C# dans le code).

Une boîte de dialogue s’affiche en réponse à une action de l’utilisateur et permet généralement aux utilisateurs de terminer l’action. Une boîte de dialogue requiert une réponse de l’utilisateur avant de pouvoir être fermée.

Les fenêtres peuvent être utilisées dans un État non modal (par exemple, un éditeur de texte qui peut avoir plusieurs documents ouverts en même temps) ou modale (par exemple, une boîte de dialogue d’exportation qui doit être fermée pour que l’application puisse continuer).

[![](dialog-images/dialog03.png "An open dialog box")](dialog-images/dialog03.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des boîtes de dialogue et des fenêtres modales dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les commandes`Register`et`Export`utilisées pour relier vos C# classes à objective-c. objets et éléments d’interface utilisateur.

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>Introduction aux boîtes de dialogue

Une boîte de dialogue s’affiche en réponse à une action de l’utilisateur (par exemple, l’enregistrement d’un fichier) et permet aux utilisateurs d’effectuer cette action. Une boîte de dialogue requiert une réponse de l’utilisateur avant de pouvoir être fermée.

D’après Apple, il existe trois façons de présenter une boîte de dialogue :

- **Modal document** : une boîte de dialogue modale de document empêche l’utilisateur d’effectuer d’autres opérations au sein d’un document donné tant qu’il n’est pas fermé.
- **Modale** de l’application : une boîte de dialogue modale d’application empêche l’utilisateur d’interagir avec l’application tant qu’elle n’est pas fermée.
- **Non modal** Une boîte de dialogue non modale permet aux utilisateurs de modifier les paramètres dans la boîte de dialogue tout en interagissant avec la fenêtre de document.

### <a name="modal-window"></a>Fenêtre modale

N’importe quel `NSWindow` standard peut être utilisé en tant que boîte de dialogue personnalisée en l’affichant de manière modale :

[![](dialog-images/modal01.png "An example modal window")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>Feuilles de dialogue modales de document

Une _feuille_ est une boîte de dialogue modale qui est attachée à une fenêtre de document donnée, ce qui empêche les utilisateurs d’interagir avec la fenêtre jusqu’à ce qu’ils fassent disparaître la boîte de dialogue. Une feuille est attachée à la fenêtre à partir de laquelle elle émerge et une seule feuille peut être ouverte pour une fenêtre à un moment donné.

[![](dialog-images/sheet08.png "An example modal sheet")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>Fenêtres préférences

Une fenêtre Préférences est une boîte de dialogue non modale qui contient les paramètres de l’application que l’utilisateur modifie rarement. Les fenêtres de préférences incluent souvent une barre d’outils qui permet à l’utilisateur de passer d’un groupe de paramètres à un autre :

[![](dialog-images/dialog02.png "An example preference window")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>Boîte de dialogue Ouvrir

La boîte de dialogue Ouvrir offre aux utilisateurs une méthode cohérente pour rechercher et ouvrir un élément dans une application :

[![](dialog-images/dialog03.png "A open dialog box")](dialog-images/dialog03.png#lightbox)

### <a name="print-and-page-setup-dialogs"></a>Boîtes de dialogue d’impression et de mise en page

macOS fournit des boîtes de dialogue d’impression et de mise en page standard que votre application peut afficher afin que les utilisateurs puissent bénéficier d’une expérience d’impression cohérente dans chaque application qu’ils utilisent.

La boîte de dialogue Imprimer peut être affichée à la fois en tant que boîte de dialogue flottante libre :

[![](dialog-images/print01.png "A print dialog box")](dialog-images/print01.png#lightbox)

Ou elle peut être affichée sous la forme d’une feuille :

[![](dialog-images/print02.png "A print sheet")](dialog-images/print02.png#lightbox)

La boîte de dialogue mise en page peut être affichée en tant que boîte de dialogue flottante libre :

[![](dialog-images/print03.png "A page setup dialog")](dialog-images/print03.png#lightbox)

Ou elle peut être affichée sous la forme d’une feuille :

[![](dialog-images/print04.png "A page setup sheet")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>Enregistrer les boîtes de dialogue

La boîte de dialogue Enregistrer permet aux utilisateurs d’enregistrer un élément dans une application de manière cohérente. La boîte de dialogue Enregistrer a deux États : **minimal** (également connu sous le nom de réduit) :

[![](dialog-images/save01.png "A save dialog")](dialog-images/save01.png#lightbox)

Et l’état **développé** :

[![](dialog-images/save02.png "An expanded save dialog")](dialog-images/save02.png#lightbox)

La boîte de dialogue enregistrement **minimal** peut également être affichée sous la forme d’une feuille :

[![](dialog-images/save03.png "A minimal save sheet")](dialog-images/save03.png#lightbox)

Comme la boîte de dialogue d’enregistrement **développée** :

[![](dialog-images/save04.png "An expanded save sheet")](dialog-images/save04.png#lightbox)

Pour plus d’informations, consultez la section [boîtes de dialogue](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1) des instructions de l’interface utilisateur d’Apple [OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>Ajout d’une fenêtre modale à un projet

Outre la fenêtre de document principale, une application Xamarin. Mac peut être amenée à afficher d’autres types de fenêtres pour l’utilisateur, telles que des préférences ou des panneaux d’inspecteur.

Pour ajouter une nouvelle fenêtre, procédez comme suit :

1. Dans le **Explorateur de solutions**, ouvrez le fichier `Main.storyboard` pour le modifier dans le Interface Builder de Xcode.
2. Faites glisser un nouveau **contrôleur d’affichage** dans le aire de conception :

    [![](dialog-images/new01.png "Selecting a View Controller from the Library")](dialog-images/new01.png#lightbox)
3. Dans l' **inspecteur d’identité**, entrez `CustomDialogController` pour le nom de la **classe**: 

    [![](dialog-images/new02.png "Setting the class name")](dialog-images/new02.png#lightbox)
4. Revenez à Visual Studio pour Mac, autorisez-le à se synchroniser avec Xcode et à créer le fichier `CustomDialogController.h`.
5. Revenez à Xcode et concevez votre interface : 

    [![](dialog-images/new03.png "Designing the UI in Xcode")](dialog-images/new03.png#lightbox)
6. Créez un **segue modal** à partir de la fenêtre principale de votre application vers le nouveau contrôleur d’affichage en faisant glisser le contrôle à partir de l’élément d’interface utilisateur qui ouvre la boîte de dialogue dans la fenêtre de la boîte de dialogue. Affectez l' **identificateur** `ModalSegue`: 

    [![](dialog-images/new06.png "A modal segue")](dialog-images/new06.png#lightbox)
7. Connectez-vous aux **actions** et aux **prises**: 

    [![](dialog-images/new04.png "Configuring an Action")](dialog-images/new04.png#lightbox)
8. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Faites en sorte que le fichier `CustomDialogController.cs` ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Ce code expose quelques propriétés pour définir le titre et la description de la boîte de dialogue, ainsi que quelques événements pour réagir à la boîte de dialogue qui est annulée ou acceptée.

Modifiez ensuite le fichier `ViewController.cs`, remplacez la méthode `PrepareForSegue` et faites en sorte qu’elle ressemble à ce qui suit :

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

Ce code initialise le segue que nous avons défini dans la Interface Builder de Xcode dans notre boîte de dialogue et configure le titre et la description. Il gère également le choix que l’utilisateur effectue dans la boîte de dialogue.

Nous pouvons exécuter notre application et afficher la boîte de dialogue personnalisée :

[![](dialog-images/new05.png "An example dialog")](dialog-images/new05.png#lightbox)

Pour plus d’informations sur l’utilisation de Windows dans une application Xamarin. Mac, consultez la documentation relative à l' [utilisation de Windows](~/mac/user-interface/window.md) .

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>Création d’une feuille personnalisée

Une _feuille_ est une boîte de dialogue modale qui est attachée à une fenêtre de document donnée, ce qui empêche les utilisateurs d’interagir avec la fenêtre jusqu’à ce qu’ils fassent disparaître la boîte de dialogue. Une feuille est attachée à la fenêtre à partir de laquelle elle émerge et une seule feuille peut être ouverte pour une fenêtre à un moment donné. 

Pour créer une feuille personnalisée dans Xamarin. Mac, procédez comme suit :

1. Dans le **Explorateur de solutions**, ouvrez le fichier `Main.storyboard` pour le modifier dans le Interface Builder de Xcode.
2. Faites glisser un nouveau **contrôleur d’affichage** dans le aire de conception :

    [![](dialog-images/new01.png "Selecting a View Controller from the Library")](dialog-images/new01.png#lightbox)
3. Concevez votre interface utilisateur :

    [![](dialog-images/sheet01.png "The UI design")](dialog-images/sheet01.png#lightbox)
4. Créez une **feuille segue** à partir de votre fenêtre principale sur le nouveau contrôleur d’affichage : 

    [![](dialog-images/sheet02.png "Selecting the Sheet segue type")](dialog-images/sheet02.png#lightbox)
5. Dans l' **inspecteur d’identité**, nommez la **classe** du contrôleur d’affichage `SheetViewController`: 

    [![](dialog-images/sheet03.png "Setting the class name")](dialog-images/sheet03.png#lightbox)
6. Définissez les **prises** et les **actions**nécessaires : 

    [![](dialog-images/sheet04.png "Defining the required Outlets and Actions")](dialog-images/sheet04.png#lightbox)
7. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser.

Modifiez ensuite le fichier `SheetViewController.cs` et faites-le ressembler à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

Modifiez ensuite le fichier `ViewController.cs`, modifiez la méthode `PrepareForSegue` et faites-lui ressembler à ce qui suit :

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

Si nous exécutons notre application et que vous ouvrez la feuille, elle est attachée à la fenêtre :

[![](dialog-images/sheet08.png "An example sheet")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>Création d’une boîte de dialogue Préférences

Avant de présenter la vue de préférence dans Interface Builder, nous devrons ajouter un type de Segue personnalisé pour gérer le basculement des préférences. Ajoutez une nouvelle classe à votre projet et appelez-la `ReplaceViewSeque`. Modifiez la classe et faites en sorte qu’elle ressemble à ce qui suit :

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

Une fois le segue personnalisé créé, nous pouvons ajouter une nouvelle fenêtre à la Interface Builder de Xcode pour gérer nos préférences.

Pour ajouter une nouvelle fenêtre, procédez comme suit :

1. Dans le **Explorateur de solutions**, ouvrez le fichier `Main.storyboard` pour le modifier dans le Interface Builder de Xcode.
2. Faites glisser un nouveau **contrôleur de fenêtre** dans le aire de conception :

    [![](dialog-images/pref01.png "Select a Window Controller from the Library")](dialog-images/pref01.png#lightbox)
3. Réorganisez la fenêtre près du concepteur de **barres de menus** :

    [![](dialog-images/pref02.png "Adding the new Window")](dialog-images/pref02.png#lightbox)
4. Créer des copies du contrôleur d’affichage attaché, car des onglets s’affichent dans votre affichage des préférences :

    [![](dialog-images/pref03.png "Adding the required View Controllers")](dialog-images/pref03.png#lightbox)
5. Faites glisser un nouveau **contrôleur de barre d’outils** à partir de la **bibliothèque**:

    [![](dialog-images/pref04.png "Select a Toolbar Controller from the Library")](dialog-images/pref04.png#lightbox)
6. Et déposez-la dans la fenêtre du Aire de conception :

    [![](dialog-images/pref05.png "Adding a new Toolbar Controller")](dialog-images/pref05.png#lightbox)
7. Disposition de la conception de votre barre d’outils :

    [![](dialog-images/pref06.png "Layout the toolbar")](dialog-images/pref06.png#lightbox)
8. Cliquez avec le bouton droit sur chaque **bouton de la barre d’outils** et faites-le glisser vers les vues que vous avez créées ci-dessus. Sélectionnez un type de Segue **personnalisé** :

    [![](dialog-images/pref07.png "Setting the segue type")](dialog-images/pref07.png#lightbox)
9. Sélectionnez le nouveau segue et définissez la **classe** sur `ReplaceViewSegue`:

    [![](dialog-images/pref08.png "Setting the segue class")](dialog-images/pref08.png#lightbox)
10. Dans le **Concepteur de menus** de la aire de conception, dans le menu application, sélectionnez **Préférences...** , puis cliquez sur Ctrl et faites glisser vers la fenêtre Préférences pour créer un **affichage** segue :

    [![](dialog-images/pref09.png "Setting the segue type")](dialog-images/pref09.png#lightbox)
11. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser.

Si nous exécutons le code et que vous sélectionnez les **Préférences...** dans le menu de l' **application**, la fenêtre s’affiche :

[![](dialog-images/pref10.png "An example preferences window")](dialog-images/pref10.png#lightbox)

Pour plus d’informations sur l’utilisation des fenêtres et des barres d’outils, consultez la documentation sur les [fenêtres](~/mac/user-interface/window.md) et les [barres d’outils](~/mac/user-interface/toolbar.md) .

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>Enregistrement et chargement des préférences

Dans une application macOS classique, lorsque l’utilisateur apporte des modifications à l’une des préférences de l’utilisateur de l’application, ces modifications sont enregistrées automatiquement. Le moyen le plus simple de gérer cela dans une application Xamarin. Mac consiste à créer une seule classe pour gérer toutes les préférences de l’utilisateur et à partager l’ensemble du système.

Tout d’abord, ajoutez une nouvelle classe `AppPreferences` au projet et héritez de `NSObject`. Les préférences sont conçues pour utiliser la [liaison de données et le codage clé-valeur](~/mac/app-fundamentals/databinding.md) , ce qui rend le processus de création et de maintenance des formulaires de préférence plus simple. Étant donné que les préférences comporteront un petit nombre de types de données simples, utilisez la `NSUserDefaults` intégrée pour stocker et récupérer des valeurs.

Modifiez le fichier `AppPreferences.cs` et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLanguage")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLanguage", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLanguage");
                SaveInt ("DefaultLanguage", value, true);
                DidChangeValue ("DefaultLanguage");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

Cette classe contient quelques routines d’assistance telles que `SaveInt`, `LoadInt`, `SaveColor`, `LoadColor`, etc. pour faciliter l’utilisation de `NSUserDefaults`. En outre, étant donné que `NSUserDefaults` n’a pas de méthode intégrée pour gérer les `NSColors`, les méthodes `NSColorToHexString` et `NSColorFromHexString` sont utilisées pour convertir les couleurs en chaînes hexadécimales basées sur le Web (`#RRGGBBAA` où `AA` est la transparence alpha) qui peuvent être facilement stockées et récupérées.

Dans le fichier `AppDelegate.cs`, créez une instance de l’objet **AppPreferences** qui sera utilisé à l’ensemble de l’application :

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>Câblage des préférences aux affichages des préférences

Ensuite, connectez la classe de préférence aux éléments d’interface utilisateur dans la fenêtre de préférence et les vues créées ci-dessus. Dans Interface Builder, sélectionnez un contrôleur d’affichage des préférences et basculez vers l' **inspecteur d’identité**, puis créez une classe personnalisée pour le contrôleur : 

[![](dialog-images/prefs12.png "The Identity Inspector")](dialog-images/prefs12.png#lightbox)

Revenez à Visual Studio pour Mac pour synchroniser vos modifications et ouvrez la classe nouvellement créée pour la modifier. Faites en sorte que la classe ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Notez que cette classe a fait deux choses : en premier lieu, il existe une propriété d’assistance `App` pour faciliter l’accès au **AppDelegate** . Deuxièmement, la propriété `Preferences` expose la classe globale **AppPreferences** pour la liaison de données avec les contrôles d’interface utilisateur placés sur cette vue.

Ensuite, double-cliquez sur le fichier de table de montage séquentiel pour le rouvrir dans Interface Builder (et consultez les modifications apportées ci-dessus). Faites glisser les contrôles d’interface utilisateur requis pour générer l’interface des préférences dans la vue. Pour chaque contrôle, basculez vers l' **inspecteur de liaison** et liez-les aux propriétés individuelles de la classe **AppPreference** :

[![](dialog-images/prefs13.png "The Binding Inspector")](dialog-images/prefs13.png#lightbox)

Répétez les étapes ci-dessus pour tous les panneaux (contrôleurs d’affichage) et les propriétés de préférence requises.

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>Application de modifications de préférence à toutes les fenêtres ouvertes

Comme indiqué ci-dessus, dans une application macOS type, lorsque l’utilisateur apporte des modifications aux préférences de l’utilisateur de l’application, ces modifications sont enregistrées automatiquement et appliquées à toutes les fenêtres que l’utilisateur peut avoir ouvertes dans l’application.

La planification et la conception soignées des préférences et Windows de votre application permettront à ce processus de se produire de manière transparente et transparente pour l’utilisateur final, avec un minimum de travail de codage.

Pour toute fenêtre qui consomme les préférences de l’application, ajoutez la propriété d’assistance suivante à son contrôleur d’affichage de contenu pour faciliter l’accès à notre **AppDelegate** :

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

Ensuite, ajoutez une classe pour configurer le contenu ou le comportement en fonction des préférences de l’utilisateur :

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

Vous devez appeler la méthode de configuration lors de la première ouverture de la fenêtre pour vous assurer qu’elle est conforme aux préférences de l’utilisateur :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

Modifiez ensuite le fichier `AppDelegate.cs` et ajoutez la méthode suivante pour appliquer toutes les modifications de préférence à toutes les fenêtres ouvertes :

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

Ajoutez ensuite une classe `PreferenceWindowDelegate` au projet et faites-lui ressembler à ce qui suit :

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWindowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

Cela entraîne l’envoi de toute modification de préférence à toutes les fenêtres ouvertes à la fermeture de la fenêtre de préférences.

Enfin, modifiez le contrôleur de la fenêtre de préférences et ajoutez le délégué créé ci-dessus :

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

Une fois toutes ces modifications en place, si l’utilisateur modifie les préférences de l’application et ferme la fenêtre de préférences, les modifications sont appliquées à toutes les fenêtres ouvertes :

[![](dialog-images/prefs14.png "An example preferences window")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>Boîte de dialogue Ouvrir

La boîte de dialogue Ouvrir offre aux utilisateurs une méthode cohérente pour rechercher et ouvrir un élément dans une application. Pour afficher une boîte de dialogue d’ouverture dans une application Xamarin. Mac, utilisez le code suivant :

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

Dans le code ci-dessus, nous ouvrons une nouvelle fenêtre de document pour afficher le contenu du fichier. Vous devrez remplacer ce code par une fonctionnalité requise par votre application.

Les propriétés suivantes sont disponibles lors de l’utilisation d’un `NSOpenPanel`:

- **CanChooseFiles** : si `true` l’utilisateur peut sélectionner des fichiers.
- **CanChooseDirectories** : si `true` l’utilisateur peut sélectionner des répertoires.
- **AllowsMultipleSelection** : si `true` l’utilisateur peut sélectionner plusieurs fichiers à la fois.
- **ResolveAliases** : si `true` sélectionner et alias, le résout sur le chemin d’accès du fichier d’origine.
- **AllowedFileTypes** : tableau de chaînes contenant les types de fichiers que l’utilisateur peut sélectionner comme extension ou _UTI_. La valeur par défaut est `null`, ce qui permet d’ouvrir n’importe quel fichier.

La méthode `RunModal ()` affiche la boîte de dialogue Ouvrir et permet à l’utilisateur de sélectionner des fichiers ou des répertoires (comme spécifié par les propriétés) et retourne `1` si l’utilisateur clique sur le bouton **ouvrir** .

La boîte de dialogue Ouvrir retourne les fichiers ou répertoires sélectionnés de l’utilisateur sous la forme d’un tableau d’URL dans la propriété `URL`.

Si nous exécutons le programme et que vous sélectionnez l’élément **Ouvrir...** dans le menu **fichier** , les éléments suivants s’affichent : 

[![](dialog-images/dialog03.png "An open dialog box")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>Boîtes de dialogue d’impression et de mise en page

macOS fournit des boîtes de dialogue d’impression et de mise en page standard que votre application peut afficher afin que les utilisateurs puissent bénéficier d’une expérience d’impression cohérente dans chaque application qu’ils utilisent.

Le code suivant affiche la boîte de dialogue d’impression standard :

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

Si nous définissons la propriété `ShowPrintAsSheet` sur `false`, exécutez l’application et affichez la boîte de dialogue Imprimer, les éléments suivants s’affichent :

[![](dialog-images/print01.png "A print dialog box")](dialog-images/print01.png#lightbox)

Si vous affectez à la propriété `ShowPrintAsSheet` la valeur `true`, exécutez l’application et affichez la boîte de dialogue Imprimer. les éléments suivants s’affichent :

[![](dialog-images/print02.png "A print sheet")](dialog-images/print02.png#lightbox)

Le code suivant affiche la boîte de dialogue mise en page :

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

Si nous définissons la propriété `ShowPrintAsSheet` sur `false`, exécutez l’application et affichez la boîte de dialogue page d’impression. les éléments suivants s’affichent :

[![](dialog-images/print03.png "A page setup dialog")](dialog-images/print03.png#lightbox)

Si vous affectez à la propriété `ShowPrintAsSheet` la valeur `true`, exécutez l’application et affichez la boîte de dialogue page d’impression. les éléments suivants s’affichent :

[![](dialog-images/print04.png "A page setup sheet")](dialog-images/print04.png#lightbox)

Pour plus d’informations sur l’utilisation des boîtes de dialogue d’impression et de mise en page, consultez la documentation [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092) et [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080) d’Apple.

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>Boîte de dialogue Enregistrer

La boîte de dialogue Enregistrer permet aux utilisateurs d’enregistrer un élément dans une application de manière cohérente.

Le code suivant affiche la boîte de dialogue d’enregistrement standard :

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

La propriété `AllowedFileTypes` est un tableau de chaînes contenant les types de fichiers que l’utilisateur peut sélectionner pour enregistrer le fichier. Le type de fichier peut être spécifié en tant qu’extension ou _UTI_. La valeur par défaut est `null`, ce qui permet d’utiliser n’importe quel type de fichier.

Si nous définissons la propriété `ShowSaveAsSheet` sur `false`, exécutez l’application et sélectionnez **Enregistrer sous...** dans le menu **fichier** , les éléments suivants s’affichent :

[![](dialog-images/save01.png "A save dialog box")](dialog-images/save01.png#lightbox)

L’utilisateur peut développer la boîte de dialogue :

[![](dialog-images/save02.png "An expanded save dialog box")](dialog-images/save02.png#lightbox)

Si nous définissons la propriété `ShowSaveAsSheet` sur `true`, exécutez l’application et sélectionnez **Enregistrer sous...** dans le menu **fichier** , les éléments suivants s’affichent :

[![](dialog-images/save03.png "A save sheet")](dialog-images/save03.png#lightbox)

L’utilisateur peut développer la boîte de dialogue :

[![](dialog-images/save04.png "An expanded save sheet")](dialog-images/save04.png#lightbox)

Pour plus d’informations sur l’utilisation de la boîte de dialogue Enregistrer, consultez la documentation [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098) d’Apple.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des fenêtres modales, des feuilles et des boîtes de dialogue système standard dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations des fenêtres modales, des feuilles et des boîtes de dialogue, comment créer et gérer des fenêtres modales et des feuilles dans le Interface Builder de Xcode et comment utiliser des fenêtres C# , des feuilles et des boîtes de dialogue modales dans le code.

## <a name="related-links"></a>Liens associés

- [MacWindows (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Menus](~/mac/user-interface/menu.md)
- [Fenêtres](~/mac/user-interface/window.md)
- [Barres d’outils](~/mac/user-interface/toolbar.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [Présentation des feuilles](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [Présentation de l’impression](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
