---
title: Xamarin.FormsPrésentation de l’interpréteur de commandes
description: Xamarin.FormsL’interpréteur de commandes fournit les fonctionnalités fondamentales nécessaires à la plupart des applications, notamment une expérience utilisateur de navigation commune, un schéma de navigation basé sur les URI et un gestionnaire de recherche intégré.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 29a99161ff2ef2d71b6c803db994522bfe80ed03
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138734"
---
# <a name="xamarinforms-shell-introduction"></a>Xamarin.FormsPrésentation de l’interpréteur de commandes

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.FormsL’interpréteur de commandes réduit la complexité du développement d’applications mobiles en fournissant les fonctionnalités fondamentales nécessaires à la plupart des applications mobiles, notamment :

- Un emplacement unique pour décrire la hiérarchie visuelle d’une application.
- Une expérience utilisateur de navigation commune.
- Un schéma de navigation basée sur des URI permettant la navigation vers n’importe quelle page dans l’application.
- Un gestionnaire de recherche intégré.

En outre, les applications Shell bénéficient d’une vitesse de rendu accrue et d’une consommation de mémoire réduite.

> [!IMPORTANT]
> Les applications existantes peuvent adopter Shell et bénéficier immédiatement d’une navigation, de performances et d’une extensibilité améliorées.

## <a name="platform-support"></a>Plateforme prise en charge

Xamarin.FormsL’interpréteur de commandes est entièrement disponible sur iOS et Android, mais uniquement partiellement disponible sur le plateforme Windows universelle (UWP). En outre, Shell est actuellement expérimental sur UWP et ne peut être utilisé qu’en ajoutant la ligne de code suivante à la classe `App` du projet UWP, avant d’appeler `Forms.Init` :

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

Pour plus d’informations sur l’ajout d’un projet UWP à une Xamarin.Forms solution, consultez [configurer des projets Windows](~/xamarin-forms/platform/windows/installation/index.md).

## <a name="shell-navigation-experience"></a>Expérience de navigation Shell

Shell fournit une expérience de navigation robuste, basée sur des menus volants et des onglets. Le niveau de navigation supérieur dans une application Shell est un menu volant ou une barre d’onglets en bas, selon les besoins de l’application en matière de navigation. L’exemple suivant montre une application où le niveau supérieur de navigation est un menu volant :

[![Capture d’écran d’un menu volant Shell, sur iOS et Android](introduction-images/flyout.png "Menu volant Shell")](introduction-images/flyout-large.png#lightbox "Menu volant Shell")

La sélection d’un élément de menu volant permet de sélectionner et d’afficher cet élément dans un onglet en bas de l’écran :

[![Capture d’écran des onglets du bas Shell, sur iOS et Android](introduction-images/monkeys.png "Onglets du bas Shell")](introduction-images/monkeys-large.png#lightbox "Onglets du bas Shell")

> [!NOTE]
> Lorsque le menu volant n’est pas ouvert, la barre d’onglets du bas représente le niveau supérieur de navigation dans l’application.

Chaque onglet affiche un [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Toutefois, si un onglet inférieur contient plusieurs pages, les pages sont accessibles via la barre d’onglets supérieure :

[![Capture d’écran des onglets du haut Shell, sur iOS et Android](introduction-images/cats.png "Onglets du haut Shell")](introduction-images/cats-large.png#lightbox "Onglets du haut Shell")

Dans chaque onglet, [`ContentPage`](xref:Xamarin.Forms.ContentPage) vous pouvez accéder à des objets supplémentaires :

[![Capture d’écran de la navigation entre les pages Shell, sur iOS et Android](introduction-images/cat-details.png "Navigation dans l’application Shell")](introduction-images/cat-details-large.png#lightbox "Navigation dans l’application Shell")

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
