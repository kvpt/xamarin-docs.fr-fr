---
title: Sécurité du transport d’application dans Xamarin. iOS
description: Application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et votre application.
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/13/2017
ms.openlocfilehash: 039a73b45f93525631635a9a73bf153c7938bc92
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766674"
---
# <a name="app-transport-security-in-xamarinios"></a>Sécurité du transport d’application dans Xamarin. iOS

_Application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et votre application._

Cet article présente les modifications de sécurité apportées par la sécurité du transport d’application sur une application iOS 9 et ce que [cela signifie pour vos projets Xamarin. iOS](#xamarinsupport), elle couvre les [options de configuration](#config) de l’ATS et explique comment refuser l’inscription à l' [ATS](#optout) ATS si nécessaire. Dans la mesure où ATS est activé par défaut, toutes les connexions Internet non sécurisées lèvent une exception dans les applications iOS 9 (à moins que vous ne les ayez explicitement autorisées).

## <a name="about-app-transport-security"></a>À propos de la sécurité du transport d’application

Comme indiqué ci-dessus, ATS garantit que toutes les communications Internet dans iOS 9 et OS X El Capitan sont conformes aux meilleures pratiques en matière de connexion, ce qui empêche la divulgation accidentelle d’informations sensibles directement par le biais de votre application ou d’une bibliothèque consommation.

Pour les applications existantes, implémentez le protocole dans la mesure du `HTTPS` possible. Pour les nouvelles applications Xamarin. iOS, vous devez `HTTPS` utiliser exclusivement pour communiquer avec les ressources Internet. En outre, la communication d’API de haut niveau doit être chiffrée à l’aide de TLS version 1,2 avec confidentialité ascendante.

Toute connexion effectuée avec [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [passer](xref:Foundation.NSUrlSession) utilise ATS par défaut dans les applications générées pour iOS 9 et OS X 10,11 (El Capitan).

## <a name="default-ats-behavior"></a>Comportement ATS par défaut

Dans la mesure où ATS est activé par défaut dans les applications conçues pour iOS 9 et OS X 10,11 (El Capitan), toutes les connexions utilisant [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [passer](xref:Foundation.NSUrlSession) seront soumises aux exigences de sécurité ATS. Si vos connexions ne répondent pas à ces exigences, elles échouent avec une exception.

### <a name="ats-connection-requirements"></a>Configuration requise pour la connexion ATS

L’ATS impose les exigences suivantes pour toutes les connexions Internet :

- Tous les chiffrements de connexion doivent utiliser le secret de transfert. Consultez la liste des chiffrements acceptés ci-dessous.
- Le protocole TLS (Transport Layer Security) doit avoir la version 1,2 ou une version ultérieure.
- Au moins une empreinte digitale SHA256 avec une clé RSA de 2048 bits ou supérieure, ou une clé à courbe elliptique (ECC) de 256 bits ou supérieure doit être utilisée pour tous les certificats.

Là encore, comme ATS est activé par défaut dans iOS 9, toute tentative de connexion qui ne répond pas à ces exigences entraîne la levée d’une exception.

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>Chiffrements compatibles avec ATS

Le type de chiffrement par PFS suivant est accepté par les communications Internet sécurisées de l’ATS :

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

Pour plus d’informations sur l’utilisation des classes de communication Internet iOS, consultez les informations de référence sur la [classe NSURLConnection](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755), la référence [CFURL](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206) ou la [référence de classe passer](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435)d’Apple.

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>Prise en charge d’ATS dans Xamarin. iOS

Dans la mesure où ATS est activé par défaut dans iOS 9 et OS X El Capitan, si votre application Xamarin. iOS ou une bibliothèque ou un service qu’elle utilise établit une connexion à Internet, vous devez effectuer une action ou vos connexions entraînent la levée d’une exception.

Pour une application existante, Apple vous suggère de prendre `HTTPS` en charge le protocole le plus rapidement possible. Si vous ne pouvez pas vous connecter à un service Web tiers qui ne prend pas en `HTTPS` charge ou si `HTTPS` la prise en charge ne serait pas pratique, vous pouvez refuser l’ATS. Pour plus d’informations, consultez la section [opt-out de l’ATS](#optout) ci-dessous.

Pour une nouvelle application Xamarin. iOS, vous devez utiliser `HTTPS` exclusivement pour communiquer avec les ressources Internet. Là encore, il peut y avoir des situations (comme l’utilisation d’un service Web tiers) si ce n’est pas possible et que vous devez choisir d’annuler l’ATS.

En outre, ATS applique la communication d’API de haut niveau à chiffrer à l’aide de TLS version 1,2 avec confidentialité ascendante. Pour plus d’informations, consultez les sections configuration de la [connexion ATS](#ats-connection-requirements) et [chiffrement compatible avec ATS](#ats-compatible-ciphers) ci-dessus.

Bien que vous ne soyez pas familiarisé avec le protocole TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)), il s’agit du successeur de SSL ([Secure Socket Layer](https://en.wikipedia.org/wiki/Transport_Layer_Security)) et fournit une collection de protocoles de chiffrement pour garantir la sécurité sur les connexions réseau.

Le niveau TLS est contrôlé par le service Web que vous consommez et, par conséquent, en dehors du contrôle de l’application. Le `HttpClient` et le `ModernHttpClient` doivent utiliser automatiquement le plus haut niveau de chiffrement TLS pris en charge par le serveur.

Selon le serveur auquel vous communiquez (surtout s’il s’agit d’un service tiers), vous devrez peut-être désactiver le secret de transfert ou sélectionner un niveau TLS inférieur. Pour plus d’informations, consultez la section [Configuration des options ATS](#configuring-ats-options) ci-dessous.

> [!IMPORTANT]
> La sécurité du transport d’application ne s’applique pas aux applications Xamarin à l’aide d' **implémentations httpclient gérées**. Elle s’applique aux connexions utilisant des **implémentations httpclient** cfnetwork ou des **implémentations httpclient passer** uniquement.

### <a name="setting-the-httpclient-implementation"></a>Définition de l’implémentation HTTPClient

Pour définir l’implémentation HTTPClient utilisée par une application iOS, double-cliquez sur le **projet** dans la **Explorateur de solutions** pour ouvrir les **Options du projet**. Accédez à la **Build iOS** et sélectionnez le type de client souhaité sous la liste déroulante **implémentation de httpclient** :

![](ats-images/client01.png "Définition des options de génération iOS")

#### <a name="managed-handler"></a>Gestionnaire managé

Le gestionnaire géré est le gestionnaire HttpClient entièrement géré qui a été fourni avec les versions précédentes de Xamarin. iOS et est le gestionnaire par défaut.

Professionnels

- Il est le plus compatible avec Microsoft .NET et une version antérieure de Xamarin.

Inconvénients

- Il n’est pas entièrement intégré à iOS (il est par exemple limité à TLS 1,0).
- Elle est généralement beaucoup plus lente que les API natives.
- Il requiert plus de code géré et crée des applications plus volumineuses.

#### <a name="cfnetwork-handler"></a>Gestionnaire CFNetwork

Le gestionnaire basé sur cfnetwork est basé sur le `CFNetwork` Framework natif.

Professionnels

- Utilise l’API native pour améliorer les performances et réduire les tailles des exécutables.
- Ajoute la prise en charge des normes plus récentes, telles que TLS 1,2.

Inconvénients

- Requiert iOS 6 ou version ultérieure.
- Non disponible dans Watchos.
- Certaines fonctionnalités et options HttpClient ne sont pas disponibles.

#### <a name="nsurlsession-handler"></a>Gestionnaire passer

Le gestionnaire basé sur passer est basé sur l' `NSUrlSession` API native.

Professionnels

- Utilise l’API native pour améliorer les performances et réduire les tailles des exécutables.
- Ajoute la prise en charge des normes plus récentes, telles que TLS 1,2.

Inconvénients

- Requiert iOS 7 ou une version ultérieure.
- Certaines fonctionnalités et options HttpClient ne sont pas disponibles.

## <a name="diagnosing-ats-issues"></a>Diagnostic des problèmes ATS

Lorsque vous tentez de vous connecter à Internet, soit directement, soit à partir d’une vue Web dans iOS 9, vous pouvez obtenir une erreur au format suivant :

> La sécurité du transport d’application a bloqué un http://www.-the-blocked-domain.com) http en texte clair (chargement des ressources, car il n’est pas sécurisé. Les exceptions temporaires peuvent être configurées via le fichier info. plist de votre application.

Dans iOS9, la sécurité d’application transport (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et votre application. En outre, ATS requiert la communication à `HTTPS` l’aide du protocole et de la communication de l’API de haut niveau à chiffrer à l’aide de TLS version 1,2 avec confidentialité ascendante.

Dans la mesure où ATS est activé par défaut dans les applications conçues pour iOS 9 et OS X 10,11 (El Capitan) `NSURLConnection`, `CFURL` toutes `NSURLSession` les connexions utilisant ou seront soumises aux exigences de sécurité ATS. Si vos connexions ne répondent pas à ces exigences, elles échouent avec une exception.

Apple fournit également l' [exemple d’application TLSTool](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2) qui peut être compilé (ou éventuellement transcodé en Xamarin et C#) et utilisé pour diagnostiquer les problèmes ATS/TLS. Pour plus d’informations sur la résolution de ce problème, consultez la section [opt-out de l’ATS](#optout) ci-dessous.

<a name="config" />

## <a name="configuring-ats-options"></a>Configuration des options ATS

Vous pouvez configurer plusieurs fonctionnalités d’ATS en définissant des valeurs pour des clés spécifiques dans le fichier **info. plist** de votre application. Les clés suivantes sont disponibles pour contrôler l’ATS (_en retrait pour montrer comment elles sont imbriquées_) :

```
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

Chaque clé a le type et la signification suivants :

- **NSAppTransportSecurity** (`Dictionary`)-Contient toutes les clés et valeurs de paramètre pour ATS.
- **NSAllowsArbitraryLoads** (`Boolean`)-Si `YES` ATS est désactivé pour un domaine **non** listé dans `NSExceptionDomains`. Pour les domaines listés, les paramètres de sécurité spécifiés sont utilisés.
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`)-Si `YES` autorise le chargement correct des pages Web alors que la protection d’Apple transport Security (ATS) est toujours activée pour le reste de l’application.
- **NSExceptionDomains** (`Dictionary`) : Collection de domaines qui et les paramètres de sécurité que l’ATS doit utiliser pour un domaine donné.
- `Dictionary`  **Domain-Name-for-exception-As-String>():collectiond’exceptionspourundomaine\<** donné (par exemple, `www.xamarin.com`).
- **NSExceptionMinimumTLSVersion** (`String`) : Version TLS minimale `TLSv1.0`en tant que, `TLSv1.1` ou `TLSv1.2` (valeur par défaut).
- **NSExceptionRequiresForwardSecrecy** (`Boolean`)-Si `NO` le domaine n’a pas besoin d’utiliser un chiffrement avec la sécurité de transfert. La valeur par défaut est `YES`.
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`)-Si `NO` (valeur par défaut), toutes les communications avec ce domaine doivent `HTTPS` être dans le protocole.
- **NSRequiresCertificateTransparency** (`Boolean`)-Si `YES` le protocole SSL du domaine (SSL) doit inclure des données de transparence valides. La valeur par défaut est `NO`.
- **NSIncludesSubdomains** (`Boolean`)-Si `YES` ces paramètres remplacent tous les sous-domaines de ce domaine. La valeur par défaut est `NO`.
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-Version TLS utilisée lorsque le domaine est un service tiers en dehors du contrôle du développeur.
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`)-Si `YES` un domaine tiers requiert le secret de transfert.
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`)-Si `YES` l’ATS autorise une communication non sécurisée avec des domaines tiers.

<a name="optout" />

### <a name="opting-out-of-ats"></a>Opt-out d’ATS

Bien qu’Apple suggère une utilisation `HTTPS` du protocole et une communication sécurisée avec les informations basées sur Internet, il peut arriver que cela ne soit pas toujours possible. Par exemple, si vous communiquez avec un service Web tiers ou si vous utilisez des publicités fournies par Internet dans votre application.

Si votre application Xamarin. iOS doit effectuer une demande à un domaine non sécurisé, les modifications suivantes du fichier **info. plist** de votre application désactivent les paramètres de sécurité par défaut imposés par l’ATS pour un domaine donné :

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

Dans Visual Studio pour Mac, double-cliquez sur `Info.plist` le fichier dans le **Explorateur de solutions**, basculez vers la vue **source** et ajoutez les clés ci-dessus :

[![](ats-images/ats01.png "Vue source du fichier info. plist")](ats-images/ats01.png#lightbox)

Si votre application a besoin de charger et d’afficher du contenu Web à partir de sites non sécurisés, ajoutez le code suivant au fichier **info. plist** de votre application pour que les pages Web soient correctement chargées, alors que la protection d’Apple transport Security (ATS) est toujours activée pour le reste de l’application :

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

Si vous le souhaitez, vous pouvez apporter les modifications suivantes au fichier **info. plist** de votre application pour désactiver complètement l’ATS pour tous les domaines et la communication Internet :

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

Dans Visual Studio pour Mac, double-cliquez sur `Info.plist` le fichier dans le **Explorateur de solutions**, basculez vers la vue **source** et ajoutez les clés ci-dessus :

[![](ats-images/ats02.png "Vue source du fichier info. plist")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> Si votre application nécessite une connexion à un site Web non sécurisé, vous devez **toujours** entrer le domaine en tant qu' `NSExceptionDomains` exception au lieu de désactiver complètement l' `NSAllowsArbitraryLoads`ATS à l’aide de. `NSAllowsArbitraryLoads` doit uniquement être utilisé dans les situations d’urgence extrêmes.

Là encore, la désactivation de l’ATS _ne doit être utilisée qu’en_ dernier recours, si le basculement vers des connexions sécurisées n’est pas disponible ou n’est pas pratique.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a introduit la sécurité de transport d’application (ATS) et décrit son mode d’application des communications sécurisées avec Internet. Tout d’abord, nous avons abordé les modifications apportées à ATS requis pour une application Xamarin. iOS s’exécutant sur iOS 9. Nous avons ensuite abordé la maîtrise des fonctionnalités et options ATS. Enfin, nous avons abordé le choix de l’ATS dans votre application Xamarin. iOS.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
