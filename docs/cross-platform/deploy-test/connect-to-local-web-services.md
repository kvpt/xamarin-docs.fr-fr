---
title: Connexion aux services web locaux à partir de simulateurs iOS et d’émulateurs Android
description: Cet article explique comment une application mobile Xamarin en cours d’exécution dans le simulateur iOS ou l’émulateur Android peut consommer un service web ASP.NET Core exécuté localement.
ms.prod: xamarin
ms.assetid: FD8FE199-898B-4841-8041-CC9CA1A00917
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
ms.openlocfilehash: 29261f2ef6366c0dac8ac82e63584366a5cca0b0
ms.sourcegitcommit: 233aaa1ac3d8f40c09b6daf6d944ea0b4cbee381
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135282"
---
# <a name="connect-to-local-web-services-from-ios-simulators-and-android-emulators"></a>Connexion aux services web locaux à partir de simulateurs iOS et d’émulateurs Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/)

De nombreuses applications mobiles consomment des services web. Pendant la phase de développement, il est courant de déployer un service web localement et de le consommer à partir d’une application mobile en cours d’exécution dans le simulateur iOS ou l’émulateur Android. Cela évite d’avoir à déployer le service web sur un point de terminaison hébergé et garantit une expérience de débogage simple, car l’application mobile et le web service mobile sont exécutés localement.

Les applications mobiles en cours d’exécution dans le simulateur iOS ou l’émulateur Android peuvent consommer des services web ASP.NET Core exécutés localement et exposés via HTTP, comme suit :

- Les applications en cours d’exécution dans le simulateur iOS peuvent se connecter aux services web HTTP locaux via l’adresse IP de vos machines ou le nom d’hôte `localhost`. Par exemple, si un service web HTTP local expose une opération GET via l’URI relatif `/api/todoitems/`, une application en cours d’exécution dans le simulateur iOS peut consommer l’opération en envoyant une requête GET à `http://localhost:<port>/api/todoitems/`.
- Les applications en cours d’exécution dans l’émulateur Android peuvent se connecter aux services web HTTP locaux via l’adresse `10.0.2.2`, qui représente un alias vers votre interface de bouclage hôte (`127.0.0.1` sur votre ordinateur de développement). Par exemple, si un service web HTTP local expose une opération GET via l’URI relatif `/api/todoitems/`, une application en cours d’exécution dans l’émulateur Android peut consommer l’opération en envoyant une requête GET à `http://10.0.2.2:<port>/api/todoitems/`.

Toutefois, un travail supplémentaire est nécessaire pour qu’une application en cours d’exécution dans le simulateur iOS ou l’émulateur Android puisse consommer un service web local exposé via le protocole HTTPS. Pour ce scénario, le processus est le suivant :

1. Créez un certificat de développement auto-signé sur votre ordinateur. Pour plus d’informations, consultez [Créer un certificat de développement](#create-a-development-certificate).
1. Configurez votre projet pour utiliser la pile réseau `HttpClient` appropriée pour votre version de débogage. Pour plus d’informations, consultez [Configurer votre projet](#configure-your-project).
1. Spécifiez l’adresse de votre ordinateur local. Pour plus d’informations, consultez [Spécifier l’adresse de l’ordinateur local](#specify-the-local-machine-address).
1. Contournez la vérification de sécurité du certificat de développement local. Pour plus d’informations, consultez [Contourner la vérification de sécurité du certificat](#bypass-the-certificate-security-check).

Chacun de ces points va être abordé à tour de rôle.

## <a name="create-a-development-certificate"></a>Créer un certificat de développement

L’installation du kit SDK .NET Core installe le certificat de développement ASP.NET Core HTTPS dans le magasin de certificats de l’utilisateur local. Mais même s’il a été installé, le certificat n’est pas approuvé. Pour approuver le certificat, effectuez cette étape unique afin d’exécuter l’outil dotnet `dev-certs` :

```dotnetcli
dotnet dev-certs https --trust
```

La commande suivante fournit de l’aide sur l’outil `dev-certs` :

```dotnetcli
dotnet dev-certs https --help
```

Sinon, lorsque vous exécutez un projet ASP.NET Core 2.1 (ou version ultérieure) qui utilise HTTPS, Visual Studio détecte si le certificat de développement est manquant et vous propose de l’installer et de l’approuver.

> [!NOTE]
> Le certificat de développement HTTPS ASP.NET Core est auto-signé.

Pour plus d’informations sur l’activation du protocole HTTPS local sur votre ordinateur, consultez [Activer HTTPS localement](/aspnet/core/getting-started#enable-local-https).

## <a name="configure-your-project"></a>Configurer votre projet

Les applications Xamarin en cours d’exécution sur iOS et Android peuvent spécifier quelle pile réseau est utilisée par la classe `HttpClient`, les options étant une pile réseau gérée ou des piles réseau natives. La pile gérée fournit un haut niveau de compatibilité avec le code .NET existant, mais elle se limite à TLS 1.0, peut être plus lente et générer un fichier exécutable de plus grande taille. Les piles natives peuvent être plus rapides et garantir une meilleure sécurité, mais elles n’offrent pas toutes les fonctionnalités de la classe `HttpClient`.

### <a name="ios"></a>iOS

Les applications Xamarin en cours d’exécution sur iOS peuvent utiliser la pile réseau gérée, ou les piles réseau natives `CFNetwork` ou `NSUrlSession`. Par défaut, les projets créés dans nouvelle plateforme iOS utilisent la pile réseau `NSUrlSession` (pour prendre en charge TLS 1.2) et des API natives pour améliorer les performances et générer un fichier exécutable de plus petite taille.

Toutefois, si une application doit se connecter à un service web sécurisé en cours d’exécution localement, pour le test des développeurs, il est plus facile d’utiliser la pile réseau gérée. Par conséquent, il est recommandé de définir des profils de build de simulateur de débogage pour utiliser la pile réseau gérée, et des profils de version Release pour utiliser la pile réseau `NSUrlSession`. Chaque pile réseau peut être définie par programmation ou via un sélecteur dans les options du projet. Pour plus d’informations, consultez [HttpClient et sélecteur d’implémentation de SSL/TLS pour iOS/macOS](~/cross-platform/macios/http-stack.md).

### <a name="android"></a>Android

Les applications Xamarin en cours d’exécution sur Android peuvent utiliser la pile réseau gérée `HttpClient`, ou la pile réseau native `AndroidClientHandler`. Par défaut, les projets créés dans nouvelle plateforme Android utilisent la pile réseau `AndroidClientHandler` (pour prendre en charge TLS 1.2) et des API natives pour améliorer les performances et générer un fichier exécutable de plus petite taille. Pour plus d’informations sur les piles réseau Android, voir [pile httpclient et sélecteur d’implémentation SSL/TLS pour Android](~/android/app-fundamentals/http-stack.md).

## <a name="specify-the-local-machine-address"></a>Spécifier l’adresse de l’ordinateur local

Le simulateur iOS et l’émulateur Android fournissent un accès aux services web sécurisés en cours d’exécution sur votre ordinateur local. Mais ils utilisent chacun une adresse d’ordinateur local différente.

### <a name="ios"></a>iOS

Le simulateur iOS utilise le réseau de l’ordinateur hôte. Les applications en cours d’exécution dans le simulateur peuvent ainsi se connecter aux services web exécutés sur votre ordinateur local via l’adresse IP de vos machines ou le nom d’hôte `localhost`. Par exemple, si un service web sécurisé local expose une opération GET via l’URI relatif `/api/todoitems/`, une application en cours d’exécution dans le simulateur iOS peut consommer l’opération en envoyant une requête GET à `https://localhost:<port>/api/todoitems/`.

> [!NOTE]
> Lorsque vous exécutez une application mobile dans le simulateur iOS à partir de Windows, l’application s’affiche dans le [simulateur iOS à distance pour Windows](~/tools/ios-simulator/index.md). Mais l’application est en cours d’exécution sur le Mac jumelé. Par conséquent, il n’existe aucun accès localhost à un service web en cours d’exécution dans Windows pour une application iOS en cours d’exécution sur un Mac.

### <a name="android"></a>Android

Chaque instance de l’émulateur Android est isolée des interfaces réseau de votre ordinateur de développement et s’exécute derrière un routeur virtuel. Un appareil émulé ne peut donc pas voir votre ordinateur de développement ou d’autres instances de l’émulateur sur le réseau.

Cependant, le routeur virtuel de chaque émulateur gère un espace de réseau spécial qui inclut des adresses préallouées, l’adresse `10.0.2.2` servant d’alias vers votre interface de bouclage hôte (127.0.0.1 sur votre ordinateur de développement). Par conséquent, si un service web sécurisé local expose une opération GET via l’URI relatif `/api/todoitems/`, une application en cours d’exécution dans le simulateur Android peut consommer l’opération en envoyant une requête GET à `https://10.0.2.2:<port>/api/todoitems/`.

### <a name="xamarinforms-example"></a>Exemple Xamarin.Forms

Dans une application Xamarin.Forms, la classe [`Device`](xref:Xamarin.Forms.Device) peut être utilisée pour détecter la plateforme sur laquelle l’application est en cours d’exécution. Le nom d’hôte approprié, qui permet d’accéder aux services web sécurisés locaux, peut être ainsi défini comme suit :

```csharp
public static string BaseAddress =
    Device.RuntimePlatform == Device.Android ? "https://10.0.2.2:5001" : "https://localhost:5001";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

## <a name="bypass-the-certificate-security-check"></a>Contourner la vérification de sécurité du certificat

Toute tentative pour appeler un service web sécurisé local à partir d’une application en cours d’exécution dans le simulateur iOS ou l’émulateur Android entraînera une exception `HttpRequestException`, même si vous utilisez la pile réseau gérée sur chaque plateforme. En effet, le certificat de développement HTTPS local est auto-signé, et les certificats auto-signés ne sont pas approuvés par iOS ou Android.

Par conséquent, il est nécessaire d’ignorer les erreurs SSL lorsqu’une application consomme un service web sécurisé local. Le mécanisme d’accomplissement de cette fonctionnalité est actuellement différent sur iOS et Android.

### <a name="ios"></a>iOS

Les erreurs SSL peuvent être ignorées sur iOS pour les services Web sécurisés locaux, lors de l’utilisation de la pile réseau gérée, en affectant à la propriété `ServicePointManager.ServerCertificateValidationCallback` la valeur d’un rappel qui ignore le résultat de la vérification de la sécurité du certificat pour le certificat de développement HTTPs local :

```csharp
#if DEBUG
    System.Net.ServicePointManager.ServerCertificateValidationCallback += (sender, certificate, chain, sslPolicyErrors) =>
    {
        if (certificate.Issuer.Equals("CN=localhost"))
            return true;
        return sslPolicyErrors == System.Net.Security.SslPolicyErrors.None;
    };
#endif
```

Dans cet exemple de code, le résultat de la validation du certificat du serveur est retourné lorsque le certificat ayant fait l’objet de la validation n’est pas le certificat `localhost`. Pour obtenir ce certificat, le résultat de la validation est ignoré et la valeur `true` est renvoyée, indiquant que le certificat est valide. Ce code doit être ajouté à la méthode `AppDelegate.FinishedLaunching` sur iOS, avant l’appel de la méthode `LoadApplication(new App())`.

> [!NOTE]
> Les piles réseau natives sur iOS ne sont pas raccordées à l' `ServerCertificateValidationCallback`.

### <a name="android"></a>Android

Les erreurs SSL peuvent être ignorées sur Android pour les services Web sécurisés locaux, lors de l’utilisation des piles réseau `AndroidClientHandler` managées et natives, en définissant la propriété `ServerCertificateCustomValidationCallback` sur un objet `HttpClientHandler` sur un rappel qui ignore le résultat de la vérification de la sécurité du certificat pour le certificat de développement HTTPs local :

```csharp
public HttpClientHandler GetInsecureHandler()
{
    var handler = new HttpClientHandler();
    handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) =>
    {
        if (cert.Issuer.Equals("CN=localhost"))
            return true;
        return errors == System.Net.Security.SslPolicyErrors.None;
    };
    return handler;
}
```

Dans cet exemple de code, le résultat de la validation du certificat du serveur est retourné lorsque le certificat ayant fait l’objet de la validation n’est pas le certificat `localhost`. Pour obtenir ce certificat, le résultat de la validation est ignoré et la valeur `true` est renvoyée, indiquant que le certificat est valide. L’objet `HttpClientHandler` résultant doit être passé en tant qu’argument au constructeur `HttpClient`.

## <a name="related-links"></a>Liens connexes

- [TodoREST (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/)
- [Activer HTTPS localement](/aspnet/core/getting-started#enable-local-https)
- [HttpClient et sélecteur d’implémentation SSL/TLS pour iOS/macOS](~/cross-platform/macios/http-stack.md)
- [Pile HttpClient et sélecteur d’implémentation SSL/TLS pour Android](~/android/app-fundamentals/http-stack.md)
