---
title: Achats dans l’application dans Xamarin. iOS
description: Ce document décrit comment vendre des produits et services numériques à l’aide des API StoreKit. Il contient des liens vers des guides qui traitent de la configuration, des produits consommables, des produits non consommables, des transactions, des abonnements, etc.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: fb8f4a8e62ea163dc58589382c31c4bf19973c36
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528269"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Achats dans l’application dans Xamarin. iOS

les applications iOS peuvent vendre des produits ou services numériques à l’aide de StoreKit, un ensemble d’API fournies par iOS, qui communiquent avec les serveurs d’Apple pour effectuer des transactions financières avec l’utilisateur via son IDENTIFIant Apple. Les API StoreKit concernent principalement la récupération des informations sur les produits et la réalisation des transactions. il n’existe pas de composant d’interface utilisateur. Les applications qui implémentent des achats dans l’application doivent créer leur propre interface utilisateur et effectuer le suivi des éléments achetés avec du code personnalisé pour fournir les produits ou services requis à l’utilisateur.

Le fait de fournir des fonctionnalités d’achat dans une application nécessite un certain nombre d’étapes:

- **Configuration de votre application** : le profil de provisionnement de l’application doit être configuré correctement.
- **Création de produits** : les descriptions et les prix des produits doivent être créés dans le portail iTunes Connect.
- **Implémentation de StoreKit** : l’API StoreKit doit être implémentée en fonction des types de produits vendus.
- **Création de l’interface utilisateur et des produits eux-mêmes** : les produits doivent être implémentés, y compris des mécanismes pour suivre chaque achat et les sauvegarder/restaurer, le cas échéant.
- **Monitoring des ventes et des fonds de réception** : utilisez les informations fournies par iTunes Connect pour surveiller les tendances des ventes et suivre vos revenus.

Ce document explique comment effectuer toutes ces étapes pour fournir des achats dans l’application à l’aide de Xamarin. iOS.

## <a name="requirements"></a>Configuration requise

Pour prendre en charge l’achat dans l’application, vous devez utiliser Xamarin. iOS 5,0 ou une version ultérieure avec Xcode 7 et versions ultérieures.

## <a name="contents"></a>Sommaire

* [Principes de base et configuration de l’achat dans l’application](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

* [Présentation de StoreKit et récupération des informations sur le produit](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

* [Achat de produits consommables](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

* [Achat de produits non consommables](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

* [Transactions et vérification](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

* [Abonnements et création de rapports](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Récapitulatif

Cet article a introduit le concept d’achats dans l’application, a expliqué comment configurer votre application pour en tirer parti et présenté des exemples à l’aide de Xamarin. iOS. Il a abordé les sujets suivants:

- **portail d’approvisionnement iOS** : instructions pour l’activation de la fonctionnalité d’achat dans l’application.
- **iTunes Connect** : configuration des produits à vendre dans votre application.
- **Kit de magasin** : explication des classes utilisées pour générer des fonctionnalités d’achat dans l’application.
- **Codage de votre application pour l’achat** – exemples de création d’un achat dans l’application dans une application Xamarin. iOS.
- **Création de rapports** : vue d’ensemble des statistiques disponibles via iTunes Connect.


## <a name="related-links"></a>Liens associés

- [InAppPurchaseSample](https://docs.microsoft.com/en-us/samples/xamarin/ios-samples/storekit/)
- [Dans le Guide de programmation de l’achat d’applications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guide du développeur iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Référence de l’infrastructure du kit Store](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificateurs de produit d’achat dans l’application Q & A](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Note technique sur l’achat dans l’application](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [Votre première soumission App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centre de ressources de l’App Store](https://developer.apple.com/appstore/index.html)
- [Conseils pour la soumission à l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Recommandations pour l’évaluation dans l’App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Gestion de vos applications](https://developer.apple.com/appstore/resources/managing/index.html)
