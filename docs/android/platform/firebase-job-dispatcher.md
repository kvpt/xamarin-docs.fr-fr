---
title: Répartiteur de travail Firebase
description: Ce guide discute de la façon de planifier le travail d’arrière-plan à l’aide de la bibliothèque Firebase Job Dispatcher de Google.
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020243"
---
# <a name="firebase-job-dispatcher"></a>Répartiteur de travail Firebase

_Ce guide discute de la façon de planifier le travail d’arrière-plan à l’aide de la bibliothèque Firebase Job Dispatcher de Google._

## <a name="overview"></a>Vue d’ensemble

Une des meilleures façons de garder une application Android sensible à l’utilisateur est de s’assurer que le travail complexe ou de longue durée est effectué en arrière-plan. Cependant, il est important que le travail d’arrière-plan n’aura pas d’impact négatif sur l’expérience de l’utilisateur avec l’appareil. 

Par exemple, un travail d’arrière-plan peut sonder un site Web toutes les trois ou quatre minutes pour demander des modifications à un jeu de données particulier. Cela semble bénin, mais il aurait un impact désastreux sur la durée de vie de la batterie. L’application réveillera à plusieurs reprises l’appareil, élèvera le processeur à un état de puissance plus élevé, alimentera les radios, fera les demandes du réseau, puis traitera les résultats. Il s’aggrave parce que l’appareil ne sera pas immédiatement à l’énergie vers le bas et revenir à l’état de faible puissance au ralenti. Des travaux d’arrière-plan mal planifiés peuvent par inadvertance maintenir l’appareil dans un état où les besoins en énergie inutiles et excessifs sont nécessaires. Cette activité apparemment innocente (sondage d’un site Web) rendra l’appareil inutilisable dans un laps de temps relativement court.

Android fournit les API suivantes pour aider à effectuer le travail en arrière-plan, mais par eux-mêmes, ils ne sont pas suffisants pour la planification d’emploi intelligent. 

- **[Les services](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; d’intention sont excellents pour effectuer le travail, mais ils ne fournissent aucun moyen de planifier le travail.
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; Ces API ne permettent que de programmer les travaux, mais ne fournissent aucun moyen d’effectuer réellement le travail. En outre, l’AlarmManager ne permet que des contraintes basées sur le temps, ce qui signifie soulever une alarme à un certain moment ou après une certaine période de temps s’est écoulé. 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; Le JobSchedule est un excellent API qui travaille avec le système d’exploitation pour planifier des travaux. Cependant, il n’est disponible que pour les applications Android qui ciblent le niveau API 21 ou plus. 
- **[Récepteurs](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; de diffusion Une application Android peut configurer des récepteurs de diffusion pour effectuer des travaux en réponse à des événements ou des intentions à l’échelle du système. Cependant, les récepteurs de diffusion ne donnent aucun contrôle sur le moment où le travail doit être exécuté. Les changements dans le système d’exploitation Android limiteront également le fonctionnement des récepteurs de diffusion, ou les types de travail auxquels ils peuvent répondre. 

Il existe deux caractéristiques clés pour effectuer efficacement le travail de fond (parfois appelé un _travail de fond_ ou un _emploi_) :

1. **Planifier intelligemment le travail** &ndash; Il est important que lorsqu’une demande fonctionne en arrière-plan, elle le fait en tant que bon citoyen. Idéalement, la demande ne devrait pas exiger qu’un emploi soit exécuté. Au lieu de cela, la demande doit spécifier les conditions qui doivent être remplies pour le moment où le travail peut s’exécuter, puis planifier ce travail pour exécuter lorsque les conditions sont remplies. Cela permet à Android d’effectuer intelligemment le travail. Par exemple, les demandes de réseau peuvent être traitées en même temps pour faire un usage maximal des frais généraux impliqués dans le réseautage.
2. **Encapsulant le travail** &ndash; Le code pour effectuer le travail d’arrière-plan doit être encapsulé dans un composant discret qui peut être exécuté indépendamment de l’interface utilisateur et sera relativement facile à rééchelonner si le travail ne parvient pas à terminer pour une raison quelconque.

Le Firebase Job Dispatcher est une bibliothèque de Google qui fournit une API fluide pour simplifier le travail de base de planification. Il est destiné à être le remplacement de Google Cloud Manager. Le répartiteur d’emploi Firebase se compose des API suivantes :

- A `Firebase.JobDispatcher.JobService` est une classe abstraite qui doit être étendue avec la logique qui fonctionnera dans le travail de fond.
- A `Firebase.JobDispatcher.JobTrigger` déclare quand le travail doit être commencé. Ceci est généralement exprimé comme une fenêtre de temps, par exemple, attendre au moins 30 secondes avant de commencer le travail, mais exécuter le travail dans les 5 minutes.
- A `Firebase.JobDispatcher.RetryStrategy` contient des informations sur ce qui doit être fait lorsqu’un travail ne s’exécute pas correctement. La stratégie de retentir précise combien de temps attendre avant d’essayer de diriger le travail à nouveau. 
- A `Firebase.JobDispatcher.Constraint` est une valeur facultative qui décrit une condition qui doit être remplie avant que le travail puisse s’exécuter, comme l’appareil est sur un réseau non prisé ou de charge.
- Il `Firebase.JobDispatcher.Job` s’agit d’une API qui unifie les API précédentes `JobDispatcher`à un unité de travail qui peut être programmé par le . La `Job.Builder` classe est utilisée pour `Job`instantanéate a .
- A `Firebase.JobDispatcher.JobDispatcher` utilise les trois API précédentes pour planifier le travail avec le système d’exploitation et pour fournir un moyen d’annuler des emplois, si nécessaire.

Pour planifier le travail avec le Répartiteur d’emploi Firebase, une application Xamarin.Android doit encapsuler le code dans un type qui étend la `JobService` classe. `JobService`a trois méthodes de cycle de vie qui peuvent être appelées au cours de la durée du travail:

- **`bool OnStartJob(IJobParameters parameters)`**&ndash; Cette méthode est l’endroit où le travail se déroulera et doit toujours être mis en œuvre. Il fonctionne sur le fil principal. Cette méthode `true` reviendra s’il reste `false` du travail, ou si le travail est fait. 
- **`bool OnStopJob(IJobParameters parameters)`**&ndash; C’est ce qu’on appelle lorsque le travail est arrêté pour une raison quelconque. Il devrait `true` revenir si le travail doit être reporté pour plus tard.
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`**&ndash; Cette méthode est `JobService` appelée lorsque le a terminé tout travail asynchrone. 

Pour planifier un travail, l’application `JobDispatcher` instantanément un objet. Ensuite, `Job.Builder` a est utilisé `Job` pour créer un `JobDispatcher` objet, qui est fourni à ce qui va essayer de planifier le travail pour exécuter.

Ce guide discutera de la façon d’ajouter le répartiteur d’emploi Firebase à une application Xamarin.Android et l’utiliser pour planifier le travail de fond.

## <a name="requirements"></a>Spécifications

Le répartiteur d’emploi Firebase nécessite Android API niveau 9 ou plus. La bibliothèque Firebase Job Dispatcher s’appuie sur certains composants fournis par Google Play Services; l’appareil doit faire installer Google Play Services.

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>Utilisation de la bibliothèque de répartiteur d’emplois Firebase à Xamarin.Android

Pour commencer avec le Firebase Job Dispatcher, ajoutez d’abord le [forfait Xamarin.Firebase.JobDispatcher NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher) au projet Xamarin.Android. Recherchez le gestionnaire de forfait NuGet pour le forfait **Xamarin.Firebase.JobDispatcher** (qui est toujours en pré-version).

Après avoir ajouté la bibliothèque Firebase `JobService` Job Dispatcher, créez une classe `FirebaseJobDispatcher`et planifiez-la pour fonctionner avec une instance de la .

### <a name="creating-a-jobservice"></a>Création d’un service d’emploi

Tout le travail effectué par la bibliothèque Firebase Job Dispatcher doit être effectué dans un type qui étend la `Firebase.JobDispatcher.JobService` classe abstraite. Création `JobService` d’un est `Service` très similaire à la création d’un avec le cadre Android: 

1. Étendre `JobService` la classe
2. Décorer la sous-classe avec le `ServiceAttribute`. Bien qu’il ne soit pas strictement `Name` nécessaire, il est `JobService`recommandé de définir explicitement le paramètre pour aider à débogage le . 
3. Ajouter `IntentFilter` un pour `JobService` déclarer le dans l’AndroidManifest.xml . **AndroidManifest.xml** Cela aidera également la bibliothèque Firebase Job `JobService`Dispatcher localiser et invoquer le .

Le code suivant est un `JobService` exemple du plus simple pour une application, en utilisant le TPL pour effectuer asynchroneously quelques travaux:

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

### <a name="creating-a-firebasejobdispatcher"></a>Création d’une base de feuJobDispatcher

Avant que des travaux puissent être programmés, il est nécessaire de créer un `Firebase.JobDispatcher.FirebaseJobDispatcher` objet. Le `FirebaseJobDispatcher` est responsable `JobService`de la planification d’un . L’extrait de code suivant est une façon `FirebaseJobDispatcher`de créer une instance de la : 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

Dans l’extrait de code `GooglePlayDriver` précédent, la `FirebaseJobDispatcher` classe est qui aide à interagir avec certaines des API de planification dans Google Play Services sur l’appareil. Le `context` paramètre `Context`est n’importe quel Android , comme une activité. Actuellement, `GooglePlayDriver` la `IDriver` seule mise en œuvre dans la bibliothèque Firebase Job Dispatcher. 

La liaison Xamarin.Android pour le Firebase Job Dispatcher `FirebaseJobDispatcher` fournit `Context`une méthode d’extension pour créer un à partir de la : 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

Une `FirebaseJobDispatcher` fois que le a été instantané, `Job` il est possible `JobService` de créer un et d’exécuter le code dans la classe. Le `Job` est créé `Job.Builder` par un objet et sera discuté dans la section suivante.

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>Création d’un Firebase.JobDispatcher.Job avec le Job.Builder

La `Firebase.JobDispatcher.Job` classe est responsable de l’encapsulation `JobService`des méta-données nécessaires pour exécuter un . A`Job` contient des informations telles que toute contrainte qui doit `Job` être remplie avant que le travail puisse s’exécuter, si le est récurrent, ou tout déclencheur qui fera exécuter le travail.  Au strict minimum, `Job` un doit avoir une _étiquette_ (une chaîne `FirebaseJobDispatcher`unique qui identifie `JobService` le travail à la ) et le type de celui qui doit être exécuté. Le répartiteur d’emploi Firebase `JobService` instantanément le moment où il est temps de diriger le travail.  A `Job` est créé en utilisant `Firebase.JobDispatcher.Job.JobBuilder` un exemple de la classe. 

L’extrait de code suivant est l’exemple le `Job` plus simple de la façon de créer une liaison Xamarin.Android:

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

Le `Job.Builder` effectuera quelques vérifications de validation de base sur les valeurs d’entrée pour le travail. Une exception sera lancée s’il `Job.Builder` n’est pas possible pour le de créer un `Job`.  Le `Job.Builder` créera `Job` un avec les défauts suivants :

- La `Job`durée de _vie_ d’un 's (combien de temps &ndash; il sera prévu `Job` d’exécuter) est seulement jusqu’à ce que l’appareil redémarre une fois que l’appareil redémarre le est perdu.
- A `Job` n’est pas récurrent, &ndash; il ne fonctionnera qu’une seule fois.
- A `Job` sera prévu pour fonctionner dès que possible.
- La stratégie de retry par défaut pour un `Job` est d’utiliser un _backoff exponentiel_ (discuté sur plus de détails ci-dessous dans la section Setting a [RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>Planification d’un emploi

Après la `Job`création de la , `FirebaseJobDispatcher` il doit être programmé avec l’avant qu’il ne soit exécuté. Il existe deux méthodes `Job`pour planifier un :

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

La valeur `FirebaseJobDispatcher.Schedule` retournée par sera l’une des valeurs suivantes integer:

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; Le `Job` a été prévu avec succès.
- `FirebaseJobDispatcher.ScheduleResultUnknownError`&ndash; Un problème inconnu s’est produit qui a empêché le `Job` fait d’être programmé.
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`&ndash; Un `IDriver` invalide a `IDriver` été utilisé ou le n’était pas disponible en quelque sorte. 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash; Le `Trigger` n’a pas été soutenu.
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash; Le service n’est pas configuré correctement ou n’est pas disponible.

### <a name="configuring-a-job"></a>Configurer un emploi

Il est possible de personnaliser un travail. Voici quelques exemples de la façon dont un emploi peut être personnalisé :

- [La transmission de paramètres à un emploi](#Passing_Parameters_to_a_Job) &ndash; A `Job` peut nécessiter des valeurs supplémentaires pour effectuer son travail, par exemple le téléchargement d’un fichier.
- [Contraintes définies](#Setting_Constraints) &ndash; Il peut être nécessaire de ne faire un travail que lorsque certaines conditions sont remplies. Par exemple, exécutez un `Job` seul lorsque l’appareil est en charge. 
- [Spécifier quand `Job` un devrait exécuter](#Setting_Job_Triggers) &ndash; Le répartiteur d’emploi Firebase permet aux applications de spécifier un moment où le travail doit s’exécuter.  
- [Déclarer une stratégie de réessayage pour les emplois défaillants](#Setting_a_RetryStrategy) &ndash; Une _stratégie de réessayage_ fournit des conseils sur ce `FirebaseJobDispatcher` qu’il faut faire avec `Jobs` cela ne parviennent pas à terminer. 

Chacun de ces sujets sera discuté davantage dans les sections suivantes.

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>Passer les paramètres à un emploi

Les paramètres sont transmis à `Bundle` un emploi en `Job.Builder.SetExtras` créant un qui est transmis avec la méthode:

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

Le `Bundle` est accessible `IJobParameters.Extras` à partir `OnStartJob` de la propriété sur la méthode:

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>Définir des contraintes

Les contraintes peuvent aider à réduire les coûts ou la vidange de la batterie sur l’appareil. La `Firebase.JobDispatcher.Constraint` classe définit ces contraintes comme des valeurs integer :

- `Constraint.OnUnmeteredNetwork`&ndash; Exécutez le travail lorsque l’appareil est connecté à un réseau non prisé. Ceci est utile pour empêcher l’utilisateur d’encourir des frais de données.
- `Constraint.OnAnyNetwork`&ndash; Exécutez le travail sur n’importe quel réseau de l’appareil est connecté à. Si spécifié `Constraint.OnUnmeteredNetwork`avec, cette valeur prendra la priorité.
- `Constraint.DeviceCharging`&ndash; Exécutez le travail seulement lorsque l’appareil est en charge.

Les contraintes sont `Job.Builder.SetConstraint` définies avec la méthode : 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

Le `JobTrigger` fournit des conseils au système d’exploitation sur le moment où le travail devrait commencer. A `JobTrigger` dispose d’une _fenêtre d’exécution_ qui `Job` définit un heure prévue pour le moment où le devrait s’exécuter. La fenêtre d’exécution a une valeur _de fenêtre de démarrage_ et une valeur de fenêtre de _fin._ La fenêtre de départ est le nombre de secondes que l’appareil doit attendre avant d’exécuter `Job`le travail et la valeur de la fenêtre de fin est le nombre maximum de secondes à attendre avant d’exécuter le . 

A `JobTrigger` peut être `Firebase.Jobdispatcher.Trigger.ExecutionWindow` créé avec la méthode.  Par `Trigger.ExecutionWindow(15,60)` exemple, le travail doit fonctionner entre 15 et 60 secondes à partir du moment où il est prévu. La `Job.Builder.SetTrigger` méthode est utilisée pour 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

Le `JobTrigger` défaut d’emploi est représenté `Trigger.Now`par la valeur , qui spécifie qu’un emploi soit exécuté dès que possible après avoir été programmé.

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>Définir une retryStrategy

Le `Firebase.JobDispatcher.RetryStrategy` est utilisé pour spécifier combien d’un retard un appareil doit utiliser avant d’essayer de ré-exécuter un emploi échoué. A `RetryStrategy` a une _politique_, qui définit quel algorithme de base de temps sera utilisé pour reprogrammer le travail raté, et une fenêtre d’exécution qui spécifie une fenêtre dans laquelle le travail doit être programmé. Cette _fenêtre de rééchelonnement_ est définie par deux valeurs. La première valeur est le nombre de secondes à attendre avant de reporter le travail (la valeur _initiale de backoff),_ et le deuxième nombre est le nombre maximum de secondes avant que le travail doit fonctionner (la valeur _maximale de backoff)._ 

Les deux types de politiques de retentie sont identifiés par ces valeurs int:

- `RetryStrategy.RetryPolicyExponential`&ndash; Une politique _de backoff exponentielle_ augmentera la valeur initiale de backoff de façon exponentielle après chaque échec. La première fois qu’un emploi échoue, la bibliothèque attendra l’intervalle _initial &ndash; qui est spécifié avant de reporter l’exemple du travail 30 secondes. La deuxième fois que le travail échoue, la bibliothèque attendra au moins 60 secondes avant d’essayer d’exécuter le travail. Après la troisième tentative ratée, la bibliothèque attendra 120 secondes, et ainsi de suite. La `RetryStrategy` valeur par défaut de la bibliothèque Firebase Job Dispatcher est représentée par l’objet. `RetryStrategy.DefaultExponential` Il a un backoff initial de 30 secondes et un backoff maximum de 3600 secondes.
- `RetryStrategy.RetryPolicyLinear`&ndash; Cette stratégie est un _backoff linéaire_ que le travail doit être reporté pour fonctionner à intervalles fixes (jusqu’à ce qu’il réussisse). Le backoff linéaire est le mieux adapté pour les travaux qui doivent être terminés dès que possible ou pour les problèmes qui se résoudront rapidement. La bibliothèque Firebase Job Dispatcher définit une `RetryStrategy.DefaultLinear` fenêtre de rééchelonnement d’au moins 30 secondes et jusqu’à 3600 secondes.

Il est possible de `RetryStrategy` définir `FirebaseJobDispatcher.NewRetryStrategy` une coutume avec la méthode. Il faut trois paramètres :

1. `int policy`&ndash; La _politique_ est l’une des valeurs précédentes, `RetryStrategy` `RetryStrategy.RetryPolicyLinear`, ou `RetryStrategy.RetryPolicyExponential`.
2. `int initialBackoffSeconds`&ndash; Le _backoff initial_ est un retard, en quelques secondes, qui est nécessaire avant d’essayer d’exécuter le travail à nouveau. La valeur par défaut pour cela est de 30 secondes. 
3. `int maximumBackoffSeconds`&ndash; La valeur _maximale de backoff_ déclare le nombre maximum de secondes à retarder avant d’essayer d’exécuter le travail à nouveau. La valeur par défaut est de 3600 secondes. 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>Annulation d’un emploi

Il est possible d’annuler tous les emplois qui ont `FirebaseJobDispatcher.CancelAll()` été programmés, ou tout simplement un seul emploi en utilisant la méthode ou la `FirebaseJobDispatcher.Cancel(string)` méthode:

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

L’une ou l’autre méthode retournera une valeur d’intégriste :

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash; Le travail a été annulé avec succès.
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash; Une erreur a empêché l’annulation du travail.
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`&ndash; Le `FirebaseJobDispatcher` n’est pas en mesure `IDriver` d’annuler le travail car il n’y a pas de valide disponible.

## <a name="summary"></a>Récapitulatif

Ce guide a discuté de la façon d’utiliser le répartiteur d’emploi Firebase pour effectuer intelligemment le travail en arrière-plan. Il a discuté de la façon d’encapsuler le travail à effectuer en tant que un `JobService` et comment utiliser le `FirebaseJobDispatcher` pour planifier ce travail, en précisant les critères avec un `JobTrigger` et comment les échecs devraient être traités avec un `RetryStrategy`.

## <a name="related-links"></a>Liens connexes

- [Xamarin.Firebase.JobDispatcher sur NuGet](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-job-dispatcher sur GitHub](https://github.com/firebase/firebase-jobdispatcher-android)
- [Liaison Xamarin.Firebase.JobDispatcher](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [Emploi intelligent-Scheduling](https://developer.android.com/topic/performance/scheduling.html)
- [Android Battery and Memory Optimizations - Google I/O 2016 (vidéo)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
