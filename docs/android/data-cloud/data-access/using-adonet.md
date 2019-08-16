---
title: Utilisation de ADO.NET avec Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 76a66b4dbde65a3fc44d3490e0147ff66b088466
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525967"
---
# <a name="using-adonet-with-android"></a>Utilisation de ADO.NET avec Android

Xamarin offre une prise en charge intégrée de la base de données SQLite disponible sur Android et peut être exposée à l’aide d’une syntaxe familière de type ADO.NET. L’utilisation de ces API vous oblige à écrire des instructions SQL qui sont traitées par SQLite `CREATE TABLE`, `INSERT` telles `SELECT` que les instructions et.

## <a name="assembly-references"></a>Références d'assembly

Pour utiliser les SQLite d’accès via ADO.net, `System.Data` vous `Mono.Data.Sqlite` devez ajouter des références à votre projet Android, comme illustré ici:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows) 

![Références Android dans Visual Studio](using-adonet-images/image7.png "Références Android dans Visual Studio") 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos) 

![Références Android dans Visual Studio pour Mac](using-adonet-images/image5.png "Références Android dans Visual Studio pour Mac") 

-----


Cliquez avec le bouton droit sur **références > modifier les références...** , puis cliquez pour sélectionner les assemblys requis.

## <a name="about-monodatasqlite"></a>À propos de mono. Data. sqlite

Nous allons utiliser la `Mono.Data.Sqlite.SqliteConnection` classe pour créer un fichier de base de données vide, `SqliteCommand` puis pour instancier des objets que nous pouvons utiliser pour exécuter des instructions SQL sur la base de données.

**Création d’une base de données vide** &ndash; Appelez la`CreateFile` méthode avec un chemin d’accès de fichier valide (c’est-à-dire accessible en écriture). Vous devez vérifier si le fichier existe déjà avant d’appeler cette méthode. dans le cas contraire, une nouvelle base de données (vide) sera créée en haut de l’ancien, et les données de l’ancien fichier seront perdues.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`La `dbPath` variable doit être déterminée conformément aux règles abordées plus haut dans ce document.

**Création d’une connexion de base de données** &ndash; Une fois le fichier de base de données SQLite créé, vous pouvez créer un objet de connexion pour accéder aux données. La connexion est construite avec une chaîne de `Data Source=file_path`connexion qui prend la forme, comme illustré ici:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Comme mentionné précédemment, une connexion ne doit jamais être réutilisée sur différents threads. En cas de doute, créez la connexion si nécessaire, puis fermez-la lorsque vous avez terminé. n’oubliez pas de le faire plus souvent que nécessaire.

**Création et exécution d’une commande de base de données** &ndash; Une fois que nous disposons d’une connexion, nous pouvons exécuter des commandes SQL arbitraires sur celle-ci. Le code ci-dessous `CREATE TABLE` montre une instruction en cours d’exécution.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

Lors de l’exécution de SQL directement sur la base de données, vous devez prendre les précautions normales afin de ne pas effectuer de demandes non valides, par exemple la tentative de création d’une table qui existe déjà. Effectuez le suivi de la structure de votre base de données afin de ne `SqliteException` pas provoquer de SQLite, par exemple la **table d’erreurs [items]** .

## <a name="basic-data-access"></a>Accès aux données de base

L’exemple de code *DataAccess_Basic* pour ce document ressemble à ceci lorsqu’il s’exécute sur Android:

![Exemple Android ADO.net](using-adonet-images/image8.png "Exemple Android ADO.net")

Le code ci-dessous montre comment effectuer des opérations SQLite simples et afficher les résultats sous forme de texte dans la fenêtre principale de l’application.

Vous devez inclure les espaces de noms suivants:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

L’exemple de code suivant montre une interaction complète de la base de données:

1. Création du fichier de base de données
2. Insertion de données
3. Interrogation des données

Ces opérations apparaissent généralement à plusieurs endroits dans votre code. par exemple, vous pouvez créer le fichier de base de données et les tables lorsque votre application démarre pour la première fois et effectuer des lectures et des écritures de données dans des écrans individuels de votre application. Dans l’exemple ci-dessous, vous avez été regroupé dans une méthode unique pour cet exemple:

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}

```

## <a name="more-complex-queries"></a>Requêtes plus complexes

Étant donné que SQLite permet d’exécuter des commandes SQL arbitraires sur les données, vous `CREATE`pouvez `INSERT`effectuer `UPDATE`toutes `DELETE`les instructions `SELECT` ,,, ou de votre choix. Vous pouvez en savoir plus sur les commandes SQL prises en charge par SQLite sur le site Web SQLite. Les instructions SQL sont exécutées à l’aide de l’une `SqliteCommand` des trois méthodes sur un objet:

- **ExecuteNonQuery** &ndash; Généralement utilisé pour la création de table ou l’insertion de données. La valeur de retour pour certaines opérations est le nombre de lignes affectées, sinon,-1.

- **ExecuteReader** Utilisé lorsqu’une collection de lignes doit être retournée `SqlDataReader`en tant que. &ndash;

- **ExecuteScalar** &ndash; Récupère une valeur unique (par exemple, un agrégat).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`les `UPDATE`instructions, `DELETE` et retournent le nombre de lignes affectées. Toutes les autres instructions SQL retournent-1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

La méthode suivante affiche une `WHERE` clause dans l' `SELECT` instruction.
Étant donné que le code élabore une instruction SQL complète, il doit s’occuper de l’échappement des caractères réservés tels que le guillemet (') autour des chaînes.

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

La méthode `ExecuteReader` retourne un objet `SqliteDataReader`. En plus de la `Read` méthode illustrée dans l’exemple, d’autres propriétés utiles sont les suivantes:

- **RowsAffected** &ndash; Nombre de lignes affectées par la requête.

- **HasRows** &ndash; Indique si des lignes ont été retournées.


### <a name="executescalar"></a>EXECUTESCALAR

Utilisez cette fonction `SELECT` pour les instructions qui retournent une valeur unique (par exemple, un agrégat).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Le `ExecuteScalar` type de retour de la `object` méthode est &ndash; que vous devez effectuer un cast du résultat en fonction de la requête de base de données. Le résultat peut être un entier d’une `COUNT` requête ou une chaîne d’une requête de `SELECT` colonne unique. Notez que cela est différent pour les `Execute` autres méthodes qui retournent un objet lecteur ou le nombre de lignes affectées.



## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
