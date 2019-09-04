---
title: Modes plan dans Xamarin. Mac
description: Cet article traite de l’utilisation des vues en mode plan dans une application Xamarin. Mac. Il décrit la création et la gestion des vues en mode plan dans Xcode et Interface Builder et leur utilisation par programmation.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 3b045d18d66463a1464e446847cb5dbfd531bbf3
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227886"
---
# <a name="outline-views-in-xamarinmac"></a>Modes plan dans Xamarin. Mac

_Cet article traite de l’utilisation des vues en mode plan dans une application Xamarin. Mac. Il décrit la création et la gestion des vues en mode plan dans Xcode et Interface Builder et leur utilisation par programmation._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes vues de plan qu’un développeur qui travaille en *objective-C* et *Xcode* . Étant donné que Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser les _Interface Builder_ de Xcode pour créer et gérer vos vues en mode plan (ou éventuellement C# les créer directement dans le code).

Un mode plan est un type de table qui permet à l’utilisateur de développer ou de réduire des lignes de données hiérarchiques. À l’instar d’une vue de table, un mode plan affiche des données pour un ensemble d’éléments connexes, avec des lignes représentant des éléments individuels et des colonnes représentant les attributs de ces éléments. Contrairement à une vue de table, les éléments d’un mode plan ne se trouvent pas dans une liste plate, ils sont organisés dans une hiérarchie, comme les fichiers et les dossiers sur un disque dur.

[![](outline-view-images/populate03.png "Exemple d’exécution d’application")](outline-view-images/populate03.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des modes plan dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Présentation des modes plan

Un mode plan est un type de table qui permet à l’utilisateur de développer ou de réduire des lignes de données hiérarchiques. À l’instar d’une vue de table, un mode plan affiche des données pour un ensemble d’éléments connexes, avec des lignes représentant des éléments individuels et des colonnes représentant les attributs de ces éléments. Contrairement à une vue de table, les éléments d’un mode plan ne se trouvent pas dans une liste plate, ils sont organisés dans une hiérarchie, comme les fichiers et les dossiers sur un disque dur.

Si un élément en mode plan contient d’autres éléments, il peut être développé ou réduit par l’utilisateur. Un élément développable affiche un triangle de publicité, qui pointe vers la droite lorsque l’élément est réduit et pointe vers le dessous lorsque l’élément est développé. Lorsque vous cliquez sur le triangle de divulgation, l’élément est développé ou réduit.

Le mode plan (`NSOutlineView`) est une sous-classe de la vue de`NSTableView`table () et, en tant que tel, hérite la majeure partie de son comportement de sa classe parente. Par conséquent, de nombreuses opérations prises en charge par une vue de table, telles que la sélection de lignes ou de colonnes, le repositionnement des colonnes en faisant glisser les en-têtes de colonne, etc., sont également prises en charge par un mode plan. Une application Xamarin. Mac contrôle ces fonctionnalités et peut configurer les paramètres de la vue plan (dans le code ou Interface Builder) pour autoriser ou interdire certaines opérations.

Un mode plan ne stocke pas ses propres données. il s’appuie plutôt sur une source de données`NSOutlineViewDataSource`() pour fournir à la fois les lignes et les colonnes requises, en fonction des besoins.

Le comportement d’un mode plan peut être personnalisé en fournissant une sous-classe du délégué en mode`NSOutlineViewDelegate`plan () pour prendre en charge la gestion des colonnes en mode plan, le type pour sélectionner les fonctionnalités, la sélection de lignes et la modification, le suivi personnalisé et les vues personnalisées pour les colonnes et lignes.

Dans la mesure où une vue en mode plan partage une grande partie de son comportement et des fonctionnalités avec une vue de table, vous souhaiterez peut-être parcourir notre documentation sur les [vues de tableau](~/mac/user-interface/table-view.md) avant de poursuivre cet article.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Création et gestion des vues en mode plan dans Xcode

Lorsque vous créez une application de cacao Xamarin. Mac, vous recevez une fenêtre vide standard par défaut. Cette fenêtre est définie dans un `.storyboard` fichier inclus automatiquement dans le projet. Pour modifier votre conception Windows, dans le **Explorateur de solutions**, double-cliquez `Main.storyboard` sur le fichier:

[![](outline-view-images/edit01.png "Sélection de la table de montage séquentiel principale")](outline-view-images/edit01.png#lightbox)

Cette opération ouvre la conception de la fenêtre dans le Interface Builder de Xcode:

[![](outline-view-images/edit02.png "Modification de l’interface utilisateur dans Xcode")](outline-view-images/edit02.png#lightbox)

Tapez `outline` dans la zone **de recherche de l’inspecteur de bibliothèque** pour faciliter la recherche des contrôles du mode plan:

[![](outline-view-images/edit03.png "Sélection d’un mode plan à partir de la bibliothèque")](outline-view-images/edit03.png#lightbox)

Faites glisser un mode plan sur le contrôleur d’affichage dans l' **éditeur d’interface**, faites-le remplir dans la zone de contenu du contrôleur d’affichage, puis affectez-le à l’emplacement où il se réduit et s’agrandit avec la fenêtre de l' **éditeur de contrainte**:

[![](outline-view-images/edit04.png "Modification des contraintes")](outline-view-images/edit04.png#lightbox)

Sélectionnez le mode plan dans la **hiérarchie** de l’interface et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](outline-view-images/edit05.png "L’inspecteur d’attribut")](outline-view-images/edit05.png#lightbox)

- **Colonne de plan** : colonne de table dans laquelle les données hiérarchiques sont affichées.
- **Enregistrement** automatique de la colonne de `true`structure: si, la colonne de structure est automatiquement enregistrée et restaurée entre les exécutions de l’application.
- **Mise en retrait** : quantité de retrait des colonnes sous un élément développé.
- La **mise en retrait suit** des `true`cellules-si, la marque de mise en retrait sera mise en retrait en même temps que les cellules.
- **Enregistrement automatique des éléments développés** - `true`si, l’état développé/réduit des éléments est automatiquement enregistré et restauré entre les exécutions de l’application.
- **Mode de contenu** : vous permet d’utiliser des vues`NSView`() ou des`NSCell`cellules () pour afficher les données dans les lignes et les colonnes. À partir de macOS 10,7, vous devez utiliser des vues.
- **Flotter les lignes** de groupe `true`: si la vue table dessine des cellules groupées comme si elles étaient flottantes.
- **Colonnes** -définit le nombre de colonnes affichées.
- **En-têtes** - `true`si, les colonnes auront des en-têtes.
- **Réorganisation** : si `true`la valeur est, l’utilisateur peut faire glisser les colonnes de la table.
- Redimensionnement: `true`si la valeur est, l’utilisateur peut faire glisser des en-têtes de colonne pour redimensionner les colonnes.
- Redimensionnement de **colonne** : contrôle la manière dont la table redimensionne automatiquement les colonnes.
- **Mettre** en surbrillance: contrôle le type de mise en surbrillance de la table lorsqu’une cellule est sélectionnée.
- **Lignes de remplacement** : `true`si une autre ligne a une couleur d’arrière-plan différente.
- **Grille horizontale** -sélectionne le type de bordure dessinée entre les cellules horizontalement.
- **Quadrillage vertical** -sélectionne le type de bordure dessinée entre les cellules verticalement.
- **Couleur** de la grille-définit la couleur de bordure de la cellule.
- **Background** : définit la couleur d’arrière-plan de la cellule.
- **Sélection** : vous permet de contrôler la manière dont l’utilisateur peut sélectionner des cellules dans la table en tant que:
  - **Multiple** -si `true`, l’utilisateur peut sélectionner plusieurs lignes et colonnes.
  - **Colonne** : si `true`la colonne est, l’utilisateur peut sélectionner des colonnes.
  - **Tapez Select** -if `true`, l’utilisateur peut taper un caractère pour sélectionner une ligne.
  - **Empty** : si `true`la valeur est, l’utilisateur n’est pas obligé de sélectionner une ligne ou une colonne, la table n’autorise aucune sélection.
- **Enregistrement** automatique: nom sous lequel le format des tables est automatiquement enregistré.
- **Informations sur** la colonne `true`-si, l’ordre et la largeur des colonnes sont automatiquement enregistrés.
- **Sauts de ligne** : sélectionnez la façon dont la cellule gère les sauts de ligne.
- **Tronque la dernière ligne visible** : `true`si la cellule est tronquée dans les données ne peut pas être contenue dans ses limites.

> [!IMPORTANT]
> À moins que vous ne conserviez une application Xamarin `NSView` . Mac héritée, les modes `NSCell` plan basés sur les vues de table doivent être utilisés sur des vues de tables basées sur. `NSCell`est considéré comme hérité et peut ne pas être pris en charge à l’avenir.

Sélectionnez une colonne de table dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l’inspecteur d' **attribut**:

[![](outline-view-images/edit06.png "L’inspecteur d’attribut")](outline-view-images/edit06.png#lightbox)

- **Titre** : définit le titre de la colonne.
- **Alignement** : définissez l’alignement du texte dans les cellules.
- **Police du titre** -sélectionne la police du texte d’en-tête de la cellule.
- **Clé de tri** : clé utilisée pour trier les données dans la colonne. Laissez vide si l’utilisateur ne peut pas trier cette colonne.
- **Selector** -est l' **action** utilisée pour effectuer le tri. Laissez vide si l’utilisateur ne peut pas trier cette colonne.
- **Order** : il s’agit de l’ordre de tri pour les données de colonnes.
- Redimensionnement-sélectionne le type de redimensionnement de la colonne.
- **Modifiable** : `true`si, l’utilisateur peut modifier des cellules dans une table basée sur des cellules.
- **Hidden** : si `true`la colonne est masquée.

Vous pouvez également redimensionner la colonne en faisant glisser sa poignée (centrée verticalement sur le côté droit de la colonne) à gauche ou à droite.

Nous allons sélectionner chaque colonne dans notre vue de table et attribuer à la première colonne un titre `Product` `Details`et la deuxième.

Sélectionnez une vue de cellule de`NSTableViewCell`table () dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](outline-view-images/edit07.png "L’inspecteur d’attribut")](outline-view-images/edit07.png#lightbox)

Il s’agit de toutes les propriétés d’une vue standard. Vous avez également la possibilité de redimensionner les lignes de cette colonne ici.

Sélectionnez une cellule d’affichage de table (par défaut, il `NSTextField`s’agit d’un) dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](outline-view-images/edit08.png "L’inspecteur d’attribut")](outline-view-images/edit08.png#lightbox)

Vous disposez de toutes les propriétés d’un champ de texte standard à définir ici. Par défaut, un champ de texte standard est utilisé pour afficher les données d’une cellule dans une colonne.

Sélectionnez une vue de cellule de`NSTableFieldCell`table () dans la **hiérarchie d’interface** et les propriétés suivantes sont disponibles dans l' **inspecteur d’attribut**:

[![](outline-view-images/edit09.png "L’inspecteur d’attribut")](outline-view-images/edit09.png#lightbox)

Les paramètres les plus importants sont les suivants:

- **Disposition** : sélectionnez la disposition des cellules de cette colonne.
- **Utilise le mode à ligne simple** : si `true`la valeur est, la cellule est limitée à une seule ligne.
- **Largeur de la première disposition** du `true`Runtime: si la valeur est, la cellule préfèrera la largeur définie pour celle-ci (manuellement ou automatiquement) lorsqu’elle sera affichée lors de la première exécution de l’application.
- **Action** -contrôle quand l' **action** de modification est envoyée pour la cellule.
- **Comportement** : définit si une cellule est sélectionnable ou modifiable.
- **Texte enrichi** : si `true`la cellule est affichée, la cellule peut afficher du texte mis en forme et un style.
- **Undo** -si `true`, la cellule assume la responsabilité de son comportement d’annulation.

Sélectionnez la vue de cellule de`NSTableFieldCell`table () en bas d’une colonne de table dans la hiérarchie de l' **interface**:

[![](outline-view-images/edit11.png "Sélection de la vue de cellule de table")](outline-view-images/edit10.png#lightbox)

Cela vous permet de modifier la vue de cellule de table utilisée comme _modèle_ de base pour toutes les cellules créées pour la colonne donnée.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Ajout d’actions et de prises

Tout comme n’importe quel autre contrôle d’IU de cacao, nous devons exposer notre mode plan et ses colonnes et C# cellules au code à l’aide d' **actions** et de **prises** (selon les fonctionnalités requises).

Le processus est le même pour tout élément de vue en mode plan que nous souhaitons exposer:

1. Basculez vers l’éditeur de l' **Assistant** et `ViewController.h` Vérifiez que le fichier est sélectionné:

    [![](outline-view-images/edit11.png "Sélection du fichier. h correct")](outline-view-images/edit11.png#lightbox)
2. Sélectionnez le mode plan dans la **hiérarchie**de l’interface, cliquez dessus et faites- `ViewController.h` le glisser vers le fichier.
3. Créez un **orifice** pour la vue plan appelée `ProductOutline`:

    [![](outline-view-images/edit13.png "Configuration d’une prise")](outline-view-images/edit13.png#lightbox)
4. Créez des **sorties** pour les colonnes de tables, ainsi `ProductColumn` que `DetailsColumn`les suivantes:

    [![](outline-view-images/edit14.png "Configuration d’une prise")](outline-view-images/edit14.png#lightbox)
5. Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser avec Xcode.

Nous allons ensuite écrire le code pour afficher des données pour le plan lors de l’exécution de l’application.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Remplissage du mode plan

Avec notre mode plan conçu en Interface Builder et exposé par le biais d’une **prise**, nous devons ensuite C# créer le code pour le remplir.

Tout d’abord, nous allons créer `Product` une classe pour stocker les informations relatives aux lignes et aux groupes de sous-produits individuels. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `Product` pour le **nom** et cliquez sur le bouton **nouveau** :

[![](outline-view-images/populate01.png "Création d’une classe vide")](outline-view-images/populate01.png#lightbox)

Faites en `Product.cs` sorte que le fichier ressemble à ce qui suit:

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

Ensuite, nous devons créer une sous-classe de `NSOutlineDataSource` pour fournir les données de notre plan au fur et à mesure qu’elles sont demandées. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `ProductOutlineDataSource` pour le **nom** et cliquez sur le bouton **nouveau** .

Modifiez le `ProductTableDataSource.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }

        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }

        }
        #endregion
    }
}
```

Cette classe possède un stockage pour les éléments de l’affichage du plan et remplace `GetChildrenCount` le pour retourner le nombre de lignes dans la table. Le `GetChild` retourne un élément parent ou enfant spécifique (comme demandé par le mode plan) `ItemExpandable` et définit l’élément spécifié en tant que parent ou enfant.

Enfin, nous devons créer une sous-classe de `NSOutlineDelegate` pour fournir le comportement de notre plan. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `ProductOutlineDelegate` pour le **nom** et cliquez sur le bouton **nouveau** .

Modifiez le `ProductOutlineDelegate.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Lorsque nous créons une instance `ProductOutlineDelegate`du, nous transmettons également une instance `ProductOutlineDataSource` du qui fournit les données pour le plan. La `GetView` méthode est chargée de retourner une vue (données) pour afficher la cellule d’une colonne et d’une ligne. Si possible, une vue existante sera réutilisée pour afficher la cellule, s’il n’est pas nécessaire de créer une nouvelle vue.

Pour remplir le plan, nous allons modifier le `MainWindow.cs` fichier et faire en `AwakeFromNib` sorte que la méthode ressemble à ce qui suit:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

Si nous exécutons l’application, les éléments suivants s’affichent:

[![](outline-view-images/populate02.png "Affichage réduit")](outline-view-images/populate02.png#lightbox)

Si nous développons un nœud en mode plan, il se présente comme suit:

[![](outline-view-images/populate03.png "Vue développée")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Tri par colonne

Permettons à l’utilisateur de trier les données dans le plan en cliquant sur un en-tête de colonne. Tout d’abord, double- `Main.storyboard` cliquez sur le fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez la `Product` colonne, entrez `Title` pour la **clé**de tri `compare:` , pour le **Sélecteur** et `Ascending` sélectionnez pour la **commande**:

[![](outline-view-images/sort01.png "Définition de l’ordre des clés de tri")](outline-view-images/sort01.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

À présent, modifions `ProductOutlineDataSource.cs` le fichier et ajoutez les méthodes suivantes:

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

La `Sort` méthode nous permet de trier les données dans la source de données en fonction d' `Product` un champ de classe donné, dans l’ordre croissant ou décroissant. La méthode substituée `SortDescriptorsChanged` sera appelée chaque fois que l’utilisera un clic sur un en-tête de colonne. La valeur de **clé** que nous avons définie dans Interface Builder et l’ordre de tri de cette colonne seront transmis.

Si nous exécutons l’application et que vous cliquez dans les en-têtes de colonne, les lignes sont triées selon cette colonne:

[![](outline-view-images/sort02.png "Exemple de sortie triée")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Sélection de ligne

Si vous souhaitez autoriser l’utilisateur à sélectionner une seule ligne, double-cliquez sur le `Main.storyboard` fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez le mode plan dans la **hiérarchie des interfaces** et décochez la case **multiple** dans l' **inspecteur d’attribut**:

[![](outline-view-images/select01.png "L’inspecteur d’attribut")](outline-view-images/select01.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.


Modifiez ensuite le `ProductOutlineDelegate.cs` fichier et ajoutez la méthode suivante:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Cela permettra à l’utilisateur de sélectionner n’importe quelle ligne dans le mode plan. Retournez `false` pour le `ShouldSelectItem` pour tout élément que vous ne voulez pas que l’utilisateur puisse sélectionner ou `false` pour chaque élément si vous ne souhaitez pas que l’utilisateur puisse sélectionner des éléments.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Sélection de plusieurs lignes

Si vous souhaitez autoriser l’utilisateur à sélectionner plusieurs lignes, double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez le mode plan dans la **hiérarchie** de l’interface et cochez la case **multiple** dans l' **inspecteur d’attribut**:

[![](outline-view-images/select02.png "L’inspecteur d’attribut")](outline-view-images/select02.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.


Modifiez ensuite le `ProductOutlineDelegate.cs` fichier et ajoutez la méthode suivante:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Cela permettra à l’utilisateur de sélectionner n’importe quelle ligne dans le mode plan. Retournez `false` pour le `ShouldSelectRow` pour tout élément que vous ne voulez pas que l’utilisateur puisse sélectionner ou `false` pour chaque élément si vous ne souhaitez pas que l’utilisateur puisse sélectionner des éléments.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tapez pour sélectionner la ligne

Si vous souhaitez autoriser l’utilisateur à taper un caractère avec le mode plan sélectionné et à sélectionner la première ligne qui contient ce caractère, double-cliquez sur `Main.storyboard` le fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez le mode plan dans la **hiérarchie** de l’interface et cochez la case **type de sélection** dans l' **inspecteur d’attribut**:

[![](outline-view-images/type01.png "Modification du type de ligne")](outline-view-images/type01.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

À présent, modifions `ProductOutlineDelegate.cs` le fichier et ajoutez la méthode suivante:

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

La `GetNextTypeSelectMatch` méthode prend le donné `searchString` et retourne l’élément de la première `Product` qui contient cette chaîne `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Réorganisation des colonnes

Si vous souhaitez autoriser l’utilisateur à faire glisser les colonnes de réorganisation en mode plan, double-cliquez `Main.storyboard` sur le fichier pour l’ouvrir et le modifier dans Interface Builder. Sélectionnez le mode plan dans la **hiérarchie** de l’interface et cochez la case réorganisation dans l' **inspecteur d’attribut**:

[![](outline-view-images/reorder01.png "L’inspecteur d’attribut")](outline-view-images/reorder01.png#lightbox)

Si nous attribuons une valeur à la propriété **enregistrement** automatique et que vous vérifiez le champ **informations sur la colonne** , les modifications que nous apportons à la disposition de la table sont automatiquement enregistrées pour nous et restaurées lors de la prochaine exécution de l’application.

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

À présent, modifions `ProductOutlineDelegate.cs` le fichier et ajoutez la méthode suivante:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

La `ShouldReorder` méthode doit retourner `true` pour toute colonne qu’il est possible de faire glisser dans le `newColumnIndex`, sinon retourner `false`;

Si nous exécutons l’application, nous pouvons faire glisser les en-têtes de colonne pour réorganiser les colonnes:

[![](outline-view-images/reorder02.png "Exemple de réorganisation des colonnes")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Modification des cellules

Si vous souhaitez autoriser l’utilisateur à modifier les valeurs d’une cellule donnée, modifiez le `ProductOutlineDelegate.cs` fichier et modifiez la `GetViewForItem` méthode comme suit:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

Maintenant, si nous exécutons l’application, l’utilisateur peut modifier les cellules de la vue table:

[![](outline-view-images/editing01.png "Exemple de modification de cellules")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Utilisation d’images en mode plan

Pour inclure une image en tant que partie de la cellule `NSOutlineView`dans un, vous devez modifier la façon dont les données sont retournées par `NSTableViewDelegate's` la méthode du `GetView` mode `NSTableCellView` plan pour utiliser un `NSTextField`au lieu du type standard. Exemple :

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
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
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Pour plus d’informations, consultez la section [utilisation d’images avec des vues en mode plan](~/mac/app-fundamentals/image.md) de notre documentation [sur l’utilisation](~/mac/app-fundamentals/image.md) de l’image.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Mode plan de la liaison de données

En utilisant le codage clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Vous avez également l’avantage de dissocier vos données de stockage (modèle de_données_) de votre interface utilisateur frontale (_Model-View-Controller_), ce qui vous permet de gérer plus facilement la conception d’applications plus souple.

Le codage de clé-valeur (KVC) est un mécanisme permettant d’accéder indirectement aux propriétés d’un objet, à l’aide de clés (chaînes spécialement mises en forme) pour identifier les propriétés au lieu d'`get/set`y accéder via des variables d’instance ou des méthodes d’accesseur (). En implémentant des accesseurs conformes de codage de clé-valeur dans votre application Xamarin. Mac, vous accédez à d’autres fonctionnalités macOS telles que l’observation de la valeur clé (KVO), la liaison de données, les données de base, les liaisons de cacao et la scriptabilité.

Pour plus d’informations, consultez la section [structure de liaison de données](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) de notre documentation sur la liaison de [données et le codage de clé-valeur](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des vues en mode plan dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations des modes plan, comment créer et gérer des vues en mode plan dans les Interface Builder de Xcode et comment travailler avec C# des modes plan dans le code.

## <a name="related-links"></a>Liens associés

- [MacOutlines (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [MacImages (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vues Table](~/mac/user-interface/table-view.md)
- [Listes de sources](~/mac/user-interface/source-list.md)
- [Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation des modes plan](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
