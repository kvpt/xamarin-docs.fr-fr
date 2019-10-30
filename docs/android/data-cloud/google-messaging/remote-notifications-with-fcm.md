---
title: Notifications distantes avec Firebase Cloud Messaging
description: Cette procédure pas à pas fournit une explication pas à pas de l’utilisation de Firebase Cloud Messaging pour implémenter des notifications distantes (également appelées notifications push) dans une application Xamarin. Android. Il illustre comment implémenter les différentes classes nécessaires pour les communications avec Firebase Cloud Messaging (FCM), fournit des exemples de configuration du manifeste Android pour l’accès à FCM et illustre la messagerie en aval à l’aide de Firebase Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: ece8b46e02943774e611fda419b3e8ef6b4e8976
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021642"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notifications distantes avec Firebase Cloud Messaging

_Cette procédure pas à pas fournit une explication pas à pas de l’utilisation de Firebase Cloud Messaging pour implémenter des notifications distantes (également appelées notifications push) dans une application Xamarin. Android. Il illustre comment implémenter les différentes classes nécessaires pour les communications avec Firebase Cloud Messaging (FCM), fournit des exemples de configuration du manifeste Android pour l’accès à FCM et illustre la messagerie en aval à l’aide de Firebase Console._

## <a name="fcm-notifications-overview"></a>Présentation des notifications FCM

Dans cette procédure pas à pas, une application de base appelée **FCMClient** sera créée pour illustrer les notions fondamentales de la messagerie FCM. **FCMClient** vérifie la présence de Google Play services, reçoit les jetons d’inscription de FCM, affiche les notifications distantes que vous envoyez à partir de la console Firebase et s’abonne aux messages de rubrique :

[![exemple de capture d’écran de l’application](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Les rubriques suivantes sont explorées :

1. Notifications en arrière-plan

2. Messages de rubrique

3. Notifications au premier plan

Au cours de cette procédure pas à pas, vous allez ajouter de façon incrémentielle des fonctionnalités à **FCMClient** et les exécuter sur un appareil ou un émulateur pour comprendre comment il interagit avec FCM. Vous allez utiliser la journalisation des transactions de l’application en temps réel avec des serveurs FCM, et vous observerez comment les notifications sont générées à partir des messages FCM que vous entrez dans l’interface graphique utilisateur des notifications de la console Firebase.

## <a name="requirements"></a>spécifications

Il sera utile de vous familiariser avec les [différents types de messages](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) qui peuvent être envoyés par Firebase Cloud Messaging. La charge utile du message détermine la manière dont une application cliente recevra et traitera le message.

Avant de pouvoir suivre cette procédure pas à pas, vous devez acquérir les informations d’identification nécessaires pour utiliser les serveurs FCM de Google. Ce processus est expliqué dans [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
En particulier, vous devez télécharger le fichier **Google-services. JSON** à utiliser avec l’exemple de code présenté dans cette procédure pas à pas. Si vous n’avez pas encore créé de projet dans la console Firebase (ou si vous n’avez pas encore téléchargé le fichier **Google-services. JSON** ), consultez [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Pour exécuter l’exemple d’application, vous avez besoin d’un appareil de test Android ou d’un émulateur compatible avec avec Firebase. Firebase Cloud Messaging prend en charge les clients s’exécutant sur Android 4,0 ou une version ultérieure, et l’application Google Play Store doit également être installée sur ces appareils (Google Play Services 9.2.1 ou version ultérieure). Si vous n’avez pas encore installé l’application Google Play Store sur votre appareil, visitez le site Web [Google Play](https://support.google.com/googleplay) pour la télécharger et l’installer. Vous pouvez également utiliser l’émulateur Android SDK avec Google Play Services installé au lieu d’un appareil de test (vous n’avez pas besoin d’installer le Google Play Store si vous utilisez l’émulateur Android SDK).

## <a name="start-an-app-project"></a>Démarrer un projet d’application

Pour commencer, créez un nouveau projet Xamarin. Android vide nommé **FCMClient**. Si vous n’êtes pas familiarisé avec la création de projets Xamarin. Android, consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Une fois la nouvelle application créée, l’étape suivante consiste à définir le nom du package et à installer plusieurs packages NuGet qui seront utilisés pour la communication avec FCM.

### <a name="set-the-package-name"></a>Définir le nom du package

Dans [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), vous avez spécifié un nom de package pour l’application prenant en charge FCM. Ce nom de package sert également d' [*ID d’application*](./firebase-cloud-messaging.md#fcm-in-action-app-id) associé à la [clé API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configurez l’application pour qu’elle utilise ce nom de package :

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrez les propriétés du projet **FCMClient** .

2. Dans la page du **manifeste Android** , définissez le nom du package.

Dans l’exemple suivant, le nom du package est défini sur `com.xamarin.fcmexample`:

[![de la définition du nom du package](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Pendant la mise à jour du **manifeste Android**, vérifiez également que l’autorisation `Internet` est activée.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez les propriétés du projet **FCMClient** .

2. Dans la page **application Android** , définissez le nom du package.

Dans l’exemple suivant, le nom du package est défini sur `com.xamarin.fcmexample`:

[![de la définition du nom du package](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Pendant la mise à jour du **manifeste Android**, vérifiez également que l’autorisation `INTERNET` est activée (sous **autorisations requises**).

-----

> [!IMPORTANT]
> L’application cliente ne pourra pas recevoir de jeton d’inscription à partir de FCM si ce nom de package ne correspond pas *exactement* au nom du package qui a été entré dans la console Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Ajouter le package de base Xamarin Google Play Services

Étant donné que la messagerie Firebase Cloud dépend de Google Play Services, le package NuGet [Xamarin Google Play Services-base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) doit être ajouté au projet Xamarin. Android. Vous aurez besoin de la version 29.0.0.2 ou d’une version ultérieure.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans Visual Studio, cliquez avec le bouton droit sur **références > gérer les packages NuGet...** .

2. Cliquez sur l’onglet **Parcourir** et recherchez **Xamarin. GooglePlayServices. base**.

3. Installez ce package dans le projet **FCMClient** :

    [![de l’installation de la base Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur **packages > ajouter des packages**....

2. Recherchez **Xamarin. GooglePlayServices. base**.

3. Installez ce package dans le projet **FCMClient** :

    [![de l’installation de la base Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Si vous recevez une erreur lors de l’installation de NuGet, fermez le projet **FCMClient** , rouvrez-le, puis réessayez l’installation de NuGet.

Lorsque vous installez **Xamarin. GooglePlayServices. base**, toutes les dépendances nécessaires sont également installées. Modifiez **MainActivity.cs** et ajoutez l’instruction `using` suivante :

```csharp
using Android.Gms.Common;
```

Cette instruction rend la classe `GoogleApiAvailability` dans **Xamarin. GooglePlayServices. base** disponible pour le code **FCMClient** .
`GoogleApiAvailability` est utilisé pour vérifier la présence de Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Ajouter le package de messagerie Xamarin Firebase

Pour recevoir des messages de FCM, le package NuGet [Xamarin Firebase-Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) doit être ajouté au projet d’application. Sans ce package, une application Android ne peut pas recevoir de messages des serveurs FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans Visual Studio, cliquez avec le bouton droit sur **références > gérer les packages NuGet...** .

2. Recherchez **Xamarin. Firebase. Messaging**.

3. Installez ce package dans le projet **FCMClient** :

    [![installation de la messagerie Xamarin Firebase](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur **packages > ajouter des packages**....

2. Recherchez **Xamarin. Firebase. Messaging**.

3. Installez ce package dans le projet **FCMClient** :

    [![installation de la messagerie Xamarin Firebase](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Lorsque vous installez **Xamarin. Firebase. Messaging**, toutes les dépendances nécessaires sont également installées.

Ensuite, modifiez **MainActivity.cs** et ajoutez les instructions `using` suivantes :

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Les deux premières instructions rendent les types dans le package NuGet **Xamarin. Firebase. Messaging** disponibles pour le code **FCMClient** . **Android. util** ajoute des fonctionnalités de journalisation qui seront utilisées pour observer les transactions avec FMS.

### <a name="add-googleplayservices-json"></a>Ajouter le fichier JSON des services Google

L’étape suivante consiste à ajouter le fichier **Google-services. JSON** au répertoire racine de votre projet :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copiez **Google-services. JSON** dans le dossier du projet.

2. Ajoutez **Google-services. JSON** au projet d’application (cliquez sur **Afficher tous les fichiers** dans le **Explorateur de solutions**, cliquez avec le bouton droit sur **Google-services. JSON**, puis sélectionnez **inclure dans le projet**).

3. Sélectionnez **Google-services. JSON** dans la fenêtre **Explorateur de solutions** .

4. Dans le volet **Propriétés** , définissez l' **action de génération** sur **GoogleServicesJson**:

    [![de la définition de l’action de génération sur GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Si l’action de génération **GoogleServicesJson** n’est pas affichée, enregistrez et fermez la solution, puis rouvrez-la.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Copiez **Google-services. JSON** dans le dossier du projet.

2. Ajoutez **Google-services. JSON** au projet d’application.

3. Cliquez avec le bouton droit sur **Google-services. JSON**.

4. Définissez l' **action de génération** sur **GoogleServicesJson**:

    [![de la définition de l’action de génération sur GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Lorsque **Google-services. JSON** est ajouté au projet (et que l’action de génération **GoogleServicesJson** est définie), le processus de génération extrait l’ID client et la [clé API](./firebase-cloud-messaging.md#fcm-in-action-api-key) , puis ajoute ces informations d’identification aux données fusionnées/générées **. Fichier AndroidManifest. xml** qui réside dans **obj/Debug/Android/fichier AndroidManifest. xml**. Ce processus de fusion ajoute automatiquement les autorisations et les autres éléments FCM nécessaires pour la connexion aux serveurs FCM.

## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Rechercher Google Play Services et créer un canal de notification

Google recommande que les applications Android vérifient la présence de la Google Play Services APK avant d’accéder aux fonctionnalités de Google Play Services (pour plus d’informations, consultez [Rechercher des Services Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Une disposition initiale pour l’interface utilisateur de l’application est créée en premier. Modifiez **Resources/layout/main. AXML** et remplacez son contenu par le code XML suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Ce `TextView` sera utilisé pour afficher les messages qui indiquent si Google Play Services est installé. Enregistrez les modifications dans **main. AXML**.

Modifiez **MainActivity.cs** et ajoutez les variables d’instance suivantes à la classe `MainActivity` :

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Les variables `CHANNEL_ID` et `NOTIFICATION_ID` seront utilisées dans la méthode [`CreateNotificationChannel`](#create-notification-channel-code) qui sera ajoutée à `MainActivity` plus tard dans cette procédure pas à pas.

Dans l’exemple suivant, la méthode `OnCreate` vérifie que Google Play Services est disponible avant que l’application ne tente d’utiliser les services FCM.
Ajoutez la méthode suivante à la classe `MainActivity` :

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Ce code vérifie l’appareil pour voir si le Google Play Services APK est installé. S’il n’est pas installé, un message s’affiche dans la `TextBox` qui indique à l’utilisateur de télécharger un APK à partir de la Google Play Store (ou de l’activer dans les paramètres système de l’appareil).

<a name="create-notification-channel-code"></a>Les applications qui s’exécutent sur Android 8,0 (niveau d’API 26) ou une version ultérieure doivent créer un [_canal de notification_](~/android/app-fundamentals/notifications/local-notifications.md) pour la publication de leurs notifications.  Ajoutez la méthode suivante à la classe `MainActivity` qui créera le canal de notification (si nécessaire) :

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Remplacez la méthode `OnCreate` par le code suivant :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` est appelé à la fin de `OnCreate` afin que la vérification de l’Google Play Services s’exécute chaque fois que l’application démarre. La méthode `CreateNotificationChannel` est appelée pour s’assurer qu’un canal de notification existe pour les appareils exécutant Android 8 ou une version ultérieure. Si votre application a une méthode `OnResume`, elle doit également appeler `IsPlayServicesAvailable` à partir de `OnResume`. Régénérez et exécutez complètement l’application. Si tout est correctement configuré, vous devriez voir un écran similaire à la capture d’écran suivante :

[![application indique que Google Play Services est disponible](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Si vous n’obtenez pas ce résultat, vérifiez que le Google Play Services APK est installé sur votre appareil (pour plus d’informations, consultez [configuration de Google Play services](https://developers.google.com/android/guides/setup)).
Vérifiez également que vous avez ajouté le package **Xamarin. google. Play. services. base** à votre projet **FCMClient** , comme expliqué précédemment.

## <a name="add-the-instance-id-receiver"></a>Ajouter le récepteur de l’ID d’instance

L’étape suivante consiste à ajouter un service qui étend `FirebaseInstanceIdService` pour gérer la création, la rotation et la mise à jour des [jetons d’inscription Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). Le service `FirebaseInstanceIdService` est requis pour que FCM puisse envoyer des messages à l’appareil. Lorsque le service `FirebaseInstanceIdService` est ajouté à l’application cliente, l’application reçoit automatiquement les messages FCM et les affiche sous forme de notifications chaque fois que l’application est en arrière-plan.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Déclarer le récepteur dans le manifeste Android

Modifiez **fichier AndroidManifest. xml** et insérez les éléments de `<receiver>` suivants dans la section `<application>` :

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Ce code XML effectue les opérations suivantes :

- Déclare une implémentation de `FirebaseInstanceIdReceiver` qui fournit un [identificateur unique](https://developers.google.com/instance-id/) pour chaque instance d’application. Ce récepteur authentifie et autorise également les actions.

- Déclare une implémentation de `FirebaseInstanceIdInternalReceiver` interne qui est utilisée pour démarrer des services en toute sécurité.

- L' [ID d’application](./firebase-cloud-messaging.md#fcm-in-action-app-id) est stocké dans le fichier **Google-services. JSON** qui a été [ajouté au projet](#add-googleplayservices-json). Les liaisons Xamarin. Android Firebase remplacent le jeton `${applicationId}` par l’ID de l’application. aucun code supplémentaire n’est requis par l’application cliente pour fournir l’ID d’application.

Le `FirebaseInstanceIdReceiver` est un `WakefulBroadcastReceiver` qui reçoit des événements `FirebaseInstanceId` et `FirebaseMessaging` et les remet à la classe que vous dérivez de `FirebaseInstanceIdService`.

### <a name="implement-the-firebase-instance-id-service"></a>Implémenter le service d’ID d’instance Firebase

Le travail d’inscription de l’application auprès de FCM est géré par le service de `FirebaseInstanceIdService` personnalisé que vous fournissez.
`FirebaseInstanceIdService` effectue les étapes suivantes :

1. Utilise l' [API ID d’instance](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) pour générer des jetons de sécurité qui autorisent l’application cliente à accéder à FCM et au serveur d’applications. En retour, l’application récupère un [jeton d’inscription](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) à partir de FCM.

2. Transmet le jeton d’inscription au serveur d’applications si le serveur d’applications en a besoin.

Ajoutez un nouveau fichier appelé **MyFirebaseIIDService.cs** et remplacez son code de modèle par ce qui suit :

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Ce service implémente une méthode `OnTokenRefresh` qui est appelée lors de la création ou de la modification initiale du jeton d’inscription. Lorsque `OnTokenRefresh` s’exécute, il récupère le jeton le plus récent à partir de la propriété `FirebaseInstanceId.Instance.Token` (qui est mise à jour de façon asynchrone par FCM). Dans cet exemple, le jeton actualisé est journalisé afin qu’il puisse être affiché dans la fenêtre Sortie :

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` est rarement appelé : il est utilisé pour mettre à jour le jeton dans les circonstances suivantes :

- Lorsque l’application est installée ou désinstallée.

- Lorsque l’utilisateur supprime des données d’application.

- Lorsque l’application efface l’ID d’instance.

- Lorsque la sécurité du jeton a été compromise.

Conformément à la documentation de l' [ID d’instance](https://developers.google.com/instance-id/guides/android-implementation) de Google, le service d’ID d’instance FCM demande que l’application actualise son jeton régulièrement (en général, tous les 6 mois).

`OnTokenRefresh` appelle également `SendRegistrationToAppServer` pour associer le jeton d’inscription de l’utilisateur au compte côté serveur (le cas échéant) qui est géré par l’application :

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Étant donné que cette implémentation dépend de la conception du serveur d’applications, un corps de méthode vide est fourni dans cet exemple. Si votre serveur d’applications nécessite des informations d’inscription FCM, modifiez `SendRegistrationToAppServer` pour associer le jeton d’ID d’instance FCM de l’utilisateur à n’importe quel compte côté serveur géré par votre application. (Notez que le jeton est opaque pour l’application cliente.)

Lorsqu’un jeton est envoyé au serveur d’applications, `SendRegistrationToAppServer` doit conserver une valeur booléenne pour indiquer si le jeton a été envoyé au serveur. Si cette valeur booléenne est false, `SendRegistrationToAppServer` envoie le jeton au serveur d’applications &ndash; dans le cas contraire, le jeton a déjà été envoyé au serveur d’applications lors d’un appel précédent. Dans certains cas (par exemple, cet `FCMClient` exemple), le serveur d’applications n’a pas besoin du jeton. par conséquent, cette méthode n’est pas requise pour cet exemple.

## <a name="implement-client-app-code"></a>Implémenter le code de l’application cliente

Maintenant que les services du récepteur sont en place, le code de l’application cliente peut être écrit pour tirer parti de ces services. Dans les sections suivantes, un bouton est ajouté à l’interface utilisateur pour enregistrer le jeton d’inscription (également appelé *jeton d’ID d’instance*) et davantage de code est ajouté à `MainActivity` pour afficher les informations de `Intent` lorsque l’application est lancée à partir d’une notification :

[![bouton de jeton de journal ajouté à l’écran de l’application](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Jetons de journal

Le code ajouté à cette étape est destiné uniquement à des fins de démonstration &ndash; une application cliente de production n’a pas besoin de journaliser les jetons d’inscription. Modifiez **Resources/layout/main. AXML** et ajoutez la déclaration de `Button` suivante immédiatement après l’élément `TextView` :

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Ajoutez le code suivant à la fin de la méthode `MainActivity.OnCreate` :

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Ce code enregistre le jeton actuel dans la fenêtre sortie lorsque le bouton de **jeton de journal** est frappé.

### <a name="handle-notification-intents"></a>Gérer les intentions de notification

Quand l’utilisateur appuie sur une notification émise par **FCMClient**, toutes les données qui accompagnent ce message de notification sont rendues disponibles dans `Intent` Extras. Modifiez **MainActivity.cs** et ajoutez le code suivant en haut de la méthode `OnCreate` (avant l’appel à `IsPlayServicesAvailable`) :

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

Le lanceur de l’application `Intent` est déclenché lorsque l’utilisateur clique sur son message de notification. ce code enregistre toutes les données associées dans le `Intent` dans la fenêtre sortie. Si une autre `Intent` doit être déclenchée, le champ `click_action` du message de notification doit être défini sur ce `Intent` (le lanceur `Intent` est utilisé quand aucun `click_action` n’est spécifié).

## <a name="background-notifications"></a>Notifications en arrière-plan

Générez et exécutez l’application **FCMClient** . Le bouton **jeton de journal** s’affiche :

[![bouton de jeton de journal est affiché](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Appuyez sur le bouton **jeton de journal** . Un message semblable à ce qui suit doit s’afficher dans la fenêtre sortie de l’IDE :

[![jeton d’ID d’instance affiché dans la fenêtre sortie](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La longue chaîne nommée avec **Token** est le jeton d’ID d’instance que vous allez coller dans la Console Firebase &ndash; sélectionner et copier cette chaîne dans le presse-papiers. Si vous ne voyez pas de jeton d’ID d’instance, ajoutez la ligne suivante au début de la méthode `OnCreate` pour vérifier que **Google-services. JSON** a été analysé correctement :

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

La valeur `google_app_id` enregistrée dans la fenêtre sortie doit correspondre à la valeur `mobilesdk_app_id` enregistrée dans **Google-services. JSON**.

### <a name="send-a-message"></a>Envoyer un message

Connectez-vous à la [console Firebase](https://console.firebase.google.com), sélectionnez votre projet, cliquez sur **notifications**, puis sur **Envoyer votre premier message**:

[bouton![envoyer votre premier message](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Dans la page **message de composition** , entrez le texte du message, puis sélectionnez **appareil unique**. Copiez le jeton d’ID d’instance à partir de la fenêtre de sortie de l’IDE, puis collez-le dans le champ **jeton d’inscription FCM** de la console Firebase :

[boîte de dialogue![message de composition](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Sur l’appareil (ou l’émulateur) Android, en arrière-plan de l’application en appuyant sur le bouton de **présentation d'** Android et en touchant l’écran d’accueil. Lorsque l’appareil est prêt, cliquez sur **Envoyer un message** dans la console Firebase :

[![bouton Envoyer un message](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quand la boîte de dialogue **consulter le message** s’affiche, cliquez sur **Envoyer**.
L’icône de notification doit apparaître dans la zone de notification de l’appareil (ou de l’émulateur) :

[![icône de notification s’affiche](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Ouvrez l’icône de notification pour afficher le message. Le message de notification doit être exactement ce qui a été tapé dans le champ **texte du message** de la console Firebase :

[![message de notification s’affiche sur l’appareil](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Appuyez sur l’icône de notification pour lancer l’application **FCMClient** . Le `Intent` les extras envoyés à **FCMClient** sont répertoriés dans la fenêtre sortie de l’IDE :

[![les listes d’intention des compléments de la clé, de l’ID de message et de la clé de réduction](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

Dans cet exemple, la clé **from** est définie sur le numéro de projet Firebase de l’application (dans cet exemple, `41590732`) et **collapse_key** est défini sur son nom de package (**com. xamarin. fcmexample**).
Si vous ne recevez pas de message, essayez de supprimer l’application **FCMClient** sur l’appareil (ou l’émulateur) et répétez les étapes ci-dessus.

> [!NOTE]
> Si vous forcez la fermeture de l’application, FCM cesse de fournir des notifications. Android empêche les diffusions de service en arrière-plan de lancer par inadvertance ou inutilement des composants d’applications arrêtées. (Pour plus d’informations sur ce comportement, consultez [lancer des contrôles sur des applications arrêtées](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Pour cette raison, il est nécessaire de désinstaller manuellement l’application chaque fois que vous l’exécutez et de l’arrêter à partir d’une session de débogage &ndash; cela force FCM à générer un nouveau jeton afin que les messages continuent à être reçus.

### <a name="add-a-custom-default-notification-icon"></a>Ajouter une icône de notification par défaut personnalisée

Dans l’exemple précédent, l’icône de notification est définie sur l’icône de l’application. Le code XML suivant configure une icône par défaut personnalisée pour les notifications. Android affiche cette icône par défaut personnalisée pour tous les messages de notification où l’icône de notification n’est pas définie explicitement.

Pour ajouter une icône de notification par défaut personnalisée, ajoutez votre icône au répertoire **Resources/Drawing** , modifiez **fichier AndroidManifest. xml**et insérez l’élément de `<meta-data>` suivant dans la section `<application>` :

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

Dans cet exemple, l’icône de notification qui se trouve dans **ressources/dessinable/ic\_stat\_ic\_notification. png** sera utilisée comme icône de notification par défaut personnalisée. Si une icône par défaut personnalisée n’est pas configurée dans **fichier AndroidManifest. xml** et qu’aucune icône n’est définie dans la charge utile de notification, Android utilise l’icône d’application comme icône de notification (comme indiqué dans la capture d’écran icône de notification ci-dessus).

## <a name="handle-topic-messages"></a>Gérer les messages de rubrique

Le code écrit jusqu’à présent gère les jetons d’inscription et ajoute des fonctionnalités de notification à distance à l’application. L’exemple suivant ajoute du code qui écoute les *messages de rubrique* et les transfère à l’utilisateur en tant que notifications distantes. Les messages de rubrique sont des messages FCM envoyés à un ou plusieurs appareils qui s’abonnent à une rubrique particulière. Pour plus d’informations sur les messages de rubrique, consultez la [rubrique messagerie](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>S’abonner à une rubrique

Modifiez les **ressources/layout/main. AXML** et ajoutez la déclaration de `Button` suivante immédiatement après l’élément `Button` précédent :

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Ce code XML ajoute un bouton **s’abonner à la notification** à la disposition.
Modifiez **MainActivity.cs** et ajoutez le code suivant à la fin de la méthode `OnCreate` :

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Ce code localise le bouton **s’abonner à la notification** dans la disposition et affecte son gestionnaire de clics au code qui appelle `FirebaseMessaging.Instance.SubscribeToTopic`, en transmettant la rubrique d’abonnement, _Actualités_. Quand l’utilisateur appuie sur le bouton **s’abonner** , l’application s’abonne à la rubrique _Actualités_ . Dans la section suivante, un message de rubrique d' _actualité_ sera envoyé à partir de l’interface graphique utilisateur des notifications de la console Firebase.

### <a name="send-a-topic-message"></a>Envoyer un message de rubrique

Désinstallez l’application, reconstruisez-la, puis réexécutez-la. Cliquez sur le bouton **s’abonner aux notifications** :

[bouton![s’abonner aux notifications](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Si l’application a été inscrite avec succès, vous devriez voir synchronisation de la **rubrique réussie** dans la fenêtre sortie de l’IDE :

[![fenêtre sortie affiche le message synchronisation de rubrique réussie](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Pour envoyer un message de rubrique, procédez comme suit :

1. Dans la console Firebase, cliquez sur **nouveau message**.

2. Dans la page **message de composition** , entrez le texte du message et sélectionnez **rubrique**.

3. Dans le menu déroulant de la **rubrique** , sélectionnez la rubrique, **Actualités**:

    [![la sélection de la rubrique Actualités](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4. Sur l’appareil (ou l’émulateur) Android, en arrière-plan de l’application en appuyant sur le bouton de **présentation d'** Android et en touchant l’écran d’accueil.

5. Lorsque l’appareil est prêt, cliquez sur **Envoyer un message** dans la console Firebase.

6. Consultez la fenêtre sortie de l’IDE pour afficher **/topics/News** dans la sortie du journal :

    [![message de/topic/News est affiché](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Lorsque ce message s’affiche dans la fenêtre sortie, l’icône de notification doit également apparaître dans la zone de notification sur l’appareil Android. Ouvrez l’icône de notification pour afficher le message de rubrique :

[![le message de rubrique apparaît en tant que notification](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Si vous ne recevez pas de message, essayez de supprimer l’application **FCMClient** sur l’appareil (ou l’émulateur) et répétez les étapes ci-dessus.

## <a name="foreground-notifications"></a>Notifications au premier plan

Pour recevoir des notifications dans les applications de premier plan, vous devez implémenter `FirebaseMessagingService`. Ce service est également requis pour la réception de données utiles et pour l’envoi de messages en amont. Les exemples suivants illustrent comment implémenter un service qui étend `FirebaseMessagingService` &ndash; l’application résultante sera en mesure de gérer les notifications distantes pendant qu’elle s’exécute au premier plan.

### <a name="implement-firebasemessagingservice"></a>Implémenter FirebaseMessagingService

Le service `FirebaseMessagingService` est responsable de la réception et du traitement des messages à partir de Firebase. Chaque application doit sous-traiter ce type et remplacer la `OnMessageReceived` pour traiter un message entrant. Quand une application est au premier plan, le rappel `OnMessageReceived` gère toujours le message.

> [!NOTE]
> Les applications n’ont que 10 secondes pour gérer un message entrant dans le Cloud Firebase. Tout travail qui prend plus de temps que celui-ci doit être planifié pour une exécution en arrière-plan à l’aide d’une bibliothèque telle que le [Planificateur de travaux Android](~/android/platform/android-job-scheduler.md) ou le [répartiteur de tâches Firebase](~/android/platform/firebase-job-dispatcher.md).

Ajoutez un nouveau fichier appelé **MyFirebaseMessagingService.cs** et remplacez son code de modèle par ce qui suit :

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Notez que le filtre d’intention de `MESSAGING_EVENT` doit être déclaré afin que les nouveaux messages FCM soient dirigés vers `MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Lorsque l’application cliente reçoit un message de FCM, `OnMessageReceived` extrait le contenu du message de l’objet `RemoteMessage` passé en appelant sa méthode `GetNotification`. Ensuite, il enregistre le contenu du message afin qu’il puisse être affiché dans la fenêtre de sortie de l’IDE :

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Si vous définissez des points d’arrêt dans `FirebaseMessagingService`, votre session de débogage peut ou non atteindre ces points d’arrêt en raison de la façon dont FCM remet les messages.

### <a name="send-another-message"></a>Envoyer un autre message

Désinstallez l’application, reconstruisez-la, réexécutez-la et suivez les étapes suivantes pour envoyer un autre message :

1. Dans la console Firebase, cliquez sur **nouveau message**.

2. Dans la page **message de composition** , entrez le texte du message, puis sélectionnez **appareil unique**.

3. Copiez la chaîne de jeton à partir de la fenêtre de sortie de l’IDE et collez-la dans le champ **jeton d’inscription FCM** de la console Firebase comme précédemment.

4. Assurez-vous que l’application est en cours d’exécution au premier plan, puis cliquez sur **Envoyer un message** dans la console Firebase :

    [![de l’envoi d’un autre message à partir de la console](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5. Quand la boîte de dialogue **consulter le message** s’affiche, cliquez sur **Envoyer**.

6. Le message entrant est enregistré dans la fenêtre de sortie de l’IDE :

    [![corps du message imprimé dans la fenêtre sortie](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)

### <a name="add-a-local-notification-sender"></a>Ajouter un expéditeur de notifications locales

Dans cet exemple, le message FCM entrant est converti en une notification locale qui est lancée pendant l’exécution de l’application au premier plan. Modifiez **MyFirebaseMessageService.cs** et ajoutez les instructions `using` suivantes :

```csharp
using FCMClient;
using System.Collections.Generic;
```

Ajoutez la méthode suivante à `MyFirebaseMessagingService` :

<a name="sendnotification-method"></a>

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Pour distinguer cette notification des notifications en arrière-plan, ce code marque les notifications avec une icône différente de l’icône de l’application. Ajoutez le fichier [ic\_stat\_ic\_notification. png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) à **ressources/dessinable** et incluez-le dans le projet **FCMClient** .

La méthode `SendNotification` utilise `NotificationCompat.Builder` pour créer la notification, et `NotificationManagerCompat` est utilisé pour lancer la notification. La notification contient une `PendingIntent` qui permettra à l’utilisateur d’ouvrir l’application et d’afficher le contenu de la chaîne transmise dans `messageBody`. Pour plus d’informations sur les `NotificationCompat.Builder`, consultez [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

Appelez la méthode `SendNotification` à la fin de la méthode `OnMessageReceived` :

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

Suite à ces modifications, `SendNotification` s’exécute chaque fois qu’une notification est reçue pendant que l’application est au premier plan et que la notification s’affiche dans la zone de notification.

Quand une application est en arrière-plan, la [charge utile du message](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) détermine le mode de traitement du message :

- Les messages de &ndash; de **notification** seront envoyés à la **barre d’état système**. Une notification locale s’affiche. Lorsque l’utilisateur appuie sur la notification, l’application est lancée.
- Les messages de &ndash; de **données** seront gérés par `OnMessageReceived`.
- **Les deux** &ndash; messages qui ont à la fois une notification et une charge utile de données sont remis à la barre d’état système. Lorsque l’application démarre, la charge utile de données apparaît dans le `Extras` du `Intent` utilisé pour démarrer l’application.

Dans cet exemple, si l’application est en arrière-plan, `SendNotification` s’exécute si le message a une charge utile de données. Dans le cas contraire, une notification en arrière-plan (illustrée plus haut dans cette procédure pas à pas) sera lancée.

### <a name="send-the-last-message"></a>Envoyer le dernier message

Désinstallez l’application, reconstruisez-la, réexécutez-la, puis procédez comme suit pour envoyer le dernier message :

1. Dans la console Firebase, cliquez sur **nouveau message**.

2. Dans la page **message de composition** , entrez le texte du message, puis sélectionnez **appareil unique**.

3. Copiez la chaîne de jeton à partir de la fenêtre de sortie de l’IDE et collez-la dans le champ **jeton d’inscription FCM** de la console Firebase comme précédemment.

4. Assurez-vous que l’application est en cours d’exécution au premier plan, puis cliquez sur **Envoyer un message** dans la console Firebase :

    [![de l’envoi du message de premier plan](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Cette fois-ci, le message qui a été enregistré dans la fenêtre sortie est également empaqueté dans une nouvelle notification &ndash; l’icône de notification s’affiche dans la barre d’état des notifications pendant que l’application s’exécute au premier plan :

[icône de notification ![pour le message de premier plan](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Lorsque vous ouvrez la notification, vous devez voir le dernier message qui a été envoyé à partir de l’interface graphique utilisateur des notifications de la console Firebase :

[notification de premier plan ![affichée avec l’icône de premier plan](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

## <a name="disconnecting-from-fcm"></a>Déconnexion de FCM

Pour annuler l’abonnement à une rubrique, appelez la méthode [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) sur la classe [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) . Par exemple, pour vous désabonner de la rubrique relative aux _Actualités_ souscrite, un bouton **Annuler l’abonnement** peut être ajouté à la disposition avec le code de gestionnaire suivant :

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Pour annuler complètement l’inscription de l’appareil dans FCM, supprimez l’ID d’instance en appelant la méthode [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) sur la classe [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) . Exemple :

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Cet appel de méthode supprime l’ID d’instance et les données qui lui sont associées. Par conséquent, l’envoi périodique de données FCM à l’appareil est interrompu.

## <a name="troubleshooting"></a>Résolution des problèmes

Les rubriques suivantes décrivent les problèmes et les solutions de contournement qui peuvent survenir lors de l’utilisation de Firebase Cloud Messaging avec Xamarin. Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp n’est pas initialisé

Dans certains cas, le message d’erreur suivant peut s’afficher :

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Il s’agit d’un problème connu que vous pouvez résoudre en nettoyant la solution et en reconstruisant le projet (**générer > solution propre**, **Générer > régénérer la solution**).

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas décrit les étapes à suivre pour implémenter des notifications distantes Firebase Cloud Messaging dans une application Xamarin. Android. Il a décrit comment installer les packages requis pour les communications FCM et explique comment configurer le manifeste Android pour l’accès aux serveurs FCM. Il fournit un exemple de code qui illustre comment vérifier la présence de Google Play Services. Il a démontré comment implémenter un service d’écoute d’ID d’instance qui négocie avec FCM pour un jeton d’inscription, et il a expliqué comment ce code crée des notifications en arrière-plan pendant que l’application est en arrière-plan. Il a expliqué comment s’abonner aux messages de rubrique et a fourni un exemple d’implémentation d’un service d’écoute de message qui est utilisé pour recevoir et afficher des notifications distantes pendant que l’application s’exécute au premier plan.

## <a name="related-links"></a>Liens connexes

- [FCMNotifications (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/firebase-fcmnotifications)
- [Messagerie cloud Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [À propos des messages FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
