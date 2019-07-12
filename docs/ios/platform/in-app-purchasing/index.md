---
title: Achats dans Xamarin.iOS dans l’application
description: Ce document décrit comment vendre des produits numériques et des services à l’aide de la APIs StoreKit. Il est lié à des guides qui abordent la configuration, des consommables, produits non consommables, des transactions, des abonnements et bien plus encore.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b4165f9e6c3088a41d6bd746b1912a64570c553a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832555"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Achats dans Xamarin.iOS dans l’application

les applications iOS peuvent vendre des produits numériques ou des services à l’aide de StoreKit – un ensemble d’API fournis par iOS qui communiquent avec les serveurs d’Apple pour effectuer des transactions financières avec l’utilisateur par le biais de leur ID Apple. Les APIs StoreKit sont principalement concernés par la récupération des informations produit et la réalisation des transactions : il n’existe aucun composant de l’interface utilisateur. Les applications qui implémentent les achats dans l’application doivent générer leur propre interface utilisateur et effectuer le suivi d’articles achetés avec du code personnalisé pour fournir des produits requis ou des services à l’utilisateur.

Fournir des fonctionnalités de l’achat dans l’application nécessite plusieurs étapes :

-  **Configuration de votre application** – profil de configuration de l’application doit être configuré correctement.
-  **Créer des produits** – prix et les descriptions de produit doivent être créés dans le portail Connect iTunes.
-  **Implémentation StoreKit** – l’API StoreKit doit être implémentée selon les types de produits vendus.
-  **Création de l’interface utilisateur et les produits eux-mêmes** – les produits doivent être appliquées, y compris les mécanismes pour effectuer le suivi de chaque achat et de sauvegarde/restauration les cas échéant.
-  **Analyse des ventes et la réception des fonds** – utilisez les informations fournies par iTunes Connect pour surveiller les tendances des ventes et de suivre vos revenus.

Ce document explique comment effectuer toutes ces étapes pour fournir des achats dans l’application à l’aide de Xamarin.iOS.

## <a name="requirements"></a>Configuration requise

Pour prendre en charge dans l’application d’achat, vous devez utiliser Xamarin.iOS 5.0 ou version ultérieure avec Xcode 7 et versions ultérieures.

## <a name="contents"></a>Sommaire

* [Principes de base et configuration de l’achat dans l’application](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

* [Vue d’ensemble de StoreKit et de récupération des informations de produit](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

* [Achat de produits consommables](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

* [Achat de produits non consommables](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

* [Transactions et vérification](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

* [Abonnements et création de rapports](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Récapitulatif

Cet article a introduit le concept d’achats dans l’application, décrit comment configurer votre application pour tirer parti et présenté des exemples à l’aide de Xamarin.iOS. Il est couvert :

-  **Portail de provisionnement iOS** – fonctionnalités acheter des instructions d’activation dans l’application.
-  **iTunes Connect** : configuration des produits de vendre dans votre application.
-  **Store Kit** – explication des classes utilisées pour générer des fonctionnalités de l’achat dans l’application.
-  **Coder votre application pour l’achat de** – exemples montrant comment créer des achats dans l’application dans une application Xamarin.iOS.
-  **Création de rapports** – vue d’ensemble des statistiques disponibles via iTunes Connect.


## <a name="related-links"></a>Liens associés

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [Dans le Guide de programmation d’achat de l’application](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guide du développeur iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Référence de l’infrastructure du Kit Store](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificateurs de produits in-App Purchase Q & r](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Note technique achat dans l’application](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [Soumission de votre première application Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centre de ressources App Store](https://developer.apple.com/appstore/index.html)
- [Conseils pour la soumission à l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Recommandations pour l’évaluation dans l’App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Gestion de vos applications](https://developer.apple.com/appstore/resources/managing/index.html)
