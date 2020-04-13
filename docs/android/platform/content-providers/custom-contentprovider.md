---
title: Création d’un ContentProvider personnalisé
description: La section précédente a montré comment consommer les données d’une implémentation intégrée de ContentProvider. Cette section expliquera comment construire un ContentProvider personnalisé et consommera ensuite ses données.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020527"
---
# <a name="creating-a-custom-contentprovider"></a>Création d’un ContentProvider personnalisé

_La section précédente a montré comment consommer les données d’une implémentation intégrée de ContentProvider. Cette section expliquera comment construire un ContentProvider personnalisé et consommera ensuite ses données._

## <a name="about-contentproviders"></a>À propos de ContentProviders

Une classe de fournisseur `ContentProvider`de contenu doit hériter de . Il devrait se composer d’un magasin de données interne qui est utilisé pour répondre aux requêtes et il devrait exposer Uris et MIME Types comme des constantes pour aider à consommer du code faire des demandes valides de données.

### <a name="uri-authority"></a>URI (Autorité)

`ContentProviders`sont accessibles sur Android à l’aide d’un Uri. Une application qui `ContentProvider` expose un ensemble de l’Uris qu’il répondra à dans son fichier **AndroidManifest.xml.** Lorsque l’application est installée, ces Uris sont enregistrées afin que d’autres applications puissent y accéder.

Dans Mono pour Android, la classe `[ContentProvider]` fournisseur de contenu devrait avoir un attribut pour spécifier l’Uri (ou Uris) qui doit être ajouté à **AndroidManifest.xml**.

### <a name="mime-type"></a>Mime Type

Le format typique pour les types MIME se compose de deux parties. Android `ContentProviders` utilisent généralement ces deux chaînes pour la première partie du type MIME:

1. `vnd.android.cursor.item`&ndash; pour représenter une seule `ContentResolver.CursorItemBaseType` ligne, utilisez la constante dans le code.

1. `vnd.android.cursor.dir`&ndash; pour plusieurs rangées, `ContentResolver.CursorDirBaseType` utilisez la constante dans le code.

La deuxième partie du type MIME est spécifique à votre application, et `vnd.` devrait utiliser une norme inverse-DNS avec un préfixe. Le code `vnd.com.xamarin.sample.Vegetables`de l’échantillon utilise .

### <a name="data-model-metadata"></a>Métadonnées de modèle de données

Les applications consommatrices doivent construire des requêtes Uri pour accéder à différents types de données. La base Uri peut être élargie pour se référer à un tableau particulier de données et peut également inclure des paramètres pour filtrer les résultats. Les colonnes et clauses utilisées avec le curseur résultant pour afficher les données doivent également être déclarées.

Pour s’assurer que seules les requêtes Uri valides sont construites, il est d’usage de fournir les chaînes valides comme valeurs constantes. Cela facilite l’accès `ContentProvider` au car il rend les valeurs détectables par l’achèvement du code, et empêche les fautes de frappe dans les cordes.

Dans l’exemple `android.provider.ContactsContract` précédent, la classe a exposé les métadonnées pour les données Contacts. Pour notre `ContentProvider` coutume, nous allons juste exposer les constantes sur la classe elle-même.

## <a name="implementation"></a>Implémentation

Il y a trois étapes `ContentProvider`pour créer et consommer une coutume :

1. **Créer une classe de base** &ndash; de données Implémenter `SQLiteOpenHelper`.

2. **Créez `ContentProvider` une** `ContentProvider` classe &ndash; Implémenter avec une instance de la base de données, métadonnées exposées comme des valeurs et des méthodes constantes pour accéder aux données.

3. **Accédez `ContentProvider` à l’intermédiaire de son Uri** &ndash; Populate a `CursorAdapter` en utilisant le `ContentProvider`, accessible via son Uri.

Comme nous `ContentProviders` l’avons déjà mentionné, peut être consommé à partir d’applications autres que l’endroit où elles sont définies. Dans cet exemple, les données sont consommées dans la même application, mais gardez à l’esprit que d’autres applications peuvent également y accéder tant qu’elles connaissent l’Uri et des informations sur le schéma (qui est généralement exposée comme des valeurs constantes).

## <a name="create-a-database"></a>Créer une base de données

La `ContentProvider` plupart des implémentations seront basées sur une `SQLite` base de données. Le code de base de données d’exemple dans **SimpleContentProvider/VegetableDatabase.cs** crée une base de données à deux colonnes très simple, comme indiqué :

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

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
  {
    throw new NotImplementedException();
  }
}
```

La mise en œuvre de base de `ContentProvider`données elle-même n’a pas besoin de considérations spéciales pour être exposé avec un , mais si vous avez l’intention de lier les `ContentProvider's` données à un `ListView` contrôle, puis une colonne unique integer nommé `_id` doit faire partie de l’ensemble de résultats. Consultez le document [ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) `ListView` pour plus de détails sur l’utilisation du contrôle.

## <a name="create-the-contentprovider"></a>Créer le ContentProvider

Le reste de cette section donne des instructions étape par étape sur la façon dont la classe **d’exemple SimpleContentProvider/VegetableProvider.cs** a été construite.

### <a name="initialize-the-database"></a>Initialiser la base de données

La première étape consiste `ContentProvider` à sous-classer et à ajouter la base de données qu’elle utilisera.

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

Le reste du code formera la mise en œuvre réelle du fournisseur de contenu qui permet de découvrir et de interroger les données.

## <a name="add-metadata-for-consumers"></a>Ajouter des métadonnées pour les consommateurs

Il existe quatre types différents de métadonnées `ContentProvider` que nous allons exposer sur la classe. Seule l’autorité est requise, le reste se fait par convention.

- **Autorité** &ndash; `ContentProvider` L’attribut *doit* être ajouté à la classe afin qu’il soit enregistré auprès de l’Android lorsque l’application est installée.

- **Uri** &ndash; `CONTENT_URI` The est exposé comme une constante de sorte qu’il est facile à utiliser dans le code. Il devrait correspondre à l’Autorité, mais inclure le régime et le chemin de base.

- **Les types** &ndash; MIME Listes des résultats et des résultats uniques sont traités comme différents types de contenu, de sorte que nous définissons deux types DE MIME pour les représenter.

- **InterfaceConsts** &ndash; Fournir une valeur constante pour chaque nom de colonne de données, de sorte que la consommation de code peut facilement les découvrir et se référer à eux sans risquer des erreurs typographiques.

Ce code montre comment chacun de ces éléments est implémenté, ajoutant à la définition de base de données de l’étape précédente :

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```

## <a name="implement-the-uri-parsing-helper"></a>Mettre en œuvre l’assistant de parsing URI

Parce que la consommation de code `ContentProvider`utilise Uris pour faire des demandes d’un , nous devons être en mesure d’analyser ces demandes pour déterminer quelles données revenir. La `UriMatcher` classe peut aider à analyser Uris, une fois qu’il `ContentProvider` a été parascé avec les modèles Uri que les supports.

L’exemple `UriMatcher` sera paradé avec deux Uris :

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash; demande de retourner la liste complète des légumes.

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash; \# où le est un placeholder pour un paramètre numérique (la `_id` rangée dans la base de données). Un lieu d’astérisque ("\*" ) peut également être utilisé pour correspondre à un paramètre de texte.

Dans le code, nous utilisons les constantes pour désigner\_des valeurs de métadonnées comme l’AUTHORITY et BASE PATH. Les codes de retour seront utilisés dans les méthodes qui ne Uri analyse, pour déterminer quelles données revenir.

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

Ce code est tout `ContentProvider` privé de la classe. Consultez la [documentation UriMatcher de Google](xref:Android.Content.UriMatcher) pour plus d’informations.

## <a name="implement-the-querymethod"></a>Mettre en œuvre la RequêteMethod

La méthode `ContentProvider` la plus `Query` simple à mettre en œuvre est la méthode. La mise en `UriMatcher` œuvre `uri` ci-dessous utilise le pour analyser le paramètre et appeler la bonne méthode de base de données. Si `uri` le contient un paramètre d’identification, puis `LastPathSegment`l’intégré est analysé (à l’aide) et utilisé dans la requête de base de données.

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

La `GetType` méthode doit également être remplacée. Cette méthode peut être appelée pour déterminer le type de contenu qui sera retourné pour un Uri donné.
Cela pourrait indiquer à l’application consommatrice comment gérer ces données.

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```

## <a name="implement-the-other-overrides"></a>Mettre en œuvre les autres remplacements

Notre exemple simple ne permet pas l’édition ou la suppression des données, mais les méthodes d’insertion, de mise à jour et de suppression doivent être mises en œuvre, alors ajoutez-les sans implémentation :

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

Cela complète la `ContentProvider` mise en œuvre de base. Une fois l’application installée, les données qu’elle expose seront disponibles à la fois à l’intérieur de l’application mais aussi à toute autre application qui connaît l’Uri pour la référer.

## <a name="access-the-contentprovider"></a>Accédez au ContentProvider

Une `VegetableProvider` fois que l’a été mis en œuvre, l’accès se fait de la même manière que le fournisseur Contacts au début de ce document: obtenir un curseur en utilisant l’Uri spécifié, puis utiliser un adaptateur pour accéder aux données.

## <a name="bind-a-listview-to-a-contentprovider"></a>Lier une ListView à un ContentProvider

Pour remplir `ListView` un avec des données, nous utilisons l’Uri qui correspond à la liste non filtrée des légumes. Dans le code, nous `VegetableProvider.CONTENT_URI`utilisons la valeur `com.xamarin.sample.vegetableprovider/vegetables`constante , qui nous savons se résout à . Notre `VegetableProvider.Query` mise en œuvre retournera un `ListView`curseur qui peut ensuite être lié à la .

Le code `SimpleContentProvider/HomeScreen.cs` dans montre comment il est `ContentProvider`simple d’afficher les données à partir d’un :

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

L’application qui en résulte ressemble à ceci:

[![Capture d’écran de l’application énumérant Légumes, Fruits, Bourgeons floraux, Légumineuses, Bulbs, Tubers](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Récupérez un seul élément d’un contentProvider

Une application consommatrice peut également vouloir accéder à des lignes de données uniques, ce qui peut être fait en construisant un Uri différent qui se réfère à une ligne spécifique (par exemple).

Utilisez `ContentResolver` directement pour accéder à un seul article, `Id`en construisant un Uri avec le requis .

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

La méthode complète ressemble à ceci:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```

## <a name="related-links"></a>Liens connexes

- [SimpleContentProvider (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
