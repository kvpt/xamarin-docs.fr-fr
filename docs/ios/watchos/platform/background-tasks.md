---
title: Tâches en arrière-plan Watchos dans Xamarin
description: Ce document explique comment utiliser des tâches en arrière-plan avec Watchos dans Xamarin, en examinant les types de tâches en arrière-plan, en utilisant des ressources, en implémentant des tâches en arrière-plan, la planification, les meilleures pratiques et bien plus encore.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/13/2017
ms.openlocfilehash: b01fbbe813b778d3c2e1cabeba620ed48a46ecac
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287154"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Tâches en arrière-plan Watchos dans Xamarin

Avec Watchos 3, il existe trois manières principales pour une application de surveillance de conserver ses informations à jour : 

- À l’aide de l’une des nombreuses nouvelles tâches en arrière-plan. 
- Avoir un de ses complications sur la face de la montre (en lui donnant plus de temps de mise à jour). 
- Faire en sorte que l’utilisateur épingle à l’application sur le nouvel ancrage (où elle est conservée en mémoire et souvent mise à jour). 

## <a name="keeping-an-app-up-to-date"></a>Mise à jour d’une application

Avant d’aborder toutes les façons dont un développeur peut garder à jour et à mettre à jour l’interface utilisateur et les données de l’application Watchos, cette section examine un ensemble typique d’utilisations et comment un utilisateur peut se déplacer entre son iPhone et son Apple Watch tout au long de la journée en fonction de  l’heure de la journée et l’activité en cours (par exemple, la conduite).

Prenons l’exemple suivant :

[![](background-tasks-images/update00.png "Comment un utilisateur peut se déplacer entre son iPhone et son Apple Watch tout au long de la journée")](background-tasks-images/update00.png#lightbox)

1. Le matin, en attendant une ligne pour un café, l’utilisateur parcourt les actualités actuelles sur son iPhone pendant plusieurs minutes.
2. Avant de quitter le café, ils vérifient rapidement la météo avec une complication sur le visage de la montre.
3. Avant le déjeuner, ils utilisent l’application Maps sur l’iPhone pour trouver un restaurant proche et réserver une réservation pour se conformer à un client.
4. Lorsqu’ils se déplacent vers le restaurant, ils reçoivent une notification sur leur Apple Watch et avec un coup d’œil rapide, ils savent que leur rendez-vous de déjeuner s’exécute tard.
5. Au soir, ils utilisent l’application Maps sur l’iPhone pour vérifier le trafic avant de se rediriger.
6. Sur la base, ils reçoivent une notification iMessage sur leur Apple Watch leur demandant de récupérer du lait et ils utilisent la fonctionnalité de réponse rapide pour envoyer la réponse « OK ».

En raison de la nature « aperçu rapide » (moins de trois secondes) de la façon dont un utilisateur souhaite utiliser un Apple Watch application, il n’y a généralement pas suffisamment de temps pour que l’application récupère les informations souhaitées et met à jour son interface utilisateur avant de l’afficher à l’utilisateur.

En utilisant les nouvelles API Apple incluses dans Watchos 3, l’application peut planifier une actualisation en _arrière-plan_ et préparer les informations souhaitées avant que l’utilisateur ne la demande. Prenons l’exemple de la complications météorologiques abordée ci-dessus :

[![](background-tasks-images/update01.png "Exemple de la complications météorologiques")](background-tasks-images/update01.png#lightbox)

1. Les planifications d’application doivent être réveillées par le système à un moment donné. 
2. L’application extrait les informations nécessaires à la génération de la mise à jour.
3. L’application régénère son interface utilisateur pour refléter les nouvelles données.
4. Lorsque l’utilisateur parvient à la complication de l’application, il dispose d’informations à jour sans que l’utilisateur n’ait à attendre la mise à jour.

Comme indiqué ci-dessus, le système Watchos réveille l’application à l’aide d’une ou de plusieurs tâches dont un pool très limité est disponible :

[![](background-tasks-images/update02.png "Le système Watchos réveille l’application à l’aide d’une ou de plusieurs tâches")](background-tasks-images/update02.png#lightbox)

Apple suggérant d’effectuer la majeure partie de cette tâche (puisqu’il s’agit d’une ressource limitée à l’application) en la déposant jusqu’à ce que l’application ait terminé le processus de mise à jour.

Le système fournit ces tâches en appelant la nouvelle `HandleBackgroundTasks` méthode `WKExtensionDelegate` du délégué. Par exemple :

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Constructors
    public ExtensionDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request here
      ...
    }
    #endregion
  }
}
```

Lorsque l’application a terminé la tâche donnée, elle le retourne au système en la marquant comme terminée :

[![](background-tasks-images/update03.png "La tâche revient au système en la marquant comme terminée")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nouvelles tâches en arrière-plan

Watchos 3 introduit plusieurs tâches en arrière-plan qu’une application peut utiliser pour mettre à jour ses informations afin de s’assurer qu’elle dispose du contenu dont l’utilisateur a besoin avant d’ouvrir l’application, par exemple :

- **Actualisation** de l’application en arrière-plan : la tâche [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) permet à l’application de mettre à jour son état en arrière-plan. Cela inclut généralement une autre tâche, par exemple le téléchargement d’un nouveau contenu à partir d’Internet à l’aide d’un [passer](https://developer.apple.com/reference/foundation/nsurlsession).
- **Actualisation des instantanés d’arrière-plan** : la tâche [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) permet à l’application de mettre à jour son contenu et son interface utilisateur avant que le système prenne un instantané qui sera utilisé pour remplir l’ancre.
- **Connectivité de surveillance en arrière-plan** : la tâche [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) est démarrée pour l’application lorsqu’elle reçoit des données d’arrière-plan de l’iPhone couplé.
- **Session d’URL d’arrière-plan** : la tâche [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) est démarrée pour l’application lorsqu’un transfert en arrière-plan requiert une autorisation ou se termine (avec succès ou par erreur).

Ces tâches sont traitées en détail dans les sections ci-dessous.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask` Est une tâche générique qui peut être planifiée pour que l’application soit réveillée à une date ultérieure :

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask réveill à une date ultérieure")](background-tasks-images/update04.png#lightbox)

Dans le cadre de l’exécution de la tâche, l’application peut effectuer n’importe quel type de traitement local, par exemple mettre à jour une chronologie de `NSUrlSession`complication ou extraire les données requises avec un.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

Le système envoie une `WKURLSessionRefreshBackgroundTask` lorsque les données ont terminé le téléchargement et sont prêtes à être traitées par l’application :

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask lorsque le téléchargement des données est terminé")](background-tasks-images/update05.png#lightbox)

Une application ne reste pas en cours d’exécution pendant le téléchargement des données en arrière-plan. Au lieu de cela, l’application planifie la demande de données, puis elle est suspendue et le système gère le téléchargement des données, en ne autoservant que l’application une fois le téléchargement terminé.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

Dans Watchos 3, Apple a ajouté la station d’accueil dans laquelle les utilisateurs peuvent épingler leurs applications favorites et y accéder rapidement. Quand l’utilisateur appuie sur le bouton latéral sur la Apple Watch, une galerie d’instantanés d’application épinglés s’affiche. L’utilisateur peut balayer vers la gauche ou la droite pour trouver l’application souhaitée, puis appuyer sur l’application pour la lancer et remplacer l’instantané par l’interface de l’application en cours d’exécution.

[![](background-tasks-images/update06.png "Remplacement de l’instantané par l’interface des applications en cours d’exécution")](background-tasks-images/update06.png#lightbox)

Le système prend régulièrement des instantanés de l’interface utilisateur de l’application ( `WKSnapshotRefreshBackgroundTask`en envoyant un) et utilise ces instantanés pour remplir l’ancre. Watchos donne à l’application la possibilité de mettre à jour son contenu et son interface utilisateur avant que cet instantané ne soit pris.

Les instantanés sont très importants dans Watchos 3, car ils fonctionnent à la fois comme images d’aperçu et de lancement pour l’application. Si l’utilisateur règle sur une application dans le Dock, il s’agrandit en plein écran, entre le premier plan et démarre l’exécution. il est donc impératif que l’instantané soit à jour :

[![](background-tasks-images/update07.png "Si l’utilisateur règle sur une application dans le Dock, il se développe en plein écran")](background-tasks-images/update07.png#lightbox)

Là encore, le système émet un `WKSnapshotRefreshBackgroundTask` pour que l’application puisse préparer (en mettant à jour les données et l’interface utilisateur) avant la prise de l’instantané :

[![](background-tasks-images/update08.png "L’application peut se préparer en mettant à jour les données et l’interface utilisateur avant la prise de l’instantané.")](background-tasks-images/update08.png#lightbox)

Lorsque l’application marque le `WKSnapshotRefreshBackgroundTask` terminé, le système prend automatiquement un instantané de l’interface utilisateur de l’application.

> [!IMPORTANT]
> Il est important de toujours planifier un `WKSnapshotRefreshBackgroundTask` après que l’application a reçu de nouvelles données et mis à jour son interface utilisateur, ou que l’utilisateur ne voit pas les informations modifiées.




En outre, lorsque l’utilisateur reçoit une notification de l’application et l’appuie pour amener l’application au premier plan, l’instantané doit être à jour puisqu’il agit également comme écran de lancement :

[![](background-tasks-images/update09.png "L’utilisateur reçoit une notification de l’application et l’appuie pour amener l’application au premier plan")](background-tasks-images/update09.png#lightbox)

S’il y a plus d’une heure depuis que l’utilisateur a interagi avec une application Watchos, il peut revenir à son état par défaut. L’État par défaut peut signifier différentes choses pour différentes applications et, en fonction de la conception d’une application, il peut ne pas avoir d’État par défaut.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

Dans Watchos 3, Apple a intégré la connectivité de surveillance avec l’API d’actualisation en `WKWatchConnectivityRefreshBackgroundTask`arrière-plan via le nouveau. À l’aide de cette nouvelle fonctionnalité, une application iPhone peut fournir des données actualisées à son équivalent d’application Watch, tandis que l’application Watchos s’exécute en arrière-plan :

[![](background-tasks-images/update10.png "Une application iPhone peut fournir des données actualisées à son homologue d’application Watch, tandis que l’application Watchos s’exécute en arrière-plan")](background-tasks-images/update10.png#lightbox)

L’initiation d’un push de complication, d’un contexte d’application, de l’envoi d’un fichier ou de la mise à jour des informations utilisateur à partir de l’application iPhone met en éveil l’application Apple Watch en arrière-plan.

Lorsque l’application Watch est réveillée via `WKWatchConnectivityRefreshBackgroundTask` un, elle doit utiliser les méthodes d’API standard pour recevoir les données de l’application iPhone.

[![](background-tasks-images/update11.png "Le workflow de données WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Vérifiez que la session a été activée.
2. Surveillez `HasContentPending` la nouvelle propriété tant que la valeur `true`est, l’application a toujours des données à traiter. Comme précédemment, l’application doit tenir sur la tâche jusqu’à ce qu’elle ait terminé le traitement de toutes les données.
3. Lorsqu’il n’y a plus de données à traiter`HasContentPending = false`(), marquez la tâche comme terminée pour la renvoyer au système. Si vous n’effectuez pas cette opération, l’exécution de l’application en arrière-plan allouée à l’application est interrompue.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Cycle de vie de l’API en arrière-plan

En plaçant tous les éléments de la nouvelle API tâches en arrière-plan ensemble, un ensemble d’interactions standard ressemble à ce qui suit :

[![](background-tasks-images/update12.png "Cycle de vie de l’API en arrière-plan")](background-tasks-images/update12.png#lightbox)

1. Tout d’abord, l’application Watchos planifie une tâche en arrière-plan à en éveil à un moment donné dans le futur.
2. L’application est réveillée par le système et a envoyé une tâche.
3. L’application traite la tâche pour terminer le travail requis.
4. Suite au traitement de la tâche, l’application devra peut-être planifier davantage de tâches en arrière-plan pour terminer davantage de travail, par exemple en téléchargeant davantage de contenu à l’aide d’un `NSUrlSession`.
5. L’application marque la tâche comme terminée et la retourne au système.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Utilisation des ressources de manière responsable

Il est essentiel qu’une application Watchos se comporte de manière responsable au sein de cet écosystème en limitant son drainage sur les ressources partagées du système.

Jetez un coup d’œil au scénario suivant :

[![](background-tasks-images/update13.png "Une application Watchos limite son drainage sur les ressources partagées du système")](background-tasks-images/update13.png#lightbox)

1. L’utilisateur lance une application Watchos à 1:00 h 00.
2. L’application planifie une tâche pour réveiller et télécharger le nouveau contenu en une heure à 2:00 h 00.
3. À 1:50, l’utilisateur ouvre à nouveau l’application, ce qui lui permet de mettre à jour ses données et l’interface utilisateur pour l’instant.
4. Au lieu de laisser la tâche sortir de nouveau l’application dans un délai de 10 minutes, l’application doit replanifier la tâche pour qu’elle s’exécute une heure plus tard à 2:50 h 00.

Tandis que chaque application est différente, Apple suggère de trouver des modèles d’utilisation, comme ceux indiqués ci-dessus, afin de préserver les ressources système.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implémentation des tâches en arrière-plan

À titre d’exemple, ce document utilise l’application MonkeySoccer Sports factice qui signale les scores de football à l’utilisateur. 

Jetez un coup d’œil au scénario d’utilisation classique suivant :

[![](background-tasks-images/update14.png "Scénario d’utilisation classique")](background-tasks-images/update14.png#lightbox)

L’équipe de football préférée de l’utilisateur est en train de trouver une grande correspondance entre 7:00 h et 9:00 PM. l’application doit donc s’attendre à ce que l’utilisateur vérifie régulièrement le score et décide d’un intervalle de mise à jour de 30 minutes.

1. L’utilisateur ouvre l’application et planifie une tâche pour la mise à jour en arrière-plan 30 minutes plus tard. L’API d’arrière-plan autorise l’exécution d’un seul type de tâche en arrière-plan à un moment donné.
2. L’application reçoit la tâche et met à jour ses données et l’interface utilisateur, puis planifie une autre tâche en arrière-plan 30 minutes plus tard. Il est important que le développeur se souvient de la planification d’une autre tâche en arrière-plan, ou que l’application ne soit jamais activé pour obtenir des mises à jour supplémentaires.
3. Là encore, l’application reçoit la tâche et met à jour ses données, met à jour son interface utilisateur et planifie une autre tâche en arrière-plan 30 minutes plus tard.
4. Le même processus se répète à nouveau.
5. La dernière tâche d’arrière-plan est reçue et l’application met à jour ses données et son interface utilisateur. Puisqu’il s’agit du score final qu’il ne planifie pas pour une nouvelle actualisation en arrière-plan. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Planification de la mise à jour en arrière-plan

Étant donné le scénario ci-dessus, l’application MonkeySoccer peut utiliser le code suivant pour planifier une mise à jour en arrière-plan :

```csharp
private void ScheduleNextBackgroundUpdate ()
{
  // Create a fire date 30 minutes into the future
  var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

  // Create 
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
  userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

Il crée `NSDate` 30 minutes dans le futur lorsque l’application souhaite être activé et crée un `NSMutableDictionary` pour contenir les détails de la tâche demandée. La `ScheduleBackgroundRefresh` méthode`SharedExtension` de est utilisée pour demander la planification de la tâche.

Le système renverra un `NSError` s’il n’a pas pu planifier la tâche demandée.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Traitement de la mise à jour

Ensuite, examinez de plus près la fenêtre de 5 minutes pour afficher les étapes nécessaires à la mise à jour du score :

[![](background-tasks-images/update15.png "La fenêtre 5 minutes présentant les étapes nécessaires pour mettre à jour le score")](background-tasks-images/update15.png#lightbox)

1. À 7:30:02, l’application est réveillée par le système et reçoit la tâche d’arrière-plan de mise à jour. Sa première priorité consiste à récupérer les scores les plus récents à partir du serveur. Voir [planification d’un passer](#Scheduling-a-NSUrlSession) ci-dessous.
2. À 7:30:05, l’application termine la tâche d’origine, le système met l’application en veille et continue à télécharger les données demandées en arrière-plan.
3. Une fois le téléchargement terminé, le système crée une tâche pour réveiller l’application afin de pouvoir traiter les informations téléchargées. Consultez [gestion des tâches en arrière-plan](#Handling-Background-Tasks) et [gestion du téléchargement](#Handling-the-Download-Completing) ci-dessous. 
4. L’application enregistre les informations mises à jour et marque la tâche comme terminée. Le développeur peut être tenté de mettre à jour l’interface utilisateur de l’application à ce stade, mais Apple suggère de planifier une tâche de capture instantanée pour gérer ce processus. Consultez [planification d’une mise à jour d’instantané](#Scheduling-a-Snapshot-Update) ci-dessous.
5. L’application reçoit la tâche d’instantané, met à jour son interface utilisateur et marque la tâche comme terminée. Consultez [gestion d’une mise à jour d’instantané](#Handling-a-Snapshot-Update) ci-dessous.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Planification d’un passer

Le code suivant peut être utilisé pour planifier le téléchargement des scores les plus récents :

```csharp
private void ScheduleURLUpdateSession ()
{
  // Create new configuration
  var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

  // Create new session
  var backgroundSession = NSUrlSession.FromConfiguration (configuration);

  // Create and start download task
  var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
  downloadTask.Resume ();
}
```

Il configure et crée un nouveau `NSUrlSession`, puis utilise cette session pour créer une tâche de téléchargement à l’aide de la `CreateDownloadTask` méthode. Elle appelle la `Resume` méthode de la tâche de téléchargement pour démarrer la session.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Gestion des tâches en arrière-plan

En substituant la `HandleBackgroundTasks` méthode `WKExtensionDelegate`de, l’application peut gérer les tâches en arrière-plan entrantes :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
    #endregion

    ...
    
    #region Public Methods
    public void CompleteTask (WKRefreshBackgroundTask task)
    {
      // Mark the task completed and remove from the collection
      task.SetTaskCompleted ();
      PendingTasks.Remove (task);
    }
    #endregion 

    #region Override Methods
    public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
    {
      // Handle background request
      foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Is this a background session task?
        var urlTask = task as WKUrlSessionRefreshBackgroundTask;
        if (urlTask != null) {
          // Create new configuration
          var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

          // Create new session
          var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

          // Keep track of all pending tasks
          PendingTasks.Add (task);
        } else {
          // Ensure that all tasks are completed
          task.SetTaskCompleted ();
        }
      }
    }
    #endregion
    
    ...
  }
}
```

La `HandleBackgroundTasks` méthode parcourt toutes les tâches que le système a envoyé à l’application ( `backgroundTasks`dans) en recherchant `WKUrlSessionRefreshBackgroundTask`un. Si l’un d’eux est trouvé, il rejoint la session et joint un `NSUrlSessionDownloadDelegate` pour gérer le téléchargement en cours (voir [gestion du téléchargement terminé](#Handling-the-Download-Completing) ci-dessous) :

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Il garde un handle sur la tâche jusqu’à ce qu’elle se termine en l’ajoutant à une collection :

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Toutes les tâches envoyées à l’application doivent être terminées, pour toutes les tâches qui ne sont pas en cours de traitement, marquez-la comme terminée :

```csharp
if (urlTask != null) {
  ...
} else {
  // Ensure that all tasks are completed
  task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Traitement du téléchargement terminé

L’application MonkeySoccer utilise le délégué `NSUrlSessionDownloadDelegate` suivant pour gérer le téléchargement terminé et traiter les données demandées :

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
  public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
  {
    #region Computed Properties
    public ExtensionDelegate WatchExtensionDelegate { get; set; }

    public WKRefreshBackgroundTask Task { get; set; }
    #endregion

    #region Constructors
    public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
    {
      // Initialize
      this.WatchExtensionDelegate = extensionDelegate;
      this.Task = task;
    }
    #endregion

    #region Override Methods
    public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
    {
      // Handle the downloaded data
      ...

      // Mark the task completed
      WatchExtensionDelegate.CompleteTask (Task);

    }
    #endregion
  }
}
```

Lorsqu’il est initialisé, il conserve un handle à la `ExtensionDelegate` fois à `WKRefreshBackgroundTask` et à qui l’a généré. Elle remplace la `DidFinishDownloading` méthode pour gérer le téléchargement terminé. Utilise ensuite la `CompleteTask` méthode `ExtensionDelegate` du pour informer la tâche qu’elle est terminée et la supprimer de la collection de tâches en attente. Consultez [gestion des tâches en arrière-plan](#Handling-Background-Tasks) ci-dessus.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Planification d’une mise à jour d’instantané

Le code suivant peut être utilisé pour planifier une tâche de capture instantanée afin de mettre à jour l’interface utilisateur avec les scores les plus récents :

```csharp
private void ScheduleSnapshotUpdate ()
{
  // Create a fire date of now
  var fireDate = NSDate.FromTimeIntervalSinceNow (0);

  // Create user info dictionary
  var userInfo = new NSMutableDictionary ();
  userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
  userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

  // Schedule for update
  WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
    // Was the Task successfully scheduled?
    if (error == null) {
      // Yes, handle if needed
    } else {
      // No, report error
    }
  });
}
```

Tout comme `ScheduleURLUpdateSession` la méthode ci-dessus, elle `NSDate` crée un pour lorsque l’application souhaite être activé et crée `NSMutableDictionary` un pour contenir les détails de la tâche demandée. La `ScheduleSnapshotRefresh` méthode`SharedExtension` de est utilisée pour demander la planification de la tâche.

Le système renverra un `NSError` s’il n’a pas pu planifier la tâche demandée.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Gestion d’une mise à jour d’instantané

Pour gérer la tâche d’instantané, `HandleBackgroundTasks` la méthode (voir [gestion des tâches en arrière-plan](#Handling-Background-Tasks) ci-dessus) est modifiée de façon à ressembler à ce qui suit :

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
  // Handle background request
  foreach (WKRefreshBackgroundTask task in backgroundTasks) {
    // Take action based on task type
    if (task is WKUrlSessionRefreshBackgroundTask) {
      var urlTask = task as WKUrlSessionRefreshBackgroundTask;

      // Create new configuration
      var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

      // Create new session
      var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

      // Keep track of all pending tasks
      PendingTasks.Add (task);
    } else if (task is WKSnapshotRefreshBackgroundTask) {
      var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

      // Update UI
      ...

      // Create a expiration date 30 minutes into the future
      var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

      // Create user info dictionary
      var userInfo = new NSMutableDictionary ();
      userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
      userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

      // Mark task complete
      snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
    } else {
      // Ensure that all tasks are completed
      task.SetTaskCompleted ();
    }
  }
}
```

La méthode teste le type de tâche en cours de traitement. S’il s’agit `WKSnapshotRefreshBackgroundTask` d’un service informatique, il obtient l’accès à la tâche :

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

La méthode met à jour l’interface utilisateur, puis crée `NSDate` un pour indiquer au système quand l’instantané est périmé. Il crée un `NSMutableDictionary` avec des informations utilisateur pour décrire le nouvel instantané et marque la tâche d’instantané comme terminée avec les informations suivantes :

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

En outre, il indique également à la tâche d’instantané que l’application n’est pas retournée à l’État par défaut (dans le premier paramètre). Les applications qui n’ont pas de concept d’État par défaut doivent toujours définir `true`cette propriété sur.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Travailler efficacement

Comme indiqué dans l’exemple ci-dessus de la fenêtre de cinq minutes utilisée par l’application MonkeySoccer pour mettre à jour ses scores, en travaillant efficacement et en utilisant les nouvelles tâches d’arrière-plan Watchos 3, l’application n’était active que pendant un total de 15 secondes : 

[![](background-tasks-images/update16.png "L’application n’était active que pendant un total de 15 secondes")](background-tasks-images/update16.png#lightbox)

Cela réduit l’impact que l’application aura sur les ressources disponibles Apple Watch et la durée de vie de la batterie, et permet également à l’application de fonctionner mieux avec d’autres applications qui s’exécutent sur la montre.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Fonctionnement de la planification

Alors qu’une application Watchos 3 se trouve au premier plan, elle est toujours planifiée pour s’exécuter et peut effectuer tout type de traitement requis, par exemple mettre à jour des données ou redessiner son interface utilisateur. Lorsque l’application passe en arrière-plan, elle est généralement suspendue par le système et toutes les opérations d’exécution sont arrêtées. 

Alors que l’application est en arrière-plan, elle peut être ciblée par le système pour exécuter rapidement une tâche spécifique. Ainsi, dans Watchos 2, le système peut réveiller temporairement une application en arrière-plan pour effectuer des opérations telles que la gestion d’une longue notification ou la mise à jour de la complication de l’application. Dans Watchos 3, il existe plusieurs nouvelles façons d’exécuter une application en arrière-plan.

Lorsqu’une application est en arrière-plan, le système impose plusieurs limites à celle-ci :

- Seules quelques secondes sont fournies pour effectuer une tâche donnée. Le système prend en compte non seulement la durée écoulée, mais aussi la puissance de l’UC consommée par l’application pour dériver cette limite.
- Toute application dépassant ses limites sera supprimée avec les codes d’erreur suivants :
  - **UC** -0xc51bad01
  - **Heure** -0xc51bad02
- Le système impose des limites différentes en fonction du type de tâche d’arrière-plan qu’il a demandé à l’application d’effectuer. Par exemple, `WKApplicationRefreshBackgroundTask` les `WKURLSessionRefreshBackgroundTask` tâches et sont des runtimes légèrement plus longs que sur d’autres types de tâches en arrière-plan.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complications et mises à jour d’application

Outre les nouvelles tâches en arrière-plan que Apple a ajoutées à Watchos 3, les complications de l’application Watchos peuvent avoir un impact sur la manière et le moment où l’application reçoit les mises à jour en arrière-plan.

Les complications sont de petits éléments visuels qui fournissent des informations utiles en un clin d’œil. En fonction de la facette de montre sélectionnée, l’utilisateur a la possibilité de personnaliser un visage de montre avec une ou plusieurs complications qui peuvent être fournies par une application Watch dans Watchos 3.

Si l’utilisateur inclut l’une des complications de l’application sur le visage de la montre, il offre les avantages suivants à l’application :

- Cela amène le système à conserver l’application dans un état prêt à être lancé, où elle tente de lancer l’application en arrière-plan, de la conserver en mémoire et de la mettre à jour plus tard.
- Les complications sont garanties au moins 50 mises à jour push par jour.

Le développeur doit toujours s’efforcer de créer des complications attrayantes pour leurs applications afin d’inciter l’utilisateur à les ajouter au visage de la montre pour les raisons indiquées ci-dessus.

Dans Watchos 2, les complications étaient la principale façon dont une application recevait le runtime en arrière-plan. Dans Watchos 3, une application de complication est toujours assurée de recevoir plusieurs mises à jour par heure. Toutefois, elle peut utiliser `WKExtensions` pour demander plus d’exécution pour mettre à jour ses complications.

Jetez un coup d’œil au code suivant utilisé pour mettre à jour la complication à partir de l’application iPhone connectée :

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

  // Get session and the number of remaining transfers
  var session = WCSession.DefaultSession;
  var transfers = session.RemainingComplicationUserInfoTransfers;

  // Create user info dictionary
  var iconattrs = new Dictionary<NSString, NSObject>
    {
      {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
      {new NSString ("reason"), new NSString ("UpdateScore")}
    };

  var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

  // Take action based on the number of transfers left
  if (transfers < 1) {
    // No transfers left, either attempt to send or inform
    // user of situation.
    ...
  } else if (transfers < 11) {
    // Running low on transfers, only send on important updates
    // else conserve for a significant change.
    ...
  } else {
    // Send data
    session.TransferCurrentComplicationUserInfo (userInfo);
  }
}
```

Elle utilise la `RemainingComplicationUserInfoTransfers` propriété `WCSession` de pour voir le nombre de transferts à priorité élevée laissés par l’application pour la journée, puis entreprend une action en fonction de ce nombre. Si l’application commence à s’exécuter à des transferts insuffisants, elle peut être désactivée pour envoyer des mises à jour mineures et envoyer des informations uniquement en cas de modification importante.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Planification et station d’accueil

Dans Watchos 3, Apple a ajouté la station d’accueil dans laquelle les utilisateurs peuvent épingler leurs applications favorites et y accéder rapidement. Quand l’utilisateur appuie sur le bouton latéral sur la Apple Watch, une galerie d’instantanés d’application épinglés s’affiche. L’utilisateur peut balayer vers la gauche ou la droite pour trouver l’application souhaitée, puis appuyer sur l’application pour la lancer et remplacer l’instantané par l’interface de l’application en cours d’exécution.

[![](background-tasks-images/dock01.png "La station d’accueil")](background-tasks-images/dock01.png#lightbox)

Le système prend régulièrement des instantanés de l’interface utilisateur de l’application et utilise ces instantanés pour remplir les documents. Watchos donne à l’application la possibilité de mettre à jour son contenu et son interface utilisateur avant que cet instantané ne soit pris.

Les applications qui ont été épinglées à la station d’accueil peuvent s’attendre à ce qui suit :

- Ils recevront un minimum de 1 jour par heure. Cela comprend à la fois une tâche d’actualisation d’application et une tâche d’instantané.
- Le budget des mises à jour est distribué entre toutes les applications du Dock. Le moins d’applications que l’utilisateur a épinglées est donc le nombre de mises à jour potentielles reçues par chaque application.
- L’application est conservée en mémoire pour que l’application reprenne rapidement lorsqu’elle est sélectionnée à partir de la station d’accueil.

La dernière application exécutée par l’utilisateur sera considérée comme l’application la _plus récemment utilisée_ et occupera le dernier emplacement du Dock. À partir de là, les utilisateurs peuvent choisir de les épingler de manière permanente à la station d’accueil. La dernière utilisation est traitée comme toute autre application favorite que l’utilisateur a déjà épinglée à la station d’accueil.

> [!IMPORTANT]
> Les applications qui n’ont été ajoutées à l’écran d’accueil ne recevront pas de planifications régulières. Pour recevoir des mises à jour régulières de planification et d’arrière-plan, une application _doit_ être ajoutée à la station d’accueil.

Comme indiqué plus haut dans ce document, les instantanés sont très importants dans Watchos 3, car ils fonctionnent à la fois comme images d’aperçu et de lancement pour l’application. Si l’utilisateur règle sur une application dans le Dock, il s’agrandit en plein écran, entre le premier plan et démarre, il est donc impératif que l’instantané soit à jour.

Il peut arriver que le système décide qu’il a besoin d’un nouvel instantané de l’interface utilisateur de l’application. Dans ce cas, la demande d’instantané n’est pas prise en compte sur le budget du runtime de l’application. La commande suivante déclenche une requête d’instantané système :

- Mise à jour de la chronologie de complication.
- Interaction de l’utilisateur avec la notification d’une application.
- Passage du premier plan à l’état d’arrière-plan.
- Après une heure d’exécution de l’état d’arrière-plan, l’application peut revenir à l’État par défaut.
- Lors du premier démarrage de Watchos.

<a name="Best-Practices" />

## <a name="best-practices"></a>Meilleures pratiques 

Apple suggère les meilleures pratiques suivantes lorsque vous travaillez avec des tâches en arrière-plan :

- Planifiez aussi souvent que l’application doit être mise à jour. Chaque fois que l’application s’exécute, elle doit réévaluer ses futurs besoins et ajuster cette planification en fonction des besoins.
- Si le système envoie une tâche d’actualisation en arrière-plan et que l’application n’a pas besoin d’une mise à jour, différez le travail jusqu’à ce qu’une mise à jour soit réellement nécessaire.
- Tenez compte de toutes les opportunités d’exécution disponibles pour une application :
  - Activation de l’ancrage et du premier plan.
  - Fonctionnalité.
  - Mises à jour de la complication.
  - Actualisations en arrière-plan.
- Utilisez `ScheduleBackgroundRefresh` pour le runtime d’arrière-plan à usage général, par exemple :
  - Interrogation du système pour obtenir des informations.
  - Planifiez `NSURLSessions` demain pour demander des données en arrière-plan. 
  - Transitions de temps connues.
  - Déclenchement des mises à jour de complication.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Meilleures pratiques pour les instantanés

Lorsque vous utilisez des mises à jour d’instantanés, Apple effectue les suggestions suivantes :

- Invalidez des instantanés uniquement lorsque cela est nécessaire, par exemple, en cas de modification importante du contenu.
- Évitez l’invalidation d’instantanés à fréquence élevée. Par exemple, une application de minuteur ne doit pas mettre à jour l’instantané toutes les secondes, mais uniquement lorsque la minuterie est terminée.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Workflow de données d’application

Apple suggère les éléments suivants pour l’utilisation d’un Data Flow :

[![](background-tasks-images/update17.png "Diagramme de workflow de données d’application")](background-tasks-images/update17.png#lightbox)

Un événement externe (par exemple, Watch Connectivity) réveille l’application. Cela force l’application à mettre à jour son modèle de données (qui représente l’état actuel des applications). Suite à la modification du modèle de données, l’application doit mettre à jour ses complications, demander un nouvel instantané, éventuellement démarrer un `NSURLSession` arrière-plan pour extraire plus de données et planifier d’autres actualisations en arrière-plan.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Cycle de vie de l’application

En raison de la station d’accueil et de la possibilité d’épingler des applications favorites au service informatique, Apple pense que les utilisateurs se déplaceront entre beaucoup plus d’applications, bien plus souvent, alors ils l’ont fait avec Watchos 2. Par conséquent, l’application doit être prête à gérer cette modification et se déplacer rapidement entre les États de premier plan et d’arrière-plan.

Apple présente les suggestions suivantes :

- Assurez-vous que l’application termine toute tâche en arrière-plan dès que possible lors de la saisie de l’activation de premier plan.
- Veillez à terminer tout le travail de premier plan avant d' `NSProcessInfo.PerformExpiringActivity`entrer l’arrière-plan en appelant.
- Lors du test d’une application dans le simulateur Watchos, aucun des budgets de tâche ne sera appliqué pour qu’une application puisse s’actualiser autant que nécessaire pour tester correctement une fonctionnalité.
- Testez toujours sur le matériel Apple Watch réel pour vous assurer que l’application n’est pas en cours d’exécution au-delà de ses budgets avant la publication sur iTunes Connect.
- Apple suggère de conserver les Apple Watch sur le chargeur pendant le test et le débogage.
- Assurez-vous que le lancement et la reprise d’une application sont minutieusement testés.
- Vérifiez que toutes les tâches de l’application sont terminées.
- Faites varier le nombre d’applications épinglées dans le Dock pour tester les scénarios les plus optimistes et les plus pessimistes.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé les améliorations apportées par Apple et la façon dont elles peuvent être utilisées pour tenir à jour une application de surveillance. Tout d’abord, la nouvelle tâche en arrière-plan Apple a été ajoutée à Watchos 3. Il a ensuite abordé le cycle de vie de l’API en arrière-plan et comment implémenter des tâches en arrière-plan dans une application Xamarin Watchos. Enfin, il a abordé le fonctionnement de la planification et a donné quelques meilleures pratiques.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
