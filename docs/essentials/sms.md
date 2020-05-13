---
title: 'Xamarin.Essentials : SMS'
description: La classe Sms de Xamarin.Essentials permet à une application d’ouvrir l’application SMS par défaut avec un message spécifié à envoyer à un destinataire.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 38651b8e29a2119f777fdbcdd82c9a8277c02497
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149908"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials : SMS

La classe **Sms** permet à une application d’ouvrir l’application SMS par défaut avec un message spécifié à envoyer à un destinataire.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>Utiliser Sms

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour utiliser la fonctionnalité SMS, appelez la méthode `ComposeAsync` avec un `SmsMessage` contenant le destinataire et le corps du message, tous deux facultatifs.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, new []{ recipient });
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

Vous pouvez également transmettre plusieurs destinataires à un `SmsMessage` :

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Code source de Sms](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentation de l’API Sms](xref:Xamarin.Essentials.Sms)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
