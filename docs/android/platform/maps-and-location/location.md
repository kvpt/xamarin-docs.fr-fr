---
title: Services d’emplacement sur Android
description: Ce guide présente la reconnaissance de l’emplacement dans les applications Android et montre comment récupérer l’emplacement de l’utilisateur à l’aide de l’API du service de localisation Android, ainsi que le fournisseur de localisation fusible disponible avec Google API services de géolocalisation.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: e027d41e98c26ef1659c27ab05df3052e19cc670
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027131"
---
# <a name="location-services-on-android"></a>Services d’emplacement sur Android

_Ce guide présente la reconnaissance de l’emplacement dans les applications Android et montre comment récupérer l’emplacement de l’utilisateur à l’aide de l’API du service de localisation Android, ainsi que le fournisseur de localisation fusible disponible avec Google API services de géolocalisation._

Android permet d’accéder à différentes technologies d’emplacement, telles que l’emplacement de la tour des cellules, le Wi-Fi et le GPS. Les détails de chaque technologie d’emplacement sont extraits par des *fournisseurs de localisation*, ce qui permet aux applications d’obtenir des emplacements de la même manière, quel que soit le fournisseur utilisé. Ce guide présente le fournisseur de localisation fusible, une partie du Google Play Services, qui détermine intelligemment la meilleure façon d’obtenir l’emplacement des appareils en fonction des fournisseurs disponibles et de la façon dont l’appareil est utilisé. L’API de service d’emplacement Android et montre comment communiquer avec le service emplacement système à l’aide d’un `LocationManager`. La deuxième partie du guide explore le API services de géolocalisation Android à l’aide du `LocationManager`.

En règle générale, les applications doivent préférer utiliser le fournisseur de localisation fusible, en revenant à l’ancienne API du service d’emplacement Android uniquement lorsque cela est nécessaire.

## <a name="location-fundamentals"></a>Notions de base de l’emplacement

Dans Android, quelle que soit l’API que vous choisissez pour travailler avec les données de localisation, plusieurs concepts restent les mêmes. Cette section présente les fournisseurs de localisation et les autorisations relatives à l’emplacement.

### <a name="location-providers"></a>Fournisseurs d’emplacement

Plusieurs technologies sont utilisées en interne pour identifier l’emplacement de l’utilisateur. Le matériel utilisé dépend du type de *fournisseur de localisation* sélectionné pour le travail de collecte des données. Android utilise trois fournisseurs de localisation :

- Le **fournisseur gps** &ndash; GPS offre l’emplacement le plus précis, utilise le plus de puissance et fonctionne mieux en extérieur. Ce fournisseur utilise une combinaison GPS et assistée GPS ([AGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), qui retourne les données GPS collectées par les tours cellulaires.

- Le **fournisseur de réseau** &ndash; fournit une combinaison de données WiFi et cellulaires, y compris les données AGPS collectées par les tours de cellule. Elle utilise moins d’énergie que le fournisseur GPS, mais retourne des données de localisation d’une précision variable.

- Le **fournisseur passif** &ndash; une option « en-dessous » à l’aide de fournisseurs demandés par d’autres applications ou services pour générer des données d’emplacement dans une application. Il s’agit d’une option d’économie d’énergie moins fiable, idéale pour les applications qui ne nécessitent pas de mises à jour de localisation constantes.

Les fournisseurs d’emplacement ne sont pas toujours disponibles. Par exemple, nous pouvons peut-être utiliser GPS pour notre application, mais GPS peut être désactivé dans les paramètres ou l’appareil n’a peut-être pas de GPS. Si un fournisseur spécifique n’est pas disponible, le choix de ce fournisseur peut retourner `null`.

### <a name="location-permissions"></a>Autorisations d’emplacement

Une application qui prend en charge l’emplacement doit accéder aux capteurs matériels d’un appareil pour recevoir des données GPS, Wi-Fi et cellulaires. L’accès est contrôlé par le biais des autorisations appropriées dans le manifeste Android de l’application.
Deux autorisations sont disponibles &ndash; en fonction des exigences de votre application et de votre choix d’API, vous pouvez en autoriser une :

- `ACCESS_FINE_LOCATION` &ndash; permet à une application d’accéder au GPS.
    Requis pour le *fournisseur GPS* et les options du *fournisseur passif* (le*fournisseur passif a besoin d’une autorisation pour accéder aux données GPS collectées par une autre application ou un autre service*). Autorisation facultative pour le *fournisseur réseau*.

- `ACCESS_COARSE_LOCATION` &ndash; permet à une application d’accéder à un emplacement mobile et Wi-Fi. Requis pour le *fournisseur réseau* si `ACCESS_FINE_LOCATION` n’est pas défini.

Pour les applications qui ciblent la version 21 de l’API (Android 5,0 Lollipop) ou une version ultérieure, vous pouvez activer les `ACCESS_FINE_LOCATION` et les exécuter toujours sur des appareils qui n’ont pas de matériel GPS. Si votre application requiert du matériel GPS, vous devez ajouter explicitement un élément `android.hardware.location.gps` `uses-feature` au manifeste Android. Pour plus d’informations, consultez la référence des éléments Android [uses-Feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) .

Pour définir les autorisations, développez le dossier **Propriétés** dans le **panneau solutions** et double-cliquez sur **fichier AndroidManifest. xml**. Les autorisations s’affichent sous **autorisations requises**:

[Capture d’écran ![des paramètres des autorisations requises du manifeste Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

La définition de l’une de ces autorisations indique à Android que votre application a besoin de l’autorisation de l’utilisateur pour accéder aux fournisseurs d’emplacement. Les appareils qui exécutent le niveau d’API 22 (Android 5,1) ou une valeur inférieure demandent à l’utilisateur d’accorder ces autorisations chaque fois que l’application est installée. Sur les appareils exécutant l’API de niveau 23 (Android 6,0) ou une version ultérieure, l’application doit effectuer une vérification d’autorisation au moment de l’exécution avant d’effectuer une demande du fournisseur de localisation. 

> [!NOTE]
>Remarque : la définition de `ACCESS_FINE_LOCATION` implique l’accès aux données de localisation grossière et fine. Vous ne devez jamais définir les deux autorisations, mais uniquement l’autorisation *minimale* requise par votre application pour fonctionner.

Cet extrait de code est un exemple de vérification de l’autorisation d’une application pour l’autorisation `ACCESS_FINE_LOCATION` :

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

Les applications doivent être tolérantes au scénario dans lequel l’utilisateur n’accorde pas d’autorisation (ou a révoqué l’autorisation) et a la possibilité de traiter en douceur cette situation. Pour plus d’informations sur l’implémentation des vérifications des autorisations au moment de l’exécution dans Xamarin. Android, consultez le [Guide des autorisations](~/android/app-fundamentals/permissions.md) .

## <a name="using-the-fused-location-provider"></a>Utilisation du fournisseur de localisation fusion

Le fournisseur de localisation fusible est le meilleur moyen pour les applications Android de recevoir des mises à jour d’emplacement à partir de l’appareil, car il permet de sélectionner efficacement le fournisseur d’emplacement au moment de l’exécution pour fournir les meilleures informations d’emplacement de manière économe en batterie. Par exemple, un utilisateur qui parcourt à travers l’extérieur obtient l’emplacement le plus approprié pour la lecture avec GPS. Si l’utilisateur parcourt les portes, où le GPS fonctionne mal (le cas échéant), le fournisseur de localisation fusible peut automatiquement basculer vers WiFi, qui fonctionne mieux avec les portes.

L’API du fournisseur d’emplacement fusible fournit un large éventail d’autres outils permettant d’offrir des applications prenant en charge l’emplacement, y compris les opérations de clôture et de suivi des activités. Dans cette section, nous allons nous concentrer sur les principes fondamentaux de la configuration de l' `LocationClient`, de l’établissement des fournisseurs et de l’obtention de l’emplacement de l’utilisateur.

Le fournisseur de localisation fusible fait partie de [Google Play services](https://developer.android.com/google/play-services/index.html).
Le package de Google Play Services doit être installé et configuré correctement dans l’application pour que l’API du fournisseur de localisation fusible fonctionne, et l’appareil doit être installé sur l’appareil Google Play Services APK.

Avant qu’une application Xamarin. Android puisse utiliser le fournisseur de localisation fusible, elle doit ajouter le package **Xamarin. GooglePlayServices. Maps** au projet. En outre, les instructions `using` suivantes doivent être ajoutées à tous les fichiers sources qui font référence aux classes décrites ci-dessous :

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Vérification de l’installation de Google Play Services

Un Xamarin. Android se bloque s’il tente d’utiliser le fournisseur de localisation fusible quand Google Play Services n’est pas installé (ou obsolète), une exception d’exécution se produit.  Si Google Play Services n’est pas installé, l’application doit revenir au service d’emplacement Android présenté ci-dessus. Si Google Play Services est obsolète, l’application peut afficher un message invitant l’utilisateur à mettre à jour la version installée de Google Play Services.

Cet extrait de code est un exemple de la façon dont une activité Android peut vérifier par programme si Google Play Services est installé :

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

Pour interagir avec le fournisseur de localisation fusible, une application Xamarin. Android doit avoir une instance du `FusedLocationProviderClient`. Cette classe expose les méthodes nécessaires pour s’abonner aux mises à jour de l’emplacement et récupérer le dernier emplacement connu de l’appareil.

La méthode `OnCreate` d’une activité est un emplacement approprié pour obtenir une référence au `FusedLocationProviderClient`, comme illustré dans l’extrait de code suivant :

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

La méthode `FusedLocationProviderClient.GetLastLocationAsync()` offre un moyen simple et non bloquant pour une application Xamarin. Android d’obtenir rapidement le dernier emplacement connu de l’appareil avec une surcharge de codage minimale.

Cet extrait de code montre comment utiliser la méthode `GetLastLocationAsync` pour récupérer l’emplacement de l’appareil :

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

### <a name="subscribing-to-location-updates"></a>Abonnement à des mises à jour de localisation

Une application Xamarin. Android peut également s’abonner à des mises à jour de l’emplacement à partir du fournisseur de localisation fusible à l’aide de la méthode `FusedLocationProviderClient.RequestLocationUpdatesAsync`, comme indiqué dans cet extrait de code :

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Cette méthode prend deux paramètres :

- **`Android.Gms.Location.LocationRequest`** &ndash; un objet `LocationRequest` est la manière dont une application Xamarin. Android transmet les paramètres sur la manière dont le fournisseur de localisation fusible doit fonctionner. Le `LocationRequest` contient des informations telles que la fréquence des demandes ou l’importance de la mise à jour exacte de l’emplacement. Par exemple, une demande d’emplacement importante oblige l’appareil à utiliser le GPS et, par conséquent, davantage d’énergie, lors de la détermination de l’emplacement. Cet extrait de code montre comment créer une `LocationRequest` pour un emplacement avec une précision élevée, en vérifiant environ toutes les cinq minutes pour une mise à jour de l’emplacement (mais pas plus de deux minutes entre les demandes). Le fournisseur de localisation fusible utilisera un `LocationRequest` comme aide pour le fournisseur de localisation à utiliser lors de la tentative de détermination de l’emplacement de l’appareil :

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`** &ndash; afin de recevoir des mises à jour d’emplacement, une application Xamarin. Android doit sous-classer la classe abstraite `LocationProvider`. Cette classe expose deux méthodes qui sont peut-être appelées par le fournisseur de localisation fusible pour mettre à jour l’application avec les informations d’emplacement. Ce sujet sera abordé plus en détail ci-dessous.

Pour avertir une application Xamarin. Android d’une mise à jour d’emplacement, le fournisseur de localisation fusible appellera le `LocationCallBack.OnLocationResult(LocationResult result)`. Le paramètre `Android.Gms.Location.LocationResult` contient les informations relatives à l’emplacement de mise à jour.

Lorsque le fournisseur de localisation fusible détecte une modification de la disponibilité des données d’emplacement, il appelle la méthode `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)`. Si la propriété `LocationAvailability.IsLocationAvailable` retourne `true`, il peut être supposé que les résultats de l’emplacement de l’appareil signalés par `OnLocationResult` sont aussi précis et à jour que requis par la `LocationRequest`. Si `IsLocationAvailable` a la valeur false, aucun résultat d’emplacement ne sera retourné par `OnLocationResult`.

Cet extrait de code est un exemple d’implémentation de l’objet `LocationCallback` :

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

## <a name="using-the-android-location-service-api"></a>Utilisation de l’API de service d’emplacement Android

Le service d’emplacement Android est une API plus ancienne permettant d’utiliser les informations d’emplacement sur Android. Les données d’emplacement sont collectées par les capteurs matériels et collectées par un service système, accessible dans l’application avec une classe `LocationManager` et une `ILocationListener`.

Le service emplacement est mieux adapté aux applications qui doivent s’exécuter sur des appareils sur lesquels Google Play Services n’est pas installé.

Le service d’emplacement est un type spécial de [service](https://developer.android.com/guide/components/services.html) géré par le système. Un service système interagit avec le matériel de l’appareil et est toujours en cours d’exécution. Pour utiliser les mises à jour de l’emplacement dans notre application, nous allons nous abonner aux mises à jour de l’emplacement à partir du service emplacement système à l’aide d’un `LocationManager` et d’un appel `RequestLocationUpdates`.

Pour obtenir l’emplacement de l’utilisateur à l’aide du service d’emplacement Android, vous devez suivre plusieurs étapes :

1. Obtenir une référence au service `LocationManager`.
2. Implémentez l’interface `ILocationListener` et gérez les événements lorsque l’emplacement change.
3. Utilisez la `LocationManager` pour demander des mises à jour d’emplacement pour un fournisseur spécifié. Le `ILocationListener` de l’étape précédente sera utilisé pour recevoir des rappels de la `LocationManager`.
4. Arrêter les mises à jour de localisation quand l’application n’est plus appropriée pour recevoir des mises à jour.

### <a name="location-manager"></a>Gestionnaire d’emplacement

Nous pouvons accéder au service emplacement système à l’aide d’une instance de la classe `LocationManager`. `LocationManager` est une classe spéciale qui nous permet d’interagir avec le service emplacement système et d’appeler des méthodes sur celui-ci. Une application peut obtenir une référence au `LocationManager` en appelant `GetSystemService` et en passant un type de service, comme indiqué ci-dessous :

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` est un bon endroit pour obtenir une référence au `LocationManager`.
Il est judicieux de conserver le `LocationManager` en tant que variable de classe, afin de pouvoir l’appeler à différents stades du cycle de vie de l’activité.

### <a name="request-location-updates-from-the-locationmanager"></a>Demander des mises à jour de l’emplacement à partir du LocationManager

Une fois que l’application a une référence à la `LocationManager`, elle doit indiquer à la `LocationManager` le type d’informations d’emplacement requis et la fréquence à laquelle ces informations doivent être mises à jour. Pour ce faire, appelez `RequestLocationUpdates` sur l’objet `LocationManager` et passez des critères pour les mises à jour et un rappel qui recevra les mises à jour de l’emplacement. Ce rappel est un type qui doit implémenter l’interface `ILocationListener` (décrit plus en détail plus loin dans ce guide).

La méthode `RequestLocationUpdates` indique au service d’emplacement système que votre application souhaite commencer à recevoir des mises à jour d’emplacement. Cette méthode vous permet de spécifier le fournisseur, ainsi que les seuils de temps et de distance pour contrôler la fréquence de mise à jour. Par exemple, la méthode ci-dessous demande des mises à jour de l’emplacement du fournisseur de localisation GPS toutes les 2000 millisecondes, et uniquement lorsque l’emplacement change de plus de 1 mètre :

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Une application doit demander des mises à jour d’emplacement uniquement si nécessaire pour que l’application fonctionne correctement. Cela préserve la durée de vie de la batterie et crée une meilleure expérience pour l’utilisateur.

### <a name="responding-to-updates-from-the-locationmanager"></a>Réponse aux mises à jour à partir du LocationManager

Une fois qu’une application a demandé des mises à jour à partir de la `LocationManager`, elle peut recevoir des informations du service en implémentant l’interface [`ILocationListener`](xref:Android.Locations.ILocationListener) . Cette interface fournit quatre méthodes pour écouter le service d’emplacement et le fournisseur d’emplacement, `OnLocationChanged`. Le système appellera `OnLocationChanged` lorsque l’emplacement de l’utilisateur change de manière à ce qu’il soit considéré comme un changement d’emplacement en fonction des critères définis lors de la demande de mises à jour de l’emplacement. 

Le code suivant illustre les méthodes de l’interface `ILocationListener` :

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Résiliation des mises à jour LocationManager

Afin d’économiser les ressources système, une application doit se désabonner des mises à jour de l’emplacement dès que possible. La méthode `RemoveUpdates` indique à l' `LocationManager` d’arrêter l’envoi de mises à jour à notre application.  Par exemple, une activité peut appeler `RemoveUpdates` dans la méthode `OnPause` afin que nous puissions économiser de l’énergie si une application n’a pas besoin de mises à jour de l’emplacement alors que son activité ne se trouve pas à l’écran :

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si votre application doit obtenir des mises à jour de l’emplacement en arrière-plan, vous souhaiterez créer un service personnalisé qui s’abonne au service emplacement système. Pour plus d’informations, reportez-vous au Guide de l' [arrière-plan avec les services Android](~/android/app-fundamentals/services/index.md) .

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Détermination du meilleur fournisseur d’emplacement pour LocationManager

L’application ci-dessus définit GPS comme fournisseur de localisation. Toutefois, GPS peut ne pas être disponible dans tous les cas, par exemple si l’appareil est à l’intérieur ou n’a pas de récepteur GPS. Si c’est le cas, le résultat est un retour `null` pour le fournisseur.

Pour que votre application fonctionne lorsque le GPS n’est pas disponible, vous utilisez la méthode `GetBestProvider` pour demander le fournisseur d’emplacement le mieux disponible (pris en charge par l’appareil et l’utilisateur) lors du lancement de l’application. Au lieu de passer un fournisseur spécifique, vous pouvez indiquer `GetBestProvider` les exigences du fournisseur, telles que la précision et l’alimentation avec un [objet`Criteria`](xref:Android.Locations.Criteria). `GetBestProvider` retourne le meilleur fournisseur pour les critères spécifiés.

Le code suivant montre comment obtenir le meilleur fournisseur disponible et l’utiliser pour demander des mises à jour d’emplacement :

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
> Si l’utilisateur a désactivé tous les fournisseurs d’emplacement, `GetBestProvider` retourne `null`. Pour voir comment ce code fonctionne sur un appareil réel, veillez à activer les réseaux GPS, Wi-Fi et cellulaires sous les **paramètres Google > mode de > d’emplacement** comme indiqué dans cette capture d’écran :
>
> [écran mode d’emplacement des paramètres ![sur un téléphone Android](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> La capture d’écran ci-dessous illustre l’emplacement d’exécution de l’application à l’aide de `GetBestProvider`:
>
> [![application GetBestProvider affichant la latitude, la longitude et le fournisseur](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> N’oubliez pas que `GetBestProvider` ne modifie pas le fournisseur de manière dynamique. Au lieu de cela, elle détermine le meilleur fournisseur disponible une fois pendant le cycle de vie de l’activité. Si l’état du fournisseur change après avoir été défini, l’application requiert du code supplémentaire dans les méthodes de `ILocationListener` &ndash; `OnProviderEnabled`, `OnProviderDisabled`et `OnStatusChanged` &ndash; pour gérer toutes les éventualités liées au commutateur du fournisseur.

## <a name="summary"></a>Résumé

Ce guide traite de l’obtention de l’emplacement de l’utilisateur à l’aide du service de localisation Android et du fournisseur de localisation fusible de Google API services de géolocalisation.

## <a name="related-links"></a>Liens connexes

- [Emplacement (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Classe Criteria](xref:Android.Locations.Criteria)
- [LocationManager, classe](xref:Android.Locations.LocationManager)
- [LocationListener, classe](xref:Android.Locations.ILocationListener)
- [API LocationClient](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [API LocationListener](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [API LocationRequest](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
