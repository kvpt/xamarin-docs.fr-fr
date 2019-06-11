---
title: Création d’une application Xamarin.iOS à l’aide de l’API Elements
description: Cet article s’appuie sur les informations présentées dans l’Introduction de l’article de la boîte de dialogue MonoTouch. Il présente une procédure pas à pas qui montre comment utiliser le MonoTouch.Dialog (serveur maître cible. (D) éléments API pour commencer rapidement à bâtir une application avec le serveur cible maître. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 9331c7e6920f94d2ff0dddc50eb8f1ff9817d982
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827857"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Création d’une application Xamarin.iOS à l’aide de l’API Elements

_Cet article s’appuie sur les informations présentées dans l’Introduction de l’article de la boîte de dialogue MonoTouch. Il présente une procédure pas à pas qui montre comment utiliser le MonoTouch.Dialog (serveur maître cible. (D) éléments API pour commencer rapidement à bâtir une application avec le serveur cible maître. D._

Dans cette procédure pas à pas, nous allons utiliser le serveur cible maître. API d’éléments de D pour créer un style maître-détail d’application qui affiche une liste de tâches. Lorsque l’utilisateur sélectionne le <span class="ui"> + </span> bouton dans la barre de navigation, une nouvelle ligne sera ajoutée à la table pour la tâche. En sélectionnant la ligne permet d’accéder à l’écran de détails qui nous permet de mettre à jour la description de la tâche et la date d’échéance, comme illustré ci-dessous :

 [![](elements-api-walkthrough-images/01-task-list-app.png "En sélectionnant la ligne permet d’accéder à l’écran de détails qui nous permet de mettre à jour la description de la tâche et la date d’échéance")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>Configuration de serveur cible maître. D

SERVEUR CIBLE MAÎTRE. D est distribué avec Xamarin.iOS. Pour l’utiliser, cliquez sur le **références** nœud d’un Xamarin.iOS de projet dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à la **MonoTouch.Dialog-1** assembly. Ensuite, ajoutez `using MonoTouch.Dialog` instructions dans votre source de code en fonction des besoins.

## <a name="elements-api-walkthrough"></a>Éléments d’API de procédure pas à pas

Dans le [Introduction à la boîte de dialogue MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) article, nous avons gagné une connaissance approfondie des différentes parties du serveur cible maître. D. Nous allons utiliser l’API d’éléments pour qu’elles retrouvent ensemble dans une application.

## <a name="setting-up-the-multi-screen-application"></a>Configuration de l’application multi-écran

Pour démarrer le processus de création d’écran, MonoTouch.Dialog crée un `DialogViewController`, puis ajoute un `RootElement`.

Pour créer une application multi-écran avec MonoTouch.Dialog, nous devons :

1.  Créer un `UINavigationController.`
1.  Créer un `DialogViewController.`
1.  Ajouter le `DialogViewController` comme racine de la  `UINavigationController.` 
1.  Ajouter un `RootElement` à la  `DialogViewController.`
1.  Ajouter `Sections` et `Elements` à la  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>À l’aide d’un UINavigationController

Pour créer une application de style de navigation, nous devons créer un `UINavigationController`, puis l’ajouter en tant que le `RootViewController` dans le `FinishedLaunching` méthode de la `AppDelegate`. Pour rendre le `UINavigationController` fonctionnent avec MonoTouch.Dialog, nous ajoutons un `DialogViewController` à la `UINavigationController` comme indiqué ci-dessous :

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
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

Le code ci-dessus crée une instance d’un `RootElement` et la passe le `DialogViewController`. Le `DialogViewController` a toujours un `RootElement` en haut de sa hiérarchie. Dans cet exemple, le `RootElement` est créée avec la chaîne « To Do List, » est utilisé comme titre dans la barre de navigation du contrôleur de navigation. À ce stade, l’exécution de l’application présentera l’écran ci-dessous :

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Exécution de l’application présente l’écran présenté ici")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Nous allons voir comment utiliser la structure hiérarchique de MonoTouch.Dialog de `Sections` et `Elements` pour ajouter d’autres écrans.

### <a name="creating-the-dialog-screens"></a>Créer les écrans de boîte de dialogue

Un `DialogViewController` est un `UITableViewController` sous-classe MonoTouch.Dialog utilise pour ajouter des écrans. MonoTouch.Dialog crée les écrans en ajoutant un `RootElement` à un `DialogViewController`, comme nous l’avons vu ci-dessus. Le `RootElement` peut avoir `Section` instances qui représentent les sections d’une table.
Les sections sont constituées d’éléments, les autres sections, ou même l’autre `RootElements`. Par l’imbrication `RootElements`, MonoTouch.Dialog crée automatiquement une application de navigation de style, comme nous allons le voir.

### <a name="using-dialogviewcontroller"></a>À l’aide de DialogViewController

Le `DialogViewController`, qui est un `UITableViewController` sous-classe, a un `UITableView` en tant que vue. Dans cet exemple, nous souhaitons ajouter des éléments à la table chaque fois que le <span class="ui"> + </span> l’appui sur bouton. Dans la mesure où le `DialogViewController` a été ajouté à un `UINavigationController`, nous pouvons utiliser le `NavigationItem`de `RightBarButton` propriété à ajouter le <span class="ui"> + </span> bouton, comme indiqué ci-dessous :

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Lorsque nous avons créé le `RootElement` précédemment, nous lui passé un seul `Section` instance afin que nous pourrions ajouter des éléments en tant que le <span class="ui"> + </span> l’appui sur bouton par l’utilisateur. Nous pouvons utiliser le code suivant pour accomplir ce de l’événement de gestionnaire pour le bouton :

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

Ce code crée un nouveau `Task` objet chaque fois que l’appui sur le bouton. L’exemple suivant montre l’implémentation simple de la `Task` classe :

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

La tâche `Name` propriété est utilisée pour créer le `RootElement`de légende avec une variable de compteur nommée `n` qui est incrémenté pour chaque nouvelle tâche. MonoTouch.Dialog transforme les éléments en les lignes qui sont ajoutés à la `TableView` lorsque chaque `taskElement` est ajouté.

## <a name="presenting-and-managing-dialog-screens"></a>Présentation et la gestion des écrans de boîte de dialogue

Nous avons utilisé un `RootElement` afin que MonoTouch.Dialog serait automatiquement créer un nouvel écran pour les détails de chaque tâche et y accéder lorsqu’une ligne est sélectionnée.

L’écran de détails de tâche elle-même se compose de deux sections ; chacune de ces sections contient un élément unique. Le premier élément est créé à partir d’un `EntryElement` pour fournir une ligne modifiable pour la tâche `Description` propriété. Lorsque l’élément est sélectionné, un clavier pour modifier le texte est présenté comme indiqué ci-dessous :

 [![](elements-api-walkthrough-images/03-create-task.png "Lorsque l’élément est sélectionné, un clavier pour modifier le texte est présenté comme indiqué")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La deuxième section contient un `DateElement` qui nous permet de gérer la tâche `DueDate` propriété. Sélection automatique de la date de charge un sélecteur de dates comme indiqué :

 [![](elements-api-walkthrough-images/04-date-picker.png "Sélectionner la date automatiquement charge un sélecteur de dates en tant que")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

À la fois dans le `EntryElement` et `DateElement` cas (ou pour tout élément de saisie de données dans MonoTouch.Dialog), les modifications apportées aux valeurs sont automatiquement conservées. Nous pouvons illustrer cela en modifiant la date et en naviguant ensuite dans les deux sens entre l’écran de la racine et divers détails de la tâche, où les valeurs dans les écrans de détails sont conservées.

## <a name="summary"></a>Récapitulatif

Cet article a présenté une procédure pas à pas qui vous a montré comment utiliser l’API d’éléments MonoTouch.Dialog. Il décrit les étapes de base pour créer une application multi-écran avec le serveur cible maître. D, y compris comment utiliser un `DialogViewController` et comment ajouter des éléments et des Sections pour créer des écrans. En outre, il vous a montré comment utiliser le serveur cible maître. D conjointement avec un `UINavigationController`.

## <a name="related-links"></a>Liens connexes

- [MTDWalkthrough (sample)](https://developer.xamarin.com/samples/monotouch/MTDWalkthrough/)
- [Introduction à MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procédure pas à pas API de réflexion](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procédure pas à pas JSON élément](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Boîte de dialogue MonoTouch sur Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de classe de UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de classe de UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
