---
title: AuthenticateUsers avec un fournisseur d’identité
description: Cet article explique comment utiliser Xamarin.Auth pour gérer le processus d’authentification dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 294483e8b831aa085fc338eb05147ced1098c1c5
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68508249"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Authentifier les utilisateurs avec un fournisseur d’identité

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)

_Xamarin.Auth est un kit de développement logiciel inter-plateformes pour l’authentification des utilisateurs et le stockage de leurs comptes. Il inclut les authentificateurs OAuth qui prennent en charge pour l’utilisation de fournisseurs d’identité comme Google, Microsoft, Facebook et Twitter. Cet article explique comment utiliser Xamarin.Auth pour gérer le processus d’authentification dans une application Xamarin.Forms._

OAuth est une norme ouverte pour l’authentification et permet à un propriétaire de la ressource notifier à un fournisseur de ressources que l’autorisation doit être accordée à un tiers pour accéder à leurs informations sans partager l’identité de propriétaires de ressources. Un exemple de ce serait être permettant à l’utilisateur à notifier à un fournisseur d’identité (par exemple, Google, Microsoft, Facebook ou Twitter) que l’autorisation doit être accordée à une application pour accéder à leurs données, sans partager l’identité de l’utilisateur. Il est couramment utilisé comme une approche pour les utilisateurs à se connecter aux sites Web et applications à l’aide d’un fournisseur d’identité, mais sans exposer son mot de passe à l’application ou le site Web.

Une vue d’ensemble du flux d’authentification lors de l’utilisation d’un fournisseur d’identité OAuth est comme suit :

1. L’application accède à un navigateur vers une URL de fournisseur d’identité.
1. Le fournisseur d’identité gère l’authentification utilisateur et retourne un code d’autorisation à l’application.
1. L’application échange le code d’autorisation pour un jeton d’accès du fournisseur d’identité.
1. L’application utilise le jeton d’accès pour accéder aux API sur le fournisseur d’identité, comme une API permettant de demander des données utilisateur de base.

L’exemple d’application montre comment utiliser Xamarin.Auth pour implémenter un flux d’authentification natif sur Google. Tandis que Google est utilisé comme fournisseur d’identité dans cette rubrique, l’approche s’applique aux autres fournisseurs d’identité. Pour plus d’informations sur l’authentification à l’aide du point de terminaison de Google OAuth 2.0, consultez [à l’aide d’OAuth2.0 à accès APIs Google](https://developers.google.com/identity/protocols/OAuth2) sur le site Web de Google.

> [!NOTE]
> Dans iOS 9 et supérieur, App Transport Security (ATS) applique des connexions sécurisées entre les ressources internet (par exemple, le serveur de l’application back-end) et l’application, ce qui empêche la divulgation accidentelle d’informations sensibles. Étant donné que ATS est activé par défaut dans les applications développées pour iOS 9, toutes les connexions seront soumis à des exigences de sécurité ATS. Si les connexions ne répondent pas à ces exigences, ils échoueront avec une exception.
> ATS peuvent être ignorées si elle n’est pas possible d’utiliser le `HTTPS` de protocole et de sécuriser la communication de ressources internet. Cela est possible en mettant à jour de l’application **Info.plist** fichier. Pour plus d’informations, consultez [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>À l’aide de Xamarin.Auth pour authentifier les utilisateurs

Xamarin.Auth prend en charge deux approches pour les applications d’interagir avec le point de terminaison du fournisseur d’identité d’autorisation :

1. À l’aide d’une vue web intégré. Bien que cela a été une pratique courante, il n’est plus recommandé pour les raisons suivantes :

    - L’application qui héberge l’affichage web peut accéder aux informations d’identification de l’utilisateur une authentification complète, pas seulement l’octroi d’autorisation OAuth était destiné à l’application. Cela viole le principe du moindre privilège, que l’application ait un accès aux informations d’identification plus puissants qu’il requiert, augmenter potentiellement la surface d’attaque de l’application.
    - L’application hôte peut capturer les noms d’utilisateur et mots de passe, automatiquement soumettez des formulaires et contourner le consentement de l’utilisateur et copiez les cookies de session et les utiliser pour effectuer des actions authentifiées en tant que l’utilisateur.
    - Les vues web incorporées ne partagent pas l’état de l’authentification avec les autres applications ou navigateur web de l’appareil, lui demandant de connexion à chaque demande d’autorisation qui est considérée comme une expérience utilisateur de moindre qualité.
    - Certains points de terminaison d’autorisation prennent des mesures pour détecter et bloquer les demandes d’autorisation qui proviennent des vues web.

1. À l’aide du navigateur web de l’appareil, qui est l’approche recommandée. À l’aide du navigateur de l’appareil pour les requêtes OAuth d’améliore la facilité d’utilisation d’une application, comme les utilisateurs doivent uniquement se connecter au fournisseur d’identité une fois par appareil, améliorer le taux de conversion de flux de connexion et l’autorisation de l’application. Navigateur de l’appareil fournit également une sécurité améliorée comme les applications sont en mesure d’inspecter et modifier du contenu dans un affichage web, mais pas le contenu affiché dans le navigateur. Il s’agit de l’approche adoptée dans cet article et exemple d’application.

Une vue d’ensemble de la façon dont l’exemple d’application utilise Xamarin.Auth pour authentifier les utilisateurs et récupérer leurs données de base est illustré dans le diagramme suivant :

![](oauth-images/google-auth.png "À l’aide de Xamarin.Auth pour s’authentifier avec Google")

L’application effectue une demande d’authentification à l’aide de Google la `OAuth2Authenticator` classe. Une réponse d’authentification est retournée, une fois que l’utilisateur a correctement authentifié auprès de Google via leur page de connexion, ce qui inclut un jeton d’accès. L’application puis envoie une demande à Google pour les données utilisateur de base, à l’aide de la `OAuth2Request` classe, avec le jeton d’accès qui est inclus dans la demande.

### <a name="setup"></a>Installation

Un projet de Console d’API Google doit être créé pour intégrer l’authentification Google dans une application Xamarin.Forms. Cela peut être accompli de la façon suivante :

1. Accédez à la [Console d’API Google](http://console.developers.google.com) site Web et connectez-vous avec les informations d’identification du compte Google.
1. Dans la liste déroulante projet, sélectionnez un projet existant ou créez-en un.
1. Dans la barre latérale sous « API Manager », sélectionnez **informations d’identification**, puis sélectionnez le **onglet écran de consentement OAuth**. Choisissez un **adresse de messagerie**, spécifiez un **nom de produit présenté aux utilisateurs**, puis appuyez sur **enregistrer**.
1. Dans le **informations d’identification** onglet, sélectionnez le **créer les informations d’identification** déroulante liste, puis choisissez **ID client OAuth**.
1. Sous **type d’Application**, sélectionnez la plateforme de l’application mobile s’exécuteront sur (**iOS** ou **Android**).
1. Renseignez les informations requises, sélectionnez le **créer** bouton.

> [!NOTE]
> Un ID Client permet à une application d’accéder à APIs Google activées et pour les applications mobiles est propre à une plateforme unique. Par conséquent, un **ID client OAuth** doit être créé pour chaque plateforme que va utiliser l’authentification dans Google.

Après avoir effectué ces étapes, Xamarin.Auth peut être utilisé pour lancer un flux d’authentification OAuth2 avec Google.

### <a name="creating-and-configuring-an-authenticator"></a>Création et configuration d’un authentificateur

De Xamarin.Auth `OAuth2Authenticator` classe est chargé de gérer le flux d’authentification OAuth. L’exemple de code suivant montre l’instanciation de la `OAuth2Authenticator` classe lors de l’authentification à l’aide du navigateur web de l’appareil :

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

Le `OAuth2Authenticator` classe requiert un nombre de paramètres, qui sont les suivantes :

- **ID client** – identifie le client qui effectue la demande et peut être récupéré à partir du projet dans le [Console d’API Google](http://console.developers.google.com).
- **Clé secrète client** – cette valeur doit être `null` ou `string.Empty`.
- **Étendue** : Cela permet d’identifier l’accès à l’API demandée par l’application, et la valeur indique à l’écran de consentement est présentée à l’utilisateur. Pour plus d’informations sur les étendues, consultez [demande d’API d’autorisation](https://developers.google.com/+/web/api/rest/oauth) sur le site Web de Google.
- **Autoriser les URL** – ce nom identifie l’URL où le code d’autorisation sera obtenu à partir de.
- **URL de redirection** – ce nom identifie l’URL où la réponse sera envoyée. La valeur de ce paramètre doit correspondre à une des valeurs qui s’affiche dans le **informations d’identification** onglet pour le projet dans le [Google Developers Console](https://console.developers.google.com/).
- **Url de AccessToken** : Cela permet d’identifier l’URL utilisée pour demander des jetons d’accès après obtention d’un code d’autorisation.
- **GetUserNameAsync Func** : facultatif `Func` qui sera utilisé pour récupérer le nom d’utilisateur du compte de façon asynchrone une fois qu’il est authentifié avec succès.
- **Utilisez l’interface utilisateur Native** : un `boolean` valeur indiquant s’il faut utiliser le navigateur web de l’appareil pour effectuer la demande d’authentification.

### <a name="setup-authentication-event-handlers"></a>Le programme d’installation de gestionnaires d’événements d’authentification

Avant de présenter l’interface utilisateur, un gestionnaire d’événements pour le `OAuth2Authenticator.Completed` événement doit être inscrit, comme indiqué dans l’exemple de code suivant :

```csharp
authenticator.Completed += OnAuthCompleted;
```

Cet événement se déclenche lorsque l’utilisateur s’authentifie avec succès ou annule l’authentification dans.

Si vous le souhaitez, un gestionnaire d’événements pour le `OAuth2Authenticator.Error` événements peuvent également être inscrits.

### <a name="presenting-the-sign-in-user-interface"></a>Présentation de l’Interface utilisateur de connexion

L’interface utilisateur de connexion peut être présenté à l’utilisateur à l’aide d’un présentateur de connexion Xamarin.Auth, ce qui doit être initialisé dans chaque projet de plateforme. L’exemple de code suivant montre comment initialiser un présenteur de connexion dans le `AppDelegate` classe dans le projet iOS :

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

L’exemple de code suivant montre comment initialiser un présenteur de connexion dans le `MainActivity` classe dans le projet Android :

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

Le projet de bibliothèque .NET Standard peut ensuite appeler le présentateur de connexion comme suit :

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Notez que l’argument pour le `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` méthode est la `OAuth2Authenticator` instance. Lorsque le `Login` méthode est appelée, l’interface utilisateur de connexion est présentée à l’utilisateur dans un onglet de navigateur web de l’appareil, ce qui est indiqué dans les captures d’écran suivante :

![](oauth-images/login.png "Infos d’identification Google")

### <a name="processing-the-redirect-url"></a>Traitement de l’URL de redirection

Une fois que l’utilisateur a terminé le processus d’authentification, contrôle retourne à l’application à partir de l’onglet du navigateur web. Pour cela, vous devez l’inscription d’un schéma d’URL personnalisé pour l’URL de redirection qui est retourné par le processus d’authentification, puis détecter et gérer l’URL personnalisée une fois qu’elle est envoyée.

Lorsque vous choisissez un schéma d’URL personnalisé à associer à une application, les applications doivent utiliser un schéma basé sur un nom sous leur contrôle. Cela est possible en utilisant le nom d’identificateur de bundle sur iOS et le nom du package sur Android et en inversant les pour rendre le schéma d’URL. Toutefois, certains fournisseurs d’identité, tel que Google, assignent des identificateurs de client basées sur les noms de domaine, qui sont ensuite inversés et utilisés en tant que le schéma d’URL. Par exemple, si Google crée un id de client de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, le schéma d’URL sera `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Notez que seul un seul `/` peut apparaître une fois le composant de schéma. Par conséquent, un exemple complet d’une URL de redirection utilisant un schéma d’URL personnalisé est `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Lorsque le navigateur web reçoit une réponse du fournisseur d’identité qui contient un schéma d’URL personnalisé, il essaie de charger l’URL, qui échoue. Au lieu de cela, le schéma d’URL personnalisé est signalé au système d’exploitation en déclenchant un événement. Il vérifie ensuite le système d’exploitation pour les schémas inscrits, et s’il existe, le système d’exploitation lancer l’application qui inscrit le schéma et envoyez-la à l’URL de redirection.

Le mécanisme d’inscription d’un schéma d’URL personnalisé avec le système d’exploitation et le schéma de manutention est spécifique à chaque plateforme.

#### <a name="ios"></a>iOS

Sur iOS, un schéma d’URL personnalisé est enregistré dans **Info.plist**, comme illustré dans la capture d’écran suivante :

![](oauth-images/info-plist.png "Enregistrement du modèle d’URL")

Le **identificateur** valeur peut être n’importe quoi et le **rôle** doit avoir la valeur **visionneuse**. Le **schémas d’Url** valeur, qui commence par `com.googleusercontent.apps`, peut être obtenu à partir de l’id de client iOS pour le projet sur [Console d’API Google](http://console.developers.google.com).

Le fournisseur d’identité issue de la demande d’autorisation, il redirige vers les URL de redirection de l’application. Étant donné que l’URL utilise un schéma personnalisé, il en résulte dans iOS lancement de l’application, en passant l’URL comme un paramètre de lancement, où elle est traitée par le `OpenUrl` la substitution de l’application `AppDelegate` (classe), ce qui est illustré dans l’exemple de code suivant :

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

Le `OpenUrl` méthode convertit l’URL reçu à partir d’un `NSUrl` à .NET `Uri`, avant le traitement de l’URL de redirection avec le `OnPageLoading` méthode publique `OAuth2Authenticator` objet. Cela entraîne Xamarin.Auth pour fermer l’onglet du navigateur web et pour analyser les données OAuth reçues.

#### <a name="android"></a>Android

Sur Android, un schéma d’URL personnalisé est inscrit en spécifiant un [ `IntentFilter` ](xref:Android.App.IntentFilterAttribute) d’attribut sur le `Activity` qui gérera le schéma. Le fournisseur d’identité issue de la demande d’autorisation, il redirige vers les URL de redirection de l’application. Comme l’URL utilise un schéma personnalisé, il en résulte dans Android lancement de l’application, en passant l’URL comme un paramètre de lancement, où elle est traitée par le `OnCreate` méthode de la `Activity` inscrits pour gérer le schéma d’URL personnalisé. L’exemple de code suivant montre la classe à partir de l’exemple d’application qui gère le schéma d’URL personnalisé :

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

Le `DataSchemes` propriété de la [ `IntentFilter` ](xref:Android.App.IntentFilterAttribute) doit être définie sur l’identificateur de client inversé est obtenue à partir de l’id de client Android pour le projet sur [Console d’API Google](http://console.developers.google.com).

Le `OnCreate` méthode convertit l’URL reçu à partir d’un `Android.Net.Url` à .NET `Uri`, avant le traitement de l’URL de redirection avec le `OnPageLoading` méthode publique `OAuth2Authenticator` objet. Cela entraîne Xamarin.Auth fermer l’onglet du navigateur web et analyser les données reçues de OAuth.

> [!IMPORTANT]
> Sur Android, Xamarin.Auth utilise le `CustomTabs` API pour communiquer avec le navigateur web et le système d’exploitation. Toutefois, il n'est pas garanti qu’un `CustomTabs` navigateur compatible sera installé sur l’appareil de l’utilisateur.

### <a name="examining-the-oauth-response"></a>Examen de la réponse OAuth

Après avoir analysé les données reçues de OAuth, Xamarin.Auth déclenchera le `OAuth2Authenticator.Completed` événement. Dans le Gestionnaire d’événements pour cet événement, le `AuthenticatorCompletedEventArgs.IsAuthenticated` propriété peut être utilisée pour identifier si l’authentification a réussi, comme indiqué dans l’exemple de code suivant :

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

Les données collectées à partir d’une authentification réussie seront disponibles dans le `AuthenticatorCompletedEventArgs.Account` propriété. Cela inclut un jeton d’accès, qui peut être utilisé pour signer des requêtes pour les données à une API fournie par le fournisseur d’identité.

### <a name="making-requests-for-data"></a>Effectuer des requêtes pour les données

Une fois que l’application obtient un jeton d’accès, il est utilisé pour effectuer une requête pour le `https://www.googleapis.com/oauth2/v2/userinfo` API, pour demander des données utilisateur de base du fournisseur d’identité. Cette demande est formulée avec du Xamarin.Auth `OAuth2Request` (classe), qui représente une demande qui est authentifiée à l’aide d’un compte récupéré à partir une `OAuth2Authenticator` de l’instance, comme indiqué dans l’exemple de code suivant :

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

Ainsi que la méthode HTTP et l’URL d’API, le `OAuth2Request` instance spécifie également une `Account` instance qui contient le jeton d’accès qui signe la demande vers l’URL spécifiée par le `Constants.UserInfoUrl` propriété. Le fournisseur d’identité retourne ensuite les données utilisateur de base comme une réponse JSON, y compris nom de l’utilisateur et l’adresse de messagerie, sous réserve qu’il identifie le jeton d’accès comme étant valide. La réponse JSON est ensuite lu et désérialisée dans le `user` variable.

Pour plus d’informations, consultez [appelant une API Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) sur le portail des développeurs de Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Stocker et récupérer des informations de compte sur les appareils

Xamarin.Auth stocke en toute sécurité `Account` stockent des objets dans un compte afin que les applications n’ont pas toujours pour ré-authentifier les utilisateurs. Le `AccountStore` classe est chargé de stocker les informations de compte et repose sur les services de trousseau dans iOS et le `KeyStore` classe dans Android.

Le code suivant montre l’exemple comment un `Account` objet est enregistré en toute sécurité :

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Comptes enregistrées sont identifiés de manière à l’aide d’une clé composée du compte `Username` propriété et un ID de service, qui est une chaîne qui est utilisée lors de la récupération des comptes à partir du magasin de comptes. Si un `Account` a été précédemment enregistré, appelant le `Save` méthode à nouveau remplace.

`Account` objets pour un service peut être récupéré en appelant le `FindAccountsForService` méthode, comme indiqué dans l’exemple de code suivant :

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

Le `FindAccountsForService` méthode retourne un `IEnumerable` collection de `Account` objets, avec le premier élément dans la collection qui est définie comme le compte de mise en correspondance.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment utiliser Xamarin.Auth pour gérer le processus d’authentification dans une application Xamarin.Forms. Xamarin.Auth fournit le `OAuth2Authenticator` et `OAuth2Request` classes qui sont utilisés par les applications Xamarin.Forms pour consommer des fournisseurs d’identité comme Google, Microsoft, Facebook et Twitter.


## <a name="related-links"></a>Liens associés

- [OAuthNativeFlow (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [OAuth 2.0 pour les applications natives](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [À l’aide d’OAuth 2.0 pour accéder aux API de Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
