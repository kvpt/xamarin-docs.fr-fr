---
title: Création de contrôles personnalisés dans Xamarin. Mac
description: Ce document décrit comment créer des contrôles personnalisés dans Xamarin. Mac. Il montre comment générer le contrôle personnalisé, suivre son état, dessiner son interface, répondre aux entrées d’utilisateur et utiliser le contrôle dans une application.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 537816213208ed6e71f0986558c9a94a327759e2
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227909"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Création de contrôles personnalisés dans Xamarin. Mac

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes contrôles utilisateur qu’un développeur qui travaille en *objective-C*, *SWIFT* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos contrôles utilisateur (ou éventuellement les créer directement dans C# le code).

Tandis que macOS offre une multitude de contrôles utilisateur intégrés, il peut arriver que vous ayez besoin de créer un contrôle personnalisé pour fournir des fonctionnalités non fournies prêtes à l’emploi ou correspondant à un thème d’interface utilisateur personnalisé (par exemple, une interface de jeu).

[![](custom-controls-images/intro01.png "Exemple de contrôle d’interface utilisateur personnalisé")](custom-controls-images/intro01.png#lightbox)

Dans cet article, nous allons aborder les principes fondamentaux de la création d’un contrôle d’interface utilisateur personnalisé réutilisable dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Présentation des contrôles personnalisés

Comme indiqué ci-dessus, il peut arriver que vous deviez créer un contrôle d’interface utilisateur personnalisé et réutilisable pour fournir des fonctionnalités uniques pour l’interface utilisateur de votre application Xamarin. Mac ou pour créer un thème d’interface utilisateur personnalisé (par exemple, une interface de jeu).

Dans ces situations, vous pouvez facilement hériter `NSControl` de et créer un outil personnalisé qui peut être ajouté à l’interface utilisateur de votre C# application par le biais du code ou de la Interface Builder de Xcode. Le fait d’hériter de `NSControl` votre contrôle personnalisé aura automatiquement toutes les fonctionnalités standard d’un contrôle d’interface utilisateur intégré (tel que `NSButton`).

Si votre contrôle d’interface utilisateur personnalisé affiche simplement des informations (comme un graphique personnalisé et un outil graphique), vous pouvez hériter `NSView` de au `NSControl`lieu de.

Quelle que soit la classe de base utilisée, les étapes de base pour créer un contrôle personnalisé sont les mêmes.

Dans cet article, vous allez créer un composant personnalisé de commutateur Flip qui fournit un thème d’interface utilisateur unique et un exemple de création d’un contrôle d’interface utilisateur personnalisé entièrement fonctionnel.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Génération du contrôle personnalisé

Étant donné que le contrôle personnalisé que nous créons répond à l’entrée d’utilisateur (clics du bouton gauche de la souris), nous `NSControl`allons hériter de. De cette façon, notre contrôle personnalisé aura automatiquement toutes les fonctionnalités standard qu’un contrôle d’interface utilisateur intégré possède et répondra comme un contrôle macOS standard.

Dans Visual Studio pour Mac, ouvrez le projet Xamarin. Mac pour lequel vous souhaitez créer un contrôle d’interface utilisateur personnalisé (ou en créer un). Ajoutez une nouvelle classe et appelez- `NSFlipSwitch`la:

[![](custom-controls-images/custom01.png "Ajout d’une nouvelle classe")](custom-controls-images/custom01.png#lightbox)

Modifiez ensuite la `NSFlipSwitch.cs` classe et faites en sorte qu’elle ressemble à ce qui suit:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

La première chose à noter à propos de notre classe personnalisée dans le fait que nous `NSControl` héritons de la commande **Register** et que vous l’utilisez pour exposer cette classe à l’interface Builder objective-C et Xcode:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Dans les sections suivantes, nous allons examiner en détail le reste du code ci-dessus.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Suivi de l’état du contrôle

Étant donné que notre contrôle personnalisé est un commutateur, nous avons besoin d’un moyen de suivre l’état activé/désactivé du commutateur. Nous la gérerons avec le code suivant `NSFlipSwitch`dans:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

Lorsque l’état du commutateur change, nous avons besoin d’un moyen de mettre à jour l’interface utilisateur. Pour ce faire, nous forçant le contrôle à redessiner `NeedsDisplay = true`son interface utilisateur avec.

Si notre contrôle nécessitait plus qu’un seul État on/off (par exemple un commutateur à plusieurs États avec 3 positions), nous aurions pu utiliser une énumération pour suivre l’État. Pour notre exemple, une valeur **bool** simple fait.

Nous avons également ajouté une méthode d’assistance pour permuter l’état du basculement entre activé et désactivé:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Plus tard, nous développerons cette classe d’assistance pour informer l’appelant lorsque l’état des commutateurs a changé.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Dessin de l’interface du contrôle

Nous allons utiliser les routines principales de dessin graphique pour dessiner l’interface utilisateur de votre contrôle personnalisé au moment de l’exécution. Avant de pouvoir le faire, nous devons activer les couches pour notre contrôle. Nous faisons cela avec la méthode privée suivante:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Cette méthode est appelée à partir de chacun des constructeurs du contrôle pour garantir que le contrôle est correctement configuré. Exemple :

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Ensuite, nous devons remplacer la `DrawRect` méthode et ajouter les routines principales graphiques pour dessiner le contrôle:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Nous allons ajuster la représentation visuelle du contrôle lorsque son état change (par exemple, en allant de **on** à **off**). Chaque fois que l’état change, nous pouvons utiliser `NeedsDisplay = true` la commande pour forcer le contrôle à redessiner pour le nouvel État.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Réponse aux entrées d’utilisateur

Il existe deux méthodes de base pour ajouter une entrée d’utilisateur à notre contrôle personnalisé: **Remplacer les routines de gestion de la souris** ou les détecteurs de **mouvement**. La méthode que nous utilisons dépend de la fonctionnalité requise par notre contrôle.

> [!IMPORTANT]
> Pour tout contrôle personnalisé que vous créez, vous devez utiliser des **méthodes override** _ou_ des détecteurs de **mouvement**, mais pas les deux en même temps qu’ils peuvent entrer en conflit.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Gestion des entrées d’utilisateur avec des méthodes override

Les objets qui héritent `NSControl` de `NSView`(ou) ont plusieurs méthodes override pour gérer l’entrée de la souris ou du clavier. Pour notre exemple de contrôle, nous voulons retourner l’état du commutateur entre **on** et **off** lorsque l’utilisateur clique sur le contrôle avec le bouton gauche de la souris. Nous pouvons ajouter les méthodes de substitution suivantes à la `NSFlipSwitch` classe pour gérer cela:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

Dans le code ci-dessus, nous `FlipSwitchState` appelons la méthode (définie ci-dessus) pour retourner l’état activé/désactivé du `MouseDown` commutateur dans la méthode. Cela forcera également le redessin du contrôle pour refléter l’état actuel.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Gestion des entrées d’utilisateur avec des détecteurs de mouvement

Si vous le souhaitez, vous pouvez utiliser des détecteurs de mouvement pour gérer l’interaction de l’utilisateur avec le contrôle. Supprimez les remplacements ajoutés ci-dessus, `Initialize` modifiez la méthode et faites-lui ressembler à ce qui suit:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

Ici, nous créons un nouveau `NSClickGestureRecognizer` et appelons notre `FlipSwitchState` méthode pour modifier l’état du commutateur quand l’utilisateur clique dessus avec le bouton gauche de la souris. La `AddGestureRecognizer (click)` méthode ajoute le module de reconnaissance de mouvement au contrôle.

Là encore, la méthode que nous utilisons dépend de ce que nous essayons d’accomplir avec notre contrôle personnalisé. Si nous avons besoin d’un accès de niveau faible à l’interaction de l’utilisateur, utilisez les méthodes override. Si nous avons besoin de fonctionnalités prédéfinies, telles que des clics de souris, utilisez des détecteurs de mouvement.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Réponse aux événements de changement d’État

Lorsque l’utilisateur modifie l’état de notre contrôle personnalisé, nous avons besoin d’un moyen de répondre au changement d’État du code (par exemple, en cas de clic sur un bouton personnalisé).

Pour fournir cette fonctionnalité, modifiez la `NSFlipSwitch` classe et ajoutez le code suivant:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null)
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

Modifiez ensuite la `FlipSwitchState` méthode et faites en sorte qu’elle ressemble à ce qui suit:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Tout d’abord, nous `ValueChanged` fournissons un événement auquel nous pouvons ajouter un C# gestionnaire dans le code afin de pouvoir effectuer une action lorsque l’utilisateur modifie l’état du commutateur.

Deuxièmement, étant donné que notre contrôle personnalisé hérite de `NSControl`, il a automatiquement une **action** qui peut être assignée dans le Interface Builder de Xcode. Pour appeler cette **action** lorsque l’état change, nous utilisons le code suivant:

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Tout d’abord, nous vérifions si une **action** a été assignée au contrôle. Ensuite, nous appelons l' **action** si elle a été définie.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Utilisation du contrôle personnalisé

Une fois notre contrôle personnalisé entièrement défini, nous pouvons l’ajouter à l’interface utilisateur de l’application Xamarin. C# Mac à l’aide du code ou de la Interface Builder de Xcode.

Pour ajouter le contrôle à l’aide de Interface Builder, effectuez tout d’abord une génération propre du projet Xamarin. Mac, puis `Main.storyboard` double-cliquez sur le fichier pour l’ouvrir dans Interface Builder pour modification:

[![](custom-controls-images/custom02.png "Modification de la table de montage séquentiel dans Xcode")](custom-controls-images/custom02.png#lightbox)

Ensuite, faites glisser `Custom View` un dans la conception de l’interface utilisateur:

[![](custom-controls-images/custom03.png "Sélection d’une vue personnalisée dans la bibliothèque")](custom-controls-images/custom03.png#lightbox)

Avec la vue personnalisée toujours sélectionnée, basculez vers l' **inspecteur d’identité** et remplacez la **classe** de `NSFlipSwitch`la vue par:

[![](custom-controls-images/custom04.png "Définition de la classe de l’affichage")](custom-controls-images/custom04.png#lightbox)

Basculez vers **l’éditeur** de l’Assistant et créez une **sortie** pour le contrôle personnalisé (en veillant à le `ViewController.h` lier au fichier et `.m` non au fichier):

[![](custom-controls-images/custom05.png "Configuration d’une nouvelle prise")](custom-controls-images/custom05.png#lightbox)

Enregistrez vos modifications, revenez à Visual Studio pour Mac et autorisez la synchronisation des modifications. Modifiez le `ViewController.cs` fichier et `ViewDidLoad` faites en sorte que la méthode ressemble à ce qui suit:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
```

Ici, nous répondons à `ValueChanged` l’événement que nous avons défini `NSFlipSwitch` ci-dessus sur la classe et écrivons la **valeur** actuelle lorsque l’utilisateur clique sur le contrôle.

Si vous le souhaitez, nous pourrions revenir à Interface Builder et définir une **action** sur le contrôle:

[![](custom-controls-images/custom06.png "Configuration d’une nouvelle action")](custom-controls-images/custom06.png#lightbox)

Une fois encore, `ViewController.cs` modifiez le fichier et ajoutez la méthode suivante:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Vous devez utiliser l' **événement** ou définir une **action** dans Interface Builder, mais vous ne devez pas utiliser les deux méthodes en même temps ou elles peuvent entrer en conflit.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail la création d’un contrôle d’interface utilisateur personnalisé réutilisable dans une application Xamarin. Mac. Nous avons vu comment dessiner l’interface utilisateur des contrôles personnalisés, les deux principales façons de répondre à la souris et aux entrées utilisateur et comment exposer le nouveau contrôle aux actions de l’Interface Builder de Xcode.

## <a name="related-links"></a>Liens associés

- [MacCustomControl (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Gestion des événements de souris](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
