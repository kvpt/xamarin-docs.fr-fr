---
title: Cartes dans Xamarin. iOS
description: Ce document décrit l’infrastructure iOS MapKit et la façon dont elle est utilisée avec Xamarin. iOS. Il explique comment ajouter un mappage, le stylet, le panoramique et le zoom, comment utiliser l’emplacement de l’utilisateur, ajouter des annotations, travailler avec des légendes et des superpositions, et effectuer une recherche locale.
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4b0e540bdcdf061f64880ea961a5e07a0a45b22e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642919"
---
# <a name="maps-in-xamarinios"></a>Cartes dans Xamarin. iOS

Les mappages sont une fonctionnalité commune à tous les systèmes d’exploitation mobiles modernes. iOS offre une prise en charge du mappage en mode natif via l’infrastructure du kit de cartes. Avec le kit de cartes, les applications peuvent facilement ajouter des cartes riches et interactives. Ces cartes peuvent être personnalisées de différentes façons, telles que l’ajout d’annotations pour marquer les emplacements sur une carte et la superposition de graphiques de formes arbitraires. Le kit de cartes offre une prise en charge intégrée pour l’indication de l’emplacement actuel d’un appareil.

## <a name="adding-a-map"></a>Ajout d’un mappage

L’ajout d’un mappage à une application s’effectue en `MKMapView` ajoutant une instance à la hiérarchie d’affichage, comme indiqué ci-dessous:

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView``UIView` sous-classe qui affiche une carte. Le simple ajout de la carte à l’aide du code ci-dessus génère une carte interactive:

 ![](images/00-map.png "Exemple de mappage")

## <a name="map-style"></a>Style de carte

 `MKMapView`prend en charge 3 styles différents de mappages. Pour appliquer un style de carte, il vous `MapType` suffit de définir la propriété sur `MKMapType` une valeur de l’énumération:
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  La capture d’écran suivante montre les différents styles de carte disponibles:

 ![](images/01-mapstyles.png "Cette capture d’écran montre les différents styles de carte disponibles")

## <a name="panning-and-zooming"></a>Panoramique et zoom

 `MKMapView`prend en charge les fonctionnalités d’interactivité de la carte, telles que:

-  Zoom à l’aide d’un geste de pincement
-  Panoramique via un mouvement panoramique


Ces fonctionnalités peuvent être activées ou désactivées en `ZoomEnabled` définissant simplement les `MKMapView` propriétés et `ScrollEnabled` de l’instance, où la valeur par défaut est true pour les deux. Par exemple, pour afficher une carte statique, définissez simplement les propriétés appropriées sur false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>Emplacement de l’utilisateur

Outre l’interaction de l’utilisateur `MKMapView` , intègre également la prise en charge de l’affichage de l’emplacement de l’appareil. Pour ce faire, il utilise l’infrastructure d' *emplacement principale* . Avant de pouvoir accéder à l’emplacement de l’utilisateur, vous devez inviter l’utilisateur. Pour ce faire, créez une instance de `CLLocationManager` et appelez `RequestWhenInUseAuthorization`.

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

Notez que dans les versions d’iOS antérieures à 8,0, toute tentative `RequestWhenInUseAuthorization` d’appel entraînera une erreur. Veillez à vérifier la version d’iOS avant d’effectuer cet appel si vous envisagez de prendre en charge les versions antérieures à 8.

L’accès à l’emplacement de l’utilisateur nécessite également des modifications du fichier **info. plist**. Vous devez définir les clés suivantes relatives aux données d’emplacement :

- **NSLocationWhenInUseUsageDescription** : cette clé est utilisée quand vous accédez à l’emplacement d’un utilisateur en train d’interagir avec votre application.
- **NSLocationAlwaysUsageDescription** : cette clé est utilisée quand votre application accède à l’emplacement d’un utilisateur en arrière-plan.

Vous pouvez ajouter ces clés en ouvrant **info. plist** et en sélectionnant *source* en bas de l’éditeur.

Une fois que vous avez mis à jour le fichier **info. plist** et demandé à l’utilisateur l’autorisation d’accéder à son emplacement, vous pouvez afficher l’emplacement `ShowsUserLocation` de l’utilisateur sur la carte en affectant à la propriété la valeur true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "Alerte autoriser l’accès à l’emplacement")
 
## <a name="annotations"></a>Annotations

 `MKMapView`prend également en charge l’affichage d’images, appelées annotations, sur une carte. Il peut s’agir d’images personnalisées ou d’épingles de différentes couleurs définies par le système. Par exemple, la capture d’écran suivante montre un mappage avec un code confidentiel et une image personnalisée:

 ![](images/03-annotations.png "Cette capture d’écran montre un mappage avec un code confidentiel et une image personnalisée")

### <a name="adding-an-annotation"></a>Ajout d’une annotation

Une annotation elle-même se compose de deux parties:

-  `MKAnnotation` Objet, qui comprend les données de modèle relatives à l’annotation, telles que le titre et l’emplacement de l’annotation.
-  `MKAnnotationView` , Qui contient l’image à afficher et éventuellement une légende qui s’affiche lorsque l’utilisateur appuie sur l’annotation.


Le kit de cartes utilise le modèle de délégation IOS pour ajouter des annotations à `Delegate` une carte, `MKMapView` où la propriété du est définie sur `MKMapViewDelegate`une instance d’un. Il s’agit de l’implémentation de ce délégué qui est chargée `MKAnnotationView` de retourner le pour une annotation.

Pour ajouter une annotation, vous devez d’abord ajouter l’annotation `AddAnnotations` en appelant `MKMapView` sur l’instance:

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

Lorsque l’emplacement de l’annotation devient visible sur la carte, le `MKMapView` appelle la méthode de `GetViewForAnnotation` son délégué pour obtenir le `MKAnnotationView` à afficher.

Par exemple, le code suivant retourne un fourni `MKPinAnnotationView`par le système:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>Réutilisation d’annotations

Pour économiser de la mémoire `MKMapView` , permet de regrouper les vues d’annotation en vue de les réutiliser, comme dans le cas de la réutilisation des cellules de tableau. L’obtention d’une vue d’annotation à partir du pool s’effectue `DequeueReusableAnnotation`à l’aide d’un appel à:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>Indication des légendes

Comme mentionné précédemment, une annotation peut éventuellement afficher une légende. Pour afficher une légende, affectez `CanShowCallout` simplement la `MKAnnotationView`valeur true à. Cela entraîne l’affichage du titre de l’annotation lorsque l’annotation est frappée, comme indiqué ci-dessous:

 ![](images/04-callout.png "Titre des annotations affiché")

### <a name="customizing-the-callout"></a>Personnalisation de la légende

La légende peut également être personnalisée pour afficher les vues d’accessoires de gauche et de droite, comme indiqué ci-dessous:

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

Ce code génère la légende suivante:

 ![](images/05-callout-accessories.png "Un exemple de légende")

Pour gérer l’utilisateur en appuyant sur l’accessoire approprié, `CalloutAccessoryControlTapped` il vous suffit `MKMapViewDelegate`d’implémenter la méthode dans le:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>Superpositions

Une autre façon de superposer des graphiques sur une carte consiste à utiliser des superpositions. Les superpositions prennent en charge le tracé de contenu graphique qui s’ajuste à la carte quand celle-ci est zoomée. iOS assure la prise en charge de plusieurs types de superpositions, notamment:

-  Polygones-couramment utilisés pour mettre en surbrillance une région d’une carte.
-  Polylignes, souvent affichées lors de l’affichage d’un itinéraire.
-  Cercles-utilisé pour mettre en surbrillance une zone circulaire d’une carte.


En outre, des superpositions personnalisées peuvent être créées pour afficher des géométries arbitraires avec un code de dessin granulaire et personnalisé. Par exemple, le radar météo est un bon candidat pour une superposition personnalisée.

#### <a name="adding-an-overlay"></a>Ajout d’une superposition

Comme pour les annotations, l’ajout d’une superposition implique deux parties:

-  Création d’un objet de modèle pour la superposition et ajout `MKMapView` de celui-ci à.
-  Création d’une vue pour la superposition `MKMapViewDelegate` dans le.


Le modèle de la superposition peut être `MKShape` n’importe quelle sous-classe. Xamarin. iOS comprend `MKShape` des sous-classes pour les polygones, `MKPolyline` les polylignes et `MKPolygon`les cercles `MKCircle` , par le biais des classes et respectivement.

Par exemple, le code suivant est utilisé pour ajouter un `MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

La vue d’une superposition est `MKOverlayView` une instance retournée `GetViewForOverlay` par dans le `MKMapViewDelegate`. Chacun `MKShape` a un correspondant `MKOverlayView` qui sait comment afficher la forme donnée. Pour `MKPolygon` y figure `MKPolygonView`. De même, `MKPolyline` correspond à `MKPolylineView` `MKCircle` et`MKCircleView`à.

Par exemple, le code suivant retourne un `MKCircleView` pour un `MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

Un cercle s’affiche sur la carte, comme indiqué ci-dessous:

 ![](images/06-circle-overlay.png "Cercle affiché sur la carte")

## <a name="local-search"></a>Recherche locale

iOS comprend une API de recherche locale avec le kit Map, qui permet des recherches asynchrones de points d’intérêt dans une région géographique spécifiée.

Pour effectuer une recherche locale, une application doit suivre les étapes suivantes:

1.  Créer `MKLocalSearchRequest` un objet.
1.  Créez un `MKLocalSearch` objet à partir `MKLocalSearchRequest` du.
1.  Appelez la `Start` méthode sur l' `MKLocalSearch` objet.
1.  Récupérez `MKLocalSearchResponse` l’objet dans un rappel.


L’API recherche locale elle-même ne fournit pas d’interface utilisateur. Il n’a même pas besoin d’utiliser un mappage. Toutefois, pour faciliter l’utilisation de la recherche locale, une application doit fournir un moyen de spécifier une requête de recherche et d’afficher les résultats. En outre, étant donné que les résultats contiendront des données d’emplacement, il est souvent judicieux de les afficher sur une carte.

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>Ajout d’une interface utilisateur de recherche locale

L’une des façons d’accepter une entrée de `UISearchBar`recherche est avec un, `UISearchController` qui est fourni par un et affiche les résultats dans une table.

Le code suivant ajoute le `UISearchController` (qui a une propriété de barre de recherche) `ViewDidLoad` dans la `MapViewController`méthode de:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;
            
//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller 
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;
```

Notez que vous êtes responsable de l’incorporation de l’objet de barre de recherche dans l’interface utilisateur. Dans cet exemple, nous l’avons attribué à TitleView de la barre de navigation, mais si vous n’utilisez pas de contrôleur de navigation dans votre application, vous devrez trouver un autre emplacement pour l’afficher.

Dans cet extrait de code, nous avons créé un autre contrôleur `searchResultsController` d’affichage personnalisé – – qui affiche les résultats de la recherche, puis nous avons utilisé cet objet pour créer notre objet de contrôleur de recherche. Nous avons également créé une nouvelle mise à jour de recherche, qui devient active lorsque l’utilisateur interagit avec la barre de recherche. Il reçoit des notifications sur les recherches avec chaque séquence de touches et est responsable de la mise à jour de l’interface utilisateur.
Nous verrons comment implémenter à la fois le `searchResultsController` et le `searchResultsUpdater` plus loin dans ce guide.

Cela entraîne l’affichage d’une barre de recherche sur la carte, comme indiqué ci-dessous:

 ![](images/07-searchbar.png "Barre de recherche affichée sur la carte")
 


### <a name="displaying-the-search-results"></a>Affichage des résultats de la recherche

Pour afficher les résultats de la recherche, nous devons créer un contrôleur d’affichage personnalisé. `UITableViewController`normalement. Comme indiqué ci-dessus `searchResultsController` , est passé au constructeur `searchController` du lorsqu’il est créé.
Le code suivant est un exemple de création de ce contrôleur d’affichage personnalisé:

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });


    }
}
```

### <a name="updating-the-search-results"></a>Mise à jour des résultats de la recherche

Le `SearchResultsUpdater` agit comme un médiateur entre la barre `searchController`de recherche de et les résultats de recherche. 

Dans cet exemple, nous devons d’abord créer la méthode Search dans `SearchResultsViewController`le. Pour ce faire, nous devons créer `MKLocalSearch` un objet et l’utiliser pour lancer une recherche pour `MKLocalSearchRequest`une, les résultats sont récupérés dans un rappel passé `Start` à la méthode `MKLocalSearch` de l’objet. Les résultats sont ensuite retournés `MKLocalSearchResponse` dans un objet contenant un `MKMapItem` tableau d’objets:

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });


}
```

Ensuite, dans notre `MapViewController` , nous allons créer une implémentation personnalisée `UISearchResultsUpdating`de, qui est affectée à `SearchResultsUpdater` la propriété de `searchController` notre dans la section [Ajout d’une interface utilisateur de recherche locale](#Adding_a_Local_Search_UI) :

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

L’implémentation ci-dessus ajoute une annotation à la carte lorsqu’un élément est sélectionné dans les résultats, comme indiqué ci-dessous:

 ![](images/08-search-results.png "Annotation ajoutée à la carte lorsqu’un élément est sélectionné dans les résultats")
 
> [!IMPORTANT]
> `UISearchController`a été implémenté dans iOS 8. Si vous souhaitez prendre en charge des appareils antérieurs à cette version, vous devez `UISearchDisplayController`utiliser.



## <a name="summary"></a>Récapitulatif

Cet article a examiné l’infrastructure du *Kit* *Map* pour iOS. Tout d’abord, il a vu `MKMapView` comment la classe autorise l’inclusion de mappages interactifs dans une application. Ensuite, il a démontré comment personnaliser davantage les mappages à l’aide d’annotations et de superpositions. Enfin, il a examiné les fonctionnalités de recherche locales qui ont été ajoutées au kit de cartes avec iOS 6,1, en indiquant comment utiliser les requêtes d’emplacement pour les points d’intérêt et les ajouter à une carte.

## <a name="related-links"></a>Liens associés

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
