---
title: Alertes critiques dans Xamarin. iOS
description: Ce document explique comment utiliser les alertes critiques avec Xamarin. iOS. Les alertes critiques, introduites avec iOS 12, sont des notifications d’interruption qui lisent un son, que la déconnexion ne soit pas activée ou que le commutateur de sonnerie soit désactivé.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: 54a214215f77b66f6a4b134dcb8d27b26c44fb6c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291291"
---
# <a name="critical-alerts-in-xamarinios"></a>Alertes critiques dans Xamarin. iOS

Avec iOS 12, les applications peuvent envoyer des alertes critiques. Les alertes critiques lisent un son, que la valeur ne pas déranger soit activée ou non, ou que le commutateur de sonnerie soit désactivé. Ces notifications sont perturbatrices et ne doivent être utilisées que lorsque les utilisateurs doivent agir immédiatement.

## <a name="custom-critical-alert-entitlement"></a>Habilitation d’alerte critique personnalisée

Pour afficher les alertes critiques dans votre application, vous devez d’abord [demander un droit de notification d’alerte critique personnalisé](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) auprès d’Apple.

Après avoir reçu ce droit d’Apple et effectué toutes les instructions associées sur la façon de configurer votre application pour l’utiliser, ajoutez le [droit](~/ios/deploy-test/provisioning/entitlements.md) personnalisé au (x) fichier (s) de droits **. plist** de votre application. Ensuite, configurez vos options de **signature d’offre groupée iOS** pour utiliser les **. plist** lors de la signature de l’application sur le simulateur et l’appareil.

## <a name="request-authorization"></a>Demander l’autorisation

La demande d’autorisation de notification d’une application demande à l’utilisateur d’autoriser ou d’interdire les notifications d’une application. Si la demande d’autorisation de notification demande l’autorisation d’envoyer des alertes critiques, l’application donne également à l’utilisateur la possibilité de s’abonner aux alertes critiques.

Le code suivant demande l’autorisation d’envoyer à la fois des alertes critiques et des notifications et des sons standard en passant le[`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions)
valeurs à [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>Alertes critiques locales

Pour envoyer une alerte critique locale, créez un[`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
et affectez `Sound` à sa propriété la valeur :

- `UNNotificationSound.DefaultCriticalSound`, qui utilise le son de notification critique par défaut.
- `UNNotificationSound.GetCriticalSound`, qui vous permet de spécifier un son personnalisé fourni avec votre application et un volume.

Créez ensuite un `UNNotificationRequest` à partir du contenu de notification et ajoutez-le au centre de notifications :

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> Les alertes critiques ne seront pas remises si elles ne sont pas activées pour votre application. Avec l’invite qui s’affiche la première fois qu’une application demande l’autorisation d’envoyer des alertes critiques, un utilisateur peut également activer ou désactiver les alertes critiques dans la section **notifications** de votre application de l’application **paramètres** iOS.

## <a name="remote-critical-alerts"></a>Alertes critiques distantes

Pour plus d’informations sur les alertes critiques distantes, consultez la session [Nouveautés dans les notifications utilisateur](https://developer.apple.com/videos/play/wwdc2018/710/) à partir de WWDC 2018 et [génération d’un document de notification à distance](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) .

## <a name="related-links"></a>Liens connexes

- [Infrastructure de notifications utilisateur dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Nouveautés des notifications utilisateur (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Meilleures pratiques et nouveautés dans les notifications utilisateur (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Génération d’une notification à distance (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
