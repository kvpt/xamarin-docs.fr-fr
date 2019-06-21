---
title: Modifications WebKit et Safari dans iOS 11
description: Ce document décrit les modifications apportées au WebKit et l’infrastructure des Services de Safari dans iOS 11. Il décrit comment travailler avec du style des mises à jour dans SFSafariViewController et les nouvelles fonctionnalités dans WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/12/2017
ms.openlocfilehash: 5ced73b1f3f5b8207ae1258dcb01a78c94df217d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268917"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>Modifications WebKit et Safari dans iOS 11

iOS 11 introduit une nouvelle version du navigateur web Safari – Safari 11.0 – qui inclut les modifications apportées aux WebKit et SafariServices. Ce guide explore ces modifications.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` a été introduit dans iOS 9 en tant qu’option d’affichage du contenu web ou l’authentification des utilisateurs à partir de votre application. Vous trouverez plus d’informations sur ses fonctionnalités dans le [vues Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) guide.

iOS 11 a introduit les mises à jour de style pour le contrôleur d’affichage Safari, ce qui donne à vos utilisateurs une expérience plus transparente entre une application et le web. Par exemple, la suppression de l’adresse de la barre indique maintenant le contrôleur d’affichage l’impression d’un navigateur dans l’application, plutôt que d’un mini-navigateur Safari. Vous pouvez également personnaliser le jeu de couleurs s’inscrivent-ils dans le jeu de couleurs de votre application en définissant le `preferredBarTintColor` et `PreferredControlTintColor` propriétés :

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

L’extrait de code suivant affiche les barres en violet et blanc, tel qu’affiché dans l’image suivante :

![Barres SFSafariViewController rendues en violet et blanc](web-images/image1.png)

Le bouton Ignorer présenté dans le contrôleur d’affichage Safari peut également être modifié en définissant le `DismissButtonStyle` propriété `Done`, `Close`, ou `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignorer le texte du bouton modifié](web-images/image2.png)

Cette valeur peut être modifiée pendant que `SFSafariViewController` est présentée.


En fonction du contenu qui est affiché à l’intérieur d’un contrôleur d’affichage Safari, il peut être nécessaire pour garantir que les barres de menus ne réduire en tant que l’utilisateur fait défiler. Cette option est activée en définissant la nouvelle `BarCollapsedEnabled` propriété `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barre réduction désactivé](web-images/image3.png)

Apple a également apporté des mises à jour à la confidentialité dans le contrôleur d’affichage Safari dans iOS 11. Désormais, les données de navigation telles que les cookies et le stockage local n’existent que sur une base par application, plutôt que sur toutes les instances de contrôleur d’affichage de Safari. Activité de navigation utilisateur restent privées dans votre application.

Fonctionnalités supplémentaires comme faire glisser déplacer prise en charge pour les URL et prise en charge pour `window.open()` ont également été ajoutées à `SFSafariViewController` dans iOS 11. Vous trouverez plus d’informations sur ces nouvelles fonctionnalités dans [documentation de SFSafariViewController d’Apple](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` a été introduit dans le cadre de WebKit dans iOS 8 comme un moyen de l’affichage du contenu web à votre utilisateur. Il est plus personnalisable que `SFSafariViewController`, ce qui vous permet de créer votre propre navigation et l’interface utilisateur.

Apple a introduit trois améliorations principales pour `WKWebView` avec iOS 11 : 

- La possibilité de gérer les cookies
- Le filtrage de contenu
- Chargement de la ressource personnalisée. 

Gestion des cookies est effectuée via le nouveau [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) (classe), ce qui vous permet d’ajouter et supprimer des cookies, pour obtenir tous les cookies stockés dans un WKWebView et observez le cookie à stocker les modifications.

Contenu de filtrage vous permet de gérer le type de contenu que votre utilisateur s’affiche, ce qui vous permet de vous assurer qu’il est sécurisé, famille et, si nécessaire, disponible uniquement pour un groupe d’utilisateurs. Ceci est implémenté via la nouvelle [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) (classe), en fournissant des paires de déclencheurs et actions dans JSON. Vous trouverez plus d’informations sur ces actions et déclencheurs dans Apple [les règles de blocage de contenu](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) guide.

iOS 11 vous permet désormais de personnaliser `WKWebView` avec le chargement de votre contenu web des ressources personnalisées. Ceci est implémenté via la `IWKUrlSchemeHandler` interface, ce qui vous permet de gérer les schémas d’URL qui ne sont pas Kit Web natifs. Cette interface possède une méthode de démarrage et d’arrêt qui doit être implémentée :

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

Une fois que le gestionnaire a été implémenté, utilisez-le pour définir le `SetUrlSchemeHandler` propriété sur le `WKWebViewConfiguration`. Ensuite, chargez l’URL d’un objet qui utilise le schéma personnalisé :

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

