---
title: Pile HttpClient et sélecteur d’implémentation SSL/TLS pour Android
description: La pile HttpClient et les sélecteurs d’implémentation SSL/TLS déterminent l’implémentation HttpClient et SSL/TLS qui sera utilisée par vos applications Xamarin. Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: f9f9b112a083615f9cf1d74d7cf81f5ca4f7901f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019236"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pile HttpClient et sélecteur d’implémentation SSL/TLS pour Android

La pile HttpClient et les sélecteurs d’implémentation SSL/TLS déterminent l’implémentation HttpClient et SSL/TLS qui sera utilisée par vos applications Xamarin. Android.

Les projets doivent faire référence à l’assembly **System .net. http** .

> [!WARNING]
> **Avril 2018** – en raison des exigences de sécurité accrues, notamment la conformité PCI, les principaux fournisseurs de Cloud et les serveurs Web sont censés cesser de prendre en charge les versions de TLS antérieures à 1,2. Les projets Xamarin créés dans les versions précédentes de Visual Studio utilisent par défaut des versions antérieures de TLS.
>
> Pour vous assurer que vos applications continuent de fonctionner avec ces serveurs et services, **vous devez mettre à jour vos projets Xamarin avec les paramètres `Android HttpClient` et `Native TLS 1.2` indiqués ci-dessous, puis recréer et redéployer vos applications** pour vos utilisateurs.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La configuration Xamarin. Android HttpClient se trouve dans **Options du projet > options Android**, puis cliquez sur le bouton **Options avancées** .

Les paramètres recommandés pour la prise en charge de TLS 1,2 sont les suivants :

[![les options Android de Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

La configuration Xamarin. Android HttpClient se trouve dans **Options du projet > générer > paramètres de build Android** , puis cliquez sur l’onglet **général** .

Les paramètres recommandés pour la prise en charge de TLS 1,2 sont les suivants :

[![Visual Studio pour Mac options Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Autres options de configuration

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler est le nouveau gestionnaire qui délègue au code natif Java/OS au lieu d’implémenter tout dans du code managé.
**Il s’agit de l’option recommandée.**

#### <a name="pros"></a>Professionnels

- Utilisez l’API native pour obtenir de meilleures performances et une taille d’exécutable réduite.
- Prise en charge des dernières normes, par exemple TLS 1,2.

#### <a name="cons"></a>Inconvénients

- Requiert Android 4,1 ou une version ultérieure.
- Certaines fonctionnalités/options HttpClient ne sont pas disponibles.

### <a name="managed-httpclienthandler"></a>Managé (HttpClientHandler)

Managed Handler est le gestionnaire HttpClient entièrement géré qui a été livré avec les versions antérieures de Xamarin. Android.

#### <a name="pros"></a>Professionnels

- Il s’agit de la plus compatible (fonctionnalités) avec MS .NET et des versions antérieures de Xamarin.

#### <a name="cons"></a>Inconvénients

- Il n’est pas entièrement intégré au système d’exploitation (par exemple, limité à TLS 1,0).
- Il est généralement beaucoup plus lent (par exemple, chiffrement) que l’API native.
- Il nécessite davantage de code géré, créant ainsi des applications plus volumineuses.

### <a name="choosing-a-handler"></a>Choix d’un gestionnaire

Le choix entre `AndroidClientHandler` et `HttpClientHandler` dépend des besoins de votre application. `AndroidClientHandler` est recommandé pour le support de sécurité le plus à jour, par exemple

- Vous avez besoin de la prise en charge de TLS 1.2 +.
- Votre application cible Android 4,1 (API 16) ou version ultérieure.
- Vous avez besoin de la prise en charge de TLS 1.2 + pour `HttpClient`.
- Vous n’avez pas besoin de la prise en charge de TLS 1.2 + pour `WebClient`.

`HttpClientHandler` est un bon choix si vous avez besoin de la prise en charge de TLS 1.2 +, mais que vous devez prendre en charge les versions d’Android antérieures à Android 4,1. C’est également un bon choix si vous avez besoin de la prise en charge de TLS 1.2 + pour `WebClient`.

À partir de Xamarin. Android 8,3, `HttpClientHandler` est par défaut de la valeur SSL (`btls`) comme fournisseur TLS sous-jacent. Le fournisseur TLS SSL ennuyeux offre les avantages suivants :

- Il prend en charge TLS 1.2 +.
- Il prend en charge toutes les versions d’Android.
- Il offre une prise en charge de TLS 1.2 + pour les `HttpClient` et les `WebClient`.

L’inconvénient de l’utilisation du protocole SSL ennuyeux en tant que fournisseur TLS sous-moyen est qu’il peut augmenter la taille du APK résultant (il ajoute environ 1 Mo de taille de APK supplémentaire par ABI pris en charge).

À partir de Xamarin. Android 8,3, le fournisseur TLS par défaut est un protocole de chiffrement SSL (`btls`). Si vous ne souhaitez pas utiliser le protocole SSL ennuyeuse, vous pouvez revenir à l’implémentation de l’historique géré par SSL en affectant à la propriété `$(AndroidTlsProvider)` la valeur `legacy` (pour plus d’informations sur la définition des propriétés de génération, consultez [processus de génération](~/android/deploy-test/building-apps/build-process.md)).

### <a name="programatically-using-androidclienthandler"></a>Programmation à l’aide de `AndroidClientHandler`

Le `Xamarin.Android.Net.AndroidClientHandler` est une implémentation `HttpMessageHandler` spécifiquement pour Xamarin. Android.
Les instances de cette classe utilisent l’implémentation native de `java.net.URLConnection` pour toutes les connexions HTTP. En théorie, cela permettra une augmentation des performances HTTP et des tailles de APK plus petites.

Cet extrait de code est un exemple illustrant comment explicitement pour une instance unique de la classe `HttpClient` :

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> L’appareil Android sous-jacent doit prendre en charge TLS 1,2 (IE. Android 4,1 et versions ultérieures). Veuillez noter que la prise en charge officielle de TLS 1,2 est dans Android 5.0 +. Toutefois, certains appareils prennent en charge TLS 1,2 dans Android 4.1 +.

## <a name="ssltls-implementation-build-option"></a>Option de build de l’implémentation SSL/TLS

Cette option de projet contrôle la bibliothèque TLS sous-jacente qui sera utilisée par toutes les requêtes Web, `HttpClient` et `WebRequest`. Par défaut, TLS 1,2 est sélectionné :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![zone de liste déroulante d’implémentation TLS/SSL dans Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![zone de liste déroulante d’implémentation TLS/SSL dans Visual Studio pour Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Exemple :

```csharp
var client = new HttpClient();
```

Si l’implémentation HttpClient était définie sur **géré** et que l’implémentation TLS était définie sur **Native TLS 1.2 +** , alors l’objet `client` utiliserait automatiquement le `HttpClientHandler` géré et TLS 1,2 (fourni par la bibliothèque BORINGSSL) pour son http obtenteur.

Toutefois, si l' **implémentation httpclient** est définie sur `AndroidHttpClient`, tous les objets `HttpClient` utiliseront la classe Java sous-jacente `java.net.URLConnection` et ne seront pas affectés par la valeur d' **implémentation TLS/SSL** . `WebRequest` objets utiliseraient la bibliothèque BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Autres méthodes de contrôle de la configuration SSL/TLS

Une application Xamarin. Android peut contrôler les paramètres TLS de trois façons :

1. Sélectionnez l’implémentation HttpClient et la bibliothèque TLS par défaut dans les options du projet.
2. Par programmation à l’aide de `Xamarin.Android.Net.AndroidClientHandler`.
3. Déclarez les variables d’environnement (facultatif).

Parmi les trois choix possibles, l’approche recommandée consiste à utiliser les options de projet Xamarin. Android pour déclarer les `HttpMessageHandler` et TLS par défaut pour l’application entière. Puis, si nécessaire, instanciez par programmation `Xamarin.Android.Net.AndroidClientHandler` objets. Ces options sont décrites ci-dessus.

La troisième option &ndash; l’utilisation des variables d’environnement &ndash; est expliquée ci-dessous.

### <a name="declare-environment-variables"></a>Déclarer des variables d’environnement

Deux variables d’environnement sont liées à l’utilisation de TLS dans Xamarin. Android :

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; cette variable d’environnement déclare le `HttpMessageHandler` par défaut qui sera utilisé par l’application. Exemple :

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; cette variable d’environnement déclarera quelle bibliothèque TLS sera utilisée, `btls`, `legacy`ou `default` (ce qui revient à omettre cette variable) :

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Cette variable d’environnement est définie par l’ajout d’un _fichier d’environnement_ au projet. Un fichier d’environnement est un fichier de texte brut au format UNIX avec une action de génération **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Capture d’écran de l’action de génération AndroidEnvironment dans Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Capture d’écran de l’action de génération AndroidEnvironment dans Visual Studio pour Mac.](http-stack-images/tls03-xs.png)

-----

Pour plus d’informations sur les variables d’environnement et Xamarin. Android, consultez le Guide de l' [environnement Xamarin. Android](~/android/deploy-test/environment.md) .

## <a name="related-links"></a>Liens associés

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
