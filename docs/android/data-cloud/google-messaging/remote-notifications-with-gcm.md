---
title: Notifications distantes avec Google Cloud Messaging
description: Cette procédure pas à pas fournit une explication pas à pas de l’utilisation de Google Cloud Messaging pour implémenter des notifications distantes (également appelées notifications push) dans une application Xamarin. Android. Elle décrit les différentes classes que vous devez implémenter pour communiquer avec Google Cloud Messaging (GCM), elle explique comment définir des autorisations dans le manifeste Android pour l’accès à GCM et illustre la messagerie de bout en bout avec un exemple de programme de test.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: a59f824e1d97966d8d0af92bc9bbcc8d80fcfa4d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249928"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notifications distantes avec Google Cloud Messaging

> [!WARNING]
> Google a supprimé le GCM le 10 avril 2018. Les documents et exemples de projets suivants peuvent ne plus être gérés. Les API serveur et client GCM de Google seront supprimées dès le 29 mai 2019. Google recommande de migrer des applications GCM vers Firebase Cloud Messaging (FCM). Pour plus d’informations sur la désapprobation et la migration de GCM, consultez [Google Cloud Messaging-Deprecated](https://developers.google.com/cloud-messaging/).
>
> Pour commencer à utiliser les notifications distantes à l’aide de Firebase Cloud Messaging avec Xamarin, consultez [notifications distantes avec FCM](remote-notifications-with-fcm.md).

_Cette procédure pas à pas fournit une explication pas à pas de l’utilisation de Google Cloud Messaging pour implémenter des notifications distantes (également appelées notifications push) dans une application Xamarin. Android. Elle décrit les différentes classes que vous devez implémenter pour communiquer avec Google Cloud Messaging (GCM), elle explique comment définir des autorisations dans le manifeste Android pour l’accès à GCM et illustre la messagerie de bout en bout avec un exemple de programme de test._

## <a name="gcm-notifications-overview"></a>Vue d’ensemble des notifications GCM

Dans cette procédure pas à pas, nous allons créer une application Xamarin. Android qui utilise Google Cloud Messaging (GCM) pour implémenter des notifications distantes (également appelées *notifications push*). Nous allons implémenter les divers services d’intention et d’écoute qui utilisent GCM pour la messagerie à distance, et nous testerons notre implémentation avec un programme de ligne de commande qui simule un serveur d’applications. 

Avant de pouvoir suivre cette procédure pas à pas, vous devez obtenir les informations d’identification nécessaires pour utiliser les serveurs GCM de Google. Ce processus est expliqué dans [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). En particulier, vous aurez besoin d’une *clé API* et d’un *ID d’expéditeur* à insérer dans l’exemple de code présenté dans cette procédure pas à pas. 

Nous allons utiliser les étapes suivantes pour créer une application cliente Xamarin. Android compatible GCM :

1. Installez les packages supplémentaires requis pour les communications avec les serveurs GCM.
2. Configurez les autorisations d’application pour l’accès aux serveurs GCM.
3. Implémentez le code pour vérifier la présence de Google Play Services. 
4. Implémentez un service d’intention d’inscription qui négocie avec GCM pour un jeton d’inscription.
5. Implémentez un service d’écoute d’ID d’instance qui écoute les mises à jour de jeton d’inscription à partir de GCM.
6. Implémentez un service d’écoute GCM qui reçoit des messages distants du serveur d’applications via GCM.

Cette application utilise une nouvelle fonctionnalité GCM appelée messagerie des *rubriques*. Dans la rubrique messagerie, le serveur d’applications envoie un message à une rubrique plutôt qu’à une liste d’appareils individuels. Les appareils qui s’abonnent à cette rubrique peuvent recevoir des messages de rubrique en tant que notifications push. Pour plus d’informations sur la messagerie de rubrique GCM, consultez la [rubrique mise en œuvre](https://developers.google.com/cloud-messaging/topic-messaging)de la messagerie Google. 

Lorsque l’application cliente est prête, nous allons implémenter une application C# de ligne de commande qui envoie une notification push à notre application cliente via GCM. 

## <a name="walkthrough"></a>Procédure pas à pas

Pour commencer, nous allons créer une solution vide appelée **RemoteNotifications**. Ensuite, nous allons ajouter un nouveau projet Android à cette solution qui est basée sur le modèle d' **application Android** . Appelons ce projet **ClientApp**. (Si vous n’êtes pas familiarisé avec la création de projets Xamarin. Android, consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) Le projet **ClientApp** contiendra le code de l’application cliente Xamarin. Android qui reçoit les notifications distantes via GCM. 

### <a name="add-required-packages"></a>Ajouter les packages requis

Avant de pouvoir implémenter notre code d’application cliente, nous devons installer plusieurs packages que nous utiliserons pour la communication avec GCM. En outre, nous devons ajouter l’application Google Play Store à notre appareil s’il n’est pas déjà installé.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Ajouter le package Xamarin Google Play Services GCM

Pour recevoir des messages de Google Cloud Messaging, l’infrastructure [Google Play services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) doit être présente sur l’appareil. Sans ce Framework, une application Android ne peut pas recevoir de messages des serveurs GCM. Google Play Services s’exécute en arrière-plan pendant que l’appareil Android est sous tension, en écoutant les messages de GCM. Lorsque ces messages arrivent, Google Play Services convertit les messages en intentions, puis diffuse ces intentions aux applications qui sont inscrites pour eux. 

Dans Visual Studio, cliquez avec le bouton droit sur **références > gérer les packages NuGet...** ; dans Visual Studio pour Mac, cliquez avec le bouton droit sur **packages > ajouter des packages**.... Recherchez **Xamarin Google Play services-GCM** et installez ce package dans le projet **ClientApp** : 

[![Installation de Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Quand vous installez **Xamarin Google Play services-GCM**, **Xamarin Google Play Services-base** est installé automatiquement. Si vous recevez une erreur, définissez le paramètre *Android au minimum* du projet sur une valeur autre que **compiler à l’aide de la version du kit de développement logiciel (SDK)** et recommencez l’installation de NuGet. 

Ensuite, modifiez **MainActivity.cs** et ajoutez les instructions `using` suivantes :

```csharp
using Android.Gms.Common;
using Android.Util;
```

Ainsi, les types du package Google Play Services GMS sont disponibles pour notre code, et il ajoute des fonctionnalités de journalisation que nous allons utiliser pour suivre nos transactions avec la GMS. 

#### <a name="google-play-store"></a>Google Play Store

Pour recevoir des messages de GCM, l’application Google Play Store doit être installée sur l’appareil. (Chaque fois qu’une application Google Play est installée sur un appareil, Google Play Store est également installé. il est donc probable qu’elle est déjà installée sur votre appareil de test.) Sans Google Play, une application Android ne peut pas recevoir de messages de GCM. Si vous n’avez pas encore installé l’application Google Play Store sur votre appareil, visitez le site Web [Google Play](https://support.google.com/googleplay) pour télécharger et installer Google Play. 

Vous pouvez également utiliser un émulateur Android exécutant Android 2,2 ou une version ultérieure au lieu d’un appareil de test (vous n’avez pas besoin d’installer Google Play Store sur un émulateur Android). Toutefois, si vous utilisez un émulateur, vous devez utiliser le Wi-Fi pour vous connecter à GCM et vous devez ouvrir plusieurs ports dans votre pare-feu Wi-Fi, comme expliqué plus loin dans cette procédure pas à pas. 

### <a name="set-the-package-name"></a>Définir le nom du package

Dans [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), nous avons spécifié un nom de package pour notre application GCM (ce nom de package sert également d' *ID d’application* associé à notre clé d’API et à l’ID de l’expéditeur). Nous allons ouvrir les propriétés du projet **ClientApp** et définir le nom du package sur cette chaîne. Dans cet exemple, nous définissons le nom du `com.xamarin.gcmexample`package sur :

[![Définition du nom du package](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Notez que l’application cliente ne sera pas en mesure de recevoir un jeton d’inscription de GCM si ce nom de package ne correspond pas *exactement* au nom du package que nous avons entré dans la console de développement Google. 

### <a name="add-permissions-to-the-android-manifest"></a>Ajouter des autorisations au manifeste Android

Les autorisations suivantes doivent être configurées pour une application Android pour pouvoir recevoir des notifications de Google Cloud Messaging : 

- `com.google.android.c2dm.permission.RECEIVE`&ndash; Accorde à notre application l’autorisation d’inscrire et de recevoir des messages de Google Cloud Messaging. (Qu’est `c2dm` -ce que cela signifie ? Il s’agit de la _messagerie Cloud vers appareil_, qui est le prédécesseur désormais déconseillé de GCM. 
    GCM utilise `c2dm` toujours un grand nombre de ses chaînes d’autorisation.) 

- `android.permission.WAKE_LOCK`&ndash; (Facultatif) empêche le processeur de l’appareil de passer en mode veille lors de l’écoute d’un message. 

- `android.permission.INTERNET`&ndash; Octroie l’accès à Internet afin que l’application cliente puisse communiquer avec GCM. 

- *package_name inscrit l’application* `.permission.C2D_MESSAGE` auprès d’Android et demande l’autorisation de recevoir en exclusivité tous les messages C2D (Cloud à périphérique). &ndash; Le préfixe *package_name* est le même que l’ID de votre application. 

Nous allons définir ces autorisations dans le manifeste Android. Modifions **fichier AndroidManifest. xml** et remplacez le contenu par le code XML suivant : 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

Dans le code XML ci-dessus, remplacez *YOUR_PACKAGE_NAME* par le nom du package pour votre projet d’application cliente. Par exemple, `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Rechercher Google Play Services

Pour cette procédure pas à pas, nous créons une application complète avec une seule `TextView` dans l’interface utilisateur. Cette application n’indique pas directement l’interaction avec GCM. Au lieu de cela, nous allons regarder la fenêtre de sortie pour voir comment notre application se passe avec GCM, et nous allons vérifier dans le tiroir de notification les nouvelles notifications à mesure qu’elles arrivent. 

Tout d’abord, nous allons créer une disposition pour la zone de message. Modifiez **Resources. Layout. main. AXML** et remplacez le contenu par le code XML suivant : 

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

Enregistrez **main. AXML** et fermez-le.

Au démarrage de l’application cliente, nous voulons vérifier que Google Play Services est disponible avant de tenter de contacter GCM. Modifiez **MainActivity.cs** et remplacez la ``count`` déclaration de la variable d’instance par la déclaration de la variable d’instance suivante : 

```csharp
TextView msgText;
```

Ensuite, ajoutez la méthode suivante à la classe **MainActivity** : 

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
            msgText.Text = "Sorry, this device is not supported";
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

Ce code vérifie l’appareil pour voir si le Google Play Services APK est installé. S’il n’est pas installé, un message s’affiche dans la zone de message qui indique à l’utilisateur de télécharger un APK à partir de la Google Play Store (ou de l’activer dans les paramètres système de l’appareil). Étant donné que nous souhaitons exécuter cette vérification au démarrage de l’application cliente, nous allons ajouter un appel à cette méthode à `OnCreate`la fin de. 

Ensuite, remplacez la `OnCreate` méthode par le code suivant :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Ce code vérifie la présence de l’Google Play Services APK et écrit le résultat dans la zone de message. 

Régénérez et exécutez complètement l’application. Un écran semblable à celui-ci doit s’afficher : 

[![Google Play Services disponible](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Si vous n’obtenez pas ce résultat, vérifiez que le Google Play Services APK est installé sur votre appareil et que le package **Google Play services-GCM Xamarin** est ajouté à votre projet **ClientApp** , comme expliqué précédemment. Si vous recevez une erreur de build, essayez de nettoyer la solution et de générer à nouveau le projet. 

Nous allons ensuite écrire du code pour contacter GCM et obtenir un jeton d’inscription.

### <a name="register-with-gcm"></a>S’inscrire auprès de GCM

Pour que l’application puisse recevoir des notifications distantes du serveur d’applications, elle doit s’inscrire auprès de GCM et récupérer un jeton d’inscription. Le travail d’inscription de notre application avec GCM est géré par `IntentService` un que nous créons. Notre `IntentService` effectue les étapes suivantes : 

1. Utilise l’API [InstanceID](https://developers.google.com/instance-id/) pour générer des jetons de sécurité qui autorisent notre application cliente à accéder au serveur d’applications. En retour, nous obtenons un jeton d’inscription à partir de GCM.

2. Transfère le jeton d’inscription au serveur d’applications (si le serveur d’applications en a besoin).

3. S’abonne à un ou plusieurs canaux de rubrique de notification.

Après cela `IntentService`, nous allons le tester pour voir si nous obtenons un jeton d’inscription à partir de GCM.

Ajoutez un nouveau fichier appelé **RegistrationIntentService.cs** et remplacez le code du modèle par ce qui suit :

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

Dans l’exemple de code ci-dessus, remplacez *YOUR_SENDER_ID* par le numéro d’ID de l’expéditeur pour votre projet d’application cliente. Pour obtenir l’ID de l’expéditeur pour votre projet : 

1. Connectez-vous à la [console Google Cloud](https://console.cloud.google.com/) et sélectionnez le nom de votre projet dans le menu déroulant. Dans le volet d' **informations du projet** qui s’affiche pour votre projet, cliquez sur **accéder aux paramètres du projet**:

    [![Sélection du projet XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2. Dans la page **paramètres** , recherchez le **numéro** &ndash; de projet. il s’agit de l’ID de l’expéditeur pour votre projet :

    [![Numéro de projet affiché](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Nous souhaitons démarrer notre `RegistrationIntentService` lorsque l’exécution de l’application démarre. Modifiez **MainActivity.cs** et modifiez la `OnCreate` méthode afin que notre `RegistrationIntentService` soit démarré après avoir vérifié la présence de Google Play services : 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

Jetons maintenant un coup d’œil à chaque section de `RegistrationIntentService` pour comprendre son fonctionnement. 

Tout d’abord, nous anvoyons notre `RegistrationIntentService` avec l’attribut suivant pour indiquer que notre service ne doit pas être instancié par le système : 

```csharp
[Service (Exported = false)]
```

Le `RegistrationIntentService` constructeur nomme le thread de travail *RegistrationIntentService* pour faciliter le débogage. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

La fonctionnalité principale de `RegistrationIntentService` réside dans la `OnHandleIntent` méthode. Passons en revue ce code pour voir comment il inscrit notre application avec GCM.

#### <a name="request-a-registration-token"></a>Demander un jeton d’inscription

`OnHandleIntent`appelle d’abord la méthode [InstanceID. GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) de Google pour demander un jeton d’inscription à GCM. Nous encapsulent ce code `lock` dans un pour vous prémunir contre la possibilité d’avoir plusieurs &ndash; intentions `lock` d’inscription simultanément, ce qui garantit que ces intentions sont traitées de manière séquentielle. Si nous ne parvenons pas à obtenir un jeton d’inscription, une exception est levée et l’erreur est journalisée. Si l’inscription réussit, `token` est défini sur le jeton d’inscription que nous avons reçu de GCM : 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

#### <a name="forward-the-registration-token-to-the-app-server"></a>Transférer le jeton d’inscription au serveur d’applications

Si nous obtenons un jeton d’inscription (autrement dit, si aucune exception n’a été `SendRegistrationToAppServer` levée), nous appelons pour associer le jeton d’inscription de l’utilisateur au compte côté serveur (le cas échéant) qui est géré par notre application. Comme cette implémentation dépend de la conception du serveur d’applications, une méthode vide est fournie ici : 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Dans certains cas, le serveur d’applications n’a pas besoin du jeton d’inscription de l’utilisateur. dans ce cas, cette méthode peut être omise. Lorsqu’un jeton d’inscription est envoyé au serveur d’applications `SendRegistrationToAppServer` , doit conserver une valeur booléenne pour indiquer si le jeton a été envoyé au serveur. Si cette valeur booléenne est false, `SendRegistrationToAppServer` envoie le jeton au serveur &ndash; d’applications dans le cas contraire, le jeton a déjà été envoyé au serveur d’applications lors d’un appel précédent. 

#### <a name="subscribe-to-the-notification-topic"></a>S’abonner à la rubrique de notification

Ensuite, nous appelons notre `Subscribe` méthode pour indiquer à GCM que nous voulons s’abonner à une rubrique de notification. Dans `Subscribe`, nous appelons l’API [GcmPubSub. Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) pour abonner notre application cliente à tous `/topics/global`les messages sous :

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

Le serveur d’applications doit envoyer des messages `/topics/global` de notification à si nous les recevons. Notez que le nom de la `/topics` rubrique sous peut être tout ce que vous voulez, tant que le serveur d’applications et l’application cliente acceptent ces noms. (Ici, nous avons choisi le `global` nom pour indiquer que nous voulons recevoir des messages sur toutes les rubriques prises en charge par le serveur d’applications.) 

Pour plus d’informations sur la messagerie de rubrique GCM côté serveur, consultez la page [Envoyer un message électronique de Google aux rubriques](https://developers.google.com/cloud-messaging/topic-messaging). 

#### <a name="implement-an-instance-id-listener-service"></a>Implémenter un service d’écoute d’ID d’instance

Les jetons d’inscription sont uniques et sécurisés ; Toutefois, l’application cliente (ou GCM) devra peut-être actualiser le jeton d’inscription en cas de réinstallation d’application ou de problème de sécurité. Pour cette raison, nous devons implémenter `InstanceIdListenerService` un qui répond aux demandes d’actualisation des jetons de GCM. 

Ajoutez un nouveau fichier appelé **InstanceIdListenerService.cs** et remplacez le code du modèle par ce qui suit : 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

Annoter avec l’attribut suivant pour indiquer que le service ne doit pas être instancié par le système et qu’il peut recevoir des demandes d’actualisation du jeton d’inscription GCM (également appelé ID d’instance) : `InstanceIdListenerService` 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

La `OnTokenRefresh` méthode de notre service `RegistrationIntentService` démarre afin de pouvoir intercepter le nouveau jeton d’inscription.

#### <a name="test-registration-with-gcm"></a>Tester l’inscription avec GCM

Régénérez et exécutez complètement l’application. Si vous recevez correctement un jeton d’inscription de GCM, le jeton d’inscription doit s’afficher dans la fenêtre sortie. Par exemple : 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Gérer les messages en aval 

Le code que nous avons implémenté jusqu’ici n’est que le code « Set-up ». Il vérifie si Google Play Services est installé et négocie avec GCM et le serveur d’applications pour préparer notre application cliente à la réception de notifications distantes. Toutefois, nous n’avons pas encore implémenté le code qui reçoit et traite réellement les messages de notification en aval. Pour ce faire, nous devons implémenter un *service d’écoute GCM*. Ce service reçoit des messages de rubrique du serveur d’applications et les diffuse localement en tant que notifications. Une fois ce service implémenté, nous allons créer un programme de test pour envoyer des messages à GCM afin que nous puissions voir si notre implémentation fonctionne correctement. 

#### <a name="add-a-notification-icon"></a>Icône Ajouter une notification

Commençons par ajouter une petite icône qui s’affichera dans la zone de notification lorsque la notification est lancée. Vous pouvez copier [cette icône](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) dans votre projet ou créer votre propre icône personnalisée. Nous allons nommer le fichier icône **ic_stat_button_click. png** et le copier dans le dossier **ressources/dessinable** . N’oubliez pas d’utiliser **ajouter > élément existant...** pour inclure ce fichier d’icône dans votre projet.

#### <a name="implement-a-gcm-listener-service"></a>Implémenter un service d’écoute GCM

Ajoutez un nouveau fichier appelé **GcmListenerService.cs** et remplacez le code du modèle par ce qui suit :

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

Jetons un coup d’œil à chaque section de notre `GcmListenerService` pour comprendre son fonctionnement. 

Tout d’abord, nous `GcmListenerService` anvoyons un attribut pour indiquer que ce service ne doit pas être instancié par le système, et nous incluons un filtre d’intention pour indiquer qu’il reçoit des messages GCM : 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Lorsque `GcmListenerService` reçoit un message de GCM, la `OnMessageReceived` méthode est appelée. Cette méthode extrait le contenu du message du passé `Bundle`, journalise le contenu du message (afin que nous puissions l’afficher dans la fenêtre Sortie) et appelle `SendNotification` pour lancer une notification locale avec le contenu du message reçu : 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

La `SendNotification` méthode utilise `Notification.Builder` pour créer la notification, puis elle utilise `NotificationManager` pour lancer la notification. En fait, cela convertit le message de notification à distance en une notification locale pour qu’il soit présenté à l’utilisateur.
Pour plus d’informations sur `Notification.Builder` l' `NotificationManager`utilisation de et de, consultez [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md).

#### <a name="declare-the-receiver-in-the-manifest"></a>Déclarer le récepteur dans le manifeste

Avant de pouvoir recevoir des messages de GCM, nous devons déclarer l’écouteur GCM dans le manifeste Android. Modifions **fichier AndroidManifest. xml** et remplacez la `<application>` section par le code XML suivant : 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

Dans le code XML ci-dessus, remplacez *YOUR_PACKAGE_NAME* par le nom du package pour votre projet d’application cliente. Dans notre exemple de procédure pas à pas, `com.xamarin.gcmexample`le nom du package est. 

Examinons ce que fait chaque paramètre de ce code XML :

|Paramètre|Description|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Déclare que notre application implémente un récepteur GCM qui capture et traite les messages de notification push entrants.|
|`com.google.android.c2dm.permission.SEND`|Déclare que seuls les serveurs GCM peuvent envoyer des messages directement à l’application.|
|`com.google.android.c2dm.intent.RECEIVE`|Filtre d’intention publicitaire que notre application gère les messages de diffusion à partir de GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Filtre d’intention : la publicité que notre application gère de nouvelles intentions d’inscription (autrement dit, nous avons implémenté un service d’écoute d’ID d’instance).|

Vous pouvez également décorer `GcmListenerService` avec ces attributs plutôt que de les spécifier en XML. ici, nous les spécifions dans **fichier AndroidManifest. xml** afin que les exemples de code soient plus faciles à suivre. 

### <a name="create-a-message-sender-to-test-the-app"></a>Créer un expéditeur de message pour tester l’application

Nous allons ajouter un C# projet d’application console du Bureau à la solution et l’appeler **MessageSender**. Nous allons utiliser cette application console pour simuler un serveur &ndash; d’applications qu’elle enverra des messages de notification à **ClientApp** via GCM. 

#### <a name="add-the-jsonnet-package"></a>Ajouter le package Json.NET

Dans cette application console, nous créons une charge utile JSON qui contient le message de notification que nous voulons envoyer à l’application cliente. Nous allons utiliser le package **JSON.net** dans **MessageSender** pour faciliter la création de l’objet JSON requis par GCM. Dans Visual Studio, cliquez avec le bouton droit sur **références > gérer les packages NuGet...** ; dans Visual Studio pour Mac, cliquez avec le bouton droit sur **packages > ajouter des packages**.... 

Nous allons Rechercher le package **JSON.net** et l’installer dans le projet : 

[![Installation du package Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)

#### <a name="add-a-reference-to-systemnethttp"></a>Ajouter une référence à System .net. http

Nous devons également ajouter une référence à `System.Net.Http` afin de pouvoir instancier un pour l' `HttpClient` envoi de notre message de test à GCM. Dans le projet **MessageSender** , cliquez avec le bouton droit sur **références > ajouter une référence** et faites défiler jusqu’à ce que vous voyez **System .net. http**. Activez la case à cocher en regard de **System .net. http** , puis cliquez sur **OK**. 

#### <a name="implement-code-that-sends-a-test-message"></a>Implémenter le code qui envoie un message de test

Dans **MessageSender**, modifiez **Program.cs** et remplacez le contenu par le code suivant :

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

Dans le code ci-dessus, remplacez *YOUR_API_KEY* par la clé API de votre projet d’application cliente. 

Ce serveur d’applications de test envoie au GCM le message au format JSON suivant :

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, à son tour, envoie ce message à votre application cliente. Nous allons créer **MessageSender** et ouvrir une fenêtre de console dans laquelle nous pouvons l’exécuter à partir de la ligne de commande.

### <a name="try-it"></a>Essayez !

Nous sommes maintenant prêts à tester notre application cliente. Si vous utilisez un émulateur ou si votre appareil communique avec GCM sur Wi-Fi, vous devez ouvrir les ports TCP suivants sur votre pare-feu pour que les messages GCM soient utilisés : 5228, 5229 et 5230.

Démarrez votre application cliente et observez la fenêtre sortie. Une fois `RegistrationIntentService` que le a reçu un jeton d’inscription de GCM, la fenêtre de sortie doit afficher le jeton avec une sortie de journal ressemblant à ce qui suit :

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

À ce stade, l’application cliente est prête à recevoir un message de notification à distance. À partir de la ligne de commande, exécutez le programme **MessageSender. exe** pour envoyer un message de notification « Hello, Xamarin » à l’application cliente.
Si vous n’avez pas encore généré le projet **MessageSender** , faites-le maintenant.

Pour exécuter **MessageSender. exe** sous Visual Studio, ouvrez une invite de commandes, accédez au répertoire **MessageSender/bin/debug** , puis exécutez la commande directement :

```cmd
MessageSender.exe
```

Pour exécuter **MessageSender. exe** sous Visual Studio pour Mac, ouvrez une session de terminal, accédez à **MessageSender/bin/déboguez** le répertoire, puis utilisez mono pour exécuter **MessageSender. exe** 

```bash
mono MessageSender.exe
```

La propagation du message par le biais de GCM peut prendre jusqu’à une minute, puis vers l’application cliente. Si le message est reçu avec succès, la sortie doit ressembler à ce qui suit dans la fenêtre Sortie : 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

En outre, vous remarquerez qu’une nouvelle icône de notification apparaît dans la barre d’état des notifications : 

[![L’icône de notification s’affiche sur l’appareil](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Lorsque vous ouvrez le tiroir de notification pour afficher les notifications, vous devez voir notre notification à distance :

[![Le message de notification s’affiche.](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Félicitations, votre application a reçu sa première notification à distance !

Notez que les messages GCM ne seront plus reçus si l’application est arrêtée forcée. Pour reprendre les notifications après un arrêt forcé, l’application doit être redémarrée manuellement. Pour plus d’informations sur cette stratégie Android, consultez [lancer des contrôles sur des applications arrêtées](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) et cette [publication de dépassement de capacité](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267)de la pile. 

## <a name="summary"></a>Récapitulatif

Cette procédure pas à pas décrit les étapes à suivre pour implémenter des notifications distantes dans une application Xamarin. Android. Il a décrit comment installer des packages supplémentaires nécessaires pour les communications GCM et explique comment configurer les autorisations d’application pour l’accès aux serveurs GCM. Il fournit un exemple de code qui illustre comment vérifier la présence de Google Play Services, comment implémenter un service d’inscription et un service d’écoute d’ID d’instance qui négocie avec GCM pour un jeton d’inscription et comment implémenter un écouteur GCM service qui reçoit et traite les messages de notification distants. Enfin, nous avons implémenté un programme de test en ligne de commande pour envoyer des notifications de test à notre application cliente par le biais de GCM. 

## <a name="related-links"></a>Liens associés

- [GCM RemoteNotifications (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
- [Messagerie cloud Google](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
