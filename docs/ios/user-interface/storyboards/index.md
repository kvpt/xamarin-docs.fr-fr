---
title: Présentation des storyboards dans Xamarin. iOS
description: Ce document fournit une introduction aux storyboards dans Xamarin. iOS. Il décrit comment une table de montage séquentiel est utilisée pour définir une interface utilisateur, SEGUES et comment utiliser le concepteur iOS pour modifier des fichiers de Storyboard.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: cf181cf6c27476b7073073467ef186c352645e39
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70768877"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Présentation des storyboards dans Xamarin. iOS

Dans ce guide, nous allons expliquer ce qu’est une table de montage séquentiel et examiner certains composants clés, tels que SEGUES. Nous allons examiner comment créer et utiliser les storyboards, et quels avantages ils ont pour un développeur.

Avant que le format de fichier de Storyboard ait été introduit par Apple comme représentation visuelle de l’interface utilisateur d’une application iOS, les développeurs créaient des fichiers XIB pour chaque contrôleur d’affichage et ont programmé la navigation entre chaque vue manuellement.  L’utilisation d’un Storyboard permet au développeur de définir à la fois les contrôleurs d’affichage et la navigation entre eux sur une aire de conception et offre une modification WYSIWYG de l’interface utilisateur de l’application.

Une table de montage séquentiel peut être créée, ouverte et modifiée à l’aide du concepteur iOS Xamarin. Ce guide explique également comment utiliser le concepteur pour créer vos storyboards lors de l’utilisation C# de pour programmer la navigation.

## <a name="requirements"></a>spécifications

Les storyboards peuvent être utilisés avec le concepteur iOS dans Visual Studio pour Mac ou avec Visual Studio 2017 avec les charges de travail Xamarin installées.

## <a name="what-is-a-storyboard"></a>Qu’est-ce qu’une table de montage séquentiel ?

Une table de montage séquentiel est la représentation visuelle de tous les écrans dans une application. Il contient une séquence de scènes, avec chaque scène représentant un *contrôleur d’affichage* et ses *vues*. Ces vues peuvent contenir des objets et des [contrôles](~/ios/user-interface/controls/index.md) qui permettront à votre utilisateur d’interagir avec votre application. Cette collection de vues et de contrôles (ou sous- *vues*) est appelée *hiérarchie d’affichage de contenu*. Les scènes sont connectées par des objets Segue, qui représentent une transition entre les contrôleurs d’affichage. Pour cela, il suffit généralement de créer un segue entre un objet sur la vue initiale et la vue de connexion. Les relations sur l’aire de conception sont illustrées dans l’image ci-dessous :

 [![](images/storyboardsview.png "The relationships on the design surface are illustrated in this image")](images/storyboardsview.png#lightbox)

Comme indiqué, la table de montage séquentiel met en page chacune de vos scènes avec un contenu déjà rendu et illustre les connexions entre elles.  À ce stade, il est intéressant de noter que lorsque nous parlerons des scènes sur un iPhone, il est possible de supposer qu’une *scène* sur le Storyboard est égale à un *écran* de contenu sur l’appareil. Toutefois, avec un iPad, il est possible d’afficher plusieurs scènes en même temps, par exemple, à l’aide d’un contrôleur d’affichage menu segue.

L’utilisation des storyboards présente de nombreux avantages pour créer l’interface utilisateur de votre application, en particulier lors de l’utilisation de Xamarin. Tout d’abord, il s’agit d’une représentation visuelle de l’interface utilisateur, car tous les objets, y compris les [contrôles personnalisés](~/ios/user-interface/designer/ios-designable-controls-overview.md) , sont rendus au moment de la conception. Cela signifie qu’avant de générer ou de déployer votre application, vous pouvez visualiser son apparence et son Flow. Prenons l’image ci-dessus, par exemple. Nous pouvons vous demander un aperçu rapide de l’aire de conception, le nombre de scènes, la disposition de chaque vue et la manière dont tout est lié. C’est ce qui rend les storyboards si puissants.

Les événements sont plus gérables avec les storyboards, en particulier lors de l’utilisation du concepteur iOS. La plupart des contrôles d’interface utilisateur comporteront une liste des événements possibles dans le Panneau Propriétés. Le gestionnaire d’événements peut être ajouté ici et terminé dans une méthode partielle de la classe de contrôleurs d’affichage.

Le contenu d’une table de montage séquentiel est stocké sous la forme d’un fichier XML. Au moment de la génération, tous les fichiers de `.storyboard` sont compilés dans des fichiers binaires appelés NIBS. Lors de l’exécution, ces NIBS sont initialisés et instanciés pour créer des vues.

## <a name="segues"></a>SEGUES

Un objet *segue*, ou *segue*, est utilisé dans le développement iOS pour représenter une transition entre des scènes. Pour créer un Segue, maintenez la touche **CTRL** enfoncée et cliquez sur glisser d’une scène à une autre. Au fur et à mesure que nous faisons glisser notre souris, un connecteur bleu s’affiche pour indiquer l’emplacement du segue dans l’image ci-dessous :

 [![](images/createsegue.png "A blue connector appears, indicating where the segue will lead as demonstrated in this image")](images/createsegue.png#lightbox)

Au niveau de la souris, un menu s’affiche pour vous permettre de choisir l’action pour notre segue. Il peut ressembler aux images ci-dessous : 

**Classes pre-iOS 8 et Size**:

[![](images/segue1.png "The Action Segue dropdown without Size Classes")](images/segue1.png#lightbox)

**Lorsque vous utilisez des classes de taille et des SEGUES adaptatifs**:

[![](images/16new.png "The Action Segue dropdown with Size Classes")](images/16new.png#lightbox)

> [!IMPORTANT]
> Si vous utilisez VMWare pour votre machine virtuelle Windows, l’option Ctrl + clic est mappée en cliquant avec le bouton _droit sur_ le bouton de la souris par défaut. Pour créer un Segue, modifiez vos préférences de clavier par le biais des **préférences**  > **clavier & souris**  > **raccourcis** souris et remappez votre **bouton secondaire** , comme illustré ci-dessous :
> 
> [![](images/image22.png "Keyboard and Mouse preference settings")](images/image22.png#lightbox)
> 
> Vous devez maintenant être en mesure d’ajouter un segue entre vos contrôleurs d’affichage comme d’habitude.

Il existe différents types de transitions, chacun donnant le contrôle de la présentation d’un nouveau contrôleur d’affichage à l’utilisateur et de la façon dont il interagit avec d’autres contrôleurs d’affichage dans le Storyboard. Celles-ci sont expliquées ci-dessous. Il est également possible de sous-créer un objet segue pour implémenter une transition personnalisée :

- **Show/Push** : un segue Push ajoute le contrôleur d’affichage à la pile de navigation. Il part du principe que le contrôleur d’affichage à l’origine du push fait partie du même contrôleur de navigation que le contrôleur d’affichage ajouté à la pile. Cela fait la même chose que `pushViewController`, et est généralement utilisé lorsqu’il existe une relation entre les données sur les écrans. L’utilisation de l’segue Push vous donne le luxe de disposer d’une barre de navigation avec un bouton précédent et un titre ajoutés à chaque vue de la pile, ce qui permet une navigation dans la hiérarchie d’affichage.
- **Modal** : un segue modal crée une relation entre deux contrôleurs d’affichage de votre projet, avec l’option d’une transition animée affichée. Le contrôleur d’affichage enfant obscurcit complètement le contrôleur d’affichage parent lorsqu’il est mis en vue. Contrairement à un segue push, qui ajoute un bouton précédent pour nous ; lors de l’utilisation d’un segue modal, `DismissViewController` doit être utilisé pour revenir au contrôleur d’affichage précédent.
- **Personnalisé** : tout segue personnalisé peut être créé en tant que sous-classe de `UIStoryboardSegue`.
- **Déroulement** – un segue de déroulement peut être utilisé pour naviguer en arrière dans un segue de type push ou modal, par exemple, en rejetant le contrôleur d’affichage présenté de façon modale. En outre, vous pouvez dérouler non seulement une, mais également une série de SEGUES push et modaux, puis revenir à plusieurs étapes de votre hiérarchie de navigation avec une seule action de déroulement. Pour comprendre comment utiliser un segue de déroulement dans iOS, consultez la recette de création d’un [déroulement SEGUES](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- Sans **source** : un segue sans source indique la scène contenant le contrôleur d’affichage initial et, par conséquent, la vue que l’utilisateur verra en premier. Elle est représentée par le segue illustré ci-dessous :  

    [![](images/sourcelesssegue.png "A sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Types segue adaptatifs

 iOS 8 a introduit des [classes de taille](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) pour permettre à un fichier de Storyboard iOS de fonctionner avec toutes les tailles d’écran disponibles, ce qui permet aux développeurs de créer une interface utilisateur pour tous les appareils iOS. Par défaut, toutes les nouvelles applications Xamarin. iOS utilisent des classes de taille. Pour utiliser les classes de taille d’un ancien projet, reportez-vous au guide [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) . 

Toute application utilisant des classes de taille utilisera également la nouvelle [*SEGUES adaptative*](~/ios/user-interface/storyboards/unified-storyboards.md). Lorsque vous utilisez des classes de taille, n’oubliez pas que nous ne spécifions pas directement si nous utilisons un iPhone ou un iPad. En d’autres termes, nous créons une interface utilisateur qui aura toujours le même aspect, quelle que soit la quantité réelle d’espace de travail. Les SEGUES adaptatifs fonctionnent en déterminant l’environnement et en déterminant la meilleure façon de présenter le contenu. Les SEGUES adaptatifs sont affichés ci-dessous : 

[![](images/adaptivesegue.png "The Adaptive Segues dropdown")](images/adaptivesegue.png#lightbox)

|Segue|Description|
|--- |--- |
|Afficher|Cela est très similaire à un segue push, mais il prend en compte le contenu de l’écran.|
|Afficher les détails|Si l’application affiche un affichage maître et détail (par exemple, dans un contrôleur d’affichage fractionné sur un iPad), le contenu remplace l’affichage détaillé. Si l’application affiche uniquement le maître ou le détail, le contenu remplace le haut de la pile du contrôleur d’affichage.|
|La présentation|Cela est similaire à l’segue modal et permet de sélectionner des styles de présentation et de transition.|
|Présentation menu segue|Cela présente le contenu sous la forme d’un menu segue|

### <a name="transferring-data-with-segues"></a>Transfert de données avec SEGUES

Les avantages d’un segue ne se terminent pas par des transitions. Elles peuvent également être utilisées pour gérer le transfert de données entre les contrôleurs d’affichage. Cela est possible en remplaçant la méthode `PrepareForSegue` sur le contrôleur d’affichage initial et en gérant les données. Lorsque le segue est déclenché, par exemple avec une pression sur un bouton, l’application appellera cette méthode, en fournissant la possibilité de préparer le nouveau contrôleur d’affichage *avant* toute navigation. Le code ci-dessous, extrait de l’exemple [Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) , illustre ce qui suit : 

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

Dans cet exemple, la méthode `PrepareForSegue` est appelée lorsque le segue est déclenché par l’utilisateur. Nous devons d’abord créer une instance du contrôleur d’affichage « réception » et la définir en tant que contrôleur d’affichage de destination de Segue. Cette opération est effectuée par la ligne de code ci-dessous :

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

La méthode a désormais la possibilité de définir des propriétés sur le `DestinationViewController`. Dans cet exemple, nous avons tiré parti de cette méthode en transmettant une liste appelée `PhoneNumbers` à l' `CallHistoryController` et en l’affectant à un objet du même nom :

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Une fois la transition terminée, l’utilisateur verra le `CallHistoryController` avec la liste remplie.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Ajout d’une table de montage séquentiel à un projet non-Storyboard

Il se peut que vous deviez ajouter une table de montage séquentiel à un fichier qui n’est pas un Storyboard. Une fois cette opération effectuée dans Visual Studio pour Mac peut être rationalisée en suivant les étapes ci-dessous :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Créez un fichier de table de montage séquentiel en accédant à **fichier > nouveau fichier > iOS > Storyboard**, comme illustré ci-dessous : 
    
    [![](images/new-storyboard-xs.png "The new file dialog")](images/new-storyboard-xs.png#lightbox)

2. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** du fichier **info. plist**, comme indiqué ci-dessous :
    
    [![](images/infoplist.png "The Info.plist editor")](images/infoplist.png#lightbox)
    
    Cela équivaut à instancier le contrôleur d’affichage initial dans la méthode `FinishedLaunching` au sein du délégué d’application. Lorsque cette option est définie, l’application instancie une fenêtre (voir ci-dessous), charge le Storyboard principal et assigne une instance du contrôleur d’affichage initial de la table de montage séquentiel (celle à côté de la segue sans source) en tant que propriété `RootViewController` de la fenêtre, puis rend le fenêtre visible à l’écran.

3. Dans le `AppDelegate`, remplacez la méthode `Window` par défaut, par le code suivant pour implémenter la propriété de fenêtre :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Créez un fichier de table de montage séquentiel en cliquant avec le bouton droit sur le projet pour **ajouter > nouveau fichier > iOS > Storyboard vide**, comme illustré ci-dessous : 
    
    [![](images/new-storyboard-vs.png "The new item dialog")](images/new-storyboard-vs.png#lightbox)

2. Ajoutez le nom de votre Storyboard à la section de l' **interface principale** de l’application iOS, comme indiqué ci-dessous :
    
    [![](images/ios-app.png "The Info.plist editor")](images/ios-app.png#lightbox)
    
    Cela équivaut à instancier le contrôleur d’affichage initial dans la méthode `FinishedLaunching` au sein du délégué d’application. Lorsque cette option est définie, l’application instancie une fenêtre (voir ci-dessous), charge le Storyboard principal et assigne une instance du contrôleur d’affichage initial de la table de montage séquentiel (celle à côté de la segue sans source) en tant que propriété `RootViewController` de la fenêtre, puis rend le fenêtre visible à l’écran.

3. Dans le `AppDelegate`, remplacez la méthode `Window` par défaut, par le code suivant pour implémenter la propriété de fenêtre :

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Création d’une table de montage séquentiel avec le concepteur iOS

Une table de montage séquentiel peut être créée à l’aide de la Xamarin Designer pour iOS, qui a été intégrée en toute transparence avec Visual Studio pour Mac et Visual Studio.

Pour commencer à utiliser le concepteur iOS pour créer des storyboards, suivez le guide [Hello, iOS multi-écran](~/ios/get-started/hello-ios-multiscreen/index.md) . Dans cette procédure pas à pas, vous allez explorer la navigation entre les contrôleurs d’affichage à l’aide de SEGUES et comment gérer des événements sur vos contrôles.

## <a name="instantiate-storyboards-manually"></a>Instancier manuellement des storyboards

Les storyboards remplacent entièrement les fichiers XIB individuels dans votre projet. Toutefois, les contrôleurs d’affichage individuels d’une table de montage séquentiel peuvent toujours être instanciés à l’aide de `Storyboard.InstantiateViewController`.

Parfois, les applications ont des exigences spéciales qui ne peuvent pas être gérées avec les transitions de Storyboard intégrées fournies par le concepteur. Par exemple, si nous devions créer une application qui lance différents écrans à partir du même bouton, en fonction de l’état actuel d’une application, nous pouvons éventuellement instancier les contrôleurs d’affichage manuellement et programmer la transition.

La capture d’écran ci-dessous montre deux contrôleurs d’affichage sur notre aire de conception, sans segue. La section suivante vous guidera tout au long de la configuration de cette transition dans le code.

 [![](images/viewcontrollerspink.png "This screenshot shows two view controllers on the design surface with no segue between them")](images/viewcontrollerspink.png#lightbox)

1. Ajoutez un _Storyboard iPhone vide_ à un projet de projet existant :
    
    [![](images/add-storyboard1.png "Adding storyboard")](images/add-storyboard1.png#lightbox)

2. Double-cliquez sur le Storyboard que vous venez de créer pour l’ouvrir, puis ajoutez un nouveau **contrôleur de navigation** à l’aire de conception. Comme le contrôleur de navigation est sans interface utilisateur, par défaut, il est fourni avec un contrôleur d’affichage racine, comme illustré ci-dessous :

    [![](images/uinavigationcontroller.png "View Controllers with Segues")](images/uinavigationcontroller.png#lightbox)

3. Sélectionnez le _contrôleur d’affichage_ en cliquant sur la barre noire en bas. Dans le panneau des **Propriétés**du concepteur, sous **identité** , nous pouvons spécifier une classe personnalisée, ainsi qu’un ID unique pour le contrôleur d’affichage. Définissez le **nom** de la classe et l' **ID du storyboard** sur `MainViewController`.

    [![](images/identitypanelnew.png "Specify custom class")](images/identitypanelnew.png#lightbox)

4. Plus tard, nous aurons besoin d’instancier nos contrôleurs d’affichage à partir de la table de montage séquentiel et utiliseront l’ID de Storyboard pour les référencer dans notre code. La définition de l’ID de restauration pour qu’il corresponde à l’ID de Storyboard garantit que le contrôleur d’affichage est recréé correctement si l’État doit être restauré.

5. Nous n’avons actuellement qu’un seul contrôleur d’affichage. Faites glisser un autre contrôleur d’affichage sur l’aire de conception. Dans le panneau des **Propriétés**, sous identité, définissez la classe et l’ID de Storyboard sur `PinkViewController`, comme illustré ci-dessous :

    [![](images/pinkvcnew.png "The Property Pad")](images/pinkvcnew.png#lightbox)
    
    L’IDE crée ces classes personnalisées pour les contrôleurs d’affichage. Vous pouvez les consulter dans le **panneau solutions**, comme illustré dans la capture d’écran ci-dessous :
    
    [![](images/solution-pad.png "Solution Pad")](images/solution-pad.png#lightbox)

6. Dans le `PinkViewController`, sélectionnez la vue en cliquant vers le centre du frame du contrôleur. Dans le Panneau Propriétés, sous affichage, changez l' **arrière-plan** en magenta :
    
    [![](images/pinkcontroller.png "Set Background color")](images/pinkcontroller.png#lightbox)

7. Enfin, faites glisser un bouton de la **boîte à outils** vers le `MainViewController`. Dans le Panneau Propriétés, attribuez-lui le nom `PinkButton` et le titre GoToPink, comme illustré ci-dessous :

    [![](images/pinkbutton.png "Set Button Name")](images/pinkbutton.png#lightbox)

La table de montage séquentiel est terminée, mais si nous déployons le projet maintenant, vous obtiendrez un écran vide. C’est parce que nous devons toujours dire à l’IDE d’utiliser notre Storyboard et à configurer un contrôleur d’affichage racine qui servira de première vue. Normalement, vous pouvez le faire via nos options de projet, comme indiqué ci-dessus. Toutefois, dans cet exemple, nous allons obtenir le même résultat dans le code, en ajoutant le code suivant à **AppDelegate**:

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
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

C’est beaucoup de code, mais seules quelques lignes ne vous sont pas familières. Tout d’abord, nous enregistrons notre Storyboard avec **AppDelegate** en transmettant le nom de la table de montage séquentiel, **fichier mainstoryboard**. Ensuite, nous indiquons à l’application d’instancier un contrôleur d’affichage initial à partir du storyboard en appelant `InstantiateInitialViewController` sur notre Storyboard, et nous définissons ce contrôleur d’affichage comme contrôleur d’affichage racine de l’application. Cette méthode détermine le premier écran que l’utilisateur voit, et crée une nouvelle instance de ce contrôleur d’affichage.

Notez que dans le volet solution, l’IDE a créé une classe `MainViewcontroller.cs`, et son `corresponding designer.cs` quand nous avons ajouté le nom de la classe au Panneau Propriétés à l’étape 4. Nous pouvons voir que cette classe a créé un constructeur spécial qui comprend une classe de base :

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```

Lors de la création d’une table de montage séquentiel à l’aide du concepteur, l’IDE ajoute automatiquement l’attribut [[register]](xref:Foundation.RegisterAttribute) en haut de la classe `designer.cs` et passe un identificateur de chaîne, qui est identique à l’ID de Storyboard spécifié à l’étape précédente. Cela permet de lier C# le à la scène appropriée dans le Storyboard.

À un moment donné, vous souhaiterez peut-être ajouter une classe existante qui n’a **pas** été créée dans le concepteur. Dans ce cas, vous devez enregistrer cette classe comme d’habitude :

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

Pour plus d’informations sur l’inscription des classes et des méthodes, reportez-vous à la documentation [type Registrar](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) .

La dernière étape de cette classe consiste à relier le bouton et la transition au contrôleur d’affichage rose. Nous instancierons le `PinkViewController` à partir de la table de montage séquentiel. Ensuite, nous allons programmer un segue Push avec `PushViewController`, comme illustré par l’exemple de code ci-dessous :

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

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

L’exécution de l’application génère une application à 2 écrans :

![](images/finishedstoryboard.png "Sample app run screens")

## <a name="conditional-segues"></a>SEGUES conditionnel

Souvent, le passage d’un contrôleur d’affichage à l’autre dépend d’une certaine condition. Par exemple, si nous avions un simple écran de connexion, nous voulons simplement passer à l’écran suivant *si* le nom d’utilisateur et le mot de passe ont été vérifiés.

Dans l’exemple suivant, nous allons ajouter un champ de mot de passe à l’exemple ci-dessus. L’utilisateur pourra uniquement accéder au *PinkViewController* s’il entre le mot de passe correct. sinon, une erreur s’affichera.

Avant de commencer, suivez les étapes 1 à 8 ci-dessus. Dans ces étapes, nous créons notre Storyboard, nous commençons à créer notre interface utilisateur et indiquons à notre application le contrôleur d’affichage à utiliser comme RootViewController.

1. Nous allons maintenant développer notre interface utilisateur et ajouter les vues supplémentaires listées à la `MainViewController` pour qu’elle ressemble à ceci dans la capture d’écran ci-dessous :

    - UITextField
        - Nom : PasswordTextField
        - Espace réservé : « entrez le mot de passe secret »
    - UILabel
        - Texte : «erreur : mot de passe incorrect. Vous ne devez pas passer ! '
        - Couleur : rouge
        - Alignement : Centre
        - Lignes : 2
        - Case à cocher « Masqué » activée    
        
    [![](images/passwordvc.png "Center Lines")](images/passwordvc.png#lightbox)
    
2. Créez un segue entre le bouton atteindre le rose et le contrôleur d’affichage en faisant glisser le pointeur de la souris du *PinkButton* vers le *PinkViewController*, puis en sélectionnant **Push** sur souris. 

3. Cliquez sur segue et donnez-lui l' *identificateur* `SegueToPink` :

    [![](images/namesegue.png "Click on the Segue and give it the Identifier SegueToPink")](images/namesegue.png#lightbox)  

4. Enfin, ajoutez la méthode ShouldPerformSegue suivante à la classe `MainViewController` :

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

Dans ce code, nous avons mis en correspondance le segueIdentifier et notre segue `SegueToPink`. nous pouvons donc tester une condition. un mot de passe valide dans ce cas. Si notre condition retourne `true`, le segue s’exécute et présente le `PinkViewController`. Si `false`, le nouveau contrôleur d’affichage ne sera pas présenté.

Nous pouvons appliquer cette approche à n’importe quel segue sur ce contrôleur d’affichage en vérifiant l’argument segueIdentifier pour la méthode ShouldPerformSegue. Dans ce cas, nous n’avons qu’un seul identificateur segue : `SegueToPink`.

Reportez-vous à la solution storyboards. Conditional dans l' [exemple de storyboards manuels](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard) pour obtenir un exemple fonctionnel.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilisation de références de Storyboard

Une référence de table de montage séquentiel vous permet d’effectuer une conception de Storyboard complexe et de la décomposer en plus petits storyboards qui sont référencés à partir de l’original, supprimant ainsi la complexité et rendant les storyboards individuels résultants plus faciles à concevoir et à gérer.

En outre, une référence de table de montage séquentiel peut fournir une _ancre_ à une autre scène au sein d’une même table de montage séquentiel ou d’une scène spécifique sur un autre plan.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Référencement d’un Storyboard externe

Pour ajouter une référence à un Storyboard externe, procédez comme suit :

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter**  > **nouveau fichier...**  > **Storyboard**de  > **iOS** . Entrez un **nom** pour la nouvelle table de montage séquentiel, puis cliquez sur le bouton **nouveau** :
    
    [![](images/ref01.png "The New File Dialog")](images/ref01.png#lightbox)
    
2. Concevez la disposition des scènes de la nouvelle table de montage séquentiel comme vous le feriez normalement et enregistrez vos modifications : 
    
    [![](images/ref02.png "The layout of the new scene")](images/ref02.png#lightbox)
    
3. Ouvrez le Storyboard auquel vous allez ajouter la référence dans le concepteur iOS.

4. Faites glisser une **référence de Storyboard** de la **boîte à outils** vers le aire de conception : 
    
    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)
    
5. Dans l’onglet **widget** de l' **Explorateur de propriétés**, sélectionnez le nom de la table de **montage séquentiel** que vous avez créée ci-dessus : 

    [![](images/ref04.png "The Widget tab")](images/ref04.png#lightbox)
    
6. Cliquez avec le bouton droit sur un widget d’interface utilisateur (comme un bouton) sur une scène existante, puis créez un nouveau segue dans la **référence de table de montage séquentiel** que vous venez de créer : 

    [![](images/ref05.png "Creating a segue")](images/ref05.png#lightbox) 
    
7. Dans le menu contextuel, sélectionnez **Afficher** pour terminer le segue : 

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox) 
    
8. Enregistrez vos modifications dans le Storyboard.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur à partir duquel vous avez créé le Segue, le contrôleur d’affichage initial du storyboard externe spécifié dans la référence de la table de montage séquentiel s’affiche.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Référencement d’une scène spécifique dans un Storyboard externe

Pour ajouter une référence à une scène spécifique, un Storyboard externe (et non le contrôleur d’affichage initial), procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur le Storyboard externe pour l’ouvrir en vue de le modifier.

2. Ajoutez une nouvelle scène et concevez sa disposition comme vous le feriez normalement : 

    [![](images/ref07.png "The new scene layout")](images/ref07.png#lightbox)
    
3. Dans l’onglet **widget** de l' **Explorateur de propriétés**, entrez un **ID de Storyboard** pour le contrôleur d’affichage de la nouvelle scène : 

    [![](images/ref08.png "Enter a Storyboard ID for the new Scenes View Controller")](images/ref08.png#lightbox)
    
4. Ouvrez le Storyboard auquel vous allez ajouter la référence dans le concepteur iOS.

5. Faites glisser une **référence de Storyboard** de la **boîte à outils** vers le aire de conception : 

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)
    
6. Dans l’onglet **widget** de l' **Explorateur de propriétés**, sélectionnez le nom de la table de **montage séquentiel** et l' **ID de référence** (Storyboard ID) de la scène que vous avez créée ci-dessus : 

    [![](images/ref09.png "The Widget tab ")](images/ref09.png#lightbox)
    
7. Cliquez avec le bouton droit sur un widget d’interface utilisateur (comme un bouton) sur une scène existante, puis créez un nouveau segue dans la **référence de table de montage séquentiel** que vous venez de créer : 

    [![](images/ref10.png "Creating a segue")](images/ref10.png#lightbox) 
    
8. Dans le menu contextuel, sélectionnez **Afficher** pour terminer le segue : 

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox) 
    
9. Enregistrez vos modifications dans le Storyboard.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur à partir duquel vous avez créé le Segue, la scène avec l' **ID de Storyboard** donné à partir du storyboard externe spécifié dans la référence de la table de montage séquentiel s’affiche.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Référencement d’une scène spécifique dans la même table de montage séquentiel

Pour ajouter une référence à une scène spécifique du même Storyboard, procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur le Storyboard pour l’ouvrir pour le modifier.

2. Ajoutez une nouvelle scène et concevez sa disposition comme vous le feriez normalement : 

    [![](images/ref11.png "The new scene layout")](images/ref11.png#lightbox)

3. Dans l’onglet **widget** de l' **Explorateur de propriétés**, entrez un **ID de Storyboard** pour le contrôleur d’affichage de la nouvelle scène : 

    [![](images/ref12.png "The Widget tab")](images/ref12.png#lightbox)
    
4. Faites glisser une **référence de Storyboard** de la **boîte à outils** vers le aire de conception : 

   [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)
    
5. Dans l’onglet **widget** de l' **Explorateur de propriétés**, sélectionnez **ID de référence** (ID de Storyboard) de la scène créée ci-dessus : 

    [![](images/ref13.png "The Widget tab")](images/ref13.png#lightbox)
    
6. Cliquez avec le bouton droit sur un widget d’interface utilisateur (comme un bouton) sur une scène existante, puis créez un nouveau segue dans la **référence de table de montage séquentiel** que vous venez de créer : 

    [![](images/ref14.png "Creating a segue")](images/ref14.png#lightbox) 
    
7. Dans le menu contextuel, sélectionnez **Afficher** pour terminer le segue : 

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox) 
    
8. Enregistrez vos modifications dans le Storyboard.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur à partir duquel vous avez créé le Segue, la scène avec l' **ID de Storyboard** donné dans le même Storyboard spécifié dans la référence de la table de montage séquentiel s’affiche.

## <a name="summary"></a>Récapitulatif

Cet article présente le concept de storyboards et la façon dont ils peuvent être bénéfiques au développement d’applications iOS. Il aborde les scènes, les contrôleurs d’affichage, les vues et les hiérarchies de vues, ainsi que la façon dont les scènes sont liées avec différents types de SEGUES.  Il explore également l’instanciation manuelle des contrôleurs d’affichage à partir d’une table de montage séquentiel et la création de SEGUES conditionnelles.

## <a name="related-links"></a>Liens associés

- [Storyboard manuel (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [Présentation du concepteur iOS](~/ios/user-interface/designer/introduction.md)
- [Convertir en storyboards](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Référence de la classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
