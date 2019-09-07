---
title: Utilisation d’une base de données de documents Azure Cosmos DB dans Xamarin. Forms
description: Cet article explique comment utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour intégrer une base de données de document Azure Cosmos DB dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: a18572b0f28cb8d6c699d0bbd062bdb067471a34
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770883"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Utilisation d’une base de données de documents Azure Cosmos DB dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Une base de données de document Azure Cosmos DB est une base de données NoSQL qui fournit l’accès à faible latence pour les documents JSON, offre un service de base de données rapide, hautement disponible et évolutif pour les applications qui nécessitent une mise à l’échelle transparente et une réplication globale. Cet article explique comment utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour intégrer une base de données de document Azure Cosmos DB dans une application Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Vidéo Microsoft Azure Cosmos DB**

Un compte de base de données de document Azure Cosmos DB peut être configuré à l’aide d’un abonnement Azure. Chaque compte de base de données peut avoir zéro ou plusieurs bases de données. Une base de données de document dans Azure Cosmos DB est un conteneur logique pour les collections de documents et les utilisateurs.

Une base de données de document Azure Cosmos DB peut contenir zéro ou plusieurs collections de documents. Chaque collection de documents peut avoir un niveau de performances différentes, ce qui permet un débit plus être spécifiés pour les collections fréquemment sollicitées et un débit inférieur pour les collections rarement sollicitées.

Chaque collection de documents se compose de zéro ou plusieurs documents JSON. Documents d’une collection sont sans schéma et par conséquent, n’avez pas besoin de partager la même structure ou les champs. Comme les documents sont ajoutés à une collection de documents, Cosmos DB indexe automatiquement les et qu’ils deviennent disponibles à interroger.

À des fins de développement, une base de données de document peut également être consommée via un émulateur. À l’aide de l’émulateur, applications peuvent être développées et testées localement, sans créer d’abonnement Azure et sans frais. Pour plus d’informations sur l’émulateur, consultez [développement localement avec l’émulateur Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Cet article et exemple d’application, d’accompagnement montre une application de liste Todo où les tâches sont stockées dans une base de données de document Azure Cosmos DB. Pour plus d’informations sur l’exemple d’application, consultez [compréhension de l’exemple](~/xamarin-forms/data-cloud/web-services/introduction.md).

Pour plus d’informations sur Azure Cosmos DB, consultez le [la Documentation Azure Cosmos DB](/azure/cosmos-db/).

## <a name="setup"></a>Installation

Le processus d’intégration d’une base de données de document Azure Cosmos DB dans une application Xamarin.Forms est comme suit :

1. Créez un compte Cosmos DB. Pour plus d’informations, consultez [créer un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Ajouter le [bibliothèque de client Azure Cosmos DB .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) package NuGet pour les projets de plateforme dans la solution Xamarin.Forms.
1. Ajouter `using` directives pour la `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, et `Microsoft.Azure.Documents.Linq` espaces de noms pour les classes qui accéderont au compte Cosmos DB.

Après avoir effectué ces étapes, la bibliothèque cliente Azure Cosmos DB .NET Standard peut être utilisée pour configurer et exécuter des requêtes sur la base de données de document.

> [!NOTE]
> La bibliothèque cliente Azure Cosmos DB .NET Standard ne peut être installée dans les projets de plateforme et pas dans un projet de bibliothèque de classes Portable (PCL). Par conséquent, l’exemple d’application est un projet d’accès partagé (SAP) pour éviter la duplication de code. Toutefois, la `DependencyService` classe peut être utilisée dans un projet de bibliothèque de classes portable pour appeler le code de bibliothèque de client Azure Cosmos DB .NET Standard contenue dans les projets propres à la plateforme.

## <a name="consuming-the-azure-cosmos-db-account"></a>Utilisation du compte Azure Cosmos DB

Le `DocumentClient` type encapsule le point de terminaison, les informations d’identification et les stratégies de connexion utilisé pour accéder au compte Azure Cosmos DB et est utilisé pour configurer et exécuter des demandes sur le compte. L’exemple de code suivant montre comment créer une instance de cette classe :

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

L’Uri de base de données Cosmos et la clé primaire doivent être fournis pour le `DocumentClient` constructeur. Il peuvent être obtenus à partir du portail Azure. Pour plus d’informations, consultez [se connecter à un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Création d’une base de données

Une base de données de document est un conteneur logique pour les collections de documents et les utilisateurs et peut être créé dans le portail Azure, ou par programmation à l’aide du `DocumentClient.CreateDatabaseIfNotExistsAsync` méthode :

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

Le `CreateDatabaseIfNotExistsAsync` méthode spécifie un `Database` de l’objet en tant qu’argument, avec le `Database` objet en spécifiant le nom de la base de données en tant que son `Id` propriété. Le `CreateDatabaseIfNotExistsAsync` méthode crée la base de données si elle n’existe pas, ou retourne la base de données si elle existe déjà. Toutefois, l’exemple d’application ignore toutes les données retournées par le `CreateDatabaseIfNotExistsAsync` (méthode).

> [!NOTE]
> Le `CreateDatabaseIfNotExistsAsync` méthode retourne un `Task<ResourceResponse<Database>>` objet et le code d’état de la réponse peuvent être vérifiée pour déterminer si une base de données a été créée, ou une base de données existante a été retourné.

### <a name="creating-a-document-collection"></a>Création d’une Collection de documents

Une collection de documents est un conteneur pour les documents JSON et peut être créé dans le portail Azure, ou par programmation à l’aide du `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` méthode :

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

Le `CreateDocumentCollectionIfNotExistsAsync` méthode requiert deux arguments obligatoires : un nom de base de données spécifié comme un `Uri`et un `DocumentCollection` objet. Le `DocumentCollection` objet représente une collection de documents dont le nom est spécifié avec le `Id` propriété. Le `CreateDocumentCollectionIfNotExistsAsync` méthode crée la collection de documents si elle n’existe pas, ou retourne la collection de documents si elle existe déjà. Toutefois, l’exemple d’application ignore toutes les données retournées par le `CreateDocumentCollectionIfNotExistsAsync` (méthode).

> [!NOTE]
> Le `CreateDocumentCollectionIfNotExistsAsync` méthode retourne un `Task<ResourceResponse<DocumentCollection>>` objet et le code d’état de la réponse peuvent être vérifiée pour déterminer si une collection de documents a été créée, ou une collection de document existante a été retournée.

Si vous le souhaitez, le `CreateDocumentCollectionIfNotExistsAsync` méthode peut également spécifier un `RequestOptions` objet qui encapsule les options qui peuvent être spécifiées pour les requêtes émises au compte Cosmos DB. Le `RequestOptions.OfferThroughput` propriété est utilisée pour définir le niveau de performance de la collection de documents, et dans l’exemple de l’application, a la valeur 400 unités de requête par seconde. Cette valeur doit être augmentée ou diminuée selon si la collection est fréquemment ou rarement accessible.

> [!IMPORTANT]
> Notez que le `CreateDocumentCollectionIfNotExistsAsync` méthode crée une nouvelle collection avec un débit réservé, ce qui a des conséquences tarifaires.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Récupération de Documents de Collection de documents

Le contenu d’une collection de documents peut être récupéré en créant et en exécutant une requête de document. Une requête de document est créée avec le `DocumentClient.CreateDocumentQuery` méthode :

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

Cette requête de façon asynchrone récupère tous les documents à partir de la collection spécifiée et place les documents dans un `List<TodoItem>` collection pour l’affichage.

Le `CreateDocumentQuery<T>` méthode spécifie un `Uri` argument qui représente la collection doit être interrogée pour les documents. Dans cet exemple, le `collectionLink` variable est un champ de niveau classe qui spécifie le `Uri` qui représente la collection de documents pour récupérer les documents à partir de :

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

Le `CreateDocumentQuery<T>` méthode crée une requête qui est exécutée de façon synchrone et retourne un `IQueryable<T>` objet. Toutefois, le `AsDocumentQuery` méthode convertit le `IQueryable<T>` de l’objet à un `IDocumentQuery<T>` objet qui peut être exécutée de façon asynchrone. La requête asynchrone est exécutée avec le `IDocumentQuery<T>.ExecuteNextAsync` (méthode), qui Récupère la page suivante des résultats de la base de données de document, avec le `IDocumentQuery<T>.HasMoreResults` propriété qui indique si des résultats supplémentaires sont à retourner à partir de la requête.

Les documents peuvent être filtrées côté serveur en incluant un `Where` clause dans la requête, qui applique un prédicat de filtrage à la requête par rapport à la collection de documents :

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Cette requête récupère tous les documents à partir de la collection dont `Done` propriété est égale à `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Insertion d’un Document dans une Collection de documents

Les documents sont définis par l’utilisateur du contenu JSON et peuvent être insérées dans une collection de documents avec le `DocumentClient.CreateDocumentAsync` méthode :

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

Le `CreateDocumentAsync` méthode spécifie un `Uri` argument qui représente la collection que le document doit être inséré dans, et un `object` argument qui représente le document à insérer.

### <a name="replacing-a-document-in-a-document-collection"></a>Remplacement d’un Document dans une Collection de documents

Les documents peuvent être remplacées dans une collection de documents avec le `DocumentClient.ReplaceDocumentAsync` méthode :

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

Le `ReplaceDocumentAsync` méthode spécifie un `Uri` argument qui représente le document dans la collection doit être remplacé, et un `object` argument qui représente les données de document mis à jour.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Suppression d’un Document à partir d’une Collection de documents

Un document peut être supprimé à partir d’une collection de documents avec le `DocumentClient.DeleteDocumentAsync` méthode :

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

Le `DeleteDocumentAsync` méthode spécifie un `Uri` argument qui représente le document dans la collection doit être supprimé.

### <a name="deleting-a-document-collection"></a>Suppression d’une Collection de documents

Une collection de documents peut être supprimée à partir d’une base de données avec le `DocumentClient.DeleteDocumentCollectionAsync` méthode :

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

Le `DeleteDocumentCollectionAsync` méthode spécifie un `Uri` argument qui représente la collection de documents à supprimer. Notez que l’appel de cette méthode supprime également les documents stockés dans la collection.

### <a name="deleting-a-database"></a>Suppression d’une base de données

Une base de données peut être supprimé à partir d’un compte de base de données Cosmos DB avec la `DocumentClient.DeleteDatabaesAsync` méthode :

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

Le `DeleteDatabaseAsync` méthode spécifie un `Uri` argument qui représente la base de données à supprimer. Notez que l’appel de cette méthode va également supprimer les collections de documents stockées dans la base de données et les documents stockés dans les collections de documents.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour intégrer une base de données de document Azure Cosmos DB dans une application Xamarin.Forms. Une base de données de document Azure Cosmos DB est une base de données NoSQL qui fournit l’accès à faible latence pour les documents JSON, offre un service de base de données rapide, hautement disponible et évolutif pour les applications qui nécessitent une mise à l’échelle transparente et une réplication globale.

## <a name="related-links"></a>Liens associés

- [TODO Azure Cosmos DB (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentation Azure Cosmos DB](/azure/cosmos-db/)
- [Bibliothèque cliente Azure Cosmos DB .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
