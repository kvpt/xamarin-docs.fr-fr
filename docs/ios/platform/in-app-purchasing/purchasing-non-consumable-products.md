---
title: Achat de produits non-consommables dans Xamarin. iOS
description: Ce document décrit les produits non consommables dans Xamarin. iOS, qui sont des fonctionnalités achetées par un utilisateur qui restent disponibles indéfiniment, quel que soit l’appareil.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 99723c390b217600e6aaea570195cf21b177abf0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648164"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Achat de produits non-consommables dans Xamarin. iOS

Les produits non consommables sont «détenus» par le client. L’objectif est qu’ils auront toujours accès à ceux-ci, même si leur appareil est perdu/volé ou s’ils en achètent un nouveau. Ils sont utiles pour les livres, les problèmes de magazine, les niveaux de jeu, les filtres photo, les fonctionnalités Pro, etc. Une fois qu’un utilisateur a acheté un produit non utilisable, il n’a jamais à le payer à nouveau. Si votre code les laisse accidentellement essayer, StoreKit affiche un message indiquant qu’il a déjà été acheté.

## <a name="non-consumable-products-sample"></a>Exemple de produits non consommables

Le [code InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contient un projet appelé «autres *biens*». L’exemple de code montre comment implémenter des produits non consommables à l’aide de filtres photo comme exemple. Une fois que vous avez acheté un filtre, vous pouvez l’appliquer à nouveau à la photo. Vous n’avez jamais besoin de le réacheter.   
   
   
   
 Le processus d’achat est illustré dans cette série de captures d’écran: le bouton **acheter** devient le bouton d’activation des fonctionnalités:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "Le processus d’achat est illustré dans cette série de captures d’écran")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 Le processus d’achat est identique à celui d’un produit consommable. la principale différence réside dans la façon dont l’achat est suivi dans le code de l’application. Dans cet exemple, le bouton acheter est disponible uniquement si le produit n’a pas déjà été acheté. sinon, le bouton active la fonctionnalité elle-même.   
   
   
   

Le diagramme suivant montre les interactions entre les classes et le serveur de l’App Store pour effectuer un achat de produit non consommable:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Interactions entre les classes et le serveur App Store pour effectuer un achat de produit non consommable")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 La principale différence par rapport à l’exemple consommable est que, une fois l’achat terminé, l’interface utilisateur est mise à jour pour empêcher la réachat. Dans cet exemple, la notification d’une transaction réussie met à jour l’interface utilisateur, de sorte que le bouton **acheter** est converti en un bouton qui active la fonctionnalité elle-même.

## <a name="re-purchasing-non-consumable-products"></a>Réachat de produits non consommables

Votre code doit normalement masquer ou réaffecter un bouton d’achat une fois que le produit a été acheté avec succès, afin d’empêcher l’utilisateur d’essayer d’acheter le produit. L’exemple d’application effectue cette opération en remplaçant le bouton **acheter** par le bouton qui permet de faire fonctionner l’exemple de filtre de photos.   
   
   
   
 Dans certains cas, une application ne peut pas déterminer si un produit non utilisable a déjà été acheté:

-  Si une application est supprimée et réinstallée sur un appareil, tous les enregistrements d’achat sont supprimés (sauf si/jusqu’à ce que l’utilisateur effectue une restauration de sauvegarde). 
-  Si l’utilisateur a installé l’application sur deux (ou plus) appareils et effectue un achat sur l’un des appareils. Les autres appareils continuent à afficher le produit disponible à l’achat. 
-  Si un client tente de réacheter un produit non consommable dans ce cas, l’App Store réexécutera le produit sans frais. L’interface utilisateur s’affiche initialement pour effectuer un achat (par exemple, une alerte de confirmation s’affiche et l’ID Apple est requis). Toutefois, l’utilisateur voit un message le avertissant que le produit a déjà été acheté.  
   
   
   
 Le chemin de code dans ce scénario est exactement le même qu’un achat régulier, les seules différences sont les suivantes:

-  L’utilisateur n’est pas facturé à nouveau pour le produit.
-  L' `SKPaymentTransaction` objet passé à l’application aura une `OriginalTransaction` propriété qui fait référence à la transaction qui a été générée lors de l’achat initial du produit. 
-  Les applications qui vendent des produits non consommables doivent également implémenter la fonctionnalité de **restauration** de StoreKit pour aider les utilisateurs à récupérer des achats existants. 
