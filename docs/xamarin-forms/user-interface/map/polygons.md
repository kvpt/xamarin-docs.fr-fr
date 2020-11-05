---
title: Xamarin.Forms Mapper des polygones, des polylignes et des cercles
description: Cet article explique comment créer des polygones, des polylignes et des cercles sur une Xamarin.Forms instance de carte.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1cb0b229af4ac819b33602e2249f20d308ec111c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374782"
---
# <a name="no-locxamarinforms-map-polygons-and-polylines"></a>Xamarin.Forms Mapper des polygones et des polylignes

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

`Polygon``Polyline`les éléments, et `Circle` vous permettent de mettre en surbrillance des zones spécifiques sur une carte. Un `Polygon` est une forme entièrement incluse qui peut avoir un trait et une couleur de remplissage. Un `Polyline` est une ligne qui ne délimite pas complètement une zone. Un `Circle` met en surbrillance une zone circulaire de la carte :

[![« Capture d’écran d’un polygone de carte et polyligne, sur iOS et Android »](polygons-images/polygon-polyline.png "Polygone et polyligne sur une carte")](polygons-images/polygon-polyline-large.png#lightbox "Polygone et polyligne sur une carte") 
 [ ![« Capture d’écran d’un cercle de carte, sur iOS et Android »](polygons-images/circle.png "Cercle sur une carte")](polygons-images/circle-large.png#lightbox "Cercle sur une carte")

Les `Polygon` `Polyline` classes, et `Circle` dérivent de la `MapElement` classe, qui expose les propriétés pouvant être liées suivantes :

- `StrokeColor` est un `Color` objet qui détermine la couleur de ligne.
- `StrokeWidth` est un `float` objet qui détermine la largeur de ligne.

La `Polygon` classe définit une propriété pouvant être liée supplémentaire :

- `FillColor` est un `Color` objet qui détermine la couleur d’arrière-plan du polygone.

En outre, les `Polygon` `Polyline` classes et définissent toutes deux une `GeoPath` propriété, qui est une liste d' [`Position`](xref:Xamarin.Forms.Maps.Position) objets qui spécifient les points de la forme.

La `Circle` classe définit les propriétés pouvant être liées suivantes :

- `Center` est un [`Position`](xref:Xamarin.Forms.Maps.Position) objet qui définit le centre du cercle, en latitude et en longitude.
- `Radius` est un [`Distance`](xref:Xamarin.Forms.Maps.Distance) objet qui définit le rayon du cercle en mètres, en kilomètres ou en miles.
- `FillColor` est une `Color` propriété qui détermine la couleur dans le périmètre du cercle.

> [!NOTE]
> Si la `StrokeColor` propriété n’est pas spécifiée, le trait est noir par défaut. Si la `FillColor` propriété n’est pas spécifiée, le remplissage est transparent par défaut. Par conséquent, si aucune propriété n’est spécifiée, la forme aura un contour noir sans remplissage.

## <a name="create-a-polygon"></a>Créer un polygone

Un `Polygon` objet peut être ajouté à un mappage en l’instanciant et en l’ajoutant à la collection de la carte `MapElements` . Cela peut être accompli en XAML de la façon suivante :

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

Les `StrokeColor` `StrokeWidth` Propriétés et sont spécifiées pour personnaliser le contour du polygone. La `FillColor` valeur de la propriété correspond à la `StrokeColor` valeur de la propriété, mais elle a une valeur alpha spécifiée pour la rendre transparente, ce qui permet à la carte sous-jacente d’être visible via la forme. La `GeoPath` propriété contient une liste d' `Position` objets définissant les coordonnées géographiques des points de polygone. Un `Polygon` objet est rendu sur le mappage une fois qu’il a été ajouté à la `MapElements` collection de `Map` .

> [!NOTE]
> Un `Polygon` est une forme entièrement insérée. Le premier et le dernier point seront automatiquement connectés s’ils ne correspondent pas.

## <a name="create-a-polyline"></a>Créer une polyligne

Un `Polyline` objet peut être ajouté à un mappage en l’instanciant et en l’ajoutant à la collection de la carte `MapElements` . Cela peut être accompli en XAML de la façon suivante :

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

Les `StrokeColor` `StrokeWidth` Propriétés et sont spécifiées pour personnaliser la ligne. La `GeoPath` propriété contient une liste d' `Position` objets définissant les coordonnées géographiques des points de polyligne. Un `Polyline` objet est rendu sur le mappage une fois qu’il a été ajouté à la `MapElements` collection de `Map` .

## <a name="create-a-circle"></a>Créer un cercle

Un `Circle` objet peut être ajouté à un mappage en l’instanciant et en l’ajoutant à la collection de la carte `MapElements` . Cela peut être accompli en XAML de la façon suivante :

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
      <maps:Map>
          <maps:Map.MapElements>
              <maps:Circle StrokeColor="#88FF0000"
                           StrokeWidth="8"
                           FillColor="#88FFC0CB">
                  <maps:Circle.Center>
                      <maps:Position>
                          <x:Arguments>
                              <x:Double>37.79752</x:Double>
                              <x:Double>-122.40183</x:Double>
                          </x:Arguments>
                      </maps:Position>
                  </maps:Circle.Center>
                  <maps:Circle.Radius>
                      <maps:Distance>
                          <x:Arguments>
                              <x:Double>250</x:Double>
                          </x:Arguments>
                      </maps:Distance>
                  </maps:Circle.Radius>
              </maps:Circle>             
          </maps:Map.MapElements>
          ...
      </maps:Map>
</ContentPage>
```

Le code C# équivalent est :

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map();

// Instantiate a Circle
Circle circle = new Circle
{
    Center = new Position(37.79752, -122.40183),
    Radius = new Distance(250),
    StrokeColor = Color.FromHex("#88FF0000"),
    StrokeWidth = 8,
    FillColor = Color.FromHex("#88FFC0CB")
};

// Add the Circle to the map's MapElements collection
map.MapElements.Add(circle);
```

L’emplacement du `Circle` sur la carte est déterminé par la valeur des `Center` `Radius` Propriétés et. La `Center` propriété définit le centre du cercle, en latitude et en longitude, tandis que la `Radius` propriété définit le rayon du cercle en mètres. Les `StrokeColor` `StrokeWidth` Propriétés et sont spécifiées pour personnaliser le contour du cercle. La `FillColor` valeur de la propriété spécifie la couleur dans le périmètre du cercle. Les deux valeurs de couleur spécifient un canal alpha, ce qui permet d’afficher la carte sous-jacente à travers le cercle. L' `Circle` objet est rendu sur le mappage une fois qu’il a été ajouté à la `MapElements` collection de `Map` .

> [!NOTE]
> La `GeographyUtils` classe a une `ToCircumferencePositions` méthode d’extension qui convertit un `Circle` objet (qui définit `Center` et les valeurs de `Radius` propriété) en une liste d' `Position` objets qui composent les coordonnées de latitude et de longitude du périmètre du cercle.

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](/samples/xamarin/xamarin-forms-samples/workingwithmaps)