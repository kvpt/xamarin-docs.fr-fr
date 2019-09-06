---
title: Modifications de l’App Store dans iOS 11
description: Ce document explore les modifications apportées à l’App Store dans iOS 11. Il traite de l’icône de magasin d’une application, des achats dans l’application promus, de la page produit repensée, de la communication client et des versions échelonnées.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: fa44ba260a62a6e0bd036c08c62371734b38d82d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286584"
---
# <a name="app-store-changes-in-ios-11"></a>Modifications de l’App Store dans iOS 11

L’App Store iOS a été entièrement repensée, ce qui permet non seulement aux utilisateurs de naviguer efficacement dans le Store, mais également, en tant que développeur, de promouvoir votre application pour les utilisateurs. Ces promotions incluent les mises à jour des achats dans l’application et les mises à jour de la page du produit. iOS 11 ajoute également des mises à jour concernant la communication avec les utilisateurs, l’ajout de votre application icône et la publication de votre application sur le public.

![Nouvelle disposition de l’App Store](app-store-changes-images/image3.jpg)

L’App Store repensé contient les sections suivantes :

- **Aujourd’hui** : cet onglet comprend des applications qui sont un « Pick » de l’éditeur ou une application proposée. Pour promouvoir votre application, renseignez les informations de la page [promouvoir](https://developer.apple.com//contact/app-store/promote/) .
- **Jeux** : toutes les applications définies en tant que catégorie de **jeu** dans iTunes Connect sont accessibles sous cet onglet.
- **Applications** : cet onglet comprend toutes les autres applications. Les utilisateurs peuvent parcourir les applications proposées, les catégories, les premiers à payer/gratuit.
- **Mises à jour** : cette application affiche les mises à jour de vos applications. Même si vous publiez une application via une [version échelonnée](#Phased_Release), les utilisateurs peuvent toujours accéder à cet onglet et télécharger la version la plus récente.
- **Rechercher** : cet onglet permet aux utilisateurs de rechercher votre application.

## <a name="store-icon"></a>Icône de magasin

Les icônes de stockage (ou les icônes marketing) ne sont plus gérées dans iTunes Connect. elles doivent plutôt être contenues en tant que [catalogue de composants](~/ios/app-fundamentals/images-icons/app-icons.md) dans votre fichier binaire d’application, comme les icônes d’application. Une icône de magasin 1024 x 1024 au format PNG doit être incluse dans un catalogue de composants pour pouvoir être envoyée correctement pour les applications iOS 11.

L’affinage de l’application permet de s’assurer que ce catalogue de ressources supplémentaire n’augmente pas la taille de l’application.


## <a name="in-app-purchases-promoted-in-the-app-store"></a>Achats dans l’application promus dans l’App Store

Apple a fait des achats dans l’application plus détectables dans l’App Store. Vous pouvez maintenant ajouter jusqu’à 20 _App Store_ dans des achats dans l’application, qui sont désormais disponibles sur la page des applications, sur la page de produit de votre application ou en effectuant une recherche.

Pour afficher vos achats dans l’application dans l’App Store, vous devez inclure les données suivantes :

- **Image** : vous devez fournir une image spécialement conçue pour l’icône qui décrit ce que fait l’achat dans l’application. Cette image doit être différente de l’icône de l’application et ne peut pas être une capture d’écran.
- **Nom** : le nom ne peut pas dépasser 30 caractères.
- **Description** : la description ne peut pas dépasser 45 caractères.

Toutes les promotions d’achat dans l’application sont soumises à une révision de l’App Store pour pouvoir être publiées.

Pour que vos achats dans l’application soient disponibles pour la promotion, ouvrez votre application et accédez à **fonctionnalités > achats dans l’application**. Accédez à la section promotion de l' **App Store (facultatif)** et ajoutez une image 1024 x 1024 et **Enregistrez**, comme illustré dans l’image suivante :

![Section promotion de l’App Store dans iTunes Connect](app-store-changes-images/image4.png)

Vous devez également ajouter la `ShouldAddStorePayment` méthode `SKPaymentTransactionObserver` au protocole dans votre application.

Pour plus d’informations sur les promotions relatives aux achats dans l’application, consultez la page [promotion des achats dans l’application](https://developer.apple.com/app-store/promoting-in-app-purchases/) d’Apple.

## <a name="redesigned-product-page"></a>Page produit repensée

Les modifications suivantes ont été apportées à la page du produit :

- Les titres sont maintenant définis à un maximum de 30 caractères
- Un sous-titre a été ajouté
  - Il doit s’agir d’un bref résumé qui vient compléter le titre.
  - Le sous-titre doit avoir un maximum de 30 caractères
- Aperçus de l’application
  - Vous pouvez maintenant avoir trois vidéos ou captures d’écran.
  - Lecture automatique vidéo quand un utilisateur visite la page.
  - Pour plus d’informations, consultez la page d’aperçu de l' [application](https://developer.apple.com/app-store/app-previews/) Apple.
- Texte promotionnel
  - Cette nouvelle fonctionnalité fournit 170 caractères de texte, ce qui vous permet de décrire les informations fréquemment modifiées sur votre application.
  - Il peut être mis à jour à tout moment, sans qu’il soit nécessaire de soumettre une nouvelle version de l’application.

## <a name="customer-communication"></a>Communication client

Dans 10,3, Apple a lancé une nouvelle façon permettant aux développeurs de communiquer directement avec les utilisateurs de l’application via la possibilité de répondre aux révisions. Vous pouvez accéder à cette nouvelle fonctionnalité dans iTunes Connect en accédant à **App > Activity > ratings and Reviews**, comme illustré dans l’image suivante :

![Boîte de dialogue d’indication de la zone pour entrer une réponse à un commentaire](app-store-changes-images/image5.png)

Voici quelques éléments à prendre en compte lors de la réponse aux utilisateurs :

- Vous ne pouvez répondre qu’une seule fois, mais les deux parties peuvent modifier leurs commentaires autant que nécessaire.
- Les utilisateurs reçoivent une notification lorsque vous répondez à un commentaire.
- Un nouveau rôle de support technique a été créé dans iTunes Connect. Les utilisateurs disposant de ce rôle ou d’un rôle d’administrateur peuvent répondre aux commentaires.

Pour plus d’informations, consultez la page [réponse à des avis](https://developer.apple.com/app-store/responding-to-reviews/) d’Apple.

<a name="Phased_Release"/>

## <a name="phased-release"></a>Version échelonnée

Avec iOS 11, Apple a implémenté l’option des mises en production échelonnées pour les mises à jour de votre application. Vous pouvez activer les mises en production échelonnées pour permettre à la mise à jour de votre application d’être déchargée progressivement pour les clients, garantissant ainsi que la demande ne surcharge pas votre environnement de production.

Les mises en production échelonnées sont activées dans iTunes Connect. Cliquez sur votre application dans la barre latérale, faites défiler jusqu’à la section **version par phases pour mises à jour automatiques** en bas, puis sélectionnez **mise à jour de mise à jour sur une période de 7 jours à l’aide d’une version échelonnée**:

![Option d’indication de la mise en sortie échelonnée des mises à jour automatiques](app-store-changes-images/image6.png)

Votre mise à jour est disponible immédiatement au téléchargement dans l’onglet mises à jour de l’App Store. Les mises en production échelonnées sont uniquement disponibles pour les utilisateurs qui ont sélectionné les téléchargements automatiques.


## <a name="related-links"></a>Liens associés

- [Nouveautés d’iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Page produit App Store mise à jour (Apple)](https://developer.apple.com/app-store/product-page/)
- [Mise à jour de votre application pour iOS 11 (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/204/)
