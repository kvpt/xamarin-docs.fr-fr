---
title: Applications d’entraînement Watchos dans Xamarin
description: Cet article aborde les améliorations apportées par Apple pour mettre en œuvre des applications dans Watchos 3 et comment les implémenter dans Xamarin.
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: f5a2b17491b026e08abf2262a998576cbb4356c5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767827"
---
# <a name="watchos-workout-apps-in-xamarin"></a>Applications d’entraînement Watchos dans Xamarin

_Cet article aborde les améliorations apportées par Apple pour mettre en œuvre des applications dans Watchos 3 et comment les implémenter dans Xamarin._

Nouveauté de Watchos 3, les applications associées à l’entraînement ont la possibilité de s’exécuter en arrière-plan sur le Apple Watch et d’accéder aux données HealthKit. L’application iOS 10 parente a également la possibilité de lancer l’application Watchos 3 sans intervention de l’utilisateur.

Les rubriques suivantes sont traitées en détail :

## <a name="about-workout-apps"></a>À propos des applications de la entraînement

Les utilisateurs des applications d’adéquation et d’entraînement peuvent être très dédiés, dévoter plusieurs heures de la journée pour atteindre leurs objectifs de santé et de pertinence. Par conséquent, ils attendent des applications réactives et faciles à utiliser qui collectent et affichent des données avec précision et s’intègrent de manière transparente à Apple Health.

Une application d’adéquation ou d’entraînement bien conçue aide les utilisateurs à faire un graphique de leurs activités pour atteindre leurs objectifs. En utilisant le Apple Watch, les applications d’adéquation et d’entraînement ont un accès instantané au rythme cardiaque, au gravage de calories et à la détection d’activité.

[![](workout-apps-images/workout01.png "Exemple d’application pertinence et entraînement")](workout-apps-images/workout01.png#lightbox)

Nouveauté de Watchos 3, l' _arrière-plan en cours d’exécution_ donne aux applications liées à l’entraînement la possibilité de s’exécuter en arrière-plan sur le Apple Watch et d’accéder aux données HealthKit.

Ce document présente la fonctionnalité d’exécution en arrière-plan, couvre le cycle de vie des applications de l’entraînement et montre comment une application de l’entraînement peut contribuer aux _anneaux d’activité_ de l’utilisateur sur le Apple Watch.

## <a name="about-workout-sessions"></a>À propos des sessions d’entraînement

Le cœur de chaque application d’entraînement est une _session_ de`HKWorkoutSession`veille () que l’utilisateur peut démarrer et arrêter. L’API de session de mise en œuvre est facile à implémenter et offre plusieurs avantages pour une application d’entraînement, par exemple :

- Détection de brûlure de mouvement et de calories basée sur le type d’activité.
- Contribution automatique aux anneaux d’activité de l’utilisateur.
- Pendant une session, l’application s’affiche automatiquement chaque fois que l’utilisateur sort de l’appareil (en déclenchant son poignet ou en interagissant avec le Apple Watch).

## <a name="about-background-running"></a>À propos de l’exécution en arrière-plan

Comme indiqué ci-dessus, avec Watchos 3, une application d’entraînement peut être configurée pour s’exécuter en arrière-plan. L’utilisation de l’arrière-plan exécutant une application de entraînement peut traiter les données des capteurs de Apple Watch en cours d’exécution en arrière-plan. Par exemple, une application peut continuer à surveiller le taux cardiaque de l’utilisateur, même si elle n’est plus affichée à l’écran.

L’exécution en arrière-plan offre également la possibilité de présenter des commentaires en direct à l’utilisateur à tout moment pendant une session d’exercices actifs, par exemple l’envoi d’une alerte haptique pour informer l’utilisateur de la progression actuelle.

En outre, l’exécution en arrière-plan permet à l’application de mettre rapidement à jour son interface utilisateur, de sorte que l’utilisateur dispose des données les plus récentes lorsqu’il parvient rapidement à le Apple Watch.

Pour garantir des performances élevées sur Apple Watch, une application espion utilisant l’arrière-plan doit limiter la quantité de travail en arrière-plan pour économiser la batterie. Si une application utilise trop d’UC en arrière-plan, elle peut être suspendue par Watchos.

### <a name="enabling-background-running"></a>Activation de l’exécution en arrière-plan

Pour activer l’exécution en arrière-plan, procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur le fichier de `Info.plist` l’application iPhone de l’extension Watch pour l’ouvrir et le modifier.
2. Basculez vers la vue **source** : 

    [![](workout-apps-images/plist01.png "Vue source")](workout-apps-images/plist01.png#lightbox)
3. Ajoutez une nouvelle clé nommée `WKBackgroundModes` et définissez le **type** sur `Array`: 

    [![](workout-apps-images/plist02.png "Ajoutez une nouvelle clé nommée WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. Ajoutez un nouvel élément au tableau avec le **type** `String` `workout-processing`et la valeur : 

    [![](workout-apps-images/plist03.png "Ajouter un nouvel élément au tableau avec le type de chaîne et une valeur d’entraînement-traitement")](workout-apps-images/plist03.png#lightbox)
5. Enregistrez les modifications dans le fichier.

## <a name="starting-a-workout-session"></a>Démarrage d’une session de exercices

Il existe trois étapes principales pour démarrer une session de exercices :

[![](workout-apps-images/workout02.png "Les trois étapes principales du démarrage d’une session de exercices")](workout-apps-images/workout02.png#lightbox)

1. L’application doit demander une autorisation pour accéder aux données dans HealthKit.
2. Créez un objet de configuration de l’entraînement pour le type d’entraînement en cours de démarrage.
3. Créer et démarrer une session d’entraînement à l’aide de la configuration d’entraînement nouvellement créée.

### <a name="requesting-authorization"></a>Demande d’autorisation

Pour qu’une application puisse accéder aux données HealthKit de l’utilisateur, elle doit demander et recevoir l’autorisation de l’utilisateur. Selon la nature de l’application d’entraînement, elle peut effectuer les types de requêtes suivants :

- Autorisation d’écrire des données :
  - Entraînements
- Autorisation de lire les données :
  - Énergie brûlée
  - Distance
  - Taux cardiaque  

Pour qu’une application puisse demander une autorisation, elle doit être configurée pour accéder à HealthKit.

Effectuez ce qui suit :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Entitlements.plist` pour l’ouvrir et le modifier.
2. Faites défiler vers le bas et cochez **Enable HealthKit**: 

    [![](workout-apps-images/auth01.png "Cochez Activer HealthKit")](workout-apps-images/auth01.png#lightbox)
3. Enregistrez les modifications dans le fichier.
4. Suivez les instructions de l' [ID d’application explicite et du profil de provisionnement](~/ios/platform/healthkit.md) et [ASSOCIez l’ID d’application et le profil de provisionnement à vos sections d’application Xamarin. iOS](~/ios/platform/healthkit.md) de l’article [Introduction à HealthKit](~/ios/platform/healthkit.md) pour approvisionner correctement l’application.
5. Enfin, utilisez les instructions de l’article présentation du [Kit d’intégrité](~/ios/platform/healthkit.md) de la programmation et des demandes d' [autorisation des utilisateurs](~/ios/platform/healthkit.md) de l’article [Introduction à HealthKit](~/ios/platform/healthkit.md) pour demander l’autorisation d’accéder à la Banque de données HealthKit de l’utilisateur.

### <a name="setting-the-workout-configuration"></a>Définition de la configuration de l’entraînement

Les sessions d’entraînement sont créées à l’aide d'`HKWorkoutConfiguration`un objet de configuration d’entraînement () qui `HKWorkoutActivityType.Running`spécifie le type d’entraînement (tel `HKWorkoutSessionLocationType.Outdoor`que) et l’emplacement de l’entraînement (tel que) :

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
  ActivityType = HKWorkoutActivityType.Running,
  LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>Création d’un délégué de session d’entraînement 

Pour gérer les événements qui peuvent se produire pendant une session de veille, l’application doit créer une instance de délégué de session d’entraînement. Ajoutez une nouvelle classe au projet et basez-la en dehors `HKWorkoutSessionDelegate` de la classe. Pour l’exemple d’exécution en extérieur, il peut se présenter comme suit :

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class OutdoorRunDelegate : HKWorkoutSessionDelegate
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; private set; }
    public HKWorkoutSession WorkoutSession { get; private set;}
    #endregion

    #region Constructors
    public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
    {
      // Initialize
      this.HealthStore = healthStore;
      this.WorkoutSession = workoutSession;

      // Attach this delegate to the session
      workoutSession.Delegate = this;
    }
    #endregion

    #region Override Methods
    public override void DidFail (HKWorkoutSession workoutSession, NSError error)
    {
      // Handle workout session failing
      RaiseFailed ();
    }

    public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
    {
      // Take action based on the change in state
      switch (toState) {
      case HKWorkoutSessionState.NotStarted:
        break;
      case HKWorkoutSessionState.Paused:
        RaisePaused ();
        break;
      case HKWorkoutSessionState.Running:
        RaiseRunning ();
        break;
      case HKWorkoutSessionState.Ended:
        RaiseEnded ();
        break;
      }

    }

    public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
    {
      base.DidGenerateEvent (workoutSession, @event);
    }
    #endregion

    #region Events
    public delegate void OutdoorRunEventDelegate ();

    public event OutdoorRunEventDelegate Failed;
    internal void RaiseFailed ()
    {
      if (this.Failed != null) this.Failed ();
    }

    public event OutdoorRunEventDelegate Paused;
    internal void RaisePaused ()
    {
      if (this.Paused != null) this.Paused ();
    }

    public event OutdoorRunEventDelegate Running;
    internal void RaiseRunning ()
    {
      if (this.Running != null) this.Running ();
    }

    public event OutdoorRunEventDelegate Ended;
    internal void RaiseEnded ()
    {
      if (this.Ended != null) this.Ended ();
    }
    #endregion
  }
}
```

Cette classe crée plusieurs événements qui seront déclenchés à mesure que l’état de la session de`DidChangeToState`l’entraînement change () et si la`DidFail`session de l’entraînement échoue (). 

### <a name="creating-a-workout-session"></a>Création d’une session d’entraînement

À l’aide de la configuration de l’entraînement et du délégué de session d’entraînement créé ci-dessus pour créer une session d’entraînement et la démarrer sur le magasin de HealthKit par défaut de l’utilisateur :

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
  // Create a workout configuration
  var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
  };

  // Create workout session
  // Start workout session
  NSError error = null;
  var workoutSession = new HKWorkoutSession (configuration, out error);

  // Successful?
  if (error != null) {
    // Report error to user and return
    return;
  }

  // Create workout session delegate and wire-up events
  RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

  RunDelegate.Failed += () => {
    // Handle the session failing
  };

  RunDelegate.Paused += () => {
    // Handle the session being paused
  };

  RunDelegate.Running += () => {
    // Handle the session running
  };

  RunDelegate.Ended += () => {
    // Handle the session ending
  };

  // Start session
  HealthStore.StartWorkoutSession (workoutSession);
}
```

Si l’application démarre cette session de suivi et que l’utilisateur revient au visage de la montre, une petite icône vert « Running Man » s’affiche au-dessus de la face :

[![](workout-apps-images/workout03.png "Petite icône Man en cours d’exécution affichée au-dessus du visage")](workout-apps-images/workout03.png#lightbox)

Si l’utilisateur appuie sur cette icône, il est redirigé vers l’application.

## <a name="data-collection-and-control"></a>Collecte et contrôle des données

Une fois qu’une session de exercices a été configurée et démarrée, l’application doit collecter les données relatives à la session (par exemple, le taux cardiaque de l’utilisateur) et contrôler l’état de la session :

[![](workout-apps-images/workout04.png "Diagramme de collecte et de contrôle des données")](workout-apps-images/workout04.png#lightbox)

1. **Exemples d’observation** : l’application doit extraire des informations de HealthKit qui seront traitées et affichées à l’utilisateur.
2. **Observation des événements** : l’application doit répondre aux événements générés par HealthKit ou à partir de l’interface utilisateur de l’application (par exemple, l’utilisateur suspendant l’entraînement).
3. **Entrer l’état d’exécution** : la session a été démarrée et est en cours d’exécution.
4. **Entrer l’état suspendu** -l’utilisateur a suspendu la session en cours et peut le redémarrer à une date ultérieure. L’utilisateur peut basculer entre les États en cours d’exécution et les États suspendus plusieurs fois dans une même session d’entraînement.
5. **Terminer la session** de séance d’entraînement : à tout moment, l’utilisateur peut mettre fin à la session de la session de faire, ou elle peut expirer et se terminer par elle-même s’il s’agissait d’un entraînement limité (par exemple, une exécution à deux milles).

La dernière étape consiste à enregistrer les résultats de la session de formation dans la Banque de HealthKit de l’utilisateur.

### <a name="observing-healthkit-samples"></a>Observation des exemples HealthKit

L’application doit ouvrir une requête d' _objet d’ancrage_ pour chacun des points de données HealthKit qui l’intéressent, tels que la vitesse cardiaque ou l’énergie active gravée. Pour chaque point de données observé, un gestionnaire de mise à jour doit être créé pour capturer les nouvelles données à mesure qu’elles sont envoyées à l’application.

À partir de ces points de données, l’application peut accumuler des totaux (tels que la distance totale d’exécution) et mettre à jour son interface utilisateur selon les besoins. En outre, l’application peut notifier les utilisateurs lorsqu’ils ont atteint un objectif ou une réalisation spécifique, par exemple terminer le prochain kilomètre d’une série.

Jetez un coup d’œil à l’exemple de code suivant :

```csharp
private void ObserveHealthKitSamples ()
{
  // Get the starting date of the required samples
  var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

  // Get data from the local device
  var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
  var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

  // Assemble compound predicate
  var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

  // Get ActiveEnergyBurned
  var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
    // Valid?
    if (error == null) {
      // Yes, process all returned samples
      foreach (HKSample sample in addedObjects) {
        var quantitySample = sample as HKQuantitySample;
        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
      }
      
      // Update User Interface
      ...
    }
  });

  // Start Query
  HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                        
}
```

Il crée un prédicat pour définir la date de début à laquelle il souhaite obtenir des données pour l' `GetPredicateForSamples` utilisation de la méthode. Il crée un ensemble d’appareils pour extraire les informations HealthKit à l' `GetPredicateForObjectsFromDevices` aide de la méthode, dans ce cas, l'`HKDevice.LocalDevice`Apple Watch local uniquement (). Les deux prédicats sont combinés en un prédicat composite (`NSCompoundPredicate`) à l' `CreateAndPredicate` aide de la méthode.

Une nouvelle `HKAnchoredObjectQuery` est créée pour le point de données souhaité (dans le `HKQuantityTypeIdentifier.ActiveEnergyBurned` cas présent pour le point de données d’énergie brûlé actif), aucune limite n’est imposée`HKSampleQuery.NoLimit`sur la quantité de données retournées () et un gestionnaire de mise à jour est défini pour gérer les données qui sont renvoyées à l’application. à partir de HealthKit. 

Le gestionnaire de mise à jour est appelé chaque fois que de nouvelles données sont remises à l’application pour le point de données spécifié. Si aucune erreur n’est retournée, l’application peut lire les données en toute sécurité, effectuer les calculs requis et mettre à jour l’interface utilisateur en fonction des besoins.

Le code effectue une boucle sur tous les échantillons`HKSample`() retournés dans le `addedObjects` tableau et les convertit en`HKQuantitySample`un exemple Quantity (). Elle obtient ensuite la valeur double de l’échantillon sous la forme d'`HKUnit.Joule`un Joule () et l’accumule dans le total cumulé de l’énergie active gravé pour l’entraînement et met à jour l’interface utilisateur.

### <a name="achieved-goal-notification"></a>Notification d’objectif obtenue

Comme indiqué ci-dessus, lorsque l’utilisateur atteint un objectif dans l’application d’entraînement (par exemple, en terminant le premier kilomètre d’une série), il peut envoyer des commentaires haptique à l’utilisateur via le moteur Taptic Engine. L’application doit également mettre à jour son interface utilisateur à ce stade, étant donné que l’utilisateur est plus susceptible de soulever son poignet pour voir l’événement qui a demandé les commentaires.

Pour lire les commentaires haptique, utilisez le code suivant :

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>Événements d’observation

Les événements sont des horodateurs que l’application peut utiliser pour mettre en surbrillance certains points pendant l’entraînement de l’utilisateur. Certains événements sont créés directement par l’application et enregistrés dans l’entraînement, et certains événements sont créés automatiquement par HealthKit.

Pour observer les événements créés par HealthKit, l’application remplace la `DidGenerateEvent` méthode de l' `HKWorkoutSessionDelegate`événement :

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);
  
  // Save HealthKit generated event
  WorkoutEvents.Add (@event);
  
  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.Lap:
    break;
  case HKWorkoutEventType.Marker:
    break;
  case HKWorkoutEventType.MotionPaused:
    break;
  case HKWorkoutEventType.MotionResumed:
    break;
  case HKWorkoutEventType.Pause:
    break;
  case HKWorkoutEventType.Resume:
    break;
  }
}
```

Apple a ajouté les nouveaux types d’événements suivants dans Watchos 3 :

- `HKWorkoutEventType.Lap`-Concernent les événements qui rompent l’entraînement en portions égales à distance. Par exemple, pour marquer un tour autour d’une piste pendant l’exécution.
- `HKWorkoutEventType.Marker`-Sont destinées à des points d’intérêt arbitraires au sein de l’entraînement. Par exemple, atteindre un point spécifique sur l’itinéraire d’une exécution extérieure.

Ces nouveaux types peuvent être créés par l’application et stockés dans l’entraînement pour une utilisation ultérieure dans la création de graphiques et de statistiques.

Pour créer un événement de marqueur, procédez comme suit :

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
  // Create and save marker event
  var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
  WorkoutEvents.Add (markerEvent);

  // Notify user
  NotifyUserOfReachedMileGoal (++MilesRun);
}
```

Ce code crée une nouvelle instance d’un événement marqueur`HKWorkoutEvent`() et l’enregistre dans une collection privée d’événements (qui sera écrite ultérieurement dans la session de veille) et notifie l’utilisateur de l’événement via des haptique.

### <a name="pausing-and-resuming-workouts"></a>Suspension et reprise des entraînements

À tout moment dans une session de mise en veille, l’utilisateur peut suspendre temporairement l’entraînement et le reprendre ultérieurement. Par exemple, ils peuvent suspendre une exécution en intérieur pour effectuer un appel important et reprendre l’exécution une fois l’appel terminé.

L’interface utilisateur de l’application doit fournir un moyen de suspendre et de reprendre l’entraînement (en appelant dans HealthKit) afin que le Apple Watch puisse économiser à la fois l’espace de l’alimentation et les données pendant que l’utilisateur a suspendu son activité. En outre, l’application doit ignorer tous les nouveaux points de données qui peuvent être reçus lorsque la session de mise en veille est dans un état suspendu.

HealthKit répondra aux appels de pause et de reprise en générant des événements d’interruption et de reprise. Pendant que la session de suivi est suspendue, aucun nouvel événement ou aucune donnée n’est envoyé à l’application par HealthKit tant que la session n’est pas reprise.

Utilisez le code suivant pour suspendre et reprendre une session d’entraînement :

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
  // Pause the current workout
  HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
  // Pause the current workout
  HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

Les événements de suspension et de reprise qui seront générés à partir de HealthKit peuvent être gérés `DidGenerateEvent` en substituant `HKWorkoutSessionDelegate`la méthode de l’une des méthodes suivantes :

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);

  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.Pause:
    break;
  case HKWorkoutEventType.Resume:
    break;
  }
}
```

### <a name="motion-events"></a>Événements motion

En outre, nouveauté de Watchos 3, les événements Motion pause`HKWorkoutEventType.MotionPaused`() et Motion continued`HKWorkoutEventType.MotionResumed`(). Ces événements sont déclenchés automatiquement par HealthKit lors d’un entraînement lorsque l’utilisateur démarre et arrête le déplacement.

Lorsque l’application reçoit un événement Motion pause, elle doit arrêter la collecte de données jusqu’à ce que l’utilisateur reprenne le mouvement et que l’événement de reprise du mouvement soit reçu. L’application ne doit pas suspendre la session de mise en oeuvre en réponse à un événement de pause.

> [!IMPORTANT]
> Les événements Motion pause et reresume de mouvement sont uniquement pris en charge pour le type`HKWorkoutActivityType.Running`d’activité RunningWorkout ().

Là encore, ces événements peuvent être gérés en substituant `DidGenerateEvent` la méthode `HKWorkoutSessionDelegate`du :

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
  base.DidGenerateEvent (workoutSession, @event);
  
  // Take action based on the type of event
  switch (@event.Type) {
  case HKWorkoutEventType.MotionPaused:
    break;
  case HKWorkoutEventType.MotionResumed:
    break;
  }
}

```

## <a name="ending-and-saving-the-workout-session"></a>Fin et enregistrement de la session d’exercices

Lorsque l’utilisateur a terminé son entraînement, l’application doit mettre fin à la session de suivi en cours et l’enregistrer dans la base de données HealthKit. Les entraînements enregistrés dans HealthKit s’affichent automatiquement dans la liste activité d’entraînement.

Nouveauté d’iOS 10, cela comprend également la liste des activités d’entraînement sur l’iPhone de l’utilisateur. Ainsi, même si le Apple Watch n’est pas à proximité, l’entraînement sera présenté sur le téléphone.

Les entraînements qui incluent des échantillons d’énergie mettent à jour l’anneau de déplacement de l’utilisateur dans l’application des activités afin que les applications tierces puissent désormais contribuer aux objectifs de déplacement quotidiens de l’utilisateur.

Les étapes suivantes sont nécessaires pour terminer et enregistrer une session de exercices :

[![](workout-apps-images/workout05.png "Finalisation et enregistrement du diagramme de session de exercices")](workout-apps-images/workout05.png#lightbox)

1. Tout d’abord, l’application doit mettre fin à la session d’entraînement.
2. La session de mise en entraînement est enregistrée dans HealthKit.
3. Ajoutez tous les échantillons (tels que l’énergie gravée ou la distance) à la session d’entraînement enregistrée.

### <a name="ending-the-session"></a>Fin de la session

Pour mettre fin à la session `EndWorkoutSession` `HKHealthStore` de l’entraînement, appelez la méthode du `HKWorkoutSession`passage de l’un des éléments suivants :

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
  // End the current workout session
  HealthStore.EndWorkoutSession (WorkoutSession);
}
```

Cela permet de réinitialiser les capteurs d’appareils en mode normal. Quand HealthKit termine la fin de l’entraînement, il reçoit un rappel `DidChangeToState` de la méthode `HKWorkoutSessionDelegate`de l’opération :

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
  // Take action based on the change in state
  switch (toState) {
  ...
  case HKWorkoutSessionState.Ended:
    StopObservingHealthKitSamples ();
    RaiseEnded ();
    break;
  }

}
```

### <a name="saving-the-session"></a>Enregistrement de la session

Une fois que l’application a terminé la session d’entraînement, elle doit créer un entraînement`HKWorkout`() et l’enregistrer (avec un événement) dans le magasin de données HealthKit`HKHealthStore`() :

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
  // Build required workout quantities 
  var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
  var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

  // Create any required metadata
  var metadata = new NSMutableDictionary ();
  metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

  // Create workout
  var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                  WorkoutSession.StartDate, 
                                  NSDate.Now, 
                                  WorkoutEvents.ToArray (), 
                                  energyBurned, 
                                  distance, 
                                  metadata);

  // Save to HealthKit
  HealthStore.SaveObject (workout, (successful, error) => {
    // Handle any errors
    if (error == null) {
      // Was the save successful
      if (successful) {

      }
    } else {
      // Report error
    }
  });

}
```

Ce code crée la quantité totale d’énergie imbrûlée et la distance pour l' `HKQuantity` entraînement en tant qu’objets. Un dictionnaire de métadonnées définissant l’entraînement est créé et l’emplacement de l’entraînement est spécifié :

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

Un nouvel `HKWorkout` objet est créé avec le même `HKWorkoutActivityType` que `HKWorkoutSession`le, les dates de début et de fin, la liste des événements (accumulés à partir des sections ci-dessus), le dictionnaire énergie brûlée, distance totale et métadonnées. Cet objet est enregistré dans le magasin d’intégrité et toutes les erreurs sont gérées.  

### <a name="adding-samples"></a>Ajout d’exemples

Lorsque l’application enregistre un ensemble d’exemples dans un entraînement, HealthKit génère une connexion entre les exemples et l’entraînement proprement dit afin que l’application puisse interroger HealthKit à une date ultérieure pour tous les échantillons associés à une entraînement donnée. À l’aide de ces informations, l’application peut générer des graphiques à partir des données de l’entraînement et les tracer sur une chronologie d’entraînement.

Pour qu’une application contribue à l’anneau de déplacement de l’application d’activité, elle doit inclure des échantillons d’énergie avec l’entraînement enregistré. En outre, les totaux de la distance et de l’énergie doivent correspondre à la somme de tous les échantillons que l’application associe à un entraînement enregistré.

Pour ajouter des exemples à une entraînement enregistré, procédez comme suit :

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...

private void SaveWorkoutSamples (HKWorkout workout)
{
  // Add samples to saved workout
  HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
    // Handle any errors
    if (error == null) {
      // Was the save successful
      if (success) {

      }
    } else {
      // Report error
    }
  });
}
```

Si vous le souhaitez, l’application peut calculer et créer un sous-ensemble plus petit d’échantillons ou un petit échantillon (couvrant l’ensemble de la plage de l’entraînement) qui est ensuite associé à l’entraînement enregistré.

## <a name="workouts-and-ios-10"></a>Entraînements et iOS 10

Chaque application d’entraînement watchOS 3 a une application de base entraînement iOS 10 parent et nouvelles pour iOS 10, cette application iOS peut être utilisée pour démarrer les Use qui placer l’Apple Watch dans le Mode d’entraînement (sans intervention de l’utilisateur) et exécutez l’application watchOS en mode en cours d’exécution en arrière-plan (consultez [sur en arrière-plan en cours d’exécution](#about-background-running) ci-dessus pour plus d’informations).

Pendant l’exécution de l’application Watchos, elle peut utiliser WatchConnectivity pour la messagerie et la communication avec l’application iOS parente.

Jetez un coup d’œil sur le fonctionnement de ce processus :

[![](workout-apps-images/workout06.png "diagramme de communication iPhone et Apple Watch")](workout-apps-images/workout06.png#lightbox)

1. L’application iPhone crée un `HKWorkoutConfiguration` objet et définit le type et l’emplacement de l’entraînement.
2. L' `HKWorkoutConfiguration` objet reçoit la version Apple Watch de l’application et, s’il n’est pas déjà en cours d’exécution, il est démarré par le système.
3. À l’aide de la configuration de l’entraînement passé, l’application Watchos 3 démarre une`HKWorkoutSession`nouvelle session de suivi ().

> [!IMPORTANT]
> Pour permettre à l’application iPhone parente de démarrer un entraînement sur le Apple Watch, l’exécution de l’application Watchos 3 doit être activée. Pour plus d’informations, consultez [activation de l’exécution en arrière-plan](#enabling-background-running) ci-dessus.

Ce processus est très similaire au processus de démarrage d’une session de suivi dans l’application Watchos 3 directement. Sur l’iPhone, utilisez le code suivant :

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
  // Can the app communicate with the watchOS version of the app?
  if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
      ActivityType = HKWorkoutActivityType.Running,
      LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Start watch app
    HealthStore.StartWatchApp (configuration, (success, error) => {
      // Handle any errors
      if (error == null) {
        // Was the save successful
        if (success) {
          ...
        }
      } else {
        // Report error
        ...
      }
    });
  }
}
```

Ce code garantit que la version espionneos de l’application est installée et que la version de l’iPhone peut s’y connecter en premier :

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
  ...
}
```

Ensuite, il crée `HKWorkoutConfiguration` un comme d’habitude et `StartWatchApp` utilise la méthode `HKHealthStore` de pour l’envoyer au Apple Watch et démarrer l’application et la session de l’entraînement.

Et sur l’application Watch OS, utilisez le code suivant dans le `WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
  // Create workout session
  // Start workout session
  NSError error = null;
  var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

  // Successful?
  if (error != null) {
    // Report error to user and return
    return;
  }

  // Create workout session delegate and wire-up events
  RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

  RunDelegate.Failed += () => {
    // Handle the session failing
  };

  RunDelegate.Paused += () => {
    // Handle the session being paused
  };

  RunDelegate.Running += () => {
    // Handle the session running
  };

  RunDelegate.Ended += () => {
    // Handle the session ending
  };

  // Start session
  HealthStore.StartWorkoutSession (workoutSession);
}
```

Il prend le `HKWorkoutConfiguration` et crée un nouveau `HKWorkoutSession` et attache une instance du personnalisé `HKWorkoutSessionDelegate`. La session d’entraînement est démarrée sur le magasin d’intégrité HealthKit de l’utilisateur.

## <a name="bringing-all-the-pieces-together"></a>Regroupement de tous les éléments

En acceptant toutes les informations présentées dans ce document, une application de veille basée sur Watchos 3 et son application d’entraînement à la base iOS 10 parente peuvent inclure les éléments suivants :

1. **iOS 10 `ViewController.cs`**  -gère le démarrage d’une session de connexion Watch et un entraînement sur le Apple Watch.
2. **Watchos 3 `ExtensionDelegate.cs`**  : gère la version Watchos 3 de l’application d’entraînement.
3. **Watchos 3 `OutdoorRunDelegate.cs`**  : un personnalisé `HKWorkoutSessionDelegate` pour gérer les événements de l’entraînement.

> [!IMPORTANT]
> Le code présenté dans les sections suivantes comprend uniquement les composants requis pour implémenter les nouvelles fonctionnalités améliorées fournies pour les applications d’entraînement dans Watchos 3. Tout le code de prise en charge et le code de présentation et de mise à jour de l’interface utilisateur ne sont pas inclus, mais peuvent être facilement créés en suivant notre documentation espionneos.<p/>

### <a name="viewcontrollercs"></a>ViewController.cs

Le `ViewController.cs` fichier dans la version d’iOS 10 parente de l’application d’entraînement inclut le code suivant :

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
  public partial class ViewController : UIViewController
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
    public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
    #endregion

    #region Constructors
    protected ViewController (IntPtr handle) : base (handle)
    {
      // Note: this .ctor should not contain any initialization logic.
    }
    #endregion

    #region Private Methods
    private void InitializeWatchConnectivity ()
    {
      // Is Watch Connectivity supported?
      if (!WCSession.IsSupported) {
        // No, abort
        return;
      }

      // Is the session already active?
      if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
        // No, start session
        ConnectivitySession.ActivateSession ();
      }
    }

    private void StartOutdoorRun ()
    {
      // Can the app communicate with the watchOS version of the app?
      if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
          ActivityType = HKWorkoutActivityType.Running,
          LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
          // Handle any errors
          if (error == null) {
            // Was the save successful
            if (success) {
              ...
            }
          } else {
            // Report error
            ...
          }
        });
      }
    }
    #endregion

    #region Override Methods
    public override void ViewDidLoad ()
    {
      base.ViewDidLoad ();

      // Start Watch Connectivity
      InitializeWatchConnectivity ();
    }
    #endregion
  }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

Le `ExtensionDelegate.cs` fichier dans la version Watchos 3 de l’application d’entraînement inclut le code suivant :

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class ExtensionDelegate : WKExtensionDelegate
  {
    #region Computed Properties
    public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
    public OutdoorRunDelegate RunDelegate { get; set; }
    #endregion

    #region Constructors
    public ExtensionDelegate ()
    {
      
    }
    #endregion

    #region Private Methods
    private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
    {
      // Create workout session
      // Start workout session
      NSError error = null;
      var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

      // Successful?
      if (error != null) {
        // Report error to user and return
        return;
      }

      // Create workout session delegate and wire-up events
      RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

      RunDelegate.Failed += () => {
        // Handle the session failing
        ...
      };

      RunDelegate.Paused += () => {
        // Handle the session being paused
        ...
      };

      RunDelegate.Running += () => {
        // Handle the session running
        ...
      };

      RunDelegate.Ended += () => {
        // Handle the session ending
        ...
      };
      
      RunDelegate.ReachedMileGoal += (miles) => {
        // Handle the reaching a session goal
        ...
      };

      RunDelegate.HealthKitSamplesUpdated += () => {
        // Update UI as required
        ...
      };

      // Start session
      HealthStore.StartWorkoutSession (workoutSession);
    }

    private void StartOutdoorRun ()
    {
      // Create a workout configuration
      var workoutConfiguration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
      };

      // Start the session
      StartWorkoutSession (workoutConfiguration);
    }
    #endregion

    #region Override Methods
    public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
    {
      // Start the session
      StartWorkoutSession (workoutConfiguration);
    }
    #endregion
  }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

Le `OutdoorRunDelegate.cs` fichier dans la version Watchos 3 de l’application d’entraînement inclut le code suivant :

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
  public class OutdoorRunDelegate : HKWorkoutSessionDelegate
  {
    #region Private Variables
    private HKAnchoredObjectQuery QueryActiveEnergyBurned;
    #endregion

    #region Computed Properties
    public HKHealthStore HealthStore { get; private set; }
    public HKWorkoutSession WorkoutSession { get; private set;}
    public float MilesRun { get; set; }
    public double ActiveEnergyBurned { get; set;}
    public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
    public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
    #endregion

    #region Constructors
    public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
    {
      // Initialize
      this.HealthStore = healthStore;
      this.WorkoutSession = workoutSession;

      // Attach this delegate to the session
      workoutSession.Delegate = this;

    }
    #endregion

    #region Private Methods
    private void ObserveHealthKitSamples ()
    {
      // Get the starting date of the required samples
      var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

      // Get data from the local device
      var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
      var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

      // Assemble compound predicate
      var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

      // Get ActiveEnergyBurned
      QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
          // Yes, process all returned samples
          foreach (HKSample sample in addedObjects) {
            // Accumulate totals
            var quantitySample = sample as HKQuantitySample;
            ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

            // Save samples
            WorkoutSamples.Add (sample);
          }

          // Inform caller
          RaiseHealthKitSamplesUpdated ();
        }
      });

      // Start Query
      HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                            
    }

    private void StopObservingHealthKitSamples ()
    {
      // Stop query
      HealthStore.StopQuery (QueryActiveEnergyBurned);
    }

    private void ResumeObservingHealthkitSamples ()
    {
      // Resume current queries 
      HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
    }

    private void NotifyUserOfReachedMileGoal (float miles)
    {
      // Play haptic feedback
      WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

      // Raise event
      RaiseReachedMileGoal (miles);
    }

    private void SaveWorkoutSession ()
    {
      // Build required workout quantities
      var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
      var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

      // Create any required metadata
      var metadata = new NSMutableDictionary ();
      metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

      // Create workout
      var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                      WorkoutSession.StartDate, 
                                      NSDate.Now, 
                                      WorkoutEvents.ToArray (), 
                                      energyBurned, 
                                      distance, 
                                      metadata);

      // Save to HealthKit
      HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
          // Was the save successful
          if (successful) {
            // Add samples to workout
            SaveWorkoutSamples (workout);
          }
        } else {
          // Report error
          ...
        }
      });

    }

    private void SaveWorkoutSamples (HKWorkout workout)
    {
      // Add samples to saved workout
      HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
          // Was the save successful
          if (success) {
            ...
          }
        } else {
          // Report error
          ...
        }
      });
    }
    #endregion

    #region Public Methods
    public void PauseWorkout ()
    {
      // Pause the current workout
      HealthStore.PauseWorkoutSession (WorkoutSession);
    }

    public void ResumeWorkout ()
    {
      // Pause the current workout
      HealthStore.ResumeWorkoutSession (WorkoutSession);
    }

    public void ReachedNextMile ()
    {
      // Create and save marker event
      var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
      WorkoutEvents.Add (markerEvent);

      // Notify user
      NotifyUserOfReachedMileGoal (++MilesRun);
    }

    public void EndOutdoorRun ()
    {
      // End the current workout session
      HealthStore.EndWorkoutSession (WorkoutSession);
    }
    #endregion

    #region Override Methods
    public override void DidFail (HKWorkoutSession workoutSession, NSError error)
    {
      // Handle workout session failing
      RaiseFailed ();
    }

    public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
    {
      // Take action based on the change in state
      switch (toState) {
      case HKWorkoutSessionState.NotStarted:
        break;
      case HKWorkoutSessionState.Paused:
        StopObservingHealthKitSamples ();
        RaisePaused ();
        break;
      case HKWorkoutSessionState.Running:
        if (fromState == HKWorkoutSessionState.Paused) {
          ResumeObservingHealthkitSamples ();
        } else {
          ObserveHealthKitSamples ();
        }
        RaiseRunning ();
        break;
      case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        SaveWorkoutSession ();
        RaiseEnded ();
        break;
      }

    }

    public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
    {
      base.DidGenerateEvent (workoutSession, @event);

      // Save HealthKit generated event
      WorkoutEvents.Add (@event);

      // Take action based on the type of event
      switch (@event.Type) {
      case HKWorkoutEventType.Lap:
        ...
        break;
      case HKWorkoutEventType.Marker:
        ...
        break;
      case HKWorkoutEventType.MotionPaused:
        ...
        break;
      case HKWorkoutEventType.MotionResumed:
        ...
        break;
      case HKWorkoutEventType.Pause:
        ...
        break;
      case HKWorkoutEventType.Resume:
        ...
        break;
      }
    }
    #endregion

    #region Events
    public delegate void OutdoorRunEventDelegate ();
    public delegate void OutdoorRunMileGoalDelegate (float miles);

    public event OutdoorRunEventDelegate Failed;
    internal void RaiseFailed ()
    {
      if (this.Failed != null) this.Failed ();
    }

    public event OutdoorRunEventDelegate Paused;
    internal void RaisePaused ()
    {
      if (this.Paused != null) this.Paused ();
    }

    public event OutdoorRunEventDelegate Running;
    internal void RaiseRunning ()
    {
      if (this.Running != null) this.Running ();
    }

    public event OutdoorRunEventDelegate Ended;
    internal void RaiseEnded ()
    {
      if (this.Ended != null) this.Ended ();
    }

    public event OutdoorRunMileGoalDelegate ReachedMileGoal;
    internal void RaiseReachedMileGoal (float miles)
    {
      if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
    }

    public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
    internal void RaiseHealthKitSamplesUpdated ()
    {
      if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
    }
    #endregion
  }
}
```

## <a name="best-practices"></a>Meilleures pratiques

Apple suggère d’utiliser les meilleures pratiques suivantes lors de la conception et de l’implémentation d’applications de suivi dans Watchos 3 et iOS 10 :

- Assurez-vous que l’application Watchos 3 entraînement continue à fonctionner même si elle ne peut pas se connecter à l’iPhone et à la version iOS 10 de l’application.
- Utilisez la distance HealthKit lorsque le GPS n’est pas disponible, car il est en mesure de générer des échantillons de distance sans GPS.
- Autorisez l’utilisateur à démarrer l’entraînement à partir de l’Apple Watch ou de l’iPhone.
- Autorise l’application à afficher des entraînements à partir d’autres sources (telles que d’autres applications tierces) dans ses vues de données historiques.
- Assurez-vous que l’application n’affiche pas les entraînements supprimés dans les données d’historique.

## <a name="summary"></a>Récapitulatif

Cet article a abordé les améliorations apportées par Apple pour mettre en œuvre des applications dans Watchos 3 et comment les implémenter dans Xamarin.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Présentation de HealthKit](~/ios/platform/healthkit.md)
