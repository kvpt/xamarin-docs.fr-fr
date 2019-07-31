---
title: TLS (Transport Layer Security) 1,2
description: Ce document explique comment activer TLS 1,2 pour les projets Xamarin. iOS, Xamarin. Android et Xamarin. Mac. Il montre comment procéder dans Visual Studio 2019 et Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 639a62316d718534677b0ae86f9e5b57791c23e1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649217"
---
# <a name="transport-layer-security-tls-12"></a>TLS (Transport Layer Security) 1,2

L’utilisation de la dernière version de [TLS ( _Transport Layer Security_ )](https://en.wikipedia.org/wiki/Transport_Layer_Security) est importante pour garantir la sécurité des communications réseau des applications.

> [!WARNING]
> **Avril 2018** – en raison des exigences de sécurité accrues, notamment la conformité PCI, les principaux fournisseurs de Cloud et les serveurs Web sont censés cesser de prendre en charge les versions de TLS antérieures à 1,2.  Les projets Xamarin créés dans les versions précédentes de Visual Studio utilisent par défaut des versions antérieures de TLS.
>
> Pour vous assurer que vos applications continuent de fonctionner avec ces serveurs et services, **vous devez mettre à jour vos projets Xamarin pour utiliser les paramètres ci-dessous, puis recréer et redéployer vos applications** pour vos utilisateurs.

Les projets doivent faire référence à l’assembly **System .net. http** et être configurés comme indiqué ci-dessous.

## <a name="update-xamarinandroid-to-tls-12"></a>Mettre à jour Xamarin. Android vers TLS 1,2

Mettez à jour l' **implémentation httpclient** et les options d' **implémentation SSL/TLS** pour activer la sécurité TLS 1,2.

> [!NOTE]
> Requiert Android 5,0 ou une version plus récente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Ces paramètres se trouvent dans **Propriétés du projet > options Android** , puis cliquez sur le bouton **avancé** :

[![Configurer HttpClient et TLS dans Visual Studio](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Ces paramètres se trouvent dans **Options du projet > générer > onglet Build Android** :

[![Configurer HttpClient et TLS dans Visual Studio pour Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Mettre à jour Xamarin. iOS vers TLS 1,2

Mettez à jour l’option d' **implémentation httpclient** pour activer la sécurité TSL 1,2.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Ce paramètre se trouve dans **Propriétés du projet > Build iOS**:

[![Configurer HttpClient et TLS dans Visual Studio](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Ce paramètre se trouve dans **Options du projet > générer > onglet Build iOS** :

[![Configurer HttpClient dans Visual Studio pour Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Mettre à jour Xamarin. Mac vers TLS 1,2

Dans Visual Studio pour Mac, pour activer TLS 1,2 dans une application Xamarin. Mac, mettez à jour l’option d' **implémentation httpclient** dans **Options du projet > Build > Build Mac**:

[![Configurer HttpClient dans Visual Studio pour Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> La prochaine version de Xamarin.Mac (4.8) prendra uniquement en charge macOS 10.9 ou ultérieur.
> Les versions précédentes de Xamarin.Mac prenaient en charge macOS 10.7 ou ultérieur, mais l’infrastructure TLS des anciennes versions de macOS n’était pas suffisante pour prendre en charge TLS 1.2. Pour cibler macOS 10.7 ou macOS 10.8, utilisez Xamarin.Mac 4.6 ou antérieur.

## <a name="alternative-configuration-options"></a>Autres options de configuration

Cette section présente les alternatives aux configurations TLS 1,2 prises en charge ci-dessus.
Les développeurs d’applications doivent uniquement prendre en compte ces alternatives s’ils comprennent les risques liés à l’utilisation de différents niveaux de prise en charge TLS.

### <a name="httpclient-implementation"></a>Implémentation de HttpClient

Les développeurs Xamarin ont toujours pu utiliser les classes de mise en réseau natives dans leur code, mais il existe également une option qui détermine la pile de mise `HttpClient` en réseau utilisée par les classes. Cela fournit une API .NET familière qui offre les avantages de la plateforme Native en termes de vitesse et de sécurité.

Les options sont les suivantes :

- **Managed Stack** : la fonctionnalité réseau fournie par mono, ou
- **Pile Native** : plusieurs API de mise en réseau fournies par les plateformes sous-jacentes (Android, iOS ou MacOS).

La pile managée fournit le plus haut niveau de compatibilité avec le code .NET existant. Toutefois, elle peut être plus lente et entraîner une plus grande taille d’exécutable.

Les options natives peuvent être plus rapides et offrent une meilleure sécurité (y compris TLS 1,2), mais elles ne fournissent pas toutes les `HttpClient` fonctionnalités et options de la classe.

### <a name="ssltls-implementation-android"></a>Implémentation SSL/TLS (Android)

Les options du projet Android vous permettent également de choisir l’implémentation SSL/TLS à prendre en charge:

- **Mono/géré** – TLS 1,1 sur Android
- **Natif** – TLS 1,2 sur Android.

Les nouveaux projets Xamarin ont par défaut l’implémentation native qui prend en charge TLS 1,2 (ce qui est recommandé pour tous les projets). Toutefois, vous pouvez revenir au code managé si nécessaire pour des raisons de compatibilité.

> [!IMPORTANT]
> L’option **mono/managé** a été [supprimée des options de projet iOS et Mac](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md) .
>
> L’option native est toujours utilisée sur les plateformes iOS et Mac.

## <a name="platform-specific-details"></a>Détails spécifiques à la plateforme

Le résumé ci-dessus explique les paramètres au niveau du projet pour l’implémentation de HttpClient et SSL/TLS dans les projets Xamarin. L’implémentation HttpClient peut également être définie dynamiquement dans le code. Pour plus d’informations, reportez-vous à ces guides spécifiques à la plateforme:

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS et Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>Récapitulatif

Les applications doivent utiliser le protocole TLS (Transport Layer Security) 1,2 dans la mesure du possible.
Vous devez mettre à jour les paramètres dans les applications existantes en suivant les instructions de cet article, puis recréer et redéployer sur vos clients.

## <a name="related-links"></a>Liens connexes

- [Sécurité de transport de l’application](~/ios/app-fundamentals/ats.md)
- [Environnement Xamarin. Android](~/android/deploy-test/environment.md)
- [Xamarin cycle 9 (2017 février)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipédia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Notes de publication mono 4,8-prise en charge de TLS 1,2](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [L’explication sur HttpClient, HttpClientHandler et WebRequestHandler](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [Client HTTP (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
