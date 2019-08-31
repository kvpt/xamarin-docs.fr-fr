---
title: Utilisation des vues de table tvOS dans Xamarin
description: Cet article traite de la conception et de l’utilisation des vues de table et des contrôleurs de vue table à l’intérieur d’une application Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0d93978d6f7b3dff6d0d7ebf7c9f9afbe3572079
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199912"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>Utilisation des vues de table tvOS dans Xamarin

_Cet article traite de la conception et de l’utilisation des vues de table et des contrôleurs de vue table à l’intérieur d’une application Xamarin. tvOS._

Dans tvOS, une vue de table est présentée sous la forme d’une seule colonne de lignes de défilement qui peut éventuellement être organisée en groupes ou sections. Les vues de table doivent être utilisées lorsque vous devez afficher une grande quantité de données de façon efficace pour l’utilisateur, de manière claire à comprendre.

Les vues de table sont généralement affichées d’un côté d’un [mode fractionné](~/ios/tvos/user-interface/split-views.md) en tant que navigation, avec les détails de l’élément sélectionné qui s’affichent sur le côté opposé:

[![](table-views-images/intro01.png "Exemple de vue table")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>À propos des vues de table

Un `UITableView` affiche une seule colonne de lignes de défilement comme une liste hiérarchique d’informations qui peuvent éventuellement être organisées en groupes ou sections: 

[![](table-views-images/table01.png "Un élément sélectionné")](table-views-images/table01.png#lightbox)

Apple propose les suggestions suivantes pour l’utilisation des tables:

- **Tenez compte de la largeur** -essayez de trouver le bon équilibre dans vos largeurs de table. Si la table est trop grande, il peut être difficile de l’analyser à partir d’une distance et de la retirer de la zone de contenu disponible. Si la table est trop étroite, les informations peuvent être tronquées ou renvoyées à la ligne, et il peut être difficile pour l’utilisateur de lire dans la salle.
- **Afficher rapidement le contenu** de la table: pour les listes de données volumineuses, chargez en différé le contenu et commencez à afficher les informations dès que la table est présentée à l’utilisateur. Si le chargement de la table prend trop de temps, l’utilisateur risque de perdre son intérêt pour votre application ou de penser qu’il est verrouillé.
- Informez l' **utilisateur de longs chargements de contenu** : si un long temps de chargement de table est inévitable, présentez une [barre de progression ou un indicateur d’activité](~/ios/tvos/user-interface/progress-indicators.md) afin qu’ils sachent que l’application n’a pas été verrouillée.

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>Types de cellules d’affichage de table

Un `UITableViewCell` est utilisé pour représenter les lignes de données individuelles dans la vue table. Apple a défini plusieurs types de cellule de table par défaut:

- **Par défaut** : ce type présente une image d’option sur le côté gauche de la cellule et le titre aligné à gauche à droite. 
- **Sous** -titre: ce type présente un titre aligné à gauche sur la première ligne et un sous-titre aligné à gauche plus petit sur la ligne suivante.
- **Valeur 1** : ce type présente un titre aligné à gauche avec un sous-titre plus clair, aligné à droite sur la même ligne.
- **Valeur 2** : ce type présente un titre aligné à droite avec un sous-titre plus clair, aligné à gauche sur la même ligne.

Tous les types de cellules d’affichage de table par défaut prennent également en charge les éléments graphiques tels que les indicateurs de divulgation ou les coches. 

En outre, vous pouvez définir un type de cellule d’affichage de table **personnalisé** et présenter une _cellule prototype_, que vous créez dans le concepteur d’interface ou via du code.

Apple propose les suggestions suivantes pour l’utilisation des cellules d’affichage de tableau:

- **Évitez** le découpage du texte-conservez les lignes de texte individuelles courtes afin qu’elles ne finissent pas tronquées. Les mots ou expressions tronqués sont difficiles à analyser de l’utilisateur dans la pièce.
- **Examinez l’état de la ligne ayant le focus** , car une ligne devient plus grande, avec des angles plus arrondis lorsqu’elle est activée, vous devez tester l’apparence de votre cellule dans tous les États. Les images ou le texte peuvent devenir découpés ou sembler incorrects dans l’État ciblé.
- **Utilisez des tables modifiables** . le déplacement ou la suppression de lignes de table est plus long sur TvOS que IOS. Vous devez décider avec précaution si cette fonctionnalité sera ajoutée à votre application tvOS ou ne vous gênera pas.
- **Créer des types de cellules personnalisées quand** cela est approprié, alors que les types de cellules de vue de table intégrés sont excellents dans de nombreuses situations, envisagez de créer des types de cellules personnalisées pour les informations non standard afin de fournir un meilleur contrôle et de mieux présenter les informations au utilisateur.

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>Utilisation des vues de table

Le moyen le plus simple d’utiliser les vues de table dans une application Xamarin. tvOS consiste à créer et à modifier leur apparence dans le concepteur d’interface.

Pour commencer, effectuez les étapes suivantes :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans Visual Studio pour Mac, démarrez un nouveau projet d’application tvOS et sélectionnez application de**vue unique** de l'**application** >  **tvOS** > , puis cliquez sur le bouton **suivant** : 

    [![](table-views-images/table02.png "Sélectionner une application avec affichage unique")](table-views-images/table02.png#lightbox)
1. Entrez un **nom** pour l’application, puis cliquez sur **suivant**: 

    [![](table-views-images/table03.png "Entrer un nom pour l’application")](table-views-images/table03.png#lightbox)
1. Ajustez le nom du **projet** et le nom de la **solution** , ou acceptez les valeurs par défaut et cliquez sur le bouton **créer** pour créer la solution: 

    [![](table-views-images/table04.png "Nom du projet et nom de la solution")](table-views-images/table04.png#lightbox)
1. Dans le **panneau solutions**, double-cliquez sur `Main.storyboard` le fichier pour l’ouvrir dans le concepteur iOS: 

    [![](table-views-images/table05.png "Fichier main. Storyboard")](table-views-images/table05.png#lightbox)
1. Sélectionner et supprimer le **contrôleur d’affichage par défaut**: 

    [![](table-views-images/table06.png "Sélectionner et supprimer le contrôleur d’affichage par défaut")](table-views-images/table06.png#lightbox)
1. Sélectionnez un **contrôleur d’affichage fractionné** dans la **boîte à outils** et faites-le glisser sur le aire de conception.
1. Par défaut, vous obtenez un [affichage fractionné](~/ios/tvos/user-interface/split-views.md) avec un **contrôleur d’affichage de navigation** et un contrôleur d’affichage de **table** dans la partie gauche et un **contrôleur d’affichage** dans la partie droite. Il s’agit de l’utilisation suggérée par Apple d’une vue de table dans tvOS: 

    [![](table-views-images/table08.png "Ajouter un affichage fractionné")](table-views-images/table08.png#lightbox)
1. Vous devez sélectionner chaque partie de la vue de table et lui affecter un nom de **classe** personnalisé dans l’onglet **widget** de l' **Explorateur de propriétés** afin de pouvoir y accéder ultérieurement dans C# le code. Par exemple, le **contrôleur d’affichage de table**: 

    [![](table-views-images/table09.png "Assigner un nom de classe")](table-views-images/table09.png#lightbox)
1. Veillez à créer une classe personnalisée pour le **contrôleur d’affichage de table**, la **vue Table** et les **cellules prototypes**. Visual Studio pour Mac ajoutera les classes personnalisées à l’arborescence de projet au fur et à mesure de leur création: 

    [![](table-views-images/table10.png "Classes personnalisées dans l’arborescence de projet")](table-views-images/table10.png#lightbox)
1. Ensuite, sélectionnez la vue de table dans la Aire de conception et ajustez ses propriétés en fonction des besoins. Tels que le nombre de **cellules prototype** et le **style** (plain ou Grouped): 

    [![](table-views-images/table11.png "Onglet widget")](table-views-images/table11.png#lightbox)
1. Pour chaque **cellule prototype**, sélectionnez-la et attribuez un **identificateur** unique sous l’onglet **widget** de l' **Explorateur de propriétés**. Cette étape est _très importante_ , car vous aurez besoin de cet identificateur plus tard lorsque vous remplirez la table. Par exemple `AttrCell`: 

    [![](table-views-images/table12.png "Onglet widget")](table-views-images/table12.png#lightbox)
1. Vous pouvez également choisir de présenter la cellule comme l’un des [types de cellule d’affichage de table par défaut](#table-view-cell-types) via la liste déroulante de **style** ou de la définir sur **personnalisé** et d’utiliser la aire de conception pour mettre en forme la cellule en faisant glisser des widgets d’interface utilisateur à partir de la **boîte à outils**: 

    [![](table-views-images/table13.png "Disposition de la cellule")](table-views-images/table13.png#lightbox)
1. Affectez un **nom** unique à chaque élément de l’interface utilisateur dans la conception de la cellule prototype dans l’onglet **widget** de l' **Explorateur** de propriétés C# afin de pouvoir y accéder ultérieurement dans le code: 

    [![](table-views-images/table14.png "Attribuer un nom")](table-views-images/table14.png#lightbox)
1. Répétez l’étape ci-dessus pour toutes les cellules prototypes dans la vue table.
1. Ensuite, assignez des classes personnalisées au reste de la conception de l’interface utilisateur, mettez en page le mode Détails et assignez des **noms** uniques à chaque élément de C# l’interface utilisateur en mode Détails afin de pouvoir y accéder également. Par exemple : 

    [![](table-views-images/table15.png "Disposition de l’interface utilisateur")](table-views-images/table15.png#lightbox)
1. Enregistrez vos modifications dans le Storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans Visual Studio, démarrez un nouveau projet d’application tvOS et sélectionnez **tvOS** > **affichage unique application** , puis entrez un nom pour votre application. Cliquez sur le bouton **OK** pour créer une solution: 

    [![](table-views-images/table02-vs.png "Sélectionner une application avec affichage unique")](table-views-images/table02-vs.png#lightbox)
1. Dans le **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier pour l’ouvrir dans le concepteur iOS: 

    [![](table-views-images/table05-vs.png "Fichier main. Storyboard")](table-views-images/table05-vs.png#lightbox)
1. Sélectionner et supprimer le **contrôleur d’affichage par défaut**: 

    [![](table-views-images/table06-vs.png "Sélectionner et supprimer le contrôleur d’affichage par défaut")](table-views-images/table06-vs.png#lightbox)
1. Sélectionnez un **contrôleur de vue fractionnée** à partir de la **boîte à outils** et faites-le glisser sur le aire de conception: 

    [![](table-views-images/table07-vs.png "Un contrôleur d’affichage fractionné")](table-views-images/table07-vs.png#lightbox)
1. Par défaut, vous obtenez un [affichage fractionné](~/ios/tvos/user-interface/split-views.md) avec un **contrôleur d’affichage de navigation** et un contrôleur d’affichage de **table** dans la partie gauche et un **contrôleur d’affichage** dans la partie droite. Il s’agit de l’utilisation suggérée par Apple d’une vue de table dans tvOS: 

    [![](table-views-images/table08-vs.png "Disposition de l’interface utilisateur")](table-views-images/table08-vs.png#lightbox)
1. Vous devez sélectionner chaque partie de la vue de table et lui affecter un nom de **classe** personnalisé dans l’onglet **widget** de l' **Explorateur de propriétés** afin de pouvoir y accéder ultérieurement dans C# le code. Par exemple, le **contrôleur d’affichage de table**: 

    [![](table-views-images/table09-vs.png "Onglet widget")](table-views-images/table09-vs.png#lightbox)
1. Veillez à créer une classe personnalisée pour le **contrôleur d’affichage de table**, la **vue Table** et les **cellules prototypes**. Visual Studio pour Mac ajoutera les classes personnalisées à l’arborescence de projet au fur et à mesure de leur création: 

    [![](table-views-images/table10-vs.png "Classes personnalisées dans l’arborescence de projet")](table-views-images/table10-vs.png#lightbox)
1. Ensuite, sélectionnez la vue de table dans la Aire de conception et ajustez ses propriétés en fonction des besoins. Tels que le nombre de **cellules prototype** et le **style** (plain ou Grouped): 

    [![](table-views-images/table11-vs.png "Onglet widget")](table-views-images/table11-vs.png#lightbox)
1. Pour chaque **cellule prototype**, sélectionnez-la et attribuez un **identificateur** unique sous l’onglet **widget** de l' **Explorateur de propriétés**. Cette étape est _très importante_ , car vous aurez besoin de cet identificateur plus tard lorsque vous remplirez la table. Par exemple `AttrCell`: 

    [![](table-views-images/table12-vs.png "Assigner un identificateur")](table-views-images/table12-vs.png#lightbox)
1. Vous pouvez également choisir de présenter la cellule comme l’un des [types de cellule d’affichage de table par défaut](#table-view-cell-types) via la liste déroulante de **style** ou de la définir sur **personnalisé** et d’utiliser la aire de conception pour mettre en forme la cellule en faisant glisser des widgets d’interface utilisateur à partir de la **boîte à outils**: 

    [![](table-views-images/table13-vs.png "La liste déroulante style")](table-views-images/table13-vs.png#lightbox)
1. Affectez un **nom** unique à chaque élément de l’interface utilisateur dans la conception de la cellule prototype dans l’onglet **widget** de l' **Explorateur** de propriétés C# afin de pouvoir y accéder ultérieurement dans le code: 

    [![](table-views-images/table14-vs.png "Onglet widget")](table-views-images/table14-vs.png#lightbox)
1. Répétez l’étape ci-dessus pour toutes les cellules prototypes dans la vue table.
1. Ensuite, assignez des classes personnalisées au reste de la conception de l’interface utilisateur, mettez en page le mode Détails et assignez des **noms** uniques à chaque élément de C# l’interface utilisateur en mode Détails afin de pouvoir y accéder également. Par exemple : 

    [![](table-views-images/table15.png "Disposition de l’interface utilisateur")](table-views-images/table15.png#lightbox)
1. Enregistrez vos modifications dans le Storyboard.

-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>Conception d’un modèle de données

Pour faciliter l’utilisation des informations qui s’affichent dans la vue table et pour faciliter la présentation des informations détaillées (lorsque l’utilisateur sélectionne ou met en surbrillance des lignes dans la vue table), créez une classe ou des classes personnalisées pour agir en tant que modèle de données pour les informations présentées. .

Prenons l’exemple d’une application de réservation de voyages qui contient une liste de **villes**, chacune contenant une liste unique d' **attractions** que l’utilisateur peut sélectionner. L’utilisateur pourra marquer une *passion comme favori*, sélectionner pour obtenir des *instructions* sur une attraction et *réserver un vol* à une ville donnée.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour créer le modèle de données d’une **attraction**, cliquez avec le bouton droit sur le nom du projet dans le **panneau solutions** puis sélectionnez **Ajouter** > **un nouveau fichier...** . Entrez `AttractionInformation` pour le **nom** et cliquez sur le bouton **nouveau** : 

[![](table-views-images/data01.png "Entrez AttractionInformation pour le nom")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour créer le modèle de données d’une **attraction**, cliquez avec le bouton droit sur le nom du projet dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **un nouvel élément...** . Sélectionnez **classe** et entrez `AttractionInformation` pour le **nom** , puis cliquez sur le bouton **Ajouter** : 

[![](table-views-images/data01-vs.png "Sélectionnez classe et entrez AttractionInformation pour le nom")](table-views-images/data01-vs.png#lightbox)

-----

Modifiez le `AttractionInformation.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

Cette classe fournit les propriétés permettant de stocker les informations relatives à un **attrait**donné.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Ensuite, cliquez de nouveau avec le bouton droit sur le nom du projet dans la **panneau solutions** et sélectionnez **Ajouter** > **un nouveau fichier...** . Entrez `CityInformation` pour le **nom** et cliquez sur le bouton **nouveau** : 

[![](table-views-images/data02.png "Entrez CityInformation pour le nom")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Ensuite, cliquez de nouveau avec le bouton droit sur le nom du projet dans la **Explorateur de solutions** et sélectionnez **Ajouter** > **un nouvel élément...** . Entrez `CityInformation` pour le **nom** et cliquez sur le bouton **Ajouter** : 

[![](table-views-images/data02-vs.png "Entrez CityInformation pour le nom")](table-views-images/data02-vs.png#lightbox)

-----

Modifiez le `CityInformation.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

Cette classe contient toutes les informations relatives à la **ville**de destination, une collection de **attractions** pour cette ville et fournit deux méthodes d’assistance`AddAttraction`() pour faciliter l’ajout de attractions à la ville.

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>Source de données de la vue de table

Chaque vue de table nécessite une source de`UITableViewDataSource`données () pour fournir les données de la table et générer les lignes nécessaires comme requis par la vue table.

Pour l’exemple ci-dessus, cliquez avec le bouton droit sur le nom du projet dans la **Explorateur de solutions**, sélectionnez **Ajouter** > **un nouveau** `AttractionTableDatasource` fichier..., puis cliquez sur le bouton **nouveau** pour créer. Modifiez ensuite le `AttractionTableDatasource.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

Jetons un coup d’œil à quelques sections de la classe en détail.

Tout d’abord, nous avons défini une constante pour contenir l’identificateur unique de la cellule prototype (il s’agit du même identificateur que celui affecté dans le concepteur d’interface ci-dessus), puis j’ai ajouté un raccourci au contrôleur d’affichage de table et créé le stockage pour nos données:

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

Ensuite, nous enregistrons le contrôleur d’affichage de table, puis créons et remplissons notre source de données (à l’aide des modèles de données définis ci-dessus) lors de la création de la classe:

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

Par exemple, la `PopulateCities` méthode crée simplement des objets de modèle de données en mémoire, mais ceux-ci peuvent facilement être lus à partir d’une base de données ou d’un service Web dans une application réelle:

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

La `NumberOfSections` méthode retourne le nombre de sections dans la table:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

Pour les vues de table de style **brut** , retourne toujours 1.

La `RowsInSection` méthode retourne le nombre de lignes dans la section actuelle:

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

Là encore, pour les vues de tables brutes, retourne le nombre total d’éléments dans la source de données.

La `TitleForHeader` méthode retourne le titre de la section donnée:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

Pour un type de vue de table **simple** , laissez le titre`""`vide ().

Enfin, lorsqu’elle est demandée par la vue table, créez et remplissez une cellule prototype `GetCell` à l’aide de la méthode: 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

Pour plus d’informations sur l’utilisation `UITableViewDatasource`d’un, consultez la documentation [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941) d’Apple.

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>Délégué de la vue de table

Chaque vue de table nécessite un délégué`UITableViewDelegate`() pour répondre à l’interaction de l’utilisateur ou à d’autres événements système sur la table.

Pour l’exemple ci-dessus, cliquez avec le bouton droit sur le nom du projet dans la **Explorateur de solutions**, sélectionnez **Ajouter** > **un nouveau** `AttractionTableDelegate` fichier..., puis cliquez sur le bouton **nouveau** pour créer. Modifiez ensuite le `AttractionTableDelegate.cs` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

Jetons un coup d’œil à plusieurs sections de cette classe dans les détails.

Tout d’abord, nous créons un raccourci vers le contrôleur d’affichage de table lors de la création de la classe:

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

Ensuite, lorsqu’une ligne est sélectionnée (l’utilisateur clique sur la surface tactile de l’Apple à distance), nous souhaitons marquer l' **attraction** représenté par la ligne sélectionnée en tant que favori:

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

Ensuite, lorsque l’utilisateur met en surbrillance une ligne (en lui donnant le focus à l’aide de la surface tactile Apple à distance), nous souhaitons présenter les détails de l' **attraction** représentée par cette ligne dans la section Détails de notre contrôleur de vue fractionnée:

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

La `CanFocusRow` méthode est appelée pour chaque ligne qui est sur le point d’être activée dans la vue table. Retourne `true` si la ligne peut obtenir le focus, sinon `false`retourne. Dans le cas de cet exemple, nous avons créé un événement `AttractionHighlighted` personnalisé qui sera déclenché sur chaque ligne lorsqu’il reçoit le focus.

Pour plus d’informations sur l’utilisation `UITableViewDelegate`d’un, consultez la documentation [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942) d’Apple.

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>Cellule d’affichage de table

Pour chaque cellule de prototype que vous avez ajoutée à la vue de table dans le concepteur d’interface, vous avez également créé une instance personnalisée de`UITableViewCell`la cellule d’affichage de table () pour vous permettre de remplir la nouvelle cellule (ligne) au fur et à mesure de sa création.

Pour l’exemple d’application, double-cliquez `AttractionTableCell.cs` sur le fichier pour l’ouvrir en vue de le modifier et faites-le ressembler à ce qui suit:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

Cette classe fournit le stockage pour l’objet de modèle de`AttractionInformation` données attractions (tel que défini ci-dessus) affiché dans la ligne donnée:

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

La `UpdateUI` méthode remplit les widgets d’interface utilisateur (ajoutés au prototype de la cellule dans le concepteur d’interface) selon les besoins:

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

Pour plus d’informations sur l’utilisation `UITableViewCell`d’un, consultez la documentation [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938) d’Apple.

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>Le contrôleur d’affichage de table

Un contrôleur d’affichage de`UITableViewController`table () gère une vue de table qui a été ajoutée à une table de montage séquentiel via le concepteur d’interface.

Pour l’exemple d’application, double-cliquez `AttractionTableViewController.cs` sur le fichier pour l’ouvrir en vue de le modifier et faites-le ressembler à ce qui suit:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

Examinons plus en détail cette classe. Tout d’abord, nous avons créé des raccourcis pour faciliter l’accès à la vue `DataSource` de `TableDelegate`table et à. Nous les utiliserons ultérieurement pour la communication entre la vue table sur le côté gauche de l’affichage fractionné et le mode Détails à droite.

Enfin, lorsque la vue de table est chargée en mémoire, nous créons des instances `AttractionTableDatasource` du `AttractionTableDelegate` et (les deux créées ci-dessus) et nous les attachons à la vue table.

Pour plus d’informations sur l’utilisation `UITableViewController`d’un, consultez la documentation [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523) d’Apple.

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>Ensemble

Comme indiqué au début de ce document, les vues de table sont généralement affichées d’un côté d’un [mode fractionné](~/ios/tvos/user-interface/split-views.md) en tant que navigation, avec les détails de l’élément sélectionné qui s’affichent sur le côté opposé. Par exemple : 

[![](table-views-images/intro01.png "Exemple d’exécution d’application")](table-views-images/intro01.png#lightbox)

Dans la mesure où il s’agit d’un modèle standard dans tvOS, nous allons examiner les étapes finales pour regrouper tout et faire interagir les côtés gauche et droit de la vue fractionnée.

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>Affichage des détails

Pour l’exemple de l’application de voyage présentée ci-dessus, une`AttractionViewController`classe personnalisée () est définie pour le contrôleur d’affichage standard présenté sur le côté droit de l’affichage fractionné comme vue détaillée:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

Ici, nous avons fourni l' **attrait** (`AttractionInformation`) affiché en tant que propriété et créé une `UpdateUI` méthode qui remplit les widgets d’interface utilisateur ajoutés à la vue dans le concepteur d’interface.

Nous avons également défini un raccourci vers le contrôleur de vue fractionnée`SplitView`() que nous allons utiliser pour communiquer les modifications à la vue de`AcctractionTableView`table ().

Enfin, des actions personnalisées (événements) ont été ajoutées `UIButton` aux trois instances créées dans le concepteur d’interface, qui permettent à l’utilisateur de marquer une _passion comme favori_, d’obtenir des _instructions_ sur une attraction et de _réserver un vol_ à un urbain.

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>Contrôleur d’affichage de navigation

Étant donné que le contrôleur d’affichage de table est imbriqué dans un contrôleur d’affichage de navigation sur le côté gauche de l’affichage fractionné, une classe personnalisée (`MasterNavigationController`) a été assignée au contrôleur d’affichage de navigation dans le concepteur d’interface et défini comme suit:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

Là encore, cette classe définit simplement quelques raccourcis pour faciliter la communication entre les deux côtés du contrôleur d’affichage fractionné:

- `SplitView`-Est un lien vers le contrôleur d’affichage fractionné`MainSpiltViewController`() auquel appartient le contrôleur d’affichage de navigation.
- `TableController`-Obtient le contrôleur d’affichage de`AttractionTableViewController`table () présenté comme vue supérieure dans le contrôleur d’affichage de navigation.

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>Le contrôleur d’affichage fractionné

Étant donné que le contrôleur de vue fractionnée est la base de notre application, nous avons`MasterSplitViewController`créé une classe personnalisée () pour celle-ci dans le concepteur d’interface et je l’ai définie comme suit:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

Tout d’abord, nous créons des raccourcis vers le côté **Détails** de la`AttractionViewController`vue fractionnée () et vers`MasterNavigationController`le côté **principal** (). Là encore, il est plus facile de communiquer entre les deux côtés.

Ensuite, lorsque le mode fractionné est chargé en mémoire, nous attachons le contrôleur du mode fractionné aux deux côtés de l’affichage fractionné et répondrons à l’utilisateur mettant en surbrillance un`AttractionHighlighted`attrait dans la vue table () en affichant les nouvelles attractions du côté **Détails** de Mode fractionné.

Consultez l’exemple d’application [tvTables](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-tvtable) pour une implémentation complète des vues de table dans un affichage fractionné.

## <a name="table-views-in-detail"></a>Affichages des tables en détail

Étant donné que tvOS est basé sur iOS, les vues de table et les contrôleurs de vue table sont conçus et se comportent de la même façon. Pour plus d’informations sur l’utilisation de la vue table dans une application Xamarin, consultez notre documentation iOS sur l' [utilisation des tableaux et des cellules](~/ios/user-interface/controls/tables/index.md) .

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation des vues de table au sein d’une application Xamarin. tvOS. Et a présenté un exemple de l’utilisation d’une vue de table à l’intérieur d’un mode fractionné, qui est l’utilisation classique d’une vue de table dans une application tvOS.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
