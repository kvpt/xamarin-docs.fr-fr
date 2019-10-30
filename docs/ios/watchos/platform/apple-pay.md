---
title: Apple Pay sur Watchos dans Xamarin
description: Cet article aborde les améliorations apportées par Apple pour Apple Pay dans Watchos 3 et comment les implémenter dans Xamarin. iOS pour les Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 372b034b7e14f3cfaadde8fe5a5370e368f161db
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030126"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay sur Watchos dans Xamarin

Apple a apporté plusieurs améliorations à Apple Pay dans Watchos 3, qui ajoute la prise en charge des paiements dans l’application. Cela permet à l’utilisateur de fournir en toute sécurité des informations de paiement et de contact pour payer des biens et services physiques directement à partir de la Apple Watch.

## <a name="about-apple-pay-enhancements"></a>À propos des améliorations apportées à Apple Pay

Comme indiqué ci-dessus, Apple a apporté plusieurs améliorations à Apple Pay dans Watchos 3, qui permettent de sécuriser le paiement et les informations de contact pour payer des biens et services physiques directement à partir du Apple Watch. Ces améliorations sont fournies par les modifications apportées à l’infrastructure PassKit.

Avec iOS 10 et Watchos 3, plusieurs nouvelles API ont été ajoutées qui fonctionnent avec iOS et Watchos pour prendre en charge les réseaux de paiement dynamiques et un nouvel environnement de test sandbox.

## <a name="passkit-framework-enhancements"></a>Améliorations apportées à PassKit Framework

Dans iOS 10, le Framework PassKit a été étendu pour prendre en charge les Apple Pay en dehors de `UIKit` et pour permettre aux émetteurs de cartes de présenter leurs cartes à partir de leurs applications. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Prise en charge des Apple Pay en dehors de UIKit

En utilisant [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) et [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), une application peut prendre en charge les mêmes fonctionnalités que celles fournies par [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sans utiliser uikit. Si cette nouvelle API est requise pour prendre en charge les Apple Pay sur le Apple Watch (et dans des intentions spécifiques également), elle est facultative dans d’autres situations (telles que les applications existantes). Toutefois, Apple suggère de passer à la nouvelle API dès que possible pour fournir une prise en charge de Apple Pay étendue dans toutes les applications du développeur avec une seule base de code. Pour plus d’informations sur les intentions et l’intégration de Siri, consultez notre [Introduction à la documentation de SiriKit](~/ios/platform/sirikit/index.md) .

### <a name="presenting-issuer-cards-from-within-apps"></a>Présentation de cartes d’émetteur à partir d’applications

Avec iOS 10 et Watchos 3, de nouvelles fonctionnalités ont été ajoutées à l’infrastructure PassKit, qui permettent aux émetteurs de cartes de présenter leurs cartes de paiement à partir de leurs propres applications. Le développeur peut ajouter une `PKPaymentButtonTypeInStore` UIButton à l’interface utilisateur de l’application qui affichera un bouton Apple Pay pour une carte.

La méthode `PresentPaymentPass` de la classe [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) peut également être utilisée pour afficher la carte par programme.

## <a name="new-payment-network-support"></a>Nouvelle prise en charge du réseau de paiement

Nouveauté d’iOS 10 et Watchos 3, une application peut prendre en charge automatiquement un nouveau réseau de paiement quand elle est disponible sans que le développeur n’ait à modifier, recompiler l’application et la renvoyer à l’App Store.

La nouvelle méthode [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) de la classe `PKPaymentNetwork` permet à une application de détecter les réseaux disponibles sur l’appareil de l’utilisateur au moment de l’exécution. En outre, la propriété [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) a été développée pour prendre le nom du fournisseur de paiement comme argument. À l’aide de ces méthodes, une application peut prendre en charge automatiquement n’importe quel réseau pris en charge par le fournisseur de paiement.

Pour plus d’informations, reportez-vous à la [configuration de Apple Pay](~/ios/platform/apple-pay.md) et au [Guide d’Apple Pay](https://developer.apple.com/apple-pay/)d’Apple.

## <a name="new-testing-environment"></a>Nouvel environnement de test

Avec iOS 10 et Watchos 3, Apple a introduit un nouvel environnement de test qui permet au développeur d’approvisionner des cartes de paiement de test directement sur un appareil iOS. Ce nouvel environnement de test retourne ensuite les données de paiement de test chiffrées à l’application.

Pour activer le nouvel environnement de test, procédez comme suit :

1. Créez un nouveau compte de test iCloud dans iTunes Connect.
2. Connectez-vous à l’appareil iOS avec le nouveau compte de test.
3. Définissez la région de votre choix pour tester l’application.
4. Utilisez l’une des cartes de paiement de test du [Guide de Apple Pay](https://developer.apple.com/apple-pay/) pour effectuer des paiements.

> [!NOTE]
> En basculant les comptes iCloud, l’appareil passe automatiquement au nouvel environnement de test. Toutefois, Apple **requiert** toujours que l’application soit testée avec des cartes réelles dans un environnement de production avant d’être envoyée à l’iTunes App Store.

## <a name="summary"></a>Récapitulatif

Cet article a abordé les améliorations apportées par Apple pour Apple Pay dans Watchos 3 et comment les implémenter dans Xamarin. iOS.
