---
title: Utiliser une base de données de documents Azure Cosmos DB dansXamarin.Forms
description: Cet article explique comment utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour intégrer une base de données de documents Azure Cosmos DB à une Xamarin.Forms application.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47b35d394eab339a8e9a1f81880e6de4233f29b6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127083"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Utiliser une base de données de documents Azure Cosmos DB dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Une base de données de documents Azure Cosmos DB est une base de données NoSQL qui fournit un accès à faible latence aux documents JSON, offrant ainsi un service de base de données rapide, hautement disponible et évolutif pour les applications qui nécessitent une mise à l’échelle transparente et une réplication globale. Cet article explique comment utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour intégrer une base de données de documents Azure Cosmos DB à une Xamarin.Forms application._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Vidéo Microsoft Azure Cosmos DB**

Un compte de base de données de documents Azure Cosmos DB peut être approvisionné à l’aide d’un abonnement Azure. Chaque compte de base de données peut avoir une ou plusieurs bases de données. Une base de données de documents dans Azure Cosmos DB est un conteneur logique pour les collections de documents et les utilisateurs.

Une base de données de documents Azure Cosmos DB peut contenir zéro ou plusieurs collections de documents. Chaque collection de documents peut avoir un niveau de performances différent, ce qui permet de spécifier davantage de débit pour les collections fréquemment sollicitées et moins de débit pour les collections rarement sollicitées.

Chaque collection de documents est composée de zéro, un ou plusieurs documents JSON. Les documents d’une collection sont sans schéma, et il n’est donc pas nécessaire de partager la même structure ou les mêmes champs. À mesure que des documents sont ajoutés à une collection de documents, Cosmos DB les indexe automatiquement et ils deviennent disponibles pour être interrogés.

À des fins de développement, une base de données de documents peut également être consommée via un émulateur. À l’aide de l’émulateur, les applications peuvent être développées et testées localement, sans créer d’abonnement Azure ni frais. Pour plus d’informations sur l’émulateur, consultez [développement local avec l’émulateur Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Cet article, ainsi que l’exemple d’application qui l’accompagne, illustre une application de liste TODO où les tâches sont stockées dans une base de données de documents Azure Cosmos DB. Pour plus d’informations sur l’exemple d’application, consultez [Présentation de l’exemple](~/xamarin-forms/data-cloud/web-services/introduction.md).

Pour plus d’informations sur Azure Cosmos DB, consultez la [Documentation Azure Cosmos DB](/azure/cosmos-db/).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="setup"></a>Installation

Le processus d’intégration d’une base de données de documents Azure Cosmos DB dans une Xamarin.Forms application est le suivant :

1. Créez un compte de Cosmos DB. Pour plus d’informations, consultez [créer un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Ajoutez le Azure Cosmos DB .NET Standard package NuGet de la [bibliothèque cliente](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) aux projets de plateforme de la Xamarin.Forms solution.
1. Ajoutez `using` des directives pour `Microsoft.Azure.Documents` les `Microsoft.Azure.Documents.Client` espaces de `Microsoft.Azure.Documents.Linq` noms, et aux classes qui accéderont au compte Cosmos DB.

Après avoir effectué ces étapes, vous pouvez utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour configurer et exécuter des demandes sur la base de données de documents.

> [!NOTE]
> La bibliothèque cliente Azure Cosmos DB .NET Standard ne peut être installée que dans des projets de plateforme, et non dans un projet de bibliothèque de classes portables (PCL). Par conséquent, l’exemple d’application est un projet d’accès partagé (SAP) pour éviter la duplication de code. Toutefois, la `DependencyService` classe peut être utilisée dans un projet PCL pour appeler Azure Cosmos DB .NET standard Code de bibliothèque client contenu dans des projets spécifiques à la plateforme.

## <a name="consuming-the-azure-cosmos-db-account"></a>Consommation du compte Azure Cosmos DB

Le `DocumentClient` type encapsule le point de terminaison, les informations d’identification et la stratégie de connexion permettant d’accéder au compte Azure Cosmos dB, et est utilisé pour configurer et exécuter des demandes sur le compte. L’exemple de code suivant montre comment créer une instance de cette classe :

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

L’URI Cosmos DB et la clé primaire doivent être fournis au `DocumentClient` constructeur. Elles peuvent être obtenues à partir du portail Azure. Pour plus d’informations, consultez [se connecter à un compte Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Création d’une base de données

Une base de données de documents est un conteneur logique pour les collections de documents et les utilisateurs et peut être créée dans le portail Azure, ou par programmation à l’aide de la `DocumentClient.CreateDatabaseIfNotExistsAsync` méthode :

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

La `CreateDatabaseIfNotExistsAsync` méthode spécifie un `Database` objet en tant qu’argument, avec l' `Database` objet qui spécifie le nom de la base de données en tant que `Id` propriété. La `CreateDatabaseIfNotExistsAsync` méthode crée la base de données si elle n’existe pas, ou retourne la base de données si elle existe déjà. Toutefois, l’exemple d’application ignore toutes les données retournées par la `CreateDatabaseIfNotExistsAsync` méthode.

> [!NOTE]
> La `CreateDatabaseIfNotExistsAsync` méthode retourne un `Task<ResourceResponse<Database>>` objet, et le code d’état de la réponse peut être vérifié pour déterminer si une base de données a été créée ou si une base de données existante a été retournée.

### <a name="creating-a-document-collection"></a>Création d’une collection de documents

Une collection de documents est un conteneur de documents JSON qui peut être créée dans le portail Azure, ou par programme à l’aide de la `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` méthode :

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

La `CreateDocumentCollectionIfNotExistsAsync` méthode requiert deux arguments obligatoires : un nom de base de données spécifié en tant que `Uri` et un `DocumentCollection` objet. L' `DocumentCollection` objet représente une collection de documents dont le nom est spécifié avec la `Id` propriété. La `CreateDocumentCollectionIfNotExistsAsync` méthode crée la collection de documents si elle n’existe pas, ou retourne la collection de documents si elle existe déjà. Toutefois, l’exemple d’application ignore toutes les données retournées par la `CreateDocumentCollectionIfNotExistsAsync` méthode.

> [!NOTE]
> La `CreateDocumentCollectionIfNotExistsAsync` méthode retourne un `Task<ResourceResponse<DocumentCollection>>` objet, et le code d’état de la réponse peut être vérifié pour déterminer si une collection de documents a été créée ou si une collection de documents existante a été retournée.

Éventuellement, la `CreateDocumentCollectionIfNotExistsAsync` méthode peut également spécifier un `RequestOptions` objet, qui encapsule les options qui peuvent être spécifiées pour les demandes émises pour le compte Cosmos DB. La `RequestOptions.OfferThroughput` propriété est utilisée pour définir le niveau de performance de la collection de documents, et dans l’exemple d’application, a la valeur 400 unités de requête par seconde. Cette valeur doit être augmentée ou réduite selon que la collection sera fréquemment ou rarement utilisée.

> [!IMPORTANT]
> Notez que la `CreateDocumentCollectionIfNotExistsAsync` méthode crée une collection avec un débit réservé, ce qui a des répercussions sur la tarification.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Récupération de documents de collection de documents

Le contenu d’une collection de documents peut être récupéré en créant et en exécutant une requête de document. Une requête de document est créée à l’aide de la `DocumentClient.CreateDocumentQuery` méthode :

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

Cette requête récupère de manière asynchrone tous les documents de la collection spécifiée et place les documents dans une `List<TodoItem>` collection pour l’affichage.

La `CreateDocumentQuery<T>` méthode spécifie un `Uri` argument qui représente la collection qui doit être interrogée pour rechercher des documents. Dans cet exemple, la `collectionLink` variable est un champ de niveau classe qui spécifie le `Uri` qui représente la collection de documents à partir de laquelle récupérer des documents :

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

La `CreateDocumentQuery<T>` méthode crée une requête qui est exécutée de façon synchrone et retourne un `IQueryable<T>` objet. Toutefois, la `AsDocumentQuery` méthode convertit l' `IQueryable<T>` objet en un `IDocumentQuery<T>` objet qui peut être exécuté de façon asynchrone. La requête asynchrone est exécutée à l’aide de la `IDocumentQuery<T>.ExecuteNextAsync` méthode, qui récupère la page de résultats suivante de la base de données de documents, avec la `IDocumentQuery<T>.HasMoreResults` propriété indiquant si des résultats supplémentaires doivent être retournés à partir de la requête.

Les documents peuvent être filtrés côté serveur en incluant une `Where` clause dans la requête, qui applique un prédicat de filtrage à la requête sur la collection de documents :

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Cette requête récupère tous les documents de la collection dont la `Done` propriété est égale à `false` .

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Insertion d’un document dans une collection de documents

Les documents sont du contenu JSON défini par l’utilisateur et peuvent être insérés dans une collection de documents à l’aide de la `DocumentClient.CreateDocumentAsync` méthode :

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

La `CreateDocumentAsync` méthode spécifie un `Uri` argument qui représente la collection dans laquelle le document doit être inséré, et un `object` argument qui représente le document à insérer.

### <a name="replacing-a-document-in-a-document-collection"></a>Remplacement d’un document dans une collection de documents

Les documents peuvent être remplacés dans une collection de documents à l’aide de la `DocumentClient.ReplaceDocumentAsync` méthode :

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

La `ReplaceDocumentAsync` méthode spécifie un `Uri` argument qui représente le document dans la collection qui doit être remplacée et un `object` argument qui représente les données de document mises à jour.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Suppression d’un document d’une collection de documents

Vous pouvez supprimer un document d’une collection de documents à l’aide de la `DocumentClient.DeleteDocumentAsync` méthode :

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

La `DeleteDocumentAsync` méthode spécifie un `Uri` argument qui représente le document dans la collection qui doit être supprimé.

### <a name="deleting-a-document-collection"></a>Suppression d’une collection de documents

Une collection de documents peut être supprimée d’une base de données avec la `DocumentClient.DeleteDocumentCollectionAsync` méthode :

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

La `DeleteDocumentCollectionAsync` méthode spécifie un `Uri` argument qui représente la collection de documents à supprimer. Notez que l’appel de cette méthode supprimera également les documents stockés dans la collection.

### <a name="deleting-a-database"></a>Suppression d'une base de données

Une base de données peut être supprimée d’un compte de base de données Cosmos DB à l’aide de la `DocumentClient.DeleteDatabaesAsync` méthode :

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

La `DeleteDatabaseAsync` méthode spécifie un `Uri` argument qui représente la base de données à supprimer. Notez que l’appel de cette méthode supprimera également les collections de documents stockées dans la base de données, ainsi que les documents stockés dans les collections de documents.

## <a name="summary"></a>Résumé

Cet article a expliqué comment utiliser la bibliothèque cliente Azure Cosmos DB .NET Standard pour intégrer une base de données de documents Azure Cosmos DB à une Xamarin.Forms application. Une base de données de documents Azure Cosmos DB est une base de données NoSQL qui fournit un accès à faible latence aux documents JSON, offrant ainsi un service de base de données rapide, hautement disponible et évolutif pour les applications qui nécessitent une mise à l’échelle transparente et une réplication globale.

## <a name="related-links"></a>Liens connexes

- [Azure Cosmos DB todo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentation Azure Cosmos DB](/azure/cosmos-db/)
- [Azure Cosmos DB .NET Standard bibliothèque cliente](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
