---
title: Présentation de l’interface tactile 3D dans Xamarin. iOS
description: Cet article explique comment utiliser les gestes tactiles 3D introduits avec iPhone 6S et iPhone 6S plus. Ces mouvements activent la sensibilité à la pression, l’aperçu et le pop, et les actions rapides.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 38d58350cf067a2d5f7813250590cc0715b6c9ea
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527391"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Présentation de l’interface tactile 3D dans Xamarin. iOS

_Cet article aborde l’utilisation des nouvelles entrées iPhone 6S et iPhone 6S, ainsi que des gestes tactiles 3D dans votre application._

[![](3d-touch-images/info01.png "Exemples d’applications tactiles 3D")](3d-touch-images/info01.png#lightbox)

Cet article fournit une introduction à l’utilisation des nouvelles API tactiles 3D pour ajouter des gestes sensibles à la pression à vos applications Xamarin. iOS qui s’exécutent sur les nouveaux appareils iPhone 6S et iPhone 6S plus.

Avec la fonction tactile 3D, une application iPhone est désormais en mesure de savoir que l’utilisateur touche l’écran de l’appareil, mais il peut déterminer la pression exercée par l’utilisateur et répondre aux différents niveaux de pression.

l’interface tactile 3D fournit les fonctionnalités suivantes à votre application:

- [Sensibilité](#Pressure-Sensitivity) à la pression: les applications peuvent désormais mesurer la clarté ou la lumière que l’utilisateur touche l’écran et tirer parti de ces informations.
  Par exemple, une application de peinture peut rendre une ligne plus épaisse ou plus fine en fonction de la façon dont l’utilisateur touche l’écran.
- [Aperçu et pop](#Peek-and-Pop) : votre application peut désormais permettre à l’utilisateur d’interagir avec ses données sans avoir à quitter leur contexte actuel. En appuyant sur le côté de l’écran, ils peuvent lire l’élément qui les intéresse (par exemple, afficher un message). En appuyant sur une pression plus complexe, elles peuvent apparaître dans l’élément.
- [Actions rapides](#Quick-Actions) : pensez à des actions rapides comme les menus contextuels qui peuvent être dépilés lorsqu’un utilisateur clique avec le bouton droit sur un élément dans une application de bureau.
  À l’aide des actions rapides, vous pouvez ajouter des raccourcis vers les fonctions dans votre application directement à partir de l’icône de l’application sur l’écran d’accueil.
- [Test de la fonction tactile 3D dans le simulateur](#Testing-3D-Touch-in-the-Simulator) : avec le matériel Mac approprié, vous pouvez tester les applications tactiles 3D dans le simulateur iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Sensibilité à la pression

Comme indiqué ci-dessus, en utilisant les nouvelles propriétés de la classe [UITouch](xref:UIKit.UITouch) , vous pouvez mesurer la quantité de pression que l’utilisateur applique à l’écran de l’appareil iOS et utiliser ces informations dans votre interface utilisateur. Par exemple, pour rendre un trait de pinceau plus transparent ou opaque en fonction de la quantité de pression.

[![](3d-touch-images/pressure01.png "Un trait de pinceau rendu plus transparent ou opaque en fonction de la quantité de pression")](3d-touch-images/pressure01.png#lightbox)

Suite à l’utilisation de l’interface tactile en 3D, si votre application s’exécute sur iOS 9 (ou une version ultérieure) et que l’appareil iOS est en mesure de prendre en `TouchesMoved` charge la fonction tactile 3D, les modifications de pression entraînent le déclenchement de l’événement.

Par exemple, lors de l' `TouchesMoved` analyse de l’événement d’un [UIView](xref:UIKit.UIView), vous pouvez utiliser le code suivant pour obtenir la pression actuelle que l’utilisateur applique à l’écran:

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

La `MaximumPossibleForce` propriété retourne la valeur la plus élevée possible `Force` pour la propriété du [UITouch](xref:UIKit.UITouch) en fonction de l’appareil iOS sur lequel l’application s’exécute.

> [!IMPORTANT]
> Les modifications de pression entraînent `TouchesMoved` le déclenchement de l’événement, même si les coordonnées X/Y n’ont pas changé. En raison de ce changement de comportement, vos applications iOS doivent être préparées `TouchesMoved` pour que l’événement soit appelé plus souvent et que les coordonnées X/Y soient identiques à celles du dernier `TouchesMoved` appel.




Pour plus d’informations, consultez TouchCanvas d' [Apple: Utilisation d’un](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) exemple d’application et d’une [référence de classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)de UITouch de manière efficace.

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Aperçu et pop

l’interface tactile 3D offre aux utilisateurs de nouvelles façons d’interagir plus rapidement que jamais avec les informations de votre application, sans avoir à naviguer à partir de leur emplacement actuel.

Par exemple, si votre application affiche un tableau de messages, l’utilisateur peut appuyer sur un élément pour afficher un aperçu de son contenu dans une vue en forme de superposition (laquelleApple fait référence à un aperçu).

[![](3d-touch-images/peekandpop01.png "Exemple de lecture du contenu")](3d-touch-images/peekandpop01.png#lightbox)

Si l’utilisateur appuie plus difficile, il passe à la vue de message standard (qui est appelée « *pop*-ping» dans la vue).

### <a name="checking-for-3d-touch-availability"></a>Vérification de la disponibilité de la fonction tactile 3D

Lorsque vous travaillez avec `UIViewController` un, vous pouvez utiliser le code suivant pour vérifier si l’appareil iOS sur lequel l’application s’exécute prend en charge la fonction tactile 3D:

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

Cette méthode peut être appelée avant *ou après* `ViewDidLoad()`.

### <a name="handling-peek-and-pop"></a>Gestion de l’aperçu et du pop

Sur un appareil iOS qui peut gérer la fonction tactile 3D, nous pouvons utiliser une instance `UIViewControllerPreviewingDelegate` de la classe pour gérer l’affichage des détails des éléments de l' **Aperçu** et du **pop** . Par exemple, si nous avions un contrôleur d’affichage de `MasterViewController` table appelé, nous pourrions utiliser le code suivant pour prendre en charge l' **Aperçu** et le **pop**:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

La `GetViewControllerForPreview` méthode est utilisée pour effectuer l’opération de **lecture** . Il obtient l’accès à la cellule de table et aux données de stockage, `DetailViewController` puis charge le à partir du storyboard actuel. En affectant `PreferredContentSize` la valeur (0, 0), nous demandons la taille de l’aperçu avant **lecture** par défaut. Enfin, nous brouillerons tout sauf la cellule avec laquelle `previewingContext.SourceRect = cell.Frame` nous affichons et nous retournons la nouvelle vue pour l’affichage.

Le `CommitViewController` réutilise la vue que nous avons créée dans l’aperçu de la vue **pop** lorsque l’utilisateur appuie plus difficile.

### <a name="registering-for-peek-and-pop"></a>Inscription pour Peek et pop

À partir du contrôleur d’affichage que nous souhaitons autoriser à l’utilisateur à **lire** et à dépiler des éléments, nous devons nous inscrire pour ce service. Dans l’exemple ci-dessus d’un contrôleur d’affichage`MasterViewController`de table (), nous utiliserons le code suivant:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

Ici, nous appelons la `RegisterForPreviewingWithDelegate` méthode avec une instance `PreviewingDelegate` du que nous avons créée ci-dessus. Sur les appareils iOS qui prennent en charge la fonction tactile 3D, l’utilisateur peut appuyer sur un élément pour l’aperçu. S’ils cliquent encore plus, l’élément s’affiche dans l’affichage standard.

Pour plus d’informations, consultez notre [exemple iOS 9 ApplicationShortcuts](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) et ViewControllerPreviews d' [Apple: Utilisation de l’exemple d’application API](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) d’aperçu UIViewController, [référence de classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), référence de [classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) et [référence de protocole UIPreviewActionItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Actions rapides

À l’aide des actions tactiles et rapides en 3D, vous pouvez ajouter des raccourcis communs, rapides et faciles à accéder aux fonctions de votre application à partir de l’icône d’écran d’accueil sur l’appareil iOS.

Comme indiqué ci-dessus, vous pouvez envisager des actions rapides comme les menus contextuels qui peuvent être dépilés lorsqu’un utilisateur clique avec le bouton droit sur un élément dans une application de bureau. Vous devez utiliser des actions rapides pour fournir des raccourcis vers les fonctions ou fonctionnalités les plus courantes de votre application.

[![](3d-touch-images/quickactions01.png "Exemple de menu actions rapides")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Définition des actions rapides statiques

Si une ou plusieurs des actions rapides requises par votre application sont statiques et n’ont pas besoin d’être modifiées, vous pouvez les définir dans le `Info.plist` fichier de l’application. Modifiez ce fichier dans un éditeur externe et ajoutez les clés suivantes:

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

Ici, nous définissons deux éléments d’action rapide statiques avec les clés suivantes:

- `UIApplicationShortcutItemIconType`-Définit l’icône qui sera affichée par l’élément d’action rapide comme l’une des valeurs suivantes:
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

  ![](3d-touch-images/uiapplicationshortcuticontype.png "Image UIApplicationShortcutIconType")

- `UIApplicationShortcutItemSubtitle`: Définit le sous-titre de l’élément.
- `UIApplicationShortcutItemTitle`-Définit le titre de l’élément.
- `UIApplicationShortcutItemType`-Valeur de chaîne que nous allons utiliser pour identifier l’élément dans notre application. Pour plus d'informations, consultez la section suivante.

> [!IMPORTANT]
> Les éléments de raccourci d’action rapide définis dans `Info.plist` le fichier ne sont pas accessibles `Application.ShortcutItems` avec la propriété. Elles sont uniquement transmises au gestionnaire d' `HandleShortcutItem` événements.





### <a name="identifying-quick-action-items"></a>Identification des éléments d’action rapide

Comme vous l’avez vu précédemment, lorsque vous avez défini vos éléments d’action rapides `Info.plist`dans le de votre application, vous avez `UIApplicationShortcutItemType` affecté une valeur de chaîne à la clé pour les identifier.

Pour faciliter l’utilisation de ces identificateurs dans le code, ajoutez une classe appelée `ShortcutIdentifier` au projet de votre application et faites en sorte qu’elle ressemble à ce qui suit:

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>Gestion d’une action rapide

Ensuite, vous devez modifier le fichier de `AppDelegate.cs` votre application pour gérer l’utilisateur en sélectionnant un élément d’action rapide à partir de l’icône de votre application sur l’écran d’accueil.

Apportez les modifications suivantes:

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

Tout d’abord, nous définissons une propriété publique `LaunchedShortcutItem` pour suivre le dernier élément d’action rapide sélectionné par l’utilisateur. Ensuite, nous substituons la `FinishedLaunching` méthode et voyons si `launchOptions` a été passé et s’ils contiennent un élément d’action rapide. Dans ce cas, nous stockons l’action rapide dans `LaunchedShortcutItem` la propriété.

Ensuite, nous remplaçons la `OnActivated` méthode et transmettons tout élément de lancement rapide sélectionné `HandleShortcutItem` à la méthode sur laquelle agir. Actuellement, nous n’écrivons un message que sur la **console**. Dans une application réelle, vous pouvez gérer les actions qui étaient requises. Une fois l’action effectuée, la `LaunchedShortcutItem` propriété est effacée.

Enfin, si votre application était déjà en cours d' `PerformActionForShortcutItem` exécution, la méthode serait appelée pour gérer l’élément d’action rapide, afin que nous ayons besoin de `HandleShortcutItem` la remplacer et d’appeler notre méthode ici également.


### <a name="creating-dynamic-quick-action-items"></a>Création d’éléments d’action rapide dynamiques

En plus de définir des éléments d’action rapide statiques dans `Info.plist` le fichier de votre application, vous pouvez créer des actions rapides dynamiques à la volée. Pour définir deux nouvelles actions rapides dynamiques, modifiez à `AppDelegate.cs` nouveau votre fichier et modifiez `FinishedLaunching` la méthode pour qu’elle ressemble à ce qui suit:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

Nous vérifions à présent si le `application` contient déjà un ensemble de créé `ShortcutItems`dynamiquement. si ce n’est pas le cas, nous `UIMutableApplicationShortcutItem` allons créer deux nouveaux objets pour définir `ShortcutItems` les nouveaux éléments et les ajouter au tableau.

Le code que nous avons déjà ajouté dans la section [gestion d’une action rapide](#Handling-a-Quick-Action) ci-dessus gère ces actions rapides dynamiques, comme les actions statiques.

Notez que vous pouvez créer une combinaison d’éléments d’action rapide statiques et dynamiques (comme nous le faisons ici), vous n’êtes pas limité à l’un ou l’autre.

Pour plus d’informations, reportez-vous à notre [exemple iOS 9 ViewControllerPreview](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) et consultez ApplicationShortcuts d' [Apple: Utilisation de](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) l’exemple d’application UIApplicationShortcutItem, de la [référence de classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), de la référence de classe [UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) et de la [référence de classe UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Test de la fonction tactile 3D dans le simulateur

Lorsque vous utilisez la dernière version de Xcode et du simulateur iOS sur un Mac compatible avec un Force Touch activer pavé tactile, vous pouvez tester la fonctionnalité tactile 3D dans le simulateur.

Pour activer cette fonctionnalité, exécutez n’importe quelle application dans un matériel iPhone simulé qui prend en charge l’interface tactile en 3D (iPhone 6S et ultérieur). Sélectionnez ensuite le menu **matériel** dans le simulateur iOS, puis activez l’option **utiliser pavé tactile force pour le menu tactile 3D** :

[![](3d-touch-images/simulator01.png "Sélectionnez le menu matériel dans le simulateur iOS et activez l’élément de menu utiliser pavé tactile force for 3D touch")](3d-touch-images/simulator01.png#lightbox)

Une fois cette fonctionnalité activée, vous pouvez appuyer sur le pavé tactile du Mac pour activer le toucher 3D comme vous le feriez sur du matériel iPhone réel.

## <a name="summary"></a>Récapitulatif

Cet article a introduit les nouvelles API tactiles 3D disponibles dans iOS 9 pour iPhone 6S et iPhone 6S plus. Il a abordé l’ajout de sensibilité à la pression à une application. utilisation de Peek et pop pour afficher rapidement les informations dans l’application à partir du contexte actuel sans navigation; et l’utilisation des actions rapides pour fournir des raccourcis vers les fonctionnalités les plus couramment utilisées de votre application.



## <a name="related-links"></a>Liens associés

- [Exemple ViewControllerPreview iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [Exemple ApplicationShortcuts iOS 9](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Préparez vos applications iPhone en 3D touch](https://developer.apple.com/ios/3d-touch/)
