---
title: 'Xamarin.Essentials: Contacts'
description: La classe contacts dans Xamarin.Essentials permet à un utilisateur de choisir un contact et de récupérer des informations le concernant.
ms.assetid: 02280c42-720a-44c3-979e-4818a20c9821
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd239a8dcf192c0bdbc6265769208f4fc989bbbe
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434485"
---
# <a name="no-locxamarinessentials-contacts"></a>Xamarin.Essentials: Contacts

La classe **contacts** permet à un utilisateur de choisir un contact et de récupérer des informations le concernant.

![API de la version préliminaire](~/media/shared/preview.png)

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **contacts** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

L’autorisation `ReadContacts` est obligatoire, et doit être configurée dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadContacts)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** , puis ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" /> />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous le **manifeste Android** , recherchez la zone **autorisations requises :** , puis cochez cette autorisation. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Dans, `Info.plist` Ajoutez les clés suivantes :

```xml
<key>NSContactsUsageDescription</key>
<string>This app needs access to contacts to pick a contact and get info.</string>
```

Veillez à mettre à jour `<string>` dans la description qui est spécifique à votre application, car elle sera affichée à vos utilisateurs.

# <a name="uwp"></a>[UWP](#tab/uwp)

Dans les `Package.appxmanifest` **fonctionnalités** sous, assurez-vous que la `Contact` fonctionnalité est activée.

-----

## <a name="picking-a-contact"></a>Sélection d’un contact

En appelant `Contacts.PickContactAsync()` la boîte de dialogue contact s’affiche et permet à l’utilisateur de recevoir des informations sur l’utilisateur.


```csharp
try
{
    var contact = await Contacts.PickContactAsync();

    if(contact == null)
        return;

    var name = contact.Name;
    var contactType = contact.ContactType; // Unknown, Personal, Work
    var numbers = contact.Numbers; // List of phone numbers
    var emails = contact.Emails; // List of email addresses 
    
}
catch (Exception ex)
{
    // Handle exception here.
}
```


## <a name="api"></a>API

- [Code source des contacts](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Contacts)
- [Documentation sur l’API contacts](xref:Xamarin.Essentials.Contacts)
