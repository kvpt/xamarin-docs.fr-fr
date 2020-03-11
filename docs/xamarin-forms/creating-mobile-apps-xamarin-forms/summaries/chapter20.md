---
title: Résumé du chapitre 20. E/s de fichier et asynchrone
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 20. E/s de fichier et asynchrone'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771039"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Résumé du chapitre 20. E/s de fichier et asynchrone

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

 Une interface graphique utilisateur doit répondre aux événements d’entrée d’utilisateur séquentiellement. Cela implique que tous les traitements des événements d’entrée utilisateur doivent se produire dans un thread unique, souvent appelé *thread principal* ou *thread d’interface utilisateur*.

Les utilisateurs attendent des interfaces graphiques utilisateur d’être réactives. Cela signifie qu’un programme doit traiter rapidement les événements d’entrée de l’utilisateur. Si cela n’est pas possible, le traitement puis doit être relégué aux threads secondaires de l’exécution.

Plusieurs exemples de programmes dans ce livre ont utilisé la classe [`WebRequest`](xref:System.Net.WebRequest) . Dans cette classe, la méthode [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) démarre un thread de travail, qui appelle une fonction de rappel lorsqu’elle est terminée. Toutefois, cette fonction de rappel s’exécute dans le thread de travail, donc le programme doit appeler [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) méthode pour accéder à l’interface utilisateur.

> [!NOTE]
> Les programmes Xamarin. Forms doivent utiliser [`HttpClient`](xref:System.Net.Http.HttpClient) plutôt que des [`WebRequest`](xref:System.Net.WebRequest) pour accéder aux fichiers sur Internet. `HttpClient` prend en charge les opérations asynchrones.

Une approche plus moderne pour le traitement asynchrone est disponible dans .NET et c#. Cela implique les classes [`Task`](xref:System.Threading.Tasks.Task) et [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) , ainsi que d’autres types dans les espaces de noms [`System.Threading`](xref:System.Threading) et [`System.Threading.Tasks`](xref:System.Threading.Tasks) , ainsi C# que les mots clés 5,0 `async` et `await`. C’est ce que ce chapitre se concentre sur.

## <a name="from-callbacks-to-await"></a>À partir des rappels pour attendre

La classe `Page` elle-même contient trois méthodes asynchrones pour afficher des zones d’alerte :

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) retourne un objet `Task`
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) retourne un objet `Task<bool>`
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) retourne un objet `Task<string>`

Les objets `Task` indiquent que ces méthodes implémentent le modèle asynchrone basé sur des tâches, appelé TAP. Ces objets `Task` sont retournés rapidement à partir de la méthode. Les `Task<T>` les valeurs de retour constituent une « promesse » qu’une valeur de type `TResult` sera disponible lorsque la tâche se termine. La valeur de retour de `Task` indique une action asynchrone qui se termine, mais sans valeur retournée.

Dans tous les cas, la `Task` est terminée lorsque l’utilisateur ferme la boîte de message d’alerte.  

### <a name="an-alert-with-callbacks"></a>Une alerte avec des rappels

L’exemple [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) montre comment gérer `Task<bool>` retourner des objets et des appels de `Device.BeginInvokeOnMainThread` à l’aide de méthodes de rappel.

### <a name="an-alert-with-lambdas"></a>Une alerte avec les expressions lambda

L’exemple [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) montre comment utiliser des fonctions lambda anonymes pour gérer les appels `Task` et `Device.BeginInvokeOnMainThread`.  

### <a name="an-alert-with-await"></a>Une alerte avec await

Une approche plus simple implique les mots clés `async` et `await` introduits dans C# 5. L’exemple [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) illustre son utilisation.

### <a name="an-alert-with-nothing"></a>Une alerte sans rien

Si la méthode asynchrone retourne `Task` plutôt que `Task<TResult>`, le programme n’a pas besoin d’utiliser l’une de ces techniques s’il n’a pas besoin de savoir quand la tâche asynchrone se termine. L’exemple [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) illustre cela.

### <a name="saving-program-settings-asynchronously"></a>Enregistrement des paramètres de programme en mode asynchrone

L’exemple [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) illustre l’utilisation de la méthode [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) de `Application` pour enregistrer les paramètres du programme à mesure qu’ils changent sans substituer la méthode `OnSleep`.

### <a name="a-platform-independent-timer"></a>Un minuteur indépendant de la plateforme

Il est possible d’utiliser [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) pour créer un minuteur indépendant de la plateforme. L’exemple [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) illustre cela.

## <a name="file-inputoutput"></a>Fichier d’entrée/sortie

Traditionnellement, l’espace de noms .NET [`System.IO`](xref:System.IO) est la source de la prise en charge des e/s de fichier. Bien que certaines méthodes dans cet espace de noms prennent en charge les opérations asynchrones, la plupart ne le faites pas. L’espace de noms prend également en charge plusieurs appels de méthode simple qui exécutent des fonctions d’e/s de fichier sophistiquées.

### <a name="good-news-and-bad-news"></a>Une bonne et mauvaise nouvelle

Toutes les plateformes prises en charge par Xamarin. Forms prennent en charge le stockage local des applications &mdash; le stockage qui est privé pour l’application.

Les bibliothèques Xamarin.iOS et Xamarin.Android incluent une version du .NET Xamarin a expressément adaptées à ces deux plateformes. Celles-ci incluent des classes de `System.IO` que vous pouvez utiliser pour effectuer des e/s de fichier avec le stockage local d’applications dans ces deux plateformes.

Toutefois, si vous recherchez ces `System.IO` des classes dans un PCL Xamarin. Forms, vous ne les trouverez pas. Le problème est ce fichier d’e/s de Microsoft complètement repensée pour l’API de Runtime de Windows. Les programmes ciblant Windows 8.1, Windows Phone 8,1 et les plateforme Windows universelle n’utilisent pas `System.IO` pour les e/s de fichier.

Cela signifie que vous devez utiliser le [`DependencyService`](xref:Xamarin.Forms.DependencyService) (abord abordé dans le [**chapitre 9. Appels d’API spécifiques**](chapter09.md) à la plateforme pour implémenter les e/s de fichier.

> [!NOTE]
> La classe portable libaries a été remplacée par .NET Standard bibliothèques 2,0 et .NET Standard 2,0 prend en charge les types de [`System.IO`](xref:System.IO) pour toutes les plateformes Xamarin. Forms. Il n’est plus nécessaire d’utiliser un `DependencyService` pour la plupart des tâches d’e/s de fichier. Consultez [gestion des fichiers dans Xamarin. Forms](~/xamarin-forms/data-cloud/data/files.md) pour une approche plus moderne des e/s de fichier.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Une première tentative de fichier inter-plateformes d’e/s

L’exemple [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) définit une interface [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) pour les e/s de fichier et les implémentations de cette interface dans toutes les plateformes. Toutefois, les implémentations de Windows Runtime ne fonctionnent pas avec les méthodes dans cette interface, car les méthodes d’e/s de fichier Windows Runtime sont asynchrones.

### <a name="accommodating-windows-runtime-file-io"></a>Prise en charge d’e/s de fichier Windows Runtime

Les programmes qui s’exécutent sous le Windows Runtime utilisent des classes dans les espaces de noms [`Windows.Storage`](/uwp/api/Windows.Storage) et [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) pour les e/s de fichier, y compris le stockage local des applications. Étant donné que Microsoft déterminé que toute opération qui requiert que plus de 50 millisecondes doivent être asynchrones pour éviter de bloquer le thread d’interface utilisateur, ces méthodes d’e/s de fichier sont principalement asynchrones.

Le code qui illustre cette nouvelle approche sera dans une bibliothèque afin qu’il peut être utilisé par d’autres applications.

## <a name="platform-specific-libraries"></a>Bibliothèques propres à une plateforme

Il est préférable de stocker le code réutilisable dans des bibliothèques. Il s’agit évidemment plus difficile lorsque différentes parties du code réutilisable sont pour les systèmes d’exploitation entièrement différents.

La solution [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) illustre une approche. Cette solution contient sept projets différents :

- [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), un PCL Xamarin. Forms normal
- [**Xamarin. FormsBook. Platform. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), bibliothèque de classes iOS
- [**Xamarin. FormsBook. Platform. Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), bibliothèque de classes Android
- [**Xamarin. FormsBook. Platform. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), bibliothèque de classes Windows universelle
- [**Xamarin. FormsBook. Platform. WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), projet partagé pour le code qui est commun à toutes les plateformes Windows

Tous les projets de plateforme individuels (à l’exception de **Xamarin. FormsBook. Platform. WinRT**) ont des références à **Xamarin. FormsBook. Platform**. Les trois projets Windows ont une référence à **Xamarin. FormsBook. Platform. WinRT**.

Tous les projets contiennent une méthode `Toolkit.Init` statique pour garantir que la bibliothèque est chargée si elle n’est pas directement référencée par un projet dans une solution d’application Xamarin. Forms.

Le projet **Xamarin. FormsBook. Platform** contient la nouvelle interface de [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) . Toutes les méthodes ont maintenant des noms avec des suffixes de `Async` et retournent des objets `Task`.

Le projet **Xamarin. FormsBook. Platform. WinRT** contient la classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) pour le Windows Runtime.

Le projet **Xamarin. FormsBook. Platform. iOS** contient la classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) pour iOS. Ces méthodes doivent maintenant être asynchrones. Certaines méthodes utilisent les versions asynchrones des méthodes définies dans `StreamWriter` et `StreamReader`: [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) et [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync). D’autres convertissent un résultat en objet `Task` à l’aide de la méthode [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) .

Le projet **Xamarin. FormsBook. Platform. Android** contient une classe de [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) similaire pour Android.

Le projet **Xamarin. FormsBook. Platform** contient également une classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) qui facilite l’utilisation de l’objet `DependencyService`.

Pour utiliser ces bibliothèques, une solution d’application doit inclure tous les projets de la solution **Xamarin. FormsBook. Platform** , et chacun des projets d’application doit avoir une référence à la bibliothèque correspondante dans **Xamarin. FormsBook. Platform**.

La solution [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) montre comment utiliser les bibliothèques **Xamarin. FormsBook. Platform** . Chacun des projets a un appel à `Toolkit.Init`. L’application se sert de l’e/s de fichier asynchrone fonctions.

### <a name="keeping-it-in-the-background"></a>En conservant en arrière-plan

Les méthodes des bibliothèques qui effectuent des appels à plusieurs méthodes asynchrones &mdash; telles que les méthodes `WriteFileAsync` et `ReadFileASync` de la classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) Windows Runtime &mdash; peuvent être rendues plus efficaces en utilisant la méthode [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) pour éviter de revenir au thread d’interface utilisateur.

### <a name="dont-block-the-ui-thread"></a>Ne pas bloquer le thread d’interface utilisateur !

Parfois, il est tentant d’éviter l’utilisation de `ContinueWith` ou `await` à l’aide de la propriété [`Result`](xref:System.Threading.Tasks.Task`1.Result) sur les méthodes. Cela doit être évitée pour qu’il peut bloquer le thread d’interface utilisateur ou même bloquer l’application.

## <a name="your-own-awaitable-methods"></a>Vos propres méthodes pouvant être attendus

Vous pouvez exécuter du code de façon asynchrone en le passant à l’une des méthodes [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) . Vous pouvez appeler `Task.Run` dans une méthode Async qui gère une partie de la surcharge.

Les différents modèles de `Task.Run` sont présentés ci-dessous.

### <a name="the-basic-mandelbrot-set"></a>L’ensemble de Mandelbrot base

Pour dessiner le groupe Mandelbrot en temps réel, la bibliothèque [**Xamarin. Forms. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) a une structure [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) similaire à celle de l’espace de noms `System.Numerics`.

L’exemple [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) a une méthode `CalculateMandeblotAsync` dans son fichier code-behind qui calcule le jeu de Mandelbrot noir et blanc de base et utilise [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) pour le placer sur une bitmap.

### <a name="marking-progress"></a>Marquage de progression

Pour signaler la progression d’une méthode asynchrone, vous pouvez instancier une classe [`Progress<T>`](xref:System.Progress`1) et définir votre méthode asynchrone pour avoir un argument de type [`IProgress<T>`](xref:System.IProgress`1). Cela est illustré dans l’exemple [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) .

### <a name="cancelling-the-job"></a>Annulation de la tâche

Vous pouvez également écrire une méthode asynchrone pour être annulable. Vous commencez par une classe nommée [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource). La propriété [`Token`](xref:System.Threading.CancellationTokenSource.Token) est une valeur de type [`CancellationToken`](xref:System.Threading.CancellationToken). Ces données sont transmises à la fonction asynchrone. Un programme appelle la méthode [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) de `CancellationTokenSource` (généralement en réponse à une action de l’utilisateur) pour annuler la fonction asynchrone.

La méthode asynchrone peut vérifier périodiquement la propriété [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) de `CancellationToken` et quitter si la propriété est `true`, ou simplement appeler la méthode [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) , auquel cas la méthode se termine par une [`OperationCancelledException`](xref:System.OperationCanceledException).

L’exemple [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) illustre l’utilisation d’une fonction annulable.

### <a name="an-mvvm-mandelbrot"></a>Un ensemble de Mandelbrot MVVM

L’exemple [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) a une interface utilisateur plus étendue et est principalement basé sur une [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) et des classes [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) :

[![Capture triple de Mandelbrot X F](images/ch20fg13-small.png "Mandelbrot MVVM")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Dans le web

La classe [`WebRequest`](xref:System.Net.WebRequest) utilisée dans certains exemples utilise un ancien protocole asynchrone appelé modèle de programmation asynchrone ou APM. Vous pouvez convertir une telle classe en protocole TAP moderne à l’aide de l’une des méthodes `FromAsync` de la classe [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) . L’exemple [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) illustre cela.

## <a name="related-links"></a>Liens connexes

- [Chapitre 20 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Chapitre 20 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Utilisation de fichiers](~/xamarin-forms/data-cloud/data/files.md)
