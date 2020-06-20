---
title: Résumé du chapitre 28. Emplacement et mappages
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 28. Emplacement et mappages'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 301dc65c7909603e117717a993959e3c73fa2d32
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84133404"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Résumé du chapitre 28. Emplacement et mappages

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

Xamarin.Formsprend en charge un [`Map`](xref:Xamarin.Forms.Maps.Map) élément qui dérive de `View` . En raison des exigences spécifiques de la plateforme impliquées dans l’utilisation des mappages, elles sont implémentées dans un assembly distinct, ** Xamarin.Forms . Mappe**et implique un espace de noms différent : `Xamarin.Forms.Maps` .

## <a name="the-geographic-coordinate-system"></a>Système de coordonnées géographiques

Un système de coordonnées géographiques identifie les positions sur un objet sphérique (ou presque sphérique) comme la terre. Une coordonnée est constituée d’un angle de *Latitude* et de *longitude* exprimés en angles.

Un grand cercle appelé `equator` se trouve à mi-chemin entre les deux pôles sur lesquels l’axe de la terre s’étend sur le plan conceptuel.

### <a name="parallels-and-latitude"></a>Parallèles et Latitude

Angle mesuré au nord ou au sud de l’Équateur à partir du Centre des lignes des marques de terre de même latitude appelées *parallèles*. Celles-ci sont comprises entre 0 et 90 degrés aux pôles Nord et Sud. Par Convention, les latitudes nord de l’Équateur sont des valeurs positives et celles du sud de l’Équateur sont des valeurs négatives.

### <a name="longitude-and-meridians"></a>Longitude et méridiens

Les deux moitiés de grands cercles du pôle Nord au pôle Sud sont des lignes de longitude égale, également appelées *méridiens*. Celles-ci sont relatives au premier méridien de Greenwich, en Angleterre. Par Convention, la longitude est du premier méridien est une valeur positive comprise entre 0 et 180 degrés, tandis que la longitude ouest du premier méridien est une valeur négative comprise entre 0 et &ndash; 180 degrés.

### <a name="the-equirectangular-projection"></a>Projection équirectangulaire

Tout plan plat de la terre présente des distorsions. Si toutes les lignes de latitude et de longitude sont rectilignes et que les différences de latitude et de longitude sont égales à celles de la carte, le résultat est une *projection équirectangulaire*. Cette carte déforme les zones plus proches des pôles, car elles sont étirées horizontalement.

### <a name="the-mercator-projection"></a>Projection Mercator

La *projection Mercator* populaire tente de compenser l’étirement horizontal en étirant également ces zones verticalement. Cela aboutit à une carte dans laquelle les zones proches des pôles apparaissent beaucoup plus volumineuses qu’elles ne le sont réellement, mais toute zone locale est conforme à la zone réelle.

### <a name="map-services-and-tiles"></a>Mapper les services et les vignettes

Les services cartographiques utilisent une variation de la projection Mercator appelée `Web Mercator` . Les services cartographiques fournissent des vignettes bitmap à un client en fonction de l’emplacement et du niveau de zoom.

## <a name="getting-the-users-location"></a>Obtention de l’emplacement de l’utilisateur

Les Xamarin.Forms `Map` classes n’incluent pas de fonction permettant d’obtenir l’emplacement géographique de l’utilisateur, mais cela est souvent souhaitable quand vous utilisez des mappages, donc un service de dépendance doit le gérer.

> [!NOTE]
> Xamarin.Formsles applications peuvent utiliser à la place la [`Geolocation`](~/essentials/geolocation.md) classe incluse dans Xamarin.Essentials .

### <a name="the-location-tracker-api"></a>API de suivi d’emplacement

La solution [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contient du code pour une API de suivi d’emplacement. La [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) structure encapsule une latitude et une longitude. L' [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interface définit deux méthodes pour démarrer et suspendre le suivi d’emplacement, ainsi qu’un événement lorsqu’un nouvel emplacement est disponible.

#### <a name="the-ios-location-manager"></a>Gestionnaire d’emplacement iOS

L’implémentation iOS de `ILocationTracker` est une [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe qui utilise iOS [`CLLocationManager`](xref:CoreLocation.CLLocationManager) .

#### <a name="the-android-location-manager"></a>Gestionnaire d’emplacement Android

L’implémentation Android de `ILocationTracker` est une [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe qui utilise la [`LocationManager`](xref:Android.Locations.LocationManager) classe Android.

#### <a name="the-uwp-geo-locator"></a>Localisateur géo UWP

L’implémentation plateforme Windows universelle de `ILocationTracker` est une [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe qui utilise la UWP [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator) .

### <a name="display-the-phones-location"></a>Afficher l’emplacement du téléphone

L’exemple [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) utilise le suivi d’emplacement pour afficher l’emplacement du téléphone, à la fois dans le texte et sur une carte de équirectangulaire.

### <a name="the-required-overhead"></a>La surcharge requise

Une surcharge est nécessaire pour que **WhereAmI** utilise le suivi d’emplacement. Tout d’abord, tous les projets de la solution **WhereAmI** doivent avoir des références aux projets correspondants dans **Xamarin. FormsBook. Platform**, et chaque projet **WhereAmI** doit appeler la `Toolkit.Init` méthode.

Une surcharge supplémentaire spécifique à la plateforme, sous la forme d’autorisations d’emplacement, est requise.

#### <a name="location-permission-for-ios"></a>Autorisation d’emplacement pour iOS

Pour iOS, le fichier **info. plist** doit inclure les éléments contenant le texte d’une question demandant à l’utilisateur d’autoriser l’obtention de l’emplacement de cet utilisateur.

#### <a name="location-permissions-for-android"></a>Autorisations d’emplacement pour Android

Les applications Android qui obtiennent l’emplacement de l’utilisateur doivent disposer d’une autorisation ACCESS_FILE_LOCATION dans le fichier AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Autorisations d’emplacement pour la série UWP

Une application plateforme Windows universelle doit avoir une `location` fonctionnalité de périphérique marquée dans le fichier Package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Utilisation de Xamarin.Forms . Mount

Plusieurs exigences sont impliquées dans l’utilisation de la `Map` classe.

### <a name="the-nuget-package"></a>Package NuGet

** Xamarin.Forms . **La bibliothèque NuGet Maps doit être ajoutée à la solution d’application. Le numéro de version doit être identique à celui du **Xamarin.Forms** package actuellement installé.

### <a name="initializing-the-maps-package"></a>Initialisation du package Maps

Les projets d’application doivent appeler la `Xamarin.FormsMaps.Init` méthode après avoir effectué un appel à `Xamarin.Forms.Forms.Init` .

### <a name="enabling-map-services"></a>Activation des services de carte

Étant donné que le `Map` peut obtenir l’emplacement de l’utilisateur, l’application doit obtenir l’autorisation pour l’utilisateur de la manière décrite précédemment dans ce chapitre :

#### <a name="enabling-ios-maps"></a>Activation des mappages iOS

Une application iOS utilisant `Map` requiert deux lignes dans le fichier info. plist.

#### <a name="enabling-android-maps"></a>Activation des cartes Android

Une clé d’autorisation est requise pour l’utilisation des services Google Map. Cette clé est insérée dans le fichier **AndroidManifest.xml** . En outre, le fichier **AndroidManifest.xml** requiert des `manifest` balises impliquées dans l’obtention de l’emplacement de l’utilisateur.

#### <a name="enabling-uwp-maps"></a>Activation des cartes UWP

Une application plateforme Windows universelle nécessite une clé d’autorisation pour l’utilisation de Bing Maps. Cette clé est passée comme argument à la `Xamarin.FormsMaps.Init` méthode. L’application doit également être activée pour les services de localisation.

### <a name="the-unadorned-map"></a>Mappage inorne

L’exemple [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) se compose d’un fichier [MapsDemoHomePage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) et d’un fichier code-behind [MapsDemoHomePage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) qui permet d’accéder à différents programmes de démonstration.

Le fichier [BasicMapPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) montre comment afficher la [`Map`](xref:Xamarin.Forms.Maps.Map) vue. Par défaut, il affiche la ville de Rome, mais la carte peut être manipulée par l’utilisateur.

Pour désactiver le défilement horizontal et vertical, affectez [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) à la propriété la valeur `false` . Pour désactiver le zoom, affectez à la valeur [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) `false` . Ces propriétés peuvent ne pas fonctionner sur toutes les plateformes.

### <a name="streets-and-terrain"></a>Rues et terrain

Vous pouvez afficher différents types de mappages en définissant la `Map` propriété [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) de type [`MapType`](xref:Xamarin.Forms.Maps.MapType) , une énumération avec trois membres :

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), la valeur par défaut
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Le fichier [MapTypesPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) montre comment utiliser une case d’option pour sélectionner le type de carte. Il utilise la classe de [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) et une classe basée sur le fichier [MapTypeRadioButton. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) .

### <a name="map-coordinates"></a>Coordonnées de la carte

Un programme peut obtenir la zone actuelle `Map` affichée par l’intermédiaire de la [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propriété. Cette propriété n’est *pas* sauvegardée par une propriété pouvant être liée, et il n’existe aucun mécanisme de notification pour indiquer quand elle a changé, de sorte qu’un programme qui souhaite surveiller la propriété doit probablement utiliser un minuteur à cet effet.

`VisibleRegion`est de type [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , une classe avec quatre propriétés en lecture seule :

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)de type[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)de type `double` , indiquant la hauteur de la zone affichée de la carte
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)de type `double` , indiquant la largeur de la zone affichée de la carte
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)de type [`Distance`](xref:Xamarin.Forms.Maps.Distance) indiquant la taille de la zone circulaire la plus grande visible sur la carte

`Position`et `Distance` sont les deux structures. `Position`définit deux propriétés en lecture seule définies par le biais du [ `Position` constructeur](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`est destiné à fournir une distance indépendante de l’unité en convertissant les unités métriques et anglaises. Une `Distance` valeur peut être créée de plusieurs façons :

- [ `Distance` constructeur](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) avec une distance en mètres
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))méthode statique
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))méthode statique
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))méthode statique

La valeur est disponible à partir de trois propriétés :

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)de type`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)de type`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)de type`double`

Le fichier [MapCoordinatesPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contient plusieurs `Label` éléments permettant d’afficher les `MapSpan` informations. Le fichier code-behind [MapCoordinatesPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) utilise un minuteur pour conserver les informations à jour lorsque l’utilisateur manipule la carte.

### <a name="position-extensions"></a>Extensions de position

Une nouvelle bibliothèque de ce livre nommée [**Xamarin. FormsBook. Toolkit. Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contient des types spécifiques à la plateforme, mais indépendants de la plateforme. La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe a une `ToString` méthode pour `Position` , et une méthode pour calculer la distance entre deux `Position` valeurs.

### <a name="setting-an-initial-location"></a>Définition d’un emplacement initial

Vous pouvez appeler la [ `MoveToRegion` ] (XREF : Xamarin.Forms . Maps. map. MoveToRegion ( Xamarin.Forms . Maps. MapSpan)) de la méthode de `Map` pour définir par programme un niveau d’emplacement et de zoom sur la carte. L’argument est de type `MapSpan` . Vous pouvez créer un `MapSpan` objet à l’aide de l’une des méthodes suivantes :

- [ `MapSpan` constructor] (XREF : Xamarin.Forms . Maps. MapSpan .% 23ctor ( Xamarin.Forms . Maps. position, System. double, System. double)) avec un `Position` et une plage de latitude et de longitude
- [ `MapSpan.FromCenterAndRadius` ] (XREF : Xamarin.Forms . Maps. MapSpan. FromCenterAndRadius ( Xamarin.Forms . Maps. position, Xamarin.Forms . Maps. distance)) avec un `Position` et un rayon

Il est également possible de créer un nouveau `MapSpan` à partir d’un existant à l’aide des méthodes [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) ou [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)) .

Le fichier [WyomingPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) et le fichier code-behind [WyomingPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) montrent comment utiliser la `MoveToRegion` méthode pour afficher l’état de Wyoming.

Vous pouvez également utiliser le [ `Map` constructeur] (XREF : Xamarin.Forms . Maps. map .% 23ctor ( Xamarin.Forms . Maps. MapSpan)) avec un `MapSpan` objet pour initialiser l’emplacement de la carte. Le fichier [XamarinHQPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) montre comment procéder entièrement en XAML pour afficher le siège de Xamarin à San Francisco.

### <a name="dynamic-zooming"></a>Zoom dynamique

Vous pouvez utiliser un `Slider` pour effectuer un zoom dynamique sur une carte. Le fichier [RadiusZoomPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) et le fichier code-behind [RadiusZoomPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) montrent comment modifier le rayon d’une carte en fonction de la `Slider` valeur.

Le fichier [LongitudeZoomPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) et le fichier code-behind [LongitudeZoomPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) affichent une autre approche qui fonctionne mieux sur Android, mais aucune de ces approches ne fonctionne bien sur les plateformes Windows.

### <a name="the-phones-location"></a>Emplacement du téléphone

La [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriété de `Map` fonctionne un peu différemment sur chaque plateforme, comme le montre le fichier [ShowLocationPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) :

- Sur iOS, un point bleu indique l’emplacement du téléphone, mais vous devez naviguer manuellement
- Sur Android, une icône s’affiche lorsque Push déplace la carte vers l’emplacement du téléphone.
- Le UWP est semblable à iOS, mais arrive parfois automatiquement à l’emplacement

Le projet **MapDemos** tente de reproduire l’approche Android en définissant d’abord un bouton basé sur une icône basé sur le fichier [MyLocationButton. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) et le fichier code-behind [MyLocationButton.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) .

Le fichier [GoToLocationPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) et le fichier code-behind [GoToLocationPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) utilisent ce bouton pour accéder à l’emplacement du téléphone.

### <a name="pins-and-science-museums"></a>Pin et musées scientifiques

Enfin, la `Map` classe définit une [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) propriété de type `IList<Pin>` . La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe définit quatre propriétés :

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)de type `string` , une propriété obligatoire
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)de type `string` , une adresse facultative explicite
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)de type `Position` indiquant où le code confidentiel est affiché sur la carte
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)de type [`PinType`](xref:Xamarin.Forms.Maps.PinType) , une énumération, qui n’est pas utilisée

Le projet **MapDemos** contient le fichier [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), qui répertorie les musées scientifiques dans le États-Unis, et les [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classes et pour la désérialisation de ces données.

Le fichier [ScienceMuseumsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) et le fichier code-behind [ScienceMuseumsPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) affichent les broches de ces musées scientifiques dans la carte. Quand l’utilisateur appuie sur un code confidentiel, il affiche l’adresse et un site Web pour l’Musée.

### <a name="the-distance-between-two-points"></a>Distance entre deux points

La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contient une [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) méthode avec un calcul simplifié de la distance entre deux emplacements géographiques.

Elle est utilisée dans le fichier [LocalMuseumsPage. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) et le fichier code-behind [LocalMuseumsPage.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) pour afficher également la distance au Musée à partir de l’emplacement de l’utilisateur :

[![Capture triple de la page musées locaux](images/ch28fg28-small.png "Distance à un emplacement")](images/ch28fg28-large.png#lightbox "Distance à un emplacement")

Le programme montre également comment restreindre dynamiquement le nombre de codes confidentiels en fonction de l’emplacement de la carte.

## <a name="geocoding-and-back-again"></a>Géocodage et retour

[** Xamarin.Forms . **](xref:Xamarin.Forms.Maps)L’assembly Maps contient également une [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe avec une [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) méthode qui convertit une adresse de texte en zéro ou plusieurs positions géographiques possibles, et une autre méthode [ `GetAddressesForPositionAsync` ] (XREF : Xamarin.Forms . Maps. géocoder. GetAddressesForPositionAsync ( Xamarin.Forms . Maps. position)) qui effectue une conversion dans l’autre direction.

Le fichier [GeocoderRoundTrip. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) et le fichier code-behind [GeocoderRoundTrip.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) illustrent cette fonctionnalité.

## <a name="related-links"></a>Liens connexes

- [Chapitre 28 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Exemples du chapitre 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.FormsCanal](~/xamarin-forms/user-interface/map/index.md)
