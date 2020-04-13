---
title: 'Xamarin.Essentials: Électronique'
description: Ce document décrit la classe WebAuthenticator dans Xamarin.Essentials, qui vous permet de démarrer les flux d’authentification basés sur le navigateur qui écoutent un rappel à l’application.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
ms.openlocfilehash: 82c136e1de7d2aa7f2d7f132b8ee1639ee44aba4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80638158"
---
# <a name="xamarinessentials-web-authenticator"></a>Xamarin.Essentials: Électronique

La classe **WebAuthenticator** vous permet d’initier des flux basés sur le navigateur qui écoutent un rappel à une URL spécifique enregistrée à l’application.

## <a name="overview"></a>Vue d’ensemble

De nombreuses applications nécessitent l’ajout d’authentification utilisateur, ce qui signifie souvent permettre à vos utilisateurs de se connecter à leurs comptes Microsoft, Facebook, Google et maintenant Apple Sign In.

[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) fournit une excellente solution clé en main pour ajouter de l’authentification à votre application. Il y a même un support pour les applications Xamarin dans leur forfait NuGet client.

Si vous êtes intéressé à utiliser votre propre service Web pour l’authentification, il est possible d’utiliser **WebAuthenticator** pour implémenter la fonctionnalité côté client.

## <a name="why-use-a-server-back-end"></a>Pourquoi utiliser un back-end serveur ?

De nombreux fournisseurs d’authentification se sont déplacés pour offrir uniquement des flux d’authentification explicites ou à deux pattes pour assurer une meilleure sécurité. Cela signifie que vous aurez besoin d’un _«secret client»_ du fournisseur pour compléter le flux d’authentification. Malheureusement, les applications mobiles ne sont pas un endroit idéal pour stocker des secrets et tout ce qui est stocké dans le code d’une application mobile, binaires, ou autrement est généralement considéré comme peu sûr.

La meilleure pratique ici est d’utiliser un backend web comme couche moyenne entre votre application mobile et le fournisseur d’authentification.

> [!IMPORTANT]
> Nous recommandons fortement de ne pas utiliser d’anciennes bibliothèques d’authentification mobile uniquement et des modèles qui ne tirent pas parti d’un backend web dans le flux d’authentification en raison de leur manque inhérent de sécurité pour stocker les secrets des clients.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **WebAuthenticator,** la configuration spécifique de la plate-forme suivante est nécessaire.

# <a name="android"></a>[Android](#tab/android)

Android nécessite une configuration de filtre d’intention pour gérer votre rappel URI. Ceci est facilement accompli en `WebAuthenticatorCallbackActivity` sous-classant la classe :

> [!NOTE]
> Vous devriez envisager la mise en œuvre de [liens App Android](https://developer.android.com/training/app-links/) pour gérer le rappel URI et s’assurer que votre application est la seule qui peut s’inscrire pour gérer le rappel URI.

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

Vous devrez également rappeler à Essentials `OnResume` de la `MainActivity`dérogation dans votre :

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

Sur iOS, vous devrez ajouter le modèle URI de rappel de votre application à votre Liste Info.p.

> [!NOTE]
> Vous devriez envisager d’utiliser [Universal App Links](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content) pour enregistrer l’URI de rappel de votre application comme une pratique exemplaire.

Vous devrez également passer `AppDelegate`outre `OpenUrl` à votre méthode d’appel dans Essentials:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Pour UWP, vous devrez déclarer votre rappel URI dans votre `Package.appxmanifest` fichier :

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

L’API se compose `AuthenticateAsync` principalement d’une méthode unique qui prend deux paramètres: L’URL qui doit être utilisée pour démarrer le flux du navigateur Web, et l’Uri que vous attendez du flux à finalement rappeler et que votre application est enregistrée pour être en mesure de gérer.

Le résultat `WebAuthenticatorResult` est un qui comprend tous les paramètres de requête analysés à partir de l’URI de rappel:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

L’API `WebAuthenticator` s’occupe de lancer l’URL dans le navigateur et d’attendre que le rappel soit reçu :

![Flux typique d’authentification Web](images/web-authenticator.png)

Si l’utilisateur annule le flux `null` à tout moment, un résultat est retourné.

## <a name="platform-differences"></a>Différences de plate-forme

# <a name="android"></a>[Android](#tab/android)

Les onglets personnalisés sont utilisés chaque fois qu’ils sont disponibles, sinon une intention est commencée pour l’URL.

# <a name="ios"></a>[iOS](#tab/ios)

Sur iOS 12 `ASWebAuthenticationSession` ou plus, est utilisé.  Sur iOS 11, `SFAuthenticationSession` est utilisé.  Sur les anciennes versions iOS, `SFSafariViewController` est utilisé si disponible, sinon Safari est utilisé.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sur UWP, `WebAuthenticationBroker` le est utilisé si pris en charge, sinon le navigateur système est utilisé.

-----

## <a name="apple-sign-in"></a>Apple Sign In

Selon les [directives d’examen d’Apple](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple), si votre application utilise un service de connexion sociale pour authentifier, il doit également offrir Apple Sign In comme une option.

Pour ajouter Apple Sign In à vos applications, vous devrez d’abord [configurer votre application pour utiliser Apple Sign In](https://docs.microsoft.com/xamarin/ios/platform/ios13/sign-in).

Pour iOS 13 et plus, vous `AppleSignInAuthenticator.AuthenticateAsync()` voudrez appeler la méthode. Cela utilisera le signe Apple natif dans API sous le capot afin que vos utilisateurs obtiennent la meilleure expérience possible sur ces appareils. Vous pouvez écrire votre code partagé pour utiliser l’API droit au moment de l’exécution comme ceci :

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var accessToken = r?.AccessToken;
```

> [!TIP]
> Pour les appareils non-iOS 13, cela démarrera le flux d’authentification web, qui peut également être utilisé pour activer Apple Sign In sur vos appareils Android et UWP.

-----

## <a name="aspnet-core-server-back-end"></a>ASP.NET’arrière du serveur de base

Il est possible d’utiliser l’API `WebAuthenticator` avec n’importe quel service web back end.  Pour l’utiliser avec une application de base ASP.NET, vous devez d’abord configurer l’application web avec les étapes suivantes :

1. Configurez vos [fournisseurs externes d’authentification sociale souhaités](https://docs.microsoft.com/aspnet/core/security/authentication/social/?view=aspnetcore-3.1&tabs=visual-studio) dans une application web ASP.NET Core.
2. Définissez le système `CookieAuthenticationDefaults.AuthenticationScheme` d’authentification par défaut dans votre `.AddAuthentication()` appel.
3. Utilisez-le `.AddCookies()` dans `.AddAuthentication()` votre Startup.cs appel.
4. Tous les fournisseurs doivent `.SaveTokens = true;`être configurés avec .

> [!TIP]
> Si vous souhaitez inclure Apple Sign In, `AspNet.Security.OAuth.Apple` vous pouvez utiliser le forfait NuGet.  Vous pouvez consulter [l’échantillon complet Startup.cs](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) dans le référentiel Essentials GitHub.

### <a name="add-a-custom-mobile-auth-controller"></a>Ajouter un contrôleur auth mobile personnalisé

Avec un flux d’authentification mobile, il est généralement souhaitable d’initier le flux directement à un fournisseur que l’utilisateur a choisi (par exemple en cliquant sur un bouton "Microsoft" sur le signe à l’écran de l’application).  Il est également important de pouvoir retourner des informations pertinentes à votre application lors d’un rappel spécifique URI pour mettre fin au flux d’authentification.

Pour ce faire, utilisez un contrôleur API personnalisé :

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

Le but de ce contrôleur est d’inférer le régime (fournisseur) que l’application demande, et d’initier le flux d’authentification avec le fournisseur social. Lorsque le fournisseur rappelle le backend web, le contrôleur analyse le résultat et redirige vers l’URI de rappel de l’application avec des paramètres.

Parfois, vous pouvez retourner des données `access_token` telles que le fournisseur de retour à l’application que vous pouvez faire via les paramètres de requête de l’URI de rappel. Ou, vous pouvez plutôt créer votre propre identité sur votre serveur et transmettre votre propre jeton à l’application. Quoi et comment vous faites cette partie est à vous!

Consultez [l’échantillon complet](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) du contrôleur dans le référentiel Essentials.

-----
## <a name="api"></a>API

- [Code source WebAuthenticator](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/WebAuthenticator)
- [Documentation de l’API de WebAuthenticator](xref:Xamarin.Essentials.WebAuthenticator)
- [ASP.NET’échantillon de serveur de base](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
