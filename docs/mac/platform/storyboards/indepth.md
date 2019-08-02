---
title: Utilisation des storyboards dans Xamarin. Mac
description: Ce document décrit comment utiliser les storyboards dans Xamarin. Mac, examiner comment les charger à partir du code, le cycle de vie des contrôleurs d’affichage, la chaîne de répondeur, SEGUES, les contrôleurs de fenêtre, les détecteurs de mouvement, etc.
ms.prod: xamarin
ms.assetid: DF4DF7C2-DDD7-4A32-B375-5C5446301EC5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 06d3127bbe7766a70efc570bd455a8d570548731
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653783"
---
# <a name="working-with-storyboards-in-xamarinmac"></a>Utilisation des storyboards dans Xamarin. Mac

Une table de montage séquentiel définit la totalité de l’interface utilisateur pour une application donnée, divisée en une vue d’ensemble fonctionnelle de ses contrôleurs d’affichage. Dans Interface Builder de Xcode, chacun de ces contrôleurs vit dans sa propre scène.

[![](indepth-images/intro01.png "Une table de montage séquentiel dans le Interface Builder de Xcode")](indepth-images/intro01.png#lightbox)

Le Storyboard est un fichier de ressources (avec les extensions `.storyboard`de) qui est inclus dans l’offre groupée de l’application Xamarin. Mac lorsqu’il est compilé et expédié. Pour définir le plan conceptuel de départ de votre application, modifiez `Info.plist` son fichier et sélectionnez l' **interface principale** dans la zone de liste déroulante: 

[![](indepth-images/sb01.png "Éditeur info. plist")](indepth-images/sb01.png#lightbox)

<a name="Loading-from-Code" />

## <a name="loading-from-code"></a>Charger à partir du code

Il peut arriver que vous deviez charger un Storyboard spécifique à partir du code et créer un contrôleur d’affichage manuellement. Vous pouvez utiliser le code suivant pour effectuer cette action:

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);
```

Le `FromName` charge le fichier de Storyboard avec le nom donné qui a été inclus dans l’offre groupée de l’application. `InstantiateControllerWithIdentifier` Crée une instance du contrôleur d’affichage avec l’identité donnée. Vous définissez l’identité dans le Interface Builder de Xcode lors de la conception de l’interface utilisateur:

[![](indepth-images/sb02.png "Définition de l’ID de Storyboard")](indepth-images/sb02.png#lightbox)

Si vous le souhaitez, vous pouvez `InstantiateInitialController` utiliser la méthode pour charger le contrôleur d’affichage auquel a été attribué le contrôleur initial dans Interface Builder:

[![](indepth-images/sb03.png "Définition du contrôleur initial")](indepth-images/sb03.png#lightbox)

Elle est marquée par le **point d’entrée de la table de montage séquentiel** et la flèche ouverte terminée ci-dessus.

<a name="View-Controllers" />

## <a name="view-controllers"></a>Contrôleurs d’affichage

Les contrôleurs d’affichage définissent les relations entre une vue donnée des informations au sein d’une application Mac et le modèle de données qui fournit ces informations. Chaque scène de niveau supérieur dans le Storyboard représente un contrôleur d’affichage dans le code de l’application Xamarin. Mac.

<a name="The-View-Controller-Lifecycle" />

### <a name="the-view-controller-lifecycle"></a>Cycle de vie du contrôleur d’affichage

Plusieurs nouvelles méthodes ont été ajoutées à la `NSViewController` classe pour prendre en charge les storyboards dans MacOS. Plus important encore, les méthodes suivantes sont utilisées pour répondre au cycle de vie de la vue contrôlée par le contrôleur d’affichage donné:

- `ViewDidLoad`-Cette méthode est appelée lorsque la vue est chargée à partir du fichier de table de montage séquentiel.
- `ViewWillAppear`-Cette méthode est appelée juste avant que la vue ne s’affiche à l’écran.
- `ViewDidAppear`-Cette méthode est appelée directement après l’affichage de la vue à l’écran.
- `ViewWillDisappear`-Cette méthode est appelée juste avant que l’affichage ne soit supprimé de l’écran.
- `ViewDidDisappear`-Cette méthode est appelée directement après que la vue a été supprimée de l’écran.
- `UpdateViewConstraints`-Cette méthode est appelée lorsque les contraintes qui définissent une position et une taille de disposition automatique doivent être mises à jour.
- `ViewWillLayout`-Cette méthode est appelée juste avant que les sous-vues de cette vue ne soient présentées à l’écran.
- `ViewDidLayout`-Cette méthode est appelée directement après que les sous-vues de la vue ont été présentées à l’écran.

<a name="The-Responder-Chain" />

### <a name="the-responder-chain"></a>Chaîne du répondeur

En outre, `NSViewControllers` font désormais partie de la chaîne de _répondeur_de la fenêtre:

[![](indepth-images/vc01.png "Chaîne du répondeur")](indepth-images/vc01.png#lightbox)

Et, par conséquent, ils sont reliés pour recevoir des événements tels que les sélections couper, copier et coller. Ce contrôleur d’affichage automatique s’exécute uniquement sur les applications qui s’exécutent sur macOS Sierra (10,12) et versions ultérieures.

<a name="Containment" />

### <a name="containment"></a>Imbrication

Dans les storyboards, les contrôleurs d’affichage (tels que le contrôleur d’affichage fractionné et le contrôleur d’affichage d’onglets) peuvent désormais implémenter la _relation contenant-contenu_, de sorte qu’ils peuvent «contenir» d’autres contrôleurs de sous-affichage:

[![](indepth-images/vc02.png "Exemple de relation contenant-contenu de contrôleur d’affichage")](indepth-images/vc02.png#lightbox)

Les contrôleurs d’affichage enfants contiennent des méthodes et des propriétés pour les lier à leur contrôleur d’affichage parent et pour utiliser l’affichage et la suppression des vues de l’écran.

Tous les contrôleurs d’affichage de conteneur intégrés à macOS ont une disposition spécifique que Apple suggère de suivre si vous créez vos propres contrôleurs d’affichage de conteneur personnalisés:

[![](indepth-images/vc03.png "Disposition du contrôleur d’affichage")](indepth-images/vc03.png#lightbox)

Le contrôleur d’affichage de collection contient un tableau d’éléments de vue de collection, chacun contenant un ou plusieurs contrôleurs d’affichage qui contiennent leurs propres vues.

<a name="Segues" />

## <a name="segues"></a>SEGUES

SEGUES fournissent les relations entre toutes les scènes qui définissent l’interface utilisateur de votre application. Si vous êtes familiarisé avec l’utilisation des storyboards dans iOS, vous savez que SEGUES pour iOS définit généralement des transitions entre les vues plein écran. Cela diffère de macOS, lorsque SEGUES définit généralement la «[relation contenant-contenu](#Containment)», où une scène est l’enfant d’une scène parente.

Dans macOS, la plupart des applications ont tendance à regrouper leurs vues dans la même fenêtre à l’aide d’éléments d’interface utilisateur tels que les onglets et les affichages fractionnés. Contrairement à iOS, où les vues doivent être activées et désactivées à l’écran, en raison d’un espace d’affichage physique limité.

<a name="Presentation-Segues" />

### <a name="presentation-segues"></a>SEGUES de présentation

En raison de l’banlieusards de Mac pour la relation contenant-contenu, il existe des situations où les _SEGUES de présentation_ sont utilisés, tels que les fenêtres modales, les vues de feuille et les popovers. macOS fournit les types de Segue intégrés suivants:

- **Afficher** : affiche la cible du segue sous la forme d’une fenêtre non modale. Par exemple, utilisez ce type de Segue pour présenter une autre instance d’une fenêtre de document dans votre application.
- **Modal** : affiche la cible du segue sous forme de fenêtre modale. Par exemple, utilisez ce type de Segue pour présenter la fenêtre de préférences de votre application.
- **Sheet** : présente la cible du segue sous forme de feuille attachée à la fenêtre parente. Par exemple, utilisez ce type de Segue pour présenter une feuille de recherche et de remplacement.
- **Menu segue** : affiche la cible du segue comme dans une fenêtre menu segue. Par exemple, utilisez ce type segue pour présenter des options lorsqu’un utilisateur clique sur un élément d’interface utilisateur.
- **Personnalisé** : présente la cible du segue à l’aide d’un type de Segue personnalisé défini par le développeur. Pour plus d’informations, consultez la section création d’un [SEGUES personnalisé](#Creating-Custom-Segues) ci-dessous.

Lorsque vous utilisez la SEGUES de présentation, vous pouvez `PrepareForSegue` remplacer la méthode du contrôleur d’affichage parent pour la présentation pour initialiser et les variables et fournir toutes les données au contrôleur d’affichage en cours de présentation.

<a name="Triggered-Segues" />

### <a name="triggered-segues"></a>SEGUES déclenché

Les SEGUES déclenchés vous permettent de spécifier des SEGUES nommés (par le biais de leur propriété d' **identificateur** dans Interface Builder) et de les déclencher par des événements tels que l’utilisateur qui clique sur un bouton ou en appelant la méthode dans le `PerformSegue` code:

```csharp
// Display the Scene defined by the given Segue ID
PerformSegue("MyNamedSegue", this);
``` 

L’ID segue est défini à l’intérieur de l’Interface Builder de Xcode quand vous disposez de l’interface utilisateur de l’application:

[![](indepth-images/sg02.png "Saisie d’un nom de Segue")](indepth-images/sg02.png#lightbox)

Dans le contrôleur d’affichage qui agit en tant que source du Segue, vous devez substituer la `PrepareForSegue` méthode et effectuer toute initialisation requise avant l’exécution de Segue et le contrôleur d’affichage spécifié s’affiche:

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

Si vous le souhaitez, vous pouvez substituer la `ShouldPerformSegue` méthode et contrôler si le segue est réellement exécuté via C# du code. Pour les contrôleurs de vue présentés manuellement `DismissController` , appelez leur méthode pour les supprimer de l’affichage lorsqu’ils ne sont plus nécessaires.

<a name="Creating-Custom-Segues" />

### <a name="creating-custom-segues"></a>Création de SEGUES personnalisés

Il peut arriver que votre application nécessite un type segue non fourni par le SEGUES de build défini dans macOS. Si c’est le cas, vous pouvez créer un segue personnalisé qui peut être affecté dans le Interface Builder de Xcode lors de la disposition de l’interface utilisateur de votre application.

Par exemple, pour créer un nouveau type segue qui remplace le contrôleur d’affichage actuel dans une fenêtre (au lieu d’ouvrir la scène cible dans une nouvelle fenêtre), nous pouvons utiliser le code suivant:

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

Voici quelques points à noter:

- Nous utilisons l' `Register` attribut pour exposer cette classe à Objective-C/MacOS.
- Nous remplaçons la `Perform` méthode pour exécuter en fait l’action de notre segue personnalisé.
- Nous remplaçons le contrôleur de `ContentViewController` la fenêtre par celui défini par la cible (destination) du segue.
- Nous supprimons le contrôleur d’affichage d’origine pour libérer de la `RemoveFromParentViewController` mémoire à l’aide de la méthode.

Pour utiliser ce nouveau type segue dans le Interface Builder de Xcode, nous devons d’abord compiler l’application, puis basculer vers Xcode et ajouter un nouveau segue entre deux scènes. Définissez le **style** sur **personnalisé** et la **classe segue** sur `ReplaceViewSegue` (le nom de notre classe segue personnalisée):

[![](indepth-images/sg01.png "Définition de la classe segue")](indepth-images/sg01.png#lightbox)

<a name="Triggered-Segues" />

## <a name="window-controllers"></a>Contrôleurs de fenêtre

Les contrôleurs de fenêtre contiennent et contrôlent les différents types de fenêtres que votre application macOS peut créer. Pour les storyboards, elles possèdent les fonctionnalités suivantes:

1. Ils doivent fournir un contrôleur d’affichage de contenu. Il s’agit du même contrôleur d’affichage de contenu que celui de la fenêtre enfant.
2. La `Storyboard` propriété contient le Storyboard à partir duquel le contrôleur de fenêtre a été chargé `null` , sinon si elle n’est pas chargée à partir d’une table de montage séquentiel.
3. Vous pouvez appeler la `DismissController` méthode pour fermer la fenêtre donnée et la supprimer de la vue.

Comme les contrôleurs de vue, les `PerformSegue`contrôleurs de `ShouldPerformSegue` fenêtre implémentent, et les méthodes, `PrepareForSegue` et peuvent être utilisés comme source d’une opération segue.

Le contrôleur de Windows est responsable des fonctionnalités suivantes d’une application macOS:

- Ils gèrent une fenêtre spécifique.
- Ils gèrent la barre de titre et la barre d’outils de la fenêtre (si disponible).
- Ils gèrent le contrôleur d’affichage de contenu pour afficher le contenu de la fenêtre.

<a name="Gesture-Recognizers" />

## <a name="gesture-recognizers"></a>Détecteurs de mouvement

Les modules de reconnaissance de mouvement pour macOS sont quasiment identiques à leurs équivalents dans iOS et permettent au développeur d’ajouter facilement des gestes (tels qu’un clic sur un bouton de la souris) aux éléments de l’interface utilisateur de votre application.

Toutefois, lorsque les gestes dans iOS sont déterminés par la conception de l’application (par exemple, en appuyant sur l’écran avec deux doigts), la plupart des mouvements dans macOS sont déterminés par le matériel.

En utilisant des détecteurs de mouvement, vous pouvez réduire la quantité de code requise pour ajouter des interactions personnalisées à un élément de l’interface utilisateur. Comme ils peuvent déterminer automatiquement entre deux clics, cliquez et faites glisser des événements, etc.

Au lieu de remplacer l' `MouseDown` événement dans votre contrôleur d’affichage, vous devez utiliser un module de reconnaissance de mouvement pour gérer l’événement d’entrée utilisateur lors de l’utilisation des storyboards.

Les détecteurs de mouvements suivants sont disponibles dans macOS:

- `NSClickGestureRecognizer`-Inscrivez les événements Mouse et up.
- `NSPanGestureRecognizer`-Enregistre le bouton de la souris enfoncé, les événements de glisser-déplacer.
- `NSPressGestureRecognizer`-Enregistre le bouton de la souris enfoncé pendant un événement de temps donné.
- `NSMagnificationGestureRecognizer`-Enregistre un événement d’agrandissement à partir du matériel pavé tactile.
- `NSRotationGestureRecognizer`-Enregistre un événement de rotation à partir du matériel pavé tactile.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilisation de références de Storyboard

Une référence de table de montage séquentiel vous permet d’effectuer une conception de Storyboard complexe et de la décomposer en plus petits storyboards qui sont référencés à partir de l’original, supprimant ainsi la complexité et rendant les storyboards individuels résultants plus faciles à concevoir et à gérer.

En outre, une référence de table de montage séquentiel peut fournir une _ancre_ à une autre scène au sein d’une même table de montage séquentiel ou d’une scène spécifique sur un autre plan.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Référencement d’un Storyboard externe

Pour ajouter une référence à un Storyboard externe, procédez comme suit:

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Storyboard Mac.  >  >  Entrez un **nom** pour la nouvelle table de montage séquentiel, puis cliquez sur le bouton **nouveau** : 

    [![](indepth-images/ref01.png "Ajout d’un nouveau Storyboard")](indepth-images/ref01.png#lightbox)
2. Dans la **Explorateur de solutions**, double-cliquez sur le nouveau nom de la table de montage séquentiel pour l’ouvrir et le modifier dans le Interface Builder de Xcode.
3. Concevez la disposition des scènes de la nouvelle table de montage séquentiel comme vous le feriez normalement et enregistrez vos modifications: 

    [![](indepth-images/ref02.png "Conception de l’interface")](indepth-images/ref02.png#lightbox)
4. Basculez vers le Storyboard auquel vous allez ajouter la référence dans le Interface Builder.
5. Faites glisser une **référence de table de montage séquentiel** de la **bibliothèque d’objets** vers l’aire de conception: 

    [![](indepth-images/ref03.png "Sélection d’une référence de table de montage séquentiel dans la bibliothèque")](indepth-images/ref03.png#lightbox)
6. Dans l' **inspecteur d’attribut**, sélectionnez le nom de la **table de montage séquentiel** que vous avez créée ci-dessus: 

    [![](indepth-images/ref04.png "Configuration de la référence")](indepth-images/ref04.png#lightbox)
7. Cliquez avec le bouton droit sur un widget d’interface utilisateur (comme un bouton) sur une scène existante, puis créez un nouveau segue dans la **référence de table de montage séquentiel** que vous venez de créer.  Dans le menu contextuel, sélectionnez **Afficher** pour terminer le segue: 

    [![](indepth-images/ref06.png "Définition du type segue")](indepth-images/ref06.png#lightbox) 
8. Enregistrez vos modifications dans le Storyboard.
9. Revenez à Visual Studio pour Mac pour synchroniser vos modifications.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur à partir duquel vous avez créé le Segue, le contrôleur de fenêtre initial du storyboard externe spécifié dans la référence de la table de montage séquentiel s’affiche.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Référencement d’une scène spécifique dans un Storyboard externe

Pour ajouter une référence à une scène spécifique, un Storyboard externe (et non le contrôleur de fenêtre initial), procédez comme suit:

1. Dans la **Explorateur de solutions**, double-cliquez sur le Storyboard externe pour l’ouvrir et le modifier dans le Interface Builder de Xcode.
2. Ajoutez une nouvelle scène et concevez sa disposition comme vous le feriez normalement: 

    [![](indepth-images/ref07.png "Conception de la disposition dans Xcode")](indepth-images/ref07.png#lightbox)
3. Dans l' **inspecteur d’identité**, entrez un **ID de Storyboard** pour le contrôleur de fenêtre du nouveau décor: 

    [![](indepth-images/ref08.png "Définition de l’ID de Storyboard")](indepth-images/ref08.png#lightbox)
4. Ouvrez le Storyboard auquel vous allez ajouter la référence dans Interface Builder.
5. Faites glisser une **référence de table de montage séquentiel** de la **bibliothèque d’objets** vers l’aire de conception: 

    [![](indepth-images/ref03.png "Sélection d’une référence de table de montage séquentiel dans la bibliothèque")](indepth-images/ref03.png#lightbox)
6. Dans l' **inspecteur d’identité**, sélectionnez le nom de la **table de montage séquentiel** et l' **ID de référence** (Storyboard ID) de la scène que vous avez créée ci-dessus: 

    [![](indepth-images/ref09.png "Définition de l’ID de référence")](indepth-images/ref09.png#lightbox)
7. Cliquez avec le bouton droit sur un widget d’interface utilisateur (comme un bouton) sur une scène existante, puis créez un nouveau segue dans la **référence de table de montage séquentiel** que vous venez de créer. Dans le menu contextuel, sélectionnez **Afficher** pour terminer le segue: 

    [![](indepth-images/ref06.png "Définition du type segue")](indepth-images/ref06.png#lightbox) 
8. Enregistrez vos modifications dans le Storyboard.
9. Revenez à Visual Studio pour Mac pour synchroniser vos modifications.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur à partir duquel vous avez créé le Segue, la scène avec l' **ID de Storyboard** donné à partir du storyboard externe spécifié dans la référence de la table de montage séquentiel s’affiche.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Référencement d’une scène spécifique dans la même table de montage séquentiel

Pour ajouter une référence à une scène spécifique du même Storyboard, procédez comme suit:

1. Dans la **Explorateur de solutions**, double-cliquez sur le Storyboard pour l’ouvrir pour le modifier.
2. Ajoutez une nouvelle scène et concevez sa disposition comme vous le feriez normalement: 

    [![](indepth-images/ref11.png "Modification de la table de montage séquentiel dans Xcode")](indepth-images/ref11.png#lightbox)
3. Dans l' **inspecteur d’identité**, entrez un **ID de Storyboard** pour le contrôleur de fenêtre du nouveau décor: 

    [![](indepth-images/ref12.png "Définition de l’ID de Storyboard")](indepth-images/ref12.png#lightbox)
4. Faites glisser une **référence de Storyboard** de la **boîte à outils** vers le aire de conception: 

    [![](indepth-images/ref03.png "Sélection d’une référence de table de montage séquentiel dans la bibliothèque")](indepth-images/ref03.png#lightbox)
5. Dans l' **inspecteur d’attribut**, sélectionnez ID de **référence** (ID de Storyboard) de la scène créée ci-dessus: 

    [![](indepth-images/ref13.png "Définition de l’ID de référence")](indepth-images/ref13.png#lightbox)
6. Cliquez avec le bouton droit sur un widget d’interface utilisateur (comme un bouton) sur une scène existante, puis créez un nouveau segue dans la **référence de table de montage séquentiel** que vous venez de créer. Dans le menu contextuel, sélectionnez **Afficher** pour terminer le segue: 

    [![](indepth-images/ref06.png "Sélection du type de Segue")](indepth-images/ref06.png#lightbox) 
7. Enregistrez vos modifications dans le Storyboard.
8. Revenez à Visual Studio pour Mac pour synchroniser vos modifications.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur à partir duquel vous avez créé le Segue, la scène avec l' **ID de Storyboard** donné dans le même Storyboard spécifié dans la référence de la table de montage séquentiel s’affiche.

<a name="Complex-Storyboard-Example" />

## <a name="complex-storyboard-example"></a>Exemple de Storyboard complexe

Pour obtenir un exemple complexe de l’utilisation des storyboards dans une application Xamarin. Mac, consultez l' [exemple d’application SourceWriter](https://docs.microsoft.com/samples/xamarin/mac-samples/sourcewriter). SourceWriter est un éditeur de code source simple qui assure la prise en charge de la complétion de code et de la coloration syntaxique de base.

Le code SourceWriter a été entièrement commenté et, le cas échéant, des liens ont être fournis entre les principales technologies ou méthodes et des informations pertinentes dans la documentation des guides Xamarin.Mac.

## <a name="related-links"></a>Liens associés

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
