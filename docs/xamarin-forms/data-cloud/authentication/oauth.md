---
title: AuthenticateUsers avec un fournisseur d’identité
description: Cet article explique comment utiliser Xamarin.Auth pour gérer le processus d’authentification dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 0fa433de7fd1acb6fb27741f1615a644315f373f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292653"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Authentifier les utilisateurs avec un fournisseur d’identité

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Xamarin. auth est un kit de développement logiciel multiplateforme pour l’authentification des utilisateurs et le stockage de leurs comptes. Il comprend des authentificateurs OAuth qui assurent la prise en charge de l’utilisation de fournisseurs d’identité tels que Google, Microsoft, Facebook et Twitter. Cet article explique comment utiliser Xamarin. auth pour gérer le processus d’authentification dans une application Xamarin. Forms._

OAuth est une norme ouverte pour l’authentification et permet à un propriétaire de la ressource notifier à un fournisseur de ressources que l’autorisation doit être accordée à un tiers pour accéder à leurs informations sans partager l’identité de propriétaires de ressources. Un exemple de ce serait être permettant à l’utilisateur à notifier à un fournisseur d’identité (par exemple, Google, Microsoft, Facebook ou Twitter) que l’autorisation doit être accordée à une application pour accéder à leurs données, sans partager l’identité de l’utilisateur. Il est couramment utilisé comme une approche pour les utilisateurs à se connecter aux sites Web et applications à l’aide d’un fournisseur d’identité, mais sans exposer son mot de passe à l’application ou le site Web.

Une vue d’ensemble du flux d’authentification lors de l’utilisation d’un fournisseur d’identité OAuth est comme suit :

1. L’application accède à un navigateur vers une URL de fournisseur d’identité.
1. Le fournisseur d’identité gère l’authentification utilisateur et retourne un code d’autorisation à l’application.
1. L’application échange le code d’autorisation pour un jeton d’accès du fournisseur d’identité.
1. L’application utilise le jeton d’accès pour accéder aux API sur le fournisseur d’identité, comme une API permettant de demander des données utilisateur de base.

L’exemple d’application montre comment utiliser Xamarin.Auth pour implémenter un flux d’authentification natif sur Google. Tandis que Google est utilisé comme fournisseur d’identité dans cette rubrique, l’approche s’applique aux autres fournisseurs d’identité. Pour plus d’informations sur l’authentification à l’aide du point de terminaison OAuth 2,0 de Google, consultez [utilisation d’OAuth 2.0 pour accéder aux API Google](https://developers.google.com/identity/protocols/OAuth2) sur le site Web de Google.

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
> L’ATS peut être exclu de s’il n’est pas possible d’utiliser le protocole `HTTPS` et de sécuriser la communication pour les ressources Internet. Pour ce faire, vous pouvez mettre à jour le fichier **info. plist** de l’application. Pour plus d’informations, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>À l’aide de Xamarin.Auth pour authentifier les utilisateurs

Xamarin.Auth prend en charge deux approches pour les applications d’interagir avec le point de terminaison du fournisseur d’identité d’autorisation :

1. À l’aide d’une vue web intégré. Bien que cela a été une pratique courante, il n’est plus recommandé pour les raisons suivantes :

    - L’application qui héberge l’affichage web peut accéder aux informations d’identification de l’utilisateur une authentification complète, pas seulement l’octroi d’autorisation OAuth était destiné à l’application. Cela viole le principe du moindre privilège, que l’application ait un accès aux informations d’identification plus puissants qu’il requiert, augmenter potentiellement la surface d’attaque de l’application.
    - L’application hôte peut capturer les noms d’utilisateur et mots de passe, automatiquement soumettez des formulaires et contourner le consentement de l’utilisateur et copiez les cookies de session et les utiliser pour effectuer des actions authentifiées en tant que l’utilisateur.
    - Les vues web incorporées ne partagent pas l’état de l’authentification avec les autres applications ou navigateur web de l’appareil, lui demandant de connexion à chaque demande d’autorisation qui est considérée comme une expérience utilisateur de moindre qualité.
    - Certains points de terminaison d’autorisation prennent des mesures pour détecter et bloquer les demandes d’autorisation qui proviennent des vues web.

1. À l’aide du navigateur web de l’appareil, qui est l’approche recommandée. À l’aide du navigateur de l’appareil pour les requêtes OAuth d’améliore la facilité d’utilisation d’une application, comme les utilisateurs doivent uniquement se connecter au fournisseur d’identité une fois par appareil, améliorer le taux de conversion de flux de connexion et l’autorisation de l’application. Navigateur de l’appareil fournit également une sécurité améliorée comme les applications sont en mesure d’inspecter et modifier du contenu dans un affichage web, mais pas le contenu affiché dans le navigateur. Il s’agit de l’approche adoptée dans cet article et exemple d’application.

Une vue d’ensemble de la façon dont l’exemple d’application utilise Xamarin.Auth pour authentifier les utilisateurs et récupérer leurs données de base est illustré dans le diagramme suivant :

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

L’application envoie une demande d’authentification à Google à l’aide de la classe `OAuth2Authenticator`. Une réponse d’authentification est retournée, une fois que l’utilisateur a correctement authentifié auprès de Google via leur page de connexion, ce qui inclut un jeton d’accès. L’application envoie ensuite une requête à Google pour les données utilisateur de base, à l’aide de la classe `OAuth2Request`, avec le jeton d’accès inclus dans la demande.

### <a name="setup"></a>Programme d’installation

Un projet de Console d’API Google doit être créé pour intégrer l’authentification Google dans une application Xamarin.Forms. Cela peut être accompli de la façon suivante :

1. Accédez au site Web de la console de l' [API Google](https://console.developers.google.com) et connectez-vous avec les informations d’identification de votre compte Google.
1. Dans la liste déroulante projet, sélectionnez un projet existant ou créez-en un.
1. Dans la barre latérale sous « gestionnaire d’API », sélectionnez **informations d’identification**, puis sélectionnez l’onglet de l' **écran de consentement OAuth**. Choisissez une **adresse de messagerie**, spécifiez un **nom de produit affiché aux utilisateurs**, puis cliquez sur **Enregistrer**.
1. Dans l’onglet **informations d’identification** , sélectionnez la liste déroulante **créer les informations d’identification** , puis choisissez **ID client OAuth**.
1. Sous **type d’application**, sélectionnez la plateforme sur laquelle l’application mobile s’exécutera (**iOS** ou **Android**).
1. Renseignez les informations requises, puis sélectionnez le bouton **créer** .

> [!NOTE]
> Un ID Client permet à une application d’accéder à APIs Google activées et pour les applications mobiles est propre à une plateforme unique. Par conséquent, vous devez créer un **ID client OAuth** pour chaque plateforme qui utilisera la connexion Google.

Après avoir effectué ces étapes, Xamarin.Auth peut être utilisé pour lancer un flux d’authentification OAuth2 avec Google.

### <a name="creating-and-configuring-an-authenticator"></a>Création et configuration d’un authentificateur

La classe de `OAuth2Authenticator` de Xamarin. auth est chargée de gérer le workflow d’authentification OAuth. L’exemple de code suivant montre l’instanciation de la classe `OAuth2Authenticator` lors de l’exécution de l’authentification à l’aide du navigateur Web de l’appareil :

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

La classe `OAuth2Authenticator` requiert un certain nombre de paramètres, qui sont les suivants :

- **ID client** : identifie le client qui effectue la demande et peut être récupéré à partir du projet dans la console d' [API Google](https://console.developers.google.com).
- **Clé secrète client** : `null` ou `string.Empty`.
- **Étendue** : identifie l’accès d’API demandé par l’application, et la valeur informe l’écran de consentement qui s’affiche pour l’utilisateur. Pour plus d’informations sur les étendues, consultez [autoriser les demandes](https://developers.google.com/docs/api/how-tos/authorizing) sur le site Web de Google.
- **Authorize URL** : identifie l’URL à partir de laquelle le code d’autorisation sera obtenu.
- **URL de redirection** : identifie l’URL où la réponse sera envoyée. La valeur de ce paramètre doit correspondre à l’une des valeurs qui s’affichent dans l’onglet **informations d’identification** du projet dans la [console des développeurs Google](https://console.developers.google.com/).
- **URL AccessToken** : identifie l’URL utilisée pour demander des jetons d’accès après l’obtention d’un code d’autorisation.
- **GetUserNameAsync Func** : `Func` facultative qui sera utilisée pour récupérer de manière asynchrone le nom d’utilisateur du compte une fois qu’il a été authentifié avec succès.
- **Utiliser l’interface utilisateur native** : valeur `boolean` indiquant s’il faut utiliser le navigateur Web de l’appareil pour effectuer la demande d’authentification.

### <a name="setup-authentication-event-handlers"></a>Le programme d’installation de gestionnaires d’événements d’authentification

Avant de présenter l’interface utilisateur, un gestionnaire d’événements pour l’événement `OAuth2Authenticator.Completed` doit être inscrit, comme indiqué dans l’exemple de code suivant :

```csharp
authenticator.Completed += OnAuthCompleted;
```

Cet événement se déclenche lorsque l’utilisateur s’authentifie avec succès ou annule l’authentification dans.

Éventuellement, un gestionnaire d’événements pour l’événement `OAuth2Authenticator.Error` peut également être enregistré.

### <a name="presenting-the-sign-in-user-interface"></a>Présentation de l’Interface utilisateur de connexion

L’interface utilisateur de connexion peut être présenté à l’utilisateur à l’aide d’un présentateur de connexion Xamarin.Auth, ce qui doit être initialisé dans chaque projet de plateforme. L’exemple de code suivant montre comment initialiser un présentateur de connexion dans la classe `AppDelegate` dans le projet iOS :

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

L’exemple de code suivant montre comment initialiser un présentateur de connexion dans la classe `MainActivity` dans le projet Android :

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

Le projet de bibliothèque .NET Standard peut ensuite appeler le présentateur de connexion comme suit :

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Notez que l’argument de la méthode `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` est l’instance `OAuth2Authenticator`. Lorsque la méthode `Login` est appelée, l’interface utilisateur de connexion est présentée à l’utilisateur dans un onglet du navigateur Web de l’appareil, qui est illustré dans les captures d’écran suivantes :

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Traitement de l’URL de redirection

Une fois que l’utilisateur a terminé le processus d’authentification, le contrôle revient à l’application à partir de l’onglet de navigateur Web. Pour ce faire, vous devez inscrire un modèle d’URL personnalisé pour l’URL de redirection retournée à partir du processus d’authentification, puis détecter et gérer l’URL personnalisée une fois qu’elle est envoyée.

Lorsque vous choisissez un schéma d’URL personnalisé à associer à une application, les applications doivent utiliser un schéma basé sur un nom sous leur contrôle. Cela est possible en utilisant le nom d’identificateur de bundle sur iOS et le nom du package sur Android et en inversant les pour rendre le schéma d’URL. Toutefois, certains fournisseurs d’identité, tel que Google, assignent des identificateurs de client basées sur les noms de domaine, qui sont ensuite inversés et utilisés en tant que le schéma d’URL. Par exemple, si Google crée un ID client de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, le schéma d’URL sera `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Notez qu’un seul `/` peut apparaître après le composant de schéma. Par conséquent, un exemple complet d’URL de redirection utilisant un modèle d’URL personnalisé est `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Lorsque le navigateur web reçoit une réponse du fournisseur d’identité qui contient un schéma d’URL personnalisé, il essaie de charger l’URL, qui échoue. Au lieu de cela, le schéma d’URL personnalisé est signalé au système d’exploitation en déclenchant un événement. Il vérifie ensuite le système d’exploitation pour les schémas inscrits, et s’il existe, le système d’exploitation lancer l’application qui inscrit le schéma et envoyez-la à l’URL de redirection.

Le mécanisme d’inscription d’un schéma d’URL personnalisé avec le système d’exploitation et le schéma de manutention est spécifique à chaque plateforme.

#### <a name="ios"></a>iOS

Sur iOS, un schéma d’URL personnalisé est inscrit dans **info. plist**, comme indiqué dans la capture d’écran suivante :

![](oauth-images/info-plist.png "URL Scheme Registration")

La valeur de l' **identificateur** peut être n’importe quoi, et la valeur de **rôle** doit être définie sur la **visionneuse**. La valeur des **schémas d’URL** , qui commence par `com.googleusercontent.apps`, peut être obtenue à partir de l’ID client iOS du projet sur la console de l' [API Google](https://console.developers.google.com).

Le fournisseur d’identité issue de la demande d’autorisation, il redirige vers les URL de redirection de l’application. Étant donné que l’URL utilise un schéma personnalisé, iOS lance l’application, en transmettant l’URL en tant que paramètre de lancement, où elle est traitée par le `OpenUrl` remplacement de la classe `AppDelegate` de l’application, qui est illustrée dans l’exemple de code suivant :

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

La méthode `OpenUrl` convertit l’URL reçue d’un `NSUrl` en `Uri`.NET, avant de traiter l’URL de redirection avec la méthode `OnPageLoading` d’un objet `OAuth2Authenticator` public. Cela entraîne Xamarin.Auth pour fermer l’onglet du navigateur web et pour analyser les données OAuth reçues.

#### <a name="android"></a>Android

Sur Android, un modèle d’URL personnalisé est inscrit en spécifiant un attribut [`IntentFilter`](xref:Android.App.IntentFilterAttribute) sur le `Activity` qui gère le schéma. Le fournisseur d’identité issue de la demande d’autorisation, il redirige vers les URL de redirection de l’application. Étant donné que l’URL utilise un schéma personnalisé, Android lance l’application, en transmettant l’URL en tant que paramètre de lancement, où elle est traitée par la méthode `OnCreate` de la `Activity` inscrite pour gérer le schéma d’URL personnalisé. L’exemple de code suivant montre la classe à partir de l’exemple d’application qui gère le schéma d’URL personnalisé :

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

La propriété `DataSchemes` du [`IntentFilter`](xref:Android.App.IntentFilterAttribute) doit être définie sur l’identificateur de client inversé qui est obtenu à partir de l’ID client Android du projet sur la [console de l’API Google](https://console.developers.google.com).

La méthode `OnCreate` convertit l’URL reçue d’un `Android.Net.Url` en `Uri`.NET, avant de traiter l’URL de redirection avec la méthode `OnPageLoading` d’un objet `OAuth2Authenticator` public. Cela entraîne Xamarin.Auth fermer l’onglet du navigateur web et analyser les données reçues de OAuth.

> [!IMPORTANT]
> Sur Android, Xamarin. auth utilise l’API `CustomTabs` pour communiquer avec le navigateur Web et le système d’exploitation. Toutefois, il n’est pas garanti qu’un navigateur compatible `CustomTabs` sera installé sur l’appareil de l’utilisateur.

### <a name="examining-the-oauth-response"></a>Examen de la réponse OAuth

Après l’analyse des données OAuth reçues, Xamarin. auth déclenchera l’événement `OAuth2Authenticator.Completed`. Dans le gestionnaire d’événements pour cet événement, la propriété `AuthenticatorCompletedEventArgs.IsAuthenticated` peut être utilisée pour déterminer si l’authentification a réussi, comme illustré dans l’exemple de code suivant :

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

Les données collectées à partir d’une authentification réussie sont disponibles dans la propriété `AuthenticatorCompletedEventArgs.Account`. Cela inclut un jeton d’accès, qui peut être utilisé pour signer des requêtes pour les données à une API fournie par le fournisseur d’identité.

### <a name="making-requests-for-data"></a>Effectuer des requêtes pour les données

Une fois que l’application a obtenu un jeton d’accès, elle est utilisée pour effectuer une requête auprès de l’API `https://www.googleapis.com/oauth2/v2/userinfo`, afin de demander des données utilisateur de base au fournisseur d’identité. Cette demande est effectuée avec la classe `OAuth2Request`. auth de Xamarin, qui représente une demande authentifiée à l’aide d’un compte récupéré à partir d’une instance de `OAuth2Authenticator`, comme illustré dans l’exemple de code suivant :

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

De même que la méthode HTTP et l’URL de l’API, l’instance de `OAuth2Request` spécifie également une instance `Account` qui contient le jeton d’accès qui signe la demande à l’URL spécifiée par la propriété `Constants.UserInfoUrl`. Le fournisseur d’identité retourne ensuite les données utilisateur de base comme une réponse JSON, y compris nom de l’utilisateur et l’adresse de messagerie, sous réserve qu’il identifie le jeton d’accès comme étant valide. La réponse JSON est ensuite lue et désérialisée dans la variable `user`.

Pour plus d’informations, consultez [appel d’une API Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) sur le portail des développeurs Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Stocker et récupérer des informations de compte sur les appareils

Xamarin. auth stocke les objets `Account` dans un magasin de comptes de manière sécurisée, de sorte que les applications n’ont pas toujours besoin de réauthentifier les utilisateurs. La classe `AccountStore` est chargée de stocker les informations de compte et est sauvegardée par les services trousseau dans iOS, et la classe `KeyStore` dans Android.

> [!IMPORTANT]
> La classe `AccountStore` dans Xamarin. auth est dépréciée et la classe Xamarin. Essentials `SecureStorage` doit être utilisée à la place. Pour plus d’informations, consultez [migration de AccountStore vers Xamarin. Essentials SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage).

L’exemple de code suivant montre comment un objet `Account` est enregistré en toute sécurité :

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Les comptes enregistrés sont identifiés de manière unique à l’aide d’une clé composée de la propriété `Username` du compte et d’un ID de service, qui est une chaîne utilisée lors de l’extraction de comptes du magasin de comptes. Si une `Account` a été enregistrée précédemment, l’appel de la méthode `Save` le remplacera.

les objets `Account` pour un service peuvent être récupérés en appelant la méthode `FindAccountsForService`, comme indiqué dans l’exemple de code suivant :

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

La méthode `FindAccountsForService` retourne une collection `IEnumerable` d’objets `Account`, le premier élément de la collection étant défini comme étant le compte correspondant.

## <a name="troubleshooting"></a>Dépannage

- Sur Android, si vous recevez une notification de Toast quand vous fermez le navigateur après l’authentification et que vous souhaitez arrêter la notification Toast, ajoutez le code suivant au projet Android après l’initialisation de Xamarin. auth :

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- Sur Android, si le navigateur ne se ferme pas automatiquement, une solution de contournement temporaire consiste à rétrograder le package Xamarin. auth à la version 1.5.0.3. Ensuite, ajoutez le port [PCL v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147) au projet Android.

## <a name="summary"></a>Résumé

Cet article a expliqué comment utiliser Xamarin.Auth pour gérer le processus d’authentification dans une application Xamarin.Forms. Xamarin. auth fournit les classes `OAuth2Authenticator` et `OAuth2Request` utilisées par les applications Xamarin. Forms pour consommer des fournisseurs d’identité tels que Google, Microsoft, Facebook et Twitter.

## <a name="related-links"></a>Liens connexes

- [OAuthNativeFlow (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2,0 pour les applications natives](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Utilisation d’OAuth 2.0 pour accéder aux API Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin. auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin. auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
