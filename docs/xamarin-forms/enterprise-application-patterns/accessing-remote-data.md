---
title: Accès aux données distantes
description: Ce chapitre explique comment l’application mobile eShopOnContainers accède aux données des microservices en conteneur.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a4c58139b0ddbaaedf5769eeac6585bac4c013e4
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832108"
---
# <a name="accessing-remote-data"></a>Accès aux données distantes

Assurez-vous de nombreuses solutions modernes basée sur le web utilisation des services web, hébergés par des serveurs web, pour fournir des fonctionnalités pour le client à distance des applications. Les opérations qui expose un service web constituent une API web.

Les applications clientes doivent être en mesure d’utiliser l’API web sans connaître la façon dont les données ou les opérations qui expose l’API sont implémentées. Cela nécessite que l’API doit respecter des normes communes qui permettent un client application et le service web d’accord sur les formats de données à utiliser et la structure des données qui sont échangées entre les applications clientes et le service web.

## <a name="introduction-to-representational-state-transfer"></a>Introduction à Representational State Transfer

Representational State Transfer (REST) est un style d’architecture pour la création de systèmes distribués basés sur l’hypermédia. Des principaux avantages du modèle REST sont qu’il a fondé sur des normes ouvertes et qu’il ne lie pas l’implémentation du modèle ou les applications clientes qui y accèdent à une implémentation spécifique. Par conséquent, un service web REST peut être implémenté à l’aide de Microsoft ASP.NET Core MVC et les applications clientes pourraient être développement à l’aide de n’importe quel langage et un ensemble d’outils qui peut générer des requêtes HTTP et analyser les réponses HTTP.

Le modèle REST utilise un schéma de navigation pour représenter les objets et les services sur un réseau, appelé ressources. Les systèmes qui implémentent REST généralement utilisent le protocole HTTP pour transmettre les demandes à accéder à ces ressources. Dans de tels systèmes, une application cliente envoie une requête sous la forme d’un URI qui identifie une ressource et une méthode HTTP (par exemple, GET, POST, PUT ou DELETE) qui indique l’opération à effectuer sur cette ressource. Le corps de la requête HTTP contient les données requises pour effectuer l’opération.

> [!NOTE]
> REST définit un modèle de requête sans état. Par conséquent, les requêtes HTTP doivent être indépendantes et peuvent se produire dans n’importe quel ordre.

La réponse à partir d’une REST demande rend utiliser des codes d’état HTTP standards. Par exemple, une requête qui retourne des données valides doit inclure le code de réponse HTTP 200 (OK), pendant une demande qui ne parvient pas à trouver ou supprimer une ressource spécifique doit renvoyer une réponse qui inclut le code d’état HTTP 404 (introuvable).

Une API web RESTful expose un ensemble de ressources connectées et fournit les opérations principales qui permettent à une application manipuler ces ressources et de naviguer facilement entre eux. Pour cette raison, les URI qui constituent une API web RESTful classique sont orientées vers les données qu’il expose et utiliser les fonctionnalités fournies par HTTP pour agir sur ces données.

Les données incluses par une application cliente dans une requête HTTP et les messages de réponse correspondants à partir du serveur web, pourraient être présentées dans une variété de formats, appelés types de médias. Lorsqu’une application cliente envoie une requête qui retourne des données dans le corps d’un message, il peut spécifier les types de médias il peut gérer dans le `Accept` en-tête de la requête. Si le serveur web prend en charge ce type de média, il peut renvoyer une réponse qui inclut le `Content-Type` en-tête qui spécifie le format des données dans le corps du message. Il est alors la responsabilité de l’application cliente pour analyser le message de réponse et d’interpréter les résultats dans le corps du message de manière appropriée.

Pour plus d’informations sur REST, consultez [conception d’API](/azure/architecture/best-practices/api-design/) et [implémentation de l’API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Utilisation d’API RESTful

L’application mobile eShopOnContainers utilise le modèle Model-View-ViewModel (MVVM) et les éléments de modèle des représentent le modèle utilisées par les entités de domaine dans l’application. Les classes de contrôleur et le référentiel de l’application de référence eShopOnContainers acceptent et renvoient la plupart de ces objets de modèle. Par conséquent, ils sont utilisés en tant qu’objets de transfert de données (DTO) qui contiennent toutes les données transmises entre l’application mobile et les microservices en conteneur. Le principal avantage de l’utilisation de DTO pour passer des données et de recevoir des données à partir d’un service web est que, par la transmission des données plus dans un seul appel à distance, l’application peut réduire le nombre d’appels distants qui doivent être apportées.

### <a name="making-web-requests"></a>Effectuer des requêtes web

L’application mobile d’eShopOnContainers utilise la `HttpClient` classe pour effectuer des demandes sur HTTP, JSON utilisé comme type de support. Cette classe fournit des fonctionnalités pour envoyer des requêtes HTTP de façon asynchrone et recevoir des réponses HTTP à partir d’un URI identifié des ressources. Le `HttpResponseMessage` classe représente un message de réponse HTTP reçu à partir d’une API REST après la réalisation d’une requête HTTP. Il contient des informations sur la réponse, y compris le code d’état, des en-têtes et des corps. Le `HttpContent` classe représente le corps HTTP et des en-têtes de contenu, tel que `Content-Type` et `Content-Encoding`. Le contenu peut être lu à l’aide de la `ReadAs` méthodes, telles que `ReadAsStringAsync` et `ReadAsByteArrayAsync`, selon le format des données.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Effectue une demande GET

Le `CatalogService` classe est utilisée pour gérer le processus d’extraction de données à partir du microservice de catalogue. Dans le `RegisterDependencies` méthode dans le `ViewModelLocator` (classe), le `CatalogService` classe est enregistrée comme un mappage de type par rapport à la `ICatalogService` type avec le conteneur d’injection de dépendance Autofac. Ensuite, lorsqu’une instance de la `CatalogViewModel` classe est créée, son constructeur accepte un `ICatalogService` type, qui résout de Autofac, qui retourne une instance de la `CatalogService` classe. Pour plus d’informations sur l’injection de dépendances, consultez [Injection de dépendance](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figure 10-1 illustre l’interaction des classes qui lisent les données de catalogue à partir du microservice de catalogue pour l’affichage par le `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Récupération des données à partir du microservice de catalogue")](accessing-remote-data-images/catalogdata-large.png#lightbox "récupération des données à partir du microservice de catalogue")

**Figure 10-1**: Récupération des données à partir du microservice de catalogue

Lorsque le `CatalogView` cible, le `OnInitialize` méthode dans la `CatalogViewModel` classe est appelée. Cette méthode récupère les données du catalogue à partir du microservice de catalogue, comme illustré dans l’exemple de code suivant :

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Cette méthode appelle la `GetCatalogAsync` méthode de la `CatalogService` instance qui a été injecté dans le `CatalogViewModel` par Autofac. L’exemple de code suivant montre la méthode `GetCatalogAsync` :

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Cette méthode génère l’URI qui identifie la ressource de la demande va être envoyée à et utilise le `RequestProvider` classe pour appeler la méthode HTTP GET sur la ressource, avant de retourner les résultats à le `CatalogViewModel`. Le `RequestProvider` classe contient des fonctionnalités qui envoie une requête sous la forme d’un URI qui identifie une ressource, une méthode HTTP qui indique l’opération à effectuer sur cette ressource, et un corps qui contient toutes les données requises pour effectuer l’opération. Pour plus d’informations sur la façon dont `RequestProvider` classe est injectée dans le `CatalogService class`, consultez [Injection de dépendance](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Le code suivant montre l’exemple le `GetAsync` méthode dans la `RequestProvider` classe :

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Cette méthode appelle la `CreateHttpClient` (méthode), qui retourne une instance de la `HttpClient` classe avec l’ensemble d’en-têtes appropriés. Il soumet ensuite une demande GET asynchrone à la ressource identifiée par l’URI, avec la réponse en cours stockée dans le `HttpResponseMessage` instance. Le `HandleResponse` méthode est ensuite appelée, ce qui lève une exception si la réponse n’inclut pas un code d’état HTTP de succès. Ensuite la réponse est lu en tant que chaîne, convertie à partir de JSON pour un `CatalogRoot` de l’objet et renvoyé à la `CatalogService`.

Le `CreateHttpClient` méthode est indiquée dans l’exemple de code suivant :

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Cette méthode crée une nouvelle instance de la `HttpClient` classe et affecte le `Accept` en-tête de toutes les demandes effectuées par le `HttpClient` l’instance à `application/json`, ce qui indique qu’elle attend le contenu de toute réponse à mettre en forme à l’aide de JSON. Ensuite, si un jeton d’accès a été passé en tant qu’argument à la `CreateHttpClient` (méthode), il est ajouté à la `Authorization` en-tête de toutes les demandes effectuées par le `HttpClient` instance, le préfixe avec la chaîne `Bearer`. Pour plus d’informations sur les autorisations, consultez [autorisation](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Lorsque le `GetAsync` méthode dans le `RequestProvider` classe appels `HttpClient.GetAsync`, le `Items` méthode dans la `CatalogController` classe dans le projet Catalog.API est appelé, ce qui est indiqué dans l’exemple de code suivant :

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Cette méthode récupère les données de catalogue à partir de la base de données SQL à l’aide d’Entity Framework et il retourne sous la forme d’un message de réponse qui inclut un code d’état HTTP de succès, et une collection de JSON au format `CatalogItem` instances.

#### <a name="making-a-post-request"></a>Effectue une demande POST

Le `BasketService` classe est utilisée pour gérer la récupération de données et des mises à jour avec le microservice de panier d’achat. Dans le `RegisterDependencies` méthode dans le `ViewModelLocator` (classe), le `BasketService` classe est enregistrée comme un mappage de type par rapport à la `IBasketService` type avec le conteneur d’injection de dépendance Autofac. Ensuite, lorsqu’une instance de la `BasketViewModel` classe est créée, son constructeur accepte un `IBasketService` type, qui résout de Autofac, qui retourne une instance de la `BasketService` classe. Pour plus d’informations sur l’injection de dépendances, consultez [Injection de dépendance](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figure 10-2 montre l’interaction des classes qui envoient les données du panier d’achat affichées par le `BasketView`, pour le microservice de panier d’achat.

[![](accessing-remote-data-images/basketdata.png "Envoi de données pour le microservice basket")](accessing-remote-data-images/basketdata-large.png#lightbox "envoi de données pour le microservice de panier d’achat")

**Figure 10-2**: Envoi de données pour le microservice de panier d’achat

Lorsqu’un élément est ajouté au panier d’achat, le `ReCalculateTotalAsync` méthode dans la `BasketViewModel` classe est appelée. Cette méthode met à jour la valeur totale des éléments dans le panier et envoie les données du panier d’achat pour le microservice basket, comme illustré dans l’exemple de code suivant :

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Cette méthode appelle la `UpdateBasketAsync` méthode de la `BasketService` instance qui a été injecté dans le `BasketViewModel` par Autofac. L’exemple de méthode suivant le `UpdateBasketAsync` méthode :

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Cette méthode génère l’URI qui identifie la ressource de la demande va être envoyée à et utilise le `RequestProvider` classe pour appeler la méthode HTTP POST sur la ressource, avant de retourner les résultats à le `BasketViewModel`. Notez qu’un jeton d’accès obtenu à partir de IdentityServer pendant le processus d’authentification est nécessaire pour autoriser les demandes pour le microservice de panier d’achat. Pour plus d’informations sur les autorisations, consultez [autorisation](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

L’exemple de code suivant montre un de la `PostAsync` méthodes dans la `RequestProvider` classe :

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Cette méthode appelle la `CreateHttpClient` (méthode), qui retourne une instance de la `HttpClient` classe avec l’ensemble d’en-têtes appropriés. Il soumet ensuite une requête POST asynchrone à la ressource identifiée par l’URI, avec les données sérialisées du panier d’achat envoyées dans le format JSON et la réponse qui est stocké dans le `HttpResponseMessage` instance. Le `HandleResponse` méthode est ensuite appelée, ce qui lève une exception si la réponse n’inclut pas un code d’état HTTP de succès. Lisez ensuite la réponse sous forme de chaîne, converti à partir de JSON pour un `CustomerBasket` de l’objet et renvoyé à la `BasketService`. Pour plus d’informations sur la `CreateHttpClient` (méthode), consultez [effectue une demande GET](#making_a_get_request).

Lorsque le `PostAsync` méthode dans le `RequestProvider` classe appels `HttpClient.PostAsync`, le `Post` méthode dans la `BasketController` classe dans le projet Basket.API est appelé, ce qui est indiqué dans l’exemple de code suivant :

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Cette méthode utilise une instance de la `RedisBasketRepository` classe pour conserver les données du panier d’achat dans le cache Redis et la retourne comme un message de réponse qui inclut un code d’état HTTP de succès et JSON au format `CustomerBasket` instance.

#### <a name="making-a-delete-request"></a>Effectue une demande de suppression

Figure 10-3 montre les interactions entre les classes qui suppriment les données du panier d’achat à partir du microservice de panier d’achat, pour le `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Données de suppression à partir du microservice de panier d’achat")

**Figure 10-3**: Suppression des données à partir du microservice de panier d’achat

Lorsque le processus de validation est appelé, le `CheckoutAsync` méthode dans la `CheckoutViewModel` classe est appelée. Cette méthode crée un nouvel ordre, avant d’effacer le panier, comme illustré dans l’exemple de code suivant :

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Cette méthode appelle la `ClearBasketAsync` méthode de la `BasketService` instance qui a été injecté dans le `CheckoutViewModel` par Autofac. L’exemple de méthode suivant le `ClearBasketAsync` méthode :

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Cette méthode génère l’URI qui identifie la ressource à laquelle la demande va être envoyée à et utilise le `RequestProvider` classe pour appeler la méthode HTTP DELETE sur la ressource. Notez qu’un jeton d’accès obtenu à partir de IdentityServer pendant le processus d’authentification est nécessaire pour autoriser les demandes pour le microservice de panier d’achat. Pour plus d’informations sur les autorisations, consultez [autorisation](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Le code suivant montre l’exemple le `DeleteAsync` méthode dans la `RequestProvider` classe :

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Cette méthode appelle la `CreateHttpClient` (méthode), qui retourne une instance de la `HttpClient` classe avec l’ensemble d’en-têtes appropriés. Il soumet ensuite une demande de suppression asynchrone à la ressource identifiée par l’URI. Pour plus d’informations sur la `CreateHttpClient` (méthode), consultez [effectue une demande GET](#making_a_get_request).

Lorsque le `DeleteAsync` méthode dans le `RequestProvider` classe appels `HttpClient.DeleteAsync`, le `Delete` méthode dans la `BasketController` classe dans le projet Basket.API est appelé, ce qui est indiqué dans l’exemple de code suivant :

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Cette méthode utilise une instance de la `RedisBasketRepository` classe pour supprimer les données du panier d’achat à partir du cache Redis.

## <a name="caching-data"></a>Mise en cache des données

Les performances d’une application peuvent être améliorées en mettant en cache les données fréquemment sollicitées dans un stockage rapide situé près de l’application. Si le stockage rapide se trouve plus près à l’application que la source d’origine, la mise en cache peut améliorer considérablement la réponse fois lors de la récupération des données.

La forme la plus courante de mise en cache est différée la mise en cache, où une application récupère des données en référençant le cache. Si les données n’est pas dans le cache, il a récupéré dans le magasin de données et ajoutés au cache. Les applications peuvent implémenter la double mise en cache avec le modèle cache-aside. Ce modèle détermine si l’élément est actuellement dans le cache. Si l’élément n’est pas dans le cache, il a lu à partir du magasin de données et ajoutés au cache. Pour plus d’informations, consultez le [Cache-Aside](/azure/architecture/patterns/cache-aside/) modèle.

> [!TIP]
> Mettre en cache les données fréquemment lues et qui changent rarement. Ces données peuvent être ajoutées au cache à la demande la première fois, qu'il est récupéré par une application. Cela signifie que l’application doit extraire les données qu’une seule fois à partir du magasin de données, et que l’accès suivant peuvent être satisfait en utilisant le cache.

Les applications distribuées, telles que l’application, de référence eShopOnContainers doivent fournir ou pour les deux les caches suivants :

-   Un cache partagé, qui est accessible par plusieurs processus ou ordinateurs.
-   Un cache privé, où les données sont stockées localement sur l’appareil qui exécute l’application.

L’application mobile eShopOnContainers utilise un cache privé, où les données sont stockées localement sur l’appareil qui exécute une instance de l’application. Pour plus d’informations sur le cache utilisé par l’application de référence eShopOnContainers, consultez [.NET Microservices : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

> [!TIP]
> Considérez le cache comme magasin de données temporaire susceptible de disparaître à tout moment. Assurez-vous que les données sont conservées dans le magasin de données d’origine, ainsi que le cache. Puis de réduire les risques de perte de données si le cache devient indisponible.

### <a name="managing-data-expiration"></a>Gérer l’Expiration des données

Il est peu pratique d’attendre que les données mises en cache sera toujours cohérentes avec les données d’origine. Les données dans le magasin de données d’origine peuvent changer lorsqu’il est été mis en cache, rendant les données mises en cache deviennent obsolètes. Par conséquent, applications doivent implémenter une stratégie qui permet de se pour assurer que les données dans le cache sont plus à jour que possible, mais peut également détecter et gérer les situations dans lesquelles les données dans le cache sont devenues obsolètes. Mécanismes de mise en cache plus activent le cache à être configuré pour faire expirer les données et donc réduisent la période pendant laquelle les données peuvent être obsolètes.

> [!TIP]
> Définir une date d’expiration par défaut du temps lors de la configuration d’un cache. De nombreux caches intègrent d’expiration, ce qui invalide les données et supprime du cache si elle n’est pas accessible pour une période spécifiée. Toutefois, être vigilant lors du choix de la période d’expiration. Si elle est effectuée trop court, données expire trop rapidement, et les avantages de la mise en cache seront réduits. S’il s’est trop long, les risques de données devenir obsolètes. Par conséquent, le délai d’expiration doit correspondre au modèle d’accès pour les applications qui utilisent les données.

Lorsque les données mises en cache expirent, elle doit être supprimée à partir du cache, et l’application doit récupérer les magasin de données à partir des données d’origine et placez-le dans le cache.

Il est également possible qu’un cache soit saturé si des données sont autorisées à rester pour une période trop longue. Par conséquent, les demandes d’ajout de nouveaux éléments au cache peuvent être nécessaires pour supprimer certains éléments dans un processus appelé *éviction*. Services de mise en cache suppriment généralement les données de manière moins récemment utilisées. Toutefois, il existe des autres stratégies d’éviction, y compris les plus récemment utilisés et in-first-out. Pour plus d’informations, consultez [des conseils de mise en cache](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>La mise en cache des Images

L’application mobile eShopOnContainers consomme des images de produits à distance qui bénéficient de la mise en cache. Ces images sont affichés par le [ `Image` ](xref:Xamarin.Forms.Image) contrôle et le `CachedImage` contrôle fourni par le [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) bibliothèque.

Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image) contrôle prend en charge la mise en cache des images téléchargées. La mise en cache est activée par défaut et stocke l’image localement pendant 24 heures. En outre, le délai d’expiration peut être configuré avec le [ `CacheValidity` ](xref:Xamarin.Forms.UriImageSource.CacheValidity) propriété. Pour plus d’informations, consultez [téléchargé la mise en cache d’Image](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

De FFImageLoading `CachedImage` contrôle est un remplacement pour le Xamarin.Forms [ `Image` ](xref:Xamarin.Forms.Image) contrôle, en fournissant des propriétés supplémentaires qui activent des fonctionnalités supplémentaires. Entre cette fonctionnalité, le contrôle fournit une mise en cache configurable, tout en prenant en charge d’erreur et le chargement des espaces réservés de l’image. L’exemple de code suivant montre comment l’application mobile eShopOnContainers utilise la `CachedImage` dans contrôler le `ProductTemplate`, qui est le modèle de données utilisé par le [ `ListView` ](xref:Xamarin.Forms.ListView) dans contrôler le `CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

Le `CachedImage` contrôle définit le `LoadingPlaceholder` et `ErrorPlaceholder` propriétés pour les images propres à la plateforme. Le `LoadingPlaceholder` propriété spécifie l’image à afficher lors de l’image spécifiée par la `Source` propriété est récupérée et le `ErrorPlaceholder` propriété spécifie l’image à afficher si une erreur se produit lorsque vous tentez de récupérer l’image spécifié par le `Source` propriété.

Comme son nom l’indique, le `CachedImage` contrôle met en cache des images à distance sur l’appareil pendant la durée spécifiée par la valeur de la `CacheDuration` propriété. Lorsque cette valeur de propriété n’est pas définie explicitement, la valeur par défaut de 30 jours est appliquée.

## <a name="increasing-resilience"></a>Augmenter la résilience

Toutes les applications qui communiquent avec les ressources et services à distance doivent être sensibles à des défaillances temporaires. Les erreurs temporaires incluent la perte momentanée de la connectivité réseau vers les services, l’indisponibilité temporaire d’un service ou les expirations de délai qui surviennent lorsqu’un service est occupé. Ces erreurs sont corrigent souvent, et si l’action est répétée après un délai approprié, il est susceptible de réussir.

Erreurs temporaires peuvent avoir un impact important sur la qualité d’une application, même s’il a été testé en toutes circonstances prévisibles. Pour vous assurer qu’une application qui communique avec les services à distance opère de façon fiable, il doit être en mesure d’effectuer toutes les conditions suivantes :

-   Détecter les erreurs lorsqu’ils se produisent et déterminent si les erreurs sont susceptibles d’être temporaire.
-   Recommencez l’opération si elle détermine que l’erreur est susceptible d’être temporaire et de suivre le nombre de fois où que l’opération a été tentée.
-   Utilisez une stratégie de nouvelle tentative appropriée, qui spécifie le nombre de nouvelles tentatives, le délai entre chaque tentative et les actions à entreprendre après l’échec d’une tentative.

Cette gestion des erreurs temporaires peuvent être obtenue en encapsulant toutes les tentatives d’accéder à un service à distance dans le code qui implémente le modèle de nouvelle tentative.

### <a name="retry-pattern"></a>Modèle nouvelle tentative

Si une application détecte un échec lorsqu’il tente d’envoyer une demande à un service distant, il peut gérer l’échec dans une des manières suivantes :

-   Recommencer l’opération. L’application peut réessayer la requête ayant échoué immédiatement.
-   Recommencer l’opération après un certain délai. L’application doit attendre un délai approprié avant de réessayer la demande.
-   L’annulation de l’opération. L’application doit annuler l’opération et signaler une exception.

La stratégie de nouvelle tentative doit être paramétrée pour répondre aux exigences métier de l’application. Par exemple, il est important optimiser le nombre de tentatives et l’intervalle à l’opération en cours a tenté de nouvelle tentative. Si l’opération fait partie d’une interaction utilisateur, l’intervalle avant nouvelle tentative doit être courts et uniquement quelques tentatives pour éviter de faire les utilisateurs à attendre une réponse. Si l’opération fait partie d’un workflow en cours d’exécution longue, où l’annulation ou de redémarrer le workflow est onéreux ou fastidieux, il convient pour attendre plus longtemps entre les tentatives et plusieurs nouvelles tentatives.

> [!NOTE]
> Une stratégie agressive de nouvelle tentative avec un délai minimal entre les tentatives et un grand nombre de nouvelles tentatives, peut se dégrader un service distant est en cours d’exécution ou est sur la capacité. En outre, une telle stratégie de nouvelle tentative peut également affecter la réactivité de l’application si elle tente en permanence effectuer une opération ayant échoué.

Si une demande échoue encore après un certain nombre de nouvelles tentatives, il est préférable de l’application pour empêcher les autres requêtes destinées à la même ressource et pour signaler un échec. Ensuite, après une période définie, l’application peut effectuer une ou plusieurs demandes à la ressource pour voir si elles aboutissent. Pour plus d’informations, consultez [modèle disjoncteur](#circuit_breaker_pattern).

> [!TIP]
> Ne jamais mettre en œuvre un mécanisme de nouvelle tentative infini. Utilisez un nombre fini de nouvelles tentatives ou implémentez le [disjoncteur](/azure/architecture/patterns/circuit-breaker/) modèle pour autoriser un service à récupérer.

L’application mobile eShopOnContainers n’implémente pas actuellement le modèle nouvelle tentative lors de la création de demandes web RESTful. Toutefois, le `CachedImage` contrôle, fourni par le [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) bibliothèque prend en charge la gestion des erreurs temporaires par une nouvelle tentative de chargement de l’image. En cas de chargement d’image, autre tentative sera effectuée. Le nombre de tentatives spécifié par le `RetryCount` propriété et les nouvelles tentatives se produit après un délai spécifié par le `RetryDelay` propriété. Si ces valeurs de propriété ne sont pas définies explicitement, par défaut les valeurs sont appliquées – 3 pour le `RetryCount` propriété et 250 MS pour la `RetryDelay` propriété. Pour plus d’informations sur la `CachedImage` du contrôle, consultez [la mise en cache des Images](#caching_images).

L’application de référence eShopOnContainers implémente le modèle nouvelle tentative. Pour plus d’informations, y compris une discussion sur la manière de combiner le modèle de nouvelle tentative avec le `HttpClient` de classe, consultez [.NET Microservices : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

Pour plus d’informations sur le modèle nouvelle tentative, consultez le [de nouvelle tentative](/azure/architecture/patterns/retry/) modèle.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Modèle disjoncteur

Dans certaines situations, les erreurs peuvent se produire en raison d’événements anticipés qui prennent plus de temps à corriger. Ces erreurs peut aller d’une perte partielle de connectivité à la défaillance complète d’un service. Dans ces situations, il est inutile pour une application de retenter une opération qui a peu de chances de réussir et à la place doit accepter que l’opération a échoué et de gérer cet échec en conséquence.

Le modèle disjoncteur peut empêcher une application à plusieurs reprises exécuter une opération qui est susceptible d’échouer, tout en permettant l’application détecter si l’erreur a été résolu.

> [!NOTE]
> L’objectif du modèle disjoncteur est différent du modèle nouvelle tentative. Le modèle nouvelle tentative permet à une application retenter une opération en partant du principe qu’elle réussira. Le modèle disjoncteur empêche l’application d’effectuer une opération qui échouera peut.

Un disjoncteur agit comme un proxy pour les opérations qui risquent d’échouer. Le proxy doit surveiller le nombre d’échecs récents qui se sont produites et utiliser ces informations pour décider s’il faut autoriser l’opération pour continuer, ou pour retourner une exception immédiatement.

Actuellement, l’application mobile eShopOnContainers n’implémente pas le modèle disjoncteur. Cependant, n’est eShopOnContainers. Pour plus d’informations, consultez [.NET Microservices : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

> [!TIP]
> Combiner les modèles de disjoncteur et réessayez. Une application peut combiner les modèles de disjoncteur et réessayez en utilisant le modèle de nouvelle tentative pour appeler une opération via un disjoncteur. Toutefois, la logique de nouvelle tentative doit être sensible aux exceptions retournées par le disjoncteur et abandonner les nouvelles tentatives si le disjoncteur indique qu’une erreur n’est pas temporaire.

Pour plus d’informations sur le modèle disjoncteur, consultez le [disjoncteur](/azure/architecture/patterns/circuit-breaker/) modèle.

## <a name="summary"></a>Récapitulatif

Assurez-vous de nombreuses solutions modernes basée sur le web utilisation des services web, hébergés par des serveurs web, pour fournir des fonctionnalités pour le client à distance des applications. Les opérations qui expose un service web constituent une API web et les applications clientes doivent être en mesure d’utiliser l’API web sans connaître la façon dont les données ou les opérations qui expose l’API sont implémentées.

Les performances d’une application peuvent être améliorées en mettant en cache les données fréquemment sollicitées dans un stockage rapide situé près de l’application. Les applications peuvent implémenter la double mise en cache avec le modèle cache-aside. Ce modèle détermine si l’élément est actuellement dans le cache. Si l’élément n’est pas dans le cache, il a lu à partir du magasin de données et ajoutés au cache.

Lors de la communication avec les API web, les applications doivent être sensibles à des défaillances temporaires. Les erreurs temporaires incluent la perte momentanée de la connectivité réseau vers les services, l’indisponibilité temporaire d’un service ou les expirations de délai qui surviennent lorsqu’un service est occupé. Ces erreurs sont corrigent souvent, et si l’action est répétée après un délai approprié, il est susceptible de réussir. Par conséquent, les applications doivent encapsuler toutes les tentatives d’accéder à une API web dans le code qui implémente un mécanisme de gestion des problèmes temporaires.


## <a name="related-links"></a>Liens associés

- [Téléchargez le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
