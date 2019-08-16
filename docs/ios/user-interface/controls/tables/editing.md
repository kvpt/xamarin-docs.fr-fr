---
title: Modification des tables avec Xamarin. iOS
description: Ce document décrit comment modifier des tables dans Xamarin. iOS. Il décrit le balayage pour supprimer, le mode édition et l’insertion de lignes.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: f82057957e76ee683e2a649fdf6c2350bf282c18
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528639"
---
# <a name="editing-tables-with-xamarinios"></a>Modification des tables avec Xamarin. iOS

Les fonctionnalités d’édition de table sont activées en substituant des méthodes dans une `UITableViewSource` sous-classe. Le comportement d’édition le plus simple est le mouvement de balayage à supprimer qui peut être implémenté avec une seule substitution de méthode.
Une modification plus complexe (y compris le déplacement de lignes) peut être effectuée à l’aide de la table en mode édition.

## <a name="swipe-to-delete"></a>Balayer pour supprimer

La fonctionnalité de balayage à supprimer est un geste naturel dans iOS que les utilisateurs attendent. 

 [![](editing-images/image10.png "Exemple de balayage à supprimer")](editing-images/image10.png#lightbox)

Il existe trois remplacements de méthode qui affectent le mouvement de balayage pour afficher un bouton **supprimer** dans une cellule:

- **CommitEditingStyle** : la source de la table détecte si cette méthode est substituée et active automatiquement le mouvement de balayage à supprimer. L’implémentation de la méthode doit `DeleteRows` appeler sur `UITableView` le pour provoquer la disparition des cellules et supprimer les données sous-jacentes de votre modèle (par exemple, un tableau, un dictionnaire ou une base de données). 
- **CanEditRow** : si CommitEditingStyle est substitué, toutes les lignes sont supposées être modifiables. Si cette méthode est implémentée et retourne false (pour certaines lignes spécifiques ou pour toutes les lignes), le mouvement de balayage à supprimer n’est pas disponible dans cette cellule. 
- **TitleForDeleteConfirmation** : spécifie éventuellement le texte du bouton **supprimer** . Si cette méthode n’est pas implémentée, le texte du bouton sera «Delete». 


Ces méthodes sont implémentées dans `TableSource` la classe suivante:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

Pour cet exemple, `UITableViewSource` le a été mis à jour `List<TableItem>` pour utiliser un (au lieu d’un tableau de chaînes) comme source de données, car il prend en charge l’ajout et la suppression d’éléments de la collection.


## <a name="edit-mode"></a>Mode édition

Quand une table est en mode édition, l’utilisateur voit un widget rouge «Stop» sur chaque ligne, ce qui affiche un bouton supprimer lorsqu’il est touché. Le tableau affiche également une icône «handle» pour indiquer que la ligne peut être glissée pour modifier l’ordre.
L’exemple **TableEditMode** implémente ces fonctionnalités comme indiqué.

 [![](editing-images/image11.png "L’exemple TableEditMode implémente ces fonctionnalités comme indiqué")](editing-images/image11.png#lightbox)

Il existe plusieurs méthodes sur `UITableViewSource` qui affectent le comportement du mode édition d’une table:

- **CanEditRow** : indique si chaque ligne peut être modifiée. Retourne false pour empêcher la suppression et la suppression en mode édition. 
- **CanMoveRow** : retourne la valeur true pour activer le déplacement’handle’ou false pour empêcher le déplacement. 
- **EditingStyleForRow** : lorsque la table est en mode édition, la valeur de retour de cette méthode détermine si la cellule affiche l’icône de suppression rouge ou l’icône d’ajout verte. Retourne `UITableViewCellEditingStyle.None` si la ligne ne doit pas être modifiable. 
- **MoveRow** : appelée lorsqu’une ligne est déplacée afin que la structure de données sous-jacente puisse être modifiée pour correspondre aux données telles qu’elles sont affichées dans la table. 


L’implémentation pour les trois premières méthodes est relativement simple, à moins que vous souhaitiez utiliser `indexPath` pour modifier le comportement de lignes spécifiques, il suffit de coder en dur les valeurs de retour pour la table entière.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

L' `MoveRow` implémentation est un peu plus compliquée, car elle doit modifier la structure de données sous-jacente pour qu’elle corresponde à la nouvelle commande. Comme les données sont implémentées en `List` tant que le code ci-dessous supprime l’élément de données à son ancien emplacement et l’insère au nouvel emplacement. Si les données ont été stockées dans une table de base de données SQLite avec une colonne «Order» (par exemple), cette méthode doit plutôt effectuer des opérations SQL pour réorganiser les nombres de cette colonne.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Enfin, pour que la table soit en mode édition, le bouton **modifier** doit appeler `SetEditing` comme suit

```csharp
table.SetEditing (true, true);
```

et lorsque l’utilisateur a terminé la modification, le bouton **terminé** doit désactiver le mode d’édition:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Style d’édition d’insertion de ligne

L’insertion de lignes à partir de la table est une interface utilisateur rare: l’exemple principal dans les applications iOS standard est l’écran **modifier le contact** . Cette capture d’écran montre le fonctionnement de la fonctionnalité d’insertion de lignes. en mode édition, une ligne supplémentaire est insérée (lorsque l’utilisateur clique dessus) insère des lignes supplémentaires dans les données. Lorsque la modification est terminée, la ligne temporaire **(Ajouter nouveau)** est supprimée.

 [![](editing-images/image12.png "Lorsque la modification est terminée, la nouvelle ligne d’ajout temporaire est supprimée")](editing-images/image12.png#lightbox)

Il existe plusieurs méthodes sur `UITableViewSource` qui affectent le comportement du mode édition d’une table. Ces méthodes ont été implémentées comme suit dans l’exemple de code:

- **EditingStyleForRow** : retourne `UITableViewCellEditingStyle.Delete` pour les lignes contenant des données et retourne `UITableViewCellEditingStyle.Insert` pour la dernière ligne (qui sera ajoutée spécifiquement pour se comporter comme un bouton d’insertion). 
- **CustomizeMoveTarget** – pendant que l’utilisateur déplace une cellule, la valeur de retour de cette méthode facultative peut remplacer son choix d’emplacement. Cela signifie que vous pouvez les empêcher de «déposer» la cellule dans certaines positions, comme dans cet exemple, afin d’éviter qu’une ligne soit déplacée après la ligne **(Ajouter nouveau)** . 
- **CanMoveRow** : retourne la valeur true pour activer le déplacement’handle’ou false pour empêcher le déplacement. Dans l’exemple, la dernière ligne a le déplacement «handle» masqué, car il est destiné au serveur en tant que bouton d’insertion uniquement. 


Nous allons également ajouter deux méthodes personnalisées pour ajouter la ligne «Insert», puis la supprimer quand elle n’est plus nécessaire. Elles sont appelées à partir des boutons **modifier** et **Terminer** :

- **WillBeginTableEditing** : lorsque le bouton **modifier** est touché, il `SetEditing` appelle pour mettre la table en mode édition. Cela déclenche la méthode WillBeginTableEditing où nous affichons la ligne **(Ajouter nouveau)** à la fin de la table pour agir en tant que «bouton d’insertion». 
- **DidFinishTableEditing** : lorsque le bouton terminé est touché `SetEditing` est appelé à nouveau pour désactiver le mode édition. L’exemple de code supprime la ligne **(Ajouter nouveau)** de la table lorsque la modification n’est plus nécessaire. 


Ces substitutions de méthode sont implémentées dans l’exemple de fichier **TableEditModeAdd/code/TableSource. cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Ces deux méthodes personnalisées sont utilisées pour ajouter et supprimer la ligne **(Ajouter nouveau)** lorsque le mode d’édition de la table est activé ou désactivé:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Enfin, ce code instancie les boutons **modifier** et **terminé** , avec des expressions lambda qui activent ou désactivent le mode édition lorsqu’ils sont touchés:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Ce modèle d’interface utilisateur d’insertion de ligne n’est pas utilisé très souvent. Toutefois `UITableView.BeginUpdates` , `EndUpdates` vous pouvez également utiliser les méthodes et pour animer l’insertion ou la suppression de cellules dans n’importe quelle table. La règle d’utilisation de ces méthodes est que la `RowsInSection` différence de valeur retournée par entre `EndUpdates` les `BeginUpdates` appels et doit correspondre au nombre net de cellules ajoutées/supprimées `DeleteRows` avec les `InsertRows` méthodes et. Si la source de source sous-jacente n’est pas modifiée pour correspondre aux insertions/suppressions sur la table, une erreur se produit.


## <a name="related-links"></a>Liens associés

- [WorkingWithTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
