---
title: Utilisation des tableaux dans le concepteur iOS
description: Dans les sections précédentes, nous avons exploré le développement à l’aide de tables. Dans ce cas, la cinquième et dernière section, nous regroupons ce que nous avons appris jusqu’à présent et créons une application de liste de tâches de base à l’aide d’un Storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 58e57f9406642a3bb0ff108bffa89d77c3f2cebb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291375"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Utilisation des tableaux dans le concepteur iOS

Les storyboards sont un moyen WYSIWYG de créer des applications iOS et sont pris en charge dans Visual Studio sur Mac et Windows. Pour plus d’informations sur les storyboards, reportez-vous au document [Présentation des storyboards](~/ios/user-interface/storyboards/index.md) . Les storyboards vous permettent également de modifier les dispositions *des cellules dans* le tableau, ce qui simplifie le développement avec des tables et des cellules

Quand vous configurez les propriétés d’une vue de table dans le concepteur iOS, vous pouvez choisir entre deux types de contenu de cellule : Contenu de prototype **dynamique** ou **statique** .

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Contenu de prototype dynamique

Une `UITableView` avec un contenu de prototype est généralement destiné à afficher une liste de données dans laquelle la cellule du prototype (ou les cellules, comme vous pouvez en définir plusieurs) sont réutilisées pour chaque élément de la liste. Les cellules n’ont pas besoin d’être instanciées, elles sont obtenues `GetView` dans la méthode en `DequeueReusableCell` appelant la méthode `UITableViewSource`de son.

 <a name="Static_Content" />


## <a name="static-content"></a>Contenu statique

`UITableView`les s avec du contenu statique permettent de concevoir des tables directement sur l’aire de conception. Vous pouvez faire glisser des cellules dans la table et les personnaliser en modifiant les propriétés et en ajoutant des contrôles.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Création d’une application pilotée par Storyboard

L’exemple StoryboardTable contient une simple application maître/détail qui utilise les deux types de UITableView dans une table de montage séquentiel. Le reste de cette section décrit comment créer un exemple de liste de tâches de petite taille qui ressemble à ceci quand vous effectuez l’opération :

 [![Exemples d’écrans](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

L’interface utilisateur est générée avec un Storyboard, et les deux écrans utilisent un UITableView. L’écran principal utilise le *contenu du prototype* pour mettre la ligne en page, et l’écran de détails utilise du *contenu statique* pour créer un formulaire de saisie de données à l’aide de dispositions de cellules personnalisées.

## <a name="walkthrough"></a>Procédure pas à pas

Créer une nouvelle solution dans Visual Studio à l’aide de **(créer) un nouveau projet... > Application avec affichage uniqueC#()** et appelez-la _StoryboardTables_.

 [![Boîte de dialogue créer un nouveau projet](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

La solution s’ouvre avec certains C# fichiers et un `Main.storyboard` fichier a déjà été créé. Double-cliquez sur `Main.storyboard` le fichier pour l’ouvrir dans le concepteur iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modification de la table de montage séquentiel

Le Storyboard sera modifié en trois étapes :

- Tout d’abord, mettez en page les contrôleurs d’affichage requis et définissez leurs propriétés.
- Ensuite, créez votre interface utilisateur en faisant glisser et en déposant des objets sur votre affichage
- Enfin, ajoutez la classe UIKit requise à chaque vue et donnez un nom à différents contrôles afin qu’ils puissent être référencés dans le code.


Une fois la table de montage séquentiel terminée, du code peut être ajouté pour faire tout le travail.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Mettre en page les contrôleurs d’affichage

La première modification apportée à la table de montage séquentiel consiste à supprimer la vue détaillée existante et à la remplacer par un UITableViewController. Procédez comme suit :

1. Sélectionnez la barre en bas du contrôleur d’affichage et supprimez-le.
2. Faites glisser un contrôleur de **navigation** et un **contrôleur d’affichage de table** sur le Storyboard à partir de la boîte à outils. 
3. Créez un segue à partir du contrôleur d’affichage racine vers le deuxième contrôleur d’affichage de table qui vient d’être ajouté. Pour créer le Segue, vous pouvez le faire glisser à *partir de la cellule de détail* vers le UITableViewController que vous venez d’ajouter. Choisissez l’option **Afficher** sous **segue Selection**. 
4. Sélectionnez le nouveau segue que vous avez créé et donnez-lui un identificateur pour référencer ce segue dans le code. Cliquez sur segue et entrez `TaskSegue` pour l' **identificateur** dans le **panneau Propriétés**, comme suit :    
  [![Attribution d’un nom à segue dans le panneau des propriétés](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Ensuite, configurez les deux vues de table en les sélectionnant et en utilisant le Panneau Propriétés. Veillez à sélectionner Afficher et ne pas afficher le contrôleur : vous pouvez utiliser la structure du document pour faciliter la sélection.

6. Remplacez le contrôleur d’affichage racine par **le contenu : Prototypes** dynamiques (la vue sur le aire de conception sera intitulée **contenu du prototype** ) :

    [![Définition de la propriété de contenu sur les prototypes dynamiques](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. Remplacez le nouveau **UITableViewController** par **content : Cellules**statiques. 


8. Le nom de classe et l’identificateur de la nouvelle UITableViewController doivent être définis. Sélectionnez le contrôleur d’affichage et tapez _TaskDetailViewController_ pour la **classe** dans le **panneau Propriétés** – cela créera un `TaskDetailViewController.cs` nouveau fichier dans le panneau solutions. Entrez le **StoryboardID** en tant que _détail_, comme illustré dans l’exemple ci-dessous. Ce sera utilisé ultérieurement pour charger cette vue dans C# le code :  

    [![Définition de l’ID de Storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. L’aire de conception de la table de montage séquentiel doit maintenant ressembler à ceci (le titre de l’élément de navigation du contrôleur d’affichage racine a été remplacé par « damier ») :

    [![Aire de conception](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Créer l'interface utilisateur

Maintenant que les vues et les SEGUES sont configurées, les éléments de l’interface utilisateur doivent être ajoutés.

#### <a name="root-view-controller"></a>Contrôleur d’affichage racine

Tout d’abord, sélectionnez la cellule prototype dans le contrôleur d’affichage maître et définissez l' **identificateur** en tant que _taskcell_, comme illustré ci-dessous. Cela sera utilisé ultérieurement dans le code pour récupérer une instance de ce UITableViewCell :

 [![définition de l’identificateur de cellule](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Ensuite, vous devez créer un bouton qui ajoutera de nouvelles tâches, comme illustré ci-dessous :

[![élément de bouton de barre dans la barre de navigation](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Effectuez ce qui suit : 

- Faites glisser un **élément de bouton de barre** de la boîte à outils vers le _côté droit de la barre de navigation_.
- Dans le **panneau Propriétés**, sous l' **élément bouton** de **la barre, sélectionnez Identificateur : Ajoutez** (pour en faire un *+* bouton plus). 
- Donnez-lui un nom afin qu’il puisse être identifié dans le code à un moment ultérieur. Notez que vous devrez donner au contrôleur d’affichage racine un nom de classe (par exemple, **ItemViewController**) pour vous permettre de définir le nom de l’élément de bouton de barre.


#### <a name="taskdetail-view-controller"></a>Contrôleur d’affichage TaskDetail

La vue détaillée nécessite beaucoup plus de travail. Les cellules de l’affichage de tableau doivent être déplacées sur la vue, puis remplies avec des étiquettes, des affichages de texte et des boutons. La capture d’écran ci-dessous montre l’interface utilisateur finie avec deux sections. Une section comporte trois cellules, trois étiquettes, deux champs de texte et un commutateur, tandis que la deuxième section contient une cellule avec deux boutons :

 [![disposition de vue détaillée](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

Les étapes permettant de générer la disposition complète sont les suivantes :

Sélectionnez la vue de table et ouvrez le panneau des **Propriétés**. Mettez à jour les propriétés suivantes :

- **Sections**: _2_ 
- **Style**: _Regroupés_
- **Séparateur**: _None_
- **Sélection**: _Aucune sélection_

Sélectionnez la section supérieure, puis sous **propriétés > section vue** de la table, modifiez les **lignes** en _3_, comme illustré ci-dessous :


 [![définition de la section supérieure sur trois lignes](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Pour chaque cellule, ouvrez le **panneau Propriétés** et définissez :

- **Style**:  _Personnalisée_
- **Identificateur** : Choisissez un identificateur unique pour chaque cellule (par exemple, «_title_», «_Notes_», «_done_»).
- Faites glisser les contrôles requis pour produire la disposition indiquée dans la capture d’écran (placez **UILabel**, **champ UITextField** et **UISwitch** sur les cellules appropriées, puis définissez les étiquettes de manière appropriée, IE. Titre, remarques et terminées).


Dans la deuxième section, définissez **lignes** sur _1_ et saisissez la poignée de redimensionnement inférieure de la cellule pour la rendre plus grande.

- **Définissez l’identificateur**: sur une valeur unique (par exemple, « Enregistrer »). 
- **Définissez l’arrière-plan**:  _Effacer la couleur_ .
- Faites glisser deux boutons sur la cellule et définissez leurs titres de manière appropriée (par exemple, _Save_ et _Delete_), comme illustré ci-dessous :

   [![définition de deux boutons dans la section inférieure](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

À ce stade, vous pouvez également définir des contraintes sur vos cellules et contrôles pour garantir une disposition adaptative.

### <a name="adding-uikit-class-and-naming-controls"></a>Ajout de la classe UIKit et des contrôles d’attribution de noms

Il existe quelques étapes finales pour créer notre Storyboard. Tout d’abord, nous devons attribuer à chacun de nos contrôles un nom sous **identity > Name** , afin qu’ils puissent être utilisés ultérieurement dans le code. Nommez-les comme suit :

- **Titre champ UITextField** : _Text_
- **Notes champ UITextField** : _NotesText_
- **UISwitch** : _DoneSwitch_
- **Supprimer UIButton** : _DeleteButton_
- **Enregistrer UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Ajout de code

Le reste du travail sera effectué dans Visual Studio sur Mac ou Windows avec C#. Notez que les noms de propriété utilisés dans le code reflètent ceux définis dans la procédure pas à pas ci-dessus.

Tout d’abord, nous souhaitons `Chores` créer une classe, qui permet d’obtenir et de définir la valeur d’ID, de nom, de notes et la valeur booléenne Done, afin que nous puissions utiliser ces valeurs dans toute l’application.

Dans votre `Chores` classe, ajoutez le code suivant :

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Ensuite, créez une `RootTableSource` classe qui hérite de `UITableViewSource`. 

La différence entre cette vue de table non-Storyboard est que la méthode `GetView` n’a pas besoin d’instancier de `theDequeueReusableCell` cellules. la méthode retourne toujours une instance de la cellule prototype (avec l’identificateur correspondant).

Le code ci-dessous `RootTableSource.cs` provient du fichier :

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

Pour utiliser la `RootTableSource` classe, créez une nouvelle collection dans le `ItemViewController`constructeur de :

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

Dans `ViewWillAppear` , transmettez la collection à la source et assignez-la à la vue table :

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Si vous exécutez l’application maintenant, l’écran principal se chargera à présent de charger et d’afficher une liste de deux tâches. Quand une tâche est touchée, le segue défini par le Storyboard entraîne l’affichage de l’écran de détails, mais elle n’affiche aucune donnée pour le moment.

Pour « envoyer un paramètre » dans un Segue, substituez la `PrepareForSegue` méthode et définissez les propriétés `DestinationViewController` sur ( `TaskDetailViewController` dans cet exemple). La classe de contrôleur d’affichage de destination a été instanciée mais n’est pas encore affichée à l’utilisateur, ce qui signifie que vous pouvez définir des propriétés sur la classe, mais pas modifier les contrôles d’interface utilisateur :

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

Dans `TaskDetailViewController` , `SetTask` la méthode affecte ses paramètres aux propriétés afin qu’elles puissent être référencées dans ViewWillAppear. Les propriétés du contrôle ne peuvent pas `SetTask` être modifiées dans, car `PrepareForSegue` peut ne pas exister lorsque est appelé :

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

Le segue ouvre alors l’écran de détails et affiche les informations sur la tâche sélectionnée. Malheureusement, il n’existe aucune implémentation pour les boutons **Enregistrer** et **supprimer** . Avant d’implémenter les boutons, ajoutez ces méthodes à **ItemViewController.cs** pour mettre à jour les données sous-jacentes et fermer l’écran de détails :

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

Ensuite, vous devez ajouter le gestionnaire d’événements du `TouchUpInside` bouton à la `ViewDidLoad` méthode de **TaskDetailViewController.cs**. La `Delegate` référence `SaveTask` `DeleteTask`de propriété à aétécrééespécifiquementpourquenouspuissionsappeleret,cequiapoureffetdefermercettevuedanslecadredesonfonctionnement:`ItemViewController`

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

Le dernier élément de fonctionnalité restant à générer est la création de nouvelles tâches. Dans **ItemViewController.cs** , ajoutez une méthode qui crée des tâches et ouvre la vue détaillée. Pour instancier une vue à partir d’une `InstantiateViewController` table de montage `Identifier` séquentiel, utilisez la méthode avec le pour cette vue, dans cet exemple, qui sera « detail » :

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

Enfin, connectez le bouton dans la barre de navigation dela `ViewDidLoad` méthode de ItemViewController.cs pour l’appeler :

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Cela termine l’exemple de Storyboard : l’application terminée ressemble à ceci :

[![Application terminée](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

L’exemple montre :

- Création d’une table avec du contenu de prototype dans laquelle les cellules sont définies pour être réutilisées pour afficher des listes de données. 
- Création d’une table avec un contenu statique pour créer un formulaire d’entrée. Cela inclut la modification du style de tableau et l’ajout de sections, de cellules et de contrôles d’interface utilisateur. 
- Comment créer un segue et substituer la `PrepareForSegue` méthode pour notifier la vue cible des paramètres dont elle a besoin. 
- Chargement direct des affichages de `Storyboard.InstantiateViewController` plan conceptuel avec la méthode.



## <a name="related-links"></a>Liens associés

- [StoryboardTable (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
