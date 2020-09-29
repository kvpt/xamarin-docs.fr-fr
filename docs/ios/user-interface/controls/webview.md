---
title: Affichages Web dans Xamarin. iOS
description: Ce document décrit les différentes façons dont une application Xamarin. iOS peut afficher du contenu Web. Il aborde WKWebView, SFSafariViewController, Safari et la sécurité du transport d’application.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: bf8f6a9014192d680b0032e034e34b594ecf193c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430537"
---
# <a name="web-views-in-xamarinios"></a>Affichages Web dans Xamarin. iOS

Au-delà de la durée de vie d’iOS Apple, les développeurs d’applications ont publié plusieurs manières d’incorporer les fonctionnalités d’affichage Web dans leurs applications. La plupart des utilisateurs utilisent le navigateur Web Safari intégré sur leur appareil iOS et, par conséquent, s’attendent à ce que les fonctionnalités d’affichage Web des autres applications soient cohérentes avec cette expérience. Ils s’attendent à ce que les mêmes gestes fonctionnent, les performances sur-par et les fonctionnalités de la même façon.

iOS 11 a introduit de nouvelles modifications à la fois dans `WKWebView` et `SFSafariViewController` . Pour plus d’informations, consultez le Guide du [Guide des modifications Web dans iOS 11](~/ios/platform/introduction-to-ios11/web.md) .

## <a name="wkwebview"></a>WKWebView

`WKWebView` a été introduit dans iOS 8, ce qui permet aux développeurs d’applications d’implémenter une interface de navigation Web similaire à celle de Mobile Safari. Cela est dû, en partie, au fait que `WKWebView` utilise le moteur JavaScript NITRO, le même moteur que celui utilisé par Mobile Safari. `WKWebView` doit toujours être utilisé sur UIWebView, dans la mesure du possible en raison des performances accrues, des mouvements conviviaux et de la facilité d’interaction entre la page Web et votre application.

`WKWebView` peut être ajouté à votre application d’une manière presque identique à UIWebView, mais en tant que développeur, vous avez beaucoup plus de contrôle sur l’interface utilisateur/expérience utilisateur et les fonctionnalités. La création et l’affichage de l’objet de vue Web affichent la page demandée, mais vous pouvez contrôler la façon dont la vue est présentée, comment l’utilisateur peut naviguer et comment l’utilisateur quitte la vue.  

Vous pouvez utiliser le code ci-dessous pour lancer une `WKWebView` dans votre application Xamarin. iOS :

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

Il est important de noter que `WKWebView` se trouve dans l' `WebKit` espace de noms. vous devez donc ajouter cette directive using à la partie supérieure de votre classe.

`WKWebView` peut également être utilisé dans les applications Xamarin. Mac, et vous devez l’utiliser si vous créez une application Mac/iOS multiplateforme.

La recette [handle JavaScript Alerts](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) fournit également des informations sur l’utilisation de WKWebView avec JavaScript.

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` est la dernière façon de fournir du contenu Web à partir de votre application et est disponible dans iOS 9 et versions ultérieures. Contrairement `UIWebView` `WKWebView` à ou, `SFSafariViewController` est un contrôleur d’affichage et ne peut donc pas être utilisé avec d’autres vues. Vous devez présenter `SFSafariViewController` comme nouveau contrôleur d’affichage, de la même façon que vous le feriez pour n’importe quel contrôleur d’affichage.

 `SFSafariViewController` est essentiellement un « mini Safari » qui peut être incorporé dans votre application. À l’instar de WKWebView, il utilise le même moteur Nitro JavaScript, mais fournit également une gamme de fonctionnalités Safari supplémentaires telles que le remplissage automatique, le lecteur et la possibilité de partager des cookies et des données avec Mobile Safari. L’interaction entre l’utilisateur et le `SFSafariViewController` n’est pas accessible à votre application. Votre application n’aura pas accès aux fonctionnalités Safari par défaut.

Il implémente également par défaut un bouton **terminé** , ce qui permet à l’utilisateur de revenir facilement à votre application, ainsi qu’aux boutons de navigation suivant et précédent, ce qui permet à l’utilisateur de naviguer dans une pile de pages Web. En outre, il permet également à l’utilisateur de disposer d’une barre d’adresses qui lui donne la tranquillité d’esprit qu’il se trouve sur la page Web attendue. La barre d’adresses ne permet pas à l’utilisateur de modifier l’URL.

Ces implémentations ne peuvent pas être modifiées `SFSafariViewController` . il est donc idéal de l’utiliser comme navigateur par défaut si votre application souhaite présenter une page Web sans aucune personnalisation.

Vous pouvez utiliser le code ci-dessous pour lancer une `SFSafariViewController` dans votre application Xamarin. iOS :

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Cela génère l’affichage Web suivant :

[![Un exemple d’affichage Web avec SFSafariViewController](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Il est également possible d’ouvrir l’application Mobile Safari à partir de votre application, en utilisant le code ci-dessous :

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

Cela génère l’affichage Web suivant :

[![Une page Web présentée dans Safari](webview-images/safari.png)](webview-images/safari.png#lightbox)

La navigation entre les utilisateurs et votre application vers Safari doit généralement être évitée. La plupart des utilisateurs ne peuvent pas s’attendre à une navigation en dehors de votre application. par conséquent, si vous quittez votre application, les utilisateurs peuvent ne jamais les retourner, ce qui se traduit essentiellement par la suppression de l’engagement.

les améliorations iOS 9 permettent à l’utilisateur de revenir facilement à votre application à l’aide d’un bouton précédent, fourni dans l’angle supérieur gauche de la page Safari.

## <a name="app-transport-security"></a>Sécurité de transport de l’application

Application transport Security, ou *ATS* a été introduit par Apple dans iOS 9 pour s’assurer que toutes les communications Internet sont conformes aux meilleures pratiques en matière de connexion sécurisée.

Pour plus d’informations sur ATS, y compris son implémentation dans votre application, reportez-vous au guide sur la [sécurité d’App transport](~/ios/app-fundamentals/ats.md) .

## <a name="uiwebview-deprecation"></a>Désapprobation UIWebView

`UIWebView` est l’approche héritée d’Apple pour fournir du contenu Web dans votre application. Elle a été publiée dans iOS 2,0 et a été dépréciée à compter du 8,0.

> [!IMPORTANT]
> `UIWebView` est déconseillé. Les nouvelles applications qui utilisent ce contrôle ne seront [pas acceptées dans l’App Store à compter du 2020 avril, et les mises à jour des applications qui utilisent ce contrôle ne seront pas acceptées par le 2020 décembre](https://developer.apple.com/news/?id=12232019b).
>
> La [ `UIWebView` documentation d’Apple](https://developer.apple.com/documentation/uikit/uiwebview) suggère que les applications doivent utiliser à la [`WKWebView`](#wkwebview) place.
>
> Si vous recherchez des ressources en ce qui concerne l' `UIWebView` avertissement de désapprobation (iTMS-90809) lors de l’utilisation de Xamarin. Forms, reportez-vous à la documentation de [Xamarin. Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809) .

Les développeurs qui ont envoyé des applications iOS au cours des six derniers mois (ou ainsi) ont peut-être reçu un avertissement de l’App Store, à propos de `UIWebView` leur dépréciation.

Les désapprobations des API sont courantes. Xamarin. iOS utilise des attributs personnalisés pour signaler ces API (et suggérer des remplacements lorsqu’ils sont disponibles) aux développeurs. Ce qui est différent cette fois, et bien moins courant, est que la désapprobation **sera appliquée** par l’App Store d’Apple au moment de l’envoi.

Malheureusement, la suppression du `UIWebView` type de `Xamarin.iOS.dll` est une [modification avec rupture binaire](/dotnet/core/compatibility/categories#binary-compatibility). Cette modification interrompt les bibliothèques tierces existantes, y compris certaines qui ne sont peut-être pas prises en charge ou même recompilables (par exemple, la source fermée). Cela permet de créer des problèmes supplémentaires pour les développeurs. Par conséquent, nous ne supprimons pas *encore*le type.

À compter de [Xamarin. iOS 13,16](/xamarin/ios/release-notes/13/13.16) , de nouveaux outils et détection sont disponibles pour vous aider à migrer à partir de `UIWebView` .

### <a name="detection"></a>Détection

Si vous avez récemment envoyé une application iOS à l’Apple App Store, vous vous demandez peut-être si cette situation s’applique à vos applications.

Pour le savoir, vous pouvez ajouter `--warn-on-type-ref=UIKit.UIWebView` aux **arguments mTouch supplémentaires** de votre projet. Cela vous avertira de **toute** référence au déprécieur au `UIWebView` sein de votre application (et de toutes ses dépendances). Différents avertissements sont utilisés pour signaler les types **avant** et **après** l’exécution de l’éditeur de liens managé.

Les avertissements, comme d’autres, peuvent être convertis en erreurs à l’aide de `-warnaserror:` . Cela peut être utile si vous souhaitez vous assurer qu’une nouvelle dépendance à `UIWebView` n’est pas ajoutée après vos vérifications. Par exemple :

* `-warnaserror:1502` signale des erreurs si des références sont trouvées dans des assemblys pré-liés.
* `-warnaserror:1503` signale des erreurs si des références sont trouvées dans des assemblys postérieurement liés.

Vous pouvez également interrompre les avertissements si les résultats de la liaison avant/après ne sont pas utiles. Par exemple :

* `-nowarn:1502` ne signale **pas** les avertissements si des références sont détectées dans des assemblys pré-liés.
* `-nowarn:1503` ne signale **pas** les avertissements si des références sont trouvées dans des assemblys postérieurement liés.

### <a name="removal"></a>Suppression

Chaque application est unique. `UIWebView`La suppression de votre application peut nécessiter des étapes différentes selon le mode et l’emplacement d’utilisation. Les scénarios les plus courants sont les suivants :

- L’intérieur de votre application n’est pas utilisé `UIWebView` . Tout est parfait. Vous **ne devez pas** avoir d’avertissements lors de l’envoi à AppStore. Rien d’autre n’est nécessaire de votre part.
- Utilisation directe de `UIWebView` par votre application. Commencez par supprimer votre utilisation de `UIWebView` , par exemple, remplacez-la par les `WKWebView` types plus récents (iOS 8) ou `SFSafariViewController` (iOS 9). Une fois cette opération terminée, l’éditeur de liens managé ne doit pas voir de référence à `UIWebView` et le fichier binaire d’application final n’en aura pas de trace.
- Utilisation indirecte. `UIWebView` peut être présent dans certaines bibliothèques tierces, gérées ou natives, qui sont utilisées par votre application. Commencez par mettre à jour vos dépendances externes avec leurs versions les plus récentes, car cette situation est peut-être déjà résolue dans une version plus récente. Si ce n’est pas le cas, contactez le ou les chargé (s) des bibliothèques et posez des questions sur leurs plans de mise à jour.

Vous pouvez également essayer les approches suivantes :

1. Si vous utilisez **Xamarin. Forms**, lisez ce billet de [blog](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/).
1. Activez l’éditeur de liens managé (sur l’ensemble du projet ou, au moins, sur la dépendance à l’aide de `UIWebView` ) afin qu’il *puisse* être supprimé, s’il n’est pas référencé. Cela permet de résoudre le problème, mais peut nécessiter des tâches supplémentaires pour sécuriser votre code.
1. Si vous ne pouvez pas modifier les paramètres de l’éditeur de liens managés, consultez les cas spéciaux ci-dessous.

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>Les applications ne peuvent pas utiliser l’éditeur de liens (ou modifier ses paramètres)

Si, pour une raison quelconque, vous n’utilisez **pas** l’éditeur de liens managé (par exemple, **ne pas lier**), le `UIWebView` symbole reste dans l’application binaire que vous envoyez à Apple et il est possible qu’il soit rejeté.

Une solution *force* consiste à ajouter des `--optimize=force-rejected-types-removal` **arguments mTouch supplémentaires**de votre projet. Cette opération supprime les traces de de `UIWebView` l’application. Toutefois, tout code qui fait référence au type ne fonctionnera **pas** correctement (attend des exceptions ou des blocages). Cette approche ne doit être utilisée que si vous êtes sûr que le code n’est pas accessible au moment de l’exécution (même s’il était accessible via une analyse statique).

#### <a name="support-for-ios-7x-or-earlier"></a>Prise en charge d’iOS 7. x (ou version antérieure)

`UIWebView` fait partie d’iOS depuis v 2.0. Les remplacements les plus courants sont `WKWebView` (iOS 8) et `SFSafariViewController` (iOS 9). Si votre application prend toujours en charge les anciennes versions d’iOS, vous devez prendre en compte les options suivantes :

* Créez iOS 8 votre version cible minimale (décision au moment de la génération).
* Utilisez uniquement `WKWebView` si l’application s’exécute sur iOS 8 + (une décision d’exécution).

#### <a name="applications-not-submitted-to-apple"></a>Applications non envoyées à Apple

Si votre application n’est pas envoyée à Apple, vous devez prévoir de quitter l’API déconseillée, car elle peut être supprimée dans les futures versions d’iOS. Toutefois, vous pouvez effectuer cette transition à l’aide de votre propre calendrier.

## <a name="related-links"></a>Liens connexes

- [Vues WebView (exemple)](/samples/xamarin/ios-samples/webview)