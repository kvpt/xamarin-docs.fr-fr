---
title: Utilisation des contrôleurs de barre d’onglets tvOS dans Xamarin
description: Ce document décrit comment utiliser les contrôleurs de barre d’onglets dans une application tvOS créée avec Xamarin. Il fournit un haut niveau de vue des barres d’onglets et traite des éléments de barre d’onglets, de l’intégration des storyboards et des éléments de barre d’onglets.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 799dd40caae0323b71cae2c6715bf8cd16f78356
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283975"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Utilisation des contrôleurs de barre d’onglets tvOS dans Xamarin

Pour de nombreux types d’applications tvOS, la navigation principale est présentée sous la forme d’une barre d’onglets en haut de l’écran. L’utilisateur fait défiler vers la gauche et vers la droite dans la liste des catégories possibles et la zone de contenu sous les modifications pour refléter la sélection de l’utilisateur.

[![](tab-bars-images/tab01.png "Exemple de barre d’onglets")](tab-bars-images/tab01.png#lightbox)

La barre d’onglets est translucide par défaut et s’affiche toujours en haut de l’écran. Lorsque le focus est activé, une barre d’onglets couvre les 140 pixels les plus hauts de l’écran, mais s’éloigne rapidement lorsque le focus se déplace vers la zone de contenu ci-dessous.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barres d’onglets dans tvOS

Le `UITabViewController` fonctionne de la même manière et a un objectif similaire sur tvOS comme il le fait dans iOS, avec les principales différences suivantes :

- Contrairement à la barre d’onglets iOS qui apparaît en bas de l’écran, les barres d’onglets dans tvOS occupent les 140 pixels les plus hauts de l’écran et sont translucides par défaut.
- Lorsque le focus quitte la barre d’onglets de la zone de contenu ci-dessous, la barre d’onglets s’affiche rapidement en haut de l’écran et est masquée. L’utilisateur peut appuyer sur le bouton de menu une fois ou balayer le [Siri à distance](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) pour afficher à nouveau la barre d’onglets.
- Le fait de faire défiler vers le bas sur le Siri distant déplace le focus vers la zone de contenu sous la barre d’onglet jusqu’au premier élément pouvant être [actif](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) dans le contenu affiché. Là encore, la barre d’onglets est masquée lorsque le focus est déplacé.
- Le fait de cliquer pour sélectionner une catégorie affichée dans la barre d’onglets bascule vers le contenu de cette catégorie et le focus est basculé vers le premier élément pouvant être actif dans cette vue.
- Le nombre de catégories affichées dans la barre d’onglets doit être fixe et toutes les catégories doivent être accessibles à tout moment ; une catégorie donnée ne doit jamais être désactivée.
- Les barres d’onglet ne prennent pas en charge la personnalisation sur tvOS. En outre, ils n’affichent pas la catégorie **plus** (comme IOS) s’il existe plus de catégories que la barre d’onglets ne peut en contenir.

Apple propose les suggestions suivantes pour l’utilisation des barres d’onglets :

- **Utiliser des barres d’onglets pour organiser logiquement le contenu** : utilisez la barre d’onglets pour organiser logiquement le contenu avec lequel votre application tvOS fonctionne. Par exemple, les graphiques en vedette, achetés et de recherche.
- **Ajouter des badges pour informer les utilisateurs du nouveau contenu** : vous pouvez éventuellement afficher un badge (un ovale rouge avec un nombre blanc ou un point d’exclamation) pour informer l’utilisateur du nouveau contenu dans une catégorie.
- **Utilisez les badges avec modération** : n’encombrez pas la barre d’onglets avec des badges et affichez-les uniquement là où elles fournissent des informations critiques à l’utilisateur.
- **Limiter le nombre de catégories** -pour réduire la complexité et garder votre application gérable, ne surchargez pas votre barre d’onglets avec des catégories et assurez-vous que toutes les catégories sont visibles et non encombrées. Les titres simples et courts fonctionnent mieux.
- **Ne désactivez pas une catégorie** : tous les onglets (catégories) doivent toujours être visibles et activés en permanence. Si un onglet donné n’a pas de contenu, fournissez une explication à l’utilisateur. Par exemple, l’onglet achats est vide si l’utilisateur n’a effectué aucun achat.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Éléments de la barre d’onglets

Chaque catégorie (onglet) de la barre d’onglets est représentée par un élément de`UITabBarItem`barre d’onglets (). Apple propose les suggestions suivantes pour l’utilisation des éléments de barre d’onglets :

- **Utiliser des onglets basés sur du texte** : lorsque l’élément de barre d’onglets peut être représenté sous la forme d’une icône, Apple suggère d’utiliser du texte uniquement, car un titre concis est plus facile à interpréter qu’une icône.
- **Utilisez des noms ou des verbes courts et explicites** : un élément de barre d’onglets doit relayer clairement le contenu qu’il contient et fonctionne mieux lorsqu’il s’agit d’un nom simple (tel que photos, films ou musique) ou de verbes (par exemple, la recherche ou la lecture).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barres d’onglets et storyboards

Le moyen le plus simple d’utiliser les barres d’onglets dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez une nouvelle application Xamarin. tvOS et sélectionnez l’application à**onglets**d'**application** >  **tvOS** > : 

    [![](tab-bars-images/tab02.png "Sélectionner une application avec onglets")](tab-bars-images/tab02.png#lightbox)
1. Suivez toutes les invites pour créer une nouvelle solution Xamarin. tvOS.
1. Dans la **panneau solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Pour modifier l' **icône** ou le **titre** d’une catégorie donnée, sélectionnez l' **élément de barre d’onglets** du **contrôleur d’affichage** dans la **structure du document**:

    [![](tab-bars-images/tab03a.png "Élément de barre d’onglets du contrôleur d’affichage dans la structure du document")](tab-bars-images/tab03a.png#lightbox)
1. Définissez ensuite les propriétés requises dans l' **onglet widget** de l' **Explorateur de propriétés**: 

    [![](tab-bars-images/tab03.png "Onglet widget")](tab-bars-images/tab03.png#lightbox)
1. Pour ajouter une nouvelle catégorie (onglet), déposez un **contrôleur d’affichage** sur votre aire de conception : 

    [![](tab-bars-images/tab04.png "Un contrôleur d’affichage")](tab-bars-images/tab04.png#lightbox)
1. Cliquez avec le contrôle sur le contrôleur d' **affichage** , puis faites-le glisser vers le nouveau **contrôleur d’affichage**.
1. Dans le menu contextuel, sélectionnez **afficher les contrôleurs** pour ajouter la nouvelle vue sous la forme d’un onglet (catégorie) : 

    [![](tab-bars-images/tab05.png "Sélectionner un onglet")](tab-bars-images/tab05.png#lightbox)
1. Concevez la disposition de l’interface utilisateur pour chaque zone de contenu Caterogies comme d’habitude, en ajoutant des éléments d’interface utilisateur dans le concepteur iOS.
1. Exposez les événements requis pour utiliser vos contrôles d' C# interface utilisateur dans le code.
1. Nommez les contrôles d’interface utilisateur que vous souhaitez C# exposer dans le code.
1. Enregistrez les modifications apportées.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez une nouvelle application Xamarin. tvOS et sélectionnez l’application à**onglets**d'**application** >  **tvOS** > : 

    [![](tab-bars-images/tab02vs.png "Sélectionner une application avec onglets")](tab-bars-images/tab02vs.png#lightbox)
1. Suivez toutes les invites pour créer une nouvelle solution Xamarin. tvOS.
1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Pour modifier l' **icône** ou le **titre** d’une catégorie donnée, sélectionnez l' **élément de barre d’onglets** du **contrôleur d’affichage** dans la **structure du document**:

    [![](tab-bars-images/tab03avs.png "Contrôleur d’affichage dans la structure du document")](tab-bars-images/tab03avs.png#lightbox)
1. Définissez ensuite les propriétés requises dans l' **onglet widget** de l' **Explorateur de propriétés**: 

    [![](tab-bars-images/tab03vs.png "Onglet widget")](tab-bars-images/tab03vs.png#lightbox)
1. Pour ajouter une nouvelle catégorie (onglet), faites glisser un **contrôleur d’affichage** à partir de la **boîte à outils** et déposez-le sur votre aire de conception : 

    [![](tab-bars-images/tab04vs.png "Un contrôleur d’affichage")](tab-bars-images/tab04vs.png#lightbox)
1. Cliquez avec le contrôle sur le contrôleur d' **affichage** , puis faites-le glisser vers le nouveau **contrôleur d’affichage**.
1. Dans le menu contextuel, sélectionnez **afficher les contrôleurs** pour ajouter la nouvelle vue sous la forme d’un onglet (catégorie) : 

    [![](tab-bars-images/tab05vs.png "Sélectionner un onglet")](tab-bars-images/tab05vs.png#lightbox)
1. Concevez la disposition de l’interface utilisateur pour chaque zone de contenu Caterogies comme d’habitude, en ajoutant des éléments d’interface utilisateur dans le concepteur iOS.
1. Exposez les événements requis pour utiliser vos contrôles d' C# interface utilisateur dans le code.
1. Nommez les contrôles d’interface utilisateur que vous souhaitez C# exposer dans le code.
1. Enregistrez les modifications apportées.

-----

> [!IMPORTANT]
> Bien qu’il soit possible d’assigner `TouchUpInside` des événements tels que à un élément d' `UIButton`interface utilisateur (par exemple,) dans le concepteur iOS, il ne sera jamais appelé, car Apple TV n’a pas d’écran tactile ni d’événements tactiles de prise en charge. Vous devez toujours utiliser l' `Primary Action` événement lors de la création de gestionnaires d’événements pour les éléments d’interface utilisateur tvOS.

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Utilisation des barres d’onglets

Utilisez la `Items` propriété `UITabBar` du `UITabBarItems` pour accéder à la collection qu’il contient comme un tableau indexé zéro (0). La `SelectedItem` propriété retourne l’onglet actuellement sélectionné (catégorie) `UITabBarItem`en tant que.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Utilisation des éléments de barre d’onglets

Pour afficher un badge sur un onglet donné (un ovale rouge avec du texte blanc), utilisez le code suivant :

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Ce qui produit les résultats suivants lors de l’exécution :

[![](tab-bars-images/tab06.png "Élément de barre d’onglets avec badge")](tab-bars-images/tab06.png#lightbox)

Utilisez la `Title` propriété `UITabBarItem` du pour modifier le titre et la `Image` propriété pour changer l’icône.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation du contrôleur de barre d’onglets à l’intérieur d’une application Xamarin. tvOS.




## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
