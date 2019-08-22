---
title: Bases de données dans Xamarin. Mac
description: Cet article aborde l’utilisation du codage clé-valeur et de l’observation clé-valeur pour permettre la liaison de données entre les bases de données SQLite et les éléments d’interface utilisateur dans les Interface Builder de Xcode. Il couvre également l’utilisation de l’ORM SQLite.NET pour fournir l’accès aux données SQLite.
ms.prod: xamarin
ms.assetid: 44FAFDA8-612A-4E0F-8BB4-5C92A3F4D552
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 3e6bc829ec947f8f3c01c7ef9679a5feadf4a42a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887373"
---
# <a name="databases-in-xamarinmac"></a>Bases de données dans Xamarin. Mac

_Cet article aborde l’utilisation du codage clé-valeur et de l’observation clé-valeur pour permettre la liaison de données entre les bases de données SQLite et les éléments d’interface utilisateur dans les Interface Builder de Xcode. Il couvre également l’utilisation de l’ORM SQLite.NET pour fournir l’accès aux données SQLite._

## <a name="overview"></a>Vue d'ensemble

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes bases de données SQLite auxquelles une application Xamarin. iOS ou Xamarin. Android peut accéder.

Dans cet article, nous allons aborder deux façons d’accéder aux données SQLite:

1. **Accès direct** : en accédant directement à une base de données SQLite, nous pouvons utiliser les données de la base de données pour le codage et la liaison de données clé-valeur avec les éléments d’interface utilisateur créés dans le Interface Builder de Xcode. En utilisant le codage clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Vous avez également l’avantage de dissocier vos données de stockage (modèle de_données_) de votre interface utilisateur frontale (_Model-View-Controller_), ce qui vous permet de gérer plus facilement la conception d’applications plus souple.
2. **SQLite.net ORM** : en utilisant le gestionnaire de relations d’objets open source [SQLite.net](http://www.sqlite.org) , nous pouvons réduire la quantité de code nécessaire pour lire et écrire des données à partir d’une base de données SQLite. Ces données peuvent ensuite être utilisées pour remplir un élément d’interface utilisateur tel qu’une vue de table.

[![Exemple d’application en cours d’exécution](databases-images/intro01.png "Exemple d’application en cours d’exécution")](databases-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation du codage clé-valeur et de la liaison de données avec les bases de données SQLite dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.

Étant donné que nous allons utiliser le codage de clé-valeur et la liaison de données, vous devez d’abord utiliser la [liaison de données et le codage de clé-valeur](~/mac/app-fundamentals/databinding.md) , car les techniques et les concepts de base seront couverts et seront utilisés dans cette documentation et dans son exemple d’application.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md) du document [Internals Xamarin. Mac.](~/mac/internals/how-it-works.md) elle explique également les `Register` attributs `Export` et utilisés pour associer vos C# classes à Objets objective-C et éléments d’interface utilisateur.

## <a name="direct-sqlite-access"></a>Accès direct SQLite

Pour les données SQLite qui vont être liées aux éléments d’interface utilisateur dans le Interface Builder de Xcode, il est fortement recommandé d’accéder directement à la base de données SQLite (par opposition à l’utilisation d’une technique telle qu’un ORM), car vous avez un contrôle total sur la façon dont les données sont écrites et lues.  à partir de la base de données.

Comme nous l’avons vu dans la documentation relative à la [liaison de données et au codage clé-valeur](~/mac/app-fundamentals/databinding.md) , en utilisant le codage de clé-valeur et les techniques de liaison de données dans votre application Xamarin. Mac, vous pouvez réduire la quantité de code que vous devez écrire et maintenir pour remplir et utiliser des éléments d’interface utilisateur. Lorsqu’il est associé à un accès direct à une base de données SQLite, il peut également réduire la quantité de code nécessaire pour lire et écrire des données dans cette base de données.

Dans cet article, nous allons modifier l’exemple d’application à partir de la liaison de données et du document de codage clé-valeur pour utiliser une base de données SQLite comme source de stockage de la liaison.

### <a name="including-sqlite-database-support"></a>Intégration de la prise en charge des bases de données SQLite

Avant de pouvoir continuer, nous devons ajouter la prise en charge de la base de données SQLite à notre application en incluant des références à quelques-uns. Fichiers dll.

Effectuez ce qui suit :

1. Dans le **panneau solutions**, cliquez avec le bouton droit sur le dossier **références** et sélectionnez **modifier les références**.
2. Sélectionnez les assemblys **mono. Data. sqlite** et **System. Data** : 

    [![Ajout des références requises](databases-images/reference01.png "Ajout des références requises")](databases-images/reference01-large.png#lightbox)
3. Cliquez sur le bouton **OK** pour enregistrer vos modifications et ajouter les références.

### <a name="modifying-the-data-model"></a>Modification du modèle de données

Maintenant que nous avons ajouté la prise en charge de l’accès direct à une base de données SQLite à notre application, nous devons modifier notre objet de modèle de données pour lire et écrire des données à partir de la base de données (et fournir une liaison de données et un codage clé-valeur). Dans le cas de notre exemple d’application, nous allons modifier la classe **PersonModel.cs** et lui donner l’aspect suivant:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _ID = "";
        private string _managerID = "";
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        private SqliteConnection _conn = null;
        #endregion

        #region Computed Properties
        public SqliteConnection Conn {
            get { return _conn; }
            set { _conn = value; }
        }

        [Export("ID")]
        public string ID {
            get { return _ID; }
            set {
                WillChangeValue ("ID");
                _ID = value;
                DidChangeValue ("ID");
            }
        }

        [Export("ManagerID")]
        public string ManagerID {
            get { return _managerID; }
            set {
                WillChangeValue ("ManagerID");
                _managerID = value;
                DidChangeValue ("ManagerID");
            }
        }

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }

        public PersonModel (string id, string name, string occupation)
        {
            // Initialize
            this.ID = id;
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (SqliteConnection conn, string id)
        {
            // Load from database
            Load (conn, id);
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion

        #region SQLite Routines
        public void Create(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Create new record ID?
            if (ID == "") {
                ID = Guid.NewGuid ().ToString();
            }

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);
                command.Parameters.AddWithValue ("@COL2", Name);
                command.Parameters.AddWithValue ("@COL3", Occupation);
                command.Parameters.AddWithValue ("@COL4", isManager);
                command.Parameters.AddWithValue ("@COL5", ManagerID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Save children to database as well
            for (nuint n = 0; n < People.Count; ++n) {
                // Grab person
                var Person = People.GetItem<PersonModel>(n);

                // Save manager ID and create the sub record
                Person.ManagerID = ID;
                Person.Create (conn);
            }

            // Save last connection
            _conn = conn;
        }

        public void Update(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);
                command.Parameters.AddWithValue ("@COL2", Name);
                command.Parameters.AddWithValue ("@COL3", Occupation);
                command.Parameters.AddWithValue ("@COL4", isManager);
                command.Parameters.AddWithValue ("@COL5", ManagerID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Save children to database as well
            for (nuint n = 0; n < People.Count; ++n) {
                // Grab person
                var Person = People.GetItem<PersonModel>(n);

                // Update sub record
                Person.Update (conn);
            }

            // Save last connection
            _conn = conn;
        }

        public void Load(SqliteConnection conn, string id) {
            bool shouldClose = false;

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Is the database already open?
            if (conn.State != ConnectionState.Open) {
                shouldClose = true;
                conn.Open ();
            }

            // Execute query
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "SELECT * FROM [People] WHERE ID = @COL1";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", id);

                using (var reader = command.ExecuteReader ()) {
                    while (reader.Read ()) {
                        // Pull values back into class
                        ID = (string)reader [0];
                        Name = (string)reader [1];
                        Occupation = (string)reader [2];
                        isManager = (bool)reader [3];
                        ManagerID = (string)reader [4];
                    }
                }
            }

            // Is this a manager?
            if (isManager) {
                // Yes, load children
                using (var command = conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

                    // Populate with data from the record
                    command.Parameters.AddWithValue ("@COL1", id);

                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Load child and add to collection
                            var childID = (string)reader [0];
                            var person = new PersonModel (conn, childID);
                            _people.Add (person);
                        }
                    }
                }
            }

            // Should we close the connection to the database
            if (shouldClose) {
                conn.Close ();
            }

            // Save last connection
            _conn = conn;
        }

        public void Delete(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Empty class
            ID = "";
            ManagerID = "";
            Name = "";
            Occupation = "";
            isManager = false;
            _people = new NSMutableArray();

            // Save last connection
            _conn = conn;
        }
        #endregion
    }
}
```

Examinons les modifications en détail ci-dessous.

Tout d’abord, nous avons ajouté plusieurs instructions using nécessaires à l’utilisation de SQLite et nous avons ajouté une variable pour enregistrer notre dernière connexion à la base de données SQLite:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection _conn = null;
```

Nous allons utiliser cette connexion enregistrée pour enregistrer automatiquement toutes les modifications apportées à l’enregistrement dans la base de données lorsque l’utilisateur modifie le contenu de l’interface utilisateur via la liaison de données:

```csharp
[Export("Name")]
public string Name {
    get { return _name; }
    set {
        WillChangeValue ("Name");
        _name = value;
        DidChangeValue ("Name");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}

[Export("Occupation")]
public string Occupation {
    get { return _occupation; }
    set {
        WillChangeValue ("Occupation");
        _occupation = value;
        DidChangeValue ("Occupation");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}

[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}
```

Toutes les modifications apportées aux propriétés **Name**, **occupation** ou **isManager** sont envoyées à la base de données si celles-ci ont été enregistrées précédemment (par `_conn` exemple, si `null`la variable n’est pas). Examinons ensuite les méthodes que nous avons ajoutées pour **créer**, **mettre à jour**, **charger** et **supprimer** des personnes dans la base de données.

#### <a name="create-a-new-record"></a>Créer un nouvel enregistrement

Le code suivant a été ajouté pour créer un nouvel enregistrement dans la base de données SQLite:

```csharp
public void Create(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Create new record ID?
    if (ID == "") {
        ID = Guid.NewGuid ().ToString();
    }

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);
        command.Parameters.AddWithValue ("@COL2", Name);
        command.Parameters.AddWithValue ("@COL3", Occupation);
        command.Parameters.AddWithValue ("@COL4", isManager);
        command.Parameters.AddWithValue ("@COL5", ManagerID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Save children to database as well
    for (nuint n = 0; n < People.Count; ++n) {
        // Grab person
        var Person = People.GetItem<PersonModel>(n);

        // Save manager ID and create the sub record
        Person.ManagerID = ID;
        Person.Create (conn);
    }

    // Save last connection
    _conn = conn;
}
```

Nous utilisons un `SQLiteCommand` pour créer le nouvel enregistrement dans la base de données. Nous obtenons une nouvelle commande à `SQLiteConnection` partir du (conn) que nous avons passé dans la `CreateCommand`méthode en appelant. Ensuite, nous définissons l’instruction SQL pour écrire réellement le nouvel enregistrement, en fournissant des paramètres pour les valeurs réelles:

```csharp
command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";
```

Plus tard, nous définissons les valeurs des paramètres `Parameters.AddWithValue` à l’aide `SQLiteCommand`de la méthode sur le. En utilisant des paramètres, nous garantissons que les valeurs (par exemple, un guillemet simple) sont correctement encodées avant d’être envoyées à SQLite. Exemple :

```csharp
command.Parameters.AddWithValue ("@COL1", ID);
```

Enfin, dans la mesure où une personne peut être responsable et avoir une collection d’employés, nous appelons de manière récursive la `Create` méthode sur ces personnes pour les enregistrer également dans la base de données:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Save manager ID and create the sub record
    Person.ManagerID = ID;
    Person.Create (conn);
}
```

#### <a name="updating-a-record"></a>Mise à jour d’un enregistrement

Le code suivant a été ajouté pour mettre à jour un enregistrement existant dans la base de données SQLite:

```csharp
public void Update(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);
        command.Parameters.AddWithValue ("@COL2", Name);
        command.Parameters.AddWithValue ("@COL3", Occupation);
        command.Parameters.AddWithValue ("@COL4", isManager);
        command.Parameters.AddWithValue ("@COL5", ManagerID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Save children to database as well
    for (nuint n = 0; n < People.Count; ++n) {
        // Grab person
        var Person = People.GetItem<PersonModel>(n);

        // Update sub record
        Person.Update (conn);
    }

    // Save last connection
    _conn = conn;
}
```

Comme pour Create ci-dessus `SQLiteCommand` , nous obtenons `SQLiteConnection`un à partir du passé et définissons notre SQL pour mettre à jour notre enregistrement (en fournissant des paramètres):

```csharp
command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";
```

Nous remplissons les valeurs des paramètres (exemple `command.Parameters.AddWithValue ("@COL1", ID);`:) et de nouveau, appelons de nouveau la mise à jour de manière récursive sur tous les enregistrements enfants:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Update sub record
    Person.Update (conn);
}
```

#### <a name="loading-a-record"></a>Chargement d’un enregistrement

Le code suivant a été ajouté pour charger un enregistrement existant à partir de la base de données SQLite:

```csharp
public void Load(SqliteConnection conn, string id) {
    bool shouldClose = false;

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Is the database already open?
    if (conn.State != ConnectionState.Open) {
        shouldClose = true;
        conn.Open ();
    }

    // Execute query
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "SELECT * FROM [People] WHERE ID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", id);

        using (var reader = command.ExecuteReader ()) {
            while (reader.Read ()) {
                // Pull values back into class
                ID = (string)reader [0];
                Name = (string)reader [1];
                Occupation = (string)reader [2];
                isManager = (bool)reader [3];
                ManagerID = (string)reader [4];
            }
        }
    }

    // Is this a manager?
    if (isManager) {
        // Yes, load children
        using (var command = conn.CreateCommand ()) {
            // Create new command
            command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

            // Populate with data from the record
            command.Parameters.AddWithValue ("@COL1", id);

            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Load child and add to collection
                    var childID = (string)reader [0];
                    var person = new PersonModel (conn, childID);
                    _people.Add (person);
                }
            }
        }
    }

    // Should we close the connection to the database
    if (shouldClose) {
        conn.Close ();
    }

    // Save last connection
    _conn = conn;
}
```

Étant donné que la routine peut être appelée de manière récursive à partir d’un objet parent (tel qu’un objet gestionnaire chargeant son objet Employees), du code spécial a été ajouté pour gérer l’ouverture et la fermeture de la connexion à la base de données:

```csharp
bool shouldClose = false;
...

// Is the database already open?
if (conn.State != ConnectionState.Open) {
    shouldClose = true;
    conn.Open ();
}
...

// Should we close the connection to the database
if (shouldClose) {
    conn.Close ();
}

```

Comme toujours, nous avons défini notre SQL pour récupérer l’enregistrement et utiliser les paramètres:

```csharp
// Create new command
command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", id);
```

Enfin, nous utilisons un lecteur de données pour exécuter la requête et retourner les champs d’enregistrement (que nous copions dans l' `PersonModel` instance de la classe):

```csharp
using (var reader = command.ExecuteReader ()) {
    while (reader.Read ()) {
        // Pull values back into class
        ID = (string)reader [0];
        Name = (string)reader [1];
        Occupation = (string)reader [2];
        isManager = (bool)reader [3];
        ManagerID = (string)reader [4];
    }
}
```

Si cette personne est responsable, nous devons également charger tous ses employés (encore une fois, en appelant de manière récursive leur `Load` méthode):

```csharp
// Is this a manager?
if (isManager) {
    // Yes, load children
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", id);

        using (var reader = command.ExecuteReader ()) {
            while (reader.Read ()) {
                // Load child and add to collection
                var childID = (string)reader [0];
                var person = new PersonModel (conn, childID);
                _people.Add (person);
            }
        }
    }
}
```

#### <a name="deleting-a-record"></a>Suppression d’un enregistrement

Le code suivant a été ajouté pour supprimer un enregistrement existant de la base de données SQLite:

```csharp
public void Delete(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Empty class
    ID = "";
    ManagerID = "";
    Name = "";
    Occupation = "";
    isManager = false;
    _people = new NSMutableArray();

    // Save last connection
    _conn = conn;
}
```

Ici, nous fournissons le SQL pour supprimer l’enregistrement des responsables et les enregistrements des employés sous ce gestionnaire (à l’aide des paramètres):

```csharp
// Create new command
command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", ID);
```

Une fois l’enregistrement supprimé, nous effacons l’instance actuelle de la `PersonModel` classe:

```csharp
// Empty class
ID = "";
ManagerID = "";
Name = "";
Occupation = "";
isManager = false;
_people = new NSMutableArray();
```

### <a name="initializing-the-database"></a>Initialisation de la base de données

Avec les modifications apportées à notre modèle de données en place pour prendre en charge la lecture et l’écriture dans la base de données, nous devons ouvrir une connexion à la base de données et l’initialiser lors de la première exécution. Nous allons ajouter le code suivant à notre fichier **MainWindow.cs** :

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection DatabaseConnection = null;
...

private SqliteConnection GetDatabaseConnection() {
    var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
    string db = Path.Combine (documents, "People.db3");

    // Create the database if it doesn't already exist
    bool exists = File.Exists (db);
    if (!exists)
        SqliteConnection.CreateFile (db);

    // Create connection to the database
    var conn = new SqliteConnection("Data Source=" + db);

    // Set the structure of the database
    if (!exists) {
        var commands = new[] {
            "CREATE TABLE People (ID TEXT, Name TEXT, Occupation TEXT, isManager BOOLEAN, ManagerID TEXT)"
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

        // Build list of employees
        var Craig = new PersonModel ("0","Craig Dunn", "Documentation Manager");
        Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
        Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
        Craig.Create (conn);

        var Larry = new PersonModel ("1","Larry O'Brien", "API Documentation Manager");
        Larry.AddPerson (new PersonModel ("Mike Norman", "API Documentor"));
        Larry.Create (conn);
    }

    // Return new connection
    return conn;
}
```

Examinons plus en détail le code ci-dessus. Tout d’abord, nous choisissons un emplacement pour la nouvelle base de données (dans cet exemple, le Bureau de l’utilisateur), nous cherchons à déterminer si la base de données existe et, si ce n’est pas le cas, je la crée:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "People.db3");

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
if (!exists)
    SqliteConnection.CreateFile (db);
```

Ensuite, nous établissons la connexion à la base de données à l’aide du chemin d’accès que nous avons créé ci-dessus:

```csharp
var conn = new SqliteConnection("Data Source=" + db);
```

Ensuite, nous créons toutes les tables SQL dans la base de données dont nous avons besoin:

```csharp
var commands = new[] {
    "CREATE TABLE People (ID TEXT, Name TEXT, Occupation TEXT, isManager BOOLEAN, ManagerID TEXT)"
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
```

Enfin, nous utilisons notre modèle de données`PersonModel`() pour créer un ensemble d’enregistrements par défaut pour la base de données la première fois que l’application est exécutée ou si la base de données est manquante:

```csharp
// Build list of employees
var Craig = new PersonModel ("0","Craig Dunn", "Documentation Manager");
Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
Craig.Create (conn);

var Larry = new PersonModel ("1","Larry O'Brien", "API Documentation Manager");
Larry.AddPerson (new PersonModel ("Mike Norman", "API Documentor"));
Larry.Create (conn);
``` 

Lorsque l’application démarre et ouvre la fenêtre principale, nous établissons une connexion à la base de données à l’aide du code que nous avons ajouté précédemment:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get access to database
    DatabaseConnection = GetDatabaseConnection ();
}
```

### <a name="loading-bound-data"></a>Chargement des données liées

Avec tous les composants permettant d’accéder directement aux données liées à partir d’une base de données SQLite en place, nous pouvons charger les données dans les différentes vues fournies par notre application et elles seront automatiquement affichées dans l’interface utilisateur.

#### <a name="loading-a-single-record"></a>Chargement d’un seul enregistrement

Pour charger un seul enregistrement dans lequel l’ID est connu, nous pouvons utiliser le code suivant:

```csharp
Person = new PersonModel (Conn, "0");
```

#### <a name="loading-all-records"></a>Chargement de tous les enregistrements

Pour charger toutes les personnes, qu’il s’agit ou non d’un responsable, utilisez le code suivant:

```csharp
// Load all employees
_conn.Open ();
using (var command = _conn.CreateCommand ()) {
    // Create new command
    command.CommandText = "SELECT ID FROM [People]";

    using (var reader = command.ExecuteReader ()) {
        while (reader.Read ()) {
            // Load child and add to collection
            var childID = (string)reader [0];
            var person = new PersonModel (_conn, childID);
            AddPerson (person);
        }
    }
}
_conn.Close ();

```

Ici, nous utilisons une surcharge du constructeur pour la `PersonModel` classe pour charger la personne en mémoire:

```csharp
var person = new PersonModel (_conn, childID);
```

Nous appelons également la classe liée aux données pour ajouter la personne à notre collection de personnes `AddPerson (person)`. cela garantit que notre interface utilisateur reconnaît la modification et l’affiche:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}
```

#### <a name="loading-top-level-records-only"></a>Chargement des enregistrements de niveau supérieur uniquement

Pour charger uniquement les gestionnaires (par exemple, pour afficher les données dans un mode plan), nous utilisons le code suivant:

```csharp
// Load only managers employees
_conn.Open ();
using (var command = _conn.CreateCommand ()) {
    // Create new command
    command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1";

    using (var reader = command.ExecuteReader ()) {
        while (reader.Read ()) {
            // Load child and add to collection
            var childID = (string)reader [0];
            var person = new PersonModel (_conn, childID);
            AddPerson (person);
        }
    }
}
_conn.Close ();
```

La seule différence réelle dans l’instruction SQL (qui charge uniquement les gestionnaires `command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1"`), mais fonctionne de la même façon que la section ci-dessus.

<a name="Databases-and-ComboBoxes" />

### <a name="databases-and-comboboxes"></a>Bases de données et comboboxs

Les contrôles de menu disponibles pour macOS (tels que la zone de liste modifiable) peuvent être configurés pour remplir la liste déroulante à partir d’une liste interne (qui peut être prédéfinie dans Interface Builder ou renseignée via du code) ou en fournissant votre propre source de données externe personnalisée. Pour plus d’informations, consultez [fourniture de données de contrôle de menu](~/mac/user-interface/standard-controls.md#Providing-Menu-Control-Data) .

Par exemple, modifiez l’exemple de liaison simple ci-dessus dans Interface Builder, ajoutez une zone de liste déroulante et `EmployeeSelector`exposez-la à l’aide d’une prise nommée:

[![Exposition d’une prise de zone de liste] déroulante (databases-images/combo01.png "Exposition d’une prise de zone de liste") déroulante](databases-images/combo01-large.png#lightbox)

Dans l' **inspecteur d’attributs**, cochez la case **saisie semi-automatique** et utiliser les propriétés de la **source de données** :

![Configuration des attributs de zone de liste] déroulante (databases-images/combo02.png "Configuration des attributs de zone de liste") déroulante

Enregistrez vos modifications et revenez à Visual Studio pour Mac à synchroniser.

#### <a name="providing-combobox-data"></a>Fourniture de données de zone de liste modifiable

Ensuite, ajoutez une nouvelle classe au projet appelé `ComboBoxDataSource` et faites-lui ressembler à ceci:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    public class ComboBoxDataSource : NSComboBoxDataSource
    {
        #region Private Variables
        private SqliteConnection _conn = null;
        private string _tableName = "";
        private string _IDField = "ID";
        private string _displayField = "";
        private nint _recordCount = 0;
        #endregion

        #region Computed Properties
        public SqliteConnection Conn {
            get { return _conn; }
            set { _conn = value; }
        }

        public string TableName {
            get { return _tableName; }
            set { 
                _tableName = value;
                _recordCount = GetRecordCount ();
            }
        }

        public string IDField {
            get { return _IDField; }
            set {
                _IDField = value; 
                _recordCount = GetRecordCount ();
            }
        }

        public string DisplayField {
            get { return _displayField; }
            set { 
                _displayField = value; 
                _recordCount = GetRecordCount ();
            }
        }

        public nint RecordCount {
            get { return _recordCount; }
        }
        #endregion

        #region Constructors
        public ComboBoxDataSource (SqliteConnection conn, string tableName, string displayField)
        {
            // Initialize
            this.Conn = conn;
            this.TableName = tableName;
            this.DisplayField = displayField;
        }

        public ComboBoxDataSource (SqliteConnection conn, string tableName, string idField, string displayField)
        {
            // Initialize
            this.Conn = conn;
            this.TableName = tableName;
            this.IDField = idField;
            this.DisplayField = displayField;
        }
        #endregion

        #region Private Methods
        private nint GetRecordCount ()
        {
            bool shouldClose = false;
            nint count = 0;

            // Has a Table, ID and display field been specified?
            if (TableName !="" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT count({IDField}) FROM [{TableName}]";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read count from query
                            var result = (long)reader [0];
                            count = (nint)result;
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return the number of records
            return count;
        }
        #endregion

        #region Public Methods
        public string IDForIndex (nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {IDField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public string ValueForIndex (nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public string IDForValue (string value)
        {
            NSString result = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {IDField} FROM [{TableName}] WHERE {DisplayField} = @VAL";

                    // Populate parameters
                    command.Parameters.AddWithValue ("@VAL", value);

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            result = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return result;
        }
        #endregion 

        #region Override Methods
        public override nint ItemCount (NSComboBox comboBox)
        {
            return RecordCount;
        }

        public override NSObject ObjectValueForItem (NSComboBox comboBox, nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public override nint IndexOfItem (NSComboBox comboBox, string value)
        {
            bool shouldClose = false;
            bool found = false;
            string field = "";
            nint index = NSRange.NotFound;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read () && !found) {
                            // Read the display field from the query
                            field = (string)reader [0];
                            ++index;

                            // Is this the value we are searching for?
                            if (value == field) {
                                // Yes, exit loop
                                found = true;
                            }
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return index;
        }

        public override string CompletedString (NSComboBox comboBox, string uncompletedString)
        {
            bool shouldClose = false;
            bool found = false;
            string field = "";

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Escape search string
                uncompletedString = uncompletedString.Replace ("'", "");

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] WHERE {DisplayField} LIKE @VAL";

                    // Populate parameters
                    command.Parameters.AddWithValue ("@VAL", uncompletedString + "%");

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            field = (string)reader [0];
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return field;
        }
        #endregion
    }
}
```

Dans cet exemple, nous créons un nouveau `NSComboBoxDataSource` qui peut présenter des éléments de zone de liste déroulante à partir de n’importe quelle source de données SQLite. Tout d’abord, nous définissons les propriétés suivantes:

- `Conn`-Obtient ou définit une connexion à la base de données SQLite.
- `TableName`-Obtient ou définit le nom de la table.
- `IDField`-Obtient ou définit le champ qui fournit l’ID unique pour la table donnée. La valeur par défaut est `ID`.
- `DisplayField`-Obtient ou définit le champ affiché dans la liste déroulante.
- `RecordCount`-Obtient le nombre d’enregistrements dans la table donnée.

Lorsque nous créons une nouvelle instance de l’objet, nous passons la connexion, le nom de la table, éventuellement le champ ID et le champ d’affichage:

```csharp
public ComboBoxDataSource (SqliteConnection conn, string tableName, string displayField)
{
    // Initialize
    this.Conn = conn;
    this.TableName = tableName;
    this.DisplayField = displayField;
}
```

La `GetRecordCount` méthode retourne le nombre d’enregistrements dans la table donnée:

```csharp
private nint GetRecordCount ()
{
    bool shouldClose = false;
    nint count = 0;

    // Has a Table, ID and display field been specified?
    if (TableName !="" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT count({IDField}) FROM [{TableName}]";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read count from query
                    var result = (long)reader [0];
                    count = (nint)result;
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return the number of records
    return count;
}
```

Elle est appelée chaque fois que `TableName`la `IDField` valeur `DisplayField` de propriété, ou est modifiée.

La `IDForIndex` méthode retourne l’ID unique (`IDField`) de l’enregistrement à l’index de l’élément de liste déroulante donné: 

```csharp
public string IDForIndex (nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {IDField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

La `ValueForIndex` méthode retourne la valeur (`DisplayField`) pour l’élément à l’index de liste déroulante donné:

```csharp
public string ValueForIndex (nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

La `IDForValue` méthode retourne l’ID unique (`IDField`) de la valeur donnée (`DisplayField`):

```csharp
public string IDForValue (string value)
{
    NSString result = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {IDField} FROM [{TableName}] WHERE {DisplayField} = @VAL";

            // Populate parameters
            command.Parameters.AddWithValue ("@VAL", value);

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    result = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return result;
}
```

Le `ItemCount` retourne le nombre d’éléments précalculés dans la liste, tel qu’il `TableName`est calculé `DisplayField` lorsque les propriétés, `IDField` ou sont modifiées:

```csharp
public override nint ItemCount (NSComboBox comboBox)
{
    return RecordCount;
}
```

La `ObjectValueForItem` méthode fournit la valeur (`DisplayField`) pour l’index de l’élément de liste déroulante donné:

```csharp
public override NSObject ObjectValueForItem (NSComboBox comboBox, nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

Notez que nous utilisons les `LIMIT` instructions et `OFFSET` dans notre commande SQLite pour limiter à un enregistrement dont nous avons besoin.

La `IndexOfItem` méthode retourne l’index de l’élément de liste`DisplayField`déroulante de la valeur () donnée:

```csharp
public override nint IndexOfItem (NSComboBox comboBox, string value)
{
    bool shouldClose = false;
    bool found = false;
    string field = "";
    nint index = NSRange.NotFound;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read () && !found) {
                    // Read the display field from the query
                    field = (string)reader [0];
                    ++index;

                    // Is this the value we are searching for?
                    if (value == field) {
                        // Yes, exit loop
                        found = true;
                    }
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return index;
}
```

Si la valeur est introuvable, `NSRange.NotFound` la valeur est retournée et tous les éléments sont désélectionnés dans la liste déroulante.

La `CompletedString` méthode retourne la première valeur correspondante (`DisplayField`) pour une entrée partiellement typée:

```csharp
public override string CompletedString (NSComboBox comboBox, string uncompletedString)
{
    bool shouldClose = false;
    bool found = false;
    string field = "";

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Escape search string
        uncompletedString = uncompletedString.Replace ("'", "");

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] WHERE {DisplayField} LIKE @VAL";

            // Populate parameters
            command.Parameters.AddWithValue ("@VAL", uncompletedString + "%");

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    field = (string)reader [0];
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return field;
}
```

#### <a name="displaying-data-and-responding-to-events"></a>Affichage des données et réponse aux événements

Pour regrouper tous les éléments, modifiez le `SubviewSimpleBindingController` et faites-le ressembler à ce qui suit:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    public partial class SubviewSimpleBindingController : AppKit.NSViewController
    {
        #region Private Variables
        private PersonModel _person = new PersonModel();
        private SqliteConnection Conn;
        #endregion

        #region Computed Properties
        //strongly typed view accessor
        public new SubviewSimpleBinding View {
            get {
                return (SubviewSimpleBinding)base.View;
            }
        }

        [Export("Person")]
        public PersonModel Person {
            get {return _person; }
            set {
                WillChangeValue ("Person");
                _person = value;
                DidChangeValue ("Person");
            }
        }

        public ComboBoxDataSource DataSource {
            get { return EmployeeSelector.DataSource as ComboBoxDataSource; }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public SubviewSimpleBindingController (IntPtr handle) : base (handle)
        {
            Initialize ();
        }

        // Called when created directly from a XIB file
        [Export ("initWithCoder:")]
        public SubviewSimpleBindingController (NSCoder coder) : base (coder)
        {
            Initialize ();
        }

        // Call to load from the XIB/NIB file
        public SubviewSimpleBindingController (SqliteConnection conn) : base ("SubviewSimpleBinding", NSBundle.MainBundle)
        {
            // Initialize
            this.Conn = conn;
            Initialize ();
        }

        // Shared initialization code
        void Initialize ()
        {
        }
        #endregion

        #region Private Methods
        private void LoadSelectedPerson (string id)
        {

            // Found?
            if (id != "") {
                // Yes, load requested record
                Person = new PersonModel (Conn, id);
            }
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Configure Employee selector dropdown
            EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");

            // Wireup events
            EmployeeSelector.Changed += (sender, e) => {
                // Get ID
                var id = DataSource.IDForValue (EmployeeSelector.StringValue);
                LoadSelectedPerson (id);
            };

            EmployeeSelector.SelectionChanged += (sender, e) => {
                // Get ID
                var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
                LoadSelectedPerson (id);
            };

            // Auto select the first person
            EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
            Person = new PersonModel (Conn, DataSource.IDForIndex(0));
    
        }
        #endregion
    }
}
```

La `DataSource` propriété fournit un raccourci vers le `ComboBoxDataSource` (créé ci-dessus) attaché à la zone de liste déroulante.

La `LoadSelectedPerson` méthode charge la personne à partir de la base de données pour l’ID unique donné:

```csharp
private void LoadSelectedPerson (string id)
{

    // Found?
    if (id != "") {
        // Yes, load requested record
        Person = new PersonModel (Conn, id);
    }
}
```

Dans la `AwakeFromNib` substitution de méthode, nous attachons tout d’abord une instance de notre source de données de zone de liste déroulante personnalisée:

```csharp
EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");
```

Ensuite, nous répondons à l’utilisateur qui modifie la valeur texte de la zone de liste déroulante en`IDField`recherchant l’ID unique associé () des données présentant et chargeant la personne donnée si elle est trouvée:

```csharp
EmployeeSelector.Changed += (sender, e) => {
    // Get ID
    var id = DataSource.IDForValue (EmployeeSelector.StringValue);
    LoadSelectedPerson (id);
};
```

Nous chargeons également une nouvelle personne si l’utilisateur sélectionne un nouvel élément dans la liste déroulante:

```csharp
EmployeeSelector.SelectionChanged += (sender, e) => {
    // Get ID
    var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
    LoadSelectedPerson (id);
};
```

Enfin, nous remplissons automatiquement la zone de liste déroulante et la personne affichée avec le premier élément de la liste:

```csharp
// Auto select the first person
EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
Person = new PersonModel (Conn, DataSource.IDForIndex(0));
```

## <a name="sqlitenet-orm"></a>SQLite.NET ORM

Comme indiqué ci-dessus, en utilisant le gestionnaire de relations d’objets [SQLite.net](http://www.sqlite.org) Open source, nous pouvons réduire la quantité de code nécessaire pour lire et écrire des données à partir d’une base de données SQLite. Cela peut ne pas être le meilleur itinéraire à suivre lors de la liaison de données en raison de plusieurs exigences liées au codage de la valeur clé et à la liaison de données sur un objet.

D’après le site Web sqlite.NET _, «SQLite est une bibliothèque de logiciels qui implémente un moteur de base de données SQL transactionnel autonome, sans serveur, sans serveur de configuration. SQLite est le moteur de base de données le plus largement déployé dans le monde. Le code source de SQLite se trouve dans le domaine public.»_

Dans les sections suivantes, nous allons montrer comment utiliser SQLite.Net pour fournir des données pour une vue de table.

### <a name="including-the-sqlitenet-nuget"></a>Inclure le NuGet SQLite.net

SQLite.NET est présenté sous la forme d’un package NuGet que vous incluez dans votre application. Avant de pouvoir ajouter la prise en charge de base de données à l’aide de SQLite.NET, nous devons inclure ce package.

Procédez comme suit pour ajouter le package:

1. Dans le **panneau solutions**, cliquez avec le bouton droit sur le dossier **packages** , puis sélectionnez **Ajouter des packages...**
2. Entrez `SQLite.net` dans la **zone de recherche** et sélectionnez l’entrée **SQLite-net** :

    [![Ajout du package NuGet SQLite](databases-images/nuget01.png "Ajout du package NuGet SQLite")](databases-images/nuget01-large.png#lightbox)
3. Cliquez sur le bouton **Ajouter un package** pour terminer.

### <a name="creating-the-data-model"></a>Création du modèle de données

Nous allons ajouter une nouvelle classe au projet et appeler `OccupationModel`. Ensuite, nous allons modifier le fichier **OccupationModel.cs** et le faire ressembler à ce qui suit:

```csharp
using System;
using SQLite;

namespace MacDatabase
{
    public class OccupationModel
    {
        #region Computed Properties
        [PrimaryKey, AutoIncrement]
        public int ID { get; set; }

        public string Name { get; set;}
        public string Description { get; set;}
        #endregion

        #region Constructors
        public OccupationModel ()
        {
        }

        public OccupationModel (string name, string description)
        {

            // Initialize
            this.Name = name;
            this.Description = description;

        }
        #endregion
    }
}
```

Tout d’abord, nous incluons sqlite.net (`using Sqlite`), puis nous exposons plusieurs propriétés qui seront écrites dans la base de données lors de l’enregistrement de cet enregistrement. La première propriété que nous créons comme clé primaire et la définit comme suit:

```csharp
[PrimaryKey, AutoIncrement]
public int ID { get; set; }
```

### <a name="initializing-the-database"></a>Initialisation de la base de données

Avec les modifications apportées à notre modèle de données en place pour prendre en charge la lecture et l’écriture dans la base de données, nous devons ouvrir une connexion à la base de données et l’initialiser lors de la première exécution. Nous allons ajouter le code suivant:

```csharp
using SQLite;
...

public SQLiteConnection Conn { get; set; }
...

private SQLiteConnection GetDatabaseConnection() {
    var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
    string db = Path.Combine (documents, "Occupation.db3");
    OccupationModel Occupation;

    // Create the database if it doesn't already exist
    bool exists = File.Exists (db);

    // Create connection to database
    var conn = new SQLiteConnection (db);

    // Initially populate table?
    if (!exists) {
        // Yes, build table
        conn.CreateTable<OccupationModel> ();

        // Add occupations
        Occupation = new OccupationModel ("Documentation Manager", "Manages the Documentation Group");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("Technical Writer", "Writes technical documentation and sample applications");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("Web & Infrastructure", "Creates and maintains the websites that drive documentation");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("API Documentation Manager", "Manages the API Documentation Group");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("API Documenter", "Creates and maintains API documentation");
        conn.Insert (Occupation);
    }

    return conn;
}
```

Tout d’abord, nous obtenons un chemin d’accès à la base de données (le Bureau de l’utilisateur dans ce cas) et voyons si la base de données existe déjà:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "Occupation.db3");
OccupationModel Occupation;

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
```

Ensuite, nous établissons une connexion à la base de données sur le chemin d’accès que nous avons créé ci-dessus:

```csharp
var conn = new SQLiteConnection (db);
```

Enfin, nous créons la table et ajoutons des enregistrements par défaut:

```csharp
// Yes, build table
conn.CreateTable<OccupationModel> ();

// Add occupations
Occupation = new OccupationModel ("Documentation Manager", "Manages the Documentation Group");
conn.Insert (Occupation);

Occupation = new OccupationModel ("Technical Writer", "Writes technical documentation and sample applications");
conn.Insert (Occupation);

Occupation = new OccupationModel ("Web & Infrastructure", "Creates and maintains the websites that drive documentation");
conn.Insert (Occupation);

Occupation = new OccupationModel ("API Documentation Manager", "Manages the API Documentation Group");
conn.Insert (Occupation);

Occupation = new OccupationModel ("API Documenter", "Creates and maintains API documentation");
conn.Insert (Occupation);
```

### <a name="adding-a-table-view"></a>Ajout d’une vue de table

En guise d’exemple d’utilisation, nous allons ajouter une vue de table à notre interface utilisateur dans l’interface Builder de Xcode. Nous allons exposer cette vue de table via une prise`OccupationTable`() afin que nous puissions y C# accéder par le biais du code:

[![Exposition d’une sortie de vue de table](databases-images/table01.png "Exposition d’une sortie de vue de table")](databases-images/table01-large.png#lightbox)

Ensuite, nous allons ajouter les classes personnalisées pour remplir cette table avec les données de la base de données SQLite.NET.

### <a name="creating-the-table-data-source"></a>Création de la source de données de la table

Nous allons créer une source de données personnalisée pour fournir les données de notre table. Tout d’abord, ajoutez une nouvelle `TableORMDatasource` classe appelée et faites-lui ressembler à ceci:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;
using SQLite;

namespace MacDatabase
{
    public class TableORMDatasource : NSTableViewDataSource
    {
        #region Computed Properties
        public List<OccupationModel> Occupations { get; set;} = new List<OccupationModel>();
        public SQLiteConnection Conn { get; set; }
        #endregion

        #region Constructors
        public TableORMDatasource (SQLiteConnection conn)
        {
            // Initialize
            this.Conn = conn;
            LoadOccupations ();
        }
        #endregion

        #region Public Methods
        public void LoadOccupations() {

            // Get occupations from database
            var query = Conn.Table<OccupationModel> ();

            // Copy into table collection
            Occupations.Clear ();
            foreach (OccupationModel occupation in query) {
                Occupations.Add (occupation);
            }

        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Occupations.Count;
        }
        #endregion
    }
}
```

Lorsque nous créons une instance de cette classe par la suite, nous transmettons notre connexion à la base de données SQLite.NET ouverte. La `LoadOccupations` méthode interroge la base de données et copie les enregistrements trouvés dans la mémoire `OccupationModel` (à l’aide de notre modèle de données).

### <a name="creating-the-table-delegate"></a>Création du délégué de table

La dernière classe dont nous avons besoin est un délégué de table personnalisé pour afficher les informations que nous avons chargées à partir de la base de données SQLite.NET. Nous allons ajouter un nouveau `TableORMDelegate` à notre projet et le faire ressembler à ce qui suit:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;
using SQLite;

namespace MacDatabase
{
    public class TableORMDelegate : NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "OccCell";
        #endregion

        #region Private Variables
        private TableORMDatasource DataSource;
        #endregion

        #region Constructors
        public TableORMDelegate (TableORMDatasource dataSource)
        {
            // Initialize
            this.DataSource = dataSource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Occupation":
                view.StringValue = DataSource.Occupations [(int)row].Name;
                break;
            case "Description":
                view.StringValue = DataSource.Occupations [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Ici, nous utilisons la collection de `Occupations` la source de données (que nous avons chargée à partir de la base de données SQLite.net) pour remplir `GetViewForItem` les colonnes de notre table via le remplacement de la méthode.

### <a name="populating-the-table"></a>Remplissage de la table

Une fois tous les éléments en place, nous allons remplir notre table lorsqu’elle est insérée dans le fichier. XIB en remplaçant la `AwakeFromNib` méthode et en la faisant ressembler à ceci:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get database connection
    Conn = GetDatabaseConnection ();

    // Create the Occupation Table Data Source and populate it
    var DataSource = new TableORMDatasource (Conn);

    // Populate the Product Table
    OccupationTable.DataSource = DataSource;
    OccupationTable.Delegate = new TableORMDelegate (DataSource);
}
```

Tout d’abord, nous obtenons l’accès à notre base de données SQLite.NET, en la créant et en la remplissant si elle n’existe pas déjà. Ensuite, nous créons une nouvelle instance de notre source de données de table personnalisée, nous passons à la connexion de base de données et nous l’attachons à la table. Enfin, nous créons une nouvelle instance de notre délégué de table personnalisé, nous transmettons notre source de données et l’attachons à la table.

## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation de la liaison de données et du codage clé-valeur avec les bases de données SQLite dans une application Xamarin. Mac. Tout d’abord, il s’est penché sur l’exposition d’une C# classe à Objective-C à l’aide du codage clé-valeur (KVC) et de l’observation clé-valeur (KVO). Ensuite, il a montré comment utiliser une classe conforme à KVO et lier les données aux éléments d’interface utilisateur du Interface Builder de Xcode. L’article traite également de l’utilisation des données SQLite via l’ORM SQLite.NET et de l’affichage de ces données dans une vue table.



## <a name="related-links"></a>Liens associés

- [MacDatabase (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macdatabase)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)
- [Contrôles standard](~/mac/user-interface/standard-controls.md)
- [Vues de table](~/mac/user-interface/table-view.md)
- [Modes plan](~/mac/user-interface/outline-view.md)
- [Vues de collection](~/mac/user-interface/collection-view.md)
- [Guide de programmation de codage de clé-valeur](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Présentation des liaisons de cacao rubriques relatives à la programmation](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introduction à la référence des liaisons de cacao](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Human Interface Guidelines pour macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
