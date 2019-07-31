---
title: Listes de sources dans Xamarin. Mac
description: Cet article traite de l’utilisation des listes de sources dans une application Xamarin. Mac. Il décrit la création et la gestion des listes de sources dans Xcode et Interface Builder et l' C# interaction avec eux dans le code.
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 33ef45fa08748e70ef376e43cb5ed9b12ba55198
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655257"
---
# <a name="source-lists-in-xamarinmac"></a>Listes de sources dans Xamarin. Mac

_Cet article traite de l’utilisation des listes de sources dans une application Xamarin. Mac. Il décrit la création et la gestion des listes de sources dans Xcode et Interface Builder et l' C# interaction avec eux dans le code._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes listes de sources qu’un développeur travaillant dans *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser _Interface Builder_ de Xcode pour créer et gérer vos listes sources (ou éventuellement les créer directement dans C# le code).

Une liste source est un type spécial de mode plan utilisé pour afficher la source d’une action, comme la barre latérale dans Finder ou iTunes.

[![](source-list-images/source05.png "Exemple de liste de sources")](source-list-images/source05.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des listes de sources dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Présentation des listes sources

Comme indiqué ci-dessus, une liste source est un type spécial de mode plan utilisé pour afficher la source d’une action, comme la barre latérale dans Finder ou iTunes. Une liste source est un type de table qui permet à l’utilisateur de développer ou de réduire des lignes de données hiérarchiques. Contrairement à une vue de table, les éléments d’une liste source ne sont pas dans une liste plate, ils sont organisés dans une hiérarchie, comme les fichiers et les dossiers sur un disque dur. Si un élément d’une liste source contient d’autres éléments, il peut être développé ou réduit par l’utilisateur.

La liste source est un mode plan spécial (`NSOutlineView`), qui est lui-même une sous-classe de la vue de table (`NSTableView`) et, en tant que tel, hérite d’une grande partie de son comportement de sa classe parente. Par conséquent, de nombreuses opérations prises en charge par un mode plan sont également prises en charge par une liste source. Une application Xamarin. Mac contrôle ces fonctionnalités et peut configurer les paramètres de la liste source (dans le code ou Interface Builder) pour autoriser ou interdire certaines opérations.

Une liste source ne stocke pas ses propres données. elle s’appuie plutôt sur une source de données`NSOutlineViewDataSource`() pour fournir à la fois les lignes et les colonnes requises, en fonction des besoins.

Le comportement d’une liste source peut être personnalisé en fournissant une sous-classe du délégué en mode`NSOutlineViewDelegate`plan () pour prendre en charge le type de plan pour sélectionner les fonctionnalités, la sélection et la modification des éléments, le suivi personnalisé et les vues personnalisées pour des éléments individuels.

Dans la mesure où une liste source partage un grand nombre de comportements et de fonctionnalités avec une vue de table et une vue en mode plan, vous souhaiterez peut-être parcourir notre documentation sur les [vues de table](~/mac/user-interface/table-view.md) et les [vues de plan](~/mac/user-interface/outline-view.md) avant de poursuivre avec cet article.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Utilisation des listes de sources

Une liste source est un type spécial de mode plan utilisé pour afficher la source d’une action, comme la barre latérale dans Finder ou iTunes. Contrairement aux vues en mode plan, avant de définir notre liste de sources dans Interface Builder, nous allons créer les classes de stockage dans Xamarin. Mac.

Tout d’abord, nous allons créer `SourceListItem` une classe pour stocker les données de notre liste source. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `SourceListItem` pour le **nom** et cliquez sur le bouton **nouveau** :

[![](source-list-images/source01.png "Ajout d’une classe vide")](source-list-images/source01.png#lightbox)

Faites en `SourceListItem.cs` sorte que le fichier ressemble à ce qui suit: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `SourceListDataSource` pour le **nom** et cliquez sur le bouton **nouveau** . Faites en `SourceListDataSource.cs` sorte que le fichier ressemble à ce qui suit:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

Cela permet de fournir les données de notre liste source.

Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `SourceListDelegate` pour le **nom** et cliquez sur le bouton **nouveau** . Faites en `SourceListDelegate.cs` sorte que le fichier ressemble à ce qui suit:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

Cela permet de fournir le comportement de notre liste source.

Enfin, dans la **Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Ajouter** > **un nouveau fichier...** Sélectionnez classe **générale** > **vide**, entrez `SourceListView` pour le **nom** et cliquez sur le bouton **nouveau** . Faites en `SourceListView.cs` sorte que le fichier ressemble à ce qui suit:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Cela crée une sous-classe personnalisée, réutilisable`SourceListView`de `NSOutlineView` () que nous pouvons utiliser pour piloter la liste de sources dans n’importe quelle application Xamarin. Mac que nous faisons.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Création et gestion des listes de sources dans Xcode

À présent, nous allons concevoir notre liste de sources dans Interface Builder. Double-cliquez sur `Main.storyboard` le fichier pour l’ouvrir et le modifier dans Interface Builder, puis faites glisser un affichage fractionné de l' **inspecteur de bibliothèque**, ajoutez-le au contrôleur d’affichage et définissez-le pour qu’il se redimensionne avec la vue dans l' **éditeur de contraintes**:

[![](source-list-images/source00.png "Modification des contraintes")](source-list-images/source00.png#lightbox)

Ensuite, faites glisser une liste source à partir de l' **inspecteur de bibliothèque**, ajoutez-la à gauche de l’affichage fractionné et affectez-lui la valeur redimensionner avec la vue dans l' **éditeur de contraintes**:

[![](source-list-images/source02.png "Modification des contraintes")](source-list-images/source02.png#lightbox)

Ensuite, basculez vers la **vue d’identité**, sélectionnez la liste source, puis remplacez sa `SourceListView`classe par:

[![](source-list-images/source03.png "Définition du nom de la classe")](source-list-images/source03.png#lightbox)

Enfin, créez une **sortie** pour notre liste source appelée `SourceList` dans le `ViewController.h` fichier:

[![](source-list-images/source04.png "Configuration d’une prise")](source-list-images/source04.png#lightbox)

Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Remplissage de la liste source

Nous allons modifier le `RotationWindow.cs` fichier dans Visual Studio pour Mac et faire en sorte `AwakeFromNib` que sa méthode ressemble à ce qui suit:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

La `Initialize ()` méthode doit être appelée par rapport à la **sortie** de la liste source _avant_ l’ajout d’éléments à celle-ci. Pour chaque groupe d’éléments, nous créons un élément parent, puis ajoutons les sous-éléments à cet élément de groupe. Chaque groupe est ensuite ajouté à la collection `SourceList.AddItem (...)`de la liste source. Les deux dernières lignes chargent les données de la liste source et développent tous les groupes:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Enfin, modifiez le `AppDelegate.cs` fichier et `DidFinishLaunching` faites en sorte que la méthode ressemble à ce qui suit:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Si nous exécutons notre application, les éléments suivants s’affichent:

[![](source-list-images/source05.png "Exemple d’exécution d’application")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des listes de sources dans une application Xamarin. Mac. Nous avons vu comment créer et gérer des listes de sources dans le Interface Builder de Xcode et comment utiliser des listes C# de sources dans le code.

## <a name="related-links"></a>Liens associés

- [MacOutlines (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vues Table](~/mac/user-interface/table-view.md)
- [Modes Plan](~/mac/user-interface/outline-view.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation des modes plan](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
