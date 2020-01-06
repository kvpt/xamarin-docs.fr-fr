---
title: Authentifier les utilisateurs avec Azure Active Directory B2C
description: Azure Active Directory B2C fournit la gestion des identités dans le Cloud pour les applications mobiles et Web destinées aux consommateurs. Cet article explique comment utiliser Azure Active Directory B2C pour intégrer la gestion des identités dans une application mobile à l’aide de la bibliothèque d’authentification Microsoft.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 946cf65f7d83722fd388bed555b9d3f35c487708
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489815"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Authentifier les utilisateurs avec Azure Active Directory B2C

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_Azure Active Directory B2C fournit la gestion des identités dans le Cloud pour les applications mobiles et Web destinées aux consommateurs. Cet article explique comment utiliser Azure Active Directory B2C pour intégrer la gestion des identités dans une application mobile à l’aide de la bibliothèque d’authentification Microsoft._

## <a name="overview"></a>Vue d'ensemble de

Azure Active Directory B2C (ADB2C) est un service de gestion des identités pour les applications accessibles aux consommateurs. Il permet aux utilisateurs de se connecter à votre application à l’aide de leurs comptes sociaux existants ou d’informations d’identification personnalisées telles que l’e-mail, le nom d’utilisateur et le mot de passe. Les comptes d’identification personnalisés sont appelés comptes _locaux_ .

Le processus pour intégrer le service de gestion d’identité Azure Active Directory B2C dans une application mobile est comme suit :

1. Créer un locataire Azure Active Directory B2C.
1. Inscrire votre application mobile avec le locataire Azure Active Directory B2C.
1. Créer des stratégies pour l’inscription et la connexion, ainsi que des flux utilisateur avec mot de passe oublié.
1. Utilisez la bibliothèque d’authentification Microsoft (MSAL) pour démarrer un flux de travail d’authentification avec votre locataire Azure Active Directory B2C.

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

Azure Active Directory B2C prend en charge plusieurs fournisseurs d’identité, notamment Microsoft, GitHub, Facebook, Twitter et bien plus encore. Pour plus d’informations sur les fonctionnalités de Azure Active Directory B2C, consultez [Azure Active Directory B2C documentation](/azure/active-directory-b2c/).

La bibliothèque d’authentification Microsoft prend en charge plusieurs architectures d’application et plateformes. Pour plus d’informations sur les fonctionnalités de MSAL, consultez [bibliothèque d’authentification Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) sur GitHub.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurer un locataire Azure Active Directory B2C

Pour exécuter l’exemple de projet, vous devez créer un locataire Azure Active Directory B2C. Pour plus d’informations, consultez [créer un locataire Azure Active Directory B2C dans le portail Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Une fois que vous avez créé un locataire, vous aurez besoin du **nom du locataire** et de l' **ID de locataire** pour configurer l’application mobile. L’ID et le nom du locataire sont définis par le domaine généré lors de la création de l’URL de votre locataire. Si votre URL de locataire générée est `https://contoso20190410tenant.onmicrosoft.com/` l' **ID de locataire** est `contoso20190410tenant.onmicrosoft.com` et le nom du **locataire** est `contoso20190410tenant`. Recherchez le domaine du locataire dans le Portail Azure en cliquant sur le **filtre de répertoire et d’abonnement** dans le menu supérieur. La capture d’écran suivante montre le bouton de filtre d’annuaire et d’abonnement Azure et le domaine du locataire :

[![nom du locataire dans l’affichage du filtre d’abonnement et de l’annuaire Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

Dans l’exemple de projet, modifiez le fichier **constants.cs** pour définir les champs `tenantName` et `tenantId`. Le code suivant montre comment ces valeurs doivent être définies si votre domaine de locataire est `https://contoso20190410tenant.onmicrosoft.com/`, remplacez ces valeurs par les valeurs de votre portail :

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Inscrire votre application mobile avec Azure Active Directory B2C

Une application mobile doit être inscrite auprès du locataire pour pouvoir se connecter et authentifier les utilisateurs. Le processus d’inscription affecte un **ID d’application** unique à l’application, et une **URL de redirection** qui redirige les réponses vers l’application après l’authentification. Pour plus d’informations, consultez [Azure Active Directory B2C : inscription de votre application](/azure/active-directory-b2c/active-directory-b2c-app-registration/). Vous devez connaître l’ID d' **application** affecté à votre application, qui est indiqué après le nom de l’application dans la vue Propriétés. La capture d’écran suivante indique où trouver l’ID d’application :

[![ID d’application dans l’affichage des propriétés de l’application Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

La bibliothèque d’authentification Microsoft s’attend à ce que l' **URL de redirection** de votre application soit votre **ID d’application** préfixé avec le texte « MSAL », suivi d’un point de terminaison appelé « auth ». Si votre ID d’application est « 1234abcd », l’URL complète doit être `msal1234abcd://auth`. Assurez-vous que votre application a activé le paramètre **Native Client** et créez un **URI de redirection personnalisé** à l’aide de votre ID d’application, comme indiqué dans la capture d’écran suivante :

![URI de redirection personnalisée dans l’affichage des propriétés de l’application Azure](azure-ad-b2c-images/azure-redirect-uri.png)

L’URL sera utilisée ultérieurement dans Android **fichier ApplicationManifest. xml** et iOS **info. plist**.

Dans l’exemple de projet, modifiez le fichier **constants.cs** pour définir le champ `clientId` sur votre **ID d’application**. Le code suivant montre comment cette valeur doit être définie si votre ID d’application est `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Créer des stratégies d’inscription et de connexion, ainsi que des stratégies de mot de passe oubliées

Une stratégie est une expérience que les utilisateurs passent pour effectuer une tâche telle que la création d’un compte ou la réinitialisation d’un mot de passe. Une stratégie spécifie également le contenu des jetons que l’application reçoit lorsque l’utilisateur retourne de l’expérience. Vous devez configurer des stratégies pour l’inscription et la connexion des comptes, et réinitialiser le mot de passe. Azure propose des stratégies intégrées qui simplifient la création de stratégies courantes. Pour plus d’informations, consultez [Azure Active Directory B2C : stratégies intégrées](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Une fois que vous avez terminé la configuration de la stratégie, vous devez disposer de deux stratégies dans la vue flux de l' **utilisateur (stratégies)** dans le portail Azure. La capture d’écran suivante montre deux stratégies configurées dans l’Portail Azure :

![Deux stratégies configurées dans la vue des flux d’utilisateurs Azure (stratégies)](azure-ad-b2c-images/azure-application-policies.png)

Dans l’exemple de projet, modifiez le fichier **constants.cs** pour définir les champs `policySignin` et `policyPassword` afin de refléter les noms que vous avez choisis lors de la configuration de la stratégie :

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>Utiliser la bibliothèque d’authentification Microsoft (MSAL) pour l’authentification

Le package NuGet de la bibliothèque d’authentification Microsoft (MSAL) doit être ajouté au projet partagé, .NET Standard et aux projets de plateforme dans une solution Xamarin. Forms. MSAL comprend une classe `PublicClientApplicationBuilder` qui construit un objet adhérant à l’interface `IPublicClientApplication`. MSAL utilise des clauses de `With` pour fournir des paramètres supplémentaires au constructeur et aux méthodes d’authentification.

Dans l’exemple de projet, le code-behind pour **app. Xaml** définit des propriétés statiques nommées `AuthenticationClient` et `UIParent`, et instancie l’objet `AuthenticationClient` dans le constructeur. La clause `WithIosKeychainSecurityGroup` fournit un nom de groupe de sécurité pour les applications iOS. La clause `WithB2CAuthority` fournit l' **autorité**par défaut, ou stratégie, qui sera utilisée pour authentifier les utilisateurs. La clause `WithRedirectUri` indique à l’instance Azure Notification Hubs l’URI de redirection à utiliser si plusieurs URI sont spécifiés. L’exemple suivant montre comment instancier le `PublicClientApplication`:

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .WithRedirectUri($"msal{Constants.ClientId}://auth")
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

> [!NOTE]
> Si votre instance Azure Notification Hubs n’a qu’un seul URI de redirection défini, l’instance `AuthenticationClient` peut fonctionner sans spécifier l’URI de redirection avec la clause `WithRedirectUri`. Toutefois, vous devez toujours spécifier cette valeur au cas où votre configuration Azure se développe pour prendre en charge d’autres clients ou méthodes d’authentification.

Le gestionnaire d’événements `OnAppearing` dans le code-behind **LoginPage.Xaml.cs** appelle `AcquireTokenSilentAsync` pour actualiser le jeton d’authentification pour les utilisateurs qui se sont connectés auparavant. Le processus d’authentification effectue une redirection vers le `LogoutPage` en cas de réussite et ne fait rien en cas d’échec. L’exemple suivant montre le processus de réauthentification en mode silencieux dans `OnAppearing`:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

Le gestionnaire d’événements `OnLoginButtonClicked` (déclenché lorsque l’utilisateur clique sur le bouton de connexion) appelle `AcquireTokenAsync`. La bibliothèque MSAL ouvre automatiquement le navigateur de l’appareil mobile et accède à la page de connexion. L’URL de connexion, appelée **autorité**, est une combinaison du nom du client et des stratégies définies dans le fichier **constants.cs** . Si l’utilisateur choisit l’option mot de passe oublié, il est renvoyé à l’application avec une exception, ce qui lance l’expérience oubliée de mot de passe. L’exemple suivant illustre le processus d’authentification :

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

La méthode `OnForgotPassword` est semblable au processus de connexion, mais elle implémente une stratégie personnalisée. `OnForgotPassword` utilise une surcharge différente de `AcquireTokenAsync`, qui vous permet de fournir une **autorité**spécifique. L’exemple suivant montre comment fournir une **autorité** personnalisée lors de l’acquisition d’un jeton :

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

Le dernier élément d’authentification est le processus de déconnexion. La méthode `OnLogoutButtonClicked` est appelée quand l’utilisateur appuie sur le bouton de déconnexion. Il parcourt tous les comptes et s’assure que leurs jetons ont été invalidés. L’exemple ci-dessous illustre l’implémentation de la déconnexion :

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

Sur iOS, le modèle d’URL personnalisé qui a été enregistré avec Azure Active Directory B2C doit être inscrit dans **info. plist**. MSAL s’attend à ce que le schéma d’URL adhère à un modèle spécifique, décrit précédemment dans [inscrire votre application mobile avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). La capture d’écran suivante montre le schéma d’URL personnalisé dans **info. plist**.

![« Inscription d’un schéma d’URL personnalisé sur iOS »](azure-ad-b2c-images/customurl-ios.png)

MSAL requiert également des habilitations de trousseau sur iOS, inscrites dans **Entitilements. plist**, comme indiqué dans la capture d’écran suivante :

![« Définition des droits d’application sur iOS »](azure-ad-b2c-images/entitlements-ios.png)

Azure Active Directory B2C issue de la demande d’autorisation, il redirige vers l’URL de redirection inscrits. Le schéma d’URL personnalisé entraîne le lancement par iOS de l’application mobile et la transmission de l’URL en tant que paramètre de lancement, où elle est traitée par le `OpenUrl` remplacement de la classe `AppDelegate` de l’application, et retourne le contrôle de l’expérience à MSAL. L’implémentation de `OpenUrl` est présentée dans l’exemple de code suivant :

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

Sur Android, le schéma d’URL personnalisé qui a été enregistré avec Azure Active Directory B2C doit être inscrit dans **fichier AndroidManifest. xml**. MSAL s’attend à ce que le schéma d’URL adhère à un modèle spécifique, décrit précédemment dans [inscrire votre application mobile avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). L’exemple suivant montre le schéma d’URL personnalisé dans **fichier AndroidManifest. xml**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- example -->
        <!-- <data android:scheme="msalaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" android:host="auth" /> -->
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

La classe `MainActivity` doit être modifiée pour fournir l’objet `UIParent` à l’application pendant l’appel de `OnCreate`. Lorsque Azure Active Directory B2C termine la demande d’autorisation, il effectue une redirection vers le schéma d’URL enregistré à partir du **fichier fichier AndroidManifest. xml**. Le schéma d’URI inscrit entraîne l’appel par Android de la méthode `OnActivityResult` avec l’URL en tant que paramètre Launch, où elle est traitée par la méthode `SetAuthenticationContinuationEventArgs`.

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Aucune installation supplémentaire n’est requise pour utiliser MSAL sur le plateforme Windows universelle

## <a name="run-the-project"></a>Exécuter le projet

Exécutez l’application sur un appareil virtuel ou physique. Appuyez sur le bouton de **connexion** pour ouvrir le navigateur et accéder à une page dans laquelle vous pouvez vous connecter ou créer un compte. Une fois le processus de connexion terminé, vous devez revenir à la page de déconnexion de l’application. La capture d’écran suivante montre l’écran de connexion de l’utilisateur s’exécutant sur Android et iOS :

![« Écran de connexion Azure ADB2C sur Android et iOS »](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Liens associés

- [AzureADB2CAuth (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Bibliothèque d’authentification Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentation de la bibliothèque d’authentification Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
