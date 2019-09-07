---
title: Icônes App Store dans Xamarin. iOS
description: Ce document explique comment utiliser les catalogues de ressources pour gérer une icône de magasin d’applications pour une application Xamarin. iOS. Auparavant, les icônes App Store étaient gérées avec iTunes Connect.
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/26/2017
ms.openlocfilehash: e0b899e69405874f381c3ab6f9a9200a856d7402
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767312"
---
# <a name="app-store-icons-in-xamarinios"></a>Icônes App Store dans Xamarin. iOS

Avant l’ajout de Xcode 9, toutes les icônes App Store ont été ajoutées via iTunes Connect. Toutefois, ce n’est plus le cas. Les icônes App Store doivent maintenant être incluses dans le cadre de votre projet et ajoutées dans un catalogue de composants. Les applications qui ne contiennent pas d’icône App Store sont rejetées par Apple.

L’icône App Store est la face de votre application aux utilisateurs. elle doit donc être mémorable et s’afficher correctement à une taille réduite. Les icônes mémorables sont nettes, simples et immédiatement reconnaissables.

Apple suggère de suivre les recommandations suivantes pour concevoir des icônes d’application :

- Créez une icône appropriée pour votre application.
- Créez une icône simple et cohérente avec la conception de votre application.
- Évitez d’utiliser des mots dans votre icône.
- Pensez à l’échelle internationale : une seule icône d’application est utilisée dans tous les App Store du monde.

Une image de 1 024 x 1 024 pixels est nécessaire pour l’icône d’application affichée dans l’App Store.  Apple indique que l’icône visible dans l’App Store et qui se trouve dans le catalogue de ressources ne peut pas être transparente ni contenir de canal alpha.

Pour plus d’informations, consultez Instructions de l' [interface utilisateur iOS](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)d’Apple.

## <a name="adding-an-app-store-icon"></a>Ajout d’une icône App Store

Vous devez désormais fournir les icônes à afficher dans l’App Store via un catalogue de ressources. 

Pour ajouter une icône App Store, procédez comme suit :

1. Recherchez l’image **AppIcon** définie dans le fichier **Assets. xcassets** de votre projet. 
    - Tous les nouveaux projets doivent être fournis avec un fichier **Assets. xcassets** qui contient un ensemble d’images AppIcon.
    - Pour ajouter un nouveau catalogue de ressources, cliquez avec le bouton droit sur votre projet, puis sélectionnez **ajouter > nouveau fichier > catalogue de ressources**.
    - Pour ajouter une nouvelle icône d’application icône d’image, cliquez avec le bouton droit dans la zone jeu d’icônes et sélectionnez **icônes d’application & images de lancement > nouvelle icône d’application**:

    ![Add New image Set (option)](app-store-icon-images/image1.png)

2. Faites défiler jusqu’à l’icône **App Store** dans la liste :

    ![Icône App Store](app-store-icon-images/image2.png)

3. Cliquez sur l’icône et recherchez votre image 1024 x 1024 pixels. Enregistrez le catalogue de ressources.

## <a name="related-links"></a>Liens associés

- [Gestion des icônes avec les catalogues de ressources](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
