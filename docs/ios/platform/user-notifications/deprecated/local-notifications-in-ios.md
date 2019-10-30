---
title: Notifications dans Xamarin. iOS
description: Cette section montre comment implémenter des notifications locales dans Xamarin. iOS. Il explique les différents éléments d’interface utilisateur d’une notification iOS et discute des API impliquées dans la création et l’affichage d’une notification.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 0cd0937229e8679af46313d0bce4c62792c0f36b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031383"
---
# <a name="notifications-in-xamarinios"></a>Notifications dans Xamarin. iOS

> [!IMPORTANT]
> Les informations contenues dans cette section concernent iOS 9 et versions antérieures. Pour iOS 10 et versions ultérieures, consultez le Guide de l' [infrastructure de notification utilisateur](~/ios/platform/user-notifications/index.md).

iOS offre trois moyens d’indiquer à l’utilisateur qu’une notification a été reçue :

- Les **sons ou les vibrations** -iOS peuvent émettre un son pour avertir les utilisateurs. Si le son est désactivé, l’appareil peut être configuré pour vibrer.
- **Alertes** : il est possible d’afficher une boîte de dialogue à l’écran avec des informations sur la notification.
- **Badges** : lorsqu’une notification est publiée, un nombre peut être affiché (avec un badge) sur l’icône de l’application.

iOS fournit également un *Centre de notification* qui affiche toutes les notifications, tant locales que distantes, à l’utilisateur. Les utilisateurs peuvent y accéder en effectuant un balayage vers le haut de l’écran :

![Le centre de notifications](local-notifications-in-ios-images/image13.png "Le centre de notifications")

## <a name="creating-local-notifications-in-ios"></a>Création de notifications locales dans iOS

iOS simplifie la création et la gestion des notifications locales.
Tout d’abord, iOS 8 requiert que les applications demandent l’autorisation de l’utilisateur pour afficher les notifications. Ajoutez le code suivant à votre application avant d’essayer d’envoyer une notification locale. l' [exemple joint](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications) le place dans la méthode **FinishedLaunching** de **AppDelegate**.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Confirmation de la possibilité d’envoyer une notification locale](local-notifications-in-ios-images/image0-sml.png "Confirmation de la possibilité d’envoyer une notification locale")](local-notifications-in-ios-images/image0.png#lightbox)

Pour planifier une notification locale, créez un objet `UILocalNotification`, définissez la `FireDate`et planifiez-la à l’aide de la méthode `ScheduleLocalNotification` sur l’objet `UIApplication.SharedApplication`. L’extrait de code suivant montre comment planifier une notification qui se déclenchera une minute à l’avenir et affichera une alerte avec un message :

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

La capture d’écran suivante montre à quoi ressemble cette alerte :

[![](local-notifications-in-ios-images/image2-sml.png "An example alert")](local-notifications-in-ios-images/image2.png#lightbox)

Notez que si l’utilisateur a choisi de *ne pas autoriser* les notifications, rien ne s’affiche.

Si vous souhaitez appliquer un badge à l’icône de l’application avec un nombre, vous pouvez le définir comme indiqué dans le code de ligne suivant :

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Dans l’ordre, lisez un son avec l’icône, définissez la propriété SoundName sur la notification comme indiqué dans l’extrait de code suivant :

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Si le son de la notification est supérieur à 30 secondes, iOS lira le son par défaut à la place.

> [!IMPORTANT]
> Il existe un bogue dans le simulateur iOS qui déclenchera la notification de délégué à deux reprises. Ce problème ne doit pas se produire lors de l’exécution de l’application sur un appareil.

## <a name="handling-notifications"></a>Gestion des notifications

les applications iOS gèrent les notifications locales et distantes de la même manière presque exactement. Quand une application est en cours d’exécution, la méthode `ReceivedLocalNotification` ou la méthode `ReceivedRemoteNotification` sur la classe `AppDelegate` sera appelée, et les informations de notification seront passées en tant que paramètre.

Une application peut gérer une notification de différentes façons. Par exemple, l’application peut simplement afficher une alerte pour rappeler aux utilisateurs tout événement. Ou la notification peut être utilisée pour afficher une alerte à l’utilisateur qu’un processus est terminé, comme la synchronisation de fichiers avec un serveur.

Le code suivant montre comment gérer une notification locale et afficher une alerte et réinitialiser le numéro de badge à zéro :

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

Si l’application n’est pas en cours d’exécution, iOS lira le son et/ou met à jour le badge d’icône selon le cas. Lorsque l’utilisateur démarre l’application associée à l’alerte, l’application se lance et la méthode `FinishedLaunching` sur le délégué d’application est appelée et les informations de notification sont transmises par le biais du paramètre `launchOptions`. Si le dictionnaire d’options contient la clé `UIApplication.LaunchOptionsLocalNotificationKey`, le `AppDelegate` sait que l’application a été lancée à partir d’une notification locale. L’extrait de code suivant illustre ce processus :

```csharp
// check for a local notification
if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
{
    var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
    if (localNotification != null)
    {
        UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        Window.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
}
```

Pour une notification à distance, `launchOptions` aura un `LaunchOptionsRemoteNotificationKey` avec un `NSDictionary` associé contenant la charge utile de notification distante. Vous pouvez extraire la charge utile de notification via les clés `alert`, `badge`et `sound`. L’extrait de code suivant montre comment recevoir des notifications distantes :

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Récapitulatif

Cette section a montré comment créer et publier une notification dans Xamarin. iOS. Elle montre comment une application peut réagir aux notifications en remplaçant la méthode `ReceivedLocalNotification` ou la méthode `ReceivedRemoteNotification` dans le `AppDelegate`.

## <a name="related-links"></a>Liens connexes

- [Notifications locales (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Notifications push et locales pour les développeurs](https://developer.apple.com/notifications/)
- [Guide de programmation des notifications locales et Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](https://docs.microsoft.com/dotnet/api/uikit.uiapplication)
- [UILocalNotification](https://docs.microsoft.com/dotnet/api/uikit.UILocalNotification)
