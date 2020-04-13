---
title: Vues Web sur Xamarin.iOS
description: Ce document décrit les différentes façons dont une application Xamarin.iOS peut afficher du contenu Web. Il traite de WKWebView, SFSafariViewController, Safari, et la sécurité des transports d’applications.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: cc6c87452bf5312d66e33b7c49d3dc216eceb7d6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628300"
---
# <a name="web-views-in-xamarinios"></a>Vues Web sur Xamarin.iOS

Au cours de la durée de vie d’iOS Apple a publié un certain nombre de façons pour les développeurs d’applications d’intégrer des fonctionnalités de vue web dans leurs applications. La plupart des utilisateurs utilisent le navigateur Web Safari intégré sur leur appareil iOS, et s’attendent donc à ce que les fonctionnalités de vue Web d’autres applications sont compatibles avec cette expérience. Ils s’attendent à ce que les mêmes gestes fonctionnent, que les performances soient à égalité et la fonctionnalité la même.

iOS 11 a introduit `WKWebView` `SFSafariViewController`de nouveaux changements à la fois et . Pour plus d’informations à ce jour, consultez les modifications Du Web dans le [guide iOS 11.](~/ios/platform/introduction-to-ios11/web.md)

## <a name="wkwebview"></a>WKWebView (en anglais)

`WKWebView`a été introduit dans iOS 8 permettant aux développeurs d’applications d’implémenter une interface de navigation web similaire à celle de Safari mobile. Cela est dû, en partie, `WKWebView` au fait que utilise le moteur Nitro Javascript, le même moteur utilisé par Safari mobile. `WKWebView`devrait toujours être utilisé sur UIWebView si possible en raison de l’augmentation des performances, intégrées dans des gestes conviviaux, et la facilité d’interaction entre la page Web et votre application.

`WKWebView`peut être ajouté à votre application d’une manière presque identique à UIWebView, mais comme le développeur, vous avez beaucoup plus de contrôle sur l’interface utilisateur / UX et la fonctionnalité. La création et l’affichage de l’objet de vue Web afficheront la page demandée, mais vous pouvez contrôler la façon dont la vue est présentée, comment l’utilisateur peut naviguer, et comment l’utilisateur sort de la vue.  

Le code ci-dessous peut `WKWebView` être utilisé pour lancer un dans votre application Xamarin.iOS:

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

Il est important `WKWebView` de noter `WebKit` que c’est dans l’espace de nom, de sorte que vous devrez ajouter cette directive en utilisant la directive au sommet de votre classe.

`WKWebView`peut également être utilisé dans les applications Xamarin.Mac, et vous devriez l’utiliser si vous créez une application multiplateforme Mac/iOS.

La recette [Handle JavaScript Alerts](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) fournit également des informations sur l’utilisation de WKWebView avec Javascript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController`est la dernière façon de fournir du contenu Web à partir de votre application et est disponible dans iOS 9 et plus tard. Contrairement `UIWebView` `WKWebView`ou `SFSafariViewController` , est un contrôleur de vue et ne peut donc pas être utilisé avec d’autres vues. Vous devez `SFSafariViewController` vous présenter comme un nouveau contrôleur de vue, de la même manière que vous présenteriez n’importe quel contrôleur de vue.

 `SFSafariViewController`est essentiellement un «mini safari» qui peut être intégré dans votre application. Comme WKWebView, il utilise le même moteur Nitro Javascript, mais fournit également une gamme de fonctionnalités Safari supplémentaires telles que AutoFill, Reader, et la possibilité de partager des cookies et des données avec Safari mobile. L’interaction entre `SFSafariViewController` l’utilisateur et l’utilisateur n’est pas accessible à votre application. Votre application n’aura accès à aucune des fonctionnalités Safari par défaut.

Il implémente également, par défaut, un bouton **Fait,** permettant à l’utilisateur de revenir facilement à votre application, et les boutons de navigation avant et arrière, permettant à votre utilisateur de naviguer à travers une pile de pages Web. En outre, il fournit également à l’utilisateur une barre d’adresse leur donnant la tranquillité d’esprit qu’ils sont sur la page Web attendue. La barre d’adresse ne permet pas à l’utilisateur de modifier l’URL.

Ces implémentations `SFSafariViewController` ne peuvent pas être modifiées, est donc idéal à utiliser comme navigateur par défaut si votre application veut présenter une page Web sans aucune personnalisation.

Le code ci-dessous peut `SFSafariViewController` être utilisé pour lancer un dans votre application Xamarin.iOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Cela produit la vue web suivante :

[![Un exemple de vue web avec SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Il est également possible d’ouvrir l’application Safari mobile à partir de votre application, en utilisant le code ci-dessous :

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Cela produit la vue web suivante :

[![Une page web présentée dans Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

La navigation des utilisateurs loin de votre application vers Safari doit généralement toujours être évitée. La plupart des utilisateurs ne s’attendent pas à la navigation en dehors de votre application, donc si vous naviguez loin de votre application, les utilisateurs peuvent ne jamais le retourner, essentiellement tuer l’engagement.

Les améliorations iOS 9 permettent à l’utilisateur de revenir facilement à votre application grâce à un bouton arrière fourni dans le coin supérieur gauche de la page Safari.

## <a name="app-transport-security"></a>Sécurité de transport de l’application

App Transport Security, ou *ATS* a été introduit par Apple dans iOS 9 pour s’assurer que toutes les communications Internet sont conformes aux meilleures pratiques de connexion sécurisées.

Pour plus d’informations sur ATS, y compris la façon de l’implémenter dans votre application, consultez le guide [App Transport Security.](~/ios/app-fundamentals/ats.md)

## <a name="uiwebview-deprecation"></a>Dépréciation d’UIWebView

`UIWebView`est la façon héritée d’Apple de fournir du contenu Web dans votre application. Il a été publié dans iOS 2.0, et a été déprécié à partir de 8.0.

> [!IMPORTANT]
> `UIWebView` est déconseillé. Les nouvelles applications utilisant ce contrôle [ne seront pas acceptées dans l’App Store à partir d’avril 2020, et les mises à jour des applications utilisant ce contrôle ne seront pas acceptées d’ici décembre 2020](https://developer.apple.com/news/?id=12232019b).
>
> [La documentation `UIWebView` d’Apple](https://developer.apple.com/documentation/uikit/uiwebview) [`WKWebView`](#wkwebview) suggère que les applications devraient plutôt être utilisées.
>
> Si vous êtes à la `UIWebView` recherche de ressources en ce qui concerne l’avertissement de dépréciation (ITMS-90809) tout en utilisant Xamarin.Forms, veuillez consulter la documentation [Xamarin.Forms WebView.](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)

Les développeurs qui ont soumis des demandes iOS au cours des six derniers `UIWebView` mois (ou plus) pourraient avoir reçu un avertissement de l’App Store, au sujet d’être dépréciés.

Les dépréciations des API sont courantes. Xamarin.iOS utilise des attributs personnalisés pour signaler ces API (et suggérer des remplacements lorsqu’ils sont disponibles) aux développeurs. Ce qui est différent cette fois, et beaucoup moins commun, c’est que la dépréciation **sera appliquée** par l’App Store d’Apple au moment de la soumission.

Malheureusement, la `UIWebView` suppression `Xamarin.iOS.dll` du type est un [changement de rupture binaire](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility). Ce changement brisera les bibliothèques existantes du 3e parti, y compris certaines qui pourraient ne pas être soutenues ou même re-compilables plus (par exemple, source fermée). Cela ne fera que créer des problèmes supplémentaires pour les développeurs. Par conséquent, nous ne supprimons pas *encore*le type .

En commençant par [Xamarin.iOS 13.16](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16) nouvelle détection et `UIWebView`des outils sont disponibles pour vous aider à migrer de .

### <a name="detection"></a>Détection

Si vous avez récemment soumis une demande iOS à l’App Store d’Apple, vous vous demandez peut-être si cette situation s’applique à votre application.s).

Pour le savoir, `--warn-on-type-ref=UIKit.UIWebView` vous pouvez ajouter aux arguments supplémentaires de votre projet **mtouch.** Cela avertira de **toute** référence à `UIWebView` la dépréciation à l’intérieur de votre application (et toutes ses dépendances). Différents avertissements sont utilisés pour signaler les types **avant** et **après** l’exécution du lien géré.

Les avertissements, comme d’autres, `-warnaserror:`peuvent être transformés en erreurs utilisant . Cela peut être utile si vous voulez `UIWebView` vous assurer qu’une nouvelle dépendance n’est pas ajoutée après vos vérifications. Par exemple :

* `-warnaserror:1502`signalera les erreurs si des références sont trouvées dans les assemblages prédésiques.
* `-warnaserror:1503`signalera les erreurs si des références sont trouvées dans les assemblages post-liés.

Vous pouvez également réduire au silence les avertissements si les résultats de liaison pré/post ne sont pas utiles. Par exemple :

* `-nowarn:1502`ne signalera **pas** les avertissements si des références sont trouvées dans les assemblages prédésiques.
* `-nowarn:1503`ne signalera **pas** les avertissements si des références sont trouvées dans les assemblées post-liées.

### <a name="removal"></a>Suppression

Chaque application est unique. La `UIWebView` suppression de votre application peut nécessiter différentes étapes en fonction de la façon dont elle est utilisée et de l’endroit où elle est utilisée. Les scénarios les plus courants sont les suivants :

- Il n’y `UIWebView` a aucune utilisation de l’intérieur de votre application. Tout va bien. Vous **ne** devriez pas avoir d’avertissements lors de votre soumission à l’AppStore. Rien d’autre n’est né cessaire de votre part.
- Utilisation directe `UIWebView` de par votre application. Commencez par supprimer votre `UIWebView`utilisation de , par exemple, remplacez-le par les types les plus récents `WKWebView` (iOS 8) ou `SFSafariViewController` (iOS 9). Une fois cela terminé, le linker `UIWebView` géré ne devrait pas voir de référence et l’application finale binaire n’aura aucune trace de celui-ci.
- Utilisation indirecte. `UIWebView`peut être présent dans certaines bibliothèques tierces, gérées ou indigènes qui sont utilisées par votre application. Commencez par mettre à jour vos dépendances externes à leurs dernières versions puisque cette situation pourrait déjà être résolue dans une version plus récente. Si ce n’est pas le cas, communiquez avec le responsable des bibliothèques et renseignez-vous sur leurs plans de mise à jour.

Vous pouvez également essayer les approches suivantes :

1. Si vous utilisez **Xamarin.Forms**, puis lire ce [blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Activez le lien géré (sur l’ensemble du projet `UIWebView`ou, du moins, sur la dépendance à l’aide) de sorte qu’il *pourrait* être supprimé, si elle n’est pas référencée. Cela résoudra le problème, mais pourrait avoir besoin de travail supplémentaire pour rendre votre lien de code-sécurisé.
1. Si vous n’êtes pas en mesure de modifier les paramètres de liaison gérée, consultez les cas spéciaux ci-dessous.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Les applications ne peuvent pas utiliser le lien (ou modifier ses paramètres)

Si pour une raison quelconque vous **n’utilisez pas** le lien géré `UIWebView` (par exemple, Ne pas **lier**) alors le symbole restera dans l’application binaire que vous le soumettez à Apple et il pourrait être rejeté.

Une solution *énergique* `--optimize=force-rejected-types-removal` est d’ajouter aux arguments supplémentaires de votre projet **mtouch**. Cela supprimera les `UIWebView` traces de l’application. Cependant, tout code qui se réfère au type ne fonctionnera **pas** correctement (attendez-vous à des exceptions ou des accidents). Cette approche ne doit être utilisée que si vous êtes sûr que le code n’est pas accessible au moment de l’exécution (même s’il était accessible grâce à une analyse statique).

#### <a name="support-for-ios-7x-or-earlier"></a>Prise en charge d’iOS 7.x (ou plus tôt)

`UIWebView`fait partie d’iOS depuis v2.0. Les remplacements les `WKWebView` plus courants sont `SFSafariViewController` (iOS 8) et (iOS 9). Si votre application prend toujours en charge les anciennes versions iOS, vous devriez considérer les options suivantes :

* Faites d’iOS 8 votre version cible minimale (une décision de temps de construction).
* N’utilisez `WKWebView` que si l’application est en cours d’exécution sur iOS 8 (une décision de durée).

#### <a name="applications-not-submitted-to-apple"></a>Demandes non soumises à Apple

Si votre demande n’est pas soumise à Apple, vous devriez prévoir de vous éloigner de l’API déprécié car elle peut être supprimée dans les futures versions iOS. Cependant, vous pouvez faire cette transition en utilisant votre propre calendrier.

## <a name="related-links"></a>Liens connexes

- [WebViews (échantillon)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)
