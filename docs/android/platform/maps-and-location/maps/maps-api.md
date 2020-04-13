---
title: Utilisation de l’API Google Maps dans votre application
description: Comment implémenter Google Maps API v2 fonctionnalités dans votre application Xamarin.Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027149"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Utilisation de l’API Google Maps dans votre application

L’utilisation de l’application Maps est excellente, mais parfois vous souhaitez inclure des cartes directement dans votre application. En plus de l’application de cartes intégrées, Google offre également une [API de cartographie native pour Android](https://developers.google.com/maps/documentation/android-sdk/intro).
L’API Maps convient aux cas où vous souhaitez garder plus de contrôle sur l’expérience de cartographie. Les choses qui sont possibles avec l’API Maps comprennent:

- Modifier programmatiquement le point de vue de la carte.
- Ajout et personnalisation des marqueurs.
- Annoter une carte avec des superpositions.

Contrairement à la désormais dépréciée Google Maps Android API v1, Google Maps Android API v2 fait partie de [Google Play Services](https://developers.google.com/android/guides/overview).
Une application Xamarin.Android doit répondre à certaines conditions préalables obligatoires avant qu’il ne soit possible d’utiliser l’API Android Google Maps.

## <a name="google-maps-api-prerequisites"></a>Conditions préalables à l’API de Google Maps

Plusieurs étapes doivent être prises avant de pouvoir utiliser l’API Maps, notamment :

- [Obtenir une clé API Maps](#obtain-maps-key)
- [Installer le Google Play Services SDK](#install-gps-sdk)
- [Installer le forfait Xamarin.GooglePlayServices.Maps à partir de NuGet](#install-gpsmaps-nuget)
- [Spécifier les autorisations requises](#declare-permissions)
- [Optionnellement, créez un émulateur avec les API Google](#create-emulator-with-google-api)

### <a name="obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Obtenir une clé API Google Maps

La première étape consiste à obtenir une clé aPI Google Maps (notez que vous ne pouvez pas réutiliser une clé API de l’héritage Google Maps v1 API). Pour plus d’informations sur la façon d’obtenir et d’utiliser la clé API avec Xamarin.Android, voir [Obtenir une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="install-the-google-play-services-sdk"></a><a name="install-gps-sdk" />Installer le Google Play Services SDK

Google Play Services est une technologie de Google qui permet aux applications Android de tirer parti de diverses fonctionnalités Google telles que Google, In-App Billing et Maps. Ces fonctionnalités sont accessibles sur les appareils Android en tant que services de fond, qui sont contenus dans le [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Les applications Android interagissent avec Google Play Services via la bibliothèque client Google Play Services. Cette bibliothèque contient les interfaces et les classes pour les services individuels tels que Maps. Le diagramme suivant montre la relation entre une application Android et Google Play Services :

![Diagramme illustrant le Google Play Store mettant à jour l’APK De Google Play Services](maps-api-images/play-services-diagram.png)

L’API Android Maps est fournie dans le cadre de Google Play Services.
Avant qu’une application Xamarin.Android puisse utiliser l’API Maps, le Google Play Services SDK doit être installé à l’aide du [gestionnaire Android SDK](~/android/get-started/installation/android-sdk.md). La capture d’écran suivante montre où dans le gestionnaire Android SDK le client des services Google Play peut être trouvé:

![Google Play Services apparaît sous Extras dans le gestionnaire Android SDK](maps-api-images/image01.png)

> [!NOTE]
> Les services Google Play APK est un produit sous licence qui peut ne pas être présent sur tous les appareils. S’il n’est pas installé, Google Maps ne fonctionnera pas sur l’appareil.

### <a name="install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" />Installer le forfait Xamarin.GooglePlayServices.Maps à partir de NuGet

Le [forfait Xamarin.GooglePlayServices.Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contient les liaisons Xamarin.Android pour l’API Google Play Services Maps.
Pour ajouter le forfait Google Play Services Map, cliquez à droite sur le dossier **Références** de votre projet dans la Solution Explorer et cliquez sur **Manage NuGet Packages...**:

![Solution Explorer montrant Manage NuGet Packages élément de menu contextal sous Références](maps-api-images/image02.png)

Cela ouvre le **NuGet Package Manager**. Cliquez **sur Parcourir** et entrez **Xamarin Google Play Services Maps** dans le domaine de la recherche. Sélectionnez **Xamarin.GooglePlayServices.Maps** et cliquez sur **Installer**. (Si ce paquet avait été installé précédemment, cliquez **sur Mise à jour**.):

[![NuGet Package Manager avec Xamarin.GooglePlayServices.Maps forfait sélectionné](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Notez que les paquets de dépendance suivants sont également installés :

- **Xamarin.GooglePlayServices.Base**
- **Xamarin.GooglePlayServices.Basement Xamarin.GooglePlayServices.Basement Xamarin.GooglePlayServices.Basement Xama**
- **Xamarin.GooglePlayServices.Tasks Xamarin.GooglePlayServices.Tasks Xamarin.GooglePlayServices.Tasks Xama**

### <a name="specify-the-required-permissions"></a><a name="declare-permissions" />Spécifier les autorisations requises

Les applications doivent identifier les exigences en matière de matériel et d’autorisation afin d’utiliser l’API Google Maps.  Certaines autorisations sont automatiquement accordées par le Google Play Services SDK, et il n’est pas nécessaire pour un développeur de les ajouter explicitement à **AndroidManfest.XML:**

- **L’accès à l’État** &ndash; du réseau L’API Maps doit être en mesure de vérifier si elle peut télécharger les tuiles de la carte.

- **L’accès Internet d’accès** &ndash; à Internet est nécessaire pour télécharger les tuiles de carte et communiquer avec les serveurs Google Play pour l’accès à l’API.

Les autorisations et fonctionnalités suivantes doivent être spécifiées dans **l’AndroidManifest.XML** pour l’API Android de Google Maps :

- **OpenGL ES v2** &ndash; L’application doit déclarer l’exigence d’OpenGL ES v2.

- **Clé API Google Maps** &ndash; La clé API est utilisée pour confirmer que l’application est enregistrée et autorisée à utiliser Google Play Services. Voir [Obtenir une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) pour plus de détails sur cette clé.

- **Demandez l’héritage Apache HTTP client** &ndash; Apps qui ciblent Android 9.0 (niveau API 28) ou au-dessus doit spécifier que l’héritage Apache HTTP client est une bibliothèque optionnelle à utiliser.

- **Accès aux services** &ndash; Google Web L’application a besoin d’autorisations pour accéder aux services Web de Google qui soutiennent l’API Android Maps.

- **Autorisations pour les notifications** &ndash; Google Play Services L’application doit être autorisée à recevoir des notifications à distance de Google Play Services.

- **Accès aux fournisseurs de localisation** &ndash; Ce sont des autorisations facultatives.
   Ils permettront `GoogleMap` à la classe d’afficher l’emplacement de l’appareil sur la carte.

En outre, Android 9 a supprimé la bibliothèque client Apache HTTP du bootclasspath, et il n’est donc pas disponible pour les applications qui ciblent API 28 ou plus. La ligne suivante doit `application` être ajoutée au nœud de votre fichier **AndroidManifest.xml** pour continuer à utiliser le client Apache HTTP dans les applications qui ciblent L’API 28 ou plus :

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Les versions très anciennes du Google Play SDK nécessitaient une application pour demander la `WRITE_EXTERNAL_STORAGE` permission. Cette exigence n’est plus nécessaire avec les récentes liaisons Xamarin pour Google Play Services.

L’extrait suivant est un exemple des paramètres qui doivent être ajoutés à **AndroidManifest.XML**:

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

En plus de demander les autorisations **AndroidManifest.XML**, une application `ACCESS_COARSE_LOCATION` doit `ACCESS_FINE_LOCATION` également effectuer des contrôles d’autorisation de temps d’exécution pour le et les autorisations. Consultez le guide [Xamarin.Android Permissions](~/android/app-fundamentals/permissions.md) pour plus d’informations sur l’exécution des contrôles d’autorisation en temps d’exécution.

### <a name="create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Créez un émulateur avec les API Google

Dans le cas où un appareil Android physique avec les services Google Play n’est pas installé, il est possible de créer une image émulateur pour le développement. Pour plus d’informations, consultez le [responsable de l’appareil](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>La classe GoogleMap

Une fois les conditions préalables remplies, il est temps de commencer à développer l’application et d’utiliser l’API Android Maps. La classe [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) est l’API principale qu’une application Xamarin.Android utilisera pour afficher et interagir avec un Google Maps pour Android. Cette classe a les responsabilités suivantes :

- Interagir avec les services Google Play pour autoriser l’application avec le service Web Google.

- Téléchargement, mise en cache et affichage des tuiles de la carte.

- Affichage des contrôles d’interface utilisateur tels que panoramique et zoom sur l’utilisateur.

- Dessin de marqueurs et de formes géométriques sur les cartes.

L’est `GoogleMap` ajouté à une activité de deux façons:

- **MapFragment** - Le [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) est un fragment `GoogleMap` spécialisé qui agit comme hôte de l’objet. L’API `MapFragment` Nécessite Android niveau 12 ou plus.
   Les anciennes versions d’Android peuvent utiliser le [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Ce guide se concentrera sur l’utilisation de la `MapFragment` classe.

- **MapView** - Le [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) est une sous-classe vue spécialisée, qui peut agir comme un hôte pour un `GoogleMap` objet. Les utilisateurs de cette classe doivent transmettre toutes `MapView` les méthodes de cycle de vie d’activité à la classe.

Chacun de ces conteneurs expose une `Map` `GoogleMap`propriété qui renvoie une instance de . La préférence doit être donnée à la classe [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) car il s’agit d’une API plus simple qui réduit la quantité de code de la plaque chauffante qu’un développeur doit implémenter manuellement.

### <a name="adding-a-mapfragment-to-an-activity"></a>Ajout d’un MapFragment à une activité

La capture d’écran suivante `MapFragment`est un exemple d’une simple :

[![Capture d’écran d’un appareil affichant un fragment Google Map](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Comme d’autres classes Fragment, il `MapFragment` y a deux façons d’ajouter un à une activité :

- **Déclarativement** - `MapFragment` Le peut être ajouté via le fichier de mise en page XML pour l’activité. L’extrait XML suivant montre un exemple de `fragment` la façon d’utiliser l’élément :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **Programmatiquement** - `MapFragment` Le peut être programmatiquement [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) instantané en utilisant la méthode, puis ajouté à une activité. Cet extrait montre la façon la plus simple `MapFragment` d’instantanér un objet et d’ajouter à une activité :

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    Il est possible `MapFragment` de configurer [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) l’objet en passant un objet à `NewInstance`. Ceci est discuté dans la section [googleMap propriétés](#googlemap_object) qui apparaît plus tard dans ce guide.

La `MapFragment.GetMapAsync` méthode est utilisée [`GoogleMap`](#googlemap_object) pour initialiser le qui est hébergé par le fragment et `MapFragment`obtenir une référence à l’objet de carte qui est hébergé par le . Cette méthode prend un objet `IOnMapReadyCallback` qui implémente l’interface.

Cette interface a une `IMapReadyCallback.OnMapReady(MapFragment map)` méthode unique, qui sera invoquée quand il `GoogleMap` est possible pour l’application d’interagir avec l’objet. L’extrait de code suivant montre comment une `MapFragment` activité Android `IOnMapReadyCallback` peut initialiser un et implémenter l’interface:

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

Il existe cinq types différents de cartes disponibles à partir de l’API Google Maps:

- **Normal** - C’est le type de carte par défaut. Il montre les routes et les caractéristiques naturelles importantes ainsi que certains points d’intérêt artificiels (tels que les bâtiments et les ponts).

- **Satellite** - Cette carte montre la photographie satellite.

- **Hybride** - Cette carte montre la photographie satellite et les cartes routières.

- **Terrain** - Cela montre principalement des caractéristiques topographiques avec certaines routes.

- **Aucun** - Cette carte ne charge pas de tuiles, elle est rendue comme une grille vide.

L’image ci-dessous montre trois des différents types de cartes, de gauche à droite (normale, hybride, terrain):

[![Trois captures d’écran d’exemple de carte : Normal, Hybrid, et Terrain](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

La `GoogleMap.MapType` propriété est utilisée pour définir ou modifier quel type de carte est affiché. L’extrait de code suivant montre comment afficher une carte satellite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="googlemap-properties"></a><a name="googlemap_object" />Propriétés GoogleMap

`GoogleMap`définit plusieurs propriétés qui peuvent contrôler la fonctionnalité et l’apparence de la carte. Une façon de configurer `GoogleMap` l’état initial d’un est de passer `MapFragment`un objet [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) lors de la création d’un . L’extrait de code suivant est un `GoogleMapOptions` exemple d’utilisation d’un objet lors de la création d’un `MapFragment`:

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

L’autre façon `GoogleMap` de configurer un est de manipuler les propriétés sur les [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) de l’objet de la carte. L’échantillon de code suivant `GoogleMap` montre comment configurer un pour afficher les commandes de zoom et une boussole :

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interagir avec le GoogleMap

L’API Android Maps fournit des API qui permettent à une activité de changer le point de vue, d’ajouter des marqueurs, de placer des superpositions personnalisées ou de dessiner des formes géométriques. Cette section discutera de la façon d’accomplir certaines de ces tâches dans Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Changer le point de vue

Les cartes sont modélisés comme un plan plat à l’écran, basé sur la projection Mercator. La vue de la carte est celle d’une *caméra* regardant droit vers le bas sur cet avion. La position de la caméra peut être contrôlée en changeant l’emplacement, le zoom, l’inclinaison et le roulement. La classe [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) est utilisée pour déplacer l’emplacement de la caméra. `CameraUpdate`les objets ne sont pas directement instantanés, au lieu de cela l’API Maps fournit la classe [CameraUpdateFactory.](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)

Une `CameraUpdate` fois qu’un objet a été créé, il est passé comme paramètre pour les méthodes [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) ou [GoogleMap.AnimateCamera.](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) La `MoveCamera` méthode met à `AnimateCamera` jour la carte instantanément tandis que la méthode fournit une transition animée et en douceur.

Cet extrait de code est un exemple simple `CameraUpdateFactory` de `CameraUpdate` la façon d’utiliser le pour créer un qui incrémentera le niveau de zoom de la carte par un niveau de zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

L’API Maps fournit une [cameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) qui regroupera toutes les valeurs possibles pour la position de la caméra. Un exemple de cette classe peut être fourni à la méthode [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) qui retournera un `CameraUpdate` objet. L’API Maps comprend également la classe [CameraPosition.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) qui `CameraPosition` fournit une API fluide pour la création d’objets.
L’extrait de code suivant montre un `CameraUpdate` exemple `CameraPosition` de création d’un et `GoogleMap`d’utilisation de celle-ci pour changer la position de la caméra sur un :

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

Dans l’extrait de code précédent, un emplacement spécifique sur la carte est représenté par la classe [LatLng.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) Le niveau de zoom est réglé à 18, ce qui est une mesure arbitraire de zoom utilisé par Google Maps. Le roulement est la mesure de la boussole dans le sens des aiguilles d’une montre à partir du Nord. La propriété Tilt contrôle l’angle de vision et spécifie un angle de 25 degrés par rapport à la verticale. La capture d’écran suivante montre l’après-exécution `GoogleMap` du code précédent :

[![Exemple Google Map montrant un emplacement spécifié avec un angle de vision incliné](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>S’appuyer sur la carte

L’API Android Maps fournit des API pour dessiner les éléments suivants sur une carte :

- **Marqueurs** - Ce sont des icônes spéciales qui sont utilisées pour identifier un seul emplacement sur une carte.

- **Superpositions** - Il s’agit d’une image qui peut être utilisée pour identifier une collection d’emplacements ou de zones sur la carte.

- **Lignes, polygones et cercles** - Ce sont des API qui permettent aux activités d’ajouter des formes à une carte.

#### <a name="markers"></a>Marqueurs

L’API Maps fournit une classe [de marqueur](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) qui résume toutes les données sur un seul emplacement sur une carte. Par défaut, la classe Marker utilise une icône standard fournie par Google Maps. Il est possible de personnaliser l’apparence d’un marqueur et de répondre aux clics de l’utilisateur.

##### <a name="adding-a-marker"></a>Ajout d’un marqueur

Pour ajouter un marqueur à une carte, il est nécessaire de créer un nouvel `GoogleMap` objet [MarkerOptions,](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) puis d’appeler la méthode [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) sur une instance. Cette méthode retournera un objet [Marqueur.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker)

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

Le titre du marqueur s’affiche dans une *fenêtre d’information* lorsque l’utilisateur tape sur le marqueur. La capture d’écran suivante montre à quoi ressemble ce marqueur :

[![Exemple Google Map avec un marqueur et une fenêtre d’information pour la crête de Vimy](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personnaliser un marqueur

Il est possible de personnaliser l’icône utilisée `MarkerOptions.InvokeIcon` par le marqueur en appelant la méthode lors de l’ajout du marqueur à la carte.
Cette méthode prend un objet [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) contenant les données nécessaires pour rendre l’icône. La classe [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) fournit quelques méthodes d’aide pour simplifier la création d’un `BitmapDescriptor`. La liste suivante présente certaines de ces méthodes :

- `DefaultMarker(float colour)`&ndash; Utilisez le marqueur Par défaut Google Maps, mais changez la couleur.

- `FromAsset(string assetName)`&ndash; Utilisez une icône personnalisée à partir du fichier spécifié dans le dossier Assets.

- `FromBitmap(Bitmap image)`&ndash; Utilisez la bitmap spécifiée comme icône.

- `FromFile(string fileName)`&ndash; Créez l’icône personnalisée à partir du fichier sur le chemin spécifié.

- `FromResource(int resourceId)`&ndash; Créez une icône personnalisée à partir de la ressource spécifiée.

L’extrait de code suivant montre un exemple de création d’un marqueur par défaut de couleur cyan :

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

#### <a name="info-windows"></a>Fenêtres d’information

*Les fenêtres d’information* sont des fenêtres spéciales qui apparaissent pour afficher des informations à l’utilisateur lorsqu’ils appuyez sur un marqueur spécifique. Par défaut, la fenêtre d’information affichera le contenu du titre du marqueur. Si le titre n’a pas été attribué, alors aucune fenêtre d’information n’apparaîtra. Une seule fenêtre d’information peut être affichée à la fois.

Il est possible de personnaliser la fenêtre d’information en implémentant l’interface [GoogleMap.IInfoWindowAdapter.](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) Il existe deux méthodes importantes sur cette interface :

- `public View GetInfoWindow(Marker marker)`&ndash; Cette méthode est appelée pour obtenir une fenêtre d’information personnalisée pour un marqueur. S’il `null` revient, alors le rendu par défaut de fenêtre sera utilisé. Si cette méthode renvoie une vue, alors cette vue sera placée à l’intérieur du cadre de fenêtre d’info.

- `public View GetInfoContents(Marker marker)`&ndash; Cette méthode ne sera appelée que `null` si GetInfoWindow revient . Cette méthode peut `null` retourner une valeur si le rendu par défaut du contenu de la fenêtre d’information doit être utilisé. Dans le cas contraire, cette méthode devrait retourner une vue avec le contenu de la fenêtre d’information.

Une fenêtre d’information n’est pas une vue en direct - au lieu Android va convertir la vue en un bitmap statique et afficher que sur l’image. Cela signifie qu’une fenêtre d’information ne peut pas répondre à des événements ou des gestes tactiles, ni ne sera automatiquement mise à jour elle-même. Pour mettre à jour une fenêtre d’information, il est nécessaire d’appeler la méthode [GoogleMap.ShowInfoWindow.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow())

L’image suivante montre quelques exemples de certaines fenêtres d’information personnalisées. L’image de gauche a son contenu personnalisé, tandis que l’image de droite a sa fenêtre et son contenu personnalisés avec des coins arrondis :

![Exemple de fenêtres de marqueur pour Melbourne, y compris l’icône et la population. La fenêtre droite a des coins arrondis.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays (GroundOverlays)

Contrairement aux marqueurs, qui identifient un emplacement spécifique sur une carte, un [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) est une image qui est utilisée pour identifier une collection d’emplacements ou une zone sur la carte.

##### <a name="adding-a-groundoverlay"></a>Ajout d’un GroundOverlay

L’ajout d’une superposition au sol à une carte est similaire à l’ajout d’un marqueur à une carte. Tout d’abord, un objet [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) est créé. Cet objet est ensuite transmis [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) comme un paramètre `GroundOverlay` à la méthode, qui retournera un objet. Cet extrait de code est un exemple d’ajout d’une superposition au sol à une carte :

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

La capture d’écran suivante montre cette superposition sur une carte:

[![Carte d’exemple avec une image superposée d’un ours polaire](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Lignes, cercles et polygones

Il existe trois types simples de figures géométriques qui peuvent être ajoutés à une carte:

- **Polyline** - Il s’agit d’une série de segments de lignes connectées. Il peut marquer un chemin sur une carte ou créer une forme géométrique.

- **Cercle** - Cela dessinera un cercle sur la carte.

- **Polygone** - Il s’agit d’une forme fermée pour marquer les zones sur une carte.

##### <a name="polylines"></a>Polylignes

Une [polyline](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) est une `LatLng` liste d’objets consécutifs qui spécifient les vertices de chaque segment de ligne. Une polyline est créée `PolylineOptions` en créant d’abord un objet et en y ajoutant les points. L’objet `PolylineOption` est ensuite `GoogleMap` transmis à `AddPolyline` un objet en appelant la méthode.

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

Les cercles sont créés en instantanéisant d’abord un objet [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) qui spécifiera le centre et le rayon du cercle en mètres. Le cercle est dessiné sur la carte en appelant [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
L’extrait de code suivant montre comment dessiner un cercle :

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Polygones

`Polygon`s sont `Polyline`similaires à s, mais ils ne sont pas ouverts terminés. `Polygon`sont une boucle fermée et ont leur intérieur rempli.
`Polygon`sont créés exactement de la `Polyline`même manière qu’un , sauf la méthode [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) invoquée.

Contrairement `Polyline`à `Polygon` un , a est auto-fermeture. Le polygone sera fermé `AddPolygon` par la méthode en traçant une ligne qui relie les premier et derniers points. L’extrait de code suivant créera un rectangle solide sur la même zone `Polyline` que l’extrait de code précédent dans l’exemple.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```

## <a name="responding-to-user-events"></a>Répondre aux événements des utilisateurs

Il existe trois types d’interactions qu’un utilisateur peut avoir avec une carte :

- **Clic de marqueur** - L’utilisateur clique sur un marqueur.

- **Marker Drag** - L’utilisateur a longtemps cliqué sur un mparger

- **Info Window Click** - L’utilisateur a cliqué sur une fenêtre d’information.

Chacun de ces événements sera discuté plus en détail ci-dessous.

### <a name="marker-click-events"></a>Événements de clic de marqueur

L’événement `MarkerClicked` est soulevé lorsque l’utilisateur tape sur un marqueur. Cet événement accepte `GoogleMap.MarkerClickEventArgs` un objet comme paramètre. Cette classe contient deux propriétés :

- `GoogleMap.MarkerClickEventArgs.Handled`&ndash; Cette propriété devrait `true` être définie pour indiquer que le gestionnaire de l’événement a consommé l’événement. Si cela est `false` réglé à ce moment-là, le comportement par défaut se produira en plus du comportement personnalisé du gestionnaire d’événements.

- `Marker`&ndash; Cette propriété est une référence au `MarkerClick` marqueur qui a soulevé l’événement.

Cet extrait de code montre un `MarkerClick` exemple d’un qui va changer la position de la caméra à un nouvel emplacement sur la carte:

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

### <a name="marker-drag-events"></a>Événements Marker Drag

Cet événement est soulevé lorsque l’utilisateur souhaite faire glisser le marqueur. Par défaut, les marqueurs ne sont pas draggables. Un marqueur peut être défini comme `Marker.Draggable` traînable `true` en définissant `MarkerOptions.Draggable` la `true` propriété vers ou en invoquant la méthode comme paramètre.

Pour faire glisser le marqueur, l’utilisateur doit d’abord cliquer longtemps sur le marqueur, puis son doigt doit rester sur la carte. Lorsque le doigt de l’utilisateur est traîné sur l’écran, le marqueur se déplace. Lorsque le doigt de l’utilisateur se soulève de l’écran, le marqueur reste en place.

La liste suivante décrit les différents événements qui seront soulevés pour un marqueur traînable :

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)`&ndash; Cet événement est soulevé lorsque l’utilisateur traîne le marqueur pour la première fois.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)`&ndash; Cet événement est soulevé au fur et à mesure que le marqueur est traîné.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)`&ndash; Cet événement est soulevé lorsque l’utilisateur est fini de faire glisser le marqueur.

Chacun des `EventArgs` contient une `P0` seule propriété appelée `Marker` qui est une référence à l’objet étant traîné.

### <a name="info-window-click-events"></a>Info Fenêtre Cliquez sur les événements

Une seule fenêtre d’information peut être affichée à la fois. Lorsque l’utilisateur clique sur une fenêtre d’information d’une carte, l’objet de la carte soulève un `InfoWindowClick` événement. L’extrait de code suivant montre comment transférer un gestionnaire à l’événement :

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

Rappelons qu’une fenêtre `View` d’information est une statique qui est rendue comme une image sur la carte. Les widgets tels que les boutons, les cases à cocher ou les vues textuelles qui sont placés à l’intérieur de la fenêtre d’information seront inertes et ne peuvent pas répondre à l’un de leurs événements utilisateur intégral.

## <a name="related-links"></a>Liens connexes

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Services Google Play](https://developers.google.com/android/guides/overview)
- [Google Maps Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Obtenir une clé Google Maps API](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [utilisations-bibliothèque](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [utilisations-fonctionnalité](https://developer.android.com/guide/topics/manifest/uses-feature-element)
