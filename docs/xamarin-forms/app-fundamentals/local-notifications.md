---
title: Xamarin.Forms notifications locales
description: Cet article explique comment envoyer et recevoir des notifications locales dans Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72371570"
---
# <a name="local-notifications-in-xamarinforms"></a>Notifications locales dans Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Les notifications locales sont des alertes envoyées par des applications installées sur un appareil mobile. Les notifications locales sont souvent utilisées pour des fonctionnalités telles que :

- Événements de calendrier
- Rappels
- Déclencheurs basés sur l’emplacement

Chaque plate-forme gère différemment la création, l’affichage et la consommation de notifications locales. Cet article explique comment créer une abstraction multiplateforme pour envoyer et recevoir des notifications locales avec Xamarin.Forms.

[![Application de notifications locales sur iOS et Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Créer une interface multiplateforme

L’application Xamarin.Forms doit créer et consommer des notifications sans se soucier des implémentations de la plate-forme sous-jacente. L’interface suivante `INotificationManager` est implémentée dans la bibliothèque de code partagée et définit une API multiplateforme que l’application peut utiliser pour interagir avec les notifications :

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Cette interface sera mise en œuvre dans chaque projet de plate-forme. L’événement `NotificationReceived` permet à l’application de traiter les notifications entrantes. La `Initialize` méthode doit effectuer toute logique de plate-forme native nécessaire pour préparer le système de notification. La `ScheduleNotification` méthode doit envoyer une notification. La `ReceiveNotification` méthode doit être appelée par la plate-forme sous-jacente lorsqu’un message est reçu.

## <a name="consume-the-interface-in-xamarinforms"></a>Consommer l’interface dans Xamarin.Forms

Une fois qu’une interface a été créée, elle peut être consommée dans le projet partagé Xamarin.Forms, même si les implémentations de la plate-forme n’ont pas encore été créées. L’application échantillon `ContentPage` contient un appelé **MainPage.xaml** avec le contenu suivant:

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

La mise `Label` en page contient un `Button` élément avec des instructions pour l’utilisateur et un qui doit planifier une notification lorsqu’il est exploité.

Le `MainPage` code de classe sous-tend l’envoi et la réception des notifications :

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

Le `MainPage` constructeur de classe utilise le `DependencyService` Xamarin.Forms pour `INotificationManager`récupérer une instance spécifique à la plate-forme de la . La `OnScheduleClicked` méthode `INotificationManager` utilise l’instance pour planifier une nouvelle notification. La `ShowNotification` méthode est appelée à partir `NotificationReceived` du gestionnaire d’événements attaché à l’événement, et insérera un nouveau `Label` dans la page lorsque l’événement est invoqué.

Pour plus d’informations sur `DependencyService`les Xamarin.Forms , voir [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Créer l’implémentation de l’interface Android

Pour que l’application Xamarin.Forms envoie et reçoive des `INotificationManager` notifications sur Android, l’application doit fournir une implémentation de l’interface.

### <a name="create-the-androidnotificationmanager-class"></a>Créer la classe AndroidNotificationManager

La `AndroidNotificationManager` classe implémente l’interface `INotificationManager` :

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

L’attribut `assembly` ci-dessus de `INotificationManager` l’espace `DependencyService`de nom enregistre l’implémentation d’interface avec le .

Android permet aux applications de définir plusieurs canaux pour les notifications. La `Initialize` méthode crée un canal de base que l’application d’échantillon utilise pour envoyer des notifications. La `ScheduleNotification` méthode définit la logique spécifique à la plate-forme nécessaire pour créer et envoyer une notification. Enfin, `ReceiveNotification` la méthode est appelée par le système d’exploitation Android lorsqu’un message est reçu, et invoque le gestionnaire de l’événement.

> [!NOTE]
> La `Application` classe est définie `Xamarin.Forms` `Android.App` dans les espaces et les espaces de noms de sorte que l’alias `AndroidApp` est défini dans les `using` déclarations pour différencier les deux.

### <a name="handle-incoming-notifications-on-android"></a>Gérer les notifications entrantes sur Android

La `MainActivity` classe doit détecter les notifications entrantes et en aviser l’instance. `AndroidNotificationManager` L’attribut `Activity` `MainActivity` sur la `LaunchMode` classe `LaunchMode.SingleTop`doit spécifier une valeur de :

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

Le `SingleTop` mode empêche plusieurs `Activity` cas d’un d’être commencé pendant que l’application est au premier plan. Cela `LaunchMode` peut ne pas convenir aux applications qui lancent plusieurs activités dans des scénarios de notification plus complexes. Pour plus `LaunchMode` d’informations sur les valeurs de recensement, voir [Android Activity LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Dans `MainActivity` la classe est modifiée pour recevoir les notifications entrantes:

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

La `CreateNotificationFromIntent` méthode extrait les `intent` données de notification `AndroidNotificationManager` de `ReceiveNotification` l’argument et les fournit à l’utilisation de la méthode. La `CreateNotificationFromIntent` méthode est appelée `OnCreate` à `OnNewIntent` la fois de la méthode et de la méthode :

- Lorsque l’application est commencée `Intent` par des données `OnCreate` de notification, les données seront transmises à la méthode.
- Si l’application est déjà au `Intent` premier plan, les `OnNewIntent` données seront transmises à la méthode.

Android offre de nombreuses options avancées pour les notifications. Pour plus d’informations, voir [Notifications in Xamarin.Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Créer l’implémentation de l’interface iOS

Pour que la demande Xamarin.Forms envoie et reçoive des notifications sur iOS, l’application doit fournir une mise en œuvre de la `INotificationManager`.

### <a name="create-the-iosnotificationmanager-class"></a>Créer la classe iOSNotificationManager

La `iOSNotificationManager` classe implémente l’interface `INotificationManager` :

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

L’attribut `assembly` ci-dessus de `INotificationManager` l’espace `DependencyService`de nom enregistre l’implémentation d’interface avec le .

Sur iOS, vous devez demander la permission d’utiliser des notifications avant de tenter de planifier une notification. La `Initialize` méthode demande l’autorisation d’utiliser des notifications locales. La `ScheduleNotification` méthode définit la logique requise pour créer et envoyer une notification. Enfin, `ReceiveNotification` la méthode sera appelée par iOS lorsqu’un message est reçu et invoque le gestionnaire de l’événement.

### <a name="handle-incoming-notifications-on-ios"></a>Gérer les notifications entrantes sur iOS

Sur iOS, vous devez créer un `UNUserNotificationCenterDelegate` délégué qui sous-classe pour gérer les messages entrants. L’exemple d’application définit une `iOSNotificationReceiver` classe :

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

Cette classe `DependencyService` utilise le pour `iOSNotificationManager` obtenir une instance de `ReceiveNotification` la classe et fournit des données de notification entrantes à la méthode.

La `AppDelegate` classe doit spécifier le délégué personnalisé lors du démarrage de l’application. La `AppDelegate` classe doit `iOSNotificationReceiver` spécifier un objet en tant que délégué lors du démarrage de l’application. `UNUserNotificationCenter` Cela se `FinishedLaunching` produit dans la méthode:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS offre de nombreuses options avancées pour les notifications. Pour plus d’informations, voir [Notifications in Xamarin.iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Test de l’application

Une fois que les projets `INotificationManager` de plate-forme contiennent une implémentation enregistrée de l’interface, l’application peut être testée sur les deux plates-formes. Exécutez l’application et cliquez sur le bouton **Notification horaire** pour créer une notification.

Sur Android, la notification apparaîtra dans la zone de notification. Lorsque la notification est mise sur écoute, l’application reçoit la notification et affiche un message sous le bouton **Notification de l’annexe** :

![Notifications locales sur Android](local-notifications-images/local-notifications-android.png)

Sur iOS, les notifications entrantes sont automatiquement reçues par l’application sans nécessiter l’entrée de l’utilisateur. L’application reçoit la notification et affiche un message sous le bouton **Notification de l’annexe** :

![Notifications locales sur iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Liens connexes

- [Exemple de projet](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notifications dans Xamarin.Android](~/android/app-fundamentals/notifications/index.md)
- [Notifications dans Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms Dependency.Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
