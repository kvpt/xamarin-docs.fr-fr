---
title: Sélecteur d’implémentation HttpClient et SSL/TLS pour iOS/macOS
description: La pile HttpClient et le sélecteur d’implémentation SSL/TLS déterminent l’implémentation HttpClient et SSL/TLS qui sera utilisée par votre application Xamarin iOS, tvOS ou macOS.
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 3b87eb6ae9e97be4ee34b092efce72c0d97c88b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015851"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>Sélecteur d’implémentation HttpClient et SSL/TLS pour iOS/macOS

Le **Sélecteur d’implémentation httpclient** pour Xamarin. iOS, Xamarin. TvOS et Xamarin. Mac contrôle les `HttpClient` implémentation à utiliser. Vous pouvez basculer vers une implémentation qui utilise des transports natifs iOS, tvOS ou macOS (`NSUrlSession` ou `CFNetwork`, selon le système d’exploitation). L’avantage est de TLS 1,2-support, des fichiers binaires plus petits et des téléchargements plus rapides. l’inconvénient est qu’elle requiert l’exécution de la boucle d’événements pour les opérations asynchrones à exécuter.

Les projets doivent faire référence à l’assembly **System .net. http** .

> [!WARNING]
> **Avril 2018** – en raison des exigences de sécurité accrues, notamment la conformité PCI, les principaux fournisseurs de Cloud et les serveurs Web sont censés cesser de prendre en charge les versions de TLS antérieures à 1,2. Les projets Xamarin créés dans les versions précédentes de Visual Studio utilisent par défaut des versions antérieures de TLS.
>
> Pour vous assurer que vos applications continuent de fonctionner avec ces serveurs et services, **vous devez mettre à jour vos projets Xamarin avec le paramètre `NSUrlSession` indiqué ci-dessous, puis recréer et redéployer vos applications** pour vos utilisateurs.

## <a name="selecting-an-httpclient-stack"></a>Sélection d’une pile HttpClient

Pour ajuster le `HttpClient` utilisé par votre application :

1. Double-cliquez sur le **nom du projet** dans la **Explorateur de solutions** pour ouvrir les options du projet.
2. Basculez vers les paramètres de **génération** de votre projet (par exemple, **Build iOS** pour une application Xamarin. IOS).
3. Dans la liste déroulante **implémentation httpclient** , sélectionnez le type de `HttpClient` comme suit : **passer** (recommandé), **cfnetwork**ou **géré**.

[![choisir l’implémentation de HttpClient à partir de Managed, CFNetwork ou passer](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> Pour TLS 1,2, la prise en charge de l’option `NSUrlSession` est recommandée.

### <a name="nsurlsession"></a>Passer

Le gestionnaire basé sur les `NSURLSession`est basé sur l’infrastructure de `NSURLSession` Native disponible dans iOS 7 et versions ultérieures. 
**Il s’agit du paramètre recommandé.**

#### <a name="pros"></a>Professionnels

- Il utilise des API natives pour de meilleures performances et une taille d’exécutable réduite.
- Prise en charge des normes les plus récentes, telles que TLS 1,2.

#### <a name="cons"></a>Inconvénients

- Requiert iOS 7 ou une version ultérieure.
- Certaines fonctionnalités ou options de `HttpClient` ne sont pas disponibles.

### <a name="cfnetwork"></a>CFNetwork

Le gestionnaire basé sur les `CFNetwork`est basé sur l’infrastructure de `CFNetwork` Native disponible dans iOS 6 et versions ultérieures.

#### <a name="pros"></a>Professionnels

- Il utilise des API natives pour de meilleures performances et une taille d’exécutable réduite.
- Prise en charge de normes plus récentes telles que TLS 1,2.

#### <a name="cons"></a>Inconvénients

- Requiert iOS 6 ou version ultérieure.
- Non disponible sur Watchos.
- Certaines fonctionnalités/options HttpClient ne sont pas disponibles.

### <a name="managed"></a>Managé

Le gestionnaire managé est le gestionnaire HttpClient entièrement géré qui a été livré avec la version précédente de Xamarin.

#### <a name="pros"></a>Professionnels

- Il possède le jeu de fonctionnalités le plus compatible avec Microsoft .NET et les versions antérieures de Xamarin.

#### <a name="cons"></a>Inconvénients

- Il n’est pas entièrement intégré aux systèmes d’exploitation Apple et est limité à TLS 1,0. Il se peut qu’il ne puisse pas se connecter à des serveurs Web ou services Cloud sécurisés à l’avenir.
- Il est généralement beaucoup plus lent au niveau du chiffrement que des API natives.
- Il requiert plus de code géré, créant ainsi un plus grand distribuable d’application.

### <a name="programmatically-setting-the-httpmessagehandler"></a>Définition par programmation de HttpMessageHandler

En plus de la configuration à l’ensemble du projet présentée ci-dessus, vous pouvez également instancier une `HttpClient` et injecter le `HttpMessageHandler` souhaité par le biais du constructeur, comme illustré dans les extraits de code suivants :

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

Il est ainsi possible d’utiliser une `HttpMessageHandler` différente de celle qui est déclarée dans la boîte de dialogue **Options du projet** .

## <a name="ssltls-implementation"></a>Implémentation SSL/TLS

SSL (Secure Socket Layer) et son successeur, TLS (Transport Layer Security), assurent la prise en charge de HTTP et d’autres connexions réseau via `System.Net.Security.SslStream`. Xamarin. iOS, Xamarin. tvOS ou Xamarin. Mac `System.Net.Security.SslStream` implémentation appelle l’implémentation SSL/TLS native d’Apple au lieu d’utiliser l’implémentation managée fournie par mono. L’implémentation native d’Apple prend en charge TLS 1,2.

> [!WARNING]
> La prochaine version de Xamarin.Mac (4.8) prendra uniquement en charge macOS 10.9 ou ultérieur.
> Les versions précédentes de Xamarin.Mac prenaient en charge macOS 10.7 ou ultérieur, mais l’infrastructure TLS des anciennes versions de macOS n’était pas suffisante pour prendre en charge TLS 1.2. Pour cibler macOS 10.7 ou macOS 10.8, utilisez Xamarin.Mac 4.6 ou antérieur.

## <a name="app-transport-security"></a>Sécurité de transport de l’application

La sécurité d’Apple _app transport_ (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et votre application. ATS garantit que toutes les communications Internet sont conformes aux meilleures pratiques en matière de connexion, ce qui empêche la divulgation accidentelle d’informations sensibles, directement via votre application ou une bibliothèque qu’elle consomme.

Dans la mesure où ATS est activé par défaut dans les applications générées pour iOS 9, tvOS 9 et OS X 10,11 (El Capitan) et les versions ultérieures, toutes les connexions utilisant `NSUrlConnection`, `CFUrl` ou `NSUrlSession` seront soumises aux exigences de sécurité ATS. Si vos connexions ne répondent pas à ces exigences, elles échouent avec une exception.

En fonction de votre pile HttpClient et des sélections d’implémentation SSL/TLS, vous devrez peut-être apporter des modifications à votre application pour qu’elle fonctionne correctement avec ATS.

Pour en savoir plus sur ATS, consultez notre [Guide de sécurité App transport](~/ios/app-fundamentals/ats.md).

## <a name="known-issues"></a>Problèmes connus

Cette section aborde les problèmes connus liés à la prise en charge de TLS dans Xamarin. iOS.

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>Échec du chargement du projet avec l’erreur « la valeur demandée AppleTLS est introuvable »

Xamarin. iOS 9,8 a introduit de nouveaux paramètres contenant le fichier **. csproj** pour une application Xamarin. iOS. Ces modifications peuvent entraîner des problèmes lorsque le projet est ouvert avec des versions antérieures de Xamarin. iOS. La capture d’écran suivante est un exemple de message d’erreur qui peut s’afficher dans ce scénario :

![Capture d’écran de l’erreur lors de la tentative de chargement du projet, valeur demandée héritée introuvable](http-stack-images/tlserror-xs.png)

Cette erreur est provoquée par l’introduction du paramètre `MtouchTlsProvider` au fichier projet dans Xamarin. iOS 9,8. S’il n’est pas possible de mettre à jour vers Xamarin. iOS 9,8 (ou une version ultérieure), la solution de contournement consiste à modifier manuellement l’application de fichier **. csproj** , à supprimer l’élément `MtouchTlsprovider`, puis à enregistrer le fichier projet modifié.

L’extrait de code suivant est un exemple de ce à quoi peut ressembler le paramètre `MtouchTlsProvider` dans un fichier **. csproj** :

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>Liens connexes

- [Transport Layer Security (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [Sécurité de transport de l’application](~/ios/app-fundamentals/ats.md)
