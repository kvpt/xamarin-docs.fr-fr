---
title: 'Xamarin.Essentials: E-mail'
description: La classe email dans Xamarin.Essentials permet à une application d’ouvrir l’application de messagerie par défaut avec les informations spécifiées, notamment Subject, corps et Recipients (à, CC, CCI).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 577628237d1e7433c2ad06466a2e804808874108
ms.sourcegitcommit: d8627a500a9b8a07615aef9822c9b1dba2bf6650
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629634"
---
# <a name="no-locxamarinessentials-email"></a>Xamarin.Essentials: E-mail

La classe **Email** permet à une application d’ouvrir l’application de messagerie par défaut en utilisant des informations spécifiques, notamment l’objet, le corps et les destinataires (À, CC, CCI).

Pour accéder à la fonctionnalité de **messagerie** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

Si la version d’Android cible de votre projet est définie sur **Android 11 (API R 30),** vous devez mettre à jour votre manifeste Android avec les requêtes utilisées avec les nouvelles [exigences de visibilité du package](https://developer.android.com/preview/privacy/package-visibility).

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** , puis ajoutez ce qui suit dans le nœud **manifeste**  :

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.SENDTO" />
    <data android:scheme="mailto" />
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

Dans iOS 9 et versions ultérieures, Apple applique les schémas qu’une application peut interroger. Pour demander si l’e-mail est une cible valide, le `mailto` schéma doit être spécifié dans le LSApplicationQueriesSchemes de votre fichier info. plist.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>mailto</string>
</array>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> Pour utiliser l’API de messagerie sur iOS, vous devez l’exécuter sur un appareil physique, sinon une exception sera levée.

## <a name="using-email"></a>Utilisation de l’e-mail

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité d’e-mail appelle la méthode `ComposeAsync` d’un `EmailMessage` contenant des informations sur l’e-mail :

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occurred
        }
    }
}
```

## <a name="file-attachments"></a>Pièces jointes

Cette fonctionnalité permet à une application d’envoyer par courrier électronique des fichiers à des clients de messagerie sur l’appareil. Xamarin.Essentials détecte automatiquement le type de fichier (MIME) et demande le fichier à ajouter en tant que pièce jointe. Chaque client de messagerie est différent et peut uniquement prendre en charge des extensions de fichier spécifiques, ou rien du tout.

Voici un exemple d’écriture de texte sur le disque et d’ajout en tant que pièce jointe à un e-mail :

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

Les clients de messagerie pour Android ne prennent pas tous en charge `Html`, car il n’y a aucun moyen de le détecter. Nous vous recommandons d’utiliser `PlainText` pour envoyer des e-mails.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune différence entre les plateformes.

# <a name="uwp"></a>[UWP](#tab/uwp)

Prend uniquement en charge `PlainText` comme `BodyFormat`, la tentative d’envoi de `Html` lèvera une `FeatureNotSupportedException`.

Tous les clients de messagerie ne prennent pas en charge l’envoi de pièces jointes. Pour plus d’informations, consultez la [documentation](/windows/uwp/contacts-and-calendar/sending-email) .

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité d’e-mail](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Email)
- [Documentation sur l’API Email](xref:Xamarin.Essentials.Email)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Email-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
