---
titre : «Résumé du chapitre 20. Async et e/s de fichier» Description : «création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 20. Async et e/s de fichier "ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : D595862D-64FD-4C0D-B0AD-C1F440564247 auteur : davidbritch ms. Author : dabritch ms. Date : 07/18/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-20-async-and-file-io"></a>Résumé du chapitre 20. E/S de fichier et asynchrone

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

 Une interface utilisateur graphique doit répondre séquentiellement aux événements d’entrée d’utilisateur. Cela implique que tous les traitements des événements d’entrée utilisateur doivent se produire dans un thread unique, souvent appelé *thread principal* ou *thread d’interface utilisateur*.

Les utilisateurs s’attendent à ce que les interfaces utilisateur graphiques soient réactives. Cela signifie qu’un programme doit traiter rapidement les événements d’entrée d’utilisateur. Si ce n’est pas possible, le traitement doit être redélégué aux threads d’exécution secondaires.

Plusieurs exemples de programmes de ce livre ont utilisé la [`WebRequest`](xref:System.Net.WebRequest) classe. Dans cette classe [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) , la méthode démarre un thread de travail, qui appelle une fonction de rappel lorsqu’elle est terminée. Toutefois, cette fonction de rappel s’exécute dans le thread de travail, donc le programme doit appeler [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) la méthode pour accéder à l’interface utilisateur.

> [!NOTE]
> Xamarin.Formsles programmes doivent utiliser [`HttpClient`](xref:System.Net.Http.HttpClient) plutôt que [`WebRequest`](xref:System.Net.WebRequest) pour accéder aux fichiers sur Internet. `HttpClient`prend en charge les opérations asynchrones.

Une approche plus moderne du traitement asynchrone est disponible dans .NET et C#. Cela implique les [`Task`](xref:System.Threading.Tasks.Task) [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) classes et, ainsi que d’autres types dans les [`System.Threading`](xref:System.Threading) espaces de noms et, ainsi [`System.Threading.Tasks`](xref:System.Threading.Tasks) que les `async` Mots clés C# 5,0 et `await` . C’est ce que ce chapitre se concentre.

## <a name="from-callbacks-to-await"></a>Des rappels à await

La `Page` classe elle-même contient trois méthodes asynchrones pour afficher des zones d’alerte :

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))retourne un `Task` objet.
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))retourne un `Task<bool>` objet.
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))retourne un `Task<string>` objet.

Les `Task` objets indiquent que ces méthodes implémentent le modèle asynchrone basé sur des tâches, appelé TAP. Ces `Task` objets sont retournés rapidement à partir de la méthode. Les `Task<T>` valeurs de retour constituent une « promesse » qu’une valeur de type `TResult` sera disponible quand la tâche se termine. La `Task` valeur de retour indique une action asynchrone qui se termine, mais pour laquelle aucune valeur n’est retournée.

Dans tous ces cas, le `Task` est terminé lorsque l’utilisateur ferme la boîte de message d’alerte.  

### <a name="an-alert-with-callbacks"></a>Alerte avec rappels

L’exemple [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) montre comment gérer les `Task<bool>` objets de retour et les `Device.BeginInvokeOnMainThread` appels à l’aide de méthodes de rappel.

### <a name="an-alert-with-lambdas"></a>Alerte avec des expressions lambda

L’exemple [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) montre comment utiliser des fonctions lambda anonymes pour la gestion `Task` et les `Device.BeginInvokeOnMainThread` appels.  

### <a name="an-alert-with-await"></a>Une alerte avec await

Une approche plus simple implique les `async` `await` Mots clés et introduits dans C# 5. L’exemple [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) illustre son utilisation.

### <a name="an-alert-with-nothing"></a>Alerte sans rien

Si la méthode asynchrone retourne `Task` plutôt que `Task<TResult>` , le programme n’a pas besoin d’utiliser l’une de ces techniques si elle n’a pas besoin de savoir quand la tâche asynchrone se termine. L’exemple [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) illustre cela.

### <a name="saving-program-settings-asynchronously"></a>Enregistrement des paramètres du programme de manière asynchrone

L’exemple [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) illustre l’utilisation de la [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) méthode de `Application` pour enregistrer les paramètres du programme à mesure qu’ils changent sans substituer la `OnSleep` méthode.

### <a name="a-platform-independent-timer"></a>Minuteur indépendant de la plateforme

Il est possible d’utiliser [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) pour créer un minuteur indépendant de la plateforme. L’exemple [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) illustre cela.

## <a name="file-inputoutput"></a>Entrée/sortie de fichier

Traditionnellement, l' [`System.IO`](xref:System.IO) espace de noms .net est la source de la prise en charge des e/s de fichier. Bien que certaines méthodes de cet espace de noms prennent en charge les opérations asynchrones, la plupart ne le font pas. L’espace de noms prend également en charge plusieurs appels de méthode simples qui exécutent des fonctions d’e/s de fichier sophistiquées.

### <a name="good-news-and-bad-news"></a>Bonnes nouvelles et mauvaises nouvelles

Toutes les plateformes prises en charge par Xamarin.Forms prennent en charge le stockage de stockage local &mdash; d’application qui est privé pour l’application.

Les bibliothèques Xamarin. iOS et Xamarin. Android incluent une version de .NET que Xamarin a expressément adaptée à ces deux plateformes. Celles-ci incluent des classes à partir desquelles `System.IO` vous pouvez utiliser pour effectuer des e/s de fichier avec le stockage local d’application dans ces deux plateformes.

Toutefois, si vous recherchez ces `System.IO` classes dans une bibliothèque de classes portable Xamarin.Forms , vous ne les trouverez pas. Le problème est que Microsoft a entièrement réorganisé les e/s de fichier pour l’API Windows Runtime. Les programmes qui ciblent Windows 8.1, Windows Phone 8,1 et le plateforme Windows universelle ne sont pas utilisés `System.IO` pour les e/s de fichier.

Cela signifie que vous devez utiliser le [`DependencyService`](xref:Xamarin.Forms.DependencyService) (abordé dans le [**chapitre 9). Appels d’API spécifiques**](chapter09.md) à la plateforme pour implémenter les e/s de fichier.

> [!NOTE]
> La classe portable libaries a été remplacée par .NET Standard bibliothèques 2,0 et .NET Standard 2,0 prend en charge [`System.IO`](xref:System.IO) les types pour toutes les Xamarin.Forms plateformes. Il n’est plus nécessaire d’utiliser un `DependencyService` pour la plupart des tâches d’e/s de fichier. Consultez [gestion des fichiers Xamarin.Forms dans](~/xamarin-forms/data-cloud/data/files.md) pour une approche plus moderne des e/s de fichier.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Première prise de sortie sur les e/s de fichier multiplateforme

L’exemple [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) définit une [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interface pour les e/s de fichier et les implémentations de cette interface dans toutes les plateformes. Toutefois, les implémentations de Windows Runtime ne fonctionnent pas avec les méthodes dans cette interface, car les méthodes d’e/s de Windows Runtime de fichier sont asynchrones.

### <a name="accommodating-windows-runtime-file-io"></a>Adaptation des e/s de fichier Windows Runtime

Les programmes s’exécutant sous le Windows Runtime utilisent des classes dans les [`Windows.Storage`](/uwp/api/Windows.Storage) [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) espaces de noms et pour les e/s de fichier, y compris le stockage local des applications. Étant donné que Microsoft a déterminé que toute opération nécessitant plus de 50 millisecondes doit être asynchrone pour éviter de bloquer le thread d’interface utilisateur, ces méthodes d’e/s de fichier sont principalement asynchrones.

Le code illustrant cette nouvelle approche se trouve dans une bibliothèque, afin de pouvoir être utilisé par d’autres applications.

## <a name="platform-specific-libraries"></a>Bibliothèques propres à une plateforme

Il est avantageux de stocker du code réutilisable dans les bibliothèques. Cela est évidemment plus difficile lorsque différentes parties du code réutilisable sont destinées à des systèmes d’exploitation entièrement différents.

La solution [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) illustre une approche. Cette solution contient sept projets différents :

- [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), un Xamarin.Forms PCL normal
- [**Xamarin. FormsBook. Platform. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), bibliothèque de classes iOS
- [**Xamarin. FormsBook. Platform. Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), bibliothèque de classes Android
- [**Xamarin. FormsBook. Platform. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), bibliothèque de classes Windows universelle
- [**Xamarin. FormsBook. Platform. WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), projet partagé pour le code qui est commun à toutes les plateformes Windows

Tous les projets de plateforme individuels (à l’exception de **Xamarin. FormsBook. Platform. WinRT**) ont des références à **Xamarin. FormsBook. Platform**. Les trois projets Windows ont une référence à **Xamarin. FormsBook. Platform. WinRT**.

Tous les projets contiennent une `Toolkit.Init` méthode statique pour garantir que la bibliothèque est chargée si elle n’est pas directement référencée par un projet dans une Xamarin.Forms solution d’application.

Le projet **Xamarin. FormsBook. Platform** contient la nouvelle [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interface. Toutes les méthodes ont maintenant des noms avec des `Async` suffixes et des objets de retour `Task` .

Le projet **Xamarin. FormsBook. Platform. WinRT** contient la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe de l’Windows Runtime.

Le projet **Xamarin. FormsBook. Platform. iOS** contient la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe pour iOS. Ces méthodes doivent maintenant être asynchrones. Certaines méthodes utilisent les versions asynchrones des méthodes définies dans `StreamWriter` et `StreamReader` : [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) et [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync) . D’autres convertissent un résultat en `Task` objet à l’aide de la [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) méthode.

Le projet **Xamarin. FormsBook. Platform. Android** contient une [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe similaire pour Android.

Le projet **Xamarin. FormsBook. Platform** contient également une [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) classe qui facilite l’utilisation de l' `DependencyService` objet.

Pour utiliser ces bibliothèques, une solution d’application doit inclure tous les projets de la solution **Xamarin. FormsBook. Platform** , et chacun des projets d’application doit avoir une référence à la bibliothèque correspondante dans **Xamarin. FormsBook. Platform**.

La solution [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) montre comment utiliser les bibliothèques **Xamarin. FormsBook. Platform** . Chacun des projets a un appel à `Toolkit.Init` . L’application utilise les fonctions d’e/s de fichier asynchrones.

### <a name="keeping-it-in-the-background"></a>Conserver en arrière-plan

Les méthodes des bibliothèques qui effectuent des appels à plusieurs méthodes asynchrones &mdash; , telles que les `WriteFileAsync` `ReadFileASync` méthodes et de la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe Windows Runtime &mdash; peuvent être rendues plus efficaces en utilisant la [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) méthode pour éviter de revenir au thread d’interface utilisateur.

### <a name="dont-block-the-ui-thread"></a>Ne bloquez pas le thread d’interface utilisateur !

Parfois, il est tentant d’éviter l’utilisation de ou de à l' `ContinueWith` `await` aide de la [`Result`](xref:System.Threading.Tasks.Task`1.Result) propriété sur les méthodes. Cela doit être évité pour qu’il puisse bloquer le thread d’interface utilisateur ou même bloquer l’application.

## <a name="your-own-awaitable-methods"></a>Vos propres méthodes await

Vous pouvez exécuter du code de façon asynchrone en le passant à l’une des [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) méthodes. Vous pouvez appeler `Task.Run` dans une méthode Async qui gère une partie de la surcharge.

Les différents `Task.Run` modèles sont présentés ci-dessous.

### <a name="the-basic-mandelbrot-set"></a>Ensemble de Mandelbrot de base

Pour dessiner le groupe Mandelbrot en temps réel, le [** Xamarin.Forms . **](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)La bibliothèque de la boîte à outils a une [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) structure semblable à celle de l' `System.Numerics` espace de noms.

L’exemple [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) contient une `CalculateMandeblotAsync` méthode dans son fichier code-behind qui calcule le jeu de Mandelbrot noir et blanc de base et utilise [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) pour le placer sur une image bitmap.

### <a name="marking-progress"></a>Marquage de la progression

Pour signaler la progression d’une méthode asynchrone, vous pouvez instancier une [`Progress<T>`](xref:System.Progress`1) classe et définir votre méthode asynchrone pour avoir un argument de type [`IProgress<T>`](xref:System.IProgress`1) . Cela est illustré dans l’exemple [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) .

### <a name="cancelling-the-job"></a>Annulation du travail

Vous pouvez également écrire une méthode asynchrone à annuler. Vous commencez par une classe nommée [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource) . La [`Token`](xref:System.Threading.CancellationTokenSource.Token) propriété est une valeur de type [`CancellationToken`](xref:System.Threading.CancellationToken) . Cette valeur est transmise à la fonction asynchrone. Un programme appelle la [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) méthode de `CancellationTokenSource` (généralement en réponse à une action de l’utilisateur) pour annuler la fonction asynchrone.

La méthode asynchrone peut vérifier périodiquement la [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) propriété de `CancellationToken` et quitter si la propriété est `true` , ou simplement appeler la [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) méthode, auquel cas la méthode se termine par un [`OperationCancelledException`](xref:System.OperationCanceledException) .

L’exemple [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) illustre l’utilisation d’une fonction annulable.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

L’exemple [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) a une interface utilisateur plus étendue et est principalement basé sur des [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) classes et :

[![Capture triple de Mandelbrot X F](images/ch20fg13-small.png "Mandelbrot MVVM")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Retour sur le Web

La [`WebRequest`](xref:System.Net.WebRequest) classe utilisée dans certains exemples utilise un ancien protocole asynchrone appelé modèle de programmation asynchrone ou APM. Vous pouvez convertir une telle classe en protocole TAP moderne à l’aide de l’une des `FromAsync` méthodes de la [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) classe. L’exemple [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) illustre cela.

## <a name="related-links"></a>Liens connexes

- [Chapitre 20 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Chapitre 20 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Utilisation de fichiers](~/xamarin-forms/data-cloud/data/files.md)
