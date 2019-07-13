---
title: Hello, tvOS Guide de démarrage rapide
description: Ce guide explique comment créer votre première application Xamarin.tvOS et sa chaîne d’outils de développement. Il présente également le Concepteur de Xamarin, qui expose des contrôles d’interface utilisateur au code et montre comment créer, exécuter et tester une application Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 859bbd22640ba3d09324fcd3853cda26e563a1cd
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865277"
---
# <a name="hello-tvos-quick-start-guide"></a>Hello, tvOS Guide de démarrage rapide

_Ce guide explique comment créer votre première application Xamarin.tvOS et sa chaîne d’outils de développement. Il présente également le Concepteur de Xamarin, qui expose des contrôles d’interface utilisateur au code et montre comment créer, exécuter et tester une application Xamarin.tvOS._

Apple a publié la 5e génération de l’Apple TV, l’Apple TV 4K, qui s’exécute tvOS 11.

La plateforme Apple TV est ouverte pour les développeurs, ce qui leur permet de créer des applications riches et immersives et publiez-les via le Store d’application intégrée des téléviseurs Apple.

Si vous êtes familiarisé avec le développement Xamarin.iOS, vous devez rechercher la transition vers tvOS relativement simple. La plupart des API et fonctionnalités est les mêmes, toutefois, de nombreuses API courants ne sont pas disponibles (par exemple, WebKit). En outre, travailler avec les avec la Siri Remote pose des difficultés de conception qui ne sont pas présentes dans l’écran tactile en fonction des appareils iOS.

Ce guide vous donne une introduction à l’utilisation de tvOS dans une application Xamarin. Pour plus d’informations sur tvOS, veuillez consulter d’Apple [se préparer pour Apple TV 4K](https://developer.apple.com/tvos/) documentation.

## <a name="overview"></a>Présentation

Xamarin.tvOS vous permet de développer des applications entièrement natives Apple TV dans C# et .NET à l’aide de contrôles d’interface qui sont utilisés lors du développement dans les mêmes bibliothèques OS X *Swift* (ou *Objective-C*) et  *Xcode*.

En outre, depuis Xamarin.tvOS les applications sont écrites dans C# et code backend commun, de .NET, peut être partagée avec les applications Xamarin.iOS, Xamarin.Android et Xamarin.Mac ; tout en proposant une expérience native sur chaque plateforme.

Cet article vous présentera les concepts clés nécessaires pour créer une application de TV Apple à l’aide de Xamarin.tvOS et Visual Studio en vous guidant tout au long du processus de création d’un base **Hello, tvOS** application qui compte le nombre de fois où un bouton a l’utilisateur cliqué sur :

[![](hello-tvos-images/run05.png "Exécution de l’application exemple")](hello-tvos-images/run05.png#lightbox)

Nous aborderons les concepts suivants :

-  **Visual Studio pour Mac** – Introduction à Visual Studio pour Mac et comment créer des applications de Xamarin.tvOS avec lui.
-  **Anatomie d’une application Xamarin.tvOS** – que Xamarin.tvOS une application se compose de.
-  **Création d’une Interface utilisateur** – comment utiliser Concepteur de Xamarin pour iOS pour créer une interface utilisateur.
-  **Déploiement et le test** – comment exécuter et tester votre application dans le simulateur de tvOS et sur un matériel réel tvOS.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Démarrage d’une nouvelle application Xamarin.tvOS dans Visual Studio pour Mac

Comme indiqué ci-dessus, nous allons créer une application Apple TV appelée `Hello-tvOS` qui ajoute un bouton unique et une étiquette à l’écran principal. Quand vous cliquez sur le bouton, l’étiquette affiche le nombre de clics effectués.

Pour commencer, nous allons effectuer les opérations suivantes :

1. Démarrez Visual Studio pour Mac :

    [![](hello-tvos-images/setup01.png "Visual Studio pour Mac")](hello-tvos-images/setup01.png#lightbox)
2. Cliquez sur le **nouvelle Solution...**  lien dans le coin supérieur gauche de l’écran pour ouvrir le **nouveau projet** boîte de dialogue.
3. Sélectionnez **tvOS** > **application** > **application avec affichage unique** et cliquez sur le **suivant** bouton :

    [![](hello-tvos-images/setup02.png "Sélectionnez l’application avec affichage unique")](hello-tvos-images/setup02.png#lightbox)
4. Entrez `Hello, tvOS` pour le **nom de l’application**, entrez votre **identificateur d’organisation** et cliquez sur le **suivant** bouton :

    [![](hello-tvos-images/setup04.png "Entrez Hello, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Entrez `Hello_tvOS` pour le **nom_projet** et cliquez sur le **créer** bouton :

    [![](hello-tvos-images/setup03.png "Entrez HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio pour Mac créer l’application Xamarin.tvOS et afficher les fichiers par défaut qui sont ajoutés à votre solution d’application :

 [![](hello-tvos-images/project01.png "Afficher les fichiers par défaut")](hello-tvos-images/project01.png#lightbox)

Visual Studio pour Mac utilise **Solutions** et **projets**, de la même façon que Visual Studio. Une solution est un conteneur qui peut recevoir un ou plusieurs projets ; les projets peuvent inclure des applications, bibliothèques de prise en charge, applications de test, etc. Dans ce cas, Visual Studio pour Mac a créé une solution et un projet d’application pour vous.

Si vous le souhaitez, vous pouvez créer un ou plusieurs code des projets de bibliothèque qui contiennent le code commun partagé. Ces projets de bibliothèque peuvent être consommées par le projet d’application ou partagés avec d’autres projets d’application Xamarin.tvOS (ou Xamarin.iOS, Xamarin.Android et Xamarin.Mac selon le type de code), tout comme vous le feriez si vous conceviez une application .NET standard.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomie d’une application Xamarin.tvOS

Si vous êtes familiarisé avec la programmation iOS, vous remarquerez que de nombreuses similitudes ici. En fait, tvOS 9 étant un sous-ensemble d’iOS 9, de nombreux concepts se croisent ici.

Examinons les fichiers dans le projet :

-   `Main.cs` : ce fichier contient le point d’entrée principal de l’application. Quand l’application est lancée, il contient la toute première classe et la méthode qui est exécutée.
-   `AppDelegate.cs` : Ce fichier contient la classe d’application principale qui est chargée d’écouter les événements du système d’exploitation.
-   `Info.plist` : Ce fichier contient des propriétés telles que le nom de l’application, icônes, etc. de l’application.
-   `ViewController.cs` – Il s’agit de la classe qui représente la fenêtre principale et contrôle le cycle de vie de celui-ci.
-   `ViewController.designer.cs` : Ce fichier contient le code de base qui vous permet d’intégrer avec interface utilisateur de l’écran principal.
-  `Main.storyboard` – L’interface utilisateur pour la fenêtre principale. Ce fichier permettre être créé et géré par le Concepteur de Xamarin pour iOS.

Dans les sections suivantes, nous allons jeter un coup de œil rapide via certains de ces fichiers. Nous explorerons les plus en détail plus tard, mais il est judicieux de comprendre leurs concepts de base maintenant.

### <a name="maincs"></a>Main.cs

Le `Main.cs` fichier contient un mappage statique `Main` méthode qui crée une nouvelle instance d’application Xamarin.tvOS et transmet le nom de la classe qui gère les événements de système d’exploitation, soit dans notre cas la `AppDelegate` classe :

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

Le `AppDelegate.cs` fichier contient notre `AppDelegate` (classe), qui est responsable de la création de notre fenêtre et écouter des événements de système d’exploitation :

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

Ce code est probablement inconnu, sauf si vous avez créé une application iOS avant, mais il est relativement simple. Commençons par examiner les lignes importantes.

Tout d’abord, jetons un œil à la déclaration de variable de niveau classe :

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

Le `Window` propriété fournit l’accès à la fenêtre principale. tvOS utilise ce que l'on appelle le *Model View Controller* modèle (MVC). En règle générale, pour chaque fenêtre que vous créez (et de nombreuses autres choses dans windows), il existe un contrôleur, ce qui est chargé de cycle de vie de la fenêtre, telles que son affichage, l’ajout de nouvelles vues (contrôles), etc.

Ensuite, nous avons le `FinishedLaunching` (méthode). Cette méthode est exécutée une fois que l’application a été instanciée, et il est chargé pour la création de la fenêtre d’application et commencer le processus d’affichage de la vue qu’il contient. Étant donné que notre application utilise une table de montage séquentiel pour définir son interface utilisateur, aucun code supplémentaire n’est nécessaire ici.

Il existe de nombreuses autres méthodes qui sont fournis dans le modèle, tels que `DidEnterBackground` et `WillEnterForeground`. Vous pouvez en supprimer en toute sécurité si les événements d’application ne sont pas utilisées dans votre application.

### <a name="viewcontrollercs"></a>ViewController.cs

Le `ViewController` classe est contrôleur de notre la fenêtre principale. Cela signifie qu’il est chargé pour le cycle de vie de la fenêtre principale. Nous allons examiner cela en détail plus tard, pour l’instant nous allons jeter un examen rapide :

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

Le fichier de concepteur pour la classe de fenêtre principale est maintenant vide, mais il sera être automatiquement rempli par Visual Studio pour Mac quand nous créons notre Interface utilisateur avec le concepteur iOS :

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Nous ne sont pas généralement ce qui concerne les fichiers de concepteur, qu’ils sont automatiquement gérés par Visual Studio pour Mac et simplement fournir le code de base requis qui autorise l’accès aux contrôles que nous ajouter à n’importe quelle fenêtre ou d’afficher dans notre application.

Maintenant que nous avons créé notre application Xamarin.tvOS et nous avons une connaissance élémentaire de ses composants, examinons la création de l’interface utilisateur.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Création de l’interface utilisateur

Vous n’êtes pas obligé d’utiliser le Concepteur de Xamarin pour iOS pour créer l’Interface utilisateur pour votre application Xamarin.tvOS, l’interface utilisateur peut être créé directement à partir de C# code mais qui n’entre pas dans le cadre de cet article. Par souci de simplicité, nous allons utiliser le concepteur iOS pour créer notre interface utilisateur dans le reste de ce didacticiel.

Pour commencer à créer votre interface utilisateur, nous allons double-cliquez sur le `Main.storyboard` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification dans le concepteur iOS :

[![](hello-tvos-images/designer01.png "Fichier Main.storyboard dans l’Explorateur de solutions")](hello-tvos-images/designer01.png#lightbox)

Cela doit lancer le concepteur et l’aspect suivant :

[![](hello-tvos-images/designer02.png "Le Concepteur")](hello-tvos-images/designer02.png#lightbox)

Pour plus d’informations sur le concepteur iOS et son fonctionnement, reportez-vous à la [Introduction au Concepteur de Xamarin pour iOS](~/ios/user-interface/designer/introduction.md) guide.

Nous pouvons maintenant commencer l’ajout de contrôles à l’aire de conception de notre application Xamarin.tvOS.

Effectuez ce qui suit :

1. Recherchez le **boîte à outils**, qui doit être à droite de l’aire de conception :

    [![](hello-tvos-images/designer03.png "La boîte à outils")](hello-tvos-images/designer03.png#lightbox)

    Si vous ne le trouvez pas ici, accédez à **Afficher > panneaux > boîte à outils** pour l’afficher.
2. Faites glisser un **étiquette** à partir de la **boîte à outils** à l’aire de conception :

    [![](hello-tvos-images/designer04.png "Faites glisser une étiquette à partir de la boîte à outils")](hello-tvos-images/designer04.png#lightbox)
3. Cliquez sur le **titre** propriété dans le **remplissage de la propriété** et modifier le titre du bouton à `Hello, tvOS` et définir le **la taille de police** à 128 :

    [![](hello-tvos-images/designer05.png "Définir le titre à Hello, tvOS et définir la taille de police à 128")](hello-tvos-images/designer05.png#lightbox)
4. Redimensionnez l’étiquette afin que tous les mots sont visibles et placez-le centré vers le haut de la fenêtre :

    [![](hello-tvos-images/designer06.png "Redimensionner et centrer l’étiquette")](hello-tvos-images/designer06.png#lightbox)
5. L’étiquette devez maintenant être contraints à sa position, afin qu’il apparaisse comme prévu. quelle que soit la taille de l’écran. Pour ce faire, cliquez sur l’étiquette jusqu'à ce que le *poignée T* s’affiche :

    [![](hello-tvos-images/designer07.png "La poignée T")](hello-tvos-images/designer07.png#lightbox)
6. Pour contraindre l’étiquette horizontalement, sélectionnez le carré central et faites-le glisser vers la ligne verticale en pointillé :

    [![](hello-tvos-images/designer08.png "Sélectionnez le carré central")](hello-tvos-images/designer08zoom.png#lightbox)

     L’étiquette doit afficher en orange.
7. Sélectionnez la poignée T en haut de l’étiquette et faites-la glisser vers le bord supérieur de la fenêtre :

    [![](hello-tvos-images/designer09.png "Faites glisser la poignée sur le bord supérieur de la fenêtre")](hello-tvos-images/designer09.png#lightbox)
8. Ensuite, cliquez sur la largeur et la hauteur *handle d’OS* comme illustré ci-dessous :

    [![](hello-tvos-images/designer10.png "La largeur et la hauteur des handles OS")](hello-tvos-images/designer10.png#lightbox)

     Lorsque chaque *handle d’OS* est activé, sélectionnez la largeur et hauteur respectivement pour définir des dimensions fixes.
9. Une fois terminé, vos contraintes doivent ressembler à celles figurant dans l’onglet Disposition du panneau Propriétés :

    [![](hello-tvos-images/designer11.png "Contraintes d’exemple")](hello-tvos-images/designer11.png#lightbox)
10. Faites glisser un **bouton** à partir de la **boîte à outils** et placez-le sous l’étiquette.
11. Cliquez sur le **titre** propriété dans le **remplissage de la propriété** et modifier le titre du bouton à `Click Me`:

    [![](hello-tvos-images/designer12.png "Remplacez le titre de boutons par cliquez ici")](hello-tvos-images/designer12.png#lightbox)
12. Répétez les étapes 5 à 8 ci-dessus pour contraindre le bouton dans la fenêtre de tvOS. Toutefois, au lieu de la poignée de T-vers le haut de la fenêtre (comme à l’étape #7), faites-le glisser vers le bas de l’étiquette :

    [![](hello-tvos-images/designer14.png "Limiter le bouton")](hello-tvos-images/designer14.png#lightbox)
13. Faites glisser une autre étiquette sous le bouton, de taille pour qu’il soit la même largeur que le premier contrôle label et son **alignement** à **Center**:

    [![](hello-tvos-images/designer15.png "Faites glisser une autre étiquette sous le bouton, de taille pour la même largeur que la première étiquette et définissez son alignement au centre")](hello-tvos-images/designer15.png#lightbox)
14. Comme la première étiquette et bouton, définissez cette étiquette pour l’épingler à l’emplacement et la taille et de centrage :

    [![](hello-tvos-images/designer16.png "Épingler l’étiquette dans l’emplacement et la taille")](hello-tvos-images/designer16.png#lightbox)
15. Enregistrez vos modifications dans l’Interface utilisateur.

Lorsque vous ont été redimensionnement et le déplacement des contrôles, vous avez devez remarqué que le concepteur vous donne brèves indications utiles qui sont basées sur [Apple TV Human Interface Guidelines pour](https://developer.apple.com/tvos/human-interface-guidelines/). Ces recommandations vous aideront à créer des applications de haute qualité qui auront un aspect familier pour les utilisateurs Apple TV.

Si vous regardez le **structure du Document** section, notez comment la disposition et la hiérarchie des éléments qui composent notre Interface utilisateur sont affichées :

[![](hello-tvos-images/designer17.png "La section structure du Document")](hello-tvos-images/designer17.png#lightbox)

À partir de là, vous pouvez sélectionner les éléments à modifier ou déplacer pour réorganiser les éléments d’interface utilisateur si nécessaire. Par exemple, si un élément d’interface utilisateur a été couvert par un autre élément, vous pourrez le glisser vers le bas de la liste pour en faire l’élément supérieur sur la fenêtre.

Maintenant que nous avons notre Interface utilisateur créée, nous devons exposer les éléments d’interface utilisateur afin que Xamarin.tvOS peut accéder et interagir avec eux dans C# code.

### <a name="accessing-the-controls-in-the-code-behind"></a>Accéder aux contrôles dans le code-behind

Il existe deux principales manières d’accéder aux contrôles que vous avez ajoutés dans le concepteur iOS à partir du code :

* Création d’un gestionnaire d’événements sur un contrôle.
* Ce qui donne le contrôle un nom, afin que nous pouvons référencer ultérieurement.

Lorsque une de ces sont ajoutés, la classe partielle dans le `ViewController.designer.cs` sera mis à jour pour refléter les modifications. Cela vous permettra à ensuite accéder aux contrôles dans le contrôleur d’affichage.

### <a name="creating-an-event-handler"></a>Création d’un gestionnaire d’événements

Dans cet exemple d’application, un clic sur le bouton nous voulons _quelque chose_ se produire, par conséquent, un gestionnaire d’événements doit être ajouté à un événement spécifique sur le bouton. Pour ce faire, procédez comme suit :

1. Dans le Concepteur de Xamarin iOS, sélectionnez le bouton sur le contrôleur d’affichage.
2. Dans le panneau Propriétés, sélectionnez le **événements** onglet :

    [![](hello-tvos-images/event1.png "L’onglet d’événements")](hello-tvos-images/event1.png#lightbox)
3. Recherchez l’événement TouchUpInside et lui donner un gestionnaire d’événements nommé `Clicked`:

    [![](hello-tvos-images/event2.png "L’événement TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Quand vous appuyez sur **entrée**, le **ViewController**fichier .cs s’ouvre, suggérant des emplacements pour votre gestionnaire d’événements dans le code. Utilisez les touches de direction de votre clavier pour définir l’emplacement :

    [![](hello-tvos-images/event3.png "Définition de l’emplacement")](hello-tvos-images/event3.png#lightbox)
5. Cela créera une méthode partielle, comme indiqué ci-dessous :

    [![](hello-tvos-images/event4.png "La méthode partielle")](hello-tvos-images/event4.png#lightbox)

Nous sommes maintenant prêts à commencer à ajouter du code pour autoriser le bouton de la fonction.

### <a name="naming-a-control"></a>Un contrôle d’affectation de noms

Lorsque le bouton est activé, l’étiquette doit mettre à jour en fonction du nombre de clics. Pour ce faire, nous devrons l’étiquette dans le code d’accès. Pour cela, vous devez lui donner un nom. Effectuez ce qui suit :

1. Ouvrez le plan conceptuel, puis sélectionnez l’étiquette du bas du contrôleur d’affichage.
2. Dans le panneau Propriétés, sélectionnez le **Widget** onglet :

    [![](hello-tvos-images/name1.png "Sélectionnez l’onglet de Widget")](hello-tvos-images/name1.png#lightbox)
3. Sous **identité > nom**, ajouter `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Ensemble ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Nous sommes maintenant prêts à démarrer la mise à jour l’étiquette !

### <a name="how-controls-are-accessed"></a>Comment les contrôles sont accessibles

Si vous sélectionnez le `ViewController.designer.cs` dans le **l’Explorateur de solutions** vous serez en mesure de voir comment la `ClickedLabel` étiquette et la `Clicked` Gestionnaire d’événements ont été mappés à un **Outlet** et **Action** dans C#:

[![](hello-tvos-images/accesscontrol.png "Outlets et Actions")](hello-tvos-images/accesscontrol.png#lightbox)

Vous pouvez également remarquer que `ViewController.designer.cs` est une classe partielle, afin que Visual Studio pour Mac n’aient à modifier `ViewController.cs` qui remplacerait toutes les modifications que nous avons apportées à la classe.

Exposition des éléments d’interface utilisateur de cette façon, vous permet d’y accéder dans le contrôleur d’affichage.

Normalement, vous n’avez jamais besoin ouvrir le `ViewController.designer.cs` vous-même, il a été présenté ici à des fins pédagogiques uniquement.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Écriture du code

Avec notre Interface utilisateur créée et ses éléments exposés au code via **Outlets** et **Actions**, nous sommes enfin prêts à écrire le code pour donner la fonctionnalité du programme.

Dans notre application, chaque fois que le premier bouton est activé, nous allons mettre à jour notre étiquette pour afficher le nombre de fois où le bouton a été utilisé. Pour ce faire, nous devons ouvrir le `ViewController.cs` fichier pour le modifier en double-cliquant dessus dans le **panneau solutions**:

[![](hello-tvos-images/code01.png "Le panneau Solution")](hello-tvos-images/code01.png#lightbox)

Tout d’abord, nous devons créer une variable de niveau classe dans notre `ViewController` classe pour suivre le nombre de clics qui ont eu lieu. Modifiez la définition de classe pour qu’elle ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Ensuite, dans la même classe (`ViewController`), nous devons remplacer le **ViewDidLoad** (méthode) et ajouter du code pour définir le message initial pour notre étiquette :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Nous devons utiliser `ViewDidLoad`, au lieu d’une autre méthode comme `Initialize`, car `ViewDidLoad` est appelée *après* le système d’exploitation a chargé et instancié l’Interface utilisateur à partir de la `.storyboard` fichier. Si nous avons essayé d’accéder au contrôle label avant le `.storyboard` fichier a été entièrement chargé et instancié, nous obtiendrions une `NullReferenceException` erreur car le contrôle d’étiquette ne serait pas encore créé.

Ensuite, nous devons ajouter le code pour répondre à l’utilisateur en cliquant sur le bouton. Ajoutez le code suivant sur partial classe pour que nous avons créé :

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Ce code sera appelé chaque fois que l’utilisateur clique sur notre bouton.

Avec tous les éléments en place, nous sommes maintenant prêts à créer et tester notre application Xamarin.tvOS.

## <a name="testing-the-application"></a>Test de l'application

Il est temps pour générer et exécuter notre application pour vous assurer qu’il s’exécute comme prévu. Nous pouvons générer et exécuter tout en une seule étape, ou nous pouvons le générer sans l’exécuter.

Chaque fois que nous générez une application, nous pouvons choisir de build que nous voulons :

-   **Déboguer** – une version debug est compilée dans un « fichier (application) avec des métadonnées supplémentaires qui nous permet de déboguer ce qui se passe pendant l’exécution de l’application.
-   **Mise en production** – une version Release crée également un « fichier, mais il n’inclut pas les informations de débogage, il est plus petit et s’exécute plus rapidement.  

Vous pouvez sélectionner le type de build à partir de la **sélecteur de configurations** dans l’angle supérieur gauche de l’écran Visual Studio pour Mac :

[![](hello-tvos-images/run01.png "Sélectionnez le type de build")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Génération de l'application

Dans notre cas, nous vous voulez uniquement une version debug, nous allons donc vous assurer que **déboguer** est sélectionné. Commençons par créer notre application tout d’abord en appuyant sur la combinaison **⌘B**, ou à partir de la **Build** menu, choisissez **générer tout**.

S’il n’était pas toutes les erreurs, vous verrez un **génération a réussi** message dans Visual Studio pour la barre d’état du Mac. Si vous rencontrez des erreurs, passez en revue votre projet et assurez-vous que vous avez suivi les étapes correctement. Commencez par confirmer que votre code (à la fois dans Xcode et dans Visual Studio pour Mac) correspond au code dans le didacticiel.

### <a name="running-the-application"></a>Exécution de l'application

Pour exécuter l’application, nous avons trois options :

-  Appuyez sur **⌘+Entrée**.
-  Dans le menu **Exécuter**, choisissez **Déboguer**.
-  Cliquez sur le bouton **Play** (Lire) dans la barre d’outils Visual Studio pour Mac (juste au-dessus de l’**Explorateur de solutions**).

L’application est générée (si elle n’a pas déjà fait), démarrage en mode débogage, le simulateur tvOS démarrera et lance l’application et afficher sa fenêtre de l’interface principale :

[![L’écran d’accueil application exemple](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

À partir de la **matériel** menu, sélectionnez **afficher Apple TV distant** afin de contrôler le simulateur.

[![](hello-tvos-images/run04.png "Sélectionnez Afficher Apple TV distant")](hello-tvos-images/run04.png#lightbox)

À l’aide à distance du simulateur, si vous cliquez sur le bouton plusieurs fois, l’étiquette doit être mis à jour avec le nombre :

[![](hello-tvos-images/run05.png "L’étiquette avec le nombre de mises à jour")](hello-tvos-images/run05.png#lightbox)

Félicitations ! Nous avons couvert pas mal de terrain ici, mais si vous avez suivi ce didacticiel à partir du début à la fin, vous devez maintenant avoir une connaissance approfondie des composants d’une application Xamarin.tvOS, ainsi que les outils utilisés pour les créer.

## <a name="where-to-next"></a>Où suivant ?

Développement présente d’applications Apple TV quelques défis en raison de la déconnexion entre l’utilisateur et l’interface (c’est à travers la pièce pas à portée de main de l’utilisateur) et les limitations tvOS place sur le stockage et la taille de l’application.

Par conséquent, nous suggérons hautement que votre lecture suit les documents avant de passer à la conception d’une application Xamarin.tvOS :

- [Introduction à tvOS 9](~/ios/tvos/platform/tvos9.md) : cet article présente tous les nouveaux et modifiés API et fonctionnalités disponibles dans tvOS 9 pour les développeurs Xamarin.tvOS.
- [Utilisation de la Navigation et le Focus](~/ios/tvos/app-fundamentals/navigation-focus.md) – les utilisateurs de votre application Xamarin.tvOS pas d’interagir avec son interface directement en tant qu’avec iOS où ils appuyer sur les images sur l’écran du périphérique, mais indirectement à travers la salle à l’aide de l’instance distante de Siri. Cet article explique le concept de Focus et comment elle est utilisée pour gérer la Navigation dans l’Interface utilisateur d’une application Xamarin.tvOS.
- [Siri Remote et Bluetooth contrôleurs](~/ios/tvos/platform/remote-bluetooth.md) : le principal moyen d’utilisateurs d’interagir avec l’Apple TV et votre application Xamarin.tvOS, consiste à utiliser la télécommande Siri inclus. Si votre application est un jeu, vous pouvez éventuellement créer prise en charge pour le tiers 3e, effectuées pour les contrôleurs de jeu de Bluetooth iOS (IFM) dans votre application, ainsi. Cet article décrit la prise en charge les contrôleurs de jeu Siri Remote et Bluetooth nouvelle dans vos applications Xamarin.tvOS.
- [Stockage de données et ressources](~/ios/tvos/app-fundamentals/resources-data-storage.md) – Contrairement aux appareils iOS, le nouveau Apple TV ne fournit pas un stockage local pour les applications tvOS. Par conséquent, si votre application Xamarin.tvOS a besoin de conserver les informations (telles que les préférences de l’utilisateur) qu’il doit stocker et récupérer ces données à partir d’iCloud. Cet article aborde l’utilisation des ressources et de stockage des données persistantes dans une application Xamarin.tvOS.
- [Utilisation des icônes et Images](~/ios/tvos/app-fundamentals/icons-images.md) – création captivante des icônes et des images sont un élément essentiel du développement d’une expérience utilisateur immersives pour vos applications Apple TV. Ce guide décrit les étapes nécessaires pour créer et inclure les ressources graphiques nécessaires pour vos applications Xamarin.tvOS.
- [Interface utilisateur](~/ios/tvos/user-interface/index.md) – y compris les contrôles d’Interface utilisateur (IU), une couverture général utilisateur expérience utiliser Interface Builder de Xcode et les principes de conception de l’expérience utilisateur lorsque vous travaillez avec Xamarin.tvOS.
- [Déploiement et le test](~/ios/tvos/deploy-test/index.md) – cette section couvre les sujets utilisés pour tester une application et comment distribuer. Rubriques incluent des éléments tels que les outils utilisés pour le débogage, le déploiement à des testeurs et comment publier une application sur le Store d’application Apple TV.

Si vous rencontrez des problèmes avec Xamarin.tvOS, veuillez consulter notre [dépannage](~/ios/tvos/troubleshooting.md) documentation pour obtenir la liste de problèmes connus et solutions.

## <a name="summary"></a>Récapitulatif

Cet article fournit un démarrage rapide pour le développement d’applications pour tvOS avec Visual Studio pour Mac en créant un simple Hello, application tvOS. Il couvert les principes fondamentaux de l’appareil tvOS d’approvisionnement, de création de l’interface, de codage de tvOS et de test sur le simulateur de tvOS.


## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Création d’applications pour tvOS avec Xamarin (vidéo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
