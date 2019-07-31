---
title: Création d’Interfaces utilisateur d’iOS dans le Code dans Xamarin.iOS
description: Ce document décrit comment utiliser le code pour générer une interface utilisateur pour une application Xamarin.iOS. Il aborde les contrôleurs d’affichage, création d’une hiérarchie d’affichage, gère une rotation et bien plus encore.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 105d06c7cea23ac0873c8de68d7ad1ad62ae1ffc
ms.sourcegitcommit: 84764b9c51e769d6d6570a362af8451607c7e0d2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665716"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Création d’interfaces utilisateur iOS dans le code dans Xamarin.iOS

L’interface utilisateur d’une application iOS est comme une vitrine : l’application obtient généralement une seule fenêtre, mais elle peut saturer la fenêtre avec comme de nombreux objets car il a besoin, et les objets et peut être modifié en fonction de ce que l’application souhaite afficher. Les objets dans ce scénario, les éléments que l’utilisateur voit, sont appelés des affichages. Pour générer un seul écran dans une application, les affichages sont empilés les uns sur les autres dans une hiérarchie d’affichage de contenu, et la hiérarchie est gérée par un contrôleur d’affichage unique. Les applications à plusieurs écrans ont plusieurs hiérarchies d’affichage de contenu, chacune avec son propre contrôleur d’affichage. L’application place les affichages dans la fenêtre pour créer une hiérarchie d’affichage de contenu différente selon l’écran sur lequel se trouve l’utilisateur.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le diagramme ci-dessous illustre les relations entre la fenêtre, les affichages, les sous-affichages et le contrôleur d’affichage qui font apparaître l’interface utilisateur dans l’écran de l’appareil :

[![](ios-code-only-images/image9.png "Ce diagramme illustre les relations entre la fenêtre, des vues, sous-vues et contrôleur d’affichage")](ios-code-only-images/image9.png#lightbox)

Ces hiérarchies d’affichage peuvent être construites à l’aide de la [concepteur Xamarin pour iOS](~/ios/user-interface/designer/index.md) dans Visual Studio, il est toutefois d’avoir une compréhension fondamentale de l’utilisation entièrement en code. Cet article décrit certains points de base pour être opérationnel et une en cours d’exécution avec le développement de l’interface utilisateur dans le code uniquement.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Le diagramme ci-dessous illustre les relations entre la fenêtre, les affichages, les sous-affichages et le contrôleur d’affichage qui font apparaître l’interface utilisateur dans l’écran de l’appareil :

[![](ios-code-only-images/image9.png "Ce diagramme illustre les relations entre la fenêtre, des vues, sous-vues et contrôleur d’affichage")](ios-code-only-images/image9.png#lightbox)

Ces hiérarchies d’affichage peuvent être construites à l’aide de la [concepteur Xamarin pour iOS](~/ios/user-interface/designer/index.md) dans Visual Studio pour Mac, cependant il est judicieux d’avoir une compréhension fondamentale de l’utilisation entièrement en code. Cet article décrit certains points de base pour être opérationnel et une en cours d’exécution avec le développement de l’interface utilisateur dans le code uniquement.

-----

## <a name="creating-a-code-only-project"></a>Création d’un projet de code uniquement

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>modèle de nouveau projet iOS

Tout d’abord, créez un projet iOS dans Visual Studio en utilisant le **fichier > Nouveau projet > Visual C# > iPhone & iPad > application iOS (Xamarin)** projet, illustré ci-dessous :

[![Boîte de dialogue Nouveau projet](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Puis sélectionnez le **application vide** modèle de projet :

[![Sélectionnez une boîte de dialogue de modèle](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

Le modèle de projet vide ajoute 4 fichiers au projet :

[![Fichiers projet](ios-code-only-images/empty-project.w157-sml.png "fichiers projet")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -contient un `UIApplicationDelegate` sous-classe, `AppDelegate` , qui est utilisé pour gérer les événements d’application à partir d’iOS. La fenêtre d’application est créée dans le `AppDelegate`de `FinishedLaunching` (méthode).
1. **Main.cs** -contient le point d’entrée pour l’application, qui spécifie la classe pour le `AppDelegate` .
1. **Info.plist** -fichier de liste de propriétés qui contient des informations de configuration d’application.
1. **Entitlements.plist** – fichier de liste de propriétés qui contient des informations sur les fonctionnalités et les autorisations de l’application.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="ios-templates"></a>modèles d’iOS


Visual Studio pour Mac ne fournit pas d’un modèle vide. Tous les modèles sont fournis avec une prise en charge de table de montage séquentiel, qui recommande d’Apple en tant que le principal moyen de créer une interface utilisateur. Toutefois, il est possible de créer votre interface utilisateur entièrement en code.

Les étapes ci-dessous vous guident lors de la suppression de la table de montage séquentiel à partir d’une application :


1. Utilisez le modèle d’application avec affichage unique pour créer un nouveau projet d’iOS :

    [![](ios-code-only-images/single-view-app.png "Utiliser le modèle d’application avec affichage unique")](ios-code-only-images/single-view-app.png#lightbox)

1. Supprimer le `Main.Storyboard` et `ViewController.cs` fichiers. Faire **pas** supprimer le `LaunchScreen.Storyboard`. Le contrôleur d’affichage doit être supprimé car il s’agit du code-behind pour le contrôleur d’affichage qui est créé dans la table de montage séquentiel :
1. Veillez à sélectionner **supprimer** à partir de la boîte de dialogue contextuelle :

    [![](ios-code-only-images/delete.png "Sélectionnez Supprimer dans la boîte de dialogue contextuelle")](ios-code-only-images/delete.png#lightbox)

1. Dans le fichier Info.plist, supprimez les informations à l’intérieur de la **déploiement Info > Interface principale** option :

    [![](ios-code-only-images/main-interface.png "Supprimer les informations à l’intérieur de l’option de l’Interface principale")](ios-code-only-images/main-interface.png#lightbox)

1. Enfin, ajoutez le code suivant à votre `FinishedLaunching` méthode dans la classe AppDelegate :

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

Le code qui a été ajouté à la `FinishedLaunching` méthode à l’étape 5 ci-dessus, est la quantité minimale de code requis pour créer une fenêtre pour votre application iOS.

-----

les applications iOS sont générées à l’aide de la [modèle MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). Le premier écran qui affiche une application est créé à partir du contrôleur d’affichage racine de la fenêtre. Consultez le [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guide pour plus d’informations sur MVC modèle lui-même.

L’implémentation pour le `AppDelegate` ajoutées par le modèle crée la fenêtre d’application, de laquelle il n’est qu’une pour chaque application iOS et le rend visible par le code suivant :

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

Si vous deviez exécuter cette application maintenant, vous obtiendriez probablement une exception levée indiquant que `Application windows are expected to have a root view controller at the end of application launch`. Nous allons ajouter un contrôleur et le rend le contrôleur d’affichage racine de l’application.

## <a name="adding-a-controller"></a>Ajout d’un contrôleur

Votre application peut contenir plusieurs contrôleurs d’affichage, mais il doit avoir un contrôleur d’affichage racine pour contrôler tous les contrôleurs d’affichage.  Ajouter un contrôleur à la fenêtre en créant un `UIViewController` instance et lui affectant la `Window.RootViewController` propriété :

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

Chaque contrôleur possède une vue associée, qui est accessible à partir de la `View` propriété. Le code ci-dessus est modifié de la vue `BackgroundColor` propriété `UIColor.LightGray` afin qu’il soit visible, comme indiqué ci-dessous :

 [![](ios-code-only-images/image1.png "En arrière-plan de la vue est un gris clair visible")](ios-code-only-images/image1.png#lightbox)

Nous pourrions définir toutes `UIViewController` sous-classe comme le `RootViewController` de cette façon également, y compris les contrôleurs à partir de UIKit ainsi qu’aux sites nous écrire nous-mêmes. Par exemple, le code suivant ajoute un `UINavigationController` comme le `RootViewController`:

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

Cela génère le contrôleur imbriqué dans le contrôleur de navigation comme indiqué ci-dessous :

 [![](ios-code-only-images/image2.png "Le contrôleur est imbriqué dans le contrôleur de navigation")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Création d’un contrôleur d’affichage

Maintenant que nous avons vu comment ajouter un contrôleur en tant que le `RootViewController` de la fenêtre, nous allons voir comment créer un contrôleur d’affichage personnalisé dans le code.

Ajoutez une nouvelle classe nommée `CustomViewController` comme indiqué ci-dessous :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Ajoutez une nouvelle classe nommée CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "Ajoutez une nouvelle classe nommée CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

La classe doit hériter de `UIViewController`, qui se trouve dans le `UIKit` espace de noms, comme indiqué :

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

## <a name="initializing-the-view"></a>L’initialisation de la vue

`UIViewController` contient une méthode appelée `ViewDidLoad` qui est appelée lorsque le contrôleur d’affichage est tout d’abord chargé en mémoire. Il s’agit d’un emplacement approprié pour effectuer l’initialisation de la vue, telles que la définition de ses propriétés.

Par exemple, le code suivant ajoute un bouton et un gestionnaire d’événements à envoyer un nouveau contrôleur d’affichage dans la pile de navigation lorsque le bouton est enfoncé :

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

Pour charger ce contrôleur dans votre application et illustrent la navigation simple, créer une nouvelle instance de `CustomViewController`. Créer un nouveau contrôleur de navigation, passez dans votre instance de contrôleur d’affichage et la valeur du nouveau contrôleur de navigation de la fenêtre `RootViewController` dans le `AppDelegate` comme précédemment :

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

Maintenant lorsque l’application chargée, le `CustomViewController` est chargé à l’intérieur d’un contrôleur de navigation :

 [![](ios-code-only-images/customvc.png "Le CustomViewController est chargé à l’intérieur d’un contrôleur de navigation")](ios-code-only-images/customvc.png#lightbox)

En cliquant sur le bouton, sera _push_ un nouveau contrôleur d’affichage dans la pile de navigation :

[![](ios-code-only-images/customvca.png "Un contrôleur d’affichage est placé sur la pile de navigation")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Création de la hiérarchie d’affichage

Dans l’exemple ci-dessus, nous avons commencé à créer une interface utilisateur dans le code en ajoutant un bouton pour le contrôleur d’affichage.

les interfaces utilisateur iOS sont constituées d’une hiérarchie d’affichage. Affichages supplémentaires, telles que des étiquettes, des boutons, des curseurs, etc. sont ajoutés comme sous-vues de certaines vue parent.

Par exemple, nous allons modifier le `CustomViewController` pour créer un écran de connexion où l’utilisateur peut entrer un nom d’utilisateur et le mot de passe. L’écran se compose de deux champs de texte et un bouton.

### <a name="adding-the-text-fields"></a>Ajouter les champs de texte

Tout d’abord, supprimer le Gestionnaire d’événements et de bouton qui a été ajouté dans le [l’initialisation de la vue](#initializing-the-view) section. 

Ajouter un contrôle pour le nom d’utilisateur en créant et en initialisant une `UITextField` et en l’ajoutant à la hiérarchie d’affichage, comme indiqué ci-dessous :

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

Lorsque nous créons le `UITextField`, nous définissons le `Frame` propriété à définir son emplacement et sa taille. Dans iOS coordonnées 0,0 se trouve dans le coin supérieur gauche avec + x à droite et + y vers le bas. Après avoir défini le `Frame` , ainsi que quelques autres propriétés, nous appelons `View.AddSubview` pour ajouter le `UITextField` à la hiérarchie d’affichage. Cela rend la `usernameField` un sous-affichage de la `UIView` de l’instance qui le `View` références de propriété. Un sous-affichage est ajouté avec un ordre de plan est supérieur à la vue parent, donc il s’affiche devant la vue parent à l’écran.

L’application avec le `UITextField` inclus est indiqué ci-dessous :

 [![](ios-code-only-images/image4.png "L’application avec le champ UITextField inclus")](ios-code-only-images/image4.png#lightbox)

Nous pouvons ajouter un `UITextField` le mot de passe de la même manière, seulement cette fois-ci, nous définissons le `SecureTextEntry` propriété sur true, comme indiqué ci-dessous :

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

Paramètre `SecureTextEntry = true` masque le texte entré dans le `UITextField` par l’utilisateur, comme indiqué ci-dessous :

 [![](ios-code-only-images/image4a.png "Paramètre SecureTextEntry true masque le texte entré par l’utilisateur")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Ajout du bouton

Ensuite, nous allons ajouter un bouton afin de l’utilisateur peut envoyer le nom d’utilisateur et le mot de passe. Le bouton est ajouté à la hiérarchie d’affichage comme tout autre contrôle, en passant en tant qu’argument à la vue parent `AddSubview` méthode à nouveau.

Le code suivant ajoute le bouton et inscrit un gestionnaire d’événements pour le `TouchUpInside` événement :

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

Cela en place, l’écran de connexion s’affiche maintenant comme indiqué ci-dessous :

 [![](ios-code-only-images/image5.png "L’écran de connexion")](ios-code-only-images/image5.png#lightbox)

Contrairement à dans les versions précédentes d’e/s, l’arrière-plan du bouton par défaut est transparent. Modification du bouton `BackgroundColor` propriété change tout cela :

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Ainsi, un bouton carré plutôt que classique arrondi à bord de bouton. Pour obtenir le bord arrondi, utilisez l’extrait de code suivant :

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Avec ces modifications, la vue ressemblera à ceci :

[![](ios-code-only-images/image6.png "Une exécution de l’exemple de la vue")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Ajout de plusieurs vues à la hiérarchie d’affichage

iOS fournit une fonctionnalité pour ajouter plusieurs vues à la hiérarchie d’affichage à l’aide de `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Ajout de fonctionnalités de bouton

Lorsqu’un clic est effectué, vos utilisateurs attendront quelque chose se produise. Par exemple, une alerte est indiquée ou navigation est effectuée à un autre écran.

Nous allons ajouter du code pour pousser un deuxième contrôleur d’affichage vers la pile de navigation.

Tout d’abord, créez le deuxième contrôleur d’affichage :

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Ensuite, ajoutez la fonctionnalité à la `TouchUpInside` événement :

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

Le volet de navigation est illustrée ci-dessous :

[![](ios-code-only-images/navigation.png "Le volet de navigation est illustrée dans ce graphique")](ios-code-only-images/navigation.png#lightbox)

Notez que par défaut, lorsque vous utilisez un contrôleur de Navigation iOS donne à l’application une barre de navigation et un bouton Précédent pour permettre de vous déplacer dans la pile.

## <a name="iterating-through-the-view-hierarchy"></a>Itération au sein de la hiérarchie d’affichage

Il est possible d’effectuer une itération dans la hiérarchie sous-affichage et choisir n’importe quelle vue particulière. Par exemple, pour rechercher chaque `UIButton` et donnez à ce bouton est une autre `BackgroundColor`, l’extrait de code suivant peut être utilisé.

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

Cela, toutefois ne fonctionne pas si la vue en cours d’itération est un `UIView` comme toutes les vues reviendra comme étant un `UIView` en tant que les objets ajoutés à la vue parent eux-mêmes héritent `UIView`.

## <a name="handling-rotation"></a>Rotation de la gestion

Si l’utilisateur fait pivoter l’appareil en mode paysage, les contrôles ne se redimensionnent pas correctement, comme l’illustre la capture d’écran suivante :

[![](ios-code-only-images/image7.png "Si l’utilisateur fait pivoter l’appareil en mode paysage, les contrôles ne se redimensionnent pas correctement")](ios-code-only-images/image7.png#lightbox)

La première consiste à résoudre ce problème en définissant le `AutoresizingMask` propriété sur chaque affichage. Dans ce cas nous voulons les contrôles à étendre horizontalement, donc nous affectons chacun `AutoresizingMask`. L’exemple suivant concerne `usernameField`, mais les mêmes doivent être appliquées à chaque gadget dans la hiérarchie d’affichage.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

Maintenant quand nous faites pivoter le simulateur ou l’appareil, tous les éléments s’étire pour remplir l’espace supplémentaire, comme indiqué ci-dessous :

[![](ios-code-only-images/image8.png "Tous les contrôles s’étire pour remplir l’espace supplémentaire")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Création de vues personnalisées

Outre l’utilisation de contrôles qui font partie de UIKit, des vues personnalisées peuvent également servir. Une vue personnalisée peut être créée en héritant de `UIView` et en remplaçant `Draw`. Nous allons créer une vue personnalisée et l’ajouter à la hiérarchie d’affichage pour montrer.

### <a name="inheriting-from-uiview"></a>Héritage de UIView

La première chose que nous devons faire est créer une classe pour la vue personnalisée. Nous allons pour cela à l’aide de la **classe** modèle dans Visual Studio pour ajouter une classe vide nommée `CircleView`. La classe de base doit être définie sur `UIView`, qui nous rappeler se trouve dans le `UIKit` espace de noms. Nous aurons également besoin du `System.Drawing` espace de noms. Les différents autres `System.*` espaces de noms ne sera pas utilisé dans cet exemple, par conséquent, n’hésitez pas à les supprimer.

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

### <a name="drawing-in-a-uiview"></a>Dessin dans une UIView

Chaque `UIView` a un `Draw` méthode est appelée par le système lorsqu’il doit être dessiné. `Draw` ne doit jamais être appelée directement. Elle est appelée par le système au cours du traitement de la boucle d’exécution. La première fois dans la boucle d’exécution après une vue est ajoutée à la hiérarchie d’affichage, son `Draw` méthode est appelée. Les appels suivants à `Draw` se produisent lorsque la vue est marquée comme devant être dessiné en appelant `SetNeedsDisplay` ou `SetNeedsDisplayInRect` sur la vue.

Nous pouvons ajouter du code de dessin à notre vue en ajoutant ce code à l’intérieur de l’élément substitué `Draw` méthode comme indiqué ci-dessous :

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

Dans la mesure où `CircleView` est un `UIView`, nous pouvons également définir `UIView` également des propriétés. Par exemple, nous pouvons définir le `BackgroundColor` dans le constructeur :

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Pour utiliser le `CircleView` nous venons de créer, nous pouvons soit ajouter en tant qu’un sous-affichage à la hiérarchie d’affichage dans un contrôleur existant, comme nous l’avons fait avec le `UILabels` et `UIButton` précédemment, ou vous pouvez le charger en tant que l’affichage d’un nouveau contrôleur. Nous allons suivre ce dernier.

### <a name="loading-a-view"></a>Le chargement d’une vue

 `UIViewController` a une méthode nommée `LoadView` qui est appelée par le contrôleur pour créer sa vue. Il s’agit d’un emplacement approprié pour créer une vue et affectez-le à du contrôleur `View` propriété.

Tout d’abord, nous avons besoin d’un contrôleur, afin de créer une nouvelle classe vide nommée `CircleController`.

Dans `CircleController` ajoutez le code suivant pour définir le `View` à un `CircleView` (vous ne devez pas appeler le `base` mise en œuvre dans votre remplacement) :

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

Enfin, nous avons besoin présenter le contrôleur lors de l’exécution. Nous allons faire cela en ajoutant un gestionnaire d’événements sur le bouton envoyer que nous avons ajouté précédemment, comme suit :

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

Maintenant, quand nous pouvons exécuter l’application et que vous appuyez sur le bouton Envoyer, la nouvelle vue avec un cercle s’affiche :

[![](ios-code-only-images/circles.png "La nouvelle vue avec un cercle s’affiche.")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Création d’un écran de lancement

Un [écran de lancement](~/ios/app-fundamentals/images-icons/launch-screens.md) s’affiche au démarrage de votre application comme un moyen d’afficher à vos utilisateurs qu’il est réactif. Comme un écran de démarrage est affiché lors du chargement de votre application, il ne peut pas créé dans le code, car l’application est toujours en cours chargée en mémoire.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Lorsque vous créez un projet iOS dans Visual Studio, un écran de lancement vous est fourni sous la forme d’un fichier .xib, qui se trouve dans le **ressources** dossier à l’intérieur de votre projet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Lorsque vous créez un projet iOS dans Visual Studio pour Mac, un écran de lancement est fourni sous la forme d’un fichier de Storyboard.

-----

Cela peut être modifié en double cliquant dessus et en l’ouvrant dans le concepteur iOS.

Apple recommande un .xib ou un fichier d’animation est utilisé pour les applications ciblant iOS 8 ou une version ultérieure, lorsque vous lancez des fichiers dans le concepteur iOS, vous utiliserez les Classes de taille et de la disposition automatique pour l’adapter à votre disposition afin qu’il vous semble correct et s’affiche correctement pour tous les appareils tailles. Une image de lancement statique peut être utilisée en plus un .xib ou d’une table de montage séquentiel pour permettre la prise en charge pour les applications qui ciblent des versions antérieures.

Pour plus d’informations sur la création d’un écran de lancement, consultez les documents ci-dessous :

- [Création d’un écran de lancement à l’aide d’un .xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [La gestion des écrans de lancement avec des Storyboards](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> À compter d’iOS 9, Apple recommande que les tables de montage séquentiel doivent être utilisés comme méthode principale de la création d’un écran de lancement.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Création d’une image de lancement pour iOS préliminaire 8 applications

Une image statique peut être utilisée en plus un .xib ou d’un écran de lancement de Storyboard si votre application cible des versions antérieures à iOS 8.

Cette image statique peut être définie dans le fichier Info.plist, ou comme un catalogue de ressources (pour iOS 7) dans votre application. Vous devrez fournir des images distinctes pour chaque taille de périphérique (320 x 480, 640 x 960, 640 x 1136) que votre application peut s’exécuter sur. Pour plus d’informations sur les tailles d’écran de lancement, afficher le [Images d’écran de lancement](~/ios/app-fundamentals/images-icons/launch-screens.md) guide.

> [!IMPORTANT]
> Si votre application ne dispose d’aucun écran de lancement, vous pouvez remarquer qu’il ne s’adapte complètement l’écran. Si c’est le cas, vous devez vous assurer d’inclure au moins, une image de 640 x 1136 nommée `Default-568@2x.png` à votre fichier Info.plist.

## <a name="summary"></a>Récapitulatif

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Cet article a expliqué comment développer des applications iOS par programmation dans Visual Studio. Nous avons vu comment créer un projet à partir d’un modèle de projet vide, expliquant comment créer et ajouter un contrôleur d’affichage racine à la fenêtre. Ensuite, nous avons montré comment utiliser des contrôles à partir de UIKit pour créer une hiérarchie d’affichage d’un contrôleur pour développer un écran de l’application. Ensuite nous avons examiné comment apporter les vues de mise en page en conséquence dans différentes orientations et nous avons vu comment créer une vue personnalisée en sous-classant `UIView`, ainsi que la charger la vue d’un contrôleur. Enfin, nous avons exploré comment ajouter un écran de lancement à une application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Cet article a expliqué comment développer des applications iOS par programmation dans Visual Studio pour Mac. Nous avons vu comment créer un projet à partir d’un modèle de vue unique, qui expliquent comment créer et ajouter un contrôleur d’affichage racine à la fenêtre. Ensuite, nous avons montré comment utiliser des contrôles à partir de UIKit pour créer une hiérarchie d’affichage d’un contrôleur pour développer un écran de l’application. Ensuite nous avons examiné comment apporter les vues de mise en page en conséquence dans différentes orientations et nous avons vu comment créer une vue personnalisée en sous-classant `UIView`, ainsi que la charger la vue d’un contrôleur. Enfin, nous avons exploré comment ajouter un écran de lancement à une application.

-----

## <a name="related-links"></a>Liens associés

- [SimpleLogin (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
