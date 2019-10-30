---
title: Planificateur de travaux Android
description: Ce guide explique comment planifier le travail en arrière-plan à l’aide de l’API du planificateur de travaux Android.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 4d28b80b32ff0d20afbe643d9c000f301a8ea582
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027811"
---
# <a name="android-job-scheduler"></a>Planificateur de travaux Android

_Ce guide explique comment planifier le travail en arrière-plan à l’aide de l’API du planificateur de travaux Android, qui est disponible sur les appareils Android exécutant Android 5,0 (niveau d’API 21) et versions ultérieures._

## <a name="overview"></a>Vue d'ensemble 

L’une des meilleures façons de conserver une application Android réactive à l’utilisateur consiste à s’assurer que le travail complexe ou à long terme est effectué en arrière-plan. Toutefois, il est important que le travail en arrière-plan n’ait pas d’impact négatif sur l’expérience de l’utilisateur avec l’appareil. 

Par exemple, un travail en arrière-plan peut interroger un site Web toutes les trois ou quatre minutes pour rechercher les modifications apportées à un jeu de données particulier. Cela semble Bénin, mais cela aurait un impact désastreux sur la durée de vie de la batterie. L’application veillera à réveiller l’appareil de façon répétée, élever le processeur à un état d’alimentation plus élevé, mettre en marche les radios, effectuer les demandes réseau, puis traiter les résultats. Il s’aggrave, car l’appareil ne se met pas immédiatement hors tension et passe à l’état de faible consommation d’énergie. Un travail en arrière-plan mal planifié peut laisser accidentellement l’appareil dans un État avec des exigences d’alimentation superflues et excessives. Cette activité apparemment innocente (interrogation d’un site Web) rendra l’appareil inutilisable dans un laps de temps relativement bref.

Android fournit les API suivantes pour faciliter l’exécution des tâches en arrière-plan, mais elles ne sont pas suffisantes pour une planification de travaux intelligente. 

- Les **[services d’intention](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; les services d’intention sont très utiles pour effectuer le travail, mais ils n’offrent aucun moyen de planifier le travail.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; ces API autorisent uniquement la planification de travail, mais n’offrent aucun moyen d’effectuer le travail. En outre, le AlarmManager autorise uniquement les contraintes de temps, ce qui signifie déclencher une alarme à un moment donné ou après l’expiration d’un certain laps de temps. 
- Les **[récepteurs de diffusion](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; une application Android peuvent configurer des récepteurs de diffusion pour effectuer un travail en réponse à des événements ou intentions au niveau du système. Toutefois, les récepteurs de diffusion ne fournissent aucun contrôle sur le moment où la tâche doit être exécutée. De même, les modifications apportées au système d’exploitation Android limitent le fonctionnement des récepteurs de diffusion ou les genres de travail auxquels ils peuvent répondre. 

Il existe deux fonctionnalités clés pour effectuer efficacement des tâches en arrière-plan (parfois appelées _travail en arrière-plan_ ou _travail_) :

1. **Planifier intelligemment le travail** &ndash; il est important que lorsqu’une application travaille en arrière-plan et qu’elle le fasse comme un bon citoyen. Dans l’idéal, l’application ne doit pas demander l’exécution d’un travail. Au lieu de cela, l’application doit spécifier des conditions qui doivent être remplies pour que le travail puisse s’exécuter, puis planifier ce travail avec le système d’exploitation qui exécutera le travail lorsque les conditions sont remplies. Cela permet à Android d’exécuter le travail pour garantir une efficacité maximale sur l’appareil. Par exemple, les demandes réseau peuvent être exécutées en même temps pour s’exécuter en même temps afin de tirer le meilleur de la surcharge impliquée dans la mise en réseau.
2. L' **encapsulation du travail** &ndash; le code pour effectuer le travail en arrière-plan doit être encapsulé dans un composant discret qui peut être exécuté indépendamment de l’interface utilisateur et qui sera relativement facile à replanifier en cas d’échec de l’exécution du travail pour certains donc.

Le planificateur de travaux Android est un Framework intégré au système d’exploitation Android qui fournit une API Fluent pour simplifier la planification du travail en arrière-plan.  Le planificateur de travaux Android contient les types suivants :

- Le `Android.App.Job.JobScheduler` est un service système utilisé pour planifier, exécuter et, si nécessaire, annuler, des travaux pour le compte d’une application Android.
- Une `Android.App.Job.JobService` est une classe abstraite qui doit être étendue avec la logique qui exécutera le travail sur le thread principal de l’application. Cela signifie que le `JobService` est responsable de la façon dont le travail doit être exécuté de façon asynchrone.
- Un objet `Android.App.Job.JobInfo` contient les critères pour guider Android lorsque le travail doit s’exécuter.

Pour planifier le travail avec le planificateur de travaux Android, une application Xamarin. Android doit encapsuler le code dans une classe qui étend la classe `JobService`. `JobService` a trois méthodes de cycle de vie qui peuvent être appelées pendant la durée de vie de la tâche :

- **bool OnStartJob (paramètres JobParameters)** &ndash; cette méthode est appelée par le `JobScheduler` pour effectuer un travail et s’exécute sur le thread principal de l’application. Il incombe au `JobService` d’effectuer de façon asynchrone le travail et `true` s’il reste du travail, ou `false` si le travail est terminé.
    
    Lorsque le `JobScheduler` appelle cette méthode, il demande et conserve un wakelock d’Android pour la durée du travail. Une fois le travail terminé, il incombe au `JobService` d’indiquer à la `JobScheduler` de ce fait en appelant la méthode `JobFinished` (décrite ci-après).

- **JobFinished (paramètres JobParameters, bool needsReschedule)** &ndash; cette méthode doit être appelée par le `JobService` pour indiquer au `JobScheduler` que le travail est terminé. Si `JobFinished` n’est pas appelé, le `JobScheduler` ne supprime pas le wakelock, ce qui entraîne un vidage inutile de la batterie. 

- **bool OnStopJob (paramètres JobParameters)** &ndash; cette méthode est appelée lorsque le travail est prématurément arrêté par Android. Elle doit retourner `true` si le travail doit être replanifié en fonction des critères de nouvelle tentative (voir ci-dessous plus en détail).

Il est possible de spécifier des _contraintes_ ou des _déclencheurs_ qui contrôlent le moment où un travail peut ou doit s’exécuter. Par exemple, il est possible de contraindre un travail afin qu’il s’exécute uniquement lorsque l’appareil est en charge ou pour démarrer un travail lorsqu’une image est prise.

Ce guide explique en détail comment implémenter une classe `JobService` et la planifie avec la `JobScheduler`.

## <a name="requirements"></a>spécifications

Le planificateur de travaux Android nécessite le niveau d’API Android 21 (Android 5,0) ou une version ultérieure. 

## <a name="using-the-android-job-scheduler"></a>Utilisation du planificateur de travaux Android

L’utilisation de l’API JobScheduler Android comporte trois étapes :

1. Implémentez un type JobService pour encapsuler le travail.
2. Utilisez un objet `JobInfo.Builder` pour créer l’objet `JobInfo` qui contiendra les critères du `JobScheduler` pour exécuter le travail. 
3. Planifiez la tâche à l’aide de `JobScheduler.Schedule`.

### <a name="implement-a-jobservice"></a>Implémenter un JobService

Tout le travail effectué par la bibliothèque du planificateur de travaux Android doit être effectué dans un type qui étend la classe abstraite `Android.App.Job.JobService`. La création d’un `JobService` est très similaire à la création d’un `Service` avec l’infrastructure Android : 

1. Étendez la classe `JobService`.
2. Décorez la sous-classe avec la `ServiceAttribute` et définissez le paramètre `Name` sur une chaîne composée du nom du package et du nom de la classe (Voir l’exemple suivant).
3. Définissez la propriété `Permission` du `ServiceAttribute` sur la chaîne `android.permission.BIND_JOB_SERVICE`.
4. Substituez la méthode `OnStartJob`, en ajoutant le code pour effectuer le travail. Android appellera cette méthode sur le thread principal de l’application pour exécuter le travail. Le travail qui prendra plus de temps que quelques millisecondes doivent être effectuées sur un thread pour éviter le blocage de l’application.
5. Une fois le travail terminé, le `JobService` doit appeler la méthode `JobFinished`. Cette méthode est la façon dont `JobService` indique à l' `JobScheduler` que le travail est terminé. Si vous n’appelez pas `JobFinished`, la `JobService` entraîne des demandes superflues sur l’appareil, ce qui réduit la durée de vie de la batterie. 
6. Il est judicieux de remplacer également la méthode `OnStopJob`. Cette méthode est appelée par Android lorsque la tâche est arrêtée avant qu’elle ne soit terminée et qu’elle fournisse à l' `JobService` la possibilité de supprimer correctement les ressources. Cette méthode doit retourner `true` s’il est nécessaire de replanifier le travail, ou `false` s’il n’est pas souhaitable d’exécuter à nouveau le travail.

Le code suivant est un exemple de la `JobService` la plus simple pour une application, l’utilisation de la bibliothèque parallèle de tâches pour effectuer un travail de façon asynchrone :

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Création d’un JobInfo pour planifier un travail

Les applications Xamarin. Android n’instancient pas directement un `JobService`, mais elles transmettent un objet `JobInfo` à l' `JobScheduler`. Le `JobScheduler` instancie l’objet `JobService` demandé, en planifiant et en exécutant le `JobService` en fonction des métadonnées du `JobInfo`. Un objet `JobInfo` doit contenir les informations suivantes :

- **JobId** &ndash; il s’agit d’une valeur `int` utilisée pour identifier un travail sur le `JobScheduler`. La réutilisation de cette valeur entraîne la mise à jour de tous les travaux existants. La valeur doit être unique pour l’application. 
- **JobService** &ndash; ce paramètre est un `ComponentName` qui identifie explicitement le type que le `JobScheduler` doit utiliser pour exécuter un travail. 

Cette méthode d’extension montre comment créer un `JobInfo.Builder` avec un `Context`Android, par exemple une activité :

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Une fonctionnalité puissante du planificateur de travaux Android est la possibilité de contrôler le moment où un travail s’exécute ou dans quelles conditions un travail peut s’exécuter. Le tableau suivant décrit quelques-unes des méthodes sur `JobInfo.Builder` qui permettent à une application d’influencer lorsqu’un travail peut s’exécuter :  

|  Méthode | Description   |
|---|---|
| `SetMinimumLatency`  | Spécifie un délai (en millisecondes) à observer avant l’exécution d’un travail. |
| `SetOverridingDeadline`  | Déclare le que le travail doit exécuter avant que ce délai (en millisecondes) s’est écoulé. |
| `SetRequiredNetworkType`  | Spécifie la configuration réseau requise pour un travail. |
| `SetRequiresBatteryNotLow` | Le travail ne peut s’exécuter que lorsque l’appareil n’affiche pas d’avertissement « batterie faible » à l’utilisateur. |
| `SetRequiresCharging` | La tâche ne peut s’exécuter qu’au moment de la charge de la batterie. |
| `SetDeviceIdle` | La tâche est exécutée lorsque l’appareil est occupé. |
| `SetPeriodic` | Spécifie que le travail doit être exécuté régulièrement. |
| `SetPersisted` | Le travail doit être redémarré par le biais des redémarrages de l’appareil. | 

Le `SetBackoffCriteria` fournit des conseils sur la durée pendant laquelle le `JobScheduler` doit attendre avant de réessayer d’exécuter un travail. Les critères d’interruption se composent de deux parties : un délai en millisecondes (valeur par défaut de 30 secondes) et le type d’interruption qui doit être utilisé (parfois appelé _stratégie_ d’interruption ou _stratégie de nouvelle tentative_). Les deux stratégies sont encapsulées dans l’énumération `Android.App.Job.BackoffPolicy` :

- `BackoffPolicy.Exponential` &ndash; une stratégie d’interruption exponentielle augmente la valeur d’interruption initiale de façon exponentielle après chaque défaillance. La première fois qu’un travail échoue, la bibliothèque attend l’intervalle initial spécifié avant de replanifier la tâche (par exemple, 30 secondes). La deuxième fois que la tâche échoue, la bibliothèque attend au moins 60 secondes avant d’essayer d’exécuter la tâche. Après la troisième tentative qui a échoué, la bibliothèque attend 120 secondes, et ainsi de suite. Valeur par défaut.
- `BackoffPolicy.Linear` &ndash; cette stratégie est une interruption linéaire que la tâche doit être replanifiée pour s’exécuter à des intervalles définis (jusqu’à ce qu’elle aboutisse). L’interruption linéaire est idéale pour le travail qui doit être effectué le plus rapidement possible ou pour les problèmes qui se résolvent rapidement. 

Pour plus d’informations sur la création d’un objet `JobInfo`, consultez [la documentation de Google pour la classe `JobInfo.Builder`](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html).

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passage de paramètres à un travail via JobInfo

Les paramètres sont passés à un travail en créant un `PersistableBundle` passé avec la méthode `Job.Builder.SetExtras` :

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

La `PersistableBundle` est accessible à partir de la propriété `Android.App.Job.JobParameters.Extras` dans la méthode `OnStartJob` d’un `JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Planification d’un travail

Pour planifier un travail, une application Xamarin. Android obtient une référence au service système `JobScheduler` et appelle la méthode `JobScheduler.Schedule` avec l’objet `JobInfo` créé à l’étape précédente. `JobScheduler.Schedule` retournera immédiatement l’une des deux valeurs entières suivantes :

- **JobScheduler. ResultSuccess** &ndash; la tâche a été planifiée avec succès. 
- **JobScheduler. ResultFailure** &ndash; la tâche n’a pas pu être planifiée. Cela est généralement dû à des paramètres de `JobInfo` conflictuels.

Ce code est un exemple de planification d’un travail et de notification à l’utilisateur des résultats de la tentative de planification :

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```

### <a name="cancelling-a-job"></a>Annulation d’un travail

Il est possible d’annuler toutes les tâches qui ont été planifiées, ou simplement un travail unique à l’aide de la méthode `JobsScheduler.CancelAll()` ou de la méthode `JobScheduler.Cancel(jobId)` :

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Récapitulatif

Ce guide a expliqué comment utiliser le planificateur de travaux Android pour effectuer intelligemment le travail en arrière-plan. Il a expliqué comment encapsuler le travail à effectuer en tant que `JobService` et comment utiliser le `JobScheduler` pour planifier ce travail, en spécifiant les critères avec un `JobTrigger` et comment les échecs doivent être gérés avec un `RetryStrategy`.

## <a name="related-links"></a>Liens associés

- [Planification de travail intelligente](https://developer.android.com/topic/performance/scheduling.html)
- [Informations de référence sur l’API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Planification de travaux comme un Pro avec JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Optimisations de la batterie et de la mémoire Android-Google I/O 2016 (vidéo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
