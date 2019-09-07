---
title: Threading dans Xamarin. iOS
description: Ce document explique comment utiliser les API System. Threading dans une application Xamarin. iOS. Il traite de la bibliothèque parallèle de tâches, de la création d’applications réactives et de garbage collection.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: d8267d4def0f7c24c660dfb4d301c111a92bb0b9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767151"
---
# <a name="threading-in-xamarinios"></a>Threading dans Xamarin. iOS

Le runtime Xamarin. iOS permet aux développeurs d’accéder aux API de thread .net, à la fois explicitement lors`System.Threading.Thread, System.Threading.ThreadPool`de l’utilisation de threads () et implicitement lors de l’utilisation de modèles de délégué asynchrones ou des méthodes BeginXxx, ainsi que de la plage complète d’API qui prennent en charge Bibliothèque parallèle de tâches.

Xamarin recommande vivement d’utiliser la [bibliothèque parallèle de tâches](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) pour créer des applications pour plusieurs raisons :
- Le planificateur TPL par défaut délègue l’exécution des tâches au pool de threads, qui, à son tour, augmente de manière dynamique le nombre de threads nécessaires à l’exécution du processus, tout en évitant un scénario où un trop grand nombre de threads finissent par concurrence pour le temps processeur. 
- Il est plus facile de réfléchir aux opérations en termes de tâches TPL. Vous pouvez facilement les manipuler, les planifier, sérialiser leur exécution ou lancer un grand nombre en parallèle avec un ensemble complet d’API. 
- C’est la base de la programmation avec les C# nouvelles extensions de langage Async. 

Le pool de threads augmentera lentement le nombre de threads selon les besoins en fonction du nombre de cœurs de processeur disponibles sur le système, de la charge système et des demandes de votre application. Vous pouvez utiliser ce pool de threads en appelant des méthodes `System.Threading.ThreadPool` dans ou à l’aide `System.Threading.Tasks.TaskScheduler` de la valeur par défaut (partie des *frameworks parallèles*).

En général, les développeurs utilisent des threads lorsqu’ils doivent créer des applications réactives et qu’ils ne souhaitent pas bloquer la boucle d’exécution de l’interface utilisateur principale.

 <a name="Developing_Responsive_Applications" />

## <a name="developing-responsive-applications"></a>Développement d’applications réactives

L’accès aux éléments de l’interface utilisateur doit être limité au même thread qui exécute la boucle principale pour votre application. Si vous souhaitez apporter des modifications à l’interface utilisateur principale à partir d’un thread, vous devez mettre le code en file d’attente à l’aide de [NSObject. InvokeOnMainThread](xref:Foundation.NSObject), comme suit :

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

Le code ci-dessus appelle le code à l’intérieur du délégué dans le contexte du thread principal, sans provoquer de conditions de concurrence qui pourraient potentiellement bloquer votre application.

 <a name="Threading_and_Garbage_Collection" />

## <a name="threading-and-garbage-collection"></a>Threading et garbage collection

Au cours de l’exécution, le runtime objective-C va créer et libérer des objets. Si les objets sont marqués pour la « libération automatique », le runtime objective-C libère ces objets dans le actuel `NSAutoReleasePool`du thread. Xamarin. iOS crée un `NSAutoRelease` pool pour chaque thread `System.Threading.ThreadPool` du et du thread principal. Ce par extension couvre tous les threads créés à l’aide du TaskScheduler par défaut dans System. Threading. Tasks.

Si vous créez vos propres threads `System.Threading` à l’aide de, vous devez `NSAutoRelease` fournir votre propre pool pour empêcher les fuites de données. Pour ce faire, encapsulez simplement votre thread dans le morceau de code suivant :

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Remarque : Depuis Xamarin. iOS 5,2, vous n’avez plus besoin de fournir `NSAutoReleasePool` votre propre compte, car un seul sera fourni automatiquement.

## <a name="related-links"></a>Liens associés

- [Utilisation du thread d’interface utilisateur](~/ios/user-interface/ios-ui/ui-thread.md)
