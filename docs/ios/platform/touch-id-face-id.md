---
title: Id tactile et ID de visage avec Xamarin. iOS
description: Ce document fournit une description de haut niveau de l’authentification biométrique dans iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: 744a07343b9da87f196c0664f57b7d950844ab04
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490296"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>Utiliser Touch ID et face ID avec Xamarin. iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)

iOS prend en charge deux systèmes d’authentification biométrique :

1. **Touch ID** utilise un capteur d’empreintes digitales sous le bouton d’hébergement.
1. L' **ID de face** utilise des capteurs d’appareil photo avant pour authentifier les utilisateurs avec une analyse faciale.

Touch ID a été introduit dans iOS 7 et face ID dans iOS 11.

Ces systèmes d’authentification s’appuient sur un processeur de sécurité matériel appelé « _enclave sécurisée_». L’enclave sécurisée est responsable du chiffrement des représentations mathématiques des données de face et d’empreinte digitale, et de l’authentification des utilisateurs à l’aide de ces informations. D’après Apple, les données de visage et d’empreinte ne laissent pas l’appareil et ne sont pas sauvegardées dans iCloud. Les applications interagissent avec l’enclave sécurisée via l’API _d’authentification locale_ et ne peuvent pas récupérer les données de visage ou d’empreinte digitale, ni accéder directement à l’enclave sécurisée.

Touch ID et face ID peuvent être utilisés par les applications pour authentifier un utilisateur avant de fournir l’accès au contenu protégé.

## <a name="local-authentication-context"></a>Contexte d’authentification local

L’authentification biométrique sur iOS s’appuie sur un objet de _contexte d’authentification local_ , qui est une instance de la classe `LAContext`. La classe `LAContext` vous permet d’effectuer les opérations suivantes :

- Vérifiez la disponibilité du matériel biométrique.
- Évaluer les stratégies d’authentification.
- Évaluez les contrôles d’accès.
- Personnaliser et afficher les invites d’authentification.
- Réutiliser ou invalider un état d’authentification.
- Gérer les informations d’identification.

## <a name="detect-available-authentication-methods"></a>Détecter les méthodes d’authentification disponibles

L’exemple de projet comprend un `AuthenticationView` sauvegardé par un `AuthenticationViewController`. Cette classe remplace la méthode `ViewWillAppear` pour détecter les méthodes d’authentification disponibles :

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    public override void ViewWillAppear(bool animated)
    {
        base.ViewWillAppear(animated);
        unAuthenticatedLabel.Text = "";
    
        var context = new LAContext();
        var buttonText = "";

        // Is login with biometrics possible?
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out var authError1))
        {
            // has Touch ID or Face ID
            if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
            {
                context.LocalizedReason = "Authorize for access to secrets"; // iOS 11
                BiometryType = context.BiometryType == LABiometryType.TouchId ? "Touch ID" : "Face ID";
                buttonText = $"Login with {BiometryType}";
            }
            // No FaceID before iOS 11
            else
            {
                buttonText = $"Login with Touch ID";
            }
        }

        // Is pin login possible?
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out var authError2))
        {
            buttonText = $"Login"; // with device PIN
            BiometryType = "Device PIN";
        }

        // Local authentication not possible
        else
        {
            // Application might choose to implement a custom username/password
            buttonText = "Use unsecured";
            BiometryType = "none";
        }
        AuthenticateButton.SetTitle(buttonText, UIControlState.Normal);
    }
}
```

La méthode `ViewWillAppear` est appelée lorsque l’interface utilisateur est sur le paragraphe de l’utilisateur. Cette méthode définit une nouvelle instance de `LAContext` et utilise la méthode `CanEvaluatePolicy` pour déterminer si l’authentification biométrique est activée. Si c’est le cas, il vérifie la version du système et `BiometryType` enum pour déterminer quelles options biométriques sont disponibles.

Si l’authentification biométrique n’est pas activée, l’application tente de revenir à l’authentification du PIN. Si aucune authentification biométrique ou PIN n’est disponible, le propriétaire de l’appareil n’a pas activé les fonctionnalités de sécurité et le contenu ne peut pas être sécurisé par le biais de l’authentification locale.

## <a name="authenticate-a-user"></a>Authentifier un utilisateur

La `AuthenticationViewController` dans l’exemple de projet comprend une méthode `AuthenticateMe`, qui est chargée d’authentifier l’utilisateur :

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    partial void AuthenticateMe(UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var localizedReason = new NSString("To access secrets");
    
        // Because LocalAuthentication APIs have been extended over time,
        // you must check iOS version before setting some properties
        context.LocalizedFallbackTitle = "Fallback";
    
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            context.LocalizedCancelTitle = "Cancel";
        }
        if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
        {
            context.LocalizedReason = "Authorize for access to secrets";
            BiometryType = context.BiometryType == LABiometryType.TouchId ? "TouchID" : "FaceID";
        }
    
        // Check if biometric authentication is possible
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = $"{BiometryType} Authentication Failed";
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, localizedReason, replyHandler);
        }

        // Fall back to PIN authentication
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = "Device PIN Authentication Failed";
                        AuthenticateButton.Hidden = true;
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, localizedReason, replyHandler);
        }

        // User hasn't configured any authentication: show dialog with options
        else
        {
            unAuthenticatedLabel.Text = "No device auth configured";
            var okCancelAlertController = UIAlertController.Create("No authentication", "This device does't have authentication configured.", UIAlertControllerStyle.Alert);
            okCancelAlertController.AddAction(UIAlertAction.Create("Use unsecured", UIAlertActionStyle.Default, alert => PerformSegue("AuthenticationSegue", this)));
            okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine("Cancel was clicked")));
            PresentViewController(okCancelAlertController, true, null);
        }
    } 
}
```

La méthode `AuthenticateMe` est appelée en réponse à l’utilisateur en appuyant sur un bouton de **connexion** . Un nouvel objet `LAContext` est instancié et la version de l’appareil est vérifiée pour déterminer les propriétés à définir dans le contexte d’authentification local.

La méthode `CanEvaluatePolicy` est appelée pour vérifier si l’authentification biométrique est activée, revenir à l’authentification du PIN si possible, et enfin offrir un mode non sécurisé si aucune authentification n’est disponible. Si une méthode d’authentification est disponible, la méthode `EvaluatePolicy` est utilisée pour afficher l’interface utilisateur et terminer le processus d’authentification.

L’exemple de projet contient des données fictives et une vue pour afficher les données si l’authentification réussit.

## <a name="related-links"></a>Liens connexes

- [Authentification locale à l’aide de Touch ID ou de l’exemple d’ID de visage](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)
- [À propos de Touch ID](https://support.apple.com/en-us/HT204587) sur support.Apple.com
- [À propos](https://support.apple.com/en-us/HT208108) de l’ID de visage sur support.Apple.com
