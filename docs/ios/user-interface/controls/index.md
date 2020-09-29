---
title: Contrôles d’interface utilisateur dans Xamarin. iOS
description: Ce document contient des liens vers des guides qui décrivent les différents contrôles d’interface utilisateur iOS disponibles pour les développeurs Xamarin. iOS. Le contenu lié présente les alertes, les boutons, les vues de collection, les images, les contrôles manuels de l’appareil photo, les cartes, les étiquettes, les sélecteurs, les sélecteurs de dates et bien plus encore.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 336e2468a3532b300697ed7fe596864e1bcf9622
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433972"
---
# <a name="user-interface-controls-in-xamarinios"></a>Contrôles d’interface utilisateur dans Xamarin. iOS

Ce document présente quelques-uns des contrôles d’interface utilisateur iOS les plus courants et explique comment les utiliser.

## <a name="alerts"></a>[Alertes](alerts.md)

À compter d’iOS 8, UIAlertController a terminé le remplacement de UIActionSheet et UIAlertView tous deux qui sont désormais dépréciés.

## <a name="buttons"></a>[Boutons](buttons.md)

La classe UIButton est utilisée pour représenter différents styles de bouton dans les écrans iOS. Cette section présente les différentes options d’utilisation des boutons dans iOS.

## <a name="collection-views"></a>[Vues de collection](uicollectionview.md)

Les vues de collection, disponibles dans la `UICollectionView` classe, sont un nouveau concept dans iOS 6 qui introduit la présentation de plusieurs éléments à l’écran à l’aide de dispositions. Les modèles permettant de fournir des données à un `UICollectionView` pour créer des éléments et interagir avec ces éléments suivent les mêmes modèles de délégation et de source de données que ceux utilisés couramment dans le développement iOS.

## <a name="images"></a>[Images](image.md)

L’ajout d’images à votre application nécessite deux étapes : tout d’abord, ajoutez les images à votre projet. Ensuite, ajoutez des contrôles et du code pour les afficher sur un écran. Reportez-vous à l’article [utilisation d’images](~/ios/app-fundamentals/images-icons/index.md) pour obtenir une couverture plus détaillée de la gestion des images dans Xamarin. iOS.

## <a name="manual-camera-controls"></a>[Contrôles manuels de l’appareil photo](intro-to-manual-camera-controls.md)

Les contrôles manuels de l’appareil photo, fournis par le `AVFoundation Framework` dans iOS 8, permettent à une application mobile de prendre le contrôle total de l’appareil photo d’un appareil iOS. Ce niveau de contrôle affiné peut être utilisé pour créer des applications d’appareil photo de niveau professionnel et fournir des compositions d’artistes en modifiant les paramètres de l’appareil photo tout en utilisant une image ou une vidéo continue.

## <a name="maps"></a>[Maps](ios-maps/index.md)

Les mappages sont une fonctionnalité commune à tous les systèmes d’exploitation mobiles modernes. iOS offre une prise en charge du mappage en mode natif via l’infrastructure du kit de cartes. Avec le kit de cartes, les applications peuvent facilement ajouter des cartes riches et interactives. Ces cartes peuvent être personnalisées de différentes façons, telles que l’ajout d’annotations pour marquer les emplacements sur une carte et la superposition de graphiques de formes arbitraires. Le kit de cartes offre une prise en charge intégrée pour l’indication de l’emplacement actuel d’un appareil.

## <a name="labels"></a>[Étiquettes](labels.md)

Le `UILabel` contrôle est utilisé pour afficher du texte en lecture seule et multiligne.

## <a name="pickers-and-date-pickers"></a>[Sélecteurs et sélecteurs de dates](picker.md)

Le contrôle sélecteur affiche un contrôle de type Wheel qui contient une liste déroulante de valeurs avec la valeur sélectionnée mise en surbrillance. Les utilisateurs font pivoter la roue pour sélectionner l’option de leur choix.

Un cas utilisateur spécifique pour les sélecteurs pour définir la date et/ou l’heure. Pour permettre à cet Apple de créer une sous-classe personnalisée de la classe UIPickerView appelée UIDatePicker.

## <a name="progress-and-activity-indicators"></a>[Indicateurs de progression et d’activité](progress-activity-indicator.md)

iOS offre deux méthodes principales pour indiquer la progression de votre application : les indicateurs d’activité (y compris un indicateur d’activité _réseau_ spécifique) et les barres de progression.

## <a name="search-bars"></a>[Barres de recherche](searchbar.md)

Le UISearchBar est utilisé pour effectuer une recherche dans une liste de valeurs. 

## <a name="sliders-switches-and-segmented-controls"></a>[Curseurs, commutateurs et contrôles segmentés](slider-switch-segmented-controls.md)

Le contrôle Slider permet une sélection simple d’une valeur numérique dans une plage. iOS utilise `UISwitch` comme entrée booléenne qui peut être représentée par un bouton radio sur d’autres plateformes. Un contrôle segmenté est un moyen organisé pour permettre aux utilisateurs d’interagir avec un petit nombre d’options.

## <a name="stack-view"></a>[Vue en pile](uistackview.md)

Le contrôle d’affichage de la pile ( `UIStackView` ) tire parti de la puissance des classes de mise en page et de taille automatiques pour gérer une pile de sous-vues, horizontale ou verticale, qui répond de manière dynamique à l’orientation et à la taille de l’écran de l’appareil iOS.

## <a name="tables-and-cells"></a>[Tableaux et cellules](tables/index.md)

Cette section présente les classes utilisées pour créer et afficher des tables, puis fournit des exemples de leur utilisation dans Xamarin. iOS. Il aborde l’utilisation de l’apparence par défaut pour les tables, la personnalisation de la disposition, l’implémentation de la modification et l’utilisation du concepteur iOS Xamarin pour concevoir une table visuellement. Parfois, l’affichage est évidemment une liste de lignes (par exemple, l’application musique) et, dans d’autres cas, il est difficile de reconnaître le contrôle de table (par exemple, modification dans l’application contacts ou une conversation dans l’application messages).

## <a name="text-input"></a>[Saisie de texte](text-input.md)

L’acceptation de l’entrée de texte de l’utilisateur est effectuée avec le `UITextField` pour les entrées sur une seule ligne et UITextView pour le texte modifiable sur plusieurs lignes. Vous pouvez faire glisser l’un de ces contrôles sur un écran et double-cliquer pour définir le texte initial.

## <a name="tab-bars-and-tab-bar-controllers"></a>[Barres d’onglets et contrôleurs de barre d’onglets](creating-tabbed-applications.md)

les applications iOS utilisant une interface utilisateur de navigation à onglets sont créées à l’aide de la classe UITabBarController. Dans cet article, nous allons découvrir comment configurer une application avec onglets contenant plusieurs contrôleurs et vues. Nous examinerons ensuite comment charger un UITabBarController quand il ne s’agit pas du contrôleur racine, par exemple après un écran de connexion.

## <a name="web-views"></a>[Vues web](webview.md)

Dans cet article, nous allons explorer les vues Web fournies par Apple, ainsi que `WKWebview` `SFSafariViewController` leurs similitudes et leurs différences, et comment elles peuvent être utilisées.

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](/samples/xamarin/ios-samples/controls)