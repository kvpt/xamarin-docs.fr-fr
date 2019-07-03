---
title: Pile HttpClient et sélecteur d’implémentation de SSL/TLS pour Android
description: Les sélecteurs de pile HttpClient et d’implémentation de SSL/TLS déterminent l’implémentation de HttpClient et SSL/TLS qui sera utilisée par vos applications Xamarin.Android.
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: e6d8ccea7331f53d2e7262630689075a8b9a1ff1
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522969"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>Pile HttpClient et sélecteur d’implémentation de SSL/TLS pour Android

Les sélecteurs de pile HttpClient et d’implémentation de SSL/TLS déterminent l’implémentation de HttpClient et SSL/TLS qui sera utilisée par vos applications Xamarin.Android.

Projets doivent faire référence à la **System.Net.Http** assembly.

> [!WARNING]
> **Avril 2018** – en raison d’une sécurité accrue requises, y compris la conformité avec PCI, principaux fournisseurs de services cloud et serveurs web sont censées arrêter la prise en charge des versions TLS antérieures à 1.2.  Projets Xamarin créés dans les versions précédentes de Visual Studio par défaut à utiliser des versions antérieures de TLS.
>
> Afin de garantir à vos applications continuent de fonctionner avec ces serveurs et les services, **vous devez mettre à jour vos projets Xamarin avec le `Android HttpClient` et `Native TLS 1.2` paramètres ci-dessous, puis régénérez et redéployez vos applications** à votre utilisateurs.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

La configuration de Xamarin.Android HttpClient est **Options du projet > Options Android**, puis cliquez sur le **Options avancées** bouton.

Voici les paramètres recommandés pour la prise en charge TLS 1.2 :

[![Options Android de Visual Studio](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

La configuration de Xamarin.Android HttpClient est **Options du projet > Build > Build Android** paramètres, puis cliquez sur le **général** onglet.

Voici les paramètres recommandés pour la prise en charge TLS 1.2 :

[![Visual Studio pour Mac des Options Android](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>Options de configuration de remplacement

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler est le nouveau gestionnaire qui délègue au lieu d’implémenter tous les éléments dans le code managé en code Java/système d’exploitation natif.
**Il s’agit de l’option recommandée.**

#### <a name="pros"></a>Professionnels de l'

- Utilisez l’API native pour améliorer les performances et la plus petite taille de l’exécutable.
- Prend en charge les dernières normes, par ex. TLS 1.2.

#### <a name="cons"></a>Inconvénients

- Nécessite Android 4.1 ou version ultérieure.
- Certaines fonctionnalités de HttpClient/options ne sont pas disponibles.

### <a name="managed-httpclienthandler"></a>Managé (HttpClientHandler)

Gestionnaire de code managé est le gestionnaire HttpClient entièrement géré qui a été livré avec les versions antérieures de Xamarin.Android.

#### <a name="pros"></a>Professionnels de l'

- Il est le plus compatible avec MS .NET (fonctionnalités) et les versions antérieures de Xamarin.

#### <a name="cons"></a>Inconvénients

- Il n’est pas entièrement intégré au système d’exploitation (par ex. limité à TLS 1.0).
- Il est généralement beaucoup plus lent (par ex. chiffrement) que l’API native.
- Elle nécessite plus le code managé, création d’applications de grande taille.



### <a name="choosing-a-handler"></a>Choix d’un gestionnaire

Le choix entre `AndroidClientHandler` et `HttpClientHandler` varie selon les besoins de votre application. `AndroidClientHandler` est recommandé pour la prise en charge plus à jour de sécurité, par ex.

-   Vous avez besoin de que prendre en charge de TLS 1.2 +.
-   Votre application cible Android 4.1 (API 16) ou version ultérieure.
-   Vous avez besoin de TLS 1.2 + prise en charge pour `HttpClient`.
-   Vous ne devez pas prendre en charge de TLS 1.2 + pour `WebClient`.

`HttpClientHandler` est un bon choix si vous avez besoin de TLS 1.2 + prennent en charge mais doit prendre en charge des versions d’Android antérieures à Android 4.1. Il est également un bon choix si vous avez besoin de TLS 1.2 + prise en charge pour `WebClient`.

À partir de Xamarin.Android 8.3, `HttpClientHandler` Boringssl par défaut (`btls`) en tant que le fournisseur TLS sous-jacent. Le fournisseur de forage SSL TLS offre les avantages suivants :

-   Il prend en charge TLS 1.2 +.
-   Il prend en charge toutes les versions Android.
-   Il fournit la prise en charge de TLS 1.2 + pour les deux `HttpClient` et `WebClient`.

L’inconvénient de l’utilisation de Boringssl en tant que le fournisseur TLS sous-jacent est que cela peut augmenter la taille du fichier APK qui en résulte (il ajoute environ 1 Mo de taille de fichier APK supplémentaire par ABI prises en charge).

À compter de Xamarin.Android 8.3, le fournisseur TLS par défaut est Boringssl (`btls`). Si vous ne souhaitez pas utiliser Boringssl, vous pouvez revenir à l’implémentation de SSL managée historique en définissant le `$(AndroidTlsProvider)` propriété `legacy` (pour plus d’informations sur la définition des propriétés de build, consultez [processus Build](~/android/deploy-test/building-apps/build-process.md)).


### <a name="programatically-using-androidclienthandler"></a>Par programmation à l’aide de `AndroidClientHandler`

Le `Xamarin.Android.Net.AndroidClientHandler` est un `HttpMessageHandler` implémentation spécifiquement pour Xamarin.Android.
Instances de cette classe utilisera natif `java.net.URLConnection` implémentation pour toutes les connexions HTTP. Ceci fournira théoriquement une augmentation des performances de HTTP et de petite taille APK.

Cet extrait de code est un exemple montrant comment explicitement pour une seule instance de la `HttpClient` classe :

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> L’appareil Android sous-jacent doit prendre en charge TLS 1.2 (p. ex. Android 4.1 et versions ultérieures). Veuillez noter que la prise en charge officielle de TLS 1.2 est dans Android 5.0 +. Toutefois certains périphériques prennent en charge TLS 1.2 Android 4.1 +.


## <a name="ssltls-implementation-build-option"></a>Option de build de mise en œuvre de SSL/TLS

Cette option de projet détermine quelle bibliothèque TLS sous-jacent sera utilisée par toutes les requêtes web, les deux `HttpClient` et `WebRequest`. Par défaut, TLS 1.2 est sélectionné :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Zone de liste déroulante d’implémentation de TLS/SSL dans Visual Studio](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![Zone de liste déroulante d’implémentation de TLS/SSL dans Visual Studio pour Mac](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

Exemple :

```csharp
var client = new HttpClient();
```

Si la valeur de l’implémentation de HttpClient **managé** et l’implémentation de TLS a été définie sur **TLS 1.2 + natif**, le `client` objet utiliseriez automatiquement managé `HttpClientHandler` et TLS 1.2 (fourni par la bibliothèque BoringSSL) pour les requêtes HTTP.

Toutefois, si le **implémentation de HttpClient** a la valeur `AndroidHttpClient`, puis toutes les `HttpClient` objets utilisera la classe Java sous-jacent `java.net.URLConnection` et ne seront pas affectées par la **implémentation de TLS/SSL** valeur. `WebRequest` objets utiliseriez la bibliothèque BoringSSL.

## <a name="other-ways-to-control-ssltls-configuration"></a>Autres façons de contrôler la configuration de SSL/TLS

Il existe trois façons qu’une application Xamarin.Android peut contrôler les paramètres TLS :

1. Sélectionnez la bibliothèque TLS par défaut et d’implémentation HttpClient dans les Options du projet.
2. Par programmation à l’aide de `Xamarin.Android.Net.AndroidClientHandler`.
3. Déclarer des variables d’environnement (facultatif).

Des trois options, l’approche recommandée consiste à utiliser les options du projet Xamarin.Android pour déclarer la valeur par défaut `HttpMessageHandler` et TLS pour toute l’application. Ensuite, si nécessaire, par programmation instancier `Xamarin.Android.Net.AndroidClientHandler` objets. Ces options sont décrites ci-dessus.

La troisième option &ndash; à l’aide de variables d’environnement &ndash; est expliqué ci-dessous.

### <a name="declare-environment-variables"></a>Déclarer des Variables d’environnement

Il existe deux variables d’environnement qui sont liés à l’utilisation de TLS dans Xamarin.Android :

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; Cette variable d’environnement déclare la valeur par défaut `HttpMessageHandler` que l’application doit utiliser. Par exemple :

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; Cette variable d’environnement considérera quelle bibliothèque TLS sera utilisée, soit `btls`, `legacy`, ou `default` (qui est le même que si vous omettez cette variable) :

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

Cette variable d’environnement est définie en ajoutant un _fichier d’environnement_ au projet. Un fichier d’environnement est un fichier texte brut au format Unix avec une action de génération **AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Capture d’écran de l’action de génération AndroidEnvironment dans Visual Studio.](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Capture d’écran de la AndroidEnvironment générer l’action dans Visual Studio pour Mac.](http-stack-images/tls03-xs.png)

-----

Veuillez consulter la [Xamarin.Android environnement](~/android/deploy-test/environment.md) guide pour plus d’informations sur les variables d’environnement et Xamarin.Android.


## <a name="related-links"></a>Liens associés

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
