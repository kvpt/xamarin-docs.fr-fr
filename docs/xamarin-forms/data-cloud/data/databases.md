---
title : " Xamarin.Forms base de données locale" Description : " Xamarin.Forms prend en charge les applications pilotées par base de données à l’aide du moteur de base de données SQLite, qui permet de charger et d’enregistrer des objets dans du code partagé. Cet article décrit comment Xamarin.Forms les applications peuvent lire et écrire des données dans une base de données SQLite locale à l’aide de sqlite.net.
ms. Prod : xamarin ms. AssetID : F687B24B-7DF0-4F8E-A21A-A9BB507480EB ms. Technology : xamarin-Forms Author : profexorgeek ms. Author : jusjohns ms. Date : 12/05/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-local-databases"></a>Xamarin.FormsBases de données locales

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

Le moteur de base de données SQLite permet Xamarin.Forms aux applications de charger et d’enregistrer des objets de données dans du code partagé. L’exemple d’application utilise une table de base de données SQLite pour stocker les éléments todo. Cet article explique comment utiliser SQLite.Net dans du code partagé pour stocker et récupérer des informations dans une base de données locale.

[![Captures d’écran de l’application ToDoList sur iOS et Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "Application ToDoList sur iOS et Android")

Intégrez SQLite.NET dans Mobile Apps en procédant comme suit :

1. [Installez le package NuGet](#install-the-sqlite-nuget-package).
1. [Configurez des constantes](#configure-app-constants).
1. [Créer une classe d’accès à la base de données](#create-a-database-access-class).
1. [Accéder aux données Xamarin.Forms dans ](#access-data-in-xamarinforms).
1. [Configuration avancée](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Installer le package NuGet SQLite

Utilisez le gestionnaire de package NuGet pour rechercher **SQLite-net-PCL** et ajouter la version la plus récente au projet de code partagé.

Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :

- **Créé par :** Frank A. Krueger (praeclarum)
- **ID :** SQLite-net-PCL
- **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Ne vous fiez pas au nom du package. Vous devez utiliser le package NuGet **sqlite-net-pcl**, même dans les projets .NET Standard.

## <a name="configure-app-constants"></a>Configurer des constantes d’application

L’exemple de projet inclut un fichier **constants.cs** qui fournit des données de configuration communes :

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

Le fichier constants spécifie les `SQLiteOpenFlag` valeurs d’énumération par défaut utilisées pour initialiser la connexion à la base de données. L' `SQLiteOpenFlag` énumération prend en charge les valeurs suivantes :

- `Create`: La connexion crée automatiquement le fichier de base de données, s’il n’existe pas.
- `FullMutex`: La connexion est ouverte en mode de thread sérialisé.
- `NoMutex`: La connexion est ouverte en mode multithread.
- `PrivateCache`: La connexion ne fait pas partie du cache partagé, même si elle est activée.
- `ReadWrite`: La connexion peut lire et écrire des données.
- `SharedCache`: La connexion participera au cache partagé, si elle est activée.
- `ProtectionComplete`: Le fichier est chiffré et inaccessible lorsque l’appareil est verrouillé.
- `ProtectionCompleteUnlessOpen`: Le fichier est chiffré jusqu’à ce qu’il soit ouvert, mais il est ensuite accessible même si l’utilisateur verrouille l’appareil.
- `ProtectionCompleteUntilFirstUserAuthentication`: Le fichier est chiffré jusqu’à ce que l’utilisateur ait démarré et déverrouillé l’appareil.
- `ProtectionNone`: Le fichier de base de données n’est pas chiffré.

Vous devrez peut-être spécifier des indicateurs différents selon la manière dont votre base de données sera utilisée. Pour plus d’informations sur `SQLiteOpenFlags` , voir [ouverture d’une nouvelle connexion de base de données](https://www.sqlite.org/c3ref/open.html) sur sqlite.org.

## <a name="create-a-database-access-class"></a>Créer une classe d’accès à la base de données

Une classe wrapper de base de données soustrait la couche d’accès aux données du reste de l’application. Cette classe centralise la logique de requête et simplifie la gestion de l’initialisation de la base de données, ce qui facilite la refactorisation ou le développement des opérations de données à mesure que l’application se développe. L’application todo définit une `TodoItemDatabase` classe à cet effet.

### <a name="lazy-initialization"></a>Initialisation tardive

`TodoItemDatabase`Utilise la classe .NET `Lazy` pour différer l’initialisation de la base de données jusqu’à son premier accès. L’utilisation de l’initialisation tardive empêche le processus de chargement de la base de données de retarder le lancement de l’application. Pour plus d’informations, [consultez &lt; &gt; classe Lazy T](xref:System.Lazy`1).

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

La connexion de base de données est un champ statique qui garantit qu’une connexion de base de données unique est utilisée pendant la durée de vie de l’application. L’utilisation d’une connexion statique permanente offre de meilleures performances que l’ouverture et la fermeture des connexions plusieurs fois pendant une session d’application unique.

La `InitializeAsync` méthode est chargée de vérifier si une table existe déjà pour le stockage des `TodoItem` objets. Cette méthode crée automatiquement la table si elle n’existe pas.

### <a name="the-safefireandforget-extension-method"></a>Méthode d’extension SafeFireAndForget

Lorsque la `TodoItemDatabase` classe est instanciée, elle doit initialiser la connexion à la base de données, qui est un processus asynchrone. Toutefois :

- Les constructeurs de classe ne peuvent pas être asynchrones.
- Une méthode Async qui n’est pas attendue ne lèvera pas d’exceptions.
- L’utilisation de la `Wait` méthode bloque le thread _et_ absorbe les exceptions.

Pour démarrer l’initialisation asynchrone, éviter le blocage de l’exécution et avoir la possibilité d’intercepter des exceptions, l’exemple d’application utilise une méthode d’extension appelée `SafeFireAndForget` . La `SafeFireAndForget` méthode d’extension fournit des fonctionnalités supplémentaires à la `Task` classe :

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

La `SafeFireAndForget` méthode attend l’exécution asynchrone de l’objet fourni `Task` et vous permet d’attacher un `Action` qui est appelé si une exception est levée.

Pour plus d’informations, consultez [modèle asynchrone basé sur les tâches (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Méthodes de manipulation de données

La `TodoItemDatabase` classe comprend des méthodes pour les quatre types de manipulation de données : créer, lire, modifier et supprimer. La bibliothèque SQLite.NET fournit un simple mappage relationnel objet (ORM) qui vous permet de stocker et de récupérer des objets sans écrire d’instructions SQL.

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

## <a name="access-data-in-xamarinforms"></a>Accéder aux données dansXamarin.Forms

La Xamarin.Forms `App` classe expose une instance de la `TodoItemDatabase` classe :

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

Cette propriété permet Xamarin.Forms aux composants d’appeler des méthodes de récupération des données et de manipulation sur l' `Database` instance en réponse à une interaction de l’utilisateur. Par exemple :

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

SQLite fournit une API robuste avec plus de fonctionnalités que celles décrites dans cet article et l’exemple d’application. Les sections suivantes couvrent les fonctionnalités qui sont importantes pour l’évolutivité.

Pour plus d’informations, consultez [la documentation SQLite](https://www.sqlite.org/docs.html) sur sqlite.org.

### <a name="write-ahead-logging"></a>Journalisation avec écriture anticipée

Par défaut, SQLite utilise un journal de restauration traditionnel. Une copie du contenu de la base de données inchangée est écrite dans un fichier de restauration distinct, puis les modifications sont écrites directement dans le fichier de base de données. La validation se produit lorsque le journal de restauration est supprimé.

La journalisation en écriture anticipée (WAL) écrit d’abord les modifications dans un fichier WAL distinct. En mode WAL, une validation est un enregistrement spécial, ajouté au fichier WAL, qui permet à plusieurs transactions de se produire dans un seul fichier WAL. Un fichier WAL est fusionné dans le fichier de base de données dans une opération spéciale appelée _point de contrôle_.

WAL peut être plus rapide pour les bases de données locales, car les lecteurs et les enregistreurs ne se bloquent pas mutuellement, ce qui permet d’effectuer des opérations de lecture et d’écriture simultanées. Toutefois, le mode WAL n’autorise pas les modifications apportées à la taille de la _page_, ajoute des associations de fichiers supplémentaires à la base de données et ajoute l’opération _de point de contrôle_ supplémentaire.

Pour activer WAL dans SQLite.NET, appelez la `EnableWriteAheadLoggingAsync` méthode sur l' `SQLiteAsyncConnection` instance :

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Pour plus d’informations, consultez [journalisation en écriture anticipée SQLite](https://www.sqlite.org/wal.html) sur sqlite.org.

### <a name="copying-a-database"></a>Copie d’une base de données

Dans certains cas, il peut être nécessaire de copier une base de données SQLite :

- Une base de données a été fournie avec votre application, mais elle doit être copiée ou déplacée vers un stockage accessible en écriture sur le périphérique mobile.
- Vous devez effectuer une sauvegarde ou une copie de la base de données.
- Vous devez modifier la version, déplacer ou renommer le fichier de base de données.

En général, le déplacement, le changement de nom ou la copie d’un fichier de base de données est le même processus que tout autre type de fichier, avec quelques remarques supplémentaires :

- Toutes les connexions de base de données doivent être fermées avant de tenter de déplacer le fichier de base de données.
- Si vous utilisez la [journalisation en écriture anticipée](#write-ahead-logging), SQLite crée un fichier d’accès à la mémoire partagée (. fichier SHM) et un fichier (Journal d’écriture anticipée) (. Wal). Veillez également à appliquer toutes les modifications apportées à ces fichiers.

Pour plus d’informations, consultez [gestion des Xamarin.Forms fichiers dans ](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Liens connexes

- [Exemple d’application TODO](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [Package NuGet SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentation SQLite](https://www.sqlite.org/docs.html)
- [Utilisation de SQLite avec Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Utilisation de SQLite avec iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Modèle asynchrone basé sur les tâches (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Lazy &lt; T ( &gt; classe)](xref:System.Lazy`1)
