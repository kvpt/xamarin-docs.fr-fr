---
title: 'Xamarin.Essentials: Presse-papiers'
description: Ce document décrit la classe Clipboard dans Xamarin.Essentials , qui vous permet de copier et coller du texte dans le presse-papiers du système entre les applications.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d0a984f0f3bf27447e250c12e38fd9adcfb0029f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802456"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Presse-papiers

La classe **Clipboard** vous permet de copier et de coller du texte dans le Presse-papiers système entre les applications.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Utilisation du Presse-papiers

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour vérifier si le **Presse-papiers** contient du texte prêt à être collé :

```csharp
var hasText = Clipboard.HasText;
```

Pour définir du texte dans le **Presse-papiers** :

```csharp
await Clipboard.SetTextAsync("Hello World");
```

Pour lire du texte à partir du **Presse-papiers** :

```csharp
var text = await Clipboard.GetTextAsync();
```

Chaque fois que le contenu du presse-papiers a changé, un événement est déclenché :

```csharp
public class ClipboardTest
{
    public ClipboardTest()
    {
        // Register for clipboard changes, be sure to unsubscribe when needed
        Clipboard.ClipboardContentChanged += OnClipboardContentChanged;
    }

    void OnClipboardContentChanged(object sender, EventArgs    e)
    {
        Console.WriteLine($"Last clipboard change at {DateTime.UtcNow:T}";);
    }
}
```

> [!TIP]
> L’accès au Presse-papiers doit être effectué sur le thread d’interface utilisateur principal. Consultez l’API [MainThread](~/essentials/main-thread.md) pour voir comment appeler des méthodes sur le thread d’interface utilisateur principal.

## <a name="api"></a>API

- [Code source de la fonctionnalité de Presse-papiers](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Clipboard)
- [Documentation sur l’API de Presse-papiers](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
