---
title: Rechercher des données avec Azure Search et Xamarin.Forms
description: Cet article explique comment utiliser la bibliothèque de recherche Microsoft Azure pour intégrer Azure Search dans une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 14d26c1360c1c1b7997598ef1263e3dd62e3c013
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561779"
---
# <a name="search-data-with-azure-search-and-no-locxamarinforms"></a>Rechercher des données avec Azure Search et Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Recherche Azure est un service Cloud qui fournit des fonctionnalités d’indexation et d’interrogation pour les données chargées. Cela supprime les exigences en matière d’infrastructure et les complexités de l’algorithme de recherche traditionnellement associées à l’implémentation de la fonctionnalité de recherche dans une application. Cet article explique comment utiliser la bibliothèque de recherche Microsoft Azure pour intégrer Azure Search dans une Xamarin.Forms application._

## <a name="overview"></a>Vue d’ensemble

Les données sont stockées dans la recherche Azure sous la forme d’index et de documents. Un *index* est un magasin de données qui peut être recherché par le service Azure Search et qui est conceptuellement similaire à une table de base de données. Un *document* est une unité unique de données pouvant faire l’objet d’une recherche dans un index, et est conceptuellement similaire à une ligne de base de données. Lors du chargement de documents et de l’envoi de requêtes de recherche à Azure Search, les demandes sont adressées à un index spécifique dans le service de recherche.

Chaque demande effectuée auprès d’Azure Search doit inclure le nom du service et une clé API. Il existe deux types de clé API :

- Les *clés d’administration* accordent des droits complets à toutes les opérations. Cela comprend la gestion du service, la création et la suppression d’index, ainsi que les sources de données.
- Les *clés de requête* accordent un accès en lecture seule aux index et aux documents. elles doivent être utilisées par les applications qui émettent des demandes de recherche.

La requête la plus courante auprès d’Azure Search consiste à exécuter une requête. Il existe deux types de requête qui peuvent être envoyés :

- Une requête de *recherche* recherche un ou plusieurs éléments dans tous les champs pouvant faire l’objet d’une recherche dans un index. Les requêtes de recherche sont générées à l’aide de la syntaxe simplifiée, ou de la syntaxe de requête Lucene. Pour plus d’informations, consultez [syntaxe de requête simple dans Azure Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)et [syntaxe de requête Lucene dans Azure Search](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Une requête de *filtre* évalue une expression booléenne sur tous les champs filtrables d’un index. Les requêtes de filtre sont générées à l’aide d’un sous-ensemble du langage de filtre OData. Pour plus d’informations, consultez [syntaxe d’expression OData pour Azure Search](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Les requêtes de recherche et les requêtes de filtre peuvent être utilisées séparément ou ensemble. En cas d’utilisation conjointe, la requête de filtre est appliquée en premier à la totalité de l’index, puis la requête de recherche est exécutée sur les résultats de la requête de filtre.

Azure Search prend également en charge la récupération de suggestions en fonction de l’entrée de recherche. Pour plus d’informations, consultez [suggestions de requêtes](#suggestion-queries).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="setup"></a>Programme d’installation

Le processus d’intégration d’Azure Search dans une Xamarin.Forms application est le suivant :

1. Créez un service Azure Search. Pour plus d’informations, consultez [créer un service Azure Search à l’aide du portail Azure](/azure/search/search-create-service-portal/).
1. Supprimez Silverlight comme version cible du .NET Framework à partir de la Xamarin.Forms bibliothèque de classes portables (PCL) de la solution. Pour ce faire, vous pouvez changer le profil PCL en un profil qui prend en charge le développement multiplateforme, mais ne prend pas en charge Silverlight, par exemple Profile 151 ou profil 92.
1. Ajoutez le package NuGet de la [bibliothèque de recherche Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) au projet PCL dans la Xamarin.Forms solution.

Après avoir effectué ces étapes, vous pouvez utiliser l’API de la bibliothèque de recherche Microsoft pour gérer les index de recherche et les sources de données, charger et gérer des documents et exécuter des requêtes.

## <a name="creating-the-azure-search-index"></a>Création de l'index Azure Search

Un schéma d’index doit être défini de façon à correspondre à la structure des données à rechercher. Cela peut être accompli dans le portail Azure ou par programme à l’aide de la `SearchServiceClient` classe. Cette classe gère les connexions à Azure Search et peut être utilisée pour créer un index. L’exemple de code suivant montre comment créer une instance de cette classe :

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

La `SearchServiceClient` surcharge du constructeur prend un nom de service de recherche et un `SearchCredentials` objet comme arguments, avec l' `SearchCredentials` objet qui encapsule la *clé d’administration* pour le service Azure Search. La *clé d’administration* est requise pour créer un index.

> [!NOTE]
> Une seule `SearchServiceClient` instance doit être utilisée dans une application pour éviter d’ouvrir un trop grand nombre de connexions à Azure Search.

Un index est défini par l' `Index` objet, comme illustré dans l’exemple de code suivant :

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

La `Index.Name` propriété doit être définie sur le nom de l’index et la `Index.Fields` propriété doit être définie sur un tableau d' `Field` objets. Chaque `Field` instance spécifie un nom, un type et des propriétés qui spécifient comment le champ est utilisé. Ces propriétés sont les suivantes :

- `IsKey` : indique si le champ est la clé de l’index. Un seul champ dans l’index, de type `DataType.String` , doit être désigné comme champ clé.
- `IsFacetable` : indique s’il est possible d’effectuer une navigation à facettes sur ce champ. La valeur par défaut est `false`.
- `IsFilterable` : indique si le champ peut être utilisé dans les requêtes de filtre. La valeur par défaut est `false`.
- `IsRetrievable` : indique si le champ peut être récupéré dans les résultats de la recherche. La valeur par défaut est `true`.
- `IsSearchable` : indique si le champ est inclus dans les recherches en texte intégral. La valeur par défaut est `false`.
- `IsSortable` : indique si le champ peut être utilisé dans les `OrderBy` expressions. La valeur par défaut est `false`.

> [!NOTE]
> La modification d’un index après son déploiement implique la reconstruction et le rechargement des données.

Un `Index` objet peut éventuellement spécifier une `Suggesters` propriété, qui définit les champs de l’index à utiliser pour prendre en charge les requêtes de saisie semi-automatique ou de suggestion de recherche. La `Suggesters` propriété doit être définie sur un tableau d' `Suggester` objets qui définissent les champs utilisés pour générer les résultats des suggestions de recherche.

Après la création de l' `Index` objet, l’index est créé en appelant `Indexes.Create` sur l' `SearchServiceClient` instance de.

> [!NOTE]
> Lorsque vous créez un index à partir d’une application qui doit rester réactive, utilisez la `Indexes.CreateAsync` méthode.

Pour plus d’informations, consultez [créer un index Azure Search à l’aide du kit de développement logiciel (SDK) .net](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Suppression de l’index recherche Azure

Un index peut être supprimé en appelant `Indexes.Delete` sur l' `SearchServiceClient` instance :

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Téléchargement de données vers l’index recherche Azure

Une fois l’index défini, les données peuvent être chargées sur celui-ci à l’aide de l’un des deux modèles suivants :

- **Modèle d’extraction** : les données sont ingérées régulièrement à partir de Azure Cosmos DB, Azure SQL Database, le stockage BLOB azure ou SQL Server hébergées sur une machine virtuelle Azure.
- **Modèle push** : les données sont envoyées par programmation à l’index. Il s’agit du modèle adopté dans cet article.

Une `SearchIndexClient` instance doit être créée pour importer des données dans l’index. Pour ce faire, vous pouvez appeler la `SearchServiceClient.Indexes.GetClient` méthode, comme illustré dans l’exemple de code suivant :

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

Les données à importer dans l’index sont empaquetées sous la forme d’un `IndexBatch` objet, qui encapsule une collection d' `IndexAction` objets. Chaque `IndexAction` instance contient un document et une propriété qui indique à Azure Search l’action à effectuer sur le document. Dans l’exemple de code ci-dessus, l' `IndexAction.Upload` action est spécifiée, ce qui a pour effet d’insérer le document dans l’index s’il est nouveau, ou de le remplacer s’il existe déjà. L' `IndexBatch` objet est ensuite envoyé à l’index en appelant la `Documents.Index` méthode sur l' `SearchIndexClient` objet. Pour plus d’informations sur les autres actions d’indexation, consultez décider de l' [action d’indexation à utiliser](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Seuls 1000 documents peuvent être inclus dans une seule requête d’indexation.

Notez que dans l’exemple de code ci-dessus, la `monkeyList` collection est créée en tant qu’objet anonyme à partir d’une collection d' `Monkey` objets. Cela crée des données pour le `id` champ et résout le mappage des noms de propriété de cas Pascal `Monkey` en noms de champs d’index de recherche de casse mixte. Ce mappage peut également être accompli en ajoutant l' `[SerializePropertyNamesAsCamelCase]` attribut à la `Monkey` classe.

Pour plus d’informations, consultez [charger des données dans Azure Search à l’aide du kit de développement logiciel (SDK) .net](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Interrogation de l’index recherche Azure

Une `SearchIndexClient` instance doit être créée pour interroger un index. Lorsqu’une application exécute des requêtes, il est recommandé de suivre le principe du moindre privilège et de créer un `SearchIndexClient` directement, en transmettant la *clé de requête* en tant qu’argument. Cela garantit que les utilisateurs disposent d’un accès en lecture seule aux index et aux documents. Cette approche est illustrée dans l’exemple de code suivant :

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

La `SearchIndexClient` surcharge du constructeur prend un nom de service de recherche, un nom d’index et un `SearchCredentials` objet comme arguments, avec l' `SearchCredentials` objet qui encapsule la *clé de requête* pour le service Azure Search.

### <a name="search-queries"></a>Requêtes de recherche

L’index peut être interrogé en appelant la `Documents.SearchAsync` méthode sur l' `SearchIndexClient` instance, comme illustré dans l’exemple de code suivant :

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

La `SearchAsync` méthode prend un argument de texte de recherche et un `SearchParameters` objet facultatif qui peut être utilisé pour affiner la requête. Une requête de recherche est spécifiée comme argument de recherche de texte, tandis qu’une requête de filtre peut être spécifiée en définissant la `Filter` propriété de l' `SearchParameters` argument. L’exemple de code suivant illustre les deux types de requêtes :

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Cette requête de filtre est appliquée à la totalité de l’index et supprime les documents des résultats où le `location` champ n’est pas égal à Chine et non égal au Viêt Nam. Après le filtrage, la requête de recherche est exécutée sur les résultats de la requête de filtre.

> [!NOTE]
> Pour filtrer sans rechercher, transmettez `*` comme argument de recherche de texte.

La `SearchAsync` méthode retourne un `DocumentSearchResult` objet qui contient les résultats de la requête. Cet objet est énuméré, chaque `Document` objet étant créé en tant qu' `Monkey` objet et ajouté au pour l' `Monkeys` `ObservableCollection` affichage. Les captures d’écran suivantes montrent les résultats de la requête de recherche renvoyés par Azure Search :

![Résultats de la recherche](azure-search-images/search.png)

Pour plus d’informations sur la recherche et le filtrage, consultez [interroger votre index Azure Search à l’aide du kit de développement logiciel (SDK) .net](/azure/search/search-query-dotnet/).

### <a name="suggestion-queries"></a>Requêtes de suggestion

Recherche Azure permet de demander des suggestions en fonction d’une requête de recherche, en appelant la `Documents.SuggestAsync` méthode sur l' `SearchIndexClient` instance. Cela est illustré dans l’exemple de code suivant :

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

La `SuggestAsync` méthode prend un argument de texte de recherche, le nom du générateur de suggestions à utiliser (défini dans l’index) et un objet facultatif `SuggestParameters` qui peut être utilisé pour affiner la requête. L' `SuggestParameters` instance définit les propriétés suivantes :

- `UseFuzzyMatching` – Lorsque la valeur est définie sur `true` , Azure search recherche les suggestions, même s’il existe un caractère substitué ou manquant dans le texte recherché.
- `HighlightPreTag` : balise précédée des accès aux suggestions.
- `HighlightPostTag` : balise ajoutée aux correspondances de suggestions.
- `MinimumCoverage` – représente le pourcentage de l’index qui doit être couvert par une requête de suggestion pour que la requête soit signalée comme ayant réussi. La valeur par défaut est 80.
- `Top` : nombre de suggestions à récupérer. Il doit s’agir d’un entier compris entre 1 et 100, avec 5 comme valeur par défaut.

L’effet global est que les 10 premiers résultats de l’index sont retournés avec la mise en surbrillance des correspondances, et les résultats incluent des documents incluant des termes de recherche de même orthographe.

La `SuggestAsync` méthode retourne un `DocumentSuggestResult` objet qui contient les résultats de la requête. Cet objet est énuméré, chaque `Document` objet étant créé en tant qu' `Monkey` objet et ajouté au pour l' `Monkeys` `ObservableCollection` affichage. Les captures d’écran suivantes montrent les résultats des suggestions renvoyées par Azure Search :

![Résultats des suggestions](azure-search-images/suggest.png)

Notez que dans l’exemple d’application, la `SuggestAsync` méthode est appelée uniquement lorsque l’utilisateur finit de saisir un terme de recherche. Toutefois, il peut également être utilisé pour prendre en charge les requêtes de recherche à saisie semi-automatique en s’exécutant sur chaque pression.

## <a name="summary"></a>Résumé

Cet article a montré comment utiliser la bibliothèque de recherche Microsoft Azure pour intégrer Azure Search dans une Xamarin.Forms application. Recherche Azure est un service Cloud qui fournit des fonctionnalités d’indexation et d’interrogation pour les données chargées. Cela supprime les exigences en matière d’infrastructure et les complexités de l’algorithme de recherche traditionnellement associées à l’implémentation de la fonctionnalité de recherche dans une application.

## <a name="related-links"></a>Liens associés

- [Recherche Azure (exemple)](/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Documentation recherche Azure](/azure/search/)
- [Bibliothèque de recherche de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)