---
title: Utilisation du ContentProvider des contacts
ms.prod: xamarin
ms.assetid: 21C5D1B4-3783-6090-33AB-78A484E65925
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: fca57b7af34ae2b28dda9bf20a95183138cbc641
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020542"
---
# <a name="using-the-contacts-contentprovider"></a>Utilisation du ContentProvider des contacts

Le code qui utilise `ContentProvider` les données d’accès `ContentProvider` exposées par un n’exige pas du tout une référence à la classe. Au lieu de cela, un Uri est utilisé pour `ContentProvider`créer un curseur sur les données exposées par le . Android utilise l’Uri pour rechercher le système `ContentProvider` pour l’application qui expose un avec cet identifiant. L’Uri est une chaîne, généralement dans un `com.android.contacts/data`format inverse-DNS tels que .

Plutôt que de faire rappeler à ces développeurs cette chaîne, `android.provider.ContactsContract` le fournisseur de *contacts* Android expose ses métadonnées dans la classe. Cette classe est utilisée pour déterminer `ContentProvider` l’Uri de la ainsi que les noms des tables et des colonnes qui peuvent être interrogés.

Certains types de données nécessitent également une autorisation spéciale d’accès. La liste des contacts `android.permission.READ_CONTACTS` intégrés nécessite la permission dans le fichier **AndroidManifest.xml.**

Il y a trois façons de créer un curseur à partir de l’Uri :

1. **ManagedQuery()** &ndash; L’approche préférée dans Android 2.3 (API Niveau `ManagedQuery` 10) et plus tôt, un retour d’un curseur et gère également automatiquement rafraîchir les données et la fermeture du curseur. Cette méthode est dépréciée dans Android 3.0 (niveau API 11).

1. **ContentResolver.Query()** &ndash; Renvoie un curseur non gémanique, ce qui signifie qu’il doit être actualisé et fermé explicitement dans le code.

1. **CursesorLoader (). LoadInBackground()** &ndash; Introduit dans Android 3.0 (API `CursorLoader` Niveau 11), `ContentProvider` est maintenant le moyen préféré de consommer un . `CursorLoader`requête un `ContentResolver` sur un thread de fond de sorte que l’interface utilisateur n’est pas bloqué.
   Cette classe est accessible dans les anciennes versions d’Android en utilisant la bibliothèque de compatibilité v4.

Chacune de ces méthodes a le même ensemble de base d’entrées:

- **Uri** &ndash; Le nom entièrement `ContentProvider` qualifié de la .
- **Spécification** &ndash; de projection des colonnes à sélectionner pour le curseur.
- **Sélection** &ndash; semblable à `WHERE` une clause SQL.
- **SélectionArgs** &ndash; Paramètres à remplacer dans la sélection.
- **SortOrder** &ndash; Colonnes à trier.

## <a name="creating-inputs-for-a-query"></a>Création d’intrants pour une requête

Le `ContactsProvider` code d’échantillon effectue une requête très simple contre le fournisseur de contacts intégré d’Android. Vous n’avez pas besoin de connaître les noms réels d’Uri ou de colonne - toutes les informations requises pour interroger les Contacts `ContentProvider` sont disponibles en tant que constantes exposées par la `ContactsContract` classe.

Quelle que soit la méthode utilisée pour récupérer le curseur, ces mêmes objets sont utilisés comme paramètres comme indiqué dans le fichier *ContactsProvider/ContactsAdapter.cs* :

```csharp
var uri = ContactsContract.Contacts.ContentUri;
string[] projection = {
   ContactsContract.Contacts.InterfaceConsts.Id,
   ContactsContract.Contacts.InterfaceConsts.DisplayName,
   ContactsContract.Contacts.InterfaceConsts.PhotoId,
};
```

Pour cet exemple, `selectionArgs` `sortOrder` le `selection`, et sera `null`ignoré en les fixant à .

## <a name="creating-a-cursor-from-a-content-provider-uri"></a>Création d’un curseur à partir d’un fournisseur de contenu Uri

Une fois que les objets paramètres ont été créés, ils peuvent être utilisés de l’une des trois façons suivantes :

### <a name="using-a-managed-query"></a>Utilisation d’une requête gérée

Les applications ciblant Android 2.3 (niveau API 10) ou antérieures doivent utiliser cette méthode :

```csharp
var cursor = activity.ManagedQuery(uri, projection, null, null, null);
```

Ce curseur sera géré par Android de sorte que vous n’avez pas besoin de le fermer.

### <a name="using-contentresolver"></a>Utilisation de ContentResolver

Accéder `ContentResolver` directement pour obtenir un curseur contre un `ContentProvider` peut être fait comme ceci:

```csharp
var cursor = activity.ContentResolver(uri, projection, null, null, null);
```

Ce curseur n’est pas menté, de sorte qu’il doit être fermé lorsqu’il n’est plus nécessaire.
Assurez-vous que le code ferme un curseur qui est ouvert, sinon une erreur se produira.

```csharp
cursor.Close();
```

Alternativement, vous `StartManagingCursor()` pouvez `StopManagingCursor()` appeler et de «gérer» le curseur. Les curseurs gérés sont automatiquement désactivés et re-interrogés lorsque les activités sont arrêtées et redémarrées.

### <a name="using-cursorloader"></a>Utilisation de CursorLoader

Les applications conçues pour Android 3.0 (niveau API 11) ou plus récents doivent utiliser cette méthode :

```csharp
var loader = new CursorLoader (activity, uri, projection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();
```

Le `CursorLoader` s’assure que toutes les opérations de curseur sont effectuées sur un thread de fond, et peut intelligemment réutilisation d’un curseur existant à travers les cas d’activité quand une activité est redémarrée (par exemple en raison d’un changement de configuration) plutôt que recharger les données à nouveau.

Les versions Android antérieures `CursorLoader` peuvent également utiliser la classe en utilisant les [bibliothèques de support v4](https://developer.android.com/tools/support-library/index.html).

## <a name="displaying-the-cursor-data-with-a-custom-adapter"></a>Affichage des données cursor avec un adaptateur personnalisé

Pour afficher l’image de contact, nous utiliserons un adaptateur personnalisé, afin que nous puissions résoudre manuellement la `PhotoId` référence à un chemin de fichier d’image.

Pour afficher les données avec un `CursorLoader` adaptateur personnalisé, l’exemple utilise un pour récupérer toutes les données de contact dans une collection locale dans la méthode **FillContacts** de **ContactsProvider/ContactsAdapter.cs**:

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

Ensuite, implémentez les méthodes `contactList` de base à l’aide de la collection. L’adaptateur est implémenté comme &ndash; il serait avec toute autre collection il `ContentProvider`n’y a pas de manipulation spéciale ici parce que les données proviennent d’un :

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

L’image est affichée (si elle existe) en utilisant l’Uri au fichier d’image sur l’appareil. L’application ressemble à ceci:

[![Capture d’écran de l’application affichant des contacts dans un ListView; une image est affichée à gauche d’une entrée](contacts-contentprovider-images/contactsprovider.png)](contacts-contentprovider-images/contactsprovider.png#lightbox)

En utilisant un modèle de code similaire, votre application peut accéder à une grande variété de données système, y compris les photos, vidéos et musique de l’utilisateur.
Certains types de données nécessitent des autorisations spéciales pour être demandés dans **AndroidManifest.xml**du projet .

## <a name="displaying-the-cursor-data-with-a-simplecursoradapter"></a>Affichage des données cursor avec un SimpleCursorAdapter

Le curseur peut également être `SimpleCursorAdapter` affiché avec un (bien que seul le nom sera affiché, pas la photo). Ce code montre comment `ContentProvider` utiliser `SimpleCursorAdapter` un avec (ce code n’apparaît pas dans l’échantillon):

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

Consultez les [ListViews et Adaptateurs](~/android/user-interface/layouts/list-view/index.md) pour `SimpleCursorAdapter`plus d’informations sur la mise en œuvre .

## <a name="related-links"></a>Liens connexes

- [ContactsAdapter Demo (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
