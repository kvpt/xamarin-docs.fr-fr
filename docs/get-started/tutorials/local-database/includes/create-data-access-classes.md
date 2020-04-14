---
ms.openlocfilehash: b11a5972c2aabace8a6991a82f5719f34450297d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "67841497"
---
Dans cet exercice, vous ajouterez des classes d’accès aux données au projet **LocalDatabaseTutorial** qui sera utilisé pour conserver les données sur les personnes dans la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **l’Explorateur de solutions**, dans le projet **LocalDatabaseTutorial**, ajoutez une classe nommée `Person` au projet. Puis, dans **Person.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Ce code définit une classe `Person` qui stockera les données relatives à chaque individu dans l’application. La propriété `ID` est identifiée par les attributs `PrimaryKey` et `AutoIncrement` pour garantir que chaque instance `Person` de la base de données bénéficie d’un ID unique fourni par SQLite.NET.

1. Dans **l’Explorateur de solutions**, dans le projet **LocalDatabaseTutorial**, ajoutez une classe nommée `Database` au projet. Puis, dans **Database.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Cette classe contient le code permettant de créer la base de données, lire les données contenues et y écrire des données. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `Database` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin d’accès est fourni par la classe `App` dans l’exercice suivant.

1. Dans **l’Explorateur de solutions**, dans le projet **LocalDatabaseTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **App.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Ce code définit une propriété `Database` qui crée une instance `Database` comme singleton. Un chemin d’accès local et un nom de fichier (correspondant à l’emplacement de stockage de la base de données) sont transmis sous forme d’argument au constructeur de classe `Database`.

    > [!IMPORTANT]
    > Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **Panneau Solutions**, dans le projet **LocalDatabaseTutorial**, ajoutez une classe nommée `Person` au projet. Puis, dans **Person.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Ce code définit une classe `Person` qui stockera les données relatives à chaque individu dans l’application. La propriété `ID` est identifiée par les attributs `PrimaryKey` et `AutoIncrement` pour garantir que chaque instance `Person` de la base de données bénéficie d’un ID unique fourni par SQLite.NET.

1. Dans **Panneau Solutions**, dans le projet **LocalDatabaseTutorial**, ajoutez une classe nommée `Database` au projet. Puis, dans **Database.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Cette classe contient le code permettant de créer la base de données, lire les données contenues et y écrire des données. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `Database` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin d’accès est fourni par la classe `App` dans l’exercice suivant.

1. Dans **Panneau Solutions**, dans le projet **LocalDatabaseTutorial**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir. Ensuite, dans **App.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Ce code définit une propriété `Database` qui crée une instance `Database` comme singleton. Un chemin d’accès local et un nom de fichier (correspondant à l’emplacement de stockage de la base de données) sont transmis sous forme d’argument au constructeur de classe `Database`.

    > [!IMPORTANT]
    > Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

1. Générez la solution pour vérifier qu’elle ne contient aucune erreur.
