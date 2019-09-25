---
title: Rechercher des barres dans Xamarin. iOS
description: Ce document explique comment utiliser les barres de recherche dans Xamarin. iOS. Il explique comment créer des barres de recherche par programmation et dans une table de montage séquentiel.
ms.prod: xamarin
ms.assetid: 22A8249A-19C6-4734-8331-E49FE3170771
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/11/2017
ms.openlocfilehash: 36e339139a0a7f853a770fdb188b5f03ee93f7ee
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70283357"
---
# <a name="search-bars-in-xamarinios"></a>Rechercher des barres dans Xamarin. iOS

Le UISearchBar est utilisé pour effectuer une recherche dans une liste de valeurs.

Il contient trois composants principaux :

- Champ utilisé pour entrer du texte. Les utilisateurs peuvent l’utiliser pour entrer leur terme de recherche.
- Bouton Effacer pour supprimer tout texte du champ de recherche.
- Bouton Annuler pour quitter la fonction de recherche.

![Barre de recherche](searchbar-images/image1.png)

## <a name="implementing-the-search-bar"></a>Implémentation de la barre de recherche

Pour implémenter la barre de recherche, commencez par instancier une nouvelle :

```csharp
searchBar = new UISearchBar();
```

Puis placez-le. L’exemple ci-dessous montre comment le placer dans la barre de navigation ou dans le HeaderView d’un tableau :

```csharp
NavigationItem.TitleView = searchBar;

// or

TableView.TableHeaderView = searchBar;
```

Définition des propriétés dans la barre de recherche :

```csharp
 searchBar = new UISearchBar(){
                Placeholder = "Enter your search Item",
                Prompt = "Search Entered here",
                ShowsScopeBar = true,
                ScopeButtonTitles = new string[]{ "Boston", "London", "SF" },
            };
```

![Propriétés de la barre de recherche](searchbar-images/image6.png)

Déclenchez `SearchButtonClicked` l’événement lorsque le bouton de recherche est enfoncé. Cela appellera votre logique de recherche :

```csharp
searchBar.SearchButtonClicked += (sender, e) => {
                Search ();
            };
```

Pour plus d’informations sur la gestion de la présentation de la barre de recherche et des résultats de recherche, reportez-vous à la recette du [contrôleur de recherche](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller) .

## <a name="using-the-search-bar-in-the-designer"></a>Utilisation de la barre de recherche dans le concepteur

Le concepteur propose deux options pour implémenter une barre de recherche dans le concepteur.

- Barre de recherche
- Barre de recherche avec contrôleur d’affichage de la recherche (déconseillé)

![Contrôles de la barre de recherche dans le concepteur](searchbar-images/image2.png)

Utilisez le panneau des propriétés pour définir les propriétés de la barre de recherche

![Concepteur des propriétés de la barre de recherche](searchbar-images/image3.png)

Ces propriétés sont expliquées ci-dessous :

- **Texte, espace réservé, invite** : ces propriétés sont utilisées pour suggérer et indiquer comment les utilisateurs doivent utiliser la barre de recherche. Par exemple, si votre application affiche une liste de magasins, vous pouvez utiliser la propriété prompt pour indiquer que les utilisateurs peuvent « entrer une ville, un nom d’histoire ou un code postal »
- **Style de recherche** : vous pouvez définir la barre de recherche pour qu’elle soit **visible** ou **minimale**. À l’aide de l’élément le plus visible, vous allez teinter tout le reste à l’écran, à l’exception de la barre de recherche, ce qui entraîne le dessin du focus sur la barre de recherche. La barre de recherche de style minimale sera fusionnée avec son environnement.
- **Fonctionnalités** : l’activation de ces propriétés affiche uniquement l’élément d’interface utilisateur. La fonctionnalité doit être implémentée pour ces derniers en déclenchant l’événement correct, comme détaillé dans les documents de l’API de la [barre de recherche](xref:UIKit.UISearchBar)
  - Affiche le bouton résultats de la recherche/signets – affiche les résultats de la recherche ou l’icône de signets dans la barre de recherche
  - Affiche le bouton Annuler : permet aux utilisateurs de quitter la fonction de recherche. Il est recommandé de sélectionner cette option.
  - Affiche la barre d’étendue : permet aux utilisateurs de limiter l’étendue de leur recherche. Par exemple, lors de la recherche dans l’application musique, l’utilisateur peut choisir s’il souhaite effectuer une recherche dans Apple Music ou dans sa bibliothèque pour obtenir une chanson ou un artiste particulier. Pour afficher différentes options, ajoutez un tableau de titres à la propriété **ScopeBarTitles** .
  ![Titres de l’étendue de la barre de recherche](searchbar-images/image4.png)

- **Comportement du texte** : ces options sont utilisées pour traiter la manière dont l’entrée utilisateur est mise en forme lors de la saisie. La mise en majuscules définit le début de chaque mot ou phrase, ou chaque caractère en majuscules. Correction et vérification de l’orthographe avec invite l’utilisateur à saisir l’orthographe suggérée des mots au fur et à mesure de leur saisie.
- **Clavier** : contrôle le style de clavier affiché pour l’entrée et, par conséquent, les touches disponibles sur le clavier. Cela comprend le pavé numérique, le pavé téléphonique, l’adresse de messagerie et l’URL, ainsi que d’autres options.
- **Apparence** : contrôle le style d’apparence du clavier et est sombre ou clair.
- **Clé de retour** : modifiez l’étiquette sur la clé de retour pour mieux refléter l’action qui sera entreprise. Les valeurs prises en charge sont les suivantes : Go, Join, Next, route, Done et Search.
- **Secure** : indique si l’entrée est masquée (par exemple, pour une entrée de mot de passe).

## <a name="related-links"></a>Liens associés

- [Contrôleur de recherche](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
