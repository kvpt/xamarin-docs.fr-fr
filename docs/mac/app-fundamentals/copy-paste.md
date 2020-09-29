---
title: Copier et coller dans Xamarin. Mac
description: Cet article traite de l’utilisation de la copie pour fournir des copies et des collages dans une application Xamarin. Mac. Il montre comment utiliser des types de données standard qui peuvent être partagés entre plusieurs applications et comment prendre en charge des données personnalisées dans une application donnée.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: e85a273c75fd09672c6c75738adcdc576705af09
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433987"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copier et coller dans Xamarin. Mac

_Cet article traite de l’utilisation de la copie pour fournir des copies et des collages dans une application Xamarin. Mac. Il montre comment utiliser des types de données standard qui peuvent être partagés entre plusieurs applications et comment prendre en charge des données personnalisées dans une application donnée._

## <a name="overview"></a>Vue d’ensemble

Lorsque vous travaillez avec C# et .NET dans une application Xamarin. Mac, vous avez accès au même support de collage (copier-coller) que celui d’un développeur qui travaille en Objective-C.

Dans cet article, nous allons aborder les deux principales façons d’utiliser la copie dans une application Xamarin. Mac :

1. **Types de données standard** : étant donné que les opérations de collage sont généralement effectuées entre deux applications non liées, aucune application ne connaît les types de données pris en charge par l’autre. Pour optimiser le potentiel de partage, la copie peut contenir plusieurs représentations d’un élément donné (à l’aide d’un ensemble standard de types de données communs), ce qui permet à l’application consommatrice de choisir la version qui convient le mieux à ses besoins.
2. **Données personnalisées** : pour prendre en charge la copie et le collage de données complexes dans votre Xamarin. Mac, vous pouvez définir un type de données personnalisé qui sera géré par le collage. Par exemple, une application de dessin vectoriel qui permet à l’utilisateur de copier et coller des formes complexes composées de plusieurs types et points de données.

[![Exemple d’application en cours d’exécution](copy-paste-images/intro01.png "Exemple d’application en cours d’exécution")](copy-paste-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation de la copie dans une application Xamarin. Mac pour prendre en charge les opérations de copie et de collage. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans cet article.

Vous pouvez également jeter un coup d’œil sur la section [exposition des classes/méthodes C# à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` `Export` attributs et utilisés pour relier vos classes C# aux objets objective-c et aux éléments d’interface utilisateur.

## <a name="getting-started-with-the-pasteboard"></a>Prise en main du collage

Le montage présente un mécanisme standardisé pour l’échange de données dans une application donnée ou entre des applications. L’utilisation courante d’un collage dans une application Xamarin. Mac consiste à gérer les opérations de copie et de collage, mais un certain nombre d’autres opérations sont également prises en charge (par exemple, glisser & déposer et Services d’application).

Pour vous familiariser rapidement, nous allons commencer par une introduction simple et pratique de l’utilisation de pasteboards dans une application Xamarin. Mac. Plus tard, nous fournirons une explication détaillée du fonctionnement de la copie et des méthodes utilisées.

Pour cet exemple, nous allons créer une application simple basée sur un document qui gère une fenêtre contenant une vue d’image. L’utilisateur peut copier et coller des images entre les documents de l’application et vers ou à partir d’autres applications ou de plusieurs fenêtres à l’intérieur de la même application.

### <a name="creating-the-xamarin-project"></a>Création du projet Xamarin

Tout d’abord, nous allons créer une nouvelle application Xamarin. Mac basée sur un document, à laquelle nous allons ajouter la prise en charge de la copie et du collage.

Effectuez les actions suivantes :

1. Démarrez Visual Studio pour Mac, puis cliquez sur le lien **nouveau projet** ....
2. Sélectionnez **Mac**  >  **App**  >  l'**application de cacao**de l’application Mac, puis cliquez sur le bouton **suivant** : 

    [![Création d’un projet d’application de cacao](copy-paste-images/sample01.png "Création d’un projet d’application de cacao")](copy-paste-images/sample01-large.png#lightbox)
3. Entrez `MacCopyPaste` pour le **nom du projet** et conservez tout le reste comme valeur par défaut. Cliquez sur Suivant : 

    [![Définition du nom du projet](copy-paste-images/sample01a.png "Définition du nom du projet")](copy-paste-images/sample01a-large.png#lightbox)

4. Cliquez sur le bouton **créer** : 

    [![Confirmation des nouveaux paramètres du projet](copy-paste-images/sample02.png "Confirmation des nouveaux paramètres du projet")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Ajouter un NSDocument

Ensuite, nous ajouterons une `NSDocument` classe personnalisée qui servira de stockage en arrière-plan pour l’interface utilisateur de l’application. Il contient une vue d’image unique et vous indique comment copier une image de la vue dans le tableau de montage par défaut et comment prendre une image à partir du tableau de montage par défaut et l’afficher dans la vue d’image.

Cliquez avec le bouton droit sur le projet Xamarin. Mac dans le **panneau solutions** puis sélectionnez **Ajouter**  >  **un nouveau fichier..**:

![Ajout d’un NSDocument au projet](copy-paste-images/sample03.png "Ajout d’un NSDocument au projet")

Entrez `ImageDocument` comme **Nom**, puis cliquez sur le bouton **Nouveau**. Modifiez la classe **ImageDocument.cs** et faites en sorte qu’elle ressemble à ce qui suit :

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

Jetons un coup d’œil à certains codes en détail ci-dessous.

Le code suivant fournit une propriété permettant de tester l’existence de données image sur la copie par défaut, si une image est disponible, `true` est retournée `false` :

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

Le code suivant copie une image de la vue d’image attachée dans le tableau de montage par défaut :

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

Et le code suivant colle une image du tableau de montage par défaut et l’affiche dans la vue d’image attachée (si la copie contient une image valide) :

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

Ce document étant en place, nous allons créer l’interface utilisateur de l’application Xamarin. Mac.

### <a name="building-the-user-interface"></a>Création de l’interface utilisateur

Double-cliquez sur le fichier **main. Storyboard** pour l’ouvrir dans Xcode. Ensuite, ajoutez une barre d’outils et une image bien et configurez-les comme suit :

[![Modification de la barre d’outils](copy-paste-images/sample04.png "Modification de la barre d’outils")](copy-paste-images/sample04-large.png#lightbox)

Ajoutez un **élément de barre d’outils** copier et coller l’image sur le côté gauche de la barre d’outils. Nous allons les utiliser comme raccourcis pour copier et coller à partir du menu Edition. Ensuite, ajoutez quatre **éléments de barre d’outils d’image** à droite de la barre d’outils. Nous les utiliserons pour alimenter correctement l’image avec certaines images par défaut.

Pour plus d’informations sur l’utilisation des barres d’outils, consultez la documentation de notre [barre d’outils](~/mac/user-interface/toolbar.md) .

Ensuite, nous allons exposer les prises et actions suivantes pour les éléments de la barre d’outils et la bonne image :

[![Création des sorties et des actions](copy-paste-images/sample05.png "Création des sorties et des actions")](copy-paste-images/sample05-large.png#lightbox)

Pour plus d’informations sur l’utilisation des sorties et des actions, consultez la section [sorties et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) de notre documentation [Hello, Mac](~/mac/get-started/hello-mac.md) .

### <a name="enabling-the-user-interface"></a>Activation de l’interface utilisateur

Avec notre interface utilisateur créée dans Xcode et notre élément d’interface exposé via des prises et des actions, nous devons ajouter le code pour activer l’interface utilisateur. Double-cliquez sur le fichier **ImageWindow.cs** dans le **panneau solutions** et faites-le ressembler à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

Examinons ce code en détail ci-dessous.

Tout d’abord, nous exposons une instance de la `ImageDocument` classe que nous avons créée ci-dessus :

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

À l’aide de `Export` , `WillChangeValue` et `DidChangeValue` , nous avons configuré la `Document` propriété pour autoriser le codage clé-valeur et la liaison de données dans Xcode.

Nous exposerons également l’image à partir de l’image que nous avons ajoutée à notre interface utilisateur dans Xcode avec la propriété suivante :

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

Quand la fenêtre principale est chargée et affichée, nous créons une instance de notre `ImageDocument` classe et nous attachons bien l’image de l’interface utilisateur avec le code suivant :

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

Enfin, en réponse à l’utilisateur qui clique sur les éléments de barre d’outils copier et coller, nous appelons l’instance de la `ImageDocument` classe pour effectuer le travail réel :

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Activation des menus fichier et modifier

La dernière chose à faire est d’activer le **nouvel** élément de menu dans le menu **fichier** (pour créer des instances de la fenêtre principale) et d’activer les éléments de menu **couper**, **copier** et **coller** du menu **Edition** .

Pour activer le **nouvel** élément de menu, modifiez le fichier **AppDelegate.cs** et ajoutez le code suivant :

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Pour plus d’informations, consultez la section [utilisation de plusieurs fenêtres](~/mac/user-interface/window.md) de la documentation de [Windows](~/mac/user-interface/window.md) .

Pour activer les éléments de menu **couper**, **copier** et **coller** , modifiez le fichier **AppDelegate.cs** et ajoutez le code suivant :

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

Pour chaque élément de menu, nous obtenons la fenêtre de clé actuelle, la plus en haut, et nous la transmettons à notre `ImageWindow` classe :

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

À partir de là, nous appelons l' `ImageDocument` instance de classe de cette fenêtre pour gérer les actions de copie et de collage. Par exemple : 

```csharp
window.Document.CopyImage (sender);
```

Nous souhaitons que les éléments de menu **couper**, **copier** et **coller** soient accessibles s’il y a des données d’image sur le collage par défaut ou dans le puits de l’image actuelle de la fenêtre active.

Nous allons ajouter un fichier **EditMenuDelegate.cs** au projet Xamarin. Mac et le faire ressembler à ce qui suit :

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

Là encore, nous obtenons la fenêtre actuelle en haut et utilisons son `ImageDocument` instance de classe pour voir si les données d’image requises existent. Ensuite, nous utilisons la `MenuWillHighlightItem` méthode pour activer ou désactiver chaque élément en fonction de cet État.

Modifiez le fichier **AppDelegate.cs** et faites en sorte que la méthode ressemble à `DidFinishLaunching` ce qui suit :

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

Tout d’abord, nous désactivons l’activation automatique et la désactivation des éléments de menu dans le menu Edition. Ensuite, nous attachons une instance de la `EditMenuDelegate` classe que nous avons créée ci-dessus.

Pour plus d’informations, consultez notre documentation sur les [menus](~/mac/user-interface/menu.md) .

### <a name="testing-the-app"></a>Test de l’application

Tout en étant en place, nous sommes prêts à tester l’application. Générez et exécutez l’application et l’interface principale s’affiche :

![Exécution de l'application](copy-paste-images/run01.png "Exécution de l'application")

Si vous ouvrez le menu Edition, Notez que **couper**, **copier** et **coller** sont désactivés, car il n’y a pas d’image dans l’image ou dans la barre de montage par défaut :

![Ouverture du menu Edition](copy-paste-images/run02.png "Ouverture du menu Edition")

Si vous ajoutez une image à la bonne image et rouvrez le menu Edition, les éléments sont désormais activés :

![L’activation des éléments du menu Edition est activée](copy-paste-images/run03.png "L’activation des éléments du menu Edition est activée")

Si vous copiez l’image et sélectionnez **nouveau** dans le menu fichier, vous pouvez coller cette image dans la nouvelle fenêtre :

![Collage d’une image dans une nouvelle fenêtre](copy-paste-images/run04.png "Collage d’une image dans une nouvelle fenêtre")

Dans les sections suivantes, nous examinerons en détail l’utilisation de la copie dans une application Xamarin. Mac.

## <a name="about-the-pasteboard"></a>À propos du montage

Dans macOS (anciennement OS X), le montage ( `NSPasteboard` ) assure la prise en charge de plusieurs processus serveur, tels que copier & coller, faire glisser & déposer et services d’application. Dans les sections suivantes, nous examinerons de plus près plusieurs concepts de collage clés.

### <a name="what-is-a-pasteboard"></a>Qu’est-ce qu’un collage ?

La `NSPasteboard` classe fournit un mécanisme standardisé pour l’échange d’informations entre des applications ou au sein d’une application donnée. La fonction principale d’une opération de collage consiste à gérer les opérations de copie et de collage :

1. Lorsque l’utilisateur sélectionne un élément dans une application et utilise l’élément de menu **couper** ou **copier** , une ou plusieurs représentations de l’élément sélectionné sont placées sur le tableau de montage.
2. Lorsque l’utilisateur utilise l’élément de menu **coller** (dans la même application ou une autre), la version des données qu’il peut gérer est copiée à partir du montage et ajoutée à l’application.

Les opérations de collage, de glisser-déplacer, de glisser-déplacer et de services d’application sont moins évidentes :

- Lorsque l’utilisateur lance une opération glisser, les données de glissement sont copiées dans le montage. Si l’opération glisser se termine par une suppression dans une autre application, cette application copie les données à partir du montage.
- Pour les services de traduction, les données à traduire sont copiées dans le collage par l’application à l’origine de la demande. Le service d’application, récupère les données à partir du collage, effectue la traduction, puis colle à nouveau les données sur le montage.

Dans leur forme la plus simple, les pasteboards sont utilisés pour déplacer des données à l’intérieur d’une application donnée ou entre des applications et qui existent dans une zone mémoire globale spéciale en dehors du processus de l’application. Bien que les concepts du pasteboards soient facilement compréhensibles, il y a plusieurs détails plus complexes qui doivent être pris en compte. Celles-ci sont traitées en détail ci-dessous.

### <a name="named-pasteboards"></a>Nommé pasteboards

Un tableau de montage peut être public ou privé et peut être utilisé à diverses fins dans une application ou entre plusieurs applications. macOS fournit plusieurs pasteboards standard, chacun avec une utilisation spécifique et bien définie :

- `NSGeneralPboard` : Collage par défaut pour les opérations **couper**, **copier** et **coller** .
- `NSRulerPboard` -Prend en charge les opérations **couper**, **copier** et **coller** sur les **règles**.
- `NSFontPboard` -Prend en charge les opérations **couper**, **copier** et **coller** sur les `NSFont` objets.
- `NSFindPboard` -Prend en charge des panneaux de recherche spécifiques à l’application qui peuvent partager le texte recherché.
- `NSDragPboard` -Prend en charge les opérations **glisser-déplacer &** .

Dans la plupart des cas, vous utiliserez l’un des pasteboards définis par le système. Toutefois, il peut y avoir des situations qui nécessitent la création de vos propres pasteboards. Dans ces situations, vous pouvez utiliser la `FromName (string name)` méthode de la `NSPasteboard` classe pour créer une copie personnalisée avec le nom donné.

Si vous le souhaitez, vous pouvez appeler la `CreateWithUniqueName` méthode de la `NSPasteboard` classe pour créer un montage nommé de façon unique.

### <a name="pasteboard-items"></a>Éléments de collage

Chaque élément de données qu’une application écrit sur un tableau de montage est considéré comme un _élément de collage_ et une copie peut contenir plusieurs éléments en même temps. De cette façon, une application peut écrire plusieurs versions des données copiées dans une copie (par exemple, du texte brut et du texte mis en forme) et la récupération de l’application peut uniquement lire les données qu’elle peut traiter (par exemple, le texte brut uniquement).

### <a name="data-representations-and-uniform-type-identifiers"></a>Représentations de données et identificateurs de type uniformes

Les opérations de collage sont généralement effectuées entre deux applications (ou plus) qui n’ont aucune connaissance des autres ou les types de données que chaque peut gérer. Comme indiqué dans la section précédente, pour optimiser le potentiel de partage d’informations, une copie peut contenir plusieurs représentations des données copiées et collées.

Chaque représentation est identifiée à l’aide d’un identificateur de type uniforme (UTI), qui n’est rien de plus qu’une simple chaîne qui identifie de façon unique le type de date présenté (pour plus d’informations, consultez la documentation [vue d’ensemble des identificateurs de type uniformes](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) d’Apple). 

Si vous créez un type de données personnalisé (par exemple, un objet de dessin dans une application de dessin de vecteurs), vous pouvez créer votre propre UTI pour l’identifier de manière unique dans les opérations de copie et de collage.

Quand une application se prépare à coller des données copiées à partir d’un tableau de montage, elle doit trouver la représentation qui correspond le mieux à ses capacités (le cas échéant). En général, il s’agit du type le plus riche disponible (par exemple, le texte mis en forme pour une application de traitement de texte), qui remonte aux formes les plus simples disponibles si nécessaire (texte brut pour un simple éditeur de texte).

<a name="Promised_Data"></a>

### <a name="promised-data"></a>Données promis

En général, vous devez fournir autant de représentations des données copiées que possible pour optimiser le partage entre les applications. Toutefois, en raison de contraintes de temps ou de mémoire, il peut être difficile d’écrire en fait chaque type de données dans la copie.

Dans ce cas, vous pouvez placer la première représentation des données sur le montage et l’application réceptrice peut demander une autre représentation, qui peut être générée à la volée juste avant l’opération de collage.

Lorsque vous placez l’élément initial dans le tableau de montage, vous spécifiez qu’une ou plusieurs des autres représentations disponibles sont fournies par un objet conforme à l' `NSPasteboardItemDataProvider` interface. Ces objets fourniront des représentations supplémentaires à la demande, comme demandé par l’application réceptrice.

### <a name="change-count"></a>Nombre de modifications

Chaque copieur conserve un _nombre de modifications_ qui s’incrémente chaque fois qu’un nouveau propriétaire est déclaré. Une application peut déterminer si le contenu de la copie a changé depuis la dernière fois qu’il l’a examiné en vérifiant la valeur du nombre de modifications.

Utilisez les `ChangeCount` `ClearContents` méthodes et de la `NSPasteboard` classe pour modifier le nombre de modifications d’un montage donné.

## <a name="copying-data-to-a-pasteboard"></a>Copie de données dans un montage

Pour effectuer une opération de copie, vous devez tout d’abord accéder à une copie, effacer tout contenu existant et écrire autant de représentations des données que nécessaire dans le cadre du collage.

Par exemple :

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

En règle générale, vous allez simplement écrire dans la copie générale, comme nous l’avons fait dans l’exemple ci-dessus. Tout objet que vous envoyez à la `WriteObjects` méthode *doit* être conforme à l' `INSPasteboardWriting` interface. Plusieurs classes intégrées (telles que,,, `NSString` , `NSImage` `NSURL` `NSColor` `NSAttributedString` et `NSPasteboardItem` ) sont automatiquement conformes à cette interface.

Si vous écrivez une classe de données personnalisée dans le tableau de montage, elle doit se conformer à l' `INSPasteboardWriting` interface ou être incluse dans un wrapper dans une instance de la `NSPasteboardItem` classe (consultez la section [types de données personnalisés](#Custom_Data_Types) ci-dessous).

## <a name="reading-data-from-a-pasteboard"></a>Lecture de données à partir d’un montage

Comme indiqué ci-dessus, pour optimiser le potentiel de partage de données entre les applications, plusieurs représentations des données copiées peuvent être écrites dans le montage. Il revient à l’application réceptrice de sélectionner la version la plus riche possible pour ses fonctionnalités (le cas échéant).

### <a name="simple-paste-operation"></a>Opération de collage simple

Vous lisez des données à partir du montage à l’aide de la `ReadObjectsForClasses` méthode. Deux paramètres sont nécessaires :

1. Tableau de `NSObject` types de classes basés que vous souhaitez lire à partir du tableau de montage. Vous devez tout d’abord le classer avec le type de données le plus souhaité, avec tous les types restants dans la préférence décroissante.
2. Dictionnaire contenant des contraintes supplémentaires (par exemple, limitation à des types de contenu d’URL spécifiques) ou un dictionnaire vide si aucune autre contrainte n’est requise.

La méthode retourne un tableau d’éléments qui répondent aux critères que nous avons transmis et qui, par conséquent, contient au plus le même nombre de types de données demandés. Il est également possible qu’aucun des types demandés ne soit présent et qu’un tableau vide soit retourné.

Par exemple, le code suivant vérifie si un `NSImage` existe dans le tableau de collage général et l’affiche dans une image bien, le cas échéant :

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>Demande de plusieurs types de données

En fonction du type d’application Xamarin. Mac créé, il peut être en mesure de gérer plusieurs représentations des données collées. Dans ce cas, il existe deux scénarios pour la récupération des données à partir du montage :

1. Effectuez un appel unique à la `ReadObjectsForClasses` méthode et fournissez un tableau de toutes les représentations que vous souhaitez (dans l’ordre par défaut).
2. Effectuer plusieurs appels à la `ReadObjectsForClasses` méthode qui demande un tableau de types différent à chaque fois.

Pour plus d’informations sur la récupération de données à partir d’un montage, consultez la section **opération de collage simple** ci-dessus.

### <a name="checking-for-existing-data-types"></a>Recherche de types de données existants

Il peut arriver que vous souhaitiez vérifier si une opération de collage contient une représentation de données donnée sans réellement lire les données à partir de la copie (comme l’activation de l’élément de menu **coller** uniquement lorsque des données valides existent).

Appelez la `CanReadObjectForClasses` méthode de la vue de la copie pour voir si elle contient un type donné.

Par exemple, le code suivant détermine si la vue de collage générale contient une `NSImage` instance :

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>Lecture des URL à partir du montage

En fonction de la fonction d’une application Xamarin. Mac donnée, il peut être nécessaire de lire des URL à partir d’un fichier de copie, mais uniquement si elles répondent à un ensemble de critères donné (par exemple, en pointant vers des fichiers ou des URL d’un type de données spécifique). Dans ce cas, vous pouvez spécifier des critères de recherche supplémentaires à l’aide du deuxième paramètre des `CanReadObjectForClasses` `ReadObjectsForClasses` méthodes ou.

<a name="Custom_Data_Types"></a>

## <a name="custom-data-types"></a>Types de données personnalisés

Il peut arriver que vous ayez besoin d’enregistrer vos propres types personnalisés dans le fichier de copie à partir d’une application Xamarin. Mac. Par exemple, une application de dessin vectoriel qui permet à l’utilisateur de copier et coller des objets de dessin.

Dans ce cas, vous devez concevoir votre classe de données personnalisée de sorte qu’elle hérite de `NSObject` et qu’elle soit conforme à quelques interfaces ( `INSCoding` `INSPasteboardWriting` et `INSPasteboardReading` ). Si vous le souhaitez, vous pouvez utiliser un `NSPasteboardItem` pour encapsuler les données à copier ou à coller.

Ces deux options sont décrites en détail ci-dessous.

### <a name="using-a-custom-class"></a>Utilisation d’une classe personnalisée

Dans cette section, nous allons développer l’exemple d’application simple que nous avons créé au début de ce document et ajouter une classe personnalisée pour suivre les informations sur l’image que nous copions et collez entre les fenêtres.

Ajoutez une nouvelle classe au projet et appelez-la **imageinfo.cs**. Modifiez le fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

Dans les sections suivantes, nous allons examiner en détail cette classe.

#### <a name="inheritance-and-interfaces"></a>Héritage et interfaces

Pour qu’une classe de données personnalisée puisse être écrite ou lue à partir d’une copie, elle doit se conformer aux `INSPastebaordWriting` `INSPasteboardReading` interfaces et. En outre, il doit hériter de `NSObject` et se conformer à l' `INSCoding` interface :

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

La classe doit également être exposée à Objective-C à l’aide de la `Register` directive et elle doit exposer toutes les propriétés ou méthodes requises à l’aide de `Export` . Par exemple :

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Nous exposons les deux champs de données que cette classe contiendra : le nom de l’image et son type (jpg, png, etc.). 

Pour plus d’informations, consultez la section [exposition des classes/méthodes C# à Objective-C](~/mac/internals/how-it-works.md) de la documentation [interne Xamarin. Mac](~/mac/internals/how-it-works.md) . elle explique `Register` les `Export` attributs et utilisés pour relier vos classes C# aux objets objective-c et aux éléments d’interface utilisateur.

#### <a name="constructors"></a>Constructeurs

Deux constructeurs (correctement exposés à Objective-C) seront requis pour notre classe de données personnalisée afin qu’il puisse être lu à partir d’un montage :

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

Tout d’abord, nous exposons le constructeur _vide_ sous la méthode objective-C par défaut de `init` .

Ensuite, nous exposons un `NSCoding` constructeur conforme qui sera utilisé pour créer une nouvelle instance de l’objet à partir du collage lors du collage sous le nom exporté de `initWithCoder` .

Ce constructeur prend un `NSCoder` (tel qu’il est créé par un lorsqu’il est `NSKeyedArchiver` écrit dans le tableau de montage), extrait les données de paires clé/valeur et les enregistre dans les champs de propriété de la classe de données.

#### <a name="writing-to-the-pasteboard"></a>Écriture dans le montage

En se conformant à l' `INSPasteboardWriting` interface, nous devons exposer deux méthodes, et éventuellement une troisième méthode, afin que la classe puisse être écrite dans le collage.

Tout d’abord, nous devons indiquer au collage la représentation des types de données dans laquelle la classe personnalisée peut être écrite :

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Chaque représentation est identifiée à l’aide d’un identificateur de type uniforme (UTI), qui n’est rien de plus qu’une simple chaîne qui identifie de façon unique le type de données présenté (pour plus d’informations, consultez la documentation [vue d’ensemble des identificateurs de type uniformes](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) d’Apple).

Pour notre format personnalisé, nous créons notre propre UTI : « com. xamarin. image-info » (Notez que est en notation inverse comme un identificateur d’application). Notre classe est également en charge de l’écriture d’une chaîne standard dans le collage ( `public.text` ). 

Ensuite, nous devons créer l’objet dans le format demandé qui est réellement écrit dans le montage :

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

Pour le `public.text` type, nous revenons un objet mis en forme simple `NSString` . Pour le `com.xamarin.image-info` type personnalisé, nous utilisons un `NSKeyedArchiver` et l' `NSCoder` interface pour encoder la classe de données personnalisée en une archive paire clé/valeur. Nous devons implémenter la méthode suivante pour gérer l’encodage :

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Les paires clé/valeur individuelles sont écrites dans l’encodeur et seront décodées à l’aide du deuxième constructeur que nous avons ajouté précédemment.

Si vous le souhaitez, nous pouvons inclure la méthode suivante pour définir des options lors de l’écriture de données dans le montage :

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Actuellement, seule l' `WritingPromised` option est disponible et doit être utilisée lorsqu’un type donné est uniquement promis et qu’il n’est pas réellement écrit dans le collage. Pour plus d’informations, consultez la section [données promises](#Promised_Data) ci-dessus.

Une fois ces méthodes en place, le code suivant peut être utilisé pour écrire notre classe personnalisée sur le montage :

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Lire à partir du montage

En se conformant à l' `INSPasteboardReading` interface, nous devons exposer trois méthodes afin que la classe de données personnalisée puisse être lue à partir du montage.

Tout d’abord, nous devons indiquer au collage la représentation des types de données que la classe personnalisée peut lire à partir du presse-papiers :

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Là encore, ils sont définis comme des UTI simples et sont les mêmes types que ceux définis dans la section **écriture dans la section de collage** ci-dessus.

Ensuite, nous devons indiquer au collage la _manière dont_ chacun des types UTI sera lu à l’aide de la méthode suivante :

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

Pour le `com.xamarin.image-info` type, nous indiquons au collage de décoder la paire clé/valeur que nous avons créée avec le lors de l' `NSKeyedArchiver` écriture de la classe dans la copie en appelant le `initWithCoder:` constructeur que nous avons ajouté à la classe.

Enfin, nous devons ajouter la méthode suivante pour lire les autres représentations de données UTI à partir de la copie :

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

Une fois que toutes ces méthodes sont en place, la classe de données personnalisée peut être lue à partir du module de copie à l’aide du code suivant :

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>Utilisation d’un NSPasteboardItem

Il peut arriver que vous deviez écrire des éléments personnalisés dans le cadre de la copie qui ne justifient pas la création d’une classe personnalisée ou que vous souhaitiez fournir des données dans un format commun, uniquement si nécessaire. Dans ce cas, vous pouvez utiliser un `NSPasteboardItem` .

Un `NSPasteboardItem` fournit un contrôle affiné sur les données écrites dans le panneau de collage et est conçu pour un accès temporaire. il doit être supprimé après avoir été écrit dans le panneau de collage.

#### <a name="writing-data"></a>Écriture de données

Pour écrire vos données personnalisées dans un `NSPasteboardItem` , vous devez fournir un personnalisé `NSPasteboardItemDataProvider` . Ajoutez une nouvelle classe au projet et appelez-la **ImageInfoDataProvider.cs**. Modifiez le fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

Comme nous l’avons fait avec la classe de données personnalisée, nous avons besoin d’utiliser les `Register` `Export` directives et pour l’exposer à Objective-C. La classe doit hériter de `NSPasteboardItemDataProvider` et doit implémenter les `FinishedWithDataProvider` `ProvideDataForType` méthodes et.

Utilisez la `ProvideDataForType` méthode pour fournir les données qui seront encapsulées dans le `NSPasteboardItem` comme suit :

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

Dans ce cas, nous stockons deux informations sur notre image (Name et ImageType) et nous les écrivons dans une simple chaîne ( `public.text` ).

Tapez écrire les données dans le montage, utilisez le code suivant :

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>Lecture des données en cours

Pour relire les données à partir du montage, utilisez le code suivant :

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation du collage dans une application Xamarin. Mac pour prendre en charge les opérations de copie et de collage. Tout d’abord, il a introduit un exemple simple pour vous familiariser avec les opérations pasteboards standard. Ensuite, il a vu un aperçu détaillé du montage et de la lecture et de l’écriture de données à partir de celui-ci. Enfin, il a étudié l’utilisation d’un type de données personnalisé pour prendre en charge la copie et le collage de types de données complexes dans une application.

## <a name="related-links"></a>Liens associés

- [MacCopyPaste (exemple)](/samples/xamarin/mac-samples/maccopypaste)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guide de programmation de la copie](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)