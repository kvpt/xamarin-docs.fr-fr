---
title: Création d’interfaces utilisateur iOS dans le code dans Xamarin. iOS
description: Ce document explique comment utiliser le code pour créer une interface utilisateur pour une application Xamarin. iOS. Il traite des contrôleurs d’affichage, de la création d’une hiérarchie d’affichage, de la gestion d’une rotation et bien plus encore.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 42a2694239fdd55efa91b7fa30be8a10cafb4cc5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010323"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Création d’interfaces utilisateur iOS dans le code dans Xamarin. iOS

L’interface utilisateur d’une application iOS est semblable à une vitrine : l’application obtient généralement une fenêtre, mais elle peut remplir la fenêtre avec autant d’objets que nécessaire, et les objets et les arrangements peuvent être modifiés en fonction de ce que l’application souhaite afficher. Les objets dans ce scénario, les éléments que l’utilisateur voit, sont appelés des affichages. Pour créer un seul écran dans une application, les vues sont empilées les unes sur les autres dans une hiérarchie d’affichage de contenu, et la hiérarchie est gérée par un contrôleur d’affichage unique. Les applications à plusieurs écrans ont plusieurs hiérarchies d’affichage de contenu, chacune avec son propre contrôleur d’affichage. L’application place les affichages dans la fenêtre pour créer une hiérarchie d’affichage de contenu différente selon l’écran sur lequel se trouve l’utilisateur.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le diagramme ci-dessous illustre les relations entre la fenêtre, les affichages, les sous-affichages et le contrôleur d’affichage qui font apparaître l’interface utilisateur dans l’écran de l’appareil :

[![](ios-code-only-images/image9.png "This diagram illustrates the relationships between the Window, Views, Subviews, and View Controller")](ios-code-only-images/image9.png#lightbox)

Ces hiérarchies de vues peuvent être construites à l’aide de l' [Xamarin Designer pour iOS](~/ios/user-interface/designer/index.md) dans Visual Studio, mais il est judicieux de comprendre comment travailler entièrement dans le code. Cet article présente quelques points de base pour être opérationnel avec le développement de l’interface utilisateur de code uniquement.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Le diagramme ci-dessous illustre les relations entre la fenêtre, les affichages, les sous-affichages et le contrôleur d’affichage qui font apparaître l’interface utilisateur dans l’écran de l’appareil :

[![](ios-code-only-images/image9.png "This diagram illustrates the relationships between the Window, Views, Subviews, and View Controller")](ios-code-only-images/image9.png#lightbox)

Ces hiérarchies de vues peuvent être construites à l’aide de la [Xamarin Designer pour iOS](~/ios/user-interface/designer/index.md) dans Visual Studio pour Mac, mais il est judicieux de comprendre comment travailler entièrement dans le code. Cet article présente quelques points de base pour être opérationnel avec le développement de l’interface utilisateur de code uniquement.

-----

## <a name="creating-a-code-only-project"></a>Création d’un projet de code uniquement

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>modèle de projet vide iOS

Tout d’abord, créez un projet iOS dans Visual Studio à l’aide du **fichier > C# projet > Visual > iPhone & iPad > application iOS (Xamarin)** , comme indiqué ci-dessous :

[![boîte de dialogue Nouveau projet](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Sélectionnez ensuite le modèle de projet **application vide** :

[![boîte de dialogue Sélectionner un modèle](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

Le modèle de projet vide ajoute 4 fichiers au projet :

[![Fichiers projet](ios-code-only-images/empty-project.w157-sml.png "Fichiers projet")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -contient une sous-classe `UIApplicationDelegate`, `AppDelegate`, qui est utilisée pour gérer les événements d’application d’iOS. La fenêtre d’application est créée dans la méthode `FinishedLaunching` de `AppDelegate`.
1. **Main.cs** : contient le point d’entrée de l’application, qui spécifie la classe de l' `AppDelegate`.
1. **Info. plist** : fichier de liste de propriétés qui contient les informations de configuration de l’application.
1. **Habilitations. plist** : fichier de liste de propriétés qui contient des informations sur les fonctionnalités et les autorisations de l’application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="ios-templates"></a>modèles iOS

Visual Studio pour Mac ne fournit pas de modèle vide. Tous les modèles sont fournis avec la prise en charge des storyboards, qu’Apple recommande comme méthode principale pour créer une interface utilisateur. Toutefois, il est possible de créer entièrement votre interface utilisateur dans le code.

Les étapes ci-dessous vous guident lors de la suppression de la table de montage séquentiel d’une application :

1. Utilisez le modèle application avec affichage unique pour créer un nouveau projet iOS :

    [![](ios-code-only-images/single-view-app.png "Use the Single View App template")](ios-code-only-images/single-view-app.png#lightbox)

1. Supprimez les fichiers `Main.Storyboard` et `ViewController.cs`. Ne supprimez **pas** le `LaunchScreen.Storyboard`. Le contrôleur d’affichage doit être supprimé, car il s’agit du code-behind pour le contrôleur d’affichage créé dans le Storyboard :
1. Veillez à sélectionner **supprimer** dans la boîte de dialogue contextuelle :

    [![](ios-code-only-images/delete.png "Select Delete from the pop-up dialog")](ios-code-only-images/delete.png#lightbox)

1. Dans le fichier info. plist, supprimez les informations contenues dans les informations de **déploiement > option d’interface principale** :

    [![](ios-code-only-images/main-interface.png "Delete the information inside the Main Interface option")](ios-code-only-images/main-interface.png#lightbox)

1. Enfin, ajoutez le code suivant à votre méthode `FinishedLaunching` dans la classe AppDelegate :

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
    ```

Le code qui a été ajouté à la méthode `FinishedLaunching` à l’étape 5 ci-dessus est la quantité minimale de code nécessaire pour créer une fenêtre pour votre application iOS.

-----

les applications iOS sont générées à l’aide du [modèle MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). Le premier écran affiché par une application est créé à partir du contrôleur d’affichage racine de la fenêtre. Pour plus d’informations sur le modèle MVC lui-même, consultez le guide [Hello, iOS multi-écran](~/ios/get-started/hello-ios-multiscreen/index.md) .

L’implémentation de la `AppDelegate` ajoutée par le modèle crée la fenêtre d’application, dont il n’y en a qu’une pour chaque application iOS, et la rend visible avec le code suivant :

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Si vous deviez exécuter cette application maintenant, vous obtiendrez probablement une exception qui indique que `Application windows are expected to have a root view controller at the end of application launch`. Nous allons ajouter un contrôleur et en faire le contrôleur d’affichage racine de l’application.

## <a name="adding-a-controller"></a>Ajout d’un contrôleur

Votre application peut contenir de nombreux contrôleurs d’affichage, mais elle doit disposer d’un contrôleur d’affichage racine pour contrôler tous les contrôleurs d’affichage.  Ajoutez un contrôleur à la fenêtre en créant une instance de `UIViewController` et en la définissant sur la propriété `Window.RootViewController` :

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Chaque contrôleur est associé à une vue, accessible à partir de la propriété `View`. Le code ci-dessus modifie la propriété `BackgroundColor` de la vue en `UIColor.LightGray` afin qu’elle soit visible, comme indiqué ci-dessous :

 [![](ios-code-only-images/image1.png "The View's background is a visible light gray")](ios-code-only-images/image1.png#lightbox)

Nous pouvons également définir une sous-classe `UIViewController` comme `RootViewController` de cette manière, y compris les contrôleurs de UIKit, ainsi que ceux que nous écrivons nous-mêmes. Par exemple, le code suivant ajoute un `UINavigationController` comme `RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Cela produit le contrôleur imbriqué dans le contrôleur de navigation, comme indiqué ci-dessous :

 [![](ios-code-only-images/image2.png "The controller nested within the navigation controller")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Création d’un contrôleur d’affichage

Maintenant que nous avons vu comment ajouter un contrôleur en tant que `RootViewController` de la fenêtre, voyons comment créer un contrôleur d’affichage personnalisé dans le code.

Ajoutez une nouvelle classe nommée `CustomViewController` comme indiqué ci-dessous :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Add a new class named CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "Add a new class named CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

La classe doit hériter de `UIViewController`, qui se trouve dans l’espace de noms `UIKit`, comme indiqué ci-dessous :

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

## <a name="initializing-the-view"></a>Initialisation de la vue

`UIViewController` contient une méthode appelée `ViewDidLoad` qui est appelée lorsque le contrôleur d’affichage est chargé pour la première fois en mémoire. Il s’agit d’un emplacement approprié pour l’initialisation de la vue, par exemple la définition de ses propriétés.

Par exemple, le code suivant ajoute un bouton et un gestionnaire d’événements pour envoyer un nouveau contrôleur d’affichage vers la pile de navigation lorsque le bouton est enfoncé :

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

Pour charger ce contrôleur dans votre application et illustrer la navigation simple, créez une nouvelle instance de `CustomViewController`. Créez un nouveau contrôleur de navigation, transmettez votre instance de contrôleur d’affichage et définissez le nouveau contrôleur de navigation sur le `RootViewController` de la fenêtre dans le `AppDelegate` comme précédemment :

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Désormais, lorsque l’application se charge, le `CustomViewController` est chargé à l’intérieur d’un contrôleur de navigation :

 [![](ios-code-only-images/customvc.png "The CustomViewController is loaded inside a navigation controller")](ios-code-only-images/customvc.png#lightbox)

En cliquant sur le bouton, _envoie_ un nouveau contrôleur d’affichage vers la pile de navigation :

[![](ios-code-only-images/customvca.png "A new View Controller pushed onto the navigation stack")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Génération de la hiérarchie d’affichage

Dans l’exemple ci-dessus, nous avons commencé à créer une interface utilisateur dans le code en ajoutant un bouton au contrôleur d’affichage.

les interfaces utilisateur iOS sont constituées d’une hiérarchie d’affichage. Des affichages supplémentaires, tels que des étiquettes, des boutons, des curseurs, etc. sont ajoutés en tant que sous-affichages d’une vue parent.

Par exemple, nous allons modifier les `CustomViewController` pour créer un écran de connexion dans lequel l’utilisateur peut entrer un nom d’utilisateur et un mot de passe. L’écran se compose de deux champs de texte et d’un bouton.

### <a name="adding-the-text-fields"></a>Ajout des champs de texte

Tout d’abord, supprimez le bouton et le gestionnaire d’événements qui a été ajouté dans la section [initialisation de la vue](#initializing-the-view) . 

Ajoutez un contrôle pour le nom d’utilisateur en créant et en initialisant un `UITextField` puis en l’ajoutant à la hiérarchie d’affichage, comme indiqué ci-dessous :

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

Lorsque nous créons la `UITextField`, nous définissons la propriété `Frame` pour définir son emplacement et sa taille. Dans iOS, la coordonnée 0,0 se trouve dans le coin supérieur gauche avec + x à droite et + y en dessous. Après avoir défini la `Frame` avec d’autres propriétés, nous appelons `View.AddSubview` pour ajouter la `UITextField` à la hiérarchie d’affichage. Cela fait de la `usernameField` une sous-vue de l’instance de `UIView` référencée par la propriété `View`. Une sous-vue est ajoutée avec un ordre de plan supérieur à sa vue parente. elle apparaît donc devant la vue parente sur l’écran.

L’application avec le `UITextField` inclus est indiquée ci-dessous :

 [![](ios-code-only-images/image4.png "The application with the UITextField included")](ios-code-only-images/image4.png#lightbox)

Nous pouvons ajouter une `UITextField` pour le mot de passe de la même manière, nous définissons la propriété `SecureTextEntry` sur true, comme indiqué ci-dessous :

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField);
      View.AddSubview(passwordField);
   }
}

```

La définition de `SecureTextEntry = true` masque le texte entré dans la `UITextField` par l’utilisateur, comme indiqué ci-dessous :

 [![](ios-code-only-images/image4a.png "Setting SecureTextEntry true hides the text entered by the user")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Ajout du bouton

Ensuite, nous allons ajouter un bouton afin que l’utilisateur puisse envoyer le nom d’utilisateur et le mot de passe. Le bouton est ajouté à la hiérarchie d’affichage comme n’importe quel autre contrôle, en le transmettant en tant qu’argument à la méthode `AddSubview` de la vue parente.

Le code suivant ajoute le bouton et inscrit un gestionnaire d’événements pour l’événement `TouchUpInside` :

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

Avec cette mise en place, l’écran de connexion s’affiche maintenant comme indiqué ci-dessous :

 [![](ios-code-only-images/image5.png "The login screen")](ios-code-only-images/image5.png#lightbox)

Contrairement aux versions précédentes d’iOS, l’arrière-plan du bouton par défaut est transparent. La modification de la propriété `BackgroundColor` du bouton modifie ce qui suit :

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Cela se traduira par un bouton carré plutôt que par un bouton bords arrondis classique. Pour obtenir le bord arrondi, utilisez l’extrait de code suivant :

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Avec ces modifications, la vue se présente comme suit :

[![](ios-code-only-images/image6.png "An example run of the view")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Ajout de plusieurs vues à la hiérarchie d’affichage

iOS offre une fonctionnalité permettant d’ajouter plusieurs vues à la hiérarchie d’affichage à l’aide de `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Ajout de la fonctionnalité de bouton

Lorsque vous cliquez sur un bouton, vos utilisateurs s’attendent à ce qu’un événement se produise. Par exemple, une alerte est affichée ou la navigation est effectuée sur un autre écran.

Nous allons ajouter du code pour pousser un deuxième contrôleur d’affichage sur la pile de navigation.

Tout d’abord, créez le deuxième contrôleur d’affichage :

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Ajoutez ensuite la fonctionnalité à l’événement `TouchUpInside` :

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

La navigation est illustrée ci-dessous :

[![](ios-code-only-images/navigation.png "The navigation is illustrated in this chart")](ios-code-only-images/navigation.png#lightbox)

Notez que, par défaut, lorsque vous utilisez un contrôleur de navigation, iOS donne à l’application une barre de navigation et un bouton précédent pour vous permettre de remonter dans la pile.

## <a name="iterating-through-the-view-hierarchy"></a>Itération dans la hiérarchie d’affichage

Il est possible d’itérer au sein de la hiérarchie de la sous-vue et de choisir une vue particulière. Par exemple, pour rechercher chaque `UIButton` et attribuer un autre `BackgroundColor`à ce bouton, vous pouvez utiliser l’extrait de code suivant.

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

Toutefois, cela ne fonctionnera pas si la vue à itérer est un `UIView`, car tous les affichages reviendront en tant que `UIView`, car les objets ajoutés à la vue parente héritent `UIView`.

## <a name="handling-rotation"></a>Gestion de la rotation

Si l’utilisateur fait pivoter l’appareil en paysage, les contrôles ne sont pas redimensionnés correctement, comme le montre la capture d’écran suivante :

[![](ios-code-only-images/image7.png "If the user rotates the device to landscape, the controls do not resize appropriately")](ios-code-only-images/image7.png#lightbox)

Une façon de résoudre ce problème consiste à définir la propriété `AutoresizingMask` sur chaque vue. Dans ce cas, nous voulons que les contrôles soient étirés horizontalement, de sorte que nous définissons chaque `AutoresizingMask`. L’exemple suivant est destiné à `usernameField`, mais il doit être appliqué à chaque gadget dans la hiérarchie d’affichage.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

À présent, lorsque vous faites pivoter l’appareil ou le simulateur, tout est étiré pour remplir l’espace supplémentaire, comme indiqué ci-dessous :

[![](ios-code-only-images/image8.png "All the controls stretch to fill the additional space")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Création de vues personnalisées

Outre l’utilisation de contrôles qui font partie de UIKit, les vues personnalisées peuvent également être utilisées. Vous pouvez créer une vue personnalisée en héritant de `UIView` et en remplaçant `Draw`. Nous allons créer une vue personnalisée et l’ajouter à la hiérarchie d’affichage pour la démonstration.

### <a name="inheriting-from-uiview"></a>Héritage à partir de UIView

La première chose à faire est de créer une classe pour la vue personnalisée. Pour ce faire, nous allons utiliser le modèle de **classe** dans Visual Studio pour ajouter une classe vide nommée `CircleView`. La classe de base doit être définie sur `UIView`, que nous rappelons dans l’espace de noms `UIKit`. Nous aurons également besoin de l’espace de noms `System.Drawing`. Les autres espaces de noms `System.*` ne seront pas utilisés dans cet exemple. n’hésitez donc pas à les supprimer.

La classe doit ressembler à ceci :

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>Dessin dans un UIView

Chaque `UIView` a une méthode `Draw` qui est appelée par le système lorsqu’elle doit être dessinée. `Draw` ne doit jamais être appelé directement. Elle est appelée par le système lors du traitement de la boucle d’exécution. La première fois que vous exécutez la boucle Run après l’ajout d’une vue à la hiérarchie de la vue, sa méthode `Draw` est appelée. Les appels suivants à `Draw` se produisent lorsque la vue est marquée comme devant être dessinée en appelant `SetNeedsDisplay` ou `SetNeedsDisplayInRect` sur la vue.

Nous pouvons ajouter du code de dessin à notre vue en ajoutant un tel code à l’intérieur de la méthode `Draw` substituée, comme indiqué ci-dessous :

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

Étant donné que `CircleView` est un `UIView`, nous pouvons également définir des propriétés `UIView`. Par exemple, nous pouvons définir le `BackgroundColor` dans le constructeur :

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Pour utiliser le `CircleView` que nous venons de créer, nous pouvons l’ajouter en tant que sous-affichage à la hiérarchie d’affichage dans un contrôleur existant, comme nous l’avons fait avec le `UILabels` et le `UIButton` précédemment, ou nous pouvons le charger en tant que vue d’un nouveau contrôleur. Commençons par ce dernier.

### <a name="loading-a-view"></a>Chargement d’une vue

 `UIViewController` a une méthode nommée `LoadView` qui est appelée par le contrôleur pour créer sa vue. Il s’agit d’un emplacement approprié pour créer une vue et l’assigner à la propriété `View` du contrôleur.

Tout d’abord, nous avons besoin d’un contrôleur. vous devez donc créer une classe vide nommée `CircleController`.

Dans `CircleController` ajoutez le code suivant pour définir la `View` à une `CircleView` (vous ne devez pas appeler l’implémentation `base` dans votre remplacement) :

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

Enfin, nous devons présenter le contrôleur au moment de l’exécution. Pour ce faire, ajoutez un gestionnaire d’événements sur le bouton Submit que nous avons ajouté précédemment, comme suit :

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Maintenant, lorsque nous exécutons l’application et que vous appuyez sur le bouton envoyer, la nouvelle vue avec un cercle s’affiche :

[![](ios-code-only-images/circles.png "The new view with a circle is displayed")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Création d’un écran de lancement

Un [écran de lancement](~/ios/app-fundamentals/images-icons/launch-screens.md) s’affiche au démarrage de votre application comme un moyen d’afficher à vos utilisateurs qu’il est réactif. Comme un écran de lancement s’affiche lors du chargement de votre application, il ne peut pas être créé dans le code, car l’application est toujours en cours de chargement en mémoire.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quand vous créez un projet iOS dans Visual Studio, un écran de lancement vous est fourni sous la forme d’un fichier. XIB, qui se trouve dans le dossier **ressources** de votre projet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Lorsque vous créez un projet iOS dans Visual Studio pour Mac, un écran de lancement vous est fourni sous la forme d’un fichier de Storyboard.

-----

Vous pouvez le modifier en double-cliquant dessus et en l’ouvrant dans le concepteur iOS.

Apple recommande l’utilisation d’un fichier. XIB ou Storyboard pour les applications qui ciblent iOS 8 ou version ultérieure. quand vous lancez l’un des deux fichiers dans le concepteur iOS, vous utilisez des classes de taille et une disposition automatique pour adapter votre disposition afin qu’il soit correct et s’affiche correctement pour tous les appareils tailles. Une image de lancement statique peut être utilisée en plus d’un. XIB ou d’un Storyboard pour permettre la prise en charge des applications ciblant des versions antérieures.

Pour plus d’informations sur la création d’un écran de lancement, reportez-vous aux documents ci-dessous :

- [Création d’un écran de lancement à l’aide d’un. XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Gestion des écrans de lancement avec les storyboards](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> Depuis iOS 9, Apple recommande d’utiliser les storyboards comme méthode principale de création d’un écran de lancement.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Création d’une image de lancement pour les applications pré-iOS 8

Une image statique peut être utilisée en plus de l’écran de lancement d’un. XIB ou d’un Storyboard si votre application cible des versions antérieures à iOS 8.

Cette image statique peut être définie dans le fichier info. plist ou en tant que catalogue de composants (pour iOS 7) dans votre application. Vous devrez fournir des images distinctes pour chaque taille d’appareil (320x480, 640x960, 640x1136) sur laquelle votre application peut s’exécuter. Pour plus d’informations sur les tailles d’écran de lancement, consultez le guide [lancer des images d’écran](~/ios/app-fundamentals/images-icons/launch-screens.md) .

> [!IMPORTANT]
> Si votre application n’a pas d’écran de lancement, vous remarquerez peut-être qu’elle ne s’ajuste pas entièrement à l’écran. Si c’est le cas, vous devez veiller à inclure au moins une image 640x1136 nommée `Default-568@2x.png` à votre fichier info. plist.

## <a name="summary"></a>Récapitulatif

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Cet article a expliqué comment développer des applications iOS par programmation dans Visual Studio. Nous avons vu comment créer un projet à partir d’un modèle de projet vide, en expliquant comment créer et ajouter un contrôleur d’affichage racine à la fenêtre. Nous avons ensuite montré comment utiliser des contrôles à partir de UIKit pour créer une hiérarchie d’affichage au sein d’un contrôleur afin de développer un écran d’application. Nous avons ensuite examiné comment faire en sorte que les vues se présentent correctement dans différentes orientations et nous avons vu comment créer une vue personnalisée en sous-classant `UIView`, ainsi que la façon de charger la vue dans un contrôleur. Enfin, nous avons expliqué comment ajouter un écran de lancement à une application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Cet article a expliqué comment développer des applications iOS par programmation dans Visual Studio pour Mac. Nous avons vu comment créer un projet à partir d’un modèle de vue unique, en expliquant comment créer et ajouter un contrôleur d’affichage racine à la fenêtre. Nous avons ensuite montré comment utiliser des contrôles à partir de UIKit pour créer une hiérarchie d’affichage au sein d’un contrôleur afin de développer un écran d’application. Nous avons ensuite examiné comment faire en sorte que les vues se présentent correctement dans différentes orientations et nous avons vu comment créer une vue personnalisée en sous-classant `UIView`, ainsi que la façon de charger la vue dans un contrôleur. Enfin, nous avons expliqué comment ajouter un écran de lancement à une application.

-----

## <a name="related-links"></a>Liens associés

- [SimpleLogin (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
