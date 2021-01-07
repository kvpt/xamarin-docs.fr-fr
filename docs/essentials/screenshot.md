---
title: 'Xamarin.Essentials: Capture d’écran'
description: Ce document décrit la classe de capture d’écran dans Xamarin.Essentials , qui vous permet de capturer l’écran actuel affiché de l’application.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e907cf06814a5b14678e4584f9aabc56f39bb164
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972264"
---
# <a name="no-locxamarinessentials-screenshot"></a>Xamarin.Essentials: Capture d’écran

La classe de **capture d’écran** vous permet d’effectuer une capture de l’écran actuel de l’application.

## <a name="get-started"></a>Prendre en main

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
