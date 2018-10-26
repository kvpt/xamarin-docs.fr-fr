---
title: Utilisation des Actions de ligne dans Xamarin.iOS
description: Ce guide explique comment créer des actions de balayage personnalisée pour les lignes de la table avec UISwipeActionsConfiguration ou UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105907"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Utilisation des Actions de ligne dans Xamarin.iOS

_Ce guide explique comment créer des actions de balayage personnalisée pour les lignes de la table avec UISwipeActionsConfiguration ou UITableViewRowAction_

![Actions de balayage sur les lignes de démonstration](row-action-images/action02.png)

iOS fournit deux façons d’effectuer des actions sur une table : `UISwipeActionsConfiguration` et `UITableViewRowAction`.

`UISwipeActionsConfiguration` a été introduit dans iOS 11 et est utilisé pour définir un ensemble d’actions qui devraient placer quand les où les balayages utilisateur _dans les deux sens_ sur une ligne dans un tableau. Ce comportement est semblable à celle de la Mail.app natif 

Le `UITableViewRowAction` classe est utilisée pour définir une action qui a lieu quand l’où les balayages utilisateur restant horizontalement sur une ligne dans un tableau.
Par exemple, lorsque modification d’une table, en faisant glisser vers la gauche sur une ligne affiche un **supprimer** bouton par défaut. En joignant plusieurs instances de la `UITableViewRowAction` classe pour un `UITableView`, plusieurs actions personnalisées peuvent être définies, chacune avec son propre texte, mise en forme et son comportement.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Il existe un trois étapes requises pour implémenter des actions de balayage avec `UISwipeActionsConfiguration`:

1. Substituer `GetLeadingSwipeActionsConfiguration` et/ou `GetTrailingSwipeActionsConfiguration` méthodes. Ces méthodes retournent un `UISwipeActionsConfiguration`. 
2. Instanciez le `UISwipeActionsConfiguration` à retourner. Cette classe prend un tableau de `UIContextualAction`.
3. Créer un `UIContextualAction`.

Ils sont expliqués plus en détail dans les sections suivantes.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implémentation des méthodes SwipeActionsConfigurations

`UITableViewController` (et également `UITableViewSource` et `UITableViewDelegate`) contient deux méthodes : `GetLeadingSwipeActionsConfiguration` et `GetTrailingSwipeActionsConfiguration`, qui permettent d’implémenter un ensemble d’actions de balayage sur une ligne de vue de table. Le début de balayage fait référence à un balayage à partir de la partie gauche de l’écran dans une langue de gauche à droite et de la partie droite de l’écran dans une langue de droite à gauche. 

L’exemple suivant (à partir de la [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) exemple) illustre l’implémentation de la configuration de balayage non significatif. Deux actions sont créées à partir des actions contextuelles, qui vous seront expliquées [ci-dessous](#create-uicontextualaction). Ces actions sont ensuite passées à un qui vient d’être initialisé [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), qui est utilisé comme valeur de retour.


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. Instancier un `UISwipeActionsConfiguration`

Instancier un `UISwipeActionsConfiguration` à l’aide de la `FromActions` méthode pour ajouter un nouveau tableau de `UIContextualAction`s, comme indiqué dans l’extrait de code suivant :

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

Il est important de noter que l’ordre dans lequel affichent vos actions dépend de comment ils sont passés dans votre tableau. Par exemple, le code ci-dessus de début où les balayages affiche les actions comme suit :

![Affiche les Actions de balayage sur une ligne de table](row-action-images/action03.png)

Pour de fin où les balayages, les actions seront affichera comme illustré dans l’image suivante :

![les Actions de balayage de fin affiché sur une ligne de table](row-action-images/action04.png)

Cet extrait de code utilise également la nouvelle `PerformsFirstActionWithFullSwipe` propriété. Par défaut, cette propriété est définie true, ce qui signifie que la première action dans le tableau se produit lorsqu’un utilisateur fait glisser sa entièrement sur une ligne. Si vous avez une action qui n’est pas destructive (par exemple « Delete », cela peut ne pas être le comportement idéal et vous devez par conséquent le définir sur `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Créer un `UIContextualAction`

L’action contextuelle est où vous créez en fait l’action qui s’affichera lorsque l’utilisateur passe d’une ligne de table.

Pour initialiser une action que vous devez fournir un `UIContextualActionStyle`, un titre, ainsi qu’un `UIContextualActionHandler`. Le `UIContextualActionHandler` accepte trois paramètres : une action et la vue de l’action a été affichée dans un gestionnaire d’achèvement :

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Différentes propriétés visuelles, telles que la couleur d’arrière-plan ou l’image de l’action peuvent être modifiées. L’extrait de code ci-dessus montre l’ajout d’une image à l’action et en définissant sa couleur d’arrière-plan bleu.

Une fois que les actions contextuelles ont été créées, ils peuvent utiliser pour initialiser le `UISwipeActionsConfiguration` dans le `GetLeadingSwipeActionsConfiguration` (méthode).

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Pour définir une ou plusieurs actions de ligne personnalisé pour un `UITableView`, vous devez créer une instance de la `UITableViewDelegate` classe et substituer les `EditActionsForRow` (méthode). Exemple :

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

Statiques `UITableViewRowAction.Create` méthode est utilisée pour créer un nouveau `UITableViewRowAction` qui afficheront une **Hi** bouton lorsque les où les balayages utilisateur restant horizontalement sur une ligne dans la table. Plus tard une nouvelle instance de la `TableDelegate` est créé et attaché à la `UITableView`. Exemple :

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quand le code ci-dessus est exécuté et les où les balayages utilisateur restant sur une ligne de table, le **Hi** bouton s’affichera au lieu du **supprimer** bouton qui s’affiche par défaut :

[![](row-action-images/action01.png "Le bouton Hi affiché au lieu du bouton Supprimer")](row-action-images/action01.png#lightbox)

Si l’utilisateur appuie sur le **Hi** bouton, `Hello World!` sera écrite dans la console dans Visual Studio pour Mac ou Visual Studio lors de l’application est exécutée en mode débogage.



## <a name="related-links"></a>Liens associés

- [TableSwipeActions (exemple)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (exemple)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
