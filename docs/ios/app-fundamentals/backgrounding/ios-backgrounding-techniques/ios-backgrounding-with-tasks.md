---
title: Backgrounding iOS avec des tâches
description: Ce document explique comment utiliser des tâches en arrière-plan pour effectuer des tâches de longue durée après l’exécution d’une application en arrière-plan.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f39ab83e00db1abd6508d26a9280fb708e681445
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004730"
---
# <a name="ios-backgrounding-with-tasks"></a>Backgrounding iOS avec des tâches

La façon la plus simple d’effectuer des opérations d’arrière-plan sur iOS consiste à diviser vos exigences en arrière-plan en tâches et à exécuter les tâches en arrière-plan. Les tâches sont inférieures à une limite de temps stricte et obtiennent généralement environ 600 secondes (10 minutes) de temps de traitement après qu’une application a été déplacée en arrière-plan sur iOS 6, et moins de 10 minutes sur iOS 7 +.

Les tâches en arrière-plan peuvent être réparties en trois catégories :

1. **Tâches d’arrière-plan sécurisées** : appelées n’importe où dans l’application où vous avez une tâche que vous ne souhaitez pas interrompre si l’application entre en arrière-plan.
1. **Tâches DidEnterBackground** -appelées au cours de la méthode de cycle de vie des applications `DidEnterBackground` pour faciliter le nettoyage et l’enregistrement de l’État.
1. **Transferts en arrière-plan (iOS 7 +)** : type spécial de tâche d’arrière-plan utilisé pour effectuer des transferts réseau sur iOS 7. Contrairement aux tâches standard, les transferts en arrière-plan n’ont pas de limite de temps prédéfinie.

Les tâches de `DidEnterBackground` et d’arrière-plan peuvent être utilisées en toute sécurité sur iOS 6 et iOS 7, avec quelques différences mineures. Étudions ces deux types de tâches plus en détail.

## <a name="creating-background-safe-tasks"></a>Création de tâches sécurisées en arrière-plan

Certaines applications contiennent des tâches qui ne doivent pas être interrompues par iOS si l’application change d’État. L’une des façons de protéger ces tâches d’être interrompues consiste à les inscrire avec iOS en tant que tâches longues. Vous pouvez utiliser ce modèle n’importe où dans votre application où vous ne souhaitez pas qu’une tâche soit interrompue si l’utilisateur place l’application en arrière-plan. Un bon candidat à ce modèle serait des tâches telles que l’envoi des informations d’inscription d’un nouvel utilisateur à votre serveur ou la vérification des informations de connexion.

L’extrait de code suivant montre comment inscrire une tâche pour l’exécuter en arrière-plan :

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

Le processus d’inscription couple une tâche à un identificateur unique, `taskID`, puis l’encapsule dans les appels de `BeginBackgroundTask` et de `EndBackgroundTask` correspondants. Pour générer l’identificateur, nous effectuons un appel à la méthode `BeginBackgroundTask` sur l’objet `UIApplication`, puis nous démarrons la tâche de longue durée, généralement sur un nouveau thread. Lorsque la tâche est terminée, nous appelons `EndBackgroundTask` et passons le même identificateur. C’est important, car iOS met fin à l’application si un appel `BeginBackgroundTask` n’a pas de `EndBackgroundTask`correspondant.

> [!IMPORTANT]
> Les tâches sécurisées en arrière-plan peuvent s’exécuter sur le thread principal ou sur un thread d’arrière-plan, selon les besoins de l’application.

## <a name="performing-tasks-during-didenterbackground"></a>Exécution de tâches pendant la DidEnterBackground

Outre la création d’une tâche de longue durée en arrière-plan, l’inscription peut être utilisée pour lancer des tâches lorsqu’une application est placée en arrière-plan. iOS fournit une méthode d’événement dans la classe *AppDelegate* appelée `DidEnterBackground` qui peut être utilisée pour enregistrer l’état de l’application, enregistrer les données utilisateur et chiffrer le contenu sensible avant qu’une application n’entre en arrière-plan. Une application a environ cinq secondes pour retourner à partir de cette méthode, sinon elle sera arrêtée. Par conséquent, les tâches de nettoyage qui peuvent prendre plus de cinq secondes peuvent être appelées à partir de la méthode `DidEnterBackground`. Ces tâches doivent être appelées sur un thread distinct.

Le processus est quasiment identique à celui de l’inscription d’une tâche de longue durée. L’extrait de code suivant illustre cette opération en action :

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Nous commençons par substituer la méthode `DidEnterBackground` dans le `AppDelegate`, où nous enregistrons notre tâche via `BeginBackgroundTask` comme nous l’avons fait dans l’exemple précédent. Nous générons ensuite un nouveau thread et effectuons la tâche de longue durée. Notez que l’appel de `EndBackgroundTask` est désormais effectué à partir de l’intérieur de la tâche de longue durée, car la méthode `DidEnterBackground` aura déjà été retournée.

> [!IMPORTANT]
> iOS utilise un [mécanisme de surveillance](https://developer.apple.com/library/ios/qa/qa1693/_index.html) pour garantir le maintien de la réactivité de l’interface utilisateur d’une application. Une application qui consacre trop de temps à `DidEnterBackground` ne répondra plus dans l’interface utilisateur. Le lancement des tâches en arrière-plan permet à `DidEnterBackground` de retourner en temps opportun, en conservant la réactivité de l’interface utilisateur et en empêchant la surveillance de tuer l’application.

## <a name="handling-background-task-time-limits"></a>Gestion des limites de temps des tâches en arrière-plan

iOS impose des limites strictes quant à la durée pendant laquelle une tâche en arrière-plan peut s’exécuter, et si l’appel de `EndBackgroundTask` n’est pas effectué dans le délai imparti, l’application est arrêtée. En effectuant le suivi du temps d’arrière-plan restant et en utilisant des gestionnaires d’expiration si nécessaire, nous pouvons éviter que iOS termine l’application.

### <a name="accessing-background-time-remaining"></a>Accès au temps restant en arrière-plan

Si une application avec des tâches inscrites est déplacée en arrière-plan, les tâches inscrites sont effectuées environ 600 secondes. Nous pouvons vérifier la durée d’exécution de la tâche à l’aide de la propriété statique `BackgroundTimeRemaining` de la classe `UIApplication`. Le code suivant nous donne le temps, en secondes, de la gauche de notre tâche en arrière-plan :

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Éviter l’arrêt d’une application avec des gestionnaires d’expiration

En plus de donner accès à la propriété `BackgroundTimeRemaining`, iOS offre un moyen pratique de gérer l’expiration du temps d’arrière-plan à l’aide d’un **Gestionnaire d’expiration**. Il s’agit d’un bloc de code facultatif qui est exécuté lorsque le temps alloué à une tâche est sur le point d’expirer. Le code dans le gestionnaire d’expiration appelle `EndBackgroundTask` et transmet l’ID de tâche, ce qui indique que l’application se comporte correctement et empêche iOS d’arrêter l’application même si la tâche n’est pas exécutée. `EndBackgroundTask` doit être appelé dans le gestionnaire d’expiration, ainsi que dans le cadre d’une exécution normale. 

Le gestionnaire d’expiration est exprimé sous la forme d’une fonction anonyme utilisant une expression lambda, comme illustré ci-dessous :

```csharp
Task.Factory.StartNew( () => {

    //expirationHandler only called if background time allowed exceeded
    var taskId = UIApplication.SharedApplication.BeginBackgroundTask(() => {
        Console.WriteLine("Exhausted time");
        UIApplication.SharedApplication.EndBackgroundTask(taskId); 
    });
    while(myFlag == true)
    {
        Console.WriteLine(UIApplication.SharedApplication.BackgroundTimeRemaining);
        myFlag = SomeCalculationNeedsMoreTime();
    }
    //Only called if loop terminated due to myFlag and not expiration of time
    UIApplication.SharedApplication.EndBackgroundTask(taskId);
});
```

Alors que les gestionnaires d’expiration ne sont pas requis pour l’exécution du code, vous devez toujours utiliser un gestionnaire d’expiration avec une tâche en arrière-plan.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tâches en arrière-plan dans iOS 7 +

La modification la plus importante d’iOS 7 en ce qui concerne les tâches en arrière-plan n’est pas la manière dont les tâches sont implémentées, mais quand elles s’exécutent.

Rappelez-vous qu’une tâche exécutée en arrière-plan comportait 600 secondes. Cette limite peut être due au fait qu’une tâche qui s’exécute en arrière-plan conserverait l’éveil de l’appareil pendant la durée de la tâche :

 [![](ios-backgrounding-with-tasks-images/ios6.png "Graph of the task keeping the app awake pre-iOS 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

le traitement en arrière-plan iOS 7 est optimisé pour une durée de vie de batterie plus longue. Dans iOS 7, l’arrière-plan devient opportuniste : au lieu de garder l’appareil allumé, les tâches respectent le mode de mise en veille de l’appareil et effectuent le traitement en bloc lorsque l’appareil sort de veille pour gérer les appels téléphoniques, les notifications, les e-mails entrants et d’autres interruptions courantes. Le diagramme suivant fournit des informations sur la façon dont une tâche peut être divisée :

 [![](ios-backgrounding-with-tasks-images/ios7.png "Graph of the task being broken into chunks post-iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Étant donné que le temps d’exécution de la tâche n’est plus continu, les tâches qui effectuent des transferts réseau doivent être gérées différemment dans iOS 7. Les développeurs sont encouragés à utiliser l’API `NSURlSession` pour gérer les transferts réseau. La section suivante est une vue d’ensemble des transferts en arrière-plan.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferts en arrière-plan

L’épine dorsale des transferts en arrière-plan dans iOS 7 est la nouvelle API `NSURLSession`. `NSURLSession` nous permet de créer des tâches pour :

1. Transférer du contenu via des interruptions de réseau et d’appareil.
1. Chargez et téléchargez des fichiers volumineux ( *service de transfert en arrière-plan* ).

Examinons de plus près la façon dont cela fonctionne.

### <a name="nsurlsession-api"></a>API passer

 `NSURLSession` est une API puissante pour le transfert de contenu sur le réseau. Il fournit un ensemble d’outils permettant de gérer le transfert de données par le biais d’interruptions du réseau et de modifications des États des applications.

L’API `NSURLSession` crée une ou plusieurs sessions, qui à leur tour génèrent des tâches sur des blocs navette de données associées sur le réseau. Les tâches s’exécutent de façon asynchrone pour transférer des données rapidement et de façon fiable. Étant donné que `NSURLSession` est asynchrone, chaque session requiert un bloc de gestionnaire d’achèvement pour permettre au système et à l’application de savoir quand un transfert est terminé.

Pour effectuer un transfert réseau valide à la fois pour les versions antérieures à iOS 7 et postérieures à iOS 7, vérifiez si une `NSURLSession` est disponible pour les transferts de file d’attente et utilisez une tâche d’arrière-plan normale pour effectuer le transfert si ce n’est pas le cas :

```csharp
if ([NSURLSession class]) {
  // Create a background session and enqueue transfers
}
else {
  // Start a background task and transfer directly
  // Do NOT make calls to update the UI here!
}
```

> [!IMPORTANT]
> Évitez d’effectuer des appels pour mettre à jour l’interface utilisateur à partir de l’arrière-plan dans du code conforme à iOS 6, car iOS 6 ne prend pas en charge les mises à jour de l’interface utilisateur en arrière-plan et met fin à l’application.

L’API `NSURLSession` comprend un ensemble complet de fonctionnalités permettant de gérer l’authentification, de gérer les transferts ayant échoué et de signaler les erreurs côté client et non côté serveur. Il permet de combler les interruptions au cours de l’exécution des tâches introduites dans iOS 7, et prend également en charge le transfert rapide et fiable de fichiers volumineux. La section suivante explore cette deuxième fonctionnalité.

### <a name="background-transfer-service"></a>Service de transfert en arrière-plan

Avant iOS 7, le chargement ou le téléchargement de fichiers en arrière-plan n’était pas fiable. Les tâches en arrière-plan bénéficient d’une durée limitée, mais le temps nécessaire au transfert d’un fichier varie en fonction du réseau et de la taille du fichier. Dans iOS 7, nous pouvons utiliser un `NSURLSession` pour charger et télécharger des fichiers volumineux. Le type de session de `NSURLSession` particulier qui gère les transferts réseau de fichiers volumineux en arrière-plan est connu sous le nom de *service de transfert en arrière-plan*.

Les transferts initiés à l’aide du service de transfert en arrière-plan sont gérés par le système d’exploitation et fournissent des API pour gérer l’authentification et les erreurs. Étant donné que les transferts ne sont pas liés par une limite de temps arbitraire, ils peuvent être utilisés pour charger ou télécharger des fichiers volumineux, mettre à jour automatiquement le contenu en arrière-plan, et bien plus encore. Pour plus d’informations sur la façon d’implémenter le service, consultez la [procédure pas à pas relative au transfert en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) .

Le service de transfert en arrière-plan est souvent associé à des notifications d’extraction en arrière-plan ou à distance pour aider les applications à actualiser le contenu en arrière-plan. Dans les deux sections suivantes, nous allons présenter le concept d’inscription d’applications entières pour qu’elles s’exécutent en arrière-plan sur iOS 6 et iOS 7.
