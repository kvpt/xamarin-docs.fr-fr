---
title: Création d’un ContentProvider personnalisé
description: La section précédente a montré comment utiliser les données d’une implémentation intégrée de ContentProvider. Cette section explique comment créer un ContentProvider personnalisé, puis utiliser ses données.
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 3e57e0cd2fa87db8035fa68995b69f231151fa09
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020527"
---
# <a name="creating-a-custom-contentprovider"></a>Création d’un ContentProvider personnalisé

_La section précédente a montré comment utiliser les données d’une implémentation intégrée de ContentProvider. Cette section explique comment créer un ContentProvider personnalisé, puis utiliser ses données._

## <a name="about-contentproviders"></a>À propos de ContentProvider

Une classe de fournisseur de contenu doit hériter de `ContentProvider`. Il doit se composer d’un magasin de données interne qui est utilisé pour répondre aux requêtes et il doit exposer des URI et des types MIME en tant que constantes pour aider à consommer du code à effectuer des demandes de données valides.

### <a name="uri-authority"></a>URI (autorité)

les `ContentProviders` sont accessibles dans Android à l’aide d’un URI. Une application qui expose une `ContentProvider` définit les URI auxquels elle doit répondre dans son fichier **fichier AndroidManifest. xml** . Lorsque l’application est installée, ces URI sont enregistrés afin que d’autres applications puissent y accéder.

Dans mono pour Android, la classe de fournisseur de contenu doit avoir un attribut `[ContentProvider]` pour spécifier l’URI (ou les URI) qui doit être ajouté à **fichier AndroidManifest. xml**.

### <a name="mime-type"></a>Type MIME

Le format standard pour les types MIME est constitué de deux parties. Les `ContentProviders` Android utilisent couramment ces deux chaînes pour la première partie du type MIME :

1. `vnd.android.cursor.item` &ndash; pour représenter une seule ligne, utilisez la constante `ContentResolver.CursorItemBaseType` dans le code.

1. `vnd.android.cursor.dir` &ndash; pour plusieurs lignes, utilisez la constante `ContentResolver.CursorDirBaseType` dans le code.

La deuxième partie du type MIME est spécifique à votre application et doit utiliser une norme DNS inverse avec un préfixe `vnd.`. L’exemple de code utilise `vnd.com.xamarin.sample.Vegetables`.

### <a name="data-model-metadata"></a>Métadonnées du modèle de données

Les applications consommatrices doivent construire des requêtes URI pour accéder à différents types de données. L’URI de base peut être développé pour faire référence à une table de données particulière et peut également inclure des paramètres pour filtrer les résultats. Les colonnes et les clauses utilisées avec le curseur résultant pour afficher les données doivent également être déclarées.

Pour vous assurer que seules les requêtes d’URI valides sont construites, il est possible de fournir les chaînes valides en tant que valeurs constantes. Cela facilite l’accès à la `ContentProvider`, car elle rend les valeurs détectables via la saisie semi-automatique du code et empêche les fautes de frappe dans les chaînes.

Dans l’exemple précédent, la classe `android.provider.ContactsContract` exposait les métadonnées pour les données de contact. Pour notre `ContentProvider` personnalisée, nous exposerons simplement les constantes sur la classe elle-même.

## <a name="implementation"></a>Implémentation

Il existe trois étapes pour créer et consommer une `ContentProvider`personnalisée :

1. **Créer une classe de base de données** &ndash; implémenter `SQLiteOpenHelper`.

2. **Créer une classe `ContentProvider`** &ndash; implémenter `ContentProvider` avec une instance de la base de données, les métadonnées exposées en tant que valeurs constantes et méthodes pour accéder aux données.

3. **Accédez au `ContentProvider` via son uri** &ndash; remplir un `CursorAdapter` à l’aide de l' `ContentProvider`, accessible via son URI.

Comme indiqué précédemment, les `ContentProviders` peuvent être consommées à partir d’applications autres que celles où elles sont définies. Dans cet exemple, les données sont consommées dans la même application, mais gardez à l’esprit que d’autres applications peuvent également y accéder tant qu’elles connaissent l’URI et les informations sur le schéma (qui est généralement exposé comme des valeurs constantes).

## <a name="create-a-database"></a>Créer une base de données

La plupart des `ContentProvider` implémentations sont basées sur une base de données `SQLite`. L’exemple de code de base de données dans **SimpleContentProvider/VegetableDatabase. cs** crée une base de données à deux colonnes très simple, comme indiqué ci-dessous :

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

L’implémentation de base de données elle-même n’a pas besoin de considérations spéciales à exposer avec un `ContentProvider`. Toutefois, si vous envisagez de lier les données `ContentProvider's` à un contrôle `ListView`, une colonne d’entiers unique nommée `_id` doit faire partie du jeu de résultats. Pour plus d’informations sur l’utilisation du contrôle `ListView`, consultez le document sur les contrôles [et les adaptateurs](~/android/user-interface/layouts/list-view/index.md) .

## <a name="create-the-contentprovider"></a>Créer le ContentProvider

Le reste de cette section fournit des instructions pas à pas sur la façon dont la classe d’exemple **SimpleContentProvider/VegetableProvider. cs** a été générée.

### <a name="initialize-the-database"></a>Initialiser la base de données

La première étape consiste à sous-reclasser `ContentProvider` et à ajouter la base de données qu’elle utilisera.

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

Le reste du Code constitue l’implémentation réelle du fournisseur de contenu qui permet de découvrir les données et de les interroger.

## <a name="add-metadata-for-consumers"></a>Ajouter des métadonnées pour les consommateurs

Il existe quatre types de métadonnées différents que nous allons exposer sur la classe `ContentProvider`. Seule l’autorité est requise, les autres sont effectuées par Convention.

- L' **autorité** &ndash; l’attribut `ContentProvider` *doit* être ajouté à la classe pour qu’elle soit inscrite auprès d’Android lors de l’installation de l’application.

- **Uri** &ndash; le `CONTENT_URI` est exposé comme une constante afin qu’il soit facile à utiliser dans le code. Il doit correspondre à l’autorité, mais inclure le schéma et le chemin de base.

- Les **types mime** &ndash; listes de résultats et les résultats uniques sont traités comme des types de contenu différents. nous définissons donc deux types MIME pour les représenter.

- **InterfaceConsts** &ndash; fournir une valeur constante pour chaque nom de colonne de données, de façon à ce que la consommation de code puisse facilement les découvrir et y faire référence sans risquer des erreurs typographiques.

Ce code montre comment chacun de ces éléments est implémenté, en l’ajoutant à la définition de base de données de l’étape précédente :

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

## <a name="implement-the-uri-parsing-helper"></a>Implémenter l’assistance à l’analyse d’URI

Étant donné que le code de consommation utilise des URI pour effectuer des demandes d’un `ContentProvider`, nous devons être en mesure d’analyser ces demandes pour déterminer les données à retourner. La classe `UriMatcher` peut aider à analyser les URI, une fois qu’elle a été initialisée avec les modèles d’URI pris en charge par le `ContentProvider`.

La `UriMatcher` dans l’exemple sera initialisée avec deux URI :

1. *« com. xamarin. Sample. VegetableProvider/legumes »* &ndash; demande de retourner la liste complète des légumes.

2. *« com. xamarin. Sample. VegetableProvider/legumes/\#»* &ndash; où le \# est un espace réservé pour un paramètre numérique (le `_id` de la ligne dans la base de données). Un espace réservé pour un astérisque («\*») peut également être utilisé pour correspondre à un paramètre de texte.

Dans le code, nous utilisons les constantes pour faire référence à des valeurs de métadonnées telles que l’autorité et le chemin de\_de BASE. Les codes de retour seront utilisés dans les méthodes qui effectuent une analyse URI pour déterminer les données à retourner.

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

Ce code est tout privé pour la classe `ContentProvider`. Pour plus d’informations, reportez-vous à [la documentation de Google UriMatcher](xref:Android.Content.UriMatcher) .

## <a name="implement-the-querymethod"></a>Implémenter QueryMethod

La méthode `ContentProvider` la plus simple à implémenter est la méthode `Query`. L’implémentation ci-dessous utilise la `UriMatcher` pour analyser le paramètre `uri` et appeler la méthode de base de données appropriée. Si le `uri` contient un paramètre ID, l’entier est analysé (à l’aide de `LastPathSegment`) et utilisé dans la requête de base de données.

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

La méthode `GetType` doit également être substituée. Cette méthode peut être appelée pour déterminer le type de contenu qui sera retourné pour un URI donné.
Cela peut indiquer à l’application consommatrice comment gérer ces données.

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

## <a name="implement-the-other-overrides"></a>Implémenter les autres remplacements

Notre exemple simple n’autorise pas la modification ou la suppression de données, mais les méthodes Insert, Update et Delete doivent être implémentées. vous devez donc les ajouter sans implémentation :

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

Cela termine l’implémentation de base de `ContentProvider`. Une fois l’application installée, les données qu’elle expose sont disponibles à l’intérieur de l’application, mais également à toute autre application connaissant l’URI pour la référencer.

## <a name="access-the-contentprovider"></a>Accéder au ContentProvider

Une fois que le `VegetableProvider` a été implémenté, l’accès à celui-ci s’effectue de la même façon que le fournisseur de contacts au début de ce document : obtenir un curseur à l’aide de l’URI spécifié, puis utiliser un adaptateur pour accéder aux données.

## <a name="bind-a-listview-to-a-contentprovider"></a>Lier un ListView à un ContentProvider

Pour remplir un `ListView` avec des données, nous utilisons l’URI qui correspond à la liste non filtrée de légumes. Dans le code, nous utilisons la valeur constante `VegetableProvider.CONTENT_URI`, que nous savons résoudre en `com.xamarin.sample.vegetableprovider/vegetables`. Notre implémentation de `VegetableProvider.Query` renverra un curseur qui peut ensuite être lié à la `ListView`.

Le code de `SimpleContentProvider/HomeScreen.cs` montre la simplicité d’affichage des données d’un `ContentProvider`:

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

L’application obtenue se présente comme suit :

[Capture d’écran ![de la liste d’applications : légumes, fruits, fleurs, légumineuses, bulbes, tubercules](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)

## <a name="retrieve-a-single-item-from-a-contentprovider"></a>Récupérer un élément unique à partir d’un ContentProvider

Une application consommatrice peut également vouloir accéder à des lignes de données uniques, ce qui peut être effectué en construisant un URI différent qui fait référence à une ligne spécifique (par exemple).

Utilisez `ContentResolver` directement pour accéder à un seul élément, en créant un URI avec le `Id`requis.

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

La méthode complète ressemble à ceci :

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

## <a name="related-links"></a>Liens associés

- [SimpleContentProvider (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
