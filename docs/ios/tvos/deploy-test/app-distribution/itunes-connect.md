---
title: Configurer votre application tvOS dans iTunes Connect
description: Cet article fournit un guide supplémentaire sur l’iOS configurer votre application dans iTunes Connect pour les configurations spécifiques à tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 92f028a632a32392a349c06c0b7ab8e15b308b9f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279533"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurer votre application tvOS dans iTunes Connect

_Cet article fournit un guide supplémentaire sur l’iOS configurer votre application dans iTunes Connect pour les configurations spécifiques à tvOS._


Outre les configurations et les paramètres que vous devrez effectuer en suivant le guide iOS [configurer votre application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) , ce document couvre les configurations spécifiques qui seront nécessaires pour libérer une application Xamarin. tvOS dans l’application Apple TV. Magasin.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Ajout d’une version finale de tvOS

Que vous soyez en train de créer une application à publier dans l’App Store d’Apple TV ou d’ajouter la prise en charge d’Apple TV à une application iOS existante, vous devez avoir créé un enregistrement iTunes Connect et le configurer à l’aide des guides spécifiques iOS suivants :

- [Création d’un enregistrement iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gestion des vidéos et des captures d’écran de l’application](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gestion du nom, de la description, des nouveautés, des mots clés et des URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Gestion des informations générales](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Si vous le souhaitez, vous pouvez également avoir besoin des éléments suivants :

- [Gestion des informations relatives à Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Gestion des informations relatives aux achats dans l’application](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Une fois toutes les étapes ci-dessus terminées, ouvrez l’enregistrement iTunes Connect de votre application et sélectionnez pour ajouter la prise en charge de tvOS à l’aide de l’encadré de gauche :

[![](itunes-connect-images/connect01.png "Ajouter la prise en charge de tvOS à l’aide de la barre latérale gauche")](itunes-connect-images/connect01.png#lightbox)

Les écrans d’informations spécifiques à tvOS sont ensuite disponibles pour l’enregistrement iTunes Connect donné :

[![](itunes-connect-images/connect02.png "Écran d’informations spécifiques à tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>Informations sur la version de tvOS

Dans la barre latérale gauche, sélectionnez **1,0 préparer pour l’envoi** dans la section application tvOS :

[![](itunes-connect-images/connect03.png "Informations sur la version de tvOS")](itunes-connect-images/connect03.png#lightbox)

Sur cet écran, fournissez les informations suivantes :

- Les captures d’écran, la description, les mots clés et les URL requis.
- Informations générales sur l’application, telles que le numéro de version, le Copyright et l’âge.
- Achats dans l’application facultatifs.
- Prise en charge facultative de la Game Center avec Leaderboards et les réalisations.
- Informations de révision de l’application requises, telles que les contacts, les comptes de démonstration et les notes.

Une fois que vous avez entré les informations requises, cliquez sur le bouton **Enregistrer** dans le coin supérieur droit de l’écran pour enregistrer vos modifications :

[![](itunes-connect-images/connect04.png "Informations sur la version de tvOS prêtes pour l’envoi")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Préparation de l’envoi en vue de la révision

Quand vous êtes enfin prêt à envoyer votre application Xamarin. tvOS à Apple TV App Store pour examen, revenez à l’enregistrement iTunes Connect de l’application, puis cliquez sur le bouton **Envoyer pour révision** dans le coin supérieur droit de l’écran :

[![](itunes-connect-images/connect05.png "Soumettre pour révision")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article donne une vue d’ensemble du paramètre spécifique tvOS requis dans iTunes Connect pour libérer une application tvOS dans le magasin d’applications Apple TV.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
