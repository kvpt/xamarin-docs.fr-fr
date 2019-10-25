---
title: Polygones et polylignes de la carte Xamarin. Forms
description: Cet article explique comment créer des polygones et des polylignes sur une instance de carte Xamarin. Forms.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/20/2019
ms.openlocfilehash: 42c00a060e0477aff4ea02f6213fa751b2adf4dd
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810496"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Polygones et polylignes de la carte Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[![« démonstration de polygone et de polyligne sur iOS et Android »](polygons-images/polygon-app-cropped.png)](polygons-images/polygon-app.png#lightbox)

les éléments `Polygon` et `Polyline` vous permettent de mettre en surbrillance des zones spécifiques sur une carte. Une `Polygon` est une forme entièrement incluse qui peut avoir un trait et une couleur de remplissage. Une `Polyline` est une ligne qui ne délimite pas complètement une zone.

> [!NOTE]
> Des exemples de `Polygon` et `Polyline` se trouvent sur le **PolygonsPage** dans l’exemple de projet.

Les classes `Polygon` et `Polyline` dérivent de `MapElement`, qui expose les propriétés de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) suivantes :

- `StrokeColor` est une `Color` propriété qui détermine la couleur de la ligne.
- `StrokeWidth` est une propriété `float` qui détermine la largeur de ligne.
- `Geopath` est défini à la fois sur `Polygon` et `Polyline`, et est une liste d’objets [`Position`](xref:Xamarin.Forms.Maps.Position) qui spécifient les points de la forme.

La classe `Polygon` définit une propriété supplémentaire :

- `FillColor` est une propriété `Color` qui détermine la couleur d’arrière-plan du polygone.

> [!NOTE]
> Si la propriété `StrokeColor` n’est pas spécifiée, le trait est noir par défaut. Si la propriété `FillColor` n’est pas spécifiée, le remplissage est transparent par défaut. Par conséquent, si aucune propriété n’est spécifiée, la forme aura un contour noir sans remplissage.

## <a name="create-a-polygon"></a>Créer un polygone

Un objet `Polygon` peut être ajouté à un mappage en l’instanciant et en l’ajoutant à la collection de `MapElements` de la carte. Cela peut être accompli en XAML de la façon suivante :

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polygon StrokeColor="#FF9900"
                           StrokeWidth="8"
                           FillColor="#88FF9900">
                 <maps:Polygon.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6368678</x:Double>
                             <x:Double>-122.137305</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polygon.Geopath>
             </maps:Polygon>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

Le code C# équivalent est :

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

Les propriétés `StrokeColor` et `StrokeWidth` sont spécifiées pour personnaliser le contour du polygone. La valeur de la propriété `FillColor` correspond à la valeur de la propriété `StrokeColor`, mais elle a une valeur alpha spécifiée pour la rendre transparente, ce qui permet à la carte sous-jacente d’être visible via la forme. La propriété `GeoPath` contient une liste d’objets `Position` définissant les coordonnées géographiques des points de polygone. Un objet `Polygon` est rendu sur le mappage une fois qu’il a été ajouté à la collection `MapElements` du `Map`.

> [!NOTE]
> Une `Polygon` est une forme entièrement comprise. Le premier et le dernier point seront automatiquement connectés s’ils ne correspondent pas.

## <a name="create-a-polyline"></a>Créer une polyligne

Un objet `Polyline` peut être ajouté à un mappage en l’instanciant et en l’ajoutant à la collection de `MapElements` de la carte. Cela peut être accompli en XAML de la façon suivante :

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
         <maps:Map.MapElements>
             <maps:Polyline StrokeColor="Blue"
                            StrokeWidth="12">
                 <maps:Polyline.Geopath>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>47.6381401</x:Double>
                             <x:Double>-122.1317367</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     ...
                 </maps:Polyline.Geopath>
             </maps:Polyline>
         </maps:Map.MapElements>
     </maps:Map>
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

Les propriétés `StrokeColor` et `StrokeWidth` sont spécifiées pour personnaliser la ligne. La propriété `GeoPath` contient une liste d’objets `Position` qui définissent les coordonnées géographiques des points de polyligne. Un objet `Polyline` est rendu sur le mappage une fois qu’il a été ajouté à la collection `MapElements` du `Map`.

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
