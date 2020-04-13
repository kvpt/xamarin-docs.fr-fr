---
title: Bases de données locales Xamarin.Forms
description: Xamarin.Forms prend en charge les applications pilotées par base de données à l’aide du moteur de base de données SQLite, ce qui permet de charger et d’enregistrer des objets dans du code partagé. Cet article explique comment les applications Xamarin.Forms peuvent lire et écrire des données dans une base de données SQLite locale à l’aide de SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
ms.openlocfilehash: 2369afc693940d83971a43877da363e2c66b31b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992402"
---
# <a name="xamarinforms-local-databases"></a>Bases de données locales Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

Le moteur de base de données SQLite permet aux applications Xamarin.Forms de charger et d’enregistrer des objets de données dans du code partagé. L’application de l’échantillon utilise une table de base de données SQLite pour stocker des articles de todo. Cet article décrit comment utiliser SQLite.Net dans le code partagé pour stocker et récupérer des informations dans une base de données locale.

[![Captures d’écran de l’application Todolist sur iOS et Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "Application Todolist sur iOS et Android")

Intégrer SQLite.NET dans les applications mobiles en suivant ces étapes :

1. [Installer le paquet NuGet](#install-the-sqlite-nuget-package).
1. [Configurer les constantes](#configure-app-constants).
1. [Créer une classe d’accès à la base de données](#create-a-database-access-class).
1. [Accéder aux données dans Xamarin.Forms](#access-data-in-xamarinforms).
1. [Configuration avancée](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Installer le paquet SQLite NuGet

Utilisez le gestionnaire de paquets NuGet pour rechercher **sqlite-net-pcl** et ajouter la dernière version au projet de code partagé.

Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :

- **Créé par :** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Ne vous fiez pas au nom du package. Vous devez utiliser le package NuGet **sqlite-net-pcl**, même dans les projets .NET Standard.

## <a name="configure-app-constants"></a>Configurer les constantes d’applications

Le projet d’échantillon comprend un fichier **Constants.cs** qui fournit des données de configuration communes :

```csharp
public static class Constants
{
    public const string DatabaseFilename = "TodoSQLite.db3";

    public const SQLite.SQLiteOpenFlags Flags =
        // open the database in read/write mode
        SQLite.SQLiteOpenFlags.ReadWrite |
        // create the database if it doesn't exist
        SQLite.SQLiteOpenFlags.Create |
        // enable multi-threaded database access
        SQLite.SQLiteOpenFlags.SharedCache;

    public static string DatabasePath
    {
        get
        {
            var basePath = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
            return Path.Combine(basePath, DatabaseFilename);
        }
    }
}
```

Le fichier constants spécifie les valeurs enum par défaut `SQLiteOpenFlag` qui sont utilisées pour initialiser la connexion de base de données. L’enum `SQLiteOpenFlag` soutient ces valeurs :

- `Create`: La connexion créera automatiquement le fichier de base de données s’il n’existe pas.
- `FullMutex`: La connexion est ouverte en mode threading sérialisé.
- `NoMutex`: La connexion est ouverte en mode multi-threading.
- `PrivateCache`: La connexion ne participera pas au cache partagé, même si elle est activée.
- `ReadWrite`: La connexion peut lire et écrire des données.
- `SharedCache`: La connexion participera au cache partagé, si elle est activée.
- `ProtectionComplete`: Le fichier est crypté et inaccessible pendant que l’appareil est verrouillé.
- `ProtectionCompleteUnlessOpen`: Le fichier est crypté jusqu’à son ouverture mais est ensuite accessible même si l’utilisateur verrouille l’appareil.
- `ProtectionCompleteUntilFirstUserAuthentication`: Le fichier est crypté jusqu’à ce que l’utilisateur a démarré et déverrouillé l’appareil.
- `ProtectionNone`: Le fichier de base de données n’est pas crypté.

Vous devrez peut-être spécifier différents drapeaux en fonction de la façon dont votre base de données sera utilisée. Pour plus `SQLiteOpenFlags`d’informations sur , voir [Ouverture d’une nouvelle connexion de base de données](https://www.sqlite.org/c3ref/open.html) sur sqlite.org.

## <a name="create-a-database-access-class"></a>Créer une classe d’accès à la base de données

Une classe d’emballage de base de données résume la couche d’accès aux données à partir du reste de l’application. Cette classe centralise la logique des requêtes et simplifie la gestion de l’initialisation des bases de données, ce qui facilite le refactor ou l’expansion des opérations de données au fur et à mesure que l’application se développe. L’application Todo `TodoItemDatabase` définit une classe à cet effet.

### <a name="lazy-initialization"></a>Initialisation tardive

Le `TodoItemDatabase` utilise la `Lazy` classe .NET pour retarder l’initialisation de la base de données jusqu’à ce qu’elle soit accessible pour la première fois. L’utilisation d’une initialisation paresseuse empêche le processus de chargement de la base de données de retarder le lancement de l’application. Pour plus d’informations, voir [Lazy&lt;T&gt; Class](xref:System.Lazy`1).

```csharp
public class TodoItemDatabase
{
    static readonly Lazy<SQLiteAsyncConnection> lazyInitializer = new Lazy<SQLiteAsyncConnection>(() =>
    {
        return new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    });

    static SQLiteAsyncConnection Database => lazyInitializer.Value;
    static bool initialized = false;

    public TodoItemDatabase()
    {
        InitializeAsync().SafeFireAndForget(false);
    }

    async Task InitializeAsync()
    {
        if (!initialized)
        {
            if (!Database.TableMappings.Any(m => m.MappedType.Name == typeof(TodoItem).Name))
            {
                await Database.CreateTablesAsync(CreateFlags.None, typeof(TodoItem)).ConfigureAwait(false);
                initialized = true;
            }
        }
    }

    //...
}
```

La connexion de base de données est un champ statique qui garantit qu’une connexion de base de données unique est utilisée pour la durée de vie de l’application. L’utilisation d’une connexion statique persistante offre de meilleures performances que l’ouverture et la fermeture des connexions plusieurs fois au cours d’une seule session d’application.

La `InitializeAsync` méthode est responsable de vérifier si `TodoItem` une table existe déjà pour stocker des objets. Cette méthode crée automatiquement la table si elle n’existe pas.

### <a name="the-safefireandforget-extension-method"></a>La méthode d’extension SafeFireAndForget

Lorsque `TodoItemDatabase` la classe est instantanée, elle doit initialiser la connexion de base de données, qui est un processus asynchrone. Toutefois :

- Les constructeurs de classe ne peuvent pas être asynchrones.
- Une méthode async qui n’est pas attendue ne jettera pas d’exceptions.
- L’utilisation de la `Wait` méthode bloque le fil _et_ avale des exceptions.

Afin de commencer l’initialisation asynchrone, éviter de bloquer l’exécution, et avoir la `SafeFireAndForget`possibilité de saisir les exceptions, l’application de l’échantillon utilise une méthode d’extension appelée . La `SafeFireAndForget` méthode d’extension fournit `Task` des fonctionnalités supplémentaires à la classe :

```csharp
public static class TaskExtensions
{
    // NOTE: Async void is intentional here. This provides a way
    // to call an async method from the constructor while
    // communicating intent to fire and forget, and allow
    // handling of exceptions
    public static async void SafeFireAndForget(this Task task,
        bool returnToCallingContext,
        Action<Exception> onException = null)
    {
        try
        {
            await task.ConfigureAwait(returnToCallingContext);
        }

        // if the provided action is not null, catch and
        // pass the thrown exception
        catch (Exception ex) when (onException != null)
        {
            onException(ex);
        }
    }
}
```

La `SafeFireAndForget` méthode attend l’exécution asynchrone `Task` de l’objet fourni, et vous permet d’attacher un `Action` qui est appelé si une exception est lancée.

Pour plus d’informations, voir [Task-based asynchrone pattern (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Méthodes de manipulation de données

La `TodoItemDatabase` classe comprend des méthodes pour les quatre types de manipulation de données : créer, lire, modifier et supprimer. La bibliothèque SQLite.NET fournit une carte relationnelle d’objet simple (ORM) qui vous permet de stocker et de récupérer des objets sans écrire les relevés SQL.

```csharp
public class TodoItemDatabase {

    // ...

    public Task<List<TodoItem>> GetItemsAsync()
    {
        return Database.Table<TodoItem>().ToListAsync();
    }

    public Task<List<TodoItem>> GetItemsNotDoneAsync()
    {
        // SQL queries are also possible
        return Database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
    }

    public Task<TodoItem> GetItemAsync(int id)
    {
        return Database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
    }

    public Task<int> SaveItemAsync(TodoItem item)
    {
        if (item.ID != 0)
        {
            return Database.UpdateAsync(item);
        }
        else
        {
            return Database.InsertAsync(item);
        }
    }

    public Task<int> DeleteItemAsync(TodoItem item)
    {
        return Database.DeleteAsync(item);
    }
}
```

## <a name="access-data-in-xamarinforms"></a>Accéder aux données dans Xamarin.Forms

La classe Xamarin.Forms `App` expose une `TodoItemDatabase` instance de la classe :

```csharp
static TodoItemDatabase database;
public static TodoItemDatabase Database
{
    get
    {
        if (database == null)
        {
            database = new TodoItemDatabase();
        }
        return database;
    }
}
```

Cette propriété permet aux composants Xamarin.Forms d’appeler des `Database` méthodes de récupération et de manipulation de données sur l’instance en réponse à l’interaction utilisateur. Par exemple :

```csharp
var saveButton = new Button { Text = "Save" };
saveButton.Clicked += async (sender, e) =>
{
    var todoItem = (TodoItem)BindingContext;
    await App.Database.SaveItemAsync(todoItem);
    await Navigation.PopAsync();
};
```

## <a name="advanced-configuration"></a>Configuration avancée

SQLite fournit une API robuste avec plus de fonctionnalités que ce qui est couvert dans cet article et l’application d’échantillon. Les sections suivantes couvrent les caractéristiques qui sont importantes pour l’évolutivité.

Pour de plus amples renseignements, consultez [la documentation SQLite](https://www.sqlite.org/docs.html) sur sqlite.org.

### <a name="write-ahead-logging"></a>Ecrire-Ahead Logging

Par défaut, SQLite utilise un journal de restauration traditionnel. Une copie du contenu de base de données inchangée est inscrite dans un fichier de restauration distinct, puis les modifications sont écrites directement dans le fichier de base de données. Le COMMIT se produit lorsque le journal de restauration est supprimé.

Write-Ahead Logging (WAL) écrit d’abord des modifications dans un fichier WAL distinct. En mode WAL, un COMMIT est un enregistrement spécial, joint au fichier WAL, qui permet à plusieurs transactions de se produire dans un seul fichier WAL. Un fichier WAL est fusionné dans le fichier de base de données dans une opération spéciale appelée _point de contrôle_.

WAL peut être plus rapide pour les bases de données locales parce que les lecteurs et les écrivains ne bloquent pas les uns les autres, permettant de lire et d’écrire des opérations d’être simultanés. Cependant, le mode WAL n’autorise pas les modifications de la taille de la _page_, ajoute des associations de fichiers supplémentaires à la base de données, et ajoute _l’opération_ de contrôle supplémentaire.

Pour activer WAL en SQLite.NET, `EnableWriteAheadLoggingAsync` appelez `SQLiteAsyncConnection` la méthode sur l’instance :

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Pour plus d’informations, voir [SQLite Write-Ahead Logging](https://www.sqlite.org/wal.html) sur sqlite.org.

### <a name="copying-a-database"></a>Copier une base de données

Il y a plusieurs cas où il peut être nécessaire de copier une base de données SQLite :

- Une base de données a été expédiée avec votre application, mais doit être copiée ou déplacée vers un stockage évoluant sur l’appareil mobile.
- Vous devez faire une sauvegarde ou une copie de la base de données.
- Vous devez version, déplacer ou renommer le fichier de base de données.

En général, le déplacement, le changement de nom ou la copie d’un fichier de base de données est le même processus que n’importe quel autre type de fichier avec quelques considérations supplémentaires :

- Toutes les connexions de base de données doivent être fermées avant de tenter de déplacer le fichier de base de données.
- Si vous utilisez [Write-Ahead Logging](#write-ahead-logging), SQLite créera un fichier d’accès à la mémoire partagée (.shm) et un fichier (Write Ahead Log) (.wal). Assurez-vous que vous appliquez des modifications à ces fichiers ainsi.

Pour plus d’informations, voir [Traitement des fichiers dans Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Liens connexes

- [Application d’échantillon de Todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [forfait SQLite.NET NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentation SQLite](https://www.sqlite.org/docs.html)
- [Utilisation de SQLite avec Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Utilisation de SQLite avec iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Modèle asynchrone basé sur les tâches (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Classe&lt;T&gt; paresseuse](xref:System.Lazy`1)
