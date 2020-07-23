---
title: Rechercher des API dans Xamarin. iOS
description: Cet article aborde l’utilisation des nouvelles API de recherche d’application fournies par iOS 9 pour permettre aux utilisateurs de rechercher des informations et des fonctionnalités à l’intérieur de vos applications Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: d95261a3b884507d3fc4a68018d57c5d3e3a2c84
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939410"
---
# <a name="search-apis-in-xamarinios"></a>Rechercher des API dans Xamarin. iOS

_Cet article traite de l’utilisation des API de recherche d’applications fournies par iOS 9 pour permettre aux utilisateurs de rechercher des informations et des fonctionnalités dans vos applications Xamarin. iOS._

La recherche a été développée dans iOS 9 pour offrir de nouvelles façons d’accéder à des informations et à des fonctionnalités à l’intérieur d’une application Xamarin. iOS. À l’aide des nouvelles API de recherche d’application, le contenu de l’application peut faire l’objet d’une recherche via les résultats de recherche Spotlight et Safari, les rappels et les suggestions de Siri et de remise. Cela permet aux utilisateurs d’accéder rapidement aux activités et aux informations contenues dans votre application.

En outre, les nouvelles API de recherche facilitent l’intégration de la recherche dans votre application sans une expérience d’implémentation de recherche antérieure. Pour cette raison, Apple prétend qu’il faut généralement quelques heures pour que le contenu d’une application iOS 9 fasse l’objet d’une recherche universelle à l’aide de la recherche d’applications.

[![Exemple de contenu d’application iOS 9 pouvant faire l’objet d’une recherche universelle à l’aide de la recherche d’application](images/intro01.png)](images/intro01.png#lightbox)

La recherche d’application est composée de trois API distinctes :

1. [**NSUserActivity**](nsuseractivity.md) : il s’agit d’une extension de l’API de transfert qu’Apple a publiée dans iOS 8. Il est utilisé pour permettre à l’utilisateur d’effectuer des recherches dans l’historique des interactions entre les applications à la fois publique et privée.

2. [**Core Spotlight**](corespotlight.md) : permet à une application d’indexer son contenu pour qu’elle soit présentée dans les résultats de recherche. Elle fonctionne comme une API de base de données où les éléments peuvent être ajoutés et supprimés et c’est la meilleure façon d’indexer le contenu privé dans une application.

3. [**Webmarkup**](web-markup.md) : pour les applications qui fournissent un accès à leur contenu via une interface Web (non seulement à partir de l’application). Le contenu Web peut être marqué avec des liens spéciaux qui seront analysés par Apple et fourniront une liaison étroite à votre application sur l’appareil iOS 9 de l’utilisateur.

## <a name="selecting-an-app-search-approach"></a>Sélection d’une approche de recherche d’application

Le choix de l’une des méthodes à implémenter dépend des types d’interaction fournis par votre application et du type de contenu qu’elle présente.

Respectez les recommandations suivantes :

- [**NSUserActivity**](nsuseractivity.md) : utilisez cette infrastructure pour fournir des options de recherche pour le contenu public et privé et pour la recherche des points de navigation au sein de votre application.

- [**Spotlight Core**](corespotlight.md) : utilisez ce Framework pour fournir des recherches sur les données privées stockées sur l’appareil.

- [**Balisage Web**](web-markup.md) : utilisez cette infrastructure pour fournir des recherches aux applications qui présentent leur contenu à l’intérieur de l’application, mais également à partir du site Web de l’application.

Chaque approche de recherche d’application est distincte et peut être utilisée individuellement, mais Apple les a conçues pour travailler ensemble. Lorsque vous utilisez plusieurs méthodes pour indexer un élément spécifique, veillez à utiliser le même **ID d’élément** sur chaque approche, afin que les liens individuels fonctionnent ensemble.

L’utilisation de plusieurs approches permet non seulement de garantir que votre contenu sera trouvé par l’utilisateur final, mais également d’améliorer le classement de votre élément à partir de la recherche.

Tandis que le processus de classement est surtout transparent pour le développeur, l’interaction de l’utilisateur avec un élément donné pèse fortement sur ce rang (par exemple, l’utilisateur touchant un lien).
En fournissant des éléments riches et informatifs, vous pouvez vous assurer qu’un utilisateur sera amené à interagir avec votre contenu, ce qui augmentera son classement.

## <a name="what-content-to-index"></a>Contenu à indexer

Apple fournit les suggestions suivantes quant au contenu et aux actions à fournir pour les index de recherche dans votre application :

- Tout contenu affiché, créé ou organisé par l’utilisateur dans votre application.
- Points de navigation et fonctionnalités au sein de l’application.
- Éléments tels que les nouveaux messages, le contenu ou d’autres types d’éléments affichés par votre application et récemment téléchargés sur l’appareil.

## <a name="app-search-enhancements"></a>Améliorations de la recherche d’applications

Core Spotlight dans iOS 10 offre plusieurs améliorations à la recherche d’applications, telles que :

- **Participatives popularité des liens approfondis (avec confidentialité différentielle)** : permet de promouvoir le contenu d’application lié de manière détaillée dans les résultats de recherche.
- **Recherche dans l’application** : utilisez la nouvelle `CSSearchQuery` classe pour fournir une fonctionnalité de recherche en vedette dans l’application similaire à la façon dont les applications de messagerie, de messages et notes fonctionnent.
- **Continuation** de la recherche : permet à un utilisateur de lancer une recherche dans Spotlight ou Safari, puis d’ouvrir une application et de poursuivre cette recherche.
- **Visualisation des résultats de la validation** : l’outil de validation de l' [API de recherche d’applications](https://search.developer.apple.com/appsearch-validation-tool) d’Apple affiche désormais une représentation visuelle du balisage d’un site Web et des liens détaillés lors de la préformation des tests.
- **Partage d’images d’application de message** : permet d’afficher les images courantes dans l’application fournies pour le partage des messages (via une extension de l’application de message) dans les recherches Spotlight.

Pour en savoir plus, consultez notre guide d’amélioration de la [recherche d’applications](~/ios/platform/search/app-search-enhancements.md) .

### <a name="proactive-suggestions"></a>Suggestions proactives

iOS 10 présente de nouvelles façons de conduire des engagements à une application en permettant au système de présenter de manière proactive des informations utiles à l’utilisateur à des moments opportuns. Tout comme iOS 9 offrait la possibilité d’ajouter une recherche poussée à l’application à l’aide de suggestions Spotlight, de remise et de Siri, avec iOS 10, une application peut exposer des fonctionnalités qui peuvent être présentées à l’utilisateur par le système depuis les emplacements suivants :

- Le sélecteur d’application
- L’écran de verrouillage
- CarPlay
- Cartes
- Interactions Siri
- Suggestions QuickType 

Une application expose cette fonctionnalité au système à l’aide d’une collection de technologies telles que [NSUserActivity](xref:Foundation.NSUserActivity), Web Markup, Core Spotlight, MapKit, Media Player et uikit.

Pour en savoir plus, consultez notre guide de [suggestions proactives](~/ios/platform/search/proactive-suggestions.md) .

## <a name="summary"></a>Résumé

Cet article a abordé les nouvelles fonctionnalités de l’API de recherche fournies par iOS 9 pour les applications Xamarin. iOS. Il a abordé les méthodes [NSUserActivity](nsuseractivity.md), [Spotlight Core](corespotlight.md) et [Web Markup](web-markup.md) pour l’indexation du contenu. Il s’est terminé avec une brève description de l’utilisation d’une approche de recherche donnée et des types de contenu qui doivent être indexés.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de programmation de recherche d’applications](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
