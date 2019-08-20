---
title: Rechercher avec le balisage Web dans Xamarin. iOS
description: Ce document décrit comment créer des résultats de recherche basés sur le Web qui renvoient à une application Xamarin. iOS. Il explique comment activer l’indexation de contenu Web, rendre le site Web de votre application détectable, à l’aide de bannières d’applications intelligentes, de liens universels et bien plus encore.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 77d526fd49ac62788bea1ab885cb1248ffc5697e
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620956"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Rechercher avec le balisage Web dans Xamarin. iOS

Pour les applications qui fournissent un accès à leur contenu via un site Web (non seulement à partir de l’application), le contenu Web peut être marqué avec des liens spéciaux qui seront analysés par Apple et fournir une liaison étroite à votre application sur l’appareil iOS 9 de l’utilisateur.

Si votre application iOS prend déjà en charge la liaison profonde mobile et que votre site Web a présenté des liens ciblés vers du contenu au sein de votre application, le robot Web _Applebot_ d’Apple indexera ce contenu et l’ajoutera automatiquement à son index Cloud:

[![](web-markup-images/webmarkup01.png "Présentation de l’index Cloud")](web-markup-images/webmarkup01.png#lightbox)

Apple affiche ces résultats dans la recherche Spotlight et les résultats de recherche Safari.
Si l’utilisateur clique sur l’un de ces résultats (et que votre application est installée), il est dirigé vers le contenu de votre application:

[![](web-markup-images/webmarkup02.png "Liaison profonde à partir d’un site Web dans les résultats de recherche")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Activation de l’indexation de contenu Web

Quatre étapes sont nécessaires pour faciliter la recherche de contenu d’application à l’aide du balisage Web:

1. Assurez-vous qu’Apple peut découvrir et indexer le site Web de votre application en le définissant comme site Web de **support** ou de **marketing** dans iTunes Connect.
2. Assurez-vous que le site Web de votre application contient le balisage requis pour implémenter la liaison profonde mobile. Pour plus d’informations, consultez les sections ci-dessous.
3. Activez la gestion des liens approfondis dans votre application iOS.
4. Ajoutez des balises pour les données structurées, exposées par le site Web de votre application, afin de fournir un résultat riche et attrayant à l’utilisateur final. Bien que cette étape ne soit pas strictement obligatoire, elle est fortement recommandée par Apple.

Les sections suivantes décrivent ces étapes en détail.

## <a name="make-your-apps-website-discoverable"></a>Rendre le site Web de votre application détectable

Le moyen le plus simple pour que Apple recherche le site Web de votre application consiste à l’utiliser comme site Web de **support** ou de **marketing** lorsque vous envoyez votre application à Apple via iTunes Connect.

## <a name="using-smart-app-banners"></a>Utilisation des bannières d’application intelligente

Fournissez une bannière d’application intelligente sur votre site Web pour présenter un lien clair dans votre application. Si l’application n’est pas déjà installée, Safari invite automatiquement l’utilisateur à installer votre application. Dans le cas contraire, l’utilisation peut appuyer sur le lien de la **vue** pour lancer votre application à partir du site Web. Par exemple, pour créer une bannière d’application intelligente, vous pouvez utiliser le code suivant:

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Pour plus d’informations, consultez la documentation relative [à la promotion des applications d’Apple avec les bannières d’application intelligentes](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) .

## <a name="using-universal-links"></a>Utilisation des liens universels

Nouveauté d’iOS 9, les liens universels fournissent une meilleure alternative aux bannières d’application intelligentes ou aux modèles d’URL personnalisés existants en fournissant les éléments suivants:

- **Unique** : la même URL ne peut pas être revendiquée par plusieurs sites Web.
- **Sécurisé** : un certificat signé est requis pour le site Web, ce qui garantit que le site Web vous appartient et qu’il est lié de manière valide à votre application.
- **Flexible** : l’utilisateur final peut contrôler si l’URL lance le site Web ou l’application.
- **Universel** : la même URL peut être utilisée pour définir le contenu de votre site Web et le contenu de votre application.

## <a name="using-twitter-cards"></a>Utilisation des cartes Twitter

Vous pouvez fournir des liens détaillés vers le contenu de votre application à l’aide d’une carte Twitter. Par exemple :

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Pour plus d’informations, consultez la documentation sur le [protocole de carte Twitter](http://dev.twitter.com/cards/mobile) de Twitter.

## <a name="using-facebook-app-links"></a>Utilisation des liens d’application Facebook

Vous pouvez fournir des liens détaillés vers le contenu de votre application à l’aide d’un lien vers une application Facebook. Par exemple :

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Pour plus d’informations, consultez la documentation [des liens vers les applications](http://applinks.org) de Facebook.

## <a name="opening-deep-links"></a>Ouverture de liens détaillés

Vous devez ajouter la prise en charge de l’ouverture et de l’affichage des liens détaillés dans votre application Xamarin. iOS. Modifiez le fichier **AppDelegate.cs** et remplacez la `OpenURL` méthode pour gérer le format d’URL personnalisé. Par exemple :

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

Dans le code ci-dessus, nous recherchons une `/appname` URL qui contient et transmettant`123` la valeur de `query` (dans cet exemple) à un contrôleur d’affichage personnalisé dans notre application pour afficher le contenu demandé à l’utilisateur.

## <a name="providing-rich-results-with-structured-data"></a>Fournir des résultats enrichis avec des données structurées

En incluant le balisage de données structurées, vous pouvez fournir à l’utilisateur final des résultats de recherche enrichis qui dépassent simplement un titre et une description. Inclure des images, des données spécifiques à l’application (telles que des évaluations) et des actions pour les résultats à l’aide du balisage de données structurées.

Les résultats enrichis sont plus attrayants et peuvent contribuer à améliorer votre classement dans l’index de recherche basé sur le Cloud en attirant davantage d’utilisateurs pour interagir avec eux.

Une option pour fournir le balisage de données structurées consiste à utiliser ouvrir un graphique. Par exemple :

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Pour plus d’informations, consultez le site Web [Open Graph](http://ogp.me) .

Un autre format courant pour le balisage de données structurées est le format microdonnées de Schema. org. Par exemple :

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

Les mêmes informations peuvent être représentées au format JSON-LD de Schema. org:

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

Voici un exemple de métadonnées de votre site Web fournissant des résultats de recherche enrichis à l’utilisateur final:

[![](web-markup-images/deeplink01.png "Résultats de recherche enrichis via le balisage de données structurées")](web-markup-images/deeplink01.png#lightbox)

Apple prend actuellement en charge les types de schémas suivants à partir de schema.org:

- AggregateRating
- ImageObject
- InteractionCount
- Offre
- Dernière
- Gammedeprix
- Recette
- SearchAction

Pour plus d’informations sur ces types de schéma, consultez [Schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fournir des actions avec des données structurées

Des types spécifiques de données structurées permettent à l’utilisateur final d’agir sur les résultats de la recherche. Actuellement, les actions suivantes sont prises en charge:

- Numérotation d’un numéro de téléphone.
- Obtention de la direction de la carte vers une adresse donnée.
- Lecture d’un fichier audio ou vidéo.

Par exemple, la définition d’une action pour composer un numéro de téléphone peut se présenter comme suit:

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

Lorsque ce résultat de recherche est présenté à l’utilisateur final, une petite icône de téléphone s’affiche dans le résultat. Si l’utilisateur appuie sur l’icône, le nombre spécifié est appelé.

Le code HTML suivant ajoute une action pour lire un fichier audio à partir du résultat de la recherche:

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

Enfin, le code HTML suivant ajoute une action pour obtenir des instructions à partir du résultat de la recherche:

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

Pour plus d’informations, consultez le site des développeurs d’Apple [app Search](https://developer.apple.com/ios/search/).



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de programmation de recherche d’applications](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
