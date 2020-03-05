---
title: Utilisation des actions de ligne dans Xamarin. iOS
description: Ce guide montre comment créer des actions de balayage personnalisées pour les lignes de table avec UISwipeActionsConfiguration ou UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 8efa116a82ba021c2a723dc6ab636f54b6b5af71
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292498"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Utilisation des actions de ligne dans Xamarin. iOS

_Ce guide montre comment créer des actions de balayage personnalisées pour les lignes de table avec UISwipeActionsConfiguration ou UITableViewRowAction_

![Démonstration des actions de balayage sur les lignes](row-action-images/action02.png)

iOS offre deux façons d’effectuer des actions sur une table : `UISwipeActionsConfiguration` et `UITableViewRowAction`.

`UISwipeActionsConfiguration` a été introduite dans iOS 11 et est utilisée pour définir un ensemble d’actions qui doivent se produire lorsque l’utilisateur fait défiler _dans les deux sens_ sur une ligne d’une vue de table. Ce comportement est similaire à celui de l’application de messagerie native.

La classe `UITableViewRowAction` est utilisée pour définir une action qui se produit lorsque l’utilisateur fait défiler horizontalement la ligne d’une vue de table vers la gauche.
Par exemple, lors de la modification d’une table, le balayage à gauche d’une ligne affiche un bouton **supprimer** par défaut. En attachant plusieurs instances de la classe `UITableViewRowAction` à un `UITableView`, plusieurs actions personnalisées peuvent être définies, chacune avec son propre texte, sa mise en forme et son propre comportement.

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Trois étapes sont nécessaires pour implémenter des actions de balayage avec `UISwipeActionsConfiguration`:

1. Substituez les méthodes de `GetLeadingSwipeActionsConfiguration` et/ou de `GetTrailingSwipeActionsConfiguration`. Ces méthodes retournent un `UISwipeActionsConfiguration`.
2. Instanciez le `UISwipeActionsConfiguration` à retourner. Cette classe prend un tableau de `UIContextualAction`.
3. Créez un `UIContextualAction`.

Celles-ci sont expliquées plus en détail dans les sections suivantes.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. implémentation des méthodes SwipeActionsConfigurations

`UITableViewController` (et également `UITableViewSource` et `UITableViewDelegate`) contiennent deux méthodes : `GetLeadingSwipeActionsConfiguration` et `GetTrailingSwipeActionsConfiguration`, qui sont utilisées pour implémenter un jeu d’actions de balayage sur une ligne de la vue table. L’action de balayage de début fait référence à un balayage de la partie gauche de l’écran dans une langue de gauche à droite et de la partie droite de l’écran dans une langue de droite à gauche.

L’exemple suivant (de l’exemple [TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions) ) illustre l’implémentation de la configuration de balayage de début. Deux actions sont créées à partir des actions contextuelles, qui sont expliquées [ci-dessous](#create-uicontextualaction). Ces actions sont ensuite transmises à un [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations)récemment initialisé, qui est utilisé comme valeur de retour.

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

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. instancier un `UISwipeActionsConfiguration`

Instanciez une `UISwipeActionsConfiguration` à l’aide de la méthode `FromActions` pour ajouter un nouveau tableau de `UIContextualAction`s, comme indiqué dans l’extrait de code suivant :

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

Il est important de noter que l’ordre d’affichage des actions dépend de la façon dont elles sont transmises à votre tableau. Par exemple, le code ci-dessus pour les balayages principaux affiche les actions comme suit :

![Actions de balayage de début affichées sur une ligne de tableau](row-action-images/action03.png)

Pour les balayages de fin, les actions sont affichées comme illustré dans l’image suivante :

![Actions de balayage à droite affichées sur une ligne de tableau](row-action-images/action04.png)

Cet extrait de code utilise également la nouvelle propriété `PerformsFirstActionWithFullSwipe`. Par défaut, cette propriété a la valeur true, ce qui signifie que la première action du tableau se produit lorsqu’un utilisateur fait défiler entièrement une ligne. Si vous avez une action qui n’est pas destructrice (par exemple, « Delete », cela peut ne pas être idéal et vous devez donc la définir sur `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Créez une classe `UIContextualAction`

L’action contextuelle consiste à créer l’action qui sera affichée lorsque l’utilisateur fait glisser une ligne de tableau.

Pour initialiser une action, vous devez fournir un `UIContextualActionStyle`, un titre et un `UIContextualActionHandler`. L' `UIContextualActionHandler` prend trois paramètres : une action, la vue dans laquelle l’action a été affichée et un gestionnaire d’achèvement :

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

Diverses propriétés visuelles, telles que la couleur d’arrière-plan ou l’image de l’action, peuvent être modifiées. L’extrait de code ci-dessus montre comment ajouter une image à l’action et définir sa couleur d’arrière-plan sur bleu.

Une fois les actions contextuelles créées, elles peuvent utiliser pour initialiser la `UISwipeActionsConfiguration` dans la méthode `GetLeadingSwipeActionsConfiguration`.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Pour définir une ou plusieurs actions de ligne personnalisées pour un `UITableView`, vous devez créer une instance de la classe `UITableViewDelegate` et substituer la méthode `EditActionsForRow`. Par exemple :

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

La méthode `UITableViewRowAction.Create` statique est utilisée pour créer un `UITableViewRowAction` qui affichera un bouton **haut** lorsque l’utilisateur fait défiler horizontalement la ligne de la table vers la gauche. Plus tard, une nouvelle instance du `TableDelegate` est créée et attachée au `UITableView`. Par exemple :

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Lorsque le code ci-dessus est exécuté et que l’utilisateur fait défiler la ligne vers la gauche, le bouton **haut** s’affiche à la place du bouton **supprimer** qui s’affiche par défaut :

[![](row-action-images/action01.png "The Hi button being displayed instead of the Delete button")](row-action-images/action01.png#lightbox)

Si l’utilisateur appuie sur le bouton **AIM** , `Hello World!` est écrit sur la console dans Visual Studio pour Mac ou Visual Studio lorsque l’application est exécutée en mode débogage.

## <a name="related-links"></a>Liens connexes

- [TableSwipeActions (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
