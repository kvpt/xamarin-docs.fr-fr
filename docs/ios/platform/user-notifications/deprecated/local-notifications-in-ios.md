---
title: Notifications dans Xamarin.iOS
description: Cette section montre comment implémenter des notifications locales dans Xamarin.iOS. Il explique les différents éléments d’interface utilisateur d’une notification d’iOS et traitent de l’API d’impliquée dans la création et affichage d’une notification.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 6710abd28a2b0f992296008d12950b95ec29783d
ms.sourcegitcommit: dd73477b1bccbd7ca45c1fb4e794da6b36ca163d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394693"
---
# <a name="notifications-in-xamarinios"></a>Notifications dans Xamarin.iOS

> [!IMPORTANT]
> Les informations contenues dans cette section se rapporte à iOS 9 et antérieures. Pour iOS 10 et versions ultérieures, consultez le [guide de framework de notification utilisateur](~/ios/platform/user-notifications/index.md).

iOS a trois façons d’indiquer à l’utilisateur qu’une notification a été reçue :

- **Son ou vibration** -iOS peuvent jouer un son pour informer les utilisateurs. Si le son est désactivé, l’appareil peut être configuré pour vibration.
- **Alertes** -il est possible d’afficher une boîte de dialogue sur l’écran des informations sur la notification.
- **Badges** - lorsqu’une notification est publiée, un nombre peut être affiché (avec un badge) sur l’icône d’application.

iOS fournit également un *centre de notifications* qui affiche toutes les notifications, à la fois locale et distantes, à l’utilisateur. Les utilisateurs peuvent y accéder en faisant défiler vers le bas en haut de l’écran :

![Le centre de notifications](local-notifications-in-ios-images/image13.png "le centre de notifications")

## <a name="creating-local-notifications-in-ios"></a>Création des notifications locales dans iOS

iOS rend relativement simple à créer et gérer les notifications locales.
Tout d’abord, iOS 8 requiert des applications pour demander l’autorisation de l’utilisateur afficher les notifications. Ajoutez le code suivant à votre application avant d’envoyer une notification locale - le [attaché l’exemple](https://developer.xamarin.com/samples/monotouch/LocalNotifications/) place dans le **AppDelegate**de **FinishedLaunching** (méthode).

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Confirmer la possibilité d’envoyer une notification locale](local-notifications-in-ios-images/image0-sml.png "confirmant la possibilité d’envoyer une notification locale")](local-notifications-in-ios-images/image0.png#lightbox)

Pour planifier une notification locale, créez un `UILocalNotification` de l’objet, définissez le `FireDate`et planifier son via la `ScheduleLocalNotification` méthode sur le `UIApplication.SharedApplication` objet. L’extrait de code suivant montre comment planifier une notification qui se déclenchent une minute à l’avenir et affiche une alerte avec un message :

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

La capture d’écran suivante montre à quoi ressemble cette alerte :

[![](local-notifications-in-ios-images/image2-sml.png "Un exemple d’alerte")](local-notifications-in-ios-images/image2.png#lightbox)

Notez que si l’utilisateur a choisi de *n'autorise pas* notifications alors rien ne s’affichera.

Si vous souhaitez appliquer un badge à l’icône d’application avec un nombre, vous pouvez définir comme indiqué dans la ligne de code suivant :

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Dans play d’ordre un son lors de l’icône, définissez la propriété SoundName sur la notification comme indiqué dans l’extrait de code suivant :

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Si le son de notification est supérieur à 30 secondes, iOS sera lu la valeur par défaut audio à la place.

> [!IMPORTANT]
> Il existe un bogue dans le simulateur iOS qui déclenche la notification de délégué à deux reprises. Ce problème ne doit pas se produire lors de l’exécution de l’application sur un appareil.

## <a name="handling-notifications"></a>Gestion des notifications

les applications iOS gérer les notifications locales et distantes dans presque exactement la même manière. Lors de l’exécution d’une application, le `ReceivedLocalNotification` méthode ou le `ReceivedRemoteNotification` méthode sur le `AppDelegate` classe sera appelée et les informations de notification recevront en tant que paramètre.

Une application peut traiter une notification de différentes façons. Par exemple, l’application peut afficher uniquement une alerte pour rappeler aux utilisateurs sur un événement. Ou bien, la notification peut être utilisée pour afficher une alerte à l’utilisateur un processus est terminé, tels que les fichiers de synchronisation en cours à un serveur.

Le code suivant montre comment gérer une notification locale et d’afficher une alerte et de rétablir le numéro de badge à zéro :

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

Si l’application n’est pas en cours d’exécution, iOS est le signal sonore et/ou mettre à jour le badge d’icône selon le cas. Lorsque l’utilisateur démarre l’application associée à l’alerte, l’application est lancée et le `FinishedLaunching` méthode sur le délégué de l’application sera appelée et les informations de notification seront transmises via le `launchOptions` paramètre. Si le dictionnaire d’options contient la clé `UIApplication.LaunchOptionsLocalNotificationKey`, puis le `AppDelegate` sait que l’application a été lancée à partir d’une notification locale. L’extrait de code suivant illustre ce processus :

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

Pour une notification à distance, `launchOptions` aura un `LaunchOptionsRemoteNotificationKey` avec associé à un `NSDictionary` contenant la charge utile de notification à distance. Vous pouvez extraire la charge utile de notification via le `alert`, `badge`, et `sound` clés. L’extrait de code suivant montre comment obtenir des notifications à distance :

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Récapitulatif

Cette section vous a montré comment créer et publier une notification dans Xamarin.iOS. Il montre comment une application peut réagir aux notifications en substituant le `ReceivedLocalNotification` méthode ou le `ReceivedRemoteNotification` méthode dans le `AppDelegate`.

## <a name="related-links"></a>Liens connexes

- [Notifications locales (exemple)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Local et Notifications Push pour les développeurs](https://developer.apple.com/notifications/)
- [Local et le Guide de programmation des notifications de Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
