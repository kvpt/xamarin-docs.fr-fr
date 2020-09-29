---
title: 'Partie 5 : Stratégies de partage de code pratiques'
description: Ce document traite des stratégies de partage de code pratiques pour les scénarios tels que les bases de données, l’accès aux fichiers, les opérations réseau et le code asynchrone.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: d10917471c37d91fa02db2585895f5694a5a4a60
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457443"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Partie 5 : Stratégies de partage de code pratiques

Cette section fournit des exemples montrant comment partager du code pour des scénarios d’application courants.

## <a name="data-layer"></a>Couche de données

La couche de données se compose d’un moteur de stockage et de méthodes pour lire et écrire des informations. Pour les performances, la flexibilité et la compatibilité entre les plateformes, le moteur de base de données SQLite est recommandé pour les applications multiplateformes Xamarin.
Il s’exécute sur un large éventail de plateformes, notamment Windows, Android, iOS et Mac.

### <a name="sqlite"></a>SQLite

SQLite est une implémentation de base de données Open source. La source et la documentation se trouvent sur [SQLite.org](https://www.sqlite.org/). La prise en charge de SQLite est disponible sur chaque plateforme mobile :

- **iOS** : intégré au système d’exploitation.
- **Android** : intégré au système d’exploitation depuis Android 2,2 (niveau d’API 10).
- **Windows** : consultez l' [extension SQLite for plateforme Windows universelle](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Même si le moteur de base de données est disponible sur toutes les plateformes, les méthodes natives permettant d’accéder à la base de données sont différentes. IOS et Android offrent des API intégrées pour accéder à SQLite qui pourraient être utilisées à partir de Xamarin. iOS ou Xamarin. Android. Toutefois, l’utilisation des méthodes du kit de développement logiciel (SDK) natif n’offre aucune possibilité de partager du code (autre que les requêtes SQL elles-mêmes, en supposant qu’elles sont stockées sous forme de chaînes). Pour plus d’informations sur la fonctionnalité de base de données native, recherchez `CoreData` dans la classe iOS ou Android. `SQLiteOpenHelper` comme ces options ne sont pas multiplateformes, elles n’entrent pas dans le cadre de ce document.

### <a name="adonet"></a>ADO.NET

Xamarin. iOS et Xamarin. Android prennent en charge `System.Data` et `Mono.Data.Sqlite` (pour plus d’informations, consultez la [documentation](~/ios/data-cloud/system.data.md) de Xamarin. IOS).
L’utilisation de ces espaces de noms vous permet d’écrire du code ADO.NET qui fonctionne sur les deux plateformes. Modifiez les références du projet pour inclure `System.Data.dll` et `Mono.Data.Sqlite.dll` , puis ajoutez ces instructions using à votre code :

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

L’exemple de code suivant fonctionnera :

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

Les implémentations réelles de ADO.NET seraient évidemment réparties entre différentes méthodes et classes (cet exemple est fourni à des fins de démonstration uniquement).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET – ORM multiplateforme

Un modèle ORM (ou mappage relationnel objet) tente de simplifier le stockage des données modélisées dans des classes. Plutôt que d’écrire manuellement des requêtes SQL qui créent des TABLEs ou SÉLECTIONNEnt, INSÈREnt et SUPPRIMEnt des données qui sont extraites manuellement à partir de champs de classe et de propriétés, un ORM ajoute une couche de code qui le fait pour vous. À l’aide de la réflexion pour examiner la structure de vos classes, un ORM peut créer automatiquement des tables et des colonnes qui correspondent à une classe et générer des requêtes pour lire et écrire les données. Cela permet au code de l’application d’envoyer et de récupérer simplement des instances d’objets dans l’ORM, ce qui se charge de toutes les opérations SQL en coulisses.

SQLite-NET agit comme un ORM simple qui vous permettra d’enregistrer et de récupérer vos classes dans SQLite. Elle masque la complexité de l’accès SQLite multiplateforme avec une combinaison de directives de compilateur et d’autres astuces.

Fonctionnalités de SQLite-NET :

- Les tables sont définies en ajoutant des attributs aux classes de modèle.
- Une instance de base de données est représentée par une sous-classe de  `SQLiteConnection` , la classe principale de la bibliothèque SQLite-net.
- Les données peuvent être insérées, interrogées et supprimées à l’aide d’objets. Aucune instruction SQL n’est requise (même si vous pouvez écrire des instructions SQL si nécessaire).
- Les requêtes LINQ de base peuvent être exécutées sur les collections retournées par SQLite-NET.

Le code source et la documentation de SQLite-NET sont disponibles sur [SQLite-net sur GitHub](https://github.com/praeclarum/sqlite-net) et ont été implémentés dans les deux études de cas. Vous trouverez ci-dessous un exemple simple de code SQLite-NET (à partir de l’étude de cas *Pro Tasky* ).

Tout d’abord, la `TodoItem` classe utilise des attributs pour définir un champ comme clé primaire de base de données :

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

Cela permet de `TodoItem` créer une table avec la ligne de code suivante (et aucune instruction SQL) sur une `SQLiteConnection` instance :

```csharp
CreateTable<TodoItem> ();
```

Les données de la table peuvent également être manipulées avec d’autres méthodes sur le `SQLiteConnection` (à nouveau, sans nécessiter d’instructions SQL) :

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Pour obtenir des exemples complets, consultez le code source de l’étude de cas.

## <a name="file-access"></a>Accès aux fichiers

L’accès aux fichiers est une partie essentielle de toute application. Voici des exemples courants de fichiers qui peuvent faire partie d’une application :

- Fichiers de base de données SQLite.
- Données générées par l’utilisateur (texte, images, son, vidéo).
- Données téléchargées pour la mise en cache (fichiers images, html ou PDF).

### <a name="systemio-direct-access"></a>Accès direct System.IO

Xamarin. iOS et Xamarin. Android autorisent l’accès au système de fichiers à l’aide des classes de l' `System.IO` espace de noms.

Chaque plateforme a des restrictions d’accès différentes qui doivent être prises en considération :

- les applications iOS s’exécutent dans un bac à sable (sandbox) avec un accès très restreint au système de fichiers. Apple détermine plus en détail comment utiliser le système de fichiers en spécifiant certains emplacements qui sont sauvegardés (et d’autres non). Pour plus d’informations, consultez le guide  [utilisation du système de fichiers dans Xamarin. iOS](~/ios/app-fundamentals/file-system.md) .
- Android restreint également l’accès à certains répertoires liés à l’application, mais prend également en charge les supports externes (par exemple, Cartes SD) et l’accès aux données partagées.
- Windows Phone 8 (Silverlight) n’autorisent pas l’accès direct aux fichiers : les fichiers ne peuvent être manipulés qu’à l’aide de `IsolatedStorage` .
- Les projets UWP Windows 8.1 WinRT et Windows 10 offrent uniquement des opérations asynchrones sur les fichiers via des `Windows.Storage` API, qui sont différentes des autres plateformes.

#### <a name="example-for-ios-and-android"></a>Exemple pour iOS et Android

Vous trouverez ci-dessous un exemple simple d’écriture et de lecture d’un fichier texte.
L’utilisation de `Environment.GetFolderPath` permet au même code de s’exécuter sur iOS et Android, qui retournent chacun un répertoire valide selon leurs conventions de système de fichiers.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

Pour plus d’informations sur la fonctionnalité de système de fichiers spécifique à iOS, reportez-vous à Xamarin. iOS [utilisation du document du système de fichiers](~/ios/app-fundamentals/file-system.md) . Lorsque vous écrivez du code d’accès aux fichiers multiplateforme, n’oubliez pas que certains systèmes de fichiers respectent la casse et ont des séparateurs de répertoire différents. Il est recommandé de toujours utiliser la même casse pour les noms de fichiers et la `Path.Combine()` méthode lors de la construction de chemins d’accès de fichier ou de répertoire.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows. Storage pour Windows 8 et Windows 10

Le *Mobile Apps création avec le livre Xamarin. Forms* [book](https://developer.xamarin.com/r/xamarin-forms/book/) 
 [chapitre 20. Async et e/s de fichier](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) incluent [des exemples pour Windows 8.1 et Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

[`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md)Il est possible de lire les fichiers et de fichiers sur ces plateformes à l’aide des API prises en charge :

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Pour plus d’informations, consultez le [chapitre du livre](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) .

<a name="Isolated_Storage"></a>

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Stockage isolé sur Windows Phone 7 & 8 (Silverlight)

Le stockage isolé est une API courante pour l’enregistrement et le chargement de fichiers sur toutes les plates-formes iOS, Android et anciennes Windows Phone.

Il s’agit du mécanisme par défaut pour l’accès aux fichiers dans Windows Phone (Silverlight) qui a été implémenté dans Xamarin. iOS et Xamarin. Android pour permettre l’écriture d’un code d’accès aux fichiers commun. La `System.IO.IsolatedStorage` classe peut être référencée sur les trois plateformes d’un [projet partagé](~/cross-platform/app-fundamentals/shared-projects.md).

Pour plus d’informations, consultez [vue d’ensemble du stockage isolé pour Windows Phone](/previous-versions/windows/apps/ff402541(v=vs.105)) .

Les API de stockage isolé ne sont pas disponibles dans les [bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md). Une alternative pour PCL est le [NuGet PCLStorage](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Accès multiplateforme aux fichiers dans classes portables

Il existe également un NuGet- [PCLStorage](https://www.nuget.org/packages/PCLStorage/) compatible avec PCL, qui est un accès multiplateforme aux fichiers pour les plateformes prises en charge par Xamarin et les API Windows les plus récentes.

## <a name="network-operations"></a>Opérations réseau

La plupart des applications mobiles comporteront un composant réseau, par exemple :

- Téléchargement d’images, de vidéo et audio (par exemple, miniatures, photos, musique).
- Téléchargement de documents (par exemple, HTML, PDF).
- Téléchargement de données utilisateur (par exemple, photos ou texte).
- Accès aux services Web ou aux API tierces (y compris SOAP, XML ou JSON).

Le .NET Framework fournit des classes différentes pour accéder aux ressources réseau : `HttpClient` , `WebClient` et `HttpWebRequest` .

### <a name="httpclient"></a>HttpClient

La `HttpClient` classe de l' `System.Net.Http` espace de noms est disponible dans Xamarin. iOS, Xamarin. Android et la plupart des plateformes Windows. Il existe une [bibliothèque cliente Microsoft http NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) qui peut être utilisée pour placer cette API dans les bibliothèques de classes portables (et Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La `WebClient` classe fournit une API simple pour récupérer des données distantes à partir de serveurs distants.

Plateforme Windows universelle opérations *doivent* être asynchrones, même si Xamarin. iOS et Xamarin. Android prennent en charge les opérations synchrones (qui peuvent être effectuées sur les threads d’arrière-plan).

Le code d’une opération asynchrone simple `WebClient` est le suivant :

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` possède également `DownloadFileCompleted` et `DownloadFileAsync` pour la récupération de données binaires.

<a name="HttpWebRequest"></a>

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` offre une personnalisation plus grande que `WebClient` et, par conséquent, nécessite davantage de code.

Le code d’une opération synchrone simple `HttpWebRequest` est le suivant :

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

Il existe un exemple dans notre [documentation sur les services Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability"></a>

### <a name="reachability"></a>Reachability

Les périphériques mobiles fonctionnent dans une série de conditions réseau allant des connexions Wi-Fi ou 4G rapides aux zones de réception médiocres et aux liaisons de données faibles. Pour cette raison, il est conseillé de détecter si le réseau est disponible et, le cas échéant, le type de réseau disponible, avant de tenter de se connecter aux serveurs distants.

Les actions qu’une application mobile peut entreprendre dans ces situations sont les suivantes :

- Si le réseau n’est pas disponible, avertissez l’utilisateur. S’ils l’ont désactivés manuellement (par exemple, En mode avion ou en désactivant le Wi-Fi), il peut résoudre le problème.
- Si la connexion est 3G, les applications peuvent se comporter différemment (par exemple, Apple n’autorise pas le téléchargement d’applications de plus de 20 Mo sur 3G). Les applications peuvent utiliser ces informations pour avertir l’utilisateur des temps de téléchargement excessifs lors de la récupération de fichiers volumineux.
- Même si le réseau est disponible, il est recommandé de vérifier la connectivité avec le serveur cible avant de lancer d’autres demandes. Cela empêchera les opérations réseau de l’application d’être chronométrées à plusieurs reprises et d’afficher également un message d’erreur plus instructif à l’utilisateur.

## <a name="webservices"></a>WebServices

Consultez notre documentation sur l' [utilisation des services Web](~/cross-platform/data-cloud/web-services/index.md), qui traite de l’accès aux points de terminaison REST, SOAP et WCF à l’aide de Xamarin. iOS. Il est possible de créer des demandes de service Web et d’analyser les réponses. Toutefois, il existe des bibliothèques qui peuvent être plus simples, notamment Azure, RestSharp et ServiceStack. Même les opérations WCF de base sont accessibles dans les applications Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure est une plateforme Cloud qui fournit un large éventail de services pour les applications mobiles, notamment le stockage et la synchronisation des données, et les notifications push.

Visitez [Azure.Microsoft.com](https://azure.microsoft.com/) pour l’essayer gratuitement.

### <a name="restsharp"></a>RestSharp

RestSharp est une bibliothèque .NET qui peut être incluse dans les applications mobiles pour fournir un client REST qui simplifie l’accès aux services Web. Il vous aide à fournir une API simple pour demander des données et analyser la réponse REST. RestSharp peut être utile

Le [site Web RestSharp](http://restsharp.org/) contient de la [documentation](https://github.com/restsharp/RestSharp/wiki) sur la façon d’implémenter un client Rest à l’aide de RestSharp.
RestSharp fournit des exemples Xamarin. iOS et Xamarin. Android sur [GitHub](https://github.com/restsharp/RestSharp/).

La [documentation des services Web](~/cross-platform/data-cloud/web-services/index.md)contient également un extrait de code Xamarin. iOS.

 <a name="ServiceStack"></a>

### <a name="servicestack"></a>ServiceStack

Contrairement à RestSharp, ServiceStack est une solution côté serveur pour héberger un service Web, ainsi qu’une bibliothèque cliente qui peut être implémentée dans les applications mobiles pour accéder à ces services.

Le [site Web ServiceStack](http://servicestack.net/) explique l’objectif du projet et des liens vers des exemples de document et de code. Les exemples incluent une implémentation complète côté serveur d’un service Web ainsi que diverses applications côté client qui peuvent y accéder.

### <a name="wcf"></a>WCF

Les outils Xamarin peuvent vous aider à utiliser certains services Windows Communication Foundation (WCF). En général, Xamarin prend en charge le même sous-ensemble de WCF côté client fourni avec le runtime Silverlight. Cela comprend l’encodage et les implémentations de protocole les plus courants de WCF : les messages SOAP encodés au format texte via le protocole de transport HTTP à l’aide de `BasicHttpBinding` .

En raison de la taille et de la complexité de l’infrastructure WCF, il peut y avoir des implémentations de service actuelles et futures qui se situent en dehors de l’étendue prise en charge par le domaine du sous-ensemble client de Xamarin. En outre, la prise en charge de WCF requiert l’utilisation d’outils uniquement disponibles dans un environnement Windows pour générer le proxy.

 <a name="Threading"></a>

## <a name="threading"></a>Thread

La réactivité des applications est importante pour les applications mobiles : les utilisateurs attendent le chargement et l’exécution des applications rapidement. Un écran « figé » qui cesse d’accepter l’entrée utilisateur s’affiche pour indiquer que l’application s’est arrêtée. il est donc important de ne pas bloquer le thread d’interface utilisateur avec des appels de blocage de longue durée, tels que des requêtes réseau ou des opérations locales lentes (telles que la décompression d’un fichier). En particulier, le processus de démarrage ne doit pas contenir de tâches de longue durée : toutes les plateformes mobiles interrompent une application dont le chargement est trop long.

Cela signifie que votre interface utilisateur doit implémenter un « indicateur de progression » ou une interface utilisateur « utilisable » qui est rapide à afficher, et des tâches asynchrones pour effectuer des opérations d’arrière-plan. L’exécution de tâches en arrière-plan requiert l’utilisation de threads, ce qui signifie que les tâches en arrière-plan ont besoin d’un moyen de communiquer avec le thread principal pour indiquer la progression ou lorsqu’elles sont terminées.

 <a name="Parallel_Task_Library"></a>

### <a name="parallel-task-library"></a>Bibliothèque de tâches parallèles

Les tâches créées à l’aide de la bibliothèque de tâches parallèles peuvent s’exécuter de façon asynchrone et retourner sur leur thread appelant, ce qui les rend très utiles pour déclencher des opérations de longue durée sans bloquer l’interface utilisateur.

Une opération Parallel Task simple peut se présenter comme suit :

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La clé `TaskScheduler.FromCurrentSynchronizationContext()` permet de réutiliser le SynchronizationContext. Current du thread appelant la méthode (ici, le thread principal en cours d’exécution `MainThreadMethod` ) pour marshaler les appels à ce thread. Cela signifie que si la méthode est appelée sur le thread d’interface utilisateur, elle retournera l' `ContinueWith` opération sur le thread d’interface utilisateur.

Si le code démarre des tâches à partir d’autres threads, utilisez le modèle suivant pour créer une référence au thread d’interface utilisateur et la tâche peut toujours le rappeler :

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread"></a>

### <a name="invoking-on-the-ui-thread"></a>Appel sur le thread d’interface utilisateur

Pour le code qui n’utilise pas la bibliothèque de tâches parallèles, chaque plateforme possède sa propre syntaxe pour le marshaling des opérations vers le thread d’interface utilisateur :

- **iOS** : `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

La syntaxe iOS et Android nécessite la mise à disposition d’une classe « Context », ce qui signifie que le code doit passer cet objet dans toutes les méthodes qui requièrent un rappel sur le thread d’interface utilisateur.

Pour effectuer des appels de thread d’interface utilisateur dans du code partagé, suivez l' [exemple IDispatchOnUIThread](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (courtois of [@follesoe](https://twitter.com/follesoe) ). Déclarez et programmez sur une `IDispatchOnUIThread` interface dans le code partagé, puis implémentez les classes spécifiques à la plateforme comme indiqué ici :

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Les développeurs Xamarin. Forms doivent utiliser le [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) code commun (projets partagés ou PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation"></a>

## <a name="platform-and-device-capabilities-and-degradation"></a>Capacités et dégradation des plateformes et des appareils

Des exemples spécifiques de gestion des différentes fonctionnalités sont fournis dans la documentation sur les fonctionnalités de la plateforme. Il traite de la détection de différentes fonctionnalités et de la dégradation appropriée d’une application pour offrir une bonne expérience utilisateur, même lorsque l’application ne peut pas fonctionner entièrement.