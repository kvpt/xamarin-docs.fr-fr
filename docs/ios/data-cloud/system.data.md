---
title: System. Data dans Xamarin. iOS
description: Ce document explique comment utiliser System. Data et mono. Data. sqlite. dll pour accéder aux données SQLite dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 44d2e468efeacea919af2d243588d0da6d72945d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766535"
---
# <a name="systemdata-in-xamarinios"></a>System. Data dans Xamarin. iOS

Xamarin. iOS 8,10 ajoute la prise en charge de [System. Data](xref:System.Data), y compris le `Mono.Data.Sqlite.dll` fournisseur ADO.net. La prise en charge comprend l’ajout des [assemblys](~/cross-platform/internals/available-assemblies.md)suivants :

- `System.Data.dll`
- `System.Data.Service.Client.dll`
- `System.Transactions.dll`
- `Mono.Data.Tds.dll`
- `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Exemples

Le programme suivant crée une base de `Documents/mydb.db3`données dans, et si la base de données n’existe pas déjà, elle est remplie avec des exemples de données. La base de données est ensuite interrogée, avec la sortie `stderr`écrite.

### <a name="add-references"></a>Ajouter des références

Tout d’abord, cliquez avec le bouton droit sur le nœud **références** et choisissez **modifier les références...** , puis sélectionnez `System.Data` et : `Mono.Data.Sqlite`

[![](system.data-images/edit-references-sml.png "Ajout de nouvelles références")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Exemple de code

Le code suivant montre un exemple simple de création d’une table et d’insertion de lignes à l’aide de commandes SQL incorporées :

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> Comme mentionné dans l’exemple de code ci-dessus, il est déconseillé d’incorporer des chaînes dans les commandes SQL, car cela rend votre code vulnérable à l' [injection SQL](https://en.wikipedia.org/wiki/SQL_injection).

### <a name="using-command-parameters"></a>Utilisation des paramètres de commande

Le code suivant montre comment utiliser les paramètres de commande pour insérer en toute sécurité du texte entré par l’utilisateur dans la base de données (même si le texte contient des caractères SQL spéciaux comme une apostrophe) :

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>Fonctionnalités manquantes

Les fonctionnalités de **System. Data** et **mono. Data. sqlite** sont manquantes.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

Les fonctionnalités manquantes dans **System. Data. dll** sont constituées des éléments suivants :

- Tout ce qui nécessite [System. CodeDom](xref:System.CodeDom) (par exemple,  [System. Data. TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
- Prise en charge du fichier de configuration XML (par exemple  [System. Data. Common. DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
- [System. Data. Common. DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (dépend de la prise en charge du fichier de configuration XML)
- [System.Data.OleDb](xref:System.Data.OleDb)
- [System.Data.Odbc](xref:System.Data.Odbc)
- La `System.EnterpriseServices.dll` dépendance a été *supprimée* de `System.Data.dll` , ce qui a entraîné la suppression de la méthode [SqlConnection. EnlistDistributedTransaction (ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) .

<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

En attendant, **mono. Data. sqlite. dll** n’a subi aucune modification de code source, mais peut à la place héberger un `Mono.Data.Sqlite.dll` certain nombre de problèmes d' *exécution* , car lie SQLite 3,5. iOS 8, en attendant, est fourni avec SQLite 3.8.5. Il suffit d’indiquer que certaines choses ont changé entre les deux versions.

Une version antérieure d’iOS est fournie avec les versions suivantes de SQLite :

- **iOS 7** -version 3.7.13.
- **iOS 6** -version 3.7.13.
- **iOS 5** -version 3.7.7.
- **iOS 4** -version 3.6.22.

Les problèmes les plus courants semblent liés à l’interrogation des schémas de base de données, par exemple la détermination de la présence de colonnes sur une `Mono.Data.Sqlite.SqliteConnection.GetSchema` table donnée au moment de l’exécution `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` , telles que la substitution de [DbConnection. GetSchema](xref:System.Data.Common.DbConnection.GetSchema) et (substitution [ DbDataReader. GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). En bref, il semble que tout ce qui utilise [DataTable](xref:System.Data.DataTable) est peu susceptible de fonctionner.

<a name="Data_Binding" />

## <a name="data-binding"></a>Liaison de données

La liaison de données n’est pas prise en charge pour l’instant.
