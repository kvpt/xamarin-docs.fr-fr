---
title: Notifications locales sur Android
description: Cette section montre comment implémenter des notifications locales dans Xamarin. Android. Il explique les différents éléments d’interface utilisateur d’une notification Android et présente les API impliquées dans la création et l’affichage d’une notification.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/16/2018
ms.openlocfilehash: 617c04e2f40af535fb381362a389524d693fad0b
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303938"
---
# <a name="local-notifications-on-android"></a>Notifications locales sur Android

_Cette section montre comment implémenter des notifications locales dans Xamarin. Android. Il explique les différents éléments d’interface utilisateur d’une notification Android et présente les API impliquées dans la création et l’affichage d’une notification._

## <a name="local-notifications-overview"></a>Vue d’ensemble des notifications locales

Android fournit deux zones contrôlées par le système pour l’affichage des icônes de notification et des informations de notification à l’utilisateur. Lorsqu’une notification est publiée pour la première fois, son icône s’affiche dans la *zone de notification*, comme illustré dans la capture d’écran suivante :

![Exemple de zone de notification sur un appareil](local-notifications-images/01-notification-shade.png)

Pour obtenir des informations détaillées sur la notification, l’utilisateur peut ouvrir le tiroir de notification (qui développe chaque icône de notification pour afficher le contenu de la notification) et effectuer toutes les actions associées aux notifications. La capture d’écran suivante montre un *tiroir de notification* qui correspond à la zone de notification affichée ci-dessus :

[![exemple de tiroir de notification affichant trois notifications](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Les notifications Android utilisent deux types de dispositions :

- La ***disposition de Base*** &ndash; un format de présentation compact et fixe.

- La ***disposition développée*** &ndash; un format de présentation qui peut s’étendre à une plus grande taille pour révéler plus d’informations.

Chacun de ces types de dispositions (et comment les créer) est expliqué dans les sections suivantes.

> [!NOTE]
> Ce guide se concentre sur les [API NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) à partir de la [bibliothèque de prise en charge Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Ces API garantissent une compatibilité descendante maximale avec Android 4,0 (niveau d’API 14).

### <a name="base-layout"></a>Disposition de base

Toutes les notifications Android sont basées sur le format de disposition de base, qui, au minimum, comprend les éléments suivants :

1. Une *icône de notification*, qui représente l’application d’origine, ou le type de notification si l’application prend en charge différents types de notifications.

2. *Titre*de la notification, ou le nom de l’expéditeur si la notification est un message personnel.

3. Message de notification.

4. *Horodateur*.

Ces éléments sont affichés comme illustré dans le diagramme suivant :

[![l’emplacement des éléments de notification](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Les dispositions de base sont limitées à la hauteur de 64 pixels indépendants (DP). Android crée ce style de notification de base par défaut.

Les notifications peuvent éventuellement afficher une grande icône qui représente l’application ou la photo de l’expéditeur. Quand une grande icône est utilisée dans une notification dans Android 5,0 et versions ultérieures, la petite icône de notification s’affiche sous la forme d’un badge sur la grande icône :

![Photo de notification simple](local-notifications-images/04-simple-notification-photo.png)

À partir d’Android 5,0, les notifications peuvent également apparaître sur l’écran de verrouillage :

[![exemple de notification de l’écran de verrouillage](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

L’utilisateur peut double-cliquer sur la notification de verrouillage de l’écran pour déverrouiller l’appareil et accéder à l’application à l’origine de cette notification, ou faire glisser pour fermer la notification. Les applications peuvent définir le niveau de visibilité d’une notification pour contrôler ce qui est affiché sur l’écran de verrouillage, et les utilisateurs peuvent choisir d’autoriser ou non le contenu sensible à être affiché dans les notifications de verrouillage de l’écran.

Android 5,0 a introduit un format de présentation de notification haute priorité appelé *TETE-up*. Les notifications de niveau supérieur s’affichent en haut de l’écran pendant quelques secondes, puis se replacent dans la zone de notification :

[![exemple de notification de tête](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Les notifications par tête permettent à l’interface utilisateur du système de placer des informations importantes devant l’utilisateur sans interrompre l’état de l’activité en cours d’exécution.

Android prend en charge les métadonnées de notification afin que les notifications puissent être triées et affichées intelligemment. Les métadonnées de notification contrôlent également la manière dont les notifications sont présentées sur l’écran de verrouillage et au format en tête. Les applications peuvent définir les types de métadonnées de notification suivants :

- **Priorité** &ndash; le niveau de priorité détermine comment et quand les notifications sont présentées. Par exemple, dans Android 5,0, les notifications de haute priorité s’affichent sous la forme de notifications principales.

- **Visibilité** &ndash; spécifie la quantité de contenu de notification à afficher lorsque la notification s’affiche sur l’écran de verrouillage.

- La **catégorie** &ndash; informe le système Comment gérer la notification dans différentes circonstances, par exemple quand l’appareil est en mode *ne pas déranger* .

> [!NOTE]
> La **visibilité** et la **catégorie** ont été introduites dans Android 5,0 et ne sont pas disponibles dans les versions antérieures d’Android. À partir d’Android 8,0, les [canaux de notification](#notif-chan) sont utilisés pour contrôler la façon dont les notifications sont présentées à l’utilisateur.

### <a name="expanded-layouts"></a>Mises en page étendues

À compter d’Android 4,1, les notifications peuvent être configurées avec des styles de disposition développés qui permettent à l’utilisateur de développer la hauteur de la notification pour afficher davantage de contenu. Par exemple, l’exemple suivant illustre une notification de disposition développée en mode contractuel :

![Notification contractée](local-notifications-images/07-contracted-notification.png)

Lorsque cette notification est développée, elle révèle l’intégralité du message :

![Notification développée](local-notifications-images/08-expanded-notification.png)

Android prend en charge trois styles de disposition développés pour les notifications d’événements uniques :

- Les &ndash; ***Big Text*** en mode contractuel affichent un extrait de la première ligne du message, suivi de deux points. En mode développé, affiche l’intégralité du message (comme indiqué dans l’exemple ci-dessus).

- La ***boîte de réception*** &ndash; en mode contrat, affiche le nombre de nouveaux messages. En mode développé, affiche le premier message électronique ou une liste des messages dans la boîte de réception.

- ***Image*** &ndash; en mode contractuel, affiche uniquement le texte du message. En mode développé, affiche le texte et une image.

[Au-delà de la notification de base](#beyond-the-basic-notification) (plus loin dans cet article), vous explique comment créer des notifications de *texte volumineux*, de *boîte de réception*et d' *image* .

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canaux de notification

À partir d’Android 8,0 (Oreo), vous pouvez utiliser la fonctionnalité *canaux de notification* pour créer un canal personnalisable par l’utilisateur pour chaque type de notification que vous souhaitez afficher. Les canaux de notification vous permettent de regrouper des notifications afin que toutes les notifications publiées sur un canal présentent le même comportement. Par exemple, vous pouvez avoir un canal de notification destiné aux notifications qui requièrent une attention immédiate et un canal « plus silencieux » distinct qui est utilisé pour les messages d’information.

L’application **YouTube** installée avec Android Oreo répertorie deux catégories de notification : les **notifications de téléchargement** et les **notifications générales**:

[![des écrans de notification pour YouTube dans Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Chacune de ces catégories correspond à un canal de notification. L’application YouTube implémente un canal de **notifications de téléchargement** et un canal de **notifications générales** . L’utilisateur peut appuyer sur **Télécharger les notifications**, ce qui affiche l’écran des paramètres pour le canal des notifications de téléchargement de l’application :

[![écran de téléchargement des notifications pour l’application YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

Dans cet écran, l’utilisateur peut modifier le comportement du canal de notifications de **Téléchargement** en procédant comme suit :

- Définissez le niveau d’importance sur **urgent**, **élevé**, **moyen**ou **faible**, ce qui permet de configurer le niveau d’interruption sonore et visuel.

- Activez ou désactivez le point de notification.

- Activez ou désactivez le voyant clignotant.

- Affichez ou masquez les notifications sur l’écran de verrouillage.

- Remplacez le paramètre **ne pas déranger** .

Le canal **des notifications générales a des** paramètres similaires :

[![écran des notifications générales pour l’application YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Notez que vous ne disposez pas d’un contrôle absolu sur la façon dont vos canaux de notification interagissent avec l’utilisateur &ndash; l’utilisateur peut modifier les paramètres des canaux de notification sur l’appareil, comme indiqué dans les captures d’écran ci-dessus. Toutefois, vous pouvez configurer les valeurs par défaut (comme indiqué ci-dessous). Comme le montrent ces exemples, la nouvelle fonctionnalité canaux de notification vous permet de fournir aux utilisateurs un contrôle affiné sur différents types de notifications.

## <a name="notification-creation"></a>Création de notifications

Pour créer une notification dans Android, vous utilisez la classe [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) du package NuGet [Xamarin. Android. support. v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Cette classe permet de créer et de publier des notifications sur des versions antérieures d’Android.
`NotificationCompat.Builder` est également abordé.

`NotificationCompat.Builder` fournit des méthodes pour définir les différentes options dans une notification, par exemple :

- Contenu, y compris le titre, le texte du message et l’icône de notification.

- Style de la notification, tel que le *texte volumineux*, la *boîte de réception*ou le style d' *image* .

- Priorité de la notification : minimale, faible, par défaut, haute ou maximale. Sur Android 8,0 et versions ultérieures, la priorité est définie via un [_canal de notification_](#notification-channels).

- Visibilité de la notification sur l’écran de verrouillage : public, privé ou secret.

- Métadonnées de catégorie qui permettent à Android de classifier et de filtrer la notification.

- Intention facultative indiquant une activité à lancer lorsque la notification est exploitée.

- ID du canal de notification sur lequel la notification sera publiée (Android 8,0 et versions ultérieures).

Une fois que vous avez défini ces options dans le générateur, vous générez un objet de notification qui contient les paramètres. Pour publier la notification, vous transmettez cet objet de notification au *Gestionnaire de notifications*. Android fournit la classe [notificationmanager](xref:Android.App.NotificationManager) , qui est responsable de la publication des notifications et de leur affichage pour l’utilisateur. Une référence à cette classe peut être obtenue à partir de n’importe quel contexte, tel qu’une activité ou un service.

### <a name="creating-a-notification-channel"></a>Création d’un canal de notification

Les applications qui s’exécutent sur Android 8,0 doivent créer un canal de notification pour leurs notifications. Un canal de notification requiert les trois éléments d’information suivants :

- Chaîne d’ID qui est unique pour le package qui identifiera le canal.
- Nom du canal qui sera affiché à l’utilisateur.  Le nom doit être compris entre 1 et 40 caractères.
- Importance du canal.

Les applications devront vérifier la version d’Android qu’elles exécutent.
Les appareils exécutant des versions antérieures à Android 8,0 ne doivent pas créer un canal de notification. La méthode suivante est un exemple de création d’un canal de notification dans une activité :

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

Le canal de notification doit être créé chaque fois que l’activité est créée. Pour la méthode `CreateNotificationChannel`, elle doit être appelée dans la méthode `OnCreate` d’une activité.

### <a name="creating-and-publishing-a-notification"></a>Création et publication d’une notification

Pour générer une notification dans Android, procédez comme suit :

1. Instancier un objet `NotificationCompat.Builder`.

2. Appelez différentes méthodes sur l’objet `NotificationCompat.Builder` pour définir des options de notification.

3. Appelez la méthode de [génération](xref:Android.App.Notification.Builder.Build) de l’objet `NotificationCompat.Builder` pour instancier un objet de notification.

4. Appelez la méthode [Notify](xref:Android.App.NotificationManager.Notify*) du gestionnaire de notifications pour publier la notification.

Vous devez fournir au moins les informations suivantes pour chaque notification :

- Petite icône (24x24 DP en taille)

- Titre abrégé

- Texte de la notification

L’exemple de code suivant illustre l’utilisation de `NotificationCompat.Builder` pour générer une notification de base. Notez que les méthodes `NotificationCompat.Builder` prennent en charge le [chaînage de méthode](https://en.wikipedia.org/wiki/Method_chaining); autrement dit, chaque méthode retourne l’objet générateur afin que vous puissiez utiliser le résultat du dernier appel de la méthode pour appeler l’appel de méthode suivant :

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

Dans cet exemple, un nouvel objet `NotificationCompat.Builder` appelé `builder` est instancié, avec l’ID du canal de notification à utiliser. Le titre et le texte de la notification sont définis, et l’icône de notification est chargée à partir de **ressources/dessinable/ic_notification. png**. L’appel à la méthode `Build` du générateur de notification crée un objet de notification avec ces paramètres. L’étape suivante consiste à appeler la méthode `Notify` du gestionnaire de notifications. Pour rechercher le gestionnaire de notifications, vous appelez `GetSystemService`, comme indiqué ci-dessus.

La méthode `Notify` accepte deux paramètres : l’identificateur de notification et l’objet de notification. L’identificateur de notification est un entier unique qui identifie la notification à votre application. Dans cet exemple, l’identificateur de notification est défini sur zéro (0); Toutefois, dans une application de production, vous voudrez attribuer un identificateur unique à chaque notification. La réutilisation de la valeur d’identificateur précédente dans un appel à `Notify` provoque le remplacement de la dernière notification.

Lorsque ce code s’exécute sur un appareil Android 5,0, il génère une notification qui ressemble à l’exemple suivant :

![Résultat de la notification pour l’exemple de code](local-notifications-images/09-hello-world.png)

L’icône de notification s’affiche sur le côté gauche de la notification &ndash; cette image d’un &ldquo;cerclé i&rdquo; a un canal alpha pour qu’Android puisse dessiner un arrière-plan de cercle gris derrière. Vous pouvez également fournir une icône sans canal alpha. Pour afficher une image photographique sous forme d’icône, consultez [format grande icône](#large-icon-format) plus loin dans cette rubrique.

L’horodateur est défini automatiquement, mais vous pouvez remplacer ce paramètre en appelant la méthode [SetWhen](xref:Android.App.Notification.Builder.SetWhen*) du générateur de notifications. Par exemple, l’exemple de code suivant définit l’horodatage sur l’heure actuelle :

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Activation du son et des vibrations

Si vous souhaitez que votre notification émette également un son, vous pouvez appeler la méthode [SetDefaults](xref:Android.App.Notification.Builder.SetDefaults*) du générateur de notifications et passer l’indicateur `NotificationDefaults.Sound` :

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Cet appel à `SetDefaults` oblige l’appareil à émettre un signal sonore lors de la publication de la notification. Si vous souhaitez que l’appareil vibre au lieu de lire un son, vous pouvez passer `NotificationDefaults.Vibrate` à `SetDefaults.` si vous souhaitez que l’appareil lise un son et vibre l’appareil, vous pouvez transmettre les deux indicateurs à `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Si vous activez le son sans spécifier de son à lire, Android utilise le son de notification système par défaut. Toutefois, vous pouvez modifier le son qui sera lu en appelant la méthode [SetSound](xref:Android.App.Notification.Builder.SetSound*) du générateur de notifications. Par exemple, pour lire le son de l’alarme avec votre notification (au lieu du son de notification par défaut), vous pouvez obtenir l’URI du son de l’alarme à partir du [RingtoneManager](xref:Android.Media.RingtoneManager) et le transmettre à `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Vous pouvez également utiliser le son de sonnerie par défaut du système pour votre notification :

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Une fois que vous avez créé un objet de notification, il est possible de définir des propriétés de notification sur l’objet de notification (au lieu de les configurer à l’avance via les méthodes de `NotificationCompat.Builder`). Par exemple, au lieu d’appeler la méthode `SetDefaults` pour activer les vibrations sur une notification, vous pouvez modifier directement l’indicateur de bit de la propriété [par défaut](xref:Android.App.Notification.Defaults) de la notification :

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Cet exemple fait vibrer l’appareil lorsque la notification est publiée.

### <a name="updating-a-notification"></a>Mise à jour d’une notification

Si vous souhaitez mettre à jour le contenu d’une notification après sa publication, vous pouvez réutiliser l’objet `NotificationCompat.Builder` existant pour créer un nouvel objet de notification et publier cette notification avec l’identificateur de la dernière notification. Par exemple :

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

Dans cet exemple, l’objet `NotificationCompat.Builder` existant est utilisé pour créer un nouvel objet de notification avec un titre et un message différents.
Le nouvel objet de notification est publié à l’aide de l’identificateur de la notification précédente, ce qui met à jour le contenu de la notification précédemment publiée :

![Notification mise à jour](local-notifications-images/12-updated-notification.png)

Le corps de la notification précédente est réutilisé &ndash; seul le titre et le texte de la notification change alors que la notification est affichée dans le tiroir de notification. Le texte du titre passe de « exemple de notification » à « notification mise à jour » et le texte du message est remplacé par «Hello World ! C’est ma première notification !» « Remplacé par ce message ».

Une notification reste visible jusqu’à ce que l’un des trois événements suivants se produise :

- L’utilisateur ignore la notification (ou clique sur *Effacer tout*).

- L’application effectue un appel à `NotificationManager.Cancel`, en passant l’ID de notification unique qui a été attribué lors de la publication de la notification.

- L’application appelle `NotificationManager.CancelAll`.

Pour plus d’informations sur la mise à jour des notifications Android, consultez [modifier une notification](https://developer.android.com/training/notify-user/managing.html#Updating).

### <a name="starting-an-activity-from-a-notification"></a>Démarrage d’une activité à partir d’une notification

Dans Android, il est courant qu’une notification soit associée à une *action* &ndash; une activité qui est lancée lorsque l’utilisateur appuie sur la notification. Cette activité peut résider dans une autre application ou même dans une autre tâche. Pour ajouter une action à une notification, vous créez un objet [PendingIntent](xref:Android.App.PendingIntent) et associez le `PendingIntent` à la notification. Un `PendingIntent` est un type spécial d’intention qui permet à l’application destinataire d’exécuter un morceau de code prédéfini avec les autorisations de l’application émettrice. Quand l’utilisateur appuie sur la notification, Android démarre l’activité spécifiée par le `PendingIntent`.

L’extrait de code suivant illustre la création d’une notification avec un `PendingIntent` qui lance l’activité de l’application d’origine, `MainActivity`:

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

Ce code est très similaire au code de notification de la section précédente, à ceci près qu’un `PendingIntent` est ajouté à l’objet de notification. Dans cet exemple, l' `PendingIntent` est associé à l’activité de l’application d’origine avant d’être passée à la méthode [SetContentIntent](xref:Android.App.Notification.Builder.SetContentIntent*) du générateur de notifications. L’indicateur `PendingIntentFlags.OneShot` est passé à la méthode `PendingIntent.GetActivity` afin que le `PendingIntent` soit utilisé une seule fois. Lorsque ce code s’exécute, la notification suivante s’affiche :

![Première notification d’action](local-notifications-images/10-first-action-notification.png)

Le fait de cliquer sur cette notification ramène l’utilisateur à l’activité d’origine.

Dans une application de production, votre application doit gérer la *pile de retour* lorsque l’utilisateur appuie sur le bouton **précédent** dans l’activité de notification (si vous n’êtes pas familiarisé avec les tâches Android et la pile de retour, consultez [tâches et pile Back](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
Dans la plupart des cas, la navigation vers l’arrière de l’activité de notification doit retourner l’utilisateur hors de l’application et revenir à l’écran d’accueil. Pour gérer la pile de retour, votre application utilise la classe [TaskStackBuilder](xref:Android.App.TaskStackBuilder) pour créer un `PendingIntent` avec une pile de retour.

Un autre aspect réel est que l’activité d’origine peut avoir besoin d’envoyer des données à l’activité de notification. Par exemple, la notification peut indiquer qu’un message texte est arrivé, et l’activité de notification (un écran d’affichage des messages), requiert l’ID du message pour afficher le message à l’utilisateur. L’activité qui crée le `PendingIntent` peut utiliser la méthode [intentionnelle. PutExtra](xref:Android.Content.Intent.PutExtra*) pour ajouter des données (par exemple, une chaîne) à l’intention afin que ces données soient passées à l’activité de notification.

L’exemple de code suivant montre comment utiliser `TaskStackBuilder` pour gérer la pile de retour, et fournit un exemple d’envoi d’une chaîne de message unique à une activité de notification appelée `SecondActivity`:

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

Dans cet exemple de code, l’application se compose de deux activités : `MainActivity` (qui contient le code de notification ci-dessus) et `SecondActivity`, l’écran que l’utilisateur voit après avoir appuyé sur la notification. Quand ce code est exécuté, une simple notification (semblable à l’exemple précédent) est présentée. Le fait de cliquer sur la notification amène l’utilisateur à l’écran `SecondActivity` :

![Capture d’écran de la deuxième activité](local-notifications-images/11-second-activity.png)

Le message de type chaîne (passé dans la méthode `PutExtra` de l’intention) est récupéré dans `SecondActivity` à l’aide de cette ligne de code :

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Ce message extrait, « Greetings from MainActivity ! », s’affiche dans l’écran `SecondActivity`, comme indiqué dans la capture d’écran ci-dessus. Quand l’utilisateur appuie sur le bouton **précédent** alors qu’il se trouve dans `SecondActivity`, la navigation sort de l’application et revient à l’écran précédant le lancement de l’application.

Pour plus d’informations sur la création d’intentions en attente, consultez [PendingIntent](xref:Android.App.PendingIntent).

<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Au-delà de la notification de base

Par défaut, les notifications ont un format de disposition de base simple dans Android, mais vous pouvez améliorer ce format de base en effectuant des appels de méthode de `NotificationCompat.Builder` supplémentaires. Dans cette section, vous allez apprendre à ajouter une grande icône de photo à votre notification. vous verrez des exemples de création de notifications de mise en page étendues.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Format de grande icône

Les notifications Android affichent généralement l’icône de l’application d’origine (sur le côté gauche de la notification). Toutefois, les notifications peuvent afficher une image ou une photo (une *grande icône*) au lieu de la petite icône standard. Par exemple, une application de messagerie peut afficher une photo de l’expéditeur plutôt que l’icône de l’application.

Voici un exemple de notification Android 5,0 de base &ndash; il affiche uniquement la petite icône d’application :

![Exemple de notification normale](local-notifications-images/13-sample-notification.png)

Et voici une capture d’écran de la notification après l’avoir modifiée pour afficher une grande icône &ndash; elle utilise une icône créée à partir d’une image d’un singe de code Xamarin :

![Exemple de notification de grande icône](local-notifications-images/14-large-icon-sample.png)

Notez que lorsqu’une notification est présentée dans un format de grande icône, la petite icône d’application s’affiche sous la forme d’un badge dans le coin inférieur droit de la grande icône.

Pour utiliser une image en tant que grande icône dans une notification, vous appelez la méthode [SetLargeIcon](xref:Android.App.Notification.Builder.SetLargeIcon*) du générateur de notifications et transmettez une image bitmap de l’image. Contrairement à `SetSmallIcon`, `SetLargeIcon` accepte uniquement une image bitmap. Pour convertir un fichier image en bitmap, vous utilisez la classe [BitmapFactory](xref:Android.Graphics.BitmapFactory) . Par exemple :

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Cet exemple de code ouvre le fichier image à l’adresse **ressources/dessinable/monkey_icon. png**, le convertit en bitmap et passe le bitmap résultant à `NotificationCompat.Builder`. En règle générale, la résolution de l’image source est supérieure à celle de la petite icône &ndash; mais pas bien plus grande. Une image trop grande peut entraîner des opérations de redimensionnement inutiles qui pourraient retarder la publication de la notification.

### <a name="big-text-style"></a>Style de texte Big

Le style *Big Text* est un modèle de mise en page développé que vous utilisez pour afficher les messages longs dans les notifications. Comme toutes les notifications de mise en page étendues, la notification Big Text est initialement affichée dans un format de présentation compact :

![Exemple de notification de grand texte](local-notifications-images/15-big-text-notification.png)

Dans ce format, seul un extrait du message est affiché, se terminant par deux points. Lorsque l’utilisateur fait glisser vers le niveau de la notification, il se développe pour afficher l’intégralité du message de notification :

![Notification de Big Text développée](local-notifications-images/16-big-text-expanded.png)

Ce format de disposition développé comprend également un résumé du texte en bas de la notification. La hauteur maximale de la notification de *grand texte* est de 256 DP.

Pour créer une notification de *texte volumineux* , vous devez instancier un objet `NotificationCompat.Builder`, comme précédemment, puis instancier et ajouter un objet [BigTextStyle](xref:Android.App.Notification.BigTextStyle) à l’objet `NotificationCompat.Builder`. Voici un exemple :

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

Dans cet exemple, le texte du message et le texte de synthèse sont stockés dans l’objet `BigTextStyle` (`textStyle`) avant d’être transmis à `NotificationCompat.Builder.`

### <a name="image-style"></a>Style d’image

Le style d' *image* (également appelé style d' *image Big* ) est un format de notification développé que vous pouvez utiliser pour afficher une image dans le corps d’une notification. Par exemple, une application de capture d’écran ou une application photo peut utiliser le style de notification d' *image* pour fournir à l’utilisateur une notification de la dernière image capturée. Notez que la hauteur maximale de la notification d' *image* est de 256 DP &ndash; Android redimensionne l’image pour s’adapter à cette restriction de hauteur maximale, dans les limites de la mémoire disponible.

Comme toutes les notifications de mise en page étendues, les notifications d' *image* sont d’abord affichées dans un format compact qui affiche un extrait du texte du message d’accompagnement :

![La notification d’image compacte n’affiche aucune image](local-notifications-images/17-image-compact.png)

Lorsque l’utilisateur fait glisser vers le niveau de la notification d' *image* , il se développe pour afficher une image. Par exemple, voici la version développée de la notification précédente :

![La notification d’image développée révèle une image](local-notifications-images/18-image-expanded.png)

Notez que lorsque la notification est affichée au format compact, elle affiche le texte de notification (le texte qui est transmis à la méthode `SetContentText` du générateur de notification, comme indiqué plus haut). Toutefois, lorsque la notification est développée pour afficher l’image, elle affiche le texte de résumé au-dessus de l’image.

Pour créer une notification d' *image* , vous instanciez un objet `NotificationCompat.Builder` comme précédemment, puis vous créez et insérez un objet [BigPictureStyle](xref:Android.App.Notification.BigPictureStyle) dans l’objet `NotificationCompat.Builder`. Par exemple :

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

À l’instar de la méthode `SetLargeIcon` de `NotificationCompat.Builder`, la méthode [BigPicture](xref:Android.App.Notification.BigPictureStyle.BigPicture*) de `BigPictureStyle` requiert un bitmap de l’image que vous souhaitez afficher dans le corps de la notification. Dans cet exemple, la méthode [DecodeResource](xref:Android.Graphics.BitmapFactory.DecodeResource*) de `BitmapFactory` lit le fichier image situé dans **ressources/drawable/x_bldg. png** et le convertit en bitmap.

Vous pouvez également afficher des images qui ne sont pas empaquetées en tant que ressources. Par exemple, l’exemple de code suivant charge une image à partir de la carte SD locale et l’affiche dans une notification d' *image* :

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

Dans cet exemple, le fichier image situé dans **/sdcard/pictures/My-Tshirt.jpg** est chargé, redimensionné à la moitié de sa taille d’origine, puis converti en bitmap en vue de son utilisation dans la notification :

![Exemple d’image T-Shirt dans la notification](local-notifications-images/19-tshirt-notification.png)

Si vous ne connaissez pas la taille du fichier image à l’avance, il est judicieux d’encapsuler l’appel à [BitmapFactory. DecodeFile](xref:Android.Graphics.BitmapFactory.DecodeFile*) dans un gestionnaire d’exceptions &ndash; une exception `OutOfMemoryError` peut être levée si l’image est trop grande pour le redimensionnement d’Android.

Pour plus d’informations sur le chargement et le décodage d’images bitmap volumineuses, consultez [charger des bitmaps volumineuses efficacement](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).

### <a name="inbox-style"></a>Style de la boîte de réception

Le format de la *boîte de réception* est un modèle de mise en page développé destiné à l’affichage de lignes de texte distinctes (par exemple, un résumé de la boîte de réception de courrier électronique) dans le corps de la notification. La notification de format de *boîte de réception* est d’abord affichée dans un format compact :

![Exemple de notification de boîte de réception compacte](local-notifications-images/20-inbox-compact.png)

Lorsque l’utilisateur fait glisser vers le bas sur la notification, il se développe pour afficher un résumé des e-mails comme indiqué dans la capture d’écran ci-dessous :

![Exemple de notification de boîte de réception développée](local-notifications-images/21-inbox-expanded.png)

Pour créer une notification de *boîte de réception* , vous devez instancier un objet `NotificationCompat.Builder`, comme précédemment, et ajouter un objet [InboxStyle](xref:Android.App.Notification.InboxStyle) à l' `NotificationCompat.Builder`. Voici un exemple :

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

Pour ajouter de nouvelles lignes de texte au corps de la notification, appelez la méthode [AddLine](xref:Android.App.Notification.InboxStyle.AddLine*) de l’objet `InboxStyle` (la hauteur maximale de la notification de la *boîte de réception* est 256 DP). Notez que, contrairement au style de *texte Big* , le style de la *boîte de réception* prend en charge des lignes de texte individuelles dans le corps de la notification.

Vous pouvez également utiliser le style de la *boîte de réception* pour toute notification qui doit afficher des lignes de texte individuelles dans un format développé. Par exemple, le style de notification de la *boîte de réception* peut être utilisé pour combiner plusieurs notifications en attente dans une notification de résumé &ndash; vous pouvez mettre à jour une notification de type *boîte de réception* unique avec de nouvelles lignes de contenu de notification (voir [mise à jour d’une notification](#updating-a-notification) ci-dessus), au lieu de générer un flux continu de nouvelles notifications similaires.

## <a name="configuring-metadata"></a>Configuration des métadonnées

`NotificationCompat.Builder` comprend des méthodes que vous pouvez appeler pour définir les métadonnées relatives à votre notification, telles que la priorité, la visibilité et la catégorie. Android utilise ces informations &mdash;, ainsi que les paramètres de préférence utilisateur &mdash; pour déterminer comment et quand afficher les notifications.

### <a name="priority-settings"></a>Paramètres de priorité

Les applications qui s’exécutent sur Android 7,1 et les versions inférieures doivent définir la priorité directement sur la notification elle-même. Le paramètre de priorité d’une notification détermine deux résultats lors de la publication de la notification :

- Où la notification s’affiche par rapport à d’autres notifications.
    Par exemple, les notifications de priorité élevée sont présentées au-dessus des notifications de priorité inférieure dans le tiroir notifications, quel que soit le moment où chaque notification a été publiée.

- Indique si la notification est affichée dans le format de notification de tête (Android 5,0 et versions ultérieures). Seules les notifications de priorité *élevée* et *maximale* sont affichées sous forme de notifications de niveau supérieur.

Xamarin. Android définit les énumérations suivantes pour la définition de la priorité de notification :

- `NotificationPriority.Max` &ndash; avertit l’utilisateur d’une condition urgente ou critique (par exemple, un appel entrant, une direction de tour ou une alerte d’urgence). Sur les appareils Android 5,0 et versions ultérieures, les notifications de priorité maximale sont affichées au format en tête.

- `NotificationPriority.High` &ndash; informe l’utilisateur des événements importants (tels que les courriers électroniques importants ou l’arrivée de messages de conversation en temps réel). Sur les appareils Android 5,0 et versions ultérieures, les notifications de priorité élevée sont affichées au format en tête.

- `NotificationPriority.Default` &ndash; avertit l’utilisateur des conditions qui ont un niveau de gravité moyen.

- `NotificationPriority.Low` &ndash; pour les informations non urgentes dont l’utilisateur a besoin pour être informé (par exemple, les rappels de mises à jour logicielles ou les mises à jour du réseau social).

- `NotificationPriority.Min` &ndash; pour les informations générales que l’utilisateur remarque uniquement lors de l’affichage des notifications (par exemple, des informations sur l’emplacement ou la météo).

Pour définir la priorité d’une notification, appelez la méthode [SetPriority](xref:Android.App.Notification.Builder.SetPriority*) de l’objet `NotificationCompat.Builder`, en passant le niveau de priorité. Par exemple :

```csharp
builder.SetPriority (NotificationPriority.High);
```

Dans l’exemple suivant, la notification de priorité élevée, « un message important ! » apparaît en haut du tiroir de notification :

![Exemple de notification de priorité élevée](local-notifications-images/22-hi-priority-drawer.png)

Étant donné qu’il s’agit d’une notification de priorité élevée, elle est également affichée sous forme de notification de tête au-dessus de l’écran d’activité actuel de l’utilisateur dans Android 5,0 :

![Exemple de notification de tête](local-notifications-images/23-heads-up-example.png)

Dans l’exemple suivant, la notification de faible priorité « pensée pour la journée » s’affiche sous une notification de niveau de batterie de priorité plus élevée :

![Exemple de notification de faible priorité](local-notifications-images/24-lo-priority-drawer.png)

Étant donné que la notification « pensée pour la journée » est une notification de faible priorité, Android ne l’affiche pas dans le format de tête.

> [!NOTE]
> Sur Android 8,0 et versions ultérieures, la priorité du canal de notification et des paramètres utilisateur déterminera la priorité de la notification.

### <a name="visibility-settings"></a>Paramètres de visibilité

À compter d’Android 5,0, le paramètre de *visibilité* est disponible pour contrôler la quantité de contenu de notification qui s’affiche sur l’écran verrouillage sécurisé.
Xamarin. Android définit les énumérations suivantes pour la définition de la visibilité des notifications :

- `NotificationVisibility.Public` &ndash; le contenu complet de la notification s’affiche sur l’écran verrouillage sécurisé.

- `NotificationVisibility.Private` &ndash; seules les informations essentielles sont affichées sur l’écran de verrouillage sécurisé (par exemple, l’icône de notification et le nom de l’application qui l’a publié), mais le reste des détails de la notification sont masqués. Toutes les notifications ont par défaut la valeur `NotificationVisibility.Private`.

- `NotificationVisibility.Secret` &ndash; rien ne s’affiche sur l’écran verrouillage sécurisé, pas même l’icône de notification. Le contenu de la notification n’est disponible qu’une fois que l’utilisateur a déverrouillé l’appareil.

Pour définir la visibilité d’une notification, les applications appellent la méthode `SetVisibility` de l’objet `NotificationCompat.Builder`, en passant le paramètre de visibilité. Par exemple, cet appel à `SetVisibility` fait de la notification `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Lors de la publication d’une notification `Private`, seul le nom et l’icône de l’application s’affichent sur l’écran verrouillage sécurisé. Au lieu du message de notification, l’utilisateur voit « déverrouiller votre appareil pour voir cette notification » :

![Déverrouiller le message de notification de votre appareil](local-notifications-images/25-lockscreen-private.png)

Dans cet exemple, **NotificationsLab** est le nom de l’application d’origine. Cette version biffée de la notification s’affiche uniquement lorsque l’écran de verrouillage est sécurisé (par exemple, sécurisé par le biais du PIN, du modèle ou du mot de passe) &ndash; si l’écran de verrouillage n’est pas sécurisé, le contenu complet de la notification est disponible sur l’écran de verrouillage.

### <a name="category-settings"></a>Paramètres de catégorie

À partir d’Android 5,0, les catégories prédéfinies sont disponibles pour le classement et le filtrage des notifications. Xamarin. Android fournit les énumérations suivantes pour ces catégories :

- `Notification.CategoryCall` &ndash; appel téléphonique entrant.

- `Notification.CategoryMessage` &ndash; message texte entrant.

- `Notification.CategoryAlarm` &ndash; une condition d’alarme ou une expiration du minuteur.

- `Notification.CategoryEmail` &ndash; message électronique entrant.

- `Notification.CategoryEvent` &ndash; un événement de calendrier.

- `Notification.CategoryPromo` &ndash; un message ou une publicité promotionnel.

- `Notification.CategoryProgress` &ndash; la progression d’une opération en arrière-plan.

- `Notification.CategorySocial` &ndash; mise à jour de réseau social.

- `Notification.CategoryError` &ndash; l’échec d’une opération d’arrière-plan ou d’un processus d’authentification.

- `Notification.CategoryTransport` &ndash; la mise à jour de la lecture du média.

- `Notification.CategorySystem` &ndash; réservé à l’utilisation du système (État du système ou de l’appareil).

- `Notification.CategoryService` &ndash; indique qu’un service en arrière-plan est en cours d’exécution.

- `Notification.CategoryRecommendation` &ndash; un message de recommandation lié à l’application en cours d’exécution.

- `Notification.CategoryStatus` &ndash; des informations sur l’appareil.

Lorsque les notifications sont triées, la priorité de la notification est prioritaire par rapport à son paramètre de catégorie. Par exemple, une notification de haute priorité sera affichée en tant que tête, même si elle appartient à la catégorie `Promo`. Pour définir la catégorie d’une notification, vous appelez la méthode `SetCategory` de l’objet `NotificationCompat.Builder`, en passant le paramètre de catégorie. Par exemple :

```csharp
builder.SetCategory (Notification.CategoryCall);
```

La fonctionnalité *ne pas déranger* (nouveauté d’Android 5,0) filtre les notifications en fonction des catégories. Par exemple, l’écran *ne pas déranger* des **paramètres** permet à l’utilisateur d’exempter les notifications pour les appels téléphoniques et les messages :

![Ne pas déranger les commutateurs d’écran](local-notifications-images/26-do-not-disturb.png)

Lorsque l’utilisateur configure *ne pas déranger* pour bloquer toutes les interruptions, à l’exception des appels téléphoniques (comme illustré dans la capture d’écran ci-dessus), Android autorise la présentation des notifications avec un paramètre de catégorie `Notification.CategoryCall` lorsque l’appareil est en mode *ne pas déranger* . Notez que `Notification.CategoryAlarm` les notifications ne sont jamais bloquées en mode *ne pas déranger* .

L’exemple [LocalNotifications](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications) montre comment utiliser `NotificationCompat.Builder` pour lancer une deuxième activité à partir d’une notification. Cet exemple de code est expliqué dans la procédure pas [à pas utilisation de notifications locales dans Xamarin. Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) .

### <a name="notification-styles"></a>Styles de notification

Pour créer des notifications de style *Big Text*, *image*ou *Inbox* avec `NotificationCompat.Builder`, votre application doit utiliser les versions de compatibilité de ces styles. Par exemple, pour utiliser le style *Big Text* , instanciez `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

De même, votre application peut utiliser `NotificationCompat.InboxStyle` et `NotificationCompat.BigPictureStyle` pour les styles de *boîte de réception* et d' *image* , respectivement.

### <a name="notification-priority-and-category"></a>Catégorie et priorité de notification

`NotificationCompat.Builder` prend en charge la méthode `SetPriority` (disponible à partir d’Android 4,1). Toutefois, la méthode `SetCategory` n’est *pas* prise en charge par `NotificationCompat.Builder` car les catégories font partie du nouveau système de métadonnées de notification introduit dans Android 5,0.

Pour prendre en charge les versions antérieures d’Android, où `SetCategory` n’est pas disponible, votre code peut vérifier le niveau d’API au moment de l’exécution pour appeler de manière conditionnelle `SetCategory` lorsque le niveau d’API est supérieur ou égal à Android 5,0 (niveau d’API 21) :

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

Dans cet exemple, le **Framework cible** de l’application est défini sur Android 5,0 et la **version Android minimale** est définie sur **Android 4,1 (niveau d’API 16)** . Étant donné que `SetCategory` est disponible dans le niveau d’API 21 et ultérieur, cet exemple de code appellera `SetCategory` uniquement lorsqu’il est disponible &ndash; il n’appellera pas `SetCategory` lorsque le niveau d’API est inférieur à 21.

### <a name="lock-screen-visibility"></a>Visibilité de l’écran de verrouillage

Android ne prenant pas en charge les notifications de verrouillage de l’écran avant Android 5,0 (niveau d’API 21), `NotificationCompat.Builder` ne prend pas en charge la méthode `SetVisibility`. Comme expliqué ci-dessus pour `SetCategory`, votre code peut vérifier le niveau d’API au moment de l’exécution et appeler `SetVisiblity` uniquement lorsqu’il est disponible :

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```

## <a name="summary"></a>Résumé

Cet article a expliqué comment créer des notifications locales dans Android. Il a décrit l’anatomie d’une notification, explique comment utiliser `NotificationCompat.Builder` pour créer des notifications, comment appliquer un style aux notifications dans des formats de grande icône, de *texte volumineux*, d' *image* et de *boîte de réception* , comment définir des paramètres de métadonnées de notification tels que priorité, visibilité et catégorie, et comment lancer une activité à partir d’une notification. Cet article a également décrit comment ces paramètres de notification fonctionnent avec les nouvelles fonctionnalités, l’écran de verrouillage et *ne perturbent pas* les fonctionnalités introduites dans Android 5,0. Enfin, vous avez appris à utiliser `NotificationCompat.Builder` pour maintenir la compatibilité des notifications avec les versions antérieures d’Android.

Pour obtenir des instructions sur la conception de notifications pour Android, consultez [notifications](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

## <a name="related-links"></a>Liens connexes

- [NotificationsLab (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-notificationslab)
- [LocalNotifications (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Notifications locales dans la procédure pas à pas Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notification de l’utilisateur](https://developer.android.com/training/notify-user/index.html)
- [Notification](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
