---
title: Authentifier un service Web RESTful
description: L’authentification de base donne accès aux ressources uniquement aux clients qui ont les informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources de service Web RESTful.
ms.prod: xamarin
ms.assetid: 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 65606b72c3944809a09b8c70b9cdbb5524e0f856
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388588"
---
# <a name="authenticate-a-restful-web-service"></a>Authentifier un service Web RESTful

_HTTP soutient l’utilisation de plusieurs mécanismes d’authentification pour contrôler l’accès aux ressources. L’authentification de base donne accès aux ressources uniquement aux clients qui ont les informations d’identification correctes. Cet article montre comment utiliser l’authentification de base pour protéger l’accès aux ressources de service Web RESTful._

> [!NOTE]
> Dans iOS 9 et plus, App Transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (comme le serveur back-end de l’application) et l’application, empêchant ainsi la divulgation accidentelle d’informations sensibles. Comme ATS est activée par défaut dans les applications conçues pour iOS 9, toutes les connexions seront soumises aux exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, elles échoueront à titre exception.
> ATS peut être exclu s’il n’est pas possible d’utiliser le protocole et de sécuriser la `HTTPS` communication pour les ressources Internet. Ceci peut être réalisé en mettant à jour le fichier **Info.plist** de l’application. Pour plus d’informations voir [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Authentifier les utilisateurs sur HTTP

L’authentification de base est le mécanisme d’authentification le plus simple pris en charge par HTTP, et implique le client d’envoyer le nom d’utilisateur et le mot de passe comme texte codé base64 non crypté. Elle fonctionne de la manière suivante :

- Si un service Web reçoit une demande de ressource protégée, il rejette la demande avec un code d’état HTTP 401 (accès refusé) et définit l’en-tête de réponse WWW-Authenticate, comme indiqué dans le diagramme suivant :

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Si un service Web reçoit une demande `Authorization` pour une ressource protégée, avec l’en-tête correctement défini, le service Web répond avec un code de statut HTTP 200, ce qui indique que la demande a réussi et que les informations demandées sont dans la réponse. Ce scénario est affiché dans le diagramme suivant :

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> L’authentification de base ne doit être utilisée que sur une connexion HTTPS. Lorsqu’il est utilisé `Authorization` sur une connexion HTTP, l’en-tête peut facilement être décodé si le trafic HTTP est capturé par un attaquant.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Spécifier l’authentification de base dans une demande Web

L’utilisation de l’authentification de base est spécifiée comme suit :

1. La chaîne "Basic" est `Authorization` ajoutée à l’en-tête de la demande.
1. Le nom d’utilisateur et le mot de passe sont combinés en une chaîne avec le format `Authorization` "nom d’utilisateur:mot de passe", qui est ensuite base64 codé et ajouté à l’en-tête de la demande.

Par conséquent, avec un nom d’utilisateur de 'XamarinUser' et un mot de passe de 'XamarinPassword', l’en-tête devient:

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La `HttpClient` classe peut `Authorization` définir la `HttpClient.DefaultRequestHeaders.Authorization` valeur d’en-tête sur la propriété. Étant `HttpClient` donné que l’instance `Authorization` existe dans plusieurs demandes, l’en-tête n’a besoin d’être défini qu’une seule fois, plutôt que lors de chaque demande, comme le montre l’exemple de code suivant :

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

Ensuite, lorsqu’une demande est faite à une `Authorization` opération de service Web, la demande est signée avec l’en-tête, indiquant si l’utilisateur a ou non la permission d’invoquer l’opération.

> [!IMPORTANT]
> Bien que ce code stocke les informations d’identification comme des constantes, ils ne doivent pas être stockés dans un format non sécurisé dans une application publiée.

## <a name="processing-the-authorization-header-server-side"></a>Traitement du côté serveur d’en-tête d’autorisation

Le service REST doit décorer chaque action avec l’attribut. `[BasicAuthentication]` Cet attribut est utilisé pour `Authorization` analyser l’en-tête et déterminer si les informations d’identification codées base64 sont valides en les comparant aux valeurs stockées dans *Web.config*. Bien que cette approche soit adaptée à un service d’échantillon, elle nécessite une extension pour un service Web orienté vers le public.

Dans le module d’authentification de base utilisé par l’IIS, les utilisateurs sont authentifiés par rapport à leurs informations d’identification Windows. Par conséquent, les utilisateurs doivent avoir des comptes sur le domaine du serveur. Cependant, le modèle d’authentification de base peut être configuré pour permettre l’authentification personnalisée, lorsque les comptes d’utilisateurs sont authentifiés par rapport à une source externe, comme une base de données. Pour plus d’informations, consultez [Basic Authentication in ASP.NET Web API](https://www.asp.net/web-api/overview/security/basic-authentication) sur le site web ASP.NET.

> [!NOTE]
> L’authentification de base n’a pas été conçue pour gérer l’enregistrement. Par conséquent, l’approche standard d’authentification de base pour la connexion est de mettre fin à la session.

## <a name="related-links"></a>Liens connexes

- [Consommer un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
