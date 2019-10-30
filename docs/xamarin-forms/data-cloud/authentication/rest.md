---
title: Authentifier un service Web RESTful
description: L’authentification de base permet d’accéder aux ressources uniquement pour les clients disposant des informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources de service Web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 23516603633116a8e28ae33004bdb6fc8764ec21
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032820"
---
# <a name="authenticate-a-restful-web-service"></a>Authentifier un service Web RESTful

_HTTP prend en charge l’utilisation de plusieurs mécanismes d’authentification pour contrôler l’accès aux ressources. L’authentification de base permet d’accéder aux ressources uniquement pour les clients disposant des informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources de service Web RESTful._

> [!NOTE]
> Dans iOS 9 et versions ultérieures, application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Dans la mesure où ATS est activé par défaut dans les applications générées pour iOS 9, toutes les connexions sont soumises aux exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, elles échouent avec une exception.
> L’ATS peut être exclu de s’il n’est pas possible d’utiliser le protocole `HTTPS` et de sécuriser la communication pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Authentification des utilisateurs via HTTP

L’authentification de base est le mécanisme d’authentification le plus simple pris en charge par HTTP, et implique que le client envoie le nom d’utilisateur et le mot de passe sous forme de texte encodé en base64 non chiffré. Il fonctionne comme suit :

- Si un service Web reçoit une demande pour une ressource protégée, il rejette la demande avec un code d’état HTTP 401 (accès refusé) et définit l’en-tête de réponse WWW-Authenticate, comme indiqué dans le diagramme suivant :

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Si un service Web reçoit une demande pour une ressource protégée, avec l’en-tête de `Authorization` correctement défini, le service Web répond avec un code d’état HTTP 200, qui indique que la demande a réussi et que les informations demandées se trouvent dans la réponse. Ce scénario est illustré dans le diagramme suivant :

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> L’authentification de base ne doit être utilisée que sur une connexion HTTPs. Lorsqu’il est utilisé sur une connexion HTTP, l’en-tête `Authorization` peut facilement être décodé si le trafic HTTP est capturé par une personne malveillante.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Spécification de l’authentification de base dans une requête Web

L’utilisation de l’authentification de base est spécifiée comme suit :

1. La chaîne « Basic » est ajoutée à l’en-tête `Authorization` de la requête.
1. Le nom d’utilisateur et le mot de passe sont combinés dans une chaîne au format « nom d’utilisateur : mot de passe », qui est ensuite encodé en base64 et ajouté à l’en-tête `Authorization` de la requête.

Par conséquent, avec un nom d’utilisateur « XamarinUser » et un mot de passe « XamarinPassword », l’en-tête devient :

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La classe `HttpClient` peut définir la valeur d’en-tête `Authorization` sur la propriété `HttpClient.DefaultRequestHeaders.Authorization`. Étant donné que l’instance de `HttpClient` existe dans plusieurs requêtes, l’en-tête `Authorization` ne doit être défini qu’une seule fois, plutôt que lors de l’exécution de chaque demande, comme illustré dans l’exemple de code suivant :

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

Ensuite, lorsqu’une demande est effectuée à une opération de service Web, la demande est signée avec l’en-tête `Authorization`, indiquant si l’utilisateur est autorisé ou non à appeler l’opération.

> [!NOTE]
> Alors que ce code stocke les informations d’identification en tant que constantes, elles ne doivent pas être stockées dans un format non sécurisé dans une application publiée. Le NuGet [Xamarith. auth](https://www.nuget.org/packages/Xamarin.Auth/) fournit des fonctionnalités pour stocker de manière sécurisée les informations d’identification. Pour plus d’informations [, consultez stockage et récupération d’informations de compte sur des appareils](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="processing-the-authorization-header-server-side"></a>Traitement du côté serveur d’en-tête d’autorisation

Le service REST doit décorer chaque action avec l’attribut `[BasicAuthentication]`. Cet attribut est utilisé pour analyser l’en-tête `Authorization` et déterminer si les informations d’identification encodées en base64 sont valides en les comparant aux valeurs stockées dans *Web. config*. Bien que cette approche soit adaptée à un exemple de service, elle nécessite une extension pour un service Web public.

Dans le module d’authentification de base utilisé par IIS, les utilisateurs sont authentifiés par rapport à leurs informations d’identification Windows. Par conséquent, les utilisateurs doivent disposer de comptes sur le domaine du serveur. Toutefois, le modèle d’authentification de base peut être configuré pour autoriser l’authentification personnalisée, où les comptes d’utilisateurs sont authentifiés par rapport à une source externe, telle qu’une base de données. Pour plus d’informations [, consultez authentification de base dans API Web ASP.net](https://www.asp.net/web-api/overview/security/basic-authentication) sur le site Web ASP.net.

> [!NOTE]
> L’authentification de base n’a pas été conçue pour gérer la déconnexion. Par conséquent, l’approche d’authentification de base standard pour la déconnexion consiste à mettre fin à la session.

## <a name="related-links"></a>Liens associés

- [Utilisation d’un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
