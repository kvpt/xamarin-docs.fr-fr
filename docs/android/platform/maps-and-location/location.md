---
title: Services de localisation sur Android
description: Ce guide introduit la connaissance de l’emplacement dans les applications Android et illustre comment obtenir l’emplacement de l’utilisateur à l’aide de l’API Android Location Service, ainsi que le fournisseur de localisation fusionné disponible avec l’API Google Location Services.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 0fc74ae2307ffd14f8c52515c93993a51455997a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80805955"
---
# <a name="location-services-on-android"></a>Services de localisation sur Android

_Ce guide introduit la connaissance de l’emplacement dans les applications Android et illustre comment obtenir l’emplacement de l’utilisateur à l’aide de l’API Android Location Service, ainsi que le fournisseur de localisation fusionné disponible avec l’API Google Location Services._

Android donne accès à diverses technologies de localisation telles que l’emplacement de la tour cellulaire, le Wi-Fi et le GPS. Les détails de chaque technologie de localisation sont résumés par *l’intermédiaire des fournisseurs de localisation,* permettant aux applications d’obtenir des emplacements de la même manière quel que soit le fournisseur utilisé. Ce guide présente le fournisseur de localisation fusionné, une partie des services Google Play, qui détermine intelligemment la meilleure façon d’obtenir l’emplacement des appareils en fonction de ce que les fournisseurs sont disponibles et comment l’appareil est utilisé. Android Location Service API et montre comment communiquer `LocationManager`avec le service de localisation du système en utilisant un . La deuxième partie du guide explore l’API `LocationManager`Android Location Services en utilisant le .

En règle générale, les applications devraient préférer utiliser le fournisseur de localisation fusionné, en retomber l’ancienne API Android Location Service seulement si nécessaire.

## <a name="location-fundamentals"></a>Principes fondamentaux de localisation

Dans Android, peu importe l’API que vous choisissez pour travailler avec les données de localisation, plusieurs concepts restent les mêmes. Cette section introduit les fournisseurs de localisation et les autorisations liées à l’emplacement.

### <a name="location-providers"></a>Fournisseurs de localisation

Plusieurs technologies sont utilisées en interne pour localiser l’utilisateur. Le matériel utilisé dépend du type de fournisseur de *localisation* sélectionné pour le travail de collecte de données. Android utilise trois fournisseurs de localisation:

- **GPS Provider** &ndash; GPS donne l’emplacement le plus précis, utilise le plus de puissance, et fonctionne mieux à l’extérieur. Ce fournisseur utilise une combinaison de GPS et GPS assisté ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), qui retourne les données GPS recueillies par les tours cellulaires.

- **Network Provider** &ndash; fournit une combinaison de données WiFi et Cellulaire, y compris les données aGPS recueillies par les tours cellulaires. Il utilise moins d’énergie que le fournisseur de GPS, mais renvoie les données de localisation de précision variable.

- **Fournisseur** &ndash; passif Une option « piggyback » utilisant les fournisseurs demandés par d’autres applications ou services pour générer des données de localisation dans une application. Il s’agit d’une option moins fiable mais économe en énergie idéale pour les applications qui ne nécessitent pas de mises à jour de localisation constante pour fonctionner.

Les fournisseurs de localisation ne sont pas toujours disponibles. Par exemple, nous pourrions utiliser le GPS pour notre application, mais le GPS peut être désactivé dans Paramètres, ou l’appareil pourrait ne pas avoir de GPS du tout. Si un fournisseur spécifique n’est pas `null`disponible, le choix de ce fournisseur peut revenir .

### <a name="location-permissions"></a>Autorisations de localisation

Une application géolocalisation a besoin d’accéder aux capteurs matériels d’un appareil pour recevoir des données GPS, Wi-Fi et cellulaires. L’accès est contrôlé par des autorisations appropriées dans le Manifeste Android de l’application.
Il y a &ndash; deux autorisations disponibles en fonction des exigences de votre demande et de votre choix d’API, vous voudrez en autoriser une :

- `ACCESS_FINE_LOCATION`&ndash; Permet à une application d’accéder au GPS.
    Requis pour les options *de fournisseur de GPS* et de fournisseur *passif* *(fournisseur passif a besoin de la permission d’accéder aux données GPS recueillies par une autre application ou service*). Autorisation facultative pour le *fournisseur de réseau*.

- `ACCESS_COARSE_LOCATION`&ndash; Permet d’accéder à l’emplacement cellulaire et Wi-Fi. Requis pour le `ACCESS_FINE_LOCATION` fournisseur de *réseau* s’il n’est pas défini.

Pour les applications qui ciblent la version API 21 (Android 5.0 Lollipop) ou plus, vous pouvez activer `ACCESS_FINE_LOCATION` et toujours fonctionner sur des appareils qui n’ont pas de matériel GPS. Si votre application nécessite du matériel `android.hardware.location.gps` `uses-feature` GPS, vous devez explicitement ajouter un élément au Manifeste Android. Pour plus d’informations, consultez la référence de l’élément [d’utilisations](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) d’Android.

Pour définir les autorisations, étendre le dossier **Propriétés** dans le **pad de solution** et double clic **AndroidManifest.xml**. Les autorisations seront énumérées en vertu **des autorisations requises**:

[![Capture d’écran des paramètres d’autorisations requises du Manifeste Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

La définition de l’une ou l’autre de ces autorisations indique à Android que votre application a besoin de la permission de l’utilisateur afin d’accéder aux fournisseurs de localisation. Les appareils qui exécutent le niveau 22 de l’API (Android 5.1) ou inférieur demanderont à l’utilisateur d’accorder ces autorisations chaque fois que l’application est installée. Sur les appareils fonctionnant au niveau 23 de l’API (Android 6.0) ou plus, l’application doit effectuer une vérification d’autorisation en temps d’exécution avant de faire une demande du fournisseur de localisation. 

> [!NOTE]
>Remarque : `ACCESS_FINE_LOCATION` Le réglage implique l’accès à des données de localisation grossières et fines. Vous ne devriez jamais avoir à définir les deux autorisations, seule l’autorisation *minimale* de votre application nécessite de travailler.

Cet extrait est un exemple de la façon de vérifier `ACCESS_FINE_LOCATION` qu’une application a la permission pour la permission:

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

Les applications doivent être tolérantes au scénario où l’utilisateur n’accordera pas la permission (ou a révoqué la permission) et avoir un moyen de faire face gracieusement à cette situation. S’il vous plaît voir le [guide autorisations](~/android/app-fundamentals/permissions.md) pour plus de détails sur la mise en œuvre des contrôles d’autorisation run-time dans Xamarin.Android.

## <a name="using-the-fused-location-provider"></a>Utilisation du fournisseur de localisation fusionné

Le fournisseur de localisation fusionné est le moyen préféré pour les applications Android de recevoir des mises à jour de localisation de l’appareil, car il sélectionnera efficacement le fournisseur de localisation pendant le temps d’exécution pour fournir les meilleures informations de localisation d’une manière efficace pour la batterie. Par exemple, un utilisateur se promenant à l’extérieur obtient la meilleure lecture d’emplacement avec GPS. Si l’utilisateur marche ensuite à l’intérieur, où le GPS fonctionne mal (le cas échéant), le fournisseur de localisation fusionné peut automatiquement passer au WiFi, qui fonctionne mieux à l’intérieur.

Le fournisseur de localisation fusionné API fournit une variété d’autres outils pour habiliter les applications de localisation, y compris la géoféconation et la surveillance des activités. Dans cette section, nous allons nous concentrer sur `LocationClient`les bases de la mise en place de la , l’établissement des fournisseurs, et d’obtenir l’emplacement de l’utilisateur.

Le fournisseur de localisation fusionné fait partie de [Google Play Services](https://developer.android.com/google/play-services/index.html).
Le paquet Google Play Services doit être installé et configuré correctement dans l’application pour que l’API du fournisseur de localisation fusionné fonctionne, et l’appareil doit faire installer l’APK google Play Services.

Avant qu’une application Xamarin.Android puisse utiliser le fournisseur de localisation fusionné, il doit ajouter le forfait **Xamarin.GooglePlayServices.Location** au projet. En outre, `using` les énoncés suivants doivent être ajoutés à tous les fichiers source qui font référence aux classes décrites ci-dessous :

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Vérifier si Google Play Services est installé

Un Xamarin.Android va planter si elle essaie d’utiliser le fournisseur de localisation fusionné lorsque Google Play Services n’est pas installé (ou obsolète), puis une exception de temps d’exécution se produirait.  Si Google Play Services n’est pas installé, alors l’application doit retomber sur le service de localisation Android discuté ci-dessus. Si Google Play Services est obsolète, l’application peut afficher un message à l’utilisateur lui demandant de mettre à jour la version installée de Google Play Services.

Cet extrait est un exemple de la façon dont une activité Android peut vérifier de façon programmatique si Google Play Services est installé :

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

Pour interagir avec le fournisseur de localisation fusionné, une application Xamarin.Android doit avoir une instance de la `FusedLocationProviderClient`. Cette classe expose les méthodes nécessaires pour vous abonner aux mises à jour de localisation et pour récupérer le dernier emplacement connu de l’appareil.

La `OnCreate` méthode d’une activité est un endroit `FusedLocationProviderClient`approprié pour obtenir une référence à la , comme démontré dans l’extrait de code suivant:

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

### <a name="getting-the-last-known-location"></a>Obtenir le dernier emplacement connu

La `FusedLocationProviderClient.GetLastLocationAsync()` méthode fournit un moyen simple et non-bloquant pour une application Xamarin.Android pour obtenir rapidement le dernier emplacement connu de l’appareil avec un minimum de codage au-dessus.

Cet extrait montre comment utiliser `GetLastLocationAsync` la méthode pour récupérer l’emplacement de l’appareil :

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

### <a name="subscribing-to-location-updates"></a>Abonnement aux mises à jour de localisation

Une application Xamarin.Android peut également s’abonner aux `FusedLocationProviderClient.RequestLocationUpdatesAsync` mises à jour de localisation du fournisseur de localisation fusionné en utilisant la méthode, comme indiqué dans cet extrait de code:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Cette méthode prend deux paramètres :

- **`Android.Gms.Location.LocationRequest`**&ndash; Un `LocationRequest` objet est la façon dont une application Xamarin.Android passe les paramètres sur la façon dont le fournisseur de localisation fusionné devrait fonctionner. Les `LocationRequest` informations de conserve telles que la fréquence des demandes doivent être faites ou l’importance d’une mise à jour précise de localisation devrait être. Par exemple, une demande d’emplacement importante entraînera l’appareil à utiliser le GPS, et par conséquent plus de puissance, lors de la détermination de l’emplacement. Cet extrait de code montre comment `LocationRequest` créer un emplacement avec une grande précision, en vérifiant environ toutes les cinq minutes pour une mise à jour de localisation (mais pas plus de deux minutes entre les demandes). Le fournisseur de localisation `LocationRequest` fusionné utilisera un guide pour quel fournisseur de localisation utiliser lorsqu’il s’agira de déterminer l’emplacement de l’appareil :

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`**&ndash; Afin de recevoir des mises à jour de localisation, `LocationProvider` une application Xamarin.Android doit sous-classer la classe abstraite. Cette classe a exposé deux méthodes qui peuvent peut-être invoquées par le fournisseur de localisation fusionné pour mettre à jour l’application avec des informations de localisation. Cela sera discuté plus en détail ci-dessous.

Pour aviser une application Xamarin.Android d’une mise à `LocationCallBack.OnLocationResult(LocationResult result)`jour de localisation, le fournisseur de localisation fusionné invoquera le . Le `Android.Gms.Location.LocationResult` paramètre contiendra les informations de localisation de mise à jour.

Lorsque le fournisseur de localisation fusionné détecte un changement dans la `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` disponibilité des données de localisation, il appelle la méthode. Si `LocationAvailability.IsLocationAvailable` la `true`propriété revient, alors on peut supposer `OnLocationResult` que les résultats de localisation de `LocationRequest`l’appareil rapportés par sont aussi précis et aussi à jour que requis par le . Si `IsLocationAvailable` elle est fausse, aucun résultat `OnLocationResult`d’emplacement ne sera retourné par .

Cet extrait de code est une `LocationCallback` mise en œuvre d’échantillon de l’objet :

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

## <a name="using-the-android-location-service-api"></a>Utilisation de l’API Android Location Service

Le service de localisation Android est une ancienne API pour l’utilisation des informations de localisation sur Android. Les données de localisation sont collectées par des capteurs matériels et `LocationManager` collectées `ILocationListener`par un service système, qui est accessible dans l’application avec une classe et un .

Le service de localisation est le mieux adapté pour les applications qui doivent s’exécuter sur des appareils qui n’ont pas Google Play Services installé.

Le service de localisation est un type spécial de [service](https://developer.android.com/guide/components/services.html) géré par le système. Un service système interagit avec le matériel de l’appareil et est toujours en cours d’exécution. Pour puiser dans les mises à jour de localisation de notre `LocationManager` application, nous vous abonnerons aux mises à jour de localisation du service de localisation du système à l’aide d’un appel et d’un `RequestLocationUpdates` appel.

Pour obtenir l’emplacement de l’utilisateur à l’aide du service de localisation Android implique plusieurs étapes:

1. Obtenez une référence `LocationManager` au service.
2. Implémentez l’interface `ILocationListener` et gérez les événements lorsque l’emplacement change.
3. Utilisez `LocationManager` les mises à jour de localisation pour demander un fournisseur spécifié. L’étape `ILocationListener` précédente sera utilisée pour recevoir des `LocationManager`rappels de la .
4. Arrêtez les mises à jour de localisation lorsque l’application n’est plus appropriée pour recevoir des mises à jour.

### <a name="location-manager"></a>Gestionnaire de localisation

Nous pouvons accéder au service de `LocationManager` localisation du système avec une instance de la classe. `LocationManager`est une classe spéciale qui nous permet d’interagir avec le service de localisation du système et les méthodes d’appel sur elle. Une application peut obtenir `LocationManager` une `GetSystemService` référence à l’appel et en passant dans un type de service, comme indiqué ci-dessous:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`est un bon endroit pour `LocationManager`obtenir une référence à la .
C’est une bonne idée `LocationManager` de garder la variable de classe, afin que nous puissions l’appeler à divers moments dans le cycle de vie de l’activité.

### <a name="request-location-updates-from-the-locationmanager"></a>Demandez des mises à jour de localisation auprès de l’EmplacementManager

Une fois que l’application a une référence à la `LocationManager`, il doit dire le `LocationManager` type d’informations de localisation qui sont nécessaires, et combien de fois ces informations doivent être mises à jour. Faites-le `RequestLocationUpdates` en `LocationManager` faisant appel à l’objet, et en passant dans certains critères pour les mises à jour et un rappel qui recevra les mises à jour de localisation. Ce rappel est un type `ILocationListener` qui doit implémenter l’interface (décrit plus en détail plus tard dans ce guide).

La `RequestLocationUpdates` méthode indique au service de localisation du système que votre application souhaite commencer à recevoir des mises à jour de localisation. Cette méthode vous permet de spécifier le fournisseur, ainsi que les seuils de temps et de distance pour contrôler la fréquence de mise à jour. Par exemple, la méthode ci-dessous demande des mises à jour de localisation du fournisseur de localisation GPS toutes les 2000 millisecondes, et seulement lorsque l’emplacement change de plus d’un mètre :

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Une application ne devrait demander des mises à jour de localisation que si souvent que nécessaire pour que l’application fonctionne bien. Cela préserve la durée de vie de la batterie et crée une meilleure expérience pour l’utilisateur.

### <a name="responding-to-updates-from-the-locationmanager"></a>Répondre aux mises à jour de l’EmplacementManager

Une fois qu’une `LocationManager`application a demandé des mises à [`ILocationListener`](xref:Android.Locations.ILocationListener) jour de la , il peut recevoir des informations du Service en implémentant l’interface. Cette interface fournit quatre méthodes pour écouter le `OnLocationChanged`service de localisation et le fournisseur de localisation, . Le système `OnLocationChanged` appellera lorsque l’emplacement de l’utilisateur changera suffisamment pour être considéré comme un changement de localisation selon les critères définis lors de la demande de mises à jour de localisation. 

Le code suivant affiche `ILocationListener` les méthodes de l’interface :

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Désabonner les mises à jour de LocationManager

Afin de conserver les ressources du système, une application doit se désabonner aux mises à jour de localisation dès que possible. La `RemoveUpdates` méthode `LocationManager` indique l’arrêt de l’envoi de mises à jour à notre application.  À titre d’exemple, `RemoveUpdates` une `OnPause` activité peut appeler la méthode afin que nous puissions conserver la puissance si une application n’a pas besoin de mises à jour de localisation alors que son activité n’est pas à l’écran :

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Si votre application a besoin d’obtenir des mises à jour de localisation en arrière-plan, vous voudrez créer un service personnalisé qui s’abonner au service de localisation du système. Consultez le guide [Backgrounding with Android Services](~/android/app-fundamentals/services/index.md) pour plus d’informations.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Déterminer le meilleur fournisseur d’emplacement pour le LocationManager

L’application ci-dessus définit le GPS en tant que fournisseur de localisation. Cependant, le GPS peut ne pas être disponible dans tous les cas, comme si l’appareil est à l’intérieur ou n’a pas de récepteur GPS. Si tel est le cas, `null` le résultat est un retour pour le fournisseur.

Pour que votre application fonctionne lorsque le GPS `GetBestProvider` n’est pas disponible, vous utilisez la méthode pour demander le meilleur fournisseur de localisation disponible (supporteur pour l’appareil et l’utilisateur) au lancement de l’application. Au lieu de passer dans un `GetBestProvider` fournisseur spécifique, vous pouvez indiquer les exigences pour le fournisseur - comme la précision et la puissance - avec un [ `Criteria` objet](xref:Android.Locations.Criteria). `GetBestProvider`retourne le meilleur fournisseur pour les critères donnés.

Le code suivant montre comment obtenir le meilleur fournisseur disponible et l’utiliser lors de la demande de mises à jour de localisation:

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
> Si l’utilisateur a désactivé `GetBestProvider` tous `null`les fournisseurs de localisation, reviendra . Pour voir comment ce code fonctionne sur un véritable appareil, assurez-vous d’activer les réseaux GPS, Wi-Fi et cellulaires sous **Google Paramètres > Location > Mode** comme indiqué dans cette capture d’écran :
>
> [![Paramètres De l’écran mode de localisation sur un téléphone Android](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> La capture d’écran ci-dessous montre l’application de localisation en cours d’exécution à l’aide `GetBestProvider`de :
>
> [![Application GetBestProvider affichant la latitude, la longitude et le fournisseur](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> Gardez à `GetBestProvider` l’esprit que ne change pas le fournisseur dynamiquement. Il détermine plutôt le meilleur fournisseur disponible une fois au cours du cycle de vie de l’activité. Si l’état du fournisseur change après qu’il a `ILocationListener` &ndash; `OnProviderEnabled`été `OnProviderDisabled`défini, l’application nécessitera un code supplémentaire dans les méthodes, , et `OnStatusChanged` &ndash; pour gérer toutes les possibilités liées au commutateur fournisseur.

## <a name="summary"></a>Récapitulatif

Ce guide portait sur l’obtention de l’emplacement de l’utilisateur à l’aide du service de localisation Android et du fournisseur de localisation fusionné de l’API de Google Location Services.

## <a name="related-links"></a>Liens connexes

- [Emplacement (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (échantillon)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Services Google Play](https://developer.android.com/google/play-services/index.html)
- [Catégorie de critères](xref:Android.Locations.Criteria)
- [LocationManager Classe](xref:Android.Locations.LocationManager)
- [LocationListener Class](xref:Android.Locations.ILocationListener)
- [LocalisationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocalisationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocalisationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
