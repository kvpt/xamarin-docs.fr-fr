---
title: Se connecter avec Apple dans Xamarin. iOS
description: La connexion avec Apple fournit la protection d’identité lors de l’utilisation de services d’authentification tiers.
ms.prod: xamarin
ms.assetid: CDA59BBF-AAE1-4D4F-B4AE-DB37220D41EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: 5cbe3f36d1aeb12be671b14a4f76c79764e814e6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375003"
---
# <a name="sign-in-with-apple-in-xamarinios"></a>Se connecter avec Apple dans Xamarin. iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/ios-samples/ios13-addingthesigninwithappleflowtoyourapp/)

La connexion avec Apple est un nouveau service qui assure la protection des identités des utilisateurs de services d’authentification tiers. À compter d’iOS 13, Apple exige que toute nouvelle application utilisant des services d’authentification tiers fournisse également la connexion avec Apple. Les applications existantes mises à jour n’ont pas besoin d’ajouter une connexion avec Apple jusqu’au 2020 avril.

Ce document explique comment vous pouvez ajouter la connexion avec Apple aux applications iOS 13.

## <a name="apple-developer-setup"></a>Installation d’Apple Developer

Avant de générer et d’exécuter une application à l’aide de la connexion avec Apple, vous devez effectuer ces étapes. Sur les [certificats de développement Apple, identificateurs &][5] portail des profils :

1. Créez un nouvel identificateur d' **ID d’application** .
2. Définissez une description dans le champ **Description** .
3. Choisissez un ID de Bundle **explicite** et définissez `com.xamarin.AddingTheSignInWithAppleFlowToYourApp` dans le champ.
4. Activez la **connexion avec** la fonctionnalité Apple et inscrivez la nouvelle identité.
5. Créez un profil de provisionnement avec la nouvelle identité.
6. Téléchargez-le et installez-le sur votre appareil.
7. Dans Visual Studio, activez la fonctionnalité **se connecter avec Apple** dans le fichier **Entitlements. plist** .

## <a name="check-sign-in-status"></a>Vérifier l’état de la connexion

Lorsque votre application démarre, ou lorsque vous devez d’abord vérifier l’état d’authentification d’un utilisateur, instanciez un `ASAuthorizationAppleIdProvider` et vérifiez l’état actuel :

```csharp
var appleIdProvider = new ASAuthorizationAppleIdProvider ();
appleIdProvider.GetCredentialState (KeychainItem.CurrentUserIdentifier, (credentialState, error) => {
    switch (credentialState) {
    case ASAuthorizationAppleIdProviderCredentialState.Authorized:
        // The Apple ID credential is valid.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.Revoked:
        // The Apple ID credential is revoked.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.NotFound:
        // No credential was found, so show the sign-in UI.
        InvokeOnMainThread (() => {
            var storyboard = UIStoryboard.FromName ("Main", null);

            if (!(storyboard.InstantiateViewController (nameof (LoginViewController)) is LoginViewController viewController))
                return;

            viewController.ModalPresentationStyle = UIModalPresentationStyle.FormSheet;
            viewController.ModalInPresentation = true;
            Window?.RootViewController?.PresentViewController (viewController, true, null);
        });
        break;
    }
});
```

Dans ce code, appelé pendant `FinishedLaunching` dans le `AppDelegate.cs` , l’application gère quand un État est `NotFound` et présente `LoginViewController` à l’utilisateur. Si l’État avait retourné `Authorized` ou `Revoked` , une autre action peut être présentée à l’utilisateur.

## <a name="a-loginviewcontroller-for-sign-in-with-apple"></a>LoginViewController pour la connexion avec Apple

Le `UIViewController` qui implémente la logique de connexion et offre une connexion avec Apple doit implémenter `IASAuthorizationControllerDelegate` et `IASAuthorizationControllerPresentationContextProviding` comme dans l' `LoginViewController` exemple ci-dessous.

```csharp
public partial class LoginViewController : UIViewController, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding {
    public LoginViewController (IntPtr handle) : base (handle)
    {
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.

        SetupProviderLoginView ();
    }

    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        PerformExistingAccountSetupFlows ();
    }

    void SetupProviderLoginView ()
    {
        var authorizationButton = new ASAuthorizationAppleIdButton (ASAuthorizationAppleIdButtonType.Default, ASAuthorizationAppleIdButtonStyle.White);
        authorizationButton.TouchUpInside += HandleAuthorizationAppleIDButtonPress;
        loginProviderStackView.AddArrangedSubview (authorizationButton);
    }

    // Prompts the user if an existing iCloud Keychain credential or Apple ID credential is found.
    void PerformExistingAccountSetupFlows ()
    {
        // Prepare requests for both Apple ID and password providers.
        ASAuthorizationRequest [] requests = {
            new ASAuthorizationAppleIdProvider ().CreateRequest (),
            new ASAuthorizationPasswordProvider ().CreateRequest ()
        };

        // Create an authorization controller with the given requests.
        var authorizationController = new ASAuthorizationController (requests);
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }

    private void HandleAuthorizationAppleIDButtonPress (object sender, EventArgs e)
    {
        var appleIdProvider = new ASAuthorizationAppleIdProvider ();
        var request = appleIdProvider.CreateRequest ();
        request.RequestedScopes = new [] { ASAuthorizationScope.Email, ASAuthorizationScope.FullName };

        var authorizationController = new ASAuthorizationController (new [] { request });
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }
}
```

![Animation de l’exemple d’application à l’aide de la connexion avec Apple](sign-in-images/sign-in-flow.png)

Cet exemple de code vérifie l’état de la connexion actuelle dans `PerformExistingAccountSetupFlows` et se connecte à la vue actuelle en tant que délégué. Si une information d’identification de trousseau iCloud existante ou des informations d’identification Apple ID est détectée, l’utilisateur est invité à l’utiliser.

Apple fournit `ASAuthorizationAppleIdButton` , un bouton spécifiquement à cet effet. Lorsqu’il est touché, le bouton déclenche le workflow géré dans la méthode `HandleAuthorizationAppleIDButtonPress` .

## <a name="handling-authorization"></a>Gestion de l’autorisation

Dans, `IASAuthorizationController` implémentez une logique personnalisée pour stocker le compte de l’utilisateur. L’exemple ci-dessous stocke le compte de l’utilisateur dans le trousseau, le service de stockage de l’Apple.

```csharp
#region IASAuthorizationController Delegate

[Export ("authorizationController:didCompleteWithAuthorization:")]
public void DidComplete (ASAuthorizationController controller, ASAuthorization authorization)
{
    if (authorization.GetCredential<ASAuthorizationAppleIdCredential> () is ASAuthorizationAppleIdCredential appleIdCredential) {
        var userIdentifier = appleIdCredential.User;
        var fullName = appleIdCredential.FullName;
        var email = appleIdCredential.Email;

        // Create an account in your system.
        // For the purpose of this demo app, store the userIdentifier in the keychain.
        try {
            new KeychainItem ("com.example.apple-samplecode.juice", "userIdentifier").SaveItem (userIdentifier);
        } catch (Exception) {
            Console.WriteLine ("Unable to save userIdentifier to keychain.");
        }

        // For the purpose of this demo app, show the Apple ID credential information in the ResultViewController.
        if (!(PresentingViewController is ResultViewController viewController))
            return;

        InvokeOnMainThread (() => {
            viewController.UserIdentifierText = userIdentifier;
            viewController.GivenNameText = fullName?.GivenName ?? "";
            viewController.FamilyNameText = fullName?.FamilyName ?? "";
            viewController.EmailText = email ?? "";

            DismissViewController (true, null);
        });
    } else if (authorization.GetCredential<ASPasswordCredential> () is ASPasswordCredential passwordCredential) {
        // Sign in using an existing iCloud Keychain credential.
        var username = passwordCredential.User;
        var password = passwordCredential.Password;

        // For the purpose of this demo app, show the password credential as an alert.
        InvokeOnMainThread (() => {
            var message = $"The app has received your selected credential from the keychain. \n\n Username: {username}\n Password: {password}";
            var alertController = UIAlertController.Create ("Keychain Credential Received", message, UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("Dismiss", UIAlertActionStyle.Cancel, null));

            PresentViewController (alertController, true, null);
        });
    }
}

[Export ("authorizationController:didCompleteWithError:")]
public void DidComplete (ASAuthorizationController controller, NSError error)
{
    Console.WriteLine (error);
}

#endregion
```

## <a name="authorization-controller"></a>Contrôleur d’autorisation

La dernière partie de cette implémentation est le `ASAuthorizationController` qui gère les demandes d’autorisation pour le fournisseur.

```csharp
#region IASAuthorizationControllerPresentation Context Providing

public UIWindow GetPresentationAnchor (ASAuthorizationController controller) => View.Window;

#endregion
```

## <a name="related-links"></a>Liens connexes

* [Se connecter avec les instructions Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
* [Connectez-vous avec Apple habilitation.][2]
* [WWDC 2019 session 706 : présentation de la connexion avec Apple.][3]
* [Configurer la connexion avec Apple pour Xamarin. Forms][4]

[1]: https://developer.apple.com/documentation/authenticationservices/adding_the_sign_in_with_apple_flow_to_your_app
[2]: https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_applesignin
[3]: https://developer.apple.com/videos/play/wwdc19/706/
[4]: ~/xamarin-forms/platform/sign-in-with-apple/setup.md
[5]: https://developer.apple.com/account/resources/identifiers/list