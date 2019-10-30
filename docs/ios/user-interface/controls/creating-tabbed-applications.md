---
title: Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin. iOS
description: Ce document décrit les contrôleurs de barre d’onglets iOS et comment les utiliser avec Xamarin. iOS. Il montre comment configurer un UITabBarController, travailler avec des images, définir des valeurs de badge, travailler avec des événements et bien plus encore.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9f8a5e568946e1aea8541211ec3adc45a25f1897
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022135"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin. iOS

Les applications avec onglets sont utilisées dans iOS pour prendre en charge les interfaces utilisateur où il est possible d’accéder à plusieurs écrans dans aucun ordre particulier. Grâce à la classe `UITabBarController`, les applications peuvent facilement inclure la prise en charge de tels scénarios à plusieurs écrans. `UITabBarController` s’occupe de la gestion multi-écran, ce qui permet au développeur d’applications de se concentrer sur les détails de chaque écran.

En règle générale, les applications avec onglets sont générées avec le `UITabBarController` en tant que `RootViewController` de la fenêtre principale. Toutefois, avec un peu de code supplémentaire, les applications à onglets peuvent également être utilisées successivement pour un autre écran initial, tel que le scénario dans lequel une application présente d’abord un écran de connexion, suivi de l’interface avec onglets.

Nous allons examiner à l’aide des onglets ici en travaillant dans une procédure pas à pas d’une application simple. Ensuite, nous allons examiner comment utiliser les onglets dans le scénario non `RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Présentation de UITabBarController

Le `UITabBarController` prend en charge le développement d’applications avec onglets en procédant comme suit :

- Autoriser l’ajout de plusieurs contrôleurs.
- Fournir une interface utilisateur avec onglets, via la classe `UITabBar`, pour permettre à un utilisateur de basculer entre les contrôleurs et leurs vues. 

Les contrôleurs sont ajoutés au `UITabBarController` par le biais de sa propriété `ViewControllers`, qui est un tableau `UIViewController`. Le `UITabBarController` lui-même gère le chargement du contrôleur approprié et la présentation de sa vue en fonction de l’onglet sélectionné.

Les onglets sont des instances de la classe `UITabBarItem`, qui sont contenues dans une instance `UITabBar`. Chaque `UITabBar` instance est accessible via la propriété `TabBarItem` du contrôleur dans chaque onglet.

Pour savoir comment utiliser le `UITabBarController`, examinons la création d’une application simple qui en utilise une.

## <a name="tabbed-application-walkthrough"></a>Procédure pas à pas d’application avec onglets

Pour cette procédure pas à pas, nous allons créer l’application suivante :

[![](creating-tabbed-applications-images/00-app.png "Sample tabbed app")](creating-tabbed-applications-images/00-app.png#lightbox)

Bien qu’il existe déjà un modèle d’application avec onglets disponible dans Visual Studio pour Mac, pour cet exemple, nous allons travailler à partir d’un projet vide pour mieux comprendre la façon dont l’application est construite.

 <a name="Creating_the_Application" />

### <a name="creating-the-application"></a>Création de l’application

Commençons par créer une nouvelle application.

Sélectionnez l’élément de menu **fichier > nouvelle > solution** dans Visual Studio pour Mac et sélectionnez une **application > iOS > modèle de projet vide** , nommez le projet `TabbedApplication`, comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Ajout de UITabBarController

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

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Définition de TabController en tant que RootViewController

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

[![](creating-tabbed-applications-images/03-secondtab.png "The second tab shown")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />

### <a name="modifying-tabbaritems"></a>Modification de TabBarItems

Maintenant que nous disposons d’une application avec onglet en cours d’exécution, nous allons modifier les `TabBarItem` pour modifier l’image et le texte qui s’affichent, ainsi que pour ajouter un badge à l’un des onglets.

 <a name="Setting_a_System_Item" />

#### <a name="setting-a-system-item"></a>Définition d’un élément système

Tout d’abord, nous allons définir le premier onglet pour utiliser un élément système. Dans le constructeur de la `TabController`, supprimez la ligne qui définit le `Title` du contrôleur pour l’instance `tab1` et remplacez-la par le code suivant pour définir la propriété `TabBarItem` du contrôleur :

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Lors de la création de l' `UITabBarItem` à l’aide d’un `UITabBarSystemItem`, le titre et l’image sont fournis automatiquement par iOS, comme indiqué dans la capture d’écran ci-dessous avec l’icône et le titre des **favoris** sur le premier onglet :

 ![](creating-tabbed-applications-images/04a-tabimage.png "The first tab with a star icon")

 <a name="Setting_the_Title_and_Image" />

#### <a name="setting-the-title-and-image"></a>Définition du titre et de l’image

Outre l’utilisation d’un élément système, le titre et l’image d’un `UITabBarItem` peuvent être définis sur des valeurs personnalisées. Par exemple, modifiez le code qui définit la propriété `TabBarItem` du contrôleur nommé `tab2` comme suit :

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Le code ci-dessus suppose qu’une image nommée `second.png` a été ajoutée à la racine du projet dans Visual Studio pour Mac. Nous avons en fait ajouté trois images à notre projet, pour couvrir toutes les résolutions d’appareils, comme indiqué ci-dessous :

 [![](creating-tabbed-applications-images/tabbedimages7new.png "The images added to the project")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

L’image de l’onglet doit être un 30x30 png avec transparence pour la résolution normale, 60 x 60 pour la haute résolution et 90 x 90 pour iPhone 6 plus résolution. Dans notre code, nous devons uniquement charger le fichier nommé `second.png` et iOS chargera automatiquement la haute résolution une sur les appareils avec un affichage de retine. Vous pouvez en savoir plus à ce sujet dans les guides [utilisation des images](~/ios/app-fundamentals/images-icons/index.md) . Par défaut, les éléments de la barre d’onglets sont gris, avec une teinte bleue quand elles sont sélectionnées.

**Remarque**

Les images ci-dessus peuvent également être ajoutées au répertoire **Resources** , qui est un répertoire spécial dont le contenu sera automatiquement copié à la racine de l’ensemble d’applications :

[![](creating-tabbed-applications-images/tabbedapplication8.png "The images as Resources")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

En outre, lorsque nous définissons directement la propriété `Title` sur le `TabBarItem`, elle remplacera toute valeur définie pour `Title` sur le contrôleur lui-même.

Lorsque nous exécutons l’application maintenant, le deuxième onglet affiche le titre et l’image personnalisés comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/05-customtab.png "The second tab with a square icon")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />

#### <a name="setting-the-badge-value"></a>Définition de la valeur du badge

Un onglet peut également afficher un badge. Par exemple, ajoutez la ligne de code suivante pour définir un badge sur le troisième onglet :

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

L’exécution de cette fonction génère une étiquette rouge avec la chaîne « Hi » dans le coin supérieur gauche de l’onglet, comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/06-badge.png "The second tab with a Hi badge")](creating-tabbed-applications-images/06-badge.png#lightbox)

Le badge est souvent utilisé pour afficher une indication de nombre non lu, de nouveaux éléments. Pour supprimer le badge, affectez la valeur null à la `BadgeValue`, comme indiqué ci-dessous :

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Onglets dans les scénarios non RootViewController

Dans l’exemple ci-dessus, nous avons montré comment utiliser une `UITabBarController` lorsqu’il s’agit de la `RootViewController` de la fenêtre. Dans cet exemple, nous allons examiner comment utiliser une `UITabBarController` lorsqu’il ne s’agit pas du `RootViewController` et montrer comment cette opération est créée à l’aide de storyboards.

 <a name="Initial_Screen_Example" />

### <a name="initial-screen-example"></a>Exemple d’écran initial

Pour ce scénario, l’écran initial est chargé à partir d’un contrôleur qui n’est pas un `UITabBarController`. Lorsque l’utilisateur interagit avec l’écran en appuyant sur un bouton, le même contrôleur d’affichage est chargé dans un `UITabBarController`, qui est ensuite présenté à l’utilisateur. La capture d’écran suivante montre le workflow de l’application :

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Commençons une nouvelle application pour cet exemple. Là encore, nous allons utiliser l' **application > iPhone > modèle deC#projet vide ()** , ce qui a pour nom l’heure du projet `InitialScreenDemo`.

Dans cet exemple, nous aurons besoin d’un Storyboard pour contenir nos contrôleurs d’affichage. Pour ajouter une table de montage séquentiel :

- Cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter > nouveau fichier**.

- Quand la boîte de dialogue nouveau fichier s’affiche, accédez à **iOS > table de montage séquentiel iPhone vide**.

Appelons ce nouvel **fichier mainstoryboard** de Storyboard, comme illustré ci-dessous : 

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Quelques étapes importantes sont à prendre en compte lors de l’ajout d’une table de montage séquentiel à un fichier qui n’est pas un Storyboard, qui est abordé dans le guide [Présentation des storyboards](~/ios/user-interface/storyboards/index.md) . Ces équivalents sont :

1. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** de l' `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Dans votre `App Delegate`, remplacez la méthode Window, par le code suivant :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Nous aurons besoin de trois contrôleurs d’affichage pour cet exemple. L’un, nommé `ViewController1`, est utilisé en tant que contrôleur d’affichage initial et dans le premier onglet. Les deux autres, nommés `ViewController2` et `ViewController3`, qui seront utilisés respectivement dans les deuxième et troisième onglets.

Ouvrez le concepteur en double-cliquant sur le fichier fichier mainstoryboard. Storyboard, puis faites glisser trois contrôleurs d’affichage sur l’aire de conception. Nous souhaitons que chacun de ces contrôleurs d’affichage ait sa propre classe correspondant au nom ci-dessus. par conséquent, sous **identity > Class**, tapez son nom, comme illustré dans la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio pour Mac génère automatiquement les classes et les fichiers de concepteur nécessaires, ce qui est visible dans le Panneau Solutions, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />

#### <a name="creating-the-ui"></a>Création de l’interface utilisateur

Ensuite, nous allons créer une interface utilisateur simple pour chacune des vues de ViewController, à l’aide du concepteur Xamarin iOS.

Nous voulons faire glisser un `Label` et un `Button` sur ViewController1 à partir de la **boîte à outils** sur le côté droit. Ensuite, nous allons utiliser le Panneau Propriétés pour modifier le nom et le texte des contrôles :

- **Étiquette** : **`Text` = **
- **Bouton** : `Title` = **utilisateur effectue une action initiale**

Nous contrôlons la visibilité de notre bouton dans un événement `TouchUpInside`, et nous devons nous y référer dans le code-behind. Nous allons l’identifier avec le **nom** `aButton` dans le panneau Propriétés, comme illustré dans la capture d’écran suivante :

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

Votre Aire de conception doit maintenant ressembler à la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Nous allons ajouter un peu plus de détails à `ViewController2` et `ViewController3`, en ajoutant une étiquette à chacun et en remplaçant respectivement le texte par « deux » et « trois ». Cela met en évidence l’utilisateur de l’onglet ou de la vue que nous examinons.

#### <a name="wiring-up-the-button"></a>Connexion du bouton

Nous allons charger `ViewController1` lors du premier démarrage de l’application. Quand l’utilisateur appuie sur le bouton, nous allons masquer le bouton et charger un `UITabBarController` avec l’instance de `ViewController1` dans le premier onglet.

Lorsque l’utilisateur relâche la `aButton`, nous voulons déclencher un événement TouchUpInside. Nous allons sélectionner le bouton, puis, dans l' **onglet événements du panneau** propriétés, déclarer le gestionnaire d’événements – `InitialActionCompleted`, afin qu’il puisse être référencé dans le code. Cela est illustré dans la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

Nous devons maintenant indiquer au contrôleur d’affichage de masquer le bouton quand l’événement se déclenche `InitialActionCompleted`. Dans `ViewController1`, ajoutez la méthode partielle suivante :

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Enregistrez le fichier et exécutez l’application. Nous devrions voir apparaître l’écran 1 et le bouton disparaissent au toucher.

#### <a name="adding-the-tab-bar-controller"></a>Ajout du contrôleur de barre d’onglets

Nous avons maintenant une vue initiale qui fonctionne comme prévu. Ensuite, nous souhaitons l’ajouter à un `UITabBarController`, ainsi que les vues 2 et 3. Nous allons ouvrir la table de montage séquentiel dans le concepteur.

Dans la **boîte à outils**, recherchez le **contrôleur de barre d’onglets** sous contrôleurs & objets et faites-le glisser sur le aire de conception. Comme vous pouvez le voir dans la capture d’écran ci-dessous, le contrôleur de barre d’onglets est sans interface utilisateur et, par conséquent, propose deux contrôleurs d’affichage par défaut :

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Supprimez ces nouveaux contrôleurs d’affichage en sélectionnant la barre noire en bas et en appuyant sur supprimer.

Dans notre Storyboard, nous pouvons utiliser SEGUES pour gérer les transitions entre les TabBarController et nos contrôleurs d’affichage. Après l’interaction avec la vue initiale, nous voulons la charger dans le TabBarController présenté à l’utilisateur. Nous allons le configurer dans le concepteur.

**Appuyez sur CTRL** et **faites glisser** le bouton vers le TabBarController. Au niveau de la souris, un menu contextuel s’affiche. Nous souhaitons utiliser un segue modal. 

Pour configurer chacun de nos onglets, **Appuyez sur CTRL +** TabBarController pour chacun de nos contrôleurs d’affichage, d’une à trois, puis sélectionnez l' **onglet** relation dans le menu contextuel, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

Votre storyboard doit ressembler à la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si nous cliquons sur l’un des éléments de la barre d’onglets et explorons le panneau Propriétés, vous pouvez voir plusieurs options, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Nous pouvons l’utiliser pour modifier certains attributs, tels que le badge, le titre et l' [identificateur](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html)iOS, entre autres.

Si nous enregistrons et exécutons l’application maintenant, nous constatons que le bouton réapparaît lorsque l’instance ViewController1 est chargée dans le TabBarController. Nous allons résoudre ce problème en vérifiant si l’affichage actuel dispose d’un contrôleur d’affichage parent. Si c’est le cas, nous savons que nous sommes à l’intérieur du TabBarController et que le bouton doit donc être masqué. Nous allons ajouter le code ci-dessous à la classe ViewController1 :

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Lorsque l’application s’exécute et que l’utilisateur appuie sur le bouton sur le premier écran, UITabBarController est chargé, avec la vue du premier écran placée dans le premier onglet, comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/first-view.png "The sample app output")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Récapitulatif

Cet article a décrit comment utiliser un `UITabBarController` dans une application. Nous avons parcouru le mode de chargement des contrôleurs dans chaque onglet, ainsi que la façon de définir des propriétés sur les onglets tels que le titre, l’image et le badge. Nous avons ensuite examiné, à l’aide des storyboards, à charger un `UITabBarController` au moment de l’exécution lorsqu’il n’est pas le `RootViewController` de la fenêtre.

## <a name="related-links"></a>Liens associés

- [Création d’applications avec onglets (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images. zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Référence de la classe UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
