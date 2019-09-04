---
title: Améliorations de la recherche d’applications dans Xamarin. iOS
description: Cet article aborde les améliorations apportées par Apple à la recherche d’applications dans iOS 10 et comment les implémenter dans Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: ec7523ac2adc3a6b4ba18a7b8a0fe21749bd7856
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227409"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Améliorations de la recherche d’applications dans Xamarin. iOS

_Cet article aborde les améliorations apportées par Apple à la recherche d’applications dans iOS 10 et comment les implémenter dans Xamarin. iOS._

Dans iOS 10, Apple a apporté plusieurs améliorations à la recherche d’applications, telles que la liaison profonde participatives, la recherche dans l’application, la continuation de la recherche et la visualisation des résultats de la validation. Cet article aborde l’implémentation de ces fonctionnalités dans une application Xamarin. iOS.

## <a name="about-app-search-enhancements"></a>À propos des améliorations de la recherche d’applications

Core Spotlight dans iOS 10 offre plusieurs améliorations à la recherche d’applications, telles que:

- **Participatives popularité des liens approfondis (avec confidentialité différentielle)** : permet de promouvoir le contenu d’application lié de manière détaillée dans les résultats de recherche.
- **Recherche dans l’application** : utilisez la nouvelle `CSSearchQuery` classe pour fournir une fonctionnalité de recherche en vedette dans l’application similaire à la façon dont les applications de messagerie, de messages et notes fonctionnent.
- Continuation de la **recherche** : permet à un utilisateur de lancer une recherche dans Spotlight ou Safari, puis d’ouvrir une application et de poursuivre cette recherche.
- **Visualisation des résultats de la validation** : l’outil de validation de l' [API de recherche d’applications](https://search.developer.apple.com/appsearch-validation-tool) d’Apple affiche désormais une représentation visuelle du balisage d’un site Web et des liens détaillés lors de la préformation des tests.
- **Partage d’images d’application de message** : permet d’afficher les images courantes dans l’application fournies pour le partage des messages (via une extension de l’application de message) dans les recherches Spotlight.

Les sections suivantes aborderont ces sujets plus en détail.

## <a name="crowdsourced-deep-link-popularity"></a>Participatives de la popularité des liens détaillés

iOS 10 fournit un mécanisme permettant de compter la fréquence à laquelle les liens détaillés populaires dans une application sont suivis par l’utilisateur et qui utilise ces informations pour améliorer le classement du contenu d’une application dans les résultats de la recherche, tout en protégeant l’identité de l’utilisateur à l’aide de la fonctionnalité *différentielle Confidentialité*.

Pour les applications qui utilisent `NSUserActivity` des objets pour fournir des URL de lien profond et `EligibleForPublicIndexing` dont la propriété `true`a la valeur, iOS 10 soumet un sous-ensemble de hachages de *confidentialité différentielle* aux serveurs Apple. Ces informations sont ensuite utilisées pour promouvoir un contenu dans l’application populaire dans les résultats de recherche.

Pour plus d’informations sur l’implémentation de la liaison profonde dans une application Xamarin. iOS, consultez notre [recherche avec](~/ios/platform/search/nsuseractivity.md) la documentation NSUserActivity.

## <a name="in-app-searching"></a>Recherche dans l’application

En implémentant la nouvelle classe [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) , une application peut fournir une technologie de recherche et de correspondance de mise en vedette pour rechercher du contenu à l’intérieur de lui-même, sans que l’utilisateur n’ait à laisser l’application (semblable à la façon dont l’application mail, les messages et les notes fonctionnent).

En règle générale, les `CSSearchQuery` applications qui prennent en charge n’ont pas besoin de conserver leur propre index de recherche distinct.

## <a name="search-continuation"></a>Continuation de recherche

Dans iOS 9, Apple a introduit les API de recherche (telles que Core `NSUserActivity` Spotlight et le balisage Web) pour fournir des informations détaillées sur le contenu d’une application, afin de permettre aux utilisateurs de Rechercher ce contenu à l’aide des interfaces de recherche Spotlight et Safari. Pour plus d’informations, consultez la documentation de notre [nouvelle API de recherche](~/ios/platform/search/index.md) .

Dans iOS 10, Apple s’appuie sur cette fonctionnalité en permettant à l’utilisateur de lancer une recherche dans Spotlight ou Safari, puis de continuer la recherche lors de l’ouverture d’une application.

Pour implémenter cette fonctionnalité, modifiez le fichier `Info.plist` de l’application, `CoreSpotlightContinuation` ajoutez la clé de type **booléen** et définissez sa `YES`valeur sur:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Modification de CoreSpotlightContinuation dans le fichier info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Modification de CoreSpotlightContinuation dans le fichier info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Pour répondre à l’utilisateur qui continue un résultat de`NSUserActivity`recherche (), `AppDelegate.cs` modifiez le fichier et remplacez `ContinueUserActivity` la méthode. Par exemple :

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

Ce code recherche le type d’action de continuation`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`de requête (), puis lit la requête actuelle de `NSUserActivity` l’utilisateur à partir du dictionnaire`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`d’informations utilisateur de la classe (). À partir de là, l’application doit prendre des mesures pour poursuivre la recherche de l’utilisateur.

Pour plus d’informations sur l’utilisation des recherches dans une application Xamarin. iOS, consultez notre page [de recherche avec](~/ios/platform/search/corespotlight.md) la documentation de base Spotlight.

## <a name="visualization-of-validation-results"></a>Visualisation des résultats de la validation

L’outil de validation de l' [API de recherche d’applications](https://search.developer.apple.com/appsearch-validation-tool) d’Apple affiche désormais une représentation visuelle du balisage d’un site Web et de la liaison profonde (y compris les balises telles que définies dans [Schema.org](http://schema.org/)) lors de la préformation des tests.

À l’aide de l’outil de validation, un développeur peut voir les informations indexées par le robot Web Applebot pour le site, telles que le titre, la description, l’URL et tout autre élément pris en charge.

Pour plus d’informations sur l’utilisation de la balise Web, consultez notre documentation [Rechercher avec le balisage Web](~/ios/platform/search/web-markup.md) .

## <a name="message-app-image-sharing"></a>Message de partage d’images d’application

Si une extension d’application de message fournit des images à partager dans des messages, l’extension peut être configurée pour permettre à l’utilisateur d’effectuer des recherches Spotlight pour les images populaires à partir des messages, sans avoir à quitter l’application.

Pour activer cette fonctionnalité, procédez comme suit:

1. Créer une extension d’application de message.
2. Ajoutez le `com.apple.developer.associated-domains` aux droits de l’application et incluez une liste de domaines Web qui hébergent les images que l’extension d’application de message partage. Pour chaque domaine, spécifiez `spotlight-image-search` le service.
3. Ajoutez un `apple-app-site-association` fichier au site Web qui héberge les images. Ce fichier comprend un dictionnaire pour le `spotlight-image-search` service et comprend l’ID de l’application, qui est l’ID d’équipe ou le préfixe d’ID d’application suivi de l’ID d’offre groupée. Le fichier peut contenir jusqu’à 500 chemins d’accès et des modèles qui seront indexés par Spotlight et inclus dans les recherches d’images populaires. Pour plus d’informations, consultez la documentation relative à [la création et au chargement du fichier d’association](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) d’Apple.
4. Autorisez Applebot à analyser les sites Web. Consultez la documentation d’Apple [sur Applebot](https://support.apple.com/HT204683) .

Pour plus d’informations, consultez la documentation relative à l' [intégration des applications de messagerie](~/ios/platform/message-app-integration/index.md) .

## <a name="summary"></a>Récapitulatif

Cet article a abordé les améliorations apportées par Apple à la recherche d’applications dans iOS 10 et comment les implémenter dans Xamarin. iOS.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
