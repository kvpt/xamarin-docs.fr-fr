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
ms.openlocfilehash: 0870dd94c15f1bd94d5c6864b3d4caeb96349f32
ms.sourcegitcommit: 83793378b28e8ef8624406309b4ecd41aa1a3a14
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503265"
---
# <a name="no-locxamarinessentials-share"></a>Xamarin.Essentials: Partager

La classe **Share** permet à une application de partager des données comme du texte et des liens web avec d’autres applications sur l’appareil.

## <a name="get-started"></a>Prise en main

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

## <a name="file"></a>Fichier

Cette fonctionnalité permet à une application de partager des fichiers avec d’autres applications sur l’appareil. Xamarin.Essentials détecte automatiquement le type de fichier (MIME) et demande un partage. Chaque plateforme peut prendre uniquement en charge certaines extensions de fichiers spécifiques.

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

## <a name="multiple-files"></a>Fichiers multiples

![API de la version préliminaire](~/media/shared/preview.png)

L’utilisation de l’option partager plusieurs fichiers diffère du fichier unique dans le seul cas où vous pouvez envoyer plusieurs fichiers à la fois :

```csharp
var file1 = Path.Combine(FileSystem.CacheDirectory, "Attachment1.txt");
File.WriteAllText(file, "Content 1");
var file2 = Path.Combine(FileSystem.CacheDirectory, "Attachment2.txt");
File.WriteAllText(file, "Content 2");

await Share.RequestAsync(new ShareMultipleFilesRequest
{
    Title = ShareFilesTitle,
    Files = new ShareFile[] { new ShareFile(file1), new ShareFile(file2) }
});
```

## <a name="presentation-location"></a>Emplacement de la présentation

Lors de la demande d’un partage sur iPados, vous avez la possibilité d’être présent dans un contrôle Pop-out. Cela spécifie l’emplacement où se trouve la liste déroulante et pointe une flèche directement vers. Cet emplacement est souvent le contrôle qui a lancé l’action. Vous pouvez spécifier l’emplacement à l’aide de la `PresentationSourceBounds` propriété :

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

Si vous utilisez Xamarin.Forms , vous pouvez passer un `View` et calculer les limites :


```
public static class ViewHelpers
{
    public static Rectangle GetAbsoluteBounds(this Xamarin.Forms.View element)
    {
        Element looper = element;

        var absoluteX = element.X + element.Margin.Top;
        var absoluteY = element.Y + element.Margin.Left;

        // Add logic to handle titles, headers, or other non-view bars

        while (looper.Parent != null)
        {
            looper = looper.Parent;
            if (looper is Xamarin.Forms.View v)
            {
                absoluteX += v.X + v.Margin.Top;
                absoluteY += v.Y + v.Margin.Left;
            }
        }

        return new Rectangle(absoluteX, absoluteY, element.Width, element.Height);
    }

    public static System.Drawing.Rectangle ToSystemRectangle(this Rectangle rect) =>
        new System.Drawing.Rectangle((int)rect.X, (int)rect.Y, (int)rect.Width, (int)rect.Height);
}
```

Il peut ensuite être utilisé lors de l’appel de `RequstAsync` :

```csharp
public Command<Xamarin.Forms.View> ShareCommand { get; } = new Command<Xamarin.Forms.View>(Share);
async void Share(Xamarin.Forms.View element)
{
    try
    {
        Analytics.TrackEvent("ShareWithFriends");
        var bounds = element.GetAbsoluteBounds();

        await Share.RequestAsync(new ShareTextRequest
        {
            PresentationSourceBounds = bounds.ToSystemRectangle(),
            Title = "Title",
            Text = "Text"
        });
    }
    catch (Exception)
    {
        // Handle exception that share failed
    }
}
```

Vous pouvez passer l’élément appelant lorsque `Command` est déclenché :

```xml
<Button Text="Share"
        Command="{Binding ShareWithFriendsCommand}"
        CommandParameter="{Binding Source={RelativeSource Self}}"/>
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
