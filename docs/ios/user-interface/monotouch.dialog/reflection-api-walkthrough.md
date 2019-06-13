---
title: Création d’une application Xamarin.iOS à l’aide de l’API de réflexion
description: Ce document décrit le MonoTouch.Dialog basé sur attribut API de réflexion, ce qui crée l’interface utilisateur basée sur des classes décorées avec attributs.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: fac19cd7273b5b396946fc1867049db19348c853
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039682"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Création d’une application Xamarin.iOS à l’aide de l’API de réflexion

Le serveur cible maître. API de réflexion D permet aux classes d’être décorés avec des attributs de ce serveur cible maître. D utilise pour créer des écrans automatiquement. L’API de réflexion fournit une liaison entre ces classes et ce qui est affiché sur l’écran. Bien que cette API ne fournit pas le contrôle précis qui effectue les éléments API, il réduit la complexité en création automatique de la hiérarchie d’éléments en fonction de la décoration de la classe.

## <a name="setting-up-mtd"></a>Configuration de serveur cible maître. D

SERVEUR CIBLE MAÎTRE. D est distribué avec Xamarin.iOS. Pour l’utiliser, cliquez sur le **références** nœud d’un Xamarin.iOS de projet dans Visual Studio 2017 ou Visual Studio pour Mac et ajoutez une référence à la **MonoTouch.Dialog-1** assembly. Ensuite, ajoutez `using MonoTouch.Dialog` instructions dans votre source de code en fonction des besoins.

## <a name="getting-started-with-the-reflection-api"></a>Mise en route avec l’API de réflexion

À l’aide de l’API de réflexion est aussi simple que :

1.  Création d’une classe décorée avec le serveur cible maître. Attributs de D.
1.  Création d’un `BindingContext` instance, en lui passant une instance de la classe ci-dessus. 
1.  Création d’un `DialogViewController` , en lui passant le `BindingContext’s` `RootElement` . 


Examinons un exemple pour illustrer comment utiliser l’API de réflexion. Dans cet exemple, nous allons créer un écran de saisie de données simple comme indiqué ci-dessous :

 [![](reflection-api-walkthrough-images/01-expense-entry.png "Dans cet exemple, nous allons créer un écran de saisie de données simple comme illustré ici")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Création d’une classe avec le serveur cible maître. Attributs des D

La première chose que nous devons utiliser l’API de réflexion est une classe décorée avec attributs. Ces attributs seront utilisés par le serveur cible maître. D en interne pour créer des objets à partir de l’API d’éléments. Par exemple, considérez la définition de classe suivante :

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

Le `SectionAttribute` entraîne dans les sections de la `UITableView` en cours de création, avec l’argument de chaîne utilisée pour remplir les en-tête de la section. Une fois qu’une section est déclarée, tous les champs qui le suit figureront dans cette section, jusqu'à ce qu’une autre section est déclarée.
Le type d’élément d’interface utilisateur créé pour le champ varient en fonction du type de champ et le serveur cible maître. Attribut D décorant.

Par exemple, le `Name` champ est un `string` et il est décoré avec un `EntryAttribute`. Cela entraîne une ligne ajoutée à la table avec un champ d’entrée de texte et la légende spécifiée. De même, le `IsApproved` champ est un `bool` avec un `CheckboxAttribute`, se traduisant par une ligne de table avec une case à cocher située à droite de la cellule du tableau. SERVEUR CIBLE MAÎTRE. D utilise le nom du champ, ajouter automatiquement un espace, à créer dans ce cas, la légende dans la mesure où il n’est pas spécifié dans un attribut.

## <a name="adding-the-bindingcontext"></a>Ajout de BindingContext

Pour utiliser le `Expense` (classe), nous devons créer un `BindingContext`. Un `BindingContext` est une classe qui lie les données à partir de la classe avec attributs pour créer la hiérarchie d’éléments. Pour en créer un, nous simplement instancier et passer une instance de la classe avec attributs au constructeur.

Par exemple, pour ajouter une interface utilisateur que nous avons déclaré à l’aide de l’attribut dans le `Expense` de classe, incluez le code suivant dans le `FinishedLaunching` méthode de la `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Il nous suffit pour créer l’interface utilisateur est d’ajouter le `BindingContext` à la `DialogViewController` et définissez-le comme le `RootViewController` de la fenêtre, comme indiqué ci-dessous :

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

À présent, l’application en cours d’exécution entraîne l’écran ci-dessus affiché.

### <a name="adding-a-uinavigationcontroller"></a>Ajout d’un UINavigationController

Notez toutefois que le titre « crée une tâche » que nous avons transmis à la `BindingContext` n’est pas affiché. Il s’agit, car le `DialogViewController` est ne fait pas partie d’un `UINavigatonController`. Nous allons modifier le code pour ajouter un `UINavigationController` en tant que la fenêtre `RootViewController,` et ajoutez le `DialogViewController` comme racine de la `UINavigationController` comme indiqué ci-dessous :

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Maintenant lorsque nous exécutons l’application, le titre s’affiche dans le `UINavigationController’s` barre de navigation en tant que la capture d’écran ci-dessous :

 [![](reflection-api-walkthrough-images/02-create-task.png "Maintenant, lorsque nous exécutons l’application, le titre apparaît dans la barre de navigation UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

En incluant un `UINavigationController`, nous pouvons désormais tirer parti d’autres fonctionnalités de serveur cible maître. D pour lequel la navigation est nécessaire. Par exemple, nous pouvons ajouter une énumération pour la `Expense` classe pour définir la catégorie pour les dépenses et le serveur cible maître. D créera automatiquement un écran de sélection. Pour illustrer, modifier le `Expense` classe pour inclure un `ExpenseCategory` champ comme suit :

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

Exécution de l’application maintenant des résultats dans une nouvelle ligne dans la table pour la catégorie comme indiqué :

 [![](reflection-api-walkthrough-images/03-set-details.png "Exécution de l’application maintenant des résultats dans une nouvelle ligne dans la table pour la catégorie comme indiqué")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Sélection de la ligne entraîne dans l’application accédant à un nouvel écran avec les lignes correspondant à l’énumération, comme indiqué ci-dessous :

 [![](reflection-api-walkthrough-images/04-set-category.png "Sélection de la ligne entraîne dans la navigation application vers un nouvel écran avec les lignes correspondant à l’énumération")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Récapitulatif

Cet article a présenté une procédure pas à pas de l’API de réflexion. Nous vous avons montré comment ajouter des attributs à une classe pour contrôler ce qui est affiché. Nous avons abordé également comment utiliser un `BindingContext` pour lier des données à partir d’une classe à la hiérarchie d’éléments qui est créée, ainsi que comment utiliser serveur cible maître. D avec un `UINavigationController`.


## <a name="related-links"></a>Liens connexes

- [MTDReflectionWalkthrough (sample)](https://developer.xamarin.com/samples/monotouch/MTDReflectionWalkthrough/)
- [Introduction à la boîte de dialogue MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [API de procédure pas à pas les éléments](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Procédure pas à pas JSON élément](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Boîte de dialogue MonoTouch sur Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Application de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Référence de classe de UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Référence de classe de UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
