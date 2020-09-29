---
title: Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin. iOS
description: Ce document décrit les contrôleurs de barre d’onglets iOS et comment les utiliser avec Xamarin. iOS. Il montre comment configurer un UITabBarController, travailler avec des images, définir des valeurs de badge, travailler avec des événements et bien plus encore.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9bd048239c404c0eb3309fdc74b26bcb94db4740
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434129"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barres d’onglets et contrôleurs de barre d’onglets dans Xamarin. iOS

Les applications avec onglets sont utilisées dans iOS pour prendre en charge les interfaces utilisateur où il est possible d’accéder à plusieurs écrans dans aucun ordre particulier. Grâce à la `UITabBarController` classe, les applications peuvent facilement inclure la prise en charge de tels scénarios à plusieurs écrans. `UITabBarController` prend en charge la gestion multi-écran, ce qui permet au développeur d’applications de se concentrer sur les détails de chaque écran.

En règle générale, les applications avec onglets sont créées avec le `UITabBarController` en tant que `RootViewController` de la fenêtre principale. Toutefois, avec un peu de code supplémentaire, les applications à onglets peuvent également être utilisées successivement pour un autre écran initial, tel que le scénario dans lequel une application présente d’abord un écran de connexion, suivi de l’interface avec onglets.

Cette page présente les deux scénarios : quand les onglets sont à la racine de la hiérarchie d’affichage de l’application et également dans le `RootViewController` scénario non.

## <a name="introducing-uitabbarcontroller"></a>Présentation de UITabBarController

Le `UITabBarController` prend en charge le développement d’applications avec onglets de la façon suivante :

- Autoriser l’ajout de plusieurs contrôleurs.
- Fournir une interface utilisateur avec onglets, via la  `UITabBar` classe, pour permettre à un utilisateur de basculer entre les contrôleurs et leurs vues.

Les contrôleurs sont ajoutés au `UITabBarController` via sa `ViewControllers` propriété, qui est un `UIViewController` tableau. Le `UITabBarController` lui-même gère le chargement du contrôleur approprié et la présentation de sa vue en fonction de l’onglet sélectionné.

Les onglets sont des instances de la `UITabBarItem` classe, qui sont contenues dans une `UITabBar` instance. Chaque `UITabBar` instance est accessible via la `TabBarItem` propriété du contrôleur dans chaque onglet.

Pour savoir comment utiliser le `UITabBarController` , examinons la création d’une application simple qui en utilise une.

## <a name="tabbed-application-walkthrough"></a>Procédure pas à pas d’application avec onglets

Pour cette procédure pas à pas, nous allons créer l’application suivante :

[![Exemple d’application avec onglets](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Bien qu’il existe déjà un modèle d’application avec onglets disponible dans Visual Studio pour Mac, pour cet exemple, ces instructions fonctionnent à partir d’un projet vide pour mieux comprendre la façon dont l’application est construite.

### <a name="creating-the-application"></a>Création de l’application

Commencez par créer une nouvelle application.

Sélectionnez l’élément de menu **fichier > nouvelle > solution** dans Visual Studio pour Mac et sélectionnez une **application > iOS > modèle de projet vide** , nommez le projet `TabbedApplication` , comme indiqué ci-dessous :

[![Sélectionner le modèle de projet vide](creating-tabbed-applications-images/newsolution1.png)](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![Nommez le projet TabbedApplication](creating-tabbed-applications-images/newsolution2.png)](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Ajout de UITabBarController

Ensuite, ajoutez une classe vide en sélectionnant **fichier > nouveau fichier** et en choisissant le modèle de **classe général : vide** . Nommez le fichier `TabController` comme indiqué ci-dessous :

[![Ajouter la classe TabController](creating-tabbed-applications-images/02-newclass.png)](creating-tabbed-applications-images/02-newclass.png#lightbox)

La `TabController` classe contient l’implémentation du `UITabBarController` qui gérera un tableau de `UIViewControllers` . Lorsque l’utilisateur sélectionne un onglet, le `UITabBarController` s’occupe de présenter la vue pour le contrôleur d’affichage approprié.

Pour implémenter le, `UITabBarController` nous devons effectuer les opérations suivantes :

1. Définissez la classe de base de  `TabController` sur  `UITabBarController` .
1. Créez  `UIViewController` des instances à ajouter à  `TabController` .
1. Ajoutez les  `UIViewController` instances à un tableau assigné à la  `ViewControllers` propriété de  `TabController` .

Ajoutez le code suivant à la `TabController` classe pour accomplir ces étapes :

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

Notez que pour chaque `UIViewController` instance, nous définissons la `Title` propriété de `UIViewController` . Lorsque les contrôleurs sont ajoutés au `UITabBarController` , le `UITabBarController` lit le `Title` pour chaque contrôleur et l’affiche sur l’étiquette de l’onglet associé, comme indiqué ci-dessous :

[![L’exemple d’application s’exécute](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Définition de TabController en tant que RootViewController

L’ordre dans lequel les contrôleurs sont placés dans les onglets correspond à l’ordre dans lequel ils sont ajoutés au `ViewControllers` tableau.

Pour que le `UITabController` se charge en tant que premier écran, nous devons en faire la fenêtre `RootViewController` , comme illustré dans le code suivant pour le `AppDelegate` :

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

Si nous exécutons l’application maintenant, le `UITabBarController` se charge avec le premier onglet sélectionné par défaut. Si vous sélectionnez l’un des autres onglets, la vue du contrôleur associé est présentée par le `UITabBarController,` , comme indiqué ci-dessous, où l’utilisateur final a sélectionné le deuxième onglet :

![Deuxième onglet affiché](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modification de TabBarItems

Maintenant que nous disposons d’une application avec onglet en cours d’exécution, nous allons modifier le `TabBarItem` pour modifier l’image et le texte qui s’affichent, ainsi que pour ajouter un badge à l’un des onglets.

#### <a name="setting-a-system-item"></a>Définition d’un élément système

Tout d’abord, nous allons définir le premier onglet pour utiliser un élément système. Dans le constructeur de `TabController` , supprimez la ligne qui définit le du contrôleur `Title` pour l' `tab1` instance et remplacez-la par le code suivant pour définir la propriété du contrôleur `TabBarItem` :

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Lorsque vous créez le `UITabBarItem` à l’aide d’un `UITabBarSystemItem` , le titre et l’image sont fournis automatiquement par iOS, comme indiqué dans la capture d’écran ci-dessous, avec l’icône et le titre des **favoris** sur le premier onglet :

 ![Le premier onglet avec une icône d’étoile](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Définition de l’image

Outre l’utilisation d’un élément système, le titre et l’image d’un `UITabBarItem` peuvent être définis sur des valeurs personnalisées. Par exemple, modifiez le code qui définit la `TabBarItem` propriété du contrôleur nommé `tab2` comme suit :

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Le code ci-dessus suppose qu’une image nommée `second.png` a été ajoutée à la racine du projet (ou à un répertoire de **ressources** ). Pour prendre en charge toutes les densités d’écran, vous avez besoin de trois images, comme indiqué ci-dessous :

![Images ajoutées au projet](creating-tabbed-applications-images/tabbedimages7new.png)

Consultez la section taille de l’icône de la **barre d’onglets** de la [page icônes personnalisées d’Apple](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/) pour obtenir des conseils sur les dimensions appropriées. La taille recommandée varie selon le style de l’image (circulaire, carré, largeur ou hauteur).

La `Image` propriété doit uniquement être définie sur le nom de fichier **second.png** , iOS chargera automatiquement les fichiers de résolution plus élevée si nécessaire. Vous pouvez en savoir plus à ce sujet dans les guides [utilisation des images](~/ios/app-fundamentals/images-icons/index.md) . Par défaut, les éléments de la barre d’onglets sont gris, avec une teinte bleue quand elles sont sélectionnées.

#### <a name="overriding-the-title"></a>Remplacement du titre

Quand la `Title` propriété est définie directement sur le `TabBarItem` , elle remplace toute valeur définie pour `Title` sur le contrôleur lui-même.

Le deuxième onglet (du milieu) de cette capture d’écran montre un titre et une image personnalisés :

![Le deuxième onglet avec une icône carrée](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>Définition de la valeur du badge

Un onglet peut également afficher un badge. Par exemple, ajoutez la ligne de code suivante pour définir un badge sur le troisième onglet :

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

L’exécution de cette fonction génère une étiquette rouge avec la chaîne « Hi » dans le coin supérieur gauche de l’onglet, comme indiqué ci-dessous :

![Deuxième onglet avec un badge Hi](creating-tabbed-applications-images/06-badge-sml.png)

Le badge est souvent utilisé pour afficher une indication de nombre non lu, de nouveaux éléments. Pour supprimer le badge, affectez la valeur `BadgeValue` null comme indiqué ci-dessous :

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Onglets dans les scénarios non RootViewController

Dans l’exemple ci-dessus, nous avons montré comment utiliser un `UITabBarController` lorsqu’il s’agit du `RootViewController` de la fenêtre. Dans cet exemple, nous allons examiner comment utiliser un `UITabBarController` lorsqu’il n’est pas le `RootViewController` et montrer comment il est créé à l’aide de storyboards.

### <a name="initial-screen-example"></a>Exemple d’écran initial

Pour ce scénario, l’écran initial est chargé à partir d’un contrôleur qui n’est pas un `UITabBarController` . Lorsque l’utilisateur interagit avec l’écran en appuyant sur un bouton, le même contrôleur d’affichage est chargé dans un `UITabBarController` , qui est ensuite présenté à l’utilisateur. La capture d’écran suivante présente le flux d’application :

[![Cette capture d’écran montre le workflow de l’application](creating-tabbed-applications-images/inital-screen-application.png)](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Commençons une nouvelle application pour cet exemple. Là encore, nous allons utiliser l' **application iPhone > > modèle de projet vide (C#)** , cette fois l’attribution d’un nom au projet `InitialScreenDemo` .

Dans cet exemple, une table de montage séquentiel est utilisée pour disposer les contrôleurs d’affichage. Pour ajouter une table de montage séquentiel :

- Cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **ajouter > nouveau fichier**.

- Quand la boîte de dialogue nouveau fichier s’affiche, accédez à **iOS > table de montage séquentiel iPhone vide**.

Appelons ce nouvel **fichier mainstoryboard** de Storyboard, comme illustré ci-dessous :

[![Ajouter un fichier fichier mainstoryboard au projet](creating-tabbed-applications-images/new-file-dialog.png)](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Quelques étapes importantes sont à prendre en compte lors de l’ajout d’une table de montage séquentiel à un fichier qui n’est pas un Storyboard, qui est abordé dans le guide [Présentation des storyboards](~/ios/user-interface/storyboards/index.md) . Ces règles sont les suivantes :

1. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** du `Info.plist` :

    [![Définir l’interface principale sur fichier mainstoryboard](creating-tabbed-applications-images/project-options.png)](creating-tabbed-applications-images/project-options.png#lightbox)
1. Dans votre `App Delegate` , remplacez la méthode de fenêtre, par le code suivant :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Nous aurons besoin de trois contrôleurs d’affichage pour cet exemple. Un, nommé `ViewController1` , sera utilisé comme contrôleur d’affichage initial et dans le premier onglet. Les deux autres, nommés `ViewController2` et `ViewController3` , qui seront utilisés dans les deuxième et troisième onglets, respectivement.

Ouvrez le concepteur en double-cliquant sur le fichier fichier mainstoryboard. Storyboard, puis faites glisser trois contrôleurs d’affichage sur l’aire de conception. Nous souhaitons que chacun de ces contrôleurs d’affichage ait sa propre classe correspondant au nom ci-dessus. par conséquent, sous **identity > Class**, tapez son nom, comme illustré dans la capture d’écran ci-dessous :

[![Définir la classe sur ViewController1](creating-tabbed-applications-images/class-name.png)](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio pour Mac génère automatiquement les classes et les fichiers de concepteur nécessaires, ce qui est visible dans le Panneau Solutions, comme illustré ci-dessous :

[![Fichiers générés automatiquement dans le projet](creating-tabbed-applications-images/solution-pad2.png)](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Création de l’interface utilisateur

Ensuite, nous allons créer une interface utilisateur simple pour chacune des vues de ViewController, à l’aide du concepteur Xamarin iOS.

Nous voulons faire glisser un `Label` et un `Button` sur ViewController1 à partir de la **boîte à outils** sur le côté droit. Ensuite, nous allons utiliser le Panneau Propriétés pour modifier le nom et le texte des contrôles :

- **Étiquette** : `Text`  =  **un**
- **Bouton** : `Title`  =  **l’utilisateur effectue une action initiale**

Nous contrôlons la visibilité de notre bouton dans un `TouchUpInside` événement, et nous devons y faire référence dans le code-behind. Nous allons l’identifier avec le **nom** `aButton` dans la panneau Propriétés, comme illustré dans la capture d’écran suivante :

[![Définissez le nom sur aButton dans le Panneau Propriétés](creating-tabbed-applications-images/abutton-properties.png)](creating-tabbed-applications-images/abutton-properties.png#lightbox)

Votre Aire de conception doit maintenant ressembler à la capture d’écran ci-dessous :

[![Votre Aire de conception doit maintenant ressembler à cette capture d’écran](creating-tabbed-applications-images/design-surface1.png)](creating-tabbed-applications-images/design-surface1.png#lightbox)

Nous allons ajouter un peu plus de détails à `ViewController2` et à `ViewController3` , en ajoutant une étiquette à chaque, et en remplaçant respectivement le texte par « deux » et « trois ». Cela met en évidence l’utilisateur de l’onglet ou de la vue que nous examinons.

#### <a name="wiring-up-the-button"></a>Connexion du bouton

Nous allons `ViewController1` le charger lors du premier démarrage de l’application. Quand l’utilisateur appuie sur le bouton, nous allons masquer le bouton et charger un `UITabBarController` avec l' `ViewController1` instance dans le premier onglet.

Lorsque l’utilisateur libère le `aButton` , nous voulons déclencher un événement TouchUpInside. Nous allons sélectionner le bouton, puis, dans l' **onglet événements du panneau** propriétés, déclarer le gestionnaire d’événements ( `InitialActionCompleted` –) pour qu’il puisse être référencé dans le code. Cela est illustré dans la capture d’écran ci-dessous :

[![Quand l’utilisateur relâche le aButton, déclencher un événement TouchUpInside](creating-tabbed-applications-images/event-handler.png)](creating-tabbed-applications-images/event-handler.png#lightbox)

Nous devons maintenant indiquer au contrôleur d’affichage de masquer le bouton lorsque l’événement se déclenche `InitialActionCompleted` . Dans `ViewController1` , ajoutez la méthode partielle suivante :

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Enregistrez le fichier et exécutez l’application. Nous devrions voir apparaître l’écran 1 et le bouton disparaissent au toucher.

#### <a name="adding-the-tab-bar-controller"></a>Ajout du contrôleur de barre d’onglets

Nous avons maintenant une vue initiale qui fonctionne comme prévu. Ensuite, nous souhaitons l’ajouter à un `UITabBarController` , avec les vues 2 et 3. Nous allons ouvrir la table de montage séquentiel dans le concepteur.

Dans la **boîte à outils**, recherchez le **contrôleur de barre d’onglets** sous contrôleurs & objets et faites-le glisser sur le aire de conception. Comme vous pouvez le voir dans la capture d’écran ci-dessous, le contrôleur de barre d’onglets est sans interface utilisateur et, par conséquent, propose deux contrôleurs d’affichage par défaut :

[![Ajout d’un contrôleur de barre d’onglets à la disposition](creating-tabbed-applications-images/tabbarcontroller.png)](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Supprimez ces nouveaux contrôleurs d’affichage en sélectionnant la barre noire en bas et en appuyant sur supprimer.

Dans notre Storyboard, nous pouvons utiliser SEGUES pour gérer les transitions entre les TabBarController et nos contrôleurs d’affichage. Après l’interaction avec la vue initiale, nous voulons la charger dans le TabBarController présenté à l’utilisateur. Nous allons le configurer dans le concepteur.

**Appuyez sur CTRL** et **faites glisser** le bouton vers le TabBarController. Au niveau de la souris, un menu contextuel s’affiche. Nous souhaitons utiliser un segue modal.

Pour configurer chacun de nos onglets, **Appuyez sur CTRL +** TabBarController pour chacun de nos contrôleurs d’affichage, d’une à trois, puis sélectionnez l' **onglet** relation dans le menu contextuel, comme illustré ci-dessous :

[![Sélectionner la relation d’onglet](creating-tabbed-applications-images/context-menu.png)](creating-tabbed-applications-images/context-menu.png#lightbox)

Votre storyboard doit ressembler à la capture d’écran ci-dessous :

[![La table de montage séquentiel doit ressembler à cette capture d’écran](creating-tabbed-applications-images/segue-layout.png)](creating-tabbed-applications-images/segue-layout.png#lightbox)

Si nous cliquons sur l’un des éléments de la barre d’onglets et explorons le panneau Propriétés, vous pouvez voir plusieurs options, comme illustré ci-dessous :

[![Définition des options de l’onglet dans l’Explorateur de propriétés](creating-tabbed-applications-images/properties-panel.png)](creating-tabbed-applications-images/properties-panel.png#lightbox)

Nous pouvons l’utiliser pour modifier certains attributs, tels que le badge, le titre et l’identificateur iOS, entre autres.

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

[![Exemple de sortie d’application](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>Récapitulatif

Cet article a décrit comment utiliser un `UITabBarController` dans une application. Nous avons parcouru le mode de chargement des contrôleurs dans chaque onglet, ainsi que la façon de définir des propriétés sur les onglets tels que le titre, l’image et le badge. Nous avons ensuite examiné, à l’aide des storyboards, à charger un `UITabBarController` lors de l’exécution lorsqu’il n’est pas le `RootViewController` de la fenêtre.

## <a name="related-links"></a>Liens connexes

- [Création d’applications avec onglets (exemple)](/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Référence de la classe UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)