---
title: Nouvelles fonctionnalités de MapKit sur iOS 11
description: 'Ce document décrit les nouvelles fonctionnalités MapKit dans iOS 11 : regroupement des marqueurs, du bouton Compass, de la vue mise à l’échelle et du bouton suivi de l’utilisateur.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: bddab35044c2b85b69146a03babd9884784baceb
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574585"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Nouvelles fonctionnalités de MapKit sur iOS 11

iOS 11 ajoute les nouvelles fonctionnalités suivantes à MapKit :

- [Clustering d’annotations](#clustering)
- [Bouton Compass](#compass)
- [Vue mise à l’échelle](#scale)
- [Bouton suivi de l’utilisateur](#user-tracking)

![Carte avec marqueurs en cluster et bouton Compass](mapkit-images/cyclemap-heading.png)

<a name="clustering"></a>

## <a name="automatically-grouping-markers-while-zooming"></a>Regroupement automatique des marqueurs pendant le zoom

L’exemple [MapKit « Tandm »](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) montre comment implémenter la nouvelle fonctionnalité de clustering d’annotations iOS 11.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. créer une `MKPointAnnotation` sous-classe

La classe d’annotation point représente chaque marqueur sur la carte. Elles peuvent être ajoutées individuellement à l’aide `MapView.AddAnnotation()` de ou à partir d’un tableau à l’aide de `MapView.AddAnnotations()` .

Les classes d’annotations de point n’ont pas de représentation visuelle, elles sont uniquement requises pour représenter les données associées au marqueur (surtout, la `Coordinate` propriété qui est sa latitude et sa longitude sur la carte) et toutes les propriétés personnalisées :

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. créer une `MKMarkerAnnotationView` sous-classe pour les marqueurs uniques

La vue d’annotation de marqueur est la représentation visuelle de chaque annotation et est stylisée à l’aide de propriétés telles que :

- **MarkerTintColor** : couleur du marqueur.
- **GlyphText** – texte affiché dans le marqueur.
- **GlyphImage** : définit l’image affichée dans le marqueur.
- **DisplayPriority** : détermine l’ordre de plan (comportement d’empilement) lorsque la carte est encombrée de marqueurs. Utilisez `Required` , `DefaultHigh` ou `DefaultLow` .

Pour prendre en charge le clustering automatique, vous devez également définir :

- **ClusteringIdentifier** : ce contrôle les marqueurs qui sont regroupés. Vous pouvez utiliser le même identificateur pour tous vos marqueurs ou utiliser des identificateurs différents pour contrôler la façon dont ils sont regroupés.

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. créer un `MKAnnotationView` pour représenter les clusters de marqueurs

Alors que la vue d’annotation qui représente un cluster de marqueurs _peut_ être une image simple, les utilisateurs s’attendent à ce que l’application fournisse des signaux visuels sur le nombre de marqueurs qui ont été regroupés ensemble.

L' [exemple de code](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) utilise coregraphics pour afficher le nombre de marqueurs dans le cluster, ainsi qu’une représentation sous forme de cercle de la proportion de chaque type de marqueur.

Vous devez également définir :

- **DisplayPriority** : détermine l’ordre de plan (comportement d’empilement) lorsque la carte est encombrée de marqueurs. Utilisez `Required` , `DefaultHigh` ou `DefaultLow` .
- **CollisionMode** – `Circle` ou `Rectangle` .

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. inscrire les classes d’affichage

Lorsque le contrôle de vue cartographique est créé et ajouté à une vue, enregistrez les types d’affichages d’annotation pour activer le comportement de clustering automatique, car le mappage fait l’objet d’un zoom avant et arrière :

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. rendu de la carte !

Lorsque le mappage est rendu, les marqueurs d’annotation sont mis en cluster ou rendus en fonction du niveau de zoom. Au fur et à mesure que le niveau de zoom change, les marqueurs animent et sortent des clusters.

![Simulateur présentant des marqueurs en cluster sur la carte](mapkit-images/cyclemap-sml.png)

Reportez-vous à la [section Maps](~/ios/user-interface/controls/ios-maps/index.md) pour plus d’informations sur l’affichage des données avec MapKit.

<a name="compass"></a>

## <a name="compass-button"></a>Bouton Compass

iOS 11 ajoute la possibilité de dépiler la boussole hors de la carte et de la rendre ailleurs dans la vue. Pour obtenir un exemple, consultez l’exemple d' [application Tandm](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) .

Créez un bouton qui ressemble à une boussole (y compris une animation dynamique lorsque l’orientation de la carte est modifiée) et le restitue sur un autre contrôle.

![Bouton Compass dans la barre de navigation](mapkit-images/compass-sml.png)

Le code ci-dessous crée un bouton Compass et le restitue dans la barre de navigation :

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

La `ShowsCompass` propriété peut être utilisée pour contrôler la visibilité de la boussole par défaut à l’intérieur de la vue cartographique.

<a name="scale"></a>

## <a name="scale-view"></a>Vue mise à l’échelle

Ajoutez la mise à l’échelle ailleurs dans la vue à l’aide de la `MKScaleView.FromMapView()` méthode pour obtenir une instance de la vue de mise à l’échelle à ajouter ailleurs dans la hiérarchie d’affichage.

![Vue de mise à l’échelle superposée sur une carte](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

La `ShowsScale` propriété peut être utilisée pour contrôler la visibilité de la boussole par défaut à l’intérieur de la vue cartographique.

<a name="user-tracking"></a>

## <a name="user-tracking-button"></a>Bouton suivi de l’utilisateur

Le bouton suivi de l’utilisateur Centre la carte à l’emplacement actuel de l’utilisateur. Utilisez la `MKUserTrackingButton.FromMapView()` méthode pour obtenir une instance du bouton, appliquer des modifications de mise en forme et ajouter ailleurs dans la hiérarchie d’affichage.

![Bouton de l’emplacement de l’utilisateur superposé sur une carte](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```

## <a name="related-links"></a>Liens connexes

- [Exemple MapKit’Tandm'](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [Nouveautés de MapKit (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/237/)
