---
title: Utilisation des Storyboards dans Xamarin.Mac
description: Ce document explique comment travailler avec des storyboards dans Xamarin.Mac, examiner comment les charger à partir de code, le cycle de vie de contrôleur d’affichage, la chaîne de répondeur, Enchaînements, contrôleurs de fenêtre, les modules de reconnaissance de mouvement et bien plus encore.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: e24b448bedc60a537bfcd4a5bfbdbe9562163818
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865935"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Utilisation des Storyboards dans Xamarin.Mac

Une table de montage définit l’ensemble de l’interface utilisateur pour une application donnée est divisée en une vue d’ensemble fonctionnelle de ses contrôleurs d’affichage. Dans l’Interface Builder de Xcode, chacun de ces contrôleurs se trouve dans son propre scène.

[![](indepth-images/intro01.png "Un storyboard dans Interface Builder de Xcode")](indepth-images/intro01.png#lightbox)

La table de montage séquentiel est un fichier de ressources (avec les extensions de `.storyboard`) qui obtient inclus dans le Xamarin.Mac bundle de l’application lorsqu’elle est compilée et expédié. Pour définir l’animation de départ pour votre application, modifiez-le de `Info.plist` fichier et sélectionnez le **Interface principale** dans la zone de liste déroulante : 

[![](indepth-images/sb01.png "L’éditeur Info.plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Chargement à partir du Code

Il peut arriver que vous deviez charger un Storyboard spécifique à partir du code et créer un contrôleur d’affichage manuellement. Vous pouvez utiliser le code suivant pour effectuer cette action :

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

Le `FromName` charge le fichier de Storyboard portant le nom spécifié qui a été inclus dans le bundle de l’application. Le `InstantiateControllerWithIdentifier` crée une instance de contrôleur d’affichage avec l’identité donnée. Vous définissez l’identité dans Interface Builder de Xcode lors de la conception de l’interface utilisateur :

[![](indepth-images/sb02.png "Définition de l’ID de Storyboard")](indepth-images/sb02.png#lightbox)

Si vous le souhaitez, vous pouvez utiliser la `InstantiateInitialController` méthode pour charger le contrôleur d’affichage qui a été attribué le contrôleur Initial dans Interface Builder :

[![](indepth-images/sb03.png "Définition du contrôleur initial")](indepth-images/sb03.png#lightbox)

Elle est marquée par le **Point d’entrée de table de montage séquentiel** et la flèche ouvertes ci-dessus.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Contrôleurs d’affichage

Contrôleurs d’affichage définissent les relations entre une vue donnée d’informations dans une application Mac et le modèle de données qui fournit cette information. Chaque scène de niveau supérieur dans le Storyboard représente un contrôleur d’affichage dans le code de l’application Xamarin.Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>Le cycle de vie de contrôleur d’affichage

Plusieurs nouvelles méthodes ont été ajoutés à la `NSViewController` classe pour prendre en charge des Storyboards dans macOS. Plus important encore, sont que les méthodes de suivi permet de répondre au cycle de vie de la vue qui est contrôlée par le contrôleur d’affichage donné :

- `ViewDidLoad` : Cette méthode est appelée lorsque la vue est chargée à partir du fichier de la table de montage séquentiel.
- `ViewWillAppear` : Cette méthode est appelée juste avant que la vue est affichée sur l’écran.
- `ViewDidAppear` : Cette méthode est appelée directement après l’affichage de la vue à l’écran.
- `ViewWillDisappear` : Cette méthode est appelée juste avant que la vue est supprimée à partir de l’écran.
- `ViewDidDisappear` : Cette méthode est appelée directement après que la vue a été supprimée de l’écran.
- `UpdateViewConstraints` : Cette méthode est appelée lorsque les contraintes qui définissent une vue automatiquement disposition position et la taille doivent être mis à jour.
- `ViewWillLayout` : Cette méthode est appelée juste avant que les sous-affichages de cette vue sont disposés à l’écran.
- `ViewDidLayout` : Cette méthode est appelée directement après que les sous-affichages de vue sont disposés à l’écran.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>La chaîne de répondeur

En outre, `NSViewControllers` font désormais partie de la fenêtre _répondeur chaîne_:

[![](indepth-images/vc01.png "La chaîne de répondeur")](indepth-images/vc01.png#lightbox)

Et par conséquent, ils sont câblé à distance pour recevoir et répondre aux événements tels que les sélections d’élément de menu couper, copier et coller. Cet accès contrôleur d’affichage par câble automatique se produit uniquement sur les applications qui s’exécutent sur macOS Sierra (10.12) et versions supérieures.

<a name="Containment" />

### <a name="containment"></a>Imbrication

Dans les tables de montage séquentiel, les contrôleurs d’affichage (par exemple, le contrôleur d’affichage fractionné et le contrôleur d’affichage onglet) peuvent désormais implémenter _relation contenant-contenu_, de sorte qu’ils peuvent « contient » autres sub contrôleurs d’affichage :

[![](indepth-images/vc02.png "Un exemple de relation contenant-contenu de vue de contrôleur")](indepth-images/vc02.png#lightbox)

Contrôleurs d’affichage enfants contiennent des méthodes et propriétés afin de les associer automatiquement à leur contrôleur de vue Parent et à travailler dans l’affichage et suppression de vues à partir de l’écran.

Tous les contrôleurs d’affichage conteneur intégrées à macOS ont une disposition spécifique qui Apple vous recommande de suivre si la création de vos propres contrôleurs d’affichage conteneur personnalisé :

[![](indepth-images/vc03.png "La disposition de contrôleur d’affichage")](indepth-images/vc03.png#lightbox)

Le contrôleur d’affichage de Collection contient un tableau d’éléments de vue de Collection, chacun d'entre eux contenant un ou plusieurs contrôleurs d’affichage qui contiennent leurs propres vues.

<a name="Segues" />

## <a name="segues"></a>Segues

Est parfait pour fournir les relations entre tous les scènes qui définissent l’interface utilisateur de votre application. Si vous êtes familiarisé avec l’utilisation dans des Storyboards dans iOS, vous savez qu’Enchaînements pour iOS définissent généralement des transitions entre les vues de plein écran. Cela diffère macOS, Segues définissent généralement «[relation contenant-contenu](#Containment)», où une scène est l’enfant d’un parent de scène.

Dans macOS, la plupart des applications ont tendance à grouper leurs vues ensemble au sein de la même fenêtre à l’aide des éléments d’interface utilisateur tels que les onglets et les affichages fractionnés. Contrairement à iOS, où les vues doivent être transférées sur et hors écran, en raison de la limite physique affiche l’espace.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>Segues de présentation

Étant donné les tendances de macOS vers la relation contenant-contenu, il existe des situations où _Segues de présentation_ sont utilisés, tels que Windows modale, vues de la feuille et Popovers. types de macOS fournit segue intégrés suivants :

- **Afficher** -affiche la cible du Segue sous la forme d’une fenêtre non modale. Par exemple, utilisez ce type de Segue pour présenter une autre instance d’une fenêtre de Document dans votre application.
- **Modal** -présente la cible du Segue comme une fenêtre modale. Par exemple, utilisez ce type de Segue pour présenter la fenêtre de préférences pour votre application.
- **Feuille** -présente la cible du Segue comme une feuille attaché à la fenêtre parente. Par exemple, utilisez ce type de segue à présenter une recherche et la feuille de remplacer.
- **Menu** -présente la cible du Segue, comme dans une fenêtre de menu. Par exemple, utiliser ce type de Segue pour présenter des options lorsque l’utilisateur clique sur un élément d’interface utilisateur.
- **Personnalisé** -présente la cible du Segue à l’aide d’un Type de Segue personnalisé défini par le développeur. Consultez le [Enchaînements de personnalisée création](#Creating-Custom-Segues) section ci-dessous pour plus d’informations.

Lorsque vous utilisez Segues de présentation, vous pouvez remplacer le `PrepareForSegue` méthode du contrôleur d’affichage parent pour les variables et de présentation pour initialiser et fournir toutes les données pour le contrôleur d’affichage en cours de présentation.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>Déclenché Segues

Segues déclenchées permettent de spécifier des Segues nommée (via leurs **identificateur** propriété dans l’Interface Builder) et demandez-lui déclenchée par des événements tels que l’utilisateur clique sur un bouton ou en appelant le `PerformSegue` méthode dans le code :

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

L’ID de Segue est défini à l’intérieur Interface Builder de Xcode lorsque vous disposez des interface utilisateur de l’application :

[![](indepth-images/sg02.png "Entrer un nom de Segue")](indepth-images/sg02.png#lightbox)

Dans le contrôleur d’affichage qui joue le rôle de la source du Segue, vous devez substituer la `PrepareForSegue` (méthode) et effectuer toute initialisation requise avant le Segue est exécuté et le contrôleur d’affichage spécifié s’affiche :

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

Si vous le souhaitez, vous pouvez remplacer le `ShouldPerformSegue` (méthode) et contrôle ou non le Segue est réellement exécuté via C# code. Pour les contrôleurs d’affichage manuellement présenté, appeler leurs `DismissController` méthode pour les supprimer à partir de l’affichage lorsqu’ils ne sont plus nécessaires.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Création personnalisée Segues

Il peut arriver lorsque votre application nécessite un type de Segue non fourni par les Segues dans build définie dans macOS. Si c’est le cas, vous pouvez créer un Segue personnalisés qui peuvent être attribuées dans Interface Builder de Xcode lorsque vous présentez à l’interface utilisateur de votre application.

Par exemple, pour créer un nouveau type de Segue qui remplace le contrôleur d’affichage actuel à l’intérieur d’une fenêtre (au lieu d’ouvrir la scène cible dans une nouvelle fenêtre), nous pouvons utiliser le code suivant :

```csharp
using System;
using AppKit;
using Foundation;

namespace OnCardMac
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

            // Swap the controllers
            source.View.Window.ContentViewController = destination;

            // Release memory
            source.RemoveFromParentViewController ();
        }
        #endregion

    }
        
}
```

Voici quelques points à noter ici :

- Nous utilisons le `Register` attribut pour exposer cette classe à Objective-C/macOS.
- Nous remplaçons le `Perform` méthode pour effectuer l’action de notre Segue personnalisé.
- Nous sommes en remplaçant la fenêtre `ContentViewController` contrôleur avec celui qui est défini par la cible (destination) du Segue.
- Nous allons supprimer le contrôleur d’affichage d’origine afin de libérer la mémoire à l’aide du `RemoveFromParentViewController` (méthode).

Pour utiliser ce nouveau type de Segue dans Interface Builder de Xcode, nous avons besoin compiler l’application tout d’abord, puis basculez vers Xcode et ajoutez un nouveau Segue entre deux scènes. Définir le **Style** à **personnalisé** et le **classe Segue** à `ReplaceViewSegue` (le nom de notre classe Segue personnalisée) :

[![](indepth-images/sg01.png "Définition de la classe de Segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Contrôleurs de fenêtre

Les contrôleurs de fenêtre contiennent et contrôlent les différents types de fenêtre que votre application macOS peut créer. Pour les plans conceptuels, ils présentent les caractéristiques suivantes :

1. Ils doivent fournir un contenu contrôleur d’affichage. Ce sera le même contrôleur d’affichage contenu qui a de la fenêtre enfant.
2. Le `Storyboard` propriété contiendra la table de montage séquentiel qui le contrôleur de fenêtre a été chargé à partir, sinon `null` si ne pas chargé à partir d’une table de montage séquentiel.
3. Vous pouvez appeler la `DismissController` méthode pour fermer la fenêtre donnée et le supprime de la vue.

Comme les contrôleurs d’affichage, fenêtre contrôleurs implémenter le `PerformSegue`, `PrepareForSegue` et `ShouldPerformSegue` méthodes et peut être utilisé comme source d’une opération de Segue.

Contrôleur de fenêtre sont chargés pour les fonctionnalités suivantes d’une application de macOS :

- Ils gèrent une fenêtre spécifique.
- Ils gèrent de barre de titre et la barre d’outils de la fenêtre (si disponible).
- Ils gèrent le contenu contrôleur d’affichage pour afficher le contenu de la fenêtre.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Modules de reconnaissance de mouvement

Modules de reconnaissance de mouvement pour macOS sont presque identiques à leurs équivalents dans iOS et permettent au développeur d’ajouter facilement des mouvements (par exemple, en cliquant sur un bouton de la souris) aux éléments d’interface utilisateur de votre application.

Toutefois, où mouvements dans iOS sont déterminées par la conception de l’application (par exemple, en appuyant sur l’écran avec deux doigts), plus mouvements dans macOS sont déterminés par le matériel.

À l’aide de modules de reconnaissance de mouvement, vous pouvez considérablement réduire la quantité de code requis pour ajouter des interactions personnalisées à un élément dans l’interface utilisateur. Comme ils peuvent déterminer automatiquement entre les clics des double et uniques, cliquez et faites glisser les événements, etc.

Au lieu de remplacer le `MouseDown` événement dans votre contrôleur d’affichage, vous devez utiliser un module de reconnaissance de mouvement pour gérer l’événement d’entrée de l’utilisateur lorsque vous travaillez avec des Storyboards.

Les modules de reconnaissance de mouvement suivantes sont disponibles dans macOS :

- `NSClickGestureRecognizer` -Inscrire les événements bas et haut de la souris.
- `NSPanGestureRecognizer` -Registres bouton de la souris vers le bas, faites glisser et événements de mise en production.
- `NSPressGestureRecognizer` -Inscrit un bouton de la souris enfoncé pendant une durée donnée d’événement de temps.
- `NSMagnificationGestureRecognizer` -Inscrit un événement d’agrandissement du matériel du pavé tactile.
- `NSRotationGestureRecognizer` -Inscrit un événement de rotation du matériel du pavé tactile.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilisation de références de table de montage séquentiel

Une référence de table de montage séquentiel vous permet de prendre une conception de table de montage séquentiel volumineuses et complexe et la décomposer en plus petites tables de montage séquentiel obtient référencés à partir de l’original, par conséquent, de suppression de la complexité et de faciliter les Storyboards individuels qui en résulte concevoir et gérer.

En outre, une référence de table de montage séquentiel peut fournir un _ancre_ à un autre scène dans la même table de montage séquentiel ou une scène spécifique sur une autre.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Faisant référence à une table de montage séquentiel externe

Pour ajouter une référence à une table de montage séquentiel externe, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le nom du projet et sélectionnez **ajouter** > **nouveau fichier...**   >  **Mac** > **Storyboard**. Entrez un **nom** pour la table de montage séquentiel et cliquez sur le **New** bouton : 

    [![](indepth-images/ref01.png "Ajout d’une table de montage séquentiel")](indepth-images/ref01.png#lightbox)
2. Dans le **l’Explorateur de solutions**, double-cliquez sur le nouveau nom de table de montage séquentiel pour l’ouvrir pour modification dans Xcode Interface Builder.
3. Concevoir la disposition de l’arrière-plan de la nouvelle table de montage séquentiel serait normalement et enregistrer vos modifications : 

    [![](indepth-images/ref02.png "Conception de l’interface")](indepth-images/ref02.png#lightbox)
4. Basculez vers la table de montage séquentiel que vous vous apprêtez à ajouter la référence à dans le Générateur d’Interface.
5. Faites glisser un **Storyboard référence** à partir de la **objet bibliothèque** sur l’aire de conception : 

    [![](indepth-images/ref03.png "Sélection d’une référence de table de montage séquentiel dans la bibliothèque")](indepth-images/ref03.png#lightbox)
6. Dans le **inspecteur d’attributs**, sélectionnez le nom de la **Storyboard** que vous avez créé ci-dessus : 

    [![](indepth-images/ref04.png "Configuration de la référence")](indepth-images/ref04.png#lightbox)
7. Cliquez sur un Widget d’interface utilisateur (par exemple, un bouton) sur une scène existante et créer un nouveau Segue à la **référence de table de montage séquentiel** que vous venez de créer.  Dans le menu contextuel, sélectionnez **afficher** pour terminer le Segue : 

    [![](indepth-images/ref06.png "Définition du type de Segue")](indepth-images/ref06.png#lightbox) 
8. Enregistrez vos modifications dans la table de montage séquentiel.
9. Revenez à Visual Studio pour Mac synchroniser vos modifications.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur que vous avez créé le Segue à partir, le contrôleur de fenêtre initiale à partir du Storyboard externe spécifié dans la référence de table de montage séquentiel seront affiche.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Référencement d’une scène spécifique dans une table de montage séquentiel externe

Pour ajouter une référence à une scène spécifique un Storyboard externe (et pas le contrôleur de fenêtre initiale), procédez comme suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur la table de montage séquentiel externe pour l’ouvrir pour modification dans Xcode Interface Builder.
2. Ajouter une nouvelle scène et concevoir sa disposition, comme vous le feriez normalement : 

    [![](indepth-images/ref07.png "Conception de la disposition dans Xcode")](indepth-images/ref07.png#lightbox)
3. Dans le **inspecteur d’identité**, entrez un **ID de Storyboard** pour contrôleur de fenêtre de la scène de nouveau : 

    [![](indepth-images/ref08.png "Définition de l’ID de Storyboard")](indepth-images/ref08.png#lightbox)
4. Ouvrez la table de montage séquentiel que vous vous apprêtez à ajouter la référence à dans Interface Builder.
5. Faites glisser un **Storyboard référence** à partir de la **objet bibliothèque** sur l’aire de conception : 

    [![](indepth-images/ref03.png "Sélection d’une référence de table de montage séquentiel à partir de la bibliothèque")](indepth-images/ref03.png#lightbox)
6. Dans le **inspecteur d’identité**, sélectionnez le nom de la **Storyboard** et **ID de référence** (ID de Storyboard) de la scène que vous avez créé ci-dessus : 

    [![](indepth-images/ref09.png "Définition de l’ID de référence")](indepth-images/ref09.png#lightbox)
7. Cliquez sur un Widget d’interface utilisateur (par exemple, un bouton) sur une scène existante et créer un nouveau Segue à la **référence de table de montage séquentiel** que vous venez de créer. Dans le menu contextuel, sélectionnez **afficher** pour terminer le Segue : 

    [![](indepth-images/ref06.png "Définition du Type de Segue")](indepth-images/ref06.png#lightbox) 
8. Enregistrez vos modifications dans la table de montage séquentiel.
9. Revenez à Visual Studio pour Mac synchroniser vos modifications.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur que vous avez créé le Segue à partir de la scène avec la donnée **ID de Storyboard** à partir du Storyboard externe spécifié dans la référence de table de montage séquentiel s’affichera.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Référencement d’une scène spécifique dans la même table de montage séquentiel

Pour ajouter une référence à une scène spécifique de la même table de montage séquentiel, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le plan conceptuel pour l’ouvrir pour modification.
2. Ajouter une nouvelle scène et concevoir sa disposition, comme vous le feriez normalement : 

    [![](indepth-images/ref11.png "Modification de la table de montage séquentiel dans Xcode")](indepth-images/ref11.png#lightbox)
3. Dans le **inspecteur d’identité**, entrez un **ID de Storyboard** pour contrôleur de fenêtre de la scène de nouveau : 

    [![](indepth-images/ref12.png "Définition de l’ID de Storyboard")](indepth-images/ref12.png#lightbox)
4. Faites glisser un **Storyboard référence** à partir de la **boîte à outils** sur l’aire de conception : 

    [![](indepth-images/ref03.png "Sélection d’une référence de table de montage séquentiel à partir de la bibliothèque")](indepth-images/ref03.png#lightbox)
5. Dans **inspecteur d’attributs**, sélectionnez **ID de référence** (ID de Storyboard) de la scène que vous avez créé ci-dessus : 

    [![](indepth-images/ref13.png "Définition de l’ID de référence")](indepth-images/ref13.png#lightbox)
6. Cliquez sur un Widget d’interface utilisateur (par exemple, un bouton) sur une scène existante et créer un nouveau Segue à la **référence de table de montage séquentiel** que vous venez de créer. Dans le menu contextuel, sélectionnez **afficher** pour terminer le Segue : 

    [![](indepth-images/ref06.png "Sélection du Type de Segue")](indepth-images/ref06.png#lightbox) 
7. Enregistrez vos modifications dans la table de montage séquentiel.
8. Revenez à Visual Studio pour Mac synchroniser vos modifications.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur que vous avez créé le Segue à partir de la scène avec la donnée **ID de Storyboard** dans la même table de montage spécifié dans la référence de table de montage séquentiel s’affichera.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemple de table de montage séquentiel complexes

Pour obtenir un exemple complexe de travailler avec des Storyboards dans une application Xamarin.Mac, veuillez consulter la [exemple d’application SourceWriter](https://developer.xamarin.com/samples/mac/SourceWriter/). SourceWriter est un éditeur de code source simple qui assure la prise en charge de la complétion de code et de la coloration syntaxique de base.

Le code SourceWriter a été entièrement commenté et, le cas échéant, des liens ont être fournis entre les principales technologies ou méthodes et des informations pertinentes dans la documentation des guides Xamarin.Mac.

## <a name="related-links"></a>Liens associés

- [MacStoryboard (sample)](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduction à Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
