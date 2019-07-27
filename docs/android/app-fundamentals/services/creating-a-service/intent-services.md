---
title: Services d’intention dans Xamarin. Android
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 4c868623ae08ac1366c1c9ea55c8d635f0a6a061
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509137"
---
# <a name="intent-services-in-xamarinandroid"></a>Services d’intention dans Xamarin. Android

## <a name="intent-services-overview"></a>Vue d’ensemble des services d’intention

Les services démarrés et liés s’exécutent sur le thread principal, ce qui signifie que pour maintenir les performances en douceur, un service doit effectuer le travail de façon asynchrone. L’une des façons les plus simples de résoudre ce problème est d’utiliser un _modèle de processeur de file d’attente de travail_, où le travail à effectuer est placé dans une file d’attente qui est servie par un thread unique.

Est une sous-classe de la `Service` classe qui fournit une implémentation spécifique d’Android de ce modèle. [`IntentService`](xref:Android.App.IntentService) Il gère la mise en file d’attente du travail, le démarrage d’un thread de travail pour la maintenance de la file d’attente et l’extraction des requêtes de la file d’attente à exécuter sur le thread de travail. Un `IntentService` s’arrête de lui-même et supprime le thread de travail lorsqu’il n’y a plus de travail dans la file d’attente.

Le travail est soumis à la file d’attente `Intent` en créant un et `Intent` en le `StartService` passant à la méthode.

Il n’est pas possible d’arrêter ou d' `OnHandleIntent` interrompre `IntentService` la méthode pendant qu’elle fonctionne. En raison de cette conception, `IntentService` un doit être maintenu &ndash; sans État. il ne doit pas s’appuyer sur une connexion active ou une communication à partir du reste de l’application. Un `IntentService` est destiné à statelessly le traitement des demandes de travail.

Deux conditions sont requises pour le sous- `IntentService`classing:

1. Le nouveau type (créé par sous-classe `IntentService`) remplace uniquement la `OnHandleIntent` méthode.
2. Le constructeur pour le nouveau type requiert une chaîne qui est utilisée pour nommer le thread de travail qui gérera les demandes. Le nom de ce thread de travail est principalement utilisé lors du débogage de l’application.

Le code suivant illustre une `IntentService` implémentation de la méthode substituée: `OnHandleIntent`

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

Le travail est envoyé à `IntentService` un en instanciant un `Intent` et en [`StartService`](xref:Android.Content.Context.StartService*) appelant ensuite la méthode en tant que paramètre. L’intention est passée au service en tant que paramètre dans la `OnHandleIntent` méthode. Cet extrait de code est un exemple d’envoi d’une demande de travail à une intention: 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService` Peut extraire les valeurs de l’intention, comme illustré dans cet extrait de code:  

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
