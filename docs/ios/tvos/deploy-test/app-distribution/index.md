---
title: Présentation de la Distribution App de tvOS
description: Ce document donne une vue d’ensemble des techniques de distribution qui sont disponibles pour l’application de Xamarin.tvOS et sert à un pointeur vers des documents plus détaillés sur le sujet.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: d2f4031eeddbaa206f38b7b1c2bb49d21482c175
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864943"
---
# <a name="tvos-app-distribution-overview"></a>Présentation de la Distribution App de tvOS

_Ce document donne une vue d’ensemble des techniques de distribution qui sont disponibles pour l’application de Xamarin.tvOS et sert à un pointeur vers des documents plus détaillés sur le sujet._


Une fois votre application Xamarin.tvOS a été développée, l’étape suivante dans le cycle de vie de développement de logiciels est de distribuer votre application aux utilisateurs, comme indiqué dans la section en surbrillance du diagramme ci-dessous :


[![La vue d’ensemble du cycle de vie de développement logiciel](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple fournit les méthodes suivantes pour distribuer une application tvOS, qui sont pris en charge par Xamarin.tvOS :

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interne (Entreprise)** ](#In-House-Distribution) 
3. [**Ad hoc**](#Ad_Hoc_Distribution) 

Dans tous ces scénarios, les applications doivent être provisionnées à l’aide du *profil de provisionnement* approprié. Les profils de provisionnement sont des fichiers qui contiennent les informations sur le code de signature, ainsi que l’identité de l’application et le mécanisme de distribution prévu. Pour la distribution hors App Store, ils contiennent également des informations relatives aux appareils sur lesquels l’application peut être déployée.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribution de l’Apple TV App Store

Il s’agit de la méthode principale que les applications de tvOS sont distribuées aux consommateurs sur les appareils Apple TV. Toutes les applications soumises pour le Store d’application Apple TV nécessitent l’approbation d’Apple.

Les applications sont soumises à l’App Store via un portail appelé *iTunes Connect*. Consultez notre [configurer votre application dans iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guide pour plus d’informations sur les rubriques suivantes :

- La gestion des contrats, taxes et des informations bancaires.
- Création un enregistrement iTunes Connect.
- La gestion des vidéos et l’application des captures d’écran.
- Nom de l’application, Description, quelles sont les nouveautés, de la gestion des mots clés et des URL.
- Conserver des informations de l’application de général.
- Gestion des informations de Game Center.
- Conserver des informations d’évaluation de l’application.
- Gestion des informations de la tarification.
- Conserver des informations d’achat dans l’application.
- Affichage des revues de l’Application.

Bien que ne pas spécifiquement écrits pour tvOS, ce document fournit des informations sur la façon de configurer et utiliser ce portail pour préparer votre application pour la publication dans le Store d’application Apple TV. Nombre des mêmes étapes sont requises si vous configurez une application iOS ou tvOS.

Une fois que vous avez terminé toutes les étapes répertoriées ci-dessus, consultez notre [configurer votre application tvOS dans iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) pour ajouter les informations spécifiques d’une application tvOS, seront nécessaire.

Il est important de noter que seuls les développeurs membres du **programme Developer d’Apple** ont accès à iTunes Connect. Les membres du programme **Developer Enterprise d’Apple** n’y ont pas accès.

Si vous rencontrez des problèmes de soumission de votre application Xamarin.tvOS vers le Store d’application Apple TV, veuillez consulter notre [dépannage](~/ios/tvos/troubleshooting.md) guide. Il contient plusieurs problèmes connus que vous pouvez rencontrer et comment les résoudre dans les Xamarin.tvOS.

Pour plus d’informations, visitez le [publication sur le Store d’application Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) guide.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribution en interne

Parfois appelée *Distribution d’entreprise*, la distribution en interne permet aux membres du **programme Developer Enterprise d’Apple** de distribuer des applications en interne à d’autres membres de la même organisation. La distribution en interne présente l’avantage de ne pas nécessiter une évaluation pour l’App Store et de ne pas limiter le nombre d’appareils sur lesquels une application peut être installée. Toutefois, il est important de noter que les membres du **programme Developer Enterprise d’Apple** n’ont **pas** accès à iTunes Connect, et que le licencié est donc responsable de la distribution de l’application.

Pour plus d’informations sur la préparation et la distribution de votre application en interne, reportez-vous à la [guide de Distribution en interne](~/ios/deploy-test/app-distribution/in-house-distribution.md). Ce document est spécifique à iOS, mais les mêmes techniques sont utilisées pour les applications tvOS.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribution ad hoc

Xamarin.tvOS applications peuvent être testé par utilisateur via une distribution ad hoc, qui est disponible à la fois sur le **Apple Developer Program**et le **Apple Developer Enterprise Program**et autorise jusqu'à 100 appareils Apple TV à tester. Le meilleur cas d’usage pour la distribution ad hoc est la distribution au sein de votre entreprise quand iTunes Connect n’est pas une option.

Pour plus d’informations sur la préparation et la distribution de votre application en interne, reportez-vous à la [guide de Distribution Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Là encore, ce document est spécifique à iOS, mais les mêmes techniques sont utilisées pour les applications tvOS.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a fourni une brève présentation des mécanismes de distribution qui sont disponibles pour les applications Xamarin.tvOS. Il introduit le Store d’application Apple TV, Ad Hoc et interne déploiement et fourni des liens vers des informations plus détaillées.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Human Interface Guides](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’application pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
