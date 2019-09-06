---
title: Gestion des notifications dans Xamarin. iOS
description: Ce document explique comment utiliser Xamarin. iOS pour tirer parti des nouvelles fonctionnalités de gestion des notifications introduites dans iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: b6c6baad2cbd923bde4dab3766040b5df4351787
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282113"
---
# <a name="notification-management-in-xamarinios"></a>Gestion des notifications dans Xamarin. iOS

Dans iOS 12, le système d’exploitation peut créer un lien profond entre le centre de notifications et l’application paramètres vers l’écran de gestion des notifications d’une application. Cet écran doit permettre aux utilisateurs d’accepter et de sortir des différents types de notifications envoyées par l’application.

## <a name="sample-app-redgreennotifications"></a>Exemple d’application : RedGreenNotifications

Pour voir un exemple de fonctionnement de la gestion des notifications, jetez un coup d’œil sur l’exemple d’application [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) .

Cet exemple d’application envoie deux types de notifications (rouge et vert) et fournit un écran qui permet aux utilisateurs d’accepter ou de sortir de l’un ou l’autre type.

Les extraits de code de ce guide proviennent de cet exemple d’application.

## <a name="notification-management-screen"></a>Écran gestion des notifications

Dans l’exemple d’application `ManageNotificationsViewController` , définit une interface utilisateur qui permet aux utilisateurs d’activer et de désactiver indépendamment les notifications rouges et les notifications vertes. Il s’agit d’une norme[`UIViewController`](xref:UIKit.UIViewController)
contenant un [`UISwitch`](xref:UIKit.UISwitch) pour chaque type de notification. En basculant le commutateur pour les deux types de notifications, dans les paramètres par défaut de l’utilisateur, le choix de l’utilisateur pour ce type de notification :

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> L’écran de gestion des notifications vérifie également si l’utilisateur a complètement désactivé les notifications pour l’application. Si c’est le cas, il masque les basculements pour les types de notifications individuels. Pour ce faire, l’écran de gestion des notifications :
>
> - Appelle [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) et examine la [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) propriété.
> - Masque les bascules pour les types de notification individuels si les notifications ont été complètement désactivées pour l’application.
> - Vérifie si les notifications ont été désactivées chaque fois que l’application passe au premier plan, car l’utilisateur peut activer/désactiver les notifications dans les paramètres iOS à tout moment.

La classe de l' `ViewController` exemple d’application, qui envoie les notifications, vérifie la préférence de l’utilisateur avant d’envoyer une notification locale pour s’assurer que la notification est d’un type que l’utilisateur veut réellement recevoir :

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Lien profond

iOS liens profond vers l’écran de gestion des notifications d’une application à partir du centre de notification et des paramètres de notification de l’application dans l’application paramètres. Pour faciliter cette tâche, une application doit :

- Indique qu’un écran de gestion des notifications est `UNAuthorizationOptions.ProvidesAppNotificationSettings` disponible en passant à la demande d’autorisation de notification de l’application.
- Implémentez `OpenSettings` la méthode [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate)à partir de.

### <a name="authorization-request"></a>Demande d’autorisation

Pour indiquer au système d’exploitation qu’un écran de gestion des notifications est disponible, une application `UNAuthorizationOptions.ProvidesAppNotificationSettings` doit passer l’option (ainsi que toutes les autres options de remise de `RequestAuthorization` notification dont elle `UNUserNotificationCenter`a besoin) à la méthode sur le.

Par exemple, dans l’exemple d' `AppDelegate`application :

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>Méthode OpenSettings

La `OpenSettings` méthode, appelée par le système pour établir un lien profond vers l’écran de gestion des notifications d’une application, doit accéder directement à cet écran à l’utilisateur.

Dans l’exemple d’application, cette méthode effectue le segue sur `ManageNotificationsViewController` le si nécessaire :

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Infrastructure de notifications utilisateur dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Nouveautés des notifications utilisateur (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Meilleures pratiques et nouveautés dans les notifications utilisateur (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Génération d’une notification à distance (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
