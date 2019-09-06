---
title: Création d’une application Xamarin. iOS à l’aide de l’API Elements
description: Cet article s’appuie sur les informations présentées dans l’article présentation de la boîte de dialogue présentation de monocontact. Il présente une procédure pas à pas qui montre comment utiliser la boîte de dialogue MonoTouch. D) API Elements pour commencer rapidement à créer une application avec MT. E.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: conceptdev
ms.author: crdun
ms.openlocfilehash: 61f214a7e072725bd7ba6cc4a3d493c8fcb909ff
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289302"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Création d’une application Xamarin. iOS à l’aide de l’API Elements

_Cet article s’appuie sur les informations présentées dans l’article présentation de la boîte de dialogue présentation de monocontact. Il présente une procédure pas à pas qui montre comment utiliser la boîte de dialogue MonoTouch. D) API Elements pour commencer rapidement à créer une application avec MT. E._

Dans cette procédure pas à pas, nous allons utiliser le MT. API D Elements pour créer un style maître/détail d’une application qui affiche une liste de tâches. Quand l’utilisateur sélectionne le **+** bouton dans la barre de navigation, une nouvelle ligne est ajoutée à la table pour la tâche. Si vous sélectionnez la ligne, vous accédez à l’écran de détails qui nous permet de mettre à jour la description de la tâche et la date d’échéance, comme illustré ci-dessous :

[![](elements-api-walkthrough-images/01-task-list-app.png "Si vous sélectionnez la ligne, vous accédez à l’écran de détails qui nous permet de mettre à jour la description de la tâche et la date d’échéance")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>Configuration de MT. E

MT. D est distribué avec Xamarin. iOS. Pour l’utiliser, cliquez avec le bouton droit sur le nœud **références** d’un projet Xamarin. iOS dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à l’assembly **MonoTouch. boîte de dialogue-1** . Ajoutez `using MonoTouch.Dialog` ensuite des instructions dans votre code source, si nécessaire.

## <a name="elements-api-walkthrough"></a>Procédure pas à pas d’API d’éléments

Dans l’article présentation de la [boîte de dialogue de monocontact](~/ios/user-interface/monotouch.dialog/index.md) , nous avons acquis une bonne compréhension des différentes parties de Mt. E. Utilisons l’API Elements pour les rassembler dans une application.

## <a name="setting-up-the-multi-screen-application"></a>Configuration de l’application multi-écran

Pour démarrer le processus de création d’écran, la boîte de dialogue `DialogViewController`monotactile crée un, `RootElement`puis ajoute un.

Pour créer une application à plusieurs écrans avec la boîte de dialogue MonoTouch, nous devons :

1. Créer un`UINavigationController.`
1. Créer un`DialogViewController.`
1. `DialogViewController` Ajoutez en tant que racine du`UINavigationController.` 
1. Ajoutez un `RootElement` au`DialogViewController.`
1. Ajoutez `Sections` et`Elements` à la`RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Utilisation d’un UINavigationController

Pour créer une application de type navigation, `UINavigationController`nous devons créer un, puis l' `RootViewController` ajouter en tant que `AppDelegate`dans la `FinishedLaunching` méthode de. Pour effectuer le `UINavigationController` travail avec la boîte de dialogue MonoTouch., `DialogViewController` nous ajoutons un au `UINavigationController` , comme indiqué ci-dessous :

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

Le code ci-dessus crée une instance `RootElement` de et la passe dans `DialogViewController`le. A toujours une `RootElement` en haut de sa hiérarchie. `DialogViewController` Dans cet exemple, le `RootElement` est créé avec la chaîne « liste des tâches », qui sert de titre dans la barre de navigation du contrôleur de navigation. À ce stade, l’exécution de l’application présente l’écran ci-dessous :

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "L’exécution de l’application présente l’écran illustré ici")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Voyons comment utiliser la structure hiérarchique de la boîte de `Sections` dialogue et `Elements` pour ajouter d’autres écrans.

### <a name="creating-the-dialog-screens"></a>Création des écrans de boîte de dialogue

Un `DialogViewController` est une `UITableViewController` sous-classe qui utilise la boîte de dialogue pour ajouter des écrans. La boîte de dialogue monotactile crée des écrans `RootElement` en ajoutant `DialogViewController`un à un, comme nous l’avons vu ci-dessus. Le `RootElement` peut avoir `Section` des instances qui représentent les sections d’un tableau.
Les sections sont constituées d’éléments, d’autres sections, voire d' `RootElements`autres. En imbriquant `RootElements`, la boîte de dialogue monotactile crée automatiquement une application de type navigation, comme nous le verrons ensuite.

### <a name="using-dialogviewcontroller"></a>Utilisation de DialogViewController

, Étant une `UITableViewController` sous-classe, a un `UITableView` comme vue. `DialogViewController` Dans cet exemple, nous souhaitons ajouter des éléments à la table chaque fois que **+** le bouton est frappé. Dans la `DialogViewController` mesure où le a `UINavigationController`été ajouté à un, `NavigationItem`nous `RightBarButton` pouvons utiliser la propriété **+** de pour ajouter le bouton, comme indiqué ci-dessous :

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Lorsque nous avons créé `RootElement` le précédent, nous lui avons passé `Section` une seule instance afin de pouvoir ajouter des éléments **+** lorsque l’utilisateur appuie sur le bouton. Pour ce faire, nous pouvons utiliser le code suivant dans le gestionnaire d’événements pour le bouton :

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

Ce code crée un nouvel `Task` objet chaque fois que le bouton est frappé. L’exemple suivant illustre l’implémentation simple de `Task` la classe :

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

La propriété de `Name` la tâche est utilisée pour créer `RootElement`la légende de la, ainsi qu’une `n` variable de compteur nommée qui est incrémentée pour chaque nouvelle tâche. La boîte de dialogue monotactile permet de transformer les éléments en lignes ajoutées au `TableView` quand chaque `taskElement` est ajouté.

## <a name="presenting-and-managing-dialog-screens"></a>Présentation et gestion des écrans de dialogue

Nous avons utilisé `RootElement` un pour que la boîte de dialogue monotactile crée automatiquement un nouvel écran pour les détails de chaque tâche et y accède lorsqu’une ligne est sélectionnée.

L’écran de détail des tâches lui-même est composé de deux sections : chacune de ces sections contient un élément unique. Le premier élément est créé à partir `EntryElement` d’un pour fournir une ligne modifiable pour `Description` la propriété de la tâche. Lorsque l’élément est sélectionné, un clavier pour la modification de texte est présenté comme indiqué ci-dessous :

 [![](elements-api-walkthrough-images/03-create-task.png "Lorsque l’élément est sélectionné, un clavier pour la modification de texte est présenté comme indiqué")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La deuxième section contient un `DateElement` qui nous permet de gérer la propriété `DueDate` de la tâche. La sélection de la date charge automatiquement un sélecteur de dates, comme indiqué ci-dessous :

 [![](elements-api-walkthrough-images/04-date-picker.png "La sélection de la date charge automatiquement un sélecteur de dates en tant que")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Dans les cas `EntryElement` et `DateElement` (ou pour tout élément de saisie de données dans la boîte de dialogue), toute modification apportée aux valeurs est conservée automatiquement. Pour illustrer cela, vous pouvez modifier la date, puis naviguer entre l’écran racine et les divers détails de la tâche, où les valeurs des écrans de détails sont conservées.

## <a name="summary"></a>Récapitulatif

Cet article a présenté une procédure pas à pas qui a montré comment utiliser l’API d’éléments MonoTouch. Dialog. Il a abordé les étapes de base pour créer une application à plusieurs écrans avec MT. D, y compris l’utilisation d' `DialogViewController` un et l’ajout d’éléments et de sections pour créer des écrans. En outre, il a montré comment utiliser MT. D conjointement avec un `UINavigationController`.

## <a name="related-links"></a>Liens connexes

- [MTDWalkthrough (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Présentation de la boîte de dialogue de monopression.](~/ios/user-interface/monotouch.dialog/index.md)
- [Démonstration de l’API de réflexion](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procédure pas à pas d’élément JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Boîte de dialogue MonoTouch sur GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de la classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de la classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
