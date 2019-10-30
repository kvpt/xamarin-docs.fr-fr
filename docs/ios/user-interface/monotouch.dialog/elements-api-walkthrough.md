---
title: Création d’une application Xamarin. iOS à l’aide de l’API Elements
description: Cet article s’appuie sur les informations présentées dans l’article présentation de la boîte de dialogue présentation de monocontact. Il présente une procédure pas à pas qui montre comment utiliser la boîte de dialogue MonoTouch. D) API Elements pour commencer rapidement à créer une application avec MT. E.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2258b2e8451f896aff59c89478833976ef7086e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002365"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Création d’une application Xamarin. iOS à l’aide de l’API Elements

_Cet article s’appuie sur les informations présentées dans l’article présentation de la boîte de dialogue présentation de monocontact. Il présente une procédure pas à pas qui montre comment utiliser la boîte de dialogue MonoTouch. D) API Elements pour commencer rapidement à créer une application avec MT. E._

Dans cette procédure pas à pas, nous allons utiliser le MT. API D Elements pour créer un style maître/détail d’une application qui affiche une liste de tâches. Quand l’utilisateur sélectionne le bouton **+** dans la barre de navigation, une nouvelle ligne est ajoutée à la table pour la tâche. Si vous sélectionnez la ligne, vous accédez à l’écran de détails qui nous permet de mettre à jour la description de la tâche et la date d’échéance, comme illustré ci-dessous :

[![](elements-api-walkthrough-images/01-task-list-app.png "Selecting the row will navigate to the detail screen that allows us to update the task description and the due date")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>Configuration de MT. E

MT. D est distribué avec Xamarin. iOS. Pour l’utiliser, cliquez avec le bouton droit sur le nœud **références** d’un projet Xamarin. iOS dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à l’assembly **MonoTouch. boîte de dialogue-1** . Ajoutez ensuite `using MonoTouch.Dialog` instructions dans votre code source, si nécessaire.

## <a name="elements-api-walkthrough"></a>Procédure pas à pas d’API d’éléments

Dans l’article présentation de la [boîte de dialogue de monocontact](~/ios/user-interface/monotouch.dialog/index.md) , nous avons acquis une bonne compréhension des différentes parties de Mt. E. Utilisons l’API Elements pour les rassembler dans une application.

## <a name="setting-up-the-multi-screen-application"></a>Configuration de l’application multi-écran

Pour démarrer le processus de création d’écran, vous pouvez créer un `DialogViewController`, puis ajouter une `RootElement`.

Pour créer une application à plusieurs écrans avec la boîte de dialogue MonoTouch, nous devons :

1. Créer un `UINavigationController.`
1. Créer un `DialogViewController.`
1. Ajoutez le `DialogViewController` comme racine du `UINavigationController.` 
1. Ajouter un `RootElement` au `DialogViewController.`
1. Ajouter `Sections` et `Elements` au `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Utilisation d’un UINavigationController

Pour créer une application de type navigation, nous devons créer un `UINavigationController`, puis l’ajouter comme `RootViewController` dans la méthode `FinishedLaunching` de l' `AppDelegate`. Pour que l' `UINavigationController` fonctionne avec la boîte de dialogue MonoTouch., nous ajoutons un `DialogViewController` à la `UINavigationController` comme indiqué ci-dessous :

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    _rootElement = new RootElement ("To Do List"){new Section ()};

    // code to create screens with MT.D will go here …

    _rootVC = new DialogViewController (_rootElement);
    _nav = new UINavigationController (_rootVC);
    _window.RootViewController = _nav;
    _window.MakeKeyAndVisible ();
            
    return true;
}
```

Le code ci-dessus crée une instance d’une `RootElement` et la passe dans le `DialogViewController`. La `DialogViewController` a toujours un `RootElement` en haut de sa hiérarchie. Dans cet exemple, le `RootElement` est créé avec la chaîne « liste des tâches », qui sert de titre dans la barre de navigation du contrôleur de navigation. À ce stade, l’exécution de l’application présente l’écran ci-dessous :

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Running the application will present the screen shown here")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Voyons comment utiliser la structure hiérarchique des `Sections` et des `Elements` de la boîte de dialogue pour ajouter d’autres écrans.

### <a name="creating-the-dialog-screens"></a>Création des écrans de boîte de dialogue

Une `DialogViewController` est une sous-classe `UITableViewController` qui utilise la boîte de dialogue pour ajouter des écrans. La boîte de dialogue monotactile crée des écrans en ajoutant un `RootElement` à un `DialogViewController`, comme nous l’avons vu ci-dessus. La `RootElement` peut avoir des instances `Section` qui représentent les sections d’un tableau.
Les sections sont constituées d’éléments, d’autres sections, voire d’autres `RootElements`. En imbriquant `RootElements`, la boîte de dialogue monotactile crée automatiquement une application de type navigation, comme nous le verrons ensuite.

### <a name="using-dialogviewcontroller"></a>Utilisation de DialogViewController

La `DialogViewController`, en tant que sous-classe `UITableViewController`, a un `UITableView` comme vue. Dans cet exemple, nous souhaitons ajouter des éléments à la table chaque fois que le bouton **+** est frappé. Étant donné que le `DialogViewController` a été ajouté à un `UINavigationController`, nous pouvons utiliser la propriété `RightBarButton` de `NavigationItem`pour ajouter le bouton **+** , comme indiqué ci-dessous :

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Lorsque nous avons créé le `RootElement` plus tôt, nous l’avons transmis à une seule `Section` instance pour pouvoir ajouter des éléments, car le bouton **+** est appuyé par l’utilisateur. Pour ce faire, nous pouvons utiliser le code suivant dans le gestionnaire d’événements pour le bouton :

```csharp
_addButton.Clicked += (sender, e) => {                
    ++n;
                
    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
    var taskElement = new RootElement (task.Name) {
        new Section () {
            new EntryElement (task.Name, "Enter task description", task.Description)
        },
        new Section () {
            new DateElement ("Due Date", task.DueDate)
        }
    };
    _rootElement [0].Add (taskElement);
};
```

Ce code crée un nouvel objet `Task` chaque fois que le bouton est frappé. L’exemple suivant illustre l’implémentation simple de la classe `Task` :

```csharp
public class Task
{   
    public Task ()
    {
    }
      
    public string Name { get; set; }
        
    public string Description { get; set; }

    public DateTime DueDate { get; set; }
}
```

La propriété `Name` de la tâche est utilisée pour créer la légende du `RootElement`avec une variable de compteur nommée `n` qui est incrémentée pour chaque nouvelle tâche. La boîte de dialogue monotactile permet de transformer les éléments en lignes ajoutées au `TableView` lorsque chaque `taskElement` est ajouté.

## <a name="presenting-and-managing-dialog-screens"></a>Présentation et gestion des écrans de dialogue

Nous avons utilisé une `RootElement` de sorte que la boîte de dialogue monotactile crée automatiquement un nouvel écran pour les détails de chaque tâche et y accède quand une ligne est sélectionnée.

L’écran de détail des tâches lui-même est composé de deux sections : chacune de ces sections contient un élément unique. Le premier élément est créé à partir d’une `EntryElement` pour fournir une ligne modifiable pour la propriété `Description` de la tâche. Lorsque l’élément est sélectionné, un clavier pour la modification de texte est présenté comme indiqué ci-dessous :

 [![](elements-api-walkthrough-images/03-create-task.png "When the element is selected, a keyboard for text editing is presented as shown")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La deuxième section contient une `DateElement` qui nous permet de gérer la propriété `DueDate` de la tâche. La sélection de la date charge automatiquement un sélecteur de dates, comme indiqué ci-dessous :

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecting the date automatically loads a date picker as")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Dans les cas `EntryElement` et `DateElement` (ou pour tout élément de saisie de données dans la boîte de dialogue), toute modification apportée aux valeurs est conservée automatiquement. Pour illustrer cela, vous pouvez modifier la date, puis naviguer entre l’écran racine et les divers détails de la tâche, où les valeurs des écrans de détails sont conservées.

## <a name="summary"></a>Récapitulatif

Cet article a présenté une procédure pas à pas qui a montré comment utiliser l’API d’éléments MonoTouch. Dialog. Il a abordé les étapes de base pour créer une application à plusieurs écrans avec MT. D, y compris l’utilisation d’un `DialogViewController` et l’ajout d’éléments et de sections pour créer des écrans. En outre, il a montré comment utiliser MT. D conjointement avec un `UINavigationController`.

## <a name="related-links"></a>Liens connexes

- [MTDWalkthrough (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Présentation de la boîte de dialogue de monopression.](~/ios/user-interface/monotouch.dialog/index.md)
- [Démonstration de l’API de réflexion](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procédure pas à pas d’élément JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Boîte de dialogue MonoTouch sur GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de la classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de la classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
