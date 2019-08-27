---
title: Services Google Licensing
ms.prod: xamarin
ms.assetid: E96BDCC3-454A-A797-5819-905E2BB1AC41
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 12/20/2017
ms.openlocfilehash: 47b9d76cb0b7b2fc48fe83d291479f7d1ed00eb8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525214"
---
# <a name="google-licensing-services"></a>Services Google Licensing

Avant Google Play, les applications Android devaient se fier à la Protection contre la copie proposée par Google Market pour s’assurer que seuls les utilisateurs autorisés pouvaient exécuter des applications sur leurs appareils. Les limitations du mécanisme de protection contre la copie en faisaient une solution tout sauf idéale pour la protection de l’application.

Google Licensing vient remplacer ce mécanisme de protection contre la copie.
Google Licensing est un service réseau souple et sécurisé que les applications Android peuvent interroger pour déterminer si une application dispose d’une licence pour s’exécuter sur un appareil donné.

Google Licensing est souple dans le sens où les applications Android contrôlent entièrement quand vérifier la licence, à quelle fréquence la vérifier et comment gérer la réponse du serveur de licences.

Google Licensing est sécurisé dans le sens où chaque réponse est signée à l’aide d’une paire de clés RSA qui est partagée exclusivement entre le serveur Google Play et l’application. Google Play fournit une clé publique pour les développeurs qui est incorporée dans l’application Android et est utilisée pour authentifier les réponses. Le serveur Google Play conserve la clé privée en interne.

Une application ayant implémenté Google Licensing fait une demande à un service hébergé par l’application Google Play sur l’appareil. Google Play envoie ensuite cette demande sur le serveur Google Licensing, qui répond en indiquant l’état de la licence : 

[![Diagramme du workflow du serveur de licences](google-licensing-services-images/gp-licensing-service-overview.png)](google-licensing-services-images/gp-licensing-service-overview.png#lightbox)

Le diagramme ci-dessus illustre ce workflow : 

- L’application fournit le nom du paquet, un *nonce* (authentificateur de chiffrement) qui est utilisé pour valider la réponse du serveur et un rappel qui peut traiter la réponse de façon asynchrone. 

- Google Play fournit des informations telles que le compte Google et des informations sur l’appareil lui-même, comme le numéro IMSI. 

Le service Google Licensing est également un composant essentiel des fichiers d’extension d’APK (qui sont décrits plus loin dans ce document). Les fichiers d’extension d’APK utilisent les services Google Licensing pour obtenir l’URL des fichiers d’extension qui seront téléchargés.


## <a name="requirements"></a>Configuration requise

Les applications qui ne sont pas achetées via Google Play ne bénéficient d’aucun avantage des services Google Licensing. Même si Google Play n’est pas installé sur un appareil, les applications qui utilisent les services de gestion des licences continuent de fonctionner normalement sur cet appareil.

Google Play nécessite un accès Internet. Une application peut mettre en cache la licence dans l’éventualité où l’appareil n’aurait pas accès aux serveurs Google Play Licensing.

Les applications gratuites ont besoin de Google Licensing uniquement si elles utilisent des fichiers d’extension d’APK.
