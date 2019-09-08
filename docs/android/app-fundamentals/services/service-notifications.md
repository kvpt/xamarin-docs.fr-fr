---
title: Notifications de service
description: Ce guide explique comment un service Android peut utiliser des notifications locales pour distribuer des informations à un utilisateur.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 5d25604db1f88702f4c24df21b3ebba6c9c2fc95
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754814"
---
# <a name="service-notifications"></a>Notifications de service

_Ce guide explique comment un service Android peut utiliser des notifications locales pour distribuer des informations à un utilisateur._

## <a name="service-notifications-overview"></a>Présentation des notifications de service

Les notifications de service permettent à une application d’afficher des informations à l’utilisateur, même si l’application Android n’est pas au premier plan. Une notification peut fournir des actions à l’utilisateur, telles que l’affichage d’une activité à partir d’une application. L’exemple de code suivant montre comment un service peut distribuer une notification à un utilisateur :

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

Cette capture d’écran est un exemple de la notification qui s’affiche :

[![Icône de notification affichée dans la barre d’État](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Lorsque l’utilisateur fait défiler l’écran de notification du haut, la notification complète s’affiche :

![Notification affichée dans la barre d’État](service-notifications-images/02-fullnotification.png)

## <a name="updating-a-notification"></a>Mise à jour d’une notification

Pour mettre à jour une notification, le service republiera la notification en utilisant le même ID de notification. Android affiche ou met à jour la notification dans la barre d’État, si nécessaire.

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

Vous trouverez plus d’informations sur les notifications dans la section [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md) du Guide des [notifications Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="related-links"></a>Liens associés

- [Notifications locales dans Android](~/android/app-fundamentals/notifications/local-notifications.md)
