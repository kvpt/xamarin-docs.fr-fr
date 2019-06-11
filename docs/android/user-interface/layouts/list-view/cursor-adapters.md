---
title: Utilisation de CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: 42b9bd528459d8ee941cc293372bf5662a493342
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827613"
---
# <a name="using-cursoradapters"></a>Utilisation de CursorAdapters


## <a name="overview"></a>Vue d'ensemble

Android fournit des classes de l’adaptateur en particulier pour afficher des données à partir d’une requête de base de données SQLite :

 **SimpleCursorAdapter** : similaire à une `ArrayAdapter` car il peut être utilisé sans sous-classement. Entrez simplement les paramètres requis (par exemple, un curseur et la disposition des informations) dans le constructeur, puis affecter à un `ListView`.

 **CursorAdapter** – valeurs d’une classe de base que vous pouvez hériter de lorsque vous avez besoin de plus de contrôle sur la liaison de données aux contrôles de disposition (par exemple, masquage/affichage de contrôles ou modifier leurs propriétés).

Adaptateurs de curseur permettent de hautes performances pour faire défiler de longues listes de données qui sont stockées dans SQLite. Le code de consommation doit définir une requête SQL dans un `Cursor` de l’objet, avant de décrire comment créer et remplir les vues pour chaque ligne.


## <a name="creating-an-sqlite-database"></a>Création d’une base de données SQLite

Pour illustrer les adaptateurs de curseur nécessite une implémentation de base de données SQLite simple. Le code dans **SimpleCursorTableAdapter/VegetableDatabase.cs** contient le code et le SQL pour créer une table et la remplir avec des données.
L’ensemble `VegetableDatabase` classe est illustrée ici :

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

Le `VegetableDatabase` classe est instanciée dans le `OnCreate` méthode de la `HomeScreen` activité. Le `SQLiteOpenHelper` gère la configuration du fichier de base de données de classe de base et garantit que le code SQL dans son `OnCreate` méthode est uniquement exécutée une seule fois. Cette classe est utilisée dans les deux exemples suivants pour `SimpleCursorAdapter` et `CursorAdapter`.

La requête de curseur *doit* ont une colonne d’entiers `_id` pour le `CursorAdapter` fonctionne. Si la table sous-jacente n’a pas une colonne d’entiers nommée `_id` ensuite utiliser un alias de colonne pour un autre entier unique dans le `RawQuery` qui compose le curseur. Reportez-vous à la [docs Android](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/) pour plus d’informations.


### <a name="creating-the-cursor"></a>Création du curseur

Les exemples utilisent un `RawQuery` pour activer une requête SQL dans un `Cursor` objet. La liste des colonnes qui est retournée à partir du curseur définit les colonnes de données qui sont disponibles pour l’affichage dans l’adaptateur de curseur. Le code qui crée la base de données dans le **SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate` méthode est indiquée ici :

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Tout code qui appelle `StartManagingCursor` doit également appeler `StopManagingCursor`. Les exemples utilisent `OnCreate` pour démarrer, et `OnDestroy` pour fermer le curseur. Le `OnDestroy` méthode contient ce code :

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Une fois qu’une application a une base de données SQLite disponible et a créé un objet curseur comme indiqué, il peut utiliser soit un `SimpleCursorAdapter` ou une sous-classe de `CusorAdapter` pour afficher les lignes dans un `ListView`.


## <a name="using-simplecursoradapter"></a>À l’aide de SimpleCursorAdapter

`SimpleCursorAdapter` est semblable à la `ArrayAdapter`, mais spécialisée pour une utilisation avec SQLite. Il ne nécessiter de sous-classement – définissez simplement certains des paramètres simples lors de la création de l’objet et assignez-la à un `ListView`de `Adapter` propriété.

Les paramètres du constructeur SimpleCursorAdapter sont :

 **Contexte** – une référence à l’activité conteneur.

 **Disposition** : l’ID de ressource de la vue de la ligne à utiliser.

 **ICursor** – un curseur contenant la requête SQLite pour les données à afficher.

 **À partir de** tableau de chaînes – un tableau de chaînes correspondant aux noms de colonnes dans le curseur.

 **Pour** tableau d’entiers : un tableau d’ID de disposition qui correspondent aux contrôles dans la disposition de ligne. La valeur de la colonne spécifiée dans le `from` tableau sera lié à la ControlID spécifié dans ce tableau dans le même index.

Le `from` et `to` tableaux doivent avoir le même nombre d’entrées, car elles forment un mappage à partir de la source de données pour les contrôles de disposition dans la vue.

Le **SimpleCursorTableAdapter/HomeScreen.cs** exemple fils de code d’un `SimpleCursorAdapter` comme suit :

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` est un moyen rapide et simple pour afficher les données de SQLite dans un `ListView`. La principale limitation est qu’elle peut lier uniquement les valeurs de colonne pour afficher les contrôles, il ne vous permet pas de modifier d’autres aspects de la disposition de ligne (par exemple, les contrôles d’affichage/masquage ou modification des propriétés).


## <a name="subclassing-cursoradapter"></a>Sous-classement CursorAdapter

Un `CursorAdapter` sous-classe a les mêmes avantages de performances que le `SimpleCursorAdapter` pour afficher les données à partir de SQLite, mais il vous donne également un contrôle complet sur la création et la disposition de chaque vue de ligne. Le `CursorAdapter` implémentation est très différente de sous-classement `BaseAdapter` , car il ne se substitue pas `GetView`, `GetItemId`, `Count` ou `this[]` indexeur.

Étant donné un travail SQLite de base de données, vous devez uniquement à substituer deux méthodes pour créer un `CursorAdapter` sous-classe :

- **BindView** – étant donné une vue, mettre à jour pour afficher les données dans le curseur fourni.

- **NewView** – appelée lorsque le `ListView` nécessite une nouvelle vue à afficher. Le `CursorAdapter` s’occupera de recyclage des vues (contrairement à la `GetView` méthode sur les adaptateurs normaux).

Les sous-classes de la carte dans les exemples précédents ont des méthodes pour retourner le nombre de lignes et récupérer l’élément en cours – la `CursorAdapter` ne nécessite pas de ces méthodes, car ces informations peuvent être collectées à partir du curseur lui-même. La création et le remplissage de chaque vue dans ces deux méthodes, si vous fractionnez le `CursorAdapter` applique la réutilisation d’affichage. Voici par opposition à une carte régulière où il est possible d’ignorer le `convertView` paramètre de la `BaseAdapter.GetView` (méthode).


### <a name="implementing-the-cursoradapter"></a>Implémentation de la CursorAdapter

Le code dans **CursorTableAdapter/HomeScreenCursorAdapter.cs** contient un `CursorAdapter` sous-classe. Il stocke une référence de contexte passée au constructeur afin qu’il puisse accéder un `LayoutInflater` dans le `NewView` (méthode). La classe complète ressemble à ceci :

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```


### <a name="assigning-the-cursoradapter"></a>Affectez le CursorAdapter

Dans le `Activity` qui afficheront les `ListView`, crée le curseur et `CursorAdapter` puis l’affecter à l’affichage de liste.

Le code qui effectue cette action dans le **CursorTableAdapter/HomeScreen.cs** `OnCreate` méthode est indiquée ici :

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

Le `OnDestroy` méthode contient le `StopManagingCursor` appel de méthode décrit précédemment.



## <a name="related-links"></a>Liens associés

- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
