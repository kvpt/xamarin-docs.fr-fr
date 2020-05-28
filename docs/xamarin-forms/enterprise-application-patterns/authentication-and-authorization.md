---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d5bf1d7821187924adc58582a5139f81235e6a0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139098"
---
# <a name="authentication-and-authorization"></a>Authentification et autorisation

L’authentification est le processus qui consiste à obtenir des informations d’identification, telles que le nom et le mot de passe d’un utilisateur, et à valider ces informations d’identification par rapport à une autorité. Si les informations d’identification sont valides, l’entité qui a envoyé les informations d’identification est considérée comme une identité authentifiée. Une fois qu’une identité a été authentifiée, un processus d’autorisation détermine si cette identité a accès à une ressource donnée.

Il existe de nombreuses approches pour intégrer l’authentification et l’autorisation dans une Xamarin.Forms application qui communique avec une application web ASP.NET MVC, y compris l’utilisation de ASP.net Core identité, des fournisseurs d’authentification externes tels que Microsoft, Google, Facebook ou Twitter, ainsi que l’intergiciel (middleware) d’authentification. L’application mobile eShopOnContainers effectue l’authentification et l’autorisation avec un microservice d’identité en conteneur qui utilise IdentityServer 4. L’application mobile demande des jetons de sécurité à partir de IdentityServer, soit pour authentifier un utilisateur, soit pour accéder à une ressource. Pour que IdentityServer émette des jetons pour le compte d’un utilisateur, l’utilisateur doit se connecter à IdentityServer. Toutefois, IdentityServer ne fournit pas d’interface utilisateur ou de base de données pour l’authentification. Par conséquent, dans l’application de référence eShopOnContainers, ASP.NET Core identité est utilisée à cette fin.

## <a name="authentication"></a>Authentification

L’authentification est requise lorsqu’une application doit connaître l’identité de l’utilisateur actuel. Le mécanisme principal de ASP.NET Core pour identifier les utilisateurs est le système d’appartenance ASP.NET Core identité, qui stocke les informations utilisateur dans un magasin de données configuré par le développeur. En règle générale, ce magasin de données est un magasin EntityFramework, bien que les magasins personnalisés ou les packages tiers puissent être utilisés pour stocker les informations d’identité dans le stockage Azure, Azure Cosmos DB ou d’autres emplacements.

Pour les scénarios d’authentification qui utilisent un magasin de données utilisateur local et qui rendent persistantes les informations d’identité entre les demandes via des cookies (comme c’est généralement le cas dans les applications Web ASP.NET MVC), ASP.NET Core identité est une solution appropriée. Toutefois, les cookies ne sont pas toujours un moyen naturel de conserver et de transmettre des données. Par exemple, une application Web ASP.NET Core qui expose des points de terminaison RESTful accessibles à partir d’une application mobile doit généralement utiliser l’authentification du jeton du porteur, puisque les cookies ne peuvent pas être utilisés dans ce scénario. Toutefois, les jetons du porteur peuvent facilement être récupérés et inclus dans l’en-tête d’autorisation des requêtes Web effectuées à partir de l’application mobile.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Émission de jetons de porteur à l’aide de IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) est un Framework Open source OpenID Connect et OAuth 2,0 pour ASP.net Core, qui peut être utilisé pour de nombreux scénarios d’authentification et d’autorisation, notamment l’émission de jetons de sécurité pour les utilisateurs d’identité ASP.net Core locale.

> [!NOTE]
> OpenID Connect et OAuth 2,0 sont très similaires, tout en ayant des responsabilités différentes.

OpenID Connect est une couche d’authentification par-dessus le protocole OAuth 2,0. OAuth 2 est un protocole qui permet aux applications de demander des jetons d’accès à un service d’un jeton de sécurité et de les utiliser pour communiquer avec les API. Cette délégation réduit la complexité des applications clientes et des API, car l’authentification et l’autorisation peuvent être centralisées.

La combinaison de OpenID Connect et OAuth 2,0 combine les deux problèmes de sécurité fondamentaux liés à l’authentification et à l’accès à l’API, et IdentityServer 4 est une implémentation de ces protocoles.

Dans les applications qui utilisent la communication directe entre les clients et les microservices, telle que l’application de référence eShopOnContainers, un microservice d’authentification dédié agissant comme un service d’émission de jeton de sécurité (STS) peut être utilisé pour authentifier les utilisateurs, comme illustré dans la figure 9-1. Pour plus d’informations sur la communication directe entre les clients et les microservices, consultez [communication entre le client et les microservices](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Authentication by a dedicated authentication microservice")

**Figure 9-1 :** Authentification par un microservice d’authentification dédié

L’application mobile eShopOnContainers communique avec le microservice d’identité, qui utilise IdentityServer 4 pour effectuer l’authentification et le contrôle d’accès pour les API. Par conséquent, l’application mobile demande des jetons à partir de IdentityServer, soit pour authentifier un utilisateur, soit pour accéder à une ressource :

- L’authentification des utilisateurs avec IdentityServer est assurée par l’application mobile qui demande un jeton d' *identité* , qui représente le résultat d’un processus d’authentification. Au minimum, il contient un identificateur pour l’utilisateur, ainsi que des informations sur le mode et le moment de l’authentification de l’utilisateur. Il peut également contenir des données d’identité supplémentaires.
- L’accès à une ressource avec IdentityServer est effectué par l’application mobile qui demande un jeton d' *accès* , ce qui permet d’accéder à une ressource API. Les clients demandent des jetons d’accès et les transfèrent à l’API. Les jetons d’accès contiennent des informations sur le client et l’utilisateur (le cas échéant). Les API utilisent ensuite ces informations pour autoriser l’accès à leurs données.

> [!NOTE]
> Un client doit être inscrit auprès de IdentityServer avant de pouvoir demander des jetons.

### <a name="adding-identityserver-to-a-web-application"></a>Ajout de IdentityServer à une application Web

Pour qu’une application Web ASP.NET Core utilise IdentityServer 4, elle doit être ajoutée à la solution Visual Studio de l’application Web. Pour plus d’informations, consultez [vue d’ensemble](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) dans la documentation IdentityServer.

Une fois que IdentityServer est inclus dans la solution Visual Studio de l’application Web, il doit être ajouté au pipeline de traitement de requête HTTP de l’application Web, afin qu’il puisse traiter les demandes aux points de terminaison OpenID Connect et OAuth 2,0. Cela est possible dans la `Configure` méthode de la classe de l’application Web `Startup` , comme illustré dans l’exemple de code suivant :

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordonnez les questions dans le pipeline de traitement de requête HTTP de l’application Web. Par conséquent, IdentityServer doit être ajouté au pipeline avant l’infrastructure d’interface utilisateur qui implémente l’écran de connexion.

### <a name="configuring-identityserver"></a>Configuration de IdentityServer

IdentityServer doit être configuré dans la `ConfigureServices` méthode de la classe de l’application Web `Startup` en appelant la `services.AddIdentityServer` méthode, comme illustré dans l’exemple de code suivant à partir de l’application de référence eShopOnContainers :

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

Après l’appel de la `services.AddIdentityServer` méthode, des API Fluent supplémentaires sont appelées pour configurer les éléments suivants :

- Informations d’identification utilisées pour la signature.
- Ressources d’identité et d’API auxquelles les utilisateurs peuvent demander l’accès.
- Clients qui se connecteront aux jetons de demande.
- Identité ASP.NET Core.

> [!TIP]
> Chargez dynamiquement la configuration IdentityServer 4. Les API de IdentityServer 4 permettent de configurer les IdentityServer à partir d’une liste en mémoire d’objets de configuration. Dans l’application de référence eShopOnContainers, ces collections en mémoire sont codées en dur dans l’application. Toutefois, dans les scénarios de production, ils peuvent être chargés dynamiquement à partir d’un fichier de configuration ou d’une base de données.

Pour plus d’informations sur la configuration de IdentityServer pour utiliser ASP.NET Core identité, consultez [utilisation de ASP.net Core identité](https://identityserver4.readthedocs.io/en/latest/quickstarts/6_aspnet_identity.html) dans la documentation IdentityServer.

#### <a name="configuring-api-resources"></a>Configuration des ressources API

Lors de la configuration des ressources de l’API, la `AddInMemoryApiResources` méthode attend une `IEnumerable<ApiResource>` collection. L’exemple de code suivant montre la `GetApis` méthode qui fournit cette collection dans l’application de référence eShopOnContainers :

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

Cette méthode spécifie que IdentityServer doit protéger les API Orders et basket. Par conséquent, les jetons d’accès managé IdentityServer sont requis lors de l’appel à ces API. Pour plus d’informations sur le `ApiResource` type, consultez [ressource API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) dans la documentation IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configuration des ressources d’identité

Lors de la configuration des ressources d’identité, la `AddInMemoryIdentityResources` méthode attend une `IEnumerable<IdentityResource>` collection. Les ressources d’identité sont des données telles que l’ID utilisateur, le nom ou l’adresse de messagerie. Chaque ressource d’identité a un nom unique et des types de revendications arbitraires peuvent lui être affectés, qui seront ensuite inclus dans le jeton d’identité pour l’utilisateur. L’exemple de code suivant montre la `GetResources` méthode qui fournit cette collection dans l’application de référence eShopOnContainers :

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

La spécification OpenID Connect spécifie des [ressources d’identité standard](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). La configuration minimale requise est que la prise en charge est fournie pour l’émission d’un ID unique pour les utilisateurs. Cela est possible en exposant la `IdentityResources.OpenId` ressource d’identité.

> [!NOTE]
> La `IdentityResources` classe prend en charge toutes les étendues définies dans la spécification OpenID Connect (OpenID, email, Profile, Telephone et Address).

IdentityServer prend également en charge la définition de ressources d’identité personnalisées. Pour plus d’informations, consultez [définition des ressources d’identité personnalisées](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) dans la documentation IdentityServer. Pour plus d’informations sur le `IdentityResource` type, consultez [ressource d’identité](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) dans la documentation IdentityServer 4.

#### <a name="configuring-clients"></a>Configuration des clients

Les clients sont des applications qui peuvent demander des jetons à partir de IdentityServer. En règle générale, les paramètres suivants doivent être définis pour chaque client au minimum :

- ID client unique.
- Interactions autorisées avec le service de jeton (connu sous le nom de type d’octroi).
- Emplacement d’envoi des jetons d’identité et d’accès (appelé URI de redirection).
- Liste des ressources auxquelles le client est autorisé à accéder (également appelées étendues).

Lors de la configuration des clients, la `AddInMemoryClients` méthode attend une `IEnumerable<Client>` collection. L’exemple de code suivant illustre la configuration de l’application mobile eShopOnContainers dans la `GetClients` méthode qui fournit cette collection dans l’application de référence eShopOnContainers :

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

- `ClientId`: Un ID unique pour le client.
- `ClientName`: Le nom complet du client, qui est utilisé pour la journalisation et l’écran de consentement.
- `AllowedGrantTypes`: Spécifie comment un client souhaite interagir avec IdentityServer. Pour plus d’informations, consultez [configuration du workflow d’authentification](#configuring_the_authentication_flow).
- `ClientSecrets`: Spécifie les informations d’identification de la clé secrète client qui sont utilisées lors de la demande de jetons à partir du point de terminaison de jeton.
- `RedirectUris`: Spécifie les URI autorisés auxquels retourner des jetons ou des codes d’autorisation.
- `RequireConsent`: Spécifie si un écran de consentement est requis.
- `RequirePkce`: Spécifie si les clients utilisant un code d’autorisation doivent envoyer une clé de vérification.
- `PostLogoutRedirectUris`: Spécifie les URI autorisés à rediriger vers après la déconnexion.
- `AllowedCorsOrigins`: Spécifie l’origine du client afin que IdentityServer puisse autoriser les appels Cross-Origin à partir de l’origine.
- `AllowedScopes`: Spécifie les ressources auxquelles le client a accès. Par défaut, un client n’a accès à aucune ressource.
- `AllowOfflineAccess`: Spécifie si le client peut demander des jetons d’actualisation.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configuration du workflow d’authentification

Le workflow d’authentification entre un client et IdentityServer peut être configuré en spécifiant les types d’octroi dans la `Client.AllowedGrantTypes` propriété. Les spécifications OpenID Connect et OAuth 2,0 définissent un certain nombre de flux d’authentification, notamment :

- implicite. Ce Flow est optimisé pour les applications basées sur un navigateur et doit être utilisé pour l’authentification de l’utilisateur uniquement ou pour les demandes d’authentification et de jeton d’accès. Tous les jetons sont transmis par le biais du navigateur, et par conséquent, des fonctionnalités avancées telles que les jetons d’actualisation ne sont pas autorisées.
- Code d’autorisation. Ce processus permet de récupérer des jetons sur un canal Back, par opposition au canal frontal du navigateur, tout en prenant également en charge l’authentification du client.
- Hybride. Ce Flow est une combinaison de types d’octroi de code d’autorisation et implicite. Le jeton d’identité est transmis via le canal Browser et contient la réponse de protocole signée, ainsi que d’autres artefacts tels que le code d’autorisation. Une fois la validation de la réponse réussie, le canal arrière doit être utilisé pour récupérer le jeton d’accès et d’actualisation.

> [!TIP]
> Utilisez le workflow d’authentification hybride. Le workflow d’authentification hybride atténue un certain nombre d’attaques qui s’appliquent au canal du navigateur. il s’agit du processus recommandé pour les applications natives qui souhaitent récupérer des jetons d’accès (et éventuellement des jetons d’actualisation).

Pour plus d’informations sur les flux d’authentification, consultez [Grant types](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) dans la documentation IdentityServer 4.

### <a name="performing-authentication"></a>Exécution de l’authentification

Pour que IdentityServer émette des jetons pour le compte d’un utilisateur, l’utilisateur doit se connecter à IdentityServer. Toutefois, IdentityServer ne fournit pas d’interface utilisateur ou de base de données pour l’authentification. Par conséquent, dans l’application de référence eShopOnContainers, ASP.NET Core identité est utilisée à cette fin.

L’application mobile eShopOnContainers s’authentifie auprès de IdentityServer avec le schéma d’authentification hybride, illustré dans la figure 9-2.

![](authentication-and-authorization-images/sign-in.png "High-level overview of the sign-in process")

**Figure 9-2 :** Vue d’ensemble globale du processus de connexion

Une demande de connexion est effectuée `<base endpoint>:5105/connect/authorize` . Après une authentification réussie, IdentityServer retourne une réponse d’authentification contenant un code d’autorisation et un jeton d’identité. Le code d’autorisation est ensuite envoyé à `<base endpoint>:5105/connect/token` , qui répond avec les jetons d’accès, d’identité et d’actualisation.

L’application mobile eShopOnContainers se déconnecte de IdentityServer en envoyant une requête à `<base endpoint>:5105/connect/endsession` , avec des paramètres supplémentaires. Une fois la déconnexion effectuée, IdentityServer répond en renvoyant un URI de redirection de déconnexion de publication à l’application mobile. La figure 9-3 illustre ce processus.

![](authentication-and-authorization-images/sign-out.png "High-level overview of the sign-out process")

**Figure 9-3 :** Vue d’ensemble globale du processus de déconnexion

Dans l’application mobile eShopOnContainers, la communication avec IdentityServer est effectuée par la `IdentityService` classe, qui implémente l' `IIdentityService` interface. Cette interface spécifie que la classe d’implémentation doit fournir des `CreateAuthorizationRequest` `CreateLogoutRequest` méthodes, et `GetTokenAsync` .

#### <a name="signing-in"></a>Connexion

Quand l’utilisateur appuie sur le bouton de **connexion** sur le `LoginView` , le `SignInCommand` de la `LoginViewModel` classe est exécuté, ce qui à son tour exécute la `SignInAsync` méthode. L’exemple de code suivant illustre cette méthode :

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Cette méthode appelle la `CreateAuthorizationRequest` méthode dans la `IdentityService` classe, qui est illustrée dans l’exemple de code suivant :

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
    dic.Add("redirect_uri", GlobalSetting.Instance.Callback);
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

Cette méthode crée l’URI pour le [point de terminaison d’autorisation](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)de IdentityServer, avec les paramètres requis. Le point de terminaison d’autorisation se trouve `/connect/authorize` sur le port 5105 du point de terminaison de base exposé comme paramètre utilisateur. Pour plus d’informations sur les paramètres utilisateur, consultez Gestion de la [configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> La surface d’attaque de l’application mobile eShopOnContainers est réduite en implémentant l’extension de clé de validation pour l’échange de code (PKCE) dans OAuth. PKCE protège le code d’autorisation de l’utiliser s’il est intercepté. Cela est réalisé par le client qui génère un vérificateur de secret, un hachage de qui est transmis dans la demande d’autorisation et qui est présenté non haché lors du remboursement du code d’autorisation. Pour plus d’informations sur PKCE, consultez [clé de vérification pour l’échange de code par des clients publics OAuth](https://tools.ietf.org/html/rfc7636) sur le site Web IETF (Internet Engineering Task Force).

L’URI retourné est stocké dans la `LoginUrl` propriété de la `LoginViewModel` classe. Lorsque la `IsLogin` propriété devient `true` , le [`WebView`](xref:Xamarin.Forms.WebView) dans le `LoginView` devient visible. Les `WebView` données lient sa [`Source`](xref:Xamarin.Forms.WebView.Source) propriété à la `LoginUrl` propriété de la `LoginViewModel` classe, ce qui fait une demande de connexion à IdentityServer lorsque la `LoginUrl` propriété est définie sur le point de terminaison d’autorisation de IdentityServer. Quand IdentityServer reçoit cette demande et que l’utilisateur n’est pas authentifié, le `WebView` est redirigé vers la page de connexion configurée, comme illustré à la Figure 9-4.

![](authentication-and-authorization-images/login.png "Login page displayed by the WebView")

**Figure 9-4 :** Page de connexion affichée par la WebView

Une fois la connexion terminée, le [`WebView`](xref:Xamarin.Forms.WebView) est redirigé vers un URI de retour. Cette `WebView` navigation entraîne l' `NavigateAsync` exécution de la méthode dans la `LoginViewModel` classe, qui est illustrée dans l’exemple de code suivant :

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

Cette méthode analyse la réponse d’authentification contenue dans l’URI de retour et, à condition qu’un code d’autorisation valide soit présent, envoie une demande au [point de terminaison de jeton](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)de IdentityServer, en transmettant le code d’autorisation, le vérificateur de secret PKCE et d’autres paramètres requis. Le point de terminaison de jeton se trouve `/connect/token` sur le port 5105 du point de terminaison de base exposé comme paramètre utilisateur. Pour plus d’informations sur les paramètres utilisateur, consultez Gestion de la [configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!TIP]
> Validez les URI de retour. Bien que l’application mobile eShopOnContainers ne valide pas l’URI de retour, la meilleure pratique consiste à valider que l’URI de retour fait référence à un emplacement connu, afin d’éviter les attaques par redirection ouverte.

Si le point de terminaison de jeton reçoit un code d’autorisation valide et un vérificateur de secret PKCE, il répond avec un jeton d’accès, un jeton d’identité et un jeton d’actualisation. Le jeton d’accès (qui autorise l’accès aux ressources de l’API) et le jeton d’identité sont ensuite stockés en tant que paramètres d’application, et la navigation entre les pages est effectuée. Par conséquent, l’effet global dans l’application mobile eShopOnContainers est le suivant : à condition que les utilisateurs soient en mesure de s’authentifier avec IdentityServer, ils accèdent à la `MainView` page, qui est un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) qui affiche l' `CatalogView` comme onglet sélectionné.

Pour plus d’informations sur la navigation entre les pages, consultez [navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md). Pour plus d’informations sur la façon dont la [`WebView`](xref:Xamarin.Forms.WebView) navigation provoque l’exécution d’une méthode de modèle de vue, consultez appel de la [navigation à l’aide de comportements](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Pour plus d’informations sur les paramètres d’application, consultez Gestion de la [configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Le eShopOnContainers autorise également une connexion fictive lorsque l’application est configurée pour utiliser des services fictifs dans le `SettingsView` . Dans ce mode, l’application ne communique pas avec IdentityServer, ce qui permet à l’utilisateur de se connecter à l’aide des informations d’identification.

#### <a name="signing-out"></a>Déconnexion

Quand l’utilisateur appuie sur le bouton **déconnexion** dans le `ProfileView` , le `LogoutCommand` de la `ProfileViewModel` classe est exécuté, ce qui à son tour exécute la `LogoutAsync` méthode. Cette méthode effectue la navigation entre les pages sur la `LoginView` page, en passant une `LogoutParameter` instance de définie à `true` comme paramètre. Pour plus d’informations sur le passage de paramètres pendant la navigation entre les pages, consultez [transmission de paramètres lors](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)de la navigation.

Quand une vue est créée et naviguée vers, la `InitializeAsync` méthode du modèle de vue associé à la vue est exécutée, ce qui exécute ensuite la `Logout` méthode de la `LoginViewModel` classe, qui est illustrée dans l’exemple de code suivant :

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

Cette méthode appelle la `CreateLogoutRequest` méthode dans la `IdentityService` classe, en passant le jeton d’identité récupéré à partir des paramètres de l’application en tant que paramètre. Pour plus d’informations sur les paramètres d’application, consultez Gestion de la [configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). L’exemple de code suivant montre la méthode `CreateLogoutRequest` :

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

Cette méthode crée l’URI sur le [point de terminaison de session de fin](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)de IdentityServer, avec les paramètres requis. Le point de terminaison de session de fin se trouve `/connect/endsession` sur le port 5105 du point de terminaison de base exposé comme paramètre utilisateur. Pour plus d’informations sur les paramètres utilisateur, consultez Gestion de la [configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

L’URI retourné est stocké dans la `LoginUrl` propriété de la `LoginViewModel` classe. Alors que la `IsLogin` propriété est `true` , le [`WebView`](xref:Xamarin.Forms.WebView) dans `LoginView` est visible. Les `WebView` données lient sa [`Source`](xref:Xamarin.Forms.WebView.Source) propriété à la `LoginUrl` propriété de la `LoginViewModel` classe, ce qui fait une demande de déconnexion à IdentityServer lorsque la `LoginUrl` propriété est définie sur le point de terminaison de session de fin de IdentityServer. Quand IdentityServer reçoit cette demande, à condition que l’utilisateur soit connecté, la déconnexion se produit. L’authentification est suivie avec un cookie géré par l’intergiciel (middleware) d’authentification des cookies à partir de ASP.NET Core. Par conséquent, la déconnexion de IdentityServer supprime le cookie d’authentification et renvoie un URI de redirection de déconnexion de publication au client.

Dans l’application mobile, le [`WebView`](xref:Xamarin.Forms.WebView) sera redirigé vers l’URI de redirection de la déconnexion après la déconnexion. Cette `WebView` navigation entraîne l' `NavigateAsync` exécution de la méthode dans la `LoginViewModel` classe, qui est illustrée dans l’exemple de code suivant :

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

Cette méthode efface à la fois le jeton d’identité et le jeton d’accès des paramètres de l’application, et affecte `IsLogin` à la propriété la valeur `false` , ce qui provoque l' [`WebView`](xref:Xamarin.Forms.WebView) invisibilité du sur la `LoginView` page. Enfin, la `LoginUrl` propriété est définie sur l’URI du point de [terminaison d’autorisation](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)de IdentityServer, avec les paramètres requis, en préparation pour la prochaine fois que l’utilisateur initie une connexion.

Pour plus d’informations sur la navigation entre les pages, consultez [navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md). Pour plus d’informations sur la façon dont la [`WebView`](xref:Xamarin.Forms.WebView) navigation provoque l’exécution d’une méthode de modèle de vue, consultez appel de la [navigation à l’aide de comportements](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Pour plus d’informations sur les paramètres d’application, consultez Gestion de la [configuration](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Le eShopOnContainers autorise également une déconnexion factice lorsque l’application est configurée pour utiliser des services fictifs dans le SettingsView. Dans ce mode, l’application ne communique pas avec IdentityServer et efface à la place tous les jetons stockés des paramètres de l’application.

<a name="authorization" />

## <a name="authorization"></a>Autorisation

Après l’authentification, ASP.NET Core API Web doivent souvent autoriser l’accès, ce qui permet à un service de rendre les API disponibles pour certains utilisateurs authentifiés, mais pas pour tous.

La restriction de l’accès à un ASP.NET Core itinéraire MVC peut être obtenue en appliquant un attribut Authorize à un contrôleur ou à une action, ce qui limite l’accès au contrôleur ou à l’action aux utilisateurs authentifiés, comme illustré dans l’exemple de code suivant :

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Si un utilisateur non autorisé tente d’accéder à un contrôleur ou une action marqué avec l' `Authorize` attribut, l’infrastructure MVC retourne un code d’état HTTP 401 (non autorisé).

> [!NOTE]
> Des paramètres peuvent être spécifiés sur l' `Authorize` attribut pour limiter une API à des utilisateurs spécifiques. Pour plus d’informations, consultez [Autorisation](/aspnet/core/security/authorization/introduction/).

Les IdentityServer peuvent être intégrés dans le flux de travail d’autorisation afin que les jetons d’accès qu’il fournit autorisent le contrôle. Cette approche est illustrée dans la figure 9-5.

![](authentication-and-authorization-images/authorization.png "Authorization by access token")

**Figure 9-5 :** Autorisation par jeton d’accès

L’application mobile eShopOnContainers communique avec le microservice d’identité et demande un jeton d’accès dans le cadre du processus d’authentification. Le jeton d’accès est ensuite transféré aux API exposées par les microservices de classement et de panier dans le cadre des demandes d’accès. Les jetons d’accès contiennent des informations sur le client et l’utilisateur. Les API utilisent ensuite ces informations pour autoriser l’accès à leurs données. Pour plus d’informations sur la configuration de IdentityServer pour protéger les API, consultez [Configuring API Resources](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configuration de IdentityServer pour effectuer une autorisation

Pour effectuer l’autorisation avec IdentityServer, son intergiciel d’autorisation doit être ajouté au pipeline de requête HTTP de l’application Web. L’intergiciel est ajouté à la `ConfigureAuth` méthode dans la classe de l’application Web `Startup` , qui est appelée à partir de la `Configure` méthode, et est illustré dans l’exemple de code suivant à partir de l’application de référence eShopOnContainers :

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

Cette méthode garantit que l’API est accessible uniquement avec un jeton d’accès valide. L’intergiciel valide le jeton entrant pour s’assurer qu’il est envoyé à partir d’un émetteur approuvé et valide que le jeton est valide pour être utilisé avec l’API qui le reçoit. Par conséquent, le fait de naviguer jusqu’au contrôleur de classement ou de panier renverra un code d’état HTTP 401 (non autorisé), indiquant qu’un jeton d’accès est requis.

> [!NOTE]
> L’intergiciel d’autorisation de IdentityServer doit être ajouté au pipeline de requête HTTP de l’application Web avant d’ajouter MVC avec `app.UseMvc()` ou `app.UseMvcWithDefaultRoute()` .

### <a name="making-access-requests-to-apis"></a>Effectuer des demandes d’accès aux API

Lorsque vous effectuez des demandes aux microservices de classement et de panier, le jeton d’accès obtenu à partir de IdentityServer pendant le processus d’authentification doit être inclus dans la demande, comme illustré dans l’exemple de code suivant :

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

Le jeton d’accès est stocké en tant que paramètre d’application et est récupéré à partir du stockage spécifique à la plateforme et inclus dans l’appel à la `GetOrderAsync` méthode dans la `OrderService` classe.

De même, le jeton d’accès doit être inclus lors de l’envoi de données à une API protégée par IdentityServer, comme illustré dans l’exemple de code suivant :

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

Le jeton d’accès est récupéré à partir du stockage spécifique à la plateforme et inclus dans l’appel à la `UpdateBasketAsync` méthode dans la `BasketService` classe.

La `RequestProvider` classe, dans l’application mobile eShopOnContainers, utilise la `HttpClient` classe pour effectuer des demandes aux API RESTful exposées par l’application de référence eShopOnContainers. Lorsque vous effectuez des demandes aux API de classement et de panier, qui requièrent une autorisation, un jeton d’accès valide doit être inclus dans la demande. Cela est possible en ajoutant le jeton d’accès aux en-têtes de l' `HttpClient` instance, comme illustré dans l’exemple de code suivant :

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

La `DefaultRequestHeaders` propriété de la `HttpClient` classe expose les en-têtes envoyés avec chaque requête, et le jeton d’accès est ajouté à l' `Authorization` en-tête préfixé avec la chaîne `Bearer` . Lorsque la demande est envoyée à une API RESTful, la valeur de l' `Authorization` en-tête est extraite et validée pour s’assurer qu’elle est envoyée à partir d’un émetteur approuvé et utilisée pour déterminer si l’utilisateur a l’autorisation d’appeler l’API qui la reçoit.

Pour plus d’informations sur la façon dont l’application mobile eShopOnContainers effectue des requêtes Web, consultez [accès aux données distantes](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Résumé

Il existe de nombreuses approches pour intégrer l’authentification et l’autorisation dans une Xamarin.Forms application qui communique avec une application web ASP.NET MVC. L’application mobile eShopOnContainers effectue l’authentification et l’autorisation avec un microservice d’identité en conteneur qui utilise IdentityServer 4. IdentityServer est une infrastructure Open source OpenID Connect et OAuth 2,0 pour ASP.NET Core qui s’intègre avec ASP.NET Core identité pour exécuter l’authentification du jeton du porteur.

L’application mobile demande des jetons de sécurité à partir de IdentityServer, soit pour authentifier un utilisateur, soit pour accéder à une ressource. Lors de l’accès à une ressource, un jeton d’accès doit être inclus dans la demande aux API qui nécessitent une autorisation. L’intergiciel (middleware) de IdentityServer valide les jetons d’accès entrants pour s’assurer qu’ils sont envoyés à partir d’un émetteur approuvé et qu’ils sont valides pour être utilisés avec l’API qui les reçoit.

## <a name="related-links"></a>Liens connexes

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
