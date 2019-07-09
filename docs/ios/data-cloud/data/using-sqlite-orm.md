---
title: Utilisation de SQLite.NET avec Xamarin.iOS
description: La bibliothèque de SQLite.NET PCL NuGet fournit un mécanisme d’accès de données simple pour les applications Xamarin.iOS. Ce document fournit une vue d’ensemble de l’utilisation de cette bibliothèque.
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/18/2018
ms.openlocfilehash: 253c67ef5c46c83884df05d265613305dd5837f5
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650163"
---
# <a name="using-sqlitenet-with-xamarinios"></a>Utilisation de SQLite.NET avec Xamarin.iOS

La bibliothèque de SQLite.NET Xamarin recommande est un ORM de base qui vous permet de stocker et récupérer des objets dans la base de données SQLite locale sur un appareil iOS.
ORM est l’abréviation de mappage objet relationnelle : une API qui vous permet d’enregistrer et récupérer les « objets » à partir d’une base de données sans avoir à écrire des instructions SQL.

<a name="Usage"/>

## <a name="usage"></a>Utilisation

Pour inclure la bibliothèque de SQLite.NET dans une application Xamarin, ajoutez le package NuGet suivant à votre projet :

- **Nom du package :** sqlite-net-pcl
- **Author :** Frank A. Krueger
- **ID**  sqlite-net-pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Package NuGet de SQLite.NET](using-sqlite-orm-images/image1a-sml.png "package NuGet de SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Il existe un nombre de différents packages SQLite : veillez à choisir celui qui convient (il peut être le meilleur résultat dans la recherche).

Une fois que vous avez la bibliothèque de SQLite.NET disponible, suivez ces trois étapes pour l’utiliser pour accéder à une base de données :

1. **Ajoutez une instruction** -ajoutez l’instruction suivante à la C# fichiers où l’accès aux données est requise :

    ```csharp
    using SQLite;
    ```

1. **Créer une base de données vide** -une référence de base de données peut être créée en passant le chemin d’accès du fichier le constructeur de classe SQLiteConnection. Vous n’avez pas besoin vérifier si le fichier existe déjà : il est automatiquement créé si nécessaire, sinon le fichier de base de données existant est être ouverte.

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    La variable dbPath doit être déterminée selon les règles décrites précédemment dans ce document.

1. **Enregistrer les données** - une fois que vous avez créé un objet SQLiteConnection, base de données commandes sont exécutées en appelant ses méthodes, telles que CREATE TABLE et Insert comme suit :

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **Récupérer des données** : pour récupérer un objet (ou une liste d’objets) utilisez la syntaxe suivante :

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemple d’accès de base de données

Le *DataAccess_Basic* exemple de code pour ce document se présente comme suit lors de l’exécution sur iOS. Le code montre comment effectuer des opérations simples de SQLite.NET et montre les résultats sous forme de texte dans la fenêtre principale de l’application.

**iOS**

 [![exemple de SQLite.NET iOS](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

L’exemple de code suivant montre une interaction de base de données entière à l’aide de la bibliothèque de SQLite.NET pour encapsuler l’accès de base de données sous-jacente. Il montre :

1.  Création du fichier de base de données
1.  Insertion des données par la création d’objets, puis en enregistrant les
1.  Interrogation des données

Vous devrez inclure ces espaces de noms :

```csharp
using SQLite; // from the github SQLite.cs class
```

Cela nécessite que vous ayez ajouté SQLite à votre projet, mise en évidence [ici](#Usage). Notez que la table de base de données SQLite est définie en ajoutant des attributs à une classe (la `Stock` classe) au lieu d’une commande CREATE TABLE.

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

À l’aide de la `[Table]` attribut sans spécifier un paramètre de nom de table entraîne la table de base de données sous-jacente avoir le même nom que la classe (dans ce cas, il s’agit de « Stock »). Le nom de la table réelle est important si vous écrivez des requêtes SQL directement sur la base de données au lieu d’utilisez les méthodes d’accès de données ORM. De même la `[Column("_id")]` attribut est facultatif et si absent une colonne sera ajouté à la table portant le même nom que la propriété dans la classe.

## <a name="sqlite-attributes"></a>Attributs de SQLite

Attributs courants que vous pouvez appliquer à vos classes pour contrôler la façon dont elles sont stockées dans la base de données sous-jacent sont les suivantes :

-  **[PrimaryKey]**  : Cet attribut peut être appliqué à une propriété entière pour le forcer à être la clé primaire de la table sous-jacente. Clés primaires composites ne sont pas pris en charge.
-  **[AutoIncrement]**  – Cet attribut entraîne la valeur d’une propriété d’entier à incrémentation automatique pour chaque nouvel objet inséré dans la base de données
-  **[Column(name)]**  – En fournissant le paramètre facultatif `name` paramètre remplace la valeur par défaut du nom de la colonne de base de données sous-jacent (qui est identique à la propriété).
-  **[Table(name)]**  – Marque la classe comme étant en mesure d’être stockées dans une table sous-jacente de SQLite. Spécifiant le paramètre de nom facultatif remplace la valeur par défaut du nom de la table de base de données sous-jacent (qui est le même que le nom de classe).
-  **[MaxLength(value)]**  – Restreindre la longueur d’une propriété de texte, lors de la tentative d’insertion d’une base de données. Code de consommation, cela doit valider avant l’insertion de l’objet que cet attribut est uniquement « coché » lors de l’insertion d’une base de données ou l’opération de mise à jour est tentée.
-  **[Ignore]**  – Provoque de SQLite.NET pour ignorer cette propriété. Cela est particulièrement utile pour les propriétés qui ont un type qui ne peut pas être stocké dans la base de données ou des propriétés que les collections du modèle qui ne peut pas être résolues automatiquement être SQLite.
-  **[Unique]**  – Permet de s’assurer que les valeurs dans la colonne de base de données sous-jacente sont uniques.


La plupart de ces attributs est facultatif, SQLite utilisera les valeurs par défaut pour les noms de table et de colonne. Vous devez toujours spécifier une clé primaire de type entier afin que les requêtes de sélection et la suppression peuvent être effectuées efficacement sur vos données.

## <a name="more-complex-queries"></a>Requêtes plus complexes

Les méthodes suivantes sur `SQLiteConnection` peut être utilisé pour effectuer d’autres opérations de données :

-  **Insérer** – ajoute un nouvel objet à la base de données.
-  **Obtenir<T>**  – tente de récupérer un objet à l’aide de la clé primaire.
-  **Table<T>**  – renvoie tous les objets de la table.
-  **Supprimer** : supprime un objet à l’aide de sa clé primaire.
-  **Requête<T>**  -exécuter une requête SQL qui retourne un nombre de lignes (sous forme d’objets).
-  **Exécutez** – Utilisez cette méthode (et non `Query` ) lorsque vous ne pensez pas les lignes à partir de SQL (par exemple, les instructions INSERT, UPDATE et DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Obtention d’un objet à la clé primaire

SQLite.Net fournit la méthode Get pour récupérer un seul objet en fonction de sa clé primaire.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Sélection d’un objet à l’aide de Linq

Les méthodes qui retournent des collections prennent en charge IEnumerable<T> afin de pouvoir utiliser Linq pour interroger ou de trier le contenu d’une table. Le code suivant montre un exemple d’utilisation de Linq pour filtrer toutes les entrées qui commencent par la lettre « A » :

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Sélection d’un objet à l’aide de SQL

Bien que SQLite.Net puisse fournir basée sur l’objet d’accès à vos données, vous devrez parfois effectuer une requête plus complexe que Linq permet (ou vous devrez peut-être améliorer les performances). Vous pouvez utiliser des commandes SQL avec la méthode de requête, comme illustré ici :

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> Lors de l’écriture des instructions SQL directement, vous créez une dépendance sur les noms des tables et des colonnes dans votre base de données qui ont été générées à partir de vos classes et leurs attributs. Si vous modifiez ces noms dans votre code, vous devez penser à mettre à jour toutes les instructions SQL écrites manuellement.

### <a name="deleting-an-object"></a>Suppression d’un objet

La clé primaire est utilisée pour supprimer la ligne, comme illustré ici :

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Vous pouvez vérifier le `rowcount` pour confirmer le nombre de lignes affecté (dans ce cas supprimés).

## <a name="using-sqlitenet-with-multiple-threads"></a>Utilisation de SQLite.NET avec plusieurs Threads

SQLite prend en charge trois modes de threads : *Seul thread*, *multithread*, et *sérialisé*. Si vous souhaitez accéder à la base de données à partir de plusieurs threads sans aucune restriction, vous pouvez configurer SQLite à utiliser le **sérialisé** en mode de thread. Il est important de définir ce mode au début de votre application (par exemple, au début de la `OnCreate` méthode).

Pour modifier le mode de thread, appelez `SqliteConnection.SetConfig` qui se trouve dans le `Mono.Data.Sqlite` espace de noms. Par exemple, cette ligne de code configure SQLite pour **sérialisé** mode :

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
