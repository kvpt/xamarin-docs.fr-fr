---
title: Personnalisation d’une épingle de carte
description: Cet article montre comment créer un renderer personnalisé pour le contrôle Map qui affiche une carte native avec une épingle personnalisée et une vue personnalisée des données de l’épingle sur chaque plateforme.
ms.prod: xamarin
ms.assetid: C5481D86-80E9-4E3D-9FB6-57B0F93711A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 54509d0a1133d86727317366b0d229bc218cb263
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870298"
---
# <a name="customizing-a-map-pin"></a>Personnalisation d’une épingle de carte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)

_Cet article montre comment créer un renderer personnalisé pour le contrôle Map qui affiche une carte native avec une épingle personnalisée et une vue personnalisée des données de l’épingle sur chaque plateforme._

Chaque vue Xamarin.Forms est accompagnée d’un renderer pour chaque plateforme qui crée une instance d’un contrôle natif. Quand un élément [`Map`](xref:Xamarin.Forms.Maps.Map) est restitué par une application Xamarin.Forms dans iOS, la classe `MapRenderer` est instanciée, entraînant à son tour l’instanciation d’un contrôle `MKMapView` natif. Sur la plateforme Android, la classe `MapRenderer` instancie un contrôle `MapView` natif. Sur la plateforme Windows universelle (UWP), la classe `MapRenderer` instancie un `MapControl` natif. Pour plus d’informations sur le renderer et les classes de contrôle natif auxquels les contrôles Xamarin.Forms sont mappés, consultez [Classes de base et contrôles natifs de renderer](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre l’élément [`Map`](xref:Xamarin.Forms.Maps.Map) et les contrôles natifs correspondants qui l’implémentent :

![](customized-pin-images/map-classes.png "Relation entre le contrôle Map et les contrôles natifs qui l’implémentent")

Il est possible d’utiliser le processus de rendu pour implémenter des personnalisations spécifiques à la plateforme en créant un renderer personnalisé pour un élément [`Map`](xref:Xamarin.Forms.Maps.Map) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_Custom_Map) une carte personnalisée Xamarin.Forms.
1. [Consommez](#Consuming_the_Custom_Map) la carte personnalisée à partir de Xamarin.Forms.
1. [Créez](#Creating_the_Custom_Renderer_on_each_Platform) le renderer personnalisé pour la carte sur chaque plateforme.

Nous allons maintenant aborder chaque élément tour à tour pour implémenter un renderer `CustomMap` qui affiche une carte native avec une épingle personnalisée et une vue personnalisée des données de l’épingle sur chaque plateforme.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) doit être initialisé et configuré avant utilisation. Pour plus d’informations, consultez [`Maps Control`](~/xamarin-forms/user-interface/map.md).

<a name="Creating_the_Custom_Map" />

## <a name="creating-the-custom-map"></a>Création de la carte personnalisée

Vous pouvez créer un contrôle de carte personnalisée en utilisant une sous-classe de la classe [`Map`](xref:Xamarin.Forms.Maps.Map), comme indiqué dans l’exemple de code suivant :

```csharp
public class CustomMap : Map
{
  public List<CustomPin> CustomPins { get; set; }
}
```

Le contrôle `CustomMap` est créé dans le projet de bibliothèque .NET Standard et définit l’API pour la carte personnalisée. La carte personnalisée expose la propriété `CustomPins` qui représente la collection des objets `CustomPin` qui vont être restitués par le contrôle de carte natif sur chaque plateforme. La classe `CustomPin` est illustrée dans l’exemple de code suivant :

```csharp
public class CustomPin : Pin
{
  public string Url { get; set; }
}
```

Cette classe définit un `CustomPin` en héritant des propriétés de la classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) et en ajoutant une propriété `Url`.

<a name="Consuming_the_Custom_Map" />

## <a name="consuming-the-custom-map"></a>Consommation de la carte personnalisée

Vous pouvez référencer le contrôle `CustomMap` en XAML dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur le contrôle de carte personnalisée. L’exemple de code suivant montre comment le contrôle `CustomMap` peut être consommé par une page XAML :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer">
    <ContentPage.Content>
        <local:CustomMap x:Name="myMap" MapType="Street"
          WidthRequest="{x:Static local:App.ScreenWidth}"
          HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails de la carte personnalisée. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer la carte personnalisée.

L’exemple de code suivant montre comment le contrôle `CustomMap` peut être consommé par une page C# :

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

L’instance `CustomMap` est utilisée pour afficher la carte native sur chaque plateforme. Sa propriété [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) définit le style d’affichage de la [`Map`](xref:Xamarin.Forms.Maps.Map) avec les valeurs possibles qui sont définies dans l’énumération [`MapType`](xref:Xamarin.Forms.Maps.MapType). Pour iOS et Android, la largeur et la hauteur de la carte sont définies via les propriétés de la classe `App` qui sont initialisées dans les projets spécifiques à la plateforme.

L’emplacement de la carte, et les épingles qu’elle contient, sont initialisés comme indiqué dans l’exemple de code suivant :

```csharp
public MapPage ()
{
  ...
  var pin = new CustomPin {
    Type = PinType.Place,
    Position = new Position (37.79752, -122.40183),
    Label = "Xamarin San Francisco Office",
    Address = "394 Pacific Ave, San Francisco CA",
    Id = "Xamarin",
    Url = "http://xamarin.com/about/"
  };

  customMap.CustomPins = new List<CustomPin> { pin };
  customMap.Pins.Add (pin);
  customMap.MoveToRegion (MapSpan.FromCenterAndRadius (
    new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
}
```

Cette initialisation ajoute une épingle personnalisée et positionne la vue de la carte avec la méthode [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), laquelle change la position et le niveau de zoom de la carte en créant un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) à partir d’une [`Position`](xref:Xamarin.Forms.Maps.Position) et d’une [`Distance`](xref:Xamarin.Forms.Maps.Distance).

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application pour personnaliser les contrôles de carte natifs.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `MapRenderer` qui restitue la carte personnalisée.
1. Remplacez la méthode `OnElementChanged` qui restitue la carte personnalisée et écrivez une logique pour la personnaliser. Cette méthode est appelée durant la création de la carte personnalisée Xamarin.Forms correspondante.
1. Ajoutez un attribut `ExportRenderer` à la classe de renderer personnalisé pour spécifier qu’il sera utilisé pour restituer la carte personnalisée Xamarin.Forms. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Il est facultatif de fournir un renderer personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé.

Le diagramme suivant montre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](customized-pin-images/solution-structure.png "Responsabilités du projet de renderer personnalisé CustomMap")

Le contrôle `CustomMap` est restitué par des classes de renderer spécifiques à la plateforme qui dérivent de la classe `MapRenderer` pour chaque plateforme. Chaque contrôle `CustomMap` est ainsi restitué avec des contrôles spécifiques à la plateforme, comme le montrent les captures d’écran suivantes :

![](customized-pin-images/screenshots.png "CustomMap sur chaque plateforme")

La classe `MapRenderer` expose la méthode `OnElementChanged`, qui est appelée quand la carte personnalisée Xamarin.Forms est créée pour restituer le contrôle natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent respectivement l’élément Xamarin.Forms auquel le renderer *était* attaché et l’élément Xamarin.Forms auquel le renderer *est* attaché. Dans l’exemple d’application, la propriété `OldElement` est `null` et la propriété `NewElement` contient une référence à l’instance `CustomMap`.

Une version remplacée de la méthode `OnElementChanged`, dans chaque classe de renderer spécifique à la plateforme, est l’emplacement où effectuer la personnalisation du contrôle natif. Une référence typée au contrôle natif en cours d’utilisation sur la plateforme est accessible par le biais de la propriété `Control`. De plus, une référence au contrôle Xamarin.Forms en cours de restitution peut être obtenue par le biais de la propriété `Element`.

Faites preuve de vigilance quand vous vous abonnez à des gestionnaires d’événements dans la méthode `OnElementChanged`, comme le montre l’exemple de code suivant :

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Vous devez configurer le contrôle natif et vous abonner aux gestionnaires d’événements seulement si le renderer personnalisé est attaché à un nouvel élément Xamarin.Forms. De même, vous devez vous désabonner des gestionnaires d’événements auxquels vous vous êtes abonné seulement si l’élément auquel le renderer est attaché change. L’adoption de cette approche permet de créer un renderer personnalisé qui n’est pas affecté par des fuites de mémoire.

Chaque classe de renderer personnalisé est décorée avec un attribut `ExportRenderer` qui inscrit le renderer auprès de Xamarin.Forms. L’attribut accepte deux paramètres : le nom de type du contrôle personnalisé Xamarin.Forms en cours de restitution et le nom de type du renderer personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent l’implémentation de chaque classe de renderer personnalisé spécifique à la plateforme.

### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

Les captures d’écran suivantes montrent la carte, avant et après la personnalisation :

![](customized-pin-images/map-layout-ios.png "Contrôle de carte avant et après la personnalisation")

Sur iOS, l’épingle est appelée *annotation* et peut être une image personnalisée ou une épingle définie par le système de différentes couleurs. Les annotations peuvent éventuellement présenter une *légende*, qui s’affiche en réponse à l’utilisateur quand il sélectionne l’annotation. La légende affiche les propriétés `Label` et `Address` de l’instance `Pin` avec les vues d’accessoires gauche et droite facultatives. Dans la capture d’écran ci-dessus, la vue d’accessoire gauche est l’image d’un singe, tandis que la vue d’accessoire droite est le bouton *Information*.

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        UIView customPinView;
        List<CustomPin> customPins;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveAnnotations(nativeMap.Annotations);
                    nativeMap.GetViewForAnnotation = null;
                    nativeMap.CalloutAccessoryControlTapped -= OnCalloutAccessoryControlTapped;
                    nativeMap.DidSelectAnnotationView -= OnDidSelectAnnotationView;
                    nativeMap.DidDeselectAnnotationView -= OnDidDeselectAnnotationView;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                customPins = formsMap.CustomPins;

                nativeMap.GetViewForAnnotation = GetViewForAnnotation;
                nativeMap.CalloutAccessoryControlTapped += OnCalloutAccessoryControlTapped;
                nativeMap.DidSelectAnnotationView += OnDidSelectAnnotationView;
                nativeMap.DidDeselectAnnotationView += OnDidDeselectAnnotationView;
            }
        }
        ...
    }
}
```

La méthode `OnElementChanged` procède à la configuration suivante de l’instance [`MKMapView`](xref:MapKit.MKMapView), sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :

- La propriété [`GetViewForAnnotation`](xref:MapKit.MKMapView.GetViewForAnnotation*) est définie sur la méthode `GetViewForAnnotation`. Cette méthode est appelée quand l’[emplacement de l’annotation devient visible sur la carte](#Displaying_the_Annotation), puis est utilisée pour personnaliser l’annotation avant de l’afficher.
- Des gestionnaires d’événements pour les événements `CalloutAccessoryControlTapped`, `DidSelectAnnotationView` et `DidDeselectAnnotationView` sont inscrits. Ces événements se déclenchent quand l’utilisateur [appuie sur l’accessoire de droite de la légende](#Tapping_on_the_Right_Callout_Accessory_View)et quand il [sélectionne](#Selecting_the_Annotation) et [désélectionne](#Deselecting_the_Annotation) l’annotation, respectivement. Les événements sont désinscrits uniquement quand l’élément auquel le renderer est attaché change.

<a name="Displaying_the_Annotation" />

#### <a name="displaying-the-annotation"></a>Affichage de l’annotation

La méthode `GetViewForAnnotation` est appelée quand l’emplacement de l’annotation devient visible sur la carte, puis est utilisée pour personnaliser l’annotation avant de l’afficher. Une annotation est composée de deux parties :

- `MkAnnotation` – contient le titre, le sous-titre et l’emplacement de l’annotation.
- `MkAnnotationView` – contient l’image qui représente l’annotation et éventuellement une légende qui s’affiche quand l’utilisateur appuie sur l’annotation.

La méthode `GetViewForAnnotation` accepte un `IMKAnnotation` qui contient les données de l’annotation et retourne un `MKAnnotationView` pour l’affichage sur la carte, comme illustré dans l’exemple de code suivant :

```csharp
protected override MKAnnotationView GetViewForAnnotation(MKMapView mapView, IMKAnnotation annotation)
{
    MKAnnotationView annotationView = null;

    if (annotation is MKUserLocation)
        return null;

    var customPin = GetCustomPin(annotation as MKPointAnnotation);
    if (customPin == null) {
        throw new Exception("Custom pin not found");
    }

    annotationView = mapView.DequeueReusableAnnotation(customPin.Id.ToString());
    if (annotationView == null) {
        annotationView = new CustomMKAnnotationView(annotation, customPin.Id.ToString());
        annotationView.Image = UIImage.FromFile("pin.png");
        annotationView.CalloutOffset = new CGPoint(0, 0);
        annotationView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile("monkey.png"));
        annotationView.RightCalloutAccessoryView = UIButton.FromType(UIButtonType.DetailDisclosure);
        ((CustomMKAnnotationView)annotationView).Id = customPin.Id.ToString();
        ((CustomMKAnnotationView)annotationView).Url = customPin.Url;
    }
    annotationView.CanShowCallout = true;

    return annotationView;
}
```

Cette méthode garantit que l’annotation s’affiche sous la forme d’une image personnalisée et non d’une épingle définie par le système, et que quand l’utilisateur appuie sur l’annotation, une légende apparaît avec du contenu à gauche et à droite du titre et de l’adresse de l’annotation. Cette opération peut être accomplie de la façon suivante :

1. La méthode `GetCustomPin` est appelée pour retourner les données personnalisées de l’épingle de l’annotation.
1. Pour économiser la mémoire, la vue de l’annotation est mise en pool pour être réutilisée avec l’appel à [`DequeueReusableAnnotation`](xref:MapKit.MKMapView.DequeueReusableAnnotation*).
1. La classe `CustomMKAnnotationView` étend la classe `MKAnnotationView` avec les propriétés `Id` et `Url` qui correspondent aux propriétés identiques de l’instance `CustomPin`. Une nouvelle instance de la `CustomMKAnnotationView` est créée, à condition que l’annotation soit `null`:
    - La propriété `CustomMKAnnotationView.Image` est définie sur l’image qui représente l’annotation sur la carte.
    - La propriété `CustomMKAnnotationView.CalloutOffset` est définie sur `CGPoint` qui spécifie que la légende est centrée au-dessus de l’annotation.
    - La propriété `CustomMKAnnotationView.LeftCalloutAccessoryView` est définie sur une image de singe qui apparaît à gauche du titre et de l’adresse de l’annotation.
    - La propriété `CustomMKAnnotationView.RightCalloutAccessoryView` est définie sur le bouton *Information* qui apparaît à droite du titre et de l’adresse de l’annotation.
    - La propriété `CustomMKAnnotationView.Id` est définie sur la propriété `CustomPin.Id` retournée par la méthode `GetCustomPin`. L’annotation peut être ainsi identifiée pour [personnaliser davantage sa légende](#Selecting_the_Annotation), si vous le souhaitez.
    - La propriété `CustomMKAnnotationView.Url` est définie sur la propriété `CustomPin.Url` retournée par la méthode `GetCustomPin`. L’URL est accessible quand l’utilisateur [appuie sur le bouton affiché dans la vue d’accessoire de droite de la légende](#Tapping_on_the_Right_Callout_Accessory_View).
1. La propriété [`MKAnnotationView.CanShowCallout`](xref:MapKit.MKAnnotationView.CanShowCallout*) est définie sur `true` pour que la légende s’affiche quand l’utilisateur appuie sur l’annotation.
1. L’annotation est retournée pour être affichée sur la carte.

<a name="Selecting_the_Annotation" />

#### <a name="selecting-the-annotation"></a>Sélection de l’annotation

Quand l’utilisateur appuie sur l’annotation, l’événement `DidSelectAnnotationView` se déclenche et exécute la méthode `OnDidSelectAnnotationView` :

```csharp
void OnDidSelectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  customPinView = new UIView ();

  if (customView.Id == "Xamarin") {
    customPinView.Frame = new CGRect (0, 0, 200, 84);
    var image = new UIImageView (new CGRect (0, 0, 200, 84));
    image.Image = UIImage.FromFile ("xamarin.png");
    customPinView.AddSubview (image);
    customPinView.Center = new CGPoint (0, -(e.View.Frame.Height + 75));
    e.View.AddSubview (customPinView);
  }
}
```

Cette méthode étend la légende existante (qui contient les vues d’accessoire gauche et droite) en lui ajoutant une instance `UIView` qui contient une image du logo Xamarin, sous réserve que la propriété `Id` de l’annotation sélectionnée soit définie sur `Xamarin`. Vous pouvez ainsi avoir des scénarios où différentes légendes peuvent s’afficher pour différentes annotations. L’affichage de l’instance `UIView` est centré au-dessus de la légende existante.

<a name="Tapping_on_the_Right_Callout_Accessory_View" />

#### <a name="tapping-on-the-right-callout-accessory-view"></a>Appui sur la vue d’accessoire de droite de la légende

Quand l’utilisateur appuie sur le bouton *Information* dans la vue d’accessoire de droite de la légende, l’événement `CalloutAccessoryControlTapped` se déclenche et exécute la méthode `OnCalloutAccessoryControlTapped` :

```csharp
void OnCalloutAccessoryControlTapped (object sender, MKMapViewAccessoryTappedEventArgs e)
{
  var customView = e.View as CustomMKAnnotationView;
  if (!string.IsNullOrWhiteSpace (customView.Url)) {
    UIApplication.SharedApplication.OpenUrl (new Foundation.NSUrl (customView.Url));
  }
}
```

Cette méthode ouvre un navigateur web et accède à l’adresse stockée dans la propriété `CustomMKAnnotationView.Url`. Notez que l’adresse a été définie lors de la création de la collection `CustomPin` dans le projet de bibliothèque .NET Standard.

<a name="Deselecting_the_Annotation" />

#### <a name="deselecting-the-annotation"></a>Désélection de l’annotation

Quand l’annotation est affichée et que l’utilisateur appuie sur la carte, l’événement `DidDeselectAnnotationView` se déclenche et exécute la méthode `OnDidDeselectAnnotationView` :

```csharp
void OnDidDeselectAnnotationView (object sender, MKAnnotationViewEventArgs e)
{
  if (!e.View.Selected) {
    customPinView.RemoveFromSuperview ();
    customPinView.Dispose ();
    customPinView = null;
  }
}
```

Cette méthode garantit que quand la légende existante n’est pas sélectionnée, la partie étendue de la légende (l’image du logo Xamarin) ne s’affiche plus non plus et ses ressources sont libérées.

Pour plus d’informations sur la personnalisation d’une instance `MKMapView`, consultez [Cartes iOS](~/ios/user-interface/controls/ios-maps/index.md).

### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

Les captures d’écran suivantes montrent la carte, avant et après la personnalisation :

![](customized-pin-images/map-layout-android.png "Contrôle de carte avant et après la personnalisation")

Sur Android, l’épingle est appelée *marqueur* et peut être une image personnalisée ou un marqueur défini par le système de différentes couleurs. Les marqueurs peuvent afficher une *fenêtre d’informations* qui apparaît en réponse à l’utilisateur quand il appuie sur le marqueur. La fenêtre d’informations affiche les propriétés `Label` et `Address` de l’instance `Pin` et peut être personnalisée pour inclure d’autres contenus. Par contre, une seule fenêtre d’informations peut être affichée à la fois.

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.Droid
{
    public class CustomMapRenderer : MapRenderer, GoogleMap.IInfoWindowAdapter
    {
        List<CustomPin> customPins;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                NativeMap.InfoWindowClick -= OnInfoWindowClick;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                customPins = formsMap.CustomPins;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(GoogleMap map)
        {
            base.OnMapReady(map);

            NativeMap.InfoWindowClick += OnInfoWindowClick;
            NativeMap.SetInfoWindowAdapter(this);
        }
        ...
    }
}
```

Sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms, la méthode `OnElementChanged` appelle la méthode `MapView.GetMapAsync`, qui obtient la `GoogleMap` sous-jacente qui est liée à la vue. Une fois que l’instance `GoogleMap` est disponible, le remplacement d’`OnMapReady` est appelé. Cette méthode inscrit un gestionnaire d’événements pour l’événement `InfoWindowClick`, qui se déclenche lors d’un [clic sur la fenêtre d’informations](#Clicking_on_the_Info_Window), et fait l’objet d’un désabonnement uniquement quand l’élément auquel le renderer est attaché change. Le remplacement d’`OnMapReady` appelle aussi la méthode `SetInfoWindowAdapter` pour spécifier que l’instance de la classe `CustomMapRenderer` fournit les méthodes pour personnaliser la fenêtre d’informations.

La classe `CustomMapRenderer` implémente l’interface `GoogleMap.IInfoWindowAdapter` pour [personnaliser la fenêtre d’informations](#Customizing_the_Info_Window). Cette interface spécifie que les méthodes suivantes doivent être implémentées :

- `public Android.Views.View GetInfoWindow(Marker marker)` – Cette méthode est appelée pour retourner une fenêtre d’informations personnalisée pour un marqueur. Si elle retourne `null`, le rendu par défaut de la fenêtre est utilisé. Si elle retourne une `View`, cette `View` est placée dans le cadre de la fenêtre d’informations.
- `public Android.Views.View GetInfoContents(Marker marker)` – Cette méthode est appelée pour retourner une `View` avec le contenu de la fenêtre d’informations, et est appelée uniquement si la méthode `GetInfoWindow` retourne `null`. Si elle retourne `null`, le rendu par défaut du contenu de la fenêtre d’informations est utilisé.

Dans l’exemple d’application, seul le contenu de la fenêtre d’informations est personnalisé, donc la méthode `GetInfoWindow` retourne `null` pour permettre cela.

#### <a name="customizing-the-marker"></a>Personnalisation du marqueur

L’icône utilisée pour représenter un marqueur peut être personnalisée en appelant la méthode `MarkerOptions.SetIcon`. Pour ce faire, vous pouvez remplacer la méthode `CreateMarker`, qui est appelée pour chaque `Pin` ajoutée à la carte :

```csharp
protected override MarkerOptions CreateMarker(Pin pin)
{
    var marker = new MarkerOptions();
    marker.SetPosition(new LatLng(pin.Position.Latitude, pin.Position.Longitude));
    marker.SetTitle(pin.Label);
    marker.SetSnippet(pin.Address);
    marker.SetIcon(BitmapDescriptorFactory.FromResource(Resource.Drawable.pin));
    return marker;
}
```

Cette méthode crée une instance `MarkerOption` pour chaque instance `Pin`. Après avoir défini la position, le libellé et l’adresse du marqueur, son icône est définie avec la méthode `SetIcon`. Cette méthode prend un objet `BitmapDescriptor` contenant les données nécessaires pour restituer l’icône, avec la classe `BitmapDescriptorFactory` qui fournit des méthodes d’assistance pour simplifier la création du `BitmapDescriptor`. Pour plus d’informations sur l’utilisation de la classe `BitmapDescriptorFactory` pour personnaliser un marqueur, consultez [Personnalisation d’un marqueur](~/android/platform/maps-and-location/maps/maps-api.md).

> [!NOTE]
> Si nécessaire, la méthode `GetMarkerForPin` peut être appelée dans votre renderer de carte pour récupérer un `Marker` dans une `Pin`.

<a name="Customizing_the_Info_Window" />

#### <a name="customizing-the-info-window"></a>Personnalisation de la fenêtre d’informations

Lorsqu’un utilisateur appuie sur le marqueur, la méthode `GetInfoContents` est exécutée à condition que la méthode `GetInfoWindow` retourne `null`. L’exemple de code suivant montre la méthode `GetInfoContents` :

```csharp
public Android.Views.View GetInfoContents (Marker marker)
{
  var inflater = Android.App.Application.Context.GetSystemService (Context.LayoutInflaterService) as Android.Views.LayoutInflater;
  if (inflater != null) {
    Android.Views.View view;

    var customPin = GetCustomPin (marker);
    if (customPin == null) {
      throw new Exception ("Custom pin not found");
    }

    if (customPin.Id.ToString() == "Xamarin") {
      view = inflater.Inflate (Resource.Layout.XamarinMapInfoWindow, null);
    } else {
      view = inflater.Inflate (Resource.Layout.MapInfoWindow, null);
    }

    var infoTitle = view.FindViewById<TextView> (Resource.Id.InfoWindowTitle);
    var infoSubtitle = view.FindViewById<TextView> (Resource.Id.InfoWindowSubtitle);

    if (infoTitle != null) {
      infoTitle.Text = marker.Title;
    }
    if (infoSubtitle != null) {
      infoSubtitle.Text = marker.Snippet;
    }

    return view;
  }
  return null;
}
```

Cette méthode retourne une `View` avec le contenu de la fenêtre d’informations. Cette opération peut être accomplie de la façon suivante :

- Une instance `LayoutInflater` est récupérée. Elle est utilisée pour instancier un fichier XML de disposition dans sa `View` correspondante.
- La méthode `GetCustomPin` est appelée pour retourner les données personnalisées de l’épingle de la fenêtre d’informations.
- La disposition `XamarinMapInfoWindow` est agrandie si la propriété `CustomPin.Id` est égale à `Xamarin`. Sinon, la propriété `MapInfoWindow` est agrandie. Vous pouvez ainsi avoir des scénarios où différentes dispositions de fenêtre d’informations peuvent s’afficher pour différents marqueurs.
- Les ressources `InfoWindowTitle` et `InfoWindowSubtitle` sont récupérées dans la disposition agrandie, et leurs propriétés `Text` sont définies sur les données correspondantes de l’instance `Marker`, sous réserve que les ressources ne sont pas `null`.
- L’instance `View` est retournée pour être affichée sur la carte.

> [!NOTE]
> Une fenêtre d’informations n’est pas une `View` dynamique. En effet, Android convertit la `View` en bitmap statique et l’affiche en tant qu’image. Cela signifie que quand une fenêtre d’informations peut répondre à un événement de clic, elle ne peut pas répondre aux événements ou gestes tactiles et les contrôles individuels de la fenêtre d’informations ne peuvent pas répondre à leurs propres événements de clic.

<a name="Clicking_on_the_Info_Window" />

#### <a name="clicking-on-the-info-window"></a>Clic sur la fenêtre d’informations

Quand l’utilisateur clique sur la fenêtre d’informations, l’événement `InfoWindowClick` se déclenche et exécute la méthode `OnInfoWindowClick` :

```csharp
void OnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
  var customPin = GetCustomPin (e.Marker);
  if (customPin == null) {
    throw new Exception ("Custom pin not found");
  }

  if (!string.IsNullOrWhiteSpace (customPin.Url)) {
    var url = Android.Net.Uri.Parse (customPin.Url);
    var intent = new Intent (Intent.ActionView, url);
    intent.AddFlags (ActivityFlags.NewTask);
    Android.App.Application.Context.StartActivity (intent);
  }
}
```

Cette méthode ouvre un navigateur web et accède à l’adresse stockée dans la propriété `Url` de l’instance `CustomPin` récupérée pour le `Marker`. Notez que l’adresse a été définie lors de la création de la collection `CustomPin` dans le projet de bibliothèque .NET Standard.

Pour plus d’informations sur la personnalisation d’une instance `MapView`, consultez [API Cartes](~/android/platform/maps-and-location/maps/maps-api.md).

### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Création du renderer personnalisé sur la plateforme Windows universelle

Les captures d’écran suivantes montrent la carte, avant et après la personnalisation :

![](customized-pin-images/map-layout-uwp.png "Contrôle de carte avant et après la personnalisation")

Sur UWP, l’épingle est appelée *icône de carte* et peut être une image personnalisée ou l’image par défaut définie par le système. Une icône de carte peut afficher un `UserControl` qui apparaît en réponse à l’utilisateur quand il appuie sur l’icône de carte. Le `UserControl` peut afficher tout contenu, notamment les propriétés `Label` et `Address` de l’instance `Pin`.

L’exemple de code suivant illustre le renderer personnalisé UWP :

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace CustomRenderer.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        MapControl nativeMap;
        List<CustomPin> customPins;
        XamarinMapOverlay mapOverlay;
        bool xamarinOverlayShown = false;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                nativeMap.MapElementClick -= OnMapElementClick;
                nativeMap.Children.Clear();
                mapOverlay = null;
                nativeMap = null;
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                nativeMap = Control as MapControl;
                customPins = formsMap.CustomPins;

                nativeMap.Children.Clear();
                nativeMap.MapElementClick += OnMapElementClick;

                foreach (var pin in customPins)
                {
                    var snPosition = new BasicGeoposition { Latitude = pin.Pin.Position.Latitude, Longitude = pin.Pin.Position.Longitude };
                    var snPoint = new Geopoint(snPosition);

                    var mapIcon = new MapIcon();
                    mapIcon.Image = RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///pin.png"));
                    mapIcon.CollisionBehaviorDesired = MapElementCollisionBehavior.RemainVisible;
                    mapIcon.Location = snPoint;
                    mapIcon.NormalizedAnchorPoint = new Windows.Foundation.Point(0.5, 1.0);

                    nativeMap.MapElements.Add(mapIcon);                    
                }
            }
        }
        ...
    }
}
```

La méthode `OnElementChanged` procède aux opérations suivantes, sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :

- Elle efface la collection `MapControl.Children` pour supprimer les éléments d’interface utilisateur existants de la carte, avant d’inscrire un gestionnaire d’événements pour l’événement `MapElementClick`. Cet événement se déclenche quand l’utilisateur appuie ou clique sur un `MapElement` du `MapControl` et fait l’objet d’un désabonnement uniquement quand l’élément auquel le renderer est attaché change.
- Chaque épingle de la collection `customPins` est affiché à l’emplacement géographique approprié sur la carte, comme suit :
  - L’emplacement de l’épingle est créé en tant qu’instance `Geopoint`.
  - Une instance `MapIcon` est créée pour représenter l’épingle.
  - L’image utilisée pour représenter la `MapIcon` est spécifiée en définissant la propriété `MapIcon.Image`. Toutefois, l’affichage de l’image de l’icône de carte n'est pas toujours garanti, car il peut être masqué par d’autres éléments sur la carte. Par conséquent, la propriété `CollisionBehaviorDesired` de l’icône de carte est définie sur `MapElementCollisionBehavior.RemainVisible` pour garantir qu’il reste visible.
  - L’emplacement de la `MapIcon` est spécifié en définissant la propriété `MapIcon.Location`.
  - La propriété `MapIcon.NormalizedAnchorPoint` est définie sur l’emplacement approximatif du pointeur sur l’image. Si cette propriété conserve sa valeur par défaut de (0,0), qui représente le coin supérieur gauche de l’image, les changements de niveau de zoom de la carte peuvent entraîner que l’image pointe sur un autre emplacement.
  - L’instance `MapIcon` est ajoutée à la collection `MapControl.MapElements`. Du coup, l’icône de carte s’affiche sur le `MapControl`.

> [!NOTE]
> Lorsque vous utilisez la même image pour plusieurs icônes de carte, l’instance `RandomAccessStreamReference` doit être déclarée au niveau de la page ou de l’application pour de meilleures performances.

#### <a name="displaying-the-usercontrol"></a>Affichage du UserControl

Lorsqu’un utilisateur appuie sur l’icône de carte, la méthode `OnMapElementClick` est exécutée. L’exemple de code suivant illustre cette méthode :

```csharp
private void OnMapElementClick(MapControl sender, MapElementClickEventArgs args)
{
    var mapIcon = args.MapElements.FirstOrDefault(x => x is MapIcon) as MapIcon;
    if (mapIcon != null)
    {
        if (!xamarinOverlayShown)
        {
            var customPin = GetCustomPin(mapIcon.Location.Position);
            if (customPin == null)
            {
                throw new Exception("Custom pin not found");
            }

            if (customPin.Id.ToString() == "Xamarin")
            {
                if (mapOverlay == null)
                {
                    mapOverlay = new XamarinMapOverlay(customPin);
                }

                var snPosition = new BasicGeoposition { Latitude = customPin.Pin.Position.Latitude, Longitude = customPin.Pin.Position.Longitude };
                var snPoint = new Geopoint(snPosition);

                nativeMap.Children.Add(mapOverlay);
                MapControl.SetLocation(mapOverlay, snPoint);
                MapControl.SetNormalizedAnchorPoint(mapOverlay, new Windows.Foundation.Point(0.5, 1.0));
                xamarinOverlayShown = true;
            }
        }
        else
        {
            nativeMap.Children.Remove(mapOverlay);
            xamarinOverlayShown = false;
        }
    }
}
```

Cette méthode crée une instance `UserControl` qui affiche des informations sur l’épingle. Cette opération peut être accomplie de la façon suivante :

- L’instance `MapIcon` est récupérée.
- La méthode `GetCustomPin` est appelée pour retourner les données d’épingle personnalisées qui s’afficheront.
- Une instance `XamarinMapOverlay` est créée pour afficher les données d’épingle personnalisées. Cette classe est un contrôle utilisateur.
- L’emplacement géographique auquel afficher l’instance `XamarinMapOverlay` du `MapControl` est créé en tant qu’instance `Geopoint`.
- L’instance `XamarinMapOverlay` est ajoutée à la collection `MapControl.Children`. Cette collection contient des éléments d’interface utilisateur XAML qui s’afficheront sur la carte.
- L’emplacement géographique de l’instance `XamarinMapOverlay` sur la carte est définie en appelant la méthode `SetLocation`.
- L’emplacement relatif de l’instance `XamarinMapOverlay`, qui correspond à l’emplacement spécifié, est défini en appelant la méthode `SetNormalizedAnchorPoint`. Ainsi, malgré les changements de niveau de zoom de la carte, l’instance `XamarinMapOverlay` s’affiche toujours à l’emplacement approprié.

Sinon, si les informations sur l’épingle sont déjà affichées sur la carte, un appui sur la carte supprime l’instance `XamarinMapOverlay` de la collection `MapControl.Children`.

#### <a name="tapping-on-the-information-button"></a>Appui sur le bouton Information

Quand l’utilisateur appuie sur le bouton *Information* dans le contrôle utilisateur `XamarinMapOverlay`, l’événement `Tapped` se déclenche et exécute la méthode `OnInfoButtonTapped` :

```csharp
private async void OnInfoButtonTapped(object sender, TappedRoutedEventArgs e)
{
    await Launcher.LaunchUriAsync(new Uri(customPin.Url));
}
```

Cette méthode ouvre un navigateur web et accède à l’adresse stockée dans la propriété `Url` de l’instance `CustomPin`. Notez que l’adresse a été définie lors de la création de la collection `CustomPin` dans le projet de bibliothèque .NET Standard.

Pour plus d’informations sur la personnalisation d’une instance `MapControl`, consultez [Vue d’ensemble des cartes et de l’emplacement](https://msdn.microsoft.com/library/windows/apps/mt219699.aspx) sur MSDN.

## <a name="summary"></a>Récapitulatif

Cet article vous a montré comment créer un renderer personnalisé pour le contrôle `Map` pour permettre aux développeurs de remplacer le rendu natif par défaut par leur propre personnalisation spécifique à la plateforme. Xamarin.Forms.Maps offre un moyen multiplateforme d’afficher des cartes qui utilisent des API de carte natives sur chaque plateforme pour fournir aux utilisateurs une expérience rapide et familière avec les cartes.


## <a name="related-links"></a>Liens associés

- [Contrôle Maps](~/xamarin-forms/user-interface/map.md)
- [Cartes iOS](~/ios/user-interface/controls/ios-maps/index.md)
- [API Cartes](~/android/platform/maps-and-location/maps/maps-api.md)
- [Épingle personnalisée (exemple)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/pin/)
