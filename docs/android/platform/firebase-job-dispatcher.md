---
title: Répartiteur de travail Firebase
description: Ce guide explique comment planifier le travail en arrière-plan à l’aide de la bibliothèque de répartiteur de tâches Firebase de Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020243"
---
# <a name="firebase-job-dispatcher"></a>Répartiteur de travail Firebase

_Ce guide explique comment planifier le travail en arrière-plan à l’aide de la bibliothèque de répartiteur de tâches Firebase de Google._

## <a name="overview"></a>Overview

L’une des meilleures façons de conserver une application Android réactive à l’utilisateur consiste à s’assurer que le travail complexe ou à long terme est effectué en arrière-plan. Toutefois, il est important que le travail en arrière-plan n’ait pas d’impact négatif sur l’expérience de l’utilisateur avec l’appareil. 

Par exemple, un travail en arrière-plan peut interroger un site Web toutes les trois ou quatre minutes pour rechercher les modifications apportées à un jeu de données particulier. Cela semble Bénin, mais cela aurait un impact désastreux sur la durée de vie de la batterie. L’application veillera à réveiller l’appareil de façon répétée, élever le processeur à un état d’alimentation plus élevé, mettre en marche les radios, effectuer les demandes réseau, puis traiter les résultats. Il s’aggrave, car l’appareil ne se met pas immédiatement hors tension et passe à l’état de faible consommation d’énergie. Un travail en arrière-plan mal planifié peut laisser accidentellement l’appareil dans un État avec des exigences d’alimentation superflues et excessives. Cette activité apparemment innocente (interrogation d’un site Web) rendra l’appareil inutilisable dans un laps de temps relativement bref.

Android fournit les API suivantes pour faciliter l’exécution des tâches en arrière-plan, mais elles ne sont pas suffisantes pour une planification de travaux intelligente. 

- Les **[services d’intention](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; les services d’intention sont très utiles pour effectuer le travail, mais ils n’offrent aucun moyen de planifier le travail.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; ces API autorisent uniquement la planification de travail, mais n’offrent aucun moyen d’effectuer le travail. En outre, le AlarmManager autorise uniquement les contraintes de temps, ce qui signifie déclencher une alarme à un moment donné ou après l’expiration d’un certain laps de temps. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; le JobSchedule est une formidable API qui fonctionne avec le système d’exploitation pour planifier des travaux. Toutefois, il est uniquement disponible pour les applications Android qui ciblent le niveau d’API 21 ou supérieur. 
- Les **[récepteurs de diffusion](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; une application Android peuvent configurer des récepteurs de diffusion pour effectuer un travail en réponse à des événements ou intentions au niveau du système. Toutefois, les récepteurs de diffusion ne fournissent aucun contrôle sur le moment où la tâche doit être exécutée. De même, les modifications apportées au système d’exploitation Android limitent le fonctionnement des récepteurs de diffusion ou les genres de travail auxquels ils peuvent répondre. 

Il existe deux fonctionnalités clés pour effectuer efficacement des tâches en arrière-plan (parfois appelées _travail en arrière-plan_ ou _travail_) :

1. **Planifier intelligemment le travail** &ndash; il est important que lorsqu’une application travaille en arrière-plan et qu’elle le fasse comme un bon citoyen. Dans l’idéal, l’application ne doit pas demander l’exécution d’un travail. Au lieu de cela, l’application doit spécifier des conditions qui doivent être remplies pour que le travail puisse s’exécuter, puis planifier l’exécution de ce travail lorsque les conditions sont remplies. Cela permet à Android d’effectuer un travail de manière intelligente. Par exemple, les demandes réseau peuvent être exécutées en même temps pour s’exécuter en même temps afin de tirer le meilleur de la surcharge impliquée dans la mise en réseau.
2. L' **encapsulation du travail** &ndash; le code pour effectuer le travail en arrière-plan doit être encapsulé dans un composant discret qui peut être exécuté indépendamment de l’interface utilisateur et sera relativement facile à replanifier si le travail échoue pour une raison quelconque.

Le répartiteur de tâches Firebase est une bibliothèque de Google qui fournit une API Fluent pour simplifier la planification du travail en arrière-plan. Il est destiné à remplacer Google Cloud Manager. Le répartiteur de tâches Firebase se compose des API suivantes :

- Une `Firebase.JobDispatcher.JobService` est une classe abstraite qui doit être étendue avec la logique qui s’exécutera dans la tâche en arrière-plan.
- Une `Firebase.JobDispatcher.JobTrigger` déclare à quel moment le travail doit être démarré. Cela est généralement exprimé sous la forme d’une fenêtre de temps, par exemple, patientez au moins 30 secondes avant de démarrer le travail, mais exécutez le travail dans un délai de 5 minutes.
- Un `Firebase.JobDispatcher.RetryStrategy` contient des informations sur ce qui doit être fait lorsqu’un travail ne s’exécute pas correctement. La stratégie de nouvelle tentative spécifie le délai d’attente avant d’essayer de réexécuter la tâche. 
- Une `Firebase.JobDispatcher.Constraint` est une valeur facultative qui décrit une condition qui doit être remplie pour que le travail puisse s’exécuter, par exemple si l’appareil se trouve sur un réseau non limité ou s’il est facturé.
- Le `Firebase.JobDispatcher.Job` est une API qui unifie les API précédentes dans en une unité de travail qui peut être planifiée par l' `JobDispatcher`. La classe `Job.Builder` est utilisée pour instancier une `Job`.
- Un `Firebase.JobDispatcher.JobDispatcher` utilise les trois API précédentes pour planifier le travail avec le système d’exploitation et pour fournir un moyen d’annuler les travaux, si nécessaire.

Pour planifier le travail avec le répartiteur de tâches Firebase, une application Xamarin. Android doit encapsuler le code dans un type qui étend la classe `JobService`. `JobService` a trois méthodes de cycle de vie qui peuvent être appelées pendant la durée de vie de la tâche :

- **`bool OnStartJob(IJobParameters parameters)`** &ndash; cette méthode est l’emplacement où le travail se produira et doit toujours être implémenté. Il s’exécute sur le thread principal. Cette méthode retournera `true` s’il reste du travail, ou `false` si le travail est terminé. 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; cela est appelé lorsque le travail est arrêté pour une raison quelconque. Elle doit retourner `true` si le travail doit être replanifié pour plus tard.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; cette méthode est appelée lorsque l' `JobService` a terminé le travail asynchrone. 

Pour planifier un travail, l’application instancie un objet `JobDispatcher`. Ensuite, un `Job.Builder` est utilisé pour créer un objet `Job`, qui est fourni à la `JobDispatcher` qui tentera et planifiera la tâche à exécuter.

Ce guide explique comment ajouter le répartiteur de tâches Firebase à une application Xamarin. Android et comment l’utiliser pour planifier le travail en arrière-plan.

## <a name="requirements"></a>Configuration requise

Le répartiteur de tâches Firebase nécessite le niveau d’API Android 9 ou une version ultérieure. La bibliothèque de répartiteur de tâches Firebase s’appuie sur certains composants fournis par Google Play Services ; Google Play Services doit être installé sur l’appareil.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Utilisation de la bibliothèque de répartiteur de tâches Firebase dans Xamarin. Android

Pour commencer à utiliser le répartiteur de tâches Firebase, ajoutez d’abord le [package NuGet Xamarin. Firebase. JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) au projet Xamarin. Android. Dans le gestionnaire de package NuGet, recherchez le package **Xamarin. Firebase. JobDispatcher** (qui est toujours en version préliminaire).

Après avoir ajouté la bibliothèque du répartiteur de tâches Firebase, créez une classe `JobService`, puis planifiez son exécution avec une instance du `FirebaseJobDispatcher`.

### <a name="creating-a-jobservice"></a>Création d’un JobService

Tout le travail effectué par la bibliothèque de répartiteur de tâches Firebase doit être effectué dans un type qui étend la classe abstraite `Firebase.JobDispatcher.JobService`. La création d’un `JobService` est très similaire à la création d’un `Service` avec l’infrastructure Android : 

1. Étendre la classe `JobService`
2. Décorez la sous-classe avec l' `ServiceAttribute`. Bien que cela ne soit pas strictement obligatoire, il est recommandé de définir explicitement le paramètre `Name` pour faciliter le débogage du `JobService`. 
3. Ajoutez un `IntentFilter` pour déclarer le `JobService` dans **fichier AndroidManifest. xml**. Cela permet également à la bibliothèque de répartiteur de tâches Firebase de localiser et d’appeler le `JobService`.

Le code suivant est un exemple de la `JobService` la plus simple pour une application, l’utilisation de la bibliothèque parallèle de tâches pour effectuer un travail de façon asynchrone :

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>Création d’un FirebaseJobDispatcher

Avant de pouvoir planifier un travail, il est nécessaire de créer un objet `Firebase.JobDispatcher.FirebaseJobDispatcher`. La `FirebaseJobDispatcher` est responsable de la planification d’une `JobService`. L’extrait de code suivant est une façon de créer une instance de l' `FirebaseJobDispatcher`: 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Dans l’extrait de code précédent, le `GooglePlayDriver` est une classe qui aide le `FirebaseJobDispatcher` à interagir avec certaines API de planification dans Google Play Services sur l’appareil. Le paramètre `context` est un `Context`Android, tel qu’une activité. Actuellement, le `GooglePlayDriver` est la seule implémentation `IDriver` dans la bibliothèque de répartiteur de tâches Firebase. 

La liaison Xamarin. Android pour le répartiteur de tâches Firebase fournit une méthode d’extension pour créer une `FirebaseJobDispatcher` à partir de la `Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Une fois le `FirebaseJobDispatcher` instancié, il est possible de créer une `Job` et d’exécuter le code de la classe `JobService`. Le `Job` est créé par un objet `Job.Builder` et sera abordé dans la section suivante.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Création d’un Firebase. JobDispatcher. job avec Job. Builder

La classe `Firebase.JobDispatcher.Job` est responsable de l’encapsulation des métadonnées nécessaires à l’exécution d’une `JobService`. Une`Job` contient des informations telles que les contraintes qui doivent être satisfaites avant que le travail puisse s’exécuter, si la `Job` est récurrente ou les déclencheurs qui entraînent l’exécution de la tâche.  Au minimum, un `Job` doit avoir une _balise_ (une chaîne unique qui identifie le travail au `FirebaseJobDispatcher`) et le type de la `JobService` qui doit être exécutée. Le répartiteur de tâches Firebase instancie le `JobService` lorsqu’il est temps d’exécuter le travail.  Une `Job` est créée à l’aide d’une instance de la classe `Firebase.JobDispatcher.Job.JobBuilder`. 

L’extrait de code suivant est l’exemple le plus simple pour créer une `Job` à l’aide de la liaison Xamarin. Android :

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

La `Job.Builder` effectue des contrôles de validation de base sur les valeurs d’entrée du travail. Une exception est levée si le `Job.Builder` ne peut pas créer de `Job`.  Le `Job.Builder` crée un `Job` avec les valeurs par défaut suivantes :

- La durée de _vie_ d’un `Job`(la durée d’exécution planifiée) est uniquement jusqu’à ce que l’appareil redémarre &ndash; une fois que l’appareil a redémarré le `Job` est perdu.
- Un `Job` n’est pas récurrent &ndash; il ne s’exécutera qu’une seule fois.
- Une `Job` sera planifiée pour s’exécuter dès que possible.
- La stratégie de nouvelle tentative par défaut pour un `Job` consiste à utiliser une interruption _exponentielle_ (décrite plus en détail ci-dessous dans la section [définition d’un RetryStrategy](#Setting_a_RetryStrategy)).

### <a name="scheduling-a-job"></a>Planification d’un travail

Une fois la `Job`créée, elle doit être planifiée avec la `FirebaseJobDispatcher` avant son exécution. Il existe deux méthodes pour planifier une `Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

La valeur retournée par `FirebaseJobDispatcher.Schedule` sera l’une des valeurs entières suivantes :

- `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; le `Job` a été correctement planifié.
- `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; un problème inconnu s’est produit, ce qui empêchait la planification de l' `Job`.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; un `IDriver` non valide a été utilisé ou le `IDriver` n’était pas disponible. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; le `Trigger` n’est pas pris en charge.
- `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; le service n’est pas configuré correctement ou n’est pas disponible.

### <a name="configuring-a-job"></a>Configuration d’un travail

Il est possible de personnaliser un travail. Voici quelques exemples de personnalisation d’un travail :

- Le [passage de paramètres à un travail](#Passing_Parameters_to_a_Job) &ndash; un `Job` peut nécessiter des valeurs supplémentaires pour effectuer son travail, par exemple le téléchargement d’un fichier.
- [Définir des contraintes](#Setting_Constraints) &ndash; il peut être nécessaire d’exécuter un travail uniquement lorsque certaines conditions sont remplies. Par exemple, exécutez uniquement une `Job` lorsque l’appareil est en charge. 
- [Spécifiez quand un `Job` doit s’exécuter](#Setting_Job_Triggers) &ndash; le répartiteur de tâches Firebase permet aux applications de spécifier une heure à laquelle le travail doit s’exécuter.  
- [Déclarez une stratégie de nouvelle tentative pour les travaux ayant échoué](#Setting_a_RetryStrategy) &ndash; une _stratégie de nouvelle tentative_ fournit des instructions à l' `FirebaseJobDispatcher` sur ce qu’il faut faire avec `Jobs` qui ne se termine pas. 

Chacun de ces sujets sera abordé plus en informations dans les sections suivantes.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passage de paramètres à un travail

Les paramètres sont passés à un travail en créant un `Bundle` passé avec la méthode `Job.Builder.SetExtras` :

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

La `Bundle` est accessible à partir de la propriété `IJobParameters.Extras` sur la méthode `OnStartJob` :

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Définition de contraintes

Les contraintes peuvent aider à réduire les coûts ou la charge de batterie sur l’appareil. La classe `Firebase.JobDispatcher.Constraint` définit ces contraintes comme des valeurs entières :

- `Constraint.OnUnmeteredNetwork` &ndash; exécuter le travail uniquement lorsque l’appareil est connecté à un réseau non limité. Cela est utile pour empêcher l’utilisateur d’effectuer des frais de données.
- `Constraint.OnAnyNetwork` &ndash; exécuter le travail sur le réseau auquel l’appareil est connecté. S’il est spécifié avec `Constraint.OnUnmeteredNetwork`, cette valeur est prioritaire.
- `Constraint.DeviceCharging` &ndash; exécuter le travail uniquement lorsque l’appareil est en charge.

Les contraintes sont définies à l’aide de la méthode `Job.Builder.SetConstraint` : 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

Le `JobTrigger` fournit des indications au système d’exploitation sur le moment où le travail doit démarrer. Une `JobTrigger` a une _fenêtre en cours d’exécution_ qui définit une heure planifiée pour l’exécution du `Job`. La fenêtre d’exécution a une valeur de _fenêtre de démarrage_ et une valeur de _fenêtre de fin_ . La fenêtre de démarrage est le nombre de secondes que l’appareil doit attendre avant d’exécuter le travail et la valeur de la fenêtre de fin est le nombre maximal de secondes d’attente avant l’exécution de la `Job`. 

Un `JobTrigger` peut être créé avec la méthode `Firebase.Jobdispatcher.Trigger.ExecutionWindow`.  Par exemple `Trigger.ExecutionWindow(15,60)` signifie que le travail doit s’exécuter entre 15 et 60 secondes à partir du moment où il est planifié. La méthode `Job.Builder.SetTrigger` est utilisée pour 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

La `JobTrigger` par défaut pour un travail est représentée par la valeur `Trigger.Now`, qui spécifie qu’une tâche doit être exécutée dès que possible après avoir été planifiée..

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Définition d’un RetryStrategy

Le `Firebase.JobDispatcher.RetryStrategy` est utilisé pour spécifier la durée d’utilisation d’un appareil avant de tenter de réexécuter une tâche ayant échoué. Une `RetryStrategy` a une _stratégie_, qui définit l’algorithme de base de temps utilisé pour replanifier la tâche ayant échoué, et une fenêtre d’exécution qui spécifie une fenêtre dans laquelle le travail doit être planifié. Cette _fenêtre de replanification_ est définie par deux valeurs. La première valeur est le nombre de secondes d’attente avant la replanification du travail (valeur _d’interruption initiale_ ), et le deuxième nombre est le nombre maximal de secondes avant que le travail ne doive s’exécuter (valeur d’interruption _maximale_ ). 

Les deux types de stratégies de nouvelle tentative sont identifiés par ces valeurs int :

- `RetryStrategy.RetryPolicyExponential` &ndash; une stratégie d’interruption _exponentielle_ augmente la valeur d’interruption initiale de façon exponentielle après chaque défaillance. La première fois qu’un travail échoue, la bibliothèque attend la _initial intervalle spécifié avant de replanifier le travail &ndash; exemple 30 secondes. La deuxième fois que la tâche échoue, la bibliothèque attend au moins 60 secondes avant d’essayer d’exécuter la tâche. Après la troisième tentative qui a échoué, la bibliothèque attend 120 secondes, et ainsi de suite. Le `RetryStrategy` par défaut de la bibliothèque de répartiteurs de tâches Firebase est représenté par l’objet `RetryStrategy.DefaultExponential`. Il a une interruption initiale de 30 secondes et une interruption maximale de 3600 secondes.
- `RetryStrategy.RetryPolicyLinear` &ndash; cette stratégie est une interruption _linéaire_ que la tâche doit être replanifiée pour s’exécuter à des intervalles définis (jusqu’à ce qu’elle aboutisse). L’interruption linéaire est idéale pour le travail qui doit être effectué le plus rapidement possible ou pour les problèmes qui se résolvent rapidement. La bibliothèque de répartiteur de tâches Firebase définit un `RetryStrategy.DefaultLinear` qui a une fenêtre de replanification d’au moins 30 secondes et jusqu’à 3600 secondes.

Il est possible de définir une `RetryStrategy` personnalisée avec la méthode `FirebaseJobDispatcher.NewRetryStrategy`. Il accepte trois paramètres :

1. `int policy` &ndash; la _stratégie_ est l’une des valeurs `RetryStrategy` précédentes, `RetryStrategy.RetryPolicyLinear`ou `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds` &ndash; l’interruption _initiale_ est un délai, en secondes, requis avant d’essayer de réexécuter la tâche. La valeur par défaut est de 30 secondes. 
3. `int maximumBackoffSeconds` &ndash; la valeur d’interruption _maximale_ déclare le nombre maximal de secondes avant d’essayer de réexécuter la tâche. La valeur par défaut est 3600 secondes. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Annulation d’un travail

Il est possible d’annuler toutes les tâches qui ont été planifiées, ou simplement un travail unique à l’aide de la méthode `FirebaseJobDispatcher.CancelAll()` ou de la méthode `FirebaseJobDispatcher.Cancel(string)` :

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

Les deux méthodes retournent une valeur entière :

- `FirebaseJobDispatcher.CancelResultSuccess` &ndash; la tâche a été annulée avec succès.
- `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; une erreur a empêché l’annulation du travail.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; la `FirebaseJobDispatcher` ne peut pas annuler le travail, car aucun `IDriver` valide n’est disponible.

## <a name="summary"></a>Résumé

Ce guide a expliqué comment utiliser le répartiteur de tâches Firebase pour effectuer intelligemment le travail en arrière-plan. Il a expliqué comment encapsuler le travail à effectuer en tant que `JobService` et comment utiliser le `FirebaseJobDispatcher` pour planifier ce travail, en spécifiant les critères avec un `JobTrigger` et comment les échecs doivent être gérés avec un `RetryStrategy`.

## <a name="related-links"></a>Liens connexes

- [Xamarin. Firebase. JobDispatcher sur NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [Firebase-Job-répartiteur sur GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin. Firebase. JobDispatcher, liaison](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Planification de travail intelligente](https://developer.android.com/topic/performance/scheduling.html)
- [Optimisations de la batterie et de la mémoire Android-Google I/O 2016 (vidéo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
