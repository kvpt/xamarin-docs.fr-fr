---
title: Mise en surbrillance d’une zone circulaire sur une carte
description: Cet article explique comment ajouter une superposition circulaire à une carte afin de mettre en surbrillance une zone circulaire de la carte. iOS et Android offrent des API pour ajouter une superposition circulaire à la carte, alors que la plateforme UWP restitue la superposition sous la forme d’un polygone.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: ed671063aad55cb6c4730494f024a2ec2a016de1
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051929"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Mise en surbrillance d’une zone circulaire sur une carte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)

_Cet article explique comment ajouter une superposition circulaire à une carte afin de mettre en surbrillance une zone circulaire de la carte._

## <a name="overview"></a>Vue d'ensemble

Une superposition est une forme graphique posée sur une carte. Les superpositions prennent en charge le tracé de contenu graphique qui s’ajuste à la carte quand celle-ci est zoomée. Les captures d’écran suivantes montrent le résultat de l’ajout d’une superposition circulaire sur une carte :

![](circle-map-overlay-images/screenshots.png)

Quand un contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) est restitué par une application Xamarin.Forms, dans iOS, la classe `MapRenderer` est instanciée, entraînant à son tour l’instanciation d’un contrôle `MKMapView` natif. Sur la plateforme Android, la classe `MapRenderer` instancie un contrôle `MapView` natif. Sur la plateforme Windows universelle (UWP), la classe `MapRenderer` instancie un `MapControl` natif. Il est possible de tirer profit du processus de rendu pour implémenter des personnalisations de carte spécifiques à la plateforme en créant un renderer personnalisé pour un `Map` sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_Custom_Map) une carte personnalisée Xamarin.Forms.
1. [Consommez](#Consuming_the_Custom_Map) la carte personnalisée à partir de Xamarin.Forms.
1. [Personnalisez](#Customizing_the_Map) la carte en créant un renderer personnalisé pour la carte sur chaque plateforme.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) doit être initialisé et configuré avant utilisation. Pour plus d’informations, consultez [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Pour plus d’informations sur la personnalisation d’une carte à l’aide d’un renderer personnalisé, consultez [Personnalisation d’une épingle de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Création de la carte personnalisée

Créez une classe `CustomCircle` qui a les propriétés `Position` et `Radius` :

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Ensuite, créez une sous-classe de la classe [ `Map` ](xref:Xamarin.Forms.Maps.Map), qui ajoute une propriété de type `CustomCircle` :

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

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
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

Cette initialisation ajoute les instances [`Pin`](xref:Xamarin.Forms.Maps.Pin) et `CustomCircle` à la carte personnalisée et positionne la vue de la carte avec la méthode [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), laquelle change la position et le niveau de zoom de la carte en créant un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) à partir d’une [`Position`](xref:Xamarin.Forms.Maps.Position) et d’une [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personnalisation de la carte

Un renderer personnalisé doit maintenant être ajouté à chaque projet d’application pour ajouter la superposition circulaire à la carte.

#### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

Créez une sous-classe de la classe `MapRenderer` et remplacez sa méthode `OnElementChanged` pour ajouter la superposition circulaire :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

Cette méthode procède à la configuration suivante, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :

- La propriété `MKMapView.OverlayRenderer` est définie sur un délégué correspondant.
- Le cercle est créé en définissant un objet `MKCircle` statique qui spécifie le centre du cercle et le rayon du cercle en mètres.
- Le cercle est ajouté à la carte en appelant la méthode `MKMapView.AddOverlay`.

Ensuite, implémentez la méthode `GetOverlayRenderer` pour personnaliser le rendu de la superposition :

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

Créez une sous-classe de la classe `MapRenderer` et remplacez ses méthodes `OnElementChanged` et `OnMapReady` pour ajouter la superposition circulaire :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

La méthode `OnElementChanged` appelle la méthode `MapView.GetMapAsync`, qui obtient la `GoogleMap` sous-jacente qui est liée à la vue, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms. Une fois que l’instance `GoogleMap` est disponible, la méthode `OnMapReady` est appelée, où le cercle est créé en instanciant un objet `CircleOptions` qui spécifie le centre du cercle et le rayon du cercle en mètres. Le cercle est ensuite ajouté à la carte en appelant la méthode `NativeMap.AddCircle`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Création du renderer personnalisé sur la plateforme Windows universelle

Créez une sous-classe de la classe `MapRenderer` et remplacez sa méthode `OnElementChanged` pour ajouter la superposition circulaire :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

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
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

Cette méthode procède aux opérations suivantes, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :

- La position et le rayon du cercle sont récupérés dans la propriété `CustomMap.Circle` et passés à la méthode `GenerateCircleCoordinates`, qui génère les coordonnées de latitude et de longitude du périmètre du cercle. Le code de cette méthode d’assistance est indiqué ci-dessous.
- Les coordonnées du périmètre du cercle sont converties en `List` de coordonnées `BasicGeoposition`.
- Le cercle est créé en instanciant un objet `MapPolygon`. La classe `MapPolygon` est utilisée pour afficher une forme multipoint sur la carte en définissant sa propriété `Path` sur un objet `Geopath` qui contient les coordonnées de la forme.
- Le cercle est rendu sur la carte en l’ajoutant à la collection `MapControl.MapElements`.


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment ajouter une superposition circulaire à une carte afin de mettre en surbrillance une zone circulaire de la carte.


## <a name="related-links"></a>Liens associés

- [Superposition circulaire sur une carte (exemple)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personnalisation d’une épingle de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
