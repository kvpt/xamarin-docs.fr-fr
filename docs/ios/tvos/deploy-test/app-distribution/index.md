---
title: Présentation de la distribution d’applications tvOS
description: Ce document fournit une vue d’ensemble des techniques de distribution disponibles pour l’application Xamarin. tvOS et sert de pointeur vers des documents plus détaillés dans la rubrique.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b311f3121ea6a58975d41b9690e31a44daa0951e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573701"
---
# <a name="tvos-app-distribution-overview"></a>Présentation de la distribution d’applications tvOS

_Ce document fournit une vue d’ensemble des techniques de distribution disponibles pour l’application Xamarin. tvOS et sert de pointeur vers des documents plus détaillés dans la rubrique._

Une fois votre application Xamarin. tvOS développée, l’étape suivante du cycle de vie du développement de logiciels consiste à distribuer votre application aux utilisateurs, comme indiqué dans la section en surbrillance du diagramme ci-dessous :

[![Présentation du cycle de vie du développement de logiciels](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple offre les moyens suivants de distribuer une application tvOS, prise en charge par Xamarin. tvOS :

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interne (Entreprise)**](#In-House-Distribution) 
3. [**Ad hoc**](#Ad_Hoc_Distribution) 

Dans tous ces scénarios, les applications doivent être provisionnées à l’aide du *profil de provisionnement* approprié. Les profils de provisionnement sont des fichiers qui contiennent les informations sur le code de signature, ainsi que l’identité de l’application et le mécanisme de distribution prévu. Pour la distribution hors App Store, ils contiennent également des informations relatives aux appareils sur lesquels l’application peut être déployée.

<a name="Apple-TV-App-Store-Distribution"></a>

## <a name="apple-tv-app-store-distribution"></a>Distribution d’Apple TV App Store

Il s’agit de la principale façon dont les applications tvOS sont distribuées aux consommateurs sur des appareils Apple TV. Toutes les applications soumises à Apple TV App Store requièrent l’approbation d’Apple.

Les applications sont soumises à l’App Store via un portail appelé *iTunes Connect*. Pour plus d’informations sur les sujets suivants, consultez notre guide [de configuration de votre application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) :

- Gestion des contrats, des taxes et des banques.
- Création d’un enregistrement iTunes Connect.
- Gestion des vidéos et des captures d’écran de l’application.
- Gestion du nom de l’application, de la description, des nouveautés, des mots clés et des URL.
- Gestion des informations générales sur l’application.
- Gestion des informations de Game Center.
- Mise à jour des informations de révision de l’application.
- Gestion des informations de tarification.
- Gestion des informations sur les achats dans l’application.
- Affichage des révisions d’application.

Bien qu’ils ne soient pas écrits spécifiquement pour tvOS, ce document fournit des informations sur la façon de configurer et d’utiliser ce portail pour préparer votre application à la publication dans l’App Store d’Apple TV. La plupart de ces étapes sont nécessaires si vous configurez une application iOS ou tvOS.

Une fois que vous avez effectué toutes les étapes décrites ci-dessus, consultez notre page [configurer votre application tvOS dans iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) pour ajouter les informations spécifiques à l’application tvOS qui seront nécessaires.

Il est important de noter que seuls les développeurs membres du **programme Developer d’Apple** ont accès à iTunes Connect. Les membres du programme **Developer Enterprise d’Apple** n’y ont pas accès.

Si vous rencontrez des problèmes lors de l’envoi de votre application Xamarin. tvOS à Apple TV App Store, consultez notre guide de [Dépannage](~/ios/tvos/troubleshooting.md) . Il contient plusieurs problèmes connus que vous pouvez rencontrer et comment les résoudre dans Xamarin. tvOS.

Pour plus d’informations, consultez la [publication dans le guide Apple TV App Store](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) .

<a name="In-House-Distribution"></a>

## <a name="in-house-distribution"></a>Distribution en interne

Parfois appelée *Distribution d’entreprise*, la distribution en interne permet aux membres du **programme Developer Enterprise d’Apple** de distribuer des applications en interne à d’autres membres de la même organisation. La distribution en interne présente l’avantage de ne pas nécessiter une évaluation pour l’App Store et de ne pas limiter le nombre d’appareils sur lesquels une application peut être installée. Toutefois, il est important de noter que les membres du **programme Developer Enterprise d’Apple** n’ont **pas** accès à iTunes Connect, et que le licencié est donc responsable de la distribution de l’application.

Pour plus d’informations sur la mise en place et la distribution de votre application en interne, consultez le [Guide de distribution en interne](~/ios/deploy-test/app-distribution/in-house-distribution.md). Ce document est spécifique à iOS, mais les mêmes techniques sont utilisées pour les applications tvOS.

<a name="Ad_Hoc_Distribution"></a>

## <a name="ad-hoc-distribution"></a>Distribution ad hoc

Les applications Xamarin. tvOS peuvent être testées par l’utilisateur via une distribution ad hoc, qui est disponible à la fois dans le **programme de développement Apple**et dans le **programme Developer Enterprise de Apple**, et permet de tester jusqu’à 100 appareils TV Apple. Le meilleur cas d’utilisation de la distribution ad hoc est la distribution au sein de votre entreprise quand iTunes Connect n’est pas une option.

Pour plus d’informations sur la mise en place et la distribution de votre application en interne, consultez le Guide de [distribution ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Là encore, ce document est spécifique à iOS, mais les mêmes techniques sont utilisées pour les applications tvOS.

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a donné un bref aperçu des mécanismes de distribution disponibles pour les applications Xamarin. tvOS. Il a introduit l’App Store d’Apple TV, le déploiement ad hoc et le déploiement interne et fournit des liens vers des informations plus détaillées.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
