---
title: Résumé du chapitre 28. Emplacement et mappages
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 28. Emplacement et mappages'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697076"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Résumé du chapitre 28. Emplacement et mappages

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Xamarin.Forms prend [`Map`](xref:Xamarin.Forms.Maps.Map) en charge `View`un élément qui dérive de . En raison des exigences spéciales de la plate-forme impliquées dans l’utilisation des cartes, ils sont `Xamarin.Forms.Maps`mis en œuvre dans un assemblage séparé, **Xamarin.Forms.Maps**, et impliquent un espace de nom différent: .

## <a name="the-geographic-coordinate-system"></a>Le système de coordination géographique

Un système de coordination géographique identifie les positions sur un objet sphérique (ou presque sphérique) comme la Terre. Une coordonnées se compose à la fois d’une *latitude* et *d’une longitude* exprimées dans les angles.

Un grand cercle `equator` appelé le est à mi-chemin entre les deux pôles à travers lequel l’axe de la Terre s’étend conceptuellement.

### <a name="parallels-and-latitude"></a>Parallèles et latitude

Un angle mesuré au nord ou au sud de l’équateur depuis le centre de la Terre marque des lignes de latitude égale connues sous le nom de *parallèles.* Ceux-ci vont de 0 degrés à l’équateur à 90 degrés aux pôles nord et sud. Selon convention, les latitudes au nord de l’équateur sont des valeurs positives, et celles au sud de l’équateur sont des valeurs négatives.

### <a name="longitude-and-meridians"></a>Longitude et méridiens

Les moitiés des grands cercles du pôle nord au pôle sud sont des lignes d’égale longitude, également connues sous le nom *de méridiens.* Ceux-ci sont par rapport au Premier Méridien à Greenwich, en Angleterre. Selon convention, les longitudes à l’est du Premier Méridien sont des valeurs positives de 0 degrés à 180 &ndash;degrés, et les longitudes à l’ouest du Premier Méridien sont des valeurs négatives de 0 degrés à 180 degrés.

### <a name="the-equirectangular-projection"></a>La projection equirectangular

Toute carte plate de la Terre introduit des distorsions. Si toutes les lignes de latitude et de longitude sont droites, et si des différences égales dans les angles de latitude et de longitude correspondent à des distances égales sur la carte, le résultat est une *projection equirectangulaire.* Cette carte déforme les zones plus proches des pôles parce qu’elles sont horizontalement étirées.

### <a name="the-mercator-projection"></a>La projection mercator

La projection populaire *mercator* tente de compenser l’étirement horizontal en étirant également ces zones verticalement. Il en résulte une carte où les zones proches des pôles semblent beaucoup plus grandes qu’elles ne le sont réellement, mais toute zone locale est conforme assez étroitement à la zone réelle.

### <a name="map-services-and-tiles"></a>Services de carte et tuiles

Les services de carte utilisent une `Web Mercator`variante de la projection Mercator appelée . Les services de carte fournissent des tuiles bitmap à un client en fonction de l’emplacement et le niveau de zoom.

## <a name="getting-the-users-location"></a>Obtenir l’emplacement de l’utilisateur

Les classes Xamarin.Forms `Map` ne comprennent pas une installation pour obtenir l’emplacement géographique de l’utilisateur, mais cela est souvent souhaitable lorsque vous travaillez avec des cartes, de sorte qu’un service de dépendance doit le gérer.

> [!NOTE]
> Les applications Xamarin.Forms [`Geolocation`](~/essentials/geolocation.md) peuvent plutôt utiliser la classe incluse dans Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>Le tracker de localisation API

La solution [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contient du code pour une API de suivi de localisation. La [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) structure encapsule une latitude et une longitude. L’interface [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) définit deux méthodes pour démarrer et mettre en pause le tracker de localisation, et un événement lorsqu’un nouvel emplacement est disponible.

#### <a name="the-ios-location-manager"></a>Le gestionnaire de localisation iOS

La mise en `ILocationTracker` œuvre iOS est [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) [`CLLocationManager`](xref:CoreLocation.CLLocationManager)une classe qui fait usage de l’iOS .

#### <a name="the-android-location-manager"></a>Le gestionnaire de localisation Android

La mise `ILocationTracker` en [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) œuvre Android est [`LocationManager`](xref:Android.Locations.LocationManager) une classe qui fait usage de la classe Android.

#### <a name="the-uwp-geo-locator"></a>Le géo-localisateur UWP

La mise en `ILocationTracker` œuvre de la plate-forme Windows universelle est une [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe qui utilise l’UWP [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator).

### <a name="display-the-phones-location"></a>Afficher l’emplacement du téléphone

[**L’échantillon WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) utilise le tracker de localisation pour afficher l’emplacement du téléphone, à la fois dans le texte et sur une carte equirectangular.

### <a name="the-required-overhead"></a>Les frais généraux requis

Certaines surcharges sont nécessaires pour **WhereAmI** d’utiliser le tracker de localisation. Tout d’abord, tous les projets de la solution **WhereAmI** doivent avoir des références aux projets correspondants dans **Xamarin.FormsBook.Platform**, et chaque projet **WhereAmI** doit appeler la `Toolkit.Init` méthode.

Quelques frais généraux supplémentaires spécifiques à la plate-forme, sous forme d’autorisations de localisation, sont nécessaires.

#### <a name="location-permission-for-ios"></a>Autorisation de localisation pour iOS

Pour iOS, le fichier **info.plist** doit inclure des éléments contenant le texte d’une question demandant à l’utilisateur de permettre d’obtenir l’emplacement de cet utilisateur.

#### <a name="location-permissions-for-android"></a>Autorisations de localisation pour Android

Les applications Android qui obtiennent l’emplacement de l’utilisateur doivent avoir une autorisation ACCESS_FILE_LOCATION dans le fichier AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Autorisations de localisation pour l’UWP

Une application Universal Windows `location` Platform doit avoir une capacité d’appareil marquée dans le fichier Package.appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Travailler avec Xamarin.Forms.Maps

Plusieurs exigences sont impliquées `Map` dans l’utilisation de la classe.

### <a name="the-nuget-package"></a>Le paquet NuGet

La bibliothèque **NuGet Xamarin.Forms.Maps** doit être ajoutée à la solution d’application. Le numéro de version doit être le même que le paquet **Xamarin.Forms** actuellement installé.

### <a name="initializing-the-maps-package"></a>Initialisation du paquet Cartes

Les projets d’application doivent appeler la `Xamarin.FormsMaps.Init` méthode après avoir fait un appel à `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Permettre l’activation des services cartographiques

Étant `Map` donné que l’application peut obtenir l’emplacement de l’utilisateur, l’application doit obtenir l’autorisation de l’utilisateur de la manière décrite plus tôt dans ce chapitre :

#### <a name="enabling-ios-maps"></a>Permettre des cartes iOS

Une application iOS utilisant `Map` a besoin de deux lignes dans le fichier info.plist.

#### <a name="enabling-android-maps"></a>Permettre des cartes Android

Une clé d’autorisation est requise pour l’utilisation des services Google Map. Cette clé est insérée dans le fichier **AndroidManifest.xml.** En outre, le fichier **AndroidManifest.xml** nécessite des `manifest` balises impliquées dans l’obtention de l’emplacement de l’utilisateur.

#### <a name="enabling-uwp-maps"></a>Permettre des cartes UWP

Une application Universal Windows Platform nécessite une clé d’autorisation pour l’utilisation de Bing Maps. Cette clé est adoptée comme `Xamarin.FormsMaps.Init` un argument à la méthode. L’application doit également être activée pour les services de localisation.

### <a name="the-unadorned-map"></a>La carte sans fioritures

[**L’échantillon MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) se compose d’un fichier [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) et [d’un](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) fichier MapsDemoHomePage.xaml.cs de code qui permet de naviguer vers divers programmes de démonstration.

Le fichier [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) montre [`Map`](xref:Xamarin.Forms.Maps.Map) comment afficher la vue. Par défaut, il affiche la ville de Rome, mais la carte peut être manipulée par l’utilisateur.

Pour désactiver le défilement horizontal [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) et `false`vertical, définissez la propriété à . Pour désactiver le zoom, réglé [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) pour `false`. Ces propriétés peuvent ne pas fonctionner sur toutes les plates-formes.

### <a name="streets-and-terrain"></a>Rues et Terrain

Vous pouvez afficher différents types `Map` de [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) cartes [`MapType`](xref:Xamarin.Forms.Maps.MapType)en définissant la propriété du type, un recensement avec trois membres :

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), par défaut
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Le fichier [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) montre comment utiliser un bouton radio pour sélectionner le type de carte. Il utilise la [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) et une classe basée sur le fichier [MapTypeRadioButton.xaml.](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)

### <a name="map-coordinates"></a>Coordonnées cartographiques

Un programme peut obtenir la `Map` zone actuelle [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) que le fait d’afficher à travers la propriété. Cette propriété *n’est pas* étayée par une propriété liant, et il n’y a pas de mécanisme de notification pour indiquer quand il a changé, donc un programme qui souhaite surveiller la propriété devrait probablement utiliser une minuterie à cette fin.

`VisibleRegion`est de [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)type , une classe avec quatre propriétés lus seulement:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)de type[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)type `double`, indiquant la hauteur de la zone affichée de la carte
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)type `double`, indiquant la largeur de la zone affichée de la carte
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)de [`Distance`](xref:Xamarin.Forms.Maps.Distance)type , indiquant la taille de la plus grande zone circulaire visible sur la carte

`Position`et `Distance` sont les deux structures. `Position`définit deux propriétés de lecture [ `Position` ](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double))seulement définies via le constructeur :

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`vise à fournir une distance unitaire indépendante en convertissant entre les unités métriques et anglaises. Une `Distance` valeur peut être créée de plusieurs façons :

- constructeur avec une distance en mètres [ `Distance` ](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))méthode statique
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))méthode statique
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))méthode statique

La valeur est disponible à partir de trois propriétés:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)de type`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)de type`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)de type`double`

Le fichier [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contient `Label` plusieurs `MapSpan` éléments pour afficher les informations. Le [fichier MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) de code-derrière utilise une minuterie pour garder les informations mises à jour pendant que l’utilisateur manipule la carte.

### <a name="position-extensions"></a>Extensions de position

Une nouvelle bibliothèque pour ce livre nommé [**Xamarin.FormsBook.Toolkit.Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contient des types spécifiques à la carte mais indépendants de la plate-forme. La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe `ToString` a `Position`une méthode pour , et `Position` une méthode pour calculer la distance entre deux valeurs.

### <a name="setting-an-initial-location"></a>Définir un emplacement initial

Vous pouvez [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) appeler `Map` la méthode de définir programmatiquement un niveau de localisation et de zoom sur la carte. L’argument est `MapSpan`de type . Vous pouvez `MapSpan` créer un objet en utilisant l’un ou l’autre des éléments suivants :

- constructeur avec `Position`une, et la latitude et la longitude span [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance))avec `Position` un rayon et un rayon

Il est également possible de `MapSpan` créer un nouveau [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) à [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))partir d’un existant en utilisant les méthodes ou .

Le fichier [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) et [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) fichier de code-derrière `MoveToRegion` démontre comment utiliser la méthode pour afficher l’état du Wyoming.

Vous pouvez également [ `Map` ](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) utiliser le `MapSpan` constructeur avec un objet pour initialiser l’emplacement de la carte. Le fichier [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) montre comment le faire entièrement dans XAML pour afficher le siège de Xamarin à San Francisco.

### <a name="dynamic-zooming"></a>Zoom dynamique

Vous pouvez `Slider` utiliser un pour zoomer dynamiquement une carte. Le fichier [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) et [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) fichier de code-derrière montrent comment changer `Slider` le rayon d’une carte basée sur la valeur.

Le fichier [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) et [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) fichier de code-derrière montrent une approche alternative qui fonctionne mieux sur Android, mais ni l’approche ne fonctionne bien sur les plates-formes Windows.

### <a name="the-phones-location"></a>Emplacement du téléphone

La [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriété `Map` des œuvres un peu différemment sur chaque plate-forme comme le montre le fichier [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) :

- Sur iOS, un point bleu indique l’emplacement du téléphone, mais vous devez y naviguer manuellement
- Sur Android, une icône est affichée que lorsqu’il est poussé déplace la carte à l’emplacement du téléphone
- L’UWP est similaire à iOS, mais navigue parfois automatiquement à l’emplacement

Le projet **MapDemos** tente d’imiter l’approche Android en définissant d’abord un bouton basé sur une icône basé sur le fichier [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) et [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) fichier de code.derrière.

Le fichier [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) et [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) fichier de code-derrière utilisent ce bouton pour naviguer à l’emplacement du téléphone.

### <a name="pins-and-science-museums"></a>Pins et musées scientifiques

Enfin, `Map` la classe [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) définit une `IList<Pin>`propriété de type . La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe définit quatre propriétés :

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)de `string`type , une propriété requise
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)de `string`type , une adresse facultative lisible par l’homme
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)type `Position`, indiquant où la broche est affichée sur la carte
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)de [`PinType`](xref:Xamarin.Forms.Maps.PinType)type, un recensement, qui n’est pas utilisé

Le projet **MapDemos** contient le fichier [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), qui [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) répertorie les musées scientifiques aux États-Unis, et [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) les classes pour déséialiser ces données.

Le fichier [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) et [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) épingles d’affichage de fichiers de code-derrière pour ces musées scientifiques dans la carte. Lorsque l’utilisateur tape une épingle, il affiche l’adresse et un site Web pour le musée.

### <a name="the-distance-between-two-points"></a>La distance entre deux points

La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) contient une méthode avec un calcul simplifié de la distance entre deux emplacements géographiques.

Ceci est utilisé dans le fichier [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) et [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) fichier de code-derrière pour également afficher la distance au musée de l’emplacement de l’utilisateur:

[![Triple capture d’écran de la page des musées locaux](images/ch28fg28-small.png "Distance à un emplacement")](images/ch28fg28-large.png#lightbox "Distance à un emplacement")

Le programme montre également comment limiter dynamiquement le nombre d’épingles en fonction de l’emplacement de la carte.

## <a name="geocoding-and-back-again"></a>Géocodage et retour

L’assemblage [**Xamarin.Forms.Maps**](xref:Xamarin.Forms.Maps) [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) contient également [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) une classe avec une méthode qui convertit une [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) adresse textuelle en positions géographiques nulles ou plus possibles, et une autre méthode qui se convertit dans l’autre sens.

Le fichier [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) et [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) fichier de code-derrière démontrent cette installation.

## <a name="related-links"></a>Liens connexes

- [Chapitre 28 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Échantillons du chapitre 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Carte Xamarin.Forms](~/xamarin-forms/user-interface/map/index.md)
