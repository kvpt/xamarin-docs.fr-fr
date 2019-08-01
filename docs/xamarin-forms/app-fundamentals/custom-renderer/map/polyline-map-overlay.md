---
title: Mise en surbrillance d’un itinéraire sur une carte
description: Cet article explique comment ajouter une superposition polyligne sur une carte. Une superposition polyligne est une série de segments de ligne connectés, généralement utilisés pour montrer un itinéraire sur une carte ou tracer la forme qui convient.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 19da0de80819a48259a28b30e003c32923d37378
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650686"
---
# <a name="highlighting-a-route-on-a-map"></a>Mise en surbrillance d’un itinéraire sur une carte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline)

_Cet article explique comment ajouter une superposition polyligne sur une carte. Une superposition polyligne est une série de segments de ligne connectés, généralement utilisés pour montrer un itinéraire sur une carte ou tracer la forme qui convient._

## <a name="overview"></a>Vue d'ensemble

Une superposition est une forme graphique posée sur une carte. Les superpositions prennent en charge le tracé de contenu graphique qui s’ajuste à la carte quand celle-ci est zoomée. Les captures d’écran suivantes montrent le résultat de l’ajout d’une superposition polyligne sur une carte :

![](polyline-map-overlay-images/screenshots.png)

Quand un contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) est restitué par une application Xamarin.Forms, dans iOS, la classe `MapRenderer` est instanciée, entraînant à son tour l’instanciation d’un contrôle `MKMapView` natif. Sur la plateforme Android, la classe `MapRenderer` instancie un contrôle `MapView` natif. Sur la plateforme Windows universelle (UWP), la classe `MapRenderer` instancie un `MapControl` natif. Il est possible de tirer profit du processus de rendu pour implémenter des personnalisations de carte spécifiques à la plateforme en créant un renderer personnalisé pour un `Map` sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_Custom_Map) une carte personnalisée Xamarin.Forms.
1. [Consommez](#Consuming_the_Custom_Map) la carte personnalisée à partir de Xamarin.Forms.
1. [Personnalisez](#Customizing_the_Map) la carte en créant un renderer personnalisé pour la carte sur chaque plateforme.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) doit être initialisé et configuré avant utilisation. Pour plus d’informations, consultez [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Pour plus d’informations sur la personnalisation d’une carte à l’aide d’un renderer personnalisé, consultez [Personnalisation d’une épingle de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Création de la carte personnalisée

Créez une sous-classe de la classe [ `Map` ](xref:Xamarin.Forms.Maps.Map), qui ajoute une propriété `RouteCoordinates` :

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

La propriété `RouteCoordinates` stocke une collection de coordonnées qui définissent l’itinéraire à mettre en surbrillance.

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Consommation de la carte personnalisée

Consommez le contrôle `CustomMap` en déclarant une instance à lui dans l’instance de la page XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

Ou consommez le contrôle `CustomMap` en déclarant une instance à lui dans l’instance de la page C# :

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

Initialisez le contrôle `CustomMap` comme suit :

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Cette initialisation spécifie une série de coordonnées de latitude et de longitude pour définir l’itinéraire de la carte à mettre en surbrillance. Ensuite, elle positionne la vue de la carte avec la méthode [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), laquelle change la position et le niveau de zoom de la carte en créant un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) à partir d’une [ `Position` ](xref:Xamarin.Forms.Maps.Position) et d’une [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personnalisation de la carte

Un renderer personnalisé doit maintenant être ajouté à chaque projet d’application pour ajouter la superposition polyligne sur la carte.

#### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

Créez une sous-classe de la classe `MapRenderer` et remplacez sa méthode `OnElementChanged` pour ajouter la superposition polyligne :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

Cette méthode procède à la configuration suivante, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :

- La propriété `MKMapView.OverlayRenderer` est définie sur un délégué correspondant.
- La collection de coordonnées de latitude et de longitude est récupérée dans la propriété `CustomMap.RouteCoordinates` et stockée sous forme de tableau d’instances `CLLocationCoordinate2D`.
- La polyligne est créée en appelant la méthode `MKPolyline.FromCoordinates` statique, qui spécifie la latitude et la longitude de chaque point.
- La polyligne est ajoutée sur la carte en appelant la méthode `MKMapView.AddOverlay`.

Ensuite, implémentez la méthode `GetOverlayRenderer` pour personnaliser le rendu de la superposition :

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

Créez une sous-classe de la classe `MapRenderer` et remplacez ses méthodes `OnElementChanged` et `OnMapReady` pour ajouter la superposition polyligne :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

La méthode `OnElementChanged` récupère la collection de coordonnées de latitude et de longitude dans la propriété `CustomMap.RouteCoordinates` et la stocke dans une variable membre. Elle appelle ensuite la méthode `MapView.GetMapAsync`, qui obtient la `GoogleMap` sous-jacente qui est liée à la vue, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms. Une fois que l’instance `GoogleMap` est disponible, la méthode `OnMapReady` est appelée, où la polyligne est créée en instanciant un objet `PolylineOptions` qui spécifie la latitude et la longitude de chaque point. La polyligne est alors ajoutée sur la carte en appelant la méthode `NativeMap.AddPolyline`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Création du renderer personnalisé sur la plateforme Windows universelle

Créez une sous-classe de la classe `MapRenderer` et remplacez sa méthode `OnElementChanged` pour ajouter la superposition polyligne :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

Cette méthode procède aux opérations suivantes, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :

- La collection de coordonnées de latitude et de longitude est récupérée dans la propriété `CustomMap.RouteCoordinates` et convertie en `List` de coordonnées `BasicGeoposition`.
- La polyligne est créée en instanciant un objet `MapPolyline`. La classe `MapPolygon` est utilisée pour afficher une ligne sur la carte en définissant sa propriété `Path` sur un objet `Geopath` qui contient les coordonnées de la ligne.
- La polyligne est rendue sur la carte en l’ajoutant à la collection `MapControl.MapElements`.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment ajouter une superposition polyligne sur une carte pour montrer un itinéraire sur une carte ou tracer la forme qui convient.


## <a name="related-links"></a>Liens associés

- [Superposition polyligne sur une carte (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline)
- [Personnalisation d’une épingle de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
