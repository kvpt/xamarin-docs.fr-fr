---
title: Aperçu de la distribution de l’application Xamarin.iOS
description: Ce document présente les techniques de distribution disponibles pour les applications Xamarin.iOS, et sert de point de départ vers d’autres documents plus détaillés sur le sujet.
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 20126849027f735e9ecd3599c290b4e7a57f837e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886578"
---
# <a name="xamarinios-app-distribution-overview"></a>Aperçu de la distribution de l’application Xamarin.iOS

_Ce document présente les techniques de distribution disponibles pour les applications Xamarin.iOS, et sert de point de départ vers d’autres documents plus détaillés sur le sujet._

Une fois l’application Xamarin.iOS développée, la prochaine étape du cycle de vie du développement du logiciel consiste à la distribuer aux utilisateurs, comme indiqué dans la section en surbrillance du diagramme ci-dessous :

[![Une fois l’application iOS développée, l’étape suivante consiste à distribuer l’application aux utilisateurs, comme le montre la section mise en évidence de ce diagramme](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple fournit les moyens suivants pour distribuer une application iOS :

- [**App Store**](#app-store-distribution)
- [**En interne (entreprise)**](#in-house-distribution)
- [**Ad hoc**](#ad-hoc-distribution)
- [**Applications personnalisées pour les entreprises**](#custom-apps-for-business)

Dans tous ces scénarios, les applications doivent être provisionnées à l’aide du *profil de provisionnement* approprié. Les profils de provisionnement sont des fichiers qui contiennent les informations sur le code de signature, ainsi que l’identité de l’application et le mécanisme de distribution prévu. Pour la distribution hors App Store, ils contiennent également des informations relatives aux appareils sur lesquels l’application peut être déployée.

## <a name="app-store-distribution"></a>Distribution dans l’App Store

> [!IMPORTANT]
> Apple [a indiqué](https://developer.apple.com/ios/submit/) qu’à partir de mars 2019 toutes les applications et mises à jour envoyées à l’App Store doivent avoir été créées avec le kit SDK d’iOS 12.1 ou ultérieur, inclus dans Xcode 10.1 ou ultérieur.
> Les applications doivent aussi prendre en charge les tailles d’écran de l’iPhone XS et de l’iPad Pro 12,9".

Il s’agit de la méthode principalement utilisée pour distribuer des applications iOS aux consommateurs sur des appareils iOS. Toutes les applications soumises à l’App Store nécessitent l’approbation d’Apple.

Les applications sont soumises à l’App Store via un portail appelé *iTunes Connect*. Le guide [Configurer votre application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) fournit des informations supplémentaires sur la configuration et l’utilisation de ce portail pour préparer la publication d’une application Xamarin.iOS dans l’App Store.

Il est important de noter que seuls les développeurs membres du **programme Developer d’Apple** ont accès à iTunes Connect. Les membres du programme **Developer Enterprise d’Apple** n’y ont pas accès.

Pour plus d’informations, visitez le guide [Distribution dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md).

## <a name="in-house-distribution"></a>Distribution interne

Parfois appelée *Distribution d’entreprise*, la distribution en interne permet aux membres du **programme Developer Enterprise d’Apple** de distribuer des applications en interne à d’autres membres de la même organisation. La distribution en interne présente l’avantage de ne pas nécessiter une évaluation pour l’App Store et de ne pas limiter le nombre d’appareils sur lesquels une application peut être installée. Toutefois, il est important de noter que les membres du **programme Developer Enterprise d’Apple** n’ont **pas** accès à iTunes Connect, et que le licencié est donc responsable de la distribution de l’application.

Pour plus d’informations sur la mise en place et la façon de distribuer une application En interne, veuillez consulter le [guide de distribution interne.](~/ios/deploy-test/app-distribution/in-house-distribution.md)

## <a name="ad-hoc-distribution"></a>Distribution ad hoc

Les utilisateurs peuvent tester les applications Xamarin.iOS via la distribution ad hoc, laquelle est disponible à travers les programmes **Developer** et **Developer Enterprise** d’Apple. Elle permet de tester jusqu’à 100 appareils iOS. Le meilleur cas d’usage pour la distribution ad hoc est celui d’une distribution en entreprise sans iTunes Connect.

Pour plus d’informations sur la mise en place et la façon de distribuer une application En interne, veuillez consulter le [guide de distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md).

## <a name="custom-apps-for-business"></a>Applications personnalisées pour les entreprises

Apple permet la [distribution personnalisée](https://developer.apple.com/business/custom-apps/) d’applications aux entreprises et à l’éducation. Consultez le [Guide des utilisateurs d’Apple Business Manager](https://support.apple.com/guide/apple-business-manager/welcome/web) pour plus d’informations.

## <a name="related-links"></a>Liens connexes

- [Distribution dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configuration d’une application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publication dans l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribution interne](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Fichier iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Prise en charge IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Dépannage](~/ios/deploy-test/troubleshooting.md)
