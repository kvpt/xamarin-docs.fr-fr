---
title: Dimensionnement automatique de la hauteur de ligne dans Xamarin. iOS
description: Ce document décrit comment ajouter à la vue table des applications Xamarin. iOS les lignes dont les hauteurs varient en fonction du contenu. Il décrit la disposition des cellules dans le concepteur iOS et l’activation de la hauteur de redimensionnement automatique.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 03e6a513f367be4c19f925427c8a896b1fc87107
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996329"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Dimensionnement automatique de la hauteur de ligne dans Xamarin. iOS

Depuis iOS 8, Apple a ajouté la possibilité de créer une vue de table ( `UITableView` ) qui peut augmenter ou diminuer automatiquement la hauteur d’une ligne donnée en fonction de la taille de son contenu à l’aide de la disposition automatique, des classes de taille et des contraintes.

iOS 11 a ajouté la possibilité que les lignes se développent automatiquement. Les en-têtes, les pieds de page et les cellules peuvent maintenant être automatiquement dimensionnés en fonction de leur contenu. Toutefois, si votre table est créée dans le concepteur iOS, Interface Builder ou si elle a des hauteurs de lignes fixes, vous devez activer manuellement les cellules de dimensionnement automatique, comme décrit dans ce guide.

## <a name="cell-layout-in-the-ios-designer"></a>Disposition des cellules dans le concepteur iOS

Ouvrez le Storyboard de la vue de table pour laquelle vous souhaitez redimensionner automatiquement la ligne dans le concepteur iOS, sélectionnez le *prototype* de la cellule et concevez la mise en page de la cellule. Par exemple :

[![Conception du prototype de la cellule](autosizing-row-height-images/table01.png)](autosizing-row-height-images/table01.png#lightbox)

Pour chaque élément du prototype, ajoutez des contraintes pour conserver les éléments à la position correcte, car la vue de table est redimensionnée pour la rotation ou des tailles d’écran d’appareil iOS différentes. Par exemple, épinglez le `Title` en haut, à gauche et à droite de l' *affichage de contenu*de la cellule :

[![Épingler le titre en haut, à gauche et à droite de l’affichage de contenu des cellules](autosizing-row-height-images/table02.png)](autosizing-row-height-images/table02.png#lightbox)

Dans le cas de notre exemple de tableau, la petite taille `Label` (sous `Title` ) est le champ qui peut être réduit et agrandi pour augmenter ou diminuer la hauteur de ligne. Pour obtenir cet effet, ajoutez les contraintes suivantes pour épingler les valeurs gauche, droite, haut et bas de l’étiquette :

[![Ces contraintes pour épingler la gauche, la droite, le haut et le bas de l’étiquette](autosizing-row-height-images/table03.png)](autosizing-row-height-images/table03.png#lightbox)

Maintenant que nous avons entièrement restreint les éléments de la cellule, nous devons clarifier l’élément qui doit être étiré. Pour ce faire, définissez la priorité **content hugging Priority** et **content compression Priority** en fonction des besoins dans la section **disposition** de la panneau Propriétés :

[![La section disposition du Panneau Propriétés](autosizing-row-height-images/table03a.png)](autosizing-row-height-images/table03a.png#lightbox)

Définissez l’élément que vous souhaitez développer pour avoir une valeur de priorité de hugging **inférieure** et une valeur de priorité de compression **inférieure** .

Ensuite, nous devons sélectionner le prototype de cellule et lui attribuer un **identificateur**unique :

[![Attribution d’un identificateur unique au prototype de cellule](autosizing-row-height-images/table04.png)](autosizing-row-height-images/table04.png#lightbox)

Dans le cas de notre exemple, `GrowCell` . Nous utiliserons cette valeur plus tard lors du remplissage de la table.

> [!IMPORTANT]
> Si votre table contient plusieurs types de cellules (**prototype**), vous devez vous assurer que chaque type a sa propre valeur unique pour que le `Identifier` redimensionnement de ligne automatique fonctionne.

Pour chaque élément de notre prototype de cellule, assignez un **nom** pour l’exposer au code C#. Par exemple :

[![Assigner un nom pour l’exposer au code C#](autosizing-row-height-images/table05.png)](autosizing-row-height-images/table05.png#lightbox)

Ensuite, ajoutez une classe personnalisée pour `UITableViewController` , `UITableView` et `UITableCell` (prototype). Par exemple :

[![Ajout d’une classe personnalisée pour UITableViewController, UITableView et UITableCell](autosizing-row-height-images/table06.png)](autosizing-row-height-images/table06.png#lightbox)

Enfin, pour vous assurer que tout le contenu attendu s’affiche dans notre étiquette, définissez la propriété **Lines** sur `0` :

[![La propriété Lines a la valeur 0](autosizing-row-height-images/table06.png)](autosizing-row-height-images/table06a.png#lightbox)

Une fois l’interface utilisateur définie, nous allons ajouter le code pour activer le redimensionnement automatique de la hauteur de ligne.

## <a name="enabling-auto-resizing-height"></a>Activation de la hauteur de redimensionnement automatique

Dans la source de source de la vue de table ( `UITableViewDatasource` ) ou la source ( `UITableViewSource` ), lorsque nous défilerons une cellule, nous devons utiliser le `Identifier` que nous avons défini dans le concepteur. Par exemple :

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

Par défaut, la vue table est définie pour la hauteur de ligne de redimensionnement automatique. Pour ce faire, la `RowHeight` propriété doit avoir la valeur `UITableView.AutomaticDimension` . Nous devons également définir la `EstimatedRowHeight` propriété dans notre `UITableViewController` . Par exemple :

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

Cette estimation ne doit pas nécessairement être exacte, juste une estimation approximative de la hauteur moyenne de chaque ligne de la vue de table.

Avec ce code en place, lorsque l’application est exécutée, chaque ligne est réduite et agrandie en fonction de la hauteur de la dernière étiquette dans le prototype de cellule. Par exemple :

[![Exemple d’exécution de table](autosizing-row-height-images/table07.png)](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>Liens associés

- [GrowRowTable (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
