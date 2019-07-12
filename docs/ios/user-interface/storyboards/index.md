---
title: Introduction aux Storyboards dans Xamarin.iOS
description: Ce document fournit une introduction aux storyboards dans Xamarin.iOS. Il décrit l’utilisation d’une table de montage séquentiel pour définir une interface utilisateur, enchaînements et comment utiliser le concepteur iOS pour modifier des fichiers de la table de montage séquentiel.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 5c3c9714cb4d6cd077f898778f4388d63018fa00
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831698"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introduction aux Storyboards dans Xamarin.iOS

Dans ce guide, nous allons expliquer quelles un Storyboard est et examiner certains des composants clés, tels que Segues. Nous allons étudier comment les Storyboards peuvent être créés et utilisés, et quels sont les avantages que qu’ils utilisent pour un développeur.

Avant que le format de fichier Storyboard a été introduit par Apple comme une représentation visuelle de l’interface utilisateur d’une application iOS, les développeurs a créé les fichiers XIB pour chaque contrôleur d’affichage et programmer le volet de navigation entre chaque vue manuellement.  À l’aide d’un Storyboard permet au développeur de définir des contrôleurs d’affichage et la navigation entre eux sur une aire de conception et permet la modification WYSIWYG de l’interface utilisateur de l’application.

Un Storyboard peut être créé, ouvert et modifié avec le Concepteur de Xamarin iOS. Ce guide effectue également une procédure pas à pas comment utiliser le concepteur pour créer vos plans conceptuels depuis tout en utilisant c# pour programmer le volet de navigation.


## <a name="requirements"></a>Spécifications

Tables de montage séquentiel utilisable avec le concepteur dans Visual Studio pour Mac iOS ou avec Visual Studio 2017 avec les charges de travail Xamarin installés.

## <a name="what-is-a-storyboard"></a>Qu’est une table de montage séquentiel ?

Un Storyboard est la représentation visuelle de tous les écrans dans une application. Il contient une séquence d’arrière-plan, avec chaque scène représentant un *contrôleur d’affichage* et son *vues*. Ces vues peuvent contenir des objets et [contrôles](~/ios/user-interface/controls/index.md) qui permettra à votre utilisateur d’interagir avec votre application. Cette collection de vues et de contrôles (ou *sous-affichages*) est appelé un *hiérarchie d’affichage de contenu*. Arrière-plan est connectés par segue objets qui représentent une transition entre des contrôleurs d’affichage. Cela est généralement obtenue en créant un segue entre un objet sur la vue initiale et la vue qui se connecte. Les relations sur l’aire de conception sont illustrées dans l’image ci-dessous :

 [![](images/storyboardsview.png "Les relations sur l’aire de conception sont illustrées dans cette image")](images/storyboardsview.png#lightbox)

Comme indiqué, le plan conceptuel sera mise en page chacun de vos scènes avec du contenu déjà et illustre les connexions entre eux.  Il est important de noter à ce stade, lorsque nous parlons d’arrière-plan sur un iPhone, il est logique de supposer qu’un *scène* sur le plan conceptuel est égal à un *écran* du contenu sur l’appareil. Toutefois, avec un iPad, qu'il est possible d’avoir plusieurs scènes apparaissent à la fois – par exemple, à l’aide un contrôleur d’affichage de menu.

Il existe de nombreux avantages à l’aide de storyboards pour créer l’interface utilisateur de votre application, en particulier lorsque vous utilisez Xamarin. Tout d’abord, il est une représentation visuelle de l’interface utilisateur, comme tous les objets – notamment [des contrôles personnalisés](~/ios/user-interface/designer/ios-designable-controls-overview.md) – sont rendus au moment du design. Cela signifie qu’avant de générer ou déployer votre application, vous pouvez visualiser son apparence et le flux. Prenez l’image ci-dessus, par exemple. Nous pouvons dire à partir de l’examen rapide de la conception sont surfaces scènes combien il, la disposition de chaque vue et comment tout est lié. C’est ce qui rend des Storyboards si puissant.

Les événements sont plus faciles à gérer avec des Storyboards, particulièrement lorsque vous utilisez le concepteur iOS. La plupart des contrôles d’interface utilisateur possède une liste d’événements possible dans le panneau Propriétés. Le Gestionnaire d’événements peut être ajouté ici et exécutée dans une méthode partielle dans la classe de contrôleurs d’affichage...

Le contenu d’un storyboard est stocké dans un fichier XML. AT heure de création, n’importe quel `.storyboard` fichiers sont compilés dans des fichiers binaires appelés plumes. Lors de l’exécution, ces plumes initialisées et instanciés pour créer des vues.

## <a name="segues"></a>Segues

Un *Segue*, ou *objet Segue*, est utilisé dans le développement iOS pour représenter une transition entre les scènes. Pour créer un segue, maintenez la **Ctrl** clé et cliquez et faites glisser à partir d’une scène à l’autre. Lorsque nous avons faites glisser nos la souris, un connecteur bleu s’affiche, indiquant où le segue entraîne comme illustré dans l’image ci-dessous :

 [![](images/createsegue.png "Un connecteur bleu s’affiche, indiquant où le segue entraîne comme illustré dans cette image")](images/createsegue.png#lightbox)

Sur le trajet de souris, un menu s’affiche permet de choisir l’action pour notre segue. Il peut ressembler aux images ci-dessous : 

**Les Classes préliminaire iOS 8 et de la taille**:

[![](images/segue1.png "La liste déroulante de Action Segue sans Classes de taille")](images/segue1.png#lightbox)

**Lorsque vous utilisez les Classes de taille et les enchaînements Adaptive**:

[![](images/16new.png "La liste déroulante de Action Segue avec les Classes de taille")](images/16new.png#lightbox)

> [!IMPORTANT]
> Si vous utilisez pour votre Machine virtuelle Windows VMWare, Ctrl + clic est mappé comme le _avec le bouton droit_ bouton de la souris par défaut. Pour créer un Segue, modifiez vos préférences de clavier via **préférences** > **clavier et souris** > **raccourcis souris** et remapper votre **Bouton secondaire** comme illustré ci-dessous :
> 
> [![](images/image22.png "Paramètres de préférence de clavier et souris")](images/image22.png#lightbox)
> 
> Vous devez maintenant être en mesure d’ajouter un segue entre vos contrôleurs d’affichage comme d’habitude.

Il existe différents types de transitions, chaque contrôle donné sur la façon dont un contrôleur d’affichage est présenté à l’utilisateur et comment elle interagit avec les autres contrôleurs d’affichage dans le Storyboard. Ils sont expliqués ci-dessous. Il est également possible de sous-classe d’un objet de segue pour implémenter une transition personnalisée :

-  **Afficher / Push** – un push de segue ajoute le contrôleur d’affichage à la pile de navigation. Il part du principe que le contrôleur d’affichage le push d’origine fait partie du même contrôleur de navigation en tant que le contrôleur d’affichage qui est ajouté à la pile. Cela fait la même chose que `pushViewController` et est généralement utilisée lorsqu’il existe une relation entre les données sur les écrans. À l’aide de la notification push segue vous donne le luxe d’avoir une barre de navigation avec un bouton Précédent et le titre ajoutés à la vue sur la pile, ce qui permet de descendre dans la navigation dans la hiérarchie d’affichage de la hiérarchie.
-  **Modale** – un segue modale créer une relation entre les contrôleurs de deux affichage dans votre projet, avec la possibilité d’une transition animée affichée. Le contrôleur d’affichage enfant masquera complètement le contrôleur d’affichage parent lorsque présentée dans l’affichage. Contrairement à une notification push segue, qui ajoute un bouton Précédent pour nous ; Quand à l’aide d’une fenêtre modale segue `DismissViewController` doit être utilisé afin de retourner le contrôleur d’affichage précédente.
-  **Personnalisé** : toute personnalisé segue peut être créés en tant que sous-classe de `UIStoryboardSegue`.
-  **Déroulement** : déroulement segue peut être utilisé pour naviguer émetteur ou modal segue – par exemple, en fermant le contrôleur d’affichage modal présentées. En outre, vous pouvez dérouler via pas qu’un seul, mais une série de push et modal enchaînements et revenir en arrière plusieurs étapes dans votre hiérarchie de navigation avec une seule action de déroulement. Pour comprendre comment utiliser un déroulement segue dans iOS, lire la [création de déroulement Enchaînements](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) Recipe (Recette).
-  **Sans** – un segue sans source indique la scène contenant le contrôleur d’affichage initial, et par conséquent vue dans laquelle l’utilisateur voient en premier. Il est représenté par le segue indiqué ci-dessous :  

    [![](images/sourcelesssegue.png "Un segue sans source")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Types de Segue ADAPTATIF

 iOS 8 introduit [catégories de taille](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) pour autoriser un fichier de storyboard iOS fonctionne avec toutes les tailles d’écran disponible, permettant aux développeurs de créer une interface utilisateur pour tous les appareils iOS. Par défaut, toutes les nouvelles applications Xamarin.iOS utilise les classes de taille. Pour utiliser les classes de taille à partir d’un projet antérieur, reportez-vous à la [Introduction aux Storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) guide. 
 
Toute application à l’aide des Classes de taille également utilisera le nouveau [ *Enchaînements Adaptive*](~/ios/user-interface/storyboards/unified-storyboards.md). Lorsque vous utilisez des classes de taille, n’oubliez pas que nous ne spécifier directement les accepte, nous utilisons un iPhone ou iPad. En d’autres termes, nous allons créer une interface utilisateur que recherche toujours la même, quelle que soit la quantité immobilier il doit fonctionner avec. Travail de Segues ADAPTATIF en juger de l’environnement et en déterminant la meilleure façon de présenter le contenu. Les enchaînements ADAPTATIF sont présentées ci-dessous : 

[![](images/adaptivesegue.png "La liste déroulante Enchaînements ADAPTATIF")](images/adaptivesegue.png#lightbox)

|Segue|Description|
|--- |--- |
|Afficher|Cela est très similaire à une notification Push segue, mais il prend le contenu de l’écran en compte.|
|Afficher les détails|Si l’application affiche une vue maître et détail (par exemple, dans un contrôleur d’affichage fractionné sur un iPad), le contenu remplace la vue détail. Si l’application affiche uniquement le maître ou détail, le contenu remplacera le haut de la pile de contrôleur d’affichage.|
|La présentation|Cela ressemble au segue Modal et autorise la sélection de styles de présentation et de transition.|
|Présentation de menu|Cela présente le contenu sous la forme d’un menu|

### <a name="transferring-data-with-segues"></a>Transfert de données avec des Segues

Les avantages d’un segue ne pas se terminer avec les transitions. Ils peuvent également être utilisés pour gérer le transfert de données entre les contrôleurs d’affichage. Pour cela en substituant le `PrepareForSegue` méthode sur le contrôleur d’affichage initial et le traitement des données nous-mêmes. Quand le segue est déclenché : par exemple, avec un sur le bouton – l’application appelle cette méthode, en offrant la possibilité de préparer le contrôleur d’affichage *avant* aucune navigation se produit. Le code ci-dessous, à partir de la [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) exemples, illustre cela : 


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

Dans cet exemple, le `PrepareForSegue` méthode sera appelée lorsque le segue est déclenché par l’utilisateur. Nous devons d’abord créer une instance de contrôleur d’affichage « réception » et le définir comme le de destination du segue contrôleur d’affichage. Cette opération est effectuée par la ligne de code ci-dessous :

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

La méthode a désormais la possibilité de définir des propriétés sur le `DestinationViewController`. Dans cet exemple nous avons tiré parti de cela en passant une liste appelée `PhoneNumbers` à la `CallHistoryController` et l’affecter à un objet du même nom :

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Une fois la transition terminée, l’utilisateur verra le `CallHistoryController` avec la liste.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Ajout d’une table de montage séquentiel à un projet table de montage séquentiel

Parfois, vous devrez peut-être ajouter une table de montage séquentiel dans un fichier précédemment non-table de montage séquentiel. Cette opération qu’une seule fois dans Visual Studio pour Mac peut être rationalisée en suivant les étapes ci-dessous :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Créer un nouveau fichier de table de montage séquentiel en accédant à **fichier > nouveau fichier > iOS > Storyboard**, comme illustré ci-dessous : 
    
    [![](images/new-storyboard-xs.png "La boîte de dialogue Nouveau fichier")](images/new-storyboard-xs.png#lightbox)

2. Ajoutez votre nom de la table de montage séquentiel à la **Interface principale** section de la **Info.plist**, comme illustré ci-dessous :
    
    [![](images/infoplist.png "L’éditeur Info.plist")](images/infoplist.png#lightbox)
    
    Cela fait l’équivalent de l’instanciation du contrôleur d’affichage Initial dans le `FinishedLaunching` méthode dans le délégué de l’application. Avec cette option, l’application instancie une fenêtre (voir ci-dessous), charge le storyboard principal et affecte une instance de contrôleur d’affichage Initial de la table de montage séquentiel (celui qui se trouve à côté du Segue sans source) en tant que le `RootViewController` propriété de la fenêtre, puis rend la fenêtre visible à l’écran.

3. Dans le `AppDelegate`, remplacer la valeur par défaut `Window` (méthode), avec le code suivant pour implémenter la propriété de fenêtre :
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Créer un nouveau fichier de table de montage séquentiel en cliquant sur le projet à **Ajouter > nouveau fichier > iOS > Storyboard vide**, comme illustré ci-dessous : 
    
    [![](images/new-storyboard-vs.png "La boîte de dialogue Nouvel élément")](images/new-storyboard-vs.png#lightbox)

2. Ajoutez votre nom de la table de montage séquentiel à la **Interface principale** section d’iOS Application, comme indiqué ci-dessous :
    
    [![](images/ios-app.png "L’éditeur Info.plist")](images/ios-app.png#lightbox)
    
    Cela fait l’équivalent de l’instanciation du contrôleur d’affichage Initial dans le `FinishedLaunching` méthode dans le délégué de l’application. Avec cette option, l’application instancie une fenêtre (voir ci-dessous), charge le storyboard principal et affecte une instance de contrôleur d’affichage Initial de la table de montage séquentiel (celui qui se trouve à côté du Segue sans source) en tant que le `RootViewController` propriété de la fenêtre, puis rend la fenêtre visible à l’écran.

3. Dans le `AppDelegate`, remplacer la valeur par défaut `Window` (méthode), avec le code suivant pour implémenter la propriété de fenêtre :

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Création d’une table de montage séquentiel avec le concepteur iOS

Une table de montage peut être créé à l’aide du Concepteur de Xamarin pour iOS, ce qui a été entièrement intégré à Visual Studio pour Mac et Visual Studio.

Pour commencer à utiliser le concepteur iOS pour créer des plans conceptuels, suivez le [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guide. Dans cette procédure pas à pas, vous allez Explorer navigation entre les contrôleurs d’affichage à l’aide de Segues et comment gérer des événements sur vos contrôles.

## <a name="instantiate-storyboards-manually"></a>Instancier manuellement des tables de montage séquentiel

Storyboards remplacement totalement les fichiers XIB individuels dans votre projet, mais les contrôleurs d’affichage individuels dans une table de montage séquentiel peuvent toujours être instanciés à l’aide de `Storyboard.InstantiateViewController`.

Parfois, les applications ont des exigences particulières qui ne peuvent pas être gérées avec les transitions de storyboard intégrés fournies par le concepteur. Par exemple, si nous consistait à créer une application qui lance des différents écrans à partir du bouton même, selon l’état actuel d’une application, nous souhaiterons peut-être instancier les contrôleurs d’affichage manuellement et programmer la transition nous-mêmes.

La capture d’écran ci-dessous montre deux contrôleurs d’affichage sur l’aire de conception de notre sans aucune segue entre eux. La section suivante guidera comment cette transition peut être configurée dans le code.

 [![](images/viewcontrollerspink.png "Cette capture d’écran montre deux contrôleurs d’affichage sur l’aire de conception sans aucune segue entre eux")](images/viewcontrollerspink.png#lightbox)

1. Ajouter un _iPhone Storyboard vide_ à un projet de projet existant :
    
    [![](images/add-storyboard1.png "Ajout de table de montage séquentiel")](images/add-storyboard1.png#lightbox)

2. Double-cliquez sur la table de montage séquentiel qui vient d’être créé pour l’ouvrir, puis ajoutez une nouvelle **contrôleur de Navigation** à l’aire de conception. Comme le contrôleur de Navigation est sans interface utilisateur, par défaut, elle sera accompagnée d’un contrôleur d’affichage racine, comme illustré ci-dessous :

    [![](images/uinavigationcontroller.png "Contrôleurs d’affichage avec Segues")](images/uinavigationcontroller.png#lightbox)

3. Sélectionnez le _contrôleur d’affichage_ en cliquant sur la barre noire située en bas. Dans le concepteur **remplissage de la propriété**, sous **identité** nous pouvons définir une classe personnalisée, ainsi qu’un ID unique pour le contrôleur d’affichage. Définir le **nom de la classe** et **ID de Storyboard** à `MainViewController`.

    [![](images/identitypanelnew.png "Spécifiez la classe personnalisée")](images/identitypanelnew.png#lightbox)

4. Plus tard, nous devons instancier nos contrôleurs d’affichage à partir de la table de montage séquentiel et utilisera l’ID de Storyboard pour y faites référence dans notre code. Définition de l’ID de la restauration pour faire correspondre l’ID de Storyboard permet de s’assurer que le contrôleur d’affichage obtient correctement recréé si l’état doit être restaurée.

5. Nous disposons actuellement uniquement un contrôleur d’affichage. Faites glisser à un autre contrôleur d’affichage sur l’aire de conception. Dans le **remplissage de la propriété**, sous l’identité, définissez la classe et l’ID de Storyboard à `PinkViewController`, comme illustré ci-dessous :

    [![](images/pinkvcnew.png "Le bloc de propriété")](images/pinkvcnew.png#lightbox)
    
    L’IDE crée ces classes personnalisées pour les contrôleurs d’affichage. Ceux-ci peuvent être affichés dans le **panneau solutions**, comme illustré dans la capture d’écran ci-dessous :
    
    [![](images/solution-pad.png "Panneau solution")](images/solution-pad.png#lightbox)

6. Dans le `PinkViewController`, sélectionnez la vue en cliquant sur vers le centre du frame du contrôleur. Dans le panneau Propriétés, sous affichage, modifiez le **arrière-plan** magenta :
    
    [![](images/pinkcontroller.png "Définir la couleur d’arrière-plan")](images/pinkcontroller.png#lightbox)

7. Enfin, faites glisser un bouton à partir de la **boîte à outils** sur le `MainViewController`. Dans le panneau Propriétés, attribuez-lui le nom `PinkButton` et le titre GoToPink, comme illustré ci-dessous :

    [![](images/pinkbutton.png "Définir le nom de bouton")](images/pinkbutton.png#lightbox)

La table de montage est terminée, mais si nous déployer le projet maintenant, nous allons un écran vide. C’est parce que nous devons toujours demander à l’IDE à utiliser notre table de montage séquentiel et pour configurer un contrôleur d’affichage racine pour servir la première vue. Normalement cela est possible via nos Options du projet, comme indiqué ci-dessus. Toutefois dans cet exemple nous allons atteint le même résultat dans le code, en ajoutant le code suivant à la **AppDelegate**:

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

Cela représente beaucoup de code, mais seulement quelques lignes ne sont pas familiarisés. Tout d’abord, nous enregistrons notre table de montage séquentiel avec le **AppDelegate** en passant le nom de la table de montage séquentiel, **MainStoryboard**. Ensuite, nous indiquons à l’application pour instancier un contrôleur d’affichage initial à partir du storyboard en appelant `InstantiateInitialViewController` sur notre plan conceptuel, et nous définissons ce contrôleur d’affichage en tant que contrôleur d’affichage racine de notre application. Cette méthode détermine le premier écran que l’utilisateur voit, et crée une nouvelle instance de ce contrôleur d’affichage.

Notez que, dans le volet de la solution que l’IDE a créé un `MainViewcontroller.cs` (classe) et son `corresponding designer.cs` lorsque nous avons ajouté le nom de classe pour le panneau de propriétés à l’étape 4. Nous pouvons voir cette classe créée d’un constructeur spécial qui inclut une classe de base :

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Lorsque vous créez une table de montage séquentiel à l’aide du concepteur, l’IDE ajoute automatiquement le [[inscrire]](xref:Foundation.RegisterAttribute) attribut en haut de la `designer.cs` classe et vous transmettez un identificateur de chaîne, qui est identique à l’ID de plan conceptuel spécifié dans le étape précédente. Cela lie le c# à la scène pertinentes dans le Storyboard.

À un moment donné vous souhaiterez peut-être ajouter une classe existante qui a été **pas** créé dans le concepteur. Dans ce cas, vous pouvez enregistrer cette classe comme d’habitude :

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

Pour plus d’informations sur l’inscription des classes et méthodes, reportez-vous à la [bureau d’enregistrement de Type](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentation.

La dernière étape de cette classe consiste à associer le bouton et la transition vers le contrôleur d’affichage rose. Nous allons instancier le `PinkViewController` à partir de la table de montage séquentiel ; ensuite, nous sera programmer un push de segue avec `PushViewController`, comme l’illustre l’exemple de code ci-dessous :

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

Exécution de l’application génère une application écran 2 :

![](images/finishedstoryboard.png "Exécution des écrans de l’application exemple")

## <a name="conditional-segues"></a>Conditionnel Segues

Souvent, le déplacement d’un contrôleur d’affichage à l’autre est dépend d’une certaine condition. Par exemple, si nous devions effectuer un écran de connexion simple nous seulement voulons passer à l’écran suivant *si* le nom d’utilisateur et le mot de passe qui avaient été vérifiés.

Dans l’exemple suivant, nous allons ajouter un champ de mot de passe à l’exemple ci-dessus. L’utilisateur seulement sera en mesure d’accéder à la *PinkViewController* s’il a entré le mot de passe, sinon une erreur s’affichera.

Avant de commencer, suivez les étapes 1 à 8 ci-dessus. Dans ces étapes nous créer notre table de montage séquentiel, commence à créer notre interface utilisateur et indiquer à quel contrôleur d’affichage à utiliser notre délégué de l’application car il s’agit de RootViewController.

1. Maintenant, nous allons créer notre interface utilisateur et ajouter des affichages supplémentaires répertoriées à la `MainViewController` afin qu’il apparaisse comme celle-ci dans la capture d’écran ci-dessous :

    - UITextField
        - Nom : PasswordTextField
        - Espace réservé : « Entrez le mot de passe Secret »
    - UILabel
        - Texte : « Erreur : Mot de passe incorrect. Vous ne sont pas transmettre ! »
        - Couleur : Rouge
        - Alignement : Center
        - Lignes : 2
        - Case à cocher « Masqué » activée 
        
    [![](images/passwordvc.png "Centre les lignes")](images/passwordvc.png#lightbox)
    
2. Créer un Segue entre le bouton Accédez rose et le contrôleur d’affichage en faisant glisser de Ctrl de la *PinkButton* à la *PinkViewController*et en sélectionnant **Push** sur le trajet de souris . 

3. Cliquez sur le Segue et lui donner le *identificateur* `SegueToPink`:

    [![](images/namesegue.png "Cliquez sur le Segue et lui donner le SegueToPink identificateur")](images/namesegue.png#lightbox)  
    

4. Enfin, ajoutez la méthode ShouldPerformSegue suivante à la `MainViewController` classe :

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

Dans ce code nous avons mis en correspondance le segueIdentifier à notre `SegueToPink` segue, donc nous pouvons ensuite tester une condition ; un mot de passe valide dans ce cas. Si notre condition retourne `true`, effectuera le Segue et présentera le `PinkViewController`. Si `false`, le contrôleur d’affichage ne seront pas présenté.

Nous pouvons appliquer cette approche à n’importe quel Segue sur ce contrôleur d’affichage en vérifiant l’argument segueIdentifier à la méthode ShouldPerformSegue. Dans ce cas nous n’avons qu’un identificateur de Segue – `SegueToPink`.

Reportez-vous à la solution Storyboards.Conditional dans les [exemple de tables de montage séquentiel manuel](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) pour obtenir un exemple.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Utilisation de références de table de montage séquentiel

Une référence de table de montage séquentiel vous permet de prendre une conception de table de montage séquentiel volumineuses et complexe et la décomposer en plus petites tables de montage séquentiel obtient référencés à partir de l’original, par conséquent, de suppression de la complexité et de faciliter les Storyboards individuels qui en résulte concevoir et gérer.

En outre, une référence de table de montage séquentiel peut fournir un _ancre_ à un autre scène dans la même table de montage séquentiel ou une scène spécifique sur une autre.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Faisant référence à une table de montage séquentiel externe

Pour ajouter une référence à une table de montage séquentiel externe, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le nom du projet et sélectionnez **ajouter** > **nouveau fichier...**   >  **iOS** > **Storyboard**. Entrez un **nom** pour la table de montage séquentiel et cliquez sur le **New** bouton :
    
    [![](images/ref01.png "La boîte de dialogue Nouveau fichier")](images/ref01.png#lightbox)
    
2. Concevoir la disposition de l’arrière-plan de la nouvelle table de montage séquentiel serait normalement et enregistrer vos modifications : 
    
    [![](images/ref02.png "La disposition de la nouvelle scène")](images/ref02.png#lightbox)
    
3. Ouvrez la table de montage séquentiel que vous vous apprêtez à ajouter la référence à dans le concepteur iOS.

4. Faites glisser un **Storyboard référence** à partir de la **boîte à outils** sur l’aire de conception : 
    
    [![](images/ref03.png "Une référence de table de montage séquentiel")](images/ref03.png#lightbox)
    
5. Dans le **Widget** onglet de la **l’Explorateur de propriétés**, sélectionnez le nom de la **Storyboard** que vous avez créé ci-dessus : 

    [![](images/ref04.png "L’onglet de Widget")](images/ref04.png#lightbox)
    
6. Cliquez sur un Widget d’interface utilisateur (par exemple, un bouton) sur une scène existante et créer un nouveau Segue à la **référence de table de montage séquentiel** que vous venez de créer : 

    [![](images/ref05.png "Création d’un segue")](images/ref05.png#lightbox) 
    
7. Dans le menu contextuel, sélectionnez **afficher** pour terminer le Segue : 

    [![](images/ref06.png "Sélectionnez Afficher pour terminer le Segue")](images/ref06.png#lightbox) 
    
8. Enregistrez vos modifications dans la table de montage séquentiel.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur que vous avez créé le Segue à partir, le contrôleur d’affichage Initial à partir du Storyboard externe spécifié dans la référence de table de montage séquentiel seront affiche.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Référencement d’une scène spécifique dans une table de montage séquentiel externe

Pour ajouter une référence à une scène spécifique un Storyboard externe (et pas le contrôleur d’affichage Initial), procédez comme suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur la table de montage séquentiel externe pour l’ouvrir pour modification.

2. Ajouter une nouvelle scène et concevoir sa disposition, comme vous le feriez normalement : 

    [![](images/ref07.png "La nouvelle disposition de la scène")](images/ref07.png#lightbox)
    
3. Dans le **Widget** onglet de la **l’Explorateur de propriétés**, entrez un **ID de Storyboard** pour le contrôleur d’affichage de la scène de nouveau : 

    [![](images/ref08.png "Entrez un ID de Storyboard pour le nouveau contrôleur de vue de scènes")](images/ref08.png#lightbox)
    
3. Ouvrez la table de montage séquentiel que vous vous apprêtez à ajouter la référence à dans le concepteur iOS.

4. Faites glisser un **Storyboard référence** à partir de la **boîte à outils** sur l’aire de conception : 

    [![](images/ref03.png "Une référence de table de montage séquentiel")](images/ref03.png#lightbox)
    
5. Dans le **Widget** onglet de la **l’Explorateur de propriétés**, sélectionnez le nom de la **Storyboard** et le **ID de référence** (ID de Storyboard) de la Scène que vous avez créé ci-dessus : 

    [![](images/ref09.png "L’onglet de Widget ")](images/ref09.png#lightbox)
    
6. Cliquez sur un Widget d’interface utilisateur (par exemple, un bouton) sur une scène existante et créer un nouveau Segue à la **référence de table de montage séquentiel** que vous venez de créer : 

    [![](images/ref10.png "Création d’un segue")](images/ref10.png#lightbox) 
    
7. Dans le menu contextuel, sélectionnez **afficher** pour terminer le Segue : 

    [![](images/ref06.png "Sélectionnez Afficher pour terminer le Segue")](images/ref06.png#lightbox) 
    
8. Enregistrez vos modifications dans la table de montage séquentiel.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur que vous avez créé le Segue à partir de la scène avec la donnée **ID de Storyboard** à partir du Storyboard externe spécifié dans la référence de table de montage séquentiel s’affichera.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Référencement d’une scène spécifique dans la même table de montage séquentiel

Pour ajouter une référence à une scène spécifique de la même table de montage séquentiel, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le plan conceptuel pour l’ouvrir pour modification.

2. Ajouter une nouvelle scène et concevoir sa disposition, comme vous le feriez normalement : 

    [![](images/ref11.png "La nouvelle disposition de la scène")](images/ref11.png#lightbox)

3. Dans le **Widget** onglet de la **l’Explorateur de propriétés**, entrez un **ID de Storyboard** pour le contrôleur d’affichage de la scène de nouveau : 

    [![](images/ref12.png "L’onglet de Widget")](images/ref12.png#lightbox)
    
3. Faites glisser un **Storyboard référence** à partir de la **boîte à outils** sur l’aire de conception : 

    [![](images/ref03.png "Une référence de table de montage séquentiel")](images/ref03.png#lightbox)
    
5. Dans le **Widget** onglet de la **l’Explorateur de propriétés**, sélectionnez **ID de référence** (ID de Storyboard) de la scène que vous avez créé ci-dessus : 

    [![](images/ref13.png "L’onglet de Widget")](images/ref13.png#lightbox)
    
6. Cliquez sur un Widget d’interface utilisateur (par exemple, un bouton) sur une scène existante et créer un nouveau Segue à la **référence de table de montage séquentiel** que vous venez de créer : 

    [![](images/ref14.png "Création d’un segue")](images/ref14.png#lightbox) 
    
7. Dans le menu contextuel, sélectionnez **afficher** pour terminer le Segue : 

    [![](images/ref06.png "Sélectionnez Afficher pour terminer le Segue")](images/ref06.png#lightbox) 
    
8. Enregistrez vos modifications dans la table de montage séquentiel.

Lorsque l’application est exécutée et que l’utilisateur clique sur l’élément d’interface utilisateur que vous avez créé le Segue à partir de la scène avec la donnée **ID de Storyboard** dans la même table de montage spécifié dans la référence de table de montage séquentiel s’affichera.

## <a name="summary"></a>Récapitulatif

Cet article présente le concept des Storyboards et comment ils peuvent être utiles dans le développement d’applications iOS. Il aborde les scènes, contrôleurs d’affichage, vues et hiérarchies d’affichage et comment les scènes sont liés avec différents types de Segues.  Il étudie également les contrôleurs d’affichage lors de l’instanciation manuellement à partir d’une table de montage séquentiel et création Segues conditionnel.



## <a name="related-links"></a>Liens associés

- [Table de montage séquentiel manuelle (exemple)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introduction au concepteur iOS](~/ios/user-interface/designer/introduction.md)
- [Conversion vers des animations](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Référence de classe de UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
