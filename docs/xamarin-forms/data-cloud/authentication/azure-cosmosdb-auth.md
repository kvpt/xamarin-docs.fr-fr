---
title: Authentifier les utilisateurs avec une base de données de documents Azure Cosmos DB et Xamarin.Forms
description: Cet article explique comment combiner le contrôle d’accès avec les collections partitionnées Azure Cosmos DB, afin qu’un utilisateur ne puisse accéder à ses propres documents que dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e79e647d64103b6d257de7233f488899bcaff40
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388601"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Authentifier les utilisateurs avec une base de données de documents Azure Cosmos DB et Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Les bases de données de documents Azure Cosmos DB prennent en charge les collections partitionnées, qui peuvent s’étendre sur plusieurs serveurs et partitions, tout en soutenant le stockage et le débit illimités. Cet article explique comment combiner le contrôle d’accès avec les collections partitionnées, de sorte qu’un utilisateur ne peut accéder à ses propres documents que dans une application Xamarin.Forms._

## <a name="overview"></a>Vue d’ensemble

Une clé de partition doit être spécifiée lors de la création d’une collection partitionnée, et les documents avec la même clé de partition seront stockés dans la même partition. Par conséquent, la spécifier l’identité de l’utilisateur comme clé de partition entraînera une collection partitionnée qui ne stockera que des documents pour cet utilisateur. Cela garantit également que la base de données de documents Azure Cosmos DB sera à l’échelle à mesure que le nombre d’utilisateurs et d’éléments augmentera.

L’accès doit être accordé à toute collection, et le modèle de contrôle d’accès À l’API SQL définit deux types de constructions d’accès :

- **Les clés maîtresses** permettent un accès administratif complet à toutes les ressources d’un compte Cosmos DB, et sont créées lors de la création d’un compte Cosmos DB.
- **Les jetons de ressources** saisissent la relation entre l’utilisateur d’une base de données et l’autorisation de l’utilisateur pour une ressource Spécifique Cosmos DB, comme une collection ou un document.

L’exposition d’une clé principale ouvre un compte Cosmos DB à la possibilité d’une utilisation malveillante ou négligente. Cependant, les jetons de ressources Azure Cosmos DB fournissent un mécanisme sûr permettant aux clients de lire, d’écrire et de supprimer des ressources spécifiques dans un compte Azure Cosmos DB selon les autorisations accordées.

Une approche typique pour demander, générer et fournir des jetons de ressources à une application mobile est d’utiliser un courtier en jetons de ressources. Le diagramme suivant montre un aperçu de haut niveau de la façon dont l’application de l’échantillon utilise un courtier en jetons de ressources pour gérer l’accès aux données de base de données de documents :

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

Le courtier en jetons de ressources est un service d’API Web de niveau intermédiaire, hébergé dans Azure App Service, qui possède la clé principale du compte Cosmos DB. L’application d’échantillon utilise le courtier en jetons de ressources pour gérer l’accès aux données de base de données de documents comme suit :

1. Sur la connexion, l’application Xamarin.Forms contacte Azure App Service pour initier un flux d’authentification.
1. Azure App Service effectue un flux d’authentification OAuth avec Facebook. Une fois le flux d’authentification terminé, l’application Xamarin.Forms reçoit un jeton d’accès.
1. L’application Xamarin.Forms utilise le jeton d’accès pour demander un jeton de ressource auprès du courtier en jetons de ressources.
1. Le courtier en jetons de ressources utilise le jeton d’accès pour demander l’identité de l’utilisateur à partir de Facebook. L’identité de l’utilisateur est ensuite utilisée pour demander un jeton de ressources de Cosmos DB, qui est utilisé pour accorder l’accès à la lecture / écrire à la collection partitionnée de l’utilisateur authentifié.
1. L’application Xamarin.Forms utilise le jeton de ressources pour accéder directement aux ressources Cosmos DB avec les autorisations définies par le jeton de ressource.

> [!NOTE]
> Lorsque le jeton de ressource expirera, les demandes subséquentes de base de données de documents recevront une exception non autorisée de 401. À ce stade, les applications Xamarin.Forms devraient rétablir l’identité et demander un nouveau jeton de ressource.

Pour plus d’informations sur le partitionnage Cosmos DB, voir [Comment cloisonner et évoluer en Azure Cosmos DB](/azure/cosmos-db/partition-data/). Pour plus d’informations sur le contrôle d’accès Cosmos DB, voir [Sécurisation de l’accès aux données Cosmos DB](/azure/cosmos-db/secure-access-to-data/) et [le contrôle d’accès dans l’API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Programme d’installation

Le processus d’intégration du courtier en jetons de ressources dans une demande Xamarin.Forms est le suivant :

1. Créez un compte Cosmos DB qui utilisera le contrôle d’accès. Pour plus d’informations, voir [Cosmos DB Configuration](#cosmosdb_configuration).
1. Créez un service d’application Azure pour héberger le courtier en jetons de ressources. Pour plus d’informations, voir [Azure App Service Configuration](#app_service_configuration).
1. Créez une application Facebook pour effectuer l’authentification. Pour plus d’informations, voir [Facebook App Configuration](#facebook_configuration).
1. Configurez le service d’application Azure pour effectuer une authentification facile avec Facebook. Pour plus d’informations, voir [Azure App Service Authentication Configuration](#app_service_authentication_configuration).
1. Configurez l’application d’échantillon Xamarin.Forms pour communiquer avec Azure App Service et Cosmos DB. Pour plus d’informations, voir [Xamarin.Forms Configuration d’application](#forms_application_configuration).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB Configuration

Le processus de création d’un compte Cosmos DB qui utilisera le contrôle d’accès est le suivant :

1. Créez un compte Cosmos DB. Pour plus d’informations, voir [Créer un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Dans le compte Cosmos DB, `UserItems`créez une nouvelle collection `/userid`nommée , spécifiant une clé de partition de .

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuration de service d’application Azure

Le processus d’hébergement du courtier en jetons de ressources dans Azure App Service est le suivant :

1. Dans le portail Azure, créez une nouvelle application Web App Service. Pour plus d’informations, voir [Créer une application web dans un environnement de service d’application](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Dans le portail Azure, ouvrez la lame Paramètres d’application pour l’application web et ajoutez les paramètres suivants :
    - `accountUrl`La valeur devrait être l’URL du compte Cosmos DB de la lame Keys du compte Cosmos DB.
    - `accountKey`La valeur devrait être la clé principale Cosmos DB (primaire ou secondaire) de la lame Keys du compte Cosmos DB.
    - `databaseId`La valeur devrait être le nom de la base de données Cosmos DB.
    - `collectionId`- la valeur devrait être le nom de la collection `UserItems`Cosmos DB (dans ce cas, ).
    - `hostUrl`La valeur doit être l’URL de l’application web à partir de la lame d’aperçu du compte App Service.

    La capture d’écran suivante démontre cette configuration :

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Publiez la solution de courtier en jetons de ressources à l’application Web Azure App Service.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuration de l’application Facebook

Le processus de création d’une application Facebook pour effectuer l’authentification est le suivant :

1. Créez une application Facebook. Pour plus d’informations, consultez [Register and Configurer an App](https://developers.facebook.com/docs/apps/register) sur le Facebook Developer Center.
1. Ajoutez le produit Facebook Login à l’application. Pour plus d’informations, voir [Ajouter Facebook Login à votre application ou site Web](https://developers.facebook.com/docs/facebook-login) sur le Centre de développeurs Facebook.
1. Configurez Facebook Login comme suit :
   - Activez le client OAuth Login.
   - Activez Web OAuth Login.
   - Réglez la redirection valide D’URI vers l’URI de l’app Web App Service, avec `/.auth/login/facebook/callback` joint.

  La capture d’écran suivante démontre cette configuration :

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Pour plus d’informations, consultez [Votre application auprès de Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuration d’authentification du service d’application Azure

Le processus de configuration app Service authentification facile est le suivant:

1. Dans le portail Azure, naviguez vers l’application Web App Service.
1. Dans le portail Azure, ouvrez la lame d’authentification/ autorisation et effectuez la configuration suivante :
    - L’authentification du service d’application doit être activée.
    - L’action à prendre lorsqu’une demande n’est pas authentifiée doit être définie pour **vous connecter à Facebook**.

    La capture d’écran suivante démontre cette configuration :

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

L’application Web App Service doit également être configurée pour communiquer avec l’application Facebook pour activer le flux d’authentification. Cela peut être accompli en sélectionnant le fournisseur d’identité Facebook, et en entrant les valeurs **App ID** et **App Secret** à partir des paramètres de l’application Facebook sur le Centre de développement Facebook. Pour plus d’informations, voir [Ajouter des informations Facebook à votre application](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuration d’application Xamarin.Forms

Le processus de configuration de la demande d’échantillon Xamarin.Forms est le suivant :

1. Ouvrez la solution Xamarin.Forms.
1. Ouvrez `Constants.cs` et mettez à jour les valeurs des constantes suivantes :
    - `EndpointUri`La valeur devrait être l’URL du compte Cosmos DB de la lame Keys du compte Cosmos DB.
    - `DatabaseName`La valeur doit être le nom de la base de données de documents.
    - `CollectionName`la valeur devrait être le nom de la collecte `UserItems`de base de données de documents (dans ce cas, ).
    - `ResourceTokenBrokerUrl`La valeur devrait être l’URL de l’application Web de courtier en jetons de ressources de la lame d’aperçu du compte App Service.

## <a name="initiating-login"></a>Initier la connexion

L’application d’échantillon initie le processus de connexion en redirigeant un navigateur vers une URL de fournisseur d’identité, comme le démontre le code d’exemple suivant :

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Cela provoque un flux d’authentification OAuth à initier entre Azure App Service et Facebook, qui affiche la page de connexion Facebook:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

La connexion peut être annulée en appuyant sur le bouton **Annuler** sur iOS ou en appuyant sur le bouton **Back** sur Android, auquel cas l’utilisateur reste non authentique et l’interface utilisateur du fournisseur d’identité est supprimée de l’écran.

## <a name="obtaining-a-resource-token"></a>Obtenir un jeton de ressource

Après une authentification réussie, l’événement s’enflamme. `WebRedirectAuthenticator.Completed` L’exemple de code suivant démontre la gestion de cet événement :

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

Le résultat d’une authentification réussie est `AuthenticatorCompletedEventArgs.Account` un jeton d’accès, qui est la propriété disponible. Le jeton d’accès est extrait et utilisé dans une `resourcetoken` demande GET à l’API du courtier en jetons de ressources.

L’API `resourcetoken` utilise le jeton d’accès pour demander l’identité de l’utilisateur à partir de Facebook, qui à son tour est utilisé pour demander un jeton de ressource de Cosmos DB. Si un document d’autorisation valide existe déjà pour l’utilisateur dans la base de données de documents, il est récupéré et un document JSON contenant le jeton de ressource est retourné à l’application Xamarin.Forms. Si un document d’autorisation valide n’existe pas pour l’utilisateur, un utilisateur et une autorisation sont créés dans la base de données de documents, et le jeton de ressource est extrait du document d’autorisation et retourné à l’application Xamarin.Forms dans un document JSON.

> [!NOTE]
> Un utilisateur de base de données de documents est une ressource associée à une base de données de documents, et chaque base de données peut contenir zéro ou plus d’utilisateurs. Une autorisation de base de données de documents est une ressource associée à un utilisateur de base de données de documents, et chaque utilisateur peut contenir zéro ou plus d’autorisations. Une ressource d’autorisation donne accès à un jeton de sécurité dont l’utilisateur a besoin lorsqu’il tente d’accéder à une ressource telle qu’un document.

Si `resourcetoken` l’API se termine avec succès, elle enverra le code d’état HTTP 200 (OK) dans la réponse, ainsi qu’un document JSON contenant le jeton de ressource. Les données suivantes de JSON montrent un message de réponse typique réussi :

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Le `WebRedirectAuthenticator.Completed` gestionnaire de l’événement lit la réponse de l’API `resourcetoken` et extrait le jeton de ressource et l’identifiant de l’utilisateur. Le jeton de ressource est ensuite `DocumentClient` passé comme argument au constructeur, qui résume le point de terminaison, les informations d’identification et la stratégie de connexion utilisée pour accéder à Cosmos DB, et est utilisé pour configurer et exécuter des demandes contre Cosmos DB. Le jeton de ressource est envoyé à chaque demande pour accéder directement à une ressource, et indique que l’accès à la collection partitionnée des utilisateurs authentifiés est accordé.

## <a name="retrieving-documents"></a>Récupération des documents

La récupération des documents qui n’appartiennent qu’à l’utilisateur authentifié peut être obtenue en créant une requête de document qui inclut l’id de l’utilisateur comme clé de partition, et est démontrée dans l’exemple de code suivant :

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

La requête récupère asynchronement tous les documents appartenant à l’utilisateur authentifié, de la collection spécifiée, et les place dans une `List<TodoItem>` collection pour l’affichage.

La `CreateDocumentQuery<T>` méthode spécifie un `Uri` argument qui représente la collection `FeedOptions` qui doit être demandée pour les documents, et un objet. L’objet `FeedOptions` spécifie qu’un nombre illimité d’éléments peut être retourné par la requête, et l’id de l’utilisateur comme une clé de partition. Cela garantit que seuls les documents de la collection partitionnée de l’utilisateur sont retournés dans le résultat.

> [!NOTE]
> Notez que les documents d’autorisation, qui sont créés par le courtier en jetons de ressources, sont stockés dans la même collection de documents que les documents créés par la demande Xamarin.Forms. Par conséquent, la requête `Where` en document contient une clause qui applique un filtrage prédicant à la requête contre la collecte de documents. Cette clause garantit que les documents d’autorisation ne sont pas retournés de la collection de documents.

Pour plus d’informations sur la récupération des documents provenant d’une collection de documents, voir [Retrieving Documents Collection Documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query).

## <a name="inserting-documents"></a>Insertion de documents

Avant d’insérer un document `TodoItem.UserId` dans une collection de documents, la propriété doit être mise à jour avec la valeur utilisée comme clé de partition, comme le démontre l’exemple de code suivant :

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Cela garantit que le document sera inséré dans la collection partitionnée de l’utilisateur.

Pour plus d’informations sur l’insertion d’un document dans une collection de documents, voir [Insérer un document dans une collection de documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>Suppression des documents

La valeur de la clé de partition doit être spécifiée lors de la suppression d’un document d’une collection partitionnée, comme le démontre l’exemple de code suivant :

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Cela garantit que Cosmos DB sait de quelle collection cloisonnée supprimer le document.

Pour plus d’informations sur la suppression d’un document à partir d’une collection de documents, voir [Supprimer un document à partir d’une collection de documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Récapitulatif

Cet article expliquait comment combiner le contrôle d’accès avec les collections partitionnées, de sorte qu’un utilisateur ne peut accéder à ses propres documents de base de données documentaires que dans une application Xamarin.Forms. Spécifier l’identité de l’utilisateur en tant que clé de partition garantit qu’une collection cloisonnée ne peut stocker que des documents pour cet utilisateur.

## <a name="related-links"></a>Liens connexes

- [Todo Azure Cosmos DB Auth (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consommation d’une base de données de documents Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Sécurisation de l’accès aux données d’Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Contrôle d’accès dans l’API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Guide de partitionnement et de mise à l’échelle dans Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Bibliothèque client Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
