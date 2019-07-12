---
title: API de recherche dans Xamarin.iOS
description: Cet article traite les nouvelles API de recherche d’application fourni par iOS 9 à autoriser les utilisateurs à rechercher des informations et des fonctionnalités à l’intérieur de vos applications Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 87f7689a082c891bc199f4221e68ed7ab19adf85
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832709"
---
# <a name="search-apis-in-xamarinios"></a>API de recherche dans Xamarin.iOS

_Cet article couvre l’utilisation de l’API de recherche d’application fourni par iOS 9 permettant aux utilisateurs de rechercher des informations et des fonctionnalités à l’intérieur de vos applications Xamarin.iOS._

Recherche a été développée dans iOS 9 pour fournir de nouvelles façons d’accéder aux informations et des fonctionnalités à l’intérieur d’une application Xamarin.iOS. En utilisant les nouvelles API de recherche d’application, contenu de l’application est effectuée recherche Spotlight et Safari résultats de recherche, remise et les rappels de Siri et les Suggestions. Cela permet aux utilisateurs d’accéder rapidement aux activités et informations approfondies au sein de votre application.

En outre, les nouvelles API de recherche facilitent l’intégrez la recherche dans votre application sans expérience de mise en œuvre de recherche précédents. Pour cette raison, Apple revendications qu’il prend généralement quelques heures pour rendre le contenu d’une application iOS 9 universellement consultable à l’aide de la recherche de l’application.

[![](images/intro01.png "Un exemple de contenu de l’application iOS 9 universellement consultable à l’aide de la recherche de l’application")](images/intro01.png#lightbox)

Recherche de l’application se compose de trois API séparées :

1. [**NSUserActivity** ](nsuseractivity.md) -il s’agit d’une extension de l’API de remise Apple publié dans iOS 8. Il est utilisé pour faciliter la recherche des historique des interactions d’application à la fois publiques et privées) par l’utilisateur.

2. [**Spotlight de base** ](corespotlight.md) -permet à une application à son contenu devant être présenté dans les résultats de la recherche d’index. Il fonctionne comme une API où les éléments peuvent être ajoutées ou supprimées et c’est le meilleur moyen pour indexer le contenu privé au sein d’une application de base de données.

3. [**WebMarkup** ](web-markup.md) : pour les applications qui fournissent l’accès à leur contenu via une interface web (pas uniquement à partir de l’application). Contenu Web peut être marqué avec liens spéciaux qui seront analysés par Apple et de fournissent un lien profond à votre application sur son appareil iOS 9.

## <a name="selecting-an-app-search-approach"></a>Choix d’une méthode de recherche de l’application

Décider laquelle de ces méthodes à implémenter varie sur les types d’interaction fournies par votre application et le type de contenu qu'il présente.

Utilisez les instructions suivantes :

- [**NSUserActivity** ](nsuseractivity.md) – cette infrastructure permet d’offrir des possibilités de recherche de contenu public et privé et également les possibilités de recherche de points de navigation au sein de votre application.

- [**Spotlight de base** ](corespotlight.md) – cette infrastructure permet d’offrir des possibilités de recherche pour les données confidentielles stockées sur l’appareil.

- [**Web balisage** ](web-markup.md) – cette infrastructure permet d’offrir des possibilités de recherche pour les applications qui présentent leur contenu non seulement à partir de l’application, mais à partir du site Web de l’application ainsi.

Chacun de la recherche de l’application approches sont distincts et peuvent être utilisés individuellement, toutefois Apple conçus pour fonctionner ensemble. Lorsque vous utilisez plusieurs approches pour indexer un élément spécifique, assurez-vous d’utiliser le même **ID d’élément** sur chaque approche, par conséquent, cette personne fonctionnent les liens entre eux.

À l’aide de plusieurs approches non seulement permet de s’assurer que votre contenu est accessible par l’utilisateur final, mais contribue également à améliorer le classement de votre élément de recherche.

Tandis que le processus de classement dans essentiellement transparente pour le développeur, l’interaction utilisateur avec un élément donné pèse fortement à ce classement (par exemple l’utilisateur touchant un lien).
En fournissant les éléments riches et informatives, vous pouvez vous assurer qu’un utilisateur s’est conduit pour interagir avec votre contenu, élève donc son classement.

## <a name="what-content-to-index"></a>Le contenu à l’Index

Apple propose les suggestions suivantes concernant le contenu et des actions pour fournir des index de recherche pour dans votre application :

- Tout contenu affiché, créé ou organisé par l’utilisateur à partir de votre application.
- Points de navigation et les fonctionnalités de l’application.
- Choses comme les nouveaux messages, de contenu ou d’autres types d’éléments affichés par votre application et qui ont récemment été téléchargées vers l’appareil.

## <a name="app-search-enhancements"></a>Améliorations de la recherche d’applications

Spotlight de base dans iOS 10 apporte plusieurs améliorations à la recherche d’application telles que :

- **Popularité de lien ciblé participatives (avec privacy différentielle)** -offre un moyen pour promouvoir le contenu de l’application lié à la profondeur dans les résultats de la recherche.
- **Dans l’application recherche** -utiliser le nouveau `CSSearchQuery` classe pour fournir la possibilité de recherche Spotlight dans l’application similaire au fonctionnement des applications de messagerie, les Messages et les Notes.
- **Rechercher la Continuation** - permet à un utilisateur de lancer une recherche dans Spotlight ou Safari, puis ouvrez une application et continuer cette recherche.
- **Visualisation des résultats de la Validation** -Apple [outil de Validation API application recherche](https://search.developer.apple.com/appsearch-validation-tool) affiche maintenant une représentation visuelle du balisage d’un site Web et lien profond lors de la réalisation de tests.
- **Message d’application partage d’images** -permet des images populaires dans l’application fournies pour le partage dans les Messages (via une Extension d’application Message) doit s’afficher dans les recherches d’actualités.

Pour en savoir plus, consultez notre [les améliorations de recherche application](~/ios/platform/search/app-search-enhancements.md) guide.

### <a name="proactive-suggestions"></a>Suggestions proactives

iOS 10 présente les nouvelles façons d’engagement de conduite à une application en autorisant le système de manière proactive présenter des informations utiles automatiquement à l’utilisateur aux moments opportuns. Tout comme iOS 9 fourni la possibilité d’ajouter une recherche approfondie à l’application à l’aide de Spotlight, remise et des Suggestions de Siri, avec iOS 10, qu'une application peut exposer des fonctionnalités qui peuvent être présentées à l’utilisateur par le système à partir des emplacements suivants :

- Le sélecteur d’application
- L’écran de verrouillage
- CarPlay
- Cartes
- Interactions de Siri
- Suggestions de QuickType 

Une application expose cette fonctionnalité dans le système à l’aide d’une collection de technologies telles que [NSUserActivity](xref:Foundation.NSUserActivity), balisage web, Spotlight de base, MapKit, Media Player et UIKit.

Pour en savoir plus, consultez notre [Suggestions proactives](~/ios/platform/search/proactive-suggestions.md) guide.

## <a name="summary"></a>Récapitulatif

Cet article a présenté les nouvelles fonctionnalités de l’API de recherche qu’iOS 9 fournit pour les applications Xamarin.iOS. Couvert [NSUserActivity](nsuseractivity.md), [Spotlight de base](corespotlight.md) et [balisage Web](web-markup.md) méthodes pour l’indexation de contenu. Il est terminé avec une brève présentation des quand une approche de recherche donnée doit être utilisée et les types de contenu doivent être indexée.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de programmation de recherche de l’application](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
