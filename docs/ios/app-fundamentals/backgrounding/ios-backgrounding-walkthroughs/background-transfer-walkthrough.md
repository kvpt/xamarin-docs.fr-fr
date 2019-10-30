---
title: Transfert en arrière-plan et passer dans Xamarin. iOS
description: Ce document fournit une procédure pas à pas qui montre comment utiliser le transfert en arrière-plan et passer pour lancer le téléchargement d’une grande image et continuer le téléchargement lorsque l’application est placée en arrière-plan.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6004598b215c85b70b057ff156c3a905a0879138
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010716"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Transfert en arrière-plan et passer dans Xamarin. iOS

Un transfert en arrière-plan est initié en configurant un `NSURLSession` d’arrière-plan et en mettant en file d’attente le chargement ou le téléchargement de tâches. Si les tâches se terminent pendant que l’application est en arrière-plan, suspendue ou arrêtée, iOS avertit l’application en appelant le gestionnaire d’achèvement dans le *AppDelegate*de l’application. Le diagramme suivant illustre cette opération :

 [![](background-transfer-walkthrough-images/transfer.png "A background transfer is initiated by configuring a background NSURLSession and enqueuing upload or download tasks")](background-transfer-walkthrough-images/transfer.png#lightbox)

Voyons à quoi cela ressemble dans le code.

## <a name="configuring-a-background-session"></a>Configuration d’une session en arrière-plan

Pour créer une session en arrière-plan, créez un nouveau `NSUrlSession` et configurez-le à l’aide d’un objet `NSUrlSessionConfiguration`.

L’objet de configuration détermine ce que la session peut faire et les types de tâches qu’elle peut exécuter.
Les sessions configurées à l’aide de la méthode `CreateBackgroundSessionConfiguration` s’exécutent dans un processus distinct et effectuent des transferts discrétionnaires (WiFi) pour préserver les données et la durée de vie de la batterie.
L’exemple de code suivant illustre la configuration appropriée d’une session de transfert en arrière-plan à l’aide de la méthode `CreateBackgroundSessionConfiguration` et d’un identificateur de chaîne unique :

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

En dehors d’un objet de configuration, une session requiert également un délégué de session et une file d’attente.
La file d’attente détermine l’ordre dans lequel les tâches se terminent. Le délégué de session chaperones le processus de transfert et gère l’authentification, la mise en cache et d’autres problèmes liés à la session.

## <a name="working-with-tasks-and-delegates"></a>Utilisation des tâches et des délégués

Maintenant que nous avons configuré une session en arrière-plan, nous allons lancer des tâches pour gérer le transfert. Nous pouvons effectuer le suivi de ces tâches à l’aide d’une instance de `NSUrlSessionDelegate` appelée un délégué de session. Le délégué de session est responsable de la sortie d’une application arrêtée ou suspendue en arrière-plan pour gérer l’authentification, les erreurs ou la fin du transfert.

Une `NSUrlSessionDelegate` fournit les méthodes de base suivantes pour vérifier l’état du transfert :

- *DidFinishEventsForBackgroundSession* : cette méthode est appelée lorsque toutes les tâches sont terminées et que le transfert est terminé.
- *DidReceiveChallenge* : appelée pour demander des informations d’identification lorsque l’autorisation est requise.
- *DidBecomeInvalidWithError* : appelée si la `NSURLSession` devient invalidée.

Les sessions en arrière-plan requièrent des délégués plus spécialisés en fonction des types de tâches en cours d’exécution. Les sessions en arrière-plan sont limitées à deux types de tâches :

- *Charger des tâches* -les tâches de type `NSUrlSessionUploadTask` utilisent le `NSUrlSessionTaskDelegate`, qui hérite de `NSUrlSessionDelegate`. Ce délégué fournit des méthodes supplémentaires pour suivre la progression du téléchargement, gérer la redirection HTTP, et bien plus encore.
- *Télécharger des tâches* -tâches de type `NSUrlSessionDownloadTask` utiliser le `NSUrlSessionDownloadDelegate`, qui hérite de `NSUrlSessionTaskDelegate`. Ce délégué fournit toutes les méthodes pour les tâches de téléchargement, ainsi que des méthodes spécifiques au téléchargement pour suivre la progression du téléchargement et déterminer quand une tâche de téléchargement a repris ou est terminée.

Le code suivant définit une tâche qui peut être utilisée pour télécharger une image à partir d’une URL. Nous commençons la tâche en appelant `CreateDownloadTask` dans notre session en arrière-plan et en transmettant la requête d’URL :

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Ensuite, nous allons créer un délégué de téléchargement de session pour effectuer le suivi de toutes les tâches de téléchargement dans cette session :

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Si vous souhaitez connaître la progression d’une tâche de téléchargement, nous pouvons remplacer la méthode `DidWriteData` pour suivre la progression et même mettre à jour l’interface utilisateur. Les mises à jour de l’interface utilisateur s’affichent immédiatement si l’application est au premier plan, ou attendent l’utilisateur lors de sa prochaine ouverture de l’application.

L’API de délégué de session fournit une large boîte à outils pour interagir avec les tâches. Pour obtenir la liste complète des méthodes de délégué de session, reportez-vous à la documentation de l’API `NSUrlSessionDelegate`.

> [!IMPORTANT]
> Les sessions en arrière-plan sont démarrées sur un thread d’arrière-plan, de sorte que tous les appels pour mettre à jour l’interface utilisateur doivent être exécutés explicitement sur le thread d’interface utilisateur en appelant `InvokeOnMainThread` pour éviter que iOS termine l’application. 

## <a name="handling-transfer-completion"></a>Gestion de l’achèvement du transfert

La dernière étape consiste à laisser l’application savoir quand toutes les tâches associées à la session sont terminées et à gérer le nouveau contenu.

Dans le *AppDelegate*, abonnez-vous à l’événement `HandleEventsForBackgroundUrl`. Lorsque l’application entre en arrière-plan et qu’une session de transfert est en cours d’exécution, cette méthode est appelée et le système passe un gestionnaire d’achèvement :

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Nous allons utiliser le gestionnaire d’achèvement pour laisser iOS savoir quand le traitement de l’application est terminé.

Souvenez-vous qu’une session peut générer plusieurs tâches pour traiter un transfert. Une fois la dernière tâche terminée, une application interrompue ou arrêtée est relancée en arrière-plan. Ensuite, l’application se reconnecte au `NSURLSession` à l’aide de l’identificateur de session unique et appelle `DidFinishEventsForBackgroundSession` sur le délégué de session. Cette méthode est l’occasion pour l’application de gérer le nouveau contenu, notamment la mise à jour de l’interface utilisateur pour refléter les résultats du transfert :

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Une fois que nous avons terminé de gérer le nouveau contenu, nous appelons le gestionnaire d’achèvement pour informer le système qu’il est possible de prendre un instantané de l’application et de revenir en mode veille :

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

Dans cette procédure pas à pas, nous avons abordé les étapes de base pour implémenter le service de transfert en arrière-plan dans iOS 7.

## <a name="related-links"></a>Liens associés

- [Transfert en arrière-plan simple (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
