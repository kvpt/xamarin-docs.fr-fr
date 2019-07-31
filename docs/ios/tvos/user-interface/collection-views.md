---
title: Utilisation des vues de collection tvOS dans Xamarin
description: Ce document décrit comment utiliser les vues de collection dans une application tvOS générée avec Xamarin. Il traite des dispositions de vue de collection, de la création de cellules et des vues supplémentaires, de la réponse aux événements utilisateur, etc.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3246dcbf58a1b6dda6838b5eb81442fdbc429af5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652349"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Utilisation des vues de collection tvOS dans Xamarin

Les vues de collection permettent d’afficher un groupe de contenu à l’aide de dispositions arbitraires. À l’aide de la prise en charge intégrée, elles permettent de créer facilement des dispositions similaires à des grilles ou linéaires, tout en prenant en charge des dispositions personnalisées.

[![](collection-views-images/collection01.png "Exemple d’affichage de collection")](collection-views-images/collection01.png#lightbox)

La vue de collection gère une collection d’éléments à l’aide d’un délégué et d’une source de données pour fournir une interaction utilisateur et le contenu de la collection. Étant donné que la vue de collection est basée sur un sous-système de mise en page indépendant de la vue elle-même, il est possible de modifier facilement la présentation des données de la vue de collection à la volée.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>À propos des vues de collection

Comme indiqué ci-dessus, une vue`UICollectionView`de collection () gère une collection ordonnée d’éléments et présente ces éléments avec des dispositions personnalisables. Les vues de collection fonctionnent de la même façon que les`UITableView`vues de table (), à ceci près qu’elles peuvent utiliser des dispositions pour présenter des éléments dans plus qu’une seule colonne.

Lors de l’utilisation d’une vue de collection dans tvOS, votre application est chargée de fournir les données associées à la collection à`UICollectionViewDataSource`l’aide d’une source de données (). Les données d’affichage de collection peuvent éventuellement être organisées et présentées dans différents groupes (sections).

La vue de collection présente les éléments individuels à l’écran à`UICollectionViewCell`l’aide d’une cellule () qui fournit la présentation d’une information donnée de la collection (comme une image et son titre).

Si vous le souhaitez, des vues supplémentaires peuvent être ajoutées à la présentation de la vue de collection pour faire office d’en-tête et de pied de page pour les sections et les cellules. La disposition de la vue de collection est chargée de définir le placement de ces vues en même temps que les cellules individuelles.

La vue de collection peut répondre à l’interaction de l’utilisateur`UICollectionViewDelegate`à l’aide d’un délégué (). Ce délégué est également chargé de déterminer si une cellule donnée peut obtenir le focus, si une cellule a été mise en surbrillance ou si elle a été sélectionnée. Dans certains cas, le délégué détermine la taille des cellules individuelles.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Dispositions de la vue de collection

Une fonctionnalité clé d’une vue de collection est sa séparation entre les données qu’elle présente et sa disposition. Une disposition de vue de`UICollectionViewLayout`collection () est responsable de la fourniture de l’organisation et de l’emplacement des cellules (et de toutes les vues supplémentaires) avec dans la présentation à l’écran de la vue de collection.

Les cellules individuelles sont créées par la vue de collection à partir de sa source de données attachée, puis sont réorganisées et affichées par la disposition de vue de collection donnée.

La disposition de la vue de collection est normalement fournie lors de la création de la vue de collection. Toutefois, vous pouvez modifier la disposition de la vue de collection à tout moment et la présentation à l’écran des données de la vue de collection sera automatiquement mise à jour à l’aide de la nouvelle disposition fournie.

La disposition de la vue de collection fournit plusieurs méthodes qui peuvent être utilisées pour animer la transition entre deux dispositions différentes (par défaut, aucune animation n’est effectuée). En outre, les dispositions de vue de collection peuvent fonctionner avec des détecteurs de mouvement pour animer davantage l’interaction de l’utilisateur qui entraîne une modification de la disposition.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Création de cellules et de vues supplémentaires

La source de données d’une vue de collection est non seulement responsable de la fourniture des données de l’élément de la collection, mais également des cellules utilisées pour afficher le contenu.

Étant donné que les vues de collection ont été conçues pour gérer de grandes collections d’éléments, les cellules individuelles peuvent être déplacées en file d’attente et réutilisées pour éviter les limitations de mémoire en cours d’exécution. Il existe deux méthodes différentes pour défiler les vues:

- `DequeueReusableCell`-Crée ou retourne une cellule du type donné (tel que spécifié dans le Storyboard de l’application).
- `DequeueReusableSupplementaryView`-Crée ou retourne une vue supplémentaire du type donné (tel que spécifié dans le Storyboard de l’application).

Avant d’appeler l’une de ces méthodes, vous devez inscrire la classe, `.xib` la table de montage séquentiel ou le fichier utilisé pour créer l’affichage de la cellule avec la vue de collection. Par exemple :

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Où `typeof(CityCollectionViewCell)` fournit la classe qui prend en charge la `CityViewDatasource.CardCellId` vue et fournit l’ID utilisé lorsque la cellule (ou la vue) est déplacée dans la file d’attente.

Une fois que la cellule est déplacée dans la file d’attente, vous la configurez avec les données de l’élément qu’elle représente et vous revenez à la vue de collection pour l’afficher.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>À propos des contrôleurs d’affichage de collection

Un contrôleur d’affichage de`UICollectionViewController`collection () est un contrôleur d'`UIViewController`affichage spécialisé () qui fournit le comportement suivant:

- Il est responsable du chargement de la vue de collection à partir `.xib` de son storyboard ou fichier et de l’instanciation de la vue. Si elle est créée dans le code, elle crée automatiquement une nouvelle vue de collection non configurée.
- Une fois la vue de collection chargée, le contrôleur tente de charger sa source de données et le délégué à `.xib` partir de la table de montage séquentiel ou du fichier. Si aucun n’est disponible, il se définit comme la source des deux.
- Garantit que les données sont chargées avant que la vue de collection ne soit renseignée pour la première fois, puis recharge et efface la sélection à chaque affichage suivant.

En outre, le contrôleur d’affichage de collection fournit des méthodes substituables qui peuvent être utilisées pour gérer le cycle de vie de `AwakeFromNib` la `ViewWillDisplay`vue de collection comme et.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Vues de collection et storyboards

Le moyen le plus simple de travailler avec une vue de collection dans votre application Xamarin. tvOS consiste à en ajouter un à son Storyboard. En guise d’exemple rapide, nous allons créer un exemple d’application qui présente une image, un titre et un bouton Sélectionner. Si l’utilisateur clique sur le bouton Sélectionner, une vue de collection s’affiche et permet à l’utilisateur de choisir une nouvelle image. Quand une image est sélectionnée, la vue de collection est fermée et la nouvelle image et le nouveau titre s’affichent.

Procédez comme suit:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

    
1. Démarrez une nouvelle **application TvOS View** dans Visual Studio pour Mac.
1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le dans le concepteur iOS.
1. Ajoutez une vue d’image, une étiquette et un bouton à la vue existante et configurez-les de façon à ce qu’elle ressemble à ce qui suit: 

    [![](collection-views-images/collection02.png "Exemple de disposition")](collection-views-images/collection02.png#lightbox)
1. Attribuez un **nom** à la vue image et à l’étiquette dans l' **onglet widget** de l' **Explorateur de propriétés**. Par exemple : 

    [![](collection-views-images/collection03.png "Définition du nom")](collection-views-images/collection03.png#lightbox)
1. Ensuite, faites glisser un contrôleur d’affichage de collection sur le Storyboard: 

    [![](collection-views-images/collection04.png "Un contrôleur d’affichage de collection")](collection-views-images/collection04.png#lightbox)
1. Maintenez la touche Ctrl enfaits glisser du bouton vers le contrôleur d’affichage de collection et sélectionnez **Push** dans le menu contextuel: 

    [![](collection-views-images/collection05.png "Sélectionner un push dans la fenêtre contextuelle")](collection-views-images/collection05.png#lightbox)
1. Lorsque l’application est exécutée, l’affichage de la collection s’affiche chaque fois que l’utilisateur clique sur le bouton.
1. Sélectionnez la vue de collection et entrez les valeurs suivantes sous l' **onglet disposition** de l' **Explorateur de propriétés**: 

    [![](collection-views-images/collection06.png "Explorateur de propriétés")](collection-views-images/collection06.png#lightbox)
1. Cela contrôle la taille des cellules individuelles et les bordures entre les cellules et le bord externe de la vue de collection.
1. Sélectionnez le contrôleur d’affichage de collection et définissez sa `CityCollectionViewController` classe sur dans l' **onglet widget**: 

    [![](collection-views-images/collection07.png "Définir la classe sur CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Sélectionnez la vue de collection et définissez sa classe `CityCollectionView` sur dans l' **onglet widget**: 

    [![](collection-views-images/collection08.png "Définir la classe sur CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Sélectionnez la cellule d’affichage de collection et définissez sa `CityCollectionViewCell` classe sur dans l' **onglet widget**: 

    [![](collection-views-images/collection09.png "Définir la classe sur CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. Dans l' **onglet widget** , vérifiez que la disposition `Flow` est et que la direction `Vertical` de **défilement** est pour la vue de collection: 

    [![](collection-views-images/collection10.png "Onglet widget")](collection-views-images/collection10.png#lightbox)
1. Sélectionnez la cellule d’affichage de collection et définissez son `CityCell` identité sur sous l' **onglet widget**: 

    [![](collection-views-images/collection11.png "Définir l’identité sur CityCell")](collection-views-images/collection11.png#lightbox)
1. Enregistrez les modifications apportées.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. Démarrez une nouvelle **vue tvOS** dans Visual Studio.
1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le dans le concepteur iOS.
1. Ajoutez une vue d’image, une étiquette et un bouton à la vue existante et configurez-les de façon à ce qu’elle ressemble à ce qui suit: 

    [![](collection-views-images/collection02vs.png "Configurer la disposition")](collection-views-images/collection02vs.png#lightbox)
1. Attribuez un **nom** à la vue image et à l’étiquette dans l' **onglet widget** de l' **Explorateur de propriétés**. Par exemple : 

    [![](collection-views-images/collection03vs.png "Explorateur de propriétés")](collection-views-images/collection03vs.png#lightbox)
1. Ensuite, faites glisser un contrôleur d’affichage de collection sur le Storyboard: 

    [![](collection-views-images/collection04vs.png "Un contrôleur d’affichage de collection")](collection-views-images/collection04vs.png#lightbox)
1. Maintenez la touche Ctrl enfaits glisser du bouton vers le contrôleur d’affichage de collection et sélectionnez **Push** dans le menu contextuel: 

    [![](collection-views-images/collection05vs.png "Sélectionner un push dans la fenêtre contextuelle")](collection-views-images/collection05vs.png#lightbox)
1. Lorsque l’application est exécutée, l’affichage de la collection s’affiche chaque fois que l’utilisateur clique sur le bouton.
1. Sélectionnez la vue de collection et, sous l' **onglet disposition** de l' **Explorateur de propriétés** , entrez la **largeur** _361_ et la **hauteur** _256_ 
1. Cela contrôle la taille des cellules individuelles et les bordures entre les cellules et le bord externe de la vue de collection.
1. Sélectionnez le contrôleur d’affichage de collection et définissez sa `CityCollectionViewController` classe sur dans l' **onglet widget**: 

    [![](collection-views-images/collection07vs.png "Définir la classe sur CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Sélectionnez la vue de collection et définissez sa classe `CityCollectionView` sur dans l' **onglet widget**: 

    [![](collection-views-images/collection08vs.png "Définir la classe sur CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Sélectionnez la cellule d’affichage de collection et définissez sa `CityCollectionViewCell` classe sur dans l' **onglet widget**: 

    [![](collection-views-images/collection09vs.png "Définir la classe sur CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. Dans l' **onglet widget** , vérifiez que la disposition `Flow` est et que la direction `Vertical` de **défilement** est pour la vue de collection: 

    [![](collection-views-images/collection10vs.png "Onglet du widget")](collection-views-images/collection10vs.png#lightbox)
1. Sélectionnez la cellule d’affichage de collection et définissez son `CityCell` identité sur sous l' **onglet widget**: 

    [![](collection-views-images/collection11vs.png "Définir l’identité sur CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Enregistrez les modifications apportées.
    

-----

Si nous avions choisi `Custom` la **disposition**de la vue de collection, nous aurions pu spécifier une disposition personnalisée. Apple fournit un intégré `UICollectionViewFlowLayout` `UICollectionViewDelegateFlowLayout` qui peut facilement présenter des données dans une disposition basée sur la grille (celles-ci sont utilisées par `flow` le style de disposition). 

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Fourniture de données pour la vue de collection

Maintenant que nous avons ajouté notre vue de collection (et le contrôleur d’affichage de collection) à notre Storyboard, nous devons fournir les données de la collection. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>Modèle de données

Tout d’abord, nous allons créer un modèle pour les données qui contient le nom de fichier de l’image à afficher, le titre et un indicateur permettant de sélectionner la ville.

Créez une `CityInfo` classe et faites en sorte qu’elle ressemble à ce qui suit:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>Cellule d’affichage de collection

Nous devons maintenant définir la manière dont les données seront présentées pour chaque cellule. Modifiez le `CityCollectionViewCell.cs` fichier (créé automatiquement à partir de votre fichier de table de montage séquentiel) et faites-le ressembler à ce qui suit:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

Pour notre application tvOS, nous affichons une image et un titre facultatif. Si la ville donnée ne peut pas être sélectionnée, nous mettons en grisé la vue d’image à l’aide du code suivant:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Lorsque la cellule contenant l’image est mise en focus par l’utilisateur, nous voulons utiliser l’effet de parallaxe intégré sur celle-ci en définissant la propriété suivante:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Pour plus d’informations sur la navigation et le focus, consultez notre section consacrée [à la navigation et au focus](~/ios/tvos/app-fundamentals/navigation-focus.md) et à la documentation sur les [contrôleurs Bluetooth et Siri](~/ios/tvos/platform/remote-bluetooth.md) distants.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>Affichage de la collection Fournisseur de données

Avec notre modèle de données créé et notre disposition de cellule définie, nous allons créer une source de données pour notre vue de collection. La source de données est chargée non seulement de fournir les données de stockage, mais également de supprimer la file d’attente des cellules pour afficher les cellules individuelles affichées à l’écran.

Créez une `CityViewDatasource` classe et faites en sorte qu’elle ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

Nous allons examiner cette classe en détail. Tout d’abord, nous `UICollectionViewDataSource` héritons de et fournissons un raccourci vers l’ID des cellules (que nous avons affecté dans le concepteur IOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

Nous fournissons ensuite le stockage pour nos données de collection et fournissons une classe pour remplir les données:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

Ensuite, nous remplaçons `NumberOfSections` la méthode et retournons le nombre de sections (groupes d’éléments) de notre vue de collection. Dans ce cas, il n’y en a qu’un seul:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Nous retournons ensuite le nombre d’éléments dans notre collection à l’aide du code suivant:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Enfin, nous défilerons une cellule réutilisable quand la vue de collection est demandée avec le code suivant:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

Une fois que nous avons obtenu une cellule d' `CityCollectionViewCell` affichage de collection de notre type, nous la remplissons avec l’élément donné.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Réponse aux événements utilisateur

Étant donné que nous voulons que l’utilisateur soit en mesure de sélectionner un élément de notre collection, nous devons fournir un délégué de vue de collection pour gérer cette interaction. Et nous devons fournir un moyen de permettre à notre vue d’appel de savoir quel est l’élément sélectionné par l’utilisateur.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>Le délégué d’application

Nous avons besoin d’un moyen de relier l’élément actuellement sélectionné de la vue de collection à la vue appelante. Nous utiliserons une propriété personnalisée sur notre `AppDelegate`. Modifiez le `AppDelegate.cs` fichier et ajoutez le code suivant:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Cela définit la propriété et définit la ville par défaut qui sera affichée initialement. Plus tard, nous utiliserons cette propriété pour afficher la sélection de l’utilisateur et autoriser la modification de la sélection.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>Délégué de la vue de collection

Ensuite, ajoutez une nouvelle `CityViewDelegate` classe au projet et faites en sorte qu’elle ressemble à ce qui suit:


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

Examinons plus en détail cette classe. Tout d’abord, nous `UICollectionViewDelegateFlowLayout`héritons de. La raison `UICollectionViewFlowLayout` pour laquelle nous héritons de cette classe et `UICollectionViewDelegate` non du est que nous utilisons le intégré pour présenter nos éléments et non un type de disposition personnalisé.

Nous retournons ensuite la taille des éléments individuels à l’aide de ce code:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Ensuite, nous décidons si une cellule donnée peut obtenir le focus à l’aide du code suivant: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

Nous vérifions si l’indicateur `CanSelect` `true` d’un élément de données de sauvegarde donné a la valeur et que vous renvoyez cette valeur. Pour plus d’informations sur la navigation et le focus, consultez notre section consacrée [à la navigation et au focus](~/ios/tvos/app-fundamentals/navigation-focus.md) et à la documentation sur les [contrôleurs Bluetooth et Siri](~/ios/tvos/platform/remote-bluetooth.md) distants.

Enfin, nous répondons à l’utilisateur en sélectionnant un élément avec le code suivant:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Ici, nous définissons la `SelectedCity` propriété de notre `AppDelegate` sur l’élément que l’utilisateur a sélectionné et nous fermons le contrôleur d’affichage de collection, en revenant à la vue qui a appelé nous. Nous n’avons pas `ParentController` encore défini la propriété de notre vue de collection. nous allons le faire ensuite.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configuration de la vue de collection

Nous devons maintenant modifier notre vue de collection et affecter notre source de données et votre délégué. Modifiez le `CityCollectionView.cs` fichier (créé pour nous automatiquement à partir de notre table de montage séquentiel) et faites-le ressembler à ce qui suit:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

Tout d’abord, nous fournissons un raccourci `AppDelegate`pour accéder à notre: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

Ensuite, nous fournissons un raccourci vers la source de données de la vue de collection et une propriété pour accéder au contrôleur d’affichage de collection (utilisé par notre délégué ci-dessus pour fermer la collection lorsque l’utilisateur effectue une sélection):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Ensuite, nous utilisons le code suivant pour initialiser la vue de collection et affecter notre classe de cellule, la source de données et le délégué:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Enfin, nous voulons que le titre sous l’image soit visible uniquement lorsque l’utilisateur l’a mis en surbrillance (in-Focus). Nous faisons cela avec le code suivant:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

Nous définissons la transparence de l’élément précédent qui perd le focus sur zéro (0) et la transparence de l’élément suivant gagne le focus sur 100%. Ces transitions sont également animées.


## <a name="configuring-the-collection-view-controller"></a>Configuration du contrôleur d’affichage de collection

À présent, nous devons effectuer la configuration finale sur notre vue de collection et autoriser le contrôleur à définir la propriété que nous avons définie afin que la vue de collection puisse être fermée une fois que l’utilisateur a fait une sélection.

Modifiez le `CityCollectionViewController.cs` fichier (créé automatiquement à partir de notre table de montage séquentiel) et faites-le ressembler à ce qui suit:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>Ensemble 

Maintenant que nous avons toutes les parties regroupées pour remplir et contrôler notre vue de collection, nous devons apporter les dernières modifications à notre vue principale pour tout réunir.

Modifiez le `ViewController.cs` fichier (créé automatiquement à partir de notre table de montage séquentiel) et faites-le ressembler à ce qui suit:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

Le code suivant affiche initialement l’élément sélectionné à partir `SelectedCity` de la propriété `AppDelegate` de et l’affiche à nouveau lorsque l’utilisateur a effectué une sélection à partir de la vue de collection:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>Test de l’application

Tout en étant en place, si vous générez et exécutez l’application, la vue principale est affichée avec la ville par défaut:

[![](collection-views-images/run01.png "Écran principal")](collection-views-images/run01.png#lightbox)

Si l’utilisateur clique sur le bouton **Sélectionner une vue** , la vue de collection s’affiche:

[![](collection-views-images/run02.png "Vue de collection")](collection-views-images/run02.png#lightbox)

Toute ville dont la propriété `CanSelect` a la `false` valeur est affichée grisée et l’utilisateur ne peut pas lui affecter le focus. Lorsque l’utilisateur met un élément en surbrillance (en le mettant en focus), le titre est affiché et il peut utiliser l’effet parallaxe pour incliner l’image en 3D.

Quand l’utilisateur clique sur une image Select, la vue de collection est fermée et la vue principale est réaffichée avec la nouvelle image:

[![](collection-views-images/run03.png "Nouvelle image sur l’écran d’accueil")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Création d’une disposition personnalisée et réorganisation d’éléments

L’une des principales fonctionnalités de l’utilisation d’une vue de collection est la possibilité de créer des dispositions personnalisées. Étant donné que tvOS hérite d’iOS, le processus de création d’une disposition personnalisée est le même. Pour plus d’informations, consultez notre documentation relative [à la présentation des vues de collection](~/ios/user-interface/controls/uicollectionview.md) .

Récemment ajoutés aux vues de collection pour iOS 9 était la possibilité d’autoriser facilement la réorganisation des éléments dans la collection. Là encore, étant donné que tvOS 9 est un sous-ensemble d’iOS 9, cette opération est effectuée de la même façon. Pour plus d’informations, consultez notre document sur [les modifications](~/ios/user-interface/controls/uicollectionview.md) de la vue de collection.


<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation des vues de collection au sein d’une application Xamarin. tvOS. Tout d’abord, elle a abordé tous les éléments qui composent la vue de collection. Ensuite, il a montré comment concevoir et implémenter une vue de collection à l’aide d’une table de montage séquentiel. Enfin, fournit des liens vers des informations sur la création de dispositions personnalisées et la réorganisation des éléments.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
