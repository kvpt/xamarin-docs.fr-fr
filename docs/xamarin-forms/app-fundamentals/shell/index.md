---
title: Xamarin.Forms Shell
description: Ce guide explique comment utiliser Xamarin.Forms Shell, qui réduit la complexité des applications Xamarin.Forms en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications.
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: 20ac6ad748e7056f7f8037a73a95de66b9eae3b6
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "69888920"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

## <a name="introduction"></a>[Introduction](introduction.md)

Xamarin.Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles. Cela inclut une expérience utilisateur de navigation commune, un schéma de navigation basée sur des URI et un gestionnaire de recherche intégré.

## <a name="create-a-xamarinforms-shell-application"></a>[Créez une application Xamarin.Forms Shell](create.md)

Le processus de création d’une application Shell Xamarin.Forms consiste à créer un fichier XAML sous-classant la classe `Shell`, à définir la propriété `MainPage` de la classe `App` de l’application sur l’objet `Shell` sous-classé, puis à décrire la hiérarchie visuelle de l’application dans la classe `Shell` sous-classée.

## <a name="flyout"></a>[Menu volant](flyout.md)

Le menu volant est le menu racine d’une application Shell, accessible via une icône ou en balayant depuis le côté de l’écran. Le menu volant se compose d’un en-tête facultatif, de différents éléments de menu volant et d’éléments de menu facultatifs.

## <a name="tabs"></a>[Tabulations](tabs.md)

Le niveau de navigation suivant le menu volant dans une application Shell est la barre d’onglets du bas. Le modèle de navigation pour une application peut également commencer avec des onglets en bas et n’utiliser aucun menu volant. Dans les deux cas, lorsqu’un onglet contient plusieurs pages, il est possible de passer de l’une à l’autre grâce aux onglets du haut.

## <a name="page-configuration"></a>[Configuration de page](configuration.md)

La classe `Shell` définit des propriétés jointes qui peuvent être utilisées pour configurer l’apparence des pages dans les applications Shell Xamarin.Forms. Cela comprend notamment la configuration des couleurs de la page, la désactivation de la barre de navigation et de la barre d’onglet ainsi que l’apparence des affichages dans la barre de navigation.

## <a name="navigation"></a>[Navigation](navigation.md)

Les applications Shell peuvent utiliser un modèle de navigation basée sur des URI dans lequel des itinéraires permettent d’accéder à n’importe quelle page dans l’application, sans avoir à suivre une hiérarchie de navigation définie.

## <a name="search"></a>[action](search.md)

Les applications Shell peuvent utiliser la fonctionnalité de recherche intégrée fournie par une zone de recherche qui peut être ajoutée en haut de chaque page.

## <a name="lifecycle"></a>[Cycle de vie](lifecycle.md)

Les applications Shell respectent le cycle de vie Xamarin.Forms. Un événement `Appearing` est déclenché lorsqu’une page est sur le point d'apparaître sur l’écran, tandis qu’un événement `Disappearing` est déclenché lorsqu’une page est sur le point de disparaître de l’écran.

## <a name="custom-renderers"></a>[Renderers personnalisés](customrenderers.md)

Les applications Shell sont hautement personnalisables via les propriétés et les méthodes présentées par les différentes classes Shell. Toutefois, il est également possible de créer un renderer Shell personnalisé lorsque des personnalisations plus sophistiquées spécifiques à une plateforme sont requises.
