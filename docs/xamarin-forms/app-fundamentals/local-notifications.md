---
title: Xamarin.Forms notifications locales
description: Cet article explique comment envoyer, planifier et recevoir des notifications locales dans Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/03/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5009e74bac79ad6a560bd4ab1cb039a74d8b49be
ms.sourcegitcommit: 342cfbd2502ad92cadada4fa9aec669b99d7830a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96604527"
---
# <a name="local-notifications-in-no-locxamarinforms"></a>Notifications locales dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/local-notifications)

Les notifications locales sont des alertes envoyées par les applications installées sur un appareil mobile. Les notifications locales sont souvent utilisées pour des fonctionnalités telles que :

- Événements de calendrier
- Rappels
- Déclencheurs basés sur l’emplacement

Chaque plateforme gère la création, l’affichage et la consommation des notifications locales différemment. Cet article explique comment créer une abstraction multiplateforme pour envoyer, planifier et recevoir des notifications locales avec Xamarin.Forms .

[![Application de notifications locales sur iOS et Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Créer une interface multiplateforme

L' Xamarin.Forms application doit créer et consommer des notifications sans se préoccuper des implémentations de la plateforme sous-jacente. L' `INotificationManager` interface suivante est implémentée dans la bibliothèque de code partagée et définit une API multiplateforme que l’application peut utiliser pour interagir avec les notifications :

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;
    void Initialize();
    void SendNotification(string title, string message, DateTime? notifyTime = null);
    void ReceiveNotification(string title, string message);
}
```

Cette interface sera implémentée dans chaque projet de plateforme. L' `NotificationReceived` événement permet à l’application de gérer les notifications entrantes. La `Initialize` méthode doit exécuter toute logique de plateforme Native nécessaire pour préparer le système de notification. La `SendNotification` méthode doit envoyer une notification, au niveau d’un facultatif `DateTime` . La `ReceiveNotification` méthode doit être appelée par la plateforme sous-jacente lors de la réception d’un message.

## <a name="consume-the-interface-in-no-locxamarinforms"></a>Utiliser l’interface dans Xamarin.Forms

Une fois qu’une interface a été créée, elle peut être consommée dans le projet partagé, Xamarin.Forms même si les implémentations de plateforme n’ont pas encore été créées. L’exemple d’application contient un `ContentPage` appelé **MainPage. Xaml** avec le contenu suivant :

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button below to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnSendClick" />
    <Label Text="Click the button below to schedule a local notification for in 10 seconds time."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick" />
</StackLayout>
```

La disposition contient `Label` des éléments qui expliquent les instructions et les `Button` éléments qui envoient ou planifient une notification lorsqu’ils sont appuyés.

La `MainPage` classe code-behind gère l’envoi et la réception des notifications :

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

    void OnSendClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.SendNotification(title, message);
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.SendNotification(title, message, DateTime.Now.AddSeconds(10));
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

Le `MainPage` constructeur de classe utilise Xamarin.Forms `DependencyService` pour récupérer une instance spécifique à la plateforme de `INotificationManager` . Les `OnSendClick` `OnScheduleClicked` méthodes et utilisent l' `INotificationManager` instance pour envoyer et planifier de nouvelles notifications. La `ShowNotification` méthode est appelée à partir du gestionnaire d’événements attaché à l' `NotificationReceived` événement et insère un nouveau `Label` dans la page lorsque l’événement est appelé.

Le `NotificationReceived` Gestionnaire d’événements effectue un cast de ses arguments d’événement vers `NotificationEventArgs` . Ce type est défini dans le Xamarin.Forms projet partagé :

```csharp
public class NotificationEventArgs : EventArgs
{
    public string Title { get; set; }
    public string Message { get; set; }
}
```

Pour plus d’informations sur le Xamarin.Forms `DependencyService` , consultez [ Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Créer l’implémentation de l’interface Android

Pour que l' Xamarin.Forms application envoie et reçoit des notifications sur Android, l’application doit fournir une implémentation de l' `INotificationManager` interface.

### <a name="create-the-androidnotificationmanager-class"></a>Créer la classe AndroidNotificationManager

La `AndroidNotificationManager` classe implémente l' `INotificationManager` interface :

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Graphics;
using Android.OS;
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

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = 0;
        int pendingIntentId = 0;

        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public static AndroidNotificationManager Instance { get; private set; }

        public void Initialize()
        {
            CreateNotificationChannel();
            Instance = this;
        }

        public void SendNotification(string title, string message, DateTime? notifyTime = null)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            if (notifyTime != null)
            {
                Intent intent = new Intent(AndroidApp.Context, typeof(AlarmHandler));
                intent.PutExtra(TitleKey, title);
                intent.PutExtra(MessageKey, message);

                PendingIntent pendingIntent = PendingIntent.GetBroadcast(AndroidApp.Context, pendingIntentId++, intent, PendingIntentFlags.CancelCurrent);
                long triggerTime = GetNotifyTime(notifyTime.Value);
                AlarmManager alarmManager = AndroidApp.Context.GetSystemService(Context.AlarmService) as AlarmManager;
                alarmManager.Set(AlarmType.RtcWakeup, triggerTime, pendingIntent);
            }
            else
            {
                Show(title, message);
            }
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

        public void Show(string title, string message)
        {
            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId++, intent, PendingIntentFlags.UpdateCurrent);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            Notification notification = builder.Build();
            manager.Notify(messageId++, notification);
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

        long GetNotifyTime(DateTime notifyTime)
        {
            DateTime utcTime = TimeZoneInfo.ConvertTimeToUtc(notifyTime);
            double epochDiff = (new DateTime(1970, 1, 1) - DateTime.MinValue).TotalSeconds;
            long utcAlarmTime = utcTime.AddSeconds(-epochDiff).Ticks / 10000;
            return utcAlarmTime; // milliseconds
        }
    }
}
```

L' `assembly` attribut situé au-dessus de l’espace de noms inscrit l' `INotificationManager` implémentation de l’interface avec `DependencyService` .

Android permet aux applications de définir plusieurs canaux pour les notifications. La `Initialize` méthode crée un canal de base utilisé par l’exemple d’application pour envoyer des notifications. La `SendNotification` méthode définit la logique propre à la plateforme requise pour créer et envoyer une notification. La `ReceiveNotification` méthode est appelée par le système d’exploitation Android lorsqu’un message est reçu et appelle le gestionnaire d’événements.

La `SendNotification` méthode crée immédiatement une notification locale, ou à un exact `DateTime` . Une notification peut être planifiée pour une `DateTime` utilisation exacte de la `AlarmManager` classe, et la notification est reçue par un objet qui dérive de la `BroadcastReceiver` classe :

```csharp
[BroadcastReceiver(Enabled = true, Label = "Local Notifications Broadcast Receiver")]
public class AlarmHandler : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        if (intent?.Extras != null)
        {
            string title = intent.GetStringExtra(AndroidNotificationManager.TitleKey);
            string message = intent.GetStringExtra(AndroidNotificationManager.MessageKey);

            AndroidNotificationManager.Instance.Show(title, message);
        }
    }
}
```

> [!IMPORTANT]
> Par défaut, les notifications planifiées à l’aide de la `AlarmManager` classe ne survivront pas au redémarrage de l’appareil. Toutefois, vous pouvez concevoir votre application pour qu’elle replanifie automatiquement les notifications si l’appareil est redémarré. Pour plus d’informations, consultez [Démarrer une alarme quand l’appareil redémarre](https://developer.android.com/training/scheduling/alarms#boot) dans [planifier des alarmes répétées](https://developer.android.com/training/scheduling/alarms) sur Developer.Android.com. Pour plus d’informations sur le traitement en arrière-plan sur Android, consultez [Guide du traitement en arrière-plan](https://developer.android.com/guide/background) sur Developer.Android.com.

Pour plus d’informations sur les récepteurs de diffusion, consultez [récepteurs de diffusion dans Xamarin. Android](~/android/app-fundamentals/broadcast-receivers.md).

### <a name="handle-incoming-notifications-on-android"></a>Gérer les notifications entrantes sur Android

La `MainActivity` classe doit détecter les notifications entrantes et notifier l' `AndroidNotificationManager` instance. L' `Activity` attribut sur la `MainActivity` classe doit spécifier la `LaunchMode` valeur `LaunchMode.SingleTop` :

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

Le `SingleTop` mode empêche le démarrage de plusieurs instances d’un `Activity` pendant que l’application est au premier plan. Cela `LaunchMode` peut ne pas convenir aux applications qui lancent plusieurs activités dans des scénarios de notification plus complexes. Pour plus d’informations sur les `LaunchMode` valeurs d’énumération, voir [Android Activity LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Dans, la `MainActivity` classe est modifiée pour recevoir les notifications entrantes :

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
        string title = intent.GetStringExtra(AndroidNotificationManager.TitleKey);
        string message = intent.GetStringExtra(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

La `CreateNotificationFromIntent` méthode extrait les données de notification de l' `intent` argument et les fournit au `AndroidNotificationManager` à l’aide de la `ReceiveNotification` méthode. La `CreateNotificationFromIntent` méthode est appelée à partir de la `OnCreate` méthode et de la `OnNewIntent` méthode :

- Lorsque l’application est démarrée par les données de notification, les `Intent` données sont transmises à la `OnCreate` méthode.
- Si l’application est déjà au premier plan, les `Intent` données sont passées à la `OnNewIntent` méthode.

Android offre de nombreuses options avancées pour les notifications. Pour plus d’informations, consultez [notifications dans Xamarin. Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Créer l’implémentation de l’interface iOS

Pour que l' Xamarin.Forms application envoie et reçoit des notifications sur iOS, l’application doit fournir une implémentation du `INotificationManager` .

### <a name="create-the-iosnotificationmanager-class"></a>Créer la classe iOSNotificationManager

La `iOSNotificationManager` classe implémente l' `INotificationManager` interface :

```csharp
using System;
using Foundation;
using UserNotifications;
using Xamarin.Forms;

[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = 0;
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

        public void SendNotification(string title, string message, DateTime? notifyTime = null)
        {
            // EARLY OUT: app doesn't have permissions
            if (!hasNotificationsPermission)
            {
                return;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };            

            UNNotificationTrigger trigger;
            if (notifyTime != null)
            {
                // Create a calendar-based trigger.
                trigger = UNCalendarNotificationTrigger.CreateTrigger(GetNSDateComponents(notifyTime.Value), false);
            }
            else
            {
                // Create a time-based trigger, interval is in seconds and must be greater than 0.
                trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);
            }                      

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });
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

        NSDateComponents GetNSDateComponents(DateTime dateTime)
        {
            return new NSDateComponents
            {
                Month = dateTime.Month,
                Day = dateTime.Day,
                Year = dateTime.Year,
                Hour = dateTime.Hour,
                Minute = dateTime.Minute,
                Second = dateTime.Second
            };
        }
    }
}
```

L' `assembly` attribut situé au-dessus de l’espace de noms inscrit l' `INotificationManager` implémentation de l’interface avec `DependencyService` .

Sur iOS, vous devez demander l’autorisation d’utiliser les notifications avant de tenter de planifier une notification. La `Initialize` méthode demande l’autorisation d’utiliser des notifications locales. La `SendNotification` méthode définit la logique requise pour créer et envoyer une notification. La `ReceiveNotification` méthode est appelée par IOS lors de la réception d’un message et appelle le gestionnaire d’événements.

> [!NOTE]
> La `SendNotification` méthode crée immédiatement une notification locale, à l’aide d’un `UNTimeIntervalNotificationTrigger` objet ou à `DateTime` l’aide d’un `UNCalendarNotificationTrigger` objet exactement.

### <a name="handle-incoming-notifications-on-ios"></a>Gérer les notifications entrantes sur iOS

Sur iOS, vous devez créer un délégué qui sous-classe `UNUserNotificationCenterDelegate` pour gérer les messages entrants. L’exemple d’application définit une `iOSNotificationReceiver` classe :

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

Cette classe utilise `DependencyService` pour recevoir une instance de la `iOSNotificationManager` classe et fournit des données de notification entrante à la `ReceiveNotification` méthode.

La `AppDelegate` classe doit spécifier le délégué personnalisé au démarrage de l’application. La `AppDelegate` classe doit spécifier un `iOSNotificationReceiver` objet en tant que `UNUserNotificationCenter` délégué au démarrage de l’application. Cela se produit dans la `FinishedLaunching` méthode :

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

## <a name="test-the-application"></a>Test de l’application

Une fois que les projets de plateforme contiennent une implémentation inscrite de l' `INotificationManager` interface, l’application peut être testée sur les deux plateformes. Exécutez l’application et cliquez sur l’un des boutons **créer une notification** pour créer une notification.

Sur Android, les notifications s’affichent dans la zone de notification. Lorsque la notification est exploitée, l’application reçoit la notification et affiche un message :

![Notifications locales sur Android](local-notifications-images/local-notifications-android.png)

Sur iOS, les notifications entrantes sont automatiquement reçues par l’application sans intervention de l’utilisateur. L’application reçoit la notification et affiche un message :

![Notifications locales sur iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Liens connexes

- [Exemple de projet](/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notifications dans Xamarin. Android](~/android/app-fundamentals/notifications/index.md)
- [Récepteurs de diffusion dans Xamarin. Android](~/android/app-fundamentals/broadcast-receivers.md)
- [Notifications dans Xamarin. iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
