---
title: Utilisation de SQLite.NET avec Xamarin. iOS
description: La bibliothèque NuGet SQLite.NET PCL fournit un mécanisme d’accès aux données simple pour les applications Xamarin. iOS. Ce document fournit une vue d’ensemble de l’utilisation de cette bibliothèque.
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: fb0981fea906a474d39834a52f0a8bfdf496ca1e
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488411"
---
# <a name="using-sqlitenet-with-xamarinios"></a>Utilisation de SQLite.NET avec Xamarin. iOS

La bibliothèque SQLite.NET que Xamarin recommande est un ORM de base qui vous permet de stocker et de récupérer des objets dans la base de données SQLite locale sur un appareil iOS.
ORM correspond au mappage relationnel d’objet : une API qui vous permet d’enregistrer et de récupérer des « objets » à partir d’une base de données sans écrire d’instructions SQL.

<a name="Usage"/>

## <a name="usage"></a>Contrôle

Pour inclure la bibliothèque SQLite.NET dans une application Xamarin, ajoutez le package NuGet suivant à votre projet :

- **Nom du package :** SQLite-net-PCL
- **Auteur :** Frank A. Krueger
- **ID**  sqlite-net-pcl
- **URL :** [NuGet.org/packages/SQLite-net-PCL](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Package NuGet SQLite.NET](using-sqlite-orm-images/image1a-sml.png "Package NuGet SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Un certain nombre de packages SQLite différents sont disponibles : Veillez à choisir le bon (il est possible qu’il ne s’agit pas du résultat le plus élevé dans la recherche).

Une fois la bibliothèque SQLite.NET disponible, suivez ces trois étapes pour l’utiliser pour accéder à une base de données :

1. **Ajoutez une instruction using** : ajoutez l’instruction suivante aux C# fichiers où l’accès aux données est requis :

    ```csharp
    using SQLite;
    ```

1. **Créer une base de données vide** : une référence de base de données peut être créée en passant le chemin d’accès du fichier au constructeur de la classe SQLiteConnection. Vous n’avez pas besoin de vérifier si le fichier existe déjà. il sera automatiquement créé si nécessaire. sinon, le fichier de base de données existant sera ouvert.

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    La variable dbPath doit être déterminée conformément aux règles abordées plus haut dans ce document.

1. **Enregistrer des données** -une fois que vous avez créé un objet SQLiteConnection, les commandes de base de données sont exécutées en appelant ses méthodes, telles que CreateTable et insert comme suit :

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **Récupérer des données** : pour récupérer un objet (ou une liste d’objets), utilisez la syntaxe suivante :

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemple d’accès aux données de base

L’exemple de code *DataAccess_Basic* pour ce document ressemble à ce qui suit lors de l’exécution sur iOS. Le code illustre comment effectuer des opérations SQLite.NET simples et affiche les résultats sous forme de texte dans la fenêtre principale de l’application.

**iOS**

 [exemple de ![iOS SQLite.NET](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

L’exemple de code suivant montre une interaction de base de données entière à l’aide de la bibliothèque SQLite.NET pour encapsuler l’accès à la base de données sous-jacent. Elle indique :

1. Création du fichier de base de données
1. Insertion de données en créant des objets, puis en les enregistrant
1. Interrogation des données

Vous devez inclure les espaces de noms suivants :

```csharp
using SQLite; // from the github SQLite.cs class
```

Pour cela, vous devez avoir ajouté SQLite à votre projet, comme [nous](#Usage)l’avons mis en surbrillance ici. Notez que la table de base de données SQLite est définie en ajoutant des attributs à une classe (la classe `Stock`) au lieu d’une commande CREATE TABLE.

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

- **[PrimaryKey]** : cet attribut peut être appliqué à une propriété de type entier pour le forcer à être la clé primaire de la table sous-jacente. Les clés primaires composites ne sont pas prises en charge.
- **[AutoIncrement]** : cet attribut entraîne l’incrémentation automatique de la valeur d’une propriété entière pour chaque nouvel objet inséré dans la base de données.
- **[Column (Name)]** &ndash; le paramètre `name` définit le nom de la colonne de base de données sous-jacente.
- **[Table (Name)]** : marque la classe comme pouvant être stockée dans une table SQLite sous-jacente avec le nom spécifié.
- **[MaxLength (valeur)]** : limite la longueur d’une propriété de texte, en cas de tentative d’insertion d’une base de données. La consommation de code doit valider cela avant l’insertion de l’objet, car cet attribut est uniquement « Checked » lorsqu’une opération d’insertion ou de mise à jour de base de données est tentée.
- **[Ignore]** : force sqlite.net à ignorer cette propriété. Cela s’avère particulièrement utile pour les propriétés qui ont un type qui ne peut pas être stocké dans la base de données, ou les propriétés qui modélisent des collections qui ne peuvent pas être résolues automatiquement, SQLite.
- **[Unique]** : garantit que les valeurs de la colonne de base de données sous-jacente sont uniques.

La plupart de ces attributs sont facultatifs. Vous devez toujours spécifier une clé primaire entière pour que les requêtes de sélection et de suppression puissent être effectuées efficacement sur vos données.

## <a name="more-complex-queries"></a>Requêtes plus complexes

Les méthodes suivantes sur `SQLiteConnection` peuvent être utilisées pour effectuer d’autres opérations de données :

- **Insert** : ajoute un nouvel objet à la base de données.
- **Obtenir\<t >** : tente de récupérer un objet à l’aide de la clé primaire.
- **Table\<t >** : retourne tous les objets de la table.
- **Delete** : supprime un objet à l’aide de sa clé primaire.
- **Query\<t >** -exécute une requête SQL qui retourne un nombre de lignes (sous la forme d’objets).
- **Exécuter** : utilisez cette méthode (et non `Query`) lorsque vous ne vous attendez pas à revenir sur les lignes de SQL (par exemple, instructions INSERT, Update et Delete).

### <a name="getting-an-object-by-the-primary-key"></a>Obtention d’un objet à l’aide de la clé primaire

SQLite.Net fournit la méthode d’extraction pour récupérer un seul objet en fonction de sa clé primaire.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Sélection d’un objet à l’aide de LINQ

Les méthodes qui retournent des collections prennent en charge IEnumerable\<T > afin que vous puissiez utiliser LINQ pour interroger ou trier le contenu d’une table. Le code suivant montre un exemple utilisant LINQ pour filtrer toutes les entrées qui commencent par la lettre « A » :

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

> [!IMPORTANT]
> Lorsque vous écrivez des instructions SQL directement, vous créez une dépendance sur les noms des tables et des colonnes de votre base de données, qui ont été générées à partir de vos classes et de leurs attributs. Si vous modifiez ces noms dans votre code, vous devez penser à mettre à jour toutes les instructions SQL écrites manuellement.

### <a name="deleting-an-object"></a>Suppression d’un objet

La clé primaire est utilisée pour supprimer la ligne, comme illustré ici :

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Vous pouvez vérifier le `rowcount` pour vérifier le nombre de lignes affectées (supprimé dans ce cas).

## <a name="using-sqlitenet-with-multiple-threads"></a>Utilisation de SQLite.NET avec plusieurs threads

SQLite prend en charge trois modes de thread différents : *à thread unique* *, multithread et* *sérialisé*. Si vous souhaitez accéder à la base de données à partir de plusieurs threads sans aucune restriction, vous pouvez configurer SQLite pour utiliser le mode de thread **sérialisé** . Il est important de définir ce mode tôt dans votre application (par exemple, au début de la méthode `OnCreate`).

Pour modifier le mode de thread, appelez `SqliteConnection.SetConfig` qui se trouve dans l’espace de noms `Mono.Data.Sqlite`. Par exemple, cette ligne de code configure SQLite pour le mode **sérialisé** :

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
