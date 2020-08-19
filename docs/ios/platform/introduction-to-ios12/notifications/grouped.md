---
title: Notifications groupées dans Xamarin. iOS
description: Avec iOS 12, il est possible de regrouper les notifications dans le centre de notifications ou l’écran de verrouillage par application ou par thread. Ce document explique comment envoyer des notifications de thread et non thread avec Xamarin. iOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 8c4ad31d91e6f9e4415309eee6c3da8a095dc097
ms.sourcegitcommit: 80bd4d74812d2c086a164323b72a14662276d302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608608"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notifications groupées dans Xamarin. iOS

Par défaut, iOS 12 place toutes les notifications d’une application dans un groupe. L’écran de verrouillage et le centre de notifications affichent ce groupe sous la forme d’une pile avec la notification la plus récente par-dessus. Les utilisateurs peuvent développer le groupe pour voir toutes les notifications qu’il contient et faire disparaître le groupe dans son ensemble.

Les applications peuvent également regrouper les notifications par thread, ce qui permet aux utilisateurs de rechercher et d’interagir plus facilement avec les informations spécifiques qui vous intéressent.

## <a name="sample-app-groupednotifications"></a>Exemple d’application : GroupedNotifications

Pour savoir comment utiliser des notifications groupées avec Xamarin. iOS, consultez l’exemple d’application [GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications) .

Cet exemple d’application simule des conversations avec différents amis, en envoyant une notification pour chaque message et en les regroupant par thread. Il montre également comment les notifications déthreadées s’imposent dans un groupe au niveau de l’application.

Les extraits de code de ce guide proviennent de cet exemple d’application.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Demander l’autorisation et autoriser les notifications au premier plan

Avant qu’une application puisse envoyer des notifications locales, elle doit demander l’autorisation de le faire. Dans l’exemple d’application [`AppDelegate`](xref:UIKit.UIApplicationDelegate) , la [`FinishedLaunching`](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) méthode demande cette autorisation :

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

Le [`Delegate`](xref:UserNotifications.UNUserNotificationCenter.Delegate) (défini ci-dessus) pour un [`UNUserNotificationCenter`](xref:UserNotifications.UNUserNotificationCenter) décide si une application de premier plan doit ou non afficher une notification entrante en appelant le gestionnaire d’achèvement passé à [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})) :

```csharp
[Export("userNotificationCenter:willPresentNotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

Le [`UNNotificationPresentationOptions.Alert`](xref:UserNotifications.UNNotificationPresentationOptions) paramètre indique que l’application doit afficher l’alerte, mais pas lire un son ou mettre à jour un badge.

## <a name="threaded-notifications"></a>Notifications de thread

Appuyez sur le message de l’exemple d’application avec le bouton **Alice** à plusieurs reprises pour qu’il envoie des notifications pour une conversation avec un ami nommé Alice.
Étant donné que les notifications de cette conversation font partie du même thread, l’écran de verrouillage et le centre de notification les regroupent ensemble.

Pour démarrer une conversation avec un autre ami, appuyez sur le bouton **choisir un nouvel ami** . Les notifications pour cette conversation s’affichent dans un groupe distinct.

### <a name="threadidentifier"></a>ThreadIdentifier

Chaque fois que l’exemple d’application démarre un nouveau thread, il crée un identificateur de thread unique :

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Pour envoyer une notification de thread, l’exemple d’application :

- Vérifie si l’application a l’autorisation d’envoyer une notification.
- Crée un [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
objet pour le contenu de la notification et définit son [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
à l’identificateur de thread créé ci-dessus.
- Crée une demande et planifie la notification :

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

Toutes les notifications de la même application avec le même identificateur de thread s’affichent dans le même groupe de notification.

> [!NOTE]
> Pour définir un identificateur de thread sur une notification distante, ajoutez la `thread-id` clé à la charge utile JSON de la notification. Pour plus d’informations, consultez la page génération d’un document de [notification à distance](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) par Apple.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` spécifie comment une notification aura un impact sur le texte résumé qui apparaît dans le coin inférieur gauche d’un groupe de notification auquel la notification appartient. iOS agrège le texte récapitulatif des notifications dans le même groupe pour créer une description résumée globale.

L’exemple d’application utilise l’auteur du message en tant qu’argument Summary. À l’aide de cette approche, le texte Résumé d’un groupe de six notifications avec Alice peut être **de 5 autres notifications d’Alice et de moi**.

## <a name="unthreaded-notifications"></a>Notifications déthreadées

Chaque pression sur le bouton de **rappel de rendez-vous** de l’application d’exemple envoie une des nombreuses notifications de rappel de rendez-vous. Étant donné que ces rappels ne sont pas liés, ils apparaissent dans le groupe de notification au niveau de l’application sur l’écran de verrouillage et dans le centre de notification.

Pour envoyer une notification non thread, la méthode de l’exemple d’application `ScheduleUnthreadedNotification` utilise du code similaire à celui indiqué ci-dessus.
Toutefois, il ne définit pas `ThreadIdentifier` sur l' `UNMutableNotificationContent` objet.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications)
- [Infrastructure de notifications utilisateur dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Nouveautés des notifications utilisateur (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Utilisation de notifications groupées (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Meilleures pratiques et nouveautés dans les notifications utilisateur (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Génération d’une notification à distance (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
