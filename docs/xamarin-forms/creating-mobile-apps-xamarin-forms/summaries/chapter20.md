---
title: Résumé du chapitre 20. E/S de fichier et asynchrone
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 20. E/S de fichier et asynchrone'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771039"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Résumé du chapitre 20. E/S de fichier et asynchrone

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

 Une interface utilisateur graphique doit répondre séquentiellement aux événements d’entrée utilisateur. Cela implique que tout traitement des événements d’entrée utilisateur doit se produire dans un seul thread, souvent appelé le *fil principal* ou le *thread d’interface utilisateur*.

Les utilisateurs s’attendent à ce que les interfaces utilisateur graphiques soient réactives. Cela signifie qu’un programme doit traiter rapidement les événements d’entrée utilisateur. Si cela n’est pas possible, le traitement doit être relégué aux fils secondaires de l’exécution.

Plusieurs exemples de programmes [`WebRequest`](xref:System.Net.WebRequest) de ce livre ont utilisé la classe. Dans cette [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) classe, la méthode démarre un thread de travailleur, qui appelle une fonction de rappel quand il est complet. Cependant, cette fonction de rappel s’exécute dans [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) le thread du travailleur, de sorte que le programme doit appeler la méthode pour accéder à l’interface utilisateur.

> [!NOTE]
> Les programmes Xamarin.Forms devraient être utilisés [`HttpClient`](xref:System.Net.Http.HttpClient) plutôt que [`WebRequest`](xref:System.Net.WebRequest) pour accéder aux fichiers sur Internet. `HttpClient`entreprises asynchrones.

Une approche plus moderne du traitement asynchrone est disponible en .NET et en C. Cela implique [`Task`](xref:System.Threading.Tasks.Task) [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) les classes et les [`System.Threading`](xref:System.Threading) [`System.Threading.Tasks`](xref:System.Threading.Tasks) classes, et d’autres types dans les `async` espaces et les noms, ainsi que le C 5.0 et `await` les mots clés. C’est ce sur quoi se concentre ce chapitre.

## <a name="from-callbacks-to-await"></a>Des rappels à l’attente

La `Page` classe elle-même contient trois méthodes asynchrones pour afficher des boîtes d’alerte :

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))retourne `Task` un objet
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))retourne `Task<bool>` un objet
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))retourne `Task<string>` un objet

Les `Task` objets indiquent que ces méthodes implémentent le modèle asynchrone basé sur les tâches, connu sous le nom de TAP. Ces `Task` objets sont rapidement retournés de la méthode. Les `Task<T>` valeurs de rendement constituent une « `TResult` promesse » qu’une valeur de type sera disponible lorsque la tâche sera terminée. La `Task` valeur de rendement indique une action asynchrone qui se terminera mais sans valeur retournée.

Dans tous ces `Task` cas, le est complet lorsque l’utilisateur rejette la boîte d’alerte.  

### <a name="an-alert-with-callbacks"></a>Une alerte avec rappels

[**L’échantillon AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) montre `Task<bool>` comment gérer `Device.BeginInvokeOnMainThread` les objets de retour et les appels à l’aide de méthodes de rappel.

### <a name="an-alert-with-lambdas"></a>Une alerte avec des lambdas

[**L’échantillon AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) montre comment utiliser les fonctions `Device.BeginInvokeOnMainThread` lambda anonymes pour la manipulation et les `Task` appels.  

### <a name="an-alert-with-await"></a>Une alerte avec l’attente

Une approche plus `async` simple `await` implique les mots clés et les mots clés introduits dans le C 5. [**L’échantillon AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) démontre leur utilisation.

### <a name="an-alert-with-nothing"></a>Une alerte sans rien

Si la méthode asynchrone `Task` `Task<TResult>`revient plutôt que, alors le programme n’a pas besoin d’utiliser l’une de ces techniques si elle n’a pas besoin de savoir quand la tâche asynchrone se termine. [**L’échantillon NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) le démontre.

### <a name="saving-program-settings-asynchronously"></a>Enregistrer les paramètres du programme asynchronement

[**L’échantillon SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) démontre l’utilisation de la [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) méthode d’enregistrement des paramètres du `Application` programme au fur et à mesure qu’ils changent sans passer outre à la `OnSleep` méthode.

### <a name="a-platform-independent-timer"></a>Un minuteur indépendant de plate-forme

Il est possible [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) d’utiliser pour créer une minuterie indépendante de plate-forme. [**L’échantillon TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) le démontre.

## <a name="file-inputoutput"></a>Entrée/sortie de fichier

Traditionnellement, l’espace nom .NET [`System.IO`](xref:System.IO) a été la source de fichier I / O soutien. Bien que certaines méthodes dans ce namespace soutenir les opérations asynchrones, la plupart ne le font pas. L’espace de nom prend également en charge plusieurs appels de méthode simples qui exécutent des fonctions sophistiquées de fichier I/O.

### <a name="good-news-and-bad-news"></a>Bonnes et mauvaises nouvelles

Toutes les plates-formes supportées par Xamarin.Forms prennent en charge le stockage de stockage &mdash; local d’application qui est privé de l’application.

Les bibliothèques Xamarin.iOS et Xamarin.Android comprennent une version de .NET que Xamarin a expressément adapté pour ces deux plates-formes. Il s’agit notamment de classes à partir de `System.IO` ce que vous pouvez utiliser pour effectuer le fichier I / O avec le stockage local application dans ces deux plates-formes.

Cependant, si vous `System.IO` recherchez ces classes dans un PCL Xamarin.Forms, vous ne les trouverez pas. Le problème est que Microsoft complètement remanié fichier I / O pour l’API Windows Runtime. Les programmes ciblant Windows 8.1, Windows Phone 8.1 `System.IO` et la plate-forme Windows universelle ne sont pas utilisés pour le fichier I/O.

Cela signifie que vous aurez [`DependencyService`](xref:Xamarin.Forms.DependencyService) besoin d’utiliser le (d’abord discuté dans [**le chapitre 9. L’API spécifique à la plate-forme appelle**](chapter09.md) à implémenter le fichier I/O.

> [!NOTE]
> Les libaries de classe portable ont été remplacées par des bibliothèques .NET Standard [`System.IO`](xref:System.IO) 2.0, et des types de supports .NET Standard 2.0 pour toutes les plateformes Xamarin.Forms. Il n’est plus `DependencyService` nécessaire d’utiliser un pour la plupart des tâches I/O de fichier. Voir [La manipulation des fichiers dans Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) pour une approche plus moderne pour classer I/O.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Un premier coup au fichier multi-plateforme I/O

[**L’échantillon TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) définit une interface pour le fichier I/O, et les implémentations de cette interface sur toutes les plates-formes. Cependant, les implémentations Windows Runtime ne fonctionnent pas avec les méthodes de cette interface parce que le fichier Windows Runtime I / O méthodes sont asynchrones.

### <a name="accommodating-windows-runtime-file-io"></a>Accommoder le fichier Windows Runtime I/O

Les programmes fonctionnant sous windows [`Windows.Storage`](/uwp/api/Windows.Storage) Runtime utilisent des classes dans les espaces et [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) les noms pour le fichier I/O, y compris le stockage local d’application. Parce que Microsoft a déterminé que toute opération nécessitant plus de 50 millisecondes devrait être asynchrone pour éviter de bloquer le fil d’interface utilisateur, ces méthodes de fichier I / O sont pour la plupart asynchrone.

Le code démontrant cette nouvelle approche sera dans une bibliothèque afin qu’elle puisse être utilisée par d’autres applications.

## <a name="platform-specific-libraries"></a>Bibliothèques propres à une plateforme

Il est avantageux de stocker du code réutilisable dans les bibliothèques. Cela est évidemment plus difficile lorsque différents morceaux du code réutilisable sont pour des systèmes d’exploitation entièrement différents.

La solution [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) démontre une approche. Cette solution contient sept projets différents :

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), un Xamarin.Forms PCL normal
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), une bibliothèque de classe iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), une bibliothèque de classe Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), une bibliothèque de classe Windows universelle
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un projet partagé pour le code qui est commun à toutes les plates-formes Windows

Tous les projets de plate-forme individuels (à l’exception de **Xamarin.FormsBook.Platform.WinRT**) ont des références à **Xamarin.FormsBook.Platform**. Les trois projets Windows ont une référence à **Xamarin.FormsBook.Platform.WinRT**.

Tous les projets `Toolkit.Init` contiennent une méthode statique pour s’assurer que la bibliothèque est chargée si elle n’est pas directement référencée par un projet dans une solution d’application Xamarin.Forms.

Le projet **Xamarin.FormsBook.Platform** [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) contient la nouvelle interface. Toutes les méthodes ont `Async` maintenant des `Task` noms avec des suffixes et des objets de retour.

Le projet **Xamarin.FormsBook.Platform.WinRT** contient la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe pour le Windows Runtime.

Le projet **Xamarin.FormsBook.Platform.iOS** contient la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe pour iOS. Ces méthodes doivent maintenant être asynchrones. Certaines des méthodes utilisent les versions asynchrones `StreamReader` [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) de [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync)méthodes définies dans `StreamWriter` et : et . D’autres convertissent `Task` un [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) résultat en objet en utilisant la méthode.

Le projet **Xamarin.FormsBook.Platform.Android** [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) contient une classe similaire pour Android.

Le projet **Xamarin.FormsBook.Platform** [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) contient également une classe `DependencyService` qui facilite l’utilisation de l’objet.

Pour utiliser ces bibliothèques, une solution d’application doit inclure tous les projets dans la solution **Xamarin.FormsBook.Platform,** et chacun des projets d’application doit avoir une référence à la bibliothèque correspondante dans **Xamarin.FormsBook.Platform**.

La solution [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) montre comment utiliser les bibliothèques **Xamarin.FormsBook.Platform.** Chacun des projets a `Toolkit.Init`un appel à . L’application utilise les fonctions asynchrones I/O du fichier.

### <a name="keeping-it-in-the-background"></a>Le garder en arrière-plan

Les méthodes dans les bibliothèques qui font &mdash; des appels `WriteFileAsync` `ReadFileASync` à plusieurs méthodes [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) asynchrones telles que la classe et les méthodes dans la classe &mdash; Windows Runtime peuvent être rendues un peu plus efficaces en utilisant la [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) méthode pour éviter de revenir au thread d’interface utilisateur.

### <a name="dont-block-the-ui-thread"></a>Ne bloquez pas le fil d’interface utilisateur!

Parfois, il est tentant d’éviter l’utilisation ou `ContinueWith` `await` en utilisant la [`Result`](xref:System.Threading.Tasks.Task`1.Result) propriété sur les méthodes. Cela doit être évité car il peut bloquer le thread d’interface utilisateur ou même accrocher l’application.

## <a name="your-own-awaitable-methods"></a>Vos propres méthodes en attente

Vous pouvez exécuter un peu de code asynchronement en le [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) passant à l’une des méthodes. Vous pouvez `Task.Run` appeler dans une méthode async qui gère certains des frais généraux.

Les `Task.Run` différents modèles sont discutés ci-dessous.

### <a name="the-basic-mandelbrot-set"></a>L’ensemble mandelbrot de base

Pour dessiner l’ensemble Mandelbrot en temps réel, la bibliothèque [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) [**Xamarin.Forms.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) a une structure similaire à celle de l’espace nom. `System.Numerics`

[**L’échantillon MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) a une `CalculateMandeblotAsync` méthode dans son fichier de code-derrière qui calcule [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) l’ensemble de base noir et blanc Mandelbrot et utilise pour le mettre sur un bitmap.

### <a name="marking-progress"></a>Marquage des progrès

Pour signaler les progrès d’une méthode asynchrone, vous pouvez instantané une [`Progress<T>`](xref:System.Progress`1) classe et définir [`IProgress<T>`](xref:System.IProgress`1)votre méthode asynchrone pour avoir un argument de type . Ceci est démontré dans l’échantillon [**de MandelbrotProgress.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress)

### <a name="cancelling-the-job"></a>Annulation du travail

Vous pouvez également écrire une méthode asynchrone pour être cancellable. Vous commencez par [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource)un cours nommé . La [`Token`](xref:System.Threading.CancellationTokenSource.Token) propriété est une [`CancellationToken`](xref:System.Threading.CancellationToken)valeur de type . Ceci est passé à la fonction asynchrone. Un programme [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) appelle `CancellationTokenSource` la méthode de (généralement en réponse à une action de l’utilisateur) pour annuler la fonction asynchrone.

La méthode asynchrone peut vérifier [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) `CancellationToken` périodiquement la propriété `true`et la sortie [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) si la propriété est, [`OperationCancelledException`](xref:System.OperationCanceledException)ou tout simplement appeler la méthode, auquel cas la méthode se termine par un .

[**L’échantillon MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) démontre l’utilisation d’une fonction cancellable.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

[**L’échantillon MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) dispose d’une interface utilisateur plus [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) étendue, et il est principalement basé sur un et [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) les classes:

[![Triple capture d’écran de Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>Retour sur le web

La [`WebRequest`](xref:System.Net.WebRequest) classe utilisée dans certains échantillons utilise un protocole asynchrone à l’ancienne appelé le modèle de programmation asynchrone ou APM. Vous pouvez convertir une telle classe au protocole `FromAsync` TAP [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) moderne en utilisant l’une des méthodes de la classe. L’échantillon [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) le démontre.

## <a name="related-links"></a>Liens connexes

- [Chapitre 20 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Échantillons du chapitre 20](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Utilisation de fichiers](~/xamarin-forms/data-cloud/data/files.md)
