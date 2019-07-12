---
title: Notifications à distance avec Firebase Cloud Messaging
description: Cette procédure pas à pas fournit une explication pas à pas montrant comment utiliser Firebase Cloud Messaging pour implémenter des notifications à distance (également appelées notifications push) dans une application Xamarin.Android. Il illustre comment implémenter les différentes classes qui sont nécessaires pour les communications avec Firebase Cloud Messaging (FCM), fournit des exemples montrant comment configurer le manifeste Android pour accéder à FCM et montre en aval de la messagerie à l’aide de la Firebase Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: a50a2014e28becacb2c9f4965b7f3377be57ab16
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830319"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notifications à distance avec Firebase Cloud Messaging

_Cette procédure pas à pas fournit une explication pas à pas montrant comment utiliser Firebase Cloud Messaging pour implémenter des notifications à distance (également appelées notifications push) dans une application Xamarin.Android. Il illustre comment implémenter les différentes classes qui sont nécessaires pour les communications avec Firebase Cloud Messaging (FCM), fournit des exemples montrant comment configurer le manifeste Android pour accéder à FCM et montre en aval de la messagerie à l’aide de la Firebase Console._

## <a name="fcm-notifications-overview"></a>Vue d’ensemble des notifications FCM

Dans cette procédure pas à pas, une application de base appelé **FCMClient** sera créé pour illustrer l’essentiel de la messagerie de FCM. **FCMClient** vérifie la présence des Services Google Play, reçoit les jetons d’inscription de FCM, affiche des notifications à distance que vous envoyez à partir de la Console Firebase et s’abonne aux messages de la rubrique :

[![Capture d’écran de l’exemple d’application](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Les sujets suivants sont présentés :

1.  Notifications en arrière-plan

2.  Messages de rubrique

3.  Notifications de premier plan

Au cours de cette procédure pas à pas, vous ajouterez progressivement des fonctionnalités à **FCMClient** et exécutez-le sur un appareil ou un émulateur pour comprendre comment elle interagit avec FCM. Vous allez utiliser la journalisation pour témoin transactions d’application en direct avec des serveurs FCM, et vous pourrez observer la façon dont les notifications sont générées à partir de messages FCM que vous entrez dans l’interface utilisateur graphique de Notifications de la Console Firebase.

## <a name="requirements"></a>Configuration requise


Il peut être utile pour vous familiariser avec la [différents types de messages](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) qui peuvent être envoyés par Firebase Cloud Messaging. La charge utile du message détermine comment une application cliente reçoit et traite le message.

Avant de poursuivre cette procédure pas à pas, vous devez obtenir les informations d’identification nécessaires pour utiliser des serveurs FCM de Google. Ce processus est expliqué dans [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
En particulier, vous devez télécharger le **google-services.JSON** fichier à utiliser avec l’exemple de code présenté dans cette procédure pas à pas. Si vous n’avez pas encore créé un projet dans la Console Firebase (ou si vous n’avez pas encore téléchargé le **google-services.JSON** fichier), consultez [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Pour exécuter l’exemple d’application, vous devez un émulateur compatible avec Firebase ou un appareil de test Android. Firebase Cloud Messaging prend en charge les clients qui exécutent sur Android 4.0 ou version ultérieure, et ces appareils doivent également avoir installé l’application Google Play Store (Google Play Services 9.2.1 ou ultérieure est requise). Si vous n’avez pas encore installé sur votre appareil à l’application Google Play Store, visitez le [Google Play](https://support.google.com/googleplay) site web pour télécharger et l’installer. Alternativement, vous pouvez utiliser l’émulateur du Kit Android SDK avec Google Play Services est installé au lieu d’un appareil de test (il est inutile d’installer le Store Play Google si vous utilisez l’émulateur du Kit Android SDK).

## <a name="start-an-app-project"></a>Démarrer un projet d’application

Pour commencer, créez un nouveau projet Xamarin.Android vide nommé **FCMClient**. Si vous n’êtes pas familiarisé avec la création de projets de Xamarin.Android, consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Une fois la nouvelle application est créée, l’étape suivante consiste à définir le nom du package et installer plusieurs packages NuGet qui seront utilisés pour la communication avec FCM.

### <a name="set-the-package-name"></a>Définir le nom du package

Dans [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), vous avez spécifié un nom de package pour l’application prenant en charge FCM. Ce nom de package sert également le [ *ID d’application* ](./firebase-cloud-messaging.md#fcm-in-action-app-id) qui est associé le [clé API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configurer l’application pour utiliser ce nom de package :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Ouvrez les propriétés de la **FCMClient** projet.

2.  Dans le **manifeste Android** , définissez le nom du package.

Dans l’exemple suivant, le nom du package a la valeur `com.xamarin.fcmexample`:

[![Définition du nom de package](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Pendant que vous mettez à jour le **manifeste Android**, également une vérification pour vous assurer que le `Internet` autorisation est activée.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1.  Ouvrez les propriétés de la **FCMClient** projet.

2.  Dans le **Application Android** , définissez le nom du package.

Dans l’exemple suivant, le nom du package a la valeur `com.xamarin.fcmexample`:

[![Définition du nom de package](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Pendant que vous mettez à jour le **manifeste Android**, également une vérification pour vous assurer que le `INTERNET` autorisation est activée (sous **autorisations requises**).

-----

> [!IMPORTANT]
> L’application cliente ne pourra pas recevoir un jeton d’inscription FCM si ce nom de package n’est pas *exactement* correspond au nom de package qui a été entré dans la Console Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Ajoutez le package de Base de Xamarin Google Play Services

Étant donné que Firebase Cloud Messaging dépend des Services Google Play, le [Xamarin Google Play Services - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) package NuGet doit être ajouté au projet Xamarin.Android. Vous avez besoin version 29.0.0.2 ou version ultérieure.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Dans Visual Studio, cliquez sur **références > Gérer les Packages NuGet...** .

2.  Cliquez sur le **Parcourir** onglet et recherchez **Xamarin.GooglePlayServices.Base**.

3.  Installez ce package dans le **FCMClient** projet :

    [![L’installation de Base des Services Google Play](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1.  Dans Visual Studio pour Mac, cliquez sur **Packages > ajouter des Packages...** .

2.  Recherchez **Xamarin.GooglePlayServices.Base**.

3.  Installez ce package dans le **FCMClient** projet :

    [![L’installation de Base des Services Google Play](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Si vous obtenez une erreur pendant l’installation de NuGet, fermez le **FCMClient** de projet, ouvrez-le à nouveau et recommencez l’installation de NuGet.

Lorsque vous installez **Xamarin.GooglePlayServices.Base**, toutes les dépendances nécessaires sont également installés. Modifier **MainActivity.cs** et ajoutez le code suivant `using` instruction :

```csharp
using Android.Gms.Common;
```

Cette instruction effectue la `GoogleApiAvailability` classe **Xamarin.GooglePlayServices.Base** disponibles pour **FCMClient** code.
`GoogleApiAvailability` est utilisé pour vérifier la présence des Services Google Play.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Ajoutez le package Xamarin Firebase Messaging

Pour recevoir des messages à partir de FCM, le [Xamarin Firebase - messagerie](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) package NuGet doit être ajouté au projet d’application. Sans ce package, une application Android ne peut pas recevoir des messages à partir de serveurs FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Dans Visual Studio, cliquez sur **références > Gérer les Packages NuGet...** .

2. Recherchez **Xamarin.Firebase.Messaging**.

3.  Installez ce package dans le **FCMClient** projet :

    [![L’installation de Xamarin Firebase de messagerie](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1.  Dans Visual Studio pour Mac, cliquez sur **Packages > ajouter des Packages...** .

2.  Recherchez **Xamarin.Firebase.Messaging**.

3.  Installez ce package dans le **FCMClient** projet :

    [![L’installation de Xamarin Firebase de messagerie](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Lorsque vous installez **Xamarin.Firebase.Messaging**, toutes les dépendances nécessaires sont également installés.

Ensuite, modifiez **MainActivity.cs** et ajoutez le code suivant `using` instructions :

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Les deux premières instructions rendent les types dans les **Xamarin.Firebase.Messaging** disponibles pour le package NuGet **FCMClient** code. **Android.Util** ajoute des fonctionnalités de journalisation qui seront utilisée pour observer les transactions avec FMS.

### <a name="add-googleplayservices-json"></a>Ajoutez le fichier JSON des Services Google

L’étape suivante consiste à ajouter le **google-services.JSON** fichier dans le répertoire racine de votre projet :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Copie **google-services.JSON** au dossier du projet.

2.  Ajouter **google-services.JSON** au projet d’application (cliquez sur **afficher tous les fichiers** dans le **l’Explorateur de solutions**, avec le bouton droit cliquez sur **google-services.JSON**, puis sélectionnez **inclure dans le projet**).

3.  Sélectionnez **google-services.JSON** dans le **l’Explorateur de solutions** fenêtre.

4.  Dans le **propriétés** volet, définissez le **Action de génération** à **GoogleServicesJson**:

    [![Définition de l’action de génération sur GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Si le **GoogleServicesJson** action de génération n’est pas affichée, enregistrez et fermez la solution, puis le rouvrir.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1.  Copie **google-services.JSON** au dossier du projet.

2.  Ajouter **google-services.JSON** au projet d’application.

3.  Avec le bouton droit **google-services.JSON**.

4.  Définir le **Action de génération** à **GoogleServicesJson**:

    [![Définition de l’action de génération sur GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Lorsque **google-services.JSON** est ajouté au projet (et le **GoogleServicesJson** action de génération est définie), le processus de génération extrait l’ID client et [clé API](./firebase-cloud-messaging.md#fcm-in-action-api-key) , puis ajoute ces informations d’identification à la fusion/générée **AndroidManifest.xml** qui réside sur **obj/Debug/android/AndroidManifest.xml**. Ce processus de fusion ajoute automatiquement toutes les autorisations et autres éléments FCM qui sont nécessaires pour la connexion aux serveurs FCM.


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Recherchez les Services Google Play et créer un canal de notification

Google recommande que les applications Android vérifier la présence de l’APK de Services Google Play avant d’accéder aux fonctionnalités des Services Google Play (pour plus d’informations, consultez [recherchez Google Play services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Une disposition initiale pour l’interface utilisateur de l’application sera créée en premier. Modifier **Resources/layout/Main.axml** et remplacez son contenu par le code XML suivant :

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

Cela `TextView` doit être utilisée pour afficher les messages qui indiquent si Google Play Services est installé. Enregistrez les modifications apportées à **Main.axml**.


Modifier **MainActivity.cs** et ajoutez les variables d’instance suivant à la `MainActivity` classe :

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Les variables `CHANNEL_ID` et `NOTIFICATION_ID` sera utilisé dans la méthode [ `CreateNotificationChannel` ](#create-notification-channel-code) qui sera ajouté à `MainActivity` plus loin dans cette procédure pas à pas.


Dans l’exemple suivant, la `OnCreate` méthode vérifiera que Google Play Services est disponible avant l’application tente d’utiliser les services FCM.
Ajoutez la méthode suivante à la `MainActivity` classe :

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

Ce code vérifie le périphérique pour voir si le fichier APK Google Play Services est installé. S’il n’est pas installé, un message s’affiche dans le `TextBox` qui demande à l’utilisateur à télécharger un fichier APK depuis le Store Play Google (ou pour l’activer dans les paramètres du périphérique système).

<a name="create-notification-channel-code"></a>Applications qui s’exécutent sur Android 8.0 (niveau d’API 26) ou version ultérieure doivent créer un [ _canal de notification_ ](~/android/app-fundamentals/notifications/local-notifications.md) pour leurs notifications de publication.  Ajoutez la méthode suivante à la `MainActivity` classe qui crée le canal de notification (si nécessaire) :

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

`IsPlayServicesAvailable` est appelée à la fin de `OnCreate` afin que les Services Google Play s’exécute à chaque démarrage de l’application. La méthode `CreateNotificationChannel` est appelée pour s’assurer qu’un canal de notification existe pour les appareils exécutant Android 8 ou version ultérieure. Si votre application comporte un `OnResume` (méthode), il doit appeler `IsPlayServicesAvailable` de `OnResume` également. Complètement régénérer et exécuter l’application. Si tout est correctement configuré, vous devez voir un écran qui ressemble à la capture d’écran suivante :

[![Application indique que Google Play Services est disponible](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Si vous n’obtenez pas ce résultat, vérifiez que le fichier APK Google Play Services est installé sur votre appareil (pour plus d’informations, consultez [paramètre des Services Google Play](https://developers.google.com/android/guides/setup)).
Vérifiez également que vous avez ajouté le **Xamarin.Google.Play.Services.Base** package pour votre **FCMClient** projet comme expliqué précédemment.


## <a name="add-the-instance-id-receiver"></a>Ajouter le récepteur d’ID d’instance

L’étape suivante consiste à ajouter un service qui étend `FirebaseInstanceIdService` pour gérer la création, la rotation et la mise à jour de [les jetons d’inscription Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). Le `FirebaseInstanceIdService` service est requis pour FCM pouvoir envoyer des messages à l’appareil. Lorsque le `FirebaseInstanceIdService` service est ajouté à l’application cliente, l’application sera automatiquement recevoir des messages FCM et les afficher en tant que notifications chaque fois que l’application est lancé en arrière-plan.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Déclarez le récepteur dans le manifeste Android

Modifier **AndroidManifest.xml** et insérez le code suivant `<receiver>` éléments dans la `<application>` section :

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

-   Déclare un `FirebaseInstanceIdReceiver` implémentation qui fournit un [identificateur unique](https://developers.google.com/instance-id/) pour chaque instance d’application. Ce récepteur également authentifie et autorise des actions.

-   Déclare un interne `FirebaseInstanceIdInternalReceiver` implémentation qui est utilisée pour démarrer les services en toute sécurité.

-   Le [ID d’application](./firebase-cloud-messaging.md#fcm-in-action-app-id) est stocké dans le **google-services.JSON** fichier qui a été [ajouté au projet](#add-googleplayservices-json). Les liaisons de Xamarin.Android Firebase remplace le jeton `${applicationId}` avec l’ID d’application ; aucun code supplémentaire n’est requis par l’application cliente pour fournir l’ID d’application.

Le `FirebaseInstanceIdReceiver` est un `WakefulBroadcastReceiver` qui reçoit `FirebaseInstanceId` et `FirebaseMessaging` événements et les remet à la classe que vous dérivez de `FirebaseInstanceIdService`.

### <a name="implement-the-firebase-instance-id-service"></a>Implémenter le Service Firebase Instance ID

Le travail de l’inscription de l’application auprès de FCM est géré par la version personnalisée `FirebaseInstanceIdService` service que vous fournissez.
`FirebaseInstanceIdService` effectue les étapes suivantes :

1.  Utilise le [API d’ID Instance](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) pour générer des jetons de sécurité qui autorisent l’application cliente pour accéder à FCM et le serveur d’applications. En retour, l’application reçoit en retour un [jeton d’inscription](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) de FCM.

2.  Transmet le jeton d’inscription sur le serveur d’application si le serveur de l’application l’exige.

Ajoutez un nouveau fichier appelé **MyFirebaseIIDService.cs** et remplacez son code du modèle par le code suivant :

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

Ce service implémente un `OnTokenRefresh` méthode est appelée lorsque le jeton d’inscription est initialement créé ou modifié. Lorsque `OnTokenRefresh` s’exécute, il récupère le dernier jeton à partir de la `FirebaseInstanceId.Instance.Token` propriété (qui est mise à jour de façon asynchrone par FCM). Dans cet exemple, le jeton actualisé est enregistré afin qu’elle peut être affichée dans la fenêtre Sortie :

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` est appelé rarement : il est utilisé pour mettre à jour le jeton dans les circonstances suivantes :

-   Lorsque l’application est installée ou désinstallée.

-   Lorsque l’utilisateur supprime les données d’application.

-   Lorsque l’application efface l’ID d’Instance.

-   Lorsque la sécurité du jeton a été compromise.

En fonction de Google [ID d’Instance](https://developers.google.com/instance-id/guides/android-implementation) documentation, le service FCM Instance ID demande que l’application actualiser son jeton périodiquement (généralement, tous les 6 mois).

`OnTokenRefresh` appelle également `SendRegistrationToAppServer` pour associer l’inscription de l’utilisateur jeton avec le compte du côté serveur (le cas échéant) qui est géré par l’application :

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Étant donné que cette implémentation dépend de la conception du serveur d’application, un corps de méthode vide est fourni dans cet exemple. Si votre serveur d’application nécessite des informations d’inscription FCM, modifiez `SendRegistrationToAppServer` pour associer le jeton d’utilisateur FCM instance ID à n’importe quel compte côté serveur géré par votre application. (Notez que le jeton est opaque à l’application cliente).

Lorsqu’un jeton est envoyé au serveur d’application, `SendRegistrationToAppServer` doit conserver une valeur booléenne indiquant si le jeton a été envoyé au serveur. Si cette valeur est false, `SendRegistrationToAppServer` envoie le jeton pour le serveur d’applications &ndash; dans le cas contraire, le jeton a déjà été envoyé au serveur d’application dans un appel précédent. Dans certains cas (telle que celle-ci `FCMClient` exemple), le serveur d’application n’a pas besoin du jeton ; par conséquent, cette méthode n’est pas nécessaire dans cet exemple.

## <a name="implement-client-app-code"></a>Implémentez le code d’application client

Maintenant que les services de récepteur sont en place, code d’application client permettre être écrites pour tirer parti de ces services. Dans les sections suivantes, un bouton est ajouté à l’interface utilisateur pour vous connecter le jeton d’inscription (également appelé le *jeton d’ID d’Instance*), et plus de code est ajouté à `MainActivity` pour afficher `Intent` informations lorsque l’application est lancée à partir d’un notification :

[![Bouton de jeton de journal ajouté à l’écran de l’application](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Jetons de journal

Le code ajouté à cette étape est destiné uniquement à des fins de démonstration &ndash; une application cliente de production n’a pas besoin de se connecter les jetons d’inscription. Modifier **Resources/layout/Main.axml** et ajoutez le code suivant `Button` déclaration immédiatement après le `TextView` élément :

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

Ce code consigne le jeton en cours dans la fenêtre de sortie lorsque le **journal Jeton** l’appui sur bouton.

### <a name="handle-notification-intents"></a>Gérer les intentions de notification

Lorsque l’utilisateur appuie sur une notification émise à partir de **FCMClient**, toutes les données qui accompagne cette notification message est mis à disposition dans `Intent` extras. Modifier **MainActivity.cs** et ajoutez le code suivant en haut de la `OnCreate` (méthode) (avant l’appel à `IsPlayServicesAvailable`) :

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

Lanceur de l’application `Intent` est déclenché quand l’utilisateur appuie sur son message de notification, donc ce code enregistrera les données associées dans le `Intent` dans la fenêtre Sortie. Si un autre `Intent` doit être déclenché, le `click_action` champ du message de notification doit être définie sur qui `Intent` (le Lanceur `Intent` est utilisé quand aucun `click_action` est spécifié).


## <a name="background-notifications"></a>Notifications en arrière-plan

Générer et exécuter le **FCMClient** application. Le **journal Jeton** bouton s’affiche :

[![Bouton de jeton de journal s’affiche.](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Appuyez sur la **journal Jeton** bouton. Un message semblable au suivant doit être affiché dans la fenêtre de sortie d’IDE :

[![Jeton d’ID instance affichée dans la fenêtre Sortie](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La longue chaîne étiqueté avec **jeton** est le jeton d’ID d’instance que vous allez coller dans la Console Firebase &ndash; sélectionnez et copiez cette chaîne dans le Presse-papiers. Si vous ne voyez pas un jeton d’ID instance, ajoutez la ligne suivante en haut de la `OnCreate` méthode pour vérifier que **google-services.JSON** a été analysé correctement :

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

Le `google_app_id` valeur enregistrée dans la fenêtre sortie doit correspondre à la `mobilesdk_app_id` valeur enregistrée dans **google-services.JSON**.

### <a name="send-a-message"></a>Envoyer un message

Connectez-vous à la [Console Firebase](https://console.firebase.google.com), sélectionnez votre projet, cliquez sur **Notifications**, puis cliquez sur **envoyer votre premier MESSAGE**:

[![Votre premier Message bouton Envoyer](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Sur le **composer un message** page, entrez le texte du message et sélectionnez **seul appareil**. Copiez le jeton d’ID instance de la fenêtre de sortie d’IDE et collez-la dans la **jeton d’inscription FCM** champ de la Console Firebase :

[![Composer la boîte de dialogue de message](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Sur l’appareil Android (ou l’émulateur), l’application d’arrière-plan en appuyant sur Android **vue d’ensemble** bouton et toucher l’écran d’accueil. Quand l’appareil est prêt, cliquez sur **envoyer un MESSAGE** dans la Console Firebase :

[![Message bouton Envoyer](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Lorsque le **message de révision** boîte de dialogue s’affiche, cliquez sur **envoyer**.
L’icône de notification doit apparaître dans la zone de notification de l’appareil (ou l’émulateur) :

[![Icône de notification s’affiche.](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Ouvrez l’icône de notification pour afficher le message. Le message de notification doit être exactement ce qui a été tapé dans le **texte du Message** champ de la Console Firebase :

[![Message de notification s’affiche sur l’appareil](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Appuyez sur l’icône de notification pour lancer le **FCMClient** application. Le `Intent` extras envoyés à **FCMClient** sont répertoriés dans la fenêtre de sortie d’IDE :

[![Répertorie les extras intentionnels à partir de la clé, ID de message et la clé de réduction](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

Dans cet exemple, le **de** clé est définie pour le numéro de projet Firebase de l’application (dans cet exemple, `41590732`) et le **collapse_key** est défini sur son nom de package ( **com.xamarin.fcmexample**).
Si vous ne recevez pas un message, essayez de supprimer le **FCMClient** application sur l’appareil (ou l’émulateur) et répétez les étapes ci-dessus.


> [!NOTE]
> Si vous force-fermer l’application, FCM cessera de remise de notifications. Android empêche les diffusions de service d’arrière-plan de par inadvertance ou inutilement lançant des composants d’applications arrêtées. (Pour plus d’informations sur ce comportement, consultez [lancer des contrôles sur les applications arrêtées](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Pour cette raison, il est nécessaire pour désinstaller manuellement l’application chaque fois que vous l’exécuter et l’arrêter à partir d’une session de débogage &ndash; cela force FCM pour générer un nouveau jeton afin que les messages continuent à recevoir.

### <a name="add-a-custom-default-notification-icon"></a>Ajouter une icône de notification personnalisée par défaut

Dans l’exemple précédent, l’icône de notification est défini sur l’icône de l’application. Le code XML suivant configure une icône personnalisée par défaut pour les notifications. Android affiche cette icône personnalisée par défaut pour tous les messages de notification lorsque l’icône de notification n’est pas définie explicitement.

Pour ajouter une icône de notification personnalisée par défaut, ajoutez votre icône pour le **ressources/drawable** directory, modifier **AndroidManifest.xml**et insérez le code suivant `<meta-data>` élément dans le `<application>`section :

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

Dans cet exemple, l’icône de notification qui réside sur **ressources/drawable/ic\_stat\_ic\_notification.png** sera utilisé en tant que l’icône de notification personnalisée par défaut. Si une icône par défaut personnalisée n’est pas configurée dans **AndroidManifest.xml** et aucune icône n’est définie dans la charge utile de notification, Android utilise l’icône d’application en tant que l’icône de notification (comme indiqué dans la notification icône capture d’écran ci-dessus).

## <a name="handle-topic-messages"></a>Gérer les messages de rubrique

Le code écrit jusqu'à présent gère les jetons d’inscription et ajoute des fonctionnalités de notification à distance à l’application. L’exemple suivant ajoute le code qui écoute les *messages de rubrique* et les transmet à l’utilisateur distantes comme des notifications. Messages de rubrique sont FCM qui sont envoyés à un ou plusieurs périphériques s’abonner à une rubrique particulière. Pour plus d’informations sur les messages de rubrique, consultez [rubrique messagerie](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>S’abonner à une rubrique

Modifier **Resources/layout/Main.axml** et ajoutez le code suivant `Button` déclaration immédiatement après la précédente `Button` élément :

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Ce code XML ajoute un **s’abonner aux notifications** bouton à la disposition.
Modifier **MainActivity.cs** et ajoutez le code suivant à la fin de la `OnCreate` méthode :

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Ce code localise le **s’abonner aux notifications** bouton dans la disposition et assigne son gestionnaire de clic au code qui appelle `FirebaseMessaging.Instance.SubscribeToTopic`, en passant dans la rubrique souscrite, _news_. Lorsque l’utilisateur appuie sur le **s’abonner** bouton, l’application s’abonne à la _news_ rubrique. Dans la section suivante, un _news_ rubrique message sera envoyé à partir de l’interface utilisateur graphique de Notifications de la Console Firebase.

### <a name="send-a-topic-message"></a>Envoyer un message de rubrique

Désinstallez l’application, régénérez ce dernier et exécutez-le à nouveau. Cliquez sur le **s’abonner aux Notifications** bouton :

[![S’abonner à un bouton de Notifications](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Si l’application s’est abonnée avec succès, vous devez voir **synchronisation rubrique réussie** fenêtre de sortie dans l’IDE :

[![Fenêtre Sortie affiche le message de synchronisation réussie de rubrique](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Utilisez les étapes suivantes pour envoyer un message de la rubrique :

1.  Dans la Console Firebase, cliquez sur **nouveau MESSAGE**.

2.  Sur le **composer un message** page, entrez le texte du message et sélectionnez **rubrique**.

3.  Dans le **rubrique** menu déroulant, sélectionnez la rubrique intégrée, **news**:

    [![En sélectionnant la rubrique des actualités](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  Sur l’appareil Android (ou l’émulateur), l’application d’arrière-plan en appuyant sur Android **vue d’ensemble** bouton et toucher l’écran d’accueil.

5.  Quand l’appareil est prêt, cliquez sur **envoyer un MESSAGE** dans la Console Firebase.

6.  Consultez la fenêtre de sortie d’IDE pour voir **/rubriques/news** dans la sortie du journal :

    [![À partir de /topic/news est signalée](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Lorsque ce message est visible dans la fenêtre Sortie, l’icône de notification doit également apparaître dans la zone de notification sur l’appareil Android. Ouvrez l’icône de notification pour afficher le message de la rubrique :

[![Le message de la rubrique s’affiche en tant que notification](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Si vous ne recevez pas un message, essayez de supprimer le **FCMClient** application sur l’appareil (ou l’émulateur) et répétez les étapes ci-dessus.

## <a name="foreground-notifications"></a>Notifications de premier plan

Pour recevoir des notifications dans les applications foregrounded, vous devez implémenter `FirebaseMessagingService`. Ce service est également nécessaire pour la réception des charges utiles de données et pour envoyer des messages en amont. Les exemples suivants illustrent comment implémenter un service qui étend `FirebaseMessagingService` &ndash; l’application résultante sera en mesure de gérer les notifications à distance pendant son exécution au premier plan.

### <a name="implement-firebasemessagingservice"></a>Implémenter FirebaseMessagingService

Le `FirebaseMessagingService` service est chargé pour recevoir et traiter les messages à partir de Firebase. Chaque application doit sous-classe ce type et que vous remplacez le `OnMessageReceived` pour traiter un message entrant. Lorsqu’une application est au premier plan, le `OnMessageReceived` rappel gère toujours le message.

> [!NOTE]
> Applications ont uniquement les 10 secondes traiter un Message de Cloud Firebase entrant. Tout le travail prend plus de temps que cela doit être planifiée pour l’exécution en arrière-plan à l’aide d’une bibliothèque comme les [Planificateur de travaux Android](~/android/platform/android-job-scheduler.md) ou [répartiteur de travail Firebase](~/android/platform/firebase-job-dispatcher.md).

Ajoutez un nouveau fichier appelé **MyFirebaseMessagingService.cs** et remplacez son code du modèle par le code suivant :

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

Notez que le `MESSAGING_EVENT` filtre intent doit être déclaré afin que les nouveaux messages FCM sont dirigées vers `MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Lorsque l’application cliente reçoit un message de FCM, `OnMessageReceived` extrait le contenu du message à partir du passé `RemoteMessage` objet en appelant son `GetNotification` (méthode). Ensuite, il enregistre le contenu du message afin qu’elle peut être affichée dans la fenêtre de sortie d’IDE :

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Si vous définissez des points d’arrêt dans `FirebaseMessagingService`, votre session de débogage peut ou peut ne pas toucher ces points d’arrêt en raison de la façon dont FCM remet les messages.


### <a name="send-another-message"></a>Envoyer un autre message

Désinstallez l’application, régénérez ce dernier, exécutez-le à nouveau, suivez ces étapes pour envoyer un autre message :

1.  Dans la Console Firebase, cliquez sur **nouveau MESSAGE**.

2.  Sur le **composer un message** page, entrez le texte du message et sélectionnez **seul appareil**.

3.  Copiez la chaîne de jeton à partir de la fenêtre de sortie d’IDE et collez-la dans la **jeton d’inscription FCM** champ de la Console Firebase comme avant.

4.  Assurez-vous que l’application s’exécute au premier plan, puis cliquez sur **envoyer un MESSAGE** dans la Console Firebase :

    [![Envoi d’un autre message à partir de la Console](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Lorsque le **message de révision** boîte de dialogue s’affiche, cliquez sur **envoyer**.

6.  Le message entrant est enregistré dans la fenêtre de sortie d’IDE :

    [![Corps du message est imprimé dans la fenêtre Sortie](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>Ajouter un expéditeur notification locale

Dans cet exemple restant, le message entrant FCM est être converti en une notification locale qui est lancée alors que l’application est en cours d’exécution au premier plan. Modifier **MyFirebaseMessageService.cs** et ajoutez le code suivant `using` instructions :

```csharp
using FCMClient;
using System.Collections.Generic;
```

Ajoutez la méthode suivante à `MyFirebaseMessagingService`:

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

Pour distinguer cette notification à partir des notifications de l’arrière-plan, ce code marque les notifications avec une icône qui diffère de l’icône d’application. Ajoutez le fichier [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) à **ressources/drawable** et l’inclure dans le **FCMClient** projet .

Le `SendNotification` méthode utilise `NotificationCompat.Builder` pour créer la notification, et `NotificationManagerCompat` est utilisée pour lancer la notification. La notification conserve un `PendingIntent` qui permet à l’utilisateur ouvrir l’application et afficher le contenu de la chaîne passée dans `messageBody`. Pour plus d’informations sur `NotificationCompat.Builder`, consultez [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

Appelez le `SendNotification` méthode à la fin de la `OnMessageReceived` méthode :

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

À la suite de ces modifications, `SendNotification` s’exécutera chaque fois qu’une notification est reçue alors que l’application est au premier plan, et la notification s’affiche dans la zone de notification.

Lorsqu’une application est en arrière-plan, le [charge utile du message](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) déterminera la façon dont le message est traité :

* **Notification** &ndash; messages seront envoyés à la **barre d’état système**. Une notification locale apparaîtront ici. Lorsque l’utilisateur appuie sur la notification lance l’application.
* **Données** &ndash; messages seront traités par `OnMessageReceived`.
* **Les deux** &ndash; les messages qui ont une notification et les données de charge utile seront remis à la barre d’état système. Lorsque l’application démarre, la charge utile de données apparaît dans le `Extras` de la `Intent` qui a été utilisé pour démarrer l’application.

Dans cet exemple, si l’application est lancé en arrière-plan, `SendNotification` s’exécutera si le message a une charge utile de données. Sinon, une notification d’arrière-plan (comme illustrée dans cette procédure pas à pas) sera lancée.

### <a name="send-the-last-message"></a>Envoyer le dernier message

Désinstallez l’application, régénérez ce dernier, exécutez-le à nouveau, puis procédez comme suit pour envoyer le dernier message :

1.  Dans la Console Firebase, cliquez sur **nouveau MESSAGE**.

2.  Sur le **composer un message** page, entrez le texte du message et sélectionnez **seul appareil**.

3.  Copiez la chaîne de jeton à partir de la fenêtre de sortie d’IDE et collez-la dans la **jeton d’inscription FCM** champ de la Console Firebase comme avant.

4.  Assurez-vous que l’application s’exécute au premier plan, puis cliquez sur **envoyer un MESSAGE** dans la Console Firebase :

    [![Envoi du message de premier plan](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Cette fois, le message a été enregistré dans la fenêtre de sortie est également empaqueté dans une nouvelle notification &ndash; l’icône de notification s’affiche dans la barre d’état de notification pendant l’exécution de l’application au premier plan :

[![Icône de notification pour le message de premier plan](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Lorsque vous ouvrez la notification, vous devez voir le dernier message qui a été envoyé à partir de l’interface utilisateur graphique de Notifications de la Console Firebase :

[![Notification de premier plan indiquée avec une icône de premier plan](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Déconnexion de FCM

Pour vous désabonner à partir d’une rubrique, appelez le [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) méthode sur le [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) classe. Par exemple, pour vous désabonner de le _news_ rubrique abonnée à plus haut, un **Unsubscribe** bouton peut être ajouté à la disposition par le code de gestionnaire suivant :

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Pour annuler l’inscription de l’appareil à partir de complètement FCM, supprimer l’ID d’instance en appelant le [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) méthode sur le [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) classe. Par exemple :

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Cet appel de méthode supprime l’ID d’instance et les données associées. Par conséquent, l’envoi périodiques de données FCM sur l’appareil est interrompue.


## <a name="troubleshooting"></a>Résolution de problèmes

Suivantes décrivent des problèmes liés et les solutions de contournement qui peuvent survenir lors de l’utilisation de Firebase Cloud Messaging avec Xamarin.Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp n’est pas initialisé.

Dans certains cas, vous pouvez voir ce message d’erreur :

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Il s’agit d’un problème connu que vous pouvez contourner par la solution de nettoyage et la régénération du projet (**Générer > Nettoyer la Solution**, **Générer > regénérer la Solution**).

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas détaillé les étapes d’implémentation Firebase Cloud Messaging des notifications à distance dans une application Xamarin.Android. Il décrit comment installer les packages requis nécessaires pour les communications de FCM, et que vous avez appris comment configurer le manifeste Android pour accéder aux serveurs FCM. Il fourni d’exemple de code qui montre comment vérifier la présence des Services Google Play. Il vous a montré comment implémenter un service d’écoute ID instance qui négocie avec FCM pour un jeton d’inscription, et que vous avez appris comment ce code crée les notifications en arrière-plan pendant que l’application est lancé en arrière-plan. Vous avez appris comment s’abonner aux messages de rubrique, et il a fourni une implémentation d’un service d’écoute de message qui est utilisé pour recevoir et afficher des notifications à distance pendant l’exécution de l’application au premier plan.


## <a name="related-links"></a>Liens connexes

- [FCMNotifications (sample)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Messagerie cloud Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [À propos des Messages FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
