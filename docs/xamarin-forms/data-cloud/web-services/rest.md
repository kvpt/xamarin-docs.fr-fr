---
title: Utilisation d’un service Web RESTful
description: L’intégration d’un service Web dans une application est un scénario courant. Cet article explique comment utiliser un service Web RESTful à partir d’une application Xamarin. Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: 6ccbb24be8c03d634c884853fb0ec339d49cf49d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029552"
---
# <a name="consume-a-restful-web-service"></a>Utilisation d’un service Web RESTful

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_L’intégration d’un service Web dans une application est un scénario courant. Cet article explique comment utiliser un service Web RESTful à partir d’une application Xamarin. Forms._

REST est un style architectural pour la création de services Web. Les requêtes REST sont effectuées via HTTP à l’aide des verbes HTTP utilisés par les navigateurs Web pour récupérer des pages Web et envoyer des données aux serveurs. Les verbes sont les suivants :

- **Obtenir** : cette opération est utilisée pour récupérer des données du service Web.
- **Après** : cette opération est utilisée pour créer un nouvel élément de données sur le service Web.
- **Put** : cette opération est utilisée pour mettre à jour un élément de données sur le service Web.
- **Correctif** : cette opération est utilisée pour mettre à jour un élément de données sur le service Web en décrivant un ensemble d’instructions sur la façon dont l’élément doit être modifié. Ce verbe n’est pas utilisé dans l’exemple d’application.
- **Supprimer** : cette opération permet de supprimer un élément de données sur le service Web.

Les API de service Web qui adhèrent au repos sont appelées API RESTful et sont définies à l’aide de :

- URI de base.
- Méthodes HTTP, telles que l’extraction, la publication, la mise à jour ou la suppression.
- Type de média pour les données, tel que JavaScript Object Notation (JSON).

Les services Web RESTful utilisent généralement des messages JSON pour retourner des données au client. JSON est un format d’échange de données textuel qui produit des charges utiles compactes, ce qui entraîne une réduction des besoins en bande passante lors de l’envoi de données. L’exemple d’application utilise la bibliothèque open source [NewtonSoft JSON.net](https://www.newtonsoft.com/json) pour sérialiser et désérialiser les messages.

La simplicité de REST a aidé à en faire la principale méthode pour accéder aux services Web dans les applications mobiles.

Lorsque l’exemple d’application est exécuté, il se connecte à un service REST hébergé localement, comme illustré dans la capture d’écran suivante :

![](rest-images/portal.png "Sample Application")

> [!NOTE]
> Dans iOS 9 et versions ultérieures, application transport Security (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Dans la mesure où ATS est activé par défaut dans les applications générées pour iOS 9, toutes les connexions sont soumises aux exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, elles échouent avec une exception.
>
>L’ATS peut être désactivée s’il n’est pas possible d’utiliser le protocole **https** et la communication sécurisée pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consommation du service Web

Le service REST est écrit à l’aide de ASP.NET Core et fournit les opérations suivantes :

|Opération|Méthode HTTP|URI relatif|Paramètres|
|--- |--- |--- |--- |
|Obtenir une liste de tâches|GET|/api/todoitems/|
|Créer un nouvel élément de tâche|Publier|/api/todoitems/|TodoItem au format JSON|
|Mettre à jour une tâche|POSÉ|/api/todoitems/|TodoItem au format JSON|
|Supprimer une tâche|SUPPR|/api/todoitems/{id}|

La majorité des URI incluent l’ID de `TodoItem` dans le chemin d’accès. Par exemple, pour supprimer le `TodoItem` dont l’ID est `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, le client envoie une demande de suppression à `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Pour plus d’informations sur le modèle de données utilisé dans l’exemple d’application, consultez [modélisation des données](~/xamarin-forms/data-cloud/web-services/introduction.md).

Lorsque l’infrastructure de l’API Web reçoit une requête, elle route la demande vers une action. Ces actions sont simplement des méthodes publiques dans la classe `TodoItemsController`. L’infrastructure utilise une table de routage pour déterminer l’action à appeler en réponse à une requête, qui est illustrée dans l’exemple de code suivant :

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La table de routage contient un modèle d’itinéraire et, lorsque l’infrastructure de l’API Web reçoit une requête HTTP, elle tente de faire correspondre l’URI au modèle de routage dans la table de routage. Si une route correspondante est introuvable, le client reçoit une erreur 404 (introuvable). Si un itinéraire correspondant est trouvé, l’API Web sélectionne le contrôleur et l’action comme suit :

- Pour rechercher le contrôleur, l’API Web ajoute « Controller » à la valeur de la variable *{Controller}* .
- Pour trouver l’action, l’API Web examine la méthode HTTP et examine les actions du contrôleur qui sont décorées avec la même méthode HTTP en tant qu’attribut.
- La variable d’espace réservé *{ID}* est mappée à un paramètre d’action.

Le service REST utilise l’authentification de base. Pour plus d’informations, consultez [authentification d’un service Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Pour plus d’informations sur le routage de API Web ASP.NET, consultez [routage dans API Web ASP.net](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) sur le site Web ASP.net. Pour plus d’informations sur la création du service REST à l’aide de ASP.NET Core, consultez [création de services principaux pour les applications mobiles natives](/aspnet/core/mobile/native-mobile-backend/).

La classe `HttpClient` est utilisée pour envoyer et recevoir des demandes via HTTP. Il fournit des fonctionnalités permettant d’envoyer des requêtes HTTP et de recevoir des réponses HTTP d’une ressource identifiée par un URI. Chaque demande est envoyée en tant qu’opération asynchrone. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

La classe `HttpResponseMessage` représente un message de réponse HTTP reçu du service Web après l’établissement d’une requête HTTP. Elle contient des informations sur la réponse, notamment le code d’État, les en-têtes et tout corps. La classe `HttpContent` représente le corps HTTP et les en-têtes de contenu, tels que `Content-Type` et `Content-Encoding`. Le contenu peut être lu à l’aide de l’une des méthodes de `ReadAs`, telles que `ReadAsStringAsync` et `ReadAsByteArrayAsync`, en fonction du format des données.

### <a name="creating-the-httpclient-object"></a>Création de l’objet HTTPClient

L’instance de `HttpClient` est déclarée au niveau de la classe afin que l’objet soit valable tant que l’application doit effectuer des requêtes HTTP, comme illustré dans l’exemple de code suivant :

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    _client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>Récupération de données

La méthode `HttpClient.GetAsync` est utilisée pour envoyer la requête d’extraction au service Web spécifié par l’URI, puis recevoir la réponse du service Web, comme illustré dans l’exemple de code suivant :

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  var response = await _client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

Le service REST envoie un code d’état HTTP dans la propriété `HttpResponseMessage.IsSuccessStatusCode`, pour indiquer si la requête HTTP a réussi ou échoué. Pour cette opération, le service REST envoie le code d’état HTTP 200 (OK) dans la réponse, ce qui indique que la demande a réussi et que les informations demandées se trouvent dans la réponse.

Si l’opération HTTP a réussi, le contenu de la réponse est Read, à des fins d’affichage. La propriété `HttpResponseMessage.Content` représente le contenu de la réponse HTTP, et la méthode `HttpContent.ReadAsStringAsync` écrit de façon asynchrone le contenu HTTP dans une chaîne. Ce contenu est ensuite converti de JSON en `List` d’instances de `TodoItem`.

### <a name="creating-data"></a>Création de données

La méthode `HttpClient.PostAsync` est utilisée pour envoyer la demande de publication au service Web spécifié par l’URI, puis pour recevoir la réponse du service Web, comme illustré dans l’exemple de code suivant :

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await _client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");

  }
  ...
}
```

L’instance de `TodoItem` est convertie en charge utile JSON pour l’envoi au service Web. Cette charge utile est ensuite incorporée dans le corps du contenu HTTP qui sera envoyé au service Web avant que la demande ne soit effectuée avec la méthode `PostAsync`.

Le service REST envoie un code d’état HTTP dans la propriété `HttpResponseMessage.IsSuccessStatusCode`, pour indiquer si la requête HTTP a réussi ou échoué. Les réponses courantes pour cette opération sont les suivantes :

- **201 (créé)** : la demande a entraîné la création d’une nouvelle ressource avant l’envoi de la réponse.
- **400 (requête INcorrecte)** : la demande n’est pas comprise par le serveur.
- **409 (conflit)** : la demande n’a pas pu être exécutée en raison d’un conflit sur le serveur.

### <a name="updating-data"></a>Mise à jour des données

La méthode `HttpClient.PutAsync` est utilisée pour envoyer la requête PUT au service Web spécifié par l’URI, puis recevoir la réponse du service Web, comme illustré dans l’exemple de code suivant :

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```

Le fonctionnement de la méthode `PutAsync` est identique à la méthode `PostAsync` utilisée pour créer des données dans le service Web. Toutefois, les réponses possibles envoyées à partir du service Web diffèrent.

Le service REST envoie un code d’état HTTP dans la propriété `HttpResponseMessage.IsSuccessStatusCode`, pour indiquer si la requête HTTP a réussi ou échoué. Les réponses courantes pour cette opération sont les suivantes :

- **204 (aucun contenu)** : la demande a été traitée avec succès et la réponse est intentionnellement vide.
- **400 (requête INcorrecte)** : la demande n’est pas comprise par le serveur.
- **404 (INtrouvable)** : la ressource demandée n’existe pas sur le serveur.

### <a name="deleting-data"></a>Suppression de données

La méthode `HttpClient.DeleteAsync` est utilisée pour envoyer la demande de suppression au service Web spécifié par l’URI, puis recevoir la réponse du service Web, comme illustré dans l’exemple de code suivant :

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  var response = await _client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

Le service REST envoie un code d’état HTTP dans la propriété `HttpResponseMessage.IsSuccessStatusCode`, pour indiquer si la requête HTTP a réussi ou échoué. Les réponses courantes pour cette opération sont les suivantes :

- **204 (aucun contenu)** : la demande a été traitée avec succès et la réponse est intentionnellement vide.
- **400 (requête INcorrecte)** : la demande n’est pas comprise par le serveur.
- **404 (INtrouvable)** : la ressource demandée n’existe pas sur le serveur.

## <a name="related-links"></a>Liens associés

- [Création de services backend pour les applications mobiles natives](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
