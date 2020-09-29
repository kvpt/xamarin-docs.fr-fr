---
title: Boutons d’action de notification dynamiques dans Xamarin. iOS
description: Avec iOS 12, une extension de contenu de notification peut ajouter, supprimer et mettre à jour les boutons d’action affichés à côté d’une notification. Ce document explique comment utiliser les boutons d’action de notification dynamiques avec Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 88eb3dec0c72acd6984b226eae5acee4feb3eaac
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433821"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Boutons d’action de notification dynamiques dans Xamarin. iOS

Dans iOS 12, les notifications peuvent ajouter, supprimer et mettre à jour dynamiquement leurs boutons d’action associés. Une telle personnalisation permet de fournir aux utilisateurs des actions qui sont directement pertinentes pour le contenu de la notification et l’interaction de l’utilisateur avec lui.

## <a name="sample-app-redgreennotifications"></a>Exemple d’application : RedGreenNotifications

Les extraits de code de ce guide proviennent de l’exemple d’application [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) , qui montre comment utiliser Xamarin. IOS pour travailler avec des boutons d’action de notification dans IOS 12.

Cet exemple d’application envoie deux types de notifications locales : rouge et vert.
Une fois que l’application a envoyé une notification, utilisez l’interface tactile en 3D pour afficher son interface utilisateur personnalisée. Ensuite, utilisez les boutons d’action de notification pour faire pivoter l’image affichée. Lorsque l’image pivote, un bouton **Réinitialiser la rotation** apparaît et disparaît si nécessaire.

Les extraits de code de ce guide proviennent de cet exemple d’application.

## <a name="default-action-buttons"></a>Boutons d’action par défaut

La catégorie d’une notification détermine ses boutons d’action par défaut.

Créez et enregistrez des catégories de notification lors du lancement d’une application.
Par exemple, dans l' [exemple d’application](#sample-app-redgreennotifications), la `FinishedLaunching` méthode de `AppDelegate` effectue les opérations suivantes :

- Définit une catégorie pour les notifications rouges et une autre pour les notifications vertes
- Inscrit ces catégories en appelant la [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
méthode de `UNUserNotificationCenter`
- Attache une seule [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
à chaque catégorie

L’exemple de code suivant montre comment cela fonctionne :

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

Sur la base de ce code, toute notification dont [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
est « rouge-catégorie » ou « vert-catégorie », par défaut, affiche un bouton d’action **rotation 20 °** .

## <a name="in-app-handling-of-notification-action-buttons"></a>Gestion dans l’application des boutons d’action de notification

`UNUserNotificationCenter` a une `Delegate` propriété de type [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate) .

Dans l’exemple d’application, se `AppDelegate` définit lui-même en tant que délégué du centre de notification de l’utilisateur dans `FinishedLaunching` :

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

`AppDelegate`Implémente ensuite[`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
pour gérer les clics de bouton d’action :

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

Cette implémentation de `DidReceiveNotificationResponse` ne gère pas le bouton d’action **rotation 20 °** de la notification. Au lieu de cela, l’extension de contenu de la notification gère les pressions sur ce bouton. La section suivante décrit plus en détail la gestion des boutons d’action de notification.

## <a name="action-buttons-in-the-notification-content-extension"></a>Boutons d’action dans l’extension de contenu de notification

Une extension de contenu de notification contient un contrôleur d’affichage qui définit l’interface personnalisée pour une notification.

Ce contrôleur d’affichage peut utiliser `GetNotificationActions` les `SetNotificationActions` méthodes et sur son [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
propriété permettant d’accéder aux boutons d’action de la notification et de les modifier.

Dans l’exemple d’application, le contrôleur d’affichage de l’extension de contenu de notification modifie les boutons d’action uniquement lorsque vous répondez à un clic sur un bouton d’action déjà existant.

> [!NOTE]
> Une extension de contenu de notification peut répondre à un appui sur un bouton d’action dans sa méthode de contrôleur d’affichage [`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) , déclarée dans le cadre de [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension).
>
> Bien qu’il partage un nom avec la `DidReceiveNotificationResponse` méthode [décrite ci-dessus](#in-app-handling-of-notification-action-buttons), il s’agit d’une méthode différente.
>
> Lorsqu’une extension de contenu de notification termine le traitement d’un appui sur un bouton, elle peut choisir d’indiquer ou non à l’application principale de gérer le même TAP-Button. Pour ce faire, il doit passer une valeur appropriée de [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) à son gestionnaire d’achèvement :
>
> - `Dismiss` indique que l’interface de notification doit être fermée et que l’application principale n’a pas besoin de gérer le robinet de bouton.
> - `DismissAndForwardAction` indique que l’interface de notification doit être fermée et que l’application principale doit également gérer le robinet de bouton.
> - `DoNotDismiss` indique que l’interface de notification ne doit pas être fermée et que l’application principale n’a pas besoin de gérer le robinet de bouton.

La méthode de l’extension de contenu `DidReceiveNotificationResponse` détermine le bouton d’action qui a été taraudé, fait pivoter l’image dans l’interface de la notification, puis affiche ou masque un bouton d’action de **réinitialisation** :

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

Dans ce cas, la méthode passe `UNNotificationContentExtensionResponseOption.DoNotDismiss` à son gestionnaire d’achèvement. Cela signifie que l’interface de la notification restera ouverte.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Infrastructure de notifications utilisateur dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Déclaration de vos types de notifications exploitables](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Nouveautés des notifications utilisateur (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Meilleures pratiques et nouveautés dans les notifications utilisateur (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Génération d’une notification à distance (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)