---
title: Planificateur de travaux Android
description: Ce guide discute de la façon de planifier le travail de fond à l’aide de l’API Android Job Scheduler.
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 10d2ae6ac35f02d75ef6e04a0531ec3f5dafd668
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291834"
---
# <a name="android-job-scheduler"></a>Planificateur de travaux Android

_Ce guide discute de la façon de planifier le travail de fond en utilisant l’API Android Job Scheduler, qui est disponible sur les appareils Android fonctionnant sous Android 5.0 (niveau API 21) et plus élevé._

## <a name="overview"></a>Vue d’ensemble 

Une des meilleures façons de garder une application Android sensible à l’utilisateur est de s’assurer que le travail complexe ou de longue durée est effectué en arrière-plan. Cependant, il est important que le travail d’arrière-plan n’aura pas d’impact négatif sur l’expérience de l’utilisateur avec l’appareil. 

Par exemple, un travail d’arrière-plan peut sonder un site Web toutes les trois ou quatre minutes pour demander des modifications à un jeu de données particulier. Cela semble bénin, mais il aurait un impact désastreux sur la durée de vie de la batterie. L’application réveillera à plusieurs reprises l’appareil, élèvera le processeur à un état de puissance plus élevé, alimentera les radios, fera les demandes du réseau, puis traitera les résultats. Il s’aggrave parce que l’appareil ne sera pas immédiatement à l’énergie vers le bas et revenir à l’état de faible puissance au ralenti. Des travaux d’arrière-plan mal planifiés peuvent par inadvertance maintenir l’appareil dans un état où les besoins en énergie inutiles et excessifs sont nécessaires. Cette activité apparemment innocente (sondage d’un site Web) rendra l’appareil inutilisable dans un laps de temps relativement court.

Android fournit les API suivantes pour aider à effectuer le travail en arrière-plan, mais par eux-mêmes, ils ne sont pas suffisants pour la planification d’emploi intelligent. 

- **[Les services](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; d’intention sont excellents pour effectuer le travail, mais ils ne fournissent aucun moyen de planifier le travail.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Ces API ne permettent que de programmer les travaux, mais ne fournissent aucun moyen d’effectuer réellement le travail. En outre, l’AlarmManager ne permet que des contraintes basées sur le temps, ce qui signifie soulever une alarme à un certain moment ou après une certaine période de temps s’est écoulé. 
- **[Récepteurs](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; de diffusion Une application Android peut configurer des récepteurs de diffusion pour effectuer des travaux en réponse à des événements ou des intentions à l’échelle du système. Cependant, les récepteurs de diffusion ne donnent aucun contrôle sur le moment où le travail doit être exécuté. Les changements dans le système d’exploitation Android limiteront également le fonctionnement des récepteurs de diffusion, ou les types de travail auxquels ils peuvent répondre. 

Il existe deux caractéristiques clés pour effectuer efficacement le travail de fond (parfois appelé un _travail de fond_ ou un _emploi_) :

1. **Planifier intelligemment le travail** &ndash; Il est important que lorsqu’une demande fonctionne en arrière-plan, elle le fait en tant que bon citoyen. Idéalement, la demande ne devrait pas exiger qu’un emploi soit exécuté. Au lieu de cela, la demande devrait spécifier les conditions qui doivent être remplies pour le moment où le travail peut s’exécuter, puis planifier ce travail avec le système d’exploitation qui effectuera le travail lorsque les conditions sont remplies. Cela permet à Android d’exécuter le travail pour assurer une efficacité maximale sur l’appareil. Par exemple, les demandes de réseau peuvent être traitées en même temps pour faire un usage maximal des frais généraux impliqués dans le réseautage.
2. **Encapsulant le travail** &ndash; Le code pour effectuer le travail d’arrière-plan doit être encapsulé dans un composant discret qui peut être exécuté indépendamment de l’interface utilisateur et sera relativement facile à rééchelonner si le travail ne parvient pas à terminer pour une raison quelconque.

L’Android Job Scheduler est un cadre intégré au système d’exploitation Android qui fournit une API fluide pour simplifier le travail de fond de planification.  L’Android Job Scheduler se compose des types suivants :

- Il `Android.App.Job.JobScheduler` s’agit d’un service système qui est utilisé pour planifier, exécuter, et si nécessaire annuler, des emplois pour le compte d’une application Android.
- Une `Android.App.Job.JobService` classe abstraite qui doit être étendue avec la logique qui exécutera le travail sur le fil principal de l’application. Cela signifie `JobService` que le est responsable de la façon dont le travail doit être effectué asynchrone.
- Un `Android.App.Job.JobInfo` objet contient les critères pour guider Android lorsque le travail doit s’exécuter.

Pour planifier le travail avec l’Android Job Scheduler, une application Xamarin.Android `JobService` doit encapsuler le code dans une classe qui étend la classe. `JobService`a trois méthodes de cycle de vie qui peuvent être appelées au cours de la durée du travail:

- **bool OnStartJob (Paramètres JobParameters)** &ndash; Cette méthode `JobScheduler` est appelée par le travail pour effectuer, et fonctionne sur le fil principal de l’application. Il incombe à `JobService` l’idée d’effectuer asynchronement le travail et le retour `true` s’il reste du travail, ou `false` si le travail est fait.
    
    Lorsque `JobScheduler` les appels de cette méthode, il demandera et conserver un wakelock d’Android pour la durée du travail. Lorsque le travail est terminé, il `JobService` est `JobScheduler` de la responsabilité `JobFinished` de dire le fait par appel la méthode (décrit ensuite).

- **JobFinished (JobParameters paramètres, bool needsReschedule)** &ndash; Cette méthode `JobService` doit `JobScheduler` être appelée par le pour dire que le travail est fait. Si `JobFinished` ce n’est pas appelé, le `JobScheduler` ne sera pas enlever le wakelock, provoquant un vidange inutile de la batterie. 

- **bool OnStopJob (Paramètres JobParameters)** &ndash; Cela s’appelle lorsque le travail est prématurément arrêté par Android. Il devrait `true` revenir si le travail doit être reporté en fonction des critères de nouvelle tentative (discuté ci-dessous plus en détail).

Il est possible de spécifier les _contraintes_ ou _les déclencheurs_ qui contrôleront lorsqu’un emploi peut ou doit s’exécuter. Par exemple, il est possible de limiter un travail de sorte qu’il ne s’exécute que lorsque l’appareil est en charge ou de commencer un emploi lors d’une photo est prise.

Ce guide discutera en détail `JobService` de la façon `JobScheduler`de mettre en œuvre une classe et de le programmer avec le .

## <a name="requirements"></a>Spécifications

L’Android Job Scheduler nécessite Android API niveau 21 (Android 5.0) ou plus. 

## <a name="using-the-android-job-scheduler"></a>Utilisation de l’Android Job Scheduler

Il ya trois étapes pour l’utilisation de l’API Android JobScheduler:

1. Implémentez un type JobService pour encapsuler le travail.
2. Utilisez `JobInfo.Builder` un objet `JobInfo` pour créer l’objet `JobScheduler` qui tiendra les critères pour l’exécution du travail. 
3. Planifiez le `JobScheduler.Schedule`travail à l’aide de .

### <a name="implement-a-jobservice"></a>Mettre en œuvre un service d’emploi

Tout le travail effectué par la bibliothèque Android Job Scheduler doit être effectué dans un type qui étend la `Android.App.Job.JobService` classe abstraite. Création `JobService` d’un est `Service` très similaire à la création d’un avec le cadre Android: 

1. Prolongez `JobService` la classe.
2. Décorer la sous-classe avec le `ServiceAttribute` et définir le `Name` paramètre à une chaîne qui est composé du nom du paquet et le nom de la classe (voir l’exemple suivant).
3. Réglez `Permission` la `ServiceAttribute` propriété `android.permission.BIND_JOB_SERVICE`sur la chaîne .
4. Remplacer la `OnStartJob` méthode, en ajoutant le code pour effectuer le travail. Android invoquera cette méthode sur le fil principal de l’application pour exécuter le travail. Travail qui prendra plus de temps que quelques millisecondes doivent être effectuées sur un thread pour éviter de bloquer l’application.
5. Lorsque le travail est `JobService` terminé, `JobFinished` le doit appeler la méthode. Cette méthode `JobService` est `JobScheduler` la façon dont indique le travail est fait. Le défaut `JobFinished` d’appeler `JobService` entraînera la mise en place de demandes inutiles sur l’appareil, réduisant la durée de vie de la batterie. 
6. C’est une bonne idée `OnStopJob` de passer outre à la méthode. Cette méthode est appelée par Android lorsque le travail est `JobService` arrêté avant qu’il ne soit terminé et fournit la possibilité de disposer correctement de toutes les ressources. Cette méthode `true` devrait revenir s’il est nécessaire de `false` reporter le travail, ou s’il n’est pas souhaitable de ré-exécuter le travail.

Le code suivant est un `JobService` exemple du plus simple pour une application, en utilisant le TPL pour effectuer asynchroneously quelques travaux:

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

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>Création d’un JobInfo pour planifier un emploi

Xamarin.Android applications ne pas `JobService` instantané un directement, au `JobInfo` lieu `JobScheduler`de cela, ils passeront un objet à la . Le `JobScheduler` sera instantané de `JobService` l’objet demandé, la planification et l’exécution des `JobService` métadonnées en fonction des métadonnées dans le `JobInfo`. Un `JobInfo` objet doit contenir les informations suivantes :

- **JobId** &ndash; c’est une `int` valeur qui est `JobScheduler`utilisé pour identifier un emploi à la . La réutilation de cette valeur mettra à jour tous les emplois existants. La valeur doit être unique pour l’application. 
- **JobService** &ndash; ce paramètre est un `ComponentName` qui `JobScheduler` identifie explicitement le type que le devrait utiliser pour exécuter un emploi. 

Cette méthode d’extension démontre `JobInfo.Builder` comment `Context`créer un avec un Android , comme une activité:

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

Une caractéristique puissante de l’Android Job Scheduler est la capacité de contrôler quand un emploi fonctionne ou dans quelles conditions un emploi peut exécuter. Le tableau suivant décrit quelques-unes des méthodes qui `JobInfo.Builder` permettent à une application d’influencer lorsqu’un emploi peut s’exécuter :  

|  Méthode | Description   |
|---|---|
| `SetMinimumLatency`  | Spécifie qu’un délai (en millisecondes) qui doit être observé avant qu’un travail soit exécuté. |
| `SetOverridingDeadline`  | Déclare que le travail doit fonctionner avant ce temps (en millisecondes) s’est écoulé. |
| `SetRequiredNetworkType`  | Spécifie les exigences du réseau pour un emploi. |
| `SetRequiresBatteryNotLow` | Le travail ne peut s’exécuter que lorsque l’appareil n’affiche pas un avertissement de « batterie basse » à l’utilisateur. |
| `SetRequiresCharging` | Le travail ne peut fonctionner que lorsque la batterie est en charge. |
| `SetDeviceIdle` | Le travail s’exécutera lorsque l’appareil est occupé. |
| `SetPeriodic` | Précise que le travail doit être exécuté régulièrement. |
| `SetPersisted` | Le travail devrait périsist à travers les redémarrages de l’appareil. | 

Le `SetBackoffCriteria` fournit quelques conseils `JobScheduler` sur combien de temps le devrait attendre avant d’essayer d’exécuter un emploi à nouveau. Il y a deux parties aux critères de backoff : un retard en millisecondes (valeur par défaut de 30 secondes) et le type de recul qui devrait être utilisé (parfois appelé la _politique de backoff_ ou la _politique de retentification_). Les deux politiques sont encapsulées dans l’enum `Android.App.Job.BackoffPolicy` :

- `BackoffPolicy.Exponential`&ndash; Une politique de backoff exponentielle augmentera la valeur initiale de backoff de façon exponentielle après chaque échec. La première fois qu’un emploi échoue, la bibliothèque attendra l’intervalle initial qui est spécifié avant de reporter le travail - exemple 30 secondes. La deuxième fois que le travail échoue, la bibliothèque attendra au moins 60 secondes avant d’essayer d’exécuter le travail. Après la troisième tentative ratée, la bibliothèque attendra 120 secondes, et ainsi de suite. Il s’agit de la valeur par défaut.
- `BackoffPolicy.Linear`&ndash; Cette stratégie est un backoff linéaire que le travail doit être reporté pour fonctionner à intervalles fixes (jusqu’à ce qu’il réussisse). Le backoff linéaire est le mieux adapté pour les travaux qui doivent être terminés dès que possible ou pour les problèmes qui se résoudront rapidement. 

Pour plus de `JobInfo` détails sur la création d’un objet, [s’il `JobInfo.Builder` ](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)vous plaît lire la documentation de Google pour la classe .

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>Passer les paramètres à un travail via le JobInfo

Les paramètres sont transmis à `PersistableBundle` un emploi en `Job.Builder.SetExtras` créant un qui est transmis avec la méthode:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

Le `PersistableBundle` est accessible `Android.App.Job.JobParameters.Extras` à partir `OnStartJob` de `JobService`la propriété dans la méthode d’un :

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>Planification d’un emploi

Pour planifier un travail, une application Xamarin.Android `JobScheduler` recevra une `JobScheduler.Schedule` référence au `JobInfo` service système et appellera la méthode avec l’objet qui a été créé dans l’étape précédente. `JobScheduler.Schedule`reviendra immédiatement avec l’une des deux valeurs integer:

- **JobScheduler.ResultSuccess** &ndash; Le travail a été planifié avec succès. 
- **JobScheduler.ResultFailure** &ndash; Le travail n’a pas pu être programmé. Ceci est généralement causé `JobInfo` par des paramètres contradictoires.

Ce code est un exemple de planification d’un emploi et d’aviser l’utilisateur des résultats de la tentative de planification :

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

### <a name="cancelling-a-job"></a>Annulation d’un emploi

Il est possible d’annuler tous les emplois qui ont `JobsScheduler.CancelAll()` été programmés, ou tout simplement un seul emploi en utilisant la méthode ou la `JobScheduler.Cancel(jobId)` méthode:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>Récapitulatif

Ce guide a discuté de la façon d’utiliser l’Android Job Scheduler pour effectuer intelligemment le travail en arrière-plan. Il a discuté de la façon d’encapsuler le travail à effectuer en tant que un `JobService` et comment utiliser le `JobScheduler` pour planifier ce travail, en précisant les critères avec un `JobTrigger` et comment les échecs devraient être traités avec un `RetryStrategy`.

## <a name="related-links"></a>Liens connexes

- [Emploi intelligent-Scheduling](https://developer.android.com/topic/performance/scheduling.html)
- [Référence de l’API JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [Planification d’emplois comme un pro avec JobScheduler](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android Battery and Memory Optimizations - Google I/O 2016 (vidéo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler - René Ruppert](https://www.youtube.com/watch?v=aSjBBPYjelE)
