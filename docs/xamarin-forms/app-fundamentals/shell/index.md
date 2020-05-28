---
title: Xamarin.FormsShell
description: Ce guide explique comment utiliser Xamarin.Forms l’interpréteur de commandes, qui réduit la complexité des Xamarin.Forms applications en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0acf00d85c2bfb823ec1cfba099179bb6743117c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138747"
---
# <a name="xamarinforms-shell"></a>Xamarin.FormsShell

## <a name="introduction"></a>[Introduction](introduction.md)

Xamarin.FormsL’interpréteur de commandes réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles. Cela inclut une expérience utilisateur de navigation commune, un schéma de navigation basée sur des URI et un gestionnaire de recherche intégré.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Créer une Xamarin.Forms application Shell](create.md)

Le processus de création d’une Xamarin.Forms application d’interpréteur de commandes consiste à créer un fichier XAML qui sous-classe la `Shell` classe, à définir la `MainPage` propriété de la classe de l’application `App` sur l’objet sous-classé, puis à `Shell` décrire la hiérarchie visuelle de l’application dans la classe sous-classée `Shell` .

## <a name="flyout"></a>[Menu volant](flyout.md)

Le menu volant est le menu racine d’une application Shell, accessible via une icône ou en balayant depuis le côté de l’écran. Le menu volant se compose d’un en-tête facultatif, de différents éléments de menu volant et d’éléments de menu facultatifs.

## <a name="tabs"></a>[Onglets](tabs.md)

Le niveau de navigation suivant le menu volant dans une application Shell est la barre d’onglets du bas. Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans les deux cas, lorsqu’un onglet contient plusieurs pages, il est possible de passer de l’une à l’autre grâce aux onglets du haut.

## <a name="page-configuration"></a>[Configuration de page](configuration.md)

La `Shell` classe définit les propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications de l' Xamarin.Forms interpréteur de commandes. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’apparence des affichages dans la barre de navigation.

## <a name="navigation"></a>[Navigation](navigation.md)

Les applications Shell peuvent utiliser un modèle de navigation basée sur des URI dans lequel des itinéraires permettent d’accéder à n’importe quelle page dans l’application, sans avoir à suivre une hiérarchie de navigation définie.

## <a name="search"></a>[action](search.md)

Les applications Shell peuvent utiliser la fonctionnalité de recherche intégrée fournie par une zone de recherche qui peut être ajoutée en haut de chaque page.

## <a name="lifecycle"></a>[Cycle de vie](lifecycle.md)

Les applications de Shell respectent le Xamarin.Forms cycle de vie et un `Appearing` événement est déclenché lorsqu’une page est sur le point d’apparaître sur l’écran, et un `Disappearing` événement est déclenché lorsqu’une page est sur le point de disparaître de l’écran.

## <a name="custom-renderers"></a>[Convertisseurs personnalisés](customrenderers.md)

Les applications Shell sont hautement personnalisables via les propriétés et les méthodes présentées par les différentes classes Shell. Toutefois, il est également possible de créer un renderer Shell personnalisé lorsque des personnalisations plus sophistiquées spécifiques à une plateforme sont requises.
