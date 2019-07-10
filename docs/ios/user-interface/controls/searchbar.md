---
title: Barres de recherche dans Xamarin.iOS
description: Ce document décrit comment utiliser des barres de recherche dans Xamarin.iOS. Il explique comment créer des barres de recherche dans une table de montage séquentiel et par programme.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2017
ms.openlocfilehash: 75abb943dbc56d7b4213e0c36c19ff338182ae8a
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674870"
---
# <a name="search-bars-in-xamarinios"></a>Barres de recherche dans Xamarin.iOS

Le UISearchBar est utilisé pour effectuer des recherches dans une liste de valeurs. 

Il contient trois composants principaux : 

- Un champ utilisé pour entrer du texte. Les utilisateurs peuvent utiliser cette option pour entrer leur terme de recherche.
- Bouton Effacer, à supprimer n’importe quel texte à partir du champ de recherche.
- Un bouton Annuler pour quitter la fonction de recherche.

![Barre de recherche](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implémentation de la barre de recherche

Pour implémenter le début de barre de recherche en instanciant un :

```csharp
searchBar = new UISearchBar();
```

Et puis les placer. L’exemple ci-dessous montre comment placer dans la barre de navigation ou dans le HeaderView d’une Table :

```csharp
NavigationItem.TitleView = searchBar;

\\or

TableView.TableHeaderView = searchBar;
```

Définition des propriétés sur la barre de recherche :

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propriétés de la barre de recherche](searchbar-images/image6.png)

Déclencher la `SearchButtonClicked` événement lorsque l’utilisateur appuie sur le bouton de recherche. Cela permet d’appeler votre logique de recherche :

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Pour plus d’informations sur la gestion de la présentation de la barre de recherche et les résultats de recherche, reportez-vous à la [recherche contrôleur](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) Recipe (Recette).

## <a name="using-the-search-bar-in-the-designer"></a>À l’aide de la barre de recherche dans le Concepteur

Le concepteur propose deux options pour l’implémentation d’une barre de recherche dans le Concepteur

- Barre de recherche
- Barre de recherche avec le contrôleur d’affichage de recherche (déconseillé)

![Contrôles de barre de recherche dans le Concepteur](searchbar-images/image2.png)

Utilisez le panneau de propriété pour définir des propriétés sur la barre de recherche

![Concepteur de propriétés de barre de recherche](searchbar-images/image3.png)

Ces propriétés sont expliquées ci-dessous :

- **Invite de texte, l’espace réservé,** – ces propriétés sont utilisées pour proposer et indiquer comment les utilisateurs doivent utiliser la barre de recherche. Par exemple, si votre application affiche une liste des magasins, vous pouvez utiliser la propriété d’invite pour signaler que les utilisateurs peuvent « entrer une ville, le nom de l’article ou le Code postal »
- **Rechercher le Style** – vous pouvez définir la barre de recherche pour être **Prominent** ou **minimale**. À l’aide de l’importante colorer tout le reste à l’écran, à l’exception de la recherche de la barre, provoquant le focus à dessiner à la barre de recherche. La barre de recherche du style minimale s’intégreront avec son environnement.
- **Fonctionnalités** : l’activation de ces propriétés n’affiche que l’élément d’interface utilisateur. La fonctionnalité doit être implémentée pour ces en déclenchant l’événement correct comme indiqué dans le [documentation sur les API de barre de recherche](xref:UIKit.UISearchBar)
    - Affiche les résultats de recherche / signets – affiche une icône de résultats de recherche ou des signets sur la barre de recherche
    - Affiche le bouton Annuler – permet aux utilisateurs de quitter la fonction de recherche. Il est recommandé que cette option est sélectionnée.
    - Affiche barre d’outils : Cela permet aux utilisateurs limiter l’étendue de leur recherche. Par exemple, lors de la recherche dans l’application musique l’utilisateur peut sélectionner si qu’ils veulent rechercher Apple Music ou leur bibliothèque pour une chanson donnée ou d’un artiste. Pour afficher les différentes options, ajoutez un tableau de titres pour le **ScopeBarTitles** propriété.
    ![Titres de portée de barre de recherche](searchbar-images/image4.png)

- **Comportement de texte** – ces options sont utilisées pour traiter le mode de formatage de l’entrée d’utilisateur lorsqu’il tape. Mise en majuscules définira le début de chaque mot ou une phrase, ou tous les caractères en majuscules. Correction et la vérification de l’orthographe avec invitent l’utilisateur avec des suggestions de mots de frappe.
- **Clavier** : contrôles le style de clavier affiché pour l’entrée, et par conséquent, les clés sont disponibles sur le clavier. Cela inclut le pavé numérique, remplissage du téléphone, E-mail, URL, ainsi que d’autres options.
- **Apparence** : contrôle le style d’apparence du clavier et seront soit sombre ou clair à thème.
- **Touche retour** : modifier l’étiquette sur la touche Retour afin de mieux refléter les mesures. Valeurs prises en charge incluent Go, Join, Next, itinéraire, terminé et recherche.
- **Sécuriser** – indique si l’entrée est masquée (par exemple pour une entrée de mot de passe).

## <a name="related-links"></a>Liens associés

- [Contrôleur de recherche](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
