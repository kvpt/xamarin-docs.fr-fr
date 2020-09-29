---
title: Création d’applications macOS modernes
description: Cet article aborde plusieurs conseils, fonctionnalités et techniques que les développeurs peuvent utiliser pour créer une application macOS moderne dans Xamarin. Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d2a3181360abd7ee7a5124602d0c4186b0a8ec38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430154"
---
# <a name="building-modern-macos-apps"></a>Création d’applications macOS modernes

_Cet article aborde plusieurs conseils, fonctionnalités et techniques que les développeurs peuvent utiliser pour créer une application macOS moderne dans Xamarin. Mac._

<a name="Building-Modern-Looks-with-Modern-Views"></a>

## <a name="building-modern-looks-with-modern-views"></a>Concevoir des présentations modernes avec des vues modernes

Un look moderne inclura une fenêtre moderne et une apparence de barre d’outils comme l’exemple d’application illustré ci-dessous :

[![Exemple d’interface utilisateur d’une application Mac moderne](modern-cocoa-apps-images/content08.png)](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views"></a>

### <a name="enabling-full-sized-content-views"></a>Activation des affichages de contenu de taille complète

Pour ce faire, dans une application Xamarin. Mac, le développeur souhaite utiliser un affichage de _contenu de taille complète_, ce qui signifie que le contenu s’étend sous les zones de l’outil et de la barre de titre et est automatiquement flou par MacOS.

Pour activer cette fonctionnalité dans le code, créez une classe personnalisée pour `NSWindowController` et faites-lui ressembler à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

Cette fonctionnalité peut également être activée dans le Interface Builder de Xcode en sélectionnant la fenêtre et en vérifiant l’affichage du contenu de la **taille complète**:

[![Modification du storyboard principal dans le Interface Builder de Xcode](modern-cocoa-apps-images/content01.png)](modern-cocoa-apps-images/content01.png#lightbox)

Lorsque vous utilisez un affichage de contenu de taille complète, le développeur peut avoir besoin de décaler le contenu sous les zones de titre et de barre d’outils afin que du contenu spécifique (tel que des étiquettes) n’y glisse pas.

Pour compliquer ce problème, les zones titre et barre d’outils peuvent avoir une hauteur dynamique en fonction de l’action actuellement effectuée par l’utilisateur, de la version de macOS installée par l’utilisateur et/ou du matériel Mac sur lequel l’application s’exécute.

Par conséquent, le simple codage en dur du décalage lors de la disposition de l’interface utilisateur ne fonctionnera pas. Le développeur doit adopter une approche dynamique.

Apple a inclus la propriété [observable clé-valeur](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` de la `NSWindow` classe pour récupérer la zone de contenu actuelle dans le code. Le développeur peut utiliser cette valeur pour positionner manuellement les éléments requis lorsque la zone de contenu change.

La meilleure solution consiste à utiliser des classes de disposition et de taille automatiques pour positionner les éléments d’interface utilisateur dans le code ou Interface Builder.

Du code similaire à l’exemple suivant peut être utilisé pour positionner des éléments d’interface utilisateur à l’aide des classes de taille automatique et de taille dans le contrôleur d’affichage de l’application :

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

Ce code crée un stockage pour une contrainte Top qui sera appliquée à une étiquette ( `ItemTitle` ) pour s’assurer qu’elle ne glisse pas dans le titre et la zone de barre d’outils :

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

En substituant la méthode du contrôleur d’affichage `UpdateViewConstraints` , le développeur peut tester pour voir si la contrainte nécessaire a déjà été générée et la créer si nécessaire.

Si une nouvelle contrainte doit être générée, la `ContentLayoutGuide` propriété de la fenêtre à laquelle le contrôle doit être soumis est accessible et castée en `NSLayoutGuide` :

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

La propriété de point d’ancrage du `NSLayoutGuide` est accessible et si elle est disponible, elle est utilisée pour générer une nouvelle contrainte avec la quantité de décalage souhaitée et la nouvelle contrainte devient active pour l’appliquer :

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars"></a>

### <a name="enabling-streamlined-toolbars"></a>Activation des barres d’outils rationalisées

Une fenêtre macOS normale comprend une barre de titre standard au niveau de l’exécution et du bord supérieur de la fenêtre. Si la fenêtre comprend également une barre d’outils, elle est affichée sous cette zone de barre de titre :

[![Une barre d’outils Mac standard](modern-cocoa-apps-images/content02.png)](modern-cocoa-apps-images/content02.png#lightbox)

Lorsque vous utilisez une barre d’outils rationalisée, la zone de titre disparaît et la barre d’outils se déplace dans la position de la barre de titre, en ligne avec les boutons Fermer, réduire et agrandir de la fenêtre :

[![Une barre d’outils Mac rationalisée](modern-cocoa-apps-images/content03.png)](modern-cocoa-apps-images/content03.png#lightbox)

La barre d’outils simplifiée est activée en substituant la `ViewWillAppear` méthode de `NSViewController` et en lui faisant ressembler à ce qui suit :

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Cet effet est généralement utilisé pour les _applications Shoebox_ (une application Windows) comme les cartes, le calendrier, les notes et les préférences système.

<a name="Using-Accessory-View-Controllers"></a>

### <a name="using-accessory-view-controllers"></a>Utilisation de contrôleurs d’affichage accessoire

Selon la conception de l’application, le développeur peut également vouloir compléter la zone de barre de titre avec un contrôleur d’affichage des accessoires qui s’affiche juste en dessous de la zone de titre/barre d’outils pour fournir des contrôles sensibles au contexte à l’utilisateur en fonction de l’activité à laquelle ils sont actuellement engagés :

[![Exemple de contrôleur d’affichage accessoire](modern-cocoa-apps-images/content04.png)](modern-cocoa-apps-images/content04.png#lightbox)

Le contrôleur d’affichage des accessoires sera automatiquement flou et redimensionné par le système sans intervention du développeur.

Pour ajouter un contrôleur d’affichage des accessoires, procédez comme suit :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir et le modifier.
2. Faites glisser un **contrôleur d’affichage personnalisé** dans la hiérarchie de la fenêtre :

    [![Ajout d’un nouveau contrôleur d’affichage personnalisé](modern-cocoa-apps-images/content05.png)](modern-cocoa-apps-images/content05.png#lightbox)
3. Disposition de l’interface utilisateur de la vue des accessoires :

    [![Conception de la nouvelle vue](modern-cocoa-apps-images/content06.png)](modern-cocoa-apps-images/content06.png#lightbox)
4. Exposez la vue des accessoires en tant que **sortie** et toute autre **action** ou **sortie** pour son interface utilisateur :

    [![Ajout de la sortie requise](modern-cocoa-apps-images/content07.png)](modern-cocoa-apps-images/content07.png#lightbox)
5. Enregistrez les modifications.
6. Revenez à Visual Studio pour Mac pour synchroniser les modifications.

Modifiez la `NSWindowController` et faites en sorte qu’elle ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

Les points clés de ce code sont les suivants : la vue est définie sur la vue personnalisée qui a été définie dans Interface Builder et exposée en tant que **sortie**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

Et `LayoutAttribute` qui définit l' _emplacement où_ l’accessoire sera affiché :

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Étant donné que macOS est maintenant entièrement localisé, `Left` les `Right` `NSLayoutAttribute` Propriétés et sont dépréciées et doivent être remplacées par `Leading` et `Trailing` .

<a name="Using-Tabbed-Windows"></a>

### <a name="using-tabbed-windows"></a>Utilisation des fenêtres avec onglets

En outre, le système macOS peut ajouter des contrôleurs d’affichage des accessoires à la fenêtre de l’application. Par exemple, pour créer des fenêtres avec onglets où plusieurs fenêtres de l’application sont fusionnées dans une fenêtre virtuelle :

[![Exemple de fenêtre Mac avec onglets](modern-cocoa-apps-images/content08.png)](modern-cocoa-apps-images/content08.png#lightbox)

En règle générale, le développeur doit utiliser des fenêtres avec onglets dans leurs applications Xamarin. Mac, le système les gère automatiquement comme suit :

- Windows sera automatiquement tabulé lorsque la `OrderFront` méthode sera appelée.
- Quand la méthode est appelée, Windows n’est pas automatiquement tabulé `OrderOut` .
- Dans le code, toutes les fenêtres avec onglets sont toujours considérées comme « visibles », mais les onglets non situés au premier plan sont masqués par le système à l’aide de CoreGraphics.
- Utilisez la `TabbingIdentifier` propriété de `NSWindow` pour regrouper des fenêtres dans des onglets.
- S’il s’agit d’une `NSDocument` application basée sur, plusieurs de ces fonctionnalités sont activées automatiquement (comme le bouton plus ajouté à la barre d’onglets) sans aucune action du développeur.
- Les `NSDocument` applications non basées sur peuvent activer le bouton « plus » dans le groupe d’onglets pour ajouter un nouveau document en substituant la `GetNewWindowForTab` méthode de `NSWindowsController` .

En regroupant tous les éléments, le `AppDelegate` d’une application qui voulait utiliser des fenêtres à onglets basées sur le système peut se présenter comme suit :

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
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

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        }
        #endregion
    }
}
```

Où la `NewDocumentNumber` propriété effectue le suivi du nombre de nouveaux documents créés et la `NewDocument` méthode crée un nouveau document et l’affiche.

Le `NSWindowController` peut se présenter comme suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

Où la `App` propriété statique fournit un raccourci pour accéder à `AppDelegate` . La `SetDefaultDocumentTitle` méthode définit un nouveau titre de documents en fonction du nombre de nouveaux documents créés.

Le code suivant indique à macOS que l’application préfère utiliser Tabs et fournit une chaîne qui permet de regrouper les fenêtres de l’application dans des onglets :

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

Et la méthode override suivante ajoute un bouton plus à la barre d’onglets qui créera un nouveau document lorsque l’utilisateur cliquera dessus :

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation"></a>

### <a name="using-core-animation"></a>Utilisation de l’animation principale

L’animation principale est un moteur de rendu graphique puissant qui est intégré à macOS. L’animation principale a été optimisée pour tirer parti du GPU (unité de traitement graphique) disponible dans le matériel macOS moderne, contrairement à l’exécution des opérations graphiques sur le processeur, ce qui peut ralentir la machine.

`CALayer`, Fourni par l’animation principale, peut être utilisé pour des tâches telles que le défilement rapide et fluide et les animations. L’interface utilisateur d’une application doit être composée de plusieurs sous-affichages et couches pour tirer pleinement parti de l’animation principale.

Un `CALayer` objet fournit plusieurs propriétés qui permettent au développeur de contrôler ce qui est présenté à l’écran par l’utilisateur, par exemple :

- `Content` -Peut être un `NSImage` ou `CGImage` qui fournit le contenu de la couche.
- `BackgroundColor` : Définit la couleur d’arrière-plan de la couche en tant que `CGColor`
- `BorderWidth` -Définit la largeur de la bordure.
- `BorderColor` -Définit la couleur de la bordure.

Pour utiliser des graphiques de base dans l’interface utilisateur de l’application, elle doit utiliser des vues _sauvegardées par couche_ , lesquelles Apple suggère que le développeur doit toujours activer dans la vue de contenu de la fenêtre. De cette façon, toutes les vues enfants héritent automatiquement de la sauvegarde de couche.

En outre, Apple suggère d’utiliser des vues sauvegardées par couche plutôt que d’ajouter une nouvelle `CALayer` sous-couche, car le système gère automatiquement plusieurs des paramètres requis (tels que ceux requis par un affichage de retine).

La sauvegarde de couche peut être activée en définissant le `WantsLayer` d’un `NSView` vers `true` ou à l’intérieur de la Interface Builder de Xcode sous l' **inspecteur d’effets de vue** en vérifiant la couche d' **animation principale**:

[![Inspecteur des effets de vue](modern-cocoa-apps-images/content09.png)](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers"></a>

#### <a name="redrawing-views-with-layers"></a>Redessiner des vues avec des couches

Une autre étape importante lors de l’utilisation de vues sauvegardées de couche dans une application Xamarin. Mac consiste à définir le `LayerContentsRedrawPolicy` du `NSView` sur `OnSetNeedsDisplay` dans le `NSViewController` . Par exemple :

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Si le développeur ne définit pas cette propriété, la vue est redessinée chaque fois que son origine du frame change, ce qui n’est pas souhaité pour des raisons de performances. Si cette propriété a `OnSetNeedsDisplay` la valeur, le développeur doit définir manuellement sur pour `NeedsDisplay` `true` forcer le redessin du contenu.

Lorsqu’une vue est marquée comme modifiée, le système vérifie la `WantsUpdateLayer` propriété de la vue. Si elle retourne `true` , la `UpdateLayer` méthode est appelée, sinon la `DrawRect` méthode de la vue est appelée pour mettre à jour le contenu de la vue.

Apple propose les suggestions suivantes pour la mise à jour du contenu d’un affichage lorsque cela est nécessaire :

- Apple préfère utiliser `UpdateLater` la fonction `DrawRect` si possible, car elle offre une amélioration significative des performances.
- Utilisez le même `layer.Contents` pour les éléments d’interface utilisateur qui ressemblent à.
- Apple préfère également que le développeur compose son interface utilisateur à l’aide d’affichages standard tels que `NSTextField` , à chaque fois que cela est possible.

Pour utiliser `UpdateLayer` , créez une classe personnalisée pour le `NSView` et faites en sorte que le code ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop"></a>

## <a name="using-modern-drag-and-drop"></a>Utilisation du glisser-déplacer moderne

Pour présenter une expérience de glisser-déplacer moderne pour l’utilisateur, le développeur doit adopter le _cheptel de glissement_ dans les opérations de glisser-déplacer de son application. Le fait de faire glisser le cheptel est l’endroit où chaque fichier ou élément déplacé initialement apparaît en tant qu’élément individuel qui se trouve dans le cadre du cheptel (groupe sous le curseur avec le nombre d’éléments) lorsque l’utilisateur continue l’opération glisser.

Si l’utilisateur met fin à l’opération glisser, les éléments individuels se déplacent et retournent à leur emplacement d’origine.

L’exemple de code suivant permet de faire glisser le cheptel sur une vue personnalisée :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

L’effet de l’opération a été obtenu en envoyant chaque élément déplacé à la `BeginDraggingSession` méthode du `NSView` en tant qu’élément distinct dans un tableau.

Lors de l’utilisation d’un `NSTableView` ou `NSOutlineView` d’un, utilisez la `PastboardWriterForRow` méthode de la `NSTableViewDataSource` classe pour démarrer l’opération de glissement :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...

            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

Cela permet au développeur de fournir un individu `NSDraggingItem` pour chaque élément de la table qui est déplacée par opposition à l’ancienne méthode `WriteRowsWith` qui écrit toutes les lignes en tant que groupe unique dans le tableau de montage.

Quand `NSCollectionViews` vous utilisez, utilisez à nouveau la `PasteboardWriterForItemAt` méthode au lieu de la `WriteItemsAt` méthode lorsque le glissement commence.

Le développeur doit toujours éviter de placer des fichiers volumineux sur le montage. Nouveauté de macOS Sierra, les _promesses de fichier_ permettent au développeur de placer des références à des fichiers donnés sur le collage qui seront ensuite comblés lorsque l’utilisateur termine l’opération de déplacement à l’aide des nouvelles `NSFilePromiseProvider` classes et `NSFilePromiseReceiver` .

<a name="Using-Modern-Event-Tracking"></a>

## <a name="using-modern-event-tracking"></a>Utilisation du suivi des événements modernes

Pour un élément d’interface utilisateur (tel que `NSButton` ) qui a été ajouté à une zone de titre ou de barre d’outils, l’utilisateur doit être en mesure de cliquer sur l’élément et de faire en sorte qu’il déclenche un événement normal (comme l’affichage d’une fenêtre contextuelle). Toutefois, étant donné que l’élément se trouve également dans la zone de titre ou de barre d’outils, l’utilisateur doit être en mesure de cliquer et de faire glisser l’élément pour déplacer également la fenêtre.

Pour accomplir cela dans le code, créez une classe personnalisée pour l’élément (tel que `NSButton` ) et substituez l' `MouseDown` événement comme suit :

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

Ce code utilise la `TrackEventsMatching` méthode du `NSWindow` que l’élément d’interface utilisateur est attaché pour intercepter les `LeftMouseUp` `LeftMouseDragged` événements et. Pour un `LeftMouseUp` événement, l’élément d’interface utilisateur répond normalement. Pour l' `LeftMouseDragged` événement, l’événement est passé à la `NSWindow` `PerformWindowDrag` méthode de pour déplacer la fenêtre à l’écran.

L’appel `PerformWindowDrag` de la méthode de la `NSWindow` classe offre les avantages suivants :

- Cela permet à la fenêtre de se déplacer, même si l’application est bloquée (par exemple, lors du traitement d’une boucle profonde).
- Le changement d’espace fonctionne comme prévu.
- La barre d’espaces s’affiche normalement.
- L’alignement et l’alignement de la fenêtre fonctionnent normalement.

<a name="Using-Modern-Container-View-Controls"></a>

## <a name="using-modern-container-view-controls"></a>Utilisation des contrôles modernes de vue de conteneur

macOS Sierra apporte de nombreuses améliorations modernes aux contrôles d’affichage de conteneur existants disponibles dans la version précédente du système d’exploitation.

<a name="Table View Enhancements"></a>

## <a name="table-view-enhancements"></a>Améliorations de l’affichage de table

Le développeur doit toujours utiliser la nouvelle `NSView` version basée sur des contrôles d’affichage de conteneur tels que `NSTableView` . Par exemple :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

Cela permet aux actions de ligne de table personnalisées d’être attachées à des lignes de la table (par exemple, pour supprimer la ligne). Pour activer ce comportement, substituez la `RowActions` méthode de l’une des méthodes `NSTableViewDelegate` suivantes :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

Le statique `NSTableViewRowAction.FromStyle` est utilisé pour créer une nouvelle action de ligne de table pour les styles suivants :

- `Regular` -Effectue une action non destructrice standard telle que la modification du contenu de la ligne.
- `Destructive` -Effectue une action destructrice telle que la suppression de la ligne de la table. Ces actions sont rendues avec un arrière-plan rouge.

<a name="Scroll-View-Enhancements"></a>

## <a name="scroll-view-enhancements"></a>Améliorations de l’affichage de défilement

Lorsque vous utilisez un mode Scroll ( `NSScrollView` ) directement ou dans le cadre d’un autre contrôle (tel que `NSTableView` ), le contenu de l’affichage de défilement peut glisser sous les zones titre et barre d’outils dans une application Xamarin. Mac à l’aide d’une apparence et de vues modernes.

Par conséquent, le premier élément de la zone de contenu de l’affichage de défilement peut être partiellement masqué par le titre et la zone de barre d’outils.

Pour corriger ce problème, Apple a ajouté deux nouvelles propriétés à la `NSScrollView` classe :

- `ContentInsets` -Permet au développeur de fournir un `NSEdgeInsets` objet définissant le décalage qui sera appliqué au haut de la vue de défilement.
- `AutomaticallyAdjustsContentInsets` -Si `true` l’affichage de défilement gère automatiquement le `ContentInsets` pour le développeur.

En utilisant le `ContentInsets` développeur, vous pouvez ajuster le démarrage de l’affichage de défilement pour permettre l’inclusion d’accessoires tels que :

- Indicateur de tri comme celui indiqué dans l’application de messagerie.
- Champ de recherche.
- Bouton Actualiser ou mettre à jour.

<a name="Auto-Layout-and-Localization-in-Modern-Apps"></a>

## <a name="auto-layout-and-localization-in-modern-apps"></a>Mise en page et localisation automatiques dans les applications modernes

Apple intègre plusieurs technologies dans Xcode qui permettent au développeur de créer facilement une application macOS internationalisée. Xcode permet désormais au développeur de séparer le texte accessible aux utilisateurs de la conception de l’interface utilisateur de l’application dans ses fichiers de Storyboard et fournit des outils pour maintenir cette séparation si l’interface utilisateur change.

Pour plus d’informations, consultez le [Guide d’internationalisation et de localisation](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)d’Apple.

<a name="Implementing-Base-Internationalization"></a>

### <a name="implementing-base-internationalization"></a>Implémentation de l’internationalisation de base

En implémentant l’internationalisation de base, le développeur peut fournir un fichier de Storyboard unique pour représenter l’interface utilisateur de l’application et séparer toutes les chaînes accessibles à l’utilisateur.

Lorsque le développeur crée le fichier de Storyboard initial (ou les fichiers) qui définissent l’interface utilisateur de l’application, il est intégré à l’internationalisation de base (le langage que le développeur parle).

Ensuite, le développeur peut exporter les localisations et les chaînes d’internationalisation de base (dans la conception de l’interface utilisateur de la table de montage séquentiel) qui peuvent être traduites dans plusieurs langues.

Plus tard, ces localisations peuvent être importées et Xcode génère les fichiers de chaîne spécifiques au langage pour le Storyboard.

<a name="Implementing-Auto-Layout-to-Support-Localization"></a>

### <a name="implementing-auto-layout-to-support-localization"></a>Implémentation de la disposition automatique pour prendre en charge la localisation

Étant donné que les versions localisées des valeurs de chaîne peuvent avoir des tailles et/ou des sens de lecture très différentes, le développeur doit utiliser la disposition automatique pour positionner et dimensionner l’interface utilisateur de l’application dans un fichier de Storyboard.

Apple suggérant d’exécuter les opérations suivantes :

- **Supprimer les contraintes de largeur fixe** : tous les affichages basés sur du texte doivent être autorisés à être redimensionnés en fonction de leur contenu. La vue largeur fixe peut rogner son contenu dans des langues spécifiques.
- **Utiliser des tailles de contenu intrinsèques** : par défaut, les vues textuelles s’ajustent automatiquement en fonction de leur contenu. Pour une vue basée sur du texte dont le dimensionnement n’est pas correct, sélectionnez-les dans le Interface Builder de Xcode, puis choisissez **modifier**la  >  **taille pour ajuster le contenu**.
- **Appliquer les attributs de début et de fin** : dans la mesure où la direction du texte peut changer en fonction de la langue de l’utilisateur, utilisez les `Leading` attributs New et `Trailing` Constraint, par opposition aux `Right` attributs et existants `Left` . `Leading` et `Trailing` s’ajuste automatiquement en fonction de la direction des langues.
- **Épingler des affichages à des vues adjacentes** : cela permet de repositionner et de redimensionner les vues en fonction de la langue sélectionnée.
- **Ne définissez pas de tailles minimale et maximale Windows** : autorisez les fenêtres à changer de taille, car la langue sélectionnée redimensionne leurs zones de contenu.
- **Les modifications de disposition de test en continu** pendant le développement au niveau de l’application doivent être testées en permanence dans différentes langues. Pour plus d’informations, consultez la documentation sur les tests d’Apple de [votre application internationale](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) .
- **Utiliser NSStackViews pour épingler des affichages ensemble**  -  `NSStackViews` autorise le décalage et la croissance de leur contenu de manière prévisible et la taille de la modification du contenu en fonction de la langue sélectionnée.

<a name="Localizing-in-Xcodes-Interface-Builder"></a>

### <a name="localizing-in-xcodes-interface-builder"></a>Localisation dans le Interface Builder de Xcode

Apple a fourni plusieurs fonctionnalités dans le Interface Builder de Xcode que le développeur peut utiliser lors de la conception ou de la modification de l’interface utilisateur d’une application pour prendre en charge la localisation. La section **orientation du texte** de l’inspecteur d' **attribut** permet au développeur de fournir des indications sur l’utilisation et la mise à jour de la direction sur une vue de type texte (telle que `NSTextField` ) :

[![Options d’orientation du texte](modern-cocoa-apps-images/content10.png)](modern-cocoa-apps-images/content10.png#lightbox)

Il existe trois valeurs possibles pour la **direction du texte**:

- **Natural** -la disposition est basée sur la chaîne assignée au contrôle.
- **De gauche à droite** : la disposition est toujours forcée de gauche à droite.
- De **droite à gauche** : la disposition est toujours forcée de droite à gauche.

Il existe deux valeurs possibles pour la **disposition**:

- **De gauche à droite** : la disposition est toujours de gauche à droite.
- **De droite à gauche** : la disposition est toujours de droite à gauche.

En règle générale, elles ne doivent pas être modifiées, sauf si un alignement spécifique est requis.

La propriété **Mirror** indique au système d’inverser des propriétés de contrôle spécifiques (telles que la position de l’image de la cellule). Il a trois valeurs possibles :

- **Automatiquement** -la position change automatiquement en fonction de la direction de la langue sélectionnée.
- **Dans l’interface de droite à gauche** , la position sera uniquement modifiée dans les langues de droite à gauche.
- **Jamais** -la position ne changera jamais.

Si le développeur a spécifié le **Centre**, le **justifier** ou l’alignement **complet** sur le contenu d’une vue textuelle, ceux-ci ne seront jamais retournés en fonction de la langue sélectionnée.

Avant macOS Sierra, les contrôles créés dans le code ne seraient pas mis en miroir automatiquement. Le développeur devait utiliser du code comme celui qui suit pour gérer la mise en miroir :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

Où les `Alignment` et `ImagePosition` sont définis en fonction du `UserInterfaceLayoutDirection` du contrôle.

macOS Sierra ajoute plusieurs nouveaux constructeurs pratiques (via la méthode statique `CreateButton` ) qui prennent plusieurs paramètres (tels que le titre, l’image et l’action) et se reflètent automatiquement. Par exemple :

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances"></a>

## <a name="using-system-appearances"></a>Utilisation des apparences du système

Les applications macOS modernes peuvent adopter une nouvelle apparence d’interface sombre qui fonctionne bien pour la création d’images, la modification ou la présentation d’applications :

[![Exemple d’interface utilisateur de fenêtre Mac sombre](modern-cocoa-apps-images/content11.png)](modern-cocoa-apps-images/content11.png#lightbox)

Pour ce faire, vous pouvez ajouter une ligne de code avant que la fenêtre ne s’affiche. Par exemple :

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

La `GetAppearance` méthode statique de la `NSAppearance` classe est utilisée pour obtenir une apparence nommée à partir du système (dans ce cas `NSAppearance.NameVibrantDark` ).

Apple propose les suggestions suivantes pour l’utilisation des apparences du système :

- Préférer les couleurs nommées aux valeurs codées en dur (telles que `LabelColor` et `SelectedControlColor` ).
- Utilisez le style de contrôle standard du système dans la mesure du possible.

Une application macOS qui utilise les apparences du système fonctionnera automatiquement correctement pour les utilisateurs qui ont activé les fonctionnalités d’accessibilité à partir de l’application préférences système. Par conséquent, Apple suggère que le développeur doit toujours utiliser les apparences du système dans leurs applications macOS.

<a name="Designing-UIs-with-Storyboards"></a>

## <a name="designing-uis-with-storyboards"></a>Conception d’interfaces utilisateur avec des storyboards

Les storyboards permettent au développeur de concevoir non seulement les éléments individuels qui composent l’interface utilisateur d’une application, mais aussi de visualiser et de concevoir le workflow de l’interface utilisateur et la hiérarchie des éléments donnés.

Les contrôleurs permettent au développeur de collecter des éléments dans une unité de composition et SEGUES abstraite et de supprimer le « code de type glue » classique requis pour se déplacer dans la hiérarchie d’affichage :

[![Modification de l’interface utilisateur dans le Interface Builder de Xcode](modern-cocoa-apps-images/content12.png)](modern-cocoa-apps-images/content12.png#lightbox)

Pour plus d’informations, consultez notre [Introduction à la documentation sur les storyboards](~/mac/platform/storyboards/index.md) .

Il existe de nombreuses instances dans lesquelles une scène donnée définie dans une table de montage séquentiel requiert des données d’une scène précédente dans la hiérarchie d’affichage. Apple propose les suggestions suivantes pour transmettre des informations entre les scènes :

- Les dependancies de données doivent toujours être en cascade vers le bas dans la hiérarchie.
- Évitez le codage des dependancies structurels de l’interface utilisateur, car cela limite la flexibilité de l’interface utilisateur.
- Utilisez les interfaces C# pour fournir des données génériques dependancies.

Le contrôleur d’affichage qui joue le rôle de la source du segue peut substituer la `PrepareForSegue` méthode et effectuer toute initialisation requise (par exemple, passer des données) avant l’exécution du segue pour afficher le contrôleur d’affichage cible. Par exemple :

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Pour plus d’informations, consultez notre documentation [SEGUES](~/mac/platform/storyboards/indepth.md#Segues) .

<a name="Propagating-Actions"></a>

## <a name="propagating-actions"></a>Propager des actions

En fonction de la conception de l’application macOS, il peut arriver que le meilleur gestionnaire pour une action sur un contrôle d’interface utilisateur soit situé dans un autre emplacement de la hiérarchie d’interface utilisateur. C’est généralement le cas des menus et des éléments de menu qui vivent dans leur propre scène, séparés du reste de l’interface utilisateur de l’application.

Pour gérer cette situation, le développeur peut créer une action personnalisée et passer l’action vers le haut de la chaîne de répondeur. Pour plus d’informations, consultez notre documentation [sur l’utilisation des actions de fenêtre personnalisée](~/mac/user-interface/menu.md) .

<a name="Modern-Mac-Features"></a>

## <a name="modern-mac-features"></a>Fonctionnalités Mac modernes

Apple a inclus plusieurs fonctionnalités accessibles aux utilisateurs dans macOS Sierra qui permettent au développeur de tirer le meilleur parti de la plate-forme Mac, par exemple :

- **NSUserActivity** : permet à l’application de décrire l’activité dans laquelle l’utilisateur participe actuellement. `NSUserActivity` a été initialement créé pour prendre en charge le transfert, où une activité démarrée sur l’un des appareils de l’utilisateur peut être récupérée et poursuivie sur un autre appareil. `NSUserActivity` fonctionne de la même manière dans macOS que dans iOS. pour plus d’informations, consultez notre [Introduction à](~/ios/platform/handoff.md) la documentation iOS sur le transfert.
- **Siri sur Mac** -Siri utilise l’activité en cours ( `NSUserActivity` ) pour fournir le contexte aux commandes qu’un utilisateur peut émettre.
- **Restauration** de l’État-lorsque l’utilisateur quitte une application sur MacOS, puis le relance, l’application est automatiquement retournée à son état précédent. Le développeur peut utiliser l’API de restauration d’État pour encoder et restaurer les États temporaires de l’interface utilisateur avant que l’interface utilisateur ne soit affichée à l’utilisateur. Si l’application est `NSDocument` basée, la restauration de l’État est gérée automatiquement. Pour activer la restauration de l’État pour les `NSDocument` applications non basées sur, affectez `Restorable` à la classe de la classe la valeur `NSWindow` `true` .
- **Documents dans le Cloud** : avant de MacOS Sierra, une application devait explicitement s’abonner à l’utilisation de documents dans le lecteur icloud de l’utilisateur. Dans macOS Sierra les dossiers **Bureau** et **documents** de l’utilisateur peuvent être synchronisés automatiquement avec leur lecteur icloud par le système. Par conséquent, des copies locales de documents peuvent être supprimées pour libérer de l’espace sur l’ordinateur de l’utilisateur. `NSDocument` les applications basées sur s’en chargent automatiquement ce changement. Tous les autres types d’applications devront utiliser un `NSFileCoordinator` pour synchroniser la lecture et l’écriture de documents.

<a name="Summary"></a>

## <a name="summary"></a>Récapitulatif

Cet article a présenté plusieurs conseils, fonctionnalités et techniques que les développeurs peuvent utiliser pour créer une application macOS moderne dans Xamarin. Mac.

## <a name="related-links"></a>Liens associés

- [Exemples macOS](/samples/browse/?products=xamarin&term=Xamarin.Mac)