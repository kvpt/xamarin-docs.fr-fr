---
title: Authentifier un Service Web RESTful
description: L’authentification de base fournit l’accès aux ressources aux clients qui ont les informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources du service web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: bf334c7fde8433b7bbe45c8caaf962705c39320d
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650397"
---
# <a name="authenticate-a-restful-web-service"></a>Authentifier un Service Web RESTful

_HTTP prend en charge l’utilisation de plusieurs mécanismes d’authentification pour contrôler l’accès aux ressources. L’authentification de base fournit l’accès aux ressources aux clients qui ont les informations d’identification correctes. Cet article montre comment utiliser l’authentification de base pour protéger l’accès aux ressources du service web RESTful._

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
> ATS peuvent être ignorées si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>L’authentification des utilisateurs via HTTP

L’authentification de base est le mécanisme d’authentification la plus simple pris en charge par HTTP et implique le client qui envoie le nom d’utilisateur et le mot de passe sous forme de texte de non chiffré en base64 encodé. Il fonctionne comme suit :

- Si un service web reçoit une demande pour une ressource protégée, il rejette la demande avec un code d’état HTTP 401 (accès refusé) et définit l’en-tête de réponse WWW-Authenticate, comme illustré dans le diagramme suivant :

![](rest-images/basic-authentication-fail.png "Échec de l’authentification de base")

- Si un service web reçoit une demande pour une ressource protégée, avec le `Authorization` en-tête correctement défini, le web service répond avec un code d’état HTTP 200, ce qui indique que la demande a réussi et que les informations demandées figurent dans la réponse. Ce scénario est illustré dans le diagramme suivant :

![](rest-images/basic-authentication-success.png "Réussite de l’authentification de base")

> [!NOTE]
> L’authentification de base doit uniquement être utilisée via une connexion HTTPS. Lorsqu’il est utilisé sur une connexion HTTP, le <code>Authorization</code> en-tête peut facilement être décodé si le trafic HTTP est capturé par une personne malveillante.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Spécification de l’authentification de base dans une requête Web

Utilisation de l’authentification de base est spécifiée comme suit :

1. La chaîne « Basic » est ajouté à la `Authorization` en-tête de la requête.
1. Le nom d’utilisateur et le mot de passe sont combinés en une chaîne au format « username : Password », qui est ensuite au format base64 encodé et ajouté à la `Authorization` en-tête de la requête.

Par conséquent, avec un nom d’utilisateur de « XamarinUser » et un mot de passe de « XamarinPassword », l’en-tête devient :

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

Le `HttpClient` classe peut définir le `Authorization` valeur d’en-tête sur le `HttpClient.DefaultRequestHeaders.Authorization` propriété. Étant donné que le `HttpClient` instance existe entre plusieurs demandes, le `Authorization` en-tête ne doit être définie une seule fois, plutôt que quand effectuer chaque demande, comme indiqué dans l’exemple de code suivant :

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    var authData = string.Format ("{0}:{1}", Constants.Username, Constants.Password);
    var authHeaderValue = Convert.ToBase64String (Encoding.UTF8.GetBytes (authData));

    _client = new HttpClient ();
    _client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue ("Basic", authHeaderValue);
  }
  ...
}
```

Puis lorsqu’une demande est faite à une opération de service web la demande est signée avec la `Authorization` en-tête, qui indique si l’utilisateur a l’autorisation d’appeler l’opération.

> [!NOTE]
> Bien que ce code stocke les informations d’identification en tant que constantes, ils ne doivent pas être stockées dans un format non sécurisé dans une application publiée. Le [Xamarith.Auth](https://www.nuget.org/packages/Xamarin.Auth/) NuGet fournit des fonctionnalités de stockage sécurisé des informations d’identification. Pour plus d’informations, consultez [stockage et la récupération des informations de compte sur les appareils](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="processing-the-authorization-header-server-side"></a>Traitement côté serveur en-tête d’autorisation

Le service REST doit décorer chaque action avec le `[BasicAuthentication]` attribut. Cet attribut est utilisé pour analyser le `Authorization` en-tête et déterminer si les informations d’identification codées en base64 valides en les comparant aux valeurs stockées dans *Web.config*. Bien que cette approche est appropriée pour un exemple de service, il nécessite l’extension pour un service web de destinées au public.

Dans le module d’authentification de base utilisé par IIS, les utilisateurs sont authentifiés par rapport à leurs informations d’identification Windows. Par conséquent, les utilisateurs doivent avoir des comptes sur le domaine du serveur. Toutefois, le modèle de l’authentification de base peut être configuré pour autoriser l’authentification personnalisée, où les comptes d’utilisateurs sont authentifiés par rapport à une source externe, comme une base de données. Pour plus d’informations, consultez [l’authentification de base dans ASP.NET Web API](http://www.asp.net/web-api/overview/security/basic-authentication) sur le site Web ASP.NET.

> [!NOTE]
> L’authentification de base n’est pas conçue pour gérer la déconnexion. Par conséquent, l’approche de l’authentification de base standard permettant de se déconnecter consiste à terminer la session.

## <a name="related-links"></a>Liens associés

- [Utiliser un service web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
