---
title: 'Xamarin.Essentials: Partager'
description: La classe share dans Xamarin.Essentials permet à une application de partager des données telles que du texte et des liens Web vers d’autres applications sur l’appareil.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ef4c9961e7e1fac20084247f4c85e87b79bcc427
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801931"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Partager

La classe **Share** permet à une application de partager des données comme du texte et des liens web avec d’autres applications sur l’appareil.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Utilisation de Share

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité Partager appelle la méthode `RequestAsync` avec une charge utile de demande de données qui comprend les informations à partager avec d’autres applications. Le texte et l’URI peuvent être mélangés, et chaque plateforme gère le filtrage en fonction du contenu.

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

L’interface utilisateur de partage avec une application externe s’affiche quand la requête est effectuée :

![Partager](images/share.png)

## <a name="files"></a>Files

Cette fonctionnalité permet à une application de partager des fichiers avec d’autres applications sur l’appareil. Xamarin.Essentialsdétecte automatiquement le type de fichier (MIME) et demande un partage. Chaque plateforme peut prendre uniquement en charge certaines extensions de fichiers spécifiques.

Voici un exemple d’écriture de texte sur le disque et de partage avec d’autres applications :

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="presentation-location"></a>Emplacement de la présentation

Lors de la demande d’un partage sur iPados, vous avez la possibilité d’être présent dans un contrôle Pop-out. Vous pouvez spécifier l’emplacement à l’aide de la `PresentationSourceBounds` propriété :

```csharp
await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file),
    PresentationSourceBounds = DeviceInfo.Platform== DevicePlatform.iOS && DeviceInfo.Idiom == DeviceIdiom.Tablet
                            ? new System.Drawing.Rectangle(0, 20, 0, 0)
                            : System.Drawing.Rectangle.Empty
});
```

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

- La propriété `Subject` permet d’indiquer l’objet souhaité du message.

# <a name="ios"></a>[iOS](#tab/ios)

- `Subject` ne sera pas utilisé.
- `Title` ne sera pas utilisé.

# <a name="uwp"></a>[UWP](#tab/uwp)

- `Title` correspond par défaut au nom de l’application, si aucune valeur n’est définie.
- `Subject` ne sera pas utilisé.

-----

## <a name="api"></a>API

- [Code source de Share](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Share)
- [Documentation de l’API Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
