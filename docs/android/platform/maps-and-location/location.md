---
title: Services de localisation
description: Ce guide présente la géolocalisation dans les applications Android et illustre comment obtenir l’emplacement de l’utilisateur à l’aide de l’API de Service d’emplacement Android, ainsi que le fournisseur de localisation multiplication disponible avec l’API de Services d’emplacement Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: 76f98f1e660f22ec25c48407f2e87cec60ff12ef
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827683"
---
# <a name="location-services"></a>Services de localisation

_Ce guide présente la géolocalisation dans les applications Android et illustre comment obtenir l’emplacement de l’utilisateur à l’aide de l’API de Service d’emplacement Android, ainsi que le fournisseur de localisation multiplication disponible avec l’API de Services d’emplacement Google._

## <a name="location-services-overview"></a>Vue d’ensemble des Services emplacement

Android fournit l’accès aux différentes technologies d’emplacement comme emplacement de tour de cellule, Wi-Fi et GPS. Les détails de chaque technologie de localisation sont extraits via *fournisseurs de localisation*, permettant aux applications d’obtenir les emplacements de la même façon, quel que soit le fournisseur utilisé. Ce guide présente le fournisseur de localisation de multiplication, une partie des Services Google Play, qui détermine intelligemment le meilleur moyen d’obtenir l’emplacement des appareils selon les fournisseurs sont disponibles et comment l’appareil est utilisé. Android API de Service de Location et montre comment communiquer avec l’emplacement du système à l’aide de Service un `LocationManager`. La deuxième partie du guide explore l’API de Services Android emplacement en utilisant le `LocationManager`.
 
En règle générale, les applications préférez utiliser le fournisseur de localisation de multiplication, revenir à l’ancienne Android emplacement Service API uniquement lorsque cela est nécessaire.

## <a name="location-fundamentals"></a>Notions fondamentales d’emplacement

Dans Android, quel que soit le API que vous choisissez d’utiliser des données d’emplacement, plusieurs concepts restent les mêmes. Cette section présente les fournisseurs de localisation et liée à l’emplacement des autorisations.

### <a name="location-providers"></a>Fournisseurs de localisation

Plusieurs technologies sont utilisées en interne afin d’identifier l’emplacement de l’utilisateur. Le matériel utilisé varie selon le type de *fournisseur de localisation* sélectionné pour le travail de collecte des données. Android utilise trois fournisseurs de localisation :

-   **Fournisseur de GPS** &ndash; GPS indique l’emplacement plus précis, utilise le plus de puissance et fonctionne mieux extérieur. Ce fournisseur utilise une combinaison de GPS et GPS assisté ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), qui retourne des données GPS collectées par tours de téléphonie mobiles.

-   **Fournisseur de réseau** &ndash; fournit une combinaison de données Wi-Fi et cellulaire, y compris les données aGPS collectées par tours de cellule. Il utilise moins d’énergie que le fournisseur de GPS, mais retourne les données d’emplacement de précision différentes.

-   **Fournisseur passif** &ndash; une option « ACKS » à l’aide de fournisseurs demandés par les autres applications ou Services pour générer des données d’emplacement dans une application. Il s’agit d’un moins fiable mais idéal d’option d’économie d’énergie pour les applications qui ne nécessitent pas de mises à jour de l’emplacement constante fonctionne.

Fournisseurs de localisation ne sont pas toujours disponibles. Par exemple, nous pourrions utiliser GPS pour notre application, mais GPS peut être désactivée dans les paramètres ou l’appareil peut-être pas GPS du tout. Si un fournisseur spécifique n’est pas disponible, en choisissant ce fournisseur peut retourner `null`.

### <a name="location-permissions"></a>Autorisations de localisation

Une application sensible à l’emplacement doit accéder aux capteurs de matériel d’un appareil pour recevoir des données cellulaires, GPS et Wi-Fi. L’accès est contrôlé par le biais des autorisations appropriées dans la manifeste d’application Android.
Il existe deux autorisations &ndash; selon les exigences de votre application et votre choix d’API, vous voudrez permettre à un :

-   `ACCESS_FINE_LOCATION` &ndash; Permet un accès aux applications à GPS.
    Requis pour le *GPS fournisseur* et *fournisseur passif* options (*fournisseur passif nécessite une autorisation pour accéder aux données GPS collectées par une autre application ou Service*). Autorisation facultative pour le *fournisseur réseau*.

-   `ACCESS_COARSE_LOCATION` &ndash; Permet un accès aux applications à l’emplacement cellulaires et Wi-Fi. Requis pour *fournisseur réseau* si `ACCESS_FINE_LOCATION` n’est pas définie.

Pour les applications qui ciblent la version 21 (Android 5.0 Lollipop) de l’API ou une version ultérieure, vous pouvez activer `ACCESS_FINE_LOCATION` et toujours exécutées sur les appareils qui n’ont pas de matériel GPS. Si votre application a besoin de matériel GPS, vous devez ajouter explicitement un `android.hardware.location.gps` `uses-feature` élément pour le manifeste Android. Pour plus d’informations, consultez le Android [utilise la fonctionnalité](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) référence d’élément.

Pour définir les autorisations, développez le **propriétés** dossier dans le **panneau solutions** et double-cliquez sur **AndroidManifest.xml**. Les autorisations sont répertoriées sous **autorisations requises**:

[![Capture d’écran des paramètres des autorisations requises de manifeste Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Une de ces autorisations indique à Android que votre application a besoin d’autorisation de l’utilisateur pour accéder aux fournisseurs d’emplacement. Appareils API niveau 22 (Android 5.1) d’exécution ou faible demande à l’utilisateur pour accorder ces autorisations à chaque fois que l’application est installée. Sur les appareils exécutant des API de niveau 23 (Android 6.0) ou une version ultérieure, l’application doit effectuer une vérification d’autorisation d’exécution avant d’effectuer une demande du fournisseur de localisation. 

> [!NOTE]
>Remarque : Paramètre `ACCESS_FINE_LOCATION` implique l’accès à des données d’emplacement approximative et fine. Vous ne devez jamais définir uniquement les deux autorisations la *minimale* autorisation votre application a besoin pour fonctionner.

Cet extrait de code est un exemple de comment vérifier qu’une application a l’autorisation pour le `ACCESS_FINE_LOCATION` autorisation :

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

Les applications doivent être à tolérance de panne du scénario où l’utilisateur accorde pas l’autorisation (ou a révoqué l’autorisation) et disposer d’un moyen pour gérer correctement cette situation. Veuillez consulter la [guide des autorisations](~/android/app-fundamentals/permissions.md) pour plus d’informations sur l’implémentation d’autorisation d’exécution vérifie dans Xamarin.Android.


## <a name="using-the-fused-location-provider"></a>À l’aide du fournisseur de localisation de multiplication

Le fournisseur de localisation de multiplication est la meilleure méthode pour les applications Android recevoir des mises à jour de l’emplacement à partir de l’appareil, car il sélectionne efficacement le fournisseur de localisation pendant l’exécution pour fournir les meilleures informations d’emplacement de manière efficace de batterie. Par exemple, un utilisateur parcourant plein air Obtient le meilleur emplacement lecture avec GPS. Si l’utilisateur parcourt à l’intérieur, où GPS fonctionne mal (si possible), le fournisseur de localisation multiplication peut automatiquement basculer vers Wi-Fi, qui fonctionne mieux à l’intérieur.
 
Le fournisseur de localisation multiplication API fournit une variété d’autres outils pour permettre aux applications de géolocalisation, y compris les gardiennage virtuel et la surveillance de l’activité. Dans cette section, nous allons le focus sur les principes fondamentaux de la configuration de la `LocationClient`, l’établissement des fournisseurs et l’obtention de l’emplacement de l’utilisateur.

Le fournisseur de localisation multiplication fait partie de [Google Play Services](https://developer.android.com/google/play-services/index.html).
Le package de Google Play Services doit être installé et configuré correctement dans l’application pour le fournisseur de localisation multiplication API pour travailler, et l’appareil doit avoir l’APK Google Play Services installé.

Avant d’une application Xamarin.Android application peut utiliser le fournisseur de localisation de multiplication, il doit ajouter la **Xamarin.GooglePlayServices.Maps** package au projet. En outre, ce qui suit `using` instructions doivent être ajoutées à tous les fichiers sources qui font référence les classes décrites ci-dessous :

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Vérifie si les Services Google Play sont installés

Un incident si elle tente d’utiliser le fournisseur de localisation multiplication lorsque Google Play Services n’est pas installé (ou obsolète) ne Xamarin.Android sera alors une exception runtime se produirait.  Si Google Play Services n’est pas installé, puis l’application doit revenir au Service d’emplacement Android décrits ci-dessus. Si les Services Google Play est obsolète, l’application peut afficher un message à l’utilisateur lui indiquant de mettre à jour la version installée des Services Google Play.

Cet extrait de code est un exemple de la façon dont une activité Android peut vérifier par programmation si Google Play Services est installé :

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);
                  
        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

Pour interagir avec le fournisseur de localisation de multiplication, une application Xamarin.Android doit avoir une instance de la `FusedLocationProviderClient`. Cette classe expose les méthodes nécessaires pour vous abonner aux mises à jour de l’emplacement et pour récupérer le dernier emplacement connu de l’appareil.

Le `OnCreate` méthode d’une activité est un emplacement adapté pour obtenir une référence à la `FusedLocationProviderClient`, comme illustré dans l’extrait de code suivant :

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>Obtention du dernier emplacement connu

Le `FusedLocationProviderClient.GetLastLocationAsync()` méthode fournit un moyen simple et non bloquante pour une application Xamarin.Android obtenir rapidement le dernier emplacement connu de l’appareil avec un minimum de codage surcharge.

Cet extrait de code montre comment utiliser le `GetLastLocationAsync` méthode pour extraire l’emplacement de l’appareil :

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>S’abonner à l’emplacement des mises à jour

Une application Xamarin.Android peut également vous abonner aux mises à jour de l’emplacement à partir du fournisseur de localisation fusionnés à l’aide de la `FusedLocationProviderClient.RequestLocationUpdatesAsync` méthode, comme indiqué dans cet extrait de code :

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Cette méthode accepte deux paramètres :

-   **`Android.Gms.Location.LocationRequest`** &ndash; Un `LocationRequest` objet est comment une application Xamarin.Android transmet les paramètres sur le fonctionne du fournisseur de localisation de multiplication. Le `LocationRequest` contient des informations telles que la manière dont les requêtes fréquentes doivent être effectuées ou l’importance qui doit correspondre à une mise à jour de l’emplacement précis. Par exemple, une demande d’emplacement important entraîne l’appareil à utiliser le GPS et par conséquent plus de puissance, lors de la détermination de l’emplacement. Cet extrait de code montre comment créer un `LocationRequest` pour un emplacement avec une grande précision, la vérification environ toutes les cinq minutes pour une mise à jour d’emplacement (mais pas plus courte de deux minutes entre les requêtes). Le fournisseur de localisation multiplication utilisera un `LocationRequest` en tant que des conseils pour le fournisseur de localisation à utiliser lors de la tentative pour déterminer l’emplacement de l’appareil :

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Afin de recevoir des mises à jour de l’emplacement, une application Xamarin.Android doit sous-classer la `LocationProvider` classe abstraite. Cette classe exposée de deux méthodes qui peut-être appelée par le fournisseur de localisation de multiplication pour mettre à jour l’application avec les informations d’emplacement. Ce sujet sera abordé plus en détail ci-dessous.

Pour avertir une application Xamarin.Android d’une mise à jour de l’emplacement, le fournisseur de localisation multiplication appellera le `LocationCallBack.OnLocationResult(LocationResult result)`. Le `Android.Gms.Location.LocationResult` paramètre contiendra les informations d’emplacement de mise à jour.

Lorsque le fournisseur de localisation multiplication détecte une modification de la disponibilité des données de localisation, il appellera le `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` (méthode). Si le `LocationAvailability.IsLocationAvailable` retourne de la propriété `true`, puis on peut supposer que les résultats de l’emplacement appareil signalé par `OnLocationResult` sont aussi précis et à jour comme requis par le `LocationRequest`. Si `IsLocationAvailable` est false, aucun résultat de l’emplacement n’est retourné par `OnLocationResult`.

Cet extrait de code est un exemple d’implémentation de la `LocationCallback` objet :

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>À l’aide de l’API de Service d’emplacement Android

Le Service d’emplacement Android est une API plus anciennes pour l’utilisation des informations d’emplacement sur Android. Données d’emplacement sont collectées par les capteurs de matériel et collectées par un service système, qui est accessible dans l’application avec un `LocationManager` classe et un `ILocationListener`.

Le Service d’emplacement est mieux adapté aux applications qui doivent s’exécuter sur les appareils qui n’ont pas de Google Play Services est installé.

Le Service d’emplacement est un type spécial de [Service](https://developer.android.com/guide/components/services.html) géré par le système. Un Service système interagit avec le matériel du périphérique et est toujours en cours d’exécution. Pour exploiter les mises à jour de l’emplacement dans notre application, nous abonner aux mises à jour de l’emplacement du Service d’emplacement de système à l’aide un `LocationManager` et un `RequestLocationUpdates` appeler.

Pour obtenir l’emplacement de l’utilisateur à l’aide du Service d’emplacement Android implique plusieurs étapes :

1.  Obtenir une référence à la `LocationManager` service.
2.  Implémentez le `ILocationListener` interface et gérer des événements lorsque l’emplacement est modifié.
3.  Utilisez le `LocationManager` à la demande d’emplacement de met à jour pour un fournisseur spécifié. Le `ILocationListener` à partir de l’étape précédente sera utilisé pour recevoir des rappels à partir de la `LocationManager`.
4.  Arrêter les mises à jour de l’emplacement lors de l’application n’est plus appropriée recevoir des mises à jour.

### <a name="location-manager"></a>Gestionnaire d’emplacement

Nous pouvons accéder au service d’emplacement système avec une instance de la `LocationManager` classe. `LocationManager` est une classe spéciale qui nous permet d’interagir avec l’emplacement du système de Service et appeler des méthodes sur celle-ci. Une application peut obtenir une référence à la `LocationManager` en appelant `GetSystemService` et en passant dans un type de Service, comme indiqué ci-dessous :

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` est un bon emplacement pour obtenir une référence à la `LocationManager`.
Il est judicieux de conserver le `LocationManager` comme une variable de classe, afin que nous pouvons l’appeler à différents stades du cycle de vie d’activité.

### <a name="request-location-updates-from-the-locationmanager"></a>Demander des mises à jour de l’emplacement à partir de la LocationManager

Une fois que l’application a une référence à la `LocationManager`, il doit indiquer le `LocationManager` quel type d’informations d’emplacement qui sont requis, et la fréquence à laquelle cette information est mise à jour. Cela en appelant `RequestLocationUpdates` sur la `LocationManager` objet et en passant des critères pour les mises à jour et un rappel qui recevra les mises à jour de l’emplacement. Ce rappel est un type qui doit implémenter le `ILocationListener` interface (décrite plus en détail plus loin dans ce guide).

Le `RequestLocationUpdates` méthode indique à l’emplacement du système Service que votre application souhaite commencer à recevoir des mises à jour de l’emplacement. Cette méthode vous permet de spécifier le fournisseur, ainsi que les seuils de temps et de distance pour contrôler la fréquence de mise à jour. Par exemple, la méthode ci-dessous demande l’emplacement des mises à jour à partir du fournisseur de localisation GPS chaque 2 000 millisecondes, et uniquement si l’emplacement change plus de 1 mètre :

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Une application doit demander des mises à jour de l’emplacement uniquement aussi souvent que nécessaire pour l’application effectuer correctement. Cela préserve l’autonomie et crée une meilleure expérience pour l’utilisateur.

### <a name="responding-to-updates-from-the-locationmanager"></a>Répondre aux mises à jour à partir de la LocationManager

Une fois qu’une application a demandé des mises à jour à partir de la `LocationManager`, il peut recevoir des informations à partir du Service en implémentant la [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interface. Cette interface fournit quatre méthodes pour l’écoute à l’emplacement de Service et le fournisseur de localisation, `OnLocationChanged`. Le système appelle `OnLocationChanged` lorsque l’utilisateur change suffisante pour être considérée comme une modification de l’emplacement en fonction de critères définis lors de la demande des mises à jour de l’emplacement. 

Le code suivant montre les méthodes dans le `ILocationListener` interface :

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>L’annulation de l’abonnement aux mises à jour LocationManager

Afin de préserver les ressources système, une application doit annuler l’abonnement aux mises à jour de l’emplacement dès que possible. Le `RemoveUpdates` méthode indique à la `LocationManager` pour arrêter l’envoi des mises à jour à notre application.  Par exemple, une activité peut appeler `RemoveUpdates` dans le `OnPause` méthode afin que nous sommes en mesure d’économiser l’énergie si une application n’a pas besoin mises à jour de l’emplacement tandis que son activité n’est pas sur l’écran :

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si votre application doit obtenir les mises à jour de l’emplacement en arrière-plan, vous souhaitez créer un Service personnalisé qui s’abonne à l’emplacement du Service système. Reportez-vous à la [Backgrounding avec Services Android](~/android/app-fundamentals/services/index.md) guide pour plus d’informations.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Déterminer le meilleur fournisseur d’emplacement pour le LocationManager

L’application ci-dessus définit GPS en tant que le fournisseur de localisation. Toutefois, GPS peut-être pas disponibles dans tous les cas, comme si l’appareil est à l’intérieur ou qu’il ne dispose pas d’un récepteur GPS. Si c’est le cas, le résultat est un `null` retour pour le fournisseur.

Pour obtenir votre application de fonctionner lorsque le GPS n’est pas disponible, vous utilisez la `GetBestProvider` méthode pour demander le meilleur disponible (prise en charge de périphérique et utilisateur activé) fournisseur de localisation au lancement de l’application. Au lieu de passer un fournisseur spécifique, vous pouvez indiquer `GetBestProvider` la configuration requise pour le fournisseur, par exemple analyse de précision et la puissance - avec un [ `Criteria` objet](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Retourne le fournisseur de meilleures pour les critères fournis.

Le code suivant montre comment obtenir le fournisseur le plus disponible et l’utiliser lors de la demande des mises à jour de l’emplacement :

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
>  Si l’utilisateur a désactivé tous les fournisseurs de localisation, `GetBestProvider` retournera `null`. Pour voir comment ce code fonctionne sur un appareil réel, veillez à activer GPS, Wi-Fi et des réseaux cellulaires sous **Google Paramètres > emplacement > Mode** comme indiqué dans cette capture d’écran :

[![Écran Mode d’emplacement des paramètres sur un téléphone Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

La capture d’écran ci-dessous montre l’emplacement application en cours d’exécution en utilisant `GetBestProvider`:

[![Application GetBestProvider affichage latitude, longitude et fournisseur](location-images/location-03.png)](location-images/location-03.png#lightbox)

N’oubliez pas que `GetBestProvider` ne modifie pas le fournisseur de manière dynamique. Au lieu de cela, il détermine le fournisseur le plus disponible une fois pendant le cycle de vie d’activité. Si l’état du fournisseur est modifié après qu’il a été défini, l’application nécessite un code supplémentaire dans le `ILocationListener` méthodes &ndash; `OnProviderEnabled`, `OnProviderDisabled`, et `OnStatusChanged` &ndash; pour gérer chaque possibilité liée à la commutateur de fournisseur.

## <a name="summary"></a>Récapitulatif

Ce guide a couvert obtenir l’emplacement de l’utilisateur à l’aide du Service d’emplacement Android et le fournisseur de localisation fusionnés à partir de l’API de Services de Location de Google.


## <a name="related-links"></a>Liens associés

- [Emplacement (exemple)](https://developer.xamarin.com/samples/monodroid/Location/)
- [FusedLocationProvider (sample)](https://developer.xamarin.com/samples/monodroid/FusedLocationProvider/)
- [Services Google Play](https://developer.android.com/google/play-services/index.html)
- [Classe de critères](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Classe de LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Classe de LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
