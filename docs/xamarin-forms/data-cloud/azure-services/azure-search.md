---
title: Rechercher des données avec Azure Search et Xamarin. Forms
description: Cet article montre comment utiliser la bibliothèque de recherche Microsoft Azure pour intégrer Azure Search dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: cd5aaac0f41ee6e4afd79397a77635e66abad219
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489789"
---
# <a name="search-data-with-azure-search-and-xamarinforms"></a>Rechercher des données avec Azure Search et Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Recherche Azure est un service Cloud qui fournit des fonctionnalités d’indexation et d’interrogation pour les données chargées. Cela supprime les exigences en matière d’infrastructure et les complexités de l’algorithme de recherche traditionnellement associées à l’implémentation de la fonctionnalité de recherche dans une application. Cet article explique comment utiliser la bibliothèque de recherche Microsoft Azure pour intégrer Azure Search dans une application Xamarin. Forms._

## <a name="overview"></a>Vue d'ensemble de

En tant qu’index et documents, les données sont stockées dans Azure Search. Un *index* est un magasin de données qui peuvent être recherchés par le service de recherche Azure et est conceptuellement semblable à une table de base de données. Un *document* est une unité de données interrogeables dans un index unique et est conceptuellement semblable à une ligne de base de données. Si téléchargement de documents et la soumission de requêtes de recherche Azure, les demandes sont effectuées vers un index spécifique dans le service de recherche.

Chaque demande adressée à Azure Search doit inclure le nom du service et une clé API. Il existe deux types de clé API :

- *Clés d’administration* accorder des droits d’accès complets à toutes les opérations. Cela inclut la gestion du service, la création et la suppression des index et des sources de données.
- *Clés de requête* accorder l’accès en lecture seule aux index et documents et doit être utilisé par les applications qui émettent des demandes de recherche.

La demande plus courantes dans Azure Search consiste à exécuter une requête. Il existe deux types de requête qui peuvent être envoyées :

- Un *recherche* requête recherche un ou plusieurs éléments dans tous les champs de recherche dans un index. Requêtes de recherche sont créés à l’aide de la syntaxe simplifiée, ou la syntaxe de requête Lucene. Pour plus d’informations, consultez [syntaxe de requête Simple dans Azure Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), et [syntaxe de requête Lucene dans Azure Search](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Un *filtre* requête évalue une expression booléenne dans tous les champs filtrables dans un index. Filtrer les requêtes sont créées à l’aide d’un sous-ensemble du langage de filtre OData. Pour plus d’informations, consultez [syntaxe d’Expression OData pour Azure Search](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Requêtes de recherche et de requêtes de filtre peuvent être utilisés séparément ou ensemble. Utilisés conjointement, la requête de filtre est appliquée en premier à la totalité de l’index, et ensuite la requête de recherche est effectuée sur les résultats de la requête de filtre.

Recherche Azure prend également en charge lors de la récupération des suggestions basées sur une entrée de recherche. Pour plus d’informations, consultez [Suggestion requêtes](#suggestions).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="setup"></a>Programme d'installation

Le processus d’intégration d’Azure Search dans une application Xamarin.Forms est comme suit :

1. Créer un service Azure Search. Pour plus d’informations, consultez [créer un service Azure Search à l’aide du portail Azure](/azure/search/search-create-service-portal/).
1. Supprimez Silverlight comme framework cible de la solution Xamarin.Forms bibliothèque de classes Portable (PCL). Pour ce faire, vous pouvez modifier le profil PCL à n’importe quel profil qui prend en charge le développement multiplateforme, mais ne prend pas en charge Silverlight, telles que le profil 151 ou 92.
1. Ajouter le [bibliothèque de recherche Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) package NuGet au projet bibliothèque de classes portable dans la solution Xamarin.Forms.

Après avoir effectué ces étapes, l’API de bibliothèque de recherche Microsoft peut être utilisé pour gérer les sources de données et les index de recherche, charger et gérer des documents et exécuter des requêtes.

## <a name="creating-the-azure-search-index"></a>Création de l’Index de recherche Azure

Un schéma d’index doit être défini qui mappe à la structure des données à rechercher. Cela peut être mis en œuvre dans le portail Azure, ou par programmation à l’aide la `SearchServiceClient` classe. Cette classe gère les connexions à Azure Search et peut être utilisée pour créer un index. L’exemple de code suivant montre comment créer une instance de cette classe :

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

Le `SearchServiceClient` surcharge de constructeur accepte un nom de service de recherche et un `SearchCredentials` de l’objet en tant qu’arguments, avec le `SearchCredentials` habillage de l’objet le *clé d’administration* pour le service recherche Azure. Le *clé d’administration* est requis pour créer un index.

> [!NOTE]
> Un seul `SearchServiceClient` instance doit être utilisée dans une application pour éviter l’ouverture de trop nombreuses connexions à Azure Search.

Un index est défini par le `Index` de l’objet, comme illustré dans l’exemple de code suivant :

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

Le `Index.Name` propriété doit être définie sur le nom de l’index et le `Index.Fields` propriété doit être définie sur un tableau de `Field` objets. Chaque `Field` instance spécifie un nom, un type et toutes les propriétés, qui spécifient la façon dont le champ est utilisé. Ces propriétés sont les suivantes :

- `IsKey` : indique si le champ est la clé de l’index. Qu’un seul champ dans l’index, de type `DataType.String`, doit être désigné en tant que le champ de clé.
- `IsFacetable` : indique s’il est possible d’effectuer la navigation à facettes sur ce champ. La valeur par défaut est `false`.
- `IsFilterable` : indique si le champ peut être utilisé dans les requêtes de filtre. La valeur par défaut est `false`.
- `IsRetrievable` : indique si le champ peut être récupéré dans les résultats de la recherche. La valeur par défaut est `true`.
- `IsSearchable` : indique si le champ est inclus dans les recherches en texte intégral. La valeur par défaut est `false`.
- `IsSortable` : indique si le champ peut être utilisé dans `OrderBy` expressions. La valeur par défaut est `false`.

> [!NOTE]
> Modification d’un index après son déploiement implique la reconstruction et de rechargement des données.

Un `Index` objet peut éventuellement spécifier un `Suggesters` propriété, qui définit les champs dans l’index à utiliser pour prendre en charge la saisie semi-automatique ou suggestion de recherche. Le `Suggesters` propriété doit être définie sur un tableau de `Suggester` les objets qui définissent les champs qui sont utilisés pour générer des résultats de la suggestion de la recherche.

Après avoir créé le `Index` de l’objet, l’index est créé en appelant `Indexes.Create` sur la `SearchServiceClient` instance.

> [!NOTE]
> Lors de la création d’un index à partir d’une application qui doit être conservé réactif, utilisez le `Indexes.CreateAsync` (méthode).

Pour plus d’informations, consultez [créer un index Azure Search à l’aide du SDK .NET](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Suppression de l’Index de recherche Azure

Un index peut être supprimé en appelant `Indexes.Delete` sur la `SearchServiceClient` instance :

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Télécharger des données dans l’Index de recherche Azure

Après avoir défini l’index, les données peuvent être téléchargées à l’aide d’un des deux modèles :

- **Modèle d’extraction** : les données sont ingérées périodiquement à partir d’Azure Cosmos DB, Azure SQL Database, stockage Blob Azure ou SQL Server hébergé dans une Machine virtuelle Azure.
- **Modèle push** : données sont envoyées par programmation à l’index. Voici le modèle adopté dans cet article.

Un `SearchIndexClient` instance doit être créée pour importer des données dans l’index. Cela peut être accompli en appelant le `SearchServiceClient.Indexes.GetClient` (méthode), comme illustré dans l’exemple de code suivant :

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

Les données doivent être importées dans l’index sont empaquetées comme un `IndexBatch` objet qui encapsule une collection de `IndexAction` objets. Chaque `IndexAction` instance contient un document et une propriété qui indique à Azure Search à l’action à effectuer sur le document. Dans l’exemple de code ci-dessus, le `IndexAction.Upload` action est spécifiée, les résultats dans le document est inséré dans l’index s’il est nouveau, ou remplacée s’il existe déjà. Le `IndexBatch` objet est ensuite envoyé à l’index en appelant le `Documents.Index` méthode sur le `SearchIndexClient` objet. Pour plus d’informations sur les autres actions d’indexation, consultez [décider d’utiliser l’action d’indexation](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Uniquement 1 000 documents peuvent être inclus dans une même demande d’indexation.

Notez que dans l’exemple de code ci-dessus, le `monkeyList` collection est créée en tant qu’un objet anonyme à partir d’une collection de `Monkey` objets. Cela crée les données pour le `id` champ et résout le mappage de casse Pascal `Monkey` noms de champ d’index de recherche des noms de propriété à casse mixte. Vous pouvez également, ce mappage également possible en ajoutant le `[SerializePropertyNamesAsCamelCase]` attribut le `Monkey` classe.

Pour plus d’informations, consultez [charger des données dans Azure Search à l’aide du SDK .NET](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Interrogation de l’Index de recherche Azure

Un `SearchIndexClient` instance doit être créée pour interroger un index. Lorsqu’une application exécute des requêtes, il est recommandé de suivre le principe du moindre privilège et de créer un `SearchIndexClient` directement, en passant le *clé de requête* en tant qu’argument. Cela garantit que les utilisateurs ont accès en lecture seule aux index et documents. Cette approche est illustrée dans l’exemple de code suivant :

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

Le `SearchIndexClient` surcharge de constructeur accepte un nom de service de recherche, le nom d’index et un `SearchCredentials` de l’objet en tant qu’arguments, avec le `SearchCredentials` habillage de l’objet le *clé de requête* pour le service recherche Azure.

### <a name="search-queries"></a>Requêtes de recherche

L’index peut être interrogé en appelant le `Documents.SearchAsync` méthode sur le `SearchIndexClient` de l’instance, comme illustré dans l’exemple de code suivant :

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

Le `SearchAsync` méthode prend un argument de texte de recherche et éventuellement un `SearchParameters` objet qui peut être utilisé pour affiner la requête. Une requête de recherche est déterminée comme argument de texte de recherche, alors qu’une requête de filtre peut être spécifiée en définissant le `Filter` propriété de la `SearchParameters` argument. L’exemple de code suivant montre que les deux types de requête :

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Cette requête de filtre est appliquée à la totalité de l’index et supprime des documents à partir des résultats où les `location` champ n’est pas égal à la Chine et non égal à Vietnam. Après le filtrage, la requête de recherche est effectuée sur les résultats de la requête de filtre.

> [!NOTE]
> Pour filtrer sans rechercher, passer `*` en tant que l’argument de texte de recherche.

Le `SearchAsync` méthode retourne un `DocumentSearchResult` objet qui contient les résultats de requête. Cet objet est énuméré, avec chaque `Document` de l’objet en cours de création en tant qu’un `Monkey` de l’objet et ajouté à la `Monkeys` `ObservableCollection` pour l’affichage. Suivant des captures d’écran Afficher résultats retournés à partir d’Azure Search :

![](azure-search-images/search.png "Search Results")

Pour plus d’informations sur la recherche et le filtrage, consultez [interroger votre index Azure Search à l’aide du SDK .NET](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Requêtes de suggestion

Recherche Azure permet de suggestions à demander selon une requête de recherche, en appelant le `Documents.SuggestAsync` méthode sur le `SearchIndexClient` instance. Cela est illustré dans l’exemple de code suivant :

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

Le `SuggestAsync` méthode accepte un argument de texte de recherche, le nom du Générateur de suggestions à utiliser (qui est défini dans l’index), et éventuellement un `SuggestParameters` objet qui peut être utilisé pour affiner la requête. Le `SuggestParameters` instance définit les propriétés suivantes :

- `UseFuzzyMatching` – Lorsque la valeur `true`, Azure Search trouve des suggestions même s’il existe un caractère erroné ou manquant dans le texte de recherche.
- `HighlightPreTag` – la balise qui est ajoutée devant les correspondances de suggestion.
- `HighlightPostTag` – la balise qui est ajoutée à l’accès de la suggestion.
- `MinimumCoverage` : représente le pourcentage de l’index doit être couvert par une requête de suggestion pour la requête soit signalé un succès. La valeur par défaut est 80.
- `Top` – le nombre de suggestions à récupérer. Il doit être un entier compris entre 1 et 100, avec 5 comme valeur par défaut.

L’effet global est que les 10 premiers résultats à partir de l’index seront retournés avec mise en surbrillance d’accès, et les résultats doivent inclure les documents qui contiennent la même orthographié termes de recherche.

Le `SuggestAsync` méthode retourne un `DocumentSuggestResult` objet qui contient les résultats de requête. Cet objet est énuméré, avec chaque `Document` de l’objet en cours de création en tant qu’un `Monkey` de l’objet et ajouté à la `Monkeys` `ObservableCollection` pour l’affichage. Les captures d’écran suivantes montrent les résultats de suggestion retournés à partir d’Azure Search :

![](azure-search-images/suggest.png "Suggestion Results")

Notez que dans l’exemple d’application, le `SuggestAsync` méthode est appelée uniquement lorsque l’utilisateur termine la saisie d’un terme de recherche. Toutefois, il peut également être utilisé pour prendre en charge les requêtes de recherche de saisie semi-automatique en exécutant sur chaque keypress.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment utiliser la bibliothèque de recherche Microsoft Azure pour intégrer Azure Search dans une application Xamarin.Forms. Recherche Azure est un service cloud qui fournit l’indexation et l’interrogation des fonctionnalités pour les données téléchargées. Cette opération supprime les exigences de l’infrastructure et la recherche algorithme complexité traditionnellement avec implémentation de la fonctionnalité de recherche dans une application.

## <a name="related-links"></a>Liens associés

- [Recherche Azure (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Documentation recherche Azure](/azure/search/)
- [Bibliothèque de recherche Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
