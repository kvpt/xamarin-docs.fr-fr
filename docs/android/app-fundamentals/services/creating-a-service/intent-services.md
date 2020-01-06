---
title: Services d’intention dans Xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: acf8824c7a575bca37301a409bdf6d5f42cca622
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488060"
---
# <a name="intent-services-in-xamarinandroid"></a>Services d’intention dans Xamarin. Android

Les services démarrés et liés s’exécutent sur le thread principal, ce qui signifie que pour maintenir les performances en douceur, un service doit effectuer le travail de façon asynchrone. L’une des façons les plus simples de résoudre ce problème est d’utiliser un _modèle de processeur de file d’attente de travail_, où le travail à effectuer est placé dans une file d’attente qui est servie par un thread unique.

Le [`IntentService`](xref:Android.App.IntentService) est une sous-classe de la classe `Service` qui fournit une implémentation spécifique à Android de ce modèle. Il gère la mise en file d’attente du travail, le démarrage d’un thread de travail pour la maintenance de la file d’attente et l’extraction des requêtes de la file d’attente à exécuter sur le thread de travail. Une `IntentService` s’arrête silencieusement et supprime le thread de travail lorsqu’il n’y a plus de travail dans la file d’attente.

Le travail est soumis à la file d’attente en créant un `Intent` puis en passant ce `Intent` à la méthode `StartService`.

Il n’est pas possible d’arrêter ou d’interrompre l' `IntentService` de la méthode `OnHandleIntent` pendant qu’elle fonctionne. En raison de cette conception, une `IntentService` doit être conservée sans état &ndash; elle ne doit pas reposer sur une connexion active ou une communication à partir du reste de l’application. Un `IntentService` est destiné à statelessly le traitement des demandes de travail.

Deux conditions sont requises pour la sous-classe `IntentService`:

1. Le nouveau type (créé par le sous-classing `IntentService`) remplace uniquement la méthode `OnHandleIntent`.
2. Le constructeur pour le nouveau type requiert une chaîne qui est utilisée pour nommer le thread de travail qui gérera les demandes. Le nom de ce thread de travail est principalement utilisé lors du débogage de l’application.

Le code suivant illustre une implémentation de `IntentService` avec la méthode de `OnHandleIntent` substituée :

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }

    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

Le travail est envoyé à un `IntentService` en instanciant un `Intent` puis en appelant la méthode [`StartService`](xref:Android.Content.Context.StartService*) avec cette intention en tant que paramètre. L’intention est transmise au service en tant que paramètre dans la méthode `OnHandleIntent`. Cet extrait de code est un exemple d’envoi d’une demande de travail à une intention : 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.PutPutExtra("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

La `IntentService` peut extraire les valeurs de l’intention, comme illustré dans cet extrait de code :  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");

    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```

## <a name="related-links"></a>Liens associés

- [IntentService](xref:Android.App.IntentService)
- [StartService](xref:Android.Content.Context.StartService*)
