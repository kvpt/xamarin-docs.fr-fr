---
title: Xamarin.Forms Initialisation et configuration de la carte
description: Xamarin.Forms. Le package NuGet Maps est requis pour utiliser la fonctionnalité Maps dans une application. En outre, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour être accordé à l’application.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b84336f836c3fa421537daf7e43de01e7be20b01
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371285"
---
# <a name="no-locxamarinforms-map-initialization-and-configuration"></a>Xamarin.Forms Initialisation et configuration de la carte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle utilise le contrôle de carte natif sur chaque plateforme. Cela offre une expérience de cartes rapide et familière pour les utilisateurs, mais signifie que certaines étapes de configuration sont nécessaires pour respecter les exigences de chaque API de plateformes.

## <a name="map-initialization"></a>Initialisation de la carte

Le [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle est fourni par le [ Xamarin.Forms . Mappe](https://www.nuget.org/packages/Xamarin.Forms.Maps/) le package NuGet, qui doit être ajouté à chaque projet de la solution.

Après l’installation de [ Xamarin.Forms . Mappe](https://www.nuget.org/packages/Xamarin.Forms.Maps/) le package NuGet, il doit être initialisé dans chaque projet de plateforme.

Sur iOS, cela doit se produire dans **AppDelegate.cs** en appelant la `Xamarin.FormsMaps.Init` méthode *après* la `Xamarin.Forms.Forms.Init` méthode :

```csharp
Xamarin.FormsMaps.Init();
```

Sur Android, cela doit se produire dans **MainActivity.cs** en appelant la `Xamarin.FormsMaps.Init` méthode *après* la `Xamarin.Forms.Forms.Init` méthode :

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

Sur la plateforme Windows universelle (UWP), cela doit se produire dans **MainPage.Xaml.cs** en appelant la `Xamarin.FormsMaps.Init` méthode à partir du `MainPage` constructeur :

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Pour plus d’informations sur le jeton d’authentification requis sur UWP, consultez [plateforme Windows universelle](#universal-windows-platform).

Une fois le package NuGet ajouté et la méthode d’initialisation appelée à l’intérieur de chaque application, les `Xamarin.Forms.Maps` API peuvent être utilisées dans le projet de code partagé.

## <a name="platform-configuration"></a>Configuration de la plateforme

Une configuration supplémentaire est requise sur Android et le plateforme Windows universelle (UWP) pour que la carte s’affiche. En outre, sur iOS, Android et UWP, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour avoir été accordé à l’application.

### <a name="ios"></a>iOS

L’affichage et l’interaction avec une carte sur iOS ne nécessitent aucune configuration supplémentaire. Toutefois, pour accéder aux services d’emplacement, vous devez définir les clés suivantes dans **info. plist** :

- iOS 11 et versions ultérieures
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – pour l’utilisation des services d’emplacement lorsque l’application est en cours d’utilisation
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/bundleresources/information_property_list/nslocationalwaysandwheninuseusagedescription) – pour l’utilisation des services de localisation à tout moment
- iOS 10 et versions antérieures
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – pour l’utilisation des services d’emplacement lorsque l’application est en cours d’utilisation
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – pour l’utilisation des services de localisation à tout moment    

Pour prendre en charge iOS 11 et les versions antérieures, vous pouvez inclure les trois clés suivantes : `NSLocationWhenInUseUsageDescription` , `NSLocationAlwaysAndWhenInUseUsageDescription` et `NSLocationAlwaysUsageDescription` .

La représentation XML de ces clés dans **info. plist** est indiquée ci-dessous. Vous devez mettre à jour les `string` valeurs pour refléter la manière dont votre application utilise les informations d’emplacement :

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your application is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Vous pouvez également ajouter les entrées **info. plist** en mode **source** lors de la modification du fichier **info. plist** :

![Info. plist pour iOS 8](setup-images/ios8-map-permissions.png "Entrées. plist des informations requises pour iOS 8")

Une invite s’affiche lorsque l’application tente d’accéder à l’emplacement de l’utilisateur, en demandant l’accès :

[![Capture d’écran de la demande d’autorisation d’emplacement sur iOS](setup-images/permission-ios.png "demande d’autorisation iOS")](setup-images/permission-ios-large.png#lightbox "demande d’autorisation iOS")

### <a name="android"></a>Android

Le processus de configuration pour l’affichage et l’interaction avec une carte sur Android est le suivant :

1. Obtenir une clé API Google Maps et l’ajouter au manifeste.
1. Spécifiez le numéro de version des services Google Play dans le manifeste.
1. Spécifiez la spécification de la bibliothèque Legacy HTTP Apache dans le manifeste.
1. facultatif Spécifiez l’autorisation WRITE_EXTERNAL_STORAGE dans le manifeste.
1. facultatif Spécifiez les autorisations d’emplacement dans le manifeste.
1. facultatif Autorisations relatives à l’emplacement d’exécution de la requête dans la `MainActivity` classe.

Pour obtenir un exemple de fichier manifeste correctement configuré, consultez [AndroidManifest.xml](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) à partir de l’exemple d’application.

#### <a name="get-a-google-maps-api-key"></a>Obtenir une clé API Google Maps

Pour utiliser l' [API Google Maps](https://developers.google.com/maps/documentation/android/) sur Android, vous devez générer une clé API. Pour ce faire, suivez les instructions de la procédure d' [obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Une fois que vous avez obtenu une clé API, elle doit être ajoutée dans l' `<application>` élément du fichier **Properties/AndroidManifest.xml** :

```xml
<application ...>
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

Cela incorpore la clé API dans le manifeste. Si vous n’avez pas de clé API valide [`Map`](xref:Xamarin.Forms.Maps.Map) , le contrôle affichera une grille vide.

> [!NOTE]
> `com.google.android.geo.API_KEY` est le nom de métadonnées recommandé pour la clé API. À des fins de compatibilité descendante, le `com.google.android.maps.v2.API_KEY` nom des métadonnées peut être utilisé, mais autorise uniquement l’authentification à l’API de cartes Android v2.

Pour que votre APK accède à Google Maps, vous devez inclure les empreintes de l’algorithme SHA-1 et les noms des packages pour chaque magasin de clés (Debug et Release) que vous utilisez pour signer vos APK. Par exemple, si vous utilisez un ordinateur pour le débogage et un autre pour générer le APK de mise en production, vous devez inclure l’empreinte de certificat SHA-1 à partir du magasin de clés de débogage du premier ordinateur et l’empreinte de certificat SHA-1 du magasin de clés de version du deuxième ordinateur. N’oubliez pas également de modifier les informations d’identification de la clé si le **nom du package** de l’application est modifié. Consultez [obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

#### <a name="specify-the-google-play-services-version-number"></a>Spécifier le numéro de version de Google Play services

Ajoutez la déclaration suivante dans l' `<application>` élément de **AndroidManifest.xml** :

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

Cela incorpore la version de Google Play services avec laquelle l’application a été compilée, dans le manifeste.

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>Spécifier la configuration requise pour la bibliothèque Legacy HTTP Apache

Si votre Xamarin.Forms application cible l’API 28 ou une version ultérieure, vous devez ajouter la déclaration suivante dans l' `<application>` élément de **AndroidManifest.xml** :

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

Cela indique à l’application d’utiliser la bibliothèque cliente Apache HTTP, qui a été supprimée du `bootclasspath` dans Android 9.

#### <a name="specify-the-write_external_storage-permission"></a>Spécifier l’autorisation WRITE_EXTERNAL_STORAGE

Si votre application cible l’API 22 ou une valeur antérieure, il peut être nécessaire d’ajouter l' `WRITE_EXTERNAL_STORAGE` autorisation au manifeste, en tant qu’enfant de l' `<manifest>` élément :

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Cela n’est pas obligatoire si votre application cible l’API 23 ou une version ultérieure.

#### <a name="specify-location-permissions"></a>Spécifier les autorisations d’emplacement

Si votre application doit accéder à l’emplacement de l’utilisateur, vous devez demander l’autorisation en ajoutant les `ACCESS_COARSE_LOCATION` `ACCESS_FINE_LOCATION` autorisations ou au manifeste (ou les deux), en tant qu’enfant de l' `<manifest>` élément :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

L' `ACCESS_COARSE_LOCATION` autorisation permet à l’API d’utiliser des données WiFi ou mobile, ou les deux, pour déterminer l’emplacement de l’appareil. Les `ACCESS_FINE_LOCATION` autorisations permettent à l’API d’utiliser les données de Global Positioning System (GPS), WiFi ou mobile pour déterminer un emplacement précis possible.

Vous pouvez également activer ces autorisations à l’aide de l’éditeur de manifeste pour ajouter les autorisations suivantes :

- `AccessCoarseLocation`
- `AccessFineLocation`

Celles-ci sont indiquées dans la capture d’écran ci-dessous :

![Autorisations requises pour Android](setup-images/android-map-permissions.png "Autorisations requises pour Android")

#### <a name="request-runtime-location-permissions"></a>Autorisations relatives à l’emplacement d’exécution de la demande

Si votre application cible l’API 23 ou une version ultérieure et doit accéder à l’emplacement de l’utilisateur, elle doit vérifier s’il dispose de l’autorisation nécessaire au moment de l’exécution et la demander si elle ne l’a pas. Cela peut être accompli de la façon suivante :

1. Dans la `MainActivity` classe, ajoutez les champs suivants :

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. Dans la `MainActivity` classe, ajoutez la `OnStart` substitution suivante :

    ```csharp
    protected override void OnStart()
    {
        base.OnStart();

        if ((int)Build.VERSION.SdkInt >= 23)
        {
            if (CheckSelfPermission(Manifest.Permission.AccessFineLocation) != Permission.Granted)
            {
                RequestPermissions(LocationPermissions, RequestLocationId);
            }
            else
            {
                // Permissions already granted - display a message.
            }
        }
    }
    ```

    À condition que l’application cible l’API 23 ou supérieure, ce code effectue une vérification d’autorisation d’exécution pour l' `AccessFineLocation` autorisation. Si l’autorisation n’a pas été accordée, une demande d’autorisation est effectuée en appelant la `RequestPermissions` méthode.

1. Dans la `MainActivity` classe, ajoutez la `OnRequestPermissionsResult` substitution suivante :

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
    {
        if (requestCode == RequestLocationId)
        {
            if ((grantResults.Length == 1) && (grantResults[0] == (int)Permission.Granted))
                // Permissions granted - display a message.
            else
                // Permissions denied - display a message.
        }
        else
        {
            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
    }
    ```

    Ce remplacement gère le résultat de la demande d’autorisation.

L’effet global de ce code est que lorsque l’application demande l’emplacement de l’utilisateur, la boîte de dialogue suivante s’affiche, qui demande l’autorisation :

[![Capture d’écran de la demande d’autorisation d’emplacement sur Android](setup-images/permission-android.png "Demande d’autorisation Android")](setup-images/permission-android-large.png#lightbox "Demande d’autorisation Android")

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Sur UWP, votre application doit être authentifiée pour pouvoir afficher une carte et consommer des services de mappage. Pour authentifier votre application, vous devez spécifier une clé d’authentification Maps. Pour plus d’informations, consultez [demander une clé d’authentification par mappages](/windows/uwp/maps-and-location/authentication-key). Le jeton d’authentification doit ensuite être spécifié dans l' `FormsMaps.Init("AUTHORIZATION_TOKEN")` appel de méthode pour authentifier l’application avec Bing Maps.

> [!NOTE]
> Sur UWP, pour utiliser des services de carte tels que le géocodage, vous devez également affecter `MapService.ServiceToken` à la propriété la valeur de la clé d’authentification. Pour ce faire, vous pouvez utiliser la ligne de code suivante : `Windows.Services.Maps.MapService.ServiceToken = "INSERT_AUTH_TOKEN_HERE";` .

En outre, si votre application doit accéder à l’emplacement de l’utilisateur, vous devez activer la fonctionnalité d’emplacement dans le manifeste du package. Cela peut être accompli de la façon suivante :

1. Dans l’ **Explorateur de solutions** , double-cliquez sur **package.appxmanifest** , puis sélectionnez l’onglet **Capacités**.
1. Dans la liste des **fonctionnalités** , activez la case à cocher **emplacement**. Cela ajoute la `location` fonctionnalité de l’appareil au fichier manifeste du package.

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

#### <a name="release-builds"></a>Versions release

Les versions de la version UWP utilisent la compilation native .NET pour compiler l’application directement en code natif. Toutefois, il en résulte que le convertisseur du [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle sur UWP peut être lié à l’exécutable. Cela peut être résolu à l’aide d’une surcharge spécifique à UWP de la `Forms.Init` méthode dans **app.Xaml.cs** :

```csharp
var assembliesToInclude = new [] { typeof(Xamarin.Forms.Maps.UWP.MapRenderer).GetTypeInfo().Assembly };
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
```

Ce code passe l’assembly dans lequel la `Xamarin.Forms.Maps.UWP.MapRenderer` classe réside, à la `Forms.Init` méthode. Cela permet de s’assurer que l’assembly n’est pas lié à l’exécutable par le processus de compilation .NET native.

> [!IMPORTANT]
> Dans le cas contraire, le contrôle ne s’affichera [`Map`](xref:Xamarin.Forms.Maps.Map) pas lors de l’exécution d’une version Release.

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Forms. Mappe les broches](~/xamarin-forms/user-interface/map/pins.md).
- [API Cartes](xref:Xamarin.Forms.Maps)
- [Convertisseur personnalisé de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)