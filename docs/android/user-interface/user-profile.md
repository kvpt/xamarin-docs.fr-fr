---
title: Profil utilisateur
ms.prod: xamarin
ms.assetid: 6BB01F75-5E98-49A1-BBA0-C2680905C59D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/22/2018
ms.openlocfilehash: 395f7c477f1f2bdb608aec918f877f6d320d75cc
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292494"
---
# <a name="user-profile"></a>Profil utilisateur

Android prend en charge l’énumération des contacts avec le fournisseur [ContactsContract](xref:Android.Provider.ContactsContract) depuis l’API niveau 5. Par exemple, le fait de répertorier les contacts est aussi simple que l’utilisation de la classe [ContactContracts. contacts](xref:Android.Provider.ContactsContract.Contacts) , comme indiqué dans l’exemple de code suivant :

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

À compter d’Android 4 (niveau d’API 14), la classe [ContactsContact. Profile](xref:Android.Provider.ContactsContract.Profile) est disponible via le fournisseur `ContactsContract`. L' `ContactsContact.Profile` fournit l’accès au profil personnel pour le propriétaire d’un appareil, y compris les données de contact, telles que le nom et le numéro de téléphone du propriétaire de l’appareil.

## <a name="required-permissions"></a>Autorisations requises

Pour lire et écrire des données de contact, les applications doivent demander les autorisations `READ_CONTACTS` et `WRITE_CONTACTS`, respectivement.
En outre, pour lire et modifier le profil utilisateur, les applications doivent demander les autorisations `READ_PROFILE` et `WRITE_PROFILE`.

## <a name="updating-profile-data"></a>Mise à jour des données de profil

Une fois ces autorisations définies, une application peut utiliser des techniques Android normales pour interagir avec les données du profil utilisateur. Par exemple, pour mettre à jour le nom d’affichage du profil, appelez [ContentResolver. Update](xref:Android.Content.ContentResolver.Update*) avec un `Uri` récupéré via la propriété [ContactsContract. Profile. ContentRawContactsUri](xref:Android.Provider.ContactsContract.Profile.ContentRawContactsUri) , comme indiqué ci-dessous :

```csharp
var values = new ContentValues ();
values.Put (ContactsContract.Contacts.InterfaceConsts.DisplayName, "John Doe");

// Update the user profile with the name "John Doe":
ContentResolver.Update (ContactsContract.Profile.ContentRawContactsUri, values, null, null);
```

## <a name="reading-profile-data"></a>Lecture des données du profil

L’émission d’une requête vers [ContactsContact. Profile. ContentUri](xref:Android.Provider.ContactsContract.Profile.ContentUri) lit les données de profil. Par exemple, le code suivant lit le nom complet du profil utilisateur :

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

Enfin, pour accéder au profil utilisateur, créez une intention avec une action `ActionView` et un `ContactsContract.Profile.ContentUri` puis passez-la à la méthode `StartActivity` comme suit :

```csharp
var intent = new Intent (Intent.ActionView,
    ContactsContract.Profile.ContentUri);
StartActivity (intent);
```

Quand vous exécutez le code ci-dessus, le profil utilisateur s’affiche comme illustré dans la capture d’écran suivante :

[Capture d’écran ![du profil affichant le profil utilisateur John Doe](user-profile-images/01-profile-screen-sml.png)](user-profile-images/01-profile-screen.png#lightbox)

L’utilisation du profil utilisateur est similaire à l’interaction avec d’autres données dans Android, et offre un niveau supplémentaire de personnalisation de l’appareil.

## <a name="related-links"></a>Liens connexes

- [ContactsProviderDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/contactsproviderdemo)
