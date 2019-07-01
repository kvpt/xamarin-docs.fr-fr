---
title: 'Xamarin.Essentials: Presse-papiers'
description: Ce document décrit la classe Clipboard de Xamarin.Essentials, qui vous permet de copier et de coller du texte dans le Presse-papiers système entre les applications.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: c186f5c61bd2fa3df305be92a03135e57e302d02
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352127"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Presse-papiers

La classe **Clipboard** vous permet de copier et de coller du texte dans le Presse-papiers système entre les applications.

## <a name="get-started"></a>Prise en main

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

> [!TIP]
> L’accès au Presse-papiers doit être effectué sur le thread d’interface utilisateur principal. Consultez l’API [MainThread](~/essentials/main-thread.md) pour voir comment appeler des méthodes sur le thread d’interface utilisateur principal.

## <a name="api"></a>API

- [Code source de la fonctionnalité de Presse-papiers](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentation sur l’API de Presse-papiers](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
