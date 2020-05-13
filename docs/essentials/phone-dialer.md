---
title: 'Xamarin.Essentials : clavier téléphonique'
description: La classe PhoneDialer de Xamarin.Essentials permet à une application de composer un numéro de téléphone sur le clavier.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
ms.openlocfilehash: a955399b40f26d4a03a4047d56f7bebe3ad5b5c4
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150050"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials : clavier téléphonique

La classe **PhoneDialer** permet à une application de composer un numéro de téléphone sur le clavier.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Utilisation du clavier téléphonique

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité de clavier téléphonique appelle la méthode `Open` pour composer un numéro de téléphone sur le clavier. Quand `Open` est demandé, l’API tente automatiquement d’appliquer le format approprié au numéro en fonction de l’indicatif téléphonique international, s’il est spécifié.

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Code source de la fonctionnalité de clavier téléphonique](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentation sur l’API de clavier téléphonique](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
