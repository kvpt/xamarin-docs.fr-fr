---
title: Xamarin. Forms Map Control
description: Le contrôle de carte est une vue multiplateforme permettant d’afficher et d’annoter des cartes. Il utilise le contrôle de carte natif pour chaque plateforme, en fournissant une expérience de cartes rapide et familière aux utilisateurs.
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 1cfda90360557af1160d421f18807f8b534967a8
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305415"
---
# <a name="xamarinforms-map-control"></a>Xamarin. Forms Map Control

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Le contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) est une vue multiplateforme permettant d’afficher et d’annoter des cartes. Elle utilise le contrôle de carte natif pour chaque plateforme, en fournissant une expérience de cartes rapide et familière aux utilisateurs :

[![Capture d’écran du contrôle de carte, sur iOS et Android](map-images/map-default.png "Commandes de la carte")](map-images/map-default-large.png#lightbox "Commandes de la carte")

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit les propriétés suivantes qui contrôlent l’apparence et le comportement de la carte :

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), de type `bool`, indique si la carte affiche l’emplacement actuel de l’utilisateur.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de type `IEnumerable`, qui spécifie la collection d’éléments `IEnumerable` à afficher.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément de la collection d’éléments affichés.
- `ItemTemplateSelector`, de type [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), qui spécifie le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), de type `bool`détermine si le mappage est autorisé à faire défiler.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), de type `bool`, détermine si la carte est autorisée à effectuer un zoom.
- `MapElements`, de type `IList<MapElement>`, représente la liste des éléments sur la carte, tels que les polygones et les polylignes.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), de type [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), indique le style d’affichage de la carte.
- `MoveToLastRegionOnLayoutChange`, de type `bool`, contrôle si la zone de mappage affichée passe de sa zone actuelle à sa zone définie précédemment quand une modification de disposition se produit.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), de type `IList<Pin>`, représente la liste des broches sur la carte.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), de type [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), retourne la région actuellement affichée de la carte.

Ces propriétés, à l’exception des propriétés `MapElements`, `Pins`et `VisibleRegion`, sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’ils peuvent être des cibles de liaisons de données.

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit également un événement `MapClicked` qui est déclenché lorsque le mappage est activé. L’objet `MapClickedEventArgs` qui accompagne l’événement a une propriété unique nommée `Position`, de type [`Position`](xref:Xamarin.Forms.Maps.Position). Lorsque l’événement est déclenché, la propriété `Position` est définie sur l’emplacement de la carte qui a été taraudé. Pour plus d’informations sur le struct [`Position`](xref:Xamarin.Forms.Maps.Position) , consultez la section [mapper la position et distance](position-distance.md).

Pour plus d’informations sur les propriétés [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)et `ItemTemplateSelector`, consultez [afficher une collection de codes confidentiels](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Afficher une carte

Vous pouvez afficher un [`Map`](xref:Xamarin.Forms.Maps.Map) en l’ajoutant à une mise en page ou à une page :

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Une définition d’espace de noms `xmlns` supplémentaire est requise pour référencer les contrôles Xamarin. Forms. Maps. Dans l’exemple précédent, l’espace de noms `Xamarin.Forms.Maps` est référencé via le mot clé `maps`.

Le code C# équivalent est :

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace WorkingWithMaps
{
    public class MapTypesPageCode : ContentPage
    {
        public MapTypesPageCode()
        {
            Map map = new Map();
            Content = map;
        }
    }
}
```

Cet exemple appelle le constructeur par défaut [`Map`](xref:Xamarin.Forms.Maps.Map) , qui centre la carte à Rome :

[![Capture d’écran du contrôle de carte avec emplacement par défaut, sur iOS et Android](map-images/map-default.png "Contrôle de carte avec emplacement par défaut")](map-images/map-default-large.png#lightbox "Contrôle de carte avec emplacement par défaut")

Un argument [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) peut également être passé à un constructeur [`Map`](xref:Xamarin.Forms.Maps.Map) pour définir le point central et le niveau de zoom de la carte lorsqu’elle est chargée. Pour plus d’informations, consultez [afficher un emplacement spécifique sur une carte](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Types de cartes

La propriété [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) peut être définie sur un membre d’énumération [`MapType`](xref:Xamarin.Forms.Maps.MapType) pour définir le style d’affichage de la carte. L’énumération `MapType` définit les membres suivants :

- `Street` spécifie qu’une carte de rue sera affichée.
- `Satellite` spécifie qu’une carte contenant une image satellite sera affichée.
- `Hybrid` spécifie qu’une carte combinant les données de la rue et du satellite sera affichée.

Par défaut, un [`Map`](xref:Xamarin.Forms.Maps.Map) affichera une carte postale si la propriété [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) n’est pas définie. Vous pouvez également définir la propriété `MapType` sur l’un des membres de l’énumération [`MapType`](xref:Xamarin.Forms.Maps.MapType) :

```xaml
<maps:Map MapType="Satellite" />
```

Le code C# équivalent est :

```csharp
Map map = new Map
{
    MapType = MapType.Satellite
};
```

Les captures d’écran suivantes montrent une [`Map`](xref:Xamarin.Forms.Maps.Map) lorsque la propriété [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) a la valeur `Street`:

[![Capture d’écran du contrôle de carte avec le type de carte de rue, sur iOS et Android](map-images/maptype-street.png "Contrôle Map avec la rue maptype")](map-images/maptype-street-large.png#lightbox "Contrôle de carte avec le type de carte de rue")

Les captures d’écran suivantes montrent une [`Map`](xref:Xamarin.Forms.Maps.Map) lorsque la propriété [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) a la valeur `Satellite`:

[![Capture d’écran du contrôle de carte avec le type de carte satellite, sur iOS et Android](map-images/maptype-satellite.png "Contrôle de carte avec le maptype satellite")](map-images/maptype-satellite-large.png#lightbox "Mapper le contrôle avec le type de carte satellite")

Les captures d’écran suivantes montrent une [`Map`](xref:Xamarin.Forms.Maps.Map) lorsque la propriété [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) a la valeur `Hybrid`:

[![Capture d’écran du contrôle de carte avec le type de carte hybride, sur iOS et Android](map-images/maptype-hybrid.png "Contrôle de carte avec le maptype hybride")](map-images/maptype-hybrid-large.png#lightbox "Contrôle de carte avec le type de carte hybride")

## <a name="display-a-specific-location-on-a-map"></a>Afficher un emplacement spécifique sur une carte

La région d’un mappage à afficher lorsqu’un mappage est chargé peut être définie en passant un argument [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) au constructeur [`Map`](xref:Xamarin.Forms.Maps.Map) :

```xaml
<maps:Map>
    <x:Arguments>
        <maps:MapSpan>
            <x:Arguments>
                <maps:Position>
                    <x:Arguments>
                        <x:Double>36.9628066</x:Double>
                        <x:Double>-122.0194722</x:Double>
                    </x:Arguments>
                </maps:Position>
                <x:Double>0.01</x:Double>
                <x:Double>0.01</x:Double>
            </x:Arguments>
        </maps:MapSpan>
    </x:Arguments>
</maps:Map>
```

Le code C# équivalent est :

```csharp
Position position = new Position(36.9628066, -122.0194722);
MapSpan mapSpan = new MapSpan(position, 0.01, 0.01);
Map map = new Map(mapSpan);
```

Cet exemple crée un objet [`Map`](xref:Xamarin.Forms.Maps.Map) qui affiche la zone spécifiée par l’objet [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . L’objet `MapSpan` est centré sur la latitude et la longitude représentées par un objet [`Position`](xref:Xamarin.Forms.Maps.Position) et s’étend sur les degrés de longitude 0,01 de latitude et 0,01. Pour plus d’informations sur le struct [`Position`](xref:Xamarin.Forms.Maps.Position) , consultez la section [mapper la position et distance](position-distance.md). Pour plus d’informations sur le passage d’arguments en XAML, consultez [passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md).

Le résultat est que, lorsque la carte est affichée, elle est centrée sur un emplacement spécifique et s’étend sur un nombre spécifique de degrés de latitude et de longitude :

[![Capture d’écran du contrôle de carte avec l’emplacement spécifié, sur iOS et Android](map-images/map-region.png "Mapper le contrôle à un emplacement spécifié")](map-images/map-region-large.png#lightbox "Mapper le contrôle à un emplacement spécifié")

## <a name="create-a-mapspan-object"></a>Créer un objet MapSpan

Il existe un certain nombre d’approches pour la création d’objets [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . Une approche courante consiste à fournir les arguments requis au constructeur `MapSpan`. Il s’agit d’une latitude et d’une longitude représentées par un objet [`Position`](xref:Xamarin.Forms.Maps.Position) , et `double` des valeurs qui représentent les degrés de latitude et de longitude qui sont fractionnés par le `MapSpan`. Pour plus d’informations sur le struct [`Position`](xref:Xamarin.Forms.Maps.Position) , consultez la section [mapper la position et distance](position-distance.md).

Il existe également trois méthodes dans la classe [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) qui retournent de nouveaux objets `MapSpan` :

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*) retourne un `MapSpan` avec la même `LongitudeDegrees` que l’instance de la classe de la méthode, et un RADIUS défini par ses arguments `north` et `south`.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*) retourne une `MapSpan` définie par ses arguments [`Position`](xref:Xamarin.Forms.Maps.Position) et [`Distance`](xref:Xamarin.Forms.Maps.Distance) .
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) retourne une `MapSpan` avec le même centre que l’instance de la classe de la méthode, mais avec un rayon multiplié par son argument `double`.

Pour plus d’informations sur le struct [`Distance`](xref:Xamarin.Forms.Maps.Distance) , consultez la section [mapper la position et distance](position-distance.md).

Une fois qu’une [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) a été créée, les propriétés suivantes sont accessibles pour récupérer des données :

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), qui représente le [`Position`](xref:Xamarin.Forms.Maps.Position) dans le centre géographique de la `MapSpan`.
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), qui représente les degrés de latitude qui sont répartis par le `MapSpan`.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), qui représente les degrés de longitude qui sont répartis par le `MapSpan`.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), qui représente le rayon `MapSpan`.

## <a name="move-the-map"></a>Déplacer la carte

La méthode [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) peut être appelée pour modifier la position et le niveau de zoom d’une carte. Cette méthode accepte un argument [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) qui définit la zone de la carte à afficher et son niveau de zoom.

Le code suivant montre un exemple de déplacement de la région affichée sur une carte :

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Zoom sur la carte

Le niveau de zoom d’un [`Map`](xref:Xamarin.Forms.Maps.Map) peut être modifié sans changer son emplacement. Pour ce faire, vous pouvez utiliser l’interface utilisateur du mappage ou par programmation en appelant la méthode [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) avec un argument [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) qui utilise l’emplacement actuel comme [`Position`](xref:Xamarin.Forms.Maps.Position) argument :

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

Dans cet exemple, la méthode [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) est appelée avec un argument [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) qui spécifie l’emplacement actuel de la carte, via la propriété [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) et le niveau de zoom en degrés de latitude et de longitude. Le résultat global est que le niveau de zoom de la carte est modifié, mais que son emplacement ne l’est pas. Une autre approche pour implémenter le zoom sur une carte consiste à utiliser la méthode [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) pour contrôler le facteur de zoom.

> [!IMPORTANT]
> Le zoom d’une carte, qu’il s’agisse d’une interface utilisateur de carte ou d’un programme, requiert que la propriété [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) soit `true`. Pour plus d’informations sur cette propriété, consultez [Disable zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personnaliser le comportement de la carte

Le comportement d’un [`Map`](xref:Xamarin.Forms.Maps.Map) peut être personnalisé en définissant certaines de ses propriétés et en gérant l’événement `MapClicked`.

> [!NOTE]
> Le comportement de mappage supplémentaire customizatin peut être obtenu en créant un convertisseur personnalisé de carte. Pour plus d’informations, consultez [Personnalisation d’une carte Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

### <a name="disable-scroll"></a>Désactiver le défilement

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit une propriété [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) de type `bool`. Par défaut, cette propriété est `true`, ce qui indique que le mappage est autorisé à faire défiler. Quand cette propriété a la valeur `false`, le mappage ne défile pas. L’exemple suivant illustre la définition de cette propriété :

```xaml
<maps:Map HasScrollEnabled="false" />
```

Le code C# équivalent est :

```csharp
Map map = new Map
{
    HasScrollEnabled = false
};
```

### <a name="disable-zoom"></a>Désactiver le zoom

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit une propriété [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) de type `bool`. Par défaut, cette propriété est `true`, ce qui indique que le zoom peut être effectué sur la carte. Lorsque cette propriété a la valeur `false`, le mappage ne peut pas être agrandi. L’exemple suivant illustre la définition de cette propriété :

```xaml
<maps:Map HasZoomEnabled="false" />
```

Le code C# équivalent est :

```csharp
Map map = new Map
{
    HasZoomEnabled = false
};
```

### <a name="show-the-users-location"></a>Afficher l’emplacement de l’utilisateur

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit une propriété [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) de type `bool`. Par défaut, cette propriété est `false`, ce qui indique que le mappage n’affiche pas l’emplacement actuel de l’utilisateur. Lorsque cette propriété a la valeur `true`, la carte affiche l’emplacement actuel de l’utilisateur. L’exemple suivant illustre la définition de cette propriété :

```xaml
<maps:Map IsShowingUser="true" />
```

Le code C# équivalent est :

```csharp
Map map = new Map
{
    IsShowingUser = true
};
```

> [!IMPORTANT]
> Sur iOS, Android et le plateforme Windows universelle, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour avoir été accordé à l’application. Pour plus d’informations, consultez Configuration de la [plateforme](setup.md#platform-configuration).

### <a name="maintain-map-region-on-layout-change"></a>Conserver la zone réactive sur la modification de la disposition

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit une propriété `MoveToLastRegionOnLayoutChange` de type `bool`. Par défaut, cette propriété est `true`, ce qui indique que la zone de mappage affichée passe de sa région actuelle à sa zone définie précédemment quand une modification de disposition se produit, par exemple lors de la rotation de l’appareil. Quand cette propriété a la valeur `false`, la zone de mappage affichée reste centrée en cas de modification de la disposition. L’exemple suivant illustre la définition de cette propriété :

```xaml
<maps:Map MoveToLastRegionOnLayoutChange="false" />
```

Le code C# équivalent est :

```csharp
Map map = new Map
{
    MoveToLastRegionOnLayoutChange = false
};
```

### <a name="map-clicks"></a>Clics de la carte

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) définit un événement `MapClicked` qui est déclenché lorsque le mappage est activé. L’objet `MapClickedEventArgs` qui accompagne l’événement a une propriété unique nommée `Position`, de type [`Position`](xref:Xamarin.Forms.Maps.Position). Lorsque l’événement est déclenché, la propriété `Position` est définie sur l’emplacement de la carte qui a été taraudé. Pour plus d’informations sur le struct [`Position`](xref:Xamarin.Forms.Maps.Position) , consultez la section [mapper la position et distance](position-distance.md).

L’exemple de code suivant montre un gestionnaire d’événements pour l’événement `MapClicked` :

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Dans cet exemple, le gestionnaire d’événements `OnMapClicked` génère la latitude et la longitude qui représentent l’emplacement du mappage taraudé. Le gestionnaire d’événements peut être inscrit avec l’événement `MapClicked` comme suit :

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

Le code C# équivalent est :

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Position et distance de la carte](position-distance.md)
- [Personnalisation d’une carte Xamarin. Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
