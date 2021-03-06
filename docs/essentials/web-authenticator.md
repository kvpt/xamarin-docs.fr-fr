---
title: 'Xamarin.Essentials: Web Authenticator'
description: Ce document décrit la classe WebAuthenticator dans Xamarin.Essentials , qui vous permet de démarrer des flux d’authentification basés sur un navigateur qui écoutent un rappel pour l’application.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4ab7c5dab6a414e15531e0e0e812d604e05ab1cc
ms.sourcegitcommit: 3edcc63fcf86409b73cd6e5dc77f0093a99b3f87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98062600"
---
# <a name="no-locxamarinessentials-web-authenticator"></a>Xamarin.Essentials: Web Authenticator

La classe **WebAuthenticator** vous permet de lancer des flux basés sur un navigateur qui écoutent un rappel vers une URL spécifique inscrite auprès de l’application.

## <a name="overview"></a>Vue d’ensemble

De nombreuses applications nécessitent l’ajout de l’authentification utilisateur, ce qui signifie souvent que les utilisateurs peuvent se connecter à leurs comptes de connexion Microsoft, Facebook, Google et Now existants.

La [bibliothèque d’authentification Microsoft (MSAL)](/azure/active-directory/develop/msal-overview) fournit une excellente solution clé en main pour l’ajout de l’authentification à votre application. Il existe même une prise en charge des applications Xamarin dans leur package NuGet client.

Si vous êtes intéressé par l’utilisation de votre propre service Web pour l’authentification, il est possible d’utiliser **WebAuthenticator** pour implémenter la fonctionnalité côté client.

## <a name="why-use-a-server-back-end"></a>Pourquoi utiliser un back end de serveur ?

De nombreux fournisseurs d’authentification ont été déplacés pour offrir uniquement des flux d’authentification explicites ou à deux phases pour garantir une meilleure sécurité. Cela signifie que vous aurez besoin d’une _« clé secrète client »_ du fournisseur pour terminer le processus d’authentification. Malheureusement, les applications mobiles ne sont pas idéales pour stocker les secrets et tout ce qui est stocké dans le code d’une application mobile, les fichiers binaires ou, dans le cas contraire, est généralement considéré comme non sécurisé.

La meilleure pratique consiste à utiliser un serveur principal Web comme couche intermédiaire entre votre application mobile et le fournisseur d’authentification.

> [!IMPORTANT]
> Nous vous recommandons vivement d’utiliser des modèles et des bibliothèques d’authentification mobiles plus anciennes qui n’exploitent pas un backend Web dans le workflow d’authentification en raison de leur manque de sécurité inhérent au stockage des secrets des clients.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **WebAuthenticator** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

Android requiert un filtre d’intention pour gérer votre URI de rappel. Pour ce faire, il suffit de sous-classer la `WebAuthenticatorCallbackActivity` classe :

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```
Si la version d’Android cible de votre projet est définie sur **Android 11 (API R 30),** vous devez mettre à jour votre manifeste Android avec les requêtes utilisées avec les nouvelles [exigences de visibilité du package](https://developer.android.com/preview/privacy/package-visibility).

Ouvrez le fichier **AndroidManifest.xml** sous le dossier Propriétés, puis ajoutez ce qui suit dans le nœud manifeste :
```XML
<queries>
    <intent>
        <action android:name="android.support.customtabs.action.CustomTabsService" />
    </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

Sur iOS, vous devez ajouter le modèle d’URI de rappel de votre application à votre fichier info. plist, par exemple :

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLName</key>
        <string>xamarinessentials</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>xamarinessentials</string>
        </array>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
    </dict>
</array>
```

Vous devez également substituer les `AppDelegate` `OpenUrl` `ContinueUserActivity` méthodes et pour appeler Essentials :

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}

public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    if (Xamarin.Essentials.Platform.ContinueUserActivity(application, userActivity, completionHandler))
        return true;
    return base.ContinueUserActivity(application, userActivity, completionHandler);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Pour UWP, vous devez déclarer votre URI de rappel dans votre `Package.appxmanifest` fichier :

```xml
    <Extensions>
        <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
        </uap:Extension>
    </Extensions>
```

-----

## <a name="using-webauthenticator"></a>Utilisation de WebAuthenticator

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

L’API se compose principalement d’une méthode unique `AuthenticateAsync` qui accepte deux paramètres : l’URL qui doit être utilisée pour démarrer le déroulement du navigateur Web, et l’URI dont vous vous attendez à ce que le déroulement du traitement soit finalement rétabli vers et que votre application soit inscrite pour être gérée.

Le résultat est un `WebAuthenticatorResult` qui comprend tous les paramètres de requête analysés à partir de l’URI de rappel :

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

L' `WebAuthenticator` API prend en charge le lancement de l’URL dans le navigateur et l’attente jusqu’à ce que le rappel soit reçu :

![Workflow d’authentification Web classique](images/web-authenticator.png)

Si l’utilisateur annule le workflow à tout moment, une `TaskCanceledException` exception est levée.

## <a name="platform-differences"></a>Différences de plateforme

# <a name="android"></a>[Android](#tab/android)

Les onglets personnalisés sont utilisés chaque fois qu’ils sont disponibles. dans le cas contraire, une intention est démarrée pour l’URL.

# <a name="ios"></a>[iOS](#tab/ios)

Sur iOS 12 ou version ultérieure, `ASWebAuthenticationSession` est utilisé.  Sur iOS 11, `SFAuthenticationSession` est utilisé.  Sur les anciennes versions d’iOS, `SFSafariViewController` est utilisé s’il est disponible ; sinon, Safari est utilisé.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sur UWP, `WebAuthenticationBroker` est utilisé s’il est pris en charge ; sinon, le navigateur système est utilisé.

-----

## <a name="apple-sign-in"></a>Connexion Apple

Conformément aux [directives de révision d’Apple](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple), si votre application utilise un service de connexion sociale pour s’authentifier, elle doit également proposer la connexion Apple en tant qu’option.

Pour ajouter une connexion Apple à vos applications, vous devez d’abord [configurer votre application pour qu’elle utilise la connexion Apple](../ios/platform/ios13/sign-in.md).

Pour iOS 13 et versions ultérieures, vous souhaiterez peut-être appeler la `AppleSignInAuthenticator.AuthenticateAsync()` méthode. Cela utilisera les API de connexion Apple natives sous le capot afin que vos utilisateurs bénéficient de la meilleure expérience possible sur ces appareils. Vous pouvez écrire votre code partagé pour utiliser l’API appropriée au moment de l’exécution comme suit :

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator.AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var authToken = string.Empty;
if (r.Properties.TryGetValue("name", out var name) && !string.IsNullOrEmpty(name))
    authToken += $"Name: {name}{Environment.NewLine}";
if (r.Properties.TryGetValue("email", out var email) && !string.IsNullOrEmpty(email))
    authToken += $"Email: {email}{Environment.NewLine}";

// Note that Apple Sign In has an IdToken and not an AccessToken
authToken += r?.AccessToken ?? r?.IdToken;
```

> [!TIP]
> Pour les appareils non-iOS 13, cette opération démarre le processus d’authentification Web, qui peut également être utilisé pour activer la connexion Apple sur vos appareils Android et UWP.
> Vous pouvez vous connecter à votre compte iCloud sur votre simulateur iOS pour tester la connexion Apple.

-----

## <a name="aspnet-core-server-back-end"></a>Back end serveur ASP.NET Core

Il est possible d’utiliser l' `WebAuthenticator` API avec n’importe quel service de back end Web.  Pour l’utiliser avec une application ASP.NET Core, vous devez d’abord configurer l’application Web en procédant comme suit :

1. Configurez les [fournisseurs d’authentification sociale externes](/aspnet/core/security/authentication/social/?tabs=visual-studio) souhaités dans une application Web ASP.net core.
2. Définissez le schéma d’authentification par défaut sur `CookieAuthenticationDefaults.AuthenticationScheme` dans votre `.AddAuthentication()` appel.
3. Utilisez `.AddCookie()` dans votre `.AddAuthentication()` appel Startup.cs.
4. Tous les fournisseurs doivent être configurés avec `.SaveTokens = true;` .


```csharp
services.AddAuthentication(o =>
    {
        o.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddFacebook(fb =>
    {
        fb.AppId = Configuration["FacebookAppId"];
        fb.AppSecret = Configuration["FacebookAppSecret"];
        fb.SaveTokens = true;
    });
```

> [!TIP]
> Si vous souhaitez inclure la connexion Apple, vous pouvez utiliser le `AspNet.Security.OAuth.Apple` package NuGet.  Vous pouvez consulter l' [exemple complet Startup.cs](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) dans le référentiel GitHub Essentials.

### <a name="add-a-custom-mobile-auth-controller"></a>Ajouter un contrôleur d’authentification mobile personnalisé

Avec un workflow d’authentification mobile, il est généralement préférable d’initialiser le Flow directement à un fournisseur que l’utilisateur a choisi (par exemple, en cliquant sur un bouton Microsoft sur l’écran de connexion de l’application).  Il est également important de pouvoir retourner les informations pertinentes à votre application au niveau d’un URI de rappel spécifique pour mettre fin au processus d’authentification.

Pour ce faire, utilisez un contrôleur d’API personnalisé :

```csharp
[Route("mobileauth")]
[ApiController]
public class AuthController : ControllerBase
{
    const string callbackScheme = "myapp";

    [HttpGet("{scheme}")] // eg: Microsoft, Facebook, Apple, etc
    public async Task Get([FromRoute]string scheme)
    {
        // 1. Initiate authentication flow with the scheme (provider)
        // 2. When the provider calls back to this URL
        //    a. Parse out the result
        //    b. Build the app callback URL
        //    c. Redirect back to the app
    }
}
```

L’objectif de ce contrôleur est de déduire le schéma (fournisseur) demandé par l’application et de lancer le processus d’authentification auprès du fournisseur de réseaux sociaux. Lorsque le fournisseur rappelle le serveur principal Web, le contrôleur analyse le résultat et redirige vers l’URI de rappel de l’application avec des paramètres.

Parfois, vous pouvez retourner des données telles que le fournisseur `access_token` à l’application que vous pouvez effectuer via les paramètres de requête de l’URI de rappel. Vous pouvez également créer votre propre identité sur votre serveur et transmettre votre propre jeton à l’application. C’est à vous de savoir comment faire cette partie !

Consultez l' [exemple de contrôleur complet](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) dans le référentiel Essentials.

> [!NOTE]
> L’exemple ci-dessus montre comment retourner le jeton d’accès à partir du fournisseur d’authentification tierce (IE : OAuth). Pour obtenir un jeton que vous pouvez utiliser pour autoriser des requêtes Web sur le backend Web, vous devez créer votre propre jeton dans votre application Web et le retourner à la place.  La [vue d’ensemble de l’authentification ASP.net Core](/aspnet/core/security/authentication) contient plus d’informations sur les scénarios d’authentification avancés dans ASP.net core.

-----
## <a name="api"></a>API

- [Code source WebAuthenticator](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/WebAuthenticator)
- [Documentation de l’API WebAuthenticator](xref:Xamarin.Essentials.WebAuthenticator)
- [Exemple de serveur ASP.NET Core](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
