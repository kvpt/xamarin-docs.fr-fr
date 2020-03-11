---
title: Utilisation de l’API Google Maps dans votre application
description: Comment implémenter les fonctionnalités de l’API de Google Maps v2 dans votre application Xamarin. Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027149"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Utilisation de l’API Google Maps dans votre application

L’utilisation de l’application Maps est très intéressante, mais parfois vous souhaitez inclure des cartes directement dans votre application. En plus de l’application Maps intégrée, Google offre également une [API de mappage native pour Android](https://developers.google.com/maps/documentation/android-sdk/intro).
L’API Maps est adaptée aux cas où vous souhaitez maintenir un contrôle accru sur l’expérience de mappage. Les éléments possibles avec l’API Maps sont les suivants :

- Modification par programmation du point de vue de la carte.
- Ajout et personnalisation des marqueurs.
- Annotation d’une carte avec des superpositions.

Contrairement à l’API Android de Google Maps actuellement dépréciée v1, l’API Android de Google Maps v2 fait partie des [Google Play services](https://developers.google.com/android/guides/overview).
Une application Xamarin. Android doit respecter certaines conditions préalables obligatoires avant de pouvoir utiliser l’API Android de Google Maps.

## <a name="google-maps-api-prerequisites"></a>Conditions préalables de l’API Google Maps

Vous devez effectuer plusieurs étapes avant de pouvoir utiliser l’API Maps, notamment :

- [Obtenir une clé API Maps](#obtain-maps-key)
- [Installer le kit de développement logiciel (SDK) Google Play Services](#install-gps-sdk)
- [Installer le package Xamarin. GooglePlayServices. Maps à partir de NuGet](#install-gpsmaps-nuget)
- [Spécifier les autorisations requises](#declare-permissions)
- [Si vous le souhaitez, créez un émulateur avec les API Google](#create-emulator-with-google-api)

### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />obtenir une clé API Google Maps

La première étape consiste à obtenir une clé API Google Maps (Notez que vous ne pouvez pas réutiliser une clé API à partir de l’API Google Maps v1 héritée). Pour plus d’informations sur l’obtention et l’utilisation de la clé API avec Xamarin. Android, consultez [obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> installer le kit de développement logiciel (SDK) Google Play Services

Google Play Services est une technologie de Google qui permet aux applications Android de tirer parti de diverses fonctionnalités de Google, telles que Google +, facturation In-App et Maps. Ces fonctionnalités sont accessibles sur les appareils Android en tant que services d’arrière-plan, qui sont contenus dans le [Google Play services apk](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Les applications Android interagissent avec Google Play Services via la bibliothèque cliente Google Play Services. Cette bibliothèque contient les interfaces et les classes pour les services individuels, tels que les mappages. Le diagramme suivant montre la relation entre une application Android et Google Play Services :

![Diagramme illustrant le Google Play Store la mise à jour de la Google Play Services APK](maps-api-images/play-services-diagram.png)

L’API des cartes Android est fournie dans le cadre de Google Play Services.
Pour qu’une application Xamarin. Android puisse utiliser l’API Maps, le kit de développement logiciel (SDK) Google Play Services doit être installé à l’aide du [Gestionnaire de Android SDK](~/android/get-started/installation/android-sdk.md). La capture d’écran suivante montre où se trouvent dans le gestionnaire de Android SDK le client Google Play services :

![Google Play Services s’affiche sous Extras dans le gestionnaire de Android SDK](maps-api-images/image01.png)

> [!NOTE]
> Le APK de services Google Play est un produit sous licence qui peut ne pas être présent sur tous les appareils. S’il n’est pas installé, Google Maps ne fonctionnera pas sur l’appareil.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> installer le package Xamarin. GooglePlayServices. Maps à partir de NuGet

Le [package Xamarin. GooglePlayServices. Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contient les liaisons Xamarin. Android pour l’API Google Play services Maps.
Pour ajouter le package de la carte Google Play Services, cliquez avec le bouton droit sur le dossier **références** de votre projet dans le Explorateur de solutions puis cliquez sur **gérer les packages NuGet...** :

![Explorateur de solutions de l’élément de menu contextuel gérer les packages NuGet sous Références](maps-api-images/image02.png)

Le gestionnaire de **package NuGet**s’ouvre. Cliquez sur **Parcourir** et entrez **Xamarin Google Play services Maps** dans le champ de recherche. Sélectionnez **Xamarin. GooglePlayServices. Maps** , puis cliquez sur **installer**. (Si ce package a déjà été installé, cliquez sur **mettre à jour**.) :

[![le gestionnaire de package NuGet avec le package Xamarin. GooglePlayServices. Maps sélectionné](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Notez que les packages de dépendances suivants sont également installés :

- **Xamarin. GooglePlayServices. base**
- **Xamarin. GooglePlayServices. sous-sol**
- **Xamarin. GooglePlayServices. Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> spécifier les autorisations requises

Les applications doivent identifier le matériel et les autorisations requises pour pouvoir utiliser l’API Google Maps.  Certaines autorisations sont accordées automatiquement par le kit de développement logiciel (SDK) Google Play Services, et il n’est pas nécessaire pour un développeur de les ajouter explicitement à **AndroidManfest. xml**:

- L' **accès à l’état du réseau** &ndash; l’API Maps doit être en mesure de vérifier s’il peut télécharger les vignettes de la carte.

- L’accès **internet** &ndash; l’accès à Internet est nécessaire pour télécharger les vignettes de carte et communiquer avec les serveurs Google Play pour l’accès aux API.

Les autorisations et les fonctionnalités suivantes doivent être spécifiées dans le **fichier fichier AndroidManifest. xml** pour l’API Android de Google Maps :

- **OpenGL es v2** &ndash; l’application doit déclarer la configuration requise pour OpenGL es v2.

- **Clé API Google Maps** &ndash; la clé API est utilisée pour confirmer que l’application est inscrite et autorisée à utiliser Google Play services. Pour plus d’informations sur cette clé, consultez [obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) .

- **Demandez l’ancien client Apache http** &ndash; les applications qui ciblent Android 9,0 (niveau d’API 28) ou supérieur doivent spécifier que le client Apache http hérité est une bibliothèque facultative à utiliser.

- L' **accès aux services Web google** &ndash; l’application a besoin d’autorisations pour accéder aux services Web de Google qui renvoient l’API Android Maps.

- Les **autorisations pour les notifications Google Play Services** &ndash; l’application doivent être autorisées à recevoir des notifications distantes de Google Play services.

- **L’accès aux fournisseurs d’emplacement** &ndash; il s’agit d’autorisations facultatives.
   Ils permettront à la classe `GoogleMap` d’afficher l’emplacement de l’appareil sur la carte.

En outre, Android 9 a supprimé la bibliothèque cliente Apache HTTP du bootclasspath et n’est donc pas disponible pour les applications qui ciblent l’API 28 ou une version ultérieure. La ligne suivante doit être ajoutée au nœud `application` de votre fichier **fichier AndroidManifest. xml** pour continuer à utiliser le client HTTP Apache dans les applications qui ciblent l’API 28 ou une version ultérieure :

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Les versions très anciennes du kit de développement logiciel (SDK) Google Play nécessitaient une application pour demander l’autorisation `WRITE_EXTERNAL_STORAGE`. Cette exigence n’est plus nécessaire avec les liaisons Xamarin récentes pour Google Play Services.

L’extrait de code suivant est un exemple des paramètres qui doivent être ajoutés à **fichier AndroidManifest. xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

En plus de demander les autorisations **fichier AndroidManifest. xml**, une application doit également effectuer des vérifications d’autorisation d’exécution pour le `ACCESS_COARSE_LOCATION` et les autorisations `ACCESS_FINE_LOCATION`. Consultez le guide des [autorisations Xamarin. Android](~/android/app-fundamentals/permissions.md) pour plus d’informations sur l’exécution de contrôles d’autorisation au moment de l’exécution.

### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />créer un émulateur avec les API Google

Dans le cas où un appareil Android physique avec Google Play services n’est pas installé, il est possible de créer une image d’émulateur pour le développement. Pour plus d’informations, consultez la [Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>La classe GoogleMap

Une fois les conditions préalables satisfaites, il est temps de commencer à développer l’application et à utiliser l’API des cartes Android. La classe [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) est l’API principale qu’une application Xamarin. Android utilisera pour afficher et interagir avec Google Maps pour Android. Cette classe a les responsabilités suivantes :

- L’interaction avec les services de Google Play pour autoriser l’application avec le service Web de Google.

- Téléchargement, mise en cache et affichage des vignettes de carte.

- Affichage des contrôles d’interface utilisateur tels que le panoramique et le zoom.

- Dessin des marqueurs et des formes géométriques sur les cartes.

La `GoogleMap` est ajoutée à une activité de l’une des deux manières suivantes :

- **MapFragment** : le [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) est un fragment spécialisé qui agit en tant qu’hôte pour l’objet `GoogleMap`. Le `MapFragment` nécessite l’API Android de niveau 12 ou version ultérieure.
   Les versions antérieures d’Android peuvent utiliser [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Ce guide se concentre sur l’utilisation de la classe `MapFragment`.

- **MapView** : le [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) est une sous-classe de vue spécialisée, qui peut agir en tant qu’hôte pour un objet `GoogleMap`. Les utilisateurs de cette classe doivent transférer toutes les méthodes du cycle de vie de l’activité à la classe `MapView`.

Chacun de ces conteneurs expose une propriété `Map` qui retourne une instance de `GoogleMap`. La préférence doit être donnée à la classe [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) , car il s’agit d’une API plus simple qui réduit le code réutilisable qu’un développeur doit implémenter manuellement.

### <a name="adding-a-mapfragment-to-an-activity"></a>Ajout d’un MapFragment à une activité

La capture d’écran suivante est un exemple de `MapFragment`simple :

[Capture d’écran ![d’un appareil affichant un fragment Google Map](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

À l’instar des autres classes de fragments, il existe deux façons d’ajouter une `MapFragment` à une activité :

- De **manière déclarative** , le `MapFragment` peut être ajouté par le biais du fichier de disposition XML pour l’activité. L’extrait de code XML suivant montre un exemple d’utilisation de l’élément `fragment` :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **Par programme** : la `MapFragment` peut être instanciée par programmation à l’aide de la méthode [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) , puis ajoutée à une activité. Cet extrait de code montre la manière la plus simple d’instancier un objet `MapFragment` et de l’ajouter à une activité :

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    Il est possible de configurer l’objet `MapFragment` en passant un objet [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) à `NewInstance`. Ce sujet est abordé dans la section [Propriétés de GoogleMap](#googlemap_object) qui s’affichent plus loin dans ce guide.

La méthode `MapFragment.GetMapAsync` est utilisée pour initialiser le [`GoogleMap`](#googlemap_object) qui est hébergé par le fragment et pour obtenir une référence à l’objet Map qui est hébergé par le `MapFragment`. Cette méthode prend un objet qui implémente l’interface `IOnMapReadyCallback`.

Cette interface a une méthode unique, `IMapReadyCallback.OnMapReady(MapFragment map)` qui sera appelée lorsqu’il est possible que l’application interagisse avec l’objet `GoogleMap`. L’extrait de code suivant montre comment une activité Android peut initialiser une `MapFragment` et implémenter l’interface `IOnMapReadyCallback` :

```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);

        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);

        // remainder of code omitted
    }

    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>Types de cartes

Il existe cinq types différents de cartes disponibles à partir de l’API Google Maps :

- **Normal** : il s’agit du type de carte par défaut. Il présente les routes et les caractéristiques naturelles importantes, ainsi que certains points d’intérêt artificiels (tels que les bâtiments et les ponts).

- **Satellite** : cette carte illustre la photographie du satellite.

- **Hybride** : cette carte illustre la photographie et les cartes routières du satellite.

- **Terrain** : présente principalement des caractéristiques topographiques avec des routes.

- **Aucun** : ce mappage ne charge pas de vignettes, il est rendu sous la forme d’une grille vide.

L’image ci-dessous montre trois des différents types de cartes, de gauche à droite (normal, hybride, terrain) :

[captures d’écran ![trois exemples de cartes : normale, hybride et terrain](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

La propriété `GoogleMap.MapType` est utilisée pour définir ou modifier le type de carte à afficher. L’extrait de code suivant montre comment afficher une carte satellite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="a-namegooglemap_object-googlemap-properties"></a><a name="googlemap_object" />les propriétés GoogleMap

`GoogleMap` définit plusieurs propriétés qui peuvent contrôler les fonctionnalités et l’apparence de la carte. L’une des méthodes permettant de configurer l’état initial d’un `GoogleMap` consiste à passer un objet [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) lors de la création d’un `MapFragment`. L’extrait de code suivant est un exemple d’utilisation d’un objet `GoogleMapOptions` lors de la création d’un `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

L’autre façon de configurer un `GoogleMap` consiste à manipuler des propriétés sur le [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) de l’objet Map. L’exemple de code suivant montre comment configurer un `GoogleMap` pour afficher les contrôles de zoom et une boussole :

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interaction avec le GoogleMap

L’API des cartes Android fournit des API qui permettent à une activité de modifier le point de vue, d’ajouter des marqueurs, de placer des superpositions personnalisées ou de dessiner des formes géométriques. Cette section explique comment accomplir certaines de ces tâches dans Xamarin. Android.

### <a name="changing-the-viewpoint"></a>Modification du point de vue

Les mappages sont modelés comme un plan plat sur l’écran, en fonction de la projection Mercator. La vue cartographique est celle d’une *caméra* qui regarde directement sur ce plan. La position de l’appareil photo peut être contrôlée en modifiant l’emplacement, le zoom, l’inclinaison et le palier. La classe [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) est utilisée pour déplacer l’emplacement de l’appareil photo. les objets `CameraUpdate` ne sont pas directement instanciés, à la place l’API Maps fournit la classe [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) .

Une fois qu’un objet `CameraUpdate` a été créé, il est passé en tant que paramètre aux méthodes [googlemap. MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) ou [googlemap. AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) . La méthode `MoveCamera` met à jour la carte instantanément, tandis que la méthode `AnimateCamera` fournit une transition lisse et animée.

Cet extrait de code est un exemple simple d’utilisation de la `CameraUpdateFactory` pour créer un `CameraUpdate` qui incrémente le niveau de zoom de la carte d’un niveau de zoom :

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

L’API Maps fournit un [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) qui regroupe toutes les valeurs possibles de la position de la caméra. Une instance de cette classe peut être fournie à la méthode [CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) , qui retourne un objet `CameraUpdate`. L’API Maps inclut également la classe [CameraPosition. Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) qui fournit une API Fluent pour la création d’objets `CameraPosition`.
L’extrait de code suivant montre un exemple de création d’un `CameraUpdate` à partir d’un `CameraPosition` et son utilisation pour modifier la position de la caméra sur un `GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);

    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);

    CameraPosition cameraPosition = builder.Build();

    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

Dans l’extrait de code précédent, un emplacement spécifique sur le mappage est représenté par la classe [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) . Le niveau de zoom est défini sur 18, qui est une mesure arbitraire du zoom utilisé par Google Maps. Le palier est la mesure de la boussole dans le sens des aiguilles d’une montre. La propriété Tilt contrôle l’angle de visualisation et spécifie un angle de 25 degrés par rapport à la verticale. La capture d’écran suivante montre les `GoogleMap` après l’exécution du code précédent :

[![exemple Google Map affichant un emplacement spécifié avec un angle d’affichage incliné](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>Dessin sur la carte

L’API des cartes Android fournit des API pour dessiner les éléments suivants sur une carte :

- **Marqueurs** : il s’agit d’icônes spéciales utilisées pour identifier un emplacement unique sur une carte.

- **Superpositions** : il s’agit d’une image qui peut être utilisée pour identifier une collection d’emplacements ou de zones sur la carte.

- **Lignes, polygones et cercles** : il s’agit d’API qui permettent aux activités d’ajouter des formes à une carte.

#### <a name="markers"></a>Markers

L’API Maps fournit une classe de [marqueur](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) qui encapsule toutes les données relatives à un emplacement unique sur une carte. Par défaut, la classe de marqueur utilise une icône standard fournie par Google Maps. Il est possible de personnaliser l’apparence d’un marqueur et de répondre aux clics de l’utilisateur.

##### <a name="adding-a-marker"></a>Ajout d’un marqueur

Pour ajouter un marqueur à un mappage, il est nécessaire de créer un nouvel objet [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) , puis d’appeler la méthode [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) sur une instance `GoogleMap`. Cette méthode retourne un objet [marqueur](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) .

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

Le titre du marqueur s’affiche dans une *fenêtre d’info* quand l’utilisateur appuie sur le marqueur. La capture d’écran suivante montre à quoi ressemble ce marqueur :

[![exemple Google Map avec un marqueur et une fenêtre d’info pour Vimy en relief](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personnalisation d’un marqueur

Il est possible de personnaliser l’icône utilisée par le marqueur en appelant la méthode `MarkerOptions.InvokeIcon` lors de l’ajout du marqueur à la carte.
Cette méthode prend un objet [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) contenant les données nécessaires pour restituer l’icône. La classe [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) fournit des méthodes d’assistance pour simplifier la création d’un `BitmapDescriptor`. La liste suivante présente quelques-unes de ces méthodes :

- `DefaultMarker(float colour)` &ndash; utilisez le marqueur Google Maps par défaut, mais modifiez la couleur.

- `FromAsset(string assetName)` &ndash; utilisez une icône personnalisée à partir du fichier spécifié dans le dossier composants.

- `FromBitmap(Bitmap image)` &ndash; utilisez la bitmap spécifiée comme icône.

- `FromFile(string fileName)` &ndash; créer l’icône personnalisée à partir du fichier au chemin d’accès spécifié.

- `FromResource(int resourceId)` &ndash; créer une icône personnalisée à partir de la ressource spécifiée.

L’extrait de code suivant montre un exemple de création d’un marqueur par défaut cyan coloré :

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);

    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Info (fenêtres)

Les *fenêtres info* sont des fenêtres spéciales qui permettent d’afficher des informations à l’utilisateur lorsqu’il appuie sur un marqueur spécifique. Par défaut, la fenêtre info affiche le contenu du titre du marqueur. Si le titre n’a pas été attribué, aucune fenêtre d’informations ne s’affiche. Une seule fenêtre d’information peut être affichée à la fois.

Il est possible de personnaliser la fenêtre d’informations en implémentant l’interface [googlemap. IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) . Il existe deux méthodes importantes sur cette interface :

- `public View GetInfoWindow(Marker marker)` &ndash; cette méthode est appelée pour obtenir une fenêtre d’informations personnalisée pour un marqueur. Si elle retourne `null`, le rendu de la fenêtre par défaut sera utilisé. Si cette méthode retourne une vue, cette vue est placée dans le cadre de la fenêtre info.

- `public View GetInfoContents(Marker marker)` &ndash; cette méthode sera appelée uniquement si GetInfoWindow retourne `null`. Cette méthode peut retourner une valeur `null` si le rendu par défaut du contenu de la fenêtre d’informations doit être utilisé. Sinon, cette méthode doit retourner une vue avec le contenu de la fenêtre info.

Une fenêtre d’informations n’est pas une vue en direct. Android convertit la vue en image bitmap statique et l’affiche sur l’image. Cela signifie qu’une fenêtre d’informations ne peut pas répondre aux événements tactiles ou aux gestes, et ne se met pas à jour automatiquement. Pour mettre à jour une fenêtre d’informations, il est nécessaire d’appeler la méthode [googlemap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) .

L’illustration suivante montre quelques exemples de certaines fenêtres d’informations personnalisées. Le contenu de l’image sur la gauche est personnalisé, tandis que l’image à droite a sa fenêtre et son contenu personnalisés avec des angles arrondis :

![Exemples de fenêtres de marquage pour Melbourne, y compris l’icône et le remplissage. La fenêtre de droite a des angles arrondis.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

Contrairement aux marqueurs, qui identifient un emplacement spécifique sur une carte, un [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) est une image qui est utilisée pour identifier une collection d’emplacements ou une zone sur la carte.

##### <a name="adding-a-groundoverlay"></a>Ajout d’un GroundOverlay

L’ajout d’une superposition de sol à une carte est semblable à l’ajout d’un marqueur à une carte. Tout d’abord, un objet [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) est créé. Cet objet est ensuite transmis en tant que paramètre à la méthode [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) , qui retourne un objet `GroundOverlay`. Cet extrait de code est un exemple d’ajout d’une superposition de sol à une carte :

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

La capture d’écran suivante montre cette superposition sur une carte :

[![exemple de carte avec une image superposée d’un support polaire](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Lignes, cercles et polygones

Il existe trois types simples de chiffres géométriques qui peuvent être ajoutés à une carte :

- **Polyline** : il s’agit d’une série de segments de ligne connectés. Il peut marquer un tracé sur une carte ou créer une forme géométrique.

- **Circle** : dessine un cercle sur la carte.

- **Polygon** : il s’agit d’une forme fermée pour le marquage de zones sur une carte.

##### <a name="polylines"></a>Polylignes

Une [polyligne](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) est une liste d’objets `LatLng` consécutifs qui spécifient les vertex de chaque segment de ligne. Une polyligne est créée en créant d’abord un objet `PolylineOptions` et en y ajoutant les points. L’objet `PolylineOption` est ensuite passé à un objet `GoogleMap` en appelant la méthode `AddPolyline`.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Cercles

Pour créer des cercles, vous devez d’abord instancier un objet [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) qui spécifie le centre et le rayon du cercle, en mètres. Le cercle est dessiné sur la carte en appelant [googlemap. AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
L’extrait de code suivant montre comment dessiner un cercle :

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Polygones

`Polygon`s sont similaires à `Polyline`s, mais ils ne sont pas ouverts. `Polygon`s sont une boucle fermée et son intérieur est rempli.
`Polygon`s sont créés exactement de la même manière qu’un `Polyline`, à l’exception de la méthode [googlemap. AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) appelée.

Contrairement à un `Polyline`, un `Polygon` se ferme automatiquement. Le polygone est fermé par la méthode `AddPolygon` en dessinant une ligne qui connecte le premier et le dernier point. L’extrait de code suivant crée un rectangle plein sur la même zone que l’extrait de code précédent dans l’exemple de `Polyline`.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```

## <a name="responding-to-user-events"></a>Réponse aux événements utilisateur

Il existe trois types d’interactions qu’un utilisateur peut avoir avec une carte :

- **Clic sur un marqueur** -l’utilisateur clique sur un marqueur.

- **Glisser-déplacer** -l’utilisateur a long-cliqué sur un mparger

- **Clic sur la fenêtre info** -l’utilisateur a cliqué sur une fenêtre d’informations.

Chacun de ces événements sera abordé plus en détail ci-dessous.

### <a name="marker-click-events"></a>Événements de clic de marqueur

L’événement `MarkerClicked` est déclenché lorsque l’utilisateur appuie sur un marqueur. Cet événement accepte un objet `GoogleMap.MarkerClickEventArgs` en tant que paramètre. Cette classe contient deux propriétés :

- `GoogleMap.MarkerClickEventArgs.Handled` &ndash; cette propriété doit avoir la valeur `true` pour indiquer que le gestionnaire d’événements a consommé l’événement. Si la valeur est `false`, le comportement par défaut se produit en plus du comportement personnalisé du gestionnaire d’événements.

- `Marker` &ndash; cette propriété est une référence au marqueur qui a déclenché l’événement `MarkerClick`.

Cet extrait de code montre un exemple de `MarkerClick` qui remplacera la position de la caméra par un nouvel emplacement sur la carte :

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);

        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```

### <a name="marker-drag-events"></a>Événements glisser des marqueurs

Cet événement est déclenché lorsque l’utilisateur souhaite faire glisser le marqueur. Par défaut, les marqueurs ne peuvent pas être déplacés. Un marqueur peut être défini comme pouvant être glissé en affectant à la propriété `Marker.Draggable` la valeur `true` ou en appelant la méthode `MarkerOptions.Draggable` avec `true` comme paramètre.

Pour faire glisser le marqueur, l’utilisateur doit d’abord cliquer longuement sur le marqueur, puis le doigt doit rester sur la carte. Lorsque le doigt de l’utilisateur est glissé sur l’écran, le marqueur se déplace. Lorsque le doigt de l’utilisateur est levé hors de l’écran, le marqueur reste en place.

La liste suivante décrit les différents événements qui seront déclenchés pour un marqueur pouvant être glissé :

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; cet événement est déclenché lorsque l’utilisateur fait glisser pour la première fois le marqueur.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; cet événement est déclenché lorsque le marqueur est glissé.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; cet événement est déclenché lorsque l’utilisateur a terminé de faire glisser le marqueur.

Chaque `EventArgs` contient une propriété unique appelée `P0` qui est une référence à l’objet `Marker` glissé.

### <a name="info-window-click-events"></a>Événements Click de la fenêtre Info

Une seule fenêtre d’informations peut être affichée à la fois. Quand l’utilisateur clique sur une fenêtre d’info dans une carte, l’objet Map génère un événement `InfoWindowClick`. L’extrait de code suivant montre comment associer un gestionnaire à l’événement :

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

Rappelez-vous qu’une fenêtre d’informations est une `View` statique qui est rendue sous la forme d’une image sur la carte. Les widgets, tels que les boutons, les cases à cocher ou les affichages de texte placés à l’intérieur de la fenêtre d’informations sont inertes et ne peuvent pas répondre à l’un de leurs événements d’utilisateur intégral.

## <a name="related-links"></a>Liens connexes

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [API Android de Google Maps v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [utilise-Library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [utilisations de-fonctionnalité](https://developer.android.com/guide/topics/manifest/uses-feature-element)
