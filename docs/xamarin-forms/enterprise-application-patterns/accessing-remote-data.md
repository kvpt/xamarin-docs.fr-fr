---
title: Accès aux données distantes
description: Ce chapitre explique comment l’application mobile eShopOnContainers accède aux données à partir des microservices en conteneur.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9c793f4d5f0cda5bff2dedef5e4e5e5bdfca69e5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70770801"
---
# <a name="accessing-remote-data"></a>Accès aux données distantes

De nombreuses solutions Web modernes utilisent des services Web hébergés par des serveurs Web pour fournir des fonctionnalités aux applications clientes distantes. Les opérations qu’un service Web expose constituent une API Web.

Les applications clientes doivent être en mesure d’utiliser l’API Web sans savoir comment les données ou opérations exposées par l’API sont implémentées. Cela implique que l’API respecte des normes communes qui permettent à une application cliente et à un service Web de s’accorder sur les formats de données à utiliser, ainsi que la structure des données échangées entre les applications clientes et le service Web.

## <a name="introduction-to-representational-state-transfer"></a>Présentation du transfert d’état de représentation

REST est un style architectural pour la création de systèmes distribués basés sur hypermédia. L’un des principaux avantages du modèle REST est qu’il est basé sur des normes ouvertes et qu’il ne lie pas l’implémentation du modèle ou les applications clientes qui y accèdent à une implémentation spécifique. Par conséquent, un service Web REST peut être implémenté à l’aide de Microsoft ASP.NET Core MVC, et les applications clientes peuvent être en développement à l’aide de n’importe quel langage et ensemble d’outils capables de générer des requêtes HTTP et d’analyser les réponses HTTP.

Le modèle REST utilise un schéma de navigation pour représenter les objets et les services sur un réseau, appelés ressources. Les systèmes qui implémentent REST utilisent généralement le protocole HTTP pour transmettre les demandes d’accès à ces ressources. Dans de tels systèmes, une application cliente envoie une requête sous la forme d’un URI qui identifie une ressource, et une méthode HTTP (par exemple, obtenir, valider, placer ou supprimer) qui indique l’opération à effectuer sur cette ressource. Le corps de la requête HTTP contient toutes les données requises pour effectuer l’opération.

> [!NOTE]
> REST définit un modèle de requête sans État. Par conséquent, les requêtes HTTP doivent être indépendantes et peuvent se produire dans n’importe quel ordre.

La réponse d’une demande REST utilise des codes d’état HTTP standard. Par exemple, une demande qui retourne des données valides doit inclure le code de réponse HTTP 200 (OK), tandis qu’une demande qui ne parvient pas à trouver ou supprimer une ressource spécifiée doit renvoyer une réponse qui inclut le code d’état HTTP 404 (introuvable).

Une API Web RESTful expose un ensemble de ressources connectées et fournit les opérations principales qui permettent à une application de manipuler ces ressources et de naviguer facilement entre elles. Pour cette raison, les URI qui constituent une API Web RESTful classique sont orientés vers les données qu’il expose, et utilisent les fonctionnalités fournies par HTTP pour fonctionner sur ces données.

Les données incluses par une application cliente dans une requête HTTP, ainsi que les messages de réponse correspondants du serveur Web, peuvent être présentées dans différents formats, appelés types de média. Lorsqu’une application cliente envoie une requête qui retourne des données dans le corps d’un message, elle peut spécifier les types de médias qu’elle peut gérer dans l’en-tête `Accept` de la requête. Si le serveur Web prend en charge ce type de média, il peut répondre avec une réponse qui comprend l’en-tête `Content-Type` qui spécifie le format des données dans le corps du message. Il incombe ensuite à l’application cliente d’analyser le message de réponse et d’interpréter les résultats dans le corps du message de manière appropriée.

Pour plus d’informations sur REST, consultez [conception d’API](/azure/architecture/best-practices/api-design/) et implémentation d' [API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Consommation des API RESTful

L’application mobile eShopOnContainers utilise le modèle MVVM (Model-View-ViewModel) et les éléments de modèle du modèle représentent les entités de domaine utilisées dans l’application. Les classes de contrôleur et de référentiel de l’application de référence eShopOnContainers acceptent et retournent un grand nombre de ces objets de modèle. Par conséquent, ils sont utilisés en tant qu’objets de transfert de données (DTO) qui contiennent toutes les données transmises entre l’application mobile et les microservices en conteneur. Le principal avantage de l’utilisation de DTO pour transmettre des données à un service Web et la réception de données à partir d’un service Web est qu’en transmettant plus de données dans un seul appel distant, l’application peut réduire le nombre d’appels distants qui doivent être effectués.

### <a name="making-web-requests"></a>Effectuer des requêtes web

L’application mobile eShopOnContainers utilise la classe `HttpClient` pour effectuer des demandes sur HTTP, avec JSON utilisé comme type de média. Cette classe fournit des fonctionnalités pour envoyer de manière asynchrone des requêtes HTTP et recevoir des réponses HTTP d’une ressource identifiée par un URI. La classe `HttpResponseMessage` représente un message de réponse HTTP reçu d’une API REST après l’établissement d’une requête HTTP. Elle contient des informations sur la réponse, notamment le code d’État, les en-têtes et tout corps. La classe `HttpContent` représente le corps HTTP et les en-têtes de contenu, tels que `Content-Type` et `Content-Encoding`. Le contenu peut être lu à l’aide de l’une des méthodes de `ReadAs`, comme `ReadAsStringAsync` et `ReadAsByteArrayAsync`, selon le format des données.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Création d’une demande de récupération

La classe `CatalogService` est utilisée pour gérer le processus d’extraction de données à partir du microservice de catalogue. Dans la méthode `RegisterDependencies` de la classe `ViewModelLocator`, la classe `CatalogService` est inscrite en tant que mappage de type par rapport au type `ICatalogService` avec le conteneur d’injection de dépendance Autofac. Ensuite, lorsqu’une instance de la classe `CatalogViewModel` est créée, son constructeur accepte un type `ICatalogService`, que Autofac résout, en retournant une instance de la classe `CatalogService`. Pour plus d’informations sur l’injection de dépendances, consultez [Introduction à l’injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

La figure 10-1 montre l’interaction des classes qui lisent les données de catalogue à partir du microservice de catalogue pour les afficher par le `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Retrieving data from the catalog microservice")](accessing-remote-data-images/catalogdata-large.png#lightbox "Retrieving data from the catalog microservice")

**Figure 10-1**: récupération des données à partir du microservice de catalogue

Quand la `CatalogView` est parcourue, la méthode `OnInitialize` de la classe `CatalogViewModel` est appelée. Cette méthode récupère les données du catalogue à partir du microservice de catalogue, comme illustré dans l’exemple de code suivant :

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Cette méthode appelle la méthode `GetCatalogAsync` de l’instance `CatalogService` qui a été injectée dans le `CatalogViewModel` par Autofac. L’exemple de code suivant montre la méthode `GetCatalogAsync` :

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

Cette méthode génère l’URI qui identifie la ressource à laquelle la demande sera envoyée et utilise la classe `RequestProvider` pour appeler la méthode HTTP obtenir sur la ressource, avant de retourner les résultats à la `CatalogViewModel`. La classe `RequestProvider` contient des fonctionnalités qui envoient une demande sous la forme d’un URI qui identifie une ressource, une méthode HTTP qui indique l’opération à effectuer sur cette ressource et un corps qui contient toutes les données requises pour effectuer l’opération. Pour plus d’informations sur la façon dont la classe `RequestProvider` est injectée dans la `CatalogService class`, consultez [Introduction à l’injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

L’exemple de code suivant montre la méthode `GetAsync` dans la classe `RequestProvider` :

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

Cette méthode appelle la méthode `CreateHttpClient`, qui retourne une instance de la classe `HttpClient` avec les en-têtes appropriés définis. Il envoie ensuite une requête d’extraction asynchrone à la ressource identifiée par l’URI, avec la réponse stockée dans l’instance `HttpResponseMessage`. La méthode `HandleResponse` est ensuite appelée, ce qui lève une exception si la réponse n’inclut pas de code d’état HTTP de réussite. La réponse est ensuite lue sous forme de chaîne, convertie de JSON en objet `CatalogRoot`, puis retournée au `CatalogService`.

La méthode `CreateHttpClient` est présentée dans l’exemple de code suivant :

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

Cette méthode crée une nouvelle instance de la classe `HttpClient` et définit l’en-tête `Accept` de toutes les demandes effectuées par l’instance `HttpClient` à `application/json`, ce qui indique qu’elle s’attend à ce que le contenu de toute réponse soit formaté à l’aide de JSON. Ensuite, si un jeton d’accès a été passé comme argument à la méthode `CreateHttpClient`, il est ajouté à l’en-tête `Authorization` de toutes les requêtes effectuées par l’instance `HttpClient`, précédée de la chaîne `Bearer`. Pour plus d’informations sur l’autorisation, consultez [authorization](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Lorsque la méthode `GetAsync` de la classe `RequestProvider` appelle `HttpClient.GetAsync`, la méthode `Items` de la classe `CatalogController` dans le projet Catalog. API est appelée, ce qui est illustré dans l’exemple de code suivant :

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

Cette méthode récupère les données de catalogue de la base de données SQL à l’aide d’EntityFramework, puis les retourne sous la forme d’un message de réponse qui comprend un code d’état HTTP de réussite et une collection d’instances de `CatalogItem` au format JSON.

#### <a name="making-a-post-request"></a>Création d’une demande de publication

La classe `BasketService` est utilisée pour gérer la récupération des données et le processus de mise à jour avec le microservice de panier. Dans la méthode `RegisterDependencies` de la classe `ViewModelLocator`, la classe `BasketService` est inscrite en tant que mappage de type par rapport au type `IBasketService` avec le conteneur d’injection de dépendance Autofac. Ensuite, lorsqu’une instance de la classe `BasketViewModel` est créée, son constructeur accepte un type `IBasketService`, que Autofac résout, en retournant une instance de la classe `BasketService`. Pour plus d’informations sur l’injection de dépendances, consultez [Introduction à l’injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

La figure 10-2 montre l’interaction des classes qui envoient les données de panier affichées par le `BasketView`, au microservice de panier.

[![](accessing-remote-data-images/basketdata.png "Sending data to the basket microservice")](accessing-remote-data-images/basketdata-large.png#lightbox "Sending data to the basket microservice")

**Figure 10-2**: envoi de données au microservice de panier

Lorsqu’un élément est ajouté au panier d’achat, la méthode `ReCalculateTotalAsync` de la classe `BasketViewModel` est appelée. Cette méthode met à jour la valeur totale des éléments dans le panier et envoie les données du panier au microservice du panier, comme illustré dans l’exemple de code suivant :

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

Cette méthode appelle la méthode `UpdateBasketAsync` de l’instance `BasketService` qui a été injectée dans le `BasketViewModel` par Autofac. La méthode suivante affiche la méthode `UpdateBasketAsync` :

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Cette méthode génère l’URI qui identifie la ressource à laquelle la demande sera envoyée et utilise la classe `RequestProvider` pour appeler la méthode HTTP de publication sur la ressource, avant de retourner les résultats à la `BasketViewModel`. Notez qu’un jeton d’accès obtenu à partir de IdentityServer pendant le processus d’authentification est requis pour autoriser les demandes au microservice du panier. Pour plus d’informations sur l’autorisation, consultez [authorization](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

L’exemple de code suivant montre l’une des méthodes `PostAsync` dans la classe `RequestProvider` :

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

Cette méthode appelle la méthode `CreateHttpClient`, qui retourne une instance de la classe `HttpClient` avec les en-têtes appropriés définis. Il envoie ensuite une requête ultérieure asynchrone à la ressource identifiée par l’URI, avec les données de panier sérialisées envoyées au format JSON, et la réponse stockée dans l’instance `HttpResponseMessage`. La méthode `HandleResponse` est ensuite appelée, ce qui lève une exception si la réponse n’inclut pas de code d’état HTTP de réussite. Ensuite, la réponse est lue sous forme de chaîne, convertie de JSON en objet `CustomerBasket` et retournée à l' `BasketService`. Pour plus d’informations sur la méthode `CreateHttpClient`, consultez la page [création d’une demande d’extraction](#making_a_get_request).

Lorsque la méthode `PostAsync` de la classe `RequestProvider` appelle `HttpClient.PostAsync`, la méthode `Post` de la classe `BasketController` dans le projet basket. API est appelée, ce qui est illustré dans l’exemple de code suivant :

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Cette méthode utilise une instance de la classe `RedisBasketRepository` pour conserver les données de panier dans le cache Redims, et la retourne en tant que message de réponse qui comprend un code d’état HTTP de réussite et une instance de `CustomerBasket` mise en forme JSON.

#### <a name="making-a-delete-request"></a>Création d’une demande de suppression

La figure 10-3 montre les interactions des classes qui suppriment les données de panier du microservice du panier, pour le `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Deleteing data from the basket microservice")

**Figure 10-3**: suppression de données du microservice de panier

Lorsque le processus d’extraction est appelé, la méthode `CheckoutAsync` de la classe `CheckoutViewModel` est appelée. Cette méthode crée une nouvelle commande, avant d’effacer le panier d’achat, comme illustré dans l’exemple de code suivant :

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Cette méthode appelle la méthode `ClearBasketAsync` de l’instance `BasketService` qui a été injectée dans le `CheckoutViewModel` par Autofac. La méthode suivante affiche la méthode `ClearBasketAsync` :

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Cette méthode génère l’URI qui identifie la ressource à laquelle la demande sera envoyée et utilise la classe `RequestProvider` pour appeler la méthode HTTP DELETE sur la ressource. Notez qu’un jeton d’accès obtenu à partir de IdentityServer pendant le processus d’authentification est requis pour autoriser les demandes au microservice du panier. Pour plus d’informations sur l’autorisation, consultez [authorization](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

L’exemple de code suivant montre la méthode `DeleteAsync` dans la classe `RequestProvider` :

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Cette méthode appelle la méthode `CreateHttpClient`, qui retourne une instance de la classe `HttpClient` avec les en-têtes appropriés définis. Il envoie ensuite une requête DELETE asynchrone à la ressource identifiée par l’URI. Pour plus d’informations sur la méthode `CreateHttpClient`, consultez la page [création d’une demande d’extraction](#making_a_get_request).

Lorsque la méthode `DeleteAsync` de la classe `RequestProvider` appelle `HttpClient.DeleteAsync`, la méthode `Delete` de la classe `BasketController` dans le projet basket. API est appelée, ce qui est illustré dans l’exemple de code suivant :

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Cette méthode utilise une instance de la classe `RedisBasketRepository` pour supprimer les données de panier du cache Redims.

## <a name="caching-data"></a>Mise en cache des données

Les performances d’une application peuvent être améliorées en mettant en cache les données fréquemment sollicitées dans un stockage rapide situé près de l’application. Si le stockage rapide est situé plus près de l’application que la source d’origine, la mise en cache peut améliorer considérablement les temps de réponse lors de la récupération des données.

La forme la plus courante de mise en cache est la mise en cache en lecture seule, où une application récupère les données en référençant le cache. Si les données ne sont pas dans le cache, elles sont extraites du magasin de données et ajoutées au cache. Les applications peuvent implémenter la mise en cache en lecture seule avec le modèle cache de réserve. Ce modèle détermine si l’élément est actuellement dans le cache. Si l’élément n’est pas dans le cache, il est lu à partir du magasin de données et ajouté au cache. Pour plus d’informations, consultez le modèle [cache de réserve](/azure/architecture/patterns/cache-aside/) .

> [!TIP]
> Mettez en cache des données qui sont fréquemment lues et qui ne changent pas fréquemment. Ces données peuvent être ajoutées au cache à la demande la première fois qu’elles sont récupérées par une application. Cela signifie que l’application ne doit extraire les données qu’une seule fois du magasin de données, et que l’accès suivant peut être satisfait à l’aide du cache.

Les applications distribuées, telles que l’application de référence eShopOnContainers, doivent fournir l’un des caches suivants ou les deux :

- Un cache partagé, qui est accessible par plusieurs processus ou ordinateurs.
- Un cache privé, où les données sont conservées localement sur l’appareil qui exécute l’application.

L’application mobile eShopOnContainers utilise un cache privé, où les données sont stockées localement sur l’appareil qui exécute une instance de l’application. Pour plus d’informations sur le cache utilisé par l’application de référence eShopOnContainers, consultez [microservices .net : architecture pour les applications .net en conteneur](https://aka.ms/microservicesebook).

> [!TIP]
> Considérez le cache comme un magasin de données transitoire qui pourrait disparaître à tout moment. Assurez-vous que les données sont conservées dans le magasin de données d’origine, ainsi que dans le cache. Les risques de perte de données sont alors réduits si le cache n’est plus disponible.

### <a name="managing-data-expiration"></a>Gestion de l’expiration des données

Il est difficile de s’attendre à ce que les données mises en cache soient toujours cohérentes avec les données d’origine. Les données dans le magasin de données d’origine peuvent changer une fois qu’elles ont été mises en cache, ce qui a pour effet de rendre obsolètes les données mises en cache. Par conséquent, les applications doivent implémenter une stratégie qui permet de s’assurer que les données du cache sont aussi à jour que possible, mais elles peuvent également détecter et gérer les situations qui surviennent lorsque les données du cache sont devenues obsolètes. La plupart des mécanismes de mise en cache permettent de configurer le cache pour faire expirer les données et, par conséquent, de réduire la période pendant laquelle les données peuvent être obsolètes.

> [!TIP]
> Définissez un délai d’expiration par défaut lors de la configuration d’un cache. De nombreux caches implémentent l’expiration, ce qui invalide les données et les supprime du cache s’il n’est pas accessible pendant une période spécifiée. Toutefois, vous devez prendre soin de choisir la période d’expiration. S’il est rendu trop court, les données expirent trop rapidement et les avantages de la mise en cache seront réduits. Si elle est trop longue, les risques de données deviennent obsolètes. Par conséquent, le délai d’expiration doit correspondre au modèle d’accès pour les applications qui utilisent les données.

Lorsque les données mises en cache expirent, elles doivent être supprimées du cache, et l’application doit récupérer les données du magasin de données d’origine et les replacer dans le cache.

Il est également possible qu’un cache puisse remplir si les données sont autorisées à rester pendant trop longtemps. Par conséquent, les demandes d’ajout de nouveaux éléments au cache peuvent être nécessaires pour supprimer certains éléments d’un processus appelé *éviction*. Les services de mise en cache suppriment généralement les données sur une base moins récemment utilisée. Toutefois, il existe d’autres stratégies d’éviction, y compris le plus récemment utilisé et le premier à premier sorti. Pour plus d’informations, consultez [Guide de mise en cache](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Mise en cache d’images

L’application mobile eShopOnContainers utilise des images de produits distantes qui tirent parti de la mise en cache. Ces images sont affichées par le contrôle [`Image`](xref:Xamarin.Forms.Image) et le contrôle `CachedImage` fourni par la bibliothèque [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) .

Le contrôle Xamarin. Forms [`Image`](xref:Xamarin.Forms.Image) prend en charge la mise en cache des images téléchargées. La mise en cache est activée par défaut et stocke l’image localement pendant 24 heures. En outre, l’heure d’expiration peut être configurée avec la propriété [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) . Pour plus d’informations, consultez [mise en cache d’images téléchargées](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

Le contrôle de `CachedImage` de FFImageLoading est un remplacement du contrôle Xamarin. Forms [`Image`](xref:Xamarin.Forms.Image) , qui fournit des propriétés supplémentaires qui activent des fonctionnalités supplémentaires. Parmi ces fonctionnalités, le contrôle fournit la mise en cache configurable, tout en prenant en charge les espaces réservés d’image et de chargement. L’exemple de code suivant montre comment l’application mobile eShopOnContainers utilise le contrôle `CachedImage` dans le `ProductTemplate`, qui est le modèle de données utilisé par le contrôle [`ListView`](xref:Xamarin.Forms.ListView) dans le `CatalogView` :

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

Le contrôle `CachedImage` définit les propriétés `LoadingPlaceholder` et `ErrorPlaceholder` sur des images spécifiques à la plateforme. La propriété `LoadingPlaceholder` spécifie l’image à afficher lorsque l’image spécifiée par la propriété `Source` est récupérée, et la propriété `ErrorPlaceholder` spécifie l’image à afficher si une erreur se produit lors de la tentative de récupération de l’image spécifiée par l' `Source` propriété.

Comme son nom l’indique, le contrôle `CachedImage` met en cache les images distantes sur l’appareil pendant l’heure spécifiée par la valeur de la propriété `CacheDuration`. Lorsque cette valeur de propriété n’est pas définie explicitement, la valeur par défaut de 30 jours est appliquée.

## <a name="increasing-resilience"></a>Amélioration de la résilience

Toutes les applications qui communiquent avec les services et ressources distants doivent être sensibles aux erreurs temporaires. Les erreurs temporaires incluent la perte momentanée de la connectivité réseau aux services, l’indisponibilité temporaire d’un service ou les délais d’attente qui surviennent lorsqu’un service est occupé. Ces erreurs sont souvent à correction automatique et, si l’action est répétée après un délai approprié, il est probable qu’elle aboutisse.

Les erreurs temporaires peuvent avoir un impact énorme sur la qualité perçue d’une application, même si elle a été rigoureusement testée dans toutes les circonstances prévisibles. Pour vous assurer qu’une application qui communique avec les services distants fonctionne de façon fiable, elle doit être en mesure d’effectuer toutes les opérations suivantes :

- Détectez les erreurs lorsqu’elles se produisent et déterminez si les erreurs sont susceptibles d’être temporaires.
- Recommencez l’opération si elle détermine que l’erreur est susceptible d’être temporaire et assure le suivi du nombre de tentatives de l’opération.
- Utilisez une stratégie de nouvelle tentative appropriée, qui spécifie le nombre de nouvelles tentatives, le délai entre chaque tentative et les actions à entreprendre après l’échec d’une tentative.

Cette gestion des erreurs temporaires peut être obtenue en encapsulant toutes les tentatives d’accès à un service distant dans le code qui implémente le modèle de nouvelle tentative.

### <a name="retry-pattern"></a>Modèle de nouvelle tentative

Si une application détecte un échec lorsqu’elle tente d’envoyer une demande à un service distant, elle peut gérer l’échec de l’une des façons suivantes :

- Nouvelle tentative de l’opération. L’application peut réessayer immédiatement la demande qui a échoué.
- Nouvelle tentative de l’opération après un certain délai. L’application doit attendre un laps de temps approprié avant de retenter la demande.
- Annulation de l’opération. L’application doit annuler l’opération et signaler une exception.

La stratégie de nouvelle tentative doit être paramétrée pour répondre aux besoins de l’application. Par exemple, il est important d’optimiser le nombre de tentatives et l’intervalle avant nouvelle tentative d’exécution de l’opération. Si l’opération fait partie d’une interaction avec l’utilisateur, l’intervalle avant nouvelle tentative doit être abrégé et seules quelques tentatives sont effectuées pour éviter que les utilisateurs attendent une réponse. Si l’opération fait partie d’un flux de travail à long terme, où l’annulation ou le redémarrage du flux de travail est onéreux ou fastidieux, il est approprié d’attendre plus longtemps entre les tentatives et de réessayer plus de temps.

> [!NOTE]
> Une stratégie de nouvelle tentative agressive avec un délai minimal entre les tentatives et un grand nombre de tentatives peut dégrader un service distant qui s’exécute à proximité ou à la capacité. En outre, une stratégie de nouvelle tentative peut également affecter la réactivité de l’application si elle tente continuellement d’effectuer une opération qui échoue.

Si une demande continue d’échouer après un certain nombre de nouvelles tentatives, il est préférable pour l’application d’empêcher d’autres demandes d’accéder à la même ressource et de signaler un échec. Ensuite, après une période définie, l’application peut effectuer une ou plusieurs demandes à la ressource pour voir si elles sont réussies. Pour plus d’informations, consultez [modèle disjoncteur](#circuit_breaker_pattern).

> [!TIP]
> N’implémentez jamais un mécanisme de nouvelle tentative sans fin. Utilisez un nombre fini de nouvelles tentatives ou implémentez le modèle [disjoncteur](/azure/architecture/patterns/circuit-breaker/) pour permettre à un service de récupérer.

L’application mobile eShopOnContainers n’implémente pas actuellement le modèle de nouvelle tentative lors de la création de requêtes Web RESTful. Toutefois, le contrôle `CachedImage`, fourni par la bibliothèque [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) , prend en charge la gestion des erreurs temporaires en réessayant le chargement de l’image. Si le chargement de l’image échoue, d’autres tentatives sont effectuées. Le nombre de tentatives est spécifié par la propriété `RetryCount`, et les nouvelles tentatives ont lieu après un délai spécifié par la propriété `RetryDelay`. Si ces valeurs de propriété ne sont pas explicitement définies, leurs valeurs par défaut sont appliquées – 3 pour la propriété `RetryCount` et 250 ms pour la propriété `RetryDelay`. Pour plus d’informations sur le contrôle `CachedImage`, consultez [Caching images](#caching_images).

L’application de référence eShopOnContainers implémente le modèle de nouvelle tentative. Pour plus d’informations, y compris une discussion sur la façon de combiner le modèle de nouvelle tentative avec la classe `HttpClient`, consultez [microservices .net : architecture pour les applications .net en conteneur](https://aka.ms/microservicesebook).

Pour plus d’informations sur le modèle de nouvelle tentative, consultez le modèle de [nouvelle tentative](/azure/architecture/patterns/retry/) .

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Modèle disjoncteur

Dans certains cas, des erreurs peuvent se produire en raison d’événements anticipés qui prennent plus de temps à corriger. Ces erreurs peuvent aller d’une perte partielle de connectivité à la défaillance complète d’un service. Dans ces situations, il n’est pas acceptable pour une application de retenter une opération qui a peu de chances d’aboutir. elle doit plutôt accepter que l’opération a échoué et gérer cet échec en conséquence.

Le modèle disjoncteur peut empêcher une application de tenter d’exécuter à plusieurs reprises une opération qui risque d’échouer, tout en permettant à l’application de détecter si l’erreur a été résolue.

> [!NOTE]
> L’objectif du modèle disjoncteur est différent du modèle de nouvelle tentative. Le modèle de nouvelle tentative permet à une application de retenter une opération dans l’attente qu’elle aboutisse. Le modèle disjoncteur empêche une application d’effectuer une opération qui risque d’échouer.

Un disjoncteur agit comme un proxy pour les opérations qui peuvent échouer. Le proxy doit surveiller le nombre de défaillances récentes qui se sont produites, et utiliser ces informations pour décider s’il faut autoriser l’opération à continuer ou pour retourner immédiatement une exception.

L’application mobile eShopOnContainers n’implémente pas actuellement le modèle disjoncteur. Toutefois, le eShopOnContainers. Pour plus d’informations, consultez [microservices .net : architecture pour les applications .net en conteneur](https://aka.ms/microservicesebook).

> [!TIP]
> Combiner les modèles de nouvelle tentative et de disjoncteur. Une application peut combiner les modèles de nouvelle tentative et de disjoncteur en utilisant le modèle nouvelle tentative pour appeler une opération par le biais d’un disjoncteur. Toutefois, la logique de nouvelle tentative doit être sensible aux exceptions retournées par le disjoncteur et abandonner les nouvelles tentatives si le disjoncteur indique qu’une erreur n’est pas temporaire.

Pour plus d’informations sur le modèle disjoncteur, [consultez le modèle disjoncteur.](/azure/architecture/patterns/circuit-breaker/)

## <a name="summary"></a>Récapitulatif

De nombreuses solutions Web modernes utilisent des services Web hébergés par des serveurs Web pour fournir des fonctionnalités aux applications clientes distantes. Les opérations qu’un service Web expose constituent une API Web, et les applications clientes doivent être en mesure d’utiliser l’API Web sans savoir comment les données ou opérations exposées par l’API sont implémentées.

Les performances d’une application peuvent être améliorées en mettant en cache les données fréquemment sollicitées dans un stockage rapide situé près de l’application. Les applications peuvent implémenter la mise en cache en lecture seule avec le modèle cache de réserve. Ce modèle détermine si l’élément est actuellement dans le cache. Si l’élément n’est pas dans le cache, il est lu à partir du magasin de données et ajouté au cache.

Lors de la communication avec les API Web, les applications doivent être sensibles aux erreurs temporaires. Les erreurs temporaires incluent la perte momentanée de la connectivité réseau aux services, l’indisponibilité temporaire d’un service ou les délais d’attente qui surviennent lorsqu’un service est occupé. Ces erreurs sont souvent à correction automatique et, si l’action est répétée après un délai approprié, il est probable qu’elle aboutisse. Par conséquent, les applications doivent encapsuler toutes les tentatives d’accès à une API Web dans le code qui implémente un mécanisme de gestion des erreurs transitoires.

## <a name="related-links"></a>Liens associés

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
