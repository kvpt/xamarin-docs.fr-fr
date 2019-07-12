---
title: Présentation de la prise en charge asynchrone
description: Ce document décrit la programmation avec async et await, concepts introduits dans C# 5 pour le rendre plus facile à écrire du code asynchrone.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: cca147f0c5dd1a217f464ffbed2a1ad2618c9b80
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830197"
---
# <a name="async-support-overview"></a>Présentation de la prise en charge asynchrone

_C#5 a introduit deux mots clés pour simplifier la programmation asynchrone : async et await. Ces mots clés vous permettent d’écrire du code simple qui utilise la bibliothèque parallèle de tâches pour exécuter des opérations longues (par exemple, l’accès réseau) dans un autre thread et facilement accéder aux résultats à la fin. Les dernières versions de Xamarin.iOS et Xamarin.Android prennent en charge d’async et await : ce document fournit des explications et un exemple d’utilisation de la nouvelle syntaxe avec Xamarin._

Prise en charge asynchrone de Xamarin repose sur les fondations de Mono 3.0 et le profil d’API est mis à niveau à partir d’une version compatible avec les mobiles de Silverlight pour être une version compatible avec les mobiles de .NET 4.5 en cours.

## <a name="overview"></a>Présentation

Ce document présente la nouvelle async et await mots clés, présente quelques exemples simples implémentation des méthodes asynchrones dans Xamarin.iOS et Xamarin.Android.

Pour une description plus complète des nouvelles fonctionnalités asynchrones de C# 5 (y compris un grand nombre d’exemples et de différents scénarios d’utilisation), consultez l’article [programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async).

L’exemple d’application effectue une requête web asynchrone simple (sans bloquer le thread principal) puis met à jour de l’interface utilisateur avec le html téléchargé et le nombre de caractères.

 [![](async-images/AsyncAwait_427x368.png "L’exemple d’application effectue une requête web asynchrone simple sans bloquer le thread principal, puis met à jour de l’interface utilisateur avec le html téléchargé et le nombre de caractères")](async-images/AsyncAwait.png#lightbox)

Prise en charge asynchrone de Xamarin repose sur les fondations de Mono 3.0 et le profil d’API est mis à niveau à partir d’une version compatible avec les mobiles de Silverlight pour être une version compatible avec les mobiles de .NET 4.5 en cours.

## <a name="requirements"></a>Configuration requise

C#5 fonctionnalités nécessitent 3.0 Mono qui est inclus dans 6.4 de Xamarin.iOS et Xamarin.Android 4.8. Vous devrez mettre à niveau votre Mono, Xamarin.iOS, Xamarin.Android et Xamarin.Mac en tirer parti.

## <a name="using-async-amp-await"></a>Utilisation d’async &amp; await

 `async` et `await` débutez C# des fonctionnalités de langage qui fonctionnent conjointement avec la bibliothèque parallèle de tâches pour le rendre facile à écrire du code multithread pour effectuer des tâches longues sans bloquer le thread principal de votre application.

## <a name="async"></a>async

### <a name="declaration"></a>Déclaration

Le `async` mot clé est placé dans une déclaration de méthode (ou sur une expression lambda ou une méthode anonyme) pour indiquer qu’il contient le code qui peut s’exécuter de façon asynchrone, ie. ne bloquent pas le thread de l’appelant.

Une méthode marquée avec `async` doit contenir au moins une expression ou instruction await. Si aucun `await`s sont présents dans la méthode, puis elle s’exécute de façon synchrone (le même comme s’il existait aucun `async` modificateur). Cela entraîne également un avertissement du compilateur (mais pas une erreur).

### <a name="return-types"></a>Types de retours

Une méthode async doit retourner un `Task`, `Task<TResult>` ou `void`.

Spécifiez le `Task` type de retour si la méthode ne retourne pas de toute autre valeur.

Spécifiez `Task<TResult>` si la méthode doit retourner une valeur, où `TResult` est le type qui est retourné (comme un `int`, par exemple).

Le `void` type de retour est utilisé principalement pour les gestionnaires d’événements qui ont besoin. Code qui appelle les méthodes asynchrones qui retournent void ne peut pas `await` sur le résultat.

### <a name="parameters"></a>Paramètres

Impossible de déclarer les méthodes asynchrones `ref` ou `out` paramètres.

## <a name="await"></a>await

L’opérateur await peut être appliqué à une tâche à l’intérieur d’une méthode marquée comme asynchrone. Il indique à la méthode arrêter l’exécution à ce stade et attendez que la tâche se termine.

À l’aide d’await ne bloque pas le thread de l’appelant – plutôt le contrôle est retourné à l’appelant. Cela signifie que le thread appelant n’est pas bloqué, donc par exemple l’utilisateur thread d’interface ne serait pas bloqué lors de l’attente d’une tâche.

Lorsque la tâche se termine, la méthode continue de s’exécuter au même point dans le code. Cela inclut le retour à la portée de réessayer d’un bloc try-catch-finally (s’il en existe). await ne peut pas être utilisé dans un bloc catch ou le bloc finally.

En savoir plus sur [await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) sur Microsoft Docs.

## <a name="exception-handling"></a>Gestion des exceptions

Exceptions qui se produisent à l’intérieur d’une méthode async sont stockées dans la tâche et levées lorsque la tâche est `await`ed. Ces exceptions peuvent être interceptées et gérées à l’intérieur d’un bloc try-catch.

## <a name="cancellation"></a>Annulation

Les méthodes asynchrones qui prennent beaucoup de temps doivent prendre en charge l’annulation. En règle générale, l’annulation est appelée comme suit :

- Un `CancellationTokenSource` objet est créé.
- Le `CancellationTokenSource.Token` instance est passée à une méthode asynchrone pouvant être annulée.
- L’annulation est demandée en appelant le `CancellationTokenSource.Cancel` (méthode).

Ensuite, la tâche s’annule et accuse réception de l’annulation.

Pour plus d’informations sur l’annulation, consultez [Réglage de votre application Async (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application).

## <a name="example"></a>Exemples

Téléchargez le [exemple solution Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (pour iOS et Android) pour voir un exemple fonctionnel de `async` et `await` dans des applications mobiles. L’exemple de code est abordée plus en détail dans cette section.

### <a name="writing-an-async-method"></a>Écriture d’une méthode async

La méthode suivante montre comment le code un `async` méthode avec un `await`tâche d’ed :

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

Notez les points suivants :

-  La déclaration de méthode inclut la `async` mot clé.
-  Le type de retour est `Task<int>` afin que le code appelant peut accéder à la `int` valeur calculée dans cette méthode.
-  L’instruction return est `return exampleInt;` qui est un objet entier – le fait que la méthode retourne `Task<int>` fait partie des améliorations de langage.


### <a name="calling-an-async-method-1"></a>Appel d’une méthode async 1

Événement clic du bouton ce gestionnaire se trouve dans l’application exemple Android pour appeler la méthode décrite ci-dessus :

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

Remarques :

-  Le délégué anonyme a le préfixe du mot clé async.
-  La méthode asynchrone DownloadHomepage retourne une tâche <int> qui est stocké dans la variable sizeTask.
-  Le code attend sur la variable sizeTask.  *Cela* est l’emplacement que la méthode est interrompue et le contrôle est retourné au code appelant jusqu'à ce que la tâche asynchrone se termine sur son propre thread.
-  Exécution *pas* suspendre lorsque la tâche est créée sur la première ligne de la méthode, en dépit de la tâche créée il. Le mot clé await indique l’emplacement où l’exécution est suspendue.
-  Lorsque la tâche asynchrone se termine, intResult est défini et l’exécution se poursuit sur le thread d’origine, à partir de la ligne await.


### <a name="calling-an-async-method-2"></a>Appel d’une méthode async 2

Dans l’exemple d’application iOS l’exemple est écrit légèrement différemment pour illustrer une approche alternative. Plutôt que d’utiliser un délégué anonyme cet exemple déclare un `async` Gestionnaire d’événements qui est affecté comme un gestionnaire d’événements standard :

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

La méthode de gestionnaire d’événements est ensuite définie comme indiqué ici :

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

Quelques points importants :

-  La méthode est marquée comme `async` mais retourne `void` . Cela est généralement effectue uniquement pour les gestionnaires d’événements (dans le cas contraire, vous devriez retourner un `Task` ou `Task<TResult>` ).
-  Le code `await` s sur le `DownloadHomepage` (méthode) directement sur une affectation à une variable ( `intResult` ) à la différence de l’exemple précédent, où nous avons utilisé un niveau intermédiaire `Task<int>` variable pour faire référence à la tâche.  *Cela* est l’emplacement où contrôle est retourné à l’appelant jusqu'à ce que la méthode asynchrone est terminée sur un autre thread.
-  Lorsque la méthode asynchrone se termine et retourne, l’exécution reprend à le `await` ce qui signifie que le résultat de l’entier retourné, puis restituée dans un widget d’interface utilisateur.


## <a name="summary"></a>Récapitulatif

Utilisation d’async et await simplifie considérablement le code requis pour générer dynamiquement des opérations à long terme sur les threads d’arrière-plan sans bloquer le thread principal. Ils facilitent également l’accéder aux résultats une fois la tâche terminée.

Ce document a donné une vue d’ensemble de nouveaux mots clés de langage et des exemples pour Xamarin.iOS et Xamarin.Android.



## <a name="related-links"></a>Liens associés

- [AsyncAwait (sample)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [Rappels comme notre générations accédez à l’instruction](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Données (iOS) (exemple)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (sample)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (sample)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async)
- [Ajuster une application Async (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await et l’interface utilisateur et les interblocages ! Mon Dieu !](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [Traitement des tâches terminées)](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [Modèle asynchrone basé sur les tâches (TAP, Task-based Asynchronous Pattern)](https://msdn.microsoft.com/library/hh873175.aspx)
- [L’asynchronie dans C# 5 (blog de Eric Lippert) – sur l’introduction des mots clés](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
