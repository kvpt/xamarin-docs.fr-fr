---
title: Storyboards dans Xamarin. iOS
description: Ce document fournit une introduction aux storyboards dans Xamarin. iOS. Il décrit comment une table de montage séquentiel est utilisée pour définir une interface utilisateur, SEGUES et comment utiliser le concepteur iOS pour modifier des fichiers de Storyboard.
ms.prod: xamarin
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: e6bbc9d7d404136984e3d1ec93186dc85691c751
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439730"
---
# <a name="storyboards-in-xamarinios"></a>Storyboards dans Xamarin. iOS

Dans ce guide, nous allons expliquer ce qu’est une table de montage séquentiel et examiner certains composants clés, tels que SEGUES. Nous allons examiner comment créer et utiliser les storyboards, et quels avantages ils ont pour un développeur.

Avant que le format de fichier de Storyboard ait été introduit par Apple comme représentation visuelle de l’interface utilisateur d’une application iOS, les développeurs créaient des fichiers XIB pour chaque contrôleur d’affichage et ont programmé la navigation entre chaque vue manuellement.  L’utilisation d’un Storyboard permet au développeur de définir à la fois les contrôleurs d’affichage et la navigation entre eux sur une aire de conception et offre une modification WYSIWYG de l’interface utilisateur de l’application.

Une table de montage séquentiel peut être créée et ouverte avec Visual Studio pour Mac. Ce guide explique également comment utiliser Xcode Interface Builder pour créer vos storyboards tout en utilisant C# pour programmer la navigation.

## <a name="requirements"></a>Spécifications

Les storyboards peuvent être utilisés avec Xcode et lancés à partir d’un projet Xamarin. iOS sur Visual Studio pour Mac.

## <a name="what-is-a-storyboard"></a>Qu’est-ce qu’une table de montage séquentiel ?

Une table de montage séquentiel est la représentation visuelle de tous les écrans dans une application. Il contient une séquence de scènes, avec chaque scène représentant un *contrôleur d’affichage* et ses *vues*. Ces vues peuvent contenir des objets et des [contrôles](~/ios/user-interface/controls/index.md) qui permettent à votre utilisateur d’interagir avec votre application. Cette collection de vues et de contrôles (ou sous- *vues*) est appelée *hiérarchie d’affichage de contenu*. Les scènes sont connectées par des objets Segue, qui représentent une transition entre les contrôleurs d’affichage. Pour cela, il suffit généralement de créer un segue entre un objet sur la vue initiale et la vue de connexion. Les relations sur l’aire de conception sont illustrées dans l’image suivante :

 [![Les relations sur l’aire de conception sont illustrées dans cette image](images/storyboardsview.png)](images/storyboardsview.png#lightbox)

Comme indiqué, la table de montage séquentiel présente chacune de vos scènes avec du contenu déjà rendu et illustre les connexions entre elles. Lorsque nous parlerons des scènes sur un iPhone, il est possible de supposer qu’une *scène* sur le Storyboard est égale à un *écran* de contenu sur l’appareil. Toutefois, avec un iPad, il est possible d’afficher plusieurs scènes simultanément, par exemple à l’aide d’un contrôleur d’affichage menu segue.

L’utilisation des storyboards présente de nombreux avantages pour créer l’interface utilisateur de votre application, en particulier lors de l’utilisation de Xamarin. Tout d’abord, il s’agit d’une représentation visuelle de l’interface utilisateur, car tous les objets, y compris les [contrôles personnalisés](../designer/ios-designable-controls-overview.md) , sont rendus au moment de la conception.
Cela signifie qu’avant de générer ou de déployer votre application, vous pouvez visualiser son apparence et son Flow. Prenons l’image précédente, par exemple. Nous pouvons vous demander un aperçu rapide de l’aire de conception, le nombre de scènes, la disposition de chaque vue et la manière dont tout est lié. C’est ce qui rend les storyboards si puissants.

Vous pouvez également gérer les événements avec des storyboards.  La plupart des contrôles d’interface utilisateur ont une liste d’événements possibles dans le Panneau Propriétés. Le gestionnaire d’événements peut être ajouté ici et terminé dans une méthode partielle de la classe de contrôleurs d’affichage.

Le contenu d’une table de montage séquentiel est stocké sous la forme d’un fichier XML. Au moment de la génération, tous les `.storyboard` fichiers sont compilés dans des fichiers binaires appelés NIBS. Au moment de l’exécution, ces NIBS sont initialisés et instanciés pour créer des vues.

## <a name="segues"></a>SEGUES

Un objet *segue*, ou *segue*, est utilisé dans le développement iOS pour représenter une transition entre des scènes. Pour créer un Segue, maintenez la touche **CTRL** enfoncée et cliquez sur glisser d’une scène à une autre. Quand vous faites glisser la souris, un connecteur bleu s’affiche pour indiquer l’emplacement de l’segue. Cela est illustré dans l’image suivante :

 [![Un connecteur bleu s’affiche pour indiquer l’emplacement du segue dans cette image.](images/createsegue.png)](images/createsegue.png#lightbox)

Au niveau de la souris, un menu s’affiche pour vous permettre de choisir l’action pour le segue. Elle peut se présenter comme suit :

**Classes pre-iOS 8 et Size**:

[![Liste déroulante d’action segue sans classes de taille](images/segue1.png)](images/segue1.png#lightbox)

**Lorsque vous utilisez des classes de taille et des SEGUES adaptatifs**:

[![Liste déroulante des actions segue avec des classes de taille](images/16new.png)](images/16new.png#lightbox)

> [!IMPORTANT]
> Si vous utilisez VMWare pour votre machine virtuelle Windows, l’option Ctrl + clic est mappée en cliquant avec le bouton _droit sur_ le bouton de la souris par défaut. Pour créer un Segue, modifiez vos préférences de clavier à l’aide des **Préférences**  >  **clavier &**  >  **raccourcis souris** et remappez votre **bouton secondaire** , comme illustré ci-dessous :
>
> [![Paramètres de préférence du clavier et de la souris](images/image22.png)](images/image22.png#lightbox)
>
> Vous devez maintenant être en mesure d’ajouter un segue entre vos contrôleurs d’affichage comme d’habitude.

Il existe différents types de transitions, chacun donnant le contrôle de la présentation d’un nouveau contrôleur d’affichage à l’utilisateur et de la façon dont il interagit avec d’autres contrôleurs d’affichage dans le Storyboard. Celles-ci sont expliquées ci-dessous. Il est également possible de sous-créer un objet segue pour implémenter une transition personnalisée :

- **Show/Push** : un segue Push ajoute le contrôleur d’affichage à la pile de navigation. Il part du principe que le contrôleur d’affichage à l’origine du push fait partie du même contrôleur de navigation que le contrôleur d’affichage ajouté à la pile. Cela fait la même chose que  `pushViewController` , et est généralement utilisé lorsqu’il existe une relation entre les données sur les écrans. L’utilisation de l’segue Push vous donne le luxe de disposer d’une barre de navigation avec un bouton précédent et un titre ajoutés à chaque vue de la pile, ce qui permet une navigation dans la hiérarchie d’affichage.
- **Modal** : un segue modal crée une relation entre deux contrôleurs d’affichage de votre projet, avec la possibilité d’afficher une transition animée. Le contrôleur d’affichage enfant obscurcit complètement le contrôleur d’affichage parent lorsqu’il est mis en vue. Contrairement à un segue push, qui ajoute un bouton précédent pour nous, vous devez utiliser un `DismissViewController` lors de l’utilisation d’un segue modal pour revenir au contrôleur d’affichage précédent.
- **Personnalisé** : tout segue personnalisé peut être créé en tant que sous-classe de `UIStoryboardSegue` .
- **Déroulement** – un segue de déroulement peut être utilisé pour naviguer en arrière dans un segue de type push ou modal, par exemple, en rejetant le contrôleur d’affichage présenté de façon modale. En outre, vous pouvez dérouler non seulement une, mais également une série de SEGUES push et modaux, puis revenir à plusieurs étapes de votre hiérarchie de navigation avec une seule action de déroulement. Pour comprendre comment utiliser un segue de déroulement dans iOS, consultez la recette de création d’un  [déroulement SEGUES](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- Sans **source** : un segue sans source indique la scène contenant le contrôleur d’affichage initial et, par conséquent, la vue que l’utilisateur verra en premier. Elle est représentée par le segue illustré ici :

    [![Segue de code source](images/sourcelesssegue.png)](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Types segue adaptatifs

 iOS 8 a introduit des [classes de taille](../storyboards/unified-storyboards.md#size-classes) pour permettre à un fichier de Storyboard iOS de fonctionner avec toutes les tailles d’écran disponibles, ce qui permet aux développeurs de créer une interface utilisateur pour tous les appareils iOS. Par défaut, toutes les nouvelles applications Xamarin. iOS utilisent des classes de taille. Pour utiliser les classes de taille d’un ancien projet, reportez-vous au guide [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .

Toute application utilisant des classes de taille utilisera également la nouvelle [*SEGUES adaptative*](unified-storyboards.md). Lorsque vous utilisez des classes de taille, n’oubliez pas que vous ne spécifiez pas directement si vous utilisez un iPhone ou un iPad. En d’autres termes, vous créez une interface utilisateur qui aura toujours le même aspect, quelle que soit la quantité réelle d’espace de travail. Le travail SEGUES adaptatif consiste à évaluer l’environnement et à déterminer la meilleure façon de présenter le contenu. Les SEGUES adaptatifs sont affichés ci-dessous :

[![Liste déroulante SEGUES adaptatif](images/adaptivesegue.png)](images/adaptivesegue.png#lightbox)

|Segue|Description|
|--- |--- |
|Afficher|Cela est très similaire à un segue push, mais il prend en compte le contenu de l’écran.|
|Afficher les détails|Si l’application affiche un affichage maître et détail (par exemple, dans un contrôleur d’affichage fractionné sur un iPad), le contenu remplace l’affichage détaillé. Si l’application affiche uniquement le maître ou le détail, le contenu remplace le haut de la pile du contrôleur d’affichage.|
|Présentation|Cela est similaire à l’segue modal et permet de sélectionner des styles de présentation et de transition.|
|Présentation menu segue|Cela présente le contenu sous la forme d’un menu segue.|

### <a name="transferring-data-with-segues"></a>Transfert de données avec SEGUES

Les avantages d’un segue ne se terminent pas par des transitions. Elles peuvent également être utilisées pour gérer le transfert de données entre les contrôleurs d’affichage. Pour ce faire, vous devez substituer la `PrepareForSegue` méthode sur le contrôleur d’affichage initial et gérer les données vous-même. Lorsque le segue est déclenché, par exemple avec une pression sur un bouton, l’application appellera cette méthode, en fournissant la possibilité de préparer le nouveau contrôleur d’affichage *avant* toute navigation. Le code suivant de l’exemple [Phoneword](/samples/xamarin/ios-samples/hello-ios) illustre ce qui suit :

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue,
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

Dans cet exemple, la `PrepareForSegue` méthode est appelée lorsque le segue est déclenché par l’utilisateur. Vous devez d’abord créer une instance du contrôleur d’affichage « réception » et la définir en tant que contrôleur d’affichage de destination de Segue. Cette opération est effectuée par la ligne de code ci-dessous :

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

La méthode a désormais la possibilité de définir des propriétés sur le `DestinationViewController` . Cet exemple tire parti de cette capacité en passant une liste appelée `PhoneNumbers` à et en l' `CallHistoryController` affectant à un objet du même nom :

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Une fois la transition terminée, l’utilisateur verra le `CallHistoryController` avec la liste remplie.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Ajout d’une table de montage séquentiel à un projet non-Storyboard

Parfois, vous devrez peut-être ajouter une table de montage séquentiel à un fichier qui n’est pas un Storyboard. Vous pouvez simplifier le processus dans Visual Studio pour Mac en procédant comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Créez un fichier de Storyboard en accédant à **fichier > nouveau fichier > iOS > Storyboard**.

    [![Boîte de dialogue nouveau fichier](images/new-storyboard-xs.png)](images/new-storyboard-xs.png#lightbox)

2. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** du fichier **info. plist**.

    [![Éditeur info. plist](images/infoplist.png)](images/infoplist.png#lightbox)

    Cela équivaut à instancier le contrôleur d’affichage initial dans la `FinishedLaunching` méthode au sein du délégué d’application. Lorsque cette option est définie, l’application instancie une fenêtre (Voir l’étape suivante), charge la table de montage séquentiel principale et assigne une instance du contrôleur d’affichage initial de la table de montage séquentiel (celle à côté de la segue sans source) en tant que `RootViewController` propriété de la fenêtre. La fenêtre est ensuite visible à l’écran.

3. Dans `AppDelegate` , remplacez la méthode par défaut par `Window` le code suivant pour implémenter la propriété de fenêtre :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Pour créer un fichier de Storyboard, cliquez avec le bouton droit sur le projet pour **ajouter > nouveau fichier > iOS > Storyboard vide**.

    [![Boîte de dialogue Nouvel élément](images/new-storyboard-vs.png)](images/new-storyboard-vs.png#lightbox)

2. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** de l’application iOS.

    [![Éditeur info. plist](images/ios-app.png)](images/ios-app.png#lightbox)

    Cela équivaut à instancier le contrôleur d’affichage initial dans la `FinishedLaunching` méthode au sein du délégué d’application. Lorsque cette option est définie, l’application instancie une fenêtre (Voir l’étape suivante), charge la table de montage séquentiel principale et assigne une instance du contrôleur d’affichage initial de la table de montage séquentiel (celle à côté de la segue sans source) en tant que `RootViewController` propriété de la fenêtre. La fenêtre est ensuite visible à l’écran.

3. Dans `AppDelegate` , remplacez la méthode par défaut par `Window` le code suivant pour implémenter la propriété de fenêtre :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>Création d’une table de montage séquentiel avec Xcode

Une table de montage séquentiel peut être créée et modifiée à l’aide de Xcode pour une utilisation dans vos applications iOS développées avec Visual Studio pour Mac.

Les storyboards remplacent entièrement des fichiers XIB individuels dans votre projet. Toutefois, les contrôleurs d’affichage individuels d’une table de montage séquentiel peuvent toujours être instanciés à l’aide de `Storyboard.InstantiateViewController` .

Parfois, les applications ont des exigences spéciales qui ne peuvent pas être gérées avec les transitions de Storyboard intégrées fournies par le concepteur. Par exemple, si vous deviez créer une application qui lance différents écrans à partir du même bouton, en fonction de l’état actuel d’une application, vous souhaiterez peut-être instancier les contrôleurs d’affichage manuellement et programmer la transition vous-même.

La capture d’écran suivante montre deux contrôleurs d’affichage sur l’aire de conception sans segue. La section suivante explique comment configurer cette transition dans le code.

1. Ajoutez un _Storyboard iPhone vide_ à un projet de projet existant :

    [![Ajout d’un Storyboard](images/add-storyboard2.png)](images/add-storyboard2.png#lightbox)

2. Cliquez avec le bouton droit sur le fichier de table de montage séquentiel et sélectionnez **Ouvrir avec > Xcode Interface Builder** pour l’ouvrir dans Xcode.

   > ACCÉLÉRATRICE Si vous préférez utiliser Xcode Interface Builder par défaut, vous pouvez le choisir dans les préférences de Visual Studio pour Mac sous **projets > iOS**.  À compter de Visual Studio version 16,9 et Visual Studio pour Mac 8,9, Xcode Interface Builder sera la seule option. *

   ![Sélection de l’outil concepteur préféré.](images/set-preferred-designer-tool.png)

3. Dans Xcode, ouvrez la bibliothèque (via **affichage > afficher la bibliothèque** ou **Maj + Commande + L**) pour afficher une liste d’objets qui peuvent être ajoutés à la table de montage séquentiel. Ajoutez un `Navigation Controller` à la table de montage séquentiel en faisant glisser l’objet de la liste sur le Storyboard. Par défaut, le `Navigation Controller` propose deux écrans. L’écran à droite est un que `TableViewController` vous allez remplacer par un affichage plus simple afin de pouvoir le supprimer en cliquant sur la vue et en appuyant sur la touche Suppr.

    [![Ajout d’un NavigationController à partir de la bibliothèque](images/add-navigation-controller.png)](images/add-navigation-controller.png#lightbox)

4. Ce contrôleur d’affichage possède sa propre classe personnalisée, et il a également besoin de son propre ID de Storyboard. Lorsque vous cliquez sur la zone située au-dessus de cette vue nouvellement ajoutée, il y a trois icônes, le plus à gauche qui représente le contrôleur d’affichage de la vue. En sélectionnant cette icône, vous pouvez définir les valeurs de la classe et de l’ID dans l’onglet identité du volet droit. Définissez ces valeurs sur `MainViewController` et assurez-vous qu’elles sont vérifiées `Use Storyboard ID` .

    [![Définition de MainViewController dans le panneau d’identité](images/identity-panel.png)](images/identity-panel.png#lightbox)

5. À l’aide de la bibliothèque, faites glisser un contrôle View Controller sur l’écran. Ce sera défini en tant que contrôleur d’affichage racine. En maintenant la touche **CTRL** enfoncée, cliquez et faites glisser le contrôleur de navigation de gauche vers le contrôleur d’affichage que vous venez d’ajouter à droite, puis sélectionnez **contrôleur d’affichage racine** dans le menu.

    [![Ajout d’un NavigationController à partir de la bibliothèque et définition du MainViewController en tant que contrôleur d’affichage racine](images/add-view-controller.png)](images/add-view-controller.png#lightbox)

6. Cette application accède à une autre vue. vous devez donc ajouter une vue supplémentaire au Storyboard, comme auparavant. Appelez-le `PinkViewController` et définissez ces valeurs de la même manière qu’avec le `MainViewController` .

    [![Ajout d’un contrôleur d’affichage supplémentaire](images/add-additional-view-controller.png)](images/add-additional-view-controller.png#lightbox)

7. Étant donné que le contrôleur d’affichage aura un arrière-plan rose, définissez cette propriété dans le panneau attributs à l’aide de la liste déroulante en regard de `Background` .

    [![Ajout d’un contrôleur d’affichage supplémentaire](images/set-pink-background.png)](images/set-pink-background.png#lightbox)

8. Étant donné que nous voulons que le `MainViewController` accède au `PinkViewController` , le premier doit avoir un bouton pour interagir avec. Utilisez la bibliothèque pour ajouter un bouton à `MainViewController` .

    [![Ajout d’un bouton à MainViewController](images/add-button.png)](images/add-button.png#lightbox)

La table de montage séquentiel est terminée, mais si vous déployez le projet maintenant, vous obtiendrez un écran vide. C’est parce que vous devez toujours demander à l’IDE d’utiliser la table de montage séquentiel et de configurer un contrôleur d’affichage racine pour servir de première vue. Normalement, cette opération peut être effectuée par le biais des options du projet, comme indiqué précédemment. Toutefois, dans cet exemple, nous allons ajouter le code suivant au **AppDelegate** pour obtenir le même résultat :

```csharp
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
    public static UIViewController initialViewController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

        window.RootViewController = initialViewController;
        window.AddSubview(initialViewController.View);
        window.MakeKeyAndVisible ();
        return true;
    }
}
```

C’est beaucoup de code, mais seules quelques lignes ne vous sont pas familières. Tout d’abord, vous enregistrez la table de montage séquentiel avec **AppDelegate** en transmettant le nom de la table de montage séquentiel, **fichier mainstoryboard**. Ensuite, vous indiquez à l’application d’instancier un contrôleur d’affichage initial à partir du storyboard en appelant `InstantiateInitialViewController` sur le Storyboard, et vous définissez ce contrôleur d’affichage en tant que contrôleur d’affichage racine de l’application. Cette méthode détermine le premier écran que l’utilisateur voit et crée une nouvelle instance de ce contrôleur d’affichage.

Notez dans le volet solution que l’IDE a créé une `MainViewcontroller.cs` classe et son `*.designer.cs` fichier correspondant quand vous avez ajouté le nom de la classe au panneau Propriétés à l’étape 4. Cette classe a créé un constructeur spécial qui comprend une classe de base :

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

Quand vous créez une table de montage séquentiel à l’aide de Xcode, l’IDE ajoute automatiquement l’attribut [[register]](xref:Foundation.RegisterAttribute) en haut de la `*.designer.cs` classe et transmet un identificateur de chaîne, qui est identique à l’ID de Storyboard spécifié à l’étape précédente. Cela permet de lier le C# à la scène appropriée dans le Storyboard.

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
    public MainViewController (IntPtr handle) : base (handle)
    {
    }
    //...
}
```

Pour plus d’informations sur l’inscription des classes et des méthodes, consultez [type Registrar](../../internals/registrar.md).

La dernière étape de cette classe consiste à relier le bouton et la transition au contrôleur d’affichage rose. Vous allez instancier le `PinkViewController` à partir de la table de montage séquentiel. ensuite, vous allez programmer un push segue avec `PushViewController` , comme illustré dans l’exemple de code suivant :

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        this.Initialize ();
    }

    public void Initialize()
    {
        //Instantiating View Controller with Storyboard ID 'PinkViewController'
        pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //When we push the button, we will push the pinkViewController onto our current Navigation Stack
        PinkButton.TouchUpInside += (o, e) =&gt;
        {
            this.NavigationController.PushViewController (pinkViewController, true);
        };
    }
}
```

L’exécution de l’application génère une application à 2 écrans :

![Exemples d’écrans d’exécution d’application](images/finishedstoryboard.png)

## <a name="conditional-segues"></a>SEGUES conditionnel

Souvent, le passage d’un contrôleur d’affichage à l’autre dépend d’une certaine condition. Par exemple, si nous avions un écran de connexion simple, nous voulons simplement passer à l’écran suivant *si* le nom d’utilisateur et le mot de passe ont été vérifiés.

Dans l’exemple suivant, nous allons ajouter un champ de mot de passe à l’exemple précédent. L’utilisateur pourra uniquement accéder au *PinkViewController* s’il entre le mot de passe correct. sinon, une erreur s’affichera.

Avant de commencer, suivez les étapes précédentes 1 à 8. Dans ces étapes, nous créons notre Storyboard, nous commençons à créer notre interface utilisateur et indiquons à notre application le contrôleur d’affichage à utiliser comme RootViewController.

1. Nous allons maintenant développer notre interface utilisateur et ajouter les vues supplémentaires listées à la `MainViewController` pour qu’elle ressemble à ceci dans la capture d’écran suivante :

    - UITextField
        - Nom : PasswordTextField
        - Espace réservé : « entrez le mot de passe secret »
    - UILabel
        - Texte : «erreur : mot de passe incorrect. Vous ne devez pas passer ! '
        - Couleur : rouge
        - Alignement : Centre
        - Lignes : 2
        - Case à cocher « Masqué » activée    

    [![Lignes de centre](images/passwordvc.png)](images/passwordvc.png#lightbox)

2. Créez un segue entre le bouton atteindre le rose et le contrôleur d’affichage en faisant glisser le curseur du *PinkButton* vers le *PinkViewController*, puis en sélectionnant **pousser** au niveau de la souris.

3. Cliquez sur segue et donnez-lui l' *identificateur* `SegueToPink` :

    [![Cliquez sur segue et donnez-lui l’identificateur SegueToPink](images/namesegue.png)](images/namesegue.png#lightbox)  

4. Enfin, ajoutez la `ShouldPerformSegue` méthode suivante à la `MainViewController` classe :

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {

        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

Dans ce code, nous avons mis en correspondance le segueIdentifier à notre `SegueToPink` segue. nous pouvons donc tester une condition. dans ce cas, il s’agit d’un mot de passe valide. Si notre condition retourne `true` , le segue exécute et présente le `PinkViewController` . Si `false` la condition est, le nouveau contrôleur d’affichage n’est pas présenté.

Nous pouvons appliquer cette approche à n’importe quel segue sur ce contrôleur d’affichage en vérifiant l’argument segueIdentifier pour la méthode ShouldPerformSegue. Dans ce cas, nous n’avons qu’un seul identificateur segue : `SegueToPink` .

Reportez-vous à la solution storyboards. Conditional dans l' [exemple de storyboards manuels](/samples/xamarin/ios-samples/manualstoryboard) pour obtenir un exemple fonctionnel.

## <a name="summary"></a>Résumé

Cet article présente le concept de storyboards et la façon dont ils peuvent être bénéfiques au développement d’applications iOS. Il aborde les scènes, les contrôleurs d’affichage, les vues et les hiérarchies de vues, ainsi que la façon dont les scènes sont liées avec différents types de SEGUES.  Il explore également l’instanciation manuelle des contrôleurs d’affichage à partir d’une table de montage séquentiel et la création de SEGUES conditionnelles.

## <a name="related-links"></a>Liens associés

- [Storyboard manuel (exemple)](/samples/xamarin/ios-samples/manualstoryboard/)
- [Présentation du concepteur iOS](../designer/introduction.md)
- [Convertir en storyboards](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Référence de la classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
