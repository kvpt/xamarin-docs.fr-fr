---
title: Authentifier les utilisateurs avec une base de données de Document Azure Cosmos DB et de Xamarin.Forms
description: Cet article explique comment combiner de contrôle d’accès avec les collections partitionnées Azure Cosmos DB, afin qu’un utilisateur peut accéder uniquement à leurs propres documents dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e55b3b9b0f204992de684ba09f3d9ff2552ce00
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832360"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Authentifier les utilisateurs avec une base de données de Document Azure Cosmos DB et de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)

_Bases de données de document Azure Cosmos DB prend en charge les collections partitionnées, ce qui peuvent s’étendre sur plusieurs serveurs et des partitions, tout en aidant le débit et stockage illimité. Cet article explique comment combiner de contrôle d’accès avec les collections partitionnées, afin qu’un utilisateur peut accéder uniquement à leurs propres documents dans une application Xamarin.Forms._

## <a name="overview"></a>Vue d'ensemble

Une clé de partition doit être spécifiée lors de la création d’une collection partitionnée, et les documents avec la même clé de partition sont stockés dans la même partition. Par conséquent, spécification de l’identité de l’utilisateur comme clé de partition entraîne une collection partitionnée qui stocke uniquement des documents pour cet utilisateur. Cela garantit également que la base de données de document Azure Cosmos DB sera mise à l’échelle en tant que le nombre d’utilisateurs et augmenter les éléments.

L’accès doit être accordé à n’importe quelle collection, et le modèle de contrôle d’accès API SQL définit deux types de construction d’accès :

- **Clés principales** activer l’accès administratif complet à toutes les ressources au sein d’un compte Cosmos DB et sont créés lorsqu’un compte Cosmos DB est créé.
- **Les jetons de ressource** capturent la relation entre l’utilisateur d’une base de données et l’autorisation de l’utilisateur dispose d’une ressource de Cosmos DB spécifique, tel qu’une collection ou un document.

Exposition d’une clé principale, le risque d’utilisation malveillante ou négligente un compte Cosmos DB s’ouvre. Toutefois, les jetons de ressource Azure Cosmos DB fournissent un mécanisme sécurisé pour autorisant les clients à lire, écrire et supprimer des ressources spécifiques dans un compte Azure Cosmos DB en fonction des autorisations accordées.

Une approche courante à la demande, génération et l’envoi des jetons de ressource à une application mobile consiste à utiliser un répartiteur de jetons de ressource. Le diagramme suivant montre une vue d’ensemble de la façon dont l’exemple d’application utilise un répartiteur de jetons de ressource pour gérer l’accès aux données de base de données de document :

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Processus d’authentification de base de données de document")

Le répartiteur de jetons de ressource est un service API Web de niveau intermédiaire, hébergé dans Azure App Service, qui possède la clé principale du compte Cosmos DB. L’exemple d’application utilise le répartiteur de jetons de ressource pour gérer l’accès aux données de base de données de document comme suit :

1. Lors de la connexion, l’application Xamarin.Forms contacte Azure App Service pour lancer un flux d’authentification.
1. Azure App Service effectue un flux d’authentification OAuth avec Facebook. Une fois le flux d’authentification terminée, l’application Xamarin.Forms reçoit un jeton d’accès.
1. L’application Xamarin.Forms utilise le jeton d’accès pour demander un jeton de ressource du répartiteur de jetons de ressource.
1. Le répartiteur de jetons de ressource utilise le jeton d’accès pour demander l’identité de l’utilisateur à partir de Facebook. Identité de l’utilisateur est ensuite utilisée pour demander un jeton de ressource à partir de Cosmos DB, qui est utilisé pour accorder l’accès en lecture/écriture à la collection partitionnée de l’utilisateur authentifié.
1. L’application Xamarin.Forms utilise le jeton de ressource pour accéder directement aux ressources Cosmos DB avec les autorisations définies par le jeton de ressource.

> [!NOTE]
> Lorsque le jeton de ressource expire, demandes de base de données de document suivants reçoit une exception non autorisée 401. À ce stade, les applications Xamarin.Forms doivent rétablir l’identité et de demander un nouveau jeton de ressource.

Pour plus d’informations sur le partitionnement Cosmos DB, consultez [la partition et mise à l’échelle dans Azure Cosmos DB](/azure/cosmos-db/partition-data/). Pour plus d’informations sur le contrôle d’accès Cosmos DB, consultez [sécurisation de l’accès aux données Cosmos DB](/azure/cosmos-db/secure-access-to-data/) et [contrôle d’accès dans l’API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Installation

Le processus permettant d’intégrer le répartiteur de jetons de ressource à une application Xamarin.Forms est comme suit :

1. Créez un compte Cosmos DB qui utilise le contrôle d’accès. Pour plus d’informations, consultez [Cosmos DB Configuration](#cosmosdb_configuration).
1. Créez un Service d’application Azure pour héberger le répartiteur de jetons de ressource. Pour plus d’informations, consultez [Configuration d’Azure App Service](#app_service_configuration).
1. Créer une application Facebook pour effectuer l’authentification. Pour plus d’informations, consultez [Configuration de l’application Facebook](#facebook_configuration).
1. Configurer le Service d’application Azure pour effectuer une authentification simple avec Facebook. Pour plus d’informations, consultez [Configuration d’Azure App Service d’authentification](#app_service_authentication_configuration).
1. Configurer l’exemple d’application Xamarin.Forms pour communiquer avec Azure App Service et Cosmos DB. Pour plus d’informations, consultez [Configuration de l’Application Xamarin.Forms](#forms_application_configuration).

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuration d’Azure Cosmos DB

Le processus de création d’un compte Cosmos DB qui utilise le contrôle d’accès est la suivante :

1. Créez un compte Cosmos DB. Pour plus d’informations, consultez [créer un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Dans le compte Cosmos DB, créez une nouvelle collection appelée `UserItems`, en spécifiant une clé de partition de `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuration d’Azure App Service

Le processus pour l’hébergement de la ressource token broker dans Azure App Service est la suivante :

1. Dans le portail Azure, créez une nouvelle application web app Service. Pour plus d’informations, consultez [créer une application web dans un environnement App Service](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Dans le portail Azure, ouvrez le panneau de paramètres d’application pour l’application web et ajoutez les paramètres suivants :
    - `accountUrl` – la valeur doit être l’URL du compte Cosmos DB à partir du panneau clés du compte Cosmos DB.
    - `accountKey` – la valeur doit être la clé principale Cosmos DB (principale ou secondaire) à partir du panneau clés du compte Cosmos DB.
    - `databaseId` – la valeur doit être le nom de la base de données Cosmos DB.
    - `collectionId` – la valeur doit être le nom de la collection Cosmos DB (dans ce cas, `UserItems`).
    - `hostUrl` – la valeur doit être l’URL de l’application web à partir du Panneau de vue d’ensemble du compte de Service de l’application.

    La capture d’écran suivante illustre cette configuration :

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "Paramètres de l’application Web App Service")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "paramètres de l’application Web App Service")

1. Publiez la solution de service Broker pour les jetons de ressource à l’application web Azure App Service.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuration de l’application Facebook

Le processus de création d’une application Facebook pour exécuter l’authentification est comme suit :

1. Créer une application Facebook. Pour plus d’informations, consultez [inscrire et configurer une application](https://developers.facebook.com/docs/apps/register) sur le Centre pour développeurs Facebook.
1. Ajouter le produit de la connexion Facebook à l’application. Pour plus d’informations, consultez [ajouter un compte de connexion Facebook à votre application ou site Web](https://developers.facebook.com/docs/facebook-login) sur le Centre pour développeurs Facebook.
1. Configurez une connexion Facebook comme suit :
   - Activer la connexion de Client OAuth.
   - Activer la connexion de OAuth Web.
   - Définir l’URI à l’URI de l’application web app Service, de redirection OAuth valides avec `/.auth/login/facebook/callback` ajouté.

  La capture d’écran suivante illustre cette configuration :

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Paramètres OAuth de connexion Facebook")

Pour plus d’informations, consultez [inscrire votre application avec Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuration de l’authentification Azure App Service

Le processus de configuration de l’authentification simple d’App Service est la suivante :

1. Dans le portail Azure, accédez à l’application web app Service.
1. Dans le portail Azure, ouvrez l’authentification / panneau autorisation et d’effectuer la configuration suivante :
    - L’authentification App Service doit être activée.
    - L’action à entreprendre lorsqu’une demande n’est pas authentifiée doit être définie sur **connexion avec Facebook**.

    La capture d’écran suivante illustre cette configuration :

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "Paramètres d’authentification App Service Web application")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "paramètres d’authentification App Service Web App")

L’application web app Service doit également être configuré pour communiquer avec l’application Facebook pour activer le flux d’authentification. Cela est possible en sélectionnant le fournisseur d’identité Facebook et en entrant le **ID d’application** et **Secret d’application** valeurs à partir des paramètres d’application Facebook sur le Centre pour développeurs Facebook. Pour plus d’informations, consultez [Facebook ajouter des informations à votre application](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuration de l’Application Xamarin.Forms

Le processus de configuration de l’exemple d’application Xamarin.Forms est comme suit :

1. Ouvrez la solution Xamarin.Forms.
1. Ouvrez `Constants.cs` et mettre à jour les valeurs des constantes suivantes :
    - `EndpointUri` – la valeur doit être l’URL du compte Cosmos DB à partir du panneau clés du compte Cosmos DB.
    - `DatabaseName` – la valeur doit être le nom de la base de données de document.
    - `CollectionName` – la valeur doit être le nom de la collection de base de données de document (dans ce cas, `UserItems`).
    - `ResourceTokenBrokerUrl` – la valeur doit être l’URL de l’application de web de répartition des jetons de ressource à partir du Panneau de vue d’ensemble du compte de Service de l’application.

## <a name="initiating-login"></a>Lancement de connexion

L’exemple d’application lance le processus de connexion à l’aide de Xamarin.Auth pour rediriger un navigateur vers une URL de fournisseur d’identité, comme illustré dans l’exemple de code suivant :

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Cela entraîne un flux d’authentification OAuth devant être initialisée entre Azure App Service et Facebook, qui affiche la page de connexion Facebook :

![](azure-cosmosdb-auth-images/login.png "Connexion Facebook")

La connexion peut être annulée en appuyant sur la **Annuler** bouton sur iOS ou en appuyant sur la **retour** bouton sur Android, auquel cas l’utilisateur reste non authentifiée et l’interface utilisateur du fournisseur identité est supprimé de l’écran.

Pour plus d’informations sur Xamarin.Auth, consultez [authentification des utilisateurs avec un fournisseur d’identité](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Obtention d’un jeton de ressource

Après l’authentification réussie, le `WebRedirectAuthenticator.Completed` se déclenche des événements. L’exemple de code suivant illustre la gestion de cet événement :

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

Le résultat d’une authentification réussie est un jeton d’accès, qui est disponible `AuthenticatorCompletedEventArgs.Account` propriété. Le jeton d’accès est extraites et utilisé dans une demande GET auprès de la ressource token broker `resourcetoken` API.

Le `resourcetoken` API utilise le jeton d’accès pour demander l’identité de l’utilisateur à partir de Facebook, qui à son tour, est utilisé pour demander un jeton de ressource à partir de Cosmos DB. Si un document d’autorisation valide existe déjà pour l’utilisateur dans la base de données de document, il est récupéré et un document JSON contenant le jeton de ressource est retourné à l’application Xamarin.Forms. Si un document d’autorisation valide n’existe pas pour l’utilisateur, un utilisateur et l’autorisation est créé dans la base de données de document et le jeton de ressource est extraite à partir du document d’autorisation et retourné à l’application Xamarin.Forms dans un document JSON.

> [!NOTE]
> Un utilisateur de base de données de document est une ressource associée à une base de données de document, et chaque base de données peut contenir zéro ou plusieurs utilisateurs. Une autorisation de base de données de document est une ressource associée à un utilisateur de base de données de document, et chaque utilisateur peut contenir zéro ou plusieurs autorisations. Une ressource d’autorisation donne accès à un jeton de sécurité nécessitant l’utilisateur lorsque vous tentez d’accéder à une ressource telle qu’un document.

Si le `resourcetoken` API se termine avec succès, il enverra un code d’état HTTP 200 (OK) dans la réponse, ainsi que d’un document JSON contenant le jeton de ressource. Les données JSON suivantes affiche un message de réponse correcte classique :

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Le `WebRedirectAuthenticator.Completed` Gestionnaire d’événements lit la réponse à partir de la `resourcetoken` API et extrait le jeton de ressource et l’id d’utilisateur. Le jeton de ressource est ensuite transmis en tant qu’argument à la `DocumentClient` constructeur, qui encapsule le point de terminaison, les informations d’identification et les stratégies de connexion utilisé pour accéder à Cosmos DB et est utilisé pour configurer et exécuter des requêtes Cosmos DB. Le jeton de ressource est envoyé avec chaque demande directement accéder à une ressource et indique que l’accès en lecture/écriture à la collection partitionnée de l’utilisateur authentifié est accordé.

## <a name="retrieving-documents"></a>Récupération de Documents

Récupération de documents qui appartiennent uniquement à l’utilisateur authentifié peut être obtenue en créant une requête de document qui inclut l’id d’utilisateur comme clé de partition, est illustrée dans l’exemple de code suivant :

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

La requête de façon asynchrone récupère tous les documents appartenant à l’utilisateur authentifié, à partir de la collection spécifiée et les place dans un `List<TodoItem>` collection pour l’affichage.

Le `CreateDocumentQuery<T>` méthode spécifie un `Uri` argument qui représente la collection doit être interrogée pour les documents, et un `FeedOptions` objet. Le `FeedOptions` objet spécifie qu’un nombre illimité d’éléments peut être retourné par la requête et l’id d’utilisateur comme clé de partition. Cela garantit que seuls les documents dans la collection partitionnée de l’utilisateur sont retournés dans le résultat.

> [!NOTE]
> Notez que les documents d’autorisation, qui sont créés par l’intermédiaire de jeton de ressource, sont stockés dans la même collection de documents en tant que les documents créés par l’application Xamarin.Forms. Par conséquent, la requête de document contient un `Where` clause qui applique un prédicat de filtrage à la requête par rapport à la collection de documents. Cette clause permet de s’assurer que les documents d’autorisation ne sont pas retournées à partir de la collection de documents.

Pour plus d’informations sur la récupération des documents à partir d’une collection de documents, consultez [Documents de la Collection de récupération de Document](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query).

## <a name="inserting-documents"></a>Insertion de Documents

Avant l’insertion d’un document dans une collection de documents, le `TodoItem.UserId` propriété doit être mis à jour avec la valeur utilisée comme clé de partition, comme illustré dans l’exemple de code suivant :

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Cela garantit que le document est inséré dans la collection partitionnée de l’utilisateur.

Pour plus d’informations sur l’insertion d’un document dans une collection de documents, consultez [insertion d’un Document dans une Collection de documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>Suppression de Documents

La valeur de clé de partition doit être spécifiée lors de la suppression d’un document à partir d’une collection partitionnée, comme illustré dans l’exemple de code suivant :

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Cela garantit que Cosmos DB sache qui partitionnée collection à supprimer le document à partir de.

Pour plus d’informations sur la suppression d’un document à partir d’une collection de documents, consultez [suppression d’un Document à partir d’une Collection de documents](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment combiner de contrôle d’accès avec les collections partitionnées, afin qu’un utilisateur peut accéder uniquement à leurs propres documents de base de données de document dans une application Xamarin.Forms. Spécification de l’identité de l’utilisateur comme clé de partition garantit qu’une collection partitionnée peut stocker uniquement des documents pour cet utilisateur.


## <a name="related-links"></a>Liens associés

- [TODO Azure Cosmos DB Auth (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [Consommation d’une base de données de documents Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Sécurisation de l’accès aux données Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Contrôle d’accès dans l’API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Guide de partitionnement et mise à l’échelle dans Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Bibliothèque cliente Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
