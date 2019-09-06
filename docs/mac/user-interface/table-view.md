---
title: Vues de table dans Xamarin. Mac
description: Cet article traite de l’utilisation des vues de table dans une application Xamarin. Mac. Il décrit la création d’affichages de tables dans Xcode et Interface Builder et l’interaction avec eux dans le code.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 758134b0c5171e46c47ff6fd8071b13a44d5789b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291619"
---
# <a name="table-views-in-xamarinmac"></a>Vues de table dans Xamarin. Mac

_Cet article traite de l’utilisation des vues de table dans une application Xamarin. Mac. Il décrit la création d’affichages de tables dans Xcode et Interface Builder et l’interaction avec eux dans le code._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes vues de table qu’un développeur qui travaille en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos vues de table (ou éventuellement les créer directement C# dans le code).

Un affichage de table affiche des données dans un format tabulaire contenant une ou plusieurs colonnes d’informations dans plusieurs lignes. En fonction du type de vue de table en cours de création, l’utilisateur peut Trier par colonne, réorganiser les colonnes, ajouter des colonnes, supprimer des colonnes ou modifier les données contenues dans la table.

[![](table-view-images/intro01.png "Exemple de table")](table-view-images/intro01.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des vues de table dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Présentation des vues de table

Un affichage de table affiche des données dans un format tabulaire contenant une ou plusieurs colonnes d’informations dans plusieurs lignes. Les vues de table sont affichées dans les vues`NSScrollView`de défilement () et à partir de MacOS 10,7, `NSView` vous pouvez utiliser n'`NSCell`importe quelle cellule à la place de () pour afficher les lignes et les colonnes. Cela dit, vous pouvez toujours utiliser `NSCell` , mais vous allez généralement sous- `NSTableCellView` classe et créer vos lignes et colonnes personnalisées.

Une vue de table ne stocke pas ses propres données. elle s’appuie plutôt sur une source de`NSTableViewDataSource`données () pour fournir à la fois les lignes et les colonnes requises, en fonction des besoins.

Le comportement d’une vue de table peut être personnalisé en fournissant une sous-classe du délégué de`NSTableViewDelegate`vue de table () pour prendre en charge la gestion des colonnes de table, le type pour sélectionner les fonctionnalités, la sélection de lignes et la modification, le suivi personnalisé et les vues personnalisées pour les colonnes individuelles et lignes.

Lorsque vous créez des vues de table, Apple suggère ce qui suit :

- Autorisez l’utilisateur à trier la table en cliquant sur un en-tête de colonne.
- Créez des en-têtes de colonnes qui sont des noms ou des expressions nominales courtes qui décrivent les données affichées dans cette colonne.

Pour plus d’informations, consultez la section [affichages du contenu](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) des [instructions relatives à l’interface humaine OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)d’Apple.

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Création et gestion des vues de table dans Xcode

Lorsque vous créez une application de cacao Xamarin. Mac, vous recevez une fenêtre vide standard par défaut. Cette fenêtre est définie dans un `.storyboard` fichier inclus automatiquement dans le projet. Pour modifier votre conception Windows, dans le **Explorateur de solutions**, double-cliquez `Main.storyboard` sur le fichier :

[![](table-view-images/edit01.png "Sélection de la table de montage séquentiel principale")](table-view-images/edit01.png#lightbox)

Cette opération ouvre la conception de la fenêtre dans le Interface Builder de Xcode :

[![](table-view-images/edit02.png "Modification de l’interface utilisateur dans Xcode")](table-view-images/edit02.png#lightbox)

Tapez `table` dans la zone **de recherche de l’inspecteur de bibliothèque** pour faciliter la recherche des contrôles d’affichage de table :

[![](table-view-images/edit03.png "Sélection d’une vue de table à partir de la bibliothèque")](table-view-images/edit03.png#lightbox)

Faites glisser un affichage de table sur le contrôleur d’affichage dans l' **éditeur d’interface**, faites-le remplir dans la zone de contenu du contrôleur d’affichage et affectez-le à l’emplacement où il se réduit et s’agrandit avec la fenêtre de l' **éditeur de contrainte**:

[![](table-view-images/edit04.png "Modification des contraintes")](table-view-images/edit04.png#lightbox)

Sélectionnez la vue de table dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l’inspecteur d' **attribut**:

[![](table-view-images/edit05.png "L’inspecteur d’attribut")](table-view-images/edit05.png#lightbox)

- **Mode de contenu** : vous permet d’utiliser des vues`NSView`() ou des`NSCell`cellules () pour afficher les données dans les lignes et les colonnes. À partir de macOS 10,7, vous devez utiliser des vues.
- **Flotter les lignes** de groupe `true`: si la vue table dessine des cellules groupées comme si elles étaient flottantes.
- **Colonnes** -définit le nombre de colonnes affichées.
- **En-têtes** - `true`si, les colonnes auront des en-têtes.
- **Réorganisation** : si `true`la valeur est, l’utilisateur peut faire glisser les colonnes de la table.
- **Redimensionnement** : `true`si la valeur est, l’utilisateur peut faire glisser des en-têtes de colonne pour redimensionner les colonnes.
- **Redimensionnement de colonne** : contrôle la manière dont la table redimensionne automatiquement les colonnes.
- **Mettre en surbrillance** : contrôle le type de mise en surbrillance de la table lorsqu’une cellule est sélectionnée.
- **Lignes de remplacement** : `true`si une autre ligne a une couleur d’arrière-plan différente.
- **Grille horizontale** -sélectionne le type de bordure dessinée entre les cellules horizontalement.
- **Quadrillage vertical** -sélectionne le type de bordure dessinée entre les cellules verticalement.
- **Couleur** de la grille-définit la couleur de bordure de la cellule.
- **Background** : définit la couleur d’arrière-plan de la cellule.
- **Sélection** : vous permet de contrôler la manière dont l’utilisateur peut sélectionner des cellules dans la table en tant que :
  - **Multiple** -si `true`, l’utilisateur peut sélectionner plusieurs lignes et colonnes.
  - **Colonne** : si `true`la colonne est, l’utilisateur peut sélectionner des colonnes.
  - **Tapez Select** -if `true`, l’utilisateur peut taper un caractère pour sélectionner une ligne.
  - **Empty** : si `true`la valeur est, l’utilisateur n’est pas obligé de sélectionner une ligne ou une colonne, la table n’autorise aucune sélection.
- **Enregistrement** automatique : nom sous lequel le format des tables est automatiquement enregistré.
- **Informations sur** la colonne `true`-si, l’ordre et la largeur des colonnes sont automatiquement enregistrés.
- **Sauts de ligne** : sélectionnez la façon dont la cellule gère les sauts de ligne.
- **Tronque la dernière ligne visible** : `true`si la cellule est tronquée dans les données ne peut pas être contenue dans ses limites.

> [!IMPORTANT]
> À moins que vous ne conserviez une application Xamarin `NSView` . Mac héritée, les vues `NSCell` de table basées sur les tables doivent être utilisées sur des vues de table basées sur. `NSCell`est considéré comme hérité et peut ne pas être pris en charge à l’avenir.

Sélectionnez une colonne de table dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l’inspecteur d' **attribut**:

[![](table-view-images/edit06.png "L’inspecteur d’attribut")](table-view-images/edit06.png#lightbox)

- **Titre** : définit le titre de la colonne.
- **Alignement** : définissez l’alignement du texte dans les cellules.
- **Police du titre** -sélectionne la police du texte d’en-tête de la cellule.
- **Clé de tri** : clé utilisée pour trier les données dans la colonne. Laissez vide si l’utilisateur ne peut pas trier cette colonne.
- **Selector** -est l' **action** utilisée pour effectuer le tri. Laissez vide si l’utilisateur ne peut pas trier cette colonne.
- **Order** : il s’agit de l’ordre de tri pour les données de colonnes.
- **Redimensionnement** -sélectionne le type de redimensionnement de la colonne.
- **Modifiable** : `true`si, l’utilisateur peut modifier des cellules dans une table basée sur des cellules.
- **Hidden** : si `true`la colonne est masquée.

Vous pouvez également redimensionner la colonne en faisant glisser sa poignée (centrée verticalement sur le côté droit de la colonne) à gauche ou à droite.

Nous allons sélectionner chaque colonne dans notre vue de table et attribuer à la première colonne un titre `Product` `Details`et la deuxième.

Sélectionnez une vue de cellule de`NSTableViewCell`table () dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](table-view-images/edit07.png "L’inspecteur d’attribut")](table-view-images/edit07.png#lightbox)

Il s’agit de toutes les propriétés d’une vue standard. Vous avez également la possibilité de redimensionner les lignes de cette colonne ici.

Sélectionnez une cellule d’affichage de table (par défaut, il `NSTextField`s’agit d’un) dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](table-view-images/edit08.png "L’inspecteur d’attribut")](table-view-images/edit08.png#lightbox)

Vous disposez de toutes les propriétés d’un champ de texte standard à définir ici. Par défaut, un champ de texte standard est utilisé pour afficher les données d’une cellule dans une colonne.

Sélectionnez une vue de cellule de`NSTableFieldCell`table () dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](table-view-images/edit09.png "L’inspecteur d’attribut")](table-view-images/edit09.png#lightbox)

Les paramètres les plus importants sont les suivants :

- **Disposition** : sélectionnez la disposition des cellules de cette colonne.
- **Utilise le mode à ligne simple** : si `true`la valeur est, la cellule est limitée à une seule ligne.
- **Largeur de la première disposition** du `true`Runtime : si la valeur est, la cellule préfèrera la largeur définie pour celle-ci (manuellement ou automatiquement) lorsqu’elle sera affichée lors de la première exécution de l’application.
- **Action** -contrôle quand l' **action** de modification est envoyée pour la cellule.
- **Comportement** : définit si une cellule est sélectionnable ou modifiable.
- **Texte enrichi** : si `true`la cellule est affichée, la cellule peut afficher du texte mis en forme et un style.
- **Undo** -si `true`, la cellule assume la responsabilité de son comportement d’annulation.

Sélectionnez la vue de cellule de`NSTableFieldCell`table () en bas d’une colonne de table dans la hiérarchie de l' **interface**:

[![](table-view-images/edit10.png "Sélection de la vue de cellule de table")](table-view-images/edit10.png#lightbox)

Cela vous permet de modifier la vue de cellule de table utilisée comme _modèle_ de base pour toutes les cellules créées pour la colonne donnée.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Ajout d’actions et de prises

Tout comme n’importe quel autre contrôle d’IU de cacao, nous devons exposer notre vue de table et ses colonnes C# et cellules au code à l’aide d' **actions** et de **prises** (selon les fonctionnalités requises).

Le processus est le même pour tous les éléments d’affichage de table que nous souhaitons exposer :

1. Basculez vers l’éditeur de l' **Assistant** et `ViewController.h` Vérifiez que le fichier est sélectionné : 

    [![](table-view-images/edit11.png "Éditeur de l’Assistant")](table-view-images/edit11.png#lightbox)
2. Sélectionnez la vue de table dans la hiérarchie de l' **interface**, cliquez dessus et faites `ViewController.h` -la glisser vers le fichier.
3. Créez un **orifice** pour la vue de la `ProductTable`table appelée : 

    [![](table-view-images/edit13.png "Configuration d’une prise")](table-view-images/edit13.png#lightbox)
4. Créez des **sorties** pour les colonnes de tables, ainsi `ProductColumn` que `DetailsColumn`les suivantes : 

    [![](table-view-images/edit14.png "Configuration d’une prise")](table-view-images/edit14.png#lightbox)
5. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser avec Xcode.

Nous allons ensuite écrire le code pour afficher des données pour la table lors de l’exécution de l’application.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Remplissage de la vue table

Avec notre vue de table conçue en Interface Builder et exposée par le biais d’une **prise**, nous C# devons ensuite créer le code pour le remplir.

Tout d’abord, nous allons créer `Product` une nouvelle classe pour stocker les informations relatives aux lignes individuelles. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `Product` pour le **nom** et cliquez sur le bouton **nouveau** :

[![](table-view-images/populate01.png "Création d’une classe vide")](table-view-images/populate01.png#lightbox)

Faites en `Product.cs` sorte que le fichier ressemble à ce qui suit :

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
    public string Title { get; set;} = "";
    public string Description { get; set;} = "";
    #endregion

    #region Constructors
    public Product ()
    {
    }

    public Product (string title, string description)
    {
      this.Title = title;
      this.Description = description;
    }
    #endregion
  }
}

```

Ensuite, nous devons créer une sous-classe de `NSTableDataSource` pour fournir les données de notre table au fur et à mesure qu’elles sont demandées. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `ProductTableDataSource` pour le **nom** et cliquez sur le bouton **nouveau** .

Modifiez le `ProductTableDataSource.cs` fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDataSource : NSTableViewDataSource
  {
    #region Public Variables
    public List<Product> Products = new List<Product>();
    #endregion

    #region Constructors
    public ProductTableDataSource ()
    {
    }
    #endregion

    #region Override Methods
    public override nint GetRowCount (NSTableView tableView)
    {
      return Products.Count;
    }
    #endregion
  }
}

```

Cette classe possède un stockage pour les éléments de la vue de table et remplace `GetRowCount` le pour retourner le nombre de lignes dans la table.

Enfin, nous devons créer une sous-classe de `NSTableDelegate` pour fournir le comportement de notre table. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `ProductTableDelegate` pour le **nom** et cliquez sur le bouton **nouveau** .

Modifiez le `ProductTableDelegate.cs` fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
  public class ProductTableDelegate: NSTableViewDelegate
  {
    #region Constants 
    private const string CellIdentifier = "ProdCell";
    #endregion

    #region Private Variables
    private ProductTableDataSource DataSource;
    #endregion

    #region Constructors
    public ProductTableDelegate (ProductTableDataSource datasource)
    {
      this.DataSource = datasource;
    }
    #endregion

    #region Override Methods
    public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
    {
      // This pattern allows you reuse existing views when they are no-longer in use.
      // If the returned view is null, you instance up a new view
      // If a non-null view is returned, you modify it enough to reflect the new data
      NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
      if (view == null) {
        view = new NSTextField ();
        view.Identifier = CellIdentifier;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = false;
      }

      // Setup view based on the column selected
      switch (tableColumn.Title) {
      case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
      case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
      }

      return view;
    }
    #endregion
  }
}
```

Lorsque nous créons une instance `ProductTableDelegate`du, nous transmettons également une instance `ProductTableDataSource` du qui fournit les données pour la table. La `GetViewForItem` méthode est chargée de retourner une vue (données) pour afficher la cellule d’une colonne et d’une ligne. Si possible, une vue existante sera réutilisée pour afficher la cellule, s’il n’est pas nécessaire de créer une nouvelle vue.

Pour remplir la table, nous allons modifier le `ViewController.cs` fichier et faire en `AwakeFromNib` sorte que la méthode ressemble à ce qui suit :

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  // Create the Product Table Data Source and populate it
  var DataSource = new ProductTableDataSource ();
  DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
  DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

  // Populate the Product Table
  ProductTable.DataSource = DataSource;
  ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Si nous exécutons l’application, les éléments suivants s’affichent :

[![](table-view-images/populate02.png "Exemple d’exécution d’application")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Tri par colonne

Permettons à l’utilisateur de trier les données de la table en cliquant sur un en-tête de colonne. Tout d’abord, double- `Main.storyboard` cliquez sur le fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez la `Product` colonne, entrez `Title` pour la **clé**de tri `compare:` , pour le **Sélecteur** et `Ascending` sélectionnez pour la **commande**:

[![](table-view-images/sort01.png "Définition de la clé de tri")](table-view-images/sort01.png#lightbox)

Sélectionnez la `Details` colonne, entrez `Description` pour la **clé**de tri `compare:` , pour le **Sélecteur** et `Ascending` sélectionnez pour la **commande**:

[![](table-view-images/sort02.png "Définition de la clé de tri")](table-view-images/sort02.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

À présent, modifions `ProductTableDataSource.cs` le fichier et ajoutez les méthodes suivantes :

```csharp
public void Sort(string key, bool ascending) {

  // Take action based on key
  switch (key) {
  case "Title":
    if (ascending) {
      Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
    } else {
      Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
    }
    break;
  case "Description":
    if (ascending) {
      Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
    } else {
      Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
    }
    break;
  }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
  // Sort the data
  if (oldDescriptors.Length > 0) {
    // Update sort
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
  } else {
    // Grab current descriptors and update sort
    NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
    Sort (tbSort[0].Key, tbSort[0].Ascending); 
  }
      
  // Refresh table
  tableView.ReloadData ();
}
```

La `Sort` méthode nous permet de trier les données dans la source de données en fonction d' `Product` un champ de classe donné, dans l’ordre croissant ou décroissant. La méthode substituée `SortDescriptorsChanged` sera appelée chaque fois que l’utilisera un clic sur un en-tête de colonne. La valeur de **clé** que nous avons définie dans Interface Builder et l’ordre de tri de cette colonne seront transmis.

Si nous exécutons l’application et que vous cliquez dans les en-têtes de colonne, les lignes sont triées selon cette colonne :

[![](table-view-images/sort03.png "Exemple d’exécution d’application")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Sélection de ligne

Si vous souhaitez autoriser l’utilisateur à sélectionner une seule ligne, double-cliquez sur le `Main.storyboard` fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez la vue de table dans la **hiérarchie d’interface** et décochez la case **multiple** dans l’inspecteur d' **attribut**:

[![](table-view-images/select01.png "L’inspecteur d’attribut")](table-view-images/select01.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.


Modifiez ensuite le `ProductTableDelegate.cs` fichier et ajoutez la méthode suivante :

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Cela permettra à l’utilisateur de sélectionner n’importe quelle ligne dans la vue table. Retournez `false` pour le `ShouldSelectRow` pour une ligne que vous ne voulez pas que l’utilisateur puisse sélectionner ou `false` pour chaque ligne si vous ne souhaitez pas que l’utilisateur puisse sélectionner des lignes.

La vue table (`NSTableView`) contient les méthodes suivantes pour l’utilisation de la sélection de lignes :

- `DeselectRow(nint)`: Désélectionne la ligne donnée dans la table.
- `SelectRow(nint,bool)`-Sélectionne la ligne donnée. Transmettez `false` le second paramètre pour sélectionner une seule ligne à la fois.
- `SelectedRow`-Retourne la ligne actuelle sélectionnée dans la table.
- `IsRowSelected(nint)`-Retourne `true` si la ligne donnée est sélectionnée.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Sélection de plusieurs lignes

Si vous souhaitez autoriser l’utilisateur à sélectionner plusieurs lignes, double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez la vue de table dans la **hiérarchie d’interface** et cochez la case **multiple** dans l’inspecteur d' **attribut**:

[![](table-view-images/select02.png "L’inspecteur d’attribut")](table-view-images/select02.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.


Modifiez ensuite le `ProductTableDelegate.cs` fichier et ajoutez la méthode suivante :

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Cela permettra à l’utilisateur de sélectionner n’importe quelle ligne dans la vue table. Retournez `false` pour le `ShouldSelectRow` pour une ligne que vous ne voulez pas que l’utilisateur puisse sélectionner ou `false` pour chaque ligne si vous ne souhaitez pas que l’utilisateur puisse sélectionner des lignes.

La vue table (`NSTableView`) contient les méthodes suivantes pour l’utilisation de la sélection de lignes :

- `DeselectAll(NSObject)`: Désélectionne toutes les lignes de la table. Utilisez `this` pour le premier paramètre à envoyer dans l’objet en cours de sélection. 
- `DeselectRow(nint)`: Désélectionne la ligne donnée dans la table.
- `SelectAll(NSobject)`: Sélectionne toutes les lignes de la table. Utilisez `this` pour le premier paramètre à envoyer dans l’objet en cours de sélection.
- `SelectRow(nint,bool)`-Sélectionne la ligne donnée. Pass `false` pour le deuxième paramètre désactivez la sélection et sélectionnez une seule ligne, `true` Pass pour étendre la sélection et inclure cette ligne.
- `SelectRows(NSIndexSet,bool)`-Sélectionne l’ensemble de lignes donné. Pass `false` pour le deuxième paramètre désactivez la sélection et sélectionnez uniquement ces lignes, `true` Pass pour étendre la sélection et inclure ces lignes.
- `SelectedRow`-Retourne la ligne actuelle sélectionnée dans la table.
- `SelectedRows`-Retourne un `NSIndexSet` contenant les index des lignes sélectionnées.
- `SelectedRowCount`-Retourne le nombre de lignes sélectionnées.
- `IsRowSelected(nint)`-Retourne `true` si la ligne donnée est sélectionnée.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tapez pour sélectionner la ligne

Si vous souhaitez autoriser l’utilisateur à taper un caractère avec la vue de table sélectionnée et à sélectionner la première ligne qui contient ce caractère, double-cliquez `Main.storyboard` sur le fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez la vue de table dans la hiérarchie de l' **interface** et cochez la case **type sélectionner** dans l' **inspecteur d’attribut**:

[![](table-view-images/type01.png "Définition du type de sélection")](table-view-images/type01.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

À présent, modifions `ProductTableDelegate.cs` le fichier et ajoutez la méthode suivante :

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
  nint row = 0;
  foreach(Product product in DataSource.Products) {
    if (product.Title.Contains(searchString)) return row;

    // Increment row counter
    ++row;
  }

  // If not found select the first row
  return 0;
}
```

La `GetNextTypeSelectMatch` méthode prend le donné `searchString` et retourne la ligne de la première `Product` qui contient cette chaîne `Title`.

Si nous exécutons l’application et que vous tapez un caractère, une ligne est sélectionnée :

[![](table-view-images/type02.png "Exemple d’exécution d’application")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Réorganisation des colonnes

Si vous souhaitez autoriser l’utilisateur à faire glisser les colonnes de réorganisation dans la vue table, double- `Main.storyboard` cliquez sur le fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez la vue de table dans la **hiérarchie d’interface** et cochez la case **réorganisation** dans l' **inspecteur d’attribut**:

[![](table-view-images/reorder01.png "L’inspecteur d’attribut")](table-view-images/reorder01.png#lightbox)

Si nous attribuons une valeur à la propriété **enregistrement** automatique et que vous vérifiez le champ **informations sur la colonne** , les modifications que nous apportons à la disposition de la table sont automatiquement enregistrées pour nous et restaurées lors de la prochaine exécution de l’application.

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

À présent, modifions `ProductTableDelegate.cs` le fichier et ajoutez la méthode suivante :

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

La `ShouldReorder` méthode doit retourner `true` pour toute colonne qu’il est possible de faire glisser dans le `newColumnIndex`, sinon retourner `false`;

Si nous exécutons l’application, nous pouvons faire glisser les en-têtes de colonne pour réorganiser les colonnes :

[![](table-view-images/reorder02.png "Exemple des colonnes réorganisées")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Modification des cellules

Si vous souhaitez autoriser l’utilisateur à modifier les valeurs d’une cellule donnée, modifiez le `ProductTableDelegate.cs` fichier et modifiez la `GetViewForItem` méthode comme suit :

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTextField ();
    view.Identifier = tableColumn.Title;
    view.BackgroundColor = NSColor.Clear;
    view.Bordered = false;
    view.Selectable = false;
    view.Editable = true;

    view.EditingEnded += (sender, e) => {
          
      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.Tag].Title = view.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.Tag].Description = view.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

Maintenant, si nous exécutons l’application, l’utilisateur peut modifier les cellules de la vue table :

[![](table-view-images/editing01.png "Exemple de modification d’une cellule")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Utilisation d’images dans les vues de table

Pour inclure une image en tant que partie de la cellule `NSTableView`dans un, vous devez modifier la façon dont les données sont retournées par `NSTableViewDelegate's` la méthode de `GetViewForItem` la `NSTableCellView` vue table pour utiliser `NSTextField`un au lieu du type standard. Exemple :

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();
    if (tableColumn.Title == "Product") {
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
    } else {
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
    }
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);
    view.Identifier = tableColumn.Title;
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    view.TextField.EditingEnded += (sender, e) => {

      // Take action based on type
      switch(view.Identifier) {
      case "Product":
        DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
        break;
      case "Details":
        DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
        break; 
      }
    };
  }

  // Tag view
  view.TextField.Tag = row;

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tags.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    break;
  }

  return view;
}
```

Pour plus d’informations, consultez la section [utilisation d’images avec des vues tabulaires](~/mac/app-fundamentals/image.md) de notre documentation [sur](~/mac/app-fundamentals/image.md) l’utilisation des images.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Ajout d’un bouton supprimer à une ligne

Selon les exigences de votre application, il peut arriver que vous deviez fournir un bouton d’action pour chaque ligne de la table. À titre d’exemple, nous allons développer l’exemple de vue de table créé ci-dessus pour inclure un bouton **supprimer** sur chaque ligne.

Tout d’abord, `Main.storyboard` modifiez le dans le Interface Builder de Xcode, sélectionnez la vue de table et augmentez le nombre de colonnes à trois (3). Ensuite, remplacez le **titre** de la nouvelle colonne par `Action`:

[![](table-view-images/delete01.png "Modification du nom de la colonne")](table-view-images/delete01.png#lightbox)

Enregistrez les modifications apportées à la table de montage séquentiel et revenez à Visual Studio pour Mac pour synchroniser les modifications.

Modifiez ensuite le `ViewController.cs` fichier et ajoutez la méthode publique suivante :

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

Dans le même fichier, modifiez la création du délégué de la vue de table à `ViewDidLoad` l’intérieur de la méthode comme suit :

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

À présent, modifiez `ProductTableDelegate.cs` le fichier pour inclure une connexion privée au contrôleur d’affichage et prenez le contrôleur en tant que paramètre lors de la création d’une nouvelle instance du délégué :

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
  this.Controller = controller;
  this.DataSource = datasource;
}
#endregion
```

Ensuite, ajoutez la nouvelle méthode privée suivante à la classe :

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
  // Add to view
  view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
  view.AddSubview (view.TextField);

  // Configure
  view.TextField.BackgroundColor = NSColor.Clear;
  view.TextField.Bordered = false;
  view.TextField.Selectable = false;
  view.TextField.Editable = true;

  // Wireup events
  view.TextField.EditingEnded += (sender, e) => {

    // Take action based on type
    switch (view.Identifier) {
    case "Product":
      DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
      break;
    case "Details":
      DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
      break;
    }
  };

  // Tag view
  view.TextField.Tag = row;
}
```

Cela prend toutes les configurations d’affichage de texte qui ont été effectuées précédemment dans la `GetViewForItem` méthode et les place dans un emplacement unique et pouvant être appelé (puisque la dernière colonne de la table n’inclut pas un affichage de texte mais un bouton).

Enfin, modifiez la `GetViewForItem` méthode et faites en sorte qu’elle ressemble à ce qui suit :

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

  // This pattern allows you reuse existing views when they are no-longer in use.
  // If the returned view is null, you instance up a new view
  // If a non-null view is returned, you modify it enough to reflect the new data
  NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
  if (view == null) {
    view = new NSTableCellView ();

    // Configure the view
    view.Identifier = tableColumn.Title;

    // Take action based on title
    switch (tableColumn.Title) {
    case "Product":
      view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
      view.AddSubview (view.ImageView);
      view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Details":
      view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
      ConfigureTextField (view, row);
      break;
    case "Action":
      // Create new button
      var button = new NSButton (new CGRect (0, 0, 81, 16));
      button.SetButtonType (NSButtonType.MomentaryPushIn);
      button.Title = "Delete";
      button.Tag = row;

      // Wireup events
      button.Activated += (sender, e) => {
        // Get button and product
        var btn = sender as NSButton;
        var product = DataSource.Products [(int)btn.Tag];

        // Configure alert
        var alert = new NSAlert () {
          AlertStyle = NSAlertStyle.Informational,
          InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
          MessageText = $"Delete {product.Title}?",
        };
        alert.AddButton ("Cancel");
        alert.AddButton ("Delete");
        alert.BeginSheetForResponse (Controller.View.Window, (result) => {
          // Should we delete the requested row?
          if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
          }
        });
      };

      // Add to view
      view.AddSubview (button);
      break;
    }

  }

  // Setup view based on the column selected
  switch (tableColumn.Title) {
  case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
  case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
  case "Action":
    foreach (NSView subview in view.Subviews) {
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

Examinons plus en détail plusieurs sections de ce code. Tout d’abord, si `NSTableViewCell` une nouvelle action est créée, l’action est effectuée en fonction du nom de la colonne. Pour les deux premières colonnes (**Product** et **Details**), la `ConfigureTextField` nouvelle méthode est appelée.

Pour la colonne **action** , une nouvelle `NSButton` est créée et ajoutée à la cellule sous la forme d’une sous-vue :

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

La propriété du `Tag` bouton est utilisée pour stocker le numéro de la ligne en cours de traitement. Ce nombre sera utilisé ultérieurement lorsque l’utilisateur demandera la suppression d’une ligne dans l’événement du `Activated` bouton :

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
  var product = DataSource.Products [(int)btn.Tag];

  // Configure alert
  var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
    MessageText = $"Delete {product.Title}?",
  };
  alert.AddButton ("Cancel");
  alert.AddButton ("Delete");
  alert.BeginSheetForResponse (Controller.View.Window, (result) => {
    // Should we delete the requested row?
    if (result == 1001) {
      // Remove the given row from the dataset
      DataSource.Products.RemoveAt((int)btn.Tag);
      Controller.ReloadTable ();
    }
  });
};
```

Au début du gestionnaire d’événements, nous obtenons le bouton et le produit qui se trouve sur la ligne de la table donnée. Ensuite, une alerte est présentée à l’utilisateur pour confirmer la suppression de la ligne. Si l’utilisateur choisit de supprimer la ligne, la ligne donnée est supprimée de la source de données et la table est rechargée :

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Enfin, si la cellule d’affichage de table est réutilisée au lieu d’être créée, le code suivant la configure en fonction de la colonne en cours de traitement :

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
  view.ImageView.Image = NSImage.ImageNamed ("tag.png");
  view.TextField.StringValue = DataSource.Products [(int)row].Title;
  view.TextField.Tag = row;
  break;
case "Details":
  view.TextField.StringValue = DataSource.Products [(int)row].Description;
  view.TextField.Tag = row;
  break;
case "Action":
  foreach (NSView subview in view.Subviews) {
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

Pour la colonne **action** , toutes les sous-vues sont analysées `NSButton` jusqu’à ce que le soit `Tag` trouvé, puis la propriété est mise à jour pour pointer vers la ligne actuelle.

Lorsque ces modifications sont en place, lorsque l’application est exécutée, chaque ligne a un bouton **supprimer** :

[![](table-view-images/delete02.png "Vue table avec boutons de suppression")](table-view-images/delete02.png#lightbox)

Quand l’utilisateur clique sur un bouton **supprimer** , une alerte s’affiche pour lui demander de supprimer la ligne spécifiée :

[![](table-view-images/delete03.png "Alerte de suppression de ligne")](table-view-images/delete03.png#lightbox)

Si l’utilisateur choisit supprimer, la ligne est supprimée et la table est redessinée :

[![](table-view-images/delete04.png "Table après la suppression de la ligne")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Vues de table de liaison de données

En utilisant le codage clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Vous avez également l’avantage de dissocier vos données de stockage (modèle de_données_) de votre interface utilisateur frontale (_Model-View-Controller_), ce qui vous permet de gérer plus facilement la conception d’applications plus souple.

Le codage de clé-valeur (KVC) est un mécanisme permettant d’accéder indirectement aux propriétés d’un objet, à l’aide de clés (chaînes spécialement mises en forme) pour identifier les propriétés au lieu d'`get/set`y accéder via des variables d’instance ou des méthodes d’accesseur (). En implémentant des accesseurs conformes de codage de clé-valeur dans votre application Xamarin. Mac, vous accédez à d’autres fonctionnalités macOS telles que l’observation de la valeur clé (KVO), la liaison de données, les données de base, les liaisons de cacao et la scriptabilité.

Pour plus d’informations, consultez la section [table Data Binding](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) de notre documentation sur la [liaison de données et le codage de clé-valeur](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des vues de table dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations des vues de table, comment créer et gérer des vues de table dans les Interface Builder de Xcode et comment utiliser les C# vues de table dans le code.

## <a name="related-links"></a>Liens associés

- [MacTables (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modes Plan](~/mac/user-interface/outline-view.md)
- [Listes de sources](~/mac/user-interface/source-list.md)
- [Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
