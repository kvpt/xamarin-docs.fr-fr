---
title: . Storyboard/. XIB-conception de l’interface utilisateur moins dans Xamarin. Mac
description: Cet article explique comment créer une interface utilisateur de l’application Xamarin. Mac C# directement à partir du code, sans fichiers. Storyboard, fichiers. xib ou Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: d51e1b20e1409d228db2f38e6c31ad1165897654
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226892"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>. Storyboard/. XIB-conception de l’interface utilisateur moins dans Xamarin. Mac

_Cet article explique comment créer une interface utilisateur de l’application Xamarin. Mac C# directement à partir du code, sans fichiers. Storyboard, fichiers. xib ou Interface Builder._

## <a name="overview"></a>Présentation

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes éléments d’interface utilisateur et aux mêmes outils que ceux d’un développeur qui travaille en *objective-C* et *Xcode* . En général, lors de la création d’une application Xamarin. Mac, vous utiliserez Interface Builder de Xcode avec des fichiers. Storyboard ou. XIB pour créer et gérer l’interface utilisateur de votre application.

Vous avez également la possibilité de créer une partie ou la totalité de l’interface utilisateur de votre application Xamarin C# . Mac directement dans le code. Dans cet article, nous allons aborder les principes fondamentaux de la création d’interfaces utilisateur et C# d’éléments d’interface utilisateur dans le code.

[![Éditeur de code Visual Studio pour Mac](xibless-ui-images/intro01.png "Éditeur de code Visual Studio pour Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Basculement d’une fenêtre pour utiliser du code

Lorsque vous créez une application de cacao Xamarin. Mac, vous recevez une fenêtre vide standard par défaut. Cette fenêtre est définie dans un fichier **main. Storyboard** (ou traditionnellement un fichier **MainWindow. XIB**) inclus automatiquement dans le projet. Cela comprend également un fichier **ViewController.cs** qui gère la vue principale de l’application (ou encore un **MainWindow.cs** et un fichier **MainWindowController.cs** ).

Pour basculer vers une fenêtre Xibless pour une application, procédez comme suit:

1. Ouvrez l’application que vous souhaitez arrêter à l' `.storyboard` aide des fichiers ou. XIB pour définir l’interface utilisateur dans Visual Studio pour Mac.
2. Dans le **panneau solutions**, cliquez avec le bouton droit sur le fichier **main. Storyboard** ou **MainWindow. XIB** , puis sélectionnez **supprimer**:

    ![Suppression de la fenêtre ou du storyboard principal](xibless-ui-images/switch01.png "Suppression de la fenêtre ou du storyboard principal")
3. Dans la **boîte de dialogue supprimer**, cliquez sur le bouton **supprimer** pour supprimer complètement le. Storyboard ou. XIB du projet:

    ![Confirmation de la suppression](xibless-ui-images/switch02.png "Confirmation de la suppression")

Nous devons maintenant modifier le fichier **MainWindow.cs** pour définir la disposition de la fenêtre et modifier le fichier **ViewController.cs** ou **MainWindowController.cs** pour créer une instance de notre `MainWindow` classe, car nous n’utilisons plus le. fichier Storyboard ou. XIB.

Les applications Xamarin. Mac modernes qui utilisent des storyboards pour leur interface utilisateur peuvent ne pas inclure automatiquement les fichiers **MainWindow.cs**, **ViewController.cs** ou **MainWindowController.cs** . Si nécessaire, ajoutez simplement une nouvelle classe C# vide au projet (**Ajouter** > un**nouveau fichier...** Classe videgénérale > ) et nommez-la identique au fichier manquant.  > 


### <a name="defining-the-window-in-code"></a>Définition de la fenêtre dans le code

Modifiez ensuite le fichier **MainWindow.cs** et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

Passons en revue quelques-uns des éléments clés.

Tout d’abord, nous avons ajouté quelques _propriétés calculées_ qui agiront comme les prises (comme si la fenêtre était créée dans un fichier. Storyboard ou. XIB):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Celles-ci nous donnent accès aux éléments d’interface utilisateur que nous allons afficher dans la fenêtre. Étant donné que la fenêtre n’est pas augmentée d’un fichier. Storyboard ou. XIB, nous avons besoin d’un moyen de l’instancier (comme nous `MainWindowController` le verrons plus tard dans la classe). C’est ce que fait cette nouvelle méthode de constructeur:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

C’est là que nous allons concevoir la disposition de la fenêtre et placer tous les éléments d’interface utilisateur nécessaires à la création de l’interface utilisateur requise. Avant de pouvoir ajouter des éléments d’interface utilisateur à une fenêtre, il faut qu’un _affichage de contenu_ contienne les éléments suivants:

```csharp
ContentView = new NSView (Frame);
```

Cela crée un affichage de contenu qui remplira la fenêtre. À présent, nous ajoutons le premier élément `NSButton`de l’interface utilisateur, un, à la fenêtre:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

La première chose à noter ici est que, contrairement à iOS, macOS utilise la notation mathématique pour définir le système de coordonnées de la fenêtre. Le point d’origine se trouve dans le coin inférieur gauche de la fenêtre, avec des valeurs qui s’incrémentent vers la droite et vers le coin supérieur droit de la fenêtre. Lorsque nous créons le nouveau `NSButton`, nous prenons en compte la position et la taille à l’écran.

La `AutoresizingMask = NSViewResizingMask.MinYMargin` propriété indique au bouton qu’il doit rester au même emplacement à partir du haut de la fenêtre lorsque la fenêtre est redimensionnée verticalement. Là encore, cette opération est nécessaire, car (0, 0) est en bas à gauche de la fenêtre.

Enfin, la `ContentView.AddSubview (ClickMeButton)` méthode ajoute le `NSButton` à la vue de contenu afin qu’il s’affiche à l’écran lorsque l’application est exécutée et que la fenêtre s’affiche.

Ensuite, une étiquette est ajoutée à la fenêtre qui affiche le nombre de clics effectués sur `NSButton` le.

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
```

Étant donné que MacOS n’a pas d’élément d’interface utilisateur d' _étiquette_ spécifique, nous avons ajouté un style `NSTextField` spécial, non modifiable pour agir en tant qu’étiquette. À l’instar du bouton, la taille et l’emplacement prennent en compte le fait que (0, 0) est en bas à gauche de la fenêtre. La `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` propriété utilise l’opérateur **or** pour combiner deux `NSViewResizingMask` fonctionnalités. L’étiquette reste alors dans le même emplacement que le haut de la fenêtre lorsque la fenêtre est redimensionnée verticalement et diminue et s’agrandit au fur et à mesure que la fenêtre est redimensionnée horizontalement.

Là encore, `ContentView.AddSubview (ClickMeLabel)` la méthode ajoute `NSTextField` le à la vue de contenu afin qu’il s’affiche à l’écran lorsque l’application est exécutée et que la fenêtre est ouverte.


### <a name="adjusting-the-window-controller"></a>Réglage du contrôleur de fenêtre

Étant donné que la conception `MainWindow` du n’est plus chargée à partir d’un fichier. Storyboard ou. XIB, nous devrons apporter des ajustements au contrôleur de fenêtre. Modifiez le fichier **MainWindowController.cs** et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

Nous allons discuter des éléments clés de cette modification.

Tout d’abord, nous définissons une nouvelle `MainWindow` instance de la classe et l’affectons à la `Window` propriété du contrôleur de fenêtre de base:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Nous définissons l’emplacement de la fenêtre de l’écran `CGRect`avec un. Tout comme le système de coordonnées de la fenêtre, l’écran définit (0,0) comme angle inférieur gauche. Ensuite, nous définissons le style de la fenêtre à l’aide de l’opérateur **or** pour combiner `NSWindowStyle` deux fonctionnalités ou plus:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

Les fonctionnalités `NSWindowStyle` suivantes sont disponibles:

- Sans **bordure** : la fenêtre n’a pas de bordure.
- **Intitulé** : la fenêtre a une barre de titre.
- Refermable: la fenêtre a un bouton Fermer et peut être fermée.
- **Miniaturizable** : la fenêtre a un bouton de la miniature et peut être réduite.
- **Redimensionnable** : la fenêtre a un bouton redimensionner et être redimensionnable.
- **Utilitaire** : la fenêtre est une fenêtre de style utilitaire (panneau).
- **DocModal** : si la fenêtre est un panneau, il s’agit d’un document modal au lieu d’un système modal.
- **NonactivatingPanel** : si la fenêtre est un panneau, elle ne devient pas la fenêtre principale.
- **TexturedBackground** : la fenêtre aura un arrière-plan texturé.
- Non mis à l' **échelle** : la fenêtre ne sera pas mise à l’échelle.
- **UnifiedTitleAndToolbar** : les zones de titre et de barre d’outils de la fenêtre sont jointes.
- **HUD** : la fenêtre s’affiche sous la forme d’un panneau d’affichage de tête.
- **FullScreenWindow** : la fenêtre peut passer en mode plein écran.
- **FullSizeContentView** : l’affichage du contenu de la fenêtre se trouve derrière le titre et la zone de la barre d’outils.

Les deux dernières propriétés définissent le _type de mise en mémoire tampon_ pour la fenêtre et si le dessin de la fenêtre est différé. Pour plus d’informations `NSWindows`sur, consultez la [présentation d’Apple à](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) la documentation Windows.

Enfin, étant donné que la fenêtre n’est pas gonflée à partir d’un fichier. Storyboard ou. XIB, nous devons la simuler dans notre MainWindowController.cs `AwakeFromNib` en appelant la méthode Windows:

```csharp
Window.AwakeFromNib ();
```

Cela vous permettra de coder la fenêtre comme une fenêtre standard chargée à partir d’un fichier. Storyboard ou. XIB.


### <a name="displaying-the-window"></a>Affichage de la fenêtre

Une fois le fichier. Storyboard ou. XIB supprimé et les fichiers **MainWindow.cs** et **MainWindowController.cs** modifiés, vous utiliserez la fenêtre comme vous le feriez pour n’importe quelle fenêtre normale créée dans le Interface Builder de Xcode avec un fichier. XIB.

La commande suivante permet de créer une nouvelle instance de la fenêtre et de son contrôleur, et d’afficher la fenêtre à l’écran:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

À ce stade, si l’application est exécutée et que le bouton a cliqué deux fois, les éléments suivants s’affichent:

![Exemple d’exécution d’application](xibless-ui-images/run01.png "Exemple d’exécution d’application")


## <a name="adding-a-code-only-window"></a>Ajout d’une fenêtre de code uniquement

Si vous souhaitez ajouter un code uniquement, une fenêtre xibless à une application Xamarin. Mac existante, cliquez avec le bouton droit sur le projet dans le **panneau solutions** puis sélectionnez **Ajouter** > un**nouveau fichier.** .. Dans la boîte de dialogue **nouveau fichier** , choisissez **Xamarin. Mac** > **cacao fenêtre avec contrôleur**, comme illustré ci-dessous:

![Ajout d’un nouveau contrôleur de fenêtre](xibless-ui-images/add01.png "Ajout d’un nouveau contrôleur de fenêtre")

Comme précédemment, nous allons supprimer le fichier. Storyboard ou. XIB par défaut du projet (dans ce cas **SecondWindow. XIB**) et suivre les étapes de la section [basculer une fenêtre pour utiliser le code](#Switching_a_Window_to_use_Code) ci-dessus pour traiter la définition de la fenêtre en code.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Ajout d’un élément d’interface utilisateur à une fenêtre dans le code

Si une fenêtre a été créée dans le code ou chargée à partir d’un fichier. Storyboard ou. XIB, il peut arriver que vous souhaitiez ajouter un élément d’interface utilisateur à une fenêtre à partir du code. Par exemple :

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

Le code ci-dessus crée `NSButton` un nouveau et l’ajoute `MyWindow` à l’instance de fenêtre pour l’affichage. Fondamentalement, tout élément d’interface utilisateur qui peut être défini dans le Interface Builder de Xcode dans un fichier. Storyboard ou. XIB peut être créé dans le code et affiché dans une fenêtre.


## <a name="defining-the-menu-bar-in-code"></a>Définition de la barre de menus dans le code

En raison des limitations actuelles dans Xamarin. Mac, il n’est pas recommandé de créer la barre de menus de votre application Xamarin`NSMenuBar`. Mac – – dans le code mais de continuer à utiliser le fichier **main. Storyboard** ou **MainMenu. XIB** pour le définir. Cela dit, vous pouvez ajouter et supprimer des menus et des éléments C# de menu dans le code.

Par exemple, modifiez le fichier **AppDelegate.cs** et faites en `DidFinishLaunching` sorte que la méthode ressemble à ce qui suit:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

La version ci-dessus crée un menu de barre d’État à partir du code et l’affiche lorsque l’application est lancée. Pour plus d’informations sur l’utilisation des menus, consultez notre documentation sur les [menus](~/mac/user-interface/menu.md) .


## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail la création d’une interface utilisateur de l’application Xamarin. Mac C# dans le code, par opposition à l’utilisation du Interface Builder de Xcode avec des fichiers. Storyboard ou. XIB.



## <a name="related-links"></a>Liens associés

- [MacXibless (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Présentation de Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
