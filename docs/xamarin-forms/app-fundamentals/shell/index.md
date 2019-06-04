---
title: Xamarin.Forms Shell
description: Ce guide explique comment utiliser Xamarin.Forms Shell, qui réduit la complexité des applications Xamarin.Forms en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: a988a99e20af76d071f55c4cd2c97b135ad077f8
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252275"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

Xamarin.Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles. Cela inclut une expérience utilisateur de navigation commune, un schéma de navigation basée sur des URI et un gestionnaire de recherche intégré.

## <a name="create-a-xamarinforms-shell-applicationcreatemd"></a>[Créer une application Shell Xamarin.Forms](create.md)

Le processus de création d’une application Shell Xamarin.Forms consiste à créer un fichier XAML sous-classant la classe `Shell`, à définir la propriété `MainPage` de la classe `App` de l’application sur l’objet `Shell` sous-classé, puis à décrire la hiérarchie visuelle de l’application dans la classe `Shell` sous-classée.

## <a name="flyoutflyoutmd"></a>[Menu volant](flyout.md)

Le menu volant est le menu racine d’une application Shell, accessible via une icône ou en balayant depuis le côté de l’écran. Le menu volant se compose d’un en-tête facultatif, de différents éléments de menu volant et d’éléments de menu facultatifs.

## <a name="tabstabsmd"></a>[Onglets](tabs.md)

Le niveau de navigation suivant le menu volant dans une application Shell est la barre d’onglets du bas. Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans les deux cas, lorsqu’un onglet contient plusieurs pages, il est possible de passer de l’une à l’autre grâce aux onglets du haut.

## <a name="page-configurationconfigurationmd"></a>[Configuration de page](configuration.md)

La classe `Shell` définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications Shell Xamarin.Forms. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’affichage des vues dans la barre de navigation.

## <a name="navigationnavigationmd"></a>[Navigation](navigation.md)

Les applications Shell peuvent utiliser un modèle de navigation basée sur des URI dans lequel des itinéraires permettent d’accéder à n’importe quelle page dans l’application, sans avoir à suivre une hiérarchie de navigation définie.

## <a name="searchsearchmd"></a>[Rechercher](search.md)

Les applications Shell peuvent utiliser la fonctionnalité de recherche intégrée fournie par une zone de recherche qui peut être ajoutée en haut de chaque page.

## <a name="custom-rendererscustomrenderersmd"></a>[Renderers personnalisés](customrenderers.md)

Les applications Shell sont hautement personnalisables via les propriétés et les méthodes présentées par les différentes classes Shell. Toutefois, il est également possible de créer un renderer Shell personnalisé lorsque des personnalisations plus sophistiquées spécifiques à une plateforme sont requises.
