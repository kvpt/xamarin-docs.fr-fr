---
titre : « Xamarin.Forms mappage » Description : «le contrôle de carte affiche un mappage et requiert le Xamarin.Forms . Mappe le package NuGet.»
ms. Prod : xamarin ms. AssetID : B669B5EE-D24C-4C69-93E1-2CA5CC9108B5 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/29/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map"></a>Xamarin.FormsCanal

## <a name="initialization-and-configuration"></a>[Initialisation et configuration](setup.md)

[ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Maps/)Le package NuGet Maps est requis pour utiliser la fonctionnalité Maps dans une application. En outre, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour être accordé à l’application.

## <a name="map-control"></a>[Map Control](map.md)

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle est une vue multiplateforme permettant d’afficher et d’annoter des cartes. Il utilise le contrôle de carte natif pour chaque plateforme, en fournissant une expérience de cartes rapide et familière aux utilisateurs.

## <a name="position-and-distance"></a>[Position et distance](position-distance.md)

Le [`Position`](xref:Xamarin.Forms.Maps.Position) struct est généralement utilisé lors du positionnement d’un mappage et de ses codes confidentiels, et du [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct qui peut éventuellement être utilisé lors du positionnement d’un mappage.

## <a name="pins"></a>[Épingles](pins.md)

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle permet aux emplacements d’être marqués avec des [`Pin`](xref:Xamarin.Forms.Maps.Pin) objets. Un `Pin` est un marqueur de carte qui ouvre une fenêtre d’informations lorsqu’il est appuyé.

## <a name="polygons-polylines-and-circles"></a>[Polygones, polylignes et cercles](polygons.md)

`Polygon``Polyline`les éléments, et `Circle` vous permettent de mettre en surbrillance des zones spécifiques sur une carte. Un `Polygon` est une forme entièrement incluse qui peut avoir un trait et une couleur de remplissage. Un `Polyline` est une ligne qui ne délimite pas complètement une zone. `Circle`Met en surbrillance une zone circulaire de la carte.

## <a name="geocoding"></a>[Géocodage](geocoder.md)

La [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe effectue une conversion entre des adresses de chaîne et des coordonnées de latitude et de longitude stockées dans des [`Position`](xref:Xamarin.Forms.Maps.Position) objets.

## <a name="launch-the-native-map-app"></a>[Lancer l’application de carte native](native-map-app.md)

L’application Map native sur chaque plateforme peut être lancée à partir d’une Xamarin.Forms application par la Xamarin.Essentials `Launcher` classe.
