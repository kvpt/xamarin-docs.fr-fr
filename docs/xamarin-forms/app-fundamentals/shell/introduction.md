---
title: Présentation de Xamarin.Forms Shell
description: Xamarin.Forms Shell fournit les fonctionnalités fondamentales nécessaires à la plupart des applications, y compris une expérience utilisateur de navigation commune, un schéma de navigation basée sur des URI et un gestionnaire de recherche intégré.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 3e63a580bbdb1c220d44b100725cdc8ce387b405
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696519"
---
# <a name="xamarinforms-shell-introduction"></a>Présentation de Xamarin.Forms Shell

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles, y compris :

- Un emplacement unique pour décrire la hiérarchie visuelle d’une application.
- Une expérience utilisateur de navigation commune.
- Un schéma de navigation basée sur des URI permettant la navigation vers n’importe quelle page dans l’application.
- Un gestionnaire de recherche intégré.

En outre, les applications Shell bénéficient d’une vitesse de rendu accrue et d’une consommation de mémoire réduite.

> [!IMPORTANT]
> Les applications existantes peuvent adopter l’interpréteur de commandes et tirer parti immédiatement de l’amélioration de la navigation, des performances et de l’extensibilité.

## <a name="platform-support"></a>Plateforme prise en charge

Xamarin. Forms Shell est entièrement disponible sur iOS et Android, mais uniquement partiellement disponible sur le plateforme Windows universelle (UWP). En outre, Shell est actuellement expérimental sur UWP et ne peut être utilisé qu’en ajoutant la ligne de code suivante à la classe `App` de votre projet UWP, avant d’appeler `Forms.Init` :

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

## <a name="shell-navigation-experience"></a>Expérience de navigation Shell

Shell fournit une expérience de navigation robuste, basée sur des menus volants et des onglets. Le niveau de navigation supérieur dans une application Shell est un menu volant ou une barre d’onglets en bas, selon les besoins de l’application en matière de navigation. L’exemple suivant montre une application où le niveau supérieur de navigation est un menu volant :

[![Capture d’écran d’un menu volant Shell, sur iOS et Android](introduction-images/flyout.png "Menu volant Shell")](introduction-images/flyout-large.png#lightbox "Menu volant Shell")

La sélection d’un élément de menu volant permet de sélectionner et d’afficher cet élément dans un onglet en bas de l’écran :

[![Capture d’écran des onglets en bas de l’interpréteur de commandes, sur iOS et Android](introduction-images/monkeys.png "Onglets du bas du shell")](introduction-images/monkeys-large.png#lightbox "Onglets du bas du shell")

> [!NOTE]
> Lorsque le menu volant n’est pas ouvert, la barre d’onglets du bas représente le niveau supérieur de navigation dans l’application.

Chaque onglet affiche une [`ContentPage`](xref:Xamarin.Forms.ContentPage). Toutefois, si un onglet inférieur contient plusieurs pages, les pages sont accessibles via la barre d’onglets supérieure :

[![Capture d’écran des principaux onglets de Shell, sur iOS et Android](introduction-images/cats.png "Onglets principaux de l’interpréteur de commandes")](introduction-images/cats-large.png#lightbox "Onglets principaux de l’interpréteur de commandes")

Chaque onglet permet d’accéder à des objets [`ContentPage`](xref:Xamarin.Forms.ContentPage) supplémentaires :

[![Capture d’écran de la navigation entre les pages Shell, sur iOS et Android](introduction-images/cat-details.png "Navigation dans l’application Shell")](introduction-images/cat-details-large.png#lightbox "Navigation dans l’application Shell")

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
