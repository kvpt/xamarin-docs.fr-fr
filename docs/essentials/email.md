---
titre : « Xamarin.Essentials : email » Description : « la classe de messagerie dans Xamarin.Essentials permet à une application d’ouvrir l’application de messagerie par défaut avec les informations spécifiées, notamment Subject, corps et Recipients (à, CC, CCI) ».
ms. AssetID : 5FBB6FF0-0E7B-4C29-8F06-91642AF12629 Author : jamesmontemagno ms. Custom : vidéo ms. Author : Jamont ms. Date : 08/20/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-email"></a>Xamarin.Essentials: E-mail

La classe **Email** permet à une application d’ouvrir l’application de messagerie par défaut en utilisant des informations spécifiques, notamment l’objet, le corps et les destinataires (À, CC, CCI).

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

Cette fonctionnalité permet à une application d’envoyer par courrier électronique des fichiers à des clients de messagerie sur l’appareil. Xamarin.Essentialsdétecte automatiquement le type de fichier (MIME) et demande le fichier à ajouter en tant que pièce jointe. Chaque client de messagerie est différent et peut uniquement prendre en charge des extensions de fichier spécifiques, ou rien du tout.

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

Tous les clients de messagerie ne prennent pas en charge l’envoi de pièces jointes. Pour plus d’informations, consultez la [documentation](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email) .

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité d’e-mail](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentation sur l’API Email](xref:Xamarin.Essentials.Email)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Email-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
