---
title: Introduction à la 3D Touch dans Xamarin.iOS
description: Cet article décrit comment utiliser des gestes tactiles 3D introduites par l’iPhone 6 s et iPhone 6 s Plus. Ces mouvements activer pression, peek et pop et actions rapides.
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: abd9fe85be412539fd51d0e6164c79ce346377a7
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832106"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Introduction à la 3D Touch dans Xamarin.iOS

_Cet article traite de l’utilisation de la nouvelle iPhone 6 s et iPhone 6 s Plus 3D Touch mouvements dans votre application._

[![](3d-touch-images/info01.png "Exemples de 3D Touch activé des applications")](3d-touch-images/info01.png#lightbox)

Cet article fournit et introduction à l’aide des nouvelles 3D Touch API pour ajouter des mouvements sensibles pression à vos applications Xamarin.iOS qui sont exécutent sur le nouvel iPhone 6 s et iPhone 6 s ainsi que des appareils.

Avec 3D Touch, une application iPhone peut désormais non seulement pour indiquer que l’utilisateur touche l’écran du périphérique, mais il est en mesure d’identifier combien l’utilisateur est exerçant une pression et répondre aux niveaux de pression différents.

3D Touch fournit les fonctionnalités suivantes à votre application :

- [Sensibilité à la pression](#Pressure-Sensitivity) - applications peuvent mesurer maintenant hard ou light l’utilisateur touche l’écran et profiter de ces informations.
  Par exemple, une application de peinture peut effectuer une ligne épaisse ou plus fins en fonction de laquelle l’utilisateur touche l’écran.
- [Aperçu et Pop](#Peek-and-Pop) -votre application peut désormais permettre à l’utilisateur interagir avec ses données sans avoir à naviguer en dehors de leur contexte actuel. En appuyant sur disque dur sur l’écran de l’écran, il peuvent lire l’élément qui que les intéresse (par exemple, pour afficher un aperçu d’un message). En appuyant sur plus difficile, ils peuvent entrer dans l’élément.
- [Actions rapides](#Quick-Actions) -pense que des Actions rapides, comme les menus contextuels qui peuvent être dépilés à distance quand un utilisateur clique sur un élément dans une application de bureau.
  À l’aide des Actions rapides, vous pouvez ajouter des raccourcis à des fonctions dans votre application directement à partir de l’icône d’application sur l’écran d’accueil.
- [Test 3D Touch dans le simulateur](#Testing-3D-Touch-in-the-Simulator) -avec du matériel Mac approprié, vous pouvez tester les applications 3D Touch activé dans le simulateur iOS.

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>Pression

Comme indiqué ci-dessus, à l’aide des nouvelles propriétés de la [UITouch](xref:UIKit.UITouch) classe que vous pouvez mesurer la quantité de pression que l’utilisateur consiste à appliquer à l’écran de l’appareil iOS et utiliser ces informations dans votre interface utilisateur. Par exemple, rendre un trait de pinceau plus translucide ou opaque en sur la quantité de pression.

[![](3d-touch-images/pressure01.png "Un trait de pinceau rendu comme plus transparent ou opaque selon la quantité de pression")](3d-touch-images/pressure01.png#lightbox)

À la suite de 3D Touch, si votre application s’exécute sur iOS 9 (ou version ultérieure) et l’appareil iOS est capable de prise en charge 3D Touch, modifications apportées à la pression provoqueront la `TouchesMoved` déclenchement d’événement.

Par exemple, lorsque vous analysez le `TouchesMoved` événement d’un [UIView](xref:UIKit.UIView), vous pouvez utiliser le code suivant pour obtenir la pression actuelle que l’application de l’utilisateur à l’écran :

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

Le `MaximumPossibleForce` propriété retourne la valeur la plus élevée possible pour le `Force` propriété de la [UITouch](xref:UIKit.UITouch) basé sur l’appareil iOS que l’application est en cours d’exécution.

> [!IMPORTANT]
> Entraînent des modifications apportées à la sollicitation de la la `TouchesMoved` événement doit être déclenché même si le X / Y coordonnées n’ont pas changé. En raison de ce changement de comportement, vos applications iOS doivent être préparées pour la `TouchesMoved` événement à appeler plus souvent et pour le X / Y coordonne le même que le dernier `TouchesMoved` appeler.




Pour plus d’informations, consultez le site d’Apple [TouchCanvas : À l’aide de UITouch efficacement](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) exemple d’application et [référence de classe UITouch](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/).

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>Aperçu et Pop

3D Touch fournit de nouvelles façons pour un utilisateur à interagir avec les informations au sein de votre application plus rapidement que jamais, sans avoir à naviguer à partir de leur emplacement actuel.

Par exemple, si votre application affiche une table des messages, l’utilisateur peut appuyer sur disque dur sur un élément pour afficher un aperçu de son contenu dans une vue de superposition (c'est-à-dire, Apple comme un *aperçu*).

[![](3d-touch-images/peekandpop01.png "Un exemple de lecture au contenu")](3d-touch-images/peekandpop01.png#lightbox)

Si l’utilisateur appuie sur plus difficile, celui-ci doit entrer la vue de message normal (qui est appelé *Pop*-ping dans la vue).

### <a name="checking-for-3d-touch-availability"></a>Vérifie la disponibilité 3D Touch

Lorsque vous travaillez avec un `UIViewController` vous pouvez utiliser le code suivant pour voir si l’appareil iOS que l’application est en cours d’exécution prend en charge 3D Touch :

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

### <a name="handling-peek-and-pop"></a>POP et gestion d’aperçu

Sur un appareil iOS qui peut gérer 3D Touch, nous pouvons utiliser une instance de la `UIViewControllerPreviewingDelegate` classe pour gérer l’affichage de **aperçu** et **Pop** détails de l’élément. Par exemple, si nous avions un contrôleur d’affichage Table appelée `MasterViewController` nous pourrions utiliser le code suivant pour prendre en charge **aperçu** et **Pop**:

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

Le `GetViewControllerForPreview` méthode est utilisée pour effectuer le **aperçu** opération. Il accède aux données de cellule et de stockage de table et charge ensuite le `DetailViewController` à partir de l’animation en cours. En définissant le `PreferredContentSize` à (0,0), nous demandons pour la valeur par défaut **aperçu** taille de l’affichage. Enfin, nous avons tout sauf la cellule nous affichons avec flou `previewingContext.SourceRect = cell.Frame` et nous retournons la nouvelle vue pour l’affichage.

Le `CommitViewController` réutilise la vue que nous avons créé dans le **aperçu** pour le **Pop** afficher lorsque l’utilisateur appuie plus difficile.

### <a name="registering-for-peek-and-pop"></a>L’inscription pour l’aperçu et Pop

À partir du contrôleur d’affichage que nous souhaitons permettre à l’utilisateur **aperçu** et **Pop** éléments à partir de, nous avons besoin pour vous inscrire à ce service. Dans l’exemple ci-dessus d’un contrôleur d’affichage Table (`MasterViewController`), nous utiliserions le code suivant :

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

Ici, nous appelons le `RegisterForPreviewingWithDelegate` méthode avec une instance de la `PreviewingDelegate` nous avons créé ci-dessus. Sur les appareils iOS qui prennent en charge 3D Touch, l’utilisateur peut appuyer sur disque dur sur un élément de la lecture à elle. Si elles appuyez sur encore plus difficile, l’élément s’affiche dans celle-ci standard afficher.

Pour plus d’informations, consultez notre [iOS 9 ApplicationShortcuts exemple](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) et d’Apple [ViewControllerPreviews : À l’aide de la UIViewController l’aperçu API](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) exemple d’application, [référence de classe UIPreviewAction](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/), [référence de classe UIPreviewActionGroup](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) et [UIPreviewActionItem Référence du protocole](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/).

<a name="Quick-Actions" />

## <a name="quick-actions"></a>Actions rapides

Utilisez 3D Touch et Actions rapides, vous pouvez ajouter courants, rapide et facile aux raccourcis de l’accès aux fonctions dans votre application à partir de l’icône d’écran d’accueil sur l’appareil iOS.

Comme indiqué ci-dessus, vous pouvez considérer des Actions rapides, comme les menus contextuels qui peuvent être dépilés à distance quand un utilisateur clique sur un élément dans une application de bureau. Vous devez utiliser des Actions rapides pour fournir des raccourcis vers les fonctions ou les fonctionnalités de votre application plus courants.

[![](3d-touch-images/quickactions01.png "Un exemple d’un menu d’Actions rapides")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>Définition des Actions rapides statiques

Si une ou plusieurs des Actions rapides requis par votre application sont statiques et n’avez pas besoin de modifier, vous pouvez les définir dans l’application `Info.plist` fichier. Modifiez ce fichier dans un éditeur externe et ajoutez les clés suivantes :

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

Ici nous allons la définir deux éléments d’Action rapide statiques avec les clés suivantes :

- `UIApplicationShortcutItemIconType` -Définit l’icône qui sera affichée par l’élément d’Action rapide comme l’une des valeurs suivantes :
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

        ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

* `UIApplicationShortcutItemSubtitle` -Définit le sous-titre pour l’élément.
* `UIApplicationShortcutItemTitle` -Définit le titre de l’élément.
* `UIApplicationShortcutItemType` -Est une valeur de chaîne que nous utiliserons pour identifier l’élément dans notre application. Pour plus d'informations, consultez la section suivante.

> [!IMPORTANT]
> Éléments de raccourci Action rapides qui sont définies dans le `Info.plist` ne peut pas accéder au fichier avec le `Application.ShortcutItems` propriété. Elles sont transmises uniquement à la `HandleShortcutItem` Gestionnaire d’événements.





### <a name="identifying-quick-action-items"></a>Identification des éléments d’Action rapide

Comme vous l’avez vu ci-dessus, lorsque vous définissez vos éléments d’Action rapide de votre application `Info.plist`, vous avez affecté une valeur de chaîne à la `UIApplicationShortcutItemType` clé pour les identifier.

Pour faciliter ces identificateurs à utiliser dans le code, ajoutez une classe appelée `ShortcutIdentifier` à du votre application projet et qu’elle ressemble à ce qui suit :

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

### <a name="handling-a-quick-action"></a>Une Action rapide de gestion

Ensuite, vous devez modifier votre application `AppDelegate.cs` fichier pour gérer l’utilisateur en sélectionnant un élément Action rapide à partir de l’icône de votre application sur l’écran d’accueil.

Effectuez les modifications suivantes :

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

Tout d’abord, nous définissons un public `LaunchedShortcutItem` propriété pour suivre le dernier élément d’Action rapide sélectionné par l’utilisateur. Ensuite, nous remplaçons le `FinishedLaunching` méthode et voir si `launchOptions` ont été transmis et si elles contient un élément Action rapide. S’ils le font, nous stockons l’Action rapide dans le `LaunchedShortcutItem` propriété.

Ensuite, nous remplaçons le `OnActivated` méthode et passe une sélectionnée d’élément de lancement rapide à la `HandleShortcutItem` méthode à être traitées. Actuellement nous écrivons uniquement un message à la **Console**. Dans une application réelle, vous pouvez gérer l’action qui a été nécessaire. Une fois que l’action a été effectuée, le `LaunchedShortcutItem` propriété est désactivée.

Enfin, si votre application a été déjà en cours d’exécution, le `PerformActionForShortcutItem` portera la méthode pour gérer l’élément Action rapide, nous devons remplacer et appeler notre `HandleShortcutItem` méthode ici aussi.


### <a name="creating-dynamic-quick-action-items"></a>Création d’actions rapide dynamique

En plus de définir l’Action rapide statique des éléments de votre application `Info.plist` fichier, vous pouvez créer dynamique à la volée Actions rapides. Pour définir les deux nouvelles Actions rapide dynamique, modifiez votre `AppDelegate.cs` à nouveau de fichiers et de modifier le `FinishedLaunching` méthode à rechercher comme suit :

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

Maintenant nous vérifions si le `application` contient déjà créés de façon dynamique un ensemble de `ShortcutItems`, si ce n’est pas nous allons créer deux nouveaux `UIMutableApplicationShortcutItem` objets pour définir les nouveaux éléments et de les ajouter à la `ShortcutItems` tableau.

Le code que nous avons ajouté déjà dans le [une Action rapide de gestion](#Handling-a-Quick-Action) section ci-dessus gère ces Actions rapides dynamique comme celles statiques.

Il convient de noter que vous pouvez créer un mélange d’éléments d’Action rapide statiques et dynamiques (comme nous le faisons ici), vous n’êtes pas limité à un ou l’autre.

Pour plus d’informations, consultez notre [iOS 9 ViewControllerPreview exemple](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/) et consultez d’Apple [ApplicationShortcuts : À l’aide de UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) exemple d’application, [référence de classe UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/), [référence de classe UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) et [ Référence de classe de UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/).

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>Test 3D Touch dans le simulateur

Lorsqu’à l’aide de la dernière version de Xcode et le simulateur iOS sur un Mac compatible avec une Force Touch activez pavé tactile, vous pouvez tester les fonctionnalités tactiles 3D dans le simulateur.

Pour activer cette fonctionnalité, exécutez n’importe quelle application dans le matériel simulé iPhone qui prend en charge 3D Touch (iPhone 6 s et versions supérieures). Ensuite, sélectionnez le **matériel** menu dans iOS Simulator et activer le **utilisez pavé tactile Force pour 3D touch** élément de menu :

[![](3d-touch-images/simulator01.png "Sélectionnez le menu de matériel dans le simulateur iOS et activer la Force de pavé tactile d’utilisation pour l’élément de menu 3D touch")](3d-touch-images/simulator01.png#lightbox)

Avec cette fonctionnalité active, vous pouvez appuyer sur plus difficile sur le pavé tactile du Mac pour activer la 3D Touch comme vous le feriez sur un matériel réel iPhone.

## <a name="summary"></a>Récapitulatif

Cet article a présenté les nouvelles 3D Touch API mis à disposition dans iOS 9 pour l’iPhone 6 s et iPhone 6 s Plus. Elle couvrait la sensibilité à la pression ajout à une application ; à l’aide d’aperçu et Pop pour afficher rapidement les informations dans l’application à partir du contexte actuel sans navigation ; et les fonctionnalités à l’aide des Actions rapides pour fournir des raccourcis vers votre application de plus couramment utilisées.



## <a name="related-links"></a>Liens associés

- [iOS 9 ViewControllerPreview exemple](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts exemple](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Préparer vos applications iPhone 3D Touch](https://developer.apple.com/ios/3d-touch/)
