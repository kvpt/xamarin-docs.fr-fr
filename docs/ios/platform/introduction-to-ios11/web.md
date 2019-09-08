---
title: Modifications apportées à WebKit et Safari dans iOS 11
description: Ce document traite des modifications apportées à WebKit et à l’infrastructure des services Safari dans iOS 11. Il décrit comment utiliser des mises à jour de styles dans SFSafariViewController et de nouvelles fonctionnalités dans WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/12/2017
ms.openlocfilehash: 6068dd148bfc3c2a778ca34753374bcecccb55d9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752214"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>Modifications apportées à WebKit et Safari dans iOS 11

iOS 11 introduit une nouvelle version du navigateur Web Safari – Safari 11,0, qui inclut les modifications apportées à WebKit et SafariServices. Ce guide explore ces modifications.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController`a été introduit dans iOS 9 en tant qu’option permettant d’afficher du contenu Web ou d’authentifier des utilisateurs à partir de votre application. Vous trouverez plus d’informations sur ses fonctionnalités dans le guide des [vues Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) .

iOS 11 a introduit des mises à jour de style pour le contrôleur d’affichage Safari, ce qui offre à vos utilisateurs une expérience plus transparente entre une application et le Web. Par exemple, la suppression de la barre d’adresses donne désormais au contrôleur d’affichage Safari le sentiment d’un navigateur dans l’application, plutôt que d’un mini-navigateur. Vous pouvez également personnaliser le modèle de couleurs en fonction du modèle de couleurs de votre application en définissant `preferredBarTintColor` les `PreferredControlTintColor` propriétés et :

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

L’extrait de code suivant restitue les barres en violet et en blanc, comme indiqué dans l’image suivante :

![Barres SFSafariViewController rendues en violet et blanc](web-images/image1.png)

Le bouton ignorer présenté dans le contrôleur d’affichage Safari peut également être modifié en affectant `DismissButtonStyle` `Done`à la propriété la valeur `Cancel`, `Close`ou :

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignorer le texte du bouton modifié](web-images/image2.png)

Cette valeur peut être modifiée pendant `SFSafariViewController` la présentation de.

En fonction du contenu affiché à l’intérieur d’un contrôleur d’affichage Safari, il peut être nécessaire de s’assurer que les barres de menus ne se réduisent pas lorsque l’utilisateur fait défiler. Cela est possible en affectant à `BarCollapsedEnabled` `false`la nouvelle propriété la valeur :

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Réduction de la barre désactivée](web-images/image3.png)

Apple a également effectué des mises à jour de la confidentialité dans le contrôleur d’affichage Safari dans iOS 11. À présent, la consultation des données telles que les cookies et le stockage local n’existe que pour chaque application, plutôt que sur toutes les instances du contrôleur de vue Safari. Cela permet de préserver la confidentialité de l’activité de l’utilisateur dans votre application.

Des fonctionnalités supplémentaires telles que la prise en charge du glisser-déplacer `window.open()` pour les URL et la `SFSafariViewController` prise en charge de ont également été ajoutées à dans iOS 11. Vous trouverez plus d’informations sur ces nouvelles fonctionnalités dans [la documentation SFSafariViewController d’Apple](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).

## <a name="webkit"></a>WebKit

`WKWebView`a été introduit dans le cadre de WebKit dans iOS 8 comme un moyen d’afficher du contenu Web pour votre utilisateur. Il est bien plus personnalisable que `SFSafariViewController`, ce qui vous permet de créer votre propre interface utilisateur et de navigation.

Apple a introduit trois améliorations principales pour `WKWebView` iOS 11 : 

- La possibilité de gérer les cookies
- Filtrage du contenu
- Chargement personnalisé des ressources. 

La gestion des cookies s’effectue par [`WKHttpCookieStore`](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) le biais de la nouvelle classe, qui vous permet d’ajouter et de supprimer des cookies, d’obtenir tous les cookies stockés dans un WKWebView et d’observer les modifications dans le magasin de cookies.

Le filtrage de contenu vous permet de gérer le type de contenu que votre utilisateur verra, ce qui vous permet de vous assurer qu’il est sécurisé, compatible avec la famille et, le cas échéant, disponible uniquement pour un groupe sélectionné d’utilisateurs. Elle est implémentée par le [`WKContentRuleList`](https://developer.apple.com/documentation/webkit/wkcontentrulelist) biais de la nouvelle classe, en fournissant des paires de déclencheurs et d’actions dans JSON. Vous trouverez plus d’informations sur ces déclencheurs et ces actions dans le guide des [règles de blocage de contenu](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) d’Apple.

iOS 11 vous permet désormais de personnaliser `WKWebView` le chargement de ressources personnalisées pour votre contenu Web. Elle est implémentée via `IWKUrlSchemeHandler` l’interface, qui vous permet de gérer les schémas d’URL qui ne sont pas natifs du Kit Web. Cette interface a une méthode Start et Stop qui doit être implémentée :

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

Une fois que le gestionnaire a été implémenté, utilisez-le `SetUrlSchemeHandler` pour définir la `WKWebViewConfiguration`propriété sur le. Ensuite, chargez l’URL d’un qui utilise le schéma personnalisé :

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```
