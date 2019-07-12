---
title: Authentification et autorisation
description: Ce chapitre explique comment l’application mobile eShopOnContainers effectue l’authentification et l’autorisation sur les microservices en conteneur.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: efaea24e559aa2f3bdfd87c1c083ce1d777dbb3f
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832166"
---
# <a name="authentication-and-authorization"></a>Authentification et autorisation

L’authentification est le processus d’obtention des informations d’identification telles que nom et mot de passe d’un utilisateur, ces informations d’identification par rapport à une autorité de valider. Si les informations d’identification sont valides, l’entité qui a envoyé les informations d’identification est considéré comme une identité authentifiée. Une fois qu’une identité a été authentifiée, un processus d’autorisation détermine si cette identité a accès à une ressource donnée.

Il existe plusieurs approches pour l’intégration de l’authentification et autorisation dans une application Xamarin.Forms qui communique avec une application web ASP.NET MVC, y compris à l’aide d’ASP.NET Core Identity, fournisseurs d’authentification externes tels que Microsoft, Google, Intergiciel (middleware) Facebook ou Twitter et l’authentification. L’application mobile eShopOnContainers effectue l’authentification et autorisation avec un microservice d’identité en conteneur qui utilise 4 d’IdentityServer. L’application mobile demande des jetons de sécurité à partir d’IdentityServer, pour authentifier un utilisateur ou pour accéder à une ressource. Pour IdentityServer pour émettre des jetons pour le compte d’un utilisateur, l’utilisateur doit connectez-vous à IdentityServer. Toutefois, IdentityServer ne fournit pas une interface utilisateur ou la base de données pour l’authentification. Par conséquent, dans l’application de référence eShopOnContainers, ASP.NET Core Identity est utilisé à cet effet.

## <a name="authentication"></a>Authentication

L’authentification est requise lorsqu’une application doit connaître l’identité de l’utilisateur actuel. Mécanisme principal utilisé d’ASP.NET Core pour identifier les utilisateurs est le système d’appartenance ASP.NET Core Identity, qui stocke les informations utilisateur dans un magasin de données configuré par le développeur. En règle générale, le magasin de données sera un magasin Entity Framework, bien que les magasins personnalisés ou des packages tiers peuvent être utilisés pour stocker les informations d’identité dans le stockage Azure, Azure Cosmos DB ou d’autres emplacements.

Pour les scénarios d’authentification qui utilisent un utilisateur local de magasin de données et conserver les informations d’identité entre les demandes via les cookies (comme souvent dans les applications web ASP.NET MVC), ASP.NET Core Identity est une solution adaptée. Toutefois, les cookies ne sont pas toujours un moyen naturel de persistance et de transmission de données. Par exemple, une application web ASP.NET Core qui expose des points de terminaison RESTful qui sont accessibles à partir d’une application mobile devrez généralement utiliser l’authentification du jeton du porteur, étant donné que les cookies ne peuvent pas être utilisés dans ce scénario. Toutefois, les jetons du porteur peuvent facilement être récupérés et inclus dans l’en-tête d’autorisation de requêtes web à partir de l’application mobile.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Émission de jetons de porteur à l’aide d’IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) est une infrastructure open source de OAuth 2.0 et OpenID Connect pour ASP.NET Core, qui peut être utilisé pour de nombreux scénarios d’authentification et d’autorisation, y compris l’émission de jetons de sécurité pour les utilisateurs locaux de ASP.NET Core Identity.

> [!NOTE]
> OpenID Connect et OAuth 2.0 sont très similaires, tout en ayant des responsabilités différentes.

OpenID Connect est une couche d’authentification sur le protocole OAuth 2.0. OAuth 2 est un protocole qui permet aux applications de demander des jetons d’accès d’un service de jeton de sécurité et les utilisent pour communiquer avec les API. Cette délégation réduit la complexité dans les API et les applications clientes étant donné que l’authentification et l’autorisation peuvent être centralisées.

La combinaison de OpenID Connect et OAuth 2.0 combiner les deux problèmes fondamentaux de sécurité d’authentification et d’accès à l’API et IdentityServer 4 est une implémentation de ces protocoles.

Dans les applications qui utilisent la communication directe de client à microservice, comme l’application de référence eShopOnContainers, un microservice d’authentification dédié agissant comme un Service STS (Security Token) peut être utilisé pour authentifier les utilisateurs, comme indiqué dans la Figure 9-1. Pour plus d’informations sur la communication directe de client à microservice, consultez [Communication entre Client et Microservices](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Authentification par un microservice d’authentification dédié")

**Figure 9-1 :** Authentification par un microservice d’authentification dédié

L’application mobile eShopOnContainers communique avec le microservice d’identité, qui utilise des IdentityServer 4 pour effectuer l’authentification et contrôle d’accès pour les API. Par conséquent, l’application mobile demande des jetons à partir d’IdentityServer, pour authentifier un utilisateur ou pour accéder à une ressource :

-   Authentification des utilisateurs avec IdentityServer s’effectue par l’application mobile demandant un *identité* jeton qui représente le résultat d’un processus d’authentification. Au minimum, il contient un identificateur pour l’utilisateur et des informations sur comment et quand l’utilisateur authentifié. Il peut également contenir des données d’identité supplémentaires.
-   L’accès à une ressource avec IdentityServer s’effectue par l’application mobile demandant un *accès* jeton, ce qui permet d’accéder à une ressource d’API. Les clients demandent des jetons d’accès et les transfèrent vers l’API. Jetons d’accès contiennent des informations sur le client et l’utilisateur (le cas échéant). API a ensuite utilisent ces informations pour autoriser l’accès à leurs données.

> [!NOTE]
> Un client doit être inscrit avec IdentityServer avant qu’il peut demander des jetons.

### <a name="adding-identityserver-to-a-web-application"></a>IdentityServer Ajout d’une Application Web

Dans l’ordre pour une application web de ASP.NET Core IdentityServer 4 à utiliser, il doit être ajouté à la solution Visual Studio de l’application web. Pour plus d’informations, consultez [vue d’ensemble](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) dans la documentation IdentityServer.

Une fois que IdentityServer est inclus dans la solution Visual Studio de l’application web, il doit être ajouté à la requête HTTP de l’application web traitement pipeline, afin qu’il puisse fournir des requêtes aux points de terminaison OpenID Connect et OAuth 2.0. Cela s’effectue dans le `Configure` méthode dans l’application web `Startup` classe, comme illustré dans l’exemple de code suivant :

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordre est important dans la requête HTTP de l’application web pipeline de traitement. Par conséquent, IdentityServer doit être ajouté au pipeline avant de l’infrastructure d’interface utilisateur qui implémente l’écran de connexion.

### <a name="configuring-identityserver"></a>Configuration d’IdentityServer

IdentityServer doit être configuré dans le `ConfigureServices` méthode dans l’application web `Startup` classe en appelant le `services.AddIdentityServer` (méthode), comme illustré dans l’exemple de code suivant à partir de l’application de référence eShopOnContainers :

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Après avoir appelé la `services.AddIdentityServer` (méthode), l’API fluent supplémentaires sont appelées pour configurer les éléments suivants :

-   Informations d’identification utilisées pour la signature.
-   API et l’identité des ressources que les utilisateurs peuvent demander l’accès à.
-   Clients qui seront connectent à demander des jetons.
-   ASP.NET Core Identity.

> [!TIP]
> Charger dynamiquement la configuration IdentityServer 4. API d’IdentityServer 4 permettent la configuration IdentityServer à partir d’une liste en mémoire des objets de configuration. Dans l’application de référence eShopOnContainers, ces collections en mémoire sont codées en dur dans l’application. Toutefois, dans les scénarios de production qu’ils peuvent être chargés dynamiquement à partir d’un fichier de configuration ou d’une base de données.

Pour plus d’informations sur la configuration IdentityServer pour utiliser ASP.NET Core Identity, consultez [à l’aide de ASP.NET Core Identity](https://identityserver4.readthedocs.io/en/latest/quickstarts/8_aspnet_identity.html) dans la documentation IdentityServer.

#### <a name="configuring-api-resources"></a>Configuration des ressources de l’API

Lors de la configuration des ressources de l’API, le `AddInMemoryApiResources` méthode attend un `IEnumerable<ApiResource>` collection. Le code suivant montre l’exemple le `GetApis` application de référence de méthode qui fournit cette collection dans eShopOnContainers :

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Cette méthode spécifie que IdentityServer doit protéger les commandes et les API de panier. Par conséquent, les accès géré IdentityServer jetons sera nécessaire lors des appels à ces API. Pour plus d’informations sur la `ApiResource` de type, consultez [ressource de l’API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) dans la documentation IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configuration des ressources d’identité

Lors de la configuration des ressources d’identité, le `AddInMemoryIdentityResources` méthode attend un `IEnumerable<IdentityResource>` collection. Ressources d’identité sont des données telles que les ID d’utilisateur, nom ou adresse de messagerie. Chaque ressource de l’identité a un nom unique et arbitraire revendication types peuvent être affectés à ce dernier, qui sera ensuite être incluse dans le jeton d’identité pour l’utilisateur. Le code suivant montre l’exemple le `GetResources` application de référence de méthode qui fournit cette collection dans eShopOnContainers :

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

La spécification OpenID Connect spécifie certains [ressources d’identité standard](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). La configuration minimale requise est que le support est fourni pour l’émission d’un ID unique pour les utilisateurs. Pour ce faire, vous devez exposer le `IdentityResources.OpenId` ressource d’identité.

> [!NOTE]
> Le `IdentityResources` classe prend en charge toutes les étendues définies dans la spécification OpenID Connect (openid, e-mail, profil, téléphone et adresse).

IdentityServer prend également en charge la définition des ressources d’identité personnalisée. Pour plus d’informations, consultez [définition des ressources d’identité personnalisée](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) dans la documentation IdentityServer. Pour plus d’informations sur la `IdentityResource` de type, consultez [ressource identité](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) dans la documentation IdentityServer 4.

#### <a name="configuring-clients"></a>Configuration des Clients

Les clients sont des applications qui demandent des jetons à partir d’IdentityServer. En règle générale, les paramètres suivants doivent être définis pour chaque client au minimum :

-   Un ID client unique.
-   Les interactions autorisées avec le service de jeton (appelé le type d’autorisation).
-   L’emplacement où les jetons d’identité et accès sont envoyés (appelé un URI de redirection).
-   Une liste de ressources que le client est autorisé à accéder à (appelée étendues).

Lors de la configuration des clients, le `AddInMemoryClients` méthode attend un `IEnumerable<Client>` collection. L’exemple de code suivant montre la configuration de l’application mobile eShopOnContainers dans le `GetClients` application de référence de méthode qui fournit cette collection dans eShopOnContainers :

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Cette configuration spécifie les données pour les propriétés suivantes :

-   `ClientId`: Un ID unique pour le client.
-   `ClientName`: Le nom d’affichage de client, qui est utilisé pour la journalisation et l’écran de consentement.
-   `AllowedGrantTypes`: Spécifie comment un client souhaite interagir avec IdentityServer. Pour plus d’informations, consultez [configuration le flux d’authentification](#configuring_the_authentication_flow).
-   `ClientSecrets`: Spécifie les informations d’identification secrètes de client qui sont utilisées lors de la demande de jetons du point de terminaison de jeton.
-   `RedirectUris`: Spécifie l’URI pour lequel renvoyer des jetons ou des codes d’autorisation autorisés.
-   `RequireConsent`: Spécifie si un écran de consentement est requis.
-   `RequirePkce`: Spécifie si les clients à l’aide d’un code d’autorisation doivent envoyer une clé de vérification.
-   `PostLogoutRedirectUris`: Spécifie l’URI autorisés pour rediriger vers après la déconnexion.
-   `AllowedCorsOrigins`: Spécifie l’origine du client afin qu’IdentityServer autorise les appels cross-origine à partir de l’origine.
-   `AllowedScopes`: Spécifie les ressources que le client a accès à. Par défaut, un client a pas accès à toutes les ressources.
-   `AllowOfflineAccess`: Indique si le client peut demander des jetons d’actualisation.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configuration du flux d’authentification

Le flux d’authentification entre un client et un IdentityServer peut être configuré en spécifiant les types d’octroi dans le `Client.AllowedGrantTypes` propriété. Les spécifications OpenID Connect et OAuth 2.0 définissent un nombre de flux d’authentification, y compris :

-   Implicite. Ce flux est optimisé pour les applications basées sur un navigateur et doit être utilisé pour l’utilisateur d’authentification uniquement, ou les demandes de jeton d’authentification et accès. Tous les jetons sont transmises via le navigateur et avancés par conséquent des fonctionnalités telles que les jetons d’actualisation ne sont pas autorisées.
-   Code d’autorisation. Ce flux offre la possibilité de récupérer des jetons sur un canal arrière, plutôt que le canal avant de navigateur, tout en prenant également en charge l’authentification du client.
-   Hybride. Ce flux est une combinaison d’implicite et types d’octroi de code d’autorisation. Le jeton d’identité est transmis via le canal de navigateur et contient la réponse de protocole signé, ainsi que d’autres artefacts tels que le code d’autorisation. Après une validation réussie de la réponse, le canal arrière doit être utilisé pour récupérer l’accès et le jeton d’actualisation.

> [!TIP]
> Utilisez le flux d’authentification hybride. Le flux d’authentification hybride permet d’atténuer un nombre d’attaques qui s’appliquent au canal de navigateur et le flux, recommandée pour les applications natives que vous souhaitez récupérer des jetons d’accès (et éventuellement des jetons d’actualisation).

Pour plus d’informations sur les flux d’authentification, consultez [Types d’octroi](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) dans la documentation IdentityServer 4.

### <a name="performing-authentication"></a>Exécution de l’authentification

Pour IdentityServer pour émettre des jetons pour le compte d’un utilisateur, l’utilisateur doit connectez-vous à IdentityServer. Toutefois, IdentityServer ne fournit pas une interface utilisateur ou la base de données pour l’authentification. Par conséquent, dans l’application de référence eShopOnContainers, ASP.NET Core Identity est utilisé à cet effet.

L’application mobile eShopOnContainers s’authentifie avec IdentityServer avec le flux d’authentification hybride, ce qui est illustré dans la Figure 9-2.

![](authentication-and-authorization-images/sign-in.png "Vue d’ensemble du processus de connexion")

**Figure 9-2 :** Vue d’ensemble du processus de connexion

Une demande de connexion est effectuée à `<base endpoint>:5105/connect/authorize`. Après l’authentification réussie, IdentityServer renvoie une réponse d’authentification contenant un code d’autorisation et un jeton d’identité. Le code d’autorisation est ensuite envoyé au `<base endpoint>:5105/connect/token`, qui répond avec l’accès, des identités et des jetons d’actualisation.

L’eShopOnContainers application mobile signes-hors IdentityServer en envoyant une demande à `<base endpoint>:5105/connect/endsession`, avec des paramètres supplémentaires. Après la déconnexion, IdentityServer répond en envoyant un URI de redirection POST-déconnexion vers l’application mobile. Figure 9-3 illustre ce processus.

![](authentication-and-authorization-images/sign-out.png "Vue d’ensemble du processus de déconnexion")

**Figure 9-3 :** Vue d’ensemble du processus de déconnexion

Dans l’application mobile eShopOnContainers, les communications avec IdentityServer sont effectuée par le `IdentityService` classe qui implémente le `IIdentityService` interface. Cette interface spécifie que la classe d’implémentation doit fournir `CreateAuthorizationRequest`, `CreateLogoutRequest`, et `GetTokenAsync` méthodes.

#### <a name="signing-in"></a>Ouverture de session

Lorsque l’utilisateur appuie sur le **connexion** bouton sur le `LoginView`, le `SignInCommand` dans le `LoginViewModel` classe est exécutée, qui à son tour s’exécute à la `SignInAsync` (méthode). L’exemple de code suivant illustre cette méthode :

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Cette méthode appelle la `CreateAuthorizationRequest` méthode dans le `IdentityService` (classe), ce qui est illustré dans l’exemple de code suivant :

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Cette méthode crée l’URI pour d’IdentityServer [point de terminaison d’autorisation](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html), avec les paramètres requis. Le point de terminaison d’autorisation est à `/connect/authorize` sur le port 5105 du point de terminaison base exposée comme un paramètre utilisateur. Pour plus d’informations sur les paramètres utilisateur, consultez [gestion de la Configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> La surface d’attaque de l’application mobile eShopOnContainers est réduite en implémentant la clé de preuve d’extension de Code Exchange (PKCE) pour OAuth. PKCE protège le code d’autorisation d’être utilisés si elle est interceptée. Pour cela, le client de génération d’un vérificateur de secret, un hachage qui est passé dans la demande d’autorisation, et qui est présentée non hachée lors de l’échange le code d’autorisation. Pour plus d’informations sur PKCE, consultez [clé de preuve pour l’échange de Code par les Clients publics OAuth](https://tools.ietf.org/html/rfc7636) sur le site web Internet Engineering Task Force.

L’URI retourné est stocké dans le `LoginUrl` propriété de la `LoginViewModel` classe. Lorsque le `IsLogin` propriété devient `true`, le [ `WebView` ](xref:Xamarin.Forms.WebView) dans le `LoginView` devient visible. Le `WebView` lie son [ `Source` ](xref:Xamarin.Forms.WebView.Source) propriété le `LoginUrl` propriété de la `LoginViewModel` classe et par conséquent, est une demande de connexion à IdentityServer lorsque le `LoginUrl` propriété est définie sur Point de terminaison d’autorisation d’IdentityServer. Lorsque IdentityServer reçoit cette demande et l’utilisateur n’est pas authentifié, le `WebView` sera redirigé vers la page de connexion configurée, ce qui est illustrée dans la Figure 9-4.

![](authentication-and-authorization-images/login.png "Page de connexion affichée par la WebView")

**Figure 9-4 :** Page de connexion affichée par la WebView

Une fois que la connexion est terminée, le [ `WebView` ](xref:Xamarin.Forms.WebView) sera redirigé vers un URI de retour. Cela `WebView` navigation entraîne la `NavigateAsync` méthode dans la `LoginViewModel` classe doit être exécuté, ce qui est illustré dans l’exemple de code suivant :

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Cette méthode analyse la réponse d’authentification qui est contenue dans l’URI de retour, et si un code d’autorisation valide est présent, il effectue une demande auprès du IdentityServer [point de terminaison de jeton](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html), en passant le code d’autorisation, le Vérificateur de secret PKCE et d’autres paramètres sont requis. Le point de terminaison de jeton est à `/connect/token` sur le port 5105 du point de terminaison base exposée comme un paramètre utilisateur. Pour plus d’informations sur les paramètres utilisateur, consultez [gestion de la Configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!TIP]
> Valider le retour d’URI. Bien que l’application mobile eShopOnContainers ne valide pas l’URI de retour, la meilleure pratique consiste à valider que l’URI de retour fait référence à un emplacement connu, pour empêcher les attaques par redirection d’open.

Si le point de terminaison de jeton reçoit un code d’autorisation valide et le vérificateur de secret de PKCE, il répond avec un jeton d’accès, le jeton d’identité et le jeton d’actualisation. Le jeton d’accès (qui permet d’accéder aux ressources d’API) et un jeton d’identité sont ensuite stockées en tant que paramètres d’application, et la navigation entre les pages est effectuée. Par conséquent, l’effet global dans l’application mobile eShopOnContainers est-ce : condition que les utilisateurs sont en mesure de s’authentifier correctement avec IdentityServer, navigation vers le `MainView` page, qui est un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) qui affiche le `CatalogView` en tant que son onglet sélectionné.

Pour plus d’informations sur la navigation entre les pages, consultez [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md). Pour plus d’informations sur la façon [ `WebView` ](xref:Xamarin.Forms.WebView) navigation provoque une méthode de modèle de vue être exécutée, consultez [à l’aide de comportements de Navigation appel](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Pour plus d’informations sur les paramètres d’application, consultez [gestion de la Configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers également autorise un simulacre se connecter quand l’application est configurée pour utiliser des services fictifs dans le `SettingsView`. Dans ce mode, l’application ne communique pas avec IdentityServer, permettant à la place à l’utilisateur pour se connecter à l’aide des informations d’identification.

#### <a name="signing-out"></a>Hors connexion

Lorsque l’utilisateur appuie sur le **se déconnecter** situé dans le `ProfileView`, le `LogoutCommand` dans le `ProfileViewModel` classe est exécutée, qui à son tour s’exécute à la `LogoutAsync` (méthode). Cette méthode effectue la navigation entre les pages à la `LoginView` page, en passant un `LogoutParameter` instance définie sur `true` en tant que paramètre. Pour plus d’informations sur le passage de paramètres lors de la navigation de page, consultez [en passant les paramètres au cours de Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Lorsqu’une vue est créée et cible, le `InitializeAsync` méthode du modèle de vue associée de la vue est exécutée, qui exécute ensuite le `Logout` méthode de la `LoginViewModel` (classe), ce qui est illustré dans l’exemple de code suivant :

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Cette méthode appelle la `CreateLogoutRequest` méthode dans la `IdentityService` classe, en passant le jeton d’identité récupérées à partir des paramètres d’application en tant que paramètre. Pour plus d’informations sur les paramètres de l’application, consultez [gestion de la Configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). L’exemple de code suivant montre la méthode `CreateLogoutRequest` :

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Cette méthode crée l’URI à IdentityServer [fin du point de terminaison de session](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession), avec les paramètres requis. Le point de terminaison de session de fin est au `/connect/endsession` sur le port 5105 du point de terminaison base exposée comme un paramètre utilisateur. Pour plus d’informations sur les paramètres utilisateur, consultez [gestion de la Configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

L’URI retourné est stocké dans le `LoginUrl` propriété de la `LoginViewModel` classe. Alors que le `IsLogin` propriété est `true`, le [ `WebView` ](xref:Xamarin.Forms.WebView) dans le `LoginView` est visible. Le `WebView` lie son [ `Source` ](xref:Xamarin.Forms.WebView.Source) propriété le `LoginUrl` propriété de la `LoginViewModel` classe et par conséquent, est une demande de déconnexion à IdentityServer lorsque le `LoginUrl` propriété est définie sur Point de terminaison session de fin de IdentityServer. Lorsque IdentityServer reçoit cette demande, sous réserve que l’utilisateur est connecté, déconnexion se produit. L’authentification est suivie avec un cookie géré par le middleware cookie d’authentification d’ASP.NET Core. Par conséquent, la déconnexion de IdentityServer supprime le cookie d’authentification et envoie une URI au client de redirection après déconnexion.

Dans l’application mobile, le [ `WebView` ](xref:Xamarin.Forms.WebView) sera redirigé vers l’URI de redirection POST-déconnexion. Cela `WebView` navigation entraîne la `NavigateAsync` méthode dans la `LoginViewModel` classe doit être exécuté, ce qui est illustré dans l’exemple de code suivant :

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Cette méthode efface le jeton d’identité et le jeton d’accès à partir des paramètres d’application et définit les `IsLogin` propriété `false`, ce qui conduit le [ `WebView` ](xref:Xamarin.Forms.WebView) sur le `LoginView` page devienne invisible . Enfin, le `LoginUrl` propriété est définie sur l’URI de IdentityServer [point de terminaison d’autorisation](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html), avec les paramètres requis, en préparation de la prochaine fois que l’utilisateur initie une connexion.

Pour plus d’informations sur la navigation entre les pages, consultez [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md). Pour plus d’informations sur la façon [ `WebView` ](xref:Xamarin.Forms.WebView) navigation provoque une méthode de modèle de vue être exécutée, consultez [à l’aide de comportements de Navigation appel](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Pour plus d’informations sur les paramètres d’application, consultez [gestion de la Configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers autorise également un simulacre déconnexion lorsque l’application est configurée pour utiliser des services fictifs dans la SettingsView. Dans ce mode, l’application ne communique pas avec IdentityServer et au lieu de cela efface tous les jetons stockées à partir des paramètres d’application.

<a name="authorization" />

## <a name="authorization"></a>Authorization

Après l’authentification, web ASP.NET Core API doivent souvent pour autoriser l’accès, qui permet à un service rendre les API disponibles pour les utilisateurs authentifiés, mais pas à toutes.

Restreindre l’accès à un itinéraire ASP.NET Core MVC peut être obtenue en appliquant un attribut Authorize à un contrôleur ou d’action, ce qui limite l’accès au contrôleur ou une action pour les utilisateurs authentifiés, comme indiqué dans l’exemple de code suivant :

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Si un utilisateur non autorisé tente d’accéder à un contrôleur ou une action qui est marquée avec le `Authorize` attribut, l’infrastructure MVC retourne un code d’état HTTP 401 (non autorisé).

> [!NOTE]
> Paramètres peuvent être spécifiés sur la `Authorize` attribut pour restreindre une API à des utilisateurs spécifiques. Pour plus d’informations, consultez [autorisation](/aspnet/core/security/authorization/introduction/).

IdentityServer peut être intégré dans le flux de travail d’autorisation afin que les jetons d’accès, il fournit l’autorisation control. Cette approche est illustrée dans la Figure 9-5.

![](authentication-and-authorization-images/authorization.png "Autorisation par jeton d’accès")

**Figure 9-5 :** Autorisation par jeton d’accès

L’application mobile eShopOnContainers communique avec le microservice d’identité et demande un jeton d’accès dans le cadre du processus d’authentification. Le jeton d’accès est ensuite transmis à l’API exposées par les microservices du panier d’achat et de classement dans le cadre des demandes d’accès. Jetons d’accès contiennent des informations sur le client et l’utilisateur. API a ensuite utilisent ces informations pour autoriser l’accès à leurs données. Pour plus d’informations sur la configuration IdentityServer pour protéger des API, consultez [configuration des ressources de l’API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configuration IdentityServer pour exécuter une autorisation

Pour effectuer d’autorisation avec IdentityServer, son intergiciel (middleware) d’autorisation doit être ajouté au pipeline de demande HTTP de l’application web. L’intergiciel (middleware) est ajouté dans le `ConfigureAuth` méthode dans l’application web `Startup` (classe), qui est appelé à partir de la `Configure` (méthode) et est illustrée dans l’exemple de code suivant à partir de l’application de référence eShopOnContainers :

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Cette méthode garantit que l’API est uniquement accessible avec un jeton d’accès valide. Le middleware valide le jeton entrant pour vous assurer qu’il est envoyé à partir d’un émetteur approuvé et vérifie que le jeton est valide pour être utilisé avec l’API qui le reçoit. Par conséquent, l’exploration vers le contrôleur de classement ou du panier d’achat retournera 401 (non autorisé) code d’état HTTP, indiquant qu’un jeton d’accès est requis.

> [!NOTE]
> Intergiciel (middleware) de d’IdentityServer d’autorisation doit être ajouté au pipeline de demande HTTP de l’application web avant d’ajouter MVC avec `app.UseMvc()` ou `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Rendre les demandes d’accès aux API

Pour effectuer des demandes pour les microservices du panier d’achat et de classement, l’accès au jeton, obtenu à partir de IdentityServer pendant le processus d’authentification doit être inclus dans la demande, comme indiqué dans l’exemple de code suivant :

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

Le jeton d’accès sont stocké sous la forme d’un paramètre d’application et est récupéré à partir du stockage de spécifique à la plateforme et inclus dans l’appel à la `GetOrderAsync` méthode dans la `OrderService` classe.

De même, le jeton d’accès doit être inclus lors de l’envoi de données à un IdentityServer protégé API, comme indiqué dans l’exemple de code suivant :

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

Le jeton d’accès est récupéré à partir du stockage de spécifique à la plateforme et inclus dans l’appel à la `UpdateBasketAsync` méthode dans la `BasketService` classe.

Le `RequestProvider` (classe), dans l’application mobile eShopOnContainers, utilise le `HttpClient` classe pour effectuer des demandes pour les API RESTful exposées par l’application de référence eShopOnContainers. Lors de la fabrication des demandes à la commande et leur panier d’achat des API, qui requièrent une autorisation, un jeton d’accès valide doit être inclus dans la demande. Cela est obtenue en ajoutant le jeton d’accès aux en-têtes de la `HttpClient` de l’instance, comme illustré dans l’exemple de code suivant :

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

Le `DefaultRequestHeaders` propriété de la `HttpClient` classe expose les en-têtes qui sont envoyées avec chaque demande, et le jeton d’accès est ajouté à la `Authorization` en-tête préfixé avec la chaîne `Bearer`. Lorsque la demande est envoyée à une API RESTful, la valeur de la `Authorization` en-tête est extraites et validé pour garantir qu’il a envoyé à partir d’un émetteur approuvé et permet de déterminer si l’utilisateur est autorisé à appeler l’API qui le reçoit.

Pour plus d’informations sur la façon dont l’application mobile eShopOnContainers effectue des requêtes web, consultez [l’accès à des données distantes](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Récapitulatif

Il existe plusieurs approches pour l’intégration de l’authentification et autorisation dans une application Xamarin.Forms qui communique avec une application web ASP.NET MVC. L’application mobile eShopOnContainers effectue l’authentification et autorisation avec un microservice d’identité en conteneur qui utilise 4 d’IdentityServer. IdentityServer est une infrastructure open source de OAuth 2.0 et OpenID Connect pour ASP.NET Core qui s’intègre avec ASP.NET Core Identity pour effectuer l’authentification du jeton du porteur.

L’application mobile demande des jetons de sécurité à partir d’IdentityServer, pour authentifier un utilisateur ou pour accéder à une ressource. Lorsque vous accédez à une ressource, un jeton d’accès doit être inclus dans la demande d’API qui requièrent une autorisation. Intergiciel (middleware) d’IdentityServer valide les jetons d’accès entrant pour vous assurer qu’ils sont envoyés à partir d’un émetteur approuvé, et qu’ils sont valides pour être utilisé avec l’API qui les reçoit.


## <a name="related-links"></a>Liens associés

- [Téléchargez le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
