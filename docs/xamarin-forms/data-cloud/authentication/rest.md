---
titre : « authentifier un service Web RESTful » : «l’authentification de base permet d’accéder aux ressources uniquement aux clients qui disposent des informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources de service Web RESTful.
ms. Prod : xamarin ms. AssetID : 7B5FFDC4-F2AA-4B12-A30A-1DACC7FECBF1 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 01/22/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="authenticate-a-restful-web-service"></a>Authentifier un service Web RESTful

_HTTP prend en charge l’utilisation de plusieurs mécanismes d’authentification pour contrôler l’accès aux ressources. L’authentification de base permet d’accéder aux ressources uniquement pour les clients disposant des informations d’identification correctes. Cet article explique comment utiliser l’authentification de base pour protéger l’accès aux ressources de service Web RESTful._

> [!NOTE]
> Dans iOS 9 et versions ultérieures, application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Dans la mesure où ATS est activé par défaut dans les applications générées pour iOS 9, toutes les connexions sont soumises aux exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, elles échouent avec une exception.
> L’ATS peut être exclu de s’il n’est pas possible d’utiliser le `HTTPS` protocole et la communication sécurisée pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="authenticating-users-over-http"></a>Authentification des utilisateurs via HTTP

L’authentification de base est le mécanisme d’authentification le plus simple pris en charge par HTTP, et implique que le client envoie le nom d’utilisateur et le mot de passe sous forme de texte encodé en base64 non chiffré. Elle fonctionne de la manière suivante :

- Si un service Web reçoit une demande pour une ressource protégée, il rejette la demande avec un code d’état HTTP 401 (accès refusé) et définit l’en-tête de réponse WWW-Authenticate, comme indiqué dans le diagramme suivant :

![](rest-images/basic-authentication-fail.png "Basic Authentication Failing")

- Si un service Web reçoit une demande pour une ressource protégée, avec l' `Authorization` en-tête correctement défini, le service Web répond avec un code d’état HTTP 200, qui indique que la demande a réussi et que les informations demandées se trouvent dans la réponse. Ce scénario est illustré dans le diagramme suivant :

![](rest-images/basic-authentication-success.png "Basic Authentication Succeeding")

> [!NOTE]
> L’authentification de base ne doit être utilisée que sur une connexion HTTPs. Lorsqu’il est utilisé sur une connexion HTTP, l' `Authorization` en-tête peut facilement être décodé si le trafic HTTP est capturé par une personne malveillante.

## <a name="specifying-basic-authentication-in-a-web-request"></a>Spécification de l’authentification de base dans une requête Web

L’utilisation de l’authentification de base est spécifiée comme suit :

1. La chaîne « Basic » est ajoutée à l' `Authorization` en-tête de la demande.
1. Le nom d’utilisateur et le mot de passe sont combinés dans une chaîne au format « nom d’utilisateur : mot de passe », qui est ensuite encodé en base64 et ajouté à l' `Authorization` en-tête de la demande.

Par conséquent, avec un nom d’utilisateur « XamarinUser » et un mot de passe « XamarinPassword », l’en-tête devient :

```csharp
Authorization: Basic WGFtYXJpblVzZXI6WGFtYXJpblBhc3N3b3Jk
```

La `HttpClient` classe peut définir la `Authorization` valeur d’en-tête sur la `HttpClient.DefaultRequestHeaders.Authorization` propriété. Étant donné que l' `HttpClient` instance existe dans plusieurs requêtes, l' `Authorization` en-tête doit être défini une seule fois, plutôt que lors de l’exécution de chaque demande, comme indiqué dans l’exemple de code suivant :

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

Quand une demande est effectuée à une opération de service Web, la demande est signée avec l' `Authorization` en-tête, indiquant si l’utilisateur est autorisé ou non à appeler l’opération.

> [!IMPORTANT]
> Alors que ce code stocke les informations d’identification en tant que constantes, elles ne doivent pas être stockées dans un format non sécurisé dans une application publiée.

## <a name="processing-the-authorization-header-server-side"></a>Traitement du côté serveur d’en-tête d’autorisation

Le service REST doit décorer chaque action avec l' `[BasicAuthentication]` attribut. Cet attribut est utilisé pour analyser l' `Authorization` en-tête et déterminer si les informations d’identification encodées en base64 sont valides en les comparant aux valeurs stockées dans *Web.config*. Bien que cette approche soit adaptée à un exemple de service, elle nécessite une extension pour un service Web public.

Dans le module d’authentification de base utilisé par IIS, les utilisateurs sont authentifiés par rapport à leurs informations d’identification Windows. Par conséquent, les utilisateurs doivent disposer de comptes sur le domaine du serveur. Toutefois, le modèle d’authentification de base peut être configuré pour autoriser l’authentification personnalisée, où les comptes d’utilisateurs sont authentifiés par rapport à une source externe, telle qu’une base de données. Pour plus d’informations [, consultez authentification de base dans API Web ASP.net](https://www.asp.net/web-api/overview/security/basic-authentication) sur le site Web ASP.net.

> [!NOTE]
> L’authentification de base n’a pas été conçue pour gérer la déconnexion. Par conséquent, l’approche d’authentification de base standard pour la déconnexion consiste à mettre fin à la session.

## <a name="related-links"></a>Liens connexes

- [Utilisation d’un service Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
