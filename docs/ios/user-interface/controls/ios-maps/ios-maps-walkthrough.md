---
title: Annotations et superpositions dans Xamarin. iOS
description: Cet article présente une procédure pas à pas expliquant comment utiliser les fonctionnalités d’annotation et de superposition de Map kit. Il montre comment ajouter une carte à une application qui affiche une annotation et une superposition à l’emplacement de la Conférence Xamarin évolution 2013.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 64b666e8e8621019da4f2acb71ab5b3bf22fad3a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889763"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Annotations et superpositions dans Xamarin. iOS

L’application que nous allons créer dans cette procédure pas à pas est illustrée ci-dessous:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "Exemple d’application MapKit")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
Vous trouverez le code complet dans l' [exemple de procédure pas à pas Maps](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough).

Commençons par créer un nouveau **projet iOS vide**et lui donner un nom pertinent. Nous allons commencer par ajouter du code à notre contrôleur d’affichage pour afficher MapView, puis créer des classes pour notre MapDelegate et les annotations personnalisées. Suivez les étapes ci-dessous pour y parvenir :

## <a name="viewcontroller"></a>ViewController


1. Ajoutez les espaces de noms suivants au `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Ajoutez une `MKMapView` variable d’instance à la classe, ainsi qu' `MapDelegate` une instance. Nous allons créer `MapDelegate` bientôt:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. Dans la méthode du `LoadView` contrôleur, ajoutez un `MKMapView` et affectez-lui `View` la propriété du contrôleur:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Nous allons ensuite ajouter du code pour initialiser le mappage dans la méthode «ViewDidLoad».

1. Dans `ViewDidLoad` ajouter du code pour définir le type de carte, affichez l’emplacement de l’utilisateur et autorisez le zoom et le panoramique:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. Ensuite, ajoutez du code pour centrer la carte et définir sa région:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. Créez une nouvelle instance de `MapDelegate` et assignez- `Delegate` la au `MKMapView`du. Là encore, nous implcodeent `MapDelegate` bientôt:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. À partir d’iOS 8, vous devez demander à l’utilisateur d’utiliser son emplacement. nous allons donc l’ajouter à notre exemple. Tout d’abord, `CLLocationManager` définissez une variable de niveau classe:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. Dans la `ViewDidLoad` méthode, nous souhaitons vérifier si l’appareil exécutant l’application utilise iOS 8, et s’il s’agit d’une demande d’autorisation quand l’application est en cours d’utilisation:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. Enfin, vous devez modifier le fichier **info. plist** pour informer les utilisateurs de la raison de demander leur emplacement. Dans le menu **source** du fichier **info. plist**, ajoutez la clé suivante:
    
    `NSLocationWhenInUseUsageDescription` 
    
    et chaîne: 

    `Maps Walkthrough Docs Sample`.


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs: classe pour les annotations personnalisées


1. Nous allons utiliser une classe personnalisée pour l’annotation appelée `ConferenceAnnotation`. Ajoutez la classe suivante au projet:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-ajout de l’annotation et de la superposition

1. Avec le `ConferenceAnnotation` en place, nous pouvons l’ajouter à la carte. De retour dans `ViewDidLoad` la méthode `ViewController`de, ajoutez l’annotation à la coordonnée centrale de la carte:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. Nous souhaitons également avoir un chevauchement de l’hôtel. Ajoutez le code suivant pour créer le `MKPolygon` à l’aide des coordonnées de l’hôtel fourni, puis ajoutez-le à la `AddOverlay`carte par appel:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```

Cela termine le code dans `ViewDidLoad`. Nous devons à présent implémenter `MapDelegate` notre classe pour gérer la création des vues d’annotation et de superposition, respectivement.


## <a name="mapdelegate"></a>MapDelegate

1. Créez une classe appelée `MapDelegate` qui hérite de `MKMapViewDelegate` et incluez `annotationId` une variable à utiliser comme identificateur de réutilisation pour l’annotation:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```

    Nous n’avons qu’une seule annotation ici afin que le code de réutilisation ne soit pas strictement nécessaire, mais il est conseillé de l’inclure.

1. Implémentez `GetViewForAnnotation` la méthode pour retourner une vue pour `ConferenceAnnotation` le à l’aide de l’image **Conference. png** incluse dans cette procédure pas à pas:

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. Quand l’utilisateur appuie sur l’annotation, nous souhaitons afficher une image représentant la ville d’Austin. Ajoutez les variables suivantes au `MapDelegate` pour l’image et la vue pour l’afficher:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Ensuite, pour afficher l’image lorsque l’annotation est frappée, implémentez la `DidSelectAnnotation` méthode comme suit:

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. Pour masquer l’image lorsque l’utilisateur désélectionne l’annotation en cliquant n’importe où sur la carte, implémentez la `DidSelectAnnotationView` méthode comme suit:

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```

    Nous disposons à présent du code pour l’annotation en place. Tout ce qui reste, c’est d’ajouter du `MapDelegate` code à pour créer la vue pour la superposition d’hôtel.

1. Ajoutez l’implémentation suivante de `GetViewForOverlay` `MapDelegate`à:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

Exécutez l'application. Nous disposons maintenant d’une carte interactive avec une annotation personnalisée et une superposition! Appuyez sur l’annotation et l’image d’Austin s’affiche, comme indiqué ci-dessous:

 [![](ios-maps-walkthrough-images/01-map-image.png "Appuyez sur l’annotation et l’image d’Austin s’affiche")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons vu comment ajouter une annotation à une carte et comment ajouter une superposition pour un polygone spécifié. Nous avons également démontré comment ajouter une prise en charge tactile à l’annotation pour animer une image sur une carte.


## <a name="related-links"></a>Liens associés

- [Exemple de procédure pas à pas de Maps](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough)
- [Exemple de démonstration cartographique](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
- [Cartes iOS](~/ios/user-interface/controls/ios-maps/index.md)
