---
title: Apple Pay dans Xamarin. iOS
description: Ce guide explore la configuration de l’environnement Xamarin. iOS pour une utilisation avec Apple Pay pour payer des biens physiques, tels que des aliments, des loisirs et des appartenances via votre application. Elle contient des informations sur les identificateurs, les certificats et les habilitations requis.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: 1d9a65ab34cb0c02368f53679d38f1d07ec1f257
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198558"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay dans Xamarin. iOS

_Ce guide explore la configuration de l’environnement Xamarin. iOS pour une utilisation avec Apple Pay pour payer des biens physiques, tels que des aliments, des loisirs et des appartenances via votre application. Elle contient des informations sur les identificateurs, les certificats et les habilitations requis._

Apple Pay a été introduite avec iOS 8, permettant aux utilisateurs de payer des biens physiques tels que des aliments, du divertissement et des appartenances via leurs appareils iOS. Elle est disponible sur iPhone 6 et iPhone 6 plus, et peut également être associée aux Apple Watch pour les achats en magasin. Lorsqu’il est utilisé sur un iPhone, il utilise Touch ID pour confirmer et autoriser les transactions sur la carte de crédit ou de débit d’un utilisateur.

## <a name="requirements"></a>Configuration requise

Apple Pay n’est disponible que dans iOS 8 et versions ultérieures, et nécessite donc au minimum Xcode 6.

Les éléments suivants sont également requis pour intégrer Apple Pay à votre application :

- Plateforme du processeur de paiement
- Identificateur de vendeur
- Un certificat de Apple Pay
- Apple Pay droit

Ce document examine ces éléments plus en détail.

## <a name="differences-between-apple-pay-and-iap"></a>Différences entre Apple Pay et IAP

La principale différence entre les Apple Pay et les *achats dans l’application* (IAP) concerne les produits qu’ils vendent. Les biens *physiques* sont vendus par le biais de Apple Pay ; les aliments, le logement et le divertissement physique (tels que les tickets de cinéma) sont tous des exemples. En revanche, les IAP vendent des biens *virtuels* , tels qu’un contenu supplémentaire ou Premium, ainsi que des abonnements, pensez à des mois supplémentaires d’un service de streaming, ou à des vies supplémentaires dans un jeu.

Les frameworks utilisés sont également une différence clé ; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) est utilisé pour Apple Pay, tandis que [STOREKIT](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fournit l’API Framework pour l’IAP.

Avec Apple Pay, Apple [déclare](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) qu’il n’y a pas de frais pour les utilisateurs, les commerçants ou les développeurs à utiliser Apple Pay pour les paiements». En comparaison, l’IAP a une facturation de 30% pour chaque transaction. En outre, avec Apple Pay, la transaction ne passe pas par Apple, mais elle passe par une plateforme de paiement.

## <a name="using-a-payment-processor-platform"></a>Utilisation d’une plateforme de processeur de paiement

L’une des parties fondamentales de Apple Pay est le traitement des paiements. Bien qu’il soit possible de le faire vous-même, cela nécessite une connaissance importante du chiffrement, comme indiqué dans le [Guide de traitement des paiements](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)d’Apple.
Les plateformes de traitement des paiements, en revanche, gèrent ces opérations pour vous, ce qui vous permet de vous concentrer sur la création de votre application.

Deux options sont disponibles :

- **Stripe** -Inscrivez-vous sur [Stripe.com](https://stripe.com/) pour accéder à leurs API.

- **JudoPay** : consultez l' [exemple de code Xamarin sur GitHub](https://github.com/Judopay/Xamarin-Sample-App)et inscrivez-vous à [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Approvisionnement pour Apple Pay

La configuration d’une application pour qu’elle utilise Apple Pay nécessite une configuration sur le portail des développeurs Apple et dans votre application. Vous devez suivre un certain nombre d’étapes pour configurer correctement votre application pour Apple Pay :

1. Créer un ID de marchand :
    - Suivez les étapes [ci-dessous](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid) .
2. Créez un ID d’application avec la fonction appliquer le paiement et ajoutez-y le marchand :
    - Suivez les étapes [ci-dessous](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid) .
3. Générez un certificat pour l’ID de commerçant :
    - Suivez les étapes [ci-dessous](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate) .
4. Générez un profil de provisionnement avec l’ID d’application nouvellement créé :
    - Suivez les étapes [ci-dessous](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning) .
5. Ajoutez Apple Pay droits :
    - Sélectionnez Apple Pay habilitation comme détaillé [ici](~/ios/deploy-test/provisioning/entitlements.md), ou ajoutez manuellement la paire clé/valeur au fichier à partir d' [ici](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Utilisation de Apple Pay

Apple a apporté plusieurs améliorations à Apple Pay dans iOS 10 qui permettent à l’utilisateur d’effectuer des paiements sécurisés à partir de sites Web et par le biais de l’interaction avec Siri et Maps.

Avec iOS 10, plusieurs nouvelles API ont été ajoutées qui fonctionnent avec iOS et Watchos pour prendre en charge les réseaux de paiement dynamiques et un nouvel environnement de test bac à sable (sandbox).

### <a name="apple-pay-website-integration"></a>Intégration d’un site Web Apple Pay

Nouveauté d’iOS 10, le développeur peut incorporer des Apple Pay directement dans leurs sites Web à l’aide de **APPLEPAY js**. Les utilisateurs qui parcourent le site Web avec Safari dans iOS ou macOS peuvent effectuer des paiements avec Apple Pay en validant la transaction sur leur iPhone ou Apple Watch. Pour plus d’informations, consultez le Guide de référence d’Apple [APPLEPAY JP Framework](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Améliorations apportées à PassKit Framework

Dans iOS 10, PassKit Framework a été étendu pour prendre en charge les Apple Pay `UIKit` en dehors de et pour permettre aux émetteurs de cartes de présenter leurs propres cartes depuis leurs applications.

#### <a name="supporting-apple-pay-outside-of-uikit"></a>Prise en charge des Apple Pay en dehors de UIKit

En utilisant [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) et [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), une application peut prendre en charge les mêmes fonctionnalités que celles fournies par [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sans utiliser uikit. Si cette nouvelle API est requise pour prendre en charge les Apple Pay sur le Apple Watch (et dans des intentions spécifiques également), elle est facultative dans d’autres situations (telles que les applications existantes). Toutefois, Apple suggère de passer à la nouvelle API dès que possible pour fournir une prise en charge de Apple Pay étendue dans toutes les applications du développeur avec une seule base de code. Pour plus d’informations sur les intentions et l’intégration de Siri, consultez notre [Introduction à la documentation de SiriKit](~/ios/platform/sirikit/index.md) .

#### <a name="presenting-issuer-cards-from-within-apps"></a>Présentation de cartes d’émetteur à partir d’applications

Avec iOS 10, de nouvelles fonctionnalités ont été ajoutées à l’infrastructure PassKit, qui permettent aux émetteurs de cartes de présenter leurs cartes à partir de leurs propres applications. Le développeur peut ajouter un `PKPaymentButtonTypeInStore` UIButton à l’interface utilisateur de l’application qui affichera un bouton Apple Pay pour une carte.

La `PresentPaymentPass` méthode de la classe [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) peut également être utilisée pour afficher la carte par programme.

### <a name="new-payment-network-support"></a>Nouvelle prise en charge du réseau de paiement

Nouveauté d’iOS 10, une application peut prendre automatiquement en charge un nouveau réseau de paiement quand elle est disponible sans que le développeur n’ait à modifier, recompiler l’application et la renvoyer à l’App Store.

La nouvelle méthode [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) de la `PKPaymentNetwork` classe permet à une application de détecter les réseaux disponibles sur l’appareil de l’utilisateur au moment de l’exécution. En outre, la propriété [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) a été développée pour prendre le nom du fournisseur de paiement comme argument. À l’aide de ces méthodes, une application peut prendre en charge automatiquement n’importe quel réseau pris en charge par le fournisseur de paiement.

Pour plus d’informations, reportez-vous à la [configuration de Apple Pay](~/ios/platform/apple-pay.md) et au [Guide d’Apple Pay](https://developer.apple.com/apple-pay/)d’Apple.

### <a name="new-testing-environment"></a>Nouvel environnement de test

Avec iOS 10, Apple a introduit un nouvel environnement de test qui permet au développeur d’approvisionner des cartes de paiement de test directement sur un appareil iOS. Ce nouvel environnement de test retourne ensuite les données de paiement de test chiffrées à l’application.

Pour activer le nouvel environnement de test, procédez comme suit :

1. Créez un nouveau compte de test iCloud dans iTunes Connect.
2. Connectez-vous à l’appareil iOS avec le nouveau compte de test.
3. Définissez la région de votre choix pour tester l’application.
4. Utilisez l’une des cartes de paiement de test du [Guide de Apple Pay](https://developer.apple.com/apple-pay/) pour effectuer des paiements.

> [!IMPORTANT]
> En basculant les comptes iCloud, l’appareil passe automatiquement au nouvel environnement de test. Toutefois, Apple **requiert** toujours que l’application soit testée avec des cartes réelles dans un environnement de production avant d’être envoyée à l’iTunes App Store.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons examiné les différents éléments requis pour utiliser Apple Pay dans votre application. Nous avons vu comment créer un ID de marchand et comment il est utilisé dans les **droits. plist**, qui doivent être modifiés manuellement.

## <a name="related-links"></a>Liens associés

- [Achats dans l’application](~/ios/platform/in-app-purchasing/index.md)
- [Présentation de PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-emporium)
