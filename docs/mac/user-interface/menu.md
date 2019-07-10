---
title: Menus dans Xamarin.Mac
description: Cet article aborde l’utilisation des menus dans une application Xamarin.Mac. Il décrit la création et gestion des menus et éléments de menu dans Xcode et Interface Builder et leur utilisation par programmation.
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 1f2b0c9ee46634ad7395fadfb770dd2da796ada2
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675153"
---
# <a name="menus-in-xamarinmac"></a>Menus dans Xamarin.Mac

_Cet article aborde l’utilisation des menus dans une application Xamarin.Mac. Il décrit la création et gestion des menus et éléments de menu dans Xcode et Interface Builder et leur utilisation par programmation._

Lorsque vous travaillez avec c# et .NET dans une application Xamarin.Mac, vous avez accès aux mêmes menus Cocoa qui effectue un développeur travaillant dans Objective-C et Xcode. Comme Xamarin.Mac s’intègre directement à Xcode, vous pouvez utiliser Interface Builder de Xcode pour créer et mettre à jour les barres de menus, des menus et des éléments de menu (ou éventuellement pour les créer directement en code c#).

Les menus sont une partie intégrante de l’expérience utilisateur d’une application Mac et apparaissent généralement dans les différentes parties de l’interface utilisateur :

- **Barre de menus de l’application** -il s’agit du menu principal s’affiche en haut de l’écran pour toutes les applications Mac.
- **Menus contextuels** -ceux-ci s’affichent lorsque l’utilisateur clique ou un élément dans une fenêtre de contrôle-clics.
- **La barre d’état** -c’est la zone à l’extrême droite de la barre de menu d’application qui apparaît en haut de l’écran (à gauche de l’horloge de barre de menu) et se développe vers la gauche, comme les éléments sont ajoutés à ce dernier.
- **Menu d’ancrage** -menu pour chaque application de la station d’accueil qui apparaît lorsque l’utilisateur clique ou contrôle-clique sur l’icône de l’application, ou lorsque l’utilisateur left-clicks l’icône et maintient le bouton de la souris enfoncé.
- **Bouton contextuelle et les listes déroulantes** -un bouton déroulant affiche un élément sélectionné et présente une liste d’options à sélectionner à partir d’un clic par l’utilisateur. Une liste déroulante est un type de bouton déroulant généralement utilisé pour la sélection des commandes spécifiques au contexte de la tâche en cours. Les deux peuvent apparaître n’importe où dans une fenêtre.

[![Un exemple de menu](menu-images/intro01.png "un exemple de menu")](menu-images/intro01-large.png#lightbox)

Dans cet article, nous traiterons les notions de base de l’utilisation des barres de menus Cocoa, des menus et des éléments de menu dans une application Xamarin.Mac. Il est fortement recommandé que vous travaillez via le [Hello, Mac](~/mac/get-started/hello-mac.md) article tout d’abord, en particulier le [Introduction à Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et [Outlets et Actions](~/mac/get-started/hello-mac.md#outlets-and-actions) sections, tel qu’il couvre les principaux concepts et techniques que nous utilisons dans cet article.

Vous pouvez souhaiter de jeter un coup de œil à la [C# exposition de classes / méthodes vers Objective-C](~/mac/internals/how-it-works.md) section de la [éléments internes de Xamarin.Mac](~/mac/internals/how-it-works.md) de document, il explique le `Register` et `Export` attributs utilisé pour AutoEventWireup vos classes c# pour les objets Objective-C et les éléments d’interface utilisateur.

## <a name="the-applications-menu-bar"></a>Barre de menus de l’application 

Contrairement aux applications en cours d’exécution sur le système d’exploitation Windows où chaque fenêtre peut avoir sa propre barre de menus attachée, toutes les applications en cours d’exécution sur macOS a une barre de menus unique qui s’exécute en haut de l’écran qui est utilisé pour chaque fenêtre de cette application :

[![Une barre de menus](menu-images/appmenu01.png "une barre de menus")](menu-images/appmenu01-large.png#lightbox)

Éléments dans cette barre de menus sont activées ou désactivées selon le contexte actuel ou l’état de l’application et son interface utilisateur à un moment donné. Par exemple : si l’utilisateur sélectionne un champ de texte, des éléments sur le **modifier** menu seront activé comme **copie** et **couper**.

En fonction d’Apple et, par défaut, toutes les applications Mac OS possèdent un ensemble standard de menus et éléments de menu qui s’affichent dans la barre de menus de l’application :

- **Menu Apple** -ce menu donne l’accès au système larges éléments qui sont disponibles à l’utilisateur à tout moment, quel que soit l’application est en cours d’exécution. Ces éléments ne peuvent pas être modifiés par le développeur.
- **Menu de l’application** -ce menu affiche le nom de l’application en gras et d’identifier l’utilisateur quelle application est en cours d’exécution. Il contient des éléments qui s’appliquent à l’application dans son ensemble et non en tant que document donné ou processus tels que la sortie de l’application.
- **Menu fichier** : éléments utilisés pour créer, ouvrir ou enregistrer les documents que votre application fonctionne avec. Si votre application n’est pas basé sur le document, ce menu peut être renommé ou supprimé.
- **Menu Edition** -contient des commandes telles que **couper**, **copie**, et **coller** qui sont utilisé pour modifier ou de modifier les éléments d’interface utilisateur de l’application.
- **Menu format** : si l’application fonctionne avec du texte, ce menu contient des commandes pour ajuster la mise en forme de ce texte.
- **Menu Affichage** -contient les commandes qui affectent l’affichage du contenu (afficher) dans l’interface utilisateur de l’application.
- **Les menus spécifiques à l’application** -il s’agit de tous les menus qui sont spécifiques à votre application (par exemple, un menu de signets pour un navigateur web). Ils doivent apparaître entre les **vue** et **fenêtre** menus dans la barre.
- **Menu fenêtre** -contient des commandes pour travailler avec windows dans votre application, ainsi qu’une liste des fenêtres actives en cours.
- **Menu Aide** -si votre application fournit une aide à l’écran, le menu d’aide doit être le menu de droite sur la barre. 

Pour plus d’informations sur la barre de menus d’application et les menus standard et les éléments de menu, consultez d’Apple [Human Interface Guidelines pour](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/).

### <a name="the-default-application-menu-bar"></a>La barre de menus d’application par défaut

Chaque fois que vous créez un nouveau projet Xamarin.Mac, vous obtenez automatiquement une barre de menus d’application par défaut standard, qui dispose des éléments standard qui a généralement une application macOS (comme nous l’avons vu dans la section ci-dessus). Barre de menus de votre application par défaut est défini dans le **Main.storyboard** fichier (avec le reste de l’interface utilisateur de votre application) sous le projet dans le **panneau solutions**:  

![Sélectionnez le storyboard principal](menu-images/appmenu02.png "sélectionner le storyboard principal")

Double-cliquez sur le **Main.storyboard** fichier pour l’ouvrir pour modification dans Xcode Interface Builder et vous verrez l’interface de l’éditeur de menu :

[![Modification de l’interface utilisateur dans Xcode](menu-images/defaultbar01.png "modification de l’interface utilisateur dans Xcode")](menu-images/defaultbar01-large.png#lightbox)

À ce stade nous pouvons cliquer sur les éléments tels que le **Open** élément de menu dans le **fichier** menu et de modifier ou ajuster ses propriétés dans le **inspecteur d’attributs**:

[![Modification des attributs d’un menu](menu-images/defaultbar02.png "la modification des attributs d’un menu")](menu-images/defaultbar02-large.png#lightbox)

Nous examinerons en ajout, modification et suppression de menus et des éléments plus loin dans cet article. Maintenant, nous voulons que simplement voir les menus et les éléments de menu sont disponibles par défaut et comment ils ont été exposés automatiquement au code via un ensemble de prédéfinis outlets et actions (pour plus d’informations, consultez notre [Outlets et Actions](~/mac/get-started/hello-mac.md#outlets-and-actions) documentation).

Par exemple, si nous cliquons sur le **connexion inspecteur** pour le **Open** élément de menu, nous pouvons voir automatiquement de connexion jusqu'à la `openDocument:` action : 

[![Affichage de l’action attachée](menu-images/defaultbar03.png "affichage de l’action attachée")](menu-images/defaultbar03-large.png#lightbox)

Si vous sélectionnez le **répondeur première** dans le **hiérarchie des interfaces** et faites défiler vers le bas dans la **inspecteur de connexion**, et vous verrez la définition de la `openDocument:` action qui les **Open** élément de menu est attaché à (ainsi que plusieurs autres actions par défaut pour l’application et ne sont pas associées automatiquement au contrôles) :

[![Affichage des actions reliées](menu-images/defaultbar04.png "affichage toutes les actions attachées")](menu-images/defaultbar04-large.png#lightbox) 

Pourquoi est-ce important ? Dans la prochaine section s’affiche le fonctionnement de ces actions définies automatiquement avec d’autres éléments d’interface utilisateur Cocoa pour automatiquement activer et désactiver des éléments de menu, mais aussi, fournir des fonctionnalités intégrées pour les éléments.

Plus tard nous allons utiliser ces actions intégrées pour activer et désactiver des éléments à partir du code et fournir notre propre fonctionnalité lorsqu’ils sont sélectionnés.

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>Fonctionnalités de menu intégré

Si vous étiez l’exécution, une application Xamarin.Mac nouvellement créée avant d’ajouter les éléments d’interface utilisateur ou le code, vous remarquerez que certains éléments sont automatiquement reliée et automatiquement activées (avec complet des fonctionnalités intégrées automatiquement), comme le **Quit** d’élément dans le **application** menu :

![Un élément de menu est activée](menu-images/appmenu03.png "un élément de menu est activée")

Même si d’autres éléments de menu, tels que **couper**, **copie**, et **coller** ne sont pas :

![Éléments de menu désactivés](menu-images/appmenu04.png "désactivé des éléments de menu")

Nous allons arrêter l’application et double-cliquez sur le **Main.storyboard** de fichiers dans le **panneau solutions** pour l’ouvrir pour modification dans Xcode Interface Builder de. Ensuite, faites glisser un **affichage de texte** à partir de la **bibliothèque** sur le contrôleur d’affichage de la fenêtre dans le **Éditeur d’Interface**:

[![Sélection d’un affichage de texte à partir de la bibliothèque](menu-images/appmenu05.png "en sélectionnant un affichage de texte à partir de la bibliothèque")](menu-images/appmenu05-large.png#lightbox)

Dans le **éditeur de contrainte** nous allons épingler l’affichage de texte sur les bords de la fenêtre et le définir où il augmente ou diminue avec la fenêtre en cliquant sur toutes les quatre je rouges en haut de l’éditeur sur le **ajouter 4 contraintes** bouton :

[![Modifier les contraintes](menu-images/appmenu06.png "modifier les contraintes")](menu-images/appmenu06-large.png#lightbox)

Enregistrez vos modifications dans la conception de l’interface utilisateur et revenir à Visual Studio pour Mac pour synchroniser les modifications avec votre projet Xamarin.Mac. Démarrer l’application, tapez du texte dans l’affichage de texte, sélectionnez-le, puis ouvrez maintenant le **modifier** menu :

![Les éléments de menu sont automatiquement activées/désactivées](menu-images/appmenu07.png "les éléments de menu sont automatiquement activé/désactivé")

Notez comment la **couper**, **copie**, et **coller** éléments sont automatiquement activées et entièrement fonctionnelle, tout cela sans écrire une seule ligne de code. 

Qu'est-ce qui se passe ? N’oubliez pas d’intégrés prédéfinir des actions qui viennent câblées jusqu'à les éléments de menu par défaut (comme présenté ci-dessus), la plupart des éléments d’interface utilisateur Cocoa qui font partie de macOS ont générés dans les raccordements à des actions spécifiques (tels que `copy:`). Par conséquent, lorsqu’ils sont ajoutés à une fenêtre, active et sélectionnés, l’élément de menu correspondant ou les éléments associés à cette action sont automatiquement activés. Si l’utilisateur sélectionne un élément de menu, la fonctionnalité intégrée dans l’élément d’interface est appelée et exécutée, tout cela sans intervention des développeurs.

### <a name="enabling-and-disabling-menus-and-items"></a>Activation et désactivation des menus et éléments

Par défaut, chaque fois qu’un événement se produit, `NSMenu` automatiquement Active ou désactive le chaque menu visible et élément basé sur le contexte de l’application. Il existe trois façons d’activer/désactiver un élément :

- **L’activation de menus automatique** -un élément de menu est activé si `NSMenu` peut trouver un objet approprié qui répond à l’action de l’élément est câblé-jusqu'à. Par exemple, l’affichage de texte ci-dessus qui avait un raccordement intégré à la `copy:` action.
- **Actions personnalisées et validateMenuItem :** : pour n’importe quel élément de menu qui est lié à un [action personnalisée de fenêtre ou une vue contrôleur](#Working-with-Custom-Window-Actions), vous pouvez ajouter la `validateMenuItem:` action et activer ou désactiver manuellement les éléments de menu.
- **L’activation manuelle menu** -définir manuellement le `Enabled` propriété de chaque `NSMenuItem` pour activer ou désactiver individuellement de chaque élément dans un menu.

Pour choisir un système, affectez la `AutoEnablesItems` propriété d’un `NSMenu`. `true` est automatique (comportement par défaut) et `false` est manuel. 

> [!IMPORTANT]
> Si vous choisissez d’utiliser l’activation de menu manuelle, none du menu éléments, même celles qui sont contrôlées par les classes AppKit comme `NSTextView`, sont automatiquement mis à jour. Vous serez facturé pour l’activation et désactivation de tous les éléments à la main dans le code.

#### <a name="using-validatemenuitem"></a>À l’aide de validateMenuItem

Comme indiqué ci-dessus, pour n’importe quel élément de menu qui est lié à un [fenêtre ou vue contrôleur Custom Action](#Working-with-Custom-Window-Actions), vous pouvez ajouter la `validateMenuItem:` action et activer ou désactiver manuellement les éléments de menu.

Dans l’exemple suivant, le `Tag` propriété sera utilisée pour déterminer le type d’élément de menu qui sera être activé ou désactivé par le `validateMenuItem:` action en fonction de l’état du texte sélectionné dans un `NSTextView`. Le `Tag` propriété a été définie dans l’Interface Builder pour chaque élément de menu :

![Définition de la propriété Tag](menu-images/validate01.png "définissant la propriété de la balise")

Et le code suivant ajouté pour le contrôleur d’affichage :

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

Lorsque ce code est exécuté, et aucun texte n’est sélectionné dans le `NSTextView`, les éléments de menu de type deux wrap sont désactivés (même s’ils sont associés à des actions sur le contrôleur d’affichage) :

![Les éléments montrant désactivé](menu-images/validate02.png "montrant les éléments désactivés")

Si une section de texte est sélectionnée et rouvert le menu, les éléments de menu de deux de type wrap seront disponibles :

![Les éléments montrant activé](menu-images/validate03.png "montrant les éléments activés")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>L’activation et la réponse aux éléments de menu dans le code

Comme nous l’avons vu ci-dessus, simplement en ajoutant des éléments d’interface utilisateur Cocoa spécifiques à notre conception de l’interface utilisateur (par exemple, un champ de texte), plusieurs des éléments de menu par défaut seront activés et fonctionnent automatiquement, sans avoir à écrire du code. Suivant jetons un œil à l’ajout de notre propre code c# à notre projet Xamarin.Mac pour activer un élément de menu et de fournir des fonctionnalités lors de l’utilisateur le sélectionne.

Par exemple, let say nous voulons l’utilisateur soit en mesure d’utiliser le **Open** d’élément dans le **fichier** menu pour sélectionner un dossier. Dans la mesure où nous voulons qu’il s’agit d’une fonction de l’application et non limité à une fenêtre de donnée ou d’un élément d’interface utilisateur, nous allons ajouter le code pour gérer cette situation à notre délégué d’application.

Dans le **panneau solutions**, double-cliquez sur le `AppDelegate.CS` fichier à ouvrir pour modification :

![En sélectionnant le délégué de l’application](menu-images/appmenu08.png "en sélectionnant le délégué de l’application")

Ajoutez le code suivant ci-dessous le `DidFinishLaunching` méthode :

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

Nous allons exécuter l’application maintenant et ouvrez le **fichier** menu : 

![Le menu fichier](menu-images/appmenu09.png "le menu fichier")

Notez que le **Open** élément de menu est maintenant activé. Si nous le sélectionnons, la boîte de dialogue Ouvrir s’affiche :

![Une boîte de dialogue Ouvrir](menu-images/appmenu10.png "une boîte de dialogue Ouvrir")

Si nous cliquons sur le **Open** bouton, notre message d’alerte s’affichera :

![Un exemple de message de boîte de dialogue](menu-images/appmenu11.png "un exemple de message de boîte de dialogue")

La ligne de clé a été `[Export ("openDocument:")]`, il indique à `NSMenu` que notre **AppDelegate** possède une méthode `void OpenDialog (NSObject sender)` qui répond à la `openDocument:` action. Si vous en souvenir ci-dessus, le **Open** élément de menu est automatiquement câblé à distance à cette action par défaut dans l’Interface Builder :

[![Afficher les actions attachées](menu-images/defaultbar03.png "affichant les actions attachées")](menu-images/defaultbar03-large.png#lightbox)

Suivant jetons un œil à créer notre propre menu, les éléments de menu et les actions et répondre à ces dans le code.

### <a name="working-with-the-open-recent-menu"></a>Utilisation de l’ouvrir le menu récents

Par défaut, le **fichier** menu contient un **Open récente** élément qui effectue le suivi des dernière plusieurs fichiers que l’utilisateur a ouvert avec votre application. Si vous créez un `NSDocument` en fonction de l’application Xamarin.Mac, ce menu sera géré automatiquement pour vous. Pour tout autre type d’application Xamarin.Mac, vous serez responsable de la gestion et résolution de cet élément de menu manuellement.

Pour gérer manuellement le **Open récente** menu, vous devez d’abord il informe qu’un nouveau fichier a été ouvert ou enregistré à l’aide de ce qui suit :

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

Même si votre application n’utilise pas `NSDocuments`, toujours utiliser le `NSDocumentController` pour maintenir la **Open récente** menu en envoyant un `NSUrl` avec l’emplacement du fichier à la `NoteNewRecentDocumentURL` méthode de la `SharedDocumentController`.

Ensuite, vous devez remplacer le `OpenFile` méthode du délégué d’application pour ouvrir n’importe quel fichier que l’utilisateur sélectionne à partir de la **ouvrir récente** menu. Exemple :

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

Retourner `true` si le fichier peut être ouvert, sinon retourner `false` et un avertissement intégré s’affichera à l’utilisateur que le fichier ne peut pas être ouvert.

Car le nom de fichier et le chemin d’accès retourné à partir de la **Open récente** menu, peut inclure un espace, nous devons correctement ce caractère d’échappement des avant de créer un `NSUrl` ou nous obtiendra une erreur. Nous le faire avec le code suivant :

```csharp
filename = filename.Replace (" ", "%20");
```

Enfin, nous créons un `NSUrl` qui pointe vers le fichier, utiliser une méthode d’assistance dans l’application déléguer à ouvrir une nouvelle fenêtre et y charger le fichier :

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

Pour extraire tous les éléments ensemble, jetons un œil à un exemple d’implémentation dans une **AppDelegate.cs** fichier :

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

En fonction des exigences de votre application, vous pourrez souhaitez pas que l’utilisateur d’ouvrir le même fichier dans plusieurs fenêtres en même temps. Dans notre exemple d’application, si l’utilisateur choisit un fichier qui est déjà ouvert (à partir de la **ouvrir récente** ou **ouvrir...** éléments de menu), la fenêtre qui contient le fichier est mises au premier plan.

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

Nous avons conçu notre `ViewController` classe destinée à contenir le chemin d’accès au fichier dans son `Path` propriété. Ensuite, nous parcourons toutes les fenêtres actuellement ouvertes dans l’application. Si le fichier est déjà ouvert dans une des fenêtres, il est amené à l’avant-plan de toutes les autres fenêtres à l’aide de :

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

Si aucune correspondance n’est trouvée, une nouvelle fenêtre s’ouvre avec le fichier chargé et que le fichier est indiqué dans le **Open récente** menu :

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

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>L’utilisation d’actions de fenêtres personnalisées

Tout comme l’intégrée **premier répondeur** les actions qui sont d’aux éléments de menu standard, vous pouvez créer de nouvelles actions personnalisées et les relier aux éléments de menu dans le Générateur d’Interface.

Tout d’abord, définissez une action personnalisée sur l’un des contrôleurs de fenêtre de votre application. Exemple :

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

Ensuite, double-cliquez sur le fichier d’animation de l’application dans le **panneau solutions** pour l’ouvrir pour modification dans Xcode Interface Builder de. Sélectionnez le **répondeur première** sous le **Application scène**, puis basculez vers le **inspecteur d’attributs**:

![L’inspecteur d’attributs](menu-images/action01.png "l’inspecteur d’attributs")

Cliquez sur le **+** bouton en bas de la **inspecteur d’attributs** pour ajouter une action personnalisée :

![Ajout d’une nouvelle action](menu-images/action02.png "ajoutant une nouvelle action")

Attribuez-lui le même nom que l’action personnalisée que vous avez créé sur votre contrôleur de fenêtre :

![Modification du nom d’action](menu-images/action03.png "modification du nom d’action")

Cliquez et faites glisser à partir d’un élément de menu pour le **répondeur première** sous le **Application scène**. Dans la liste déroulante, sélectionnez la nouvelle action que vous venez de créer (`defineKeyword:` dans cet exemple) :

![Attachement d’une action](menu-images/action04.png "attachement d’une action")

Enregistrez les modifications dans la table de montage séquentiel et revenir à Visual Studio pour Mac synchroniser les modifications. Si vous exécutez l’application, l’élément de menu que vous avez connecté l’action personnalisée sera automatiquement être activé ou désactivé (basé sur la fenêtre avec l’action étant ouvert) et en sélectionnant l’élément de menu sera déclencher l’action :

[![Pour tester l’action nouvelle](menu-images/action05.png "la nouvelle action de test")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>Ajout, modification et suppression de menus

Comme nous l’avons vu dans les sections précédentes, une application Xamarin.Mac est fourni avec un nombre prédéfini de menus par défaut et les éléments de menu spécifiques contrôles d’interface utilisateur seront automatiquement activer et y répondre. Nous avons également vu comment ajouter du code à notre application permettant d’activer également et répondre à ces éléments par défaut.

Dans cette section, nous allons examiner la suppression d’éléments de menu qui nous n’avez pas besoin, la réorganisation des menus et l’ajout de nouveaux menus, les éléments de menu et les actions.

Double-cliquez sur le **Main.storyboard** de fichiers dans le **panneau solutions** à ouvrir pour modification :

[![Modification de l’interface utilisateur dans Xcode](menu-images/maint01.png "modification de l’interface utilisateur dans Xcode")](menu-images/maint01-large.png#lightbox)

Pour notre application Xamarin.Mac spécifique nous n’allons pas à être à l’aide de la valeur par défaut **vue** menu donc nous allons le supprimer. Dans le **hiérarchie des interfaces** sélectionner le **vue** élément de menu qui fait partie de la barre de menus principale :

![En sélectionnant l’élément de menu Afficher](menu-images/maint02.png "en sélectionnant l’élément de menu Affichage")

Appuyez sur SUPPR ou retour arrière pour supprimer le menu. Ensuite, nous n’allons pas être à l’aide de tous les éléments dans le **Format** menu et nous voulons déplacer les éléments que nous allons utiliser sous les menus sub. Dans le **hiérarchie des interfaces** sélectionner les éléments suivants :

![Plusieurs éléments de mise en surbrillance](menu-images/maint03.png "plusieurs éléments de mise en surbrillance")

Faites glisser les éléments sous le parent **Menu** dans le sous-menu dans lequel ils sont actuellement :

[![En faisant glisser des éléments de menu au menu parent](menu-images/maint04.png "en faisant glisser des éléments de menu au menu parent")](menu-images/maint04-large.png#lightbox)

Votre menu doit maintenant ressembler à :

[![Les éléments dans le nouvel emplacement](menu-images/maint05.png "les éléments dans le nouvel emplacement")](menu-images/maint05-large.png#lightbox)

Suivant faisons glisser le **texte** sous-menu out à partir de la **Format** menu et placez-la sur la barre de menus principale entre le **Format** et **fenêtre** menus :

[![Le menu de texte](menu-images/maint06.png "menu le texte")](menu-images/maint06-large.png#lightbox)

Revenons dans le **Format** menu et supprimer le **police** élément de sous-menu. Ensuite, sélectionnez le **Format** menu et renommez-le « Police » :

[![Le menu de la police](menu-images/maint07.png "menu de la police")](menu-images/maint07-large.png#lightbox)

Ensuite, nous allons créer un menu personnalisé d’expressions prédéfini qui sera obtenir automatiquement ajouté au texte contenu dans l’affichage de texte lorsqu’ils sont sélectionnés. Dans la zone de recherche en bas de la **inspecteur de bibliothèque** type dans « menu ». Cela rend plus faciles à trouver et de travailler avec tous les éléments de l’interface utilisateur du menu :

![L’inspecteur de bibliothèque](menu-images/maint08.png "l’inspecteur de bibliothèque")

Maintenant nous allons procédez comme suit pour créer notre menu :

1. Faites glisser un **élément de Menu** à partir de la **inspecteur de bibliothèque** sur la barre de menus entre le **texte** et **fenêtre** menus : 

    ![Sélection d’un nouvel élément de menu dans la bibliothèque](menu-images/maint10.png "en sélectionnant un élément de menu dans la bibliothèque")
2. Renommer l’élément « Expressions » : 

    [![Définition du nom de menu](menu-images/maint09.png "définition du nom de menu")](menu-images/maint09-large.png#lightbox)
3. Ensuite faire glisser un **Menu** à partir de la **inspecteur de bibliothèque**: 

    ![Sélection d’un menu à partir de la bibliothèque](menu-images/maint11.png "en sélectionnant un menu à partir de la bibliothèque")
4. Liste déroulante, puis **Menu** sur le nouveau **élément de Menu** nous venez de créer et modifier son nom à « Expressions » : 

    [![Modification du nom de menu](menu-images/maint12.png "modification du nom de menu")](menu-images/maint12-large.png#lightbox)
5. Maintenant nous allons renommer la valeur par défaut trois **des éléments de Menu** « Address », « Date » et « Greeting » : 

    [![Le menu de Phrases](menu-images/maint13.png "menu de Phrases le")](menu-images/maint13-large.png#lightbox)
6. Nous allons ajouter une quatrième **élément de Menu** en faisant glisser un **élément de Menu** à partir de la **inspecteur de bibliothèque** et l’appeler « Signature » : 

    [![Modification du nom d’élément de menu](menu-images/maint14.png "modification du nom d’élément de menu")](menu-images/maint14-large.png#lightbox)
7. Enregistrez les modifications dans la barre de menus.

Maintenant nous allons créer un ensemble d’actions personnalisées afin que nos nouveaux éléments de menu sont exposés au code c#. Dans Xcode passons à la **Assistant** vue :

[![Créer les actions requises](menu-images/maint15.png "création les actions requises")](menu-images/maint15-large.png#lightbox)

Nous allons effectuer les opérations suivantes :

1. Glisser-déplacer à partir de la **adresse** élément de menu pour le **AppDelegate.h** fichier.
2. Commutateur le **connexion** type **Action**: 

    [![En sélectionnant le type d’action](menu-images/maint17.png "en sélectionnant le type d’action")](menu-images/maint17-large.png#lightbox)
3. Entrez un **nom** de « phraseAddress » et appuyez sur la **Connect** bouton permettant de créer la nouvelle action : 

    [![Configuration de l’action](menu-images/maint18.png "configuration de l’action")](menu-images/maint18-large.png#lightbox)
4. Répétez les étapes ci-dessus pour le **Date**, **salutation**, et **Signature** des éléments de menu : 

    [![Les actions terminées](menu-images/maint19.png "les actions terminées")](menu-images/maint19-large.png#lightbox)
5. Enregistrez les modifications dans la barre de menus.

Ensuite, nous devons créer un outlet pour notre affichage de texte afin que nous ne pouvons ajuster son contenu à partir du code. Sélectionnez le **ViewController.h** de fichiers dans le **éditeur de l’Assistant** et créer un nouveau magasin appelé `documentText`:

[![Création d’un outlet](menu-images/maint20.png "création d’un outlet")](menu-images/maint20-large.png#lightbox)

Revenez à Visual Studio pour Mac synchroniser les modifications à partir de Xcode. Modifiez ensuite le **ViewController.cs** de fichiers et qu’elle ressemble à ce qui suit :

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

Cela expose le texte de notre affichage de texte en dehors de la `ViewController` classe et informe le délégué de l’application lorsque la fenêtre les gains ou perd le focus. À présent modifier le **AppDelegate.cs** de fichiers et qu’elle ressemble à ce qui suit :

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

Ici, nous avons apporté les `AppDelegate` un partiel classe afin que nous pouvons utiliser les actions et outlets que nous avons défini dans l’Interface Builder. Nous avons également d’exposer un `textEditor` pour effectuer le suivi de la fenêtre qui est actuellement active.

Les méthodes suivantes sont utilisées pour gérer notre menu personnalisé et les éléments de menu :

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

Maintenant, si nous exécutons notre application, tous les éléments dans le **expression** menu sera actif et ajoute l’expression donnée à la vue de texte lorsque sélectionné :

![Un exemple de l’application en cours d’exécution](menu-images/maint21.png "un exemple de l’application en cours d’exécution")

Maintenant que nous avons les principes fondamentaux de l’utilisation de la barre de menus d’application vers le bas, examinons la création d’un menu contextuel personnalisé.

### <a name="creating-menus-from-code"></a>Création de menus à partir du code

Outre la création de menus et éléments de menu avec Interface Builder de Xcode, il peut arriver lorsqu’une application Xamarin.Mac doit créer, modifier ou supprimer un menu, un sous-menu ou un élément de menu à partir du code.

Dans l’exemple suivant, une classe est créée pour contenir les informations sur les éléments de menu et les sous-menus qui vont être créées dynamiquement à la volée :

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

#### <a name="adding-menus-and-items"></a>Ajout de menus et éléments

Avec cette classe est définie, la routine suivante analyse une collection de `LanguageFormatCommand`objets et manière récursive build nouveaux menus et éléments de menu en les ajoutant au bas du menu existant (créé dans l’Interface Builder) qui a été passé dans :

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

Pour toute `LanguageFormatCommand` objet qui a une valeur vide `Title` propriétés, cette routine génère un **élément de menu séparateur** (une fine ligne grise) entre des sections du menu :

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

Si un titre est fourni, un nouvel élément de menu à ce poste est créé :

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

Si le `LanguageFormatCommand` objet contient des enfants `LanguageFormatCommand` objets, un sous-menu est créé et le `AssembleMenu` méthode est appelée pour générer ce menu de manière récursive :

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

Pour tout nouvel élément de menu qui n’a pas de sous-menus, le code est ajouté pour gérer l’élément de menu sélectionné par l’utilisateur :

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>Test de la création de menu

Avec tout le code ci-dessus en place, si la collection suivante de `LanguageFormatCommand` objets ont été créés :

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

Cette collection est transmise à la `AssembleMenu` (fonction) (avec le **Format** Menu définie en tant que la base), les menus dynamiques suivants et les éléments de menu doivent être créés :

![Les nouveaux éléments de menu dans l’application en cours d’exécution](menu-images/dynamic01.png "les nouveaux éléments de menu dans l’application en cours d’exécution")

#### <a name="removing-menus-and-items"></a>Suppression de menus et éléments

Si vous avez besoin supprimer un menu ou un élément de menu à partir de l’interface utilisateur de l’application, vous pouvez utiliser la `RemoveItemAt` méthode de la `NSMenu` classe simplement en lui attribuant le zéro à index de base de l’élément à supprimer.

Par exemple, pour supprimer les menus et les éléments de menu créés par la routine ci-dessus, vous pouvez utiliser le code suivant :

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

Dans le cas le code ci-dessus, les premiers éléments de quatre menu sont créés dans Xcode Interface Builder et rangements disponibles dans l’application, afin qu’ils ne sont pas supprimés de manière dynamique.

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>Menus contextuels

Menus contextuels s’affichent lorsque l’utilisateur clique ou un élément dans une fenêtre de contrôle-clics. Par défaut, plusieurs des éléments d’interface utilisateur intégrées à macOS déjà ont des menus contextuels qui leur sont attachées (par exemple, l’affichage de texte). Toutefois, il peut arriver que vous souhaitiez créer nos propres menus contextuels personnalisés pour un élément d’interface utilisateur que nous avons ajouté à une fenêtre.

Permet de modifier notre **Main.storyboard** fichier dans Xcode et ajoutez un **fenêtre** fenêtre à notre conception, définissez son **classe** à « NSPanel » dans le **inspecteur d’identité**, ajouter un nouveau **Assistant** d’élément à la **fenêtre** menu et l’attacher à la nouvelle fenêtre en utilisant un **afficher Segue**:

[![Définition du type de segue](menu-images/context01.png "définissant le type de segue")](menu-images/context01-large.png#lightbox)

Nous allons effectuer les opérations suivantes :

1. Faites glisser un **étiquette** à partir de la **inspecteur de bibliothèque** sur le **panneau** fenêtre et définissez son texte sur « Propriété » : 

    [![Modification de valeur de l’étiquette](menu-images/context03.png "modifiant la valeur de l’étiquette")](menu-images/context03-large.png#lightbox)
2. Ensuite faire glisser un **Menu** à partir de la **inspecteur de bibliothèque** sur le contrôleur d’affichage dans la hiérarchie d’affichage et éléments de menu par défaut de renommer les trois **Document**, **texte**  et **police**:

    [![Les éléments de menu requis](menu-images/context02.png "les éléments de menu requis")](menu-images/context02-large.png#lightbox)
3. Maintenant glisser-déplacer à partir de la **libellé de la propriété** sur le **Menu**:

    [![Déplacer pour créer un segue](menu-images/context04.png "déplacer pour créer un segue")](menu-images/context04-large.png#lightbox)
4. Dans la boîte de dialogue contextuelle, sélectionnez **Menu**: 

    ![Définition du type de segue](menu-images/context05.png "définissant le type de segue")
5. À partir de la **inspecteur d’identité**, set, classe de contrôleur d’affichage pour « PanelViewController » : 

    [![Définition de la classe segue](menu-images/context10.png "définition de la classe de segue")](menu-images/context10-large.png#lightbox)
6. Revenez à Visual Studio pour Mac à synchroniser, puis revenez à Interface Builder.
7. Basculez vers le **éditeur de l’Assistant** et sélectionnez le **PanelViewController.h** fichier.
8. Créez une action pour le **Document** appelé élément de menu `propertyDocument`: 

    [![Configuration de l’action](menu-images/context06.png "configuration de l’action")](menu-images/context06-large.png#lightbox)
9. Répéter des actions de création pour les autres éléments de menu : 

    [![Les actions requises](menu-images/context07.png "les actions requises")](menu-images/context07-large.png#lightbox)
10. Pour finir, créer un outlet pour le **libellé de la propriété** appelée `propertyLabel`: 

    [![Configuration de l’outlet](menu-images/context08.png "configuration de l’outlet")](menu-images/context08-large.png#lightbox)
11. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.

Modifier le **PanelViewController.cs** fichier, puis ajoutez le code suivant :

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

Maintenant, si nous pouvons exécuter l’application et que vous avec le bouton droit sur l’étiquette de propriété dans le panneau de configuration, nous verrons notre menu contextuel personnalisé. Si nous sélectionner et d’élément dans le menu, valeur de l’étiquette change :

![Le menu contextuel en cours d’exécution](menu-images/context09.png "le menu contextuel en cours d’exécution")

Suivant jetons un œil à la création de menus de barre d’état.

## <a name="status-bar-menus"></a>Menus de barre d’état

Menus de barre d’état affichent une collection d’éléments de menu d’état qui fournissent une interaction avec ou des commentaires à l’utilisateur, par exemple un menu ou d’une image de refléter l’état de l’application. Menu de barre d’état d’une application est activé et actif, même si l’application s’exécute en arrière-plan. La barre d’état de l’échelle du système se trouve sur le côté droit de la barre de menus d’application et est la seule barre d’état actuellement disponibles dans macOS.

Nous allons modifier notre **AppDelegate.cs** et effectuez le `DidFinishLaunching` méthode ressemble ce qui suit :

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` nous donne accès à la barre d’état de l’échelle du système. `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` Crée un nouvel élément de barre d’état. À partir de là, nous créer un menu et un nombre d’éléments de menu et attacher le menu à l’élément de barre d’état que nous venons de créer. 

Si nous exécutons l’application, le nouvel élément de barre d’état s’affichera. Sélection d’un élément dans le menu change le texte dans l’affichage de texte : 

![Le menu de barre d’état en cours d’exécution](menu-images/statusbar01.png "le menu de barre d’état en cours d’exécution")

Ensuite, jetons un œil à la création d’éléments de menu ancrer personnalisé.

## <a name="custom-dock-menus"></a>Menus de la station d’accueil personnalisée

Le menu d’ancrage s’affiche pour votre application de Mac lorsque l’utilisateur clique ou contrôle-clique sur l’icône de l’application dans le dock :

![Menu d’ancrage personnalisé](menu-images/dock01.png "personnalisé ancrer le menu")

Nous allons créer un menu d’ancrage personnalisé pour notre application en procédant comme suit :

1. Dans Visual Studio pour Mac, cliquez sur de l’application projet, puis sélectionnez **ajouter** > **nouveau fichier...** Dans la boîte de dialogue Nouveau fichier, sélectionnez **Xamarin.Mac** > **définition d’Interface vide**, utilisez « DockMenu » pour le **nom** et cliquez sur le **New**  bouton permettant de créer la nouvelle **DockMenu.xib** fichier :

    ![Ajout d’une définition d’interface vide](menu-images/dock02.png "Ajout d’une définition d’interface vide")
2. Dans le **panneau solutions**, double-cliquez sur le **DockMenu.xib** fichier à ouvrir pour modification dans Xcode. Créer un nouveau **Menu** avec les éléments suivants : **Adresse**, **Date**, **salutation**, et **Signature** 

    [![Mise en page de l’interface utilisateur](menu-images/dock03.png "mise en page de l’interface utilisateur")](menu-images/dock03-large.png#lightbox)
3. Ensuite, nous allons vous connecter à nos nouveaux éléments de menu à nos actions existantes que nous avons créé notre menu personnalisé dans le [Ajout, modification et suppression de Menus](#Adding,_Editing_and_Deleting_Menus) section ci-dessus. Basculez vers le **inspecteur de connexion** et sélectionnez le **répondeur première** dans le **hiérarchie des interfaces**. Faites défiler vers le bas et recherchez le `phraseAddress:` action. Faites glisser une ligne à partir du cercle sur cette action pour le **adresse** élément de menu :

    [![En faisant glisser pour associer une action](menu-images/dock04.png "en faisant glisser pour associer une action")](menu-images/dock04-large.png#lightbox)
4. Répétez pour tous les autres éléments leur attachement à leurs actions correspondantes : 

    [![Les actions requises](menu-images/dock05.png "les actions requises")](menu-images/dock05-large.png#lightbox)
5. Ensuite, sélectionnez le **Application** dans le **hiérarchie des interfaces**. Dans le **connexion inspecteur**, faites glisser une ligne à partir du cercle le `dockMenu` prise au menu nous venons de créer :

    [![En faisant glisser le câble de l’outlet](menu-images/dock06.png "en faisant glisser le câble de la prise")](menu-images/dock06-large.png#lightbox)
6. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.
7. Double-cliquez sur le **Info.plist** fichier à ouvrir pour modification : 

    [![Modification du fichier Info.plist](menu-images/dock07.png "Modification du fichier Info.plist")](menu-images/dock07-large.png#lightbox)
8. Cliquez sur le **Source** onglet en bas de l’écran : 

    [![En sélectionnant la vue de Source](menu-images/dock08.png "en sélectionnant la vue de Source")](menu-images/dock08-large.png#lightbox)
9. Cliquez sur **ajouter nouvelle entrée**définir le nom de propriété à « AppleDockMenu » et la valeur à « DockMenu » (le nom de notre nouveau fichier .xib sans l’extension), cliquez sur le bouton plus vert : 

    [![Ajout de l’élément DockMenu](menu-images/dock09.png "Ajout de l’élément DockMenu")](menu-images/dock09-large.png#lightbox)

Maintenant si nous pouvons exécuter notre application et que vous avec le bouton droit sur son icône dans le Dock, nos nouveaux éléments de menu seront affiche :

![Un exemple du menu d’ancrage en cours d’exécution](menu-images/dock10.png "un exemple du menu d’ancrage en cours d’exécution")

Si nous sélectionnons un des éléments personnalisés dans le menu, le texte dans notre affichage de texte est modifié.

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>Bouton contextuelle et les listes déroulantes

Un bouton déroulant affiche un élément sélectionné et présente une liste d’options à sélectionner à partir d’un clic par l’utilisateur. Une liste déroulante est un type de bouton déroulant généralement utilisé pour la sélection des commandes spécifiques au contexte de la tâche en cours. Les deux peuvent apparaître n’importe où dans une fenêtre.

Nous allons créer un bouton déroulant personnalisé pour notre application en procédant comme suit :

1. Modifier le **Main.storyboard** fichier dans Xcode, puis faites glisser un **bouton de menu contextuel** à partir de la **inspecteur de bibliothèque** sur le **panneau** fenêtre que nous avons créé dans le [Menus contextuels](#Contextual_Menus) section : 

    [![Ajout d’un bouton de menu contextuel](menu-images/popup01.png "Ajout d’un bouton de menu contextuel")](menu-images/popup01-large.png#lightbox)
2. Ajouter un nouvel élément de menu et configurer les titres des éléments dans le menu contextuel pour : **Adresse**, **Date**, **salutation**, et **Signature** 

    [![Configurer les éléments de menu](menu-images/popup02.png "configuration les éléments de menu")](menu-images/popup02-large.png#lightbox)
3. Ensuite, nous allons connecter nos nouveaux éléments de menu pour les actions que nous avons créé notre menu personnalisé dans le [Ajout, modification et suppression de Menus](#Adding,_Editing_and_Deleting_Menus) section ci-dessus. Basculez vers le **inspecteur de connexion** et sélectionnez le **répondeur première** dans le **hiérarchie des interfaces**. Faites défiler vers le bas et recherchez le `phraseAddress:` action. Faites glisser une ligne à partir du cercle sur cette action pour le **adresse** élément de menu : 

    [![En faisant glisser pour associer une action](menu-images/popup03.png "en faisant glisser pour associer une action")](menu-images/popup03-large.png#lightbox)
4. Répétez pour tous les autres éléments leur attachement à leurs actions correspondantes : 

    [![Toutes les actions nécessaires](menu-images/popup04.png "toutes les actions nécessaires")](menu-images/popup04-large.png#lightbox)
5. Enregistrez vos modifications et revenir à Visual Studio pour Mac se synchroniser avec Xcode.

Maintenant, si nous pouvons exécuter notre application et que vous sélectionnez un élément dans la fenêtre contextuelle, le texte dans notre affichage de texte change :

![Un exemple de la fenêtre contextuelle en cours d’exécution](menu-images/popup05.png "un exemple de la fenêtre contextuelle en cours d’exécution")

Vous pouvez créer et utiliser des listes déroulantes dans la même façon en tant que boutons contextuelle. Au lieu d’attacher à des actions existant, vous pouvez créer vos propres actions comme nous l’avons fait pour notre menu contextuel dans le [Menus contextuels](#Contextual_Menus) section.

## <a name="summary"></a>Récapitulatif

Cet article a examiné en détail à l’utilisation des menus et des éléments de menu dans une application Xamarin.Mac. Tout d’abord, nous avons examiné la barre de menus, puis nous avons étudié la création de menus contextuels, ensuite nous avons examiné les menus de barre d’état personnalisé ancrer des menus. Enfin, nous avons abordé les menus déroulants et listes déroulantes.


## <a name="related-links"></a>Liens associés

- [MacMenus (sample)](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Human Interface Guidelines pour - Menus](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [Présentation des Menus de l’Application et les listes contextuelles](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
