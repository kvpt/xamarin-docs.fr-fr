---
title: 'Xamarin.Essentials: Numéroteur téléphonique'
description: La classe PhoneDialer dans Xamarin.Essentials permet à une application d’ouvrir un numéro de téléphone dans le numéroteur
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9bd281a61fd53ef3f6d0d3d2307f78a218f33cf4
ms.sourcegitcommit: db423d51356cf5a2dfa1b3925204797b1baf3cd9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92734777"
---
# <a name="no-locxamarinessentials-phone-dialer"></a>Xamarin.Essentials: Numéroteur téléphonique

La classe **PhoneDialer** permet à une application de composer un numéro de téléphone sur le clavier.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

# <a name="android"></a>[Android](#tab/android)

Si la version d’Android cible de votre projet est définie sur **Android 11 (API R 30),** vous devez mettre à jour votre manifeste Android avec les requêtes utilisées avec les nouvelles [exigences de visibilité du package](https://developer.android.com/preview/privacy/package-visibility).

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** , puis ajoutez ce qui suit dans le nœud **manifeste**  :

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.DIAL" />
    <data android:scheme="tel"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

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

- [Code source de la fonctionnalité de clavier téléphonique](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/PhoneDialer)
- [Documentation sur l’API de clavier téléphonique](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
