---
title: Xamarin.FormsMap Control
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 32564b48b6d5e7acc619a966af30a086fb56d5e6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129400"
---
# <a name="xamarinforms-map-control"></a>Xamarin.FormsMap Control

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle est une vue multiplateforme permettant d’afficher et d’annoter des cartes. Elle utilise le contrôle de carte natif pour chaque plateforme, en fournissant une expérience de cartes rapide et familière aux utilisateurs :

[![Capture d’écran du contrôle de carte, sur iOS et Android](map-images/map-default.png "Commandes de la carte")](map-images/map-default-large.png#lightbox "Commandes de la carte")

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit les propriétés suivantes qui contrôlent l’apparence et le comportement de la carte :

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), de type `bool` , indique si la carte affiche l’emplacement actuel de l’utilisateur.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de type `IEnumerable` , qui spécifie la collection d' `IEnumerable` éléments à afficher.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément de la collection d’éléments affichés.
- `ItemTemplateSelector`, de type [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) , qui spécifie le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), de type `bool` , détermine si le mappage est autorisé à faire défiler.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), de type `bool` , détermine si la carte est autorisée à effectuer un zoom.
- `MapElements`, de type `IList<MapElement>` , représente la liste des éléments sur la carte, tels que les polygones et les polylignes.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), de type [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) , indique le style d’affichage de la carte.
- `MoveToLastRegionOnLayoutChange`, de type `bool` , contrôle si la zone de mappage affichée passe de sa zone actuelle à sa zone définie précédemment quand une modification de disposition se produit.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), de type `IList<Pin>` , représente la liste des broches sur la carte.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), de type [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , retourne la région actuellement affichée de la carte.

Ces propriétés, à l’exception des `MapElements` Propriétés, `Pins` et `VisibleRegion` , sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit également un `MapClicked` événement qui est déclenché lorsque le mappage est frappé. L' `MapClickedEventArgs` objet qui accompagne l’événement a une propriété unique nommée `Position` , de type [`Position`](xref:Xamarin.Forms.Maps.Position) . Lorsque l’événement est déclenché, la `Position` propriété est définie sur l’emplacement de la carte qui a été frappé. Pour plus d’informations sur la [`Position`](xref:Xamarin.Forms.Maps.Position) structure, consultez [position et distance](position-distance.md)de la carte.

Pour plus d’informations sur les [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) Propriétés, et `ItemTemplateSelector` , consultez [afficher une collection de codes confidentiels](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Afficher une carte

Une [`Map`](xref:Xamarin.Forms.Maps.Map) peut être affichée en l’ajoutant à une disposition ou une page :

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Une `xmlns` définition d’espace de noms supplémentaire est requise pour référencer le Xamarin.Forms . Mappe les contrôles. Dans l’exemple précédent, l' `Xamarin.Forms.Maps` espace de noms est référencé via le `maps` mot clé.

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

Vous [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) pouvez également passer un argument à un [`Map`](xref:Xamarin.Forms.Maps.Map) constructeur pour définir le point central et le niveau de zoom de la carte lorsqu’elle est chargée. Pour plus d’informations, consultez [afficher un emplacement spécifique sur une carte](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Types de cartes

La [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriété peut être définie sur un [`MapType`](xref:Xamarin.Forms.Maps.MapType) membre de l’énumération pour définir le style d’affichage de la carte. L’énumération `MapType` définit les membres suivants :

- `Street`Spécifie qu’une carte de rue sera affichée.
- `Satellite`Spécifie qu’une carte contenant une image satellite sera affichée.
- `Hybrid`Spécifie qu’une carte combinant les données de la rue et du satellite sera affichée.

Par défaut, un [`Map`](xref:Xamarin.Forms.Maps.Map) affiche une carte postale si la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriété n’est pas définie. La `MapType` propriété peut également être définie sur l’un des membres de l' [`MapType`](xref:Xamarin.Forms.Maps.MapType) énumération :

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

Les captures d’écran suivantes montrent une [`Map`](xref:Xamarin.Forms.Maps.Map) lorsque la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriété a la valeur `Street` :

[![Capture d’écran du contrôle de carte avec le type de carte de rue, sur iOS et Android](map-images/maptype-street.png "Contrôle Map avec la rue maptype")](map-images/maptype-street-large.png#lightbox "Contrôle de carte avec le type de carte de rue")

Les captures d’écran suivantes montrent une [`Map`](xref:Xamarin.Forms.Maps.Map) lorsque la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriété a la valeur `Satellite` :

[![Capture d’écran du contrôle de carte avec le type de carte satellite, sur iOS et Android](map-images/maptype-satellite.png "Contrôle de carte avec le maptype satellite")](map-images/maptype-satellite-large.png#lightbox "Mapper le contrôle avec le type de carte satellite")

Les captures d’écran suivantes montrent une [`Map`](xref:Xamarin.Forms.Maps.Map) lorsque la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) propriété a la valeur `Hybrid` :

[![Capture d’écran du contrôle de carte avec le type de carte hybride, sur iOS et Android](map-images/maptype-hybrid.png "Contrôle de carte avec le maptype hybride")](map-images/maptype-hybrid-large.png#lightbox "Contrôle de carte avec le type de carte hybride")

## <a name="display-a-specific-location-on-a-map"></a>Afficher un emplacement spécifique sur une carte

La région d’un mappage à afficher lorsqu’un mappage est chargé peut être définie en passant un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argument au [`Map`](xref:Xamarin.Forms.Maps.Map) constructeur :

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

Cet exemple crée un [`Map`](xref:Xamarin.Forms.Maps.Map) objet qui affiche la zone spécifiée par l' [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objet. L' `MapSpan` objet est centré sur la latitude et la longitude représentées par un [`Position`](xref:Xamarin.Forms.Maps.Position) objet, et s’étend sur les degrés de longitude 0,01 de latitude et 0,01. Pour plus d’informations sur la [`Position`](xref:Xamarin.Forms.Maps.Position) structure, consultez [position et distance](position-distance.md)de la carte. Pour plus d’informations sur le passage d’arguments en XAML, consultez [passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md).

Le résultat est que, lorsque la carte est affichée, elle est centrée sur un emplacement spécifique et s’étend sur un nombre spécifique de degrés de latitude et de longitude :

[![Capture d’écran du contrôle de carte avec l’emplacement spécifié, sur iOS et Android](map-images/map-region.png "Mapper le contrôle à un emplacement spécifié")](map-images/map-region-large.png#lightbox "Mapper le contrôle à un emplacement spécifié")

## <a name="create-a-mapspan-object"></a>Créer un objet MapSpan

Il existe un certain nombre d’approches pour la création d' [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objets. Une approche courante consiste à fournir les arguments requis au `MapSpan` constructeur. Il s’agit d’une latitude et d’une longitude représentées par un [`Position`](xref:Xamarin.Forms.Maps.Position) objet, et de `double` valeurs qui représentent les degrés de latitude et de longitude qui sont répartis par le `MapSpan` . Pour plus d’informations sur la [`Position`](xref:Xamarin.Forms.Maps.Position) structure, consultez [position et distance](position-distance.md)de la carte.

Il existe également trois méthodes dans la [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) classe qui retournent de nouveaux `MapSpan` objets :

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)retourne un `MapSpan` avec le même `LongitudeDegrees` que l’instance de la classe de la méthode, et un RADIUS défini par ses `north` `south` arguments et.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)retourne un `MapSpan` défini par ses [`Position`](xref:Xamarin.Forms.Maps.Position) [`Distance`](xref:Xamarin.Forms.Maps.Distance) arguments et.
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)retourne un `MapSpan` avec le même centre que l’instance de la classe de la méthode, mais avec un rayon multiplié par son `double` argument.

Pour plus d’informations sur la [`Distance`](xref:Xamarin.Forms.Maps.Distance) structure, consultez [position et distance](position-distance.md)de la carte.

Une fois qu’un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) a été créé, les propriétés suivantes sont accessibles pour récupérer des données :

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), qui représente le [`Position`](xref:Xamarin.Forms.Maps.Position) dans le centre géographique de `MapSpan` .
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), qui représente les degrés de latitude qui sont répartis par le `MapSpan` .
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), qui représente les degrés de longitude qui sont fractionnées par le `MapSpan` .
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), qui représente le `MapSpan` rayon.

## <a name="move-the-map"></a>Déplacer la carte

La [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) méthode peut être appelée pour modifier la position et le niveau de zoom d’une carte. Cette méthode accepte un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argument qui définit la zone de la carte à afficher et son niveau de zoom.

Le code suivant montre un exemple de déplacement de la région affichée sur une carte :

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Faire un zoom sur la carte

Le niveau de zoom d’un [`Map`](xref:Xamarin.Forms.Maps.Map) peut être modifié sans altérer son emplacement. Pour ce faire, vous pouvez utiliser l’interface utilisateur du mappage ou par programmation en appelant la [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) méthode avec un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argument qui utilise l’emplacement actuel comme [`Position`](xref:Xamarin.Forms.Maps.Position) argument :

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

Dans cet exemple, la [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) méthode est appelée avec un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argument qui spécifie l’emplacement actuel de la carte, via la [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propriété, et le niveau de zoom en degrés de latitude et de longitude. Le résultat global est que le niveau de zoom de la carte est modifié, mais que son emplacement ne l’est pas. Une autre approche pour implémenter le zoom sur une carte consiste à utiliser la [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) méthode pour contrôler le facteur de zoom.

> [!IMPORTANT]
> Le zoom d’une carte, qu’il s’agisse d’une interface utilisateur de carte ou d’un programme, requiert que la [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) propriété soit `true` . Pour plus d’informations sur cette propriété, consultez [Disable zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personnaliser le comportement de la carte

Le comportement d’un [`Map`](xref:Xamarin.Forms.Maps.Map) peut être personnalisé en définissant certaines de ses propriétés et en gérant l' `MapClicked` événement.

> [!NOTE]
> La personnalisation du comportement de mappage supplémentaire peut être obtenue en créant un convertisseur personnalisé de carte. Pour plus d’informations, consultez [Personnalisation d’un Xamarin.Forms mappage](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

### <a name="disable-scroll"></a>Désactiver le défilement

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit une [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propriété de type `bool` . Par défaut, cette propriété a la valeur, ce qui `true` indique que la carte est autorisée à faire défiler. Quand cette propriété a la valeur `false` , le mappage ne défile pas. L’exemple suivant illustre la définition de cette propriété :

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit une [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) propriété de type `bool` . Par défaut, cette propriété a `true` la valeur, qui indique que le zoom peut être effectué sur la carte. Lorsque cette propriété a la valeur `false` , le mappage ne peut pas être agrandi. L’exemple suivant illustre la définition de cette propriété :

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit une [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriété de type `bool` . Par défaut, cette propriété a la valeur `false` , ce qui indique que la carte n’affiche pas l’emplacement actuel de l’utilisateur. Quand cette propriété a la valeur `true` , la carte affiche l’emplacement actuel de l’utilisateur. L’exemple suivant illustre la définition de cette propriété :

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit une `MoveToLastRegionOnLayoutChange` propriété de type `bool` . Par défaut, cette propriété a `true` la valeur, qui indique que la zone de mappage affichée passe de sa région actuelle à sa zone définie précédemment quand une modification de disposition se produit, par exemple lors de la rotation de l’appareil. Quand cette propriété a la valeur `false` , la zone de mappage affichée reste centrée en cas de modification de la disposition. L’exemple suivant illustre la définition de cette propriété :

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit un `MapClicked` événement qui est déclenché lorsque le mappage est activé. L' `MapClickedEventArgs` objet qui accompagne l’événement a une propriété unique nommée `Position` , de type [`Position`](xref:Xamarin.Forms.Maps.Position) . Lorsque l’événement est déclenché, la `Position` propriété est définie sur l’emplacement de la carte qui a été frappé. Pour plus d’informations sur la [`Position`](xref:Xamarin.Forms.Maps.Position) structure, consultez [position et distance](position-distance.md)de la carte.

L’exemple de code suivant montre un gestionnaire d’événements pour l' `MapClicked` événement :

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Dans cet exemple, le `OnMapClicked` Gestionnaire d’événements génère la latitude et la longitude qui représentent l’emplacement du mappage taraudé. Le gestionnaire d’événements peut être inscrit avec l' `MapClicked` événement comme suit :

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
- [Personnalisation d’une Xamarin.Forms carte](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
