---
title: 'Xamarin.Essentials: Capture d’écran'
description: Ce document décrit la classe de capture d’écran dans Xamarin.Essentials , qui vous permet de capturer l’écran actuel affiché de l’application.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 085da722aa2e893f97efb1c89f20b03da330ac3e
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414790"
---
# <a name="no-locxamarinessentials-screenshot"></a>Xamarin.Essentials: Capture d’écran

La classe de **capture d’écran** vous permet d’effectuer une capture de l’écran actuel de l’application.

![API de la version préliminaire](~/media/shared/preview.png)


## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenshot"></a>Utilisation de la capture d’écran

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Appelez ensuite `CaptureAsync` pour prendre une capture d’écran de l’écran actuel de l’application en cours d’exécution. Cela revient à retourner un `ScreenshotResult` qui peut être utilisé pour obtenir `Width` , `Height` et un `Stream` de la capture d’écran.


```csharp
async Task CaptureScreenshot()
{
    var screenshot = await Screenshot.CaptureAsync();
    var stream = await screenshot.OpenReadAsync();

    Image = ImageSource.FromStream(() => stream);
}
```


## <a name="api"></a>API

- [Capture d’écran du code source](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Screenshot)
- [Documentation de l’API de capture d’écran](xref:Xamarin.Essentials.Screenshot)
