---
title: Envoyer et recevoir des notifications Push avec Azure Notification Hubs etXamarin.Forms
description: Cet article explique comment utiliser Azure Notification Hubs pour envoyer des notifications push interplateformes à des Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- Firebase
ms.openlocfilehash: 5fd657a3d55bd26b95e79e39540dcfe5b8bce08f
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918586"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-no-locxamarinforms"></a>Envoyer et recevoir des notifications Push avec Azure Notification Hubs etXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

Les notifications push fournissent des informations à partir d’un système principal vers une application mobile. Apple, Google et d’autres plateformes disposent chacune de leur propre service de notification push (PNS). Azure Notification Hubs vous permet de centraliser les notifications sur les plateformes afin que votre application principale puisse communiquer avec un concentrateur unique, ce qui prend en charge la distribution des notifications à chaque PNS spécifique à la plateforme.

Intégrez Azure Notification Hubs dans Mobile Apps en procédant comme suit :

1. [Configurez des notification services push et Azure notification Hub](#set-up-push-notification-services-and-azure-notification-hub).
1. [Comprendre comment utiliser des modèles et des balises](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Créer une Xamarin.Forms application multiplateforme](#xamarinforms-application-functionality).
1. [Configurez le projet Android natif pour les notifications push](#configure-the-android-application-for-notifications).
1. [Configurez le projet iOS natif pour les notifications push](#configure-ios-for-notifications).
1. [Tester les notifications à l’aide d’Azure notification Hub](#test-notifications-in-the-azure-portal).
1. [Créer une application principale pour envoyer des notifications](#create-a-notification-dispatcher).

> [!NOTE]
> Si vous n’avez pas [d’abonnement Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://aka.ms/azfree-docs-mobileapps) avant de commencer.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurer Push Notification Services et Azure Notification Hubs

L’intégration d’Azure Notification Hubs avec une application Xamarin.Forms mobile est semblable à l’intégration d’azure notification hubs avec une application native Xamarin. Configurez une Firebase application Cloud Messaging (FCM) en suivant les Firebase étapes de la console dans [notifications push vers Xamarin. Android à l’aide d’Azure notification hubs](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Effectuez les étapes suivantes à l’aide du didacticiel Xamarin. Android :

1. Définissez un nom de package Android, tel que `com.xamarin.notifysample` , qui est utilisé dans l’exemple.
1. Téléchargez `google-services.json` à partir de la Firebase console. Vous ajouterez ce fichier à votre application Android dans une prochaine étape.
1. Créez une instance Azure notification Hub et donnez-lui un nom. Cet article et l’exemple utilisent `xdocsnotificationhub` comme nom de concentrateur.
1. Copiez la **clé du serveur** FCM et enregistrez-la en tant que **clé API** sous **Google (GCM/FCM)** dans votre Hub de notification Azure.

La capture d’écran suivante montre la configuration de la plateforme Google dans le hub de notification Azure :

![Capture d’écran de la configuration de Google pour Azure notification Hub](azure-notification-hub-images/fcm-notification-hub-config.png "Configuration de Google pour Azure notification Hub")

Vous aurez besoin d’un ordinateur macOS pour terminer l’installation des appareils iOS. Configurez Apple Push Notification Services (APNS) en suivant les étapes initiales dans [notifications push vers Xamarin. IOS à l’aide d’Azure notification hubs](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Effectuez les étapes suivantes à l’aide du didacticiel Xamarin. iOS :

1. Définissez un identificateur de Bundle iOS. Cet article et l’exemple utilisent `com.xamarin.notifysample` comme identificateur de bundle.
1. Créez un fichier de demande de signature de certificat (CSR) et utilisez-le pour générer un certificat de notification push.
1. Téléchargez le certificat de notification push sous **Apple (APNs)** dans votre Hub de notification Azure.

La capture d’écran suivante montre la configuration de la plateforme Apple dans le hub de notification Azure :

![Capture d’écran de la configuration d’Apple Azure notification Hub](azure-notification-hub-images/apns-notification-hub-config.png "Configuration Apple d’Azure notification Hub")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Inscrire des modèles et des balises auprès du Hub de notification Azure

Azure notification Hub exige que les applications mobiles s’inscrivent auprès du Hub, définissent des modèles et s’abonnent à des balises. L’inscription lie un handle PNS spécifique à une plateforme à un identificateur dans le hub de notification Azure. Pour en savoir plus sur les inscriptions, consultez [gestion des inscriptions](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

Les modèles permettent aux appareils de spécifier des modèles de message paramétrés. Les messages entrants peuvent être personnalisés par appareil et par étiquette. Pour en savoir plus sur les modèles, consultez [modèles](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Les balises peuvent être utilisées pour s’abonner à des catégories de messages telles que Actualités, sports et météo. Par souci de simplicité, l’exemple d’application définit un modèle par défaut avec un paramètre unique appelé `messageParam` et une seule balise appelée `default` . Dans les systèmes plus complexes, les balises spécifiques à l’utilisateur peuvent être utilisées pour messageer un utilisateur sur plusieurs appareils pour des notifications personnalisées. Pour en savoir plus sur les balises, consultez [routage et expressions de balise](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Pour recevoir des messages, chaque application native doit effectuer les étapes suivantes :

1. Obtenez un handle ou un jeton PNS à partir de la plateforme PNS.
1. Inscrivez le descripteur PNS auprès d’Azure notification Hub.
1. Spécifiez un modèle qui contient les mêmes paramètres que les messages sortants.
1. Abonnez-vous à la balise ciblée par les messages sortants.

Ces étapes sont décrites en détail pour chaque plateforme dans les sections [configurer l’application Android pour les notifications](#configure-the-android-application-for-notifications) et [configurer IOS pour les notifications](#configure-ios-for-notifications) .

## <a name="no-locxamarinforms-application-functionality"></a>Xamarin.Formsfonctionnalités de l’application

L’exemple Xamarin.Forms d’application affiche la liste des messages de notification push. Cela est accompli avec la `AddMessage` méthode, qui ajoute le message de notification push spécifié à l’interface utilisateur. Cette méthode empêche également les messages en double d’être ajoutés à l’interface utilisateur et s’exécute sur le thread principal afin qu’il puisse être appelé à partir de n’importe quel thread. Le code suivant montre la méthode `AddMessage` :

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

L’exemple d’application contient un `AppConstants.cs` fichier qui définit les propriétés utilisées par les projets de plateforme. Ce fichier doit être personnalisé avec les valeurs de votre Hub de notification Azure. Le code suivant affiche le `AppConstants.cs` fichier :

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

Personnalisez les valeurs suivantes dans `AppConstants` pour connecter l’exemple d’application à votre Hub de notification Azure :

* `NotificationHubName`: Utilisez le nom du concentrateur de notification Azure que vous avez créé dans votre Portail Azure.
* `ListenConnectionString`: Cette valeur se trouve dans le hub de notification Azure sous **stratégies d’accès**.

La capture d’écran suivante montre où ces valeurs se trouvent dans le Portail Azure :

![Capture d’écran de la stratégie d’accès Azure notification Hub](azure-notification-hub-images/notification-hub-access-policy.png "Stratégie d’accès Azure notification Hub")

## <a name="configure-the-android-application-for-notifications"></a>Configurer l’application Android pour les notifications

Procédez comme suit pour configurer l’application Android afin de recevoir et traiter les notifications :

1. Configurez le **nom du package** Android pour qu’il corresponde au nom du package dans la Firebase console.
1. Installez les packages NuGet suivants pour interagir avec Google Play Firebase et Azure notification hubs :
    1. `Xamarin.GooglePlayServices.Base`
    1. `Xamarin.Firebase.Messaging`
    1. `Xamarin.Azure.NotificationHubs.Android`
1. Copiez le `google-services.json` fichier que vous avez téléchargé lors de l’installation de FCM dans le projet et définissez l’action de génération sur `GoogleServicesJson` .
1. [Configurer](#configure-android-manifest) `AndroidManifest.xml` pour communiquer avec Firebase .
1. [Remplacer](#override-firebasemessagingservice-to-handle-messages) `FirebaseMessagingService` pour gérer les messages.
1. [Ajoutez](#add-incoming-notifications-to-the-xamarinforms-ui) des notifications entrantes à Xamarin.Forms l’interface utilisateur.

> [!NOTE]
> L' `GoogleServicesJson` action de génération fait partie du `Xamarin.GooglePlayServices.Base` package NuGet. Visual Studio 2019 définit les actions de génération disponibles au démarrage. Si vous ne voyez pas `GoogleServicesJson` comme action de génération, redémarrez Visual Studio 2019 après avoir installé les packages NuGet.

### <a name="configure-android-manifest"></a>Configurer le manifeste Android

Les `receiver` éléments de l' `application` élément permettent à l’application de communiquer avec Firebase . Les `uses-permission` éléments permettent à l’application de gérer des messages et de s’inscrire auprès d’Azure notification Hub. La totalité du `AndroidManifest.xml` résultat doit ressembler à l’exemple ci-dessous :

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

### <a name="override-no-locfirebasemessagingservice-to-handle-messages"></a>Remplacer `FirebaseMessagingService` pour gérer les messages

Pour vous inscrire auprès de Firebase et gérer les messages, sous-classez la `FirebaseMessagingService` classe. L’exemple d’application définit une `FirebaseService` classe qui sous-classe le `FirebaseMessagingService` . Cette classe est marquée avec un `IntentFilter` attribut, qui comprend le `com.google.firebase.MESSAGING_EVENT` filtre. Ce filtre permet à Android de transmettre des messages entrants à cette classe à des fins de gestion :

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

Lorsque l’application est démarrée, le Firebase Kit de développement logiciel (SDK) demande automatiquement un identificateur de jeton unique à partir du Firebase serveur. En cas de demande réussie, la `OnNewToken` méthode est appelée sur la `FirebaseService` classe. L’exemple de projet remplace cette méthode et inscrit le jeton auprès d’Azure Notification Hubs :

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

La `SendRegistrationToServer` méthode inscrit l’appareil auprès d’Azure notification Hub et s’abonne aux balises à l’aide d’un modèle. L’exemple d’application définit une balise unique appelée `default` et un modèle avec un paramètre unique appelé `messageParam` dans le `AppConstants.cs` fichier. Pour plus d’informations sur l’inscription, les balises et les modèles, consultez [inscrire des modèles et des balises avec le hub de notification Azure](#register-templates-and-tags-with-the-azure-notification-hub).

Lorsqu’un message est reçu, la `OnMessageReceived` méthode est appelée sur la `FirebaseService` classe :

```csharp
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

    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

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
```

Les messages entrants sont convertis en notification locale à l’aide de la `SendLocalNotification` méthode. Cette méthode crée un nouveau `Intent` et place le contenu du message dans le `Intent` en tant que `string` `Extra` . Quand l’utilisateur appuie sur la notification locale, que l’application soit au premier plan ou en arrière-plan, le `MainActivity` est lancé et a accès au contenu du message via l' `Intent` objet.

La notification locale et l' `Intent` exemple nécessitent que l’utilisateur appuie sur la notification. Cela est souhaitable lorsque l’utilisateur doit agir avant de modifier l’état de l’application. Toutefois, vous souhaiterez peut-être accéder aux données du message sans intervention de l’utilisateur dans certains cas. L’exemple précédent envoie également le message directement à l' `MainPage` instance actuelle avec la `SendMessageToMainPage` méthode. En production, si vous implémentez les deux méthodes pour un seul type de message, l' `MainPage` objet recevra des messages en double si l’utilisateur appuie sur la notification.

> [!NOTE]
> L’application Android recevra uniquement les notifications push si elle s’exécute en arrière-plan ou au premier plan. Pour recevoir des notifications push lorsque le principal `Activity` n’est pas en cours d’exécution, vous devez implémenter un service qui dépasse le cadre de cet exemple. Pour plus d’informations, consultez [création de services Android](/xamarin/android/app-fundamentals/services/) .

### <a name="add-incoming-notifications-to-the-no-locxamarinforms-ui"></a>Ajouter des notifications entrantes à l' Xamarin.Forms interface utilisateur

La `MainActivity` classe doit obtenir l’autorisation de gérer les notifications et de gérer les données des messages entrants. Le code suivant illustre l’implémentation complète de `MainActivity` :

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

        if (!IsPlayServiceAvailable())
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

L' `Activity` attribut affecte à l’application la `LaunchMode` valeur `SingleTop` . Ce mode de lancement indique au système d’exploitation Android de n’autoriser qu’une seule instance de cette activité. Avec ce mode de lancement, les données entrantes `Intent` sont routées vers la `OnNewIntent` méthode, qui extrait les données de message et les envoie à l' `MainPage` instance par le biais de la `AddMessage` méthode. Si votre application utilise un mode de lancement différent, elle doit gérer les `Intent` données différemment.

La `OnCreate` méthode utilise une méthode d’assistance appelée `IsPlayServiceAvailable` pour garantir que l’appareil prend en charge Google Play service. Les émulateurs ou appareils qui ne prennent pas en charge Google Play service ne peuvent pas recevoir de notifications push de Firebase .

## <a name="configure-ios-for-notifications"></a>Configurer iOS pour les notifications

Le processus de configuration de l’application iOS pour recevoir des notifications est le suivant :

1. Configurez l' **identificateur de Bundle** dans le `Info.plist` fichier pour qu’il corresponde à la valeur utilisée dans le profil de provisionnement.
1. Ajoutez l’option **activer les notifications push** au `Entitlements.plist` fichier.
1. Ajoutez le `Xamarin.Azure.NotificationHubs.iOS` package NuGet à votre projet.
1. [Inscrivez-vous](#register-for-notifications-with-apns) aux notifications avec APNs.
1. [Inscrire](#register-with-azure-notification-hub-and-subscribe-to-tags) l’application auprès d’Azure notification Hub et s’abonner à des balises.
1. [Ajouter](#add-apns-notifications-to-xamarinforms-ui) APNS les notifications à Xamarin.Forms l’interface utilisateur.

La capture d’écran suivante montre l’option **activer les notifications push** sélectionnée dans le `Entitlements.plist` fichier dans Visual Studio :

![Capture d’écran du droit notifications push](azure-notification-hub-images/push-notification-entitlement.png "Habilitation des notifications push")

### <a name="register-for-notifications-with-apns"></a>S’inscrire aux notifications avec APNS

La `FinishedLaunching` méthode dans le `AppDelegate.cs` fichier doit être substituée pour s’inscrire aux notifications distantes. L’inscription diffère selon la version d’iOS utilisée sur l’appareil. Le projet iOS dans l’exemple d’application remplace la `FinishedLaunching` méthode à appeler `RegisterForRemoteNotifications` comme indiqué dans l’exemple suivant :

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

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>S’inscrire auprès d’Azure notification Hub et s’abonner à des balises

Lorsque l’appareil s’est correctement inscrit pour les notifications distantes pendant la `FinishedLaunching` méthode, iOS appelle la `RegisteredForRemoteNotifications` méthode. Cette méthode doit être substituée pour effectuer les actions suivantes :

1. Instanciez le `SBNotificationHub` .
1. Annule l’inscription des inscriptions existantes.
1. Inscrivez l’appareil auprès de votre Hub de notification.
1. S’abonner à des balises spécifiques à l’aide d’un modèle.

Pour plus d’informations sur l’inscription de l’appareil, des modèles et des balises, consultez [inscrire des modèles et des balises auprès du Hub de notification Azure](#register-templates-and-tags-with-the-azure-notification-hub). Le code suivant illustre l’inscription de l’appareil et des modèles :

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
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
> L’inscription à des notifications distantes peut échouer dans des situations telles qu’aucune connexion réseau. Vous pouvez choisir de substituer la `FailedToRegisterForRemoteNotifications` méthode pour gérer l’échec de l’inscription.

### <a name="add-apns-notifications-to-no-locxamarinforms-ui"></a>Ajouter des notifications APNS à Xamarin.Forms l’interface utilisateur

Quand un appareil reçoit une notification à distance, iOS appelle la `ReceivedRemoteNotification` méthode. Le JSON de message entrant est converti en un `NSDictionary` objet, et la `ProcessNotification` méthode extrait des valeurs du dictionnaire et les envoie à l' Xamarin.Forms `MainPage` instance. La `ReceivedRemoteNotifications` méthode est substituée pour appeler `ProcessNotification` comme indiqué dans le code suivant :

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

## <a name="test-notifications-in-the-azure-portal"></a>Tester les notifications dans le Portail Azure

Azure Notification Hubs vous permet de vérifier que votre application peut recevoir des messages de test. La section **test Send** dans le hub de notification vous permet de choisir la plateforme cible et d’envoyer un message. L’affectation de la valeur à l' **expression d’étiquette envoyer** à permet d' `default` Envoyer des messages aux applications qui ont inscrit un modèle pour la `default` balise. En cliquant sur le bouton envoyer, vous générez un rapport qui comprend le nombre d’appareils auxquels le message a été **envoyé** . La capture d’écran suivante montre un test de notification Android dans le Portail Azure :

![Capture d’écran d’un message de test Azure notification Hub](azure-notification-hub-images/azure-notification-hub-test-send.png "Message de test du Hub de notification Azure")

### <a name="testing-tips"></a>Conseils de test

1. Lorsque vous testez qu’une application peut recevoir des notifications push, vous devez utiliser un appareil physique. Les appareils virtuels Android et iOS ne sont peut-être pas configurés correctement pour recevoir des notifications push.
1. L’exemple d’application Android inscrit son jeton et ses modèles une fois que le Firebase jeton est émis. Lors du test, vous devrez peut-être demander un nouveau jeton et vous réinscrire auprès du Hub de notification Azure. La meilleure façon de le forcer est de nettoyer votre projet, de supprimer `bin` les `obj` dossiers et et de désinstaller l’application de l’appareil avant la reconstruction et le déploiement.
1. De nombreuses parties du workflow de notification push sont exécutées de façon asynchrone. Cela peut entraîner des points d’arrêt non atteints ou atteints dans un ordre inattendu. Utilisez l’enregistrement de l’appareil ou du débogage pour suivre l’exécution sans interrompre le workflow de l’application. Filtrez le journal des appareils Android à l’aide du `DebugTag` spécifié dans `Constants` .
1. Lorsque le débogage est arrêté dans Visual Studio, l’application est fermée de force. Tous les récepteurs de messages ou autres services lancés dans le cadre du processus de débogage sont fermés et ne répondent pas aux événements de message.

## <a name="create-a-notification-dispatcher"></a>Créer un répartiteur de notifications

Azure Notification Hubs permettre à votre application principale de distribuer les notifications aux appareils sur les plateformes. L’exemple illustre la distribution des notifications avec une application console. L’application comprend le `DispatcherConstants.cs` fichier, qui définit les propriétés suivantes :

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

Vous devez configurer le `DispatcherConstants.cs` fichier pour qu’il corresponde à votre configuration Azure notification Hub. La valeur de la `SubscriptionTags` propriété doit correspondre aux valeurs utilisées dans les applications clientes. La `NotificationHubName` propriété est le nom de votre instance Azure notification Hub. La `FullAccessConnectionString` propriété est la clé d’accès trouvée dans vos **stratégies d’accès**au hub de notification. La capture d’écran suivante montre l’emplacement `NotificationHubName` des `FullAccessConnectionString` Propriétés et dans le portail Azure :

![Capture d’écran du nom et FullAccessConnectionString du Hub de notification Azure](azure-notification-hub-images/notification-hub-full-access-policy.png "Nom et FullAccessConnectionString du Hub de notification Azure")

L’application console parcourt chaque `SubscriptionTags` valeur et envoie des notifications aux abonnés à l’aide d’une instance de la `NotificationHubClient` classe. Le code suivant illustre la classe de l’application console `Program` :

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

Lorsque l’exemple d’application console est exécuté, la barre d’espace peut être appuyée pour envoyer des messages. Les appareils qui exécutent les applications clientes doivent recevoir des notifications numérotées, à condition qu’elles soient correctement configurées.

## <a name="related-links"></a>Liens connexes

* [Modèles de notification push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Gestion de l’inscription des appareils](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Routage et expressions de balise](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Didacticiel notification hubs Azure Xamarin. Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Didacticiel notification hubs Azure Xamarin. iOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
