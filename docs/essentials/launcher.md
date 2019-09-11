---
title: 'Xamarin.Essentials : Lanceur Inter-app'
description: La classe Lanceur dans Xamarin.Essentials permet à une application d’ouvrir un URI par le système.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 3e49e12faac1fc93816d9a51646f639046c200c5
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887372"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Lanceur

La classe **Lanceur** permet à une application d’ouvrir un URI par le système. Elle est souvent utilisée en cas de lien profond avec les schémas d’URI personnalisés d’une autre application. Si vous souhaitez ouvrir le navigateur sur un site web, reportez-vous à l’API **[Navigateur](open-browser.md)** .

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>Utilisation de Lanceur

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour utiliser la fonctionnalité Lanceur, appelez la méthode `OpenAsync` et passez un `string` ou `Uri` pour l’ouvrir. Si vous le souhaitez, vous pouvez aussi utiliser la méthode `CanOpenAsync` pour vérifier si le schéma d’URI peut être géré par une application sur l’appareil.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

Cette méthode peut être combinée en un seul appel à `TryOpenAsync`, qui vérifie si le paramètre peut être ouvert et, le cas échéant, l’ouvre.

```csharp
public class LauncherTest
{
    public async Task<bool> OpenRideShareAsync()
    {
        return await Launcher.TryOpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="files"></a>Fichiers

Cette fonctionnalité permet à une application de demander à d’autres applications d’ouvrir et d’afficher un fichier. Xamarin.Essentials détectera automatiquement le type de fichier (MIME) et demandera que le fichier soit ouvert.

Voici un exemple d’écriture de texte sur disque et de demande d’ouverture :

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
```

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="androidtabandroid"></a>[Android](#tab/android)

La tâche retournée à partir de `CanOpenAsync` se termine immédiatement.

# <a name="iostabios"></a>[iOS](#tab/ios)

Si l’application de destination sur cet appareil n’a jamais été ouverte par `OpenAsync` à partir de votre application auparavant, iOS invite l’utilisateur une fois à autoriser votre application à l’ouvrir.

La tâche retournée à partir de `CanOpenAsync` se termine immédiatement.

Plus d’informations sur l’implémentation d’iOS sont disponibles [ici](xref:UIKit.UIApplication.CanOpenUrl*)

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Aucune spécificité pour cette plateforme.

-----

## <a name="api"></a>API

- [Code source de Lanceur](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentation de l’API Lanceur](xref:Xamarin.Essentials.Launcher)
