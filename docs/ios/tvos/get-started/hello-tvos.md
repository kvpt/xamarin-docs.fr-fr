---
title: Hello, tvOS Guide de démarrage rapide
description: Ce guide vous guide dans la création de votre première application Xamarin. tvOS et de ses chaîne d’outils de développement. Il présente également Xamarin designer, qui expose les contrôles d’interface utilisateur au code, et montre comment générer, exécuter et tester une application Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 59019e08c279a9910281de98fe64db798379e1a4
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119164"
---
# <a name="hello-tvos-quick-start-guide"></a>Hello, tvOS Guide de démarrage rapide

_Ce guide vous guide dans la création de votre première application Xamarin. tvOS et de ses chaîne d’outils de développement. Il présente également Xamarin designer, qui expose les contrôles d’interface utilisateur au code, et montre comment générer, exécuter et tester une application Xamarin. tvOS._

Apple a publié la 5e génération d’Apple TV, la version d’Apple TV 4K, qui exécute tvOS 11.

La plateforme Apple TV est ouverte aux développeurs, ce qui leur permet de créer des applications riches et immersifs et de les diffuser via l’App Store intégré d’Apple TV.

Si vous êtes familiarisé avec le développement Xamarin. iOS, vous devriez trouver la transition vers tvOS relativement simple. La plupart des API et des fonctionnalités sont identiques, cependant, de nombreuses API courantes ne sont pas disponibles (par exemple, WebKit). En outre, l’utilisation du avec la télécommande Siri pose des problèmes de conception qui ne sont pas présents dans les appareils iOS basés sur un écran tactile.

Ce guide vous donnera une introduction à l’utilisation de tvOS dans une application Xamarin. Pour plus d’informations sur tvOS, consultez la documentation d’Apple [Ready pour Apple TV 4k](https://developer.apple.com/tvos/) .

## <a name="overview"></a>Présentation

Xamarin. tvOS vous permet de développer des applications de TV Apple entièrement C# natives dans et .net à l’aide des mêmes bibliothèques OS X et de contrôles d’interface que ceux utilisés lors du développement dans *SWIFT* (ou *objective-C*) et *Xcode*.

En outre, étant donné que les applications Xamarin. tvOS C# sont écrites dans et .net, le code backend commun peut être partagé avec les applications Xamarin. iOS, Xamarin. Android et Xamarin. Mac. tout en distribuant une expérience native sur chaque plateforme.

Cet article vous présente les concepts clés nécessaires à la création d’une application de TV Apple à l’aide de Xamarin. tvOS et de Visual Studio en vous guidant tout au long du processus de création d’une application **Hello, tvOS** de base qui compte le nombre de clics sur un bouton:

[![](hello-tvos-images/run05.png "Exemple d’exécution d’application")](hello-tvos-images/run05.png#lightbox)

Nous allons aborder les concepts suivants:

- **Visual Studio pour Mac** : présentation du Visual Studio pour Mac et de la création d’applications Xamarin. tvOS.
- **Anatomie d’une application Xamarin. tvOS** – qu’est-ce qu’une application Xamarin. tvOS.
- **Création d’une interface utilisateur** : comment utiliser pour Xamarin Designer pour IOS pour créer une interface utilisateur.
- **Déploiement et test** : comment exécuter et tester votre application dans le simulateur tvOS et sur le matériel tvOS réel.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Démarrage d’une nouvelle application Xamarin. tvOS dans Visual Studio pour Mac

Comme indiqué ci-dessus, nous allons créer une application de `Hello-tvOS` TV Apple appelée qui ajoute un bouton et une étiquette uniques à l’écran principal. Quand vous cliquez sur le bouton, l’étiquette affiche le nombre de clics effectués.

Pour commencer, procédez comme suit:

1. Démarrez Visual Studio pour Mac :

    [![](hello-tvos-images/setup01.png "Visual Studio pour Mac")](hello-tvos-images/setup01.png#lightbox)
2. Cliquez sur le lien **nouvelle solution...** dans le coin supérieur gauche de l’écran pour ouvrir la boîte de dialogue **nouveau projet** .
3. Sélectionnez application de**vue unique** de l'**application** >  **tvOS** > , puis cliquez sur le bouton **suivant** :

    [![](hello-tvos-images/setup02.png "Sélectionner une application avec affichage unique")](hello-tvos-images/setup02.png#lightbox)
4. Entrez `Hello, tvOS` pour le **nom**de l’application, entrez l’identificateur de votre **organisation** , puis cliquez sur le bouton **suivant** :

    [![](hello-tvos-images/setup04.png "Entrez Hello, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Entrez `Hello_tvOS` pour le **nom du projet** , puis cliquez sur le bouton **créer** :

    [![](hello-tvos-images/setup03.png "Entrez HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio pour Mac créera la nouvelle application Xamarin. tvOS et affichera les fichiers par défaut qui seront ajoutés à la solution de votre application:

 [![](hello-tvos-images/project01.png "Affichage des fichiers par défaut")](hello-tvos-images/project01.png#lightbox)

Visual Studio pour Mac utilise des **solutions** et des **projets**, exactement de la même façon que Visual Studio. Une solution est un conteneur qui peut recevoir un ou plusieurs projets ; les projets peuvent inclure des applications, bibliothèques de prise en charge, applications de test, etc. Dans ce cas, Visual Studio pour Mac a créé à la fois une solution et un projet d’application pour vous.

Si vous le souhaitez, vous pouvez créer un ou plusieurs projets de bibliothèque de code contenant du code commun et partagé. Ces projets de bibliothèque peuvent être consommés par le projet d’application ou partagés avec d’autres projets d’application Xamarin. tvOS (ou Xamarin. iOS, Xamarin. Android et Xamarin. Mac en fonction du type de code), comme vous le feriez si vous étiez en train de créer une application .NET standard.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomie d’une application Xamarin. tvOS

Si vous êtes familiarisé avec la programmation iOS, vous remarquerez un grand nombre de similarités. En fait, tvOS 9 est un sous-ensemble d’iOS 9, donc un grand nombre de concepts seront abordés ici.

Jetons un coup d’œil aux fichiers du projet:

- `Main.cs` : ce fichier contient le point d’entrée principal de l’application. Quand l’application est lancée, il contient la toute première classe et la méthode qui est exécutée.
- `AppDelegate.cs`: Ce fichier contient la classe d’application principale qui est chargée d’écouter les événements du système d’exploitation.
- `Info.plist`: Ce fichier contient des propriétés d’application telles que le nom de l’application, des icônes, etc.
- `ViewController.cs`: Il s’agit de la classe qui représente la fenêtre principale et contrôle le cycle de vie de celui-ci.
- `ViewController.designer.cs`: Ce fichier contient le code de base qui vous permet de l’intégrer à l’interface utilisateur de l’écran principal.
- `Main.storyboard`: Interface utilisateur pour la fenêtre principale. Ce fichier peut être créé et géré par le Xamarin Designer pour iOS.

Dans les sections suivantes, nous allons examiner rapidement certains de ces fichiers. Nous les explorerons plus en détail plus tard, mais il est judicieux de comprendre leurs principes de base dès maintenant.

### <a name="maincs"></a>Main.cs

Le `Main.cs` fichier contient une méthode `Main` statique qui crée une instance de l’application Xamarin. tvOS et transmet le nom de la classe qui gérera les événements du système d’exploitation, qui `AppDelegate` , dans notre cas, est la classe:

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

Le `AppDelegate.cs` fichier contient notre `AppDelegate` classe, qui est responsable de la création de la fenêtre et de l’écoute des événements du système d’exploitation:

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

Ce code n’est probablement pas familier, sauf si vous avez déjà créé une application iOS, mais cela est relativement simple. Examinons les lignes importantes.

Tout d’abord, examinons la déclaration de variable au niveau de la classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

La `Window` propriété permet d’accéder à la fenêtre principale. tvOS utilise ce que l’on appelle le modèle MVC ( *Model View Controller* ). En règle générale, pour chaque fenêtre que vous créez (et pour de nombreux autres éléments dans Windows), il existe un contrôleur, qui est responsable du cycle de vie de la fenêtre, par exemple son affichage, l’ajout de nouvelles vues (contrôles), etc.

Nous avons ensuite la `FinishedLaunching` méthode. Cette méthode s’exécute après l’instanciation de l’application, et il est chargé de créer la fenêtre d’application et de commencer le processus d’affichage de la vue dans celle-ci. Étant donné que notre application utilise une table de montage séquentiel pour définir son interface utilisateur, aucun code supplémentaire n’est nécessaire ici.

Il existe de nombreuses autres méthodes fournies dans le modèle, telles que `DidEnterBackground` et `WillEnterForeground`. Elles peuvent être supprimées en toute sécurité si les événements d’application ne sont pas utilisés dans votre application.

### <a name="viewcontrollercs"></a>ViewController.cs

La `ViewController` classe est le contrôleur de la fenêtre principale. Cela signifie qu’il est responsable du cycle de vie de la fenêtre principale. Nous allons examiner cela en détail plus tard, pour l’instant, commençons par un bref aperçu:

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

Le fichier de concepteur pour la classe de fenêtre principale est vide pour l’instant, mais il sera rempli automatiquement par Visual Studio pour Mac lors de la création de notre interface utilisateur avec le concepteur iOS:

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

Nous n’avons généralement pas besoin de fichiers de concepteur, car ils sont gérés automatiquement par Visual Studio pour Mac et fournissent simplement le code de plomb nécessaire qui permet d’accéder aux contrôles que nous ajoutons à une fenêtre ou une vue dans notre application.

Maintenant que nous avons créé notre application Xamarin. tvOS et que nous avons une compréhension élémentaire de ses composants, examinons la création de l’interface utilisateur.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Création de l’interface utilisateur

Vous n’êtes pas obligé d’utiliser Xamarin Designer pour iOS pour créer l’interface utilisateur de votre application Xamarin. tvOS, l’interface utilisateur peut être C# créée directement à partir du code, mais cela dépasse le cadre de cet article. Par souci de simplicité, nous utiliserons le concepteur iOS pour créer l’interface utilisateur dans le reste de ce didacticiel.

Pour commencer à créer votre interface utilisateur, nous allons double- `Main.storyboard` cliquer sur le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier dans le concepteur iOS:

[![](hello-tvos-images/designer01.png "Fichier Main.storyboard dans l’Explorateur de solutions")](hello-tvos-images/designer01.png#lightbox)

Cette commande lance le concepteur et ressemble à ce qui suit:

[![](hello-tvos-images/designer02.png "Le concepteur")](hello-tvos-images/designer02.png#lightbox)

Pour plus d’informations sur le concepteur iOS et son fonctionnement, reportez-vous à la [Présentation du Guide de Xamarin Designer pour iOS](~/ios/user-interface/designer/introduction.md) .

Nous pouvons maintenant commencer à ajouter des contrôles à la surface de dessin de notre application Xamarin. tvOS.

Effectuez ce qui suit :

1. Recherchez la **boîte à outils**, qui doit se trouver à droite de l’aire de conception:

    [![](hello-tvos-images/designer03.png "Boîte à outils")](hello-tvos-images/designer03.png#lightbox)

    Si vous ne le trouvez pas ici, accédez à **afficher > pad > boîte à outils** pour l’afficher.
2. Faites glisser une **étiquette** de la **boîte à outils** vers l’aire de conception:

    [![](hello-tvos-images/designer04.png "Faire glisser une étiquette à partir de la boîte à outils")](hello-tvos-images/designer04.png#lightbox)
3. Cliquez sur la propriété **title** dans le panneau des **Propriétés** et remplacez le titre du bouton `Hello, tvOS` par et définissez la taille de la **police** sur 128:

    [![](hello-tvos-images/designer05.png "Définissez le titre sur Hello, tvOS et définissez la taille de police sur 128")](hello-tvos-images/designer05.png#lightbox)
4. Redimensionnez l’étiquette afin que tous les mots soient visibles et placez-le centré en haut de la fenêtre:

    [![](hello-tvos-images/designer06.png "Redimensionner et centrer l’étiquette")](hello-tvos-images/designer06.png#lightbox)
5. L’étiquette doit à présent être contrainte d’être positionnée sur sa position, afin qu’elle apparaisse comme prévu. quelle que soit la taille de l’écran. Pour ce faire, cliquez sur l’étiquette jusqu’à ce que la *poignée en forme de T* s’affiche:

    [![](hello-tvos-images/designer07.png "Handle en forme de T")](hello-tvos-images/designer07.png#lightbox)
6. Pour contraindre l’étiquette horizontalement, sélectionnez le carré central et faites-le glisser sur la ligne verticale en pointillés:

    [![](hello-tvos-images/designer08.png "Sélectionner le carré central")](hello-tvos-images/designer08zoom.png#lightbox)

     L’étiquette doit devenir orange.
7. Sélectionnez la poignée T en haut de l’étiquette, puis faites-la glisser vers le bord supérieur de la fenêtre:

    [![](hello-tvos-images/designer09.png "Faites glisser la poignée vers le bord supérieur de la fenêtre.")](hello-tvos-images/designer09.png#lightbox)
8. Ensuite, cliquez sur la largeur, puis sur la poignée de hauteur de l' *OS* , comme illustré ci-dessous:

    [![](hello-tvos-images/designer10.png "La largeur et la hauteur des poignées osseuses")](hello-tvos-images/designer10.png#lightbox)

     Lorsque vous cliquez sur chaque *poignée osseuse* , sélectionnez la largeur et la hauteur respectivement pour définir des dimensions fixes.
9. Lorsque vous avez terminé, vos contraintes doivent ressembler à celles de l’onglet disposition du panneau Propriétés:

    [![](hello-tvos-images/designer11.png "Exemples de contraintes")](hello-tvos-images/designer11.png#lightbox)
10. Faites glisser un **bouton** de la **boîte à outils** et placez-le sous l’étiquette.
11. Cliquez sur la propriété **title** dans le **bloc de propriétés** et remplacez le titre du bouton `Click Me`par:

    [![](hello-tvos-images/designer12.png "Modifiez le titre des boutons pour cliquer sur me")](hello-tvos-images/designer12.png#lightbox)
12. Répétez les étapes 5 à 8 ci-dessus pour contraindre le bouton dans la fenêtre tvOS. Toutefois, au lieu de faire glisser le handle T vers le haut de la fenêtre (comme à l’étape #7), faites-le glisser vers le bas de l’étiquette:

    [![](hello-tvos-images/designer14.png "Contraindre le bouton")](hello-tvos-images/designer14.png#lightbox)
13. Faites glisser une autre étiquette sous le bouton, redimensionnez-la de façon à ce qu’elle ait la même largeur que la première étiquette et définissez son **alignement** sur **Center**:

    [![](hello-tvos-images/designer15.png "Faites glisser une autre étiquette sous le bouton, redimensionnez-la de façon à ce qu’elle ait la même largeur que la première étiquette et définissez son alignement sur Center")](hello-tvos-images/designer15.png#lightbox)
14. Comme le premier contrôle Label et le bouton, définissez cette étiquette sur Center et épinglez-la dans emplacement et taille:

    [![](hello-tvos-images/designer16.png "Épingler l’étiquette à l’emplacement et à la taille")](hello-tvos-images/designer16.png#lightbox)
15. Enregistrez les modifications apportées à l’interface utilisateur.

Au fur et à mesure que vous redimensionnez et déplacez des contrôles, vous avez dû remarquer que le concepteur vous donne des conseils d’accrochage basés sur [les instructions d’interface utilisateur Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Ces instructions vous aideront à créer des applications de haute qualité qui auront une apparence familière pour les utilisateurs d’Apple TV.

Si vous regardez dans la section **structure du document** , vous remarquerez comment la disposition et la hiérarchie des éléments qui composent notre interface utilisateur sont affichées:

[![](hello-tvos-images/designer17.png "La section structure du document")](hello-tvos-images/designer17.png#lightbox)

À partir de là, vous pouvez sélectionner des éléments à modifier ou à faire glisser pour réorganiser les éléments d’interface utilisateur si nécessaire. Par exemple, si un élément d’interface utilisateur est couvert par un autre élément, vous pouvez le faire glisser vers le bas de la liste pour en faire l’élément le plus haut dans la fenêtre.

Maintenant que nous avons créé notre interface utilisateur, nous devons exposer les éléments de l’interface utilisateur de sorte que Xamarin. tvOS puisse y accéder et C# interagir avec eux dans le code.

### <a name="accessing-the-controls-in-the-code-behind"></a>Accès aux contrôles dans le code-behind

Il existe deux façons principales d’accéder aux contrôles que vous avez ajoutés dans le concepteur iOS à partir du code:

- Création d’un gestionnaire d’événements sur un contrôle.
- Donnez un nom au contrôle pour pouvoir le référencer ultérieurement.

Quand l’un de ces deux est ajouté, la classe partielle `ViewController.designer.cs` dans le sera mise à jour pour refléter les modifications. Cela vous permettra d’accéder ensuite aux contrôles dans le contrôleur d’affichage.

### <a name="creating-an-event-handler"></a>Création d’un gestionnaire d’événements

Dans cet exemple d’application, lorsque l’utilisateur clique sur le bouton, nous voulons qu’un événement se produise. un gestionnaire d’événements doit donc être ajouté à un événement spécifique sur le bouton. Pour configurer cette configuration, procédez comme suit:

1. Dans le concepteur iOS Xamarin, sélectionnez le bouton sur le contrôleur d’affichage.
2. Dans le panneau Propriétés, sélectionnez l’onglet **événements** :

    [![](hello-tvos-images/event1.png "Onglet événements")](hello-tvos-images/event1.png#lightbox)
3. Recherchez l’événement TouchUpInside et donnez-lui un gestionnaire d’événements `Clicked`nommé:

    [![](hello-tvos-images/event2.png "Événement TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Lorsque vous appuyez sur **entrée**, le fichier **ViewController**. cs s’ouvre, suggérant des emplacements pour votre gestionnaire d’événements dans le code. Utilisez les touches de direction de votre clavier pour définir l’emplacement:

    [![](hello-tvos-images/event3.png "Définition de l’emplacement")](hello-tvos-images/event3.png#lightbox)
5. Cette opération crée une méthode partielle comme indiqué ci-dessous:

    [![](hello-tvos-images/event4.png "Méthode partielle")](hello-tvos-images/event4.png#lightbox)

Nous sommes maintenant prêts à commencer à ajouter du code pour permettre au bouton de fonctionner.

### <a name="naming-a-control"></a>Nommer un contrôle

Lorsque l’utilisateur clique sur le bouton, l’étiquette doit être mise à jour en fonction du nombre de clics. Pour ce faire, nous devons accéder à l’étiquette dans le code. Pour ce faire, donnez-lui un nom. Effectuez ce qui suit :

1. Ouvrez le Storyboard, puis sélectionnez l’étiquette en bas du contrôleur d’affichage.
2. Dans le panneau Propriétés, sélectionnez l’onglet **widget** :

    [![](hello-tvos-images/name1.png "Sélectionnez l’onglet widget")](hello-tvos-images/name1.png#lightbox)
3. Sous **identité > nom**, ajoutez `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Définir ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Nous sommes maintenant prêts à commencer la mise à jour de l’étiquette.

### <a name="how-controls-are-accessed"></a>Mode d’accès aux contrôles

Si vous `ViewController.designer.cs` sélectionnez dans la **Explorateur de solutions** vous pouvez voir `ClickedLabel` comment l’étiquette et le `Clicked` gestionnaire d’événements ont été mappés à une **sortie** et à une **action** dans C#:

[![](hello-tvos-images/accesscontrol.png "Prises et actions")](hello-tvos-images/accesscontrol.png#lightbox)

Vous remarquerez peut `ViewController.designer.cs` -être également que est une classe partielle, de sorte que `ViewController.cs` Visual Studio pour Mac n’a pas à modifier, ce qui remplacerait les modifications que nous avons apportées à la classe.

L’exposition des éléments d’interface utilisateur de cette façon vous permet d’y accéder dans le contrôleur d’affichage.

Normalement, vous n’avez jamais besoin d' `ViewController.designer.cs` ouvrir le vous-même, il a été présenté ici à titre éducatif uniquement.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Écriture du code

Avec notre interface utilisateur créée et ses éléments d’interface utilisateur exposés au code via des **prises** et des **actions**, nous sommes enfin prêts à écrire le code pour fournir la fonctionnalité du programme.

Dans notre application, chaque fois que vous cliquez sur le premier bouton, nous allons mettre à jour notre étiquette pour afficher le nombre de clics effectués sur le bouton. Pour ce faire, vous devez ouvrir le `ViewController.cs` fichier pour le modifier en double-cliquant dessus dans la **panneau solutions**:

[![](hello-tvos-images/code01.png "Le panneau Solution")](hello-tvos-images/code01.png#lightbox)

Tout d’abord, nous devons créer une variable de niveau classe dans `ViewController` notre classe pour suivre le nombre de clics qui se sont produits. Modifiez la définition de classe pour qu’elle ressemble à ce qui suit :

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

Ensuite, dans la même classe (`ViewController`), nous devons remplacer la méthode **ViewDidLoad** et ajouter du code pour définir le message initial pour notre étiquette:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Nous devons `ViewDidLoad`utiliser, au lieu d’une autre méthode `Initialize`comme, car `ViewDidLoad` est appelée *après que* le système d’exploitation a chargé et instancié l’interface utilisateur `.storyboard` à partir du fichier. Si nous avons essayé d’accéder au contrôle Label avant `.storyboard` le chargement et l’instanciation complets du fichier, nous obtenons `NullReferenceException` une erreur, car le contrôle Label n’est pas encore créé.

Ensuite, nous devons ajouter le code pour répondre à l’utilisateur en cliquant sur le bouton. Ajoutez le code suivant à la classe partielle pour que nous ayons créé:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Ce code est appelé chaque fois que l’utilisateur clique sur notre bouton.

Tout en étant en place, nous sommes maintenant prêts à créer et à tester notre application Xamarin. tvOS.

## <a name="testing-the-application"></a>Test de l'application

Il est temps de créer et d’exécuter notre application pour vérifier qu’elle s’exécute comme prévu. Nous pouvons générer et exécuter tout en une seule étape, ou vous pouvez le créer sans l’exécuter.

Chaque fois que vous générez une application, nous pouvons choisir le type de build que nous souhaitons:

- **Débogage** – une version Debug est compilée dans un fichier' ' (application) avec des métadonnées supplémentaires qui nous permettent de déboguer ce qui se passe pendant l’exécution de l’application.
- **Release** : une version Release crée également un fichier' ', mais elle n’inclut pas d’informations de débogage. elle est donc plus petite et s’exécute plus rapidement.  

Vous pouvez sélectionner le type de build dans le **Sélecteur de configuration** , dans le coin supérieur gauche de l’écran Visual Studio pour Mac:

[![](hello-tvos-images/run01.png "Sélectionner le type de build")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Génération de l'application

Dans notre cas, nous voulons juste une version Debug, donc assurez-vous que **débogage** est sélectionné. Nous allons d’abord créer notre application en appuyant sur **⌘ B**, ou dans le menu **générer** , choisissez **générer tout**.

S’il n’y a pas d’erreurs, vous verrez un message **Build SUCCEEDED (génération réussie** ) dans la barre d’état de Visual Studio pour Mac. Si des erreurs se sont produites, passez en revue votre projet et assurez-vous que vous avez suivi les étapes correctement. Commencez par confirmer que votre code (à la fois dans Xcode et dans Visual Studio pour Mac) correspond au code du didacticiel.

### <a name="running-the-application"></a>Exécution de l'application

Pour exécuter l’application, trois options s’offrent à vous:

- Appuyez sur **⌘+Entrée**.
- Dans le menu **Exécuter**, choisissez **Déboguer**.
- Cliquez sur le bouton **Play** (Lire) dans la barre d’outils Visual Studio pour Mac (juste au-dessus de l’**Explorateur de solutions**).

L’application sera générée (si elle n’a pas déjà été générée), démarrer en mode débogage, le simulateur tvOS démarrera et l’application sera lancée et affichera sa fenêtre d’interface principale:

[![Écran d’accueil de l’exemple d’application](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Dans le menu **matériel** , sélectionnez **Afficher Apple TV à distance** pour pouvoir contrôler le simulateur.

[![](hello-tvos-images/run04.png "Sélectionnez Afficher Apple TV à distance")](hello-tvos-images/run04.png#lightbox)

À l’aide de l’accès distant du simulateur, si vous cliquez sur le bouton plusieurs fois, l’étiquette doit être mise à jour avec le nombre:

[![](hello-tvos-images/run05.png "Étiquette avec le nombre mis à jour")](hello-tvos-images/run05.png#lightbox)

Félicitations ! Nous avons abordé un grand nombre de sujets ici, mais si vous avez suivi ce didacticiel du début à la fin, vous devez maintenant avoir une compréhension approfondie des composants d’une application Xamarin. tvOS, ainsi que des outils utilisés pour les créer.

## <a name="where-to-next"></a>Où ensuite?

Le développement d’applications de téléviseurs Apple présente quelques défis en raison de la déconnexion entre l’utilisateur et l’interface (il se trouve dans la salle qui n’est pas dans la main de l’utilisateur) et les limitations tvOSent la taille et le stockage de l’application.

Par conséquent, nous vous suggérons vivement de lire les documents suivants avant de passer à la conception d’une application Xamarin. tvOS:

- [Présentation de tvOS 9](~/ios/tvos/platform/tvos9.md) : cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans tvOS 9 pour les développeurs Xamarin. tvOS.
- [Utilisation de la navigation et du focus](~/ios/tvos/app-fundamentals/navigation-focus.md) : les utilisateurs de votre application Xamarin. tvOS n’interagissent pas avec son interface directement comme avec iOS, où ils appuient sur des images sur l’écran de l’appareil, mais indirectement à travers la salle à l’aide de la télécommande Siri. Cet article traite du concept de focus et de la façon dont il est utilisé pour gérer la navigation dans une interface utilisateur de l’application Xamarin. tvOS.
- [Contrôleurs distants et Bluetooth Siri](~/ios/tvos/platform/remote-bluetooth.md) : la principale façon dont les utilisateurs interagissent avec Apple TV, et votre application Xamarin. tvOS, se fait via le Siri distant inclus. Si votre application est un jeu, vous pouvez éventuellement créer une prise en charge pour les contrôleurs de jeu Bluetooth tiers, effectués pour iOS (IFM) dans votre application. Cet article traite de la prise en charge des nouveaux contrôleurs de jeu distants Siri et Bluetooth dans vos applications Xamarin. tvOS.
- [Stockage des ressources et des données](~/ios/tvos/app-fundamentals/resources-data-storage.md) : contrairement aux appareils iOS, le nouveau Apple TV ne fournit pas de stockage local permanent pour les applications tvOS. Par conséquent, si votre application Xamarin. tvOS doit conserver des informations (telles que les préférences de l’utilisateur), elle doit stocker et récupérer ces données à partir de iCloud. Cet article traite de l’utilisation des ressources et du stockage de données persistant dans une application Xamarin. tvOS.
- [Utilisation d’icônes et d’images](~/ios/tvos/app-fundamentals/icons-images.md) : la création d’icônes et d’images captivistes est un élément essentiel du développement d’une expérience utilisateur immersive pour vos applications Apple TV. Ce guide aborde les étapes nécessaires à la création et à l’inclusion des ressources graphiques nécessaires à vos applications Xamarin. tvOS.
- [Interface utilisateur](~/ios/tvos/user-interface/index.md) : couverture de l’expérience utilisateur générale (UX) incluant les contrôles de l’interface utilisateur, utilisez les principes de conception de l’interface Builder et de l’expérience utilisateur de Xcode lors de l’utilisation de Xamarin. tvOS.
- [Déploiement et test](~/ios/tvos/deploy-test/index.md) : cette section décrit les rubriques utilisées pour tester une application et comment la distribuer. Les rubriques incluent des éléments tels que les outils utilisés pour le débogage, le déploiement vers les testeurs et la publication d’une application dans l’App Store d’Apple TV.

Si vous rencontrez des problèmes lors de l’utilisation de Xamarin. tvOS, consultez notre documentation de [Dépannage](~/ios/tvos/troubleshooting.md) pour obtenir une liste des problèmes et solutions connus.

## <a name="summary"></a>Récapitulatif

Cet article a fourni un démarrage rapide du développement d’applications pour tvOS avec Visual Studio pour Mac en créant une simple application Hello, tvOS. Il a abordé les principes de base de l’approvisionnement d’appareils tvOS, de la création d’interface, du codage pour tvOS et des tests sur le simulateur tvOS.


## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Création d’applications pour tvOS avec Xamarin (vidéo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
