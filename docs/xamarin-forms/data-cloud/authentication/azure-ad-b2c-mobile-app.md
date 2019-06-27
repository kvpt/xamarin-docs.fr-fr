---
title: L’intégration d’Azure Active Directory B2C dans Azure Mobile Apps
description: Azure B2C Active de répertoire est une solution de gestion des identités de cloud pour applications mobiles et web destinées aux consommateurs. Cet article montre comment utiliser Azure Active Directory B2C pour fournir l’authentification et l’autorisation à une instance d’Azure Mobile Apps avec Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2019
ms.openlocfilehash: 2f9cfceee4765dea946dfdac974ac2d56595ef94
ms.sourcegitcommit: 9aaae4f0af096cd136b3dcfbb9af591ba307dc25
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67398695"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>L’intégration d’Azure Active Directory B2C dans Azure Mobile Apps

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure B2C Active de répertoire fournit la gestion des identités de cloud pour les applications grand public. Cet article explique comment utiliser Azure Active Directory B2C pour intégrer la gestion des identités dans une instance d’Azure Mobile Apps avec Xamarin.Forms._

## <a name="overview"></a>Vue d'ensemble

Azure Mobile Apps permettent de développer des applications avec un serveur principal évolutif, hébergée dans Azure App Service. Il prend en charge l’authentification d’appareils mobiles, la synchronisation hors connexion et push notifications. Pour plus d’informations sur Azure Mobile Apps, consultez [consommer une application Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md), et [authentification des utilisateurs avec Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure B2C Active de répertoire est un service de gestion d’identité pour les applications destinées aux consommateurs, ce qui permet aux consommateurs de :

- Se connecter avec leurs comptes sociaux existants (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Créer de nouvelles informations d’identification (adresse de messagerie et mot de passe, ou nom d’utilisateur et mot de passe). Ces informations d’identification sont appelées *local* comptes.

Pour plus d’informations sur Azure Active Directory B2C, consultez [authentification des utilisateurs avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure B2C Active de répertoire peut servir à gérer le flux de travail de l’authentification pour une application Mobile Azure. Cette approche configure la gestion des identités dans le cloud et permet des modifications sans modifier le code d’application.


Azure Mobile Apps avec Azure Active Directory B2C permet de deux méthodes d’authentification :

- [Client géré](#client-managed-authentication) – l’application mobile Xamarin.Forms commence le processus d’authentification par le locataire Azure Active Directory B2C et transmet le jeton d’authentification reçu à l’instance d’Azure Mobile Apps.
- [Géré par le serveur](#server-managed-authentication) – l’instance Azure Mobile Apps utilise le locataire Azure Active Directory B2C pour démarrer le processus d’authentification via un workflow basé sur le web.

Dans les deux cas, l’expérience d’authentification est fournie par le locataire Azure Active Directory B2C. Dans l’exemple d’application, l’écran de connexion doit ressembler les captures d’écran suivante :

![](azure-ad-b2c-mobile-app-images/screenshots.png "Page de connexion")

Cet exemple montre la connexion à un compte local, mais vous pouvez également activer Microsoft, Google, Facebook et autres fournisseurs d’identité.

## <a name="setup"></a>Installation

Dans les deux flux de travail, le processus initial pour l’intégration d’un locataire Azure Active Directory B2C avec Azure Mobile Apps est la suivante :

1. Créer une instance d’Azure Mobile Apps dans le portail Azure. Cela génère un projet de démarrage similaire à l’exemple de projet. Pour plus d’informations, consultez [consommer une application Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Créer un locataire Azure Active Directory B2C. Pour plus d’informations, consultez [authentification des utilisateurs avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).
1. Activer l’authentification dans l’instance d’Azure Mobile Apps et de l’application Xamarin.Forms. Pour plus d’informations, consultez [authentification des utilisateurs avec Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

> [!NOTE]
> La bibliothèque d’authentification Microsoft (MSAL) est requis lors de l’utilisation d’un flux de travail de l’authentification de client géré. MSAL utilise le navigateur web de l’appareil pour s’authentifier. Une fois que l’authentification est effectuée dans le navigateur, l’utilisateur est redirigé vers un schéma d’URL personnalisé. L’application enregistre le schéma d’URL personnalisé, ce qui lui permet de reprendre le contrôle de l’expérience utilisateur et de répondre à l’authentification. Pour plus d’informations sur l’utilisation de MSAL pour communiquer avec un locataire Azure Active Directory B2C, consultez [authentification des utilisateurs avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

## <a name="client-managed-authentication"></a>Authentification de client géré

Dans l’authentification de client géré, une application Xamarin.Forms contacte un locataire Azure Active Directory B2C pour démarrer le flux d’authentification. Après l’authentification, le locataire Azure Active Directory B2C retourne un jeton d’identité, qui est fourni à l’instance d’Azure Mobile Apps. Cela permet à l’application Xamarin.Forms effectuer des actions qui nécessitent des autorisations de l’utilisateur authentifié.

### <a name="azure-active-directory-b2c-client-managed-tenant-configuration"></a>Configuration de locataire gérée par le client Azure Active Directory B2C

Pour un flux de travail géré par le client de l’authentification, le locataire Azure Active Directory B2C doit être configuré comme suit :

- Définissez **inclure un client natif** sur « Oui ».
- Définir l’URI de redirection personnalisé. La documentation de MSAL recommande à l’aide de « msal » associé à votre ID d’application et suivie de «  :/ / auth / ». Pour plus d’informations, consultez [URI de redirection des applications clientes MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#redirect-uri).

La capture d’écran suivante illustre cette configuration :

[![« Configuration d’azure B2C Active de répertoire »](azure-ad-b2c-mobile-app-images/azure-redirect-uri-cropped.png)](azure-ad-b2c-mobile-app-images/azure-redirect-uri.png#lightbox "configuration d’Azure Active Directory B2C")

La stratégie de connexion utilisée dans le locataire Azure Active Directory B2C doit également être configurée afin que l’URL de réponse est définie pour le même schéma d’URL personnalisé. Cela est possible en sélectionnant **exécuter le flux de l’utilisateur** dans le portail Azure pour accéder aux paramètres de stratégie. Enregistrez l’URL de métadonnées trouvée sur cet écran, car vous en aurez besoin pour la configuration de l’application Mobile. La capture d’écran suivante illustre cette configuration et l’URL que vous devez copier :

[![« Azure Active Directory B2C stratégies »](azure-ad-b2c-mobile-app-images/client-flow-policies-cropped.png)](azure-ad-b2c-mobile-app-images/client-flow-policies.png#lightbox "configuration de la stratégie Azure B2C Active de répertoire")

### <a name="azure-mobile-app-configuration"></a>Configuration d’application Mobile Azure

Pour un workflow d’authentification géré par le client, configurez l’instance Azure Mobile Apps comme suit :

- L’authentification App Service doit être activée.
- L’action à exécuter quand une demande n’est pas authentifiée doit être définie sur **vous connecter avec Azure Active Directory**.

La capture d’écran suivante illustre cette configuration :

[![« Configuration de l’authentification d’azure Mobile Apps »](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configuration de l’authentification d’Azure Mobile Apps")

Configurer l’instance d’Azure Mobile Apps pour communiquer avec le locataire Azure Active Directory B2C :

- Cliquez sur la configuration d’Azure Active Directory et activer **avancé** mode pour le fournisseur d’authentification Azure Active Directory.
- Définissez **ID Client** à la **ID d’Application** du locataire Azure Active Directory B2C.
- Définir le **Url de l’émetteur** à l’URL des métadonnées de la stratégie que vous avez copié précédemment lors de la configuration du client.

La capture d’écran suivante illustre cette configuration :

![« Azure Mobile Apps avancée configuration »](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Connexion

L’exemple de code suivant montre les appels de méthode clé pour le démarrage d’un workflow d’authentification client géré :

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...

    authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        string.Empty,
        UIBehavior.SelectAccount,
        string.Empty,
        App.UiParent);

    ...

    var payload = new JObject();
    if (authenticationResult != null && !string.IsNullOrWhiteSpace(authenticationResult.AccessToken))
    {
        payload["access_token"] = authenticationResult.AccessToken;
    }

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    success = true;

    ...
}
```

La bibliothèque d’authentification Microsoft (MSAL) est utilisé pour démarrer un flux de travail de l’authentification avec le locataire Azure Active Directory B2C. Le `AcquireTokenAsync` méthode lance le navigateur web de l’appareil et affiche les options d’authentification définies dans la stratégie d’Azure Active Directory B2C est spécifiée par la stratégie référencée par le biais du `Constants.AuthoritySignin` constante. Cette stratégie définit l’expérience de connexion et de l’inscription de l’utilisateur et les revendications de que l’application reçoit après une authentification réussie.

Le résultat de la `AcquireTokenAsync` appel de méthode est un `AuthenticationResult` instance. Si l’authentification réussit, le `AuthenticationResult` instance contiendra un jeton d’accès, qui sera mis en cache localement. Si l’authentification échoue, le `AuthenticationResult` instance contiendra les données qui indique pourquoi l’authentification a échoué. Pour plus d’informations sur comment utiliser MSAL pour communiquer avec un locataire Azure Active Directory B2C, consultez [authentification des utilisateurs avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Lorsque le `CurrentClient.LoginAsync` méthode est appelée, l’instance d’Azure Mobile Apps reçoit le jeton d’accès encapsulé dans un `JObject`. La présence d’un moyen de jeton valide, l’instance d’Azure Mobile Apps n’a pas besoin pour démarrer son propre flux d’authentification OAuth 2.0. Au lieu de cela, le `CurrentClient.LoginAsync` méthode retourne un `MobileServiceUser` instance est stockée dans le `MobileServiceClient.CurrentUser` propriété. Cette propriété fournit `UserId` et `MobileServiceAuthenticationToken` propriétés. Ces éléments représentent l’utilisateur authentifié et le jeton, qui peut être utilisé jusqu'à son expiration. Le jeton d’authentification sera inclus dans toutes les demandes effectuées à l’instance d’Azure Mobile Apps, permettant à l’application Xamarin.Forms effectuer des actions qui nécessitent des autorisations de l’utilisateur authentifié.

### <a name="signing-out"></a>Déconnexion

L’exemple de code suivant montre comment le processus de déconnexion géré par le client est appelé :

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    IEnumerable<IAccount> accounts = await ADB2CClient.GetAccountsAsync();
    while (accounts.Any())
    {
        await ADB2CClient.RemoveAsync(accounts.First());
        accounts = await ADB2CClient.GetAccountsAsync();
    }
    User = null;

    ...
}
```

Le `CurrentClient.LogoutAsync` méthode authentifie l’utilisateur avec l’instance d’Azure Mobile Apps à retirer, et ensuite tous les jetons d’authentification sont effacés à partir du cache local créé par MSAL.

## <a name="server-managed-authentication"></a>Authentification de serveur géré

Dans l’authentification de serveur géré, une application Xamarin.Forms contacte une instance d’Azure Mobile Apps, qui utilise le locataire Azure Active Directory B2C pour gérer le flux d’authentification OAuth 2.0 en affichant une page de connexion, tel que défini dans la stratégie B2C. Après authentification réussie, l’instance d’Azure Mobile Apps retourne un jeton qui permet à l’application Xamarin.Forms effectuer des actions qui nécessitent des autorisations de l’utilisateur authentifié.

### <a name="azure-active-directory-b2c-server-managed-tenant-configuration"></a>Configuration de locataire gérée par le serveur Azure Active Directory B2C

Pour un flux de travail géré par le serveur de l’authentification, le locataire Azure Active Directory B2C doit être configuré comme suit :

- Incluent une API web app/web et autoriser le flux implicite.
- Définir l’URL de réponse à l’adresse de l’application Mobile Azure, suivie de `/.auth/login/aad/callback`.

La capture d’écran suivante illustre cette configuration :

[![« Configuration d’azure B2C Active de répertoire »](azure-ad-b2c-mobile-app-images/server-flow-config-cropped.png)](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configuration d’Azure Active Directory B2C")

La stratégie utilisée dans le locataire Azure Active Directory B2C doit avoir configuré l’URL de réponse. Cela s’effectue en définissant l’URL de réponse à l’adresse de votre application Azure Mobile App suivie `/.auth/login/aad/callback`. Vous devez également enregistrer l’URL des métadonnées trouvé en haut de cet écran, car vous en aurez besoin pour la configuration de l’application Mobile. La capture d’écran suivante illustre cette configuration et vous devez enregistrer l’URL des métadonnées :

![« Configuration de la stratégie azure B2C Active de répertoire »](azure-ad-b2c-mobile-app-images/server-flow-policies.png)

### <a name="azure-mobile-apps-instance-configuration"></a>Configuration de l’instance Azure Mobile Apps

Pour un flux de travail géré par le serveur de l’authentification, l’instance d’Azure Mobile Apps doit être configuré comme suit :

- L’authentification App Service doit être activée.
- L’action à exécuter quand une demande n’est pas authentifiée doit être définie sur **vous connecter avec Azure Active Directory**.

La capture d’écran suivante illustre cette configuration :

[![« Configuration de l’authentification d’azure Mobile Apps »](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configuration de l’authentification d’Azure Mobile Apps")

L’instance d’Azure Mobile Apps doit également être configuré pour communiquer avec le locataire Azure Active Directory B2C :

- Cliquez sur la configuration d’Azure Active Directory et activer **avancé** mode pour le fournisseur d’authentification Azure Active Directory.
- Définissez **ID Client** à la **ID d’Application** du locataire Azure Active Directory B2C.
- Le **Url de l’émetteur** est l’URL des métadonnées de la stratégie que vous avez copié précédemment lors de la configuration du client.

La capture d’écran suivante illustre cette configuration :

![« Azure Mobile Apps avancée configuration »](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Connexion

L’exemple de code suivant montre comment démarrer un workflow d’authentification géré par le serveur :

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...

    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);

    ...
}
```

Lorsque le `CurrentClient.LoginAsync` méthode est appelée, l’instance d’Azure Mobile Apps exécute la stratégie Azure Active Directory B2C liée, ce qui démarre le flux d’authentification OAuth 2.0. Chaque `AuthenticateAsync` méthode est spécifique à la plateforme. Toutefois, chaque `AuthenticateAsync` les appels de méthode le `CurrentClient.LoginAsync` (méthode) et spécifie qu’un client Azure Active Directory sera utilisé dans le processus d’authentification. Pour plus d’informations, consultez [connexion des utilisateurs](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

Le `CurrentClient.LoginAsync` méthode retourne un `MobileServiceUser` instance est stockée dans le `CurrentClient.CurrentUser` propriété. Cette propriété fournit `UserId` et `MobileServiceAuthenticationToken` propriétés. Ces éléments représentent l’utilisateur authentifié et un jeton d’authentification pour l’utilisateur, ce qui peut être utilisé jusqu'à son expiration. Le jeton d’authentification sera inclus dans toutes les demandes effectuées à l’instance d’Azure Mobile Apps, permettant à l’application Xamarin.Forms effectuer des actions sur l’instance d’Azure Mobile Apps qui nécessitent des autorisations de l’utilisateur authentifié.

### <a name="signing-out"></a>Déconnexion

L’exemple de code suivant montre comment le processus de déconnexion géré par le serveur est appelé :

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    ...
}
```

Le `MobileServiceClient.LogoutAsync` méthode authentifie l’utilisateur avec l’instance d’Azure Mobile Apps à retirer. Pour plus d’informations, consultez [journalisation Out utilisateurs](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).


## <a name="related-links"></a>Liens connexes

- [TodoAzureAuth ClientFlow (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [TodoAzureAuth ServerFlow (exemple)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [Utilisation d’une application Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Authentification des utilisateurs avec Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Authentification des utilisateurs avec Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Package nuget bibliothèque d’authentification Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentation de la bibliothèque d’authentification Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
