---
title: Recherche avec le balisage Web dans Xamarin.iOS
description: Ce document décrit comment créer des résultats de recherche basée sur le web, un lien vers une application Xamarin.iOS. Il explique comment permettre à du contenu web l’indexation, rendre le site Web de votre application détectable, à l’aide de bannières actives, des liens universels et bien plus encore.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: a9cf3dab9c112bf7ff99cbc0dd9541c3c1e35142
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830141"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Recherche avec le balisage Web dans Xamarin.iOS

Pour les applications qui fournissent l’accès à leur contenu via un site web (pas uniquement à partir de l’application), contenu web peut être marqué avec liens spéciaux qui seront analysés par Apple et de fournissent un lien profond à votre application sur son appareil iOS 9.

Si votre application iOS prend déjà en charge le lien profond mobile et votre site Web affiche des liens ciblés vers le contenu au sein de votre application, Apple _Applebot_ robot d’indexation ce contenu d’index et les ajouter automatiquement à leur Index Cloud :

[![](web-markup-images/webmarkup01.png "Vue d’ensemble des Index de cloud")](web-markup-images/webmarkup01.png#lightbox)

Apple affichera ces résultats dans les résultats de la recherche Spotlight et recherche de Safari.
Si l’utilisateur appuie sur un de ces résultats (et qu’ils ont installé votre application), puis il est dirigé vers le contenu dans votre application :

[![](web-markup-images/webmarkup02.png "Lien à partir d’un site Web dans les résultats de la recherche profond")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>L’activation de l’indexation de contenu Web

Il existe quatre étapes requises pour que vous contenu l’application consultable à l’aide de balisage Web :

1. Assurez-vous que Apple permettre découvrir et d’index du site Web de votre application en la définissant en tant que le **prise en charge** ou **Marketing** site Web dans iTunes Connect.
2. Assurez-vous que le site Web de votre application contient le balisage requis pour implémenter le lien profond mobile. Consultez les sections ci-dessous pour plus d’informations.
3. Activer le lien ciblé de gestion dans votre application iOS.
4. Ajouter le balisage pour les données structurées exposés par le site Web de votre application pour fournir un résultat de riche et attrayant à l’utilisateur final. Cette étape n’est pas strictement obligatoire, il est vivement recommandé par Apple.

Les sections suivantes passe en revue ces étapes en détail.

## <a name="make-your-apps-website-discoverable"></a>Rendre le site Web de votre application détectable

Le moyen le plus simple d’avoir à Apple de trouver le site Web de votre application consiste à utiliser en tant que le **prise en charge** ou **Marketing** site Web lorsque vous soumettez votre application à Apple via iTunes Connect.

## <a name="using-smart-app-banners"></a>À l’aide de bannières intelligente

Fournir une bannière d’application actives sur votre site Web pour présenter un lien clair dans votre application. Si l’application n’est pas déjà installée, Safari vous invite automatiquement l’utilisateur à installer votre application. Sinon, l’utilisation peut appuyer sur le **vue** lien pour lancer votre application depuis le site Web. Par exemple, pour créer une bannière d’application intelligente, vous pouvez utiliser le code suivant :

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Pour plus d’informations, consultez le site d’Apple [la promotion des applications avec Smart bannières](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentation.

## <a name="using-universal-links"></a>À l’aide de liens universels

Nouveau à iOS 9, des liens universels fournissent une meilleure alternative à bannières actives ou les schémas d’URL personnalisés existants en fournissant les éléments suivants :

- **Unique** – la même URL ne peut pas être revendiquée par plus d’un site Web.
- **Sécuriser** – un certificat signé est requis pour le site Web qui garantit que le site Web est détenu par vous et correctement lié à la votre application.
- **Flexible** – l’utilisateur final peut contrôler si l’URL lance le site Web ou l’application.
- **Universal** – la même URL peut être utilisée pour définir le contenu de votre site Web et de votre application.

## <a name="using-twitter-cards"></a>À l’aide de cartes de Twitter

Vous pouvez fournir des liens ciblés vers le contenu de votre application à l’aide d’une carte de Twitter. Par exemple :

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Pour plus d’informations, consultez le site de Twitter [protocole de carte Twitter](http://dev.twitter.com/cards/mobile) documentation.

## <a name="using-facebook-app-links"></a>À l’aide de liens d’application Facebook

Vous pouvez fournir des liens ciblés vers le contenu de votre application à l’aide d’un Facebook application Link. Par exemple :

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Pour plus d’informations, consultez le site de Facebook [liens application](http://applinks.org) documentation.

## <a name="opening-deep-links"></a>Ouverture des liens ciblés

Vous devez ajouter la prise en charge pour l’ouverture et l’affichage des liens ciblés dans votre application Xamarin.iOS. Modifier le **AppDelegate.cs** de fichiers et de remplacer le `OpenURL` méthode pour gérer le format d’URL personnalisé. Par exemple :

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

    // Handling a URL in the form http://company.com/appname/?123
    try {
        var components = new NSUrlComponents(url,true);
        var path = components.Path;
        var query = components.Query;

        // Is this a known format?
        if (path == "/appname") {
            // Display the view controller for the content
            // specified in query (123)
            return ContentViewController.LoadContent(query);
        }
    } catch {
        // Ignore issue for now
    }

    return false;
}
```

Dans le code ci-dessus, nous recherchons une URL contenant `/appname` et en passant la valeur de `query` (`123` dans cet exemple) à un contrôleur d’affichage personnalisé dans notre application pour afficher le contenu demandé à l’utilisateur.

## <a name="providing-rich-results-with-structured-data"></a>En fournissant des résultats riches avec des données structurées

En incluant le balisage de données structurées, vous pouvez fournir des résultats de recherche riche pour l’utilisateur final qui vont au-delà de tout simplement un titre et une description. Inclure des images, des données spécifiques d’application (par exemple, des évaluations) et des actions pour les résultats à l’aide de la balise structuré de données.

Résultats de riches sont plus intéressantes et peuvent aider à améliorer les Index de recherche de base de votre classement dans le Cloud en incitant davantage d’utilisateurs d’interagir avec elles.

Une option permettant de balise structuré de données consiste à l’aide d’Open Graph. Par exemple :

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Pour plus d’informations, consultez le [Open Graph](http://ogp.me) site Web.

Un autre courantes format de balise structuré de données est de schema.org microdonnées. Par exemple :

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

Les mêmes informations peuvent être représentées au format de JSON-LD de schema.org :

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

Voici un exemple de métadonnées à partir de votre site Web en fournissant des résultats de recherche riche pour l’utilisateur final :

[![](web-markup-images/deeplink01.png "Résultats par le biais de balise structuré de données de recherche avancée")](web-markup-images/deeplink01.png#lightbox)

Apple prend actuellement en charge les types de schéma suivants à partir de schema.org :

- AggregateRating
- ImageObject
- InteractionCount
- Offres
- Organisation
- PriceRange
- Recipe (Recette)
- SearchAction

Pour plus d’informations sur ces types de schéma, consultez [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fournissant des Actions avec des données structurées

Les types spécifiques de données structurées permettra un résultat de recherche pouvoir être exploitable par l’utilisateur final. Actuellement, les actions suivantes sont prises en charge :

- Composer un numéro de téléphone.
- Obtention de direction de mappage à une adresse donnée.
- Lecture d’un fichier audio ou vidéo.

Par exemple, la définition d’une Action pour composer un numéro de téléphone peut se présenter comme suit :

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Lorsque ce résultat de recherche est présenté à l’utilisateur final, une icône de téléphone petit s’affichera dans le résultat. Si l’utilisateur appuie sur l’icône, le nombre spécifié sera appelé.

Ajoutez une action pour lire un fichier audio à partir du résultat de recherche le code HTML suivant :

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Enfin, le code HTML suivant ajoutez une action pour obtenir des instructions à partir du résultat de recherche :

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Pour plus d’informations, consultez le site d’Apple [Site de développement application recherche](https://developer.apple.com/ios/search/).



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de programmation de recherche de l’application](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
