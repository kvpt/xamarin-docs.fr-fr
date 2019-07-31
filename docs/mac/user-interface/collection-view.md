---
title: Vues de collection dans Xamarin. Mac
description: Cet article décrit l’utilisation des vues de collection dans une application Xamarin. Mac. Il aborde la création et la gestion des vues de collection dans Xcode et Interface Builder et leur utilisation par programmation.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/24/2017
ms.openlocfilehash: 432e875969164b6481671d769d488c5f34458fe0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657262"
---
# <a name="collection-views-in-xamarinmac"></a>Vues de collection dans Xamarin. Mac

_Cet article décrit l’utilisation des vues de collection dans une application Xamarin. Mac. Il aborde la création et la gestion des vues de collection dans Xcode et Interface Builder et leur utilisation par programmation._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, le développeur a accès aux mêmes contrôles d’affichage de collection AppKit qu’un développeur qui travaille en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, le développeur utilise _Interface Builder_ de Xcode pour créer et gérer des vues de collection.

Un `NSCollectionView` affiche une grille de sous-affichages organisés à `NSCollectionViewLayout`l’aide d’un. Chaque sous-vue de la grille est représentée par `NSCollectionViewItem` un qui gère le chargement du contenu de la vue à `.xib` partir d’un fichier.

[![Exemple d’exécution d’application](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Cet article décrit les principes fondamentaux de l’utilisation des vues de collection dans une application Xamarin. Mac. Il est fortement recommandé de travailler sur l’article [Hello, Mac](~/mac/get-started/hello-mac.md) en premier, en particulier [sur la présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il couvre les concepts et les techniques clés utilisés. tout au long de cet article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` commandes `Export` et utilisées pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>À propos des vues de collection

L’objectif principal d’une vue de collection`NSCollectionView`() est de réorganiser visuellement un groupe d’objets de manière organisée à l’aide d’une`NSCollectionViewLayout`disposition de vue de collection ()`NSCollectionViewItem`, avec chaque objet individuel () qui obtient sa propre vue dans la collection de plus grande taille. Les vues de collection fonctionnent via la liaison de données et les techniques de codage clé-valeur. par conséquent, vous devez lire la documentation sur la [liaison de données et le codage de clé-valeur](~/mac/app-fundamentals/databinding.md) avant de poursuivre cet article.

La vue de collection n’a pas d’élément d’affichage de collection intégré standard (comme un mode plan ou table). par conséquent, le développeur est responsable de la conception et de l’implémentation d’une _vue prototype_ à l’aide d’autres contrôles AppKit tels que les champs d’image, les champs de texte, les étiquettes suite. Cette vue prototype sera utilisée pour afficher et utiliser chaque élément géré par la vue de collection et stocké dans un `.xib` fichier.

Étant donné que le développeur est responsable de l’apparence d’un élément d’affichage de collection, la vue de collection n’a pas de prise en charge intégrée pour mettre en surbrillance un élément sélectionné dans la grille. L’implémentation de cette fonctionnalité sera traitée dans cet article.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Définition du modèle de données

Avant de lier des données à une vue de collection dans Interface Builder, une classe conforme (KVC)/Key-Value observant (KVO) doit être définie dans l’application Xamarin. Mac pour agir en tant que _modèle de données_ pour la liaison. Le modèle de données fournit toutes les données qui seront affichées dans la collection et reçoit toutes les modifications apportées aux données que l’utilisateur effectue dans l’interface utilisateur lors de l’exécution de l’application.

Prenons l’exemple d’une application qui gère un groupe d’employés, la classe suivante peut être utilisée pour définir le modèle de données:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

Le `PersonModel` modèle de données sera utilisé dans le reste de cet article.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Utilisation d’une vue de collection

La liaison de données avec une vue de collection est très similaire à la liaison avec une `NSCollectionViewDataSource` vue de table, comme utilisé pour fournir des données pour la collection. Étant donné que la vue de collection n’a pas de format d’affichage prédéfini, un travail supplémentaire est nécessaire pour fournir des commentaires sur l’interaction de l’utilisateur et effectuer le suivi de la sélection de l’utilisateur.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Création du prototype de cellule

Étant donné que la vue de collection n’inclut pas de prototype de cellule par défaut, le développeur doit ajouter `.xib` un ou plusieurs fichiers à l’application Xamarin. Mac pour définir la disposition et le contenu des cellules individuelles.

Effectuez ce qui suit :

1. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter** > **un nouveau fichier...**
2. Sélectionnez le `EmployeeItem` contrôleur d'**affichage** **Mac** > , donnez-lui un nom (comme dans cet exemple), puis cliquez sur le bouton **nouveau** pour créer: 

    ![Ajout d’un nouveau contrôleur d’affichage](collection-view-images/proto01.png)

    Cela ajoute un `EmployeeItem.cs`fichier, `EmployeeItemController.cs` et `EmployeeItemController.xib` à la solution du projet.
3. Double-cliquez sur `EmployeeItemController.xib` le fichier pour l’ouvrir et le modifier dans le Interface Builder de Xcode.
4. Ajoutez un `NSBox` `NSImageView` et deux`NSLabel` contrôles à la vue et mettez-les en forme comme suit:

    ![Conception de la disposition du prototype de cellule](collection-view-images/proto02.png)
5. Ouvrez l' **éditeur** de l’Assistant et créez un Outlet `NSBox` pour le afin qu’il puisse être utilisé pour indiquer l’état de sélection d’une cellule:

    ![Exposition des NSBox dans une sortie](collection-view-images/proto03.png)
6. Revenez à l' **éditeur standard** et sélectionnez la vue d’image.
7. Dans l' **inspecteur de liaison**, sélectionnez **lier au** > **propriétaire du fichier** et entrez un chemin d’accès `self.Person.Icon`de **clé de modèle** :

    ![Liaison de l’icône](collection-view-images/proto04.png)
8. Sélectionnez la première étiquette et, dans **l’inspecteur de liaison**, sélectionnez **lier au** > **propriétaire du fichier** et entrez un chemin d' `self.Person.Name`accès de **clé de modèle** :

    ![Liaison du nom](collection-view-images/proto05.png)
9. Sélectionnez la deuxième étiquette et, dans **l’inspecteur de liaison**, sélectionnez **lier au** > **propriétaire du fichier** et entrez un chemin d' `self.Person.Occupation`accès de **clé de modèle** :

    ![Liaison de la profession](collection-view-images/proto06.png)
10. Enregistrez les modifications apportées au `.xib` fichier et revenez à Visual Studio pour synchroniser les modifications.

Modifiez le `EmployeeItemController.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

En examinant ce code en détail, la classe hérite `NSCollectionViewItem` de, de sorte qu’elle peut agir comme un prototype pour une cellule d’affichage de collection. La `Person` propriété expose la classe qui a été utilisée pour lier des données à la vue d’image et aux étiquettes dans Xcode. Il s’agit d’une instance `PersonModel` du créé ci-dessus.

La `BackgroundColor` propriété est un raccourci vers le `NSBox` du `FillColor` contrôle qui sera utilisé pour afficher l’état de sélection d’une cellule. En substituant la `Selected` propriété de l' `NSCollectionViewItem`élément, le code suivant définit ou efface cet état de sélection:

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>Création de la source de données de la vue de collection

Une source de données d’affichage`NSCollectionViewDataSource`de collection () fournit toutes les données d’une vue de collection et crée et remplit une cellule d’affichage de `.xib` collection (à l’aide du prototype) comme requis pour chaque élément de la collection.

Ajoutez une nouvelle classe au projet, appelez- `CollectionViewDataSource` la et faites-lui ressembler à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

En examinant ce code en détail, la classe hérite `NSCollectionViewDataSource` de et expose une liste `PersonModel` d’instances par `Data` le biais de sa propriété.

Étant donné que cette collection n’a qu’une seule section, le code `GetNumberOfSections` remplace la méthode et `1`retourne toujours. En outre, la `GetNumberofItems` méthode est substituée, car elle retourne le nombre d’éléments dans `Data` la liste de propriétés.

La `GetItem` méthode est appelée chaque fois qu’une nouvelle cellule est requise et se présente comme suit:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

La `MakeItem` méthode de la vue de collection est appelée pour créer ou retourner une instance réutilisable `EmployeeItemController` du et `Person` sa propriété est définie sur l’élément affiché dans la cellule demandée. 

Le `EmployeeItemController` doit être inscrit avec le contrôleur d’affichage de collection au préalable à l’aide du code suivant:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

L' **identificateur** (`EmployeeCell`) utilisé dans l' `MakeItem` appel _doit_ correspondre au nom du contrôleur d’affichage inscrit avec la vue de collection. Cette étape sera traitée en détail ci-dessous.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Gestion de la sélection des éléments

Pour gérer la sélection et la désélection d’éléments dans la collection, un `NSCollectionViewDelegate` est requis. Étant donné que cet exemple utilisera le type `NSCollectionViewFlowLayout` de disposition intégré, `NSCollectionViewDelegateFlowLayout` une version spécifique de ce délégué sera requise.

Ajoutez une nouvelle classe au projet, appelez- `CollectionViewDelegate` la et faites-lui ressembler à ce qui suit:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

Les `ItemsSelected` méthodes `ItemsDeselected` et sont substituées et utilisées pour définir ou effacer la `PersonSelected` propriété du contrôleur d’affichage qui gère la vue de collection lorsque l’utilisateur sélectionne ou désélectionne un élément. Cela s’affiche en détail ci-dessous.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Création de la vue de collection dans Interface Builder

Une fois que tous les éléments de prise en charge requis sont en place, le Storyboard principal peut être modifié et une vue de collection y est ajoutée.

Effectuez ce qui suit :

1. Double-cliquez sur `Main.Storyboard` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier dans le Interface Builder de Xcode.
2. Faites glisser une vue de collection dans la vue principale et redimensionnez-la pour remplir la vue:

    ![Ajout d’une vue de collection à la disposition](collection-view-images/collection01.png)
3. Une fois la vue de collection sélectionnée, utilisez l’éditeur de contrainte pour l’épingler à la vue lorsqu’elle est redimensionnée:

    ![Ajout de contraintes](collection-view-images/collection02.png)
4. Assurez-vous que la vue de collection est sélectionnée dans le **aire de conception** (et non pas à la vue de **défilement** ou la **vue de clip** qui la contient), basculez vers l’éditeur de l' **Assistant** et créez une **prise** pour la vue de collection:

    ![Ajout de contraintes](collection-view-images/collection03.png)
5. Enregistrez les modifications et revenez à Visual Studio pour effectuer la synchronisation.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Réunir tout cela

Tous les éléments de prise en charge ont été mis en place avec une classe qui joue le rôle de modèle`PersonModel`de données ( `NSCollectionViewDataSource` ), a été ajouté `NSCollectionViewDelegateFlowLayout` pour fournir des données, a `NSCollectionView` été créé pour gérer la sélection des éléments et a été ajouté à la table de montage séquentiel principale et exposé comme sortie (`EmployeeCollection`).

La dernière étape consiste à modifier le contrôleur d’affichage qui contient la vue de collection et à rassembler tous les éléments pour remplir la collection et gérer la sélection des éléments.

Modifiez le `ViewController.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

En examinant ce code en détail, une `Datasource` propriété est définie pour contenir une instance `CollectionViewDataSource` du qui fournira les données pour la vue de collection. Une `PersonSelected` propriété est définie pour contenir le `PersonModel` représentant l’élément actuellement sélectionné dans la vue de collection. Cette propriété déclenche également l' `SelectionChanged` événement lorsque la sélection change.

La `ConfigureCollectionView` classe est utilisée pour inscrire le contrôleur d’affichage qui agit en tant que prototype de cellule avec la vue de collection à l’aide de la ligne suivante:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Notez que l' **identificateur** (`EmployeeCell`) utilisé pour enregistrer le prototype correspond à celui appelé dans la `GetItem` méthode de la `CollectionViewDataSource` méthode définie ci-dessus:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

En outre, le type du contrôleur d’affichage **doit** correspondre exactement au nom du `.xib` fichier qui définit le prototype. Dans le cas de cet exemple, `EmployeeItemController` et `EmployeeItemController.xib`.

La disposition réelle des éléments dans la vue de collection est contrôlée par une classe de disposition de vue de collection et peut être modifiée dynamiquement au moment de l’exécution en assignant une nouvelle instance à la `CollectionViewLayout` propriété. La modification de cette propriété met à jour l’apparence de la vue de collection sans animer la modification.

Apple fournit deux types de disposition intégrés avec la vue de collection qui gère la plupart des utilisations courantes `NSCollectionViewFlowLayout` : `NSCollectionViewGridLayout`et. Si le développeur a requis un format personnalisé, tel que la disposition des éléments dans un cercle, ils peuvent créer une instance personnalisée `NSCollectionViewLayout` de et substituer les méthodes requises pour obtenir l’effet souhaité.

Cet exemple utilise la disposition de fluide par défaut afin de créer une instance `NSCollectionViewFlowLayout` de la classe et de la configurer comme suit:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

La `ItemSize` propriété définit la taille de chaque cellule dans la collection. La `SectionInset` propriété définit les indéfinis à partir du bord de la collection dans laquelle les cellules seront disposées. `MinimumInteritemSpacing`définit l’espacement minimal entre les `MinimumLineSpacing` éléments et définit l’espacement minimal entre les lignes de la collection.

La disposition est assignée à la vue de collection et une `CollectionViewDelegate` instance du est attachée pour gérer la sélection des éléments:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

La `PopulateWithData` méthode crée une nouvelle instance de, `CollectionViewDataSource`la remplit avec des données, l’attache à la vue de collection et appelle la `ReloadData` méthode pour afficher les éléments:

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

La `ViewDidLoad` méthode est substituée et appelle les `ConfigureCollectionView` méthodes `PopulateWithData` et pour afficher l’affichage final de la collection à l’utilisateur:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des vues de collection dans une application Xamarin. Mac. Tout d’abord, il s’est penché sur l’exposition d’une C# classe à Objective-C à l’aide du codage clé-valeur (KVC) et de l’observation clé-valeur (KVO). Ensuite, il a montré comment utiliser une classe conforme à KVO et lier les données aux vues de collection du Interface Builder de Xcode. Enfin, il a montré comment interagir avec les vues de C# collection dans le code.

## <a name="related-links"></a>Liens associés

- [MacCollectionNew (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
