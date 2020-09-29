---
title: Utilisation de CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: 81ce3a31a151c8783e09bc42f2dcc2c64168a2e4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457573"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>Utilisation de CursorAdapters avec Xamarin. Android

Android fournit des classes d’adaptateurs spécifiquement pour afficher des données à partir d’une requête de base de données SQLite :

 **SimpleCursorAdapter** : similaire à un `ArrayAdapter` , car il peut être utilisé sans sous-classe. Il vous suffit de fournir les paramètres requis (par exemple, un curseur et des informations de mise en page) dans le constructeur, puis de les assigner à un `ListView` .

 **CursorAdapter** : classe de base dont vous pouvez hériter lorsque vous avez besoin de davantage de contrôle sur la liaison des valeurs de données aux contrôles de disposition (par exemple, masquage/affichage de contrôles ou modification de leurs propriétés).

Les adaptateurs de curseur offrent un moyen hautes performances de faire défiler les longues listes de données stockées dans SQLite. Le code de consommation doit définir une requête SQL dans un `Cursor` objet, puis décrire comment créer et remplir les vues pour chaque ligne.

## <a name="creating-an-sqlite-database"></a>Création d’une base de données SQLite

Pour illustrer les adaptateurs de curseur, il est nécessaire d’implémenter une base de données SQLite simple. Le code dans **SimpleCursorTableAdapter/VegetableDatabase. cs** contient le code et SQL pour créer une table et la remplir avec des données.
La `VegetableDatabase` classe complète est présentée ici :

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

La `VegetableDatabase` classe sera instanciée dans la `OnCreate` méthode de l' `HomeScreen` activité. La `SQLiteOpenHelper` classe de base gère l’installation du fichier de base de données et garantit que le SQL dans sa `OnCreate` méthode n’est exécuté qu’une seule fois. Cette classe est utilisée dans les deux exemples suivants pour `SimpleCursorAdapter` et `CursorAdapter` .

La requête de curseur *doit* avoir une colonne `_id` de type entier pour que le `CursorAdapter` fonctionne. Si la table sous-jacente n’a pas de colonne d’entiers nommée `_id` , utilisez un alias de colonne pour un autre entier unique dans le `RawQuery` qui compose le curseur. Pour plus d’informations, reportez-vous aux [documents Android](xref:Android.Widget.CursorAdapter) .

### <a name="creating-the-cursor"></a>Création du curseur

Les exemples utilisent un `RawQuery` pour transformer une requête SQL en `Cursor` objet. La liste des colonnes retournée à partir du curseur définit les colonnes de données qui peuvent être affichées dans l’adaptateur de curseur. Le code qui crée la base de données dans la méthode **SimpleCursorTableAdapter/homescreen. cs** `OnCreate` est illustré ici :

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

Tout code qui appelle `StartManagingCursor` doit également appeler `StopManagingCursor` . Les exemples utilisent `OnCreate` pour démarrer et `OnDestroy` pour fermer le curseur. La `OnDestroy` méthode contient le code suivant :

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

Une fois qu’une application a une base de données SQLite disponible et a créé un objet curseur comme indiqué, elle peut utiliser une `SimpleCursorAdapter` ou une sous-classe de `CusorAdapter` pour afficher des lignes dans un `ListView` .

## <a name="using-simplecursoradapter"></a>Utilisation de SimpleCursorAdapter

`SimpleCursorAdapter` est semblable à `ArrayAdapter` , mais spécialisé pour une utilisation avec sqlite. Elle ne nécessite pas de sous-classement. il vous suffit de définir des paramètres simples lors de la création de l’objet, puis de l’assigner à la `ListView` `Adapter` propriété de.

Les paramètres du constructeur SimpleCursorAdapter sont les suivants :

 **Context** : référence à l’activité contenante.

 **Layout** : ID de ressource de la vue de ligne à utiliser.

 **ICursor** : curseur contenant la requête SQLite pour les données à afficher.

 **À partir du** tableau de chaînes : tableau de chaînes correspondant aux noms des colonnes dans le curseur.

 **En** tableau d’entiers : tableau d’ID de disposition qui correspondent aux contrôles dans la disposition de ligne. La valeur de la colonne spécifiée dans le `from` tableau sera liée à l’ControlID spécifié dans ce tableau au même index.

Les `from` `to` tableaux et doivent avoir le même nombre d’entrées, car ils forment un mappage de la source de données aux contrôles de disposition de la vue.

L’exemple de code **SimpleCursorTableAdapter/homescreen. cs** est le `SimpleCursorAdapter` suivant :

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` est un moyen rapide et simple d’afficher des données SQLite dans un `ListView` . La principale limitation est qu’elle ne peut lier des valeurs de colonne qu’à des contrôles d’affichage, mais elle ne vous permet pas de modifier d’autres aspects de la disposition des lignes (par exemple, en affichant/masquant des contrôles ou en modifiant des propriétés).

## <a name="subclassing-cursoradapter"></a>Sous-classement CursorAdapter

Une `CursorAdapter` sous-classe offre les mêmes avantages en matière de performances que le `SimpleCursorAdapter` pour l’affichage de données à partir de SQLite, mais elle vous donne également un contrôle total sur la création et la mise en page de chaque vue de ligne. L' `CursorAdapter` implémentation est très différente de la sous-classe, `BaseAdapter` car elle ne remplace `GetView` pas `GetItemId` , `Count` ou `this[]` indexeur.

Pour une base de données SQLite fonctionnelle, il vous suffit de remplacer deux méthodes pour créer une `CursorAdapter` sous-classe :

- **BindView** : à partir d’une vue, mettez-la à jour pour afficher les données dans le curseur fourni.

- **NewView** – appelé lorsque `ListView` requiert l’affichage d’une nouvelle vue. Le `CursorAdapter` s’occupe du recyclage des vues (contrairement `GetView` à la méthode sur les adaptateurs standard).

Les sous-classes d’adaptateurs dans les exemples précédents ont des méthodes pour retourner le nombre de lignes et pour récupérer l’élément actuel `CursorAdapter` . le ne requiert pas ces méthodes, car ces informations peuvent être collectées à partir du curseur lui-même. En fractionnant la création et le remplissage de chaque vue en ces deux méthodes, le `CursorAdapter` applique la réutilisation de la vue. Cela est différent d’un adaptateur normal dans lequel il est possible d’ignorer le `convertView` paramètre de la `BaseAdapter.GetView` méthode.

### <a name="implementing-the-cursoradapter"></a>Implémentation de CursorAdapter

Le code dans **CursorTableAdapter/HomeScreenCursorAdapter. cs** contient une `CursorAdapter` sous-classe. Il stocke une référence de contexte passée au constructeur afin qu’il puisse accéder `LayoutInflater` à un dans la `NewView` méthode. La classe complète se présente comme suit :

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

### <a name="assigning-the-cursoradapter"></a>Attribution de CursorAdapter

Dans le `Activity` qui affichera `ListView` , créez le curseur, `CursorAdapter` puis affectez-le à l’affichage de liste.

Le code qui effectue cette action dans la méthode **CursorTableAdapter/homescreen. cs** `OnCreate` est illustré ici :

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

La `OnDestroy` méthode contient l' `StopManagingCursor` appel de méthode décrit précédemment.

## <a name="related-links"></a>Liens associés

- [SimpleCursorTableAdapter (exemple)](/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (exemple)](/samples/xamarin/monodroid-samples/cursortableadapter)