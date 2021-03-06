---
title: Utilisation du ContentProvider des contacts
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: 8c3608837132e221bec12b2c00fb2dd0b1730e76
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454076"
---
# <a name="using-the-contacts-contentprovider"></a>Utilisation du ContentProvider des contacts

Le code qui utilise les données d’accès exposées par un `ContentProvider` ne requiert pas de référence à la `ContentProvider` classe. Au lieu de cela, un URI est utilisé pour créer un curseur sur les données exposées par le `ContentProvider` . Android utilise l’URI pour Rechercher l’application qui expose un `ContentProvider` avec cet identificateur dans le système. L’URI est une chaîne, généralement dans un format DNS inversé tel que `com.android.contacts/data` .

Plutôt que de faire en sorte que les développeurs mémorisent cette chaîne, le fournisseur de *contacts* Android expose ses métadonnées dans la `android.provider.ContactsContract` classe. Cette classe est utilisée pour déterminer l’URI du `ContentProvider` , ainsi que les noms des tables et des colonnes qui peuvent être interrogées.

Certains types de données requièrent également une autorisation spéciale pour accéder à. La liste des contacts prédéfinis requiert l' `android.permission.READ_CONTACTS` autorisation dans le fichier **AndroidManifest.xml** .

Il existe trois façons de créer un curseur à partir de l’URI :

1. **ManagedQuery ()** &ndash; L’approche préférée dans Android 2,3 (niveau d’API 10) et les versions antérieures, a `ManagedQuery` retourne un curseur et gère également l’actualisation des données et la fermeture du curseur. Cette méthode est déconseillée dans Android 3,0 (niveau d’API 11).

1. **ContentResolver. Query ()** &ndash; Retourne un curseur non managé, ce qui signifie qu’il doit être actualisé et fermé explicitement dans le code.

1. **CursorLoader (). LoadInBackground ()** &ndash; introduite dans Android 3,0 (niveau d’API 11), `CursorLoader` est désormais la meilleure façon de consommer un `ContentProvider` . `CursorLoader` interroge un `ContentResolver` sur un thread d’arrière-plan pour que l’interface utilisateur ne soit pas bloquée.
   Cette classe est accessible dans les versions antérieures d’Android à l’aide de la bibliothèque de compatibilité v4.

Chacune de ces méthodes a le même ensemble de base d’entrées :

- **URI** &ndash; Nom qualifié complet de  `ContentProvider` .
- **Projection** &ndash; Spécification des colonnes à sélectionner pour le curseur.
- **Sélection** &ndash; Similaire à une  `WHERE` clause SQL.
- **SelectionArgs** &ndash; Paramètres à substituer dans la sélection.
- **OrdreTri** &ndash; Colonnes de tri.

## <a name="creating-inputs-for-a-query"></a>Création d’entrées pour une requête

L' `ContactsProvider` exemple de code effectue une requête très simple sur le fournisseur de contacts intégré d’Android. Vous n’avez pas besoin de connaître le nom réel de l’URI ou de la colonne. toutes les informations requises pour interroger les contacts `ContentProvider` sont disponibles sous forme de constantes exposées par la `ContactsContract` classe.

Quelle que soit la méthode utilisée pour récupérer le curseur, ces mêmes objets sont utilisés comme paramètres, comme indiqué dans le fichier *ContactsProvider/ContactsAdapter. cs* :

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Pour cet exemple, le `selection` `selectionArgs` et le `sortOrder` sont ignorés en leur affectant la valeur `null` .

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Création d’un curseur à partir d’un URI de fournisseur de contenu

Une fois les objets de paramètres créés, ils peuvent être utilisés de l’une des trois façons suivantes :

### <a name="using-a-managed-query"></a>Utilisation d’une requête managée

Les applications ciblant Android 2,3 (niveau d’API 10) ou version antérieure doivent utiliser cette méthode :

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Ce curseur sera géré par Android. vous n’avez donc pas besoin de le fermer.

### <a name="using-contentresolver"></a>Utilisation de ContentResolver

L’accès `ContentResolver` direct à pour obtenir un curseur sur un `ContentProvider` peut être effectué de la façon suivante :

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Ce curseur étant non géré, il doit être fermé quand il n’est plus nécessaire.
Assurez-vous que le code ferme un curseur qui est ouvert ; sinon, une erreur se produit.

```csharp
cursor.Close();
```

Vous pouvez également appeler `StartManagingCursor()` et `StopManagingCursor()` pour « gérer » le curseur. Les curseurs gérés sont automatiquement désactivés et interrogés à nouveau lorsque les activités sont arrêtées et redémarrées.

### <a name="using-cursorloader"></a>Utilisation de CursorLoader

Les applications générées pour Android 3,0 (niveau d’API 11) ou plus récentes doivent utiliser cette méthode :

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

Le `CursorLoader` garantit que toutes les opérations de curseur sont effectuées sur un thread d’arrière-plan et peut réutiliser intelligemment un curseur existant entre les instances d’activité lorsqu’une activité est redémarrée (par exemple, en raison d’une modification de la configuration) au lieu de recharger les données.

Les versions antérieures d’Android peuvent également utiliser la classe à l' `CursorLoader` aide des [bibliothèques de prise en charge v4](https://developer.android.com/tools/support-library/index.html).

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Affichage des données de curseur avec un adaptateur personnalisé

Pour afficher l’image de contact, nous allons utiliser un adaptateur personnalisé, afin de pouvoir résoudre manuellement la `PhotoId` référence à un chemin d’accès de fichier image.

Pour afficher les données avec un adaptateur personnalisé, l’exemple utilise un `CursorLoader` pour récupérer toutes les données de contact dans une collection locale dans la méthode **FillContacts** à partir de **ContactsProvider/ContactsAdapter. cs**:

```csharp
void FillContacts ()
{
   var uri = ContactsContract.Contacts.ContentUri;
   string[] projection = {
       ContactsContract.Contacts.InterfaceConsts.Id,
       ContactsContract.Contacts.InterfaceConsts.DisplayName,
       ContactsContract.Contacts.InterfaceConsts.PhotoId
  };
   // CursorLoader introduced in Honeycomb (3.0, API11)
   var loader = new CursorLoader(activity, uri, projection, null, null, null);
   var cursor = (ICursor)loader.LoadInBackground();
   contactList = new List<Contact> ();
   if (cursor.MoveToFirst ()) {
      do {
          contactList.Add (new Contact{
              Id = cursor.GetLong (cursor.GetColumnIndex (projection [0])),
              DisplayName = cursor.GetString (cursor.GetColumnIndex (projection [1])),
              PhotoId = cursor.GetString (cursor.GetColumnIndex (projection [2]))
          });
       } while (cursor.MoveToNext());
   }
}
```

Implémentez ensuite les méthodes de BaseAdapter à l’aide de la `contactList` collection. L’adaptateur est implémenté de la même façon qu’avec n’importe quelle autre collection &ndash; . il n’y a pas de traitement spécial ici, car les données proviennent d’un `ContentProvider` :

```csharp
Activity activity;
public ContactsAdapter (Activity activity)
{
   this.activity = activity;
   FillContacts ();
}
public override int Count {
   get { return contactList.Count; }
}
public override Java.Lang.Object GetItem (int position)
{
  return null; // could wrap a Contact in a Java.Lang.Object to return it here if needed
}
public override long GetItemId (int position)
{
   return contactList [position].Id;
}
public override View GetView (int position, View convertView, ViewGroup parent)
{
   var view = convertView ?? activity.LayoutInflater.Inflate (Resource.Layout.ContactListItem, parent, false);
   var contactName = view.FindViewById<TextView> (Resource.Id.ContactName);
   var contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
   contactName.Text = contactList [position].DisplayName;
   if (contactList [position].PhotoId == null) {
       contactImage = view.FindViewById<ImageView> (Resource.Id.ContactImage);
       contactImage.SetImageResource (Resource.Drawable.ContactImage);
   } else {
       var contactUri = ContentUris.WithAppendedId (ContactsContract.Contacts.ContentUri, contactList [position].Id);
       var contactPhotoUri = Android.Net.Uri.WithAppendedPath (contactUri, Contacts.Photos.ContentDirectory);
       contactImage.SetImageURI (contactPhotoUri);
   }
   return view;
}
```

L’image s’affiche (si elle existe) à l’aide de l’URI du fichier image sur l’appareil. L’application se présente comme suit :

[![Capture d’écran de l’application affichant les contacts dans un ListView ; une image est affichée à gauche d’une entrée](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

À l’aide d’un modèle de code similaire, votre application peut accéder à une grande variété de données système, y compris les photos, les vidéos et la musique de l’utilisateur.
Certains types de données requièrent des autorisations spéciales pour être demandées dans le **AndroidManifest.xml**du projet.

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Affichage des données de curseur avec un SimpleCursorAdapter

Le curseur peut également être affiché avec un `SimpleCursorAdapter` (bien que seul le nom s’affiche, pas la photo). Ce code montre comment utiliser un `ContentProvider` avec `SimpleCursorAdapter` (ce code n’apparaît pas dans l’exemple) :

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName
};
var loader = new CursorLoader (this, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
var fromColumns = new string[] {ContactsContract.Contacts.InterfaceConsts.DisplayName};
var toControlIds = new int[] {Android.Resource.Id.Text1};
adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlsIds);
listView.Adapter = adapter;
```

Reportez-vous aux [ListViews et aux adaptateurs](~/android/user-interface/layouts/list-view/index.md) pour plus d’informations sur l’implémentation de `SimpleCursorAdapter` .

## <a name="related-links"></a>Liens associés

- [Démonstration ContactsAdapter (exemple)](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)