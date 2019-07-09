---
title: Utiliser un Service Web RESTful
description: L’intégration d’un service web dans une application est un scénario courant. Cet article montre comment utiliser un service web RESTful à partir d’une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: adfc3247bce4d40d73580d6ca9baa31df4f57229
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658966"
---
# <a name="consume-a-restful-web-service"></a>Utiliser un Service Web RESTful

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)

_L’intégration d’un service web dans une application est un scénario courant. Cet article montre comment utiliser un service web RESTful à partir d’une application Xamarin.Forms._

Representational State Transfer (REST) est un style d’architecture pour la création de services web. Demandes REST sont établies via HTTP en utilisant les mêmes verbes HTTP qui utilisent de navigateurs web pour récupérer les pages web et d’envoyer des données aux serveurs. Les verbes sont :

- **OBTENIR** : cette opération est utilisée pour récupérer des données à partir du service web.
- **POST** : cette opération est utilisée pour créer un nouvel élément de données sur le service web.
- **PUT** – cette opération est utilisée pour mettre à jour un élément de données sur le service web.
- **CORRECTIF** : cette opération est utilisée pour mettre à jour un élément de données sur le service web en décrivant un ensemble d’instructions sur la façon dont l’élément doit être modifié. Ce verbe n’est pas utilisé dans l’exemple d’application.
- **SUPPRIMER** – cette opération est utilisée pour supprimer un élément de données sur le service web.

API qui adhèrent aux REST de service Web sont appelées des API RESTful et sont définis à l’aide de :

- Un URI de base.
- Méthodes HTTP, telles que GET, POST, PUT, PATCH ou DELETE.
- Un type de média pour les données, tels que JavaScript Objet Notation (JSON).

Les services web rESTful utilisent généralement des messages JSON pour retourner des données au client. JSON est un format d’échange de données textuelles que produit compact charges utiles, ce qui se traduit réduit les besoins en bande passante lors de l’envoi de données. L’exemple d’application utilise open source [bibliothèque NewtonSoft JSON.NET](http://www.newtonsoft.com/json) pour sérialiser et désérialiser les messages.

La simplicité de REST a permis de rendre la méthode principale pour accéder aux services web dans les applications mobiles.

Lorsque l’exemple d’application est exécuté, il se connecte à un service REST hébergé localement, comme illustré dans la capture d’écran suivante :

![](rest-images/portal.png "Exemple d’Application")

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
>
>ATS peuvent être ignorées si elle n’est pas possible d’utiliser le **HTTPS** de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consommation du Service Web

Le service REST est écrit à l’aide d’ASP.NET Core et fournit les opérations suivantes :

|Opération|Méthode HTTP|URI relatif|Paramètres|
|--- |--- |--- |--- |
|Obtenir une liste de tâches|GET|/api/todoitems/|
|Créer un élément de tâche|PUBLIER|/api/todoitems/|TodoItem au format JSON|
|Mettre à jour une tâche|PUT|/api/todoitems/|TodoItem au format JSON|
|Supprimer une tâche|SUPPR|/ API/todoitems / {id}|

La majorité des URI incluent le `TodoItem` ID dans le chemin d’accès. Par exemple, pour supprimer le `TodoItem` dont l’ID est `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, le client envoie une requête DELETE `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Pour plus d’informations sur le modèle de données utilisé dans l’exemple d’application, consultez [les données de modélisation](~/xamarin-forms/data-cloud/web-services/introduction.md).

Lorsque l’infrastructure API Web reçoit une demande, il achemine la demande à une action. Ces actions sont des méthodes publiques simplement dans le `TodoItemsController` classe. Le framework utilise une table de routage pour déterminer l’action à appeler en réponse à une demande, ce qui est illustrée dans l’exemple de code suivant :

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La table de routage contient un modèle d’itinéraire, et lorsque l’infrastructure API Web reçoit une requête HTTP, il tente de faire correspondre l’URI par rapport au modèle d’itinéraire dans la table de routage. Si une correspondance itinéraire Impossible de trouver que le client reçoit une erreur 404 (introuvable). Si un itinéraire correspondant est trouvé, API Web sélectionne le contrôleur et l’action comme suit :

- Pour trouver le contrôleur, API Web ajoute « controller » à la valeur de la *{controller}* variable.
- Pour rechercher l’action, les API Web examine la méthode HTTP et examine les actions de contrôleur sont décorées avec la même méthode HTTP en tant qu’attribut.
- Le *{id}* variable d’espace réservé est mappé à un paramètre d’action.

Le service REST utilise l’authentification de base. Pour plus d’informations, consultez [l’authentification d’un service web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Pour plus d’informations sur le routage de l’API Web ASP.NET, consultez [routage dans ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) sur le site Web ASP.NET. Pour plus d’informations sur la création du service REST à l’aide d’ASP.NET Core, consultez [création de Services principaux pour les Applications mobiles natives](/aspnet/core/mobile/native-mobile-backend/).

Le `HttpClient` classe est utilisée pour envoyer et recevoir des demandes via HTTP. Il fournit des fonctionnalités pour envoyer des requêtes HTTP et recevoir des réponses HTTP à partir d’un URI identifié des ressources. Chaque requête est envoyée en tant qu’une opération asynchrone. Pour plus d’informations sur les opérations asynchrones, consultez [vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md).

Le `HttpResponseMessage` classe représente un message de réponse HTTP reçu du service web après la réalisation d’une requête HTTP. Il contient des informations sur la réponse, y compris le code d’état, des en-têtes et des corps. Le `HttpContent` classe représente le corps HTTP et des en-têtes de contenu, tel que `Content-Type` et `Content-Encoding`. Le contenu peut être lu à l’aide de la `ReadAs` méthodes, telles que `ReadAsStringAsync` et `ReadAsByteArrayAsync`, selon le format des données.

### <a name="creating-the-httpclient-object"></a>Création de l’objet HTTPClient

Le `HttpClient` instance est déclarée au niveau de la classe afin que l’objet est utilisable que pendant tant que l’application a besoin effectuer des demandes HTTP, comme indiqué dans l’exemple de code suivant :

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

Le `HttpClient.GetAsync` méthode est utilisée pour envoyer la demande GET au service web spécifié par l’URI et la réponse du service web, comme indiqué dans l’exemple de code suivant :

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

Le service REST envoie un code d’état HTTP le `HttpResponseMessage.IsSuccessStatusCode` propriété, pour indiquer si la requête HTTP a réussi ou échoué. Pour cette opération le reste service envoie le code d’état HTTP 200 (OK) dans la réponse, ce qui indique que la demande a réussi et que les informations demandées dans la réponse.

Si l’opération HTTP a réussi, le contenu de la réponse est en lecture pour l’affichage. Le `HttpResponseMessage.Content` propriété représente le contenu de la réponse HTTP et le `HttpContent.ReadAsStringAsync` méthode écrit de façon asynchrone le contenu HTTP vers une chaîne. Ce contenu est ensuite converti à partir de JSON pour un `List` de `TodoItem` instances.

### <a name="creating-data"></a>Création de données

Le `HttpClient.PostAsync` méthode est utilisée pour envoyer la demande POST au service web spécifié par l’URI, puis de recevoir la réponse du service web, comme indiqué dans l’exemple de code suivant :

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

Le `TodoItem` instance est convertie en une charge utile JSON pour l’envoi au service web. Cette charge utile est ensuite incorporée dans le corps du contenu HTTP qui sera envoyé au service web avant de la demande est effectuée avec la `PostAsync` (méthode).

Le service REST envoie un code d’état HTTP le `HttpResponseMessage.IsSuccessStatusCode` propriété, pour indiquer si la requête HTTP a réussi ou échoué. Les réponses courantes pour cette opération sont :

- **201 (créé)** – la demande a provoqué une nouvelle ressource créée avant que la réponse a été envoyée.
- **400 (demande incorrecte)** – la demande n’est pas comprise par le serveur.
- **409 (conflit)** – la demande n’a pas pu être exécutée en raison d’un conflit sur le serveur.

### <a name="updating-data"></a>Mise à jour des données

Le `HttpClient.PutAsync` méthode est utilisée pour envoyer la demande PUT au service web spécifié par l’URI et la réponse du service web, comme indiqué dans l’exemple de code suivant :

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```
L’opération de la `PutAsync` méthode est identique à la `PostAsync` méthode qui est utilisée pour créer des données dans le service web. Toutefois, les réponses possibles envoyés par le service web diffèrent.

Le service REST envoie un code d’état HTTP le `HttpResponseMessage.IsSuccessStatusCode` propriété, pour indiquer si la requête HTTP a réussi ou échoué. Les réponses courantes pour cette opération sont :

- **204 (aucun contenu)** : la demande a été correctement traitée et que la réponse vide est intentionnelle.
- **400 (demande incorrecte)** – la demande n’est pas comprise par le serveur.
- **404 (introuvable)** – la ressource demandée n’existe pas sur le serveur.

### <a name="deleting-data"></a>Suppression de données

Le `HttpClient.DeleteAsync` méthode est utilisée pour envoyer la demande de suppression pour le service web spécifié par l’URI et la réponse du service web, comme indiqué dans l’exemple de code suivant :

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

Le service REST envoie un code d’état HTTP le `HttpResponseMessage.IsSuccessStatusCode` propriété, pour indiquer si la requête HTTP a réussi ou échoué. Les réponses courantes pour cette opération sont :

- **204 (aucun contenu)** : la demande a été correctement traitée et que la réponse vide est intentionnelle.
- **400 (demande incorrecte)** – la demande n’est pas comprise par le serveur.
- **404 (introuvable)** – la ressource demandée n’existe pas sur le serveur.

## <a name="related-links"></a>Liens associés

- [Création de services backend pour les applications mobiles natives](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
