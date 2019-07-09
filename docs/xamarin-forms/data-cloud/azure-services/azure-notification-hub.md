---
title: Envoyer et recevoir des notifications push avec Azure Notification Hubs et Xamarin.Forms
description: Cet article explique comment utiliser Azure Notification Hubs pour envoyer des notifications de push multiplateforme pour les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: 474398922bf00e3a430166d8b2e073d200e6ed6e
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659326"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Envoyer et recevoir des notifications push avec Azure Notification Hubs et Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png)télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureNotificationHub)

Informations de remise de notifications push à partir d’un système principal à une application mobile. Apple, Google et autres plateformes ont leur propre Service de Notification Push (PNS). Azure Notification Hubs permettent de centraliser les notifications sur les plateformes afin que votre application back-end peut communiquer avec un hub unique, qui prend en charge de la distribution des notifications à chaque PNS propres à la plateforme.

Intégrer Azure Notification Hubs dans des applications mobiles en suivant ces étapes :

1. [Configurer les Services de Notification Push et Azure Notification Hub](#set-up-push-notification-services-and-azure-notification-hub).
1. [Comprendre comment utiliser des modèles et des balises](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Créer une application de Xamarin.Forms multiplateforme](#xamarinforms-application-functionality).
1. [Configurer le projet Android natif pour les notifications push](#configure-the-android-application-for-notifications).
1. [Configurer le projet iOS native pour les notifications push](#configure-ios-for-notifications).
1. [Tester les notifications à l’aide d’Azure Notification Hub](#test-notifications-in-the-azure-portal).
1. [Créer une application principale pour envoyer des notifications](#create-a-notification-dispatcher).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurer les Services de Notification Push et Azure Notification Hub

L’intégration d’Azure Notification Hubs avec une application mobile Xamarin.Forms est similaire à l’intégration d’Azure Notification Hubs dans une application native Xamarin. Configurer un **application de FCM** en suivant les étapes de la Console Firebase dans [des notifications Push à Xamarin.Android à l’aide d’Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Procédez comme suit à l’aide du didacticiel de Xamarin.Android :

1. Définir un nom de package Android comme `com.xamarin.notifysample`, qui est utilisé dans l’exemple.
1. Télécharger **google-services.JSON** à partir de la console Firebase. Vous allez ajouter ce fichier à votre application Android dans une étape ultérieure.
1. Créer une instance d’Azure Notification Hub et lui donner un nom. Utilisation de cet article et exemple `xdocsnotificationhub` en tant que le nom du concentrateur.
1. Copiez le FCM **clé serveur** et enregistrez-le sous le **clé API** sous **Google (GCM/FCM)** dans votre Hub de Notification Azure.

La capture d’écran suivante montre la configuration de la plateforme Google dans le Hub de Notification Azure :

![Capture d’écran de la configuration d’Azure Notification Hub Google](azure-notification-hub-images/fcm-notification-hub-config.png "configuration d’Azure Notification Hub Google")

Vous aurez besoin d’un ordinateur macOS pour terminer l’installation pour les appareils iOS. Configurer APNS en suivant les étapes initial dans [des notifications Push à Xamarin.iOS à l’aide d’Azure Notification Hubs](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Procédez comme suit à l’aide du didacticiel de Xamarin.iOS :

1. Définir un identificateur d’offre groupée iOS. Utilisation de cet article et exemple `com.xamarin.notifysample` en tant que l’identificateur de bundle.
1. Créer un fichier de demande de signature de certificat (CSR) et l’utiliser pour générer un certificat de notification push.
1. Télécharger le certificat de notification push sous **Apple (APNS)** dans votre Hub de Notification Azure.

La capture d’écran suivante montre la configuration de plateforme Apple dans le Hub de Notification Azure :

![Capture d’écran de la configuration d’Azure Notification Hub Apple](azure-notification-hub-images/apns-notification-hub-config.png "configuration Azure Notification Hub Apple")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Inscrire des modèles et des balises avec Azure Notification Hub

Hub de Notification Azure requiert des applications mobiles à inscrire auprès du hub, de définir des modèles et de s’abonner aux balises. L’inscription lie un handle PNS propres à la plateforme à un identificateur dans le Hub de Notification Azure. Pour en savoir plus sur les inscriptions, consultez [gestion des inscriptions](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Modèles permettent de spécifier des modèles de message paramétrable, les appareils. Les messages entrants peuvent être personnalisés par appareil, par balise. Pour en savoir plus sur les modèles, consultez [modèles](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Balises peuvent être utilisées pour vous abonner aux catégories de messages tels que des actualités, sport et la météo. Par souci de simplicité, l’exemple d’application définit un modèle par défaut avec un paramètre unique appelé `messageParam` et une balise unique appelé `default`. Dans les systèmes plus complexes, les balises spécifiques à l’utilisateur permet à un utilisateur de message sur des appareils pour des notifications personnalisées. Pour en savoir plus sur les balises, consultez [routage et expressions de balise](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Pour recevoir des messages, chaque application native doit effectuer ces étapes :

1. Obtenir un Handle PNS ou un jeton à partir de la plateforme PNS.
1. Inscrire le Handle PNS auprès d’Azure Notification Hub.
1. Spécifiez un modèle qui contient les mêmes paramètres que les messages sortants.
1. S’abonner à la balise ciblée par les messages sortants.

Ces étapes sont décrites plus en détail pour chaque plateforme dans le [configurer l’application Android pour les notifications](#configure-the-android-application-for-notifications) et [configurer iOS pour les notifications](#configure-ios-for-notifications) sections.

## <a name="xamarinforms-application-functionality"></a>Fonctionnalités de l’application Xamarin.Forms

L’exemple d’application Xamarin.Forms affiche une liste de messages de notification push. Cela est possible avec la `AddMessage` (méthode), qui ajoute le message de notification push spécifié à l’interface utilisateur. Cette méthode empêche l’ajout à l’interface utilisateur de messages en double et s’exécute sur le thread principal, il peut donc être appelée à partir de n’importe quel thread. Le code suivant montre la méthode `AddMessage` :

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

L’exemple d’application contienne un **AppConstants.cs** fichier qui définit les propriétés utilisées par les projets de plateforme. Ce fichier doit être personnalisée avec des valeurs à partir de votre Hub de Notification Azure. Le code suivant illustre la **AppConstants.cs** fichier :

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

Personnaliser les valeurs suivantes dans `AppConstants` pour se connecter à l’exemple d’application à votre Hub de Notification Azure :

* `NotificationHubName`: Utilisez le nom du Hub de Notification Azure créées dans votre portail Azure.
* `ListenConnectionString`: Cette valeur se trouve dans le Hub de Notification Azure sous **des stratégies d’accès**.

La capture d’écran montre où ces valeurs se trouvent dans le portail Azure :

![Capture d’écran de la stratégie d’accès Azure Notification Hub](azure-notification-hub-images/notification-hub-access-policy.png "stratégie d’accès Azure Notification Hub")

## <a name="configure-the-android-application-for-notifications"></a>Configurer l’application Android pour les notifications

Procédez comme suit pour configurer l’application Android pour recevoir et traiter les notifications :

1. Configurer le Android **nom_package** doit correspondre au nom de package dans la console Firebase.
1. Installer les packages NuGet suivants pour interagir avec Google Play, Firebase et Azure Notification Hubs :
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging.
    1. Xamarin.Azure.NotificationHubs.Android.
1. Copie le `google-services.json` fichier que vous avez téléchargé pendant l’installation FCM au projet et définissez l’Action de génération `GoogleServicesJson`.
1. [Configurer AndroidManifest.xml pour communiquer avec Firebase](#configure-android-manifest).
1. [L’application auprès de Firebase et Azure Notification Hub à l’aide un `FirebaseInstanceIdService` ](#register-using-a-custom-firebaseinstanceidservice).
1. [Traiter les messages avec un `FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice).
1. [Ajout de notifications entrantes à l’interface utilisateur Xamarin.Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> Le **GoogleServicesJson** build action fait partie de la **Xamarin.GooglePlayServices.Base** package NuGet. Visual Studio 2019 définit les Actions de génération disponibles lors du démarrage. Si vous ne voyez pas **GoogleServicesJson** comme une Action de génération, redémarrez Visual Studio 2019 après avoir installé les packages NuGet.

### <a name="configure-android-manifest"></a>Configurer le manifeste Android

Le `receiver` éléments au sein du `application` élément autorise l’application communiquer avec Firebase. Le `uses-permission` éléments permettent à l’application gérer les messages et l’inscrire auprès d’Azure Notification Hub. L’ensemble **AndroidManifest.xml** doit ressembler à l’exemple ci-dessous :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>S’inscrire à l’aide d’un FirebaseInstanceIdService personnalisé

Firebase émet des jetons qui identifient de façon unique un périphérique sur le PNS. Les jetons ont une durée de vie longue, mais sont parfois actualisées. Lors de l’émission ou l’actualisation d’un jeton, l’application doit inscrire son nouveau jeton auprès d’Azure Notification Hub. L’inscription est gérée par une instance d’une classe qui dérive de `FirebaseInstanceIdService`.

Dans l’exemple d’application `FirebaseRegistrationService` hérite de la classe `FirebaseInstanceIdService`. Cette classe a un `IntentFilter` qui inclut `com.google.firebase.INSTANCE_ID_EVENT`, ce qui permet au système d’exploitation Android appellent automatiquement `OnTokenRefresh` quand un jeton émis par Firebase.

Le code suivant montre le personnalisé `FirebaseInstanceIdService` à partir de l’exemple d’application :

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

Le `SendRegistrationToServer` méthode dans le `FirebaseRegistrationClass` inscrit l’appareil auprès du Hub de Notification Azure et s’abonne aux balises avec un modèle. L’exemple d’application définit une balise unique appelée `default` et un modèle avec un paramètre unique appelé `messageParam` dans le **AppConstants.cs** fichier. Pour plus d’informations sur l’inscription, les balises et les modèles, consultez [inscrire les balises et les modèles avec Azure Notification Hub](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>Traiter des messages avec un FirebaseMessagingService

Les messages entrants sont acheminés vers un `FirebaseMessagingService` instance, où ils peuvent être convertis en une notification locale. Le projet Android dans l’exemple d’application contient une classe appelée `FirebaseService` qui hérite de `FirebaseMessagingService`. Cette classe a un `IntentFilter` qui inclut `com.google.firebase.MESSAGING_EVENT`, ce qui permet au système d’exploitation Android appellent automatiquement `OnMessageReceived` lors de la réception d’un message de notification push.

L’exemple suivant montre le `FirebaseService` à partir de l’exemple d’application :

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    public override void OnMessageReceived(RemoteMessage message)
    {
        base.OnMessageReceived(message);
        string messageBody = string.Empty;

        if (message.GetNotification() != null)
        {
            messageBody = message.GetNotification().Body;
        }

        // NOTE: test messages sent via the Azure portal will be received here
        else
        {
            messageBody = message.Data.Values.First();
        }

        // convert the incoming message to a local notification
        SendLocalNotification(messageBody);

        // send the incoming message directly to the MainPage
        SendMessageToMainPage(messageBody);
    }

    void SendLocalNotification(string body)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        intent.PutExtra("message", body);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetContentTitle("XamarinNotify Message")
            .SetSmallIcon(Resource.Drawable.ic_launcher)
            .SetContentText(body)
            .SetAutoCancel(true)
            .SetShowWhen(false)
            .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
        }

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }

    void SendMessageToMainPage(string body)
    {
        (App.Current.MainPage as MainPage)?.AddMessage(body);
    }
}
```

Les messages entrants sont convertis en une notification locale avec le `SendLocalNotification` (méthode). Cette méthode crée un nouveau `Intent` et place le message contenu dans le `Intent` comme un `string` `Extra`. Lorsque l’utilisateur appuie sur la notification locale, si l’application se trouve dans le premier plan ou d’arrière-plan, le `MainActivity` est lancé et a accès au contenu du message via le `Intent` objet.

La notification locale et `Intent` exemple oblige l’utilisateur d’effectuer l’action de clic avec sur la notification. Ceci est souhaitable lorsque l’utilisateur doit agir avant les changements d’état de l’application. Toutefois, vous souhaiterez accéder aux données du message sans nécessiter une action de l’utilisateur dans certains cas. L’exemple précédent envoie également le message directement à l’actuel `MainPage` instance avec le `SendMessageToMainPage` (méthode). En production, si vous implémentez les deux méthodes pour un seul type de message, le `MainPage` objet obtiendra des messages en double si l’utilisateur appuie sur la notification.

> [!NOTE]
> L’application Android sera uniquement recevoir des notifications push s’il s’exécute en arrière-plan ou de premier plan. Pour recevoir des notifications push lors de la main `Activity` est ne pas en cours d’exécution, vous devez implémenter un service, ce qui dépasse le cadre de cet exemple. Pour plus d’informations, consultez [création de Services Android](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Ajout de notifications entrantes à l’interface utilisateur Xamarin.Forms

Le `MainActivity` classe a besoin d’obtenir l’autorisation nécessaire pour gérer les notifications et de gérer les données de message entrant. Le code suivant montre l’ensemble `MainActivity` implémentation :

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (IsPlayServiceAvailable() == false)
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

Le `Activity` attribut définit l’application `LaunchMode` à `SingleTop`. Ce mode de lancement indique le système d’exploitation Android afin d’autoriser uniquement une seule instance de cette activité. Avec ce mode de lancement, entrant `Intent` données sont acheminées vers le `OnNewIntent` (méthode), qui extrait des données de message et l’envoie à la `MainPage` instance via le `AddMessage` (méthode). Si votre application utilise un mode de lancement différents, il doit gérer `Intent` données différemment.

Le `OnCreate` méthode utilise une méthode d’assistance appelée `IsPlayServiceAvailable` pour vous assurer de l’appareil prend en charge un Service Google Play. Émulateurs ou périphériques qui ne prennent pas en charge le Service Google Play ne peut pas recevoir des notifications push à partir de Firebase.

## <a name="configure-ios-for-notifications"></a>Configurer iOS pour les notifications

Le processus de configuration de l’application iOS de recevoir des notifications est :

1. Configurer le **identificateur de Bundle** dans le **Info.plist** fichier pour correspondre à la valeur utilisée dans le profil de provisionnement.
1. Ajouter le **activer les Notifications Push** option le **Entitlements.plist** fichier.
1. Ajouter le **Xamarin.Azure.NotificationHubs.iOS** package NuGet à votre projet.
1. [S’inscrire aux notifications avec APNS](#register-for-notifications-with-apns).
1. [Inscrire l’application avec Azure Notification Hub et vous abonner aux balises](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Ajouter des notifications APNS à l’interface utilisateur Xamarin.Forms](#add-apns-notifications-to-xamarinforms-ui).

La capture d’écran suivante montre le **activer les Notifications Push** option sélectionnée dans le **Entitlements.plist** fichier dans Visual Studio :

![Capture d’écran du droit de Notifications Push](azure-notification-hub-images/push-notification-entitlement.png "droit des notifications de Push")

### <a name="register-for-notifications-with-apns"></a>Inscription aux notifications avec APNS

Le `FinishedLaunching` méthode dans le **AppDelegate.cs** fichier doit être substitué pour s’inscrire aux notifications à distance. L’inscription diffère selon la version iOS utilisée sur l’appareil. Le projet iOS dans l’exemple d’application remplace le `FinishedLaunching` méthode à appeler `RegisterForRemoteNotifications` comme indiqué dans l’exemple suivant :

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Inscrire auprès d’Azure Notification Hub et vous abonner aux balises

Quand l’appareil a été enregistré pour les notifications à distance pendant la `FinishedLaunching` (méthode), iOS appellera le `RegisteredForRemoteNotifications` (méthode). Cette méthode doit être substituée pour effectuer les actions suivantes :

1. Instanciez le `SBNotificationHub`.
1. Annuler l’inscription des enregistrements existants.
1. Inscrire l’appareil avec votre hub de notification.
1. S’abonner à des balises spécifiques avec un modèle.

Pour plus d’informations sur l’inscription de l’appareil, les modèles et les balises, consultez [inscrire les balises et les modèles avec Azure Notification Hub](#register-templates-and-tags-with-the-azure-notification-hub). Le code suivant illustre l’inscription de l’appareil et les modèles :

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> L’inscription aux notifications à distance peut échouer dans des situations telles qu’aucune connexion réseau. Vous pouvez choisir de remplacer le `FailedToRegisterForRemoveNotifications` méthode pour gérer l’échec de l’inscription.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Ajouter des notifications APNS à l’interface utilisateur Xamarin.Forms

Quand un appareil reçoit une notification à distance, iOS appelle le `ReceivedRemoteNotification` (méthode). Message entrant JSON est converti en un `NSDictionary` objet et le `ProcessNotification` méthode extrait les valeurs du dictionnaire et les envoie à la Xamarin.Forms `MainPage` instance. Le `ReceivedRemoteNotifications` méthode est substituée pour appeler `ProcessNotification` comme indiqué dans le code suivant :

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>Tester les notifications dans le portail Azure

Azure Notification Hubs permettent de vérifier que votre application peut recevoir des messages de test. Le **Test d’envoi** section dans le hub de notification vous permet de choisir la plate-forme cible et envoyer un message. Définition de la **envoyer à l’expression de balise** à `default` enverra les messages aux applications qui ont inscrit un modèle pour le `default` balise. En cliquant sur le **envoyer** bouton génère un rapport qui inclut le nombre d’appareils atteint avec le message. La capture d’écran suivante montre un test de notification Android dans le portail Azure :

![Capture d’écran d’un message de test Azure Notification Hub](azure-notification-hub-images/azure-notification-hub-test-send.png "message de test Azure Notification Hub")

### <a name="testing-tips"></a>Conseils pour le test

1. Lorsque vous testez une application peut recevoir des notifications push, vous devez utiliser un appareil physique. Appareils virtuels Android et iOS ne peuvent pas être configurés correctement pour recevoir des notifications push.
1. L’exemple d’application Android inscrit son jeton et les modèles une fois lorsque le jeton Firebase est émis. Pendant le test, vous devrez demander un nouveau jeton et les réinscrire auprès du Hub de Notification Azure. La meilleure façon d’imposer l’opération consiste à nettoyer votre projet, supprimez le `bin` et `obj` dossiers et désinstaller l’application à partir de l’appareil avant la génération et le déploiement.
1. De nombreux éléments de flux de la notification push sont exécutées de façon asynchrone. Cela peut entraîner des points d’arrêt n’est pas atteinte ou en cours d’accès dans un ordre inattendu. Utilisez la journalisation de périphérique ou de débogage pour suivre l’exécution sans interrompre le flux de l’application. Filtrer le journal d’appareil Android à l’aide du `DebugTag` spécifié dans `Constants`.

## <a name="create-a-notification-dispatcher"></a>Créer un répartiteur de notification

Azure Notification Hubs activer votre application back-end distribuer des notifications aux appareils sur plusieurs plateformes. L’exemple illustre l’envoi de notification avec le **NotificationDispatcher** application console. L’application inclut le **DispatcherConstants.cs** fichier qui définit les propriétés suivantes :

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Vous devez configurer le **DispatcherConstants.cs** pour faire correspondre correspondent à votre configuration d’Azure Notification Hub. La valeur de la `SubscriptionTags` propriété doit correspondre à des valeurs utilisées dans les applications clientes. Le `NotificationHubName` propriété correspond au nom de votre instance Azure Notification Hub. Le `FullAccessConnectionString` propriété est la clé d’accès trouvée dans votre notification hub **des stratégies d’accès**. La capture d’écran suivante montre l’emplacement de la `NotificationHubName` et `FullAccessConnectionString` propriétés dans le portail Azure :

![Capture d’écran du nom de Hub de Notification Azure et FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "FullAccessConnectionString et nom de Hub de Notification Azure")

L’application de console effectue une itération sur chaque `SubscriptionTags` valeur et envoie des notifications aux abonnés à l’aide d’une instance de la `NotificationHubClient` classe. Le code suivant illustre l’application de console `Program` classe :

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

Lorsque l’exemple d’application console est exécutée, la barre d’espace peut être enfoncé pour envoyer des messages. Appareils exécutant le client applications doivent recevoir des notifications numérotées, fourni qu’ils sont correctement configurés.

## <a name="related-links"></a>Liens connexes

* [Modèles de Notification de push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Gestion de l’inscription de périphérique](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Routage et Expressions de balises](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Xamarin.Android Azure Notification Hubs didacticiel](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Xamarin.iOS Azure Notification Hubs didacticiel](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
