---
title: Authentifier les utilisateurs avec une base de données de documents Azure Cosmos DB etXamarin.Forms
description: Cet article explique comment combiner le contrôle d’accès avec Azure Cosmos DB collections partitionnées, afin qu’un utilisateur puisse uniquement accéder à ses propres documents dans une Xamarin.Forms application.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b0322db5ebcc70347bf35157e3dc7c057e58cf18
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136095"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Authentifier les utilisateurs avec une base de données de documents Azure Cosmos DB etXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB les bases de données de documents prennent en charge les collections partitionnées, qui peuvent s’étendre sur plusieurs serveurs et partitions, tout en prenant en charge un stockage et un débit illimités. Cet article explique comment combiner le contrôle d’accès avec des collections partitionnées, afin qu’un utilisateur puisse uniquement accéder à ses propres documents dans une Xamarin.Forms application._

## <a name="overview"></a>Vue d'ensemble

Une clé de partition doit être spécifiée lors de la création d’une collection partitionnée, et les documents ayant la même clé de partition sont stockés dans la même partition. Par conséquent, la spécification de l’identité de l’utilisateur en tant que clé de partition se traduira par une collection partitionnée qui ne stocke que les documents pour cet utilisateur. Cela garantit également que la base de données de documents Azure Cosmos DB sera mise à l’échelle à mesure que le nombre d’utilisateurs et d’éléments augmente.

L’accès doit être accordé à n’importe quel regroupement et le modèle de contrôle d’accès de l’API SQL définit deux types de constructions d’accès :

- Les **clés principales** permettent un accès administratif complet à toutes les ressources au sein d’un compte Cosmos dB, et sont créées lors de la création d’un compte Cosmos DB.
- Les **jetons de ressources** capturent la relation entre l’utilisateur d’une base de données et l’autorisation qu’il a pour une ressource Cosmos DB spécifique, telle qu’une collection ou un document.

L’exposition d’une clé principale ouvre un compte Cosmos DB dans le risque d’une utilisation malveillante ou de négligence. Toutefois, Azure Cosmos DB jetons de ressources fournissent un mécanisme sécurisé pour permettre aux clients de lire, d’écrire et de supprimer des ressources spécifiques dans un compte Azure Cosmos DB en fonction des autorisations accordées.

Une approche classique de la demande, de la génération et de la diffusion de jetons de ressource à une application mobile consiste à utiliser un répartiteur de jetons de ressource. Le diagramme suivant montre une vue d’ensemble de la façon dont l’exemple d’application utilise un service Broker de jetons de ressources pour gérer l’accès aux données de la base de données de documents :

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

Le répartiteur de jetons de ressource est un service d’API Web de niveau intermédiaire, hébergé dans Azure App Service, qui possède la clé principale du compte Cosmos DB. L’exemple d’application utilise le Service Broker de jetons de ressources pour gérer l’accès aux données de la base de données de documents comme suit :

1. Lors de la connexion, l' Xamarin.Forms application contacte Azure App service pour initier un workflow d’authentification.
1. Azure App Service effectue un workflow d’authentification OAuth avec Facebook. Une fois le workflow d’authentification terminé, l' Xamarin.Forms application reçoit un jeton d’accès.
1. L' Xamarin.Forms application utilise le jeton d’accès pour demander un jeton de ressource à partir du répartiteur de jetons de ressource.
1. Le répartiteur de jetons de ressources utilise le jeton d’accès pour demander l’identité de l’utilisateur à Facebook. L’identité de l’utilisateur est ensuite utilisée pour demander un jeton de ressource à partir de Cosmos DB, qui est utilisé pour accorder l’accès en lecture/écriture à la collection partitionnée de l’utilisateur authentifié.
1. L' Xamarin.Forms application utilise le jeton de ressource pour accéder directement aux ressources de Cosmos DB avec les autorisations définies par le jeton de ressource.

> [!NOTE]
> Lorsque le jeton de ressource expire, les demandes de base de données de documents suivantes reçoivent une exception non autorisée 401. À ce stade, Xamarin.Forms les applications doivent rétablir l’identité et demander un nouveau jeton de ressource.

Pour plus d’informations sur le partitionnement de Cosmos DB, consultez [comment partitionner et mettre à l’échelle dans Azure Cosmos DB](/azure/cosmos-db/partition-data/). Pour plus d’informations sur le contrôle d’accès Cosmos DB, consultez [sécurisation de l’accès à Cosmos DB Data](/azure/cosmos-db/secure-access-to-data/) and [Access Control dans l’API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Installation

Le processus d’intégration du répartiteur de jetons de ressource dans une Xamarin.Forms application est le suivant :

1. Créez un compte Cosmos DB qui utilisera le contrôle d’accès. Pour plus d’informations, consultez [Cosmos DB configuration](#cosmosdb_configuration).
1. Créez un Azure App Service pour héberger le répartiteur de jetons de ressource. Pour plus d’informations, consultez [Azure App service Configuration](#app_service_configuration).
1. Créez une application Facebook pour effectuer l’authentification. Pour plus d’informations, consultez [Configuration des applications Facebook](#facebook_configuration).
1. Configurez le Azure App Service pour effectuer une authentification facile avec Facebook. Pour plus d’informations, consultez [configuration de l’authentification Azure App service](#app_service_authentication_configuration).
1. Configurez l' Xamarin.Forms exemple d’application pour communiquer avec Azure App service et Cosmos DB. Pour plus d’informations, consultez Configuration de l' [ Xamarin.Forms application](#forms_application_configuration).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuration de Azure Cosmos DB

Le processus de création d’un compte Cosmos DB qui utilise le contrôle d’accès est le suivant :

1. Créez un compte de Cosmos DB. Pour plus d’informations, consultez [créer un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Dans le compte Cosmos DB, créez une nouvelle collection nommée `UserItems` , en spécifiant une clé de partition de `/userid` .

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuration de Azure App Service

Le processus d’hébergement du répartiteur de jetons de ressource dans Azure App Service se présente comme suit :

1. Dans le Portail Azure, créez une nouvelle application Web App Service. Pour plus d’informations, consultez [créer une application Web dans une app service Environment](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Dans la Portail Azure, ouvrez le panneau Paramètres de l’application pour l’application Web, puis ajoutez les paramètres suivants :
    - `accountUrl`: la valeur doit être l’URL du compte Cosmos DB à partir du panneau clés du compte Cosmos DB.
    - `accountKey`: la valeur doit être la Cosmos DB clé principale (primaire ou secondaire) à partir du panneau clés du compte Cosmos DB.
    - `databaseId`: la valeur doit être le nom de la base de données Cosmos DB.
    - `collectionId`: la valeur doit être le nom de la collection Cosmos DB (dans ce cas, `UserItems` ).
    - `hostUrl`: la valeur doit être l’URL de l’application Web à partir du panneau vue d’ensemble du compte App Service.

    La capture d’écran suivante illustre cette configuration :

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Publiez la solution du répartiteur de jetons de ressources sur l’application Web Azure App Service.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuration de l’application Facebook

Le processus de création d’une application Facebook pour effectuer l’authentification est le suivant :

1. Créer une application Facebook. Pour plus d’informations, consultez [inscrire et configurer une application](https://developers.facebook.com/docs/apps/register) dans le centre de développement Facebook.
1. Ajoutez le produit de connexion Facebook à l’application. Pour plus d’informations, consultez [Ajouter une connexion Facebook à votre application ou site Web](https://developers.facebook.com/docs/facebook-login) dans le centre de développement Facebook.
1. Configurez la connexion Facebook comme suit :
   - Activez la connexion OAuth du client.
   - Activez la connexion OAuth Web.
   - Définissez l’URI de redirection OAuth valide sur l’URI de l’application Web App Service, avec `/.auth/login/facebook/callback` Ajout de.

  La capture d’écran suivante illustre cette configuration :

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Pour plus d’informations, consultez [inscrire votre application auprès de Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuration de l’authentification Azure App Service

Le processus de configuration de App Service Easy Authentication est le suivant :

1. Dans le portail Azure, accédez à l’application Web App Service.
1. Dans le portail Azure, ouvrez le panneau authentification/autorisation et effectuez la configuration suivante :
    - L’authentification App Service doit être activée.
    - L’action à entreprendre lorsqu’une demande n’est pas authentifiée doit être définie pour se **connecter à Facebook**.

    La capture d’écran suivante illustre cette configuration :

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

L’application Web App Service doit également être configurée pour communiquer avec l’application Facebook afin d’activer le workflow d’authentification. Pour ce faire, sélectionnez le fournisseur d’identité Facebook, puis entrez les valeurs **ID** de l’application et secret de l' **application** à partir des paramètres de l’application Facebook dans le centre de développement Facebook. Pour plus d’informations, consultez [Ajouter des informations Facebook à votre application](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Xamarin.FormsConfiguration de l’application

Le processus de configuration de l' Xamarin.Forms exemple d’application est le suivant :

1. Ouvrez la Xamarin.Forms solution.
1. Ouvrez `Constants.cs` et mettez à jour les valeurs des constantes suivantes :
    - `EndpointUri`: la valeur doit être l’URL du compte Cosmos DB à partir du panneau clés du compte Cosmos DB.
    - `DatabaseName`: la valeur doit être le nom de la base de données de documents.
    - `CollectionName`: la valeur doit être le nom de la collection de bases de données de documents (dans ce cas, `UserItems` ).
    - `ResourceTokenBrokerUrl`: la valeur doit être l’URL de l’application Web du Service Broker pour les jetons de ressources à partir du panneau vue d’ensemble du compte App Service.

## <a name="initiating-login"></a>Initialisation de la connexion

L’exemple d’application lance le processus de connexion en redirigeant un navigateur vers une URL de fournisseur d’identité, comme illustré dans l’exemple de code suivant :

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Cela entraîne le lancement d’un workflow d’authentification OAuth entre Azure App Service et Facebook, qui affiche la page de connexion à Facebook :

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

La connexion peut être annulée en appuyant sur le bouton **Annuler** sur iOS ou en appuyant sur le bouton **précédent** sur Android. dans ce cas, l’utilisateur reste non authentifié et l’interface utilisateur du fournisseur d’identité est supprimée de l’écran.

## <a name="obtaining-a-resource-token"></a>Obtention d’un jeton de ressource

Après une authentification réussie, l' `WebRedirectAuthenticator.Completed` événement se déclenche. L’exemple de code suivant montre comment gérer cet événement :

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

Le résultat d’une authentification réussie est un jeton d’accès, qui est la `AuthenticatorCompletedEventArgs.Account` propriété disponible. Le jeton d’accès est extrait et utilisé dans une demande d’extraction à l’API du répartiteur de jetons de ressource `resourcetoken` .

L' `resourcetoken` API utilise le jeton d’accès pour demander l’identité de l’utilisateur à Facebook, qui à son tour est utilisé pour demander un jeton de ressource à partir de Cosmos DB. Si un document d’autorisation valide existe déjà pour l’utilisateur dans la base de données de documents, il est récupéré et un document JSON contenant le jeton de ressource est retourné à l' Xamarin.Forms application. Si un document d’autorisation valide n’existe pas pour l’utilisateur, un utilisateur et une autorisation sont créés dans la base de données de documents, et le jeton de ressource est extrait du document d’autorisation et retourné à l' Xamarin.Forms application dans un document JSON.

> [!NOTE]
> Un utilisateur de base de données de documents est une ressource associée à une base de données de documents, et chaque base de données peut contenir zéro utilisateur ou plus. Une autorisation de base de données de document est une ressource associée à un utilisateur de base de données de documents, et chaque utilisateur peut contenir zéro ou plusieurs autorisations. Une ressource d’autorisation permet d’accéder à un jeton de sécurité dont l’utilisateur a besoin lorsqu’il tente d’accéder à une ressource telle qu’un document.

Si l' `resourcetoken` API se termine correctement, elle envoie le code d’état HTTP 200 (OK) dans la réponse, ainsi qu’un document JSON contenant le jeton de ressource. Les données JSON suivantes affichent un message de réponse de réussite type :

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Le `WebRedirectAuthenticator.Completed` Gestionnaire d’événements lit la réponse de l' `resourcetoken` API et extrait le jeton de ressource et l’ID d’utilisateur. Le jeton de ressource est ensuite passé comme argument au `DocumentClient` constructeur, qui encapsule le point de terminaison, les informations d’identification et la stratégie de connexion permettant d’accéder à Cosmos DB et est utilisé pour configurer et exécuter des demandes sur Cosmos DB. Le jeton de ressource est envoyé avec chaque demande d’accès direct à une ressource, et indique que l’accès en lecture/écriture à la collection partitionnée des utilisateurs authentifiés est accordé.

## <a name="retrieving-documents"></a>Récupération de documents

Il est possible de récupérer des documents qui n’appartiennent qu’à l’utilisateur authentifié en créant une requête de document qui comprend l’ID de l’utilisateur en tant que clé de partition et est illustrée dans l’exemple de code suivant :

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

La requête récupère de manière asynchrone tous les documents appartenant à l’utilisateur authentifié, à partir de la collection spécifiée, et les place dans une `List<TodoItem>` collection pour l’affichage.

La `CreateDocumentQuery<T>` méthode spécifie un `Uri` argument qui représente la collection qui doit être interrogée pour les documents et `FeedOptions` un objet. L' `FeedOptions` objet spécifie qu’un nombre illimité d’éléments peut être retourné par la requête et l’ID de l’utilisateur en tant que clé de partition. Cela garantit que seuls les documents de la collection partitionnée de l’utilisateur sont retournés dans le résultat.

> [!NOTE]
> Notez que les documents d’autorisation, qui sont créés par le répartiteur de jetons de ressource, sont stockés dans la même collection de documents que les documents créés par l' Xamarin.Forms application. Par conséquent, la requête de document contient une `Where` clause qui applique un prédicat de filtrage à la requête sur la collection de documents. Cette clause garantit que les documents d’autorisation ne sont pas retournés à partir de la collection de documents.

Pour plus d’informations sur la récupération de documents à partir d’une collection de documents, consultez [extraction de documents de collection](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query)de documents.

## <a name="inserting-documents"></a>Insertion de documents

Avant l’insertion d’un document dans une collection de documents, la `TodoItem.UserId` propriété doit être mise à jour avec la valeur utilisée comme clé de partition, comme illustré dans l’exemple de code suivant :

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Cela permet de s’assurer que le document sera inséré dans la collection partitionnée de l’utilisateur.

Pour plus d’informations sur l’insertion d’un document dans une collection de documents, consultez [insertion d’un document dans une collection de documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>Suppression de documents

La valeur de clé de partition doit être spécifiée lors de la suppression d’un document d’une collection partitionnée, comme illustré dans l’exemple de code suivant :

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Cela garantit que Cosmos DB sait à partir de quelle collection partitionnée supprimer le document.

Pour plus d’informations sur la suppression d’un document d’une collection de documents, consultez [Suppression d’un document d’une collection de documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Résumé

Cet article a expliqué comment combiner le contrôle d’accès avec des collections partitionnées, afin qu’un utilisateur puisse uniquement accéder à ses propres documents de base de données de documents dans une Xamarin.Forms application. La spécification de l’identité de l’utilisateur en tant que clé de partition garantit qu’une collection partitionnée ne peut stocker que des documents pour cet utilisateur.

## <a name="related-links"></a>Liens connexes

- [TODO Azure Cosmos DB auth (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consommation d’une base de données de documents Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Sécurisation de l’accès aux données d’Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Contrôle d’accès dans l’API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Guide de partitionnement et de mise à l’échelle dans Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Bibliothèque cliente Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
