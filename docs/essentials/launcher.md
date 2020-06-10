---
title : « Xamarin.Essentials Launcher » Description : « la classe du lanceur dans Xamarin.Essentials permet à une application d’ouvrir un URI par le système ».
ms. AssetID : BABF40CC-8BEE-43FD-BE12-6301DF27DD33 Author : jamesmontemagno ms. Custom : vidéo ms. Author : Jamont ms. Date : 08/20/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Lanceur

La classe **Lanceur** permet à une application d’ouvrir un URI par le système. Elle est souvent utilisée en cas de lien profond avec les schémas d’URI personnalisés d’une autre application. Si vous souhaitez ouvrir le navigateur sur un site web, reportez-vous à l’API **[Navigateur](open-browser.md)**.

## <a name="get-started"></a>Prendre en main

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

### <a name="additional-platform-setup"></a>Configuration de plateforme supplémentaire

# <a name="android"></a>[Android](#tab/android)

Aucune configuration supplémentaire.

# <a name="ios"></a>[iOS](#tab/ios)

Dans iOS 9 et versions ultérieures, Apple applique les schémas qu’une application peut interroger. Pour spécifier les schémas que vous souhaitez utiliser, vous devez spécifier `LSApplicationQueriesSchemes` dans votre `Info.plist` fichier.

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune configuration supplémentaire.

-----

## <a name="files"></a>Fichiers

Cette fonctionnalité permet à une application de demander à d’autres applications d’ouvrir et d’afficher un fichier. Xamarin.Essentialsdétecte automatiquement le type de fichier (MIME) et demande l’ouverture du fichier.

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

# <a name="android"></a>[Android](#tab/android)

La tâche retournée à partir de `CanOpenAsync` se termine immédiatement.

# <a name="ios"></a>[iOS](#tab/ios)

Si l’application de destination sur cet appareil n’a jamais été ouverte par `OpenAsync` à partir de votre application auparavant, iOS invite l’utilisateur une fois à autoriser votre application à l’ouvrir.

La tâche retournée à partir de `CanOpenAsync` se termine immédiatement.

Plus d’informations sur l’implémentation d’iOS sont disponibles [ici](xref:UIKit.UIApplication.CanOpenUrl*)

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

## <a name="api"></a>API

- [Code source de Lanceur](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentation de l’API Lanceur](xref:Xamarin.Essentials.Launcher)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Launcher-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
