---
title: Contacts et ContactsUI dans Xamarin. iOS
description: Cet article traite de l’utilisation des nouvelles infrastructures d’interface utilisateur contacts et contacts dans une application Xamarin. iOS. Ces infrastructures remplacent le carnet d’adresses existant et l’interface utilisateur du carnet d’adresses utilisé dans les versions précédentes d’iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 9a5ca5988bc5bf43af4837aa689c89a016829282
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939982"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contacts et ContactsUI dans Xamarin. iOS

_Cet article traite de l’utilisation des nouvelles infrastructures d’interface utilisateur contacts et contacts dans une application Xamarin. iOS. Ces infrastructures remplacent le carnet d’adresses existant et l’interface utilisateur du carnet d’adresses utilisé dans les versions précédentes d’iOS._

Avec l’introduction d’iOS 9, Apple a publié deux nouveaux frameworks, `Contacts` et `ContactsUI` , qui remplacent les infrastructures d’interface utilisateur du carnet d’adresses et du carnet d’adresses existantes utilisées par IOS 8 et les versions antérieures.

Les deux nouvelles infrastructures contiennent les fonctionnalités suivantes :

- [**Contacts**](#contacts) : permet d’accéder aux données de la liste de contacts de l’utilisateur.
  Étant donné que la plupart des applications requièrent uniquement un accès en lecture seule, cette infrastructure a été optimisée pour l’accès en lecture seule thread-safe.

- [**ContactsUI**](#contactsui) : fournit des éléments d’interface utilisateur Xamarin. IOS pour afficher, modifier, sélectionner et créer des contacts sur des appareils iOS.

[![Exemple de feuille de contact sur un appareil iOS](contacts-images/add01.png)](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Les `AddressBook` infrastructures et existantes `AddressBookUI` utilisées par IOS 8 (et les versions antérieures) ont été dépréciées dans iOS 9 et doivent être remplacées par les nouvelles et nouvelles infrastructures le plus `Contacts` `ContactsUI` rapidement possible pour toute application Xamarin. iOS existante. Les nouvelles applications doivent être écrites sur les nouvelles infrastructures.

Dans les sections suivantes, nous allons examiner ces nouvelles infrastructures et comment les implémenter dans une application Xamarin. iOS.

<a name="contacts"></a>

## <a name="the-contacts-framework"></a>L’infrastructure des contacts

L’infrastructure de contacts fournit un accès Xamarin. iOS aux informations de contact de l’utilisateur. Étant donné que la plupart des applications requièrent uniquement un accès en lecture seule, cette infrastructure a été optimisée pour l’accès en lecture seule thread-safe.

<a name="Contact_Objects"></a>

### <a name="contact-objects"></a>Objets contact

La `CNContact` classe fournit un accès en lecture seule thread-safe aux propriétés d’un contact, telles que le nom, l’adresse ou les numéros de téléphone. `CNContact`fonctionne comme un `NSDictionary` et contient plusieurs collections de propriétés en lecture seule (telles que des adresses ou des numéros de téléphone) :

[![Vue d’ensemble des objets contact](contacts-images/contactobjects.png)](contacts-images/contactobjects.png#lightbox)

Pour toute propriété pouvant avoir plusieurs valeurs (telles que l’adresse de messagerie ou les numéros de téléphone), elles sont représentées sous la forme d’un tableau d' `NSLabeledValue` objets. `NSLabeledValue`est un tuple thread-safe constitué d’un jeu d’étiquettes et de valeurs en lecture seule où l’étiquette définit la valeur pour l’utilisateur (par exemple, la messagerie personnelle ou professionnelle). L’infrastructure de contacts fournit une sélection d’étiquettes prédéfinies (par le biais des `CNLabelKey` `CNLabelPhoneNumberKey` classes statiques et) que vous pouvez utiliser dans votre application, ou vous avez la possibilité de définir des légendes personnalisées pour vos besoins.

Pour toute application Xamarin. iOS qui doit ajuster les valeurs d’un contact existant (ou en créer d’autres), utilisez la `NSMutableContact` version de la classe et ses sous-classes (telles que `CNMutablePostalAddress` ).

Par exemple, le code suivant crée un nouveau contact et l’ajoute à la collection de contacts de l’utilisateur :

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Si ce code est exécuté sur un appareil iOS 9, un nouveau contact est ajouté au regroupement de l’utilisateur. Par exemple :

[![](contacts-images/add01.png "A new contact added to the user's collection")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Mise en forme et localisation des contacts

L’infrastructure de contacts contient plusieurs objets et méthodes qui peuvent vous aider à mettre en forme et à localiser du contenu à l’utilisateur. Par exemple, le code suivant formate correctement un nom de contact et une adresse postale pour l’affichage :

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Pour les étiquettes de propriété que vous afficherez dans l’interface utilisateur de votre application, l’infrastructure de contacts possède des méthodes pour la localisation de ces chaînes également. Là encore, il est basé sur les paramètres régionaux actuels du périphérique iOS sur lequel l’application est exécutée. Par exemple :

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Récupération des contacts existants

En utilisant une instance de la `CNContactStore` classe, vous pouvez extraire les informations de contact de la base de données des contacts de l’utilisateur. `CNContactStore`Contient toutes les méthodes nécessaires à l’extraction ou à la mise à jour des contacts et des groupes à partir de la base de données. Étant donné que ces méthodes sont synchrones, il est recommandé de les exécuter sur un thread d’arrière-plan pour éviter de bloquer l’interface utilisateur.

En utilisant des prédicats (générés à partir de la `CNContact` classe), vous pouvez filtrer les résultats retournés lors de l’extraction des contacts de la base de données. Pour extraire uniquement les contacts qui contiennent la chaîne `Appleseed` , utilisez le code suivant :

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Les prédicats génériques et composés ne sont pas pris en charge par l’infrastructure de contacts.

Par exemple, pour limiter l’extraction uniquement aux propriétés **GivenName** et **familyName** du contact, utilisez le code suivant :

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Enfin, pour rechercher la base de données et retourner les résultats, utilisez le code suivant :

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Si ce code a été exécuté après l’exemple que nous avons créé dans la section de l' **objet contacts** ci-dessus, il retourne le contact « John Appleseed » que nous venons de créer.

### <a name="contact-access-privacy"></a>Confidentialité de l’accès aux contacts

Étant donné que les utilisateurs finaux peuvent accorder ou refuser l’accès à leurs informations de contact pour chaque application, la première fois que vous effectuez un appel au `CNContactStore` , une boîte de dialogue s’affiche pour leur demander d’autoriser l’accès à votre application.

La demande d’autorisation ne sera présentée qu’une seule fois, lors de la première exécution de l’application, et les exécutions ou appels ultérieurs à `CNContactStore` utiliseront l’autorisation que l’utilisateur a sélectionnée à ce moment-là.

Vous devez concevoir votre application afin qu’elle gère correctement l’utilisateur qui refuse l’accès à sa base de données de contacts.

#### <a name="fetching-partial-contacts"></a>Récupération de contacts partiels

Un _contact partiel_ est un contact que seules certaines des propriétés disponibles ont été extraites du magasin de contacts pour. Si vous essayez d’accéder à une propriété qui n’a pas été extraite précédemment, une exception est générée.

Vous pouvez facilement vérifier si un contact donné possède la propriété souhaitée à l’aide `IsKeyAvailable` `AreKeysAvailable` des méthodes ou de l' `CNContact` instance. Par exemple :

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> Les `GetUnifiedContact` `GetUnifiedContacts` méthodes et de la `CNContactStore` classe retournent _uniquement_ un contact partiel limité aux propriétés demandées à partir des clés FETCH fournies.

### <a name="unified-contacts"></a>Contacts unifiés

Un utilisateur peut avoir différentes sources d’informations de contact pour une seule personne dans sa base de données de contacts (par exemple, iCloud, Facebook ou Google Mail). Dans les applications iOS et OS X, ces informations de contact sont automatiquement liées et affichées à l’utilisateur sous la forme d’un contact unique et _unifié_:

[![Vue d’ensemble des contacts unifiés](contacts-images/unified01.png)](contacts-images/unified01.png#lightbox)

Ce contact unifié est une vue temporaire en mémoire des informations de contact de lien qui recevra son propre identificateur unique (qui doit être utilisé pour récupérer à zéro le contact, le cas échéant). Par défaut, l’infrastructure des contacts retourne un contact unifié chaque fois que cela est possible.

### <a name="creating-and-updating-contacts"></a>Création et mise à jour des contacts

Comme nous l’avons vu dans la section [objets de contact](#Contact_Objects) ci-dessus, vous utilisez un `CNContactStore` et une instance d’un `CNMutableContact` pour créer des contacts qui sont ensuite écrits dans la base de données de contacts de l’utilisateur à l’aide d’un `CNSaveRequest` :

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

Un `CNSaveRequest` peut également être utilisé pour mettre en cache plusieurs modifications de contact et de groupe en une seule opération et traiter par lot ces modifications dans `CNContactStore` .

Pour mettre à jour un contact non mutable obtenu à partir d’une opération d’extraction, vous devez d’abord demander une copie mutable que vous pouvez ensuite modifier et enregistrer dans le magasin de contacts. Par exemple :

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Contacter les notifications de modification

Chaque fois qu’un contact est modifié, le magasin de contacts publie `CNContactStoreDidChangeNotification` dans le centre de notifications par défaut. Si vous avez mis en cache ou si vous affichez actuellement des contacts, vous devez actualiser ces objets à partir du magasin de contacts ( `CNContactStore` ).

### <a name="containers-and-groups"></a>Conteneurs et groupes

Les contacts d’un utilisateur peuvent exister localement sur l’appareil de l’utilisateur ou en tant que contacts synchronisés sur l’appareil à partir d’un ou de plusieurs comptes de serveur (tels que Facebook ou Google). Chaque pool de contacts a son propre _conteneur_ et un contact donné ne peut exister que dans un seul conteneur.

[![Vue d’ensemble des conteneurs et des groupes](contacts-images/containers01.png)](contacts-images/containers01.png#lightbox)

Certains conteneurs autorisent la réorganisation des contacts dans un ou plusieurs _groupes_ ou _sous-groupes_. Ce comportement dépend du magasin de stockage pour un conteneur donné. Par exemple, iCloud a un seul conteneur, mais peut avoir de nombreux groupes (mais pas de sous-groupes). Microsoft Exchange en revanche, ne prend pas en charge les groupes, mais peut avoir plusieurs conteneurs (un pour chaque dossier Exchange).

[![Chevauchement dans les conteneurs et les groupes](contacts-images/containers02.png)](contacts-images/containers02.png#lightbox)

<a name="contactsui"></a>

## <a name="the-contactsui-framework"></a>Framework ContactsUI

Dans les cas où votre application n’a pas besoin de présenter une interface utilisateur personnalisée, vous pouvez utiliser l’infrastructure ContactsUI pour présenter les éléments d’interface utilisateur permettant d’afficher, de modifier, de sélectionner et de créer des contacts dans votre application Xamarin. iOS.

En utilisant les contrôles intégrés d’Apple, vous réduisez non seulement la quantité de code que vous devez créer pour prendre en charge les contacts dans votre application Xamarin. iOS, mais vous présentez une interface cohérente aux utilisateurs de l’application.

### <a name="the-contact-picker-view-controller"></a>Contrôleur d’affichage du sélecteur de contacts

Le contrôleur d’affichage du sélecteur de contacts ( `CNContactPickerViewController` ) gère l’affichage du sélecteur de contact standard qui permet à l’utilisateur de sélectionner un contact ou une propriété de contact dans la base de données de contacts de l’utilisateur. L’utilisateur peut sélectionner un ou plusieurs contacts (en fonction de son utilisation) et le contrôleur d’affichage du sélecteur de contact ne demande pas d’autorisation avant d’afficher le sélecteur.

Avant d’appeler la `CNContactPickerViewController` classe, vous définissez les propriétés que l’utilisateur peut sélectionner et définir des prédicats pour contrôler l’affichage et la sélection des propriétés de contact.

Utilisez une instance de la classe qui hérite de `CNContactPickerDelegate` pour répondre à l’interaction de l’utilisateur avec le sélecteur. Par exemple :

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Pour permettre à l’utilisateur de sélectionner une adresse e-mail à partir des contacts dans sa base de données, vous pouvez utiliser le code suivant :

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>Le contrôleur d’affichage des contacts

La classe contact View Controller ( `CNContactViewController` ) fournit un contrôleur pour présenter une vue de contact standard à l’utilisateur final. La vue contact peut afficher de nouveaux contacts inconnus ou existants et le type doit être spécifié avant que la vue ne s’affiche en appelant le constructeur statique correct ( `FromNewContact` , `FromUnknownContact` , `FromContact` ). Par exemple :

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Résumé

Cet article a décrit en détail l’utilisation des frameworks d’interface utilisateur contact et contact dans une application Xamarin. iOS. Tout d’abord, il a abordé les différents types d’objets que l’infrastructure de contact fournit et comment vous les utilisez pour créer des contacts existants ou y accéder. Il a également examiné l’infrastructure de l’interface utilisateur de contact pour sélectionner les contacts existants et afficher les informations de contact.

## <a name="related-links"></a>Liens associés

- [Exemple de contacts](https://docs.microsoft.com/samples/xamarin/ios-samples/contacts/)
- [Nouveautés d’iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Informations de référence sur l’infrastructure des contacts](https://developer.apple.com/documentation/contacts?language=objc)
- [Référence du Framework ContactsUI](https://developer.apple.com/documentation/contactsui?language=objc)
