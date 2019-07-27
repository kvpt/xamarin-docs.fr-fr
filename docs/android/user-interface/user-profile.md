---
title: Profil utilisateur
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2018
ms.openlocfilehash: bed346b33ac92f6a1c73cdd3b29fb70ba17c5e91
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509669"
---
# <a name="user-profile"></a>Profil utilisateur

Android prend en charge l’énumération des contacts avec le fournisseur [ContactsContract](xref:Android.Provider.ContactsContract) depuis l’API niveau 5. Par exemple, le fait de répertorier les contacts est aussi simple que l’utilisation de la classe [ContactContracts. contacts](xref:Android.Provider.ContactsContract.Contacts) , comme indiqué dans l’exemple de code suivant:

```csharp
// Get the URI for the user's contacts:
var uri = ContactsContract.Contacts.ContentUri;

// Setup the "projection" (columns we want) for only the ID and display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.Id, 
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the user's contacts data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();

// Print the contact data to the console if reading back succeeds:
if (cursor != null)
{
    if (cursor.MoveToFirst())
    {
        do
        {
            Console.WriteLine("Contact ID: {0}, Contact Name: {1}",
                               cursor.GetString(cursor.GetColumnIndex(projection[0])),
                               cursor.GetString(cursor.GetColumnIndex(projection[1])));
        } while (cursor.MoveToNext());
    }
}
```

À compter d’Android 4 (niveau d’API 14), la classe [ContactsContact. Profile](xref:Android.Provider.ContactsContract.Profile) est `ContactsContract` disponible via le fournisseur. Le `ContactsContact.Profile` fournit l’accès au profil personnel pour le propriétaire d’un appareil, y compris les données de contact, telles que le nom et le numéro de téléphone du propriétaire de l’appareil.

## <a name="required-permissions"></a>Autorisations requises

Pour lire et écrire des données de contact, les applications `READ_CONTACTS` doivent `WRITE_CONTACTS` demander les autorisations et, respectivement.
En outre, pour lire et modifier le profil utilisateur, les applications doivent demander `READ_PROFILE` les `WRITE_PROFILE` autorisations et.

## <a name="updating-profile-data"></a>Mise à jour des données de profil

Une fois ces autorisations définies, une application peut utiliser des techniques Android normales pour interagir avec les données du profil utilisateur. Par exemple, pour mettre à jour le nom d’affichage du profil, appelez [ContentResolver. Update](xref:Android.Content.ContentResolver.Update*) avec un `Uri` récupéré via la propriété [ContactsContract. Profile. ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri) , comme indiqué ci-dessous:

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lecture des données du profil

L’émission d’une requête vers [ContactsContact. Profile. ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri) lit les données de profil. Par exemple, le code suivant lit le nom complet du profil utilisateur:

```csharp
// Read the profile
var uri = ContactsContract.Profile.ContentUri;

// Setup the "projection" (column we want) for only the display name:
string[] projection = {
    ContactsContract.Contacts.InterfaceConsts.DisplayName };

// Use a CursorLoader to retrieve the data:
CursorLoader loader = new CursorLoader(this, uri, projection, null, null, null);
ICursor cursor = (ICursor)loader.LoadInBackground();
if (cursor != null)
{
    if (cursor.MoveToFirst ())
    {
        Console.WriteLine(cursor.GetString (cursor.GetColumnIndex (projection [0])));
    }
}
```

## <a name="navigating-to-the-user-profile"></a>Navigation vers le profil utilisateur

Enfin, pour accéder au profil utilisateur, créez une intention avec une `ActionView` action et un `ContactsContract.Profile.ContentUri` , puis passez-la à `StartActivity` la méthode comme suit:

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

Quand vous exécutez le code ci-dessus, le profil utilisateur s’affiche comme illustré dans la capture d’écran suivante:

[![Capture d’écran du profil affichant le profil utilisateur John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

L’utilisation du profil utilisateur est similaire à l’interaction avec d’autres données dans Android, et offre un niveau supplémentaire de personnalisation de l’appareil.

## <a name="related-links"></a>Liens associés

- [ContactsProviderDemo (sample)](https://developer.xamarin.com/samples/monodroid/ContactsProviderDemo/)
- [Présentation du sandwich glacé](http://www.android.com/about/ice-cream-sandwich/)
- [Plateforme Android 4,0](https://developer.android.com/sdk/android-4.0.html)
