---
title: Utilisation de SQLite.NET avec Android
description: La bibliothèque NuGet SQLite.NET PCL fournit un mécanisme d’accès aux données simple pour les applications Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: 96f084dc49a5558767b162eee59eff722f247904
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023671"
---
# <a name="using-sqlitenet-with-android"></a>Utilisation de SQLite.NET avec Android

La bibliothèque SQLite.NET que Xamarin recommande est un ORM très basique qui vous permet de stocker et de récupérer facilement des objets dans la base de données SQLite locale sur un appareil Android. ORM représente le mappage relationnel objet &ndash; une API qui vous permet d’enregistrer et de récupérer des « objets » à partir d’une base de données sans écrire d’instructions SQL.

Pour inclure la bibliothèque SQLite.NET dans une application Xamarin, ajoutez le package NuGet suivant à votre projet :

- **Nom du package :** SQLite-net-PCL
- **Auteur :** Frank A. Krueger
- **ID**  sqlite-net-pcl
- **URL :** [NuGet.org/packages/SQLite-net-PCL](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Package NuGet SQLite.NET](using-sqlite-orm-images/image1a-sml.png "Package NuGet SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Un certain nombre de packages SQLite différents sont disponibles : Veillez à choisir le bon (il est possible qu’il ne s’agit pas du résultat le plus élevé dans la recherche).

Une fois la bibliothèque SQLite.NET disponible, suivez ces trois étapes pour l’utiliser pour accéder à une base de données :

1. **Ajoutez une instruction using** &ndash; ajoutez l’instruction suivante aux C# fichiers où l’accès aux données est requis :

    ```csharp
    using SQLite;
    ```

2. **Créer une base de données vide** &ndash; une référence de base de données peut être créée en passant le chemin d’accès du fichier au constructeur de la classe SQLiteConnection. Vous n’avez pas besoin de vérifier si le fichier existe déjà &ndash; il sera automatiquement créé, le cas échéant, dans le cas contraire, le fichier de base de données existant sera ouvert. La variable de `dbPath` doit être déterminée en fonction des règles abordées plus haut dans ce document :

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3. **Enregistrer les données** &ndash; une fois que vous avez créé un objet SQLiteConnection, les commandes de base de données sont exécutées en appelant ses méthodes, telles que CreateTable et insert comme suit :

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4. **Récupérez des données** &ndash; pour récupérer un objet (ou une liste d’objets), utilisez la syntaxe suivante :

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemple d’accès aux données de base

L’exemple de code *DataAccess_Basic* pour ce document ressemble à ce qui suit lors de l’exécution sur Android. Le code illustre comment effectuer des opérations SQLite.NET simples et affiche les résultats sous forme de texte dans la fenêtre principale de l’application.

**Android**

![Exemple Android SQLite.NET](using-sqlite-orm-images/image3.png "Exemple Android SQLite.NET")

L’exemple de code suivant montre une interaction de base de données entière à l’aide de la bibliothèque SQLite.NET pour encapsuler l’accès à la base de données sous-jacent.
Elle indique :

1. Création du fichier de base de données

2. Insertion de données en créant des objets, puis en les enregistrant

3. Interrogation des données

Vous devez inclure les espaces de noms suivants :

```csharp
using SQLite; // from the github SQLite.cs class
```

La dernière nécessite que vous ayez ajouté SQLite à votre projet. Notez que la table de base de données SQLite est définie en ajoutant des attributs à une classe (la classe `Stock`) au lieu d’une commande CREATE TABLE.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

Si vous utilisez l’attribut `[Table]` sans spécifier de paramètre de nom de table, la table de base de données sous-jacente aura le même nom que la classe (dans le cas présent, « stock »). Le nom réel de la table est important si vous écrivez des requêtes SQL directement sur la base de données au lieu d’utiliser les méthodes d’accès aux données ORM. De même, l’attribut `[Column("_id")]` est facultatif et, s’il est absent, une colonne est ajoutée à la table avec le même nom que la propriété dans la classe.

## <a name="sqlite-attributes"></a>Attributs SQLite

Les attributs courants que vous pouvez appliquer à vos classes pour contrôler la façon dont ils sont stockés dans la base de données sous-jacente sont les suivants :

- **[PrimaryKey]** &ndash; cet attribut peut être appliqué à une propriété entière pour le forcer à être la clé primaire de la table sous-jacente. Les clés primaires composites ne sont pas prises en charge.

- **[AutoIncrement]** &ndash; cet attribut entraîne l’incrémentation automatique de la valeur d’une propriété entière pour chaque nouvel objet inséré dans la base de données.

- **[Column (Name)]** &ndash; la spécification du paramètre facultatif `name` remplacera la valeur par défaut du nom de la colonne de base de données sous-jacente (qui est identique à la propriété).

- **[Table (Name)]** &ndash; marque la classe comme pouvant être stockée dans une table SQLite sous-jacente. La spécification du paramètre de nom facultatif remplace la valeur par défaut du nom de la table de base de données sous-jacente (qui est identique au nom de la classe).

- **[MaxLength (value)]** &ndash; limiter la longueur d’une propriété de texte, en cas de tentative d’insertion d’une base de données. La consommation de code doit valider cela avant l’insertion de l’objet, car cet attribut est uniquement « Checked » lorsqu’une opération d’insertion ou de mise à jour de base de données est tentée.

- **[Ignore]** &ndash; fait en sorte que sqlite.net ignore cette propriété.
    Cela s’avère particulièrement utile pour les propriétés qui ont un type qui ne peut pas être stocké dans la base de données, ou des propriétés qui modélisent des collections qui ne peuvent pas être résolues automatiquement par SQLite.

- **[Unique]** &ndash; garantit que les valeurs de la colonne de base de données sous-jacente sont uniques.

La plupart de ces attributs sont facultatifs, SQLite utilise les valeurs par défaut pour les noms de table et de colonne. Vous devez toujours spécifier une clé primaire entière pour que les requêtes de sélection et de suppression puissent être effectuées efficacement sur vos données.

## <a name="more-complex-queries"></a>Requêtes plus complexes

Les méthodes suivantes sur `SQLiteConnection` peuvent être utilisées pour effectuer d’autres opérations de données :

- **Insert** &ndash; ajoute un nouvel objet à la base de données.

- **Obtient&lt;t&gt;** &ndash; tente de récupérer un objet à l’aide de la clé primaire.

- La **table&lt;t&gt;** &ndash; retourne tous les objets de la table.

- **Delete** &ndash; supprime un objet à l’aide de sa clé primaire.

- **Query&lt;t&gt;** &ndash; exécuter une requête SQL qui retourne un nombre de lignes (sous forme d’objets).

- **Exécutez** &ndash; Utilisez cette méthode (et non `Query`) lorsque vous ne vous attendez pas à revenir sur les lignes de SQL (comme les instructions INSERT, Update et Delete).

### <a name="getting-an-object-by-the-primary-key"></a>Obtention d’un objet à l’aide de la clé primaire

SQLite.Net fournit la méthode d’extraction pour récupérer un seul objet en fonction de sa clé primaire.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Sélection d’un objet à l’aide de LINQ

Les méthodes qui retournent des collections prennent en charge `IEnumerable<T>` afin que vous puissiez utiliser LINQ pour interroger ou trier le contenu d’une table. Le code suivant montre un exemple utilisant LINQ pour filtrer toutes les entrées qui commencent par la lettre « A » :

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Sélection d’un objet à l’aide de SQL

Même si SQLite.Net peut fournir un accès basé sur les objets à vos données, vous pouvez parfois avoir besoin d’effectuer une requête plus complexe que LINQ n’autorise (ou vous pouvez avoir besoin de performances plus rapides). Vous pouvez utiliser des commandes SQL avec la méthode Query, comme illustré ici :

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Lorsque vous écrivez des instructions SQL directement, vous créez une dépendance sur les noms des tables et des colonnes de votre base de données, qui ont été générées à partir de vos classes et de leurs attributs. Si vous modifiez ces noms dans votre code, vous devez penser à mettre à jour toutes les instructions SQL écrites manuellement.

### <a name="deleting-an-object"></a>Suppression d’un objet

La clé primaire est utilisée pour supprimer la ligne, comme illustré ici :

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Vous pouvez vérifier le `rowcount` pour vérifier le nombre de lignes affectées (supprimé dans ce cas).

## <a name="using-sqlitenet-with-multiple-threads"></a>Utilisation de SQLite.NET avec plusieurs threads

SQLite prend en charge trois modes de thread différents : *à thread unique* *, multithread et* *sérialisé*. Si vous souhaitez accéder à la base de données à partir de plusieurs threads sans aucune restriction, vous pouvez configurer SQLite pour utiliser le mode de thread **sérialisé** . Il est important de définir ce mode tôt dans votre application (par exemple, au début de la méthode `OnCreate`).

Pour modifier le mode de thread, appelez `SqliteConnection.SetConfig`. Par exemple, cette ligne de code configure SQLite pour le mode **sérialisé** :

```csharp
using using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

La version Android de SQLite a une limitation qui nécessite quelques étapes supplémentaires. Si l’appel à `SqliteConnection.SetConfig` produit une exception SQLite comme `library used incorrectly`, vous devez utiliser la solution de contournement suivante :

1. Établissez un lien vers la bibliothèque **libsqlite.so** Native afin que les API `sqlite3_shutdown` et `sqlite3_initialize` soient mises à la disposition de l’application :

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```

2. Tout au début de la méthode `OnCreate`, ajoutez ce code pour arrêter les SQLite, le configurer pour le mode **sérialisé** et réinitialiser sqlite :

    ```csharp
    using using Mono.Data.Sqlite;
    ...
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Cette solution de contournement fonctionne également pour la bibliothèque de `Mono.Data.Sqlite`. Pour plus d’informations sur le SQLite et le multithreading, consultez [SQLite et plusieurs threads](https://www.sqlite.org/threadsafe.html).

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
