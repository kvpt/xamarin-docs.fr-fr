---
title: Menus dans Xamarin. Mac
description: Cet article traite de l’utilisation des menus dans une application Xamarin. Mac. Il décrit la création et la gestion des menus et des éléments de menu dans Xcode et Interface Builder et leur utilisation par programmation.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c607e4668e4754edaec50a4c8b24776c7bb5844e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430778"
---
# <a name="menus-in-xamarinmac"></a>Menus dans Xamarin. Mac

_Cet article traite de l’utilisation des menus dans une application Xamarin. Mac. Il décrit la création et la gestion des menus et des éléments de menu dans Xcode et Interface Builder et leur utilisation par programmation._

Lorsque vous travaillez avec C# et .NET dans une application Xamarin. Mac, vous avez accès aux mêmes menus de cacao qu’un développeur travaillant dans objective-C et Xcode. Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser Interface Builder de Xcode pour créer et gérer vos barres de menus, menus et éléments de menu (ou éventuellement les créer directement en code C#).

Les menus font partie intégrante de l’expérience utilisateur d’une application Mac et apparaissent généralement dans les différentes parties de l’interface utilisateur :

- **Barre de menus de l’application** : il s’agit du menu principal qui apparaît en haut de l’écran pour chaque application Mac.
- **Menus contextuels** : ceux-ci s’affichent lorsque l’utilisateur clique avec le bouton droit ou clique sur un élément de contrôle dans une fenêtre.
- **La barre d’État** : il s’agit de la zone située à l’extrême droite de la barre de menus de l’application qui apparaît en haut de l’écran (à gauche de l’horloge de la barre de menus) et s’agrandit à gauche à mesure que des éléments y sont ajoutés.
- **Menu ancrer** : le menu de chaque application dans le Dock qui s’affiche quand l’utilisateur clique avec le bouton droit sur l’icône de l’application ou clique avec le bouton droit sur l’icône de l’application, ou lorsque l’utilisateur clique sur l’icône et maintient le bouton de la souris enfoncé.
- **Boutons contextuels et listes déroulantes** : un bouton contextuel affiche un élément sélectionné et présente la liste des options à sélectionner lorsque l’utilisateur clique dessus. Une liste déroulante est un type de bouton contextuel généralement utilisé pour sélectionner des commandes spécifiques au contexte de la tâche actuelle. Les deux peuvent apparaître n’importe où dans une fenêtre.

[![Un exemple de menu](menu-images/intro01.png "Un exemple de menu")](menu-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des barres de menus, des menus et des éléments de menu de cacao dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans cet article.

Vous pouvez également jeter un coup d’œil sur la section [exposition des classes/méthodes C# à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` `Export` attributs et utilisés pour relier vos classes C# aux objets objective-c et aux éléments d’interface utilisateur.

## <a name="the-applications-menu-bar"></a>Barre de menus de l’application 

Contrairement aux applications qui s’exécutent sur le système d’exploitation Windows où toutes les fenêtres peuvent être attachées à leur propre barre de menus, chaque application s’exécutant sur macOS possède une barre de menus unique qui s’exécute le long de la partie supérieure de l’écran utilisé pour chaque fenêtre de cette application :

[![Barre de menus](menu-images/appmenu01.png "Barre de menus")](menu-images/appmenu01-large.png#lightbox)

Les éléments de cette barre de menus sont activés ou désactivés en fonction du contexte ou de l’état actuel de l’application et de son interface utilisateur à un moment donné. Par exemple : si l’utilisateur sélectionne un champ de texte, les éléments du menu **Edition** sont activés, tels que **copier** et **couper**.

D’après Apple et, par défaut, toutes les applications macOS possèdent un ensemble standard de menus et d’éléments de menu qui s’affichent dans la barre de menus de l’application :

- **Menu Apple** : ce menu donne accès aux éléments système accessibles à l’utilisateur à tout moment, quelle que soit l’application en cours d’exécution. Ces éléments ne peuvent pas être modifiés par le développeur.
- **Menu** de l’application : ce menu affiche le nom de l’application en gras et aide l’utilisateur à identifier l’application en cours d’exécution. Il contient des éléments qui s’appliquent à l’application dans son ensemble et non à un document ou processus donné, comme la sortie de l’application.
- **Menu fichier** -éléments utilisés pour créer, ouvrir ou enregistrer des documents avec lesquels votre application fonctionne. Si votre application n’est pas basée sur des documents, ce menu peut être renommé ou supprimé.
- **Menu Edition** -contient les commandes telles que **couper**, **copier**et **coller** , qui sont utilisées pour modifier ou modifier des éléments dans l’interface utilisateur de l’application.
- **Menu Format** : si l’application fonctionne avec du texte, ce menu contient des commandes pour ajuster la mise en forme de ce texte.
- **Menu Affichage** -contient les commandes qui affectent la façon dont le contenu est affiché (affiché) dans l’interface utilisateur de l’application.
- **Menus spécifiques** à l’application : il s’agit de tous les menus spécifiques à votre application (par exemple, un menu de signets pour un navigateur Web). Ils doivent apparaître entre les menus **affichage** et **fenêtre** sur la barre.
- **Menu fenêtre** : contient des commandes permettant d’utiliser des fenêtres dans votre application, ainsi qu’une liste des fenêtres ouvertes actuelles.
- **Menu aide** : Si votre application fournit de l’aide à l’écran, le menu aide doit être le menu le plus à droite de la barre. 

Pour plus d’informations sur la barre de menus de l’application et les menus et les éléments de menu standard, consultez [les instructions relatives à l’interface humaine](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)d’Apple.

### <a name="the-default-application-menu-bar"></a>Barre de menus de l’application par défaut

Chaque fois que vous créez un nouveau projet Xamarin. Mac, vous recevez automatiquement une barre de menus d’application par défaut standard qui contient les éléments typiques qu’une application macOS devrait normalement avoir (comme indiqué dans la section ci-dessus). La barre de menus par défaut de votre application est définie dans le fichier **main. Storyboard** (avec le reste de l’interface utilisateur de votre application) sous le projet dans le **panneau solutions**:  

![Sélectionner le Storyboard principal](menu-images/appmenu02.png "Sélectionner le Storyboard principal")

Double-cliquez sur le fichier **main. Storyboard** pour l’ouvrir en vue de le modifier dans le Interface Builder de Xcode et l’interface de l’éditeur de menus s’affiche :

[![Modification de l’interface utilisateur dans Xcode](menu-images/defaultbar01.png "Modification de l’interface utilisateur dans Xcode")](menu-images/defaultbar01-large.png#lightbox)

À partir de là, nous pouvons cliquer sur des éléments tels que l’option **ouvrir** dans le menu **fichier** et modifier ou ajuster ses propriétés dans l' **inspecteur d’attributs**:

[![Modification des attributs d’un menu](menu-images/defaultbar02.png "Modification des attributs d’un menu")](menu-images/defaultbar02-large.png#lightbox)

Nous allons ajouter, modifier et supprimer des menus et des éléments plus loin dans cet article. Pour l’instant, nous voulons juste voir quels menus et éléments de menu sont disponibles par défaut et comment ils ont été automatiquement exposés au code via un ensemble d’actions et de prises prédéfinies (pour plus d’informations, consultez la documentation sur les [prises et les actions](~/mac/get-started/hello-mac.md#outlets-and-actions) ).

Par exemple, si vous cliquez sur l' **inspecteur de connexion** pour l’élément de menu **ouvrir** , nous voyons qu’il est automatiquement relié à l' `openDocument:` action : 

[![Affichage de l’action attachée](menu-images/defaultbar03.png "Affichage de l’action attachée")](menu-images/defaultbar03-large.png#lightbox)

Si vous sélectionnez le **premier répondeur** dans la **hiérarchie d’interface** et faites défiler l' **inspecteur de connexion**vers le haut, vous verrez la définition de l' `openDocument:` action à laquelle l’élément de menu **ouvert** est attaché (avec plusieurs autres actions par défaut pour l’application qui sont et qui ne sont pas automatiquement associées à des contrôles) :

[![Affichage de toutes les actions attachées](menu-images/defaultbar04.png "Affichage de toutes les actions attachées")](menu-images/defaultbar04-large.png#lightbox) 

Pourquoi est-ce important ? Dans la section suivante, vous verrez comment ces actions définies automatiquement fonctionnent avec d’autres éléments de l’interface utilisateur de cacao pour activer et désactiver automatiquement des éléments de menu, ainsi que pour fournir des fonctionnalités intégrées pour les éléments.

Plus tard, nous allons utiliser ces actions intégrées pour activer et désactiver des éléments à partir du code et fournir nos propres fonctionnalités lorsqu’elles sont sélectionnées.

<a name="Built-In_Menu_Functionality"></a>

### <a name="built-in-menu-functionality"></a>Fonctionnalités de menu intégrées

Si vous avez exécuté une application Xamarin. Mac nouvellement créée avant d’ajouter des éléments d’interface utilisateur ou du code, vous remarquerez que certains éléments sont automatiquement câblés et activés pour vous (avec des fonctionnalités entièrement intégrées), telles que l’élément **quitter** dans le menu de l' **application** :

![Élément de menu activé](menu-images/appmenu03.png "Élément de menu activé")

Contrairement à d’autres éléments de menu, tels que **couper**, **copier**et **coller** , ne sont pas :

![Éléments de menu désactivés](menu-images/appmenu04.png "Éléments de menu désactivés")

Nous allons arrêter l’application et double-cliquer sur le fichier **main. Storyboard** dans le **panneau solutions** pour l’ouvrir et le modifier dans le Interface Builder de Xcode. Ensuite, faites glisser un **affichage de texte** de la **bibliothèque** sur le contrôleur d’affichage de la fenêtre dans l’éditeur d' **interface**:

[![Sélection d’un affichage de texte à partir de la bibliothèque](menu-images/appmenu05.png "Sélection d’un affichage de texte à partir de la bibliothèque")](menu-images/appmenu05-large.png#lightbox)

Dans l' **éditeur de contrainte** , épinglez l’affichage du texte aux bords de la fenêtre et définissez-le à l’endroit où il s’agrandit et diminue avec la fenêtre en cliquant sur les quatre poutres rouge en haut de l’éditeur et en cliquant sur le bouton **Ajouter 4 contraintes** :

[![Modification des contraintes](menu-images/appmenu06.png "Modification des contraintes")](menu-images/appmenu06-large.png#lightbox)

Enregistrez les modifications apportées à la conception de l’interface utilisateur et revenez à la Visual Studio pour Mac pour synchroniser les modifications avec votre projet Xamarin. Mac. À présent, démarrez l’application, tapez du texte dans l’affichage de texte, sélectionnez-le, puis ouvrez le menu **modifier** :

![Les éléments de menu sont activés/désactivés automatiquement](menu-images/appmenu07.png "Les éléments de menu sont activés/désactivés automatiquement")

Notez que les éléments **couper**, **copier**et **coller** sont automatiquement activés et entièrement fonctionnels, tout cela sans écrire une seule ligne de code. 

Comment cela se fait-il ? N’oubliez pas les actions prédéfinies intégrées qui sont associées aux éléments de menu par défaut (comme indiqué ci-dessus), la plupart des éléments de l’interface utilisateur de cacao qui font partie de macOS ont des crochets intégrés à des actions spécifiques (par exemple `copy:` ,). Ainsi, lorsqu’ils sont ajoutés à une fenêtre, actifs et sélectionnés, l’élément de menu correspondant ou les éléments associés à cette action sont automatiquement activés. Si l’utilisateur sélectionne cet élément de menu, les fonctionnalités intégrées à l’élément d’interface utilisateur sont appelées et exécutées, sans intervention du développeur.

### <a name="enabling-and-disabling-menus-and-items"></a>Activation et désactivation des menus et des éléments

Par défaut, chaque fois qu’un événement utilisateur se produit, `NSMenu` active et désactive automatiquement chaque menu visible et élément de menu en fonction du contexte de l’application. Il existe trois façons d’activer/de désactiver un élément :

- **Activation automatique du menu** : un élément de menu est activé si `NSMenu` peut trouver un objet approprié qui répond à l’action à laquelle l’élément est connecté. Par exemple, l’affichage de texte ci-dessus avait un raccordement intégré à l' `copy:` action.
- **Actions personnalisées et validateMenuItem :** pour tout élément de menu lié à une [action personnalisée de fenêtre ou de contrôleur d’affichage](#Working-with-Custom-Window-Actions), vous pouvez ajouter l' `validateMenuItem:` action et activer ou désactiver manuellement les éléments de menu.
- **Activation manuelle du menu** : vous définissez manuellement la `Enabled` propriété de chaque `NSMenuItem` élément pour activer ou désactiver chaque élément d’un menu.

Pour choisir un système, définissez la `AutoEnablesItems` propriété d’un `NSMenu` . `true` est automatique (comportement par défaut) et `false` est manuel. 

> [!IMPORTANT]
> Si vous choisissez d’utiliser l’activation manuelle du menu, aucun des éléments de menu, même ceux contrôlés par les classes AppKit comme `NSTextView` , n’est mis à jour automatiquement. Vous êtes responsable de l’activation et de la désactivation de tous les éléments à la main dans le code.

#### <a name="using-validatemenuitem"></a>Utilisation de validateMenuItem

Comme indiqué ci-dessus, pour tout élément de menu lié à une [action personnalisée de fenêtre ou de contrôleur d’affichage](#Working-with-Custom-Window-Actions), vous pouvez ajouter l' `validateMenuItem:` action et activer ou désactiver manuellement les éléments de menu.

Dans l’exemple suivant, la `Tag` propriété est utilisée pour décider du type d’élément de menu qui sera activé/désactivé par l' `validateMenuItem:` action en fonction de l’état du texte sélectionné dans un `NSTextView` . La `Tag` propriété a été définie dans Interface Builder pour chaque élément de menu :

![Définition de la propriété Tag](menu-images/validate01.png "Définition de la propriété Tag")

Et le code suivant ajouté au contrôleur d’affichage :

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

Quand ce code est exécuté et qu’aucun texte n’est sélectionné dans le `NSTextView` , les deux éléments de menu de retour à la ligne sont désactivés (même s’ils sont connectés à des actions sur le contrôleur d’affichage) :

![Montrer les éléments désactivés](menu-images/validate02.png "Montrer les éléments désactivés")

Si une section de texte est sélectionnée et que le menu est rouvert, les deux éléments de menu de retour à la ligne sont disponibles :

![Montrer les éléments activés](menu-images/validate03.png "Montrer les éléments activés")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>Activation et réponse aux éléments de menu dans le code

Comme nous l’avons vu précédemment, en ajoutant simplement des éléments d’interface utilisateur de cacao spécifiques à la conception de l’interface utilisateur (par exemple, un champ de texte), plusieurs des éléments de menu par défaut sont activés et fonctionnent automatiquement, sans qu’il soit nécessaire d’écrire du code. Voyons ensuite comment ajouter notre propre code C# à notre projet Xamarin. Mac pour activer un élément de menu et fournir des fonctionnalités lorsque l’utilisateur le sélectionne.

Par exemple, imaginons que l’utilisateur soit en mesure d’utiliser l’élément **ouvrir** dans le menu **fichier** pour sélectionner un dossier. Étant donné que nous voulons qu’il s’agit d’une fonction au niveau de l’application sans être limité à une fenêtre ou un élément d’interface utilisateur, nous allons ajouter le code pour le gérer à notre délégué d’application.

Dans la **panneau solutions**, double-cliquez sur le fichier pour l' `AppDelegate.CS` ouvrir et le modifier :

![Sélection du délégué d’application](menu-images/appmenu08.png "Sélection du délégué d’application")

Ajoutez le code suivant sous la `DidFinishLaunching` méthode :

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

Nous allons exécuter l’application maintenant et ouvrir le menu **fichier** : 

![Menu fichier](menu-images/appmenu09.png "Menu fichier")

Notez que l’élément de menu **ouvrir** est maintenant activé. Si vous sélectionnez cette option, la boîte de dialogue Ouvrir s’affiche :

![Boîte de dialogue ouverte](menu-images/appmenu10.png "Boîte de dialogue ouverte")

Si nous cliquons sur le bouton **ouvrir** , notre message d’alerte s’affiche :

![Un exemple de message de boîte de dialogue](menu-images/appmenu11.png "Un exemple de message de boîte de dialogue")

Ici, la ligne de clé était `[Export ("openDocument:")]` , elle indique `NSMenu` que notre **AppDelegate** a une méthode `void OpenDialog (NSObject sender)` qui répond à l' `openDocument:` action. Si vous vous en souvenez, l’élément de menu **ouvrir** est automatiquement câblé à cette action par défaut dans Interface Builder :

[![Affichage des actions attachées](menu-images/defaultbar03.png "Affichage des actions attachées")](menu-images/defaultbar03-large.png#lightbox)

Voyons ensuite comment créer le menu, les éléments de menu et les actions et y répondre dans le code.

### <a name="working-with-the-open-recent-menu"></a>Utilisation du menu ouvert récent

Par défaut, le menu **fichier** contient un élément **récent ouvert** qui effectue le suivi des derniers fichiers ouverts par l’utilisateur avec votre application. Si vous créez une `NSDocument` application Xamarin. Mac basée sur, ce menu sera géré automatiquement. Pour tout autre type d’application Xamarin. Mac, vous êtes responsable de la gestion et de la réponse de cet élément de menu manuellement.

Pour gérer manuellement le menu **ouvert récent** , vous devez d’abord l’informer qu’un nouveau fichier a été ouvert ou enregistré à l’aide des éléments suivants :

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Même si votre application n’utilise pas `NSDocuments` , vous utilisez toujours le `NSDocumentController` pour gérer le menu **ouvert récent** en envoyant un `NSUrl` avec l’emplacement du fichier à la `NoteNewRecentDocumentURL` méthode de `SharedDocumentController` .

Ensuite, vous devez remplacer la `OpenFile` méthode du délégué d’application pour ouvrir n’importe quel fichier que l’utilisateur sélectionne dans le menu **ouvrir récemment** . Par exemple :

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

Retourne `true` si le fichier peut être ouvert, sinon retourne `false` et un avertissement intégré s’affiche pour l’utilisateur indiquant que le fichier n’a pas pu être ouvert.

Étant donné que le nom de fichier et le chemin d’accès retournés depuis le menu **ouvert récent** , peuvent inclure un espace, nous devons insérer correctement ce caractère dans une séquence d’échappement avant de créer un `NSUrl` ou nous obtenons une erreur. Nous faisons cela avec le code suivant :

```csharp
filename = filename.Replace (" ", "%20");
```

Enfin, nous créons un `NSUrl` qui pointe vers le fichier et utilisons une méthode d’assistance dans le délégué d’application pour ouvrir une nouvelle fenêtre et y charger le fichier :

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Pour tout rassembler, jetons un coup d’œil à un exemple d’implémentation dans un fichier **AppDelegate.cs** :

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

En fonction des exigences de votre application, il est possible que vous ne souhaitiez pas que l’utilisateur ouvre le même fichier dans plusieurs fenêtres en même temps. Dans notre exemple d’application, si l’utilisateur choisit un fichier qui est déjà ouvert (à partir de l’ouverture ou d’une ouverture **récente** ) **.** éléments de menu), la fenêtre qui contient le fichier est placée au premier plan.

Pour ce faire, nous avons utilisé le code suivant dans notre méthode d’assistance :

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

Nous avons conçu notre `ViewController` classe pour stocker le chemin d’accès au fichier dans sa `Path` propriété. Ensuite, nous parcourons toutes les fenêtres actuellement ouvertes dans l’application. Si le fichier est déjà ouvert dans l’une des fenêtres, il est placé devant toutes les autres fenêtres à l’aide de :

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Si aucune correspondance n’est trouvée, une nouvelle fenêtre s’ouvre avec le fichier chargé et le fichier est noté dans le menu **ouvrir récemment** :

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions"></a>

### <a name="working-with-custom-window-actions"></a>Utilisation d’actions de fenêtre personnalisée

À l’instar des premières actions de **répondeur** intégrées qui sont pré-câblées aux éléments de menu standard, vous pouvez créer de nouvelles actions personnalisées et les relier aux éléments de menu dans Interface Builder.

Tout d’abord, définissez une action personnalisée sur l’un des contrôleurs de fenêtre de votre application. Par exemple :

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Ensuite, double-cliquez sur le fichier Storyboard de l’application dans le **panneau solutions** pour l’ouvrir et le modifier dans le Interface Builder de Xcode. Sélectionnez le **premier répondeur** sous la **scène**de l’application, puis basculez vers l' **inspecteur d’attributs**:

![L’inspecteur d’attributs](menu-images/action01.png "L’inspecteur d’attributs")

Cliquez sur le **+** bouton en bas de l' **inspecteur d’attributs** pour ajouter une nouvelle action personnalisée :

![Ajout d’une nouvelle action](menu-images/action02.png "Ajout d’une nouvelle action")

Donnez-lui le même nom que l’action personnalisée que vous avez créée sur votre contrôleur de Windows :

![Modification du nom de l’action](menu-images/action03.png "Modification du nom de l’action")

Cliquez avec le contrôle sur un élément de menu et faites-le glisser vers le **premier répondeur** sous la scène de l' **application**. Dans la liste déroulante, sélectionnez la nouvelle action que vous venez de créer ( `defineKeyword:` dans cet exemple) :

![Attachement d’une action](menu-images/action04.png "Attachement d’une action")

Enregistrez les modifications apportées à la table de montage séquentiel et revenez à Visual Studio pour Mac pour synchroniser les modifications. Si vous exécutez l’application, l’élément de menu auquel vous avez connecté l’action personnalisée sera automatiquement activé/désactivé (en fonction de la fenêtre avec l’action en cours d’ouverture) et la sélection de l’élément de menu déclenchera l’action suivante :

[![Test de la nouvelle action](menu-images/action05.png "Test de la nouvelle action")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus"></a>

### <a name="adding-editing-and-deleting-menus"></a>Ajout, modification et suppression de menus

Comme nous l’avons vu dans les sections précédentes, une application Xamarin. Mac est fournie avec un nombre prédéfini de menus et d’éléments de menu par défaut que des contrôles d’interface utilisateur spécifiques activeront et répondront automatiquement. Nous avons également vu comment ajouter du code à notre application qui activera et répondra également à ces éléments par défaut.

Dans cette section, nous allons supprimer les éléments de menu dont nous n’avons pas besoin, en réorganisant les menus et en ajoutant de nouveaux menus, éléments de menu et actions.

Double-cliquez sur le fichier **main. Storyboard** dans le **panneau solutions** pour l’ouvrir et le modifier :

[![Modification de l’interface utilisateur dans Xcode](menu-images/maint01.png "Modification de l’interface utilisateur dans Xcode")](menu-images/maint01-large.png#lightbox)

Pour notre application Xamarin. Mac spécifique, nous n’allons pas utiliser le menu **affichage** par défaut pour que nous puissions le supprimer. Dans la **hiérarchie** de l’interface, sélectionnez l’élément de menu **affichage** qui fait partie de la barre de menus principale :

![Sélection de l’élément de menu Affichage](menu-images/maint02.png "Sélection de l’élément de menu Affichage")

Appuyez sur la touche SUPPR ou retour arrière pour supprimer le menu. Ensuite, nous n’allons pas utiliser tous les éléments du menu **format** et nous voulons déplacer les éléments que nous allons utiliser dans les sous-menus. Dans la **hiérarchie** de l’interface, sélectionnez les éléments de menu suivants :

![Mise en surbrillance de plusieurs éléments](menu-images/maint03.png "Mise en surbrillance de plusieurs éléments")

Faites glisser les éléments sous le **menu** parent du sous-menu où ils sont actuellement :

[![Glissement d’éléments de menu dans le menu parent](menu-images/maint04.png "Glissement d’éléments de menu dans le menu parent")](menu-images/maint04-large.png#lightbox)

Votre menu doit maintenant ressembler à ce qui suit :

[![Les éléments dans le nouvel emplacement](menu-images/maint05.png "Les éléments dans le nouvel emplacement")](menu-images/maint05-large.png#lightbox)

Nous allons ensuite faire glisser le sous-menu de **texte** depuis le menu **format** et le placer dans la barre de menus principale entre les menus **format** et **fenêtre** :

[![Menu texte](menu-images/maint06.png "Menu texte")](menu-images/maint06-large.png#lightbox)

Revenons dans le menu **format** et supprimons l’élément de sous-menu **police** . Ensuite, sélectionnez le menu **format** et renommez-le « police » :

[![Menu police](menu-images/maint07.png "Menu police")](menu-images/maint07-large.png#lightbox)

Créons ensuite un menu personnalisé d’expressions prédéfinies qui seront ajoutées automatiquement au texte de l’affichage de texte lorsqu’elles sont sélectionnées. Dans la zone de recherche située en bas du type de l' **inspecteur de bibliothèque** , dans le menu. Cela facilitera la recherche et l’utilisation de tous les éléments de l’interface utilisateur du menu :

![L’inspecteur de bibliothèque](menu-images/maint08.png "L’inspecteur de bibliothèque")

À présent, procédez comme suit pour créer notre menu :

1. Faites glisser un **élément de menu** de l’inspecteur de **bibliothèque** vers la barre de menus située entre le **texte** et les menus de la **fenêtre** : 

    ![Sélection d’un nouvel élément de menu dans la bibliothèque](menu-images/maint10.png "Sélection d’un nouvel élément de menu dans la bibliothèque")
2. Renommez l’élément « expressions » : 

    [![Définition du nom de menu](menu-images/maint09.png "Définition du nom de menu")](menu-images/maint09-large.png#lightbox)
3. Ensuite, faites glisser un **menu** à partir de l' **inspecteur de bibliothèque**: 

    ![Sélection d’un menu dans la bibliothèque](menu-images/maint11.png "Sélection d’un menu dans la bibliothèque")
4. Déposez ensuite le **menu** sur le nouvel **élément de menu** que nous venons de créer et remplacez son nom par « expressions » : 

    [![Modification du nom de menu](menu-images/maint12.png "Modification du nom de menu")](menu-images/maint12-large.png#lightbox)
5. Nous allons maintenant renommer les trois **éléments de menu** par défaut « Address », « date » et « Greeting » : 

    [![Menu expressions](menu-images/maint13.png "Menu expressions")](menu-images/maint13-large.png#lightbox)
6. Nous allons ajouter un quatrième **élément de menu** en faisant glisser un **élément de menu** à partir de l’inspecteur de **bibliothèque** et en l’appelant « signature » : 

    [![Modification du nom d’un élément de menu](menu-images/maint14.png "Modification du nom d’un élément de menu")](menu-images/maint14-large.png#lightbox)
7. Enregistrez les modifications apportées à la barre de menus.

Créons maintenant un ensemble d’actions personnalisées afin que les nouveaux éléments de menu soient exposés au code C#. Dans Xcode, basculez vers la vue **Assistant** :

[![Création des actions requises](menu-images/maint15.png "Création des actions requises")](menu-images/maint15-large.png#lightbox)

Procédez comme suit :

1. Ctrl-faites glisser à partir de l’élément de menu **adresse** vers le fichier **AppDelegate. h** .
2. Changez le type de **connexion** en **action**: 

    [![Sélection du type d’action](menu-images/maint17.png "Sélection du type d’action")](menu-images/maint17-large.png#lightbox)
3. Entrez un **nom** de « phraseAddress » et appuyez sur le bouton de **connexion** pour créer la nouvelle action : 

    [![Configuration de l'action](menu-images/maint18.png "Configuration de l'action")](menu-images/maint18-large.png#lightbox)
4. Répétez les étapes ci-dessus pour les éléments de menu **Date**, **salutation**et **signature** : 

    [![Actions terminées](menu-images/maint19.png "Actions terminées")](menu-images/maint19-large.png#lightbox)
5. Enregistrez les modifications apportées à la barre de menus.

Ensuite, nous devons créer une prise pour notre affichage de texte afin de pouvoir ajuster son contenu à partir du code. Sélectionnez le fichier **ViewController. h** dans l' **éditeur** de l’Assistant et créez une nouvelle sortie appelée `documentText` :

[![Création d’une sortie](menu-images/maint20.png "Création d’une sortie")](menu-images/maint20-large.png#lightbox)

Revenez à Visual Studio pour Mac pour synchroniser les modifications à partir de Xcode. Modifiez ensuite le fichier **ViewController.cs** et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

Cela expose le texte de notre vue de texte en dehors de la `ViewController` classe et informe le délégué de l’application lorsque la fenêtre gagne ou perd le focus. Modifiez maintenant le fichier **AppDelegate.cs** et faites-le ressembler à ce qui suit :

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

Ici, nous avons créé `AppDelegate` une classe partielle pour pouvoir utiliser les actions et les prises de sortie que nous avons définies dans Interface Builder. Nous exposerons également un `textEditor` pour suivre la fenêtre qui est actuellement active.

Les méthodes suivantes sont utilisées pour gérer nos menus et éléments de menu personnalisés :

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

Maintenant, si nous exécutons notre application, tous les éléments du menu **expression** seront actifs et ajouterons l’expression « donnera » à l’affichage de texte lorsqu’ils sont sélectionnés :

![Exemple de l’application en cours d’exécution](menu-images/maint21.png "Exemple de l’application en cours d’exécution")

Maintenant que nous avons les bases de l’utilisation de la barre de menus de l’application, examinons la création d’un menu contextuel personnalisé.

### <a name="creating-menus-from-code"></a>Création de menus à partir du code

En plus de créer des menus et des éléments de menu avec Interface Builder de Xcode, il peut arriver qu’une application Xamarin. Mac doive créer, modifier ou supprimer un menu, un sous-menu ou un élément de menu à partir du code.

Dans l’exemple suivant, une classe est créée pour contenir les informations sur les éléments de menu et les sous-menus qui seront créés dynamiquement à la volée :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>Ajouter des menus et des éléments

Une fois cette classe définie, la routine suivante analyse une collection d' `LanguageFormatCommand` objets et génère de manière récursive de nouveaux menus et éléments de menu en les ajoutant au bas du menu existant (créé dans Interface Builder) qui a été transmis :

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

Pour tout `LanguageFormatCommand` objet dont la propriété est vide `Title` , cette routine crée un **élément de menu séparateur** (une ligne grise fine) entre les sections de menu :

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Si un titre est fourni, un nouvel élément de menu portant ce titre est créé :

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Si l' `LanguageFormatCommand` objet contient des `LanguageFormatCommand` objets enfants, un sous-menu est créé et la `AssembleMenu` méthode est appelée de manière récursive pour générer ce menu :

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Pour tout nouvel élément de menu qui n’a pas de sous-menus, du code est ajouté pour gérer l’élément de menu sélectionné par l’utilisateur :

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Test de la création du menu

Avec tout le code ci-dessus en place, si la collection d' `LanguageFormatCommand` objets suivante a été créée :

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

Et cette collection est passée à la `AssembleMenu` fonction (avec le menu **format** défini en tant que base), les menus dynamiques et les éléments de menu suivants sont créés :

![Nouveaux éléments de menu dans l’application en cours d’exécution](menu-images/dynamic01.png "Nouveaux éléments de menu dans l’application en cours d’exécution")

#### <a name="removing-menus-and-items"></a>Supprimer des menus et des éléments

Si vous devez supprimer un menu ou un élément de menu de l’interface utilisateur de l’application, vous pouvez utiliser la `RemoveItemAt` méthode de la `NSMenu` classe en lui donnant simplement l’index de base zéro de l’élément à supprimer.

Par exemple, pour supprimer les menus et les éléments de menu créés par la routine ci-dessus, vous pouvez utiliser le code suivant :

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

Dans le cas du code ci-dessus, les quatre premiers éléments de menu sont créés dans les Interface Builder et les objets de Xcode disponibles dans l’application, de sorte qu’ils ne sont pas supprimés dynamiquement.

<a name="Contextual_Menus"></a>

## <a name="contextual-menus"></a>Menus contextuels

Les menus contextuels s’affichent quand l’utilisateur clique avec le bouton droit ou clique sur un élément de contrôle dans une fenêtre. Par défaut, plusieurs des éléments d’interface utilisateur intégrés à macOS ont déjà des menus contextuels qui leur sont associés (tels que l’affichage de texte). Toutefois, il peut arriver que vous souhaitiez créer des menus contextuels personnalisés pour un élément d’interface utilisateur que nous avons ajouté à une fenêtre.

Nous allons modifier notre fichier **main. Storyboard** dans Xcode et ajouter une fenêtre de **fenêtre** à notre conception, définir sa **classe** sur « NSPanel » dans l' **inspecteur d’identité**, ajouter un nouvel élément **Assistant** au menu **fenêtre** , puis l’attacher à la nouvelle fenêtre à l’aide d’un **segue afficher**:

[![Définition du type segue](menu-images/context01.png "Définition du type segue")](menu-images/context01-large.png#lightbox)

Procédez comme suit :

1. Faites glisser une **étiquette** de l' **inspecteur de bibliothèque** vers la fenêtre **du panneau** et définissez son texte sur « propriété » : 

    [![Modification de la valeur de l’étiquette](menu-images/context03.png "Modification de la valeur de l’étiquette")](menu-images/context03-large.png#lightbox)
2. Ensuite, faites glisser un **menu** de l' **inspecteur de bibliothèque** vers le contrôleur d’affichage dans la hiérarchie de vue et renommez les trois éléments de menu par défaut **document**, **texte** et **police**:

    [![Éléments de menu requis](menu-images/context02.png "Éléments de menu requis")](menu-images/context02-large.png#lightbox)
3. À présent, faites glisser à partir de l' **étiquette de propriété** sur le **menu**:

    [![Glissement pour créer un segue](menu-images/context04.png "Glissement pour créer un segue")](menu-images/context04-large.png#lightbox)
4. Dans la boîte de dialogue contextuelle, sélectionnez **menu**: 

    ![Définition du type segue](menu-images/context05.png "Définition du type segue")
5. À partir de l' **inspecteur d’identité**, définissez la classe du contrôleur d’affichage sur « PanelViewController » : 

    [![Définition de la classe segue](menu-images/context10.png "Définition de la classe segue")](menu-images/context10-large.png#lightbox)
6. Revenez à Visual Studio pour Mac à synchroniser, puis revenez à Interface Builder.
7. Basculez vers l’éditeur de l' **Assistant** et sélectionnez le fichier **PanelViewController. h** .
8. Créez une action pour l’élément de menu **document** appelé `propertyDocument` : 

    [![Configuration de l'action](menu-images/context06.png "Configuration de l'action")](menu-images/context06-large.png#lightbox)
9. Répétez la création des actions pour les autres éléments de menu : 

    [![Actions requises](menu-images/context07.png "Actions requises")](menu-images/context07-large.png#lightbox)
10. Enfin, créez une sortie pour l' **étiquette de propriété** appelée `propertyLabel` : 

    [![Configuration de la sortie](menu-images/context08.png "Configuration de la sortie")](menu-images/context08-large.png#lightbox)
11. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Modifiez le fichier **PanelViewController.cs** et ajoutez le code suivant :

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

Maintenant, si nous exécutons l’application et que vous cliquez avec le bouton droit sur l’étiquette de la propriété dans le volet, notre menu contextuel personnalisé s’affiche. Si vous sélectionnez l’élément et dans le menu, la valeur de l’étiquette est modifiée :

![Menu contextuel en cours d’exécution](menu-images/context09.png "Menu contextuel en cours d’exécution")

Voyons ensuite comment créer des menus de barre d’État.

## <a name="status-bar-menus"></a>Menus de la barre d’État

Les menus de la barre d’état affichent une collection d’éléments de menu État qui fournissent une interaction avec l’utilisateur ou des commentaires à l’utilisateur, tel qu’un menu ou une image reflétant l’état d’une application. Le menu de la barre d’état d’une application est activé et actif même si l’application s’exécute en arrière-plan. La barre d’État à l’ensemble du système se trouve à droite de la barre de menus de l’application et est la seule barre d’État actuellement disponible dans macOS.

Nous allons modifier notre fichier **AppDelegate.cs** et faire en sorte que la méthode ressemble à `DidFinishLaunching` ce qui suit :

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nous donne accès à la barre d’État du système. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` crée un nouvel élément de la barre d’État. À partir de là, nous créons un menu et un certain nombre d’éléments de menu et attachons le menu à l’élément de barre d’État que nous venons de créer. 

Si nous exécutons l’application, le nouvel élément de la barre d’État s’affiche. La sélection d’un élément dans le menu modifie le texte dans l’affichage de texte : 

![Menu de la barre d’État en cours d’exécution](menu-images/statusbar01.png "Menu de la barre d’État en cours d’exécution")

Voyons ensuite comment créer des éléments de menu d’ancrage personnalisés.

## <a name="custom-dock-menus"></a>Menus d’ancrage personnalisés

Le menu ancre s’affiche pour votre application Mac quand l’utilisateur clique avec le bouton droit sur l’icône de l’application dans l’ancre :

![Menu d’ancrage personnalisé](menu-images/dock01.png "Menu d’ancrage personnalisé")

Nous allons créer un menu d’ancrage personnalisé pour notre application en procédant comme suit :

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur le projet de l’application, puis sélectionnez **Ajouter**  >  **un nouveau fichier...** Dans la boîte de dialogue nouveau fichier, sélectionnez **Xamarin. Mac**  >  **définition d’interface vide**, utilisez « dockmenu » comme **nom** et cliquez sur le bouton **nouveau** pour créer le nouveau fichier **dockmenu. XIB** :

    ![Ajout d’une définition d’interface vide](menu-images/dock02.png "Ajout d’une définition d’interface vide")
2. Dans la **panneau solutions**, double-cliquez sur le fichier **dockmenu. XIB** pour l’ouvrir et le modifier dans Xcode. Créez un nouveau **menu** avec les éléments suivants : **adresse**, **Date**, **salutation**et **signature** 

    [![Disposition de l’interface utilisateur](menu-images/dock03.png "Disposition de l’interface utilisateur")](menu-images/dock03-large.png#lightbox)
3. Ensuite, nous allons connecter nos nouveaux éléments de menu à nos actions existantes que nous avons créées pour notre menu personnalisé dans la section [Ajouter, modifier et supprimer des menus](#Adding,_Editing_and_Deleting_Menus) ci-dessus. Basculez vers l' **inspecteur de connexion** et sélectionnez le **premier répondeur** dans la hiérarchie de l' **interface**. Faites défiler l’action et recherchez l' `phraseAddress:` action. Faites glisser une ligne du cercle sur cette action vers l’élément de menu **adresse** :

    [![Glissement pour associer une action](menu-images/dock04.png "Glissement pour associer une action")](menu-images/dock04-large.png#lightbox)
4. Répétez cette opération pour tous les autres éléments de menu qui les joignent à leurs actions correspondantes : 

    [![Actions requises](menu-images/dock05.png "Actions requises")](menu-images/dock05-large.png#lightbox)
5. Ensuite, sélectionnez l' **application** dans la **hiérarchie**de l’interface. Dans l' **inspecteur de connexion**, faites glisser une ligne du cercle sur la `dockMenu` sortie vers le menu que nous venons de créer :

    [![Faire glisser le câble vers le haut de la sortie](menu-images/dock06.png "Faire glisser le câble vers le haut de la sortie")](menu-images/dock06-large.png#lightbox)
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.
7. Double-cliquez sur le fichier **info. plist** pour l’ouvrir et le modifier : 

    [![Modification du fichier info. plist](menu-images/dock07.png "Modification du fichier Info.plist")](menu-images/dock07-large.png#lightbox)
8. Cliquez sur l’onglet **source** en bas de l’écran : 

    [![Sélection de la vue source](menu-images/dock08.png "Sélection de la vue source")](menu-images/dock08-large.png#lightbox)
9. Cliquez sur **Ajouter une nouvelle entrée**, sur le bouton vert plus, définissez le nom de la propriété sur « AppleDockMenu » et la valeur sur « dockmenu » (le nom de notre nouveau fichier. XIB sans l’extension) : 

    [![Ajout de l’élément DockMenu](menu-images/dock09.png "Ajout de l’élément DockMenu")](menu-images/dock09-large.png#lightbox)

Maintenant, si nous exécutons notre application et que vous cliquez avec le bouton droit sur son icône dans le Dock, les nouveaux éléments de menu s’affichent :

![Exemple du menu d’ancrage en cours d’exécution](menu-images/dock10.png "Exemple du menu d’ancrage en cours d’exécution")

Si vous sélectionnez l’un des éléments personnalisés dans le menu, le texte de notre vue de texte sera modifié.

<a name="Pop-up_Menus_and_Pull-Down_Lists"></a>

## <a name="pop-up-button-and-pull-down-lists"></a>Liste déroulante et bouton déroulant

Un bouton contextuel affiche un élément sélectionné et présente une liste d’options à sélectionner lorsque l’utilisateur clique dessus. Une liste déroulante est un type de bouton contextuel généralement utilisé pour sélectionner des commandes spécifiques au contexte de la tâche actuelle. Les deux peuvent apparaître n’importe où dans une fenêtre.

Nous allons créer un bouton contextuel personnalisé pour notre application en procédant comme suit :

1. Modifiez le **fichier main. Storyboard** dans Xcode et faites glisser un **bouton contextuel** de l' **inspecteur de bibliothèque** vers la fenêtre **du panneau** que nous avons créée dans la section [menus contextuels](#Contextual_Menus) : 

    [![Ajout d’un bouton contextuel](menu-images/popup01.png "Ajout d’un bouton contextuel")](menu-images/popup01-large.png#lightbox)
2. Ajoutez un nouvel élément de menu et définissez les titres des éléments dans la fenêtre contextuelle sur : **adresse**, **Date**, **salutation**et **signature** 

    [![Configuration des éléments de menu](menu-images/popup02.png "Configuration des éléments de menu")](menu-images/popup02-large.png#lightbox)
3. Ensuite, nous allons connecter nos nouveaux éléments de menu aux actions existantes que nous avons créées pour notre menu personnalisé dans la section [Ajouter, modifier et supprimer des menus](#Adding,_Editing_and_Deleting_Menus) ci-dessus. Basculez vers l' **inspecteur de connexion** et sélectionnez le **premier répondeur** dans la hiérarchie de l' **interface**. Faites défiler l’action et recherchez l' `phraseAddress:` action. Faites glisser une ligne du cercle sur cette action vers l’élément de menu **adresse** : 

    [![Glissement pour associer une action](menu-images/popup03.png "Glissement pour associer une action")](menu-images/popup03-large.png#lightbox)
4. Répétez cette opération pour tous les autres éléments de menu qui les joignent à leurs actions correspondantes : 

    [![Toutes les actions requises](menu-images/popup04.png "Toutes les actions requises")](menu-images/popup04-large.png#lightbox)
5. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Maintenant, si nous exécutons l’application et que vous sélectionnez un élément dans la fenêtre contextuelle, le texte de notre vue de texte sera modifié :

![Exemple de la fenêtre contextuelle en cours d’exécution](menu-images/popup05.png "Exemple de la fenêtre contextuelle en cours d’exécution")

Vous pouvez créer et utiliser des listes déroulantes exactement de la même façon que les boutons contextuels. Au lieu de vous attacher à une action existante, vous pouvez créer vos propres actions personnalisées comme nous l’avons fait pour notre menu contextuel dans la section [menus contextuels](#Contextual_Menus) .

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des menus et des éléments de menu dans une application Xamarin. Mac. Tout d’abord, nous avons examiné la barre de menus de l’application, puis nous avons examiné la création de menus contextuels. ensuite, nous avons examiné les menus de barre d’État et les menus d’ancrage personnalisés. Enfin, nous avons abordé les menus contextuels et les listes déroulantes.

## <a name="related-links"></a>Liens associés

- [MacMenus (exemple)](/samples/xamarin/mac-samples/macmenus)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Instructions relatives à l’interface humaine-menus](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Présentation des menus de l’application et des listes contextuelles](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)