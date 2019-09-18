---
title: Utiliser la connexion avec Apple pour Xamarin. Forms
description: Découvrez comment implémenter la connexion avec Apple dans vos applications mobiles Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: c852a80136c8aae3a4da0fd3d8a8fa259e08aa4d
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021166"
---
# <a name="use-sign-in-with-apple-in-xamarinforms"></a>Utiliser la connexion avec Apple dans Xamarin. Forms

![Cette API est actuellement en version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/Redth/Xamarin.AppleSignIn.Sample)

Connectez-vous avec Apple pour toutes les nouvelles applications sur iOS 13 qui utilisent des services d’authentification tiers. Les détails de l’implémentation entre iOS et Android sont très différents. Ce guide explique comment procéder aujourd’hui dans Xamarin. Forms.

Dans ce guide et exemples, des services de plateforme spécifiques sont utilisés pour gérer la connexion avec Apple :

- Android utilisant un service Web générique parlant à Azure Functions avec OpenID/OpenAuth
- iOS utilise l’API native pour l’authentification sur iOS 13 et revient à un service Web générique pour iOS 12 et versions antérieures

## <a name="a-sample-apple-sign-in-flow"></a>Exemple de workflow de connexion Apple

Cet exemple propose une implémentation de consignes strictes pour que la connexion Apple fonctionne dans votre application Xamarin. Forms.

Nous utilisons deux Azure Functions pour faciliter le déroulement de l’authentification :

1. `applesignin_auth`-Génère l’URL d’autorisation de connexion Apple et y redirige.  Nous faisons cela côté serveur, au lieu de l’application mobile, afin de mettre en cache le `state` et de le valider lorsque les serveurs d’Apple envoient un rappel.
2. `applesignin_callback`-Gère le rappel après Apple et échange en toute sécurité le code d’autorisation pour un jeton d’accès et un jeton d’ID.  Enfin, elle redirige vers le schéma d’URI de l’application, en passant les jetons dans un fragment d’URL.

L’application mobile s’inscrit elle-même pour gérer le modèle d’URI personnalisé que nous avons sélectionné `xamarinformsapplesignin://`(dans ce `applesignin_callback` cas), la fonction peut relayer les jetons vers celle-ci.

Lorsque l’utilisateur démarre l’authentification, les étapes suivantes se produisent :

1. L’application mobile génère une `nonce` valeur `state` et la transmet à la `applesignin_auth` fonction Azure.
2. La `applesignin_auth` fonction Azure génère une URL d’autorisation de connexion Apple (à l' `state` aide `nonce`des et des) et redirige le navigateur de l’application mobile vers celle-ci.
3. L’utilisateur entre ses informations d’identification en toute sécurité dans la page d’autorisation de connexion Apple hébergée sur les serveurs d’Apple.
4. Une fois le processus de connexion Apple terminé sur les serveurs Apple, Apple effectue une redirection `redirect_uri` vers la qui est `applesignin_callback` la fonction Azure.
5. La demande d’Apple envoyée à la `applesignin_callback` fonction est validée pour garantir que `state` le correct est retourné et que les revendications de jeton d’ID sont valides.
6. La `applesignin_callback` fonction Azure échange le `code` qui lui est envoyé par Apple, pour un jeton d' _accès_, un _jeton d’actualisation_et un _jeton d’ID_ (qui contient des revendications sur l’ID d’utilisateur, le nom et l’adresse de messagerie).
7. La `applesignin_callback` fonction Azure redirige enfin vers le schéma d’URI de l’application (`xamarinformsapplesignin://`) en ajoutant un fragment d’URI avec les `xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...`jetons (par exemple,).
8. L’application mobile analyse le fragment d’URI dans un `AppleAccount` et valide la `nonce` revendication reçue correspond à la `nonce` générée au début du Workflow.
9. L’application mobile est désormais authentifiée.

## <a name="azure-functions"></a>Azure Functions

Cet exemple utilise Azure Functions. Un contrôleur de ASP.NET Core ou une solution de serveur Web similaire peut également offrir la même fonctionnalité.

### <a name="configuration"></a>Configuration

Plusieurs paramètres d’application doivent être configurés lors de l’utilisation de Azure Functions :

- `APPLE_SIGNIN_KEY_ID`-C’est votre `KeyId` version antérieure.
- `APPLE_SIGNIN_TEAM_ID`-Il s’agit généralement de l' _ID d’équipe_ trouvé dans votre [profil d’appartenance](https://developer.apple.com/account/#/membership)
- `APPLE_SIGNIN_SERVER_ID`: Il s’agit `ServerId` de l’ancienne version.  Il ne s’agit *pas* de votre _ID d’ensemble_d’applications, mais plutôt de l' *identificateur* de l’ID de *services* que vous avez créé.
- `APPLE_SIGNIN_APP_CALLBACK_URI`: Il s’agit du modèle d’URI personnalisé que vous souhaitez rediriger vers votre application avec.  Dans cet exemple `xamarinformsapplesignin://` , est utilisé.
- `APPLE_SIGNIN_REDIRECT_URI`: *URL de redirection* que vous configurez lors de la création de votre *ID de services* dans la section de configuration *de la connexion Apple* .  Pour effectuer un test, il peut ressembler à ceci :`http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY`-Le contenu textuel de votre `.p8` fichier, avec `\n` toutes les nouvelles lignes supprimées. il s’agit donc d’une chaîne longue.

### <a name="security-considerations"></a>Considérations relatives à la sécurité

**Ne** stockez jamais votre clé P8 à l’intérieur du code de votre application. Le code d’application est facile à télécharger et à désassembler. 

Il est également considéré comme une mauvaise pratique d’utiliser `WebView` un pour héberger le workflow d’authentification et d’intercepter les événements de navigation d’URL pour obtenir le code d’autorisation. À l’heure actuelle, il n’existe actuellement aucune méthode entièrement sécurisée pour gérer la connexion avec Apple sur des appareils non iOS13s sans héberger du code sur un serveur pour gérer l’échange de jetons. Nous vous recommandons d’héberger le code de génération de l’URL d’autorisation sur un serveur afin de pouvoir mettre en cache l’État et le valider quand Apple émet un rappel de publication sur votre serveur.

## <a name="a-cross-platform-sign-in-service"></a>Service de connexion multiplateforme

À l’aide de Xamarin. Forms DependencyService, vous pouvez créer des services d’authentification distincts qui utilisent les services de plateforme sur iOS et un service Web générique pour Android et d’autres plateformes non-iOS basées sur une interface partagée.

```csharp
public interface IAppleSignInService
{
    bool Callback(string url);

    Task<AppleAccount> SignInAsync();
}
```

Sur iOS, les API natives sont utilisées :

```csharp
public class AppleSignInServiceiOS : IAppleSignInService
{
#if __IOS__13
    AuthManager authManager;
#endif

    bool Is13 => UIDevice.CurrentDevice.CheckSystemVersion(13, 0);
    WebAppleSignInService webSignInService;

    public AppleSignInServiceiOS()
    {
        if (!Is13)
            webSignInService = new WebAppleSignInService();
    }

    public async Task<AppleAccount> SignInAsync()
    {
        // Fallback to web for older iOS versions
        if (!Is13)
            return await webSignInService.SignInAsync();

        AppleAccount appleAccount = default;

#if __IOS__13
        var provider = new ASAuthorizationAppleIdProvider();
        var req = provider.CreateRequest();

        authManager = new AuthManager(UIApplication.SharedApplication.KeyWindow);

        req.RequestedScopes = new[] { ASAuthorizationScope.FullName, ASAuthorizationScope.Email };
        var controller = new ASAuthorizationController(new[] { req });

        controller.Delegate = authManager;
        controller.PresentationContextProvider = authManager;

        controller.PerformRequests();

        var creds = await authManager.Credentials;

        if (creds == null)
            return null;

        appleAccount = new AppleAccount();
        appleAccount.IdToken = JwtToken.Decode(new NSString(creds.IdentityToken, NSStringEncoding.UTF8).ToString());
        appleAccount.Email = creds.Email;
        appleAccount.UserId = creds.User;
        appleAccount.Name = NSPersonNameComponentsFormatter.GetLocalizedString(creds.FullName, NSPersonNameComponentsFormatterStyle.Default, NSPersonNameComponentsFormatterOptions.Phonetic);
        appleAccount.RealUserStatus = creds.RealUserStatus.ToString();
#endif

        return appleAccount;
    }

    public bool Callback(string url) => true;
}

#if __IOS__13
class AuthManager : NSObject, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding
{
    public Task<ASAuthorizationAppleIdCredential> Credentials
        => tcsCredential?.Task;

    TaskCompletionSource<ASAuthorizationAppleIdCredential> tcsCredential;

    UIWindow presentingAnchor;

    public AuthManager(UIWindow presentingWindow)
    {
        tcsCredential = new TaskCompletionSource<ASAuthorizationAppleIdCredential>();
        presentingAnchor = presentingWindow;
    }

    public UIWindow GetPresentationAnchor(ASAuthorizationController controller)
        => presentingAnchor;

    [Export("authorizationController:didCompleteWithAuthorization:")]
    public void DidComplete(ASAuthorizationController controller, ASAuthorization authorization)
    {
        var creds = authorization.GetCredential<ASAuthorizationAppleIdCredential>();
        tcsCredential?.TrySetResult(creds);
    }

    [Export("authorizationController:didCompleteWithError:")]
    public void DidComplete(ASAuthorizationController controller, NSError error)
        => tcsCredential?.TrySetException(new Exception(error.LocalizedDescription));
}
#endif
```

L’indicateur `__IOS__13` de compilation est utilisé pour assurer la prise en charge d’iOS 13, ainsi que des versions héritées qui permettent de revenir au service Web générique.

Sur Android, le service Web générique avec Azure Functions est utilisé :

```csharp
public class WebAppleSignInService : IAppleSignInService
{
    // IMPORTANT: This is what you register each native platform's url handler to be
    public const string CallbackUriScheme = "xamarinformsapplesignin";
    public const string InitialAuthUrl = "http://local.test:7071/api/applesignin_auth";

    string currentState;
    string currentNonce;

    TaskCompletionSource<AppleAccount> tcsAccount = null;

    public bool Callback(string url)
    {
        // Only handle the url with our callback uri scheme
        if (!url.StartsWith(CallbackUriScheme + "://"))
            return false;

        // Ensure we have a task waiting
        if (tcsAccount != null && !tcsAccount.Task.IsCompleted)
        {
            try
            {
                // Parse the account from the url the app opened with
                var account = AppleAccount.FromUrl(url);

                // IMPORTANT: Validate the nonce returned is the same as our originating request!!
                if (!account.IdToken.Nonce.Equals(currentNonce))
                    tcsAccount.TrySetException(new InvalidOperationException("Invalid or non-matching nonce returned"));

                // Set our account result
                tcsAccount.TrySetResult(account);
            }
            catch (Exception ex)
            {
                tcsAccount.TrySetException(ex);
            }
        }

        tcsAccount.TrySetResult(null);
        return false;
    }

    public async Task<AppleAccount> SignInAsync()
    {
        tcsAccount = new TaskCompletionSource<AppleAccount>();

        // Generate state and nonce which the server will use to initial the auth
        // with Apple.  The nonce should flow all the way back to us when our function
        // redirects to our app
        currentState = Util.GenerateState();
        currentNonce = Util.GenerateNonce();

        // Start the auth request on our function (which will redirect to apple)
        // inside a browser (either SFSafariViewController, Chrome Custom Tabs, or native browser)
        await Xamarin.Essentials.Browser.OpenAsync($"{InitialAuthUrl}?&state={currentState}&nonce={currentNonce}",
            Xamarin.Essentials.BrowserLaunchMode.SystemPreferred);

        return await tcsAccount.Task;
    }
}
```

## <a name="summary"></a>Récapitulatif

Cet article décrit les étapes nécessaires à la configuration de la connexion avec Apple pour une utilisation dans vos applications Xamarin. Forms.

## <a name="related-links"></a>Liens connexes

- [XamarinFormsAppleSignIn (exemple)](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [Se connecter avec les instructions Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
