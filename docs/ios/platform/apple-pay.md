---
title: Apple Pay dans Xamarin.iOS
description: Ce guide décrit la configuration de l’environnement de Xamarin.iOS pour une utilisation avec Apple Pay de payer des marchandises physiques, telles que la nourriture, les divertissements et les appartenances via votre application. Il inclut des informations sur les identificateurs requis, des certificats et des habilitations.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 7f1851b6a7dec10580c9feea590cf25be30b4aa1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832091"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay dans Xamarin.iOS

_Ce guide décrit la configuration de l’environnement de Xamarin.iOS pour une utilisation avec Apple Pay de payer des marchandises physiques, telles que la nourriture, les divertissements et les appartenances via votre application. Il inclut des informations sur les identificateurs requis, des certificats et des habilitations._

Apple Pay a été introduite en même temps qu’iOS 8, permettant aux utilisateurs de payer des marchandises physiques tels que des produits alimentaires, les divertissements et les appartenances via leurs appareils iOS. Il est disponible sur iPhone 6 et iPhone 6 Plus et peut également être associé à l’Apple Watch pour les achats dans le magasin. Lorsqu’il est utilisé sur un iPhone, il utilise des Touch ID pour confirmer et autoriser des transactions à d’un utilisateur carte de crédit ou débit.

## <a name="requirements"></a>Configuration requise

Apple Pay est uniquement disponible dans iOS 8 et versions ultérieures et par conséquent nécessite un minimum de Xcode 6.

Les éléments suivants sont également requis pour intégrer Apple Pay dans votre application :

- Plateforme de processeur de paiement
- Identificateur de marchand
- Un certificat Apple Pay
- Droit Apple Pay

Ce document examinerons ces éléments plus en détail.

## <a name="differences-between-apple-pay-and-iap"></a>Différences entre Apple Pay et produits

La principale différence entre Apple Pay et *achats dans l’application* (produits), se rapporte aux produits qu’ils vendent. *Physique* biens sont vendus via Apple Pay ; alimentaire, hébergement et divertissement physique (par exemple, les tickets de cinéma) sont des exemples de cela. En revanche, vend des produits *virtuel* marchandises, par exemple premium ou des contenus supplémentaires et des abonnements : pensez à des mois supplémentaires d’un service de diffusion en continu, ou supplémentaire se trouve dans un jeu.

Les frameworks utilisés sont également une différence essentielle ; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) est utilisée pour Apple Pay, alors que [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fournit l’API de l’infrastructure pour les produits.

Avec Apple Pay, Apple [états](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) qu’il « [] pas facture-t-il les utilisateurs, les commerçants ou les développeurs à utiliser Apple Pay pour les paiements ». En comparaison, les produits a un coût de 30 % pour chaque transaction. En outre, avec Apple Pay, la transaction ne traverse pas Apple du tout, au lieu de cela, il passe par une plateforme de paiement.

## <a name="using-a-payment-processor-platform"></a>À l’aide d’une plateforme de processeur de paiement

Une des parties fondamentales de Apple Pay est le traitement des paiements. Bien qu’il soit possible de le faire vous-même, il nécessite une connaissance significative de la cryptographie
- comme détaillé dans Apple [guide de traitement des paiements](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Plateformes de traitement du paiement, gérer quant à eux, ces opérations pour vous, ce qui vous permet de vous concentrer sur la création de votre application.

Deux options sont les suivantes :

- **Stripe** -Inscrivez-vous sur [Stripe.com](https://stripe.com/) pour accéder à leurs API.

- **JudoPay** -Découvrez leurs [Xamarin exemple de code sur github](https://github.com/Judopay/Xamarin-Sample-App)et pour vous inscrire [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>L’approvisionnement pour Apple Pay

Configuration d’une application pour utiliser Apple Pay nécessite une installation sur le portail des développeurs Apple et au sein de votre application. Il existe un nombre d’étapes à suivre pour configurer correctement votre application pour Apple pay :

1. Créer un ID de marchand :
    - Suivez les étapes [ici](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Créer un ID d’application avec la fonctionnalité Appliquer payer et ajoutez-lui le marchand à celui-ci :
    - Suivez les étapes [ici](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Générer un certificat pour l’ID de marchand :
    - Suivez les étapes [ici](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Générer un profil de provisionnement avec l’ID d’application nouvellement créé :
    - Suivez les étapes [ici](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Ajouter des droits de Apple Pay :
    - Sélectionnez le droit de paiement Apple comme détaillé [ici](~/ios/deploy-test/provisioning/entitlements.md), ou ajoutez manuellement la paire clé/valeur dans le fichier à partir de [ici](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Utilisation de Apple Pay

Apple a apporté plusieurs améliorations à Apple Pay dans iOS 10 qui autorise l’utilisateur à procéder à des paiements sécurisés à partir de sites Web et via l’interaction avec Siri et cartes.

Avec iOS 10, plusieurs nouvelles API ont été ajoutés qui fonctionnent avec iOS et watchOS pour prendre en charge des réseaux de paiement dynamique et un nouvel environnement de test de bac à sable.

### <a name="apple-pay-website-integration"></a>Intégration de site Web d’Apple Pay

Nouveau à iOS 10, le développeur peut incorporer Apple Pay directement dans leurs sites Web à l’aide **ApplePay JS**. Les utilisateurs parcourent le site Web avec Safari dans iOS ou macOS peuvent procéder à des paiements avec Apple Pay en validant la transaction sur leur iPhone ou l’Apple Watch. Pour plus d’informations, consultez le site d’Apple [ApplePay JP Framework référence](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Améliorations du Framework PassKit

Dans iOS 10, le framework PassKit a été développé pour prendre en charge Apple Pay en dehors de `UIKit` et pour permettre des émetteurs de carte présenter leurs propres cartes à partir de leurs applications.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Prise en charge Apple Pay en dehors de UIKit

À l’aide de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) et [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), une application peut prendre en charge les mêmes fonctionnalités fournies par [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sans utiliser UIKit. Cette nouvelle API est requise pour prendre en charge Apple Pay sur l’Apple Watch (et aussi les intentions spécifiques), mais il est facultatif dans d’autres situations (par exemple, les applications existantes). Toutefois, les suggestions d’Apple déplacement à la nouvelle API dès que possible pour fournir d’Apple Pay prise en charge étendue dans l’ensemble de toutes les applications du développeur avec une seule base de code. Pour plus d’informations sur les intentions et l’intégration de Siri, veuillez consulter notre [présentation de SiriKit](~/ios/platform/sirikit/index.md) documentation.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Présentation des cartes de l’émetteur à partir d’au sein d’applications

Avec iOS 10, les nouvelles fonctionnalités ont été ajoutées au framework PassKit acceptant des émetteurs de carte présenter leurs cartes à partir de leurs propres applications. Le développeur peut ajouter un `PKPaymentButtonTypeInStore` UIButton à interface utilisateur de l’application qui affiche un bouton Apple Pay pour une carte.

Le `PresentPaymentPass` méthode de la [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe peut également être utilisée pour afficher par programme la carte.

### <a name="new-payment-network-support"></a>Nouvelle prise en charge réseau de paiement

Nouveau à iOS 10, une application peut automatiquement en charge un nouveau réseau de paiement lorsqu’elle est disponible sans le développeur n’ait à modifier, de recompiler l’application et de le renvoyer à l’App Store.

La nouvelle [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) méthode de la `PKPaymentNetwork` classe permet à une application découvrir les réseaux disponibles sur le périphérique l’utilisateur lors de l’exécution. En outre, le [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) propriété a été étendue pour prendre le paiement nom du fournisseur en tant qu’argument. À l’aide de ces méthodes, une application peut automatiquement prendre en charge n’importe quel réseau prenant en charge le service de paiement.

Pour plus d’informations, consultez notre [Apple payer Configuration](~/ios/platform/apple-pay.md) et d’Apple [Apple payer Guide](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Nouvel environnement de test

Avec iOS 10, Apple a introduit un nouvel environnement de test qui permet au développeur de configurer des cartes de paiement de test directement sur un appareil iOS. Ce nouvel environnement de test renvoie ensuite les données de paiement de test chiffré à l’application.

Pour activer le nouvel environnement de test, procédez comme suit :

1. Créer un nouveau compte d’iCloud test dans iTunes Connect.
2. Connectez-vous à l’appareil iOS avec le nouveau compte de test.
3. Définir la région souhaitée pour tester l’application dans.
4. Utilisez une des cartes de paiement de test à partir de la [Apple payer Guide](https://developer.apple.com/apple-pay/) de paiement.

> [!IMPORTANT]
> En basculant les comptes iCloud, l’appareil bascule automatiquement vers le nouvel environnement de test. Toutefois, Apple toujours **requiert** l’application à tester avec de véritables cartes dans un environnement de production avant la soumission à l’App Store d’iTunes.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons exploré les différents éléments requis pour utiliser Apple Pay au sein de votre application. Nous avons vu comment créer un ID de marchand, et comment elle est utilisée dans le **Entitlements.plist**, qui doit être modifié manuellement.

## <a name="related-links"></a>Liens associés

- [Achats dans l’application](~/ios/platform/in-app-purchasing/index.md)
- [Présentation de PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (exemple)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
