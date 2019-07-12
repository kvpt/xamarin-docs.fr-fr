---
title: watchOS les tâches en arrière-plan dans Xamarin
description: Ce document décrit comment utiliser des tâches en arrière-plan avec watchOS dans Xamarin, jeter un coup de œil sur les types de tâches en arrière-plan, à l’aide de ressources, implémentation des tâches en arrière-plan, la planification, meilleures pratiques et bien plus encore.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: e28ba19fdc972b962f0dcd2757f1ba9087ac5c27
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831778"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS les tâches en arrière-plan dans Xamarin

Avec watchOS 3, il existe trois méthodes principales qu’une application watch peut maintenir ses informations à jour : 

- À l’aide d’une des nouvelles plusieurs tâches en arrière-plan. 
- À un des ses Complications sur le cadran de montre (ce qui donne il temps supplémentaire pour mettre à jour). 
- Avoir le code confidentiel utilisateur pour l’application à l’écran d’ancrage (où ses conservés en mémoire et souvent mises à jour). 

## <a name="keeping-an-app-up-to-date"></a>Mise à jour une application permanente

Avant d’aborder toutes les méthodes, qu'un développeur peut conserver les données et l’Interface utilisateur d’une application watchOS en cours et mis à jour, cette section prendra un coup de œil à un ensemble typique de modèles d’utilisation et comment un utilisateur peut se déplacer entre leur iPhone et leurs Apple Watch pendant la journée en fonction de  l’heure du jour et l’activité ils actuellement font (par exemple, la commande).

Prenons l’exemple suivant :

[![](background-tasks-images/update00.png "Comment un utilisateur peut se déplacer entre leur iPhone et leurs Apple Watch pendant la journée")](background-tasks-images/update00.png#lightbox)

1. Dans la matinée, tout en un café, l’utilisateur parcourt les actualités sur leur iPhone pendant plusieurs minutes.
2. Avant de quitter le café, ils vérifient rapidement la météo avec une Complication sur leur cadran de montre.
3. Avant le déjeuner, ils utilisent l’application Maps sur l’iPhone pour trouver un restaurant à proximité et d’une réservation pour répondre à un client.
4. Lors de déplacements au restaurant, ils obtiennent une notification sur leur Apple Watch et avec un rapide coup de œil, qu’ils connaissent leur rendez-vous déjeuner est en retard.
5. Le soir, il l’utilise les mappages sur l’iPhone pour vérifier le trafic avant d’accueil de conduite.
6. Sur la façon dont domestique, ils reçoivent une notification iMessage sur leurs Apple Watch leur demandant à assimiler certains lait, ceux-ci utilisent la fonctionnalité de réponse rapide pour envoyer la réponse « OK ».

En raison de le « aperçu » (moins de trois secondes) la nature de la façon dont un utilisateur souhaite utilisez généralement une application Apple Watch, il n’est pas suffisamment de temps pour l’application extraire les informations souhaitées et mettre à jour son interface utilisateur avant de les afficher à l’utilisateur.

À l’aide de la nouvelle API Apple a inclus dans watchOS 3, l’application peut planifier une _actualisation en arrière-plan_ et avoir les informations voulues prêtes avant que l’utilisateur le demande. Prenons l’exemple de la Complication météo décrits ci-dessus :

[![](background-tasks-images/update01.png "Un exemple de la Complication météo")](background-tasks-images/update01.png#lightbox)

1. Les planifications de l’application d’être réveillés par le système à un moment donné. 
2. L’application extrait les informations que vous devez pour générer la mise à jour.
3. L’application régénère son Interface utilisateur pour refléter les nouvelles données.
4. Lorsque l’utilisateur coup à Complication de l’application, il dispose des informations à jour sans que l’utilisateur de devoir patienter pendant la mise à jour.

Comme indiqué ci-dessus, le système watchOS sort de l’application à l’aide d’une ou plusieurs tâches, dont il dispose d’un pool très limité disponible :

[![](background-tasks-images/update02.png "Le système watchOS sort de l’application à l’aide d’une ou plusieurs tâches")](background-tasks-images/update02.png#lightbox)

Apple vous suggérons de tirer le meilleur de cette tâche (dans la mesure où il s’agit de ce type d’une ressource limitée à l’application) en maintenant sur celui-ci jusqu'à ce que l’application a terminé le processus de mise à jour lui-même.

Le système propose ces services tâches en appelant la nouvelle `HandleBackgroundTasks` méthode de la `WKExtensionDelegate` déléguer. Par exemple :

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

Lorsque l’application a terminé la tâche donnée, elle le retourne au système en le marquant terminée :

[![](background-tasks-images/update03.png "La tâche retourne au système en le marquant terminée")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nouvelles tâches en arrière-plan

watchOS 3 introduit plusieurs tâches en arrière-plan qui une application peut utiliser pour mettre à jour ses informations en vous assurant qu’il a le contenu de l’utilisateur a besoin avant de l’ouvrir l’application, telles que :

- **Actualisation de l’application d’arrière-plan** - le [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) tâche autorise l’application à mettre à jour son état en arrière-plan. En général, cela inclut une autre tâche, telles que le téléchargement de nouveau contenu à partir d’internet en utilisant un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Actualisation de la capture instantanée d’arrière-plan** - le [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) tâche autorise l’application à mettre à jour son contenu et l’interface utilisateur avant que le système prend un instantané qui sera utilisé pour remplir l’écran d’ancrage.
- **En arrière-plan espion connectivité** - le [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) tâche est démarrée pour l’application lorsqu’il reçoit les données d’arrière-plan de l’iPhone apparié.
- **Session de l’URL d’arrière-plan** - le [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) tâche est démarrée pour l’application lorsqu’un transfert en arrière-plan nécessite une autorisation ou qu’il se termine (correctement ou erreur).

Ces tâches seront abordées en détail dans les sections ci-dessous.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

Le `WKApplicationRefreshBackgroundTask` est une tâche générique qui peut être planifiée pour que l’application sortie à une date ultérieure :

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask sorti à une date ultérieure")](background-tasks-images/update04.png#lightbox)

Au sein de l’exécution de la tâche, l’application peut effectuer tout type de traitement local, telles que mise à jour une chronologie Complication ou extraire des données requises avec une `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

Le système enverra un `WKURLSessionRefreshBackgroundTask` lorsque les données a terminé de télécharger et prêtes à être traitées par l’application :

[![](background-tasks-images/update05.png "Le WKURLSessionRefreshBackgroundTask lorsque les données de téléchargement sont terminé")](background-tasks-images/update05.png#lightbox)

Une application ne reste pas en cours d’exécution pendant le téléchargement de données en arrière-plan. Au lieu de cela, l’application planifie la demande de données, puis elle est suspendue et le système gère le téléchargement des données, reawakening uniquement l’application lorsque le téléchargement est terminé.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

Dans watchOS 3, Apple a ajouté la station d’accueil où les utilisateurs peuvent épingler leurs applications favorites et y accéder rapidement. Lorsque l’utilisateur appuie sur le bouton sur l’Apple Watch, une galerie de captures instantanées application épinglée s’affichera. L’utilisateur peut balayer gauche ou droite pour rechercher l’application souhaitée, puis appuyez sur l’application pour le lancer en remplaçant l’instantané avec l’interface de l’application en cours d’exécution.

[![](background-tasks-images/update06.png "Remplacement de l’instantané avec l’interface d’applications en cours d’exécution")](background-tasks-images/update06.png#lightbox)

Le système prend régulièrement des instantanés de l’interface utilisateur de l’application (en envoyant un `WKSnapshotRefreshBackgroundTask`) et utilise ces captures instantanées pour remplir l’écran d’ancrage. watchOS donne la possibilité de mettre à jour son contenu ni l’interface utilisateur avant que cet instantané est mis à l’application.

Captures instantanées sont très importantes dans watchOS 3 dans la mesure où ils fonctionnent en tant qu’images de la version préliminaire et le lancement de l’application. Si l’utilisateur se règle sur une application dans le Dock, il développez en plein écran, entrez le premier plan et commencer son exécution, il est donc impératif que l’instantané soit à jour :

[![](background-tasks-images/update07.png "Si l’utilisateur se règle sur une application dans le Dock, il va se développer en plein écran")](background-tasks-images/update07.png#lightbox)

Là encore, le système émet un `WKSnapshotRefreshBackgroundTask` afin de pouvoir préparer l’application (par la mise à jour les données et l’interface utilisateur) avant la capture instantanée :

[![](background-tasks-images/update08.png "L’application peut préparer en mettant à jour les données et l’interface utilisateur avant la capture instantanée")](background-tasks-images/update08.png#lightbox)

Lorsque l’application marque le `WKSnapshotRefreshBackgroundTask` terminé, le système mettra automatiquement un instantané de l’interface utilisateur de l’application.

> [!IMPORTANT]
> Il est important de toujours planifier un `WKSnapshotRefreshBackgroundTask` après l’application a reçu de nouvelles données et mis à jour son Interface utilisateur ou l’utilisateur ne voit pas les informations modifiées.




En outre, lorsque l’utilisateur reçoit une notification de l’application et appuie dessus afin de mettre l’application au premier plan, la capture instantanée doit être à jour dans la mesure où il s’agit en tant que l’écran d’installation :

[![](background-tasks-images/update09.png "L’utilisateur reçoit une notification de l’application et appuie dessus afin de mettre l’application au premier plan")](background-tasks-images/update09.png#lightbox)

S’il a été plus d’une heure dans la mesure où l’utilisateur interagisse avec une application watchOS, il sera en mesure de retourner à son état par défaut. L’état par défaut peut signifier différentes choses pour différentes applications et, en fonction de la conception d’une application, est peut-être pas un état par défaut du tout.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

Dans watchOS 3, Apple a intégré espion connectivité avec l’API d’actualisation en arrière-plan via le nouveau `WKWatchConnectivityRefreshBackgroundTask`. À l’aide de cette nouvelle fonctionnalité, une application iPhone peut fournir des données fraîches à son homologue d’application watch, l’application watchOS est en cours d’exécution en arrière-plan :

[![](background-tasks-images/update10.png "Une application iPhone peut fournir des données fraîches à son homologue d’application watch, l’application watchOS est en cours d’exécution en arrière-plan")](background-tasks-images/update10.png#lightbox)

Initialisez un Push de Complication, contexte d’application, envoi d’un fichier ou la mise à jour des informations sur l’utilisateur à partir de l’application iPhone sortira de veille l’application Apple Watch en arrière-plan.

Lorsque l’application watch est sortie un `WKWatchConnectivityRefreshBackgroundTask` il doit utiliser les méthodes d’API standards pour recevoir les données à partir de l’application iPhone.

[![](background-tasks-images/update11.png "Le flux de données WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Assurez-vous que la Session a activé.
2. Surveiller la nouvelle `HasContentPending` propriété tant que la valeur est `true`, l’application a toujours des données à traiter. Comme avant, l’application doit conserver la tâche jusqu'à ce qu’il a terminé le traitement de toutes les données.
3. Lorsqu’il n’existe plus aucune donnée à traiter (`HasContentPending = false`), marquez la tâche terminée pour rétablir le système. Dans le cas arriveront à runtime alloué d’arrière-plan de l’application résultant dans un rapport d’incidents.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Le cycle de vie des API en arrière-plan

Placer tous les éléments de la nouvelle API de tâches en arrière-plan, un ensemble typique d’interactions se présente comme suit :

[![](background-tasks-images/update12.png "Le cycle de vie des API en arrière-plan")](background-tasks-images/update12.png#lightbox)

1. Tout d’abord, l’application watchOS planifie un tâche pour être en éveil en tant qu’un moment donné dans le futur en arrière-plan.
2. L’application est sortie par le système et envoyée une tâche.
3. L’application traite la tâche pour effectuer le travail a été nécessaire.
4. Par conséquent de traitement de la tâche, l’application peut avoir besoin planifier des tâches à effectuer plus de travail à l’avenir, telles que le téléchargement de contenu plus à l’aide des informations complémentaires un `NSUrlSession`.
5. L’application marque la tâche terminée et le renvoie au système.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>À l’aide des ressources de façon responsable

Il est essentiel qu’une application watchOS se comporte de façon responsable au sein de cet écosystème en limitant son vidage sur des ressources partagées du système.

Examinons le scénario suivant :

[![](background-tasks-images/update13.png "Une application watchOS limite son vidage sur des ressources partagées du système")](background-tasks-images/update13.png#lightbox)

1. L’utilisateur lance une application watchOS à 13 h 00.
2. L’application planifie une tâche à sortir de veille et de télécharger de nouveaux contenus dans une heure à 2 h 00.
3. À 13 h 50 l’utilisateur rouvre l’application qui lui permet de mettre à jour ses données et l’interface utilisateur pour l’instant.
4. Au lieu de laisser la mise en éveil tâche l’application dans 10 minutes, l’application doit replanifier la tâche pour exécuter une heure plus tard à 2 h 50.

Bien que chaque application soit différente, Apple suggère de trouver des modèles d’utilisation, comme ceux indiqués ci-dessus, vous pouvez économiser des ressources système.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implémentation des tâches en arrière-plan

Pour cet exemple, ce document utilisera l’application de sports MonkeySoccer factice qui signale les scores de football à l’utilisateur. 

Examinons le scénario d’utilisation typique suivant :

[![](background-tasks-images/update14.png "Le scénario d’utilisation classique")](background-tasks-images/update14.png#lightbox)

Équipe de football Favoris de l’utilisateur joue une correspondance big à partir de 7 h 00 à 9:00 PM afin que l’application doit attendre l’utilisateur à vérifier régulièrement le score et elle décide d’un intervalle de mise à jour de 30 minutes.

1. L’utilisateur ouvre l’application et il planifie une tâche de mise à jour d’arrière-plan de 30 minutes plus tard. L’API d’arrière-plan ne permet qu’un seul type de tâche à exécuter à un moment donné d’arrière-plan.
2. L’application reçoit la tâche et met à jour ses données et l’interface utilisateur, puis d’arrière-plan des planifications pour une autre tâche 30 minutes plus tard. Il est important que le développeur se souvient planifier l’arrière-plan d’une autre tâche, ou l’application n’est jamais ré-activée pour obtenir d’autres mises à jour.
3. Là encore, l’application reçoit la tâche et met à jour ses données, met à jour son interface utilisateur et planifie un autre arrière-plan tâche 30 minutes plus tard.
4. Le même processus se répète à nouveau.
5. Arrière-plan de la dernière tâche est reçue et l’application à nouveau des mises à jour ses données et l’interface utilisateur. Dans la mesure où il s’agit le score final, qu'il ne planifier pour une nouvelle actualisation en arrière-plan. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Planification de mise à jour en arrière-plan

Étant donné le scénario ci-dessus, l’application MonkeySoccer pouvez utiliser le code suivant pour planifier une mise à jour en arrière-plan :

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

Il crée un nouveau `NSDate` 30 minutes dans le futures quand l’application souhaite être activé et crée un `NSMutableDictionary` pour contenir les détails de la tâche demandée. Le `ScheduleBackgroundRefresh` méthode de la `SharedExtension` est utilisé pour demander la tâche planifiée.

Le système retourne un `NSError` si elle n’a pas pu planifier la tâche demandée.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Traitement de la mise à jour

Ensuite, examinons plus en détail la fenêtre de 5 minutes montrant les étapes nécessaires pour mettre à jour le score :

[![](background-tasks-images/update15.png "La fenêtre de 5 minutes montrant les étapes nécessaires pour mettre à jour le score")](background-tasks-images/update15.png#lightbox)

1. À 7 h 30:02 mis en éveil par le système et de l’application étant donnée la tâche d’arrière-plan de mise à jour. Sa première priorité consiste à obtenir les derniers résultats à partir du serveur. Consultez [planification une NSUrlSession](#Scheduling-a-NSUrlSession) ci-dessous.
2. À 7 h 30:05, l’application termine la tâche d’origine, le système met l’application en veille et qu’il continue à télécharger les données demandées en arrière-plan.
3. Lorsque le système a terminé le téléchargement, il crée une nouvelle tâche afin de sortir de l’application afin de pouvoir traiter les informations téléchargées. Consultez [gestion des tâches en arrière-plan](#Handling-Background-Tasks) et [gère la fin du téléchargement](#Handling-the-Download-Completing) ci-dessous. 
4. L’application enregistre les informations de mise à jour et marque la tâche terminée. Le développeur peut-être être tenté de mettre à jour l’Interface utilisateur de l’application à ce stade, Apple suggère de planification d’une tâche de capture instantanée pour gérer ce processus. Consultez [planification d’une mise à jour de l’instantané](#Scheduling-a-Snapshot-Update) ci-dessous.
5. L’application reçoit la tâche d’instantané, met à jour son Interface utilisateur et marque la tâche terminée. Consultez [une mise à jour de l’instantané de la gestion des](#Handling-a-Snapshot-Update) ci-dessous.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Planification d’une NSUrlSession

Le code suivant peut être utilisé pour planifier le téléchargement des derniers résultats :

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

Il configure et crée un nouveau `NSUrlSession`, puis utilise cette session pour créer un nouveau téléchargement de tâches à l’aide de la `CreateDownloadTask` (méthode). Il appelle le `Resume` méthode de la tâche pour démarrer la session de téléchargement.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Gestion des tâches d’arrière-plan

En substituant le `HandleBackgroundTasks` méthode de la `WKExtensionDelegate`, l’application peut gérer les tâches en arrière-plan entrant :

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

Le `HandleBackgroundTasks` méthode parcourt toutes les tâches que le système a envoyé à l’application (dans `backgroundTasks`) recherchant une `WKUrlSessionRefreshBackgroundTask`. S’il existe, il rejoint la session et attache un `NSUrlSessionDownloadDelegate` pour gérer la fin du téléchargement (consultez [le télécharger à la fin de la gestion des](#Handling-the-Download-Completing) ci-dessous) :

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Il conserve un pointeur sur la tâche jusqu'à son achèvement en l’ajoutant à une collection :

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Toutes les tâches envoyées à l’application doivent être terminée, pour n’importe quelle tâche non gérée, marquez-la terminée :

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Gestion de la fin du téléchargement

L’application MonkeySoccer utilise les éléments suivants `NSUrlSessionDownloadDelegate` délégué pour gérer la fin du téléchargement et de traiter les données demandées :

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

Lors de l’initialisation, il conserve un pointeur à la fois à la `ExtensionDelegate` et le `WKRefreshBackgroundTask` qui en généré dynamiquement. Ce paramètre remplace le `DidFinishDownloading` méthode pour gérer le téléchargement terminé. Utilise ensuite le `CompleteTask` méthode de la `ExtensionDelegate` pour informer la tâche qu’il a terminé et le supprime de la collection de tâches en attente. Consultez [gestion des tâches en arrière-plan](#Handling-Background-Tasks) ci-dessus.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Planification d’une mise à jour de l’instantané

Le code suivant peut être utilisé pour planifier une tâche de capture instantanée pour mettre à jour l’interface utilisateur avec les derniers résultats :

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

Tout comme `ScheduleURLUpdateSession` méthode ci-dessus, il crée un `NSDate` lorsque l’application souhaite être activé et crée un `NSMutableDictionary` pour contenir les détails de la tâche demandée. Le `ScheduleSnapshotRefresh` méthode de la `SharedExtension` est utilisé pour demander la tâche planifiée.

Le système retourne un `NSError` si elle n’a pas pu planifier la tâche demandée.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Gestion d’une mise à jour de l’instantané

Pour gérer la tâche d’instantané, le `HandleBackgroundTasks` (méthode) (consultez [gère les tâches en arrière-plan](#Handling-Background-Tasks) ci-dessus) est modifiée pour ressembler à ceci :

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

La méthode teste le type de tâche en cours de traitement. S’il s’agit une `WKSnapshotRefreshBackgroundTask` accède à la tâche :

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

La méthode met à jour de l’Interface utilisateur, puis crée un `NSDate` pour indiquer au système quand l’instantané sera obsolète. Il crée un `NSMutableDictionary` avec les informations utilisateur pour décrire la nouvelle capture instantanée et la marque la tâche d’instantané s’est terminée avec ces informations :

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

En outre, il indique également la tâche d’instantané que l’application ne revient pas à l’état par défaut (dans le premier paramètre). Les applications qui n’ont aucun concept d’un état par défaut doivent toujours définir cette propriété `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Fonctionne efficacement

Comme dans l’exemple ci-dessus de la fenêtre de cinq minutes nécessaire à l’application MonkeySoccer pour mettre à jour de son scores, à travailler efficacement et à l’aide de la nouvelle watchOS 3 tâches en arrière-plan, l’application était uniquement active pour un total de 15 secondes : 

[![](background-tasks-images/update16.png "L’application a uniquement été active pour un total de 15 secondes")](background-tasks-images/update16.png#lightbox)

Cela réduit l’impact de l’application aura sur les ressources de l’Apple Watch disponibles et autonomie et autorise également l’application fonctionne mieux avec d’autres applications s’exécutant sur la surveillance.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Fonctionne de la planification

Bien qu’une application watchOS 3 soit au premier plan, il est toujours planifié pour exécuter et peut effectuer n’importe quel type de traitement requis telles que les données mises à jour ou renouveler son interface utilisateur. Lorsque l’application se déplace dans l’arrière-plan, il est généralement interrompue par le système et toutes les opérations d’exécution sont interrompues. 

Lorsque l’application est en arrière-plan, il peut être ciblé par le système pour exécuter rapidement une tâche spécifique. Par conséquent, watchOS 2, le système peut temporairement veille une application d’arrière-plan pour effectuer des opérations comme la gestion de la notification de look long ou mettre à jour de Complication l’application. Dans watchOS 3, il existe plusieurs façons de nouveau une application peut être exécutée en arrière-plan.

Lorsqu’une application est en arrière-plan, le système impose des limites de plusieurs dessus :

- Il ne reçoit que quelques secondes pour terminer une tâche donnée. Le système prend en considération non seulement la quantité de temps passé, mais également la quantité d’énergie du processeur l’application consomme pour dériver de cette limite.
- N’importe quelle application qui dépasse la limite est supprimée avec les codes d’erreur suivants :
    - **Processeur** -0xc51bad01
    - **Temps** -0xc51bad02
- Le système sera imposent des limites différentes en fonction du type de tâche en arrière-plan a demandé à l’application à effectuer. Par exemple, `WKApplicationRefreshBackgroundTask` et `WKURLSessionRefreshBackgroundTask` tâches bénéficient des runtimes légèrement plus longues par rapport à d’autres types de tâches en arrière-plan.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Les mises à jour de l’application et les complications

Outre les nouvelles tâches en arrière-plan qui Apple a ajouté à watchOS 3, Complications d’une application watchOS peuvent avoir une incidence sur quand et comment l’application reçoit des mises à jour en arrière-plan.

Complications sont de petits éléments visuels qui fournissent des informations utiles en un clin de œil. Selon le cadran de montre sélectionné, l’utilisateur a la possibilité de personnaliser un cadran de montre avec un ou plusieurs des complications peuvent être fournie par une application watch dans watchOS 3.

Si l’utilisateur inclut l’une des Complications de l’application sur leur cadran de montre, il donne à l’application suit mis à jour avantages :

- Elle force le système afin de maintenir l’application dans prêt-à-lancement d’un état, où il tente de lancer l’application en arrière-plan, il conserve en mémoire et laisse l’il temps supplémentaire pour mettre à jour.
- Complications sont garanties au moins 50 mises à jour de push par jour.

Le développeur doit s’efforcent toujours créer des Complications attrayantes pour leurs applications pour inciter un utilisateur à les ajouter à leur cadran de montre pour les raisons répertoriées ci-dessus.

Dans watchOS 2, les Complications ont été le principal moyen qu’une application reçue d’exécution en arrière-plan. Dans watchOS 3, une application de Complication sera toujours assurée de recevoir plusieurs mises à jour par heure, toutefois, il peut utiliser `WKExtensions` pour demander plus de runtime pour mettre à jour ses complications.

Examinons le code suivant pour mettre à jour de la Complication à partir de l’application iPhone connecté :

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

Il utilise le `RemainingComplicationUserInfoTransfers` propriété de la `WCSession` pour voir combien de haute priorité transfère l’application a quitté du jour, puis effectue une action en fonction de ce nombre. Si l’application commence à manquer sur les transferts, il peut différez lors de l’envoi des mises à jour mineures et envoyer des informations uniquement lorsqu’il existe une modification significative.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Planification et la station d’accueil

Dans watchOS 3, Apple a ajouté la station d’accueil où les utilisateurs peuvent épingler leurs applications favorites et y accéder rapidement. Lorsque l’utilisateur appuie sur le bouton sur l’Apple Watch, une galerie de captures instantanées application épinglée s’affichera. L’utilisateur peut balayer gauche ou droite pour rechercher l’application souhaitée, puis appuyez sur l’application pour le lancer en remplaçant l’instantané avec l’interface de l’application en cours d’exécution.

[![](background-tasks-images/dock01.png "L’écran d’ancrage")](background-tasks-images/dock01.png#lightbox)

Périodiquement, le système prend des captures instantanées de l’interface utilisateur de l’application et utilise ces captures instantanées pour remplir les documents. watchOS donne la possibilité de mettre à jour son contenu ni l’interface utilisateur avant que cet instantané est mis à l’application.

Les applications qui ont été épinglées sur l’écran d’ancrage peuvent s’attendre les éléments suivants :

- Il reçoit au moins un mis à jour par heure. Cela inclut une tâche d’actualisation d’application et une tâche de capture instantanée.
- Le budget de mise à jour est distribué entre toutes les applications dans l’écran d’ancrage. Par conséquent, le moins d’applications que l’utilisateur a été épinglée, les mises à jour plus le risque que chaque application recevra.
- L’application sera maintenue en mémoire afin de l’application va reprendre rapidement lorsque sélectionné à partir de la station d’accueil.

La dernière application exécutée par l’utilisateur sera considéré comme le _utilisés le plus récemment_ application et occupera le dernier emplacement dans l’écran d’ancrage. À partir de là, il utilisateur peut choisir d’épingler définitivement à l’écran d’ancrage. Le plus récemment utilisé sera traité comme toute autre application favorite l’utilisateur a déjà épinglée sur l’écran d’ancrage.

> [!IMPORTANT]
> Les applications qui ont été ajoutées uniquement à l’écran d’accueil pas reçoivent une planification régulière. Pour recevoir une planification régulière et arrière-plan met à jour, une application _doit_ être ajouté à la station d’accueil.

Comme indiqué plus haut dans ce document, des captures instantanées sont très importantes dans watchOS 3 dans la mesure où ils fonctionnent en tant qu’images de la version préliminaire et le lancement de l’application. Si l’utilisateur se règle sur une application dans le Dock, il développez en plein écran, entrez le premier plan et commencer son exécution, il est donc impératif que l’instantané soit à jour.

Il peut arriver lorsque le système décide qu’il a besoin d’un instantané de frais de l’interface utilisateur de l’application. Dans ce cas, la demande de capture instantanée n’est pas décomptés budget de runtime de l’application. Déclenchent une demande de capture instantanée du système les éléments suivants :

- Une mise à jour de la chronologie de Complication.
- Interaction utilisateur avec notification de l’application.
- Passage de premier plan à l’état d’arrière-plan.
- Après une heure de l’état d’arrière-plan, par conséquent, l’application peut retourner l’état par défaut.
- Lorsque watchOS premier démarrage.

<a name="Best-Practices" />

## <a name="best-practices"></a>Meilleures pratiques 

Apple suggère les meilleures pratiques suivantes lorsque vous travaillez avec des tâches en arrière-plan :

- Planification aussi souvent que l’application doit être mis à jour. Chaque fois que l’application s’exécute, il doit réévaluer ses besoins futurs et ajustez cette planification en fonction des besoins.
- Si le système envoie une tâche d’actualisation en arrière-plan et l’application ne nécessite pas une mise à jour, différer le travail jusqu'à ce qu’une mise à jour soit réellement nécessaire.
- Prenez en compte toutes les opportunités de runtime sont disponibles pour une application :
    - Activation d’ancrage et de premier plan.
    - Notifications.
    - Mises à jour de la complication.
    - Actualisations en arrière-plan.
- Utilisez `ScheduleBackgroundRefresh` pour l’exécution en arrière-plan à usage général comme :
    - Interrogation du système pour plus d’informations.
    - Planifier l’avenir `NSURLSessions` pour demander des données en arrière-plan. 
    - Transitions de temps connu.
    - Déclencher des mises à jour de la Complication.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Meilleures pratiques d’instantané

Lorsque vous travaillez avec les mises à jour de l’instantané, Apple rend les suggestions suivantes :

- Invalident les instantanés uniquement lorsque nécessaire, par exemple, lorsqu’il existe une modification significative de contenu.
- Éviter l’invalidation des instantanés à fréquence élevée. Par exemple, une application du minuteur ne doit pas mettre à jour l’instantané chaque seconde, elle doit être effectuée uniquement lorsque le minuteur s’est terminée.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flux de données d’application

Apple suggère ce qui suit pour l’utilisation des flux de données :

[![](background-tasks-images/update17.png "Diagramme de flux de données d’application")](background-tasks-images/update17.png#lightbox)

Un événement externe (par exemple, une connectivité espion) sort de l’application. Cela force l’application à mettre à jour son modèle de données (qui représente l’état actuel d’applications). Par conséquent de la modification de modèle de données l’application doit mettre à jour ses Complications, demande un nouvel instantané, éventuellement démarrer un arrière-plan `NSURLSession` pour extraire des données plus et planifier davantage d’arrière-plan actualise.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Le cycle de vie d’application

En raison de l’écran d’ancrage et la possibilité d’épingler des applications favorites à celui-ci, estime que Apple que vous envisagez de déplacer des utilisateurs entre les applications bien plus, beaucoup plus souvent, puis ils le faisaient dans watchOS 2. Par conséquent, l’application doit être prête à gérer cette modification et de déplacer rapidement entre les États de premier plan et d’arrière-plan.

Apple a les suggestions suivantes :

- Assurez-vous que l’application termine une tâche en arrière-plan dès que possible lors de l’entrée de l’activation de premier plan.
- Veillez à terminer toutes les tâches de premier plan avant d’entrer l’arrière-plan en appelant `NSProcessInfo.PerformExpiringActivity`.
- Lorsque vous testez une application dans le simulateur watchOS, aucun des budgets de tâche sont appliquées pour qu’une application puisse actualiser autant que nécessaire pour tester correctement une fonctionnalité.
- Testez toujours sur du vrai matériel Apple Watch pour vous assurer que l’application n’est pas en cours d’exécution après son budgets avant la publication dans iTunes Connect.
- Apple suggère en conservant l’Apple Watch sur le chargeur lors de tests et le débogage.
- Assurez-vous que les deux à froid lancement et la reprise d’une application sont testées.
- Vérifiez que toutes les tâches d’application sont en cours terminées.
- Faire varier le nombre d’applications qui sont épinglés dans le Dock pour tester les meilleures et le pire cas de scénarios.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté les améliorations apportées par Apple pour watchOS et comment ils peuvent être utilisés pour maintenir une application watch à jour. Tout d’abord, elle couvrait toutes les nouvelles Apple de tâche en arrière-plan a ajoutées à watchOS 3. Ensuite, elle couvrait le cycle de vie des API en arrière-plan et comment implémenter des tâches en arrière-plan dans une application watchOS de Xamarin. Enfin, il couvert les planifications de travaux et a donné quelques meilleures pratiques.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
