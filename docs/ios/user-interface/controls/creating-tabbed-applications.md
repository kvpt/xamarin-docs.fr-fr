---
title: Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin.iOS
description: Ce document décrit les contrôleurs de barre d’onglet iOS et comment les utiliser avec Xamarin.iOS. Il montre comment configurer un UITabBarController, de travailler avec des images, de définir les valeurs de badge, le travail avec des événements et bien plus encore.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 25d8563288cce614bc2823b0146e5121688c6f02
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291830"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin. iOS

Applications à onglets sont utilisées dans iOS pour prendre en charge des interfaces utilisateur où plusieurs écrans sont accessibles dans aucun ordre particulier. Grâce à la classe `UITabBarController`, les applications peuvent facilement inclure la prise en charge de tels scénarios à plusieurs écrans. `UITabBarController` s’occupe de la gestion multi-écran, ce qui permet au développeur d’applications de se concentrer sur les détails de chaque écran.

En règle générale, les applications avec onglets sont générées avec le `UITabBarController` en tant que `RootViewController` de la fenêtre principale. Toutefois, avec un peu de code supplémentaire, les applications à onglets permet également à la suite pour certains autres écran initial, tels que le scénario dans lequel une application présente tout d’abord un écran de connexion, suivi de l’interface à onglets.

Cette page présente les deux scénarios : quand les onglets sont à la racine de la hiérarchie d’affichage de l’application et également dans le scénario non`RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Présentation de UITabBarController

Le `UITabBarController` prend en charge le développement d’applications avec onglets en procédant comme suit :

- Autorise plusieurs contrôleurs à ajouter à ce dernier.
- Fournir une interface utilisateur avec onglets, via la classe `UITabBar`, pour permettre à un utilisateur de basculer entre les contrôleurs et leurs vues.

Les contrôleurs sont ajoutés au `UITabBarController` par le biais de sa propriété `ViewControllers`, qui est un tableau `UIViewController`. Le `UITabBarController` lui-même gère le chargement du contrôleur approprié et la présentation de sa vue en fonction de l’onglet sélectionné.

Les onglets sont des instances de la classe `UITabBarItem`, qui sont contenues dans une instance `UITabBar`. Chaque `UITabBar` instance est accessible via la propriété `TabBarItem` du contrôleur dans chaque onglet.

Pour savoir comment utiliser le `UITabBarController`, examinons la création d’une application simple qui en utilise une.

## <a name="tabbed-application-walkthrough"></a>Procédure pas à pas d’application avec onglets

Pour cette procédure pas à pas, nous allons créer l’application suivante :

[Exemple d’application ![avec onglets](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Bien qu’il existe déjà un modèle d’application avec onglets disponible dans Visual Studio pour Mac, pour cet exemple, ces instructions fonctionnent à partir d’un projet vide pour mieux comprendre la façon dont l’application est construite.

### <a name="creating-the-application"></a>Création de l’application

Commencez par créer une nouvelle application.

Sélectionnez l’élément de menu **fichier > nouvelle > solution** dans Visual Studio pour Mac et sélectionnez une **application > iOS > modèle de projet vide** , nommez le projet `TabbedApplication`, comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Ajout de la UITabBarController

Ensuite, ajoutez une classe vide en sélectionnant **fichier > nouveau fichier** et en choisissant le modèle de **classe général : vide** . Nommez le fichier `TabController` comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

La classe `TabController` contient l’implémentation du `UITabBarController` qui gérera un tableau d' `UIViewControllers`. Lorsque l’utilisateur sélectionne un onglet, le `UITabBarController` s’occupe de présenter la vue pour le contrôleur d’affichage approprié.

Pour implémenter le `UITabBarController` il est nécessaire d’effectuer les opérations suivantes :

1. Définissez la classe de base de `TabController` sur `UITabBarController`.
1. Créez `UIViewController` instances à ajouter à la `TabController`.
1. Ajoutez les instances de `UIViewController` à un tableau affecté à la propriété `ViewControllers` de l' `TabController`.

Ajoutez le code suivant à la classe `TabController` pour accomplir ces étapes :

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

Notez que pour chaque `UIViewController` instance, nous définissons la propriété `Title` de la `UIViewController`. Lorsque les contrôleurs sont ajoutés au `UITabBarController`, le `UITabBarController` lit les `Title` pour chaque contrôleur et les affiche sur l’étiquette de l’onglet associé, comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Définition de la TabController comme le RootViewController

L’ordre dans lequel les contrôleurs sont placés dans les onglets correspond à l’ordre dans lequel ils sont ajoutés au tableau de `ViewControllers`.

Pour que le `UITabController` se charge en tant que premier écran, nous devons en faire le `RootViewController`de la fenêtre, comme illustré dans le code suivant pour l' `AppDelegate`:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        tabController = new TabController ();
        window.RootViewController = tabController;

        window.MakeKeyAndVisible ();

        return true;
    }
}
```

Si nous exécutons l’application maintenant, le `UITabBarController` se charge avec le premier onglet sélectionné par défaut. Si vous sélectionnez l’un des autres onglets, la vue du contrôleur associé est présentée par la `UITabBarController,` comme indiqué ci-dessous, où l’utilisateur final a sélectionné le deuxième onglet :

![Deuxième onglet affiché](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modification TabBarItems

Maintenant que nous disposons d’une application avec onglet en cours d’exécution, nous allons modifier les `TabBarItem` pour modifier l’image et le texte qui s’affichent, ainsi que pour ajouter un badge à l’un des onglets.

#### <a name="setting-a-system-item"></a>Définition d’un élément système

Tout d’abord, nous allons définir le premier onglet à utiliser un élément du système. Dans le constructeur de la `TabController`, supprimez la ligne qui définit le `Title` du contrôleur pour l’instance `tab1` et remplacez-la par le code suivant pour définir la propriété `TabBarItem` du contrôleur :

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Lors de la création de l' `UITabBarItem` à l’aide d’un `UITabBarSystemItem`, le titre et l’image sont fournis automatiquement par iOS, comme indiqué dans la capture d’écran ci-dessous avec l’icône et le titre des **favoris** sur le premier onglet :

 ![Le premier onglet avec une icône d’étoile](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Définition de l’image

Outre l’utilisation d’un élément système, le titre et l’image d’un `UITabBarItem` peuvent être définis sur des valeurs personnalisées. Par exemple, modifiez le code qui définit la propriété `TabBarItem` du contrôleur nommé `tab2` comme suit :

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Le code ci-dessus suppose qu’une image nommée `second.png` a été ajoutée à la racine du projet (ou à un répertoire **Resources** ). Pour prendre en charge toutes les densités d’écran, vous avez besoin de trois images, comme indiqué ci-dessous :

![Images ajoutées au projet](creating-tabbed-applications-images/tabbedimages7new.png)

Consultez la section taille de l’icône de la **barre d’onglets** de la [page icônes personnalisées d’Apple](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/) pour obtenir des conseils sur les dimensions appropriées. La taille recommandée varie selon le style de l’image (circulaire, carré, largeur ou hauteur).

La propriété `Image` doit uniquement être définie sur le deuxième nom de fichier **. png.** iOS chargera automatiquement les fichiers de résolution plus élevée si nécessaire. Vous pouvez en savoir plus à ce sujet dans les guides [utilisation des images](~/ios/app-fundamentals/images-icons/index.md) . Par défaut, éléments de barre d’onglet sont gris, avec une teinte bleu lorsque sélectionné.

#### <a name="overriding-the-title"></a>Remplacement du titre

Lorsque la propriété `Title` est définie directement sur l' `TabBarItem`, elle remplace toute valeur définie pour `Title` sur le contrôleur lui-même.

Le deuxième onglet (du milieu) de cette capture d’écran montre un titre et une image personnalisés :

![Le deuxième onglet avec une icône carrée](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>Définition de la valeur du badge

Un onglet peut également afficher un badge. Par exemple, ajoutez la ligne suivante de code pour définir un badge sur le troisième onglet :

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Cette exécution entraîne une étiquette rouge avec la chaîne « Hi » dans le coin supérieur gauche de l’onglet comme indiqué ci-dessous :

![Deuxième onglet avec un badge Hi](creating-tabbed-applications-images/06-badge-sml.png)

Le badge est souvent utilisé pour afficher une indication numérique non lue, nouveaux éléments. Pour supprimer le badge, affectez la valeur null à la `BadgeValue`, comme indiqué ci-dessous :

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Onglets dans les scénarios non RootViewController

Dans l’exemple ci-dessus, nous avons montré comment utiliser une `UITabBarController` lorsqu’il s’agit de la `RootViewController` de la fenêtre. Dans cet exemple, nous allons examiner comment utiliser une `UITabBarController` lorsqu’il ne s’agit pas du `RootViewController` et montrer comment cette opération est créée à l’aide de storyboards.

### <a name="initial-screen-example"></a>Exemple d’écran initial

Pour ce scénario, l’écran initial est chargé à partir d’un contrôleur qui n’est pas un `UITabBarController`. Lorsque l’utilisateur interagit avec l’écran en appuyant sur un bouton, le même contrôleur d’affichage est chargé dans un `UITabBarController`, qui est ensuite présenté à l’utilisateur. La capture d’écran suivante présente le flux d’application :

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Nous allons démarrer une nouvelle application pour cet exemple. Là encore, nous allons utiliser l' **application > iPhone > modèle deC#projet vide ()** , ce qui a pour nom l’heure du projet `InitialScreenDemo`.

Dans cet exemple, une table de montage séquentiel est utilisée pour disposer les contrôleurs d’affichage. Pour ajouter une table de montage séquentiel :

- Cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter > nouveau fichier**.

- Quand la boîte de dialogue nouveau fichier s’affiche, accédez à **iOS > table de montage séquentiel iPhone vide**.

Appelons ce nouvel **fichier mainstoryboard** de Storyboard, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Quelques étapes importantes sont à prendre en compte lors de l’ajout d’une table de montage séquentiel à un fichier qui n’est pas un Storyboard, qui est abordé dans le guide [Présentation des storyboards](~/ios/user-interface/storyboards/index.md) . Ces règles sont les suivantes :

1. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** de l' `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Dans votre `App Delegate`, remplacez la méthode Window, par le code suivant :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Nous allons avoir besoin de trois contrôleurs d’affichage pour cet exemple. L’un, nommé `ViewController1`, est utilisé en tant que contrôleur d’affichage initial et dans le premier onglet. Les deux autres, nommés `ViewController2` et `ViewController3`, qui seront utilisés respectivement dans les deuxième et troisième onglets.

Ouvrez le concepteur en double-cliquant sur le fichier MainStoryboard.storyboard et faites glisser trois contrôleurs d’affichage une session sur l’aire de conception. Nous souhaitons que chacun de ces contrôleurs d’affichage ait sa propre classe correspondant au nom ci-dessus. par conséquent, sous **identity > Class**, tapez son nom, comme illustré dans la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio pour Mac génère automatiquement les classes et les fichiers de concepteur nécessaires, ceci peut être observé dans le panneau Solution, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Création de l’interface utilisateur

Ensuite, nous allons créer une interface utilisateur simple pour chacune des vues de ViewController, à l’aide du Concepteur de Xamarin iOS.

Nous voulons faire glisser un `Label` et un `Button` sur ViewController1 à partir de la **boîte à outils** sur le côté droit. Ensuite, nous allons utiliser le panneau Propriétés pour modifier le nom et le texte des contrôles à ce qui suit :

- **Étiquette** : **`Text` = **
- **Bouton** : `Title` = **utilisateur effectue une action initiale**

Nous contrôlons la visibilité de notre bouton dans un événement `TouchUpInside`, et nous devons nous y référer dans le code-behind. Nous allons l’identifier avec le **nom** `aButton` dans le panneau Propriétés, comme illustré dans la capture d’écran suivante :

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

Votre aire de conception doit maintenant ressembler à la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Nous allons ajouter un peu plus de détails à `ViewController2` et `ViewController3`, en ajoutant une étiquette à chacun et en remplaçant respectivement le texte par « deux » et « trois ». Cela met en surbrillance à l’utilisateur que nous nous intéressons à onglet/vue.

#### <a name="wiring-up-the-button"></a>Connexion du bouton

Nous allons charger `ViewController1` lors du premier démarrage de l’application. Quand l’utilisateur appuie sur le bouton, nous allons masquer le bouton et charger un `UITabBarController` avec l’instance de `ViewController1` dans le premier onglet.

Lorsque l’utilisateur relâche la `aButton`, nous voulons déclencher un événement TouchUpInside. Nous allons sélectionner le bouton, puis, dans l' **onglet événements du panneau** propriétés, déclarer le gestionnaire d’événements – `InitialActionCompleted`, afin qu’il puisse être référencé dans le code. Ceci est illustré dans la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

Nous devons maintenant indiquer au contrôleur d’affichage de masquer le bouton quand l’événement se déclenche `InitialActionCompleted`. Dans `ViewController1`, ajoutez la méthode partielle suivante :

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Enregistrez le fichier et exécuter l’application. Nous devrions voir l’écran un s’affiche et le bouton disparaissent sur retoucher.

#### <a name="adding-the-tab-bar-controller"></a>Ajout du contrôleur de barre d’onglets

Nous avons maintenant notre affichage Initial fonctionne comme prévu. Ensuite, nous souhaitons l’ajouter à un `UITabBarController`, ainsi que les vues 2 et 3. Nous allons ouvrir la table de montage séquentiel dans le concepteur.

Dans la **boîte à outils**, recherchez le **contrôleur de barre d’onglets** sous contrôleurs & objets et faites-le glisser sur le aire de conception. Comme vous pouvez le voir dans la capture d’écran ci-dessous, le contrôleur de barre d’onglet est sans interface utilisateur et par conséquent apporte deux contrôleurs d’affichage avec elle par défaut :

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Supprimer ces nouveaux contrôleurs de vue en sélectionnant la barre noire située en bas, puis en appuyant sur Supprimer.

Dans notre table de montage séquentiel, nous pouvons utiliser Segues pour gérer les transitions entre le TabBarController et nos contrôleurs d’affichage. Après l’interaction avec la vue initiale, nous souhaitons charger dans le TabBarController présenté à l’utilisateur. Nous allons configurer cela dans le concepteur.

**Appuyez sur CTRL** et **faites glisser** le bouton vers le TabBarController. Sur le trajet de souris, un menu contextuel s’affiche. Nous souhaitons utiliser un segue modal.

Pour configurer chacun de nos onglets, **Appuyez sur CTRL +** TabBarController pour chacun de nos contrôleurs d’affichage, d’une à trois, puis sélectionnez l' **onglet** relation dans le menu contextuel, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

Votre Storyboard doit ressembler à la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si nous cliquez sur l’un des éléments de barre d’onglet et explorez le panneau Propriétés, vous pouvez voir un nombre de différentes options, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Nous pouvons l’utiliser pour modifier certains attributs, tels que le badge, le titre et l’identificateur iOS, entre autres.

Si nous enregistrer et exécuter l’application maintenant, nous allons trouver que le bouton s’affiche à nouveau lorsque l’instance de ViewController1 est chargé dans le TabBarController. Nous allons résoudre ce problème en vérifiant si la vue actuelle a un contrôleur d’affichage du parent. Le cas échéant, nous savons que nous sommes à l’intérieur de la TabBarController, et par conséquent, le bouton doit être masqué. Nous allons ajouter le code ci-dessous à la classe ViewController1 :

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Lorsque l’application s’exécute et l’utilisateur appuie sur le bouton sur le premier écran, le UITabBarController est chargé avec la vue dans le premier écran placé dans le premier onglet, comme indiqué ci-dessous :

[![la sortie de l’exemple d’application](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>Résumé

Cet article a décrit comment utiliser un `UITabBarController` dans une application. Nous avons présenté comment charger des contrôleurs dans chaque onglet, ainsi que comment définir des propriétés sur les onglets ce titre, image et badge. Nous avons ensuite examiné, à l’aide des storyboards, à charger un `UITabBarController` au moment de l’exécution lorsqu’il n’est pas le `RootViewController` de la fenêtre.

## <a name="related-links"></a>Liens connexes

- [Création d’applications avec onglets (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images. zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Référence de la classe UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
