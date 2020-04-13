---
title: Carte Xamarin.Essentials
description: La classe Map dans Xamarin.Essentials permet à une application d’ouvrir l’application de cartes installée à un emplacement spécifique.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: c0875534d88ea5b66b3072c35b9d38894fe98934
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61354569"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials : Carte

La classe **Map** permet à une application d’ouvrir l’application de cartes installée à un emplacement spécifique.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>Utilisation de Map

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité Carte appelle la méthode `OpenAsync` avec le `Location` ou `Placemark` à ouvrir avec des `MapLaunchOptions` facultatif.

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(location, options);
    }
}
```

Lors de l’ouverture avec un `Placemark`, les informations suivantes sont requises :

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Méthodes d’extension

Si vous avez déjà une référence à un `Location` ou `Placemark`, vous pouvez utiliser la méthode d’extension intégrée `OpenMapAsync` avec `MapLaunchOptions` facultatif :

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        await placemark.OpenMapAsync();
    }
}
```

## <a name="directions-mode"></a>Mode Directions

Si vous appelez `OpenMapAsync` sans `MapLaunchOptions`, la carte se lancera à l’emplacement spécifié. Si vous le souhaitez, vous pouvez avoir un itinéraire de navigation calculé à partir de la position actuelle de l’appareil. Pour ce faire, il vous de définir `NavigationMode` sur `MapLaunchOptions` :

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

- `NavigationMode` prend en charge Bicycling (vélo), Driving (voiture) et Walking (marche).

# <a name="ios"></a>[iOS](#tab/ios)

- `NavigationMode` prend en charge Bicycling (vélo), Transit et Walking (marche).

# <a name="uwp"></a>[UWP](#tab/uwp)

- `NavigationMode` prend en charge Bicycling (vélo), Transit et Walking (marche).

--------------

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Android utilise le schéma d’URI `geo:` pour lancer l’application Cartes sur l’appareil. L’utilisateur peut être invité à sélectionner à partir d’une application existante qui prend en charge ce schéma d’URI.  Xamarin.Essentials est testé avec Google Maps, qui prend en charge ce schéma.

# <a name="ios"></a>[iOS](#tab/ios)

Aucun détail d’implémentation spécifique à la plateforme.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucun détail d’implémentation spécifique à la plateforme.

--------------

## <a name="api"></a>API

- [Code source de Map](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Map)
- [Documentation de l’API Map](xref:Xamarin.Essentials.Map)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Maps-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
