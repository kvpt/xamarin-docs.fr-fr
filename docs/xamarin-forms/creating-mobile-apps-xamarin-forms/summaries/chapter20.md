---
title: Résumé du chapitre 20. E/s de fichier et asynchrone
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 20. E/s de fichier et asynchrone'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771039"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Résumé du chapitre 20. E/s de fichier et asynchrone

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

 Une interface graphique utilisateur doit répondre aux événements d’entrée d’utilisateur séquentiellement. Cela implique que tout le traitement des événements d’entrée de l’utilisateur doit se trouver dans un thread unique, souvent appelé le *thread principal* ou *thread d’interface utilisateur*.

Les utilisateurs attendent des interfaces graphiques utilisateur d’être réactives. Cela signifie qu’un programme doit traiter rapidement les événements d’entrée de l’utilisateur. Si cela n’est pas possible, le traitement puis doit être relégué aux threads secondaires de l’exécution.

Plusieurs exemples de programmes dans ce livre ont utilisé le [ `WebRequest` ](xref:System.Net.WebRequest) classe. Dans cette classe le [ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) méthode démarre un thread de travail, qui appelle une fonction de rappel lorsqu’il est terminé. Toutefois, cette fonction de rappel s’exécute dans le thread de travail, le programme doit donc appeler [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) méthode pour accéder à l’interface utilisateur.

> [!NOTE]
> Xamarin.Forms les programmes doivent utiliser [ `HttpClient` ](xref:System.Net.Http.HttpClient) plutôt que [ `WebRequest` ](xref:System.Net.WebRequest) pour accéder aux fichiers via internet. `HttpClient` prend en charge les opérations asynchrones.

Une approche plus moderne pour le traitement asynchrone est disponible dans .NET et c#. Cela implique le [ `Task` ](xref:System.Threading.Tasks.Task) et [ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1) classes et autres types dans le [ `System.Threading` ](xref:System.Threading) et [ `System.Threading.Tasks` ](xref:System.Threading.Tasks) espaces de noms, ainsi que le C# 5.0 `async` et `await` mots clés. C’est ce que ce chapitre se concentre sur.

## <a name="from-callbacks-to-await"></a>À partir des rappels pour attendre

Le `Page` classe elle-même contient trois méthodes asynchrones pour afficher les zones de l’alerte :

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Retourne un `Task` objet
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) Retourne un `Task<bool>` objet
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) Retourne un `Task<string>` objet

Le `Task` objets indiquent que ces méthodes implémentent le modèle asynchrone basé sur une tâche, appelé TAP. Ces `Task` objets sont retournés rapidement à partir de la méthode. Le `Task<T>` retournent des valeurs constituent la « promesse » qui une valeur de type `TResult` seront disponibles lorsque la tâche se termine. Le `Task` valeur renvoyée indique une action asynchrone sera terminée, mais aucune valeur n’est retournée.

Dans tous ces cas, le `Task` est terminée lorsque l’utilisateur ferme la fenêtre d’alerte.  

### <a name="an-alert-with-callbacks"></a>Une alerte avec des rappels

Le [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) exemple montre comment gérer `Task<bool>` retournent des objets et `Device.BeginInvokeOnMainThread` appels à l’aide de méthodes de rappel.

### <a name="an-alert-with-lambdas"></a>Une alerte avec les expressions lambda

Le [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) exemple illustre l’utilisation des fonctions lambda anonyme pour la gestion des `Task` et `Device.BeginInvokeOnMainThread` appels.  

### <a name="an-alert-with-await"></a>Une alerte avec await

Une approche plus simple implique la `async` et `await` mots clés introduits dans c# 5. Le [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) exemple illustre son utilisation.

### <a name="an-alert-with-nothing"></a>Une alerte sans rien

Si la méthode asynchrone retourne `Task` plutôt que `Task<TResult>`, puis le programme n’a pas besoin d’utiliser une de ces techniques si elle n’a pas besoin de savoir quand la tâche asynchrone est terminée. Le [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) illustre cela.

### <a name="saving-program-settings-asynchronously"></a>Enregistrement des paramètres de programme en mode asynchrone

Le [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) exemple illustre l’utilisation de la [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) méthode de `Application` pour enregistrer les paramètres du programme mesure qu’elles changent sans substitution de la `OnSleep` (méthode).

### <a name="a-platform-independent-timer"></a>Un minuteur indépendant de la plateforme

Il est possible d’utiliser [ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32)) pour créer un minuteur indépendant de la plateforme. Le [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) illustre cela.

## <a name="file-inputoutput"></a>Fichier d’entrée/sortie

En règle générale, le .NET [ `System.IO` ](xref:System.IO) espace de noms a été la source de prise en charge d’e/s de fichier. Bien que certaines méthodes dans cet espace de noms prennent en charge les opérations asynchrones, la plupart ne le faites pas. L’espace de noms prend également en charge plusieurs appels de méthode simple qui exécutent des fonctions d’e/s de fichier sophistiquées.

### <a name="good-news-and-bad-news"></a>Une bonne et mauvaise nouvelle

Toutes les plateformes prises en charge par Xamarin.Forms prise en charge le stockage local application &mdash; stockage privé pour l’application.

Les bibliothèques Xamarin.iOS et Xamarin.Android incluent une version du .NET Xamarin a expressément adaptées à ces deux plateformes. Ceux-ci incluent des classes provenant `System.IO` que vous pouvez utiliser pour effectuer des e/s de fichier avec le stockage local des applications dans ces deux plateformes.

Toutefois, si vous recherchez ces `System.IO` classes dans une bibliothèque de classes portable Xamarin.Forms, vous ne trouverez pas les. Le problème est ce fichier d’e/s de Microsoft complètement repensée pour l’API de Runtime de Windows. Programmes qui ciblent Windows 8.1, Windows Phone 8.1 et la plateforme Windows universelle n’utilisent pas `System.IO` d’e/s de fichier.

Cela signifie que vous devrez utiliser le [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (abordé en [ **chapitre 9. Appels d’API spécifiques à la plateforme** ](chapter09.md) pour implémenter des e/s de fichier.

> [!NOTE]
> Les bibliothèques de classes portable ont été remplacés par les bibliothèques .NET Standard 2.0, et prend en charge de .NET Standard 2.0 [ `System.IO` ](xref:System.IO) types pour toutes les plateformes de Xamarin.Forms. Il n’est plus nécessaire d’utiliser un `DependencyService` pour la plupart des tâches d’e/s de fichier. Consultez [gestion des fichiers dans Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) pour une approche plus moderne d’e/s de fichier.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Une première tentative de fichier inter-plateformes d’e/s

Le [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) exemple définit un [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interface pour les e/s de fichier et les implémentations de cette interface dans toutes les plateformes. Toutefois, les implémentations de Windows Runtime ne fonctionnent pas avec les méthodes dans cette interface, car les méthodes d’e/s de fichier Windows Runtime sont asynchrones.

### <a name="accommodating-windows-runtime-file-io"></a>Prise en charge d’e/s de fichier Windows Runtime

Utilisent les classes de programmes s’exécutant sous le Runtime Windows le [ `Windows.Storage` ](/uwp/api/Windows.Storage) et [ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams) espaces de noms pour le fichier d’e/s, y compris le stockage local des applications. Étant donné que Microsoft déterminé que toute opération qui requiert que plus de 50 millisecondes doivent être asynchrones pour éviter de bloquer le thread d’interface utilisateur, ces méthodes d’e/s de fichier sont principalement asynchrones.

Le code qui illustre cette nouvelle approche sera dans une bibliothèque afin qu’il peut être utilisé par d’autres applications.

## <a name="platform-specific-libraries"></a>Bibliothèques propres à une plateforme

Il est préférable de stocker le code réutilisable dans des bibliothèques. Il s’agit évidemment plus difficile lorsque différentes parties du code réutilisable sont pour les systèmes d’exploitation entièrement différents.

Le [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solution illustre une approche. Cette solution contient sept projets différents :

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), une bibliothèque de classes portable Xamarin.Forms normal
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), une bibliothèque de classes iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), une bibliothèque de classes Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), une bibliothèque de classes Windows Universal
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un projet partagé pour le code qui est commune à toutes les plateformes Windows

Tous les projets de plateforme individuels (à l’exception de **Xamarin.FormsBook.Platform.WinRT**) ont des références à **Xamarin.FormsBook.Platform**. Les trois projets de Windows ont une référence à **Xamarin.FormsBook.Platform.WinRT**.

Tous les projets contiennent un statique `Toolkit.Init` méthode pour vous assurer que la bibliothèque est chargée s’il n’est pas directement référencé par un projet dans une solution d’application Xamarin.Forms.

Le **Xamarin.FormsBook.Platform** projet contient le nouvel [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interface. Toutes les méthodes ont maintenant des noms avec `Async` retour et les suffixes `Task` objets.

Le **Xamarin.FormsBook.Platform.WinRT** projet contient le [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe pour l’exécution de Windows.

Le **Xamarin.FormsBook.Platform.iOS** projet contient le [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe pour iOS. Ces méthodes doivent maintenant être asynchrones. Certaines des méthodes utilisent des versions asynchrones des méthodes définies dans `StreamWriter` et `StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String)) et [ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync). D’autres convertir un résultat pour un `Task` à l’aide de l’objet le [ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*) (méthode).

Le **Xamarin.FormsBook.Platform.Android** projet contient un texte similaire [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe pour Android.

Le **Xamarin.FormsBook.Platform** projet contient également un [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) classe qui facilite l’utilisation de la `DependencyService` objet.

Pour utiliser ces bibliothèques, une solution d’application doit inclure tous les projets de la **Xamarin.FormsBook.Platform** solution et chacun des projets d’application doivent avoir une référence à la bibliothèque correspondante dans  **Xamarin.FormsBook.Platform**.

Le [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) solution montre comment utiliser le **Xamarin.FormsBook.Platform** bibliothèques. Chacun des projets a un appel à `Toolkit.Init`. L’application se sert de l’e/s de fichier asynchrone fonctions.

### <a name="keeping-it-in-the-background"></a>En conservant en arrière-plan

Méthodes dans les bibliothèques qui effectuent des appels à plusieurs méthodes asynchrones &mdash; tels que le `WriteFileAsync` et `ReadFileASync` méthodes dans le Runtime Windows [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe &mdash; peut être effectuée quelque plus efficace à l’aide de la [ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) méthode afin d’éviter de basculer vers le thread d’interface utilisateur.

### <a name="dont-block-the-ui-thread"></a>Ne pas bloquer le thread d’interface utilisateur !

Parfois, il est tentant afin d’éviter l’utilisation de `ContinueWith` ou `await` à l’aide de la [ `Result` ](xref:System.Threading.Tasks.Task`1.Result) propriété sur les méthodes. Cela doit être évitée pour qu’il peut bloquer le thread d’interface utilisateur ou même bloquer l’application.

## <a name="your-own-awaitable-methods"></a>Vos propres méthodes pouvant être attendus

Vous pouvez exécuter du code de façon asynchrone en le passant à une de la [ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action)) méthodes. Vous pouvez appeler `Task.Run` au sein d’une méthode async qui gère la partie de la surcharge.

Les différents `Task.Run` modèles sont décrits ci-dessous.

### <a name="the-basic-mandelbrot-set"></a>L’ensemble de Mandelbrot base

Pour dessiner l’ensemble en temps réel, de Mandelbrot le [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque possède un [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) structure similaire à celui de la `System.Numerics` espace de noms.

Le [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) exemple comporte un `CalculateMandeblotAsync` méthode dans son fichier code-behind qui calcule l’ensemble de Mandelbrot base noir et blanc et utilise [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)placer sur une image bitmap.

### <a name="marking-progress"></a>Marquage de progression

Pour signaler la progression à partir d’une méthode asynchrone, vous pouvez instancier un [ `Progress<T>` ](xref:System.Progress`1) classe et de définir votre méthode asynchrone pour avoir un argument de type [ `IProgress<T>` ](xref:System.IProgress`1). Cela est illustré dans le [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) exemple.

### <a name="cancelling-the-job"></a>Annulation de la tâche

Vous pouvez également écrire une méthode asynchrone pour être annulable. Commencer avec une classe nommée [ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource). Le [ `Token` ](xref:System.Threading.CancellationTokenSource.Token) propriété est une valeur de type [ `CancellationToken` ](xref:System.Threading.CancellationToken). Ces données sont transmises à la fonction asynchrone. Un programme appelle la [ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel) méthode de `CancellationTokenSource` (généralement en réponse à une action de l’utilisateur) pour annuler la fonction asynchrone.

La méthode asynchrone peut vérifier périodiquement la [ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested) propriété du `CancellationToken` et quitter si la propriété est `true`, ou simplement appeler le [ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) (méthode), dans auquel cas la méthode se termine par un [ `OperationCancelledException` ](xref:System.OperationCanceledException).

Le [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) exemple illustre l’utilisation d’une fonction pouvant être annulée.

### <a name="an-mvvm-mandelbrot"></a>Un ensemble de Mandelbrot MVVM

Le [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) exemple a une interface utilisateur plus étendue, et il est principalement basé sur un [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) et [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)classes :

[![Capture d’écran triple de Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>Dans le web

Le [ `WebRequest` ](xref:System.Net.WebRequest) classe utilisée dans certains exemples utilise un ancienne protocole asynchrone appelé modèle de programmation asynchrone ou APM. Vous pouvez convertir une telle classe au protocole TAP modern à l’aide d’un de le `FromAsync` méthodes dans le [ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1) classe. Le [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) illustre cela.

## <a name="related-links"></a>Liens connexes

- [Chapitre 20 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Chapitre 20 échantillons](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Utilisation de fichiers](~/xamarin-forms/data-cloud/data/files.md)
