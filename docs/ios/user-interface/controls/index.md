---
title: Contrôles d’interface utilisateur dans Xamarin. iOS
description: Ce document contient des liens vers des guides qui décrivent les différents contrôles d’interface utilisateur iOS disponibles pour les développeurs Xamarin. iOS. Le contenu lié présente les alertes, les boutons, les vues de collection, les images, les contrôles manuels de l’appareil photo, les cartes, les étiquettes, les sélecteurs, les sélecteurs de dates et bien plus encore.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 28dc915e373ed16336551fa28fde8cbae8ee815e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657491"
---
# <a name="user-interface-controls-in-xamarinios"></a>Contrôles d’interface utilisateur dans Xamarin. iOS

Ce document présente quelques-uns des contrôles d’interface utilisateur iOS les plus courants et explique comment les utiliser.

## <a name="alertsalertsmd"></a>[Alertes](alerts.md)

À compter d’iOS 8, UIAlertController a terminé le remplacement de UIActionSheet et UIAlertView tous deux qui sont désormais dépréciés.

## <a name="buttonsbuttonsmd"></a>[Boutons](buttons.md)

La classe UIButton est utilisée pour représenter différents styles de bouton dans les écrans iOS. Cette section présente les différentes options d’utilisation des boutons dans iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Vues de collection](uicollectionview.md)

Les vues de collection, disponibles `UICollectionView` dans la classe, sont un nouveau concept dans iOS 6 qui introduit la présentation de plusieurs éléments à l’écran à l’aide de dispositions. Les modèles permettant de fournir des données `UICollectionView` à un pour créer des éléments et interagir avec ces éléments suivent les mêmes modèles de délégation et de source de données que ceux utilisés couramment dans le développement iOS.

## <a name="imagesimagemd"></a>[Images](image.md)

L’ajout d’images à votre application nécessite deux étapes: tout d’abord, ajoutez les images à votre projet. Ensuite, ajoutez des contrôles et du code pour les afficher sur un écran. Reportez-vous à l’article [utilisation d’images](~/ios/app-fundamentals/images-icons/index.md) pour obtenir une couverture plus détaillée de la gestion des images dans Xamarin. iOS.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Contrôles manuels de l’appareil photo](intro-to-manual-camera-controls.md)

Les contrôles manuels de l’appareil photo, `AVFoundation Framework` fournis par le dans iOS 8, permettent à une application mobile de prendre le contrôle total de l’appareil photo d’un appareil iOS. Ce niveau de contrôle affiné peut être utilisé pour créer des applications d’appareil photo de niveau professionnel et fournir des compositions d’artistes en modifiant les paramètres de l’appareil photo tout en utilisant une image ou une vidéo continue.

## <a name="mapsios-mapsindexmd"></a>[Cartes](ios-maps/index.md)

Les mappages sont une fonctionnalité commune à tous les systèmes d’exploitation mobiles modernes. iOS offre une prise en charge du mappage en mode natif via l’infrastructure du kit de cartes. Avec le kit de cartes, les applications peuvent facilement ajouter des cartes riches et interactives. Ces cartes peuvent être personnalisées de différentes façons, telles que l’ajout d’annotations pour marquer les emplacements sur une carte et la superposition de graphiques de formes arbitraires. Le kit de cartes offre une prise en charge intégrée pour l’indication de l’emplacement actuel d’un appareil.

## <a name="labelslabelsmd"></a>[Étiquettes](labels.md)

Le `UILabel` contrôle est utilisé pour afficher du texte en lecture seule et multiligne.

## <a name="pickers-and-date-pickerspickermd"></a>[Sélecteurs et sélecteurs de dates](picker.md)

Le contrôle sélecteur affiche un contrôle de type Wheel qui contient une liste déroulante de valeurs avec la valeur sélectionnée mise en surbrillance. Les utilisateurs font pivoter la roue pour sélectionner l’option de leur choix.

Un cas utilisateur spécifique pour les sélecteurs pour définir la date et/ou l’heure. Pour permettre à cet Apple de créer une sous-classe personnalisée de la classe UIPickerView appelée UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicateurs de progression et d’activité](progress-activity-indicator.md)

iOS offre deux méthodes principales pour indiquer la progression de votre application: Indicateurs d’activité (y compris un indicateur d’activité _réseau_ spécifique) et barres de progression.

## <a name="search-barssearchbarmd"></a>[Barres de recherche](searchbar.md)

Le UISearchBar est utilisé pour effectuer une recherche dans une liste de valeurs. 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Curseurs, commutateurs et contrôles segmentés](slider-switch-segmented-controls.md)

Le contrôle Slider permet une sélection simple d’une valeur numérique dans une plage. iOS utilise `UISwitch` comme entrée booléenne qui peut être représentée par un bouton radio sur d’autres plateformes. Un contrôle segmenté est un moyen organisé pour permettre aux utilisateurs d’interagir avec un petit nombre d’options.

## <a name="stack-viewuistackviewmd"></a>[Vue en pile](uistackview.md)

Le contrôle d’affichage de`UIStackView`la pile () tire parti de la puissance des classes de mise en page et de taille automatiques pour gérer une pile de sous-vues, horizontale ou verticale, qui répond de manière dynamique à l’orientation et à la taille de l’écran de l’appareil iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tableaux et cellules](tables/index.md)

Cette section présente les classes utilisées pour créer et afficher des tables, puis fournit des exemples de leur utilisation dans Xamarin. iOS. Il aborde l’utilisation de l’apparence par défaut pour les tables, la personnalisation de la disposition, l’implémentation de la modification et l’utilisation du concepteur iOS Xamarin pour concevoir une table visuellement. Parfois, l’affichage est évidemment une liste de lignes (par exemple, l’application musique) et, dans d’autres cas, il est difficile de reconnaître le contrôle de table (par exemple, modification dans l’application contacts ou une conversation dans l’application messages).

## <a name="text-inputtext-inputmd"></a>[Entrée de texte](text-input.md)

L’acceptation de l’entrée de texte de `UITextField` l’utilisateur est effectuée avec le pour les entrées sur une seule ligne et UITextView pour le texte modifiable sur plusieurs lignes. Vous pouvez faire glisser l’un de ces contrôles sur un écran et double-cliquer pour définir le texte initial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barres d’onglets et contrôleurs de barre d’onglets](creating-tabbed-applications.md)

les applications iOS utilisant une interface utilisateur de navigation à onglets sont créées à l’aide de la classe UITabBarController. Dans cet article, nous allons découvrir comment configurer une application avec onglets contenant plusieurs contrôleurs et vues. Nous examinerons ensuite comment charger un UITabBarController quand il ne s’agit pas du contrôleur racine, par exemple après un écran de connexion.

## <a name="web-viewsuiwebviewmd"></a>[Affichages Web](uiwebview.md)

Dans cet article, nous allons explorer chacun des trois affichages Web fournis par Apple: `UIWebView`, `WKWebview`, et `SFSafariViewController`, leurs similitudes et différences, et comment ils peuvent être utilisés.

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
