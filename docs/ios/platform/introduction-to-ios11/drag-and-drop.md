---
title: Glisser-déplacer dans Xamarin. iOS
description: Ce document explique comment implémenter le glisser-déplacer dans les applications Xamarin. iOS à l’aide des API introduites dans iOS 11. En particulier, il aborde l’activation de la fonction glisser-déplacer dans UITableView.
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/05/2017
ms.openlocfilehash: 928936815c89dd74d0ad3775f59ea210702c8857
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032174"
---
# <a name="drag-and-drop-in-xamarinios"></a>Glisser-déplacer dans Xamarin. iOS

_Implémentation du glisser-déplacer pour iOS 11_

iOS 11 comprend la prise en charge de la fonction glisser-déplacer pour copier des données entre des applications sur l’iPad. Les utilisateurs peuvent sélectionner et faire glisser tous les types de contenu à partir d’applications positionnées côte à côte, ou en faisant glisser le pointeur sur une icône d’application qui déclenche l’ouverture de l’application et autoriser la suppression des données :

![Glisser-déplacer l’exemple de l’application personnalisée vers l’application notes](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> Le glisser-déplacer est uniquement disponible dans la même application sur iPhone.

Envisagez de prendre en charge les opérations de glisser-déplacer où le contenu peut être créé ou modifié :

- Les contrôles de texte prennent en charge le glisser-déplacer pour toutes les applications basées sur iOS 11, sans aucun travail supplémentaire.
- Les vues de table et les vues de collection incluent des améliorations dans iOS 11 qui simplifient l’ajout du comportement de glisser-déplacer.
- Toutes les autres vues peuvent être prises en charge par glisser-déplacer avec une personnalisation supplémentaire.

Lorsque vous ajoutez la prise en charge de la fonction glisser-déplacer à vos applications, vous pouvez fournir différents niveaux de fidélité du contenu. par exemple, vous pouvez fournir à la fois un texte mis en forme et une version en texte brut des données afin que l’application réceptrice puisse choisir la solution qui convient le mieux à la cible du glissement. Il est également possible de personnaliser la visualisation par glisser-déplacer et de permettre le glissement de plusieurs éléments à la fois.

## <a name="drag-and-drop-with-text-controls"></a>Glisser-déplacer avec les contrôles de texte

`UITextView` et `UITextField` prennent automatiquement en charge le glissement du texte sélectionné et la suppression du contenu de texte dans.

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>Glisser-déplacer avec UITableView

`UITableView` a intégré la gestion des interactions de glisser-déplacer avec les lignes de table, ce qui ne nécessite que quelques méthodes pour activer le comportement par défaut.

Il existe deux interfaces impliquées :

- `IUITableViewDragDelegate` : les informations de packages lorsqu’un glisser est initié dans la vue table.
- `IUITableViewDropDelegate` : traite les informations quand une opération de suppression est tentée et terminée.

Dans l' [exemple DragAndDropTableView](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview) , ces deux interfaces sont implémentées sur la classe `UITableViewController`, ainsi que le délégué et la source de données. Elles sont affectées dans la méthode `ViewDidLoad` :

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

Le code requis minimal pour ces deux interfaces est expliqué ci-dessous.

### <a name="table-view-drag-delegate"></a>Vue de table faire glisser un délégué

La seule méthode _requise_ pour prendre en charge le glissement d’une ligne à partir d’une vue de table est `GetItemsForBeginningDragSession`. Si l’utilisateur commence à faire glisser une ligne, cette méthode est appelée.

Une implémentation est illustrée ci-dessous. Il récupère les données associées à la ligne glissée, les encode et configure un `NSItemProvider` qui détermine la manière dont les applications gèrent la partie « Drop » de l’opération (par exemple, si elles peuvent gérer le type de données, `PlainText`dans l’exemple) :

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

Il existe de nombreuses méthodes facultatives sur le délégué Drag qui peuvent être implémentées pour personnaliser le comportement de glissement, par exemple pour fournir plusieurs représentations de données qui peuvent être utilisées dans des applications cibles (telles que du texte mis en forme et du texte brut, ou encore un vecteur et versions bitmap d’un dessin). Vous pouvez également fournir des représentations de données personnalisées à utiliser lors d’une opération de glisser-déplacer au sein de la même application.

### <a name="table-view-drop-delegate"></a>Liste déroulante de vue de table

Les méthodes sur le délégué de dépôt sont appelées lorsqu’une opération glisser se produit sur une vue de table ou se termine au-dessus de lui. Les méthodes requises déterminent si les données sont autorisées à être supprimées, et les actions effectuées si la suppression est terminée :

- `CanHandleDropSession` : lorsqu’une opération de glisser-déplacer est en cours et éventuellement supprimée de l’application, cette méthode détermine si les données glissées peuvent être supprimées.
- `DropSessionDidUpdate` : pendant que l’opération de glisser-déplacer est en cours, cette méthode est appelée pour déterminer l’action qui est prévue. Les informations de la vue de table qui sont glissées, la session de glissement et le chemin d’accès d’index possible peuvent toutes être utilisées pour déterminer le comportement et les commentaires visuels fournis à l’utilisateur.
- `PerformDrop` : lorsque l’utilisateur termine la suppression (en retirant son doigt), cette méthode extrait les données glissées et modifie la vue de table pour ajouter les données dans une ou de nouvelles lignes.

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` indique si la vue de table peut accepter les données glissées. Dans cet extrait de code, `CanLoadObjects` est utilisé pour confirmer que cette vue de table peut accepter des données de chaîne.

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

La méthode `DropSessionDidUpdate` est appelée à plusieurs reprises pendant que l’opération glisser est en cours, afin de fournir des signaux visuels à l’utilisateur.

Dans le code ci-dessous, `HasActiveDrag` est utilisé pour déterminer si l’opération provient de la vue de table actuelle. Dans ce cas, seules les lignes uniques sont autorisées à être déplacées.
Si le glissement provient d’une autre source, une opération de copie est indiquée :

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

L’opération Drop peut être `Cancel`, `Move`ou `Copy`.

L’intention de déposer peut être d’insérer une nouvelle ligne ou d’ajouter/ajouter des données à une ligne existante.

#### <a name="performdrop"></a>PerformDrop

La méthode `PerformDrop` est appelée lorsque l’utilisateur termine l’opération et modifie la vue table et la source de données pour refléter les données déplacées.

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

Du code supplémentaire peut être ajouté pour charger de façon asynchrone des objets de données volumineux.

### <a name="testing-drag-and-drop"></a>Test du glisser-déplacer

Vous devez utiliser un iPad pour tester l' [exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview).
Ouvrez l’exemple avec une autre application (telle que des notes) et faites glisser des lignes et du texte entre eux :

![capture d’écran de l’opération glisser en cours](drag-and-drop-images/01-sml.png)

## <a name="related-links"></a>Liens associés

- [Glisser-déplacer les instructions d’interface utilisateur (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [Exemple de vue de table glisser-déplacer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [Exemple de vue de collection glisser-déplacer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [Présentation de la fonction glisser-déplacer (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/203/)
- [Glisser-déplacer avec la vue de la collection et de la table (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/223/)
