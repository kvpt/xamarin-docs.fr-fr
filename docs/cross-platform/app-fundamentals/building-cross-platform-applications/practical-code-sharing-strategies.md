---
title: 'Partie 5 : Stratégies de partage de code pratiques'
description: Ce document traite des stratégies pour des scénarios tels que les bases de données, accès aux fichiers, les opérations réseau et le code asynchrone de partage de code de pratique.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0e37e138607fb0e00fbdc463ac7c53facf81395d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291989"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Partie 5 : Stratégies de partage de code pratiques

Cette section donne des exemples montrant comment partager du code pour les scénarios d’application courants.

## <a name="data-layer"></a>Couche de données

La couche de données se compose d’un moteur de stockage et des méthodes pour lire et écrire des informations. Pour assurer la compatibilité inter-plateformes, de flexibilité et de performances le SQLite moteur de base de données est recommandé pour les applications Xamarin multiplateformes.
Il s’exécute sur un large éventail de plateformes, notamment Windows, Android, iOS et Mac.

### <a name="sqlite"></a>SQLite

SQLite est une implémentation de base de données open source. La source et la documentation se trouvent sur [SQLite.org](https://www.sqlite.org/). La prise en charge de SQLite est disponible sur chaque plateforme mobile :

- **iOS** : intégré au système d’exploitation.
- **Android** : intégré au système d’exploitation depuis Android 2,2 (niveau d’API 10).
- **Windows** : consultez l' [extension SQLite for plateforme Windows universelle](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Même avec le moteur de base de données disponible sur toutes les plateformes, les méthodes natives pour accéder à la base de données sont différentes. À la fois iOS et Android offrent des API intégrées pour accéder à SQLite qui pourrait être utilisée à partir de Xamarin.iOS ou Xamarin.Android, toutefois à l’aide des méthodes natives du Kit de développement logiciel n’offre aucune possibilité de partager du code (autre que peut-être les requêtes SQL eux-mêmes, en supposant qu’ils sont stockés sous forme de chaînes) . Pour plus d’informations sur la fonctionnalité de base de données native, recherchez `CoreData` dans la classe `SQLiteOpenHelper` iOS ou Android. comme ces options ne sont pas multiplateformes, elles n’entrent pas dans le cadre de ce document.

### <a name="adonet"></a>ADO.NET

Xamarin. iOS et Xamarin. Android prennent en charge `System.Data` et `Mono.Data.Sqlite` (pour plus d’informations, consultez la [documentation](~/ios/data-cloud/system.data.md) de Xamarin. IOS).
À l’aide de ces espaces de noms vous permet d’écrire du code ADO.NET qui fonctionne sur les deux plateformes. Modifiez les références du projet pour inclure `System.Data.dll` et `Mono.Data.Sqlite.dll` et ajoutez les instructions using suivantes à votre code :

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

L’exemple de code suivant fonctionne :

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

Les implémentations réelles d’ADO.NET seraient évidemment être réparties entre différentes méthodes et classes (cet exemple est uniquement à des fins de démonstration).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-inter-plateformes ORM

Un ORM (ou un mappeur objet-relationnel) tente de simplifier le stockage de données modélisées dans les classes. Plutôt que que créer des TABLEs ou les sélectionner manuellement écriture de requêtes SQL, champs de données INSERT et DELETE qui sont extrait manuellement à partir de la classe et propriétés, un ORM ajoute une couche de code qui fait pour vous. À l’aide de la réflexion pour examiner la structure de vos classes, un ORM peut automatiquement créer les tables et colonnes qui correspondent à une classe et de génèrent des requêtes pour lire et écrire les données. Ainsi, le code d’application pour simplement envoyer et récupérer des instances d’objet à l’ORM, qui prend en charge de toutes les opérations SQL sous le capot.

SQLite-NET agit comme un simple ORM qui vous permet d’enregistrer et de récupérer vos classes dans SQLite. Elle masque la complexité de cross platform SQLite accès avec une combinaison de directives de compilateur et autres astuces.

Fonctionnalités de SQLite-NET :

- Tables sont définies en ajoutant des attributs aux classes de modèle.
- Une instance de base de données est représentée par une sous-classe de `SQLiteConnection`, la classe principale de la bibliothèque SQLite-net.
- Données peuvent être insérées, interrogés et supprimées à l’aide d’objets. Aucune instruction SQL n’est nécessaires (bien que vous pouvez écrire des instructions SQL si nécessaire).
- Des requêtes Linq de base peuvent être effectuées sur les collections retournées par SQLite-NET.

Le code source et la documentation de SQLite-NET sont disponibles sur [SQLite-net sur GitHub](https://github.com/praeclarum/sqlite-net) et ont été implémentés dans les deux études de cas. Vous trouverez ci-dessous un exemple simple de code SQLite-NET (à partir de l’étude de cas *Pro Tasky* ).

Tout d’abord, la classe `TodoItem` utilise des attributs pour définir un champ comme clé primaire de base de données :

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

Cela permet de créer une table `TodoItem` avec la ligne de code suivante (et aucune instruction SQL) sur une instance `SQLiteConnection` :

```csharp
CreateTable<TodoItem> ();
```

Les données de la table peuvent également être manipulées avec d’autres méthodes sur le `SQLiteConnection` (encore une fois, sans instructions SQL) :

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Consultez le code source étude de cas pour obtenir des exemples complets.

## <a name="file-access"></a>Accès aux fichiers

Accès aux fichiers est certaine soit une partie essentielle de n’importe quelle application. Exemples courants de fichiers peuvent faire partie d’une inclusion de l’application :

- Fichiers de base de données SQLite.
- Données générées par l’utilisateur (texte, images, audio, vidéo).
- Données téléchargées pour la mise en cache (images, html ou PDF).

### <a name="systemio-direct-access"></a>Accès Direct de System.IO

Xamarin. iOS et Xamarin. Android autorisent l’accès au système de fichiers à l’aide des classes de l’espace de noms `System.IO`.

Chaque plateforme a des restrictions d’accès différents qui doivent être prises en considération :

- applications d’iOS s’exécutent dans un bac à sable avec un accès de système de fichiers très limité. Apple davantage détermine la façon dont vous devez utiliser le système de fichiers en spécifiant certains emplacements qui sont sauvegardés (et autres personnes qui ne sont pas). Pour plus d’informations, consultez le guide [utilisation du système de fichiers dans Xamarin. iOS](~/ios/app-fundamentals/file-system.md) .
- Android restreint également l’accès à certains répertoires associés à l’application, mais il prend également en charge les supports externes (par exemple). Une carte SD) et l’accès aux données partagées.
- Windows Phone 8 (Silverlight) n’autorisent pas l’accès direct aux fichiers : les fichiers ne peuvent être manipulés qu’à l’aide de `IsolatedStorage`.
- Les projets UWP Windows 8.1 WinRT et Windows 10 offrent uniquement des opérations asynchrones sur les fichiers via des API `Windows.Storage`, qui sont différentes des autres plateformes.

#### <a name="example-for-ios-and-android"></a>Exemple pour iOS et Android

Vous trouverez ci-dessous un exemple simple qui écrit et lit un fichier texte.
L’utilisation de `Environment.GetFolderPath` permet au même code de s’exécuter sur iOS et Android, qui retournent chacun un répertoire valide selon leurs conventions de système de fichiers.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

Pour plus d’informations sur la fonctionnalité de système de fichiers spécifique à iOS, reportez-vous à Xamarin. iOS [utilisation du document du système de fichiers](~/ios/app-fundamentals/file-system.md) . Lorsque vous écrivez le code d’accès aux fichiers entre plates-formes, n’oubliez pas que certains systèmes de fichiers respectent la casse et ont des séparateurs de répertoire différent. Il est recommandé de toujours utiliser la même casse pour les noms de fichiers et la méthode `Path.Combine()` lors de la construction de chemins d’accès de fichier ou de répertoire.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows.Storage pour Windows 8 et Windows 10

L' *Mobile Apps Creating with Xamarin. forms* [
](https://developer.xamarin.com/r/xamarin-forms/book/) [chapitre 20. Async et e/s de fichier](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) incluent [des exemples pour Windows 8.1 et Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

À l’aide d’un [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) il est possible de lire et de fichier fichiers sur ces plateformes à l’aide des API prises en charge :

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Pour plus d’informations, consultez le [chapitre du livre](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) .

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Stockage isolé sur Windows Phone 7 et 8 (Silverlight)

Le stockage isolé est une API commune pour enregistrer et charger des fichiers sur tous les iOS, Android et les plateformes Windows Phone plus anciens.

Il est le mécanisme par défaut pour l’accès de fichier dans Windows Phone (Silverlight) qui a été implémenté dans Xamarin.iOS et Xamarin.Android pour autoriser le code d’accès au fichier commun à écrire. La classe `System.IO.IsolatedStorage` peut être référencée sur les trois plateformes d’un [projet partagé](~/cross-platform/app-fundamentals/shared-projects.md).

Pour plus d’informations, consultez [vue d’ensemble du stockage isolé pour Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) .

Les API de stockage isolé ne sont pas disponibles dans les [bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md). Une alternative pour PCL est le [NuGet PCLStorage](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Accès aux fichiers d’inter-plateformes dans les bibliothèques de classes portables

Il existe également un NuGet- [PCLStorage](https://www.nuget.org/packages/PCLStorage/) compatible avec PCL, qui est un accès multiplateforme aux fichiers pour les plateformes prises en charge par Xamarin et les API Windows les plus récentes.

## <a name="network-operations"></a>Opérations réseau

Les applications mobiles plus aura un composant réseau, par exemple :

- Téléchargement des images, audio et vidéos (par exemple). miniatures, photos, musique).
- Téléchargement de documents (par exemple). CODE HTML, PDF).
- Chargement des données utilisateur (par exemple, des photos ou texte).
- L’accès aux services web ou 3e partie API (y compris SOAP, XML ou JSON).

Le .NET Framework fournit des classes différentes pour accéder aux ressources réseau : `HttpClient`, `WebClient`et `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

La classe `HttpClient` de l’espace de noms `System.Net.Http` est disponible dans Xamarin. iOS, Xamarin. Android et la plupart des plateformes Windows. Il existe une [bibliothèque cliente Microsoft http NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) qui peut être utilisée pour placer cette API dans les bibliothèques de classes portables (et Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La classe `WebClient` fournit une API simple pour récupérer des données distantes à partir de serveurs distants.

Plateforme Windows universelle opérations *doivent* être asynchrones, même si Xamarin. iOS et Xamarin. Android prennent en charge les opérations synchrones (qui peuvent être effectuées sur les threads d’arrière-plan).

Le code pour une opération simple de `WebClient` asynchrone est :

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

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>httpWebRequest

`HttpWebRequest` offre plus de personnalisation que `WebClient` et, par conséquent, nécessite davantage de code.

Le code d’une opération de `HttpWebRequest` synchrone simple est le suivant :

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

 <a name="Reachability" />

### <a name="reachability"></a>Accessibilité

Les appareils mobiles fonctionnent sous différentes conditions de réseau à partir de Wi-Fi rapide ou connexions 4G sur des zones de réception de mauvaise qualité et des liaisons de données lentes EDGE. Pour cette raison, il est conseillé de détecter si le réseau est disponible et le cas échéant, quel type de réseau est disponible, avant de tenter de se connecter aux serveurs distants.

Actions de qu'une application mobile peut prendre dans ces situations sont les suivantes :

- Si le réseau n’est pas disponible, demandez à l’utilisateur. Si elles ont désactivés manuellement une (par exemple). Le mode avion ou en désactivant Wi-Fi), puis ils peuvent résoudre le problème.
- Si la connexion est 3G, les applications peuvent se comporter différemment (par exemple, Apple n’autorise pas les applications plus de 20 Mo à télécharger plus de 3G). Applications pourrait utiliser ces informations pour avertir l’utilisateur sur le téléchargement excessive fois lors de la récupération des fichiers volumineux.
- Même si le réseau est disponible, il est conseillé de vérifier la connectivité avec le serveur cible avant de lancer d’autres demandes. Cela empêchera les opérations de réseau de l’application à partir de l’expiration du délai à plusieurs reprises et permettent également un message d’erreur plus explicite à afficher à l’utilisateur.

## <a name="webservices"></a>WebServices

Consultez notre documentation sur l' [utilisation des services Web](~/cross-platform/data-cloud/web-services/index.md), qui traite de l’accès aux points de terminaison REST, SOAP et WCF à l’aide de Xamarin. iOS. Il est possible de demandes de service web de création manuelle et analyser les réponses, cependant, il existe des bibliothèques à en faire beaucoup plus simples, notamment Azure, RestSharp et ServiceStack. Les opérations de base même WCF sont accessibles dans les applications Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure est une plateforme de cloud qui fournit un large éventail de services pour les applications mobiles, y compris le stockage de données et de synchronisation et de notifications push.

Visitez [Azure.Microsoft.com](https://azure.microsoft.com/) pour l’essayer gratuitement.

### <a name="restsharp"></a>RestSharp

RestSharp est une bibliothèque .NET qui peut être incluse dans les applications mobiles pour fournir un client REST qui simplifie l’accès aux services web. Il vous aide à en fournissant une API simple permettant de demander des données et d’analyser la réponse REST. RestSharp peut être utile

Le [site Web RestSharp](http://restsharp.org/) contient de la [documentation](https://github.com/restsharp/RestSharp/wiki) sur la façon d’implémenter un client Rest à l’aide de RestSharp.
RestSharp fournit des exemples Xamarin. iOS et Xamarin. Android sur [GitHub](https://github.com/restsharp/RestSharp/).

La [documentation des services Web](~/cross-platform/data-cloud/web-services/index.md)contient également un extrait de code Xamarin. iOS.

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

Contrairement à RestSharp, ServiceStack est une solution de côté serveur pour héberger un service web, ainsi qu’une bibliothèque de client qui peut être implémentée dans les applications mobiles pour accéder à ces services.

Le [site Web ServiceStack](http://servicestack.net/) explique l’objectif du projet et des liens vers des exemples de document et de code. Les exemples incluent une implémentation de côté serveur complète d’un service web, ainsi que diverses applications côté client qui peuvent y accéder.

### <a name="wcf"></a>WCF

Outils Xamarin peuvent vous aider à utiliser certains services Windows Communication Foundation (WCF). En règle générale, Xamarin prend en charge le même sous-ensemble de côté client de WCF est livré avec le runtime de Silverlight. Cela comprend l’encodage et les implémentations de protocole les plus courants de WCF : les messages SOAP encodés au format texte via le protocole de transport HTTP à l’aide de l' `BasicHttpBinding`.

En raison de la taille et la complexité de l’infrastructure WCF, il peut exister des implémentations de service actuels et futurs qui seront se situent en dehors de l’étendue de prise en charge par le domaine du client-sous-ensemble de Xamarin. En outre, la prise en charge WCF nécessite l’utilisation des outils disponibles uniquement dans un environnement Windows pour générer le proxy.

 <a name="Threading" />

## <a name="threading"></a>Thread

Réactivité de l’application est importante pour les applications mobiles : les utilisateurs attendent des applications pour charger et exécuter rapidement. Un écran « figé » cesse d’accepter l’entrée d’utilisateur s’affiche pour indiquer que l’application s’est arrêté anormalement, par conséquent, il est important de ne pas bloquer le thread d’interface utilisateur avec des appels de blocage de longues telles que les demandes du réseau ou lentes opérations locales (par exemple, un fichier de la décompression). En particulier, le processus de démarrage ne doit pas contenir de tâches longues – toutes les plateformes mobiles va arrêter une application qui prend trop de temps à charger.

Cela signifie que votre interface utilisateur doit implémenter un indicateur de progression ou l’interface utilisateur sinon utilisable est rapide à afficher et des tâches asynchrones pour effectuer des opérations d’arrière-plan. L’exécution de tâches en arrière-plan nécessite l’utilisation de threads, ce qui signifie que les besoins de tâches en arrière-plan un moyen pour communiquer avec le thread principal pour indiquer la progression ou quand ceux-ci ont terminé.

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>Bibliothèque parallèle de tâches

Les tâches créées avec la bibliothèque parallèle de tâches peuvent exécuter de façon asynchrone et renvoyer sur leur thread appelant, ce qui les rend très utile pour déclencher des opérations longues sans bloquer l’interface utilisateur.

Une opération de tâche parallèle simple peut se présenter comme suit :

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La clé est `TaskScheduler.FromCurrentSynchronizationContext()` qui réutilise le SynchronizationContext. Current du thread appelant la méthode (ici le thread principal qui exécute `MainThreadMethod`) comme un moyen de marshaler les appels à ce thread. Cela signifie que si la méthode est appelée sur le thread d’interface utilisateur, elle exécutera à nouveau l’opération de `ContinueWith` sur le thread d’interface utilisateur.

Si le code démarre les tâches à partir d’autres threads, utilisez le modèle suivant pour créer une référence au thread d’interface utilisateur et la tâche peut toujours rappeler à celui-ci :

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>Appel sur le Thread d’interface utilisateur

Pour le code qui n’utilise pas la bibliothèque parallèle de tâches, chaque plateforme a sa propre syntaxe pour les opérations de marshaling vers le thread d’interface utilisateur :

- **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin. Forms** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS et Android syntaxe requiert une classe de 'contexte' soit disponible, ce qui signifie que le code doit passer cet objet dans toutes les méthodes qui nécessitent un rappel sur le thread d’interface utilisateur.

Pour effectuer des appels de thread d’interface utilisateur dans du code partagé, suivez l' [exemple IDispatchOnUIThread](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (aimablement de [@follesoe](https://twitter.com/follesoe)). Déclarez et programmez sur une interface `IDispatchOnUIThread` dans le code partagé, puis implémentez les classes spécifiques à la plateforme comme indiqué ici :

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

Les développeurs Xamarin. Forms doivent utiliser [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) dans du code commun (projets partagés ou PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>Plateforme et les fonctionnalités de l’appareil et une dégradation

Plus des exemples spécifiques de traiter les différentes fonctionnalités figurent dans la documentation sur les fonctionnalités de plateforme. Il traite à la détection des différentes fonctionnalités et comment dégrader normalement une application de fournir une expérience utilisateur optimale, même lorsque l’application ne peut pas fonctionner à son plein potentiel.
