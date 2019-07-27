---
title: 'Procédure pas à pas: utilisation de notifications locales dans Xamarin. Android'
description: Cette procédure pas à pas montre comment utiliser des notifications locales dans des applications Xamarin. Android. Il montre les principes fondamentaux de la création et de la publication d’une notification locale. Quand l’utilisateur clique sur la notification dans la zone de notification, il démarre une deuxième activité.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: fb70ea126216642af513036211f7dd2a86fd9559
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509543"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Procédure pas à pas: utilisation de notifications locales dans Xamarin. Android

_Cette procédure pas à pas montre comment utiliser des notifications locales dans des applications Xamarin. Android. Il montre les principes fondamentaux de la création et de la publication d’une notification locale. Quand l’utilisateur clique sur la notification dans la zone de notification, il démarre une deuxième activité._


## <a name="overview"></a>Présentation

Dans cette procédure pas à pas, nous allons créer une application Android qui déclenche une notification lorsque l’utilisateur clique sur un bouton dans une activité. Quand l’utilisateur clique sur la notification, il lance une deuxième activité qui affiche le nombre de fois où l’utilisateur a cliqué sur le bouton dans la première activité.

Les captures d’écran suivantes illustrent quelques exemples de cette application:

[![Exemples de captures d’écran avec notification](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Ce guide se concentre sur les [API NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) à partir de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Ces API garantissent une compatibilité descendante maximale avec Android 4,0 (niveau d’API 14).

## <a name="creating-the-project"></a>Création du projet

Pour commencer, nous allons créer un nouveau projet Android à l’aide du modèle d' **application Android** . Appelons ce projet **LocalNotifications**. (Si vous n’êtes pas familiarisé avec la création de projets Xamarin. Android, consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)

Modifiez le fichier de ressources **values/Strings. xml** de sorte qu’il contienne deux ressources de chaîne supplémentaires qui seront utilisées lors de la création du canal de notification:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Ajouter le package NuGet Android. support. v4

Dans cette procédure pas à pas, `NotificationCompat.Builder` nous utilisons pour créer notre notification locale. Comme expliqué dans [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md), nous devons inclure la [bibliothèque de prise en charge Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet dans `NotificationCompat.Builder`notre projet à utiliser.

Ensuite, nous allons modifier **MainActivity.cs** et ajouter l’instruction `using` suivante afin que les types de `Android.Support.V4.App` soient disponibles pour notre code:

```csharp
using Android.Support.V4.App;
```

En outre, nous devons préciser au compilateur que nous utilisons la `Android.Support.V4.App` version de `TaskStackBuilder` plutôt que la `Android.App` version. Ajoutez l’instruction `using` suivante pour résoudre toute ambiguïté:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Créer le canal de notification

Ensuite, ajoutez une méthode à `MainActivity` qui créera un canal de notification (si nécessaire):

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

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Mettez à `OnCreate` jour la méthode pour appeler cette nouvelle méthode:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Définir l’ID de notification

Nous aurons besoin d’un ID unique pour notre canal de notification et de notification. Nous allons modifier **MainActivity.cs** et ajouter la variable d’instance statique suivante à `MainActivity` la classe:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Ajouter du code pour générer la notification

Ensuite, nous devons créer un nouveau gestionnaire d’événements pour l’événement `Click` de bouton. Ajoutez la méthode suivante à `MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

La `OnCreate` méthode de MainActivity doit effectuer l’appel pour créer le canal de notification et assigner la `Click` `ButtonOnClick` méthode à l’événement du bouton (remplacez le gestionnaire d’événements délégué fourni par le modèle):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>Créer une deuxième activité

À présent, nous devons créer une autre activité que Android affichera lorsque l’utilisateur cliquera sur notre notification. Ajoutez une autre activité Android à votre projet appelé **SecondActivity**. Ouvrez **SecondActivity.cs** et remplacez son contenu par le code suivant:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

Nous devons également créer une disposition des ressources pour **SecondActivity**. Ajoutez un nouveau fichier de **disposition Android** à votre projet appelé **second. AXML**. Modifiez **second. AXML** et collez le code de disposition suivant:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Icône Ajouter une notification

Enfin, ajoutez une petite icône qui s’affiche dans la zone de notification lorsque la notification est lancée. Vous pouvez copier [cette icône](local-notifications-walkthrough-images/ic-stat-button-click.png) dans votre projet ou créer votre propre icône personnalisée. Nommez le **\_bouton\_fichier\_icône de stats IC Click. png** et copiez-le dans le dossier **ressources/dessinable** . N’oubliez pas d’utiliser **ajouter > élément existant...** pour inclure ce fichier d’icône dans votre projet.


### <a name="run-the-application"></a>Exécution de l'application

Générez et exécutez l’application. La première activité doit être présentée, comme dans la capture d’écran suivante:

[![Capture d’écran première activité](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Lorsque vous cliquez sur le bouton, vous remarquerez que la petite icône de la notification s’affiche dans la zone de notification:

[![Icône de notification s’affiche](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Si vous faites défiler vers le dessous et exposez le tiroir de notification, la notification suivante doit s’afficher:

[![Message de notification](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Lorsque vous cliquez sur la notification, celle-ci doit disparaître, et l’autre activité &ndash; doit être lancée en regardant un peu comme la capture d’écran suivante:

[![Capture d’écran de la deuxième activité](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Félicitations ! À ce stade, vous avez terminé la procédure pas à pas de notification locale Android et vous disposez d’un exemple fonctionnel auquel vous pouvez vous référer. Il y a beaucoup plus de notifications que nous n’avons indiquées ici. par conséquent, si vous souhaitez plus d’informations, consultez [la documentation de Google sur](https://developer.android.com/guide/topics/ui/notifiers/notifications.html)les notifications.


## <a name="summary"></a>Récapitulatif

Cette procédure pas `NotificationCompat.Builder` à pas permet de créer et d’afficher des notifications. Il a montré un exemple simple montrant comment démarrer une deuxième activité pour répondre à l’interaction de l’utilisateur avec la notification, et elle a démontré le transfert de données de la première activité à la deuxième.


## <a name="related-links"></a>Liens associés

- [LocalNotifications (exemple)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Canaux de notification Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notification](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
