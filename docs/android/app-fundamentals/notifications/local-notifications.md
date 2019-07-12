---
title: Notifications locales
description: Cette section montre comment implémenter des notifications locales dans Xamarin.Android. Il explique les différents éléments d’interface utilisateur d’une notification d’Android et traite des API d’impliquée dans la création et affichage d’une notification.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: c36b31e28011bea287903ee0681a316209abd22d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829993"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>Notifications locales

_Cette section montre comment implémenter des notifications locales dans Xamarin.Android. Il explique les différents éléments d’interface utilisateur d’une notification d’Android et traite des API d’impliquée dans la création et affichage d’une notification._

## <a name="local-notifications-overview"></a>Vue d’ensemble des notifications locales

Android fournit deux zones système contrôlé pour afficher les icônes de notification et des informations de notification à l’utilisateur. Lorsqu’une notification est tout d’abord publiée, son icône s’affiche dans le *zone de notification*, comme illustré dans la capture d’écran suivante :

![Zone de notification d’exemple sur un appareil](local-notifications-images/01-notification-shade.png)

Pour obtenir plus d’informations sur la notification, l’utilisateur peut ouvrir le tiroir de notification (qui se développe chaque icône de notification pour afficher le contenu de la notification) et effectuer toutes les actions associées aux notifications. La capture d’écran suivante montre un *tiroir de notification* qui correspond à la zone de notification affichée au-dessus :

[![Tiroir de notification exemple affichant trois notifications](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Notifications Android utilisent deux types de disposition :

-   ***Disposition de base*** &ndash; un format de présentation compact et fixe.

-   ***Disposition développée*** &ndash; un format de présentation qui peut se développer pour une plus grande taille pour afficher plus d’informations.

Chacun de ces types de disposition (et comment les créer) sont expliquées dans les sections suivantes.

> [!NOTE]
> Ce guide se concentre sur la [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) à partir de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Ces API est assurer la valeur maximale compatibilité descendante pour Android 4.0 (API niveau 14).


### <a name="base-layout"></a>Disposition de base

Toutes les notifications Android sont construites sur le format de disposition de base, qui, au minimum, inclut les éléments suivants :

1.  Un *icône de notification*, qui représente l’application d’origine, ou le type de notification si l’application prend en charge différents types de notifications.

2.  La notification *titre*, ou le nom de l’expéditeur si la notification est un message personnel.

3.  Le message de notification.

4.  Un *timestamp*.

Ces éléments sont affichés comme illustré dans le diagramme suivant :

[![Emplacement des éléments de notification](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Dispositions de base sont limitées à 64 pixels indépendants de densité (dp) en hauteur. Android crée ce style de notification de base par défaut.

Si vous le souhaitez, les notifications peuvent afficher une grande icône qui représente l’application ou une photo de l’expéditeur. Lorsqu’une grande icône est utilisée dans une notification dans Android 5.0 et versions ultérieures, l’icône de notification small est affichée sous la forme un badge sur l’icône volumineux :

![Photo de notification simple](local-notifications-images/04-simple-notification-photo.png)

Notifications à partir d’Android 5.0, peuvent également apparaître sur l’écran de verrouillage :

[![Exemple de notification écran de verrouillage](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

L’utilisateur peut double-cliquer la notification d’écran de verrou pour déverrouiller l’appareil et accéder à l’application à l’origine de cette notification, ou faites défiler pour faire disparaître la notification. Applications peuvent définir le niveau de visibilité d’une notification pour contrôler ce qui est affiché sur l’écran de verrouillage, et les utilisateurs peuvent choisir s’il faut autoriser le contenu sensible à afficher dans les notifications de verrouillage d’écran.

Android 5.0 a introduit un format de présentation de notification de haute priorité appelé *tête haute*. Notifications de tête haute faites glisser vers le bas à partir du haut de l’écran pendant quelques secondes et puis reformatage sauvegarder sur la zone de notification :

[![Exemple de notification de profondes](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Notifications de tête haute rendent possible pour le système de l’interface utilisateur pour placer des informations importantes devant l’utilisateur sans interrompre l’état de l’activité en cours d’exécution.

Android prend en charge les métadonnées de notification afin que les notifications peuvent être triées et affichées intelligemment. Métadonnées de notification contrôle également la façon dont les notifications sont présentées sur l’écran de verrouillage et au format de tête haute. Applications peuvent définir les types de métadonnées de notification suivants :

-   **Priorité** &ndash; le niveau de priorité détermine comment et quand les notifications sont présentées. Par exemple, dans Android 5.0, les notifications de haute priorité sont affichées sous forme de notifications de tête haute.

-   **Visibilité** &ndash; spécifie la quantité de contenu notification doit être affiché lorsque la notification s’affiche sur l’écran de verrouillage.

-   **Catégorie** &ndash; indique au système comment gérer les notifications dans différentes circonstances, par exemple quand l’appareil est dans *ne pas déranger* mode.

> [!NOTE]
> **Visibilité** et **catégorie** ont été introduites dans Android 5.0 et ne sont pas disponibles dans les versions antérieures d’Android. Commençant par Android 8.0, [canaux de notification](#notif-chan) permettent de contrôler la façon dont les notifications sont présentées à l’utilisateur.


### <a name="expanded-layouts"></a>Dispositions développées

À compter d’Android 4.1, des notifications peuvent être configurées avec des styles de mise en page développée permettant aux utilisateurs d’étendre la hauteur de la notification pour afficher plus de contenu. Par exemple, l’exemple suivant illustre une notification de mise en page développée en mode conventionnel :

![Notification sous contractée](local-notifications-images/07-contracted-notification.png)

Cette notification est développée, elle révèle l’intégralité du message :

![Notification développée](local-notifications-images/08-expanded-notification.png)

Android prend en charge trois styles de mise en page développé pour les notifications d’événement unique :

-   ***Big Text*** &ndash; en mode conventionnel, affiche un extrait de la première ligne du message suivi par deux points. En mode développé, affiche le message entier (comme indiqué dans l’exemple ci-dessus).

-   ***Boîte de réception*** &ndash; en mode conventionnel, affiche le nombre de nouveaux messages. En mode développé, affiche le premier message de courrier électronique ou une liste des messages dans la boîte de réception.

-   ***Image*** &ndash; en mode conventionnel, affiche uniquement le texte du message. En mode développé, affiche le texte et une image.

[Au-delà de la Notification de base](#beyond-the-basic-notification) (plus loin dans cet article) explique comment créer *Big Text*, *boîte de réception*, et *Image* notifications.

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canaux de notification

À compter d’Android 8.0 (Oreo), vous pouvez utiliser la *canaux de notification* fonctionnalité pour créer un canal personnalisables par l’utilisateur pour chaque type de notification que vous souhaitez afficher. Canaux de notification permettent à votre place aux notifications de groupe afin que toutes les notifications publié dans une pièce de canal, le même comportement. Par exemple, vous pouvez avoir un canal de notification qui est conçu pour les notifications qui requièrent une attention immédiate et un canal « silencieux » distinct qui est utilisé pour les messages d’information.

Le **YouTube** application est installée avec Android Oreo répertorie deux catégories de notification : **Notifications de téléchargement** et **notifications générales**:

[![Écrans de notification pour YouTube dans Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Chacun de ces catégories correspondant à un canal de notification. L’application YouTube implémente un **Notifications télécharger** canal et un **Notifications générales** canal. L’utilisateur peut appuyer **notifications de téléchargement**, qui affiche l’écran des paramètres pour le canal de notifications de téléchargement de l’application :

[![Télécharger l’écran de notifications pour l’application YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

Dans cet écran, l’utilisateur peut modifier le comportement de la **télécharger** notifications de canal en procédant comme suit :

-   Définir l’Importance au niveau **Urgent**, **haute**, **support**, ou **faible**, qui configure le niveau de l’interruption audio et visuel.

-   Activer le point de notification ou désactiver.

-   Activer le voyant clignote ou désactiver.

-   Afficher ou masquer les notifications sur l’écran de verrouillage.

-   Remplacer le **ne pas déranger** paramètre.

Le **Notifications générales** canal a des paramètres similaires :

[![Écran de notifications générales pour l’application YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Notez que vous n’avez pas de contrôle absolu sur la façon dont vos canaux de notification interagissent avec l’utilisateur &ndash; l’utilisateur peut modifier les paramètres pour n’importe quel canal de notification sur l’appareil comme indiqué dans les captures d’écran ci-dessus. Toutefois, vous pouvez configurer les valeurs par défaut (comme sera décrite ci-dessous). Comme ces exemples illustrent, la nouvelle fonctionnalité de canaux de notification permet aux utilisateurs un contrôle affiné sur les différents types de notifications.


## <a name="notification-creation"></a>Création de notification

Pour créer une notification dans Android, vous utilisez le [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) classe à partir de la [Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) package NuGet. Cette classe permet de créer et publier des notifications sur les versions antérieures d’Android. Pour plus d’informations sur l’utilisation de `NotificationCompat.Builder`, consultez [compatibilité](#compatibility) plus loin dans cette rubrique.

`NotificationCompat.Builder` Fournit des méthodes permettant de définir les différentes options dans une notification, tels que :

-   Le contenu, y compris le titre, le texte du message et l’icône de notification.

-   Le style de la notification, tel que *Big Text*, *boîte de réception*, ou *Image* style.

-   La priorité de la notification : minimum, faible, par défaut, élevée ou maximale. Sur Android 8.0 et versions ultérieures, la priorité est définie via un [ _canal de notification_](#notification-channels).

-   La visibilité de la notification sur l’écran de verrouillage : public, privé ou secret.

-   Métadonnées de catégorie qui vous aide à Android classer et filtrer la notification.

-   Une intention facultative qui indique une activité à lancer lorsque l’appui sur la notification.

-   L’ID du canal de notification que la notification sera publiée (Android 8.0 et versions ultérieures).

Après avoir défini ces options dans le Générateur de rapports, vous générez un objet de notification qui contient les paramètres. Pour publier la notification, vous passez cet objet de notification pour le *Gestionnaire de notifications*. Android fournit le [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) (classe), qui est chargé pour la publication des notifications et les afficher à l’utilisateur. Une référence à cette classe peut être obtenue à partir de n’importe quel contexte, tel qu’une activité ou un service.


### <a name="creating-a-notification-channel"></a>Création d’un canal de notification

Les applications qui sont exécutent sur Android 8.0 doivent créer un canal de notification pour les notifications. Un canal de notification requiert les trois éléments d’information suivants :

* Une chaîne d’ID qui est unique pour le package qui identifiera le canal.
* Le nom du canal qui sera affiché à l’utilisateur.  Le nom doit être compris entre 1 et 40 caractères.
* L’importance du canal.

Applications devez vérifier la version d’Android qu’ils sont en cours d’exécution.
Appareils exécutant des versions antérieures à Android 8.0 ne doivent pas créer un canal de notification. La méthode suivante est un exemple montrant comment créer un canal de notification dans une activité :

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

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Le canal de notification doit être créé à chaque fois que l’activité est créée. Pour le `CreateNotificationChannel` (méthode), elle doit être appelée le `OnCreate` méthode d’une activité.

### <a name="creating-and-publishing-a-notification"></a>Création et publication d’une notification

Pour générer une notification dans Android, procédez comme suit :

1.  Instancier un `NotificationCompat.Builder` objet.

2.  Appeler des méthodes différentes sur le `NotificationCompat.Builder` objet pour définir les options de notification.

3.  Appelez le [Build](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) méthode de la `NotificationCompat.Builder` objet pour instancier un objet de notification.

4.  Appelez le [Notify](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) méthode du Gestionnaire de notification pour la notification de publication.

Vous devez fournir au moins les informations suivantes pour chaque notification :

-   Une petite icône (24 x 24 dp taille)

-   Un titre court

-   Le texte de la notification

L’exemple de code suivant illustre comment utiliser `NotificationCompat.Builder` pour générer une notification de base. Notez que `NotificationCompat.Builder` méthodes prennent en charge [enchaînement](https://en.wikipedia.org/wiki/Method_chaining); autrement dit, chaque méthode retourne l’objet de générateur de rapports afin de pouvoir utiliser le résultat du dernier appel de méthode à appeler l’appel de méthode suivant :

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Dans cet exemple, un nouveau `NotificationCompat.Builder` objet appelé `builder` est instancié, ainsi que l’ID du canal de notification à utiliser. Le titre et le texte de la notification sont définies, et l’icône de notification est chargé à partir de **Resources/drawable/ic_notification.png**. L’appel au générateur notification `Build` méthode crée un objet de notification avec ces paramètres. L’étape suivante consiste à appeler le `Notify` méthode du Gestionnaire de notification. Pour obtenir le Gestionnaire de notifications, vous appelez `GetSystemService`, comme indiqué ci-dessus.

Le `Notify` méthode accepte deux paramètres : l’identificateur de notification et l’objet de notification. L’identificateur de notification est un entier unique qui identifie la notification à votre application. Dans cet exemple, l’identificateur de notification a la valeur zéro (0) ; Toutefois, dans une application de production, vous pouvez donner à chaque notification un identificateur unique. Réutilisation de la valeur d’identificateur précédent dans un appel à `Notify` provoque la dernière notification doivent être remplacées.

Lorsque ce code s’exécute sur un appareil Android 5.0, il génère une notification qui ressemble à l’exemple suivant :

![Résultat de la notification pour l’exemple de code](local-notifications-images/09-hello-world.png)

L’icône de notification s’affiche sur le côté gauche de la notification &ndash; cette image d’un cercle &ldquo;je&rdquo; a un canal alpha afin qu’Android peut dessiner un fond gris circulaire derrière lui. Vous pouvez également fournir une icône sans un canal alpha. Pour afficher une image de photo sous forme d’icône, consultez [grand Format icône](#large-icon-format) plus loin dans cette rubrique.

L’horodatage est défini automatiquement, mais vous pouvez remplacer ce paramètre en appelant le [SetWhen](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) méthode du Générateur de notification. Par exemple, l’exemple de code suivant définit l’horodatage à l’heure actuelle :

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Vibration et son activation

Si vous souhaitez que votre notification également un signal sonore, vous pouvez appeler le Générateur de notification [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) (méthode) et passez le `NotificationDefaults.Sound` indicateur :

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Cet appel à `SetDefaults` entraîne l’appareil lire un son lorsque la notification est publiée. Si vous souhaitez que l’appareil vibrer plutôt que d’un signal sonore, vous pouvez transmettre `NotificationDefaults.Vibrate` à `SetDefaults.` si vous souhaitez que l’appareil d’émettre un signal sonore et vibrer l’appareil, vous pouvez passer les deux indicateurs à `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Si vous activez le son sans spécifier un son à lire, Android utilise le son de notification du système par défaut. Toutefois, vous pouvez modifier le son est lu en appelant le Générateur de notification [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) (méthode). Par exemple, pour lire l’alarme sonore avec votre notification (au lieu du son de notification par défaut), vous pouvez obtenir l’URI pour l’alarme sonore à partir de la [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) et transmettez-le à `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Vous pouvez également utiliser la sonnerie par défaut système audio pour votre notification :

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Après avoir créé un objet de notification, il est possible de définir des propriétés de notification sur l’objet de notification (au lieu de les configurer à l’avance via `NotificationCompat.Builder` méthodes). Par exemple, au lieu d’appeler le `SetDefaults` méthode permettant de vibration sur une notification, vous pouvez modifier directement l’indicateur de bit de la notification [par défaut est](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) propriété :

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

L’exemple suivant génère l’appareil faire vibrer lorsque la notification est publiée.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Une notification de la mise à jour

Si vous souhaitez mettre à jour le contenu d’une notification une fois qu’il a été publié, vous pouvez réutiliser existant `NotificationCompat.Builder` objet pour créer un nouvel objet de notification et de publier cette notification avec l’identificateur de la dernière notification. Par exemple :

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

Dans cet exemple, existant `NotificationCompat.Builder` objet est utilisé pour créer un nouvel objet de notification avec un autre titre et un message.
Le nouvel objet de notification est publié à l’aide de l’identificateur de la notification précédente, et cela met à jour le contenu de la notification publiée précédemment :

![Notification de mise à jour](local-notifications-images/12-updated-notification.png)

Le corps de la notification précédente est réutilisé &ndash; uniquement le titre et le texte de la notification change tandis que la notification s’affiche dans le tiroir de notification. Le texte du titre par « Exemple de Notification » « Notification de mise à jour » et le texte du message passe de « Hello World ! Voici ma première notification ! » à « Changé à ce message ».

Une notification reste visible jusqu'à ce que l’une des trois situations suivantes se produit :

-   L’utilisateur fermera la notification (ou appuie sur *Effacer tout*).

-   L’application effectue un appel à `NotificationManager.Cancel`, en passant l’ID unique de notification qui a été affecté lors de la notification a été publiée.

-   L’application appelle `NotificationManager.CancelAll`.

Pour plus d’informations sur la mise à jour de notifications Android, consultez [modifier une Notification](https://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>À partir d’une activité à partir d’une notification

Dans Android, il est courant pour une notification à associer à un *action* &ndash; une activité qui s’affiche lorsque l’utilisateur appuie sur la notification. Cette activité peut résider dans une autre application ou même dans une autre tâche. Pour ajouter une action à une notification, vous créez un [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) de l’objet et associer le `PendingIntent` avec la notification. Un `PendingIntent` est un type spécial d’intention qui permet à l’application destinataire exécuter une partie prédéfinie de code avec les autorisations de l’application émettrice. Lorsque l’utilisateur appuie sur la notification, Android démarre l’activité spécifiée par le `PendingIntent`.

L’extrait de code suivant illustre comment créer une notification avec un `PendingIntent` qui lancera l’activité de l’application d’origine, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Ce code est très similaire au code de notification dans la section précédente, à ceci près qu’un `PendingIntent` est ajouté à l’objet de notification. Dans cet exemple, le `PendingIntent` est associé à l’activité de l’application d’origine avant d’être passé au générateur notification [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) (méthode). Le `PendingIntentFlags.OneShot` indicateur est passé à la `PendingIntent.GetActivity` méthode afin que le `PendingIntent` est utilisée une seule fois. Lorsque ce code s’exécute, la notification suivante s’affiche :

![Première notification d’action](local-notifications-images/10-first-action-notification.png)

En appuyant sur cette notification prend l’utilisateur vers l’activité d’origine.

Dans une application de production, votre application doit gérer le *pile de retour* lorsque l’utilisateur appuie sur le **retour** bouton au sein de l’activité de notification (si vous n’êtes pas familiarisé avec les tâches Android et de la pile de retour, consultez [ Tâches et la pile de retour](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
Dans la plupart des cas, la navigation vers l’arrière en dehors de l’activité de notification doit renvoyer l’utilisateur en dehors de l’application et revenir à l’écran d’accueil. Pour gérer la pile de retour, votre application utilise le [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) classe pour créer un `PendingIntent` avec une pile de retour.

Une autre considération réelle est que l’activité d’origine devra peut-être envoyer des données à l’activité de notification. Par exemple, la notification peut indiquer qu’un message texte est arrivé, et l’activité de notification (un message affichant l’écran), nécessite l’ID du message à afficher le message à l’utilisateur. L’activité qui crée le `PendingIntent` pouvez utiliser la [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) méthode pour ajouter des données (par exemple, une chaîne) à l’intention afin que ces données sont passées à l’activité de notification.

L’exemple de code suivant illustre comment utiliser `TaskStackBuilder` pour gérer la pile de retour et il contient un exemple montrant comment envoyer une chaîne de message unique à une activité de notification appelée `SecondActivity`:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Dans cet exemple de code, l’application se compose de deux activités : `MainActivity` (qui contient le code de la notification ci-dessus), et `SecondActivity`, l’écran de l’utilisateur voit après avoir appuyé sur la notification. Quand ce code est exécuté, une notification simple (semblable à l’exemple précédent) est présentée. Appuyant sur la notification dirige l’utilisateur vers le `SecondActivity` écran :

![Capture d’écran de deuxième activité](local-notifications-images/11-second-activity.png)

Le message de type chaîne (passé dans l’intention `PutExtra` (méthode)) est récupérée dans `SecondActivity` par le biais de cette ligne de code :

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Ce message récupéré, « Message d’accueil à partir de MainActivity ! », s’affiche dans le `SecondActivity` s’affiche, comme illustré dans la capture d’écran ci-dessus. Lorsque l’utilisateur appuie sur le **retour** bouton lors dans `SecondActivity`, navigation mène l’application et revenir à l’écran précédant le lancement de l’application.

Pour plus d’informations sur la création en attente d’intentions, consultez [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Au-delà de la notification de base

Notifications par défaut dans un format de disposition de base simple dans Android, mais vous pouvez améliorer ce format de base en rendant supplémentaires `NotificationCompat.Builder` appels de méthode. Dans cette section, vous allez apprendre à ajouter une icône de grande photo à votre notification, et vous verrez des exemples montrant comment créer des notifications de mise en page développée.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Format de grandes icônes

Notifications Android affichent généralement l’icône de l’application d’origine (sur le côté gauche de la notification). Toutefois, les notifications peuvent afficher une image ou une photo (un *grande icône*) au lieu de la petite icône standard. Par exemple, une application de messagerie peut afficher une photo de l’expéditeur plutôt que sur l’icône d’application.

Voici un exemple d’une notification Android 5.0 base &ndash; il affiche uniquement l’icône de la petite application :

![Exemple de notification normal](local-notifications-images/13-sample-notification.png)

Et Voici une capture d’écran de la notification après l’avoir modifié pour afficher une grande icône &ndash; il utilise une icône créée à partir d’une image d’un monkey de code Xamarin :

![Exemple de notification de grandes icônes](local-notifications-images/14-large-icon-sample.png)

Notez que lorsqu’une notification est présentée dans le format des icônes de grande taille, l’icône de petite application est affichée comme un badge sur le coin inférieur droit d’une grande icône.

Pour utiliser une image comme une grande icône dans une notification, vous appelez le Générateur de notification [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) (méthode) et passez un bitmap de l’image. Contrairement aux `SetSmallIcon`, `SetLargeIcon` accepte uniquement une image bitmap. Pour convertir un fichier image dans un bitmap, vous utilisez le [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) classe. Par exemple :

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Cet exemple de code ouvre le fichier image à **Resources/drawable/monkey_icon.png**, le convertit en une image bitmap et transmet la bitmap résultante à `NotificationCompat.Builder`. En règle générale, la résolution de l’image source est supérieure à la petite icône &ndash; mais pas beaucoup plus volumineux. Une image qui est trop volumineux peut entraîner des opérations de redimensionnement inutiles qui pourrait retarder la validation de la notification.


### <a name="big-text-style"></a>Style de texte volumineux

Le *Big Text* style est un modèle de disposition développée que vous utilisez pour l’affichage des messages longs dans les notifications. Comme toutes les notifications de mise en page développée, la notification Big Text s’affiche initialement dans un format de présentation compacte :

![Exemple de notification Big Text](local-notifications-images/15-big-text-notification.png)

Dans ce format, seul un extrait du message s’affiche, terminé par deux points. Lorsque l’utilisateur fait glisser vers le bas sur la notification, il se développe pour afficher le message de notification en entier :

![Notification de texte Big développée](local-notifications-images/16-big-text-expanded.png)

Ce format de disposition développé comprend également texte du résumé en bas de la notification. La hauteur maximale de la *Big Text* notification est 256 dp.

Pour créer un *Big Text* notification, vous instanciez un `NotificationCompat.Builder` de l’objet, comme précédemment, puis d’instancier et d’ajouter un [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) de l’objet à le `NotificationCompat.Builder` objet. Voici un exemple :

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

Dans cet exemple, le texte du message et le texte de résumé sont stockés dans le `BigTextStyle` objet (`textStyle`) avant d’être passé à `NotificationCompat.Builder.`


### <a name="image-style"></a>Style de l’image

Le *Image* style (également appelé le *Bigpicture* style) est un format développé de notification que vous pouvez utiliser pour afficher une image dans le corps d’une notification. Par exemple, une application de la capture d’écran ou une application photo peut utiliser le *Image* notification style pour l’utilisateur avec une notification de la dernière image qui a été capturée. Notez que la hauteur maximale de la *Image* notification est 256 dp &ndash; Android va se redimensionner l’image pour tenir dans cette restriction de hauteur maximale, dans les limites de mémoire disponible.

Comme tous développé des notifications de mise en page, *Image* tout d’abord, les notifications sont affichées dans un format compact qui affiche un extrait du texte du message qui accompagne cet article :

![Notification de l’image compact n’indique aucune image](local-notifications-images/17-image-compact.png)

Lorsque l’utilisateur fait glisser vers le bas le *Image* notification, celle-ci se développe pour afficher une image. Par exemple, voici la version développée de la notification précédente :

![Afin d’afficher une notification image développée autre](local-notifications-images/18-image-expanded.png)

Notez que lorsque la notification est affichée dans un format compact, il affiche le texte de notification (le texte qui est passé au générateur notification `SetContentText` méthode, comme indiqué précédemment). Toutefois, lorsque la notification est développée pour afficher l’image, il affiche le texte du résumé au-dessus de l’image.

Pour créer un *Image* notification, vous instanciez un `NotificationCompat.Builder` objet comme auparavant, puis créer et insérer un [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) de l’objet dans le `NotificationCompat.Builder` objet. Par exemple :

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

Comme le `SetLargeIcon` méthode de `NotificationCompat.Builder`, le [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) méthode de `BigPictureStyle` requiert une image bitmap de l’image que vous souhaitez afficher dans le corps de la notification. Dans cet exemple, le [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) méthode de `BitmapFactory` lit le fichier image situé **Resources/drawable/x_bldg.png** et le convertit en une image bitmap.

Vous pouvez également afficher des images qui ne sont pas empaquetés en tant que ressource. Par exemple, l’exemple de code suivant charge une image à partir de la carte SD locale et l’affiche dans un *Image* notification :

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

Dans cet exemple, le fichier image situé **/sdcard/Pictures/my-tshirt.jpg** est chargé, redimensionné à la moitié de sa taille d’origine, puis convertie en une image bitmap pour une utilisation dans la notification :

![Exemple d’image standard de notification](local-notifications-images/19-tshirt-notification.png)

Si vous ne connaissez pas la taille du fichier image à l’avance, il est judicieux d’encapsuler l’appel à [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) dans un gestionnaire d’exceptions &ndash; un `OutOfMemoryError` exception peut être levée si l’image est trop grande pour Android à redimensionner.

Pour plus d’informations sur le chargement et le décodage d’images bitmap volumineux, consultez [charge grand Bitmaps efficacement](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Style de la boîte de réception

Le *boîte de réception* format est un modèle de disposition développé prévu pour l’affichage des lignes distinctes de texte (par exemple, une boîte de réception résumé) dans le corps de la notification. Le *boîte de réception* format notification s’affiche tout d’abord dans un format compact :

![Exemple de notification de boîte de réception compact](local-notifications-images/20-inbox-compact.png)

Lorsque l’utilisateur fait glisser vers le bas sur la notification, il se développe pour afficher un résumé de l’e-mail comme indiqué dans la capture d’écran ci-dessous :

![Exemple de notification de boîte de réception développé](local-notifications-images/21-inbox-expanded.png)

Pour créer un *boîte de réception* notification, vous instanciez un `NotificationCompat.Builder` de l’objet, comme avant et ajoutez un [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) de l’objet à le `NotificationCompat.Builder`. Voici un exemple :

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

Pour ajouter de nouvelles lignes de texte pour le corps de la notification, appelez le [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) méthode de la `InboxStyle` objet (la hauteur maximale de la *boîte de réception* notification est 256 dp). Notez que, contrairement à *Big Text* style, le *boîte de réception* style prend en charge des lignes de texte dans le corps de la notification.

Vous pouvez également utiliser le *boîte de réception* style pour les notifications qui devant afficher des lignes de texte dans un format développé. Par exemple, le *boîte de réception* style de notification peut être utilisé pour combiner plusieurs notifications en attente en une synthèse &ndash; vous pouvez mettre à jour un seul *boîte de réception* style notification avec new lignes de contenu de la notification (voir [une Notification de la mise à jour](#updating-a-notification) ci-dessus), plutôt que de générer un flux continu de notifications principalement similaire.


## <a name="configuring-metadata"></a>Configuration des métadonnées

`NotificationCompat.Builder` inclut des méthodes que vous pouvez appeler pour définir les métadonnées sur votre notification, telles que la priorité, de visibilité et de catégorie. Android utilise ces informations &mdash; , ainsi que les paramètres de préférence utilisateur &mdash; pour déterminer quand et comment afficher les notifications.


### <a name="priority-settings"></a>Paramètres de priorité

Applications qui s’exécutent sur Android 7.1 et inférieure besoin définir la priorité directement sur la notification elle-même. Le paramètre de priorité d’une notification détermine deux résultats lors de la publication de la notification :

-   Où la notification s’affiche par rapport à d’autres notifications.
    Par exemple, notifications de priorité élevée sont présentées ci-dessus des notifications de priorité inférieure dans le tiroir de notifications, indépendamment du lorsque chaque notification a été publiée.

-   Indique si la notification est affichée dans le format de notification profondes (Android 5.0 et versions ultérieur). Uniquement *haute* et *maximale* notifications de priorité sont affichées sous forme de notifications de tête haute.

Xamarin.Android définit les énumérations suivantes pour définir la priorité de notification :

-   `NotificationPriority.Max` &ndash; Avertit l’utilisateur à une condition d’urgence ou critiques (par exemple, un appel entrant, les instructions étape par étape ou une alerte en cas d’urgence). Sur Android 5.0 et versions ultérieures, les notifications de la priorité maximale sont affichées au format de tête haute.

-   `NotificationPriority.High` &ndash; Informe l’utilisateur des événements importants (comme les e-mails importants ou l’arrivée des messages de conversation en temps réel). Sur Android 5.0 et versions ultérieures, les notifications de priorité élevée sont affichées au format de tête haute.

-   `NotificationPriority.Default` &ndash; Informe l’utilisateur des conditions qui ont le niveau d’importance moyenne.

-   `NotificationPriority.Low` &ndash; Pour plus d’informations non urgent que l’utilisateur doit être informé de (par exemple, les rappels de mise à jour de logiciels ou les mises à jour de réseau social).

-   `NotificationPriority.Min` &ndash; Pour plus d’informations que l’utilisateur Remarque uniquement lorsque affichage des notifications (par exemple, les informations emplacement ou la météo).

Pour définir la priorité d’une notification, appelez le [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) méthode de la `NotificationCompat.Builder` objet, en passant le niveau de priorité. Par exemple :

```csharp
builder.SetPriority (NotificationPriority.High);
```

Dans l’exemple suivant, la notification de priorité élevée, « message important ! » apparaît en haut du tiroir de notification :

![Exemple de notification de haute priorité](local-notifications-images/22-hi-priority-drawer.png)

Comme il s’agit d’une notification de priorité élevée, il s’affiche également en tant que tête haute notification au-dessus d’actuel activité écran l’utilisateur dans Android 5.0 :

![Exemple de notification de tête haute](local-notifications-images/23-heads-up-example.png)

Dans l’exemple suivant, la notification de « Pensé pour la journée » de faible priorité s’affiche sous une notification de niveau batterie de priorité plus élevée :

![Exemple de notification de faible priorité](local-notifications-images/24-lo-priority-drawer.png)

Étant donné que la notification de « Pensée pour la journée » est une notification de faible priorité, Android affichera pas au format profondes.

> [!NOTE]
> Sur Android 8.0 et versions ultérieures, la priorité du canal et l’utilisateur des paramètres de notification détermine la priorité de la notification.

### <a name="visibility-settings"></a>Paramètres de visibilité

Android 5.0, à partir de la *visibilité* paramètre n’est disponible pour contrôler la quantité de contenu notification s’affiche sur l’écran de verrouillage sécurisé.
Xamarin.Android définit les énumérations suivantes pour la définition de la visibilité de la notification :

-   `NotificationVisibility.Public` &ndash; La totalité du contenu de la notification s’affiche sur l’écran de verrouillage sécurisé.

-   `NotificationVisibility.Private` &ndash; Uniquement les informations essentielles sont affichées sur l’écran de verrouillage sécurisé (par exemple, l’icône de notification et le nom de l’application qui a posté il), mais le reste des détails de la notification sont masquées. Par défaut, toutes les notifications `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Rien ne s’affiche sur l’écran de verrouillage sécurisé, même pas l’icône de notification. Le contenu de notification est disponible uniquement après que l’utilisateur déverrouille l’appareil.

Pour définir la visibilité d’une notification, applications appel la `SetVisibility` méthode de la `NotificationCompat.Builder` objet, en passant le paramètre de visibilité. Par exemple, cet appel à `SetVisibility` rend la notification `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quand un `Private` notification est publiée, uniquement le nom et l’icône de l’application s’affiche sur l’écran de verrouillage sécurisé. Au lieu du message de notification, l’utilisateur voit « Déverrouiller votre appareil pour voir cette notification » :

![Déverrouiller votre message de notification d’appareil](local-notifications-images/25-lockscreen-private.png)

Dans cet exemple, **NotificationsLab** est le nom de l’application d’origine. Cette version de manière rédigée de la notification s’affiche uniquement lorsque l’écran de verrouillage est sécurisé (par exemple, sécurisé par le biais de code confidentiel, modèle ou mot de passe) &ndash; si l’écran de verrouillage n’est pas sécurisée, la totalité du contenu de la notification est disponible sur l’écran de verrouillage.


### <a name="category-settings"></a>Paramètres de catégorie

Depuis Android 5.0, catégories prédéfinies sont disponibles pour le classement et filtrage des notifications. Xamarin.Android fournit les énumérations suivantes pour ces catégories :

-   `Notification.CategoryCall` &ndash; Appel téléphonique entrant.

-   `Notification.CategoryMessage` &ndash; Message texte entrant.

-   `Notification.CategoryAlarm` &ndash; Une alarme condition du minuteur d’expiration ou.

-   `Notification.CategoryEmail` &ndash; Message de courrier électronique entrant.

-   `Notification.CategoryEvent` &ndash; Un événement de calendrier.

-   `Notification.CategoryPromo` &ndash; Un message promotionnel ou la publication.

-   `Notification.CategoryProgress` &ndash; La progression d’une opération d’arrière-plan.

-   `Notification.CategorySocial` &ndash; Mise à jour de réseau social.

-   `Notification.CategoryError` &ndash; Échec d’un processus d’authentification ou d’opération en arrière-plan.

-   `Notification.CategoryTransport` &ndash; Mise à jour de la lecture multimédia.

-   `Notification.CategorySystem` &ndash; Réservé pour une utilisation système (état du système ou périphérique).

-   `Notification.CategoryService` &ndash; Indique qu’un service en arrière-plan est en cours d’exécution.

-   `Notification.CategoryRecommendation` &ndash; Un message de recommandation lié à l’application en cours d’exécution.

-   `Notification.CategoryStatus` &ndash; Informations sur l’appareil.

Quand les notifications sont triées, priorité de la notification est prioritaire sur son paramètre de catégorie. Par exemple, une notification de priorité élevée s’affichera en tant que tête haute même s’il appartient à la `Promo` catégorie. Pour définir la catégorie d’une notification, vous appelez le `SetCategory` méthode de la `NotificationCompat.Builder` objet, en passant le paramètre de catégorie. Par exemple :

```csharp
builder.SetCategory (Notification.CategoryCall);
```

Le *ne pas déranger* (nouveau dans Android 5.0) de la fonctionnalité filtre des notifications en fonction des catégories. Par exemple, le *ne pas déranger* écran dans **paramètres** permet à l’utilisateur pour envoyer des notifications exemptes des appels téléphoniques et des messages :

![Ne pas déranger les commutateurs de l’écran](local-notifications-images/26-do-not-disturb.png)

Lorsque l’utilisateur configure *ne pas déranger* pour bloquer toutes les interruptions, à l’exception des appels téléphoniques (comme illustré dans la capture d’écran ci-dessus), Android permet de notifications avec un paramètre de catégorie de `Notification.CategoryCall` qui sera présenté lors de l’appareil est dans *ne pas déranger* mode. Notez que `Notification.CategoryAlarm` notifications ne sont jamais bloquées dans *ne pas déranger* mode.

Le [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) exemple montre comment utiliser `NotificationCompat.Builder` pour lancer une deuxième activité à partir d’une notification. Cet exemple de code est expliquée dans le [à l’aide des Notifications locales dans Xamarin.Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) procédure pas à pas.


### <a name="notification-styles"></a>Styles de notification

Pour créer *Big Text*, *Image*, ou *boîte de réception* style notifications avec `NotificationCompat.Builder`, votre application doit utiliser les versions de compatibilité de ces styles. Par exemple, pour utiliser le *Big Text* de style, instanciez `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

De même, votre application peut utiliser `NotificationCompat.InboxStyle` et `NotificationCompat.BigPictureStyle` pour *boîte de réception* et *Image* styles, respectivement.


### <a name="notification-priority-and-category"></a>Catégorie et priorité de notification

`NotificationCompat.Builder` prend en charge le `SetPriority` (méthode) (disponible à partir de Android 4.1). Toutefois, le `SetCategory` méthode est *pas* pris en charge par `NotificationCompat.Builder` , car les catégories font partie du nouveau système de métadonnées de notification qui a été introduit dans Android 5.0.

Pour prendre en charge les versions antérieures de l’emplacement où Android, `SetCategory` est non disponible, votre code peut vérifier le niveau d’API pendant l’exécution pour appeler de manière conditionnelle `SetCategory` lorsque le niveau d’API est égal à ou supérieur à Android 5.0 (niveau 21 d’API) :

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

Dans de cet exemple, l’application **Framework cible** est défini sur Android 5.0 et les **Version Android minimale** a la valeur **Android 4.1 (niveau API 16)** . Étant donné que `SetCategory` est disponible dans le niveau d’API 21 et versions ultérieur, cet exemple de code appellera `SetCategory` uniquement lorsqu’il est disponible &ndash; elle n’appelle pas `SetCategory` lorsque le niveau d’API est inférieur à 21.


### <a name="lock-screen-visibility"></a>Visibilité d’écran de verrouillage

Étant donné que Android ne prenait pas en charge des notifications de verrouillage d’écran avant Android 5.0 (niveau d’API 21), `NotificationCompat.Builder` ne prend pas en charge la `SetVisibility` (méthode). Comme expliqué ci-dessus pour `SetCategory`, votre code peut vérifier le niveau d’API au runtime et appelez `SetVisiblity` uniquement lorsqu’il est disponible :

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment créer des notifications locales dans Android. Il décrit l’anatomie d’une notification, vous avez appris comment utiliser `NotificationCompat.Builder` pour créer des notifications, les notifications de style dans les grandes icônes, *Big Text*, *Image* et *boîte de réception*  formats, comment définir des paramètres de métadonnées telles que la priorité, de visibilité et de catégorie de notification et comment lancer une activité à partir d’une notification. Cet article décrit également le fonctionnement de ces paramètres de notification avec la nouvelle tête de haute, écran de verrouillage, et *ne pas déranger* fonctionnalités introduites dans Android 5.0. Enfin, vous avez appris à utiliser `NotificationCompat.Builder` pour assurer la compatibilité avec les versions antérieures d’Android notification.

Pour obtenir des instructions sur la conception des notifications pour Android, consultez [Notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="related-links"></a>Liens associés

- [NotificationsLab (sample)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (sample)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notifications locales dans la procédure pas à pas Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Avertir l’utilisateur](https://developer.android.com/training/notify-user/index.html)
- [Notification](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
