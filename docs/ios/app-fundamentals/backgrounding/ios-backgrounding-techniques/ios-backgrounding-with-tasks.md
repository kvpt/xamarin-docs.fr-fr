---
title: Backgrounding iOS avec des tâches
description: Ce document décrit comment utiliser des tâches en arrière-plan pour effectuer des tâches longues après qu’une application est placée en arrière-plan.
ms.prod: xamarin
ms.assetid: 205D230E-C618-4D69-96EE-4B91D7819121
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: ec5439e11b0edd2b6ad5391254e40e01271879a7
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268830"
---
# <a name="ios-backgrounding-with-tasks"></a>Backgrounding iOS avec des tâches

La façon la plus simple pour effectuer backgrounding dans iOS consiste à diviser vos exigences backgrounding en tâches et exécuter les tâches en arrière-plan. Les tâches sont sous une limite de temps stricte et obtiennent généralement environ 600 secondes (10 minutes) de temps de traitement après qu’une application a déplacé à l’arrière-plan sur iOS 6 et moins de 10 minutes sur iOS 7 +.

Tâches en arrière-plan peuvent être divisés en trois catégories :

1.  **Les tâches en arrière-plan-Safe** : appelée n’importe où dans l’application où vous avez une tâche vous ne voulez interrompue doit l’application, entrez l’arrière-plan.
1.  **Les tâches DidEnterBackground** : appelée pendant la `DidEnterBackground` méthode de cycle de vie d’application pour faciliter le nettoyage et l’enregistrement d’état.
1.  **En arrière-plan transferts (iOS 7 +)** -un type spécial de tâche en arrière-plan utilisé pour effectuer les transferts de réseau sur iOS 7. Contrairement aux tâches récurrentes, transferts en arrière-plan ont une limite de temps prédéterminée.


Arrière-plan-safe et `DidEnterBackground` tâches sont sécurisés à utiliser sur iOS 6 et iOS 7, avec quelques différences mineures. Nous allons examiner ces deux types de tâches de façon plus détaillée.

## <a name="creating-background-safe-tasks"></a>Création de tâches d’arrière-plan-Safe

Certaines applications contiennent des tâches qui ne doit pas être interrompues par iOS doit état de modifier l’application. Un pour protéger ces tâches ne seront pas interrompus consiste à les enregistrer avec iOS en tant que tâches longues. Vous pouvez utiliser ce modèle n’importe où dans votre application où vous voulez une tâche interrompue doit le put utilisateur l’application en arrière-plan. Un excellent candidat pour ce modèle serait des tâches telles que l’envoi d’informations d’inscription d’un nouvel utilisateur à votre serveur ou de vérification des informations de connexion.

L’extrait de code suivant illustre l’inscription d’une tâche à exécuter en arrière-plan :

```csharp
nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});

//runs on main or background thread
FinishLongRunningTask(taskID);

UIApplication.SharedApplication.EndBackgroundTask(taskID);
```

Le processus d’inscription paires une tâche avec un identificateur unique, `taskID`et il encapsule ensuite mise en correspondance `BeginBackgroundTask` et `EndBackgroundTask` appels. Pour générer l’identificateur, nous effectuer un appel à la `BeginBackgroundTask` méthode sur le `UIApplication` de l’objet, puis démarrez la tâche à long terme, généralement sur un nouveau thread. Lorsque la tâche est terminée, nous appelons `EndBackgroundTask` et passez le même identificateur. Ceci est important car iOS pour fermer l’application si un `BeginBackgroundTask` appel n’a pas une mise en correspondance `EndBackgroundTask`.

> [!IMPORTANT]
> Les tâches en arrière-plan-safe peuvent s’exécuter sur le thread principal ou sur un thread d’arrière-plan, en fonction des besoins de l’application.


## <a name="performing-tasks-during-didenterbackground"></a>Effectuer des tâches pendant DidEnterBackground

En plus de rendre une tâche longue en arrière-plan-safe, l’inscription peut servir à lancer des tâches comme une application est mises en arrière-plan. iOS fournit une méthode d’événement dans le *AppDelegate* classe appelée `DidEnterBackground` qui peut être utilisé pour enregistrer l’état de l’application, enregistrez les données utilisateur et chiffrer le contenu sensible avant une application entre dans l’arrière-plan. Une application a environ cinq secondes à retourner à partir de cette méthode, ou elle est arrêtée. Par conséquent, les tâches de nettoyage peuvent prendre plus de cinq secondes peuvent être appelées à partir d’à l’intérieur de la `DidEnterBackground` (méthode). Ces tâches doivent être appelés sur un thread distinct.

Le processus est presque identique à celui de l’inscription d’une tâche à long terme. L’extrait de code suivant illustre cela en action :

```csharp
public override void DidEnterBackground (UIApplication application) {
  nint taskID = UIApplication.SharedApplication.BeginBackgroundTask( () => {});
  new Task ( () => {
    DoWork();
    UIApplication.SharedApplication.EndBackgroundTask(taskID);
  }).Start();
}
```

Nous commençons en substituant le `DidEnterBackground` méthode dans le `AppDelegate`, où nous enregistrons notre tâche via `BeginBackgroundTask` comme nous l’avons fait dans l’exemple précédent. Ensuite, nous générer un nouveau thread et d’effectuer de notre tâche à long terme. Notez que le `EndBackgroundTask` est maintenant appelé à partir d’à l’intérieur de la tâche à long terme, étant donné que le `DidEnterBackground` méthode ont déjà renvoyé.

> [!IMPORTANT]
> iOS utilise un [mécanisme de surveillance](https://developer.apple.com/library/ios/qa/qa1693/_index.html) pour vous assurer que l’interface utilisateur d’une application reste réactive. Une application qui se consacre beaucoup trop de temps dans `DidEnterBackground` cesse de répondre dans l’interface utilisateur. Permet de lancer des tâches à exécuter en arrière-plan `DidEnterBackground` à retourner dans un délai raisonnable, que l’interface utilisateur reste réactive et empêche l’arrêt de l’application de l’agent de surveillance.


## <a name="handling-background-task-time-limits"></a>Limitation de durée de tâche en arrière-plan gestion

iOS place des limites strictes sur une tâche en arrière-plan de longue durée peut s’exécuter et, si le `EndBackgroundTask` appel n’est pas effectué dans le temps imparti, l’application va être interrompue. En conservant la backgrounding le temps restant et l’utilisation des gestionnaires d’expiration lorsque cela est nécessaire, nous pouvons éviter iOS arrêter l’application.

### <a name="accessing-background-time-remaining"></a>L’accès à des temps d’arrière-plan restant

Si une application avec des tâches est déplacée à l’arrière-plan, les tâches enregistrées obtenez environ 600 secondes à s’exécuter. Nous pouvons vérifier combien de temps la tâche doit s’effectuer à l’aide de la méthode statique `BackgroundTimeRemaining` propriété de la `UIApplication` classe. Le code suivant, nous donneront la durée, en secondes, pendant laquelle notre tâche en arrière-plan a quitté :

```csharp
double timeRemaining = UIApplication.SharedApplication.BackgroundTimeRemaining;
```

### <a name="avoiding-app-termination-with-expiration-handlers"></a>Éviter l’arrêt de l’application avec les gestionnaires d’Expiration

En plus de donner accès à la `BackgroundTimeRemaining` propriété, iOS offre un moyen approprié pour gérer l’expiration de l’heure d’arrière-plan via un **Gestionnaire d’Expiration**. Il s’agit d’un bloc de code qui sera exécutée lorsque le temps alloué à une tâche est sur le point d’expirer facultatif. Appelle du code dans le Gestionnaire d’Expiration `EndBackgroundTask` et passe l’ID de tâche, ce qui indique que l’application se comporte correctement et iOS empêche l’arrêt de l’application même si la tâche s’exécute plus assez de temps. `EndBackgroundTask` doit être appelée dans le Gestionnaire d’expiration, ainsi que dans le cadre normal de l’exécution. 

Le Gestionnaire d’expiration est exprimé comme une fonction anonyme à l’aide d’une expression lambda, comme illustré ci-dessous :

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

Alors que les gestionnaires d’expiration ne sont pas requis pour l’exécution de code, vous devez toujours utiliser un gestionnaire d’expiration avec une tâche en arrière-plan.

 <a name="background_tasks_in_iOS_7" />

## <a name="background-tasks-in-ios-7"></a>Tâches en arrière-plan sur iOS 7 +

La principale modification apportée dans iOS 7 en ce qui concerne les tâches en arrière-plan est pas comment les tâches sont implémentés, mais lorsqu’ils exécutent.

Rappelez-vous que préliminaire iOS 7, une tâche en cours d’exécution en arrière-plan avait 600 secondes pour terminer. L’une des raisons pour cette limite sont qu’une tâche en arrière-plan, conservez l’appareil éveillés pendant la durée de la tâche :

 [![](ios-backgrounding-with-tasks-images/ios6.png "Graphique de la tâche en conservant l’application iOS préliminaire éveillés 7")](ios-backgrounding-with-tasks-images/ios6.png#lightbox)

le traitement en arrière-plan iOS 7 est optimisé pour la durée de vie de la batterie. Dans iOS 7, backgrounding devient opportuniste : au lieu de garder l’appareil en éveil, les tâches respectent lorsque l’appareil est en veille et procéder à leur traitement dans des segments lors de l’appareil sort de veille pour gérer les appels téléphoniques, notifications, entrant des messages électroniques et autres interruptions courantes. Le diagramme suivant fournit un aperçu de comment une tâche peut être rompue haut :

 [![](ios-backgrounding-with-tasks-images/ios7.png "Graphique de la tâche en cours divisée en segmente postérieur à iOS 7")](ios-backgrounding-with-tasks-images/ios7.png#lightbox)

Étant donné que la tâche d’exécution n’est pas continue de plus, les tâches qui effectuent des transferts réseau doivent être gérés différemment dans iOS 7. Les développeurs sont encouragés à utiliser le `NSURlSession` API pour gérer les transferts réseau. La section suivante est une vue d’ensemble des transferts en arrière-plan.

 <a name="background-transfers" />

## <a name="background-transfers"></a>Transferts en arrière-plan

La colonne vertébrale de transferts en arrière-plan dans iOS 7 est la nouvelle `NSURLSession` API. `NSURLSession` permet de créer des tâches pour :

1.  Transférer du contenu via les interruptions réseau et de périphériques.
1.  Charger et télécharger des fichiers volumineux ( *Service de transfert en arrière-plan* ).


Examinons plus en détail comment cela fonctionne.

### <a name="nsurlsession-api"></a>API de NSURLSession

 `NSURLSession` est une API puissante pour le transfert de contenu sur le réseau. Il fournit un ensemble d’outils pour gérer le transfert de données par le biais d’interruptions et les modifications dans les États de l’application.

Le `NSURLSession` API crée une ou plusieurs sessions, qui à son tour générer dynamiquement des tâches pour la navette des blocs de données associées sur le réseau. Tâches exécutées de façon asynchrone pour transférer des données rapide et fiable. Étant donné que `NSURLSession` est asynchrone, chaque session requiert un bloc de gestionnaire d’achèvement à laisser le système et l’application de savoir quand un transfert est terminé.

Pour effectuer un transfert réseau qui est valide sur préliminaire iOS 7 et post-iOS 7, vérifiez si un `NSURLSession` est disponible pour les transferts de file d’attente et une tâche en arrière-plan régulière permet d’effectuer le transfert si elle n’est pas :

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
> Évitez d’effectuer des appels à mettre à jour l’interface utilisateur à partir de l’arrière-plan dans iOS 6 d’un code conforme, comme iOS 6 ne prend pas en charge les mises à jour de l’interface utilisateur en arrière-plan et mettent fin à l’application.


Le `NSURLSession` API inclut un ensemble complet de fonctionnalités pour gérer l’authentification, gérer les transferts ayant échoués et signaler les erreurs côté client - mais pas côté serveur -. Il permet de pont que les interruptions de la tâche exécution introduit dans iOS 7 et prend également en charge pour le transfert de fichiers volumineux rapidement et de manière fiable. La section suivante explique cette deuxième fonctionnalité.

### <a name="background-transfer-service"></a>Service de transfert en arrière-plan

Avant d’iOS 7, téléchargement de fichiers en arrière-plan n’étaient pas fiables. Tâches en arrière-plan obtenir une durée limitée à exécuter, mais le temps que nécessaire pour transférer un fichier varie avec le réseau et la taille du fichier. Dans iOS 7, nous pouvons utiliser un `NSURLSession` pour charger et télécharger des fichiers volumineux avec succès. Le particulier `NSURLSession` type de session qui gère les transferts de fichiers volumineux en arrière-plan réseau est connu comme le *Service de transfert en arrière-plan*.

Les transferts lancées à l’aide du Service de transfert en arrière-plan sont gérés par le système d’exploitation et fournissent des API pour gérer l’authentification et les erreurs. Étant donné que les transferts ne sont pas liés par une limite de temps arbitraire, elles peuvent servir à charger ou télécharger des fichiers volumineux, contenu dans l’arrière-plan et bien plus encore mise à jour automatique. Reportez-vous à la [procédure pas à pas de transfert en arrière-plan](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md) pour plus d’informations sur la façon d’implémenter le Service.

Le Service de transfert en arrière-plan est souvent associé à des Notifications à distance pour aider les applications à actualiser le contenu en arrière-plan ou de récupération en arrière-plan. Dans les deux sections suivantes, nous introduisons le concept de l’inscription des applications entières pour exécuter en arrière-plan sur iOS 6 et iOS 7.

