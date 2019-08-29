---
title: 'Xamarin.Essentials: Messagerie'
description: La classe Email de Xamarin.Essentials permet à une application d’ouvrir l’application de messagerie par défaut en utilisant des informations spécifiques, notamment l’objet, le corps et les destinataires (À, CC, CCI).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: cccbe5f539e2807e749433623e938438e67965e8
ms.sourcegitcommit: 9a46ee759ec4a738da348e8f8904d0f482ef0f25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060093"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Messagerie

La classe **Email** permet à une application d’ouvrir l’application de messagerie par défaut en utilisant des informations spécifiques, notamment l’objet, le corps et les destinataires (À, CC, CCI).

## <a name="get-started"></a>Prise en main

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

Cette fonctionnalité permet à une application d’envoyer des fichiers par e-mail dans des clients de messagerie sur l’appareil. Xamarin.Essentials détectera automatiquement le type de fichier (MIME) et demandera que le fichier soit ajouté en tant que pièce jointe. Chaque client de messagerie est différent et susceptible de ne prendre en charge que certaines extensions de fichiers spécifiques, voire aucune.

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

Les clients de messagerie pour Android ne prennent pas tous en charge `Html`, car il n’y a aucun moyen de le détecter. Nous vous recommandons d’utiliser `PlainText` pour envoyer des e-mails.

# <a name="iostabios"></a>[iOS](#tab/ios)

Aucune différence entre les plateformes.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Prend uniquement en charge `PlainText` comme `BodyFormat`, la tentative d’envoi de `Html` lèvera une `FeatureNotSupportedException`.

Tous les clients de messagerie ne prennent pas en charge l’envoi de pièces jointes. Pour plus d’informations, consultez la [documentation](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email) .

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité d’e-mail](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentation sur l’API d’e-mail](xref:Xamarin.Essentials.Email)
