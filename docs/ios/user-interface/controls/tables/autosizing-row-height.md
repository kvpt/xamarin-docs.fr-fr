---
title: Dimensionnement automatique de la hauteur de ligne dans Xamarin. iOS
description: Ce document décrit comment ajouter à la vue table des applications Xamarin. iOS les lignes dont les hauteurs varient en fonction du contenu. Il décrit la disposition des cellules dans le concepteur iOS et l’activation de la hauteur de redimensionnement automatique.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4129370ecb465340a893e0a7f16703a08cc1db72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021930"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Dimensionnement automatique de la hauteur de ligne dans Xamarin. iOS

À compter d’iOS 8, Apple a ajouté la possibilité de créer une vue de table (`UITableView`) qui peut croître automatiquement et réduire la hauteur d’une ligne donnée en fonction de la taille de son contenu à l’aide de la disposition automatique, des classes de taille et des contraintes.

iOS 11 a ajouté la possibilité que les lignes se développent automatiquement. Les en-têtes, les pieds de page et les cellules peuvent maintenant être automatiquement dimensionnés en fonction de leur contenu. Toutefois, si votre table est créée dans le concepteur iOS, Interface Builder ou si elle a des hauteurs de lignes fixes, vous devez activer manuellement les cellules de dimensionnement automatique, comme décrit dans ce guide.

## <a name="cell-layout-in-the-ios-designer"></a>Disposition des cellules dans le concepteur iOS

Ouvrez le Storyboard de la vue de table pour laquelle vous souhaitez redimensionner automatiquement la ligne dans le concepteur iOS, sélectionnez le *prototype* de la cellule et concevez la mise en page de la cellule. Exemple :

[![](autosizing-row-height-images/table01.png "The Cell's Prototype design")](autosizing-row-height-images/table01.png#lightbox)

Pour chaque élément du prototype, ajoutez des contraintes pour conserver les éléments à la position correcte, car la vue de table est redimensionnée pour la rotation ou des tailles d’écran d’appareil iOS différentes. Par exemple, épinglez le `Title` en haut, à gauche et à droite de l' *affichage de contenu*de la cellule :

[![](autosizing-row-height-images/table02.png "Pinning the Title to the top, left and right of the Cells Content View")](autosizing-row-height-images/table02.png#lightbox)

Dans le cas de notre exemple de table, la petite `Label` (sous la `Title`) est le champ qui peut être réduit et agrandi pour augmenter ou diminuer la hauteur de ligne. Pour obtenir cet effet, ajoutez les contraintes suivantes pour épingler les valeurs gauche, droite, haut et bas de l’étiquette :

[![](autosizing-row-height-images/table03.png "These constraints to pin the left, right, top and bottom of the label")](autosizing-row-height-images/table03.png#lightbox)

Maintenant que nous avons entièrement restreint les éléments de la cellule, nous devons clarifier l’élément qui doit être étiré. Pour ce faire, définissez la priorité **content hugging Priority** et **content compression Priority** en fonction des besoins dans la section **disposition** de la panneau Propriétés :

[![](autosizing-row-height-images/table03a.png "The Layout section of the Properties Pad")](autosizing-row-height-images/table03a.png#lightbox)

Définissez l’élément que vous souhaitez développer pour avoir une valeur de priorité de hugging **inférieure** et une valeur de priorité de compression **inférieure** .

Ensuite, nous devons sélectionner le prototype de cellule et lui attribuer un **identificateur**unique :

[![](autosizing-row-height-images/table04.png "Giving the Cell Prototype a unique Identifier")](autosizing-row-height-images/table04.png#lightbox)

Dans le cas de notre exemple, `GrowCell`. Nous utiliserons cette valeur plus tard lors du remplissage de la table.

> [!IMPORTANT]
> Si votre table contient plusieurs types de cellules (**prototype**), vous devez vous assurer que chaque type a sa propre `Identifier` unique pour que le redimensionnement de ligne automatique fonctionne.

Pour chaque élément de notre prototype de cellule, assignez un **nom** pour C# l’exposer au code. Exemple :

[![](autosizing-row-height-images/table05.png "Assign a Name to expose it to C# code")](autosizing-row-height-images/table05.png#lightbox)

Ensuite, ajoutez une classe personnalisée pour le `UITableViewController`, le `UITableView` et le `UITableCell` (prototype). Exemple : 

[![](autosizing-row-height-images/table06.png "Adding a custom class for the UITableViewController, the UITableView and the UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Enfin, pour vous assurer que tout le contenu attendu s’affiche dans notre étiquette, définissez la propriété **Lines** sur `0`:

[![](autosizing-row-height-images/table06.png "The Lines property set to 0")](autosizing-row-height-images/table06a.png#lightbox)

Une fois l’interface utilisateur définie, nous allons ajouter le code pour activer le redimensionnement automatique de la hauteur de ligne.

## <a name="enabling-auto-resizing-height"></a>Activation de la hauteur de redimensionnement automatique

Dans la source de source de la vue de table (`UITableViewDatasource`) ou la source (`UITableViewSource`), lorsque nous défilerons une cellule, nous devons utiliser le `Identifier` que nous avons défini dans le concepteur. Exemple :

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

Par défaut, la vue table est définie pour la hauteur de ligne de redimensionnement automatique. Pour ce faire, la propriété `RowHeight` doit être définie sur `UITableView.AutomaticDimension`. Nous devons également définir la propriété `EstimatedRowHeight` dans notre `UITableViewController`. Exemple :

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

Avec ce code en place, lorsque l’application est exécutée, chaque ligne est réduite et agrandie en fonction de la hauteur de la dernière étiquette dans le prototype de cellule. Exemple :

[![](autosizing-row-height-images/table07.png "A sample table run")](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>Liens associés

- [GrowRowTable (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
