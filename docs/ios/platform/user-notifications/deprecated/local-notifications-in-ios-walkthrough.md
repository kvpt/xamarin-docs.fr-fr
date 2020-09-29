---
title: 'Procédure pas à pas : utilisation de notifications locales dans Xamarin. iOS'
description: Dans cette section, nous allons découvrir comment utiliser des notifications locales dans une application Xamarin. iOS. Il illustre les principes de base de la création et de la publication d’une notification qui contiendra une alerte lorsqu’elle sera reçue par l’application.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 915b5cb11aed96598e0460125734b15757f45466
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436141"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Procédure pas à pas : utilisation de notifications locales dans Xamarin. iOS

_Dans cette section, nous allons découvrir comment utiliser des notifications locales dans une application Xamarin. iOS. Il illustre les principes de base de la création et de la publication d’une notification qui contiendra une alerte lorsqu’elle sera reçue par l’application._

> [!IMPORTANT]
> Les informations contenues dans cette section concernent iOS 9 et les versions antérieures, elles ont été laissées ici pour prendre en charge les anciennes versions d’iOS. Pour iOS 10 et versions ultérieures, consultez le Guide de l' [infrastructure de notification utilisateur](~/ios/platform/user-notifications/index.md) pour prendre en charge la notification locale et distante sur un appareil iOS.

## <a name="walkthrough"></a>Procédure pas à pas

Laissez-vous créer une application simple qui affichera les notifications locales en action. Cette application n’a qu’un seul bouton. Lorsque nous cliquons sur le bouton, une notification locale est créée. Une fois que la période spécifiée s’est écoulée, la notification s’affiche.

1. Dans Visual Studio pour Mac, créez une solution iOS de vue unique et appelez-la `Notifications` .
1. Ouvrez le `Main.storyboard` fichier, puis faites glisser un bouton sur la vue. Nommez le **bouton Button et**donnez-lui le titre **Ajouter une notification**. Vous pouvez également définir des [contraintes](~/ios/user-interface/designer/designer-auto-layout.md) sur le bouton à ce stade : 

    ![Définition de certaines contraintes sur le bouton](local-notifications-in-ios-walkthrough-images/image3.png)
1. Modifiez la `ViewController` classe, puis ajoutez le gestionnaire d’événements suivant à la méthode ViewDidLoad :

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    Ce code crée une notification qui utilise un son, définit la valeur du badge d’icône sur 1 et affiche une alerte à l’utilisateur.

1. Modifiez ensuite le fichier `AppDelegate.cs` , puis ajoutez le code suivant à la `FinishedLaunching` méthode. Nous avons vérifié que l’appareil exécute iOS 8, si c’est le cas, nous devons **demander à l'** utilisateur l’autorisation de recevoir des notifications :

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. Toujours dans `AppDelegate.cs` , ajoutez la méthode suivante qui sera appelée lors de la réception d’une notification :

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
    {
        // show an alert
        UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
    ```

1. Nous devons gérer le cas dans lequel la notification a été lancée en raison d’une notification locale. Modifiez la méthode `FinishedLaunching` dans la `AppDelegate` pour inclure l’extrait de code suivant :

    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
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
    }
    ```

1. Enfin, exécutez l’application. Sur iOS 8, vous êtes invité à autoriser les notifications. Cliquez sur **OK** , puis sur le bouton **Ajouter une notification** . Après une brève pause, vous devriez voir la boîte de dialogue d’alerte, comme illustré dans les captures d’écran suivantes :

    ![Confirmation de la possibilité d’envoyer des notifications ](local-notifications-in-ios-walkthrough-images/image0.png) ![ bouton Ajouter une notification ](local-notifications-in-ios-walkthrough-images/image1.png) ![ boîte de dialogue alerte de notification](local-notifications-in-ios-walkthrough-images/image2.png)

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas a montré comment utiliser les différentes API pour créer et publier des notifications dans iOS. Elle a également montré comment mettre à jour l’icône de l’application avec un badge pour fournir des commentaires spécifiques à l’application à l’utilisateur.

## <a name="related-links"></a>Liens associés

- [Notifications locales (exemple)](/samples/xamarin/ios-samples/localnotifications)
- [Guide de programmation des notifications locales et Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)