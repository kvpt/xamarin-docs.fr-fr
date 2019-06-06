---
title: Présentation de Xamarin.Forms Shell
description: Xamarin.Forms Shell fournit les fonctionnalités fondamentales nécessaires à la plupart des applications, y compris une expérience utilisateur de navigation commune, un schéma de navigation basée sur des URI et un gestionnaire de recherche intégré.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: 266dc91e80ced89410046a83cc79e45371e8c0a4
ms.sourcegitcommit: b4a12607ca944de10fd166139765241a4501831c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66686989"
---
# <a name="xamarinforms-shell-introduction"></a>Présentation de Xamarin.Forms Shell

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Xamarin.Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles, y compris :

- Un emplacement unique pour décrire la hiérarchie visuelle d’une application.
- Une expérience utilisateur de navigation commune.
- Un schéma de navigation basée sur des URI permettant la navigation vers n’importe quelle page dans l’application.
- Un gestionnaire de recherche intégré.

En outre, les applications Shell bénéficient d’une vitesse de rendu accrue et d’une consommation de mémoire réduite.

> [!IMPORTANT]
> Xamarin.Forms Shell est uniquement disponible sur iOS et Android. Les applications iOS et Android existantes peuvent adopter Shell, bénéficiant immédiatement d’une navigation, de performances et d’une extensibilité améliorées.

## <a name="shell-navigation-experience"></a>Expérience de navigation Shell

Shell fournit une expérience de navigation robuste, basée sur des menus volants et des onglets. Le niveau de navigation supérieur dans une application Shell est un menu volant ou une barre d’onglets en bas, selon les besoins de l’application en matière de navigation. L’exemple suivant montre une application où le niveau supérieur de navigation est un menu volant :

[![Capture d’écran d’un menu volant Shell sur iOS et Android](introduction-images/flyout.png "Menu volant Shell")](introduction-images/flyout-large.png#lightbox "Menu volant Shell")

La sélection d’un élément de menu volant permet de sélectionner et d’afficher cet élément dans un onglet en bas de l’écran :

[![Capture d’écran des onglets Shell inférieurs sur iOS et Android](introduction-images/monkeys.png "Onglets Shell inférieurs")](introduction-images/monkeys-large.png#lightbox "Onglets Shell inférieurs")

> [!NOTE]
> Lorsque le menu volant n’est pas ouvert, la barre d’onglets du bas représente le niveau supérieur de navigation dans l’application.

Chaque onglet affiche une [`ContentPage`](xref:Xamarin.Forms.ContentPage). Toutefois, si un onglet inférieur contient plusieurs pages, les pages sont accessibles via la barre d’onglets supérieure :

[![Capture d’écran des onglets Shell supérieurs sur iOS et Android](introduction-images/cats.png "Onglets Shell supérieurs")](introduction-images/cats-large.png#lightbox "Onglets Shell supérieurs")

Chaque onglet permet d’accéder à des objets [`ContentPage`](xref:Xamarin.Forms.ContentPage) supplémentaires :

[![Capture d’écran de la navigation dans une page Shell sur iOS et Android](introduction-images/cat-details.png "Navigation dans une application Shell")](introduction-images/cat-details-large.png#lightbox "Navigation dans une application Shell")

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
