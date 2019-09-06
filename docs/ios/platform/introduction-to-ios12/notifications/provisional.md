---
title: Notifications provisoires dans Xamarin. iOS
description: Ce document explique comment utiliser Xamarin. iOS pour travailler avec des notifications provisoires. Les notifications provisoires, introduites dans iOS 12, permettent aux applications d’envoyer des notifications silencieuses sans autorisation explicite de l’utilisateur.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: d321e8061d3091abeaa3cff6a6af9172c981cb60
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291204"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notifications provisoires dans Xamarin. iOS

Les notifications provisoires permettent aux applications de remettre des notifications sans consentement préalable explicite de l’utilisateur. Ces notifications arrivent en mode silencieux et s’affichent uniquement dans le centre de notifications, ce qui permet aux utilisateurs de les afficher en préversion avant d’opter pour leur livraison continue.

Dans le centre de notifications, les utilisateurs peuvent spécifier qu’une application doit cesser de recevoir des notifications provisoires, continuer à les mettre en service ou commencer à les diffuser de manière plus claire.

## <a name="sample-app-redgreennotifications"></a>Exemple d’application : RedGreenNotifications

Jetez un coup d’œil à l’exemple d’application [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , qui envoie des notifications provisoires.

## <a name="sending-provisional-notifications"></a>Envoi de notifications provisoires

Pour envoyer des notifications provisoires, fournissez `UNAuthorizationOptions.Provisional` une option au[`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
méthode de `UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

Si l’utilisateur promeut des notifications provisoires vers `UNAuthorizationOptions` une remise visible `RequestAuthorization` , les valeurs transmises à déterminent les nouveaux paramètres de remise des `UNAuthorizationOptions.Alert` notifications (dans le code ci-dessus, et `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Infrastructure de notifications utilisateur dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Nouveautés des notifications utilisateur (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Meilleures pratiques et nouveautés dans les notifications utilisateur (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Génération d’une notification à distance (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
