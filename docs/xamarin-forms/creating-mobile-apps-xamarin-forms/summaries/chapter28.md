---
title: Résumé du chapitre 28. Emplacement et mappages
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 28. Emplacement et mappages'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 8c0b60a5064bbcf605f66a7b9dcce5da1f84a015
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770896"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Résumé du chapitre 28. Emplacement et mappages

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Xamarin.Forms prend en charge un [ `Map` ](xref:Xamarin.Forms.Maps.Map) élément dérivé `View`. En raison des exigences de plate-forme spéciales liées à l’utilisation de cartes, elles sont implémentées dans un assembly distinct, **Xamarin.Forms.Maps**, impliquent l’intervention d’un autre espace de noms : `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>Le système de coordonnées géographique

Un système de coordonnées géographiques identifie les positions sur un objet sphérique (ou presque sphérique), comme la terre. Une coordonnée comprend à la fois un *latitude* et *longitude* exprimée en angles.

Un plus grand cercle appelé le `equator` se trouve à mi-chemin entre deux pôles par le biais duquel l’axe de la terre s’étend sur le plan conceptuel.

### <a name="parallels-and-latitude"></a>Parallels et latitude

Angle mesuré à l’Équateur à partir du centre des marques de terre Nord ou lignes de latitude égale appelé *parallels*. Ces comprises entre 0 degrés à l’Équateur et 90 degrés dans le nord et sud du centre pôles. Par convention, les latitudes au nord de l’Équateur sont des valeurs positives et celles au sud de l’Équateur sont des valeurs négatives.

### <a name="longitude-and-meridians"></a>Longitude et méridiens

Moitiés de cercles great depuis le pôle nord au Pôle Sud sont des lignes de longitude égale, également appelés *méridiens*. Il s’agit par rapport à du premier méridien de Greenwich en Angleterre. Par convention, longitudes à l’est des valeurs positives de 0 degrés et 180 degrés et longitudes à l’ouest du premier méridien sont des valeurs négatives comprises entre 0 degré à &ndash;180 degrés.

### <a name="the-equirectangular-projection"></a>La projection équirectangulaire

Tout mappage plat de la terre introduit des distorsions. Si toutes les lignes de latitude et longitude sont droits, et si égales différences dans les angles de latitude et longitude correspondent aux distances égales sur la carte, le résultat est un *projection équirectangulaire*. Ce mappage déforme plus près des zones et les pôles, car ils sont étirées horizontalement.

### <a name="the-mercator-projection"></a>La projection Mercator

La célèbre *projection Mercator* tente de compenser l’étirement horizontal en l’étirant également ces zones de verticalement. Cela entraîne une carte où les zones proches pôles apparaissent bien plus important qu’elles sont réellement, mais n’importe quel réseau local conforme très près à la zone réelle.

### <a name="map-services-and-tiles"></a>Services de mappage et des vignettes

Services de carte utilisent une variation de la projection Mercator appelée `Web Mercator`. Les services de mappage remettre les vignettes de bitmap à un client basé sur l’emplacement et niveau de zoom.

## <a name="getting-the-users-location"></a>Obtention de l’emplacement de l’utilisateur

Xamarin.Forms `Map` classes n’incluent pas une fonctionnalité pour obtenir l’emplacement géographique de l’utilisateur, mais il est souvent souhaitable lors de l’utilisation de cartes, par conséquent, un service de dépendance doit la gérer.

> [!NOTE]
> Les applications Xamarin.Forms peuvent utiliser à la place la [ `Geolocation` ](~/essentials/geolocation.md) classe inclus dans Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>L’API de traceur emplacement

Le [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solution contient le code pour une API de traceur emplacement. Le [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) structure encapsule une latitude et une longitude. Le [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interface définit deux méthodes pour démarrer et interrompre le dispositif de suivi d’emplacement et un événement lorsqu’un nouvel emplacement est disponible.

#### <a name="the-ios-location-manager"></a>Le Gestionnaire d’emplacement iOS

L’implémentation d’iOS de `ILocationTracker` est un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe rend utiliser d’iOS [ `CLLocationManager` ](xref:CoreLocation.CLLocationManager).

#### <a name="the-android-location-manager"></a>Le Gestionnaire d’emplacement Android

L’implémentation Android de `ILocationTracker` est un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe qui utilise le Android [ `LocationManager` ](xref:Android.Locations.LocationManager) classe.

#### <a name="the-uwp-geo-locator"></a>Le localisateur de géo UWP

L’implémentation de la plateforme Windows universelle de `ILocationTracker` est un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe qui utilise la plateforme Windows universelle [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator).

### <a name="display-the-phones-location"></a>Afficher l’emplacement du téléphone

Le [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) exemple utilise le dispositif de suivi d’emplacement pour afficher l’emplacement du téléphone, à la fois dans le texte et sur une carte équirectangulaire.

### <a name="the-required-overhead"></a>La surcharge de requis

Une certaine surcharge est requise pour **WhereAmI** à utiliser le dispositif de suivi d’emplacement. Tout d’abord, tous les projets dans le **WhereAmI** solution doit disposer de références aux projets correspondants dans **Xamarin.FormsBook.Platform**et chaque **WhereAmI** projet doit appeler le `Toolkit.Init` (méthode).

Une surcharge supplémentaire spécifique à la plateforme, sous la forme d’autorisations de localisation, est requise.

#### <a name="location-permission-for-ios"></a>Autorisation d’emplacement pour iOS

Pour iOS, le **info.plist** fichier doit inclure les éléments contenant le texte d’une question demandant à l’utilisateur pour permettre l’obtention de l’emplacement de l’utilisateur.

#### <a name="location-permissions-for-android"></a>Autorisations de localisation pour Android

Les applications Android qui obtiennent l’emplacement de l’utilisateur doivent avoir une autorisation ACCESS_FILE_LOCATION dans le fichier AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Autorisations de localisation pour la plateforme Windows universelle

Une application de plateforme Windows universelle doit avoir un `location` fonctionnalité de l’appareil est marqué dans le fichier Package.appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Utilisation de Xamarin.Forms.Maps

Plusieurs conditions requises sont impliqués dans à l’aide de la `Map` classe.

### <a name="the-nuget-package"></a>Le package NuGet

Le **Xamarin.Forms.Maps** bibliothèque NuGet doit être ajouté à la solution d’application. Le numéro de version doit être le même que le **Xamarin.Forms** package actuellement installé.

### <a name="initializing-the-maps-package"></a>L’initialisation de l’ensemble de cartes

Les projets d’application doivent appeler la `Xamarin.FormsMaps.Init` méthode après avoir effectué un appel à `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>L’activation des services de mappage

Étant donné que le `Map` peut obtenir l’emplacement de l’utilisateur, l’application doit obtenir l’autorisation nécessaire pour l’utilisateur de la manière décrite plus haut dans ce chapitre :

#### <a name="enabling-ios-maps"></a>L’activation d’iOS mappe

Une application iOS à l’aide `Map` a besoin de deux lignes dans le fichier info.plist.

#### <a name="enabling-android-maps"></a>L’activation d’Android mappe

Une clé d’autorisation est nécessaire pour l’utilisation des services de mappage de Google. Cette clé est insérée dans le **AndroidManifest.xml** fichier. En outre, le **AndroidManifest.xml** fichier nécessite `manifest` balises nécessaires pour obtenir l’emplacement de l’utilisateur.

#### <a name="enabling-uwp-maps"></a>L’activation UWP mappe

Une application de plateforme Windows universelle nécessite une clé d’autorisation pour l’utilisation de Bing Maps. Cette clé est passée en tant qu’argument à la `Xamarin.FormsMaps.Init` (méthode). L’application doit également être activée pour les services d’emplacement.

### <a name="the-unadorned-map"></a>La carte non

Le [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) exemple se compose d’un [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) fichier et [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) fichier de code-behind Permet d’accéder à différents programmes de démonstration.

Le [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) fichier montre comment afficher le [ `Map` ](xref:Xamarin.Forms.Maps.Map) vue. Par défaut, il affiche la ville de Rome, mais le mappage peut être manipulé par l’utilisateur.

Pour désactiver le défilement horizontal et vertical, définissez la [ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) propriété `false`. Pour désactiver le zoom, définissez [ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) à `false`. Ces propriétés peut ne pas fonctionnent sur toutes les plateformes.

### <a name="streets-and-terrain"></a>Rues et le Terrain

Vous pouvez afficher différents types de cartes en définissant le `Map` propriété [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) de type [ `MapType` ](xref:Xamarin.Forms.Maps.MapType), une énumération avec trois membres :

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), la valeur par défaut
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Le [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) fichier montre comment utiliser un bouton radio pour sélectionner le type de carte. Il utilise le [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque et une classe basée sur le [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) fichier.

### <a name="map-coordinates"></a>Coordonnées de la carte

Un programme peut obtenir la zone actuelle qui le `Map` affiche via les [ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion) propriété. Cette propriété est *pas* soutenu par une propriété pouvant être liée, et il n’existe aucun mécanisme de notification pour indiquer quand elle a changé, donc un programme qui souhaite surveiller la propriété doit probablement utiliser un minuteur à cet effet.

`VisibleRegion` est de type [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan), une classe avec quatre propriétés en lecture seule :

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) de type [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) de type `double`, indiquant la hauteur de la zone affichée de la carte
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) de type `double`, indiquant la largeur de la zone affichée de la carte
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) de type [ `Distance` ](xref:Xamarin.Forms.Maps.Distance), indiquant la taille de la plus grande surface circulaire visible sur la carte

`Position` et `Distance` sont les deux structures. `Position` définit deux propriétés en lecture seule définies via la [ `Position` constructeur](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` vise à fournir une distance centrée sur l’unité par la conversion entre les métriques et les unités en anglais. Un `Distance` valeur permettre être créée de plusieurs façons :

- [`Distance` constructeur](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) avec une distance en mètres
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) méthode statique
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) méthode statique
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) méthode statique

La valeur est disponible à partir de trois propriétés :

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) de type `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) de type `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) de type `double`

Le [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) fichier contient plusieurs `Label` éléments pour afficher les `MapSpan` plus d’informations. Le [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) fichier code-behind utilise un minuteur pour conserver les informations de mise à jour que l’utilisateur manipule la carte.

### <a name="position-extensions"></a>Extensions de position

Une nouvelle bibliothèque pour ce livre nommé [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contient des types spécifiques à la carte mais indépendante de la plateforme. Le [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe a un `ToString` méthode pour `Position`et une méthode pour calculer la distance entre deux `Position` valeurs.

### <a name="setting-an-initial-location"></a>Définissez un emplacement initial

Vous pouvez appeler la [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) méthode de `Map` pour définir par programmation un emplacement et niveau de zoom sur la carte. L’argument est de type `MapSpan`. Vous pouvez créer un `MapSpan` de l’objet à l’aide d’une des opérations suivantes :

- [`MapSpan` constructeur](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) avec un `Position`et l’intervalle de latitude et longitude
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) avec un `Position` et radius

Il est également possible de créer un nouveau `MapSpan` à partir d’un à l’aide de méthodes [ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) ou [ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

Le [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) fichier et [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) fichier code-behind montre comment utiliser le `MoveToRegion` méthode pour afficher l’état du Wyoming.

Vous pouvez également utiliser le [ `Map` constructeur](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) avec un `MapSpan` objet pour initialiser l’emplacement de la carte. Le [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) fichier montre comment effectuer cette opération entièrement dans XAML pour afficher le siège social de Xamarin à San Francisco.

### <a name="dynamic-zooming"></a>Le zoom dynamique

Vous pouvez utiliser un `Slider` pour zoomer dynamiquement une carte. Le [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) fichier et [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) fichier code-behind montrent comment modifier le rayon d’une table basée sur la `Slider` valeur.

Le [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) fichier et [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) fichier code-behind afficher une autre approche qui fonctionne mieux sur Android, mais aucune de ces approches fonctionne correctement sur le Windows plateformes.

### <a name="the-phones-location"></a>L’emplacement du téléphone

Le [ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser) propriété du `Map` fonctionne un peu différemment sur chaque plateforme en tant que le [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) fichier montre :

- Sur iOS, un point bleu indique l’emplacement du téléphone, mais vous devez accéder manuellement
- Sur Android, une icône s’affiche que quand vous appuyez déplace le mappage à l’emplacement du téléphone
- La plateforme Windows universelle est similaire à iOS, mais parfois automatiquement accède à l’emplacement

Le **MapDemos** projet tente de reproduire l’approche Android en premier définissant un bouton basé sur l’icône en fonction de la [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) fichier et [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) fichier code-behind.

Le [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) fichier et [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) fichier code-behind Utilisez ce bouton pour accéder à l’emplacement du téléphone.

### <a name="pins-and-science-museums"></a>Musées de science et codes confidentiels

Enfin, le `Map` classe définit un [ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins) propriété de type `IList<Pin>`. Le [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) classe définit quatre propriétés :

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) de type `string`, une propriété obligatoire
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) de type `string`, une adresse explicite facultative
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) de type `Position`, indiquant où le code confidentiel est affiché sur la carte
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) de type [ `PinType` ](xref:Xamarin.Forms.Maps.PinType), une énumération, qui n’est pas utilisée

Le **MapDemos** projet contient le fichier [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), qui répertorie des musées science aux États-Unis, et [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) et [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classes pour la désérialisation de ces données.

Le [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) fichier et [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) broches d’affichage de fichier code-behind pour ces musées science dans le mappage. Lorsque l’utilisateur appuie sur un code confidentiel, il affiche l’adresse et un site Web pour le musée.

### <a name="the-distance-between-two-points"></a>La distance entre deux points

Le [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contient un [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) méthode avec un calcul simplifié de la distance entre deux emplacements géographiques.

Cela est utilisé dans le [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) fichier et [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) fichier code-behind pour afficher également la distance rend au musée à partir de l’emplacement de l’utilisateur :

[![Capture d’écran triple de Page musées locale](images/ch28fg28-small.png "Distance vers un emplacement")](images/ch28fg28-large.png#lightbox "Distance vers un emplacement")

Le programme montre aussi comment limiter dynamiquement le nombre de codes confidentiels en fonction de l’emplacement de la carte.

## <a name="geocoding-and-back-again"></a>Géocodage et revenir

Le [ **Xamarin.Forms.Maps** ](xref:Xamarin.Forms.Maps) assembly contient également un [ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder) classe avec un [ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) méthode convertit une adresse de texte en zéro ou plus possibles géographiques positions et une autre méthode [ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) qui convertit dans l’autre direction.

Le [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) fichier et [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) fichier code-behind illustrent cette fonctionnalité.

## <a name="related-links"></a>Liens connexes

- [Chapitre 28 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Exemples de chapitre 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mappage de Xamarin.Forms](~/xamarin-forms/user-interface/map.md)
