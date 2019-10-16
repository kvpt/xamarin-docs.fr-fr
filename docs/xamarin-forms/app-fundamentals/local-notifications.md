---
title: Notifications locales Xamarin. Forms
description: Cet article explique comment envoyer et recevoir des notifications locales dans Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: 49afc8c6484c465bf5c9e1d64a8c9122a0bcf506
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72371570"
---
# <a name="local-notifications-in-xamarinforms"></a>Notifications locales dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Les notifications locales sont des alertes envoyées par les applications installées sur un appareil mobile. Les notifications locales sont souvent utilisées pour des fonctionnalités telles que :

- Événements de calendrier
- Des rappels
- Déclencheurs basés sur l’emplacement

Chaque plateforme gère la création, l’affichage et la consommation des notifications locales différemment. Cet article explique comment créer une abstraction multiplateforme pour envoyer et recevoir des notifications locales avec Xamarin. Forms.

[application de notifications de @no__t 1Local sur iOS et Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Créer une interface multiplateforme

L’application Xamarin. Forms doit créer et consommer des notifications sans se préoccuper des implémentations de la plateforme sous-jacente. L’interface `INotificationManager` suivante est implémentée dans la bibliothèque de code partagée et définit une API multiplateforme que l’application peut utiliser pour interagir avec les notifications :

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Cette interface sera implémentée dans chaque projet de plateforme. L’événement `NotificationReceived` permet à l’application de gérer les notifications entrantes. La méthode `Initialize` doit exécuter toute logique de plateforme Native nécessaire pour préparer le système de notification. La méthode `ScheduleNotification` doit envoyer une notification. La méthode `ReceiveNotification` doit être appelée par la plateforme sous-jacente lors de la réception d’un message.

## <a name="consume-the-interface-in-xamarinforms"></a>Utiliser l’interface dans Xamarin. Forms

Une fois qu’une interface a été créée, elle peut être consommée dans le projet Xamarin. Forms partagé même si les implémentations de plateforme n’ont pas encore été créées. L’exemple d’application contient un `ContentPage` appelé **MainPage. Xaml** avec le contenu suivant :

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick"/>
</StackLayout>
```

La disposition contient un élément `Label` avec des instructions pour l’utilisateur et une `Button` qui doit planifier une notification lorsqu’elle est exploitée.

Le code-behind de la classe `MainPage` gère l’envoi et la réception des notifications :

```csharp
public partial class MainPage : ContentPage
{
    INotificationManager notificationManager;
    int notificationNumber = 0;

    public MainPage()
    {
        InitializeComponent();

        notificationManager = DependencyService.Get<INotificationManager>();
        notificationManager.NotificationReceived += (sender, eventArgs) =>
        {
            var evtData = (NotificationEventArgs)eventArgs;
            ShowNotification(evtData.Title, evtData.Message);
        };
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.ScheduleNotification(title, message);
    }

    void ShowNotification(string title, string message)
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            var msg = new Label()
            {
                Text = $"Notification Received:\nTitle: {title}\nMessage: {message}"
            };
            stackLayout.Children.Add(msg);
        });
    }
}
```

Le constructeur de classe `MainPage` utilise Xamarin. Forms `DependencyService` pour récupérer une instance spécifique à la plateforme du `INotificationManager`. La méthode `OnScheduleClicked` utilise l’instance `INotificationManager` pour planifier une nouvelle notification. La méthode `ShowNotification` est appelée à partir du gestionnaire d’événements attaché à l’événement `NotificationReceived` et insère un nouveau `Label` dans la page lorsque l’événement est appelé.

Pour plus d’informations sur Xamarin. Forms `DependencyService`, consultez [Xamarin. Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Créer l’implémentation de l’interface Android

Pour que l’application Xamarin. Forms envoie et reçoit des notifications sur Android, l’application doit fournir une implémentation de l’interface `INotificationManager`.

### <a name="create-the-androidnotificationmanager-class"></a>Créer la classe AndroidNotificationManager

La classe `AndroidNotificationManager` implémente l’interface `INotificationManager` :

```csharp
using Android.Support.V4.App;
using Xamarin.Forms;
using AndroidApp = Android.App.Application;

[assembly: Dependency(typeof(LocalNotifications.Droid.AndroidNotificationManager))]
namespace LocalNotifications.Droid
{
    public class AndroidNotificationManager : INotificationManager
    {
        const string channelId = "default";
        const string channelName = "Default";
        const string channelDescription = "The default channel for notifications.";
        const int pendingIntentId = 0;

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = -1;
        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            CreateNotificationChannel();
        }

        public int ScheduleNotification(string title, string message)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            messageId++;

            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId, intent, PendingIntentFlags.OneShot);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            var notification = builder.Build();
            manager.Notify(messageId, notification);

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message,
            };
            NotificationReceived?.Invoke(null, args);
        }

        void CreateNotificationChannel()
        {
            manager = (NotificationManager)AndroidApp.Context.GetSystemService(AndroidApp.NotificationService);

            if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
            {
                var channelNameJava = new Java.Lang.String(channelName);
                var channel = new NotificationChannel(channelId, channelNameJava, NotificationImportance.Default)
                {
                    Description = channelDescription
                };
                manager.CreateNotificationChannel(channel);
            }

            channelInitialized = true;
        }
    }
}
```

L’attribut `assembly` au-dessus de l’espace de noms inscrit l’implémentation de l’interface `INotificationManager` avec le `DependencyService`.

Android permet aux applications de définir plusieurs canaux pour les notifications. La méthode `Initialize` crée un canal de base que l’exemple d’application utilise pour envoyer des notifications. La méthode `ScheduleNotification` définit la logique propre à la plateforme requise pour créer et envoyer une notification. Enfin, la méthode `ReceiveNotification` est appelée par le système d’exploitation Android lorsqu’un message est reçu et appelle le gestionnaire d’événements.

> [!NOTE]
> La classe `Application` est définie à la fois dans les espaces de noms `Xamarin.Forms` et `Android.App`, de sorte que l’alias `AndroidApp` est défini dans les instructions `using` pour différencier les deux.

### <a name="handle-incoming-notifications-on-android"></a>Gérer les notifications entrantes sur Android

La classe `MainActivity` doit détecter les notifications entrantes et notifier l’instance `AndroidNotificationManager`. L’attribut `Activity` sur la classe `MainActivity` doit spécifier une valeur `LaunchMode` de `LaunchMode.SingleTop` :

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

Le mode `SingleTop` empêche le démarrage de plusieurs instances d’une `Activity` pendant que l’application est au premier plan. Ce `LaunchMode` peut ne pas convenir aux applications qui lancent plusieurs activités dans des scénarios de notification plus complexes. Pour plus d’informations sur les valeurs d’énumération `LaunchMode`, consultez [activité Android LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Dans la classe `MainActivity` est modifié pour recevoir les notifications entrantes :

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    // ...

    global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
    LoadApplication(new App());
    CreateNotificationFromIntent(Intent);
}

protected override void OnNewIntent(Intent intent)
{
    CreateNotificationFromIntent(intent);
}

void CreateNotificationFromIntent(Intent intent)
{
    if (intent?.Extras != null)
    {
        string title = intent.Extras.GetString(AndroidNotificationManager.TitleKey);
        string message = intent.Extras.GetString(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

La méthode `CreateNotificationFromIntent` extrait les données de notification à partir de l’argument `intent` et les fournit au `AndroidNotificationManager` à l’aide de la méthode `ReceiveNotification`. La méthode `CreateNotificationFromIntent` est appelée à partir de la méthode `OnCreate` et de la méthode `OnNewIntent` :

- Lorsque l’application est démarrée par les données de notification, les données `Intent` sont transmises à la méthode `OnCreate`.
- Si l’application est déjà au premier plan, les données `Intent` sont transmises à la méthode `OnNewIntent`.

Android offre de nombreuses options avancées pour les notifications. Pour plus d’informations, consultez [notifications dans Xamarin. Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Créer l’implémentation de l’interface iOS

Pour que l’application Xamarin. Forms envoie et reçoit des notifications sur iOS, l’application doit fournir une implémentation du `INotificationManager`.

### <a name="create-the-iosnotificationmanager-class"></a>Créer la classe iOSNotificationManager

La classe `iOSNotificationManager` implémente l’interface `INotificationManager` :

```csharp
[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = -1;

        bool hasNotificationsPermission;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            // request the permission to use local notifications
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert, (approved, err) =>
            {
                hasNotificationsPermission = approved;
            });
        }

        public int ScheduleNotification(string title, string message)
        {
            // EARLY OUT: app doesn't have permissions
            if(!hasNotificationsPermission)
            {
                return -1;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };

            // Local notifications can be time or location based
            // Create a time-based trigger, interval is in seconds and must be greater than 0
            var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message
            };
            NotificationReceived?.Invoke(null, args);
        }
    }
}
```

L’attribut `assembly` au-dessus de l’espace de noms inscrit l’implémentation de l’interface `INotificationManager` avec le `DependencyService`.

Sur iOS, vous devez demander l’autorisation d’utiliser les notifications avant de tenter de planifier une notification. La méthode `Initialize` demande l’autorisation d’utiliser des notifications locales. La méthode `ScheduleNotification` définit la logique requise pour créer et envoyer une notification. Enfin, la méthode `ReceiveNotification` sera appelée par iOS lors de la réception d’un message et appelle le gestionnaire d’événements.

### <a name="handle-incoming-notifications-on-ios"></a>Gérer les notifications entrantes sur iOS

Sur iOS, vous devez créer un délégué qui sous-classe `UNUserNotificationCenterDelegate` pour gérer les messages entrants. L’exemple d’application définit une classe `iOSNotificationReceiver` :

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        DependencyService.Get<INotificationManager>().ReceiveNotification(notification.Request.Content.Title, notification.Request.Content.Body);

        // alerts are always shown for demonstration but this can be set to "None"
        // to avoid showing alerts if the app is in the foreground
        completionHandler(UNNotificationPresentationOptions.Alert);
    }
}
```

Cette classe utilise la `DependencyService` pour récupérer une instance de la classe `iOSNotificationManager` et fournit des données de notification entrante à la méthode `ReceiveNotification`.

La classe `AppDelegate` doit spécifier le délégué personnalisé au démarrage de l’application. La classe `AppDelegate` doit spécifier un objet `iOSNotificationReceiver` comme délégué `UNUserNotificationCenter` lors du démarrage de l’application. Cela se produit dans la méthode `FinishedLaunching` :

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS offre de nombreuses options avancées pour les notifications. Pour plus d’informations, consultez [notifications dans Xamarin. iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Tester l’application

Une fois que les projets de plateforme contiennent une implémentation inscrite de l’interface `INotificationManager`, l’application peut être testée sur les deux plateformes. Exécutez l’application et cliquez sur le bouton **planifier la notification** pour créer une notification.

Sur Android, la notification s’affichera dans la zone de notification. Lorsque la notification est frappée, l’application reçoit la notification et affiche un message sous le bouton **planifier la notification** :

![Notifications locales sur Android](local-notifications-images/local-notifications-android.png)

Sur iOS, les notifications entrantes sont automatiquement reçues par l’application sans intervention de l’utilisateur. L’application reçoit la notification et affiche un message sous le bouton **planifier la notification** :

![Notifications locales sur iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Liens connexes

- [Exemple de projet](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notifications dans Xamarin. Android](~/android/app-fundamentals/notifications/index.md)
- [Notifications dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Dépendances Xamarin. Forms. service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
