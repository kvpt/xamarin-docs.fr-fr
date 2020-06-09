---
title: Utilisation des champs de recherche et de texte tvOS dans Xamarin
description: Ce document explique comment utiliser le texte et les champs de recherche dans une application tvOS créée avec Xamarin. Il fournit une vue d’ensemble globale des champs de texte et de recherche, et présente les claviers, l’intégration des storyboards, les modèles de données de recherche, etc.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 83edaf46519790d9674b7b537fc424ddd7a09a1c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566147"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Utilisation des champs de recherche et de texte tvOS dans Xamarin

Lorsque cela est nécessaire, votre application Xamarin. tvOS peut demander un petit morceau de texte à l’utilisateur (par exemple, des ID utilisateur et des mots de passe) à l’aide d’un champ de texte et du clavier visuel :

[![](text-fields-and-search-images/intro01.png "Sample Search Field")](text-fields-and-search-images/intro01.png#lightbox)

Vous pouvez éventuellement fournir la fonctionnalité de recherche par mot clé du contenu de l’application à l’aide d’un champ de recherche :

[![](text-fields-and-search-images/intro02.png "Sample Search Results")](text-fields-and-search-images/intro02.png#lightbox)

Ce document aborde les détails de l’utilisation de texte et de champs de recherche dans une application Xamarin. tvOS.

<a name="About-Text-and-Search-Fields"></a>

## <a name="about-text-and-search-fields"></a>À propos des champs de recherche et de texte

Comme indiqué ci-dessus, si nécessaire, votre Xamarin. tvOS peut présenter un ou plusieurs champs de texte pour collecter de petites quantités de texte de l’utilisateur à l’aide d’un clavier à l’écran (ou d’un clavier Bluetooth facultatif en fonction de la version de tvOS installée par l’utilisateur).

En outre, si votre application présente de grandes quantités de contenu à l’utilisateur (par exemple, une musique, des films ou une collection d’images), vous souhaiterez peut-être inclure un champ de recherche qui permet à l’utilisateur d’entrer une petite quantité de texte pour filtrer la liste des éléments disponibles.

<a name="Text-Fields"></a>

## <a name="text-fields"></a>Champs de texte

Dans tvOS, un champ de texte est présenté sous la forme d’une zone de saisie à hauteur fixe et à angle arrondi qui affichera un clavier visuel lorsque l’utilisateur cliquera dessus :

[![](text-fields-and-search-images/text01.png "Text Fields In tvOS")](text-fields-and-search-images/text01.png#lightbox)

Lorsque l’utilisateur déplace le [focus](~/ios/tvos/app-fundamentals/navigation-focus.md) sur un champ de texte donné, il s’agrandit et affiche une ombre profonde. Vous devrez garder cela à l’esprit lors de la conception de votre interface utilisateur, car les champs de texte peuvent chevaucher d’autres éléments d’interface utilisateur en cas de focus.

Apple propose les suggestions suivantes pour l’utilisation des champs de texte :

- **Utilisez l’entrée de texte avec parcimonie** -en raison de la nature du clavier visuel, l’entrée de longues sections de texte ou le remplissage de plusieurs champs de texte est fastidieux pour l’utilisateur. Une meilleure solution consiste à limiter la quantité d’entrées de texte à l’aide de listes de sélection ou de [boutons](~/ios/tvos/user-interface/buttons.md).
- **Utilisez des indicateurs pour communiquer** le champ de texte à usage peut afficher des « indications » d’espace réservé lorsqu’il est vide. Le cas échéant, utilisez des indications pour décrire l’objectif de votre champ de texte au lieu d’une étiquette distincte.
- **Sélectionnez le type de clavier par défaut approprié** -tvOS fournit plusieurs types de clavier différents et polyvalents, que vous pouvez spécifier pour votre champ de texte. Par exemple, le clavier d’adresse de messagerie peut faciliter l’entrée en permettant à l’utilisateur de sélectionner dans une liste d’adresses entrées récemment.
- Le **cas échéant, utilisez des champs de texte sécurisés** : un champ de texte sécurisé présente les caractères entrés en tant que points (au lieu des véritables lettres). Utilisez toujours un champ de texte sécurisé lors de la collecte d’informations sensibles, telles que des mots de passe.

<a name="Keyboards"></a>

## <a name="keyboards"></a>Claviers

Chaque fois que l’utilisateur clique sur un champ de texte dans l’interface utilisateur, un clavier à l’écran linéaire s’affiche. L’utilisateur utilise la surface tactile [Siri à distance](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) pour sélectionner des lettres individuelles à partir du clavier et entrer les informations demandées :

[![](text-fields-and-search-images/keyboard01.png "The Siri Remote keyboard")](text-fields-and-search-images/keyboard01.png#lightbox)

Si la vue actuelle contient plusieurs champs de texte, un bouton **suivant** s’affiche automatiquement pour permettre à l’utilisateur d’accéder au champ de texte suivant. Un bouton **terminé** s’affiche pour le dernier champ de texte qui se termine par l’entrée de texte et renvoie l’utilisateur à l’écran précédent.

À tout moment, l’utilisateur peut également appuyer sur le bouton de **menu** de l’entrée de texte Siri Remote to end, puis revenir à l’écran précédent.

Apple propose les suggestions suivantes pour l’utilisation des claviers à l’écran :

- **Sélectionnez le type de clavier par défaut approprié** -tvOS fournit plusieurs types de clavier différents et polyvalents, que vous pouvez spécifier pour votre champ de texte. Par exemple, le clavier d’adresse de messagerie peut faciliter l’entrée en permettant à l’utilisateur de sélectionner dans une liste d’adresses entrées récemment.
- Le **cas échéant, utilisez les affichages des accessoires du clavier** -en plus des informations standard qui sont toujours affichées, des vues accessoires facultatives (telles que des images ou des étiquettes) peuvent être ajoutées au clavier visuel pour clarifier l’objectif de l’entrée de texte ou pour aider l’utilisateur à entrer les informations requises.

Pour plus d’informations sur l’utilisation du clavier visuel, consultez [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType)d’Apple, [gestion du clavier](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [vues personnalisées pour](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) le Guide de programmation de l’entrée de données et du [texte dans](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) la documentation iOS.

<a name="Search"></a>

## <a name="search"></a>Recherche

Un champ de recherche présente un écran spécialisé qui fournit un champ de texte et un clavier visuel qui permet à l’utilisateur de filtrer une collection d’éléments affichés sous le clavier :

[![](text-fields-and-search-images/search01.png "Sample search results")](text-fields-and-search-images/search01.png#lightbox)

Lorsque l’utilisateur entre des lettres dans le champ de recherche, les résultats ci-dessous reflètent automatiquement les résultats de la recherche. À tout moment, l’utilisateur peut déplacer le focus sur les résultats et sélectionner l’un des éléments présentés.

Apple propose les suggestions suivantes pour l’utilisation des champs de recherche :

- **Fournir des recherches récentes** : dans la mesure où l’entrée de texte avec le Siri distant peut être fastidieuse et que les utilisateurs ont tendance à répéter les demandes de recherche, envisagez d’ajouter une section de résultats de recherche récents avant les résultats actuels sous la zone du clavier.
- **Lorsque cela est possible, limitez le nombre de résultats** , car une grande liste d’éléments peut être difficile à analyser et à parcourir par l’utilisateur, envisagez de limiter le nombre de résultats renvoyés.
- Le **cas échéant, fournissez des filtres de résultats de recherche** : si le contenu fourni par votre application se prête lui-même, envisagez d’ajouter des barres d’étendue pour permettre à l’utilisateur de filtrer davantage les résultats de recherche retournés.

Pour plus d’informations, consultez Référence de la [classe UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)d’Apple.

<a name="Working-with-Text-Fields"></a>

## <a name="working-with-text-fields"></a>Utilisation des champs de texte

Le moyen le plus simple d’utiliser des champs de texte dans une application Xamarin. tvOS consiste à les ajouter à la conception de l’interface utilisateur à l’aide du concepteur iOS.

Effectuez les actions suivantes :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur le fichier pour l' `Main.storyboard` ouvrir et le modifier.
1. Faites glisser un ou plusieurs **champs de texte** int l’aire de conception sur une vue :

    [![](text-fields-and-search-images/text02.png "A Text Field")](text-fields-and-search-images/text02.png#lightbox)
1. Sélectionnez les **champs de texte** et attribuez à chacun un **nom** unique dans l’onglet **widget** du **panneau Propriétés**:

    [![](text-fields-and-search-images/text03.png "The Widget tab of the Properties Pad")](text-fields-and-search-images/text03.png#lightbox)
1. Dans la section **champ de texte** , vous pouvez définir des éléments tels que l’indicateur d' **espace réservé** et la **valeur**par défaut :

    [![](text-fields-and-search-images/text04.png "The Text Field section")](text-fields-and-search-images/text04.png#lightbox)
1. Faites défiler la liste pour définir des propriétés telles que vérification de l' **orthographe**, mise en **majuscules** et **type de clavier**par défaut :

    [![](text-fields-and-search-images/text05.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05.png#lightbox)
1. Enregistrez les modifications apportées à votre Storyboard.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir et le modifier.
1. Faites glisser un ou plusieurs **champs de texte** int l’aire de conception sur une vue :

    [![](text-fields-and-search-images/text02-vs.png "A Text Field")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Sélectionnez les **champs de texte** et attribuez à chacun un **nom** unique dans l’onglet **widget** de l' **Explorateur de propriétés**:

    [![](text-fields-and-search-images/text03-vs.png "The Widget tab")](text-fields-and-search-images/text03-vs.png#lightbox)
1. Dans la section **champ de texte** , vous pouvez définir des éléments tels que l’indicateur d' **espace réservé** et la **valeur**par défaut :

    [![](text-fields-and-search-images/text04-vs.png "The Text Field section")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Faites défiler la liste pour définir des propriétés telles que vérification de l' **orthographe**, mise en **majuscules** et **type de clavier**par défaut :

    [![](text-fields-and-search-images/text05-vs.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05-vs.png#lightbox)
1. Enregistrez les modifications apportées à votre Storyboard.

-----

Dans le code, vous pouvez obtenir ou définir la valeur d’un champ de texte à l’aide de sa `Text` propriété :

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

Vous pouvez éventuellement utiliser les `Started` événements de `Ended` champ de texte et pour répondre au début et à la fin de la saisie de texte.

<a name="Working-with-Search-Fields"></a>

## <a name="working-with-search-fields"></a>Utilisation des champs de recherche

Le moyen le plus simple d’utiliser les champs de recherche dans une application Xamarin. tvOS consiste à les ajouter à la conception de l’interface utilisateur à l’aide du concepteur d’interface.

Effectuez les actions suivantes :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **panneau solutions**, double-cliquez sur le fichier pour l' `Main.storyboard` ouvrir et le modifier.
1. Faites glisser un nouveau contrôleur d’affichage de collection vers le Storyboard pour présenter les résultats de la recherche de l’utilisateur :

    [![](text-fields-and-search-images/search02.png "A Collection View Controller")](text-fields-and-search-images/search02.png#lightbox)
1. Dans l’onglet **widget** du **panneau Propriétés**, utilisez `SearchResultsViewController` pour la **classe** et `SearchResults` pour l’ID de **Storyboard**:

    [![](text-fields-and-search-images/search03.png "The Widget tab")](text-fields-and-search-images/search03.png#lightbox)
1. Sélectionnez le **prototype de cellule** sur l’aire de conception.
1. Dans l’onglet **widget** de l' **Explorateur de propriétés**, utilisez `SearchResultCell` pour **la classe** et `ImageCell` pour l' **identificateur**:

    [![](text-fields-and-search-images/search04.png "The Widget tab")](text-fields-and-search-images/search04.png#lightbox)
1. Mettez en forme la conception du **prototype de cellule** et exposez chaque élément avec un **nom** unique dans l’onglet **widget** de l' **Explorateur de propriétés**:

    [![](text-fields-and-search-images/search05.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05.png#lightbox)
1. Enregistrez les modifications apportées à votre Storyboard.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Main.storyboard` pour l’ouvrir et le modifier.
1. Faites glisser un nouveau contrôleur d’affichage de collection vers le Storyboard pour présenter les résultats de la recherche de l’utilisateur :

    [![](text-fields-and-search-images/seach02-vs.png "A Collection View Controller")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. Dans l’onglet **widget** de l' **Explorateur de propriétés**, utilisez `SearchResultsViewController` pour la **classe** et `SearchResults` pour l' **ID de Storyboard**:

    [![](text-fields-and-search-images/search03-vs.png "The Widget tab")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Sélectionnez le **prototype de cellule** sur l’aire de conception.
1. Dans l’onglet **widget** de l' **Explorateur de propriétés**, utilisez `SearchResultCell` pour **la classe** et `ImageCell` pour l' **identificateur**:

    [![](text-fields-and-search-images/search04-vs.png "The Widget tab")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Mettez en forme la conception du **prototype de cellule** et exposez chaque élément avec un **nom** unique dans l’onglet **widget** de l' **Explorateur de propriétés**:

    [![](text-fields-and-search-images/search05-vs.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Enregistrez les modifications apportées à votre Storyboard.

-----

<a name="Provide-a-Data-Model"></a>

### <a name="provide-a-data-model"></a>Fournir un modèle de données

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Ensuite, vous devez fournir une classe pour agir en tant que modèle de données pour les résultats que l’utilisateur recherche. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter**  >  **un nouveau fichier...**  >  **Général**  >  **Classe vide** et fournissez un **nom**:

[![](text-fields-and-search-images/search06.png "Select Empty Class and provide a Name")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Ensuite, vous devez fournir une classe pour agir en tant que modèle de données pour les résultats que l’utilisateur recherche. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Ajouter**  >  **un nouvel élément...**  >  **Apple**  >  **Divers**  >  **Et** fournissez un **nom**:

[![](text-fields-and-search-images/search06-vs.png "Select Class and provide a Name")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Par exemple, une application qui permet à l’utilisateur de rechercher une collection d’images par titre et par mot clé peut se présenter comme suit :

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell"></a>

### <a name="the-collection-view-cell"></a>Cellule d’affichage de collection

Une fois le modèle de données en place, modifiez la **cellule du prototype** ( `SearchResultViewCell.cs` ) et faites en sorte qu’elle ressemble à ce qui suit :

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

La `UpdateUI` méthode sera utilisée pour afficher des champs individuels des éléments **PictureInformation** (la `PictureInfo` propriété) dans les éléments d’interface utilisateur nommés chaque fois que la propriété est mise à jour. Par exemple, l’image et le titre associés à l’image.

<a name="The-Collection-View-Controller"></a>

### <a name="the-collection-view-controller"></a>Contrôleur d’affichage de collection

Modifiez ensuite le contrôleur d’affichage de collection des résultats de la recherche ( `SearchResultsViewController.cs` ) et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

Tout d’abord, l' `IUISearchResultsUpdating` interface est ajoutée à la classe pour gérer le filtre de contrôleur de recherche mis à jour par l’utilisateur :

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Une constante est également définie pour spécifier l’ID de la **cellule prototype** (qui correspond à l’ID défini dans le concepteur d’interface ci-dessus) qui sera utilisé ultérieurement lorsque le contrôleur de collection demandera une nouvelle cellule :

```csharp
public const string CellID = "ImageCell";
```

Le stockage est créé pour la liste complète des éléments recherchés, le terme de filtre de recherche et la liste des éléments correspondant à ce terme :

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

Lorsque la `SearchFilter` est modifiée, la liste des éléments correspondants est mise à jour et le contenu de la vue de collection est rechargé. La `FindPictures` routine est chargée de rechercher les éléments qui correspondent au nouveau terme de recherche :

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

La valeur de la `SearchFilter` est mise à jour (ce qui met à jour la vue de collection des résultats) lorsque l’utilisateur modifie le filtre dans le contrôleur de recherche :

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

La `PopulatePictures` méthode remplit initialement la collection d’éléments disponibles :

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

Dans le cadre de cet exemple, tous les exemples de données sont créés dans la mémoire lors du chargement du contrôleur d’affichage de collection. Dans une application réelle, ces données seraient probablement lues à partir d’une base de données ou d’un service Web, et uniquement si nécessaire pour éviter de surExécuter la mémoire limitée d’Apple TV.

Les `NumberOfSections` `GetItemsCount` méthodes et fournissent le nombre d’éléments correspondants :

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

La `GetCell` méthode retourne une nouvelle **cellule prototype** (basée sur le `CellID` défini ci-dessus dans la table de montage séquentiel) pour chaque élément de la vue de collection :

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

La `WillDisplayCell` méthode est appelée avant l’affichage de la cellule, afin qu’elle puisse être configurée :

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

La `DidUpdateFocus` méthode fournit un retour visuel à l’utilisateur lorsqu’il met en surbrillance des éléments dans la vue de collection des résultats :

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

Enfin, la `ItemSelected` méthode gère l’utilisateur qui sélectionne un élément (en cliquant sur la surface tactile avec Siri distante) dans la vue de collection des résultats :

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

Si le champ de recherche a été présenté sous la forme d’une vue de boîte de dialogue modale (en haut de la vue qui l’appelle), utilisez la `DismissViewController` méthode pour faire disparaître le mode recherche lorsque l’utilisateur sélectionne un élément. Pour cet exemple, le champ de recherche est présenté en tant que contenu d’un onglet de vue d’onglet. il n’est donc pas fermé ici.

Pour plus d’informations sur les vues de collection, consultez notre documentation sur l' [utilisation des affichages de collection](~/ios/tvos/user-interface/collection-views.md) .

<a name="Presenting the Search Field"></a>

### <a name="presenting-the-search-field"></a>Présentation du champ de recherche

Il existe deux façons principales de présenter un champ de recherche (ainsi que le clavier visuel et les résultats de la recherche associés) à l’utilisateur dans tvOS :

- **Vue modale** de la boîte de dialogue : le champ de recherche peut être présenté sur la vue actuelle et le contrôleur d’affichage sous la forme d’un affichage de boîte de dialogue modale plein écran. Cela s’effectue généralement en réponse à l’utilisateur qui clique sur un bouton ou un autre élément d’interface utilisateur. La boîte de dialogue est fermée lorsque l’utilisateur sélectionne un élément dans les résultats de la recherche.
- **Afficher le contenu** : le champ de recherche est une partie directe d’une vue donnée. Par exemple, en tant que contenu d’un onglet de recherche dans un contrôleur d’affichage d’onglets.

Pour obtenir l’exemple d’une liste d’images pouvant faire l’objet d’une recherche, le champ de recherche s’affiche sous l’onglet Rechercher et le contrôleur d’affichage des onglets de recherche se présente comme suit :

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

Tout d’abord, une constante est définie et correspond à l' **identificateur de Storyboard** affecté au contrôleur d’affichage de collection des résultats de la recherche dans le concepteur d’interface :

```csharp
public const string SearchResultsID = "SearchResults";
```

Ensuite, la `ShowSearchController` méthode crée un nouveau contrôleur de collection d’affichages de recherche et l’affiche comme nécessaire :

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

Dans la méthode ci-dessus, une fois qu’un `SearchResultsViewController` a été instancié à partir de la table de montage séquentiel, un nouveau `UISearchController` est créé pour présenter le champ de recherche et le clavier visuel à l’utilisateur. La collection des résultats de la recherche (telle que définie par le `SearchResultsViewController` ) sera affichée sous ce clavier.

Ensuite, le `SearchBar` est configuré avec des informations telles que l’indicateur d' **espace réservé** . Cela fournit aux utilisateurs des informations sur le type de recherche en cours de préformation.

Le champ de recherche est ensuite présenté à l’utilisateur de l’une des deux manières suivantes :

- **Vue modale** de la boîte de dialogue-la `PresentViewController` méthode est appelée pour présenter la recherche sur la vue existante, en plein écran.
- **Afficher le contenu** -un `UISearchContainerViewController` est créé pour contenir le contrôleur de recherche. Un `UINavigationController` est créé pour contenir le conteneur de recherche, puis le contrôleur de navigation est ajouté au contrôleur d’affichage `AddChildViewController (navController)` et la vue présentée `View.Add (navController.View)` .

Enfin, et à nouveau en fonction du type de présentation, `ViewDidLoad` la `ViewDidAppear` méthode ou appellera la `ShowSearchController` méthode pour présenter la recherche à l’utilisateur :

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

Lorsque l’application est exécutée et que l’onglet de recherche est sélectionné par l’utilisateur, la liste complète des éléments non filtrés est présentée à l’utilisateur :

[![](text-fields-and-search-images/intro02.png "Default search results")](text-fields-and-search-images/intro02.png#lightbox)

Lorsque l’utilisateur commence à entrer un terme de recherche, la liste des résultats est filtrée en fonction de ce terme et mis à jour automatiquement :

[![](text-fields-and-search-images/intro03.png "Filtered search results")](text-fields-and-search-images/intro03.png#lightbox)

À tout moment, l’utilisateur peut basculer le focus sur un élément dans les résultats de la recherche, puis cliquer sur la surface tactile de la télécommande Siri pour la sélectionner.

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a abordé la conception et l’utilisation de texte et de champs de recherche à l’intérieur d’une application Xamarin. tvOS. Il a montré comment créer du texte et rechercher du contenu de collection dans le concepteur d’interface et qu’il a démontré deux façons différentes de présenter un champ de recherche à l’utilisateur dans tvOS.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
