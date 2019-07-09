---
title: À l’aide d’ADO.NET avec Xamarin.iOS
description: Ce document décrit comment utiliser ADO.NET en tant que méthode pour accéder à SQLite dans une application Xamarin.iOS. Il aborde les références d’assembly et l’exemple BasicDataAccess Mono.Data.Sqlite.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 293ccc66395edbe18399717caf632292ff760b0b
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650184"
---
# <a name="using-adonet-with-xamarinios"></a>À l’aide d’ADO.NET avec Xamarin.iOS

Xamarin prend en charge pour la base de données SQLite est disponible sur iOS, exposé à l’aide de la syntaxe ADO.NET familier. À l’aide de ces API vous oblige à écrire des instructions SQL qui sont traitées par SQLite, tel que `CREATE TABLE`, `INSERT` et `SELECT` instructions.

## <a name="assembly-references"></a>Références d'assembly

Utiliser SQLite via ADO.NET, vous devez ajouter l’accès `System.Data` et `Mono.Data.Sqlite` fait référence à votre projet iOS, comme indiqué ici (pour obtenir des exemples dans Visual Studio pour Mac et Visual Studio) :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Références d’assembly dans Visual Studio pour Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Références d’assembly dans Visual Studio")

-----

Avec le bouton droit **références > modifier les références...**  puis cliquez pour sélectionner les assemblys requis.

## <a name="about-monodatasqlite"></a>À propos de Mono.Data.Sqlite

Nous allons utiliser le `Mono.Data.Sqlite.SqliteConnection` classe pour créer un fichier de base de données vide, puis à instancier `SqliteCommand` objets que nous pouvons utiliser pour exécuter des instructions SQL par rapport à la base de données.


1. **Création d’une base de données vide** -appelez le `CreateFile` avec valide (méthode) (ie. accessible en écriture) chemin d’accès du fichier. Vous devez vérifier si le fichier existe déjà avant d’appeler cette méthode, sinon une nouvelle base de données (vide) est créé par-dessus l’ancienne version, et les données dans l’ancien fichier seront perdues :

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > Le `dbPath` variable doit être déterminée selon les règles décrites précédemment dans ce document.

2. **Création d’une connexion de base de données** - une fois le fichier de base de données SQLite a été créé. vous pouvez créer un objet de connexion pour accéder aux données. La connexion est construite avec une chaîne de connexion qui prend la forme de `Data Source=file_path`, comme illustré ici :

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Comme mentionné précédemment, une connexion doit jamais être utilisée à nouveau sur différents threads. En cas de doute, créez la connexion en fonction des besoins et fermez-le lorsque vous avez terminé ; mais n’oubliez pas de faire plus souvent que nécessaire en trop.
    
3. **Création et exécution d’une commande de base de données** - une fois que nous disposons d’une connexion, nous pouvons exécuter des commandes SQL arbitraires par rapport à elle. Le code suivant montre une instruction CREATE TABLE en cours d’exécution.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

Lors de l’exécution de SQL directement sur la base de données, vous devez prendre les précautions normales de faire des demandes non valides, tels que la tentative de création d’une table qui existe déjà. Effectuer le suivi de la structure de votre base de données afin que vous n’entraînent pas une SqliteException tels que « table d’erreur SQLite [éléments] existe déjà ».

## <a name="basic-data-access"></a>Accès aux données de base

Le *DataAccess_Basic* exemple de code pour ce document se présente comme suit lors de l’exécution sur iOS :

 ![](using-adonet-images/image9.png "exemple ADO.NET iOS")

Le code ci-dessous montre comment effectuer des opérations simples de SQLite et montre les résultats sous forme de texte dans la fenêtre principale de l’application.

Vous devrez inclure ces espaces de noms :

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

L’exemple de code suivant montre une interaction de base de données entière :

1.  Création du fichier de base de données
2.  Insertion des données
3.  Interrogation des données

Ces opérations apparaissent généralement dans plusieurs endroits dans votre code, par exemple vous pouvez créer le fichier de base de données et les tables lors du premier démarrage de votre application et effectuer des lectures de données et les écritures dans les écrans individuels dans votre application. Dans l’exemple ci-dessous ont été regroupées dans une seule méthode pour cet exemple :

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

SQLite autorisant des commandes SQL arbitraires à exécuter sur les données, vous pouvez effectuer tout ce qui les créer, insérer, mettre à jour, supprimer ou que les instructions SELECT. Vous pouvez découvrir les commandes SQL prises en charge par SQLite sur le site Web de Sqlite. Les instructions SQL sont exécutées à l’aide d’une des trois méthodes sur un objet SqliteCommand :

-  **ExecuteNonQuery** : généralement utilisés pour l’insertion de données ou de la création de table. La valeur de retour pour certaines opérations est le nombre de lignes affectées, sinon il est -1.
-  **ExecuteReader** – utilisé lors de la collection de lignes doit être retournée comme un `SqlDataReader` .
-  **ExecuteScalar** : récupère une valeur unique (par exemple un agrégat).


### <a name="executenonquery"></a>EXECUTENONQUERY

Les instructions INSERT, UPDATE et DELETE retourne le nombre de lignes affectées. Toutes les autres instructions SQL retourne -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

La méthode suivante montre une clause WHERE dans l’instruction SELECT. Étant donné que le code consiste à créer une instruction SQL complète il doit veiller à échapper les caractères réservés tels que le guillemet (') autour de chaînes.

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

La méthode ExecuteReader retourne un objet SqliteDataReader. En plus de la méthode Read indiquée dans l’exemple, les autres propriétés utiles sont les suivantes :

-  **RowsAffected** – nombre de lignes affectées par la requête.
-  **HasRows** : indique si toutes les lignes ont été retournés.


### <a name="executescalar"></a>EXECUTESCALAR

Utilisez ces instructions SELECT qui retournent une valeur unique (par exemple, un agrégat).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

Le `ExecuteScalar` est de type de retour de la méthode `object` – vous devez effectuer un cast du résultat en fonction de la requête de base de données. Le résultat peut être un entier à partir d’une requête de nombre ou une chaîne à partir d’une requête de sélection de colonne unique. Notez que cela est différent à d’autres méthodes Execute qui retournent un objet de lecteur ou le nombre de lignes affectées.


## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recettes de données](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
