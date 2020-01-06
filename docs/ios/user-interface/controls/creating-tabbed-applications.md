---
title: Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin.iOS
description: Ce document décrit les contrôleurs de barre d’onglet iOS et comment les utiliser avec Xamarin.iOS. Il montre comment configurer un UITabBarController, de travailler avec des images, de définir les valeurs de badge, le travail avec des événements et bien plus encore.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ad4682e9a3d4de2565bee54ffa159fd739572e24
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663314"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin. iOS

Applications à onglets sont utilisées dans iOS pour prendre en charge des interfaces utilisateur où plusieurs écrans sont accessibles dans aucun ordre particulier. Via le `UITabBarController` (classe), les applications peuvent inclure facilement la prise en charge pour de tels scénarios multi-écran. `UITabBarController` s’occupe de la gestion des écran multiples, ce qui permet au développeur de se concentrer sur les détails de chaque écran.

En règle générale, les applications à onglets sont générées avec le `UITabBarController` en cours la `RootViewController` de la fenêtre principale. Toutefois, avec un peu de code supplémentaire, les applications à onglets permet également à la suite pour certains autres écran initial, tels que le scénario dans lequel une application présente tout d’abord un écran de connexion, suivi de l’interface à onglets.

Cette page présente les deux scénarios : quand les onglets sont à la racine de la hiérarchie d’affichage de l’application et également dans le scénario non`RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Présentation de UITabBarController

Le `UITabBarController` prend en charge avec onglet développement d’applications par ce qui suit :

- Autorise plusieurs contrôleurs à ajouter à ce dernier.
- En fournissant une interface utilisateur à onglets, via le `UITabBar` (classe), pour autoriser un utilisateur de basculer entre les contrôleurs et leurs points de vue. 

Contrôleurs sont ajoutés à la `UITabBarController` via son `ViewControllers` propriété, qui est un `UIViewController` tableau. Le `UITabBarController` lui-même gère le chargement du contrôleur approprié et à la présentation de sa vue en fonction de l’onglet sélectionné.

Les onglets sont des instances de la `UITabBarItem` (classe), qui sont contenus dans un `UITabBar` instance. Chaque `UITabBar` instance est accessible via la `TabBarItem` propriété du contrôleur dans chaque onglet.

Pour comprendre comment travailler avec le `UITabBarController`, nous allons étudier la création d’une application simple qui utilise un.

## <a name="tabbed-application-walkthrough"></a>Procédure pas à pas d’application avec onglets

Pour cette procédure pas à pas, nous allons créer l’application suivante :

[Exemple d’application ![avec onglets](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Bien qu’il existe déjà un modèle d’application avec onglets disponible dans Visual Studio pour Mac, pour cet exemple, ces instructions fonctionnent à partir d’un projet vide pour mieux comprendre la façon dont l’application est construite.

### <a name="creating-the-application"></a>Création de l’application

Commencez par créer une nouvelle application.

Sélectionnez le **fichier > Nouveau > Solution** élément de menu dans Visual Studio pour Mac, puis sélectionnez un **iOS > application > projet vide** modèle, nommez le projet `TabbedApplication`, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Ajout de la UITabBarController

Ensuite, ajoutez une classe vide en sélectionnant **fichier > nouveau fichier** et en choisissant le **général : classe vide** modèle. Nommez le fichier `TabController` comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

Le `TabController` classe contiendra l’implémentation de la `UITabBarController` qui gère un tableau de `UIViewControllers`. Lorsque l’utilisateur sélectionne un onglet, le `UITabBarController` s’occupera de présenter l’affichage pour le contrôleur d’affichage approprié.

Pour implémenter le `UITabBarController` nous devons effectuer les opérations suivantes :

1. Définir la classe de base de `TabController` à `UITabBarController` . 
1. Créer `UIViewController` instances à ajouter à la `TabController` . 
1. Ajouter le `UIViewController` instances dans un tableau assigné à la `ViewControllers` propriété de la `TabController` . 

Ajoutez le code suivant à la `TabController` classe pour atteindre ces étapes :

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

Notez que pour chaque `UIViewController` instance, nous définissons le `Title` propriété de la `UIViewController`. Lorsque les contrôleurs sont ajoutés à la `UITabBarController`, le `UITabBarController` lira le `Title` pour chaque contrôleur et l’afficher dans l’étiquette de l’onglet associé comme indiqué ci-dessous :

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Définition de la TabController comme le RootViewController

L’ordre que les contrôleurs sont placées dans les onglets correspond à l’ordre qu’ils sont ajoutés à la `ViewControllers` tableau.

Pour obtenir le `UITabController` pour charger en tant que le premier écran, nous devons faire de la fenêtre `RootViewController`, comme illustré dans le code suivant pour le `AppDelegate`:

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

Si nous exécutons l’application maintenant, le `UITabBarController` sera chargé avec le premier onglet sélectionné par défaut. Affichage des résultats dans le contrôleur associé de sélection de l’un des autres onglets présenté par le `UITabBarController,` comme indiqué ci-dessous dans lequel l’utilisateur final a sélectionné le deuxième onglet :

![Deuxième onglet affiché](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modification TabBarItems

Maintenant que nous avons une exécution onglet application, nous allons modifier le `TabBarItem` pour modifier l’image et le texte qui s’affiche, ainsi que pour ajouter un badge à un des onglets.

#### <a name="setting-a-system-item"></a>Définition d’un élément système

Tout d’abord, nous allons définir le premier onglet à utiliser un élément du système. Dans le constructeur de la `TabController`, supprimez la ligne qui définit le contrôleur `Title` pour le `tab1` d’instance et remplacez-le par le code suivant pour définir le contrôleur `TabBarItem` propriété :

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Lorsque vous créez le `UITabBarItem` à l’aide un `UITabBarSystemItem`, le titre et l’image sont fournies automatiquement par iOS, comme illustré dans la capture d’écran ci-dessous montrant le **favoris** icône et le titre sur le premier onglet :

 ![Le premier onglet avec une icône d’étoile](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Définition de l’image

Outre l’utilisation d’un élément du système, le titre et l’image d’un `UITabBarItem` peut être défini sur des valeurs personnalisées. Par exemple, modifiez le code qui définit le `TabBarItem` propriété du contrôleur nommé `tab2` comme suit :

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

La propriété `Image` doit uniquement être définie sur le deuxième nom de fichier **. png.** iOS chargera automatiquement les fichiers de résolution plus élevée si nécessaire. Vous trouverez plus d’informations à ce sujet dans le [utilisation des Images](~/ios/app-fundamentals/images-icons/index.md) guides. Par défaut, éléments de barre d’onglet sont gris, avec une teinte bleu lorsque sélectionné.

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

Le badge est souvent utilisé pour afficher une indication numérique non lue, nouveaux éléments. Pour supprimer le badge, définissez le `BadgeValue` sur null comme indiqué ci-dessous :

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Onglets dans les scénarios non RootViewController

Dans l’exemple ci-dessus, nous vous avons montré comment travailler avec un `UITabBarController` lorsqu’il se trouve le `RootViewController` de la fenêtre. Dans cet exemple, nous allons examiner comment utiliser un `UITabBarController` quand il n’est pas le `RootViewController` et afficher cette création d’utiliser les Storyboards.

### <a name="initial-screen-example"></a>Exemple d’écran initial

Pour ce scénario, l’écran initial est chargé à partir d’un contrôleur qui n’est pas un `UITabBarController`. Quand l’utilisateur interagit avec l’écran en appuyant sur un bouton, le même contrôleur d’affichage seront chargé dans un `UITabBarController`, qui est ensuite présenté à l’utilisateur. La capture d’écran suivante montre le flux d’application :

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Nous allons démarrer une nouvelle application pour cet exemple. Là encore, nous allons utiliser le **iPhone > application > projet vide (C#)** modèle, cette fois le projet d’affectation de noms `InitialScreenDemo`.

Dans cet exemple, une table de montage séquentiel est utilisée pour disposer les contrôleurs d’affichage. Pour ajouter une table de montage séquentiel :

- Avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter > nouveau fichier**.

- Lorsque la boîte de dialogue Nouveau fichier s’affiche, accédez à **iOS > iPhone Storyboard vide**.

Appelons cette table de montage séquentiel **MainStoryboard** , comme illustré ci-dessous : 

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Il existe quelques étapes importantes à noter lors de l’ajout d’une table de montage vers un fichier précédemment non-table de montage séquentiel, qui font l’objet dans le [Introduction aux Storyboards](~/ios/user-interface/storyboards/index.md) guide. Il s'agit des paramètres suivants :

1. Ajoutez votre nom de la table de montage séquentiel à la **Interface principale** section de la `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Dans votre `App Delegate`, substituez la méthode de la fenêtre, avec le code suivant :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Nous allons avoir besoin de trois contrôleurs d’affichage pour cet exemple. L’un, nommé `ViewController1`, est utilisé en tant que contrôleur d’affichage initial et dans le premier onglet. Les deux autres, nommés `ViewController2` et `ViewController3`, qui seront utilisés respectivement dans les deuxième et troisième onglets.

Ouvrez le concepteur en double-cliquant sur le fichier MainStoryboard.storyboard et faites glisser trois contrôleurs d’affichage une session sur l’aire de conception. Nous voulons que chacun de ces contrôleurs d’affichage pour avoir leur propre classe correspondant au nom ci-dessus, c’est le cas, sous **identité > classe**, tapez son nom, comme illustré dans la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio pour Mac génère automatiquement les classes et les fichiers de concepteur nécessaires, ceci peut être observé dans le panneau Solution, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Création de l’interface utilisateur

Ensuite, nous allons créer une interface utilisateur simple pour chacune des vues de ViewController, à l’aide du Concepteur de Xamarin iOS.

Nous souhaitons faire glisser un `Label` et un `Button` sur ViewController1 à partir de la **boîte à outils** sur le côté droit. Ensuite, nous allons utiliser le panneau Propriétés pour modifier le nom et le texte des contrôles à ce qui suit :

- **Étiquette** : `Text`  =  **une**
- **Bouton** : `Title`  =  **utilisateur exécute une Action initiale**

Nous contrôlera la visibilité de notre bouton dans un `TouchUpInside` événement et nous devons y faire référence dans le code-behind. Nous allons identifier avec le **nom** `aButton` dans le panneau Propriétés, comme illustré dans la capture d’écran suivante :

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

Votre aire de conception doit maintenant ressembler à la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Nous allons ajouter un peu plus en détail pour `ViewController2` et `ViewController3`, en ajoutant une étiquette à chacun et modifier le texte pour 'Deux' et 'Trois' respectivement. Cela met en surbrillance à l’utilisateur que nous nous intéressons à onglet/vue.

#### <a name="wiring-up-the-button"></a>Connexion du bouton

Nous allons charger `ViewController1` premier démarrage de l’application. Lorsque l’utilisateur actionne le bouton, nous allons masquer le bouton et charger un `UITabBarController` avec la `ViewController1` instance dans le premier onglet.

Lorsque l’utilisateur relâche le `aButton`, nous voulons un déclenchement de l’événement TouchUpInside. Nous allons sélectionner le bouton, puis, dans le **onglet événements** du panneau Propriétés, déclarez le Gestionnaire d’événements – `InitialActionCompleted` – afin de pouvoir y faire référence à dans le code. Ceci est illustré dans la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

Nous devons maintenant indiquer le contrôleur d’affichage pour masquer le bouton lorsque l’événement se déclenche `InitialActionCompleted`. Dans `ViewController1`, ajoutez la méthode partielle suivante :

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Enregistrez le fichier et exécuter l’application. Nous devrions voir l’écran un s’affiche et le bouton disparaissent sur retoucher.

#### <a name="adding-the-tab-bar-controller"></a>Ajout du contrôleur de barre d’onglets

Nous avons maintenant notre affichage Initial fonctionne comme prévu. Ensuite, nous souhaitons ajouter à un `UITabBarController`, ainsi que les vues 2 et 3. Nous allons ouvrir la table de montage séquentiel dans le concepteur.

Dans le **boîte à outils**, recherchez le **contrôleur de barre d’onglet** sous contrôleurs & objets et le déplacer sur l’aire de conception. Comme vous pouvez le voir dans la capture d’écran ci-dessous, le contrôleur de barre d’onglet est sans interface utilisateur et par conséquent apporte deux contrôleurs d’affichage avec elle par défaut :

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Supprimer ces nouveaux contrôleurs de vue en sélectionnant la barre noire située en bas, puis en appuyant sur Supprimer.

Dans notre table de montage séquentiel, nous pouvons utiliser Segues pour gérer les transitions entre le TabBarController et nos contrôleurs d’affichage. Après l’interaction avec la vue initiale, nous souhaitons charger dans le TabBarController présenté à l’utilisateur. Nous allons configurer cela dans le concepteur.

**CTRL + clic** et **glisser** à partir du bouton à la TabBarController. Sur le trajet de souris, un menu contextuel s’affiche. Nous souhaitons utiliser un segue modal. 

Pour configurer chacun de nos onglets, **Ctrl + clic** à partir de la TabBarController à chacun de nos contrôleurs d’affichage dans l’ordre d’un à trois, puis sélectionnez la relation **onglet** dans le menu contextuel, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

Votre Storyboard doit ressembler à la capture d’écran ci-dessous :

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si nous cliquez sur l’un des éléments de barre d’onglet et explorez le panneau Propriétés, vous pouvez voir un nombre de différentes options, comme illustré ci-dessous :

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Nous pouvons l’utiliser pour modifier certains attributs tels que le badge, le titre et l’iOS [identificateur](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), entre autres

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

## <a name="summary"></a>Récapitulatif

Cet article a abordé comment utiliser un `UITabBarController` dans une application. Nous avons présenté comment charger des contrôleurs dans chaque onglet, ainsi que comment définir des propriétés sur les onglets ce titre, image et badge. Nous avons examiné puis, à l’aide de storyboards, comment charger un `UITabBarController` lors de l’exécution lorsqu’il n’est pas le `RootViewController` de la fenêtre.

## <a name="related-links"></a>Liens connexes

- [Création d’Applications avec onglets (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Référence de classe de UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
