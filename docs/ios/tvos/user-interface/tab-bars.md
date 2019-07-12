---
title: Utilisation de tvOS contrôleurs de barre d’onglet dans Xamarin
description: Ce document décrit comment utiliser des contrôleurs de barre d’onglet dans une application tvOS générées avec Xamarin. Il fournit un niveau élevé sur l’affichage de barres d’onglets et traite des éléments de barre d’onglet, l’intégration de la table de montage séquentiel et éléments de barre d’onglet.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 7ac4d0effc1067b065bad114160dc8648e998dad
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830793"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Utilisation de tvOS contrôleurs de barre d’onglet dans Xamarin

Pour de nombreux types d’applications de tvOS, navigation principale est présentée comme une barre d’onglets dans la partie supérieure de l’écran. L’utilisateur fait glisser sa gauche et droite sur la liste des catégories possibles et la zone de contenu ci-dessous les modifications pour refléter la sélection de l’utilisateur.

[![](tab-bars-images/tab01.png "Exemple de barre d’onglets")](tab-bars-images/tab01.png#lightbox)

La barre d’onglet est translucide par défaut et apparaît toujours en haut de l’écran. En cas de focus, une barre d’onglets couvrent les 140 pixels supérieurs de l’écran mais sera rapidement diapositive disparaître lors de la sélection se déplace vers la zone de contenu ci-dessous.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barres d’onglets dans tvOS

Le `UITabViewController` fonctionne de manière similaire et possède un objectif similaire sur tvOS comme il le fait dans iOS, avec les différences clés suivantes :

- Contrairement à la barre d’onglets sur iOS qui s’affiche en bas de l’écran, les barres d’onglets dans tvOS occupent 140 pixels supérieures de l’écran et sont translucides par défaut.
- Lorsque le focus quitte la barre d’onglet pour la zone de contenu ci-dessous, la barre d’onglet glisser rapidement la partie supérieure de l’écran et être masquée. L’utilisateur peut soit appuyez une fois sur le bouton de Menu ou passez sur le [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) pour afficher la barre d’onglet à nouveau.
- Balayer vers le bas de la télécommande Siri déplacera le focus vers la zone de contenu sous la barre d’onglet pour la première [élément peut recevoir le focus](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) dans le contenu affiché. Là encore, cela masquera la barre d’onglets une fois que la sélection se déplace.
- Cliquer pour sélectionner une catégorie affichée dans la barre d’onglet passe à qui la catégorie du contenu et le focus bascule vers le premier élément pouvant être actif dans cette vue.
- Le nombre de catégories affichées dans la barre d’onglet doit être fixe et toutes les catégories doivent être accessibles à tout moment, une catégorie donnée ne doit jamais être désactivée.
- Barres d’onglets ne prennent pas en charge la personnalisation sur tvOS. En outre, elles ne présentent pas la **plus** catégorie (comme iOS) s’il existe plus de catégories que peut être contenue dans la barre d’onglet.

Apple a les suggestions suivantes pour l’utilisation des barres d’onglets :

- **Utilisez les barres d’onglets pour organiser logiquement les contenu** -utiliser la barre d’onglet pour organiser logiquement le contenu associé à votre application tvOS. Par exemple, par défaut, les graphiques de haut, acheté et la recherche.
- **Ajouter les Badges à informer les utilisateurs de nouveau contenu** -vous pouvez éventuellement afficher un Badge (un ovale rouge avec un nombre blanc ou un point d’exclamation) pour informer l’utilisateur du nouveau contenu dans une catégorie.
- **Utilisez Badges avec parcimonie** - ne viennent pas alourdir la barre d’onglets avec des Badges et uniquement les afficher où ils fournissent des informations critiques à l’utilisateur.
- **Limiter le nombre de catégories** : pour réduire la complexité et que vous application reste gérable, ne pas surcharger votre barre d’onglets avec des catégories et vous assurer que toutes les catégories sont visibles et non encombré. Titres simples et courtes fonctionnent le mieux.
- **Ne désactivez pas une catégorie** -tous les onglets (catégories) doit toujours être visible et activé en permanence. Si un onglet donné n’a aucun contenu, pourquoi de fournir une explication à l’utilisateur. Par exemple, l’onglet achats sera vide si l’utilisateur n’a effectué aucune des achats.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Éléments de la barre d’onglet

Chaque catégorie (onglet) dans la barre d’onglet est représentée par un élément de barre d’onglet (`UITabBarItem`). Apple a les suggestions suivantes pour l’utilisation des éléments de barre d’onglet :

- **Utilisez les onglets en fonction de texte** -tandis que la barre TabItem est en mesure d’être représentées sous forme d’icône, les suggestions d’Apple en utilisant le texte uniquement, car un titre concis est plus facile à interpréter qu’une icône.
- **Utiliser des noms significatifs à court ou verbes** -élément de barre d’onglet A indiquent clairement le contenu qu’il contienne et fonctionne mieux lorsqu’elle est un nom simple (par exemple, des Photos, musique ou des vidéos) ou des verbes (tels que la recherche ou Play).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barres d’onglets et des tables de montage séquentiel

Pour travailler avec des barres d’onglets dans une application Xamarin.tvOS, le plus simple consiste à ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)
    
1. Démarrez une nouvelle application Xamarin.tvOS et sélectionnez **tvOS** > **application** > **application avec onglets**: 

    [![](tab-bars-images/tab02.png "Sélectionnez l’application avec onglets")](tab-bars-images/tab02.png#lightbox)
1. Suivez toutes les invites pour créer une nouvelle Xamarin.tvOS Solution.
1. Dans le **panneau solutions**, double-cliquez sur le `Main.storyboard` de fichier et l’ouvrir pour modification.
1. Pour modifier le **icône** ou **titre** pour une catégorie donnée, sélectionnez le **élément de barre d’onglet** pour le **contrôleur d’affichage** dans le  **Structure du document**:

    [![](tab-bars-images/tab03a.png "Élément de la barre d’onglet pour le contrôleur d’affichage dans la structure du Document")](tab-bars-images/tab03a.png#lightbox)
1. Puis définissez les propriétés requises le **onglet Widget** de la **l’Explorateur de propriétés**: 

    [![](tab-bars-images/tab03.png "L’onglet de Widget")](tab-bars-images/tab03.png#lightbox)
1. Pour ajouter une nouvelle catégorie (onglet), déposez un **contrôleur d’affichage** votre aire de conception : 

    [![](tab-bars-images/tab04.png "Un contrôleur d’affichage")](tab-bars-images/tab04.png#lightbox)
1. Cliquez et faites glisser de la **contrôleur d’affichage onglet** au nouveau **contrôleur d’affichage**.
1. Dans le menu contextuel, sélectionnez **afficher les contrôleurs** pour ajouter la nouvelle vue sous forme d’onglet (catégorie) : 

    [![](tab-bars-images/tab05.png "Sélectionnez l’onglet")](tab-bars-images/tab05.png#lightbox)
1. Concevoir la disposition de l’interface utilisateur pour chaque zone de contenu Caterogies comme d’habitude, en ajoutant des éléments d’interface utilisateur dans le concepteur iOS.
1. Exposer tous les événements requis pour travailler avec les contrôles d’IU dans C# code.
1. Nommez les contrôles d’interface utilisateur que vous souhaitez exposer dans C# code.
1. Enregistrez les modifications apportées.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Démarrez une nouvelle application Xamarin.tvOS et sélectionnez **tvOS** > **application** > **application avec onglets**: 

    [![](tab-bars-images/tab02vs.png "Sélectionnez l’application avec onglets")](tab-bars-images/tab02vs.png#lightbox)
1. Suivez toutes les invites pour créer une nouvelle Xamarin.tvOS Solution.
1. Dans le **l’Explorateur de solutions**, double-cliquez sur le `Main.storyboard` de fichier et l’ouvrir pour modification.
1. Pour modifier le **icône** ou **titre** pour une catégorie donnée, sélectionnez le **élément de barre d’onglet** pour le **contrôleur d’affichage** dans le  **Structure du document**:

    [![](tab-bars-images/tab03avs.png "Le contrôleur d’affichage dans la structure du Document")](tab-bars-images/tab03avs.png#lightbox)
1. Puis définissez les propriétés requises le **onglet Widget** de la **l’Explorateur de propriétés**: 

    [![](tab-bars-images/tab03vs.png "L’onglet de Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Pour ajouter une nouvelle catégorie (onglet), faites glisser un **contrôleur d’affichage** à partir de la **boîte à outils** et déposez-le sur votre surface de conception : 

    [![](tab-bars-images/tab04vs.png "Un contrôleur d’affichage")](tab-bars-images/tab04vs.png#lightbox)
1. Cliquez et faites glisser de la **contrôleur d’affichage onglet** au nouveau **contrôleur d’affichage**.
1. Dans le menu contextuel, sélectionnez **afficher les contrôleurs** pour ajouter la nouvelle vue sous forme d’onglet (catégorie) : 

    [![](tab-bars-images/tab05vs.png "Sélectionnez l’onglet")](tab-bars-images/tab05vs.png#lightbox)
1. Concevoir la disposition de l’interface utilisateur pour chaque zone de contenu Caterogies comme d’habitude, en ajoutant des éléments d’interface utilisateur dans le concepteur iOS.
1. Exposer tous les événements requis pour travailler avec les contrôles d’IU dans C# code.
1. Nommez les contrôles d’interface utilisateur que vous souhaitez exposer dans C# code.
1. Enregistrez les modifications apportées.
    
-----

> [!IMPORTANT]
> Bien qu’il soit possible d’attribuer des événements tels que `TouchUpInside` à un élément d’interface utilisateur (comme un `UIButton`) dans le concepteur iOS, il ne sera jamais appelée car Apple TV n’a pas une pression tactile écran ou de prendre en charge les événements tactiles. Vous devez toujours utiliser le `Primary Action` événement lors de la création des éléments d’interface utilisateur de gestionnaires d’événements pour tvOS.

Pour plus d’informations sur l’utilisation des tables de montage séquentiel, consultez notre [Hello, tvOS Guide de démarrage rapide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Utilisation des barres d’onglets

Utilisez le `Items` propriété de la `UITabBar` pour accéder à la collection de `UITabBarItems` qu’il contient en tant qu’un tableau indexé zéro (0). Le `SelectedItem` propriété retournera l’onglet actuellement sélectionné (catégorie) comme un `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Utilisation des éléments de barre d’onglets

Pour afficher un Badge d’un onglet donné (un ovale rouge avec un texte blanc), utilisez le code suivant :

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Ce qui produit les résultats suivants lorsque vous exécutez :

[![](tab-bars-images/tab06.png "Un élément de barre d’onglet avec badge")](tab-bars-images/tab06.png#lightbox)

Utilisez le `Title` propriété de la `UITabBarItem` pour modifier le titre et le `Image` propriété à modifier l’icône.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté la conception et fonctionne avec le contrôleur de barre d’onglet à l’intérieur d’une application Xamarin.tvOS.




## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
