---
title: Création d’une application Xamarin. iOS à l’aide de l’API de réflexion
description: Ce document décrit l’API de réflexion basée sur les attributs MonoTouch. Dialog, qui crée une interface utilisateur basée sur des classes décorées avec des attributs.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: bdbff7760e7680173c57e5fc83cecb80967c0a51
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996095"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Création d’une application Xamarin. iOS à l’aide de l’API de réflexion

MT. L’API de réflexion D permet aux classes d’être décorées avec les attributs de ce dernier. D utilise pour créer des écrans automatiquement. L’API de réflexion fournit une liaison entre ces classes et ce qui est affiché à l’écran. Bien que cette API ne fournisse pas le contrôle affiné que fait l’API Elements, elle réduit la complexité en générant automatiquement la hiérarchie des éléments en fonction de la décoration de la classe.

## <a name="setting-up-mtd"></a>Configuration de MT. E

MT. D est distribué avec Xamarin. iOS. Pour l’utiliser, cliquez avec le bouton droit sur le nœud **références** d’un projet Xamarin. iOS dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à l’assembly **MonoTouch. boîte de dialogue-1** . Ajoutez ensuite `using MonoTouch.Dialog` des instructions dans votre code source, si nécessaire.

## <a name="getting-started-with-the-reflection-api"></a>Prise en main de l’API de réflexion

L’utilisation de l’API de réflexion est aussi simple que :

1. Création d’une classe décorée avec MT. Attributs D.
1. Création d’une `BindingContext` instance, en lui passant une instance de la classe ci-dessus.
1. Création d’un `DialogViewController` , en lui passant le `BindingContext’s` `RootElement` .

Examinons un exemple pour illustrer l’utilisation de l’API de réflexion. Dans cet exemple, nous allons créer un écran d’entrée de données simple comme indiqué ci-dessous :

 [![Dans cet exemple, nous allons créer un écran d’entrée de données simple comme indiqué ici](reflection-api-walkthrough-images/01-expense-entry.png)](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Création d’une classe avec MT. Attributs D

La première chose dont nous avons besoin pour utiliser l’API de réflexion est une classe décorée avec des attributs. Ces attributs seront utilisés par MT. D en interne pour créer des objets à partir de l’API d’éléments. Par exemple, considérez la définition de classe suivante :

```csharp
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;

    [Section("Expense Details")]

    [Caption("Description")]
    [Entry]
    public string Details;

    [Checkbox]
    public bool IsApproved = true;
}
```

Entraîne la `SectionAttribute` création de sections du `UITableView` , avec l’argument de chaîne utilisé pour remplir l’en-tête de la section. Une fois qu’une section est déclarée, chaque champ qui la suit est inclus dans cette section, jusqu’à ce qu’une autre section soit déclarée.
Le type d’élément d’interface utilisateur créé pour le champ dépend du type du champ et du MT. Attribut D décoration.

Par exemple, le `Name` champ est un `string` et est décoré avec un `EntryAttribute` . Cela entraîne l’ajout d’une ligne à la table avec un champ d’entrée de texte et la légende spécifiée. De même, le `IsApproved` champ est un `bool` avec un `CheckboxAttribute` , ce qui donne une ligne de tableau avec une case à cocher à droite de la cellule de tableau. MT. D utilise le nom du champ, en ajoutant automatiquement un espace, pour créer la légende dans ce cas, puisqu’il n’est pas spécifié dans un attribut.

## <a name="adding-the-bindingcontext"></a>Ajout de l’BindingContext

Pour utiliser la `Expense` classe, nous devons créer un `BindingContext` . Un `BindingContext` est une classe qui lie les données de la classe avec attributs pour créer la hiérarchie d’éléments. Pour en créer un, il vous suffit de l’instancier et de passer une instance de la classe avec attributs au constructeur.

Par exemple, pour ajouter l’interface utilisateur que nous avons déclarée à l’aide de l’attribut dans la `Expense` classe, incluez le code suivant dans la `FinishedLaunching` méthode du `AppDelegate` :

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Ensuite, tout ce que nous devons faire pour créer l’interface utilisateur est d’ajouter le `BindingContext` à `DialogViewController` et de le définir en tant que `RootViewController` de la fenêtre, comme indiqué ci-dessous :

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{   
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    var expense = new Expense ();
    var bctx = new BindingContext (null, expense, "Create a task");
    var dvc = new DialogViewController (bctx.Root);

    window.RootViewController = dvc;
    window.MakeKeyAndVisible ();

    return true;
}
```

L’exécution de l’application entraîne désormais l’affichage de l’écran ci-dessus.

### <a name="adding-a-uinavigationcontroller"></a>Ajout d’un UINavigationController

Notez cependant que le titre « créer une tâche » que nous avons transmise à `BindingContext` n’est pas affiché. Cela est dû au fait que ne fait `DialogViewController` pas partie d’un `UINavigatonController` . Nous allons modifier le code pour ajouter un `UINavigationController` comme pour la fenêtre `RootViewController,` et ajouter `DialogViewController` en tant que racine du `UINavigationController` comme indiqué ci-dessous :

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Maintenant, lorsque nous exécutons l’application, le titre s’affiche dans la `UINavigationController’s` barre de navigation, comme le montre la capture d’écran ci-dessous :

 [![Maintenant, lorsque nous exécutons l’application, le titre s’affiche dans la barre de navigation UINavigationControllers](reflection-api-walkthrough-images/02-create-task.png)](reflection-api-walkthrough-images/02-create-task.png#lightbox)

En incluant un `UINavigationController` , nous pouvons désormais tirer parti des autres fonctionnalités de Mt. D pour laquelle la navigation est nécessaire. Par exemple, nous pouvons ajouter une énumération à la `Expense` classe pour définir la catégorie des dépenses et Mt. D crée automatiquement un écran de sélection. Pour illustrer, modifiez la `Expense` classe pour inclure un `ExpenseCategory` champ comme suit :

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}

public class Expense
{
    …

    [Caption("Category")]
    public Category ExpenseCategory;
}
```

L’exécution de l’application génère désormais une nouvelle ligne dans la table pour la catégorie, comme indiqué ci-dessous :

 [![L’exécution de l’application entraîne désormais la création d’une nouvelle ligne dans la table pour la catégorie, comme indiqué ci-dessous.](reflection-api-walkthrough-images/03-set-details.png)](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Si vous sélectionnez la ligne, l’application accède à un nouvel écran avec des lignes correspondant à l’énumération, comme indiqué ci-dessous :

 [![Si vous sélectionnez la ligne, l’application accède à un nouvel écran avec des lignes correspondant à l’énumération](reflection-api-walkthrough-images/04-set-category.png)](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>Récapitulatif

Cet article a présenté une procédure pas à pas de l’API de réflexion. Nous avons montré comment ajouter des attributs à une classe pour contrôler ce qui est affiché. Nous avons également expliqué comment utiliser un `BindingContext` pour lier les données d’une classe à la hiérarchie d’éléments créée, ainsi que l’utilisation de Mt. D avec un `UINavigationController` .

## <a name="related-links"></a>Liens connexes

- [MTDReflectionWalkthrough (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [Présentation de la boîte de dialogue MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Procédure pas à pas d’API d’éléments](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procédure pas à pas d’élément JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Boîte de dialogue MonoTouch sur GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de la classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de la classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
