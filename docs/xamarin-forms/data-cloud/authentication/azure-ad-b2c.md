---
title: Authentifier les utilisateurs avec Azure Active Directory B2C
description: Azure B2C Active de répertoire assure la gestion des identités de cloud pour applications mobiles et web destinées aux consommateurs. Cet article explique comment utiliser Azure Active Directory B2C pour intégrer la gestion des identités dans une application mobile avec la bibliothèque d’authentification de Microsoft.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: 06f5716c8decb21de39fd46abe734b5fdcd6bd43
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67417949"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Authentifier les utilisateurs avec Azure Active Directory B2C

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure B2C Active de répertoire assure la gestion des identités de cloud pour applications mobiles et web destinées aux consommateurs. Cet article explique comment utiliser Azure Active Directory B2C pour intégrer la gestion des identités dans une application mobile avec la bibliothèque d’authentification de Microsoft._

## <a name="overview"></a>Vue d'ensemble

Azure B2C Active de répertoire (ADB2C) est un service de gestion d’identité pour les applications grand public. Il permet aux utilisateurs de se connecter à votre application à l’aide de leurs comptes sociaux existants ou les informations d’identification personnalisées telles que l’e-mail ou nom d’utilisateur et mot de passe. Informations d’identification personnalisées comptes sont appelés _local_ comptes.

Le processus pour intégrer le service de gestion d’identité Azure Active Directory B2C dans une application mobile est comme suit :

1. Créer un locataire Azure Active Directory B2C
1. Inscrire votre application mobile avec le locataire Azure Active Directory B2C
1. Créer des stratégies pour l’inscription et la connexion et vous avez oublié le mot de passe utilisateur flux
1. Utilisez la bibliothèque d’authentification Microsoft (MSAL) pour démarrer un flux de travail de l’authentification auprès de votre client Azure Active Directory B2C.

> [!NOTE]
> Azure B2C Active de répertoire prend en charge plusieurs fournisseurs d’identité, y compris Microsoft, GitHub, Facebook, Twitter et bien plus encore. Pour plus d’informations sur les fonctionnalités d’Azure Active Directory B2C, consultez [Documentation Azure Active Directory B2C](/azure/active-directory-b2c/).
>
> Bibliothèque d’authentification Microsoft prend en charge plusieurs plateformes et architectures d’application. Pour plus d’informations sur les fonctionnalités de la bibliothèque MSAL, consultez [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) sur GitHub.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurer un client Azure Active Directory B2C

Pour exécuter l’exemple de projet, vous devez créer un locataire Azure Active Directory B2C. Pour plus d’informations, consultez [créer un locataire Azure Active Directory B2C dans le portail Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Une fois que vous créez un client, vous devez le **nom_client** et **id_locataire** pour configurer l’application mobile. Le nom et ID de client sont définies par le domaine généré lorsque vous avez créé votre URL de locataire. Si votre URL de locataire générée est `https://contoso20190410tenant.onmicrosoft.com/` le **id_locataire** est `contoso20190410tenant.onmicrosoft.com` et **nom_client** est `contoso20190410tenant`. Rechercher le domaine du locataire dans le portail Azure en cliquant sur le **filtre directory et abonnement** dans le menu supérieur. La capture d’écran suivante montre l’annuaire Azure et bouton de filtre d’abonnement et le domaine du client :

[![Nom du locataire dans la vue de filtre de répertoire et d’abonnement Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

Dans l’exemple de projet, modifiez le **Constants.cs** fichier pour définir le `tenantName` et `tenantId` champs. Le code suivant montre comment ces valeurs doivent être définies si votre domaine du locataire est `https://contoso20190410tenant.onmicrosoft.com/`, remplacez ces valeurs avec les valeurs de votre portail :

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Inscrire votre application mobile avec Azure Active Directory B2C

Une application mobile doit être inscrite auprès du client avant de pouvoir vous connecter et authentifier les utilisateurs. Le processus d’inscription assigne une valeur unique **ID d’Application** à l’application et un **l’URL de redirection** qui dirige les réponses vers l’application après l’authentification. Pour plus d’informations, consultez [Azure Active Directory B2C : Inscrire votre application](/azure/active-directory-b2c/active-directory-b2c-app-registration/). Vous devez connaître le **ID d’Application** affecté à votre application, qui se trouve après le nom de l’application dans l’affichage des propriétés. La capture d’écran suivante montre où trouver l’ID d’Application :

[![ID d’application dans l’affichage des propriétés application Windows Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft Authentication Library s’attend le **l’URL de redirection** pour votre application à être votre **ID d’Application** préfixé avec le texte « msal » et suivie d’un point de terminaison appelé « auth ». Si votre ID d’Application est « 1234abcd », l’URL complète doit être `msal1234abcd://auth`. Assurez-vous que votre application a activé le **Native client** définition et créer un **URI de redirection personnalisé** à l’aide de votre ID d’Application comme illustré dans la capture d’écran suivante :

![URI de redirection personnalisé dans l’affichage des propriétés application Windows Azure](azure-ad-b2c-images/azure-redirect-uri.png)

L’URL sera utilisé ultérieurement dans les deux Android **ApplicationManifest.xml** et iOS **Info.plist**.

Dans l’exemple de projet, modifiez le **Constants.cs** fichier pour définir le `clientId` champ votre **ID d’Application**. Le code suivant montre comment cette valeur doit être définie si votre ID d’Application est `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Créer des stratégies d’inscription et connexion et vous avez oublié de stratégies de mot de passe

Une stratégie est une expérience utilisateurs passent par pour terminer une tâche telle que la création d’un compte ou réinitialiser un mot de passe. Une stratégie spécifie également le contenu des jetons que l’application reçoit lorsque l’utilisateur revient à partir de l’expérience. Vous devez définir des stratégies pour les deux comptes d’abonnement et la connexion et réinitialiser le mot de passe. Azure propose des stratégies intégrées qui simplifient la création de stratégies courantes. Pour plus d’informations, consultez [Azure Active Directory B2C : Les stratégies intégrées](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Lorsque vous avez terminé la configuration d’une stratégie, vous devez disposer des deux stratégies dans le **flux d’utilisateurs (stratégies)** vue dans le portail Azure. La capture d’écran suivante montre deux stratégies configurées dans le portail Azure :

![Afficher les deux stratégies configurées dans les flux d’utilisateurs de Azure (stratégies)](azure-ad-b2c-images/azure-application-policies.png)

Dans l’exemple de projet, modifiez le **Constants.cs** fichier pour définir le `policySignin` et `policyPassword` champs pour refléter les noms que vous avez choisi lors de l’installation de la stratégie :

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

Le package NuGet de bibliothèque d’authentification Microsoft (MSAL) doit être ajouté à la partagé projet .NET Standard et les projets de plateforme dans une solution Xamarin.Forms. MSAL inclut un `PublicClientApplicationBuilder` classe qui construit un objet adhérant à la `IPublicClientApplication` interface. MSAL utilise `With` clauses pour fournir des paramètres supplémentaires pour les méthodes de constructeur et l’authentification.

Dans l’exemple de projet, le code-behind pour **App.xaml** définit les propriétés statiques nommées `AuthenticationClient` et `UIParent`et instancie les `AuthenticationClient` objet dans le constructeur. Le `WithIosKeychainSecurityGroup` clause fournit un nom de groupe de sécurité pour les applications iOS. Le `WithB2CAuthority` clause fournit la valeur par défaut **autorité**, ou de la stratégie, qui sera utilisé pour authentifier les utilisateurs. L’exemple suivant montre comment instancier le `PublicClientApplication`:

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
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

Le `OnAppearing` Gestionnaire d’événements dans le **LoginPage.xaml.cs** code-behind d’appels `AcquireTokenSilentAsync` pour actualiser le jeton d’authentification pour les utilisateurs qui se sont connectés avant. Le processus d’authentification redirige vers le `LogoutPage` en cas de réussite et ne fait rien en cas d’échec. L’exemple suivant montre le processus de la réauthentification en mode silencieux dans `OnAppearing`:

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

Le `OnLoginButtonClicked` Gestionnaire d’événements (déclenché en cas de clic sur le bouton de connexion) appels `AcquireTokenAsync`. La bibliothèque MSAL automatiquement ouvre le navigateur de périphérique mobile et navigue vers la page de connexion. L’URL de connexion, appelé un **autorité**, est une combinaison du nom du client et les stratégies définies dans le **Constants.cs** fichier. Si l’utilisateur choisit l’oublié l’option de mot de passe, ils sont retournés à l’application avec une exception, ce qui lance l’oublié l’expérience de mot de passe. L’exemple suivant montre le processus d’authentification :

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

Le `OnForgotPassword` méthode est similaire au processus d’ouverture de session, mais implémente une stratégie personnalisée. `OnForgotPassword` utilise une autre surcharge de `AcquireTokenAsync`, qui vous permet de fournir un spécifique **autorité**. L’exemple suivant montre comment fournir un personnalisé **autorité** lors de l’acquisition d’un jeton :

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

La dernière étape d’authentification est le processus de déconnexion. Le `OnLogoutButtonClicked` méthode est appelée lorsque l’utilisateur appuie sur le bouton de déconnexion. Il effectue une itération sur tous les comptes et garantit que leurs jetons ont été invalidées. L’exemple ci-dessous illustre l’implémentation de déconnexion :

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

Sur iOS, le schéma d’URL personnalisé qui a été inscrit avec Azure Active Directory B2C doit être inscrite dans **Info.plist**. MSAL attend le schéma d’URL pour adhérer à un modèle spécifique, décrit précédemment dans [inscrire votre application mobile avec Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). La capture d’écran suivante montre le schéma d’URL personnalisé dans **Info.plist**.

![« L’inscription d’un schéma d’URL personnalisé sur iOS »](azure-ad-b2c-images/customurl-ios.png)

MSAL requiert également des droits du trousseau sur iOS, enregistré dans le **Entitilements.plist**, comme illustré dans la capture d’écran suivante :

![« Définition des droits de l’application sur iOS »](azure-ad-b2c-images/entitlements-ios.png)

Azure Active Directory B2C issue de la demande d’autorisation, il redirige vers l’URL de redirection inscrits. Le schéma d’URL personnalisé entraîne iOS lancement de l’application mobile et en passant l’URL comme un paramètre de lancement, où elle est traitée par le `OpenUrl` la substitution de l’application `AppDelegate` classe et retourne le contrôle de l’expérience à la bibliothèque MSAL. Le `OpenUrl` implémentation est illustrée dans l’exemple de code suivant :

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

Sur Android, le schéma d’URL personnalisé qui a été inscrit avec Azure Active Directory B2C doit être inscrite dans le **AndroidManifest.xml**. MSAL attend le schéma d’URL pour adhérer à un modèle spécifique, décrit précédemment dans [inscrire votre application mobile avec Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). L’exemple suivant montre le schéma d’URL personnalisé dans le **AndroidManifest.xml**.

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
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />"
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

Le `MainActivity` classe doit être modifiée pour fournir le `UIParent` objet à l’application pendant le `OnCreate` appeler. Azure Active Directory B2C issue de la demande d’autorisation, il redirige vers le schéma d’URL inscrit à partir de la **AndroidManifest.xml**. Le schéma d’URI des résultats dans Android qui appelle le `OnActivityResult` méthode avec l’URL comme un paramètre de lancement, où elle est traitée par le `SetAuthenticationContinuationEventArgs` (méthode).

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

Aucune configuration supplémentaire n’est nécessaire pour utiliser la bibliothèque MSAL sur la plateforme Windows universelle

## <a name="run-the-project"></a>Exécuter le projet

Exécutez l’application sur un appareil virtuel ou physique. En appuyant sur la **connexion** bouton doit ouvrir le navigateur et accédez à une page où vous pouvez vous connecter ou créer un compte. Après avoir terminé le processus de connexion, vous devriez être redirigé vers la page de déconnexion de l’application. La capture d’écran suivante affiche l’utilisateur d’authentification dans l’écran en cours d’exécution sur iOS et Android :

![« Connexion ADB2C azure écran sur Android et iOS »](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Liens associés

- [AzureADB2CAuth (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Bibliothèque d’authentification Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentation de la bibliothèque d’authentification Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
