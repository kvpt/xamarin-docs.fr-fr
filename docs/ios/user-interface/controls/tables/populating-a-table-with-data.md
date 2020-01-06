---
title: Remplissage d’une table avec des données dans Xamarin. iOS
description: Ce document explique comment remplir une table avec des données dans une application Xamarin. iOS. Il traite des UITableViewSource, de la réutilisation des cellules, de l’ajout d’un index, des en-têtes et des pieds de page.
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 954273907fea1c57ca9d2aaac5ceb7d81115f553
ms.sourcegitcommit: 57b98c3c818a77cae94749665878d155ad031ff9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75500331"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>Remplissage d’une table avec des données dans Xamarin. iOS

Pour ajouter des lignes à un `UITableView` vous devez implémenter une sous-classe `UITableViewSource` et substituer les méthodes que la vue de table appelle pour s’alimenter elle-même.

Ce guide couvre les sujets suivants :

- Sous-classement d’un UITableViewSource
- Réutilisation des cellules
- Ajout d’un index
- Ajout d’en-têtes et de pieds de page

<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Sous-classe UITableViewSource

Une sous-classe `UITableViewSource` est assignée à chaque `UITableView`. La vue table interroge la classe source pour déterminer comment se restituer elle-même (par exemple, le nombre de lignes requises et la hauteur de chaque ligne si elle est différente de la valeur par défaut). Plus important encore, la source fournit chaque vue de cellule remplie avec des données.

Il n’y a que deux méthodes obligatoires requises pour créer une table à partir de données :

- **RowsInSection** : retourne un nombre [`nint`](~/cross-platform/macios/nativetypes.md) du nombre total de lignes de données que la table doit afficher.
- **GetCell** : retourne une `UITableCellView` remplie avec des données pour l’index de ligne correspondant passé à la méthode.

L’exemple de fichier BasicTable **TableSource.cs** a l’implémentation la plus simple possible de `UITableViewSource`. Dans l’extrait de code ci-dessous, vous pouvez voir qu’il accepte un tableau de chaînes à afficher dans la table et qu’il retourne un style de cellule par défaut contenant chaque chaîne :

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //if there are no cells to reuse, create a new one
            if (cell == null){ 
                cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); 
            }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

Une `UITableViewSource` peut utiliser n’importe quelle structure de données, à partir d’un tableau de chaînes simple (comme indiqué dans cet exemple) à une liste < > ou d’une autre collection. L’implémentation de `UITableViewSource` méthodes isole la table de la structure de données sous-jacente.

Pour utiliser cette sous-classe, créez un tableau de chaînes pour construire la source, puis affectez-la à une instance de `UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

La table résultante ressemble à ceci :

 [![](populating-a-table-with-data-images/image3.png "Sample table running")](populating-a-table-with-data-images/image3.png#lightbox)

La plupart des tables permettent à l’utilisateur de toucher une ligne pour la sélectionner et d’effectuer une autre action (par exemple, la diffusion d’une chanson, l’appel d’un contact ou l’affichage d’un autre écran). Pour ce faire, vous devez effectuer quelques opérations. Tout d’abord, nous allons créer un AlertController pour afficher un message lorsque l’utilisateur clique sur une ligne en ajoutant le code suivant à la méthode `RowSelected` :

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

Ensuite, créez une instance de notre contrôleur d’affichage :

```csharp
HomeScreen owner;
```

Ajoutez un constructeur à votre classe UITableViewSource qui prend un contrôleur d’affichage comme paramètre et l’enregistre dans un champ :

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```

Modifiez la méthode ViewDidLoad où la classe UITableViewSource est créée pour transmettre la référence `this` :

```csharp
table.Source = new TableSource(tableItems, this);
```

Enfin, dans votre méthode `RowSelected`, appelez `PresentViewController` sur le champ mis en cache :

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```

L’utilisateur peut maintenant toucher une ligne et une alerte s’affiche :

 [![](populating-a-table-with-data-images/image4.png "The row selected alert")](populating-a-table-with-data-images/image4.png#lightbox)

## <a name="cell-reuse"></a>Réutilisation des cellules

Dans cet exemple, il n’y a que six éléments, donc aucune réutilisation de cellule n’est requise. Toutefois, lors de l’affichage de centaines ou de milliers de lignes, il s’agit d’un gaspillage de mémoire pour créer des centaines ou des milliers d’objets `UITableViewCell` lorsque seuls quelques-uns sont adaptés à l’écran à la fois.

Pour éviter cette situation, lorsqu’une cellule disparaît de l’écran, sa vue est placée dans une file d’attente en vue de sa réutilisation. Lorsque l’utilisateur fait défiler, la table appelle `GetCell` pour demander de nouvelles vues à afficher – pour réutiliser une cellule existante (qui n’est pas actuellement affichée), il suffit d’appeler la méthode `DequeueReusableCell`. Si une cellule peut être réutilisée, elle est retournée, sinon une valeur null est retournée et votre code doit créer une nouvelle instance de cellule.

Cet extrait de code de l’exemple illustre le modèle :

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

Le `cellIdentifier` crée effectivement des files d’attente distinctes pour différents types de cellules. Dans cet exemple, toutes les cellules ont la même apparence pour qu’un seul identificateur codé en dur soit utilisé. S’il existe différents types de cellules, ils doivent chacun avoir une chaîne d’identificateur différente, à la fois lorsqu’ils sont instanciés et lorsqu’ils sont demandés à partir de la file d’attente de réutilisation.

### <a name="cell-reuse-in-ios-6"></a>Réutilisation de la cellule dans iOS 6 +

iOS 6 a ajouté un modèle de réutilisation des cellules semblable à l’introduction avec les vues de collection. Bien que le modèle de réutilisation existant indiqué ci-dessus soit toujours pris en charge à des fins de compatibilité descendante, ce nouveau modèle est préférable, car il supprime la nécessité de vérifier la valeur null dans la cellule.

Avec le nouveau modèle, une application inscrit la classe de cellule ou XIB à utiliser en appelant `RegisterClassForCellReuse` ou `RegisterNibForCellReuse` dans le constructeur du contrôleur. Ensuite, lors de la mise en file d’attente de la cellule dans la méthode `GetCell`, appelez simplement `DequeueReusableCell` en passant l’identificateur que vous avez enregistré pour la classe Cell ou XIB et le chemin d’accès de l’index.

Par exemple, le code suivant inscrit une classe de cellule personnalisée dans un UITableViewController :

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

Une fois la classe MyCell inscrite, la cellule peut être déplacée dans la file d’attente de la méthode `GetCell` du `UITableViewSource` sans avoir besoin de la vérification des valeurs NULL supplémentaires, comme indiqué ci-dessous :

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

Sachez que lorsque vous utilisez le nouveau modèle de réutilisation avec une classe de cellule personnalisée, vous devez implémenter le constructeur qui prend un `IntPtr`, comme indiqué dans l’extrait de code ci-dessous, sinon objective-C ne peut pas construire une instance de la classe de cellule :

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

Vous pouvez voir des exemples des rubriques expliquées ci-dessus dans l’exemple **BasicTable** lié à cet article.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Ajout d’un index

Un index aide l’utilisateur à faire défiler les longues listes, généralement classées par ordre alphabétique, bien que vous puissiez indexer selon les critères de votre choix. L’exemple **BasicTableIndex** charge une liste plus longue d’éléments à partir d’un fichier pour illustrer l’index. Chaque élément de l’index correspond à une « section » de la table.

 [![](populating-a-table-with-data-images/image5.png "The Index display")](populating-a-table-with-data-images/image5.png#lightbox)

Pour prendre en charge les « sections », les données de la table doivent être regroupées, de sorte que l’exemple BasicTableIndex crée un `Dictionary<>` à partir du tableau de chaînes en utilisant la première lettre de chaque élément comme clé de dictionnaire :

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

La sous-classe `UITableViewSource` nécessite alors l’ajout ou la modification des méthodes suivantes pour utiliser la `Dictionary<>` :

- **NumberOfSections** : cette méthode est facultative. par défaut, le tableau suppose une section. Lorsque vous affichez un index, cette méthode doit retourner le nombre d’éléments dans l’index (par exemple, 26 si l’index contient toutes les lettres de l’alphabet anglais).
- **RowsInSection** : retourne le nombre de lignes dans une section donnée.
- **SectionIndexTitles** : retourne le tableau de chaînes qui sera utilisé pour afficher l’index. L’exemple de code retourne un tableau de lettres.

Les méthodes mises à jour dans l’exemple de fichier **BasicTableIndex/TableSource. cs** se présentent comme suit :

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

Les index sont généralement utilisés uniquement avec le style de table simple.

<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Ajout d’en-têtes et de pieds de page

Les en-têtes et les pieds de page peuvent être utilisés pour regrouper visuellement des lignes dans une table. La structure de données requise est très similaire à l’ajout d’un index : un `Dictionary<>` fonctionne vraiment bien. Au lieu d’utiliser l’alphabet pour regrouper les cellules, cet exemple regroupe les légumes par type botanique.
La sortie ressemble à ceci :

 [![](populating-a-table-with-data-images/image6.png "Sample Headers and Footers")](populating-a-table-with-data-images/image6.png#lightbox)

Pour afficher les en-têtes et les pieds de page, la sous-classe `UITableViewSource` requiert les méthodes supplémentaires suivantes :

- **TitleForHeader** : retourne le texte à utiliser comme en-tête
- **TitleForFooter** : retourne le texte à utiliser comme pied de page.

Les méthodes mises à jour dans l’exemple de fichier **BasicTableHeaderFooter/code/TableSource. cs** se présentent comme suit :

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

Vous pouvez personnaliser davantage l’apparence de l’en-tête et du pied de page à l’aide d’un objet de vue, à l’aide des `GetViewForHeader` et `GetViewForFooter` méthode remplace sur `UITableViewSource`.

## <a name="related-links"></a>Liens associés

- [WorkingWithTables (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
