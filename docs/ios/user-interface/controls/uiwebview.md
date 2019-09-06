---
title: Affichages Web dans Xamarin. iOS
description: Ce document décrit les différentes façons dont une application Xamarin. iOS peut afficher du contenu Web. Il aborde UIWebView, WKWebView, SFSafariViewController, Safari et la sécurité de transport d’application.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: e593a594bbf0fd6398c277d531258f6fded515f1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282545"
---
# <a name="web-views-in-xamarinios"></a>Affichages Web dans Xamarin. iOS

Au-delà de la durée de vie d’iOS Apple, les développeurs d’applications ont publié plusieurs manières d’incorporer les fonctionnalités d’affichage Web dans leurs applications. La plupart des utilisateurs utilisent le navigateur Web Safari intégré sur leur appareil iOS et, par conséquent, s’attendent à ce que les fonctionnalités d’affichage Web des autres applications soient cohérentes avec cette expérience. Ils s’attendent à ce que les mêmes gestes fonctionnent, les performances sur-par et les fonctionnalités de la même façon.

Dans cet article, nous allons explorer chacun des trois affichages Web fournis par Apple : `UIWebView`, `WKWebview`, et `SFSafariViewController`, leurs similitudes et différences, et comment ils peuvent être utilisés. 

iOS 11 a introduit de nouvelles modifications `WKWebView` à `SFSafariViewController`la fois dans et. Pour plus d’informations, consultez le Guide du [Guide des modifications Web dans iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="uiwebview"></a>UIWebView

`UIWebView`est l’approche héritée d’Apple pour fournir du contenu Web dans votre application. Elle a été publiée dans iOS 2,0 et a été dépréciée à compter du 8,0.

Si vous envisagez de prendre en charge les versions d’iOS antérieures à 8,0 `UIWebView`, vous devez utiliser. En raison du fait que `UIWebView` est moins optimisé pour les performances que les alternatives, il est recommandé de vérifier la version IOS de l’utilisateur. Si la version 8,0 ou ultérieure, l’utilisation de l’une des options expliquées ci-dessous permet de créer une meilleure expérience utilisateur.
 
Pour ajouter un UIWebView à votre application Xamarin. iOS, utilisez le code suivant :
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Cela génère l’affichage Web suivant :

[![](uiwebview-images/webview.png "L’effet de ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Pour plus d’informations sur `UIWebView`l’utilisation de, consultez les recettes suivantes :


- [Charger une page Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [Charger le contenu local](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [Charger des documents non Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView`a été introduit dans iOS 8, ce qui permet aux développeurs d’applications d’implémenter une interface de navigation Web similaire à celle de Mobile Safari. Cela est dû, en partie, au fait que utilise `WKWebView` le moteur JavaScript NITRO, le même moteur que celui utilisé par Mobile Safari. `WKWebView`doit toujours être utilisé sur UIWebView était possible en raison de l' [augmentation des performances](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), de l’intégration des gestes conviviaux et de la facilité d’interaction entre la page Web et votre application.
  
`WKWebView`peut être ajouté à votre application d’une manière presque identique à UIWebView, mais en tant que développeur, vous avez beaucoup plus de contrôle sur l’interface utilisateur/expérience utilisateur et les fonctionnalités. La création et l’affichage de l’objet de vue Web affichent la page demandée, mais vous pouvez contrôler la façon dont la vue est présentée, comment l’utilisateur peut naviguer et comment l’utilisateur quitte la vue.  

Vous pouvez utiliser le code ci-dessous pour `WKWebView` lancer une dans votre application Xamarin. iOS :

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Cela génère l’affichage Web suivant :

[![](uiwebview-images/wkwebview.png "Un exemple d’affichage Web sans ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

Il est important de noter que `WKWebView` se trouve dans l’espace de noms WebKit. vous devez donc ajouter cette directive using à la partie supérieure de votre classe.

`WKWebView`peut également être utilisé dans les applications Xamarin. Mac, et vous pouvez donc envisager de l’utiliser si vous créez une application Mac/iOS multiplateforme.

La recette [handle JavaScript Alerts](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) fournit également des informations sur l’utilisation de WKWebView avec JavaScript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController`est la dernière façon de fournir du contenu Web à partir de votre application et est disponible dans iOS 9 et versions ultérieures. Contrairement `UIWebView` à `WKWebView`ou ,`SFSafariViewController` est un contrôleur d’affichage et ne peut donc pas être utilisé avec d’autres vues. Vous devez présenter `SFSafariViewController` comme nouveau contrôleur d’affichage, de la même façon que vous le feriez pour n’importe quel contrôleur d’affichage.
 
 `SFSafariViewController`est essentiellement un « mini Safari » qui peut être incorporé dans votre application. À l’instar de WKWebView, il utilise le même moteur Nitro JavaScript, mais fournit également une gamme de fonctionnalités Safari supplémentaires telles que le remplissage automatique, le lecteur et la possibilité de partager des cookies et des données avec Mobile Safari. L’interaction entre l’utilisateur et `SFSafariViewController` le n’est pas accessible à votre application. Votre application n’aura pas accès aux fonctionnalités Safari par défaut.
 
Il implémente également par défaut un bouton **terminé** , ce qui permet à l’utilisateur de revenir facilement à votre application, ainsi qu’aux boutons de navigation suivant et précédent, ce qui permet à l’utilisateur de naviguer dans une pile de pages Web. En outre, il permet également à l’utilisateur de disposer d’une barre d’adresses qui lui donne la tranquillité d’esprit qu’il se trouve sur la page Web attendue. La barre d’adresses ne permet pas à l’utilisateur de modifier l’URL. 

Ces implémentations ne peuvent pas être modifiées `SFSafariViewController` . il est donc idéal de l’utiliser comme navigateur par défaut si votre application souhaite présenter une page Web sans aucune personnalisation.

Vous pouvez utiliser le code ci-dessous pour `SFSafariViewController` lancer une dans votre application Xamarin. iOS :

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Cela génère l’affichage Web suivant :

[![](uiwebview-images/sfsafariviewcontroller.png "Un exemple d’affichage Web avec SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Il est également possible d’ouvrir l’application Mobile Safari à partir de votre application, en utilisant le code ci-dessous :

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Cela génère l’affichage Web suivant :

[![](uiwebview-images/safari.png "Une page Web présentée dans Safari")](uiwebview-images/safari.png#lightbox)

La navigation entre les utilisateurs et votre application vers Safari doit généralement être évitée. La plupart des utilisateurs ne peuvent pas s’attendre à une navigation en dehors de votre application. par conséquent, si vous quittez votre application, les utilisateurs peuvent ne jamais les retourner, ce qui se traduit essentiellement par la suppression de l’engagement.

les améliorations iOS 9 permettent à l’utilisateur de revenir facilement à votre application à l’aide d’un bouton précédent, fourni dans l’angle supérieur gauche de la page Safari.

## <a name="app-transport-security"></a>Sécurité de transport de l’application

Application transport Security, ou *ATS* a été introduit par Apple dans iOS 9 pour s’assurer que toutes les communications Internet sont conformes aux meilleures pratiques en matière de connexion sécurisée.

Pour plus d’informations sur ATS, y compris son implémentation dans votre application, reportez-vous au guide sur la [sécurité d’App transport](~/ios/app-fundamentals/ats.md) .

## <a name="related-links"></a>Liens associés

- [Vues WebView (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
