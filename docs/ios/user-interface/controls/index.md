---
title: Contrôles d’Interface utilisateur dans Xamarin.iOS
description: Ce document contient des liens vers des guides qui décrivent les différentes iOS contrôles d’interface utilisateur qui permettent aux développeurs de Xamarin.iOS. Contenu lié présente les alertes, boutons, vues de collection, images, contrôles de la caméra manuelle, cartes, étiquettes, sélecteurs, les sélecteurs de dates et bien plus encore.
ms.prod: xamarin
ms.assetid: C00EA232-ADCC-42AD-BF86-B526414A21C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: fab653eb24b504fa17e6f2eac32915fd88580817
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827830"
---
# <a name="user-interface-controls-in-xamarinios"></a>Contrôles d’Interface utilisateur dans Xamarin.iOS

Ce document présente certains des contrôles d’interface utilisateur iOS courants et comment les utiliser.

## <a name="alertsalertsmd"></a>[Alertes](alerts.md)

À compter d’iOS 8, UIAlertController a terminé UIActionSheet remplacé et UIAlertView qui sont désormais déconseillés.

## <a name="buttonsbuttonsmd"></a>[Boutons](buttons.md)

La classe UIButton est utilisée pour représenter les divers différents styles de bouton dans les écrans d’iOS. Cette section présente les différentes options pour travailler avec des boutons dans iOS.

## <a name="collection-viewsuicollectionviewmd"></a>[Vues de collection](uicollectionview.md)

Vues de collection, disponibles dans le `UICollectionView` de classe, sont un nouveau concept dans iOS 6 présentant les présenter plusieurs éléments sur l’écran à l’aide de dispositions. Les modèles pour fournir des données à un `UICollectionView` pour créer des éléments et d’interagir avec ces suivi d’éléments que la délégation même et source de données modèles couramment utilisés dans le développement iOS.

## <a name="imagesimagemd"></a>[Images](image.md)

Ajout d’images à votre application requiert deux étapes : tout d’abord, ajoutez les images à votre projet ; Ensuite, ajoutez des contrôles et du code pour les afficher sur un écran. Reportez-vous à la [utilisation des Images](~/ios/app-fundamentals/images-icons/index.md) article pour plus d’une couverture d’image gère dans Xamarin.iOS.

## <a name="manual-camera-controlsintro-to-manual-camera-controlsmd"></a>[Contrôles manuels de l’appareil photo](intro-to-manual-camera-controls.md)

Les contrôles de caméra manuel, fournis par le `AVFoundation Framework` dans iOS 8, autoriser une application mobile prendre le contrôle total sur l’appareil photo d’un appareil iOS. Ce niveau de granularité fin de contrôle peut être utilisé pour créer des applications professionnelles et de fournir des compositions d’artiste en ajustant les paramètres de l’appareil photo lors de la prise d’une image fixe ou une vidéo.

## <a name="mapsios-mapsindexmd"></a>[Cartes](ios-maps/index.md)

Les mappages sont une fonctionnalité commune dans tous les systèmes d’exploitation mobiles modernes. iOS prend en charge de mappage en mode natif par le biais du Kit de la carte. Avec le Kit de la carte, applications peuvent facilement ajouter des mappages riches et interactives. Ces cartes peuvent être personnalisés de plusieurs façons, telles que l’ajout d’annotations pour marquer des emplacements sur une carte et recouvrir des graphiques de formes arbitraires. Kit de carte a même la prise en charge intégrée de l’affichage de l’emplacement actuel d’un appareil.

## <a name="labelslabelsmd"></a>[Étiquettes](labels.md)

Le `UILabel` contrôle est utilisé pour l’affichage unique et plusieurs lignes, texte en lecture seule.

## <a name="pickers-and-date-pickerspickermd"></a>[Sélecteurs et les sélecteurs de dates](picker.md)

Le contrôle de sélecteur affiche 'roulette-like' contrôle qui contient une liste déroulante de valeurs avec la valeur sélectionnée est en cours de mise en surbrillance. Les utilisateurs faites tourner la roulette pour sélectionner l’option qu’ils souhaitent.

Un utilisateur spécifique de cas pour les sélecteurs pour définir la date et/ou heure. Prévoir cette Apple a créé une sous-classe personnalisée de la classe UIPickerView appelée UIDatePicker.

## <a name="progress-and-activity-indicatorsprogress-activity-indicatormd"></a>[Indicateurs de progression et d’activité](progress-activity-indicator.md)

iOS fournit deux méthodes principales pour indiquer la progression dans votre application : Indicateurs d’activité (y compris un spécifique _réseau_ indicateur d’activité) et les barres de progression.

## <a name="search-barssearchbarmd"></a>[Barres de recherche](searchbar.md)

Le UISearchBar est utilisé pour effectuer des recherches dans une liste de valeurs. 

## <a name="sliders-switches-and-segmented-controlsslider-switch-segmented-controlsmd"></a>[Curseurs, commutateurs et contrôles segmentés](slider-switch-segmented-controls.md)

Le contrôle slider permet une sélection simple d’une valeur numérique dans une plage. iOS utilise le `UISwitch` comme entrée d’une valeur booléenne qui peut être représentée par une case sur d’autres plateformes. Un contrôle segmenté est une façon organisée pour permettre aux utilisateurs d’interagir avec un petit nombre d’options.

## <a name="stack-viewuistackviewmd"></a>[Vue en pile](uistackview.md)

Le contrôle de la vue de la pile (`UIStackView`) tire parti de la puissance de disposition automatique et des Classes de taille pour gérer une pile de sous-vues, horizontalement ou verticalement, qui répond dynamiquement à la taille d’écran et l’orientation de l’appareil iOS.

## <a name="tables-and-cellstablesindexmd"></a>[Tableaux et cellules](tables/index.md)

sa section introduit les classes utilisées pour créer et afficher les tables, puis fournit des exemples montrant comment les utiliser dans Xamarin.iOS. Il couvre à l’aide de l’apparence par défaut pour les tables, personnalisation de la disposition, la mise en œuvre de la modification et l’utilisation du Concepteur de Xamarin iOS à concevoir une table visuellement. Parfois, l’affichage est évidemment une liste de lignes (par exemple, l’application Music) et d’autres moments, qu'il est difficile de reconnaître le contrôle de table (par exemple la modification dans l’application Contacts ou une conversation dans l’application Messages).

## <a name="text-inputtext-inputmd"></a>[Entrée de texte](text-input.md)

Accepter une entrée de texte utilisateur s’effectue avec la `UITextField` pour les entrées de ligne et de UITextView pour le texte modifiable multiligne. Vous pouvez faire glisser un de ces contrôles sur un écran et double-cliquez dessus pour définir le texte initial.

## <a name="tab-bars-and-tab-bar-controllerscreating-tabbed-applicationsmd"></a>[Barres d’onglets et contrôleurs de barre d’onglets](creating-tabbed-applications.md)

les applications iOS à l’aide d’une interface utilisateur de navigation de l’onglet sont générées à l’aide de la classe UITabBarController. Dans cet article, nous allons comment configurer une application à onglets qui contient plusieurs contrôleurs et des vues. Nous examinerons ensuite comment charger un UITabBarController lorsqu’il n’est pas le contrôleur de la racine, comme après un écran de connexion.

## <a name="web-viewsuiwebviewmd"></a>[Affichages Web](uiwebview.md)

Dans cet article, nous allons examiner chacun des trois vues Web fournies par Apple : `UIWebView`, `WKWebview`, et `SFSafariViewController`, leurs similitudes et différences et comment elles peuvent être utilisées.

## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
