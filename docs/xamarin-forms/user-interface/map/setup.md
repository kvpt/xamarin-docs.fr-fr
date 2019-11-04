---
title: Initialisation et configuration de la carte Xamarin. Forms
description: Le package NuGet Xamarin. Forms. Maps est requis pour utiliser la fonctionnalité Maps dans une application. En outre, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour être accordé à l’application.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/31/2019
ms.openlocfilehash: dd451ae1acd233c1d3de675357bb172f25716f59
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426159"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Initialisation et configuration de la carte Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Le contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) utilise le contrôle de carte natif sur chaque plateforme. Cela offre une expérience de cartes rapide et familière pour les utilisateurs, mais signifie que certaines étapes de configuration sont nécessaires pour respecter les exigences de chaque API de plateformes.

## <a name="map-initialization"></a>Initialisation de la carte

Le contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) est fourni par le package NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , qui doit être ajouté à chaque projet de la solution.

Après l’installation du package NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , il doit être initialisé dans chaque projet de plateforme.

Sur iOS, cela doit se produire dans **AppDelegate.cs** en appelant la méthode `Xamarin.FormsMaps.Init` *après* la méthode `Xamarin.Forms.Forms.Init` :

```csharp
Xamarin.FormsMaps.Init();
```

Sur Android, cela doit se produire dans **MainActivity.cs** en appelant la méthode `Xamarin.FormsMaps.Init` *après* la méthode `Xamarin.Forms.Forms.Init` :

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

Sur la plateforme Windows universelle (UWP), cela doit se produire dans **MainPage.Xaml.cs** en appelant la méthode `Xamarin.FormsMaps.Init` à partir du constructeur `MainPage` :

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Une fois le package NuGet ajouté et la méthode d’initialisation appelée à l’intérieur de chaque application, `Xamarin.Forms.Maps` API peuvent être utilisées dans le projet de code partagé.

## <a name="platform-configuration"></a>Configuration de la plateforme

Une configuration supplémentaire est requise sur Android et le plateforme Windows universelle (UWP) pour que la carte s’affiche. En outre, sur iOS, Android et UWP, l’accès à l’emplacement de l’utilisateur nécessite des autorisations d’emplacement pour avoir été accordé à l’application.

### <a name="ios"></a>iOS

L’affichage et l’interaction avec une carte sur iOS ne nécessitent aucune configuration supplémentaire. Toutefois, pour accéder aux services d’emplacement, vous devez définir les clés suivantes dans **info. plist**:

- iOS 11 et versions ultérieures
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : pour utiliser des services de localisation quand l’application est en cours d’utilisation
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : pour utiliser des services de localisation à tout moment
- iOS 10 et versions antérieures
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : pour utiliser des services de localisation quand l’application est en cours d’utilisation
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : pour utiliser des services de localisation à tout moment    

Pour prendre en charge iOS 11 et les versions antérieures, vous pouvez inclure les trois clés suivantes : `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` et `NSLocationAlwaysUsageDescription`.

La représentation XML de ces clés dans **info. plist** est indiquée ci-dessous. Vous devez mettre à jour les valeurs de `string` pour refléter la manière dont votre application utilise les informations d’emplacement :

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
1. facultatif Autorisations relatives à l’emplacement d’exécution de la requête dans la classe `MainActivity`.

Pour obtenir un exemple de fichier manifeste correctement configuré, consultez [fichier AndroidManifest. xml](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) dans l’exemple d’application.

#### <a name="get-a-google-maps-api-key"></a>Obtenir une clé API Google Maps

Pour utiliser l' [API Google Maps](https://developers.google.com/maps/documentation/android/) sur Android, vous devez générer une clé API. Pour ce faire, suivez les instructions de la procédure d' [obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Une fois que vous avez obtenu une clé API, elle doit être ajoutée dans l’élément `<application>` du fichier **Properties/fichier AndroidManifest. xml** :

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

Cela incorpore la clé API dans le manifeste. Sans clé d’API valide, le contrôle [`Map`](xref:Xamarin.Forms.Maps.Map) affichera une grille vide.

> [!NOTE]
> Notez que, pour que vos APK accèdent à Google Maps, vous devez inclure les empreintes de l’algorithme SHA-1 et les noms des packages pour chaque magasin de clés (Debug et Release) que vous utilisez pour signer vos APK. Par exemple, si vous utilisez un ordinateur pour le débogage et un autre pour générer le APK de mise en production, vous devez inclure l’empreinte de certificat SHA-1 à partir du magasin de clés de débogage du premier ordinateur et l’empreinte de certificat SHA-1 du magasin de clés de version de deuxième ordinateur. N’oubliez pas également de modifier les informations d’identification de la clé si le **nom du package** de l’application est modifié. Consultez [obtention d’une clé API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

#### <a name="specify-the-google-play-services-version-number"></a>Spécifier le numéro de version de Google Play services

Ajoutez la déclaration suivante dans l’élément `<application>` de **fichier AndroidManifest. xml**:

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

Cela incorpore la version de Google Play services avec laquelle l’application a été compilée, dans le manifeste.

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>Spécifier la configuration requise pour la bibliothèque Legacy HTTP Apache

Si votre application Xamarin. Forms cible l’API 28 ou une version ultérieure, vous devez ajouter la déclaration suivante dans l’élément `<application>` de **fichier AndroidManifest. xml**:

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

Cela indique à l’application d’utiliser la bibliothèque cliente Apache HTTP, qui a été supprimée du `bootclasspath` dans Android 9.

#### <a name="specify-the-write_external_storage-permission"></a>Spécifier l’autorisation WRITE_EXTERNAL_STORAGE

Si votre application cible l’API 22 ou une valeur antérieure, il peut être nécessaire d’ajouter l’autorisation `WRITE_EXTERNAL_STORAGE` au manifeste, en tant qu’enfant de l’élément `<manifest>` :

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Cela n’est pas obligatoire si votre application cible l’API 23 ou une version ultérieure.

#### <a name="specify-location-permissions"></a>Spécifier les autorisations d’emplacement

Si votre application doit accéder à l’emplacement de l’utilisateur, vous devez demander l’autorisation en ajoutant les autorisations `ACCESS_COARSE_LOCATION` ou `ACCESS_FINE_LOCATION` au manifeste (ou les deux), en tant qu’enfant de l’élément `<manifest>` :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

L’autorisation `ACCESS_COARSE_LOCATION` permet à l’API d’utiliser des données WiFi ou mobile, ou les deux, pour déterminer l’emplacement de l’appareil. Les autorisations de `ACCESS_FINE_LOCATION` permettent à l’API d’utiliser les données Global Positioning System (GPS), WiFi ou mobile pour déterminer un emplacement précis possible.

Vous pouvez également activer ces autorisations à l’aide de l’éditeur de manifeste pour ajouter les autorisations suivantes :

- `AccessCoarseLocation`
- `AccessFineLocation`

Celles-ci sont indiquées dans la capture d’écran ci-dessous :

![Autorisations requises pour Android](setup-images/android-map-permissions.png "Autorisations requises pour Android")

#### <a name="request-runtime-location-permissions"></a>Autorisations relatives à l’emplacement d’exécution de la demande

Si votre application cible l’API 23 ou une version ultérieure et doit accéder à l’emplacement de l’utilisateur, elle doit vérifier s’il dispose de l’autorisation nécessaire au moment de l’exécution et la demander si elle ne l’a pas. Cela peut être accompli de la façon suivante :

1. Dans la classe `MainActivity`, ajoutez les champs suivants :

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. Dans la classe `MainActivity`, ajoutez la substitution `OnStart` suivante :

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

    À condition que l’application cible l’API 23 ou une version ultérieure, ce code effectue une vérification d’autorisation Runtime pour l’autorisation `AccessFineLocation`. Si l’autorisation n’a pas été accordée, une demande d’autorisation est effectuée en appelant la méthode `RequestPermissions`.

1. Dans la classe `MainActivity`, ajoutez la substitution `OnRequestPermissionsResult` suivante :

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

Sur UWP, votre application doit être authentifiée pour pouvoir afficher une carte et consommer des services de mappage. Pour authentifier votre application, vous devez spécifier une clé d’authentification Maps. Pour plus d’informations, consultez [demander une clé d’authentification par mappages](/windows/uwp/maps-and-location/authentication-key). Le jeton d’authentification doit ensuite être spécifié dans l’appel de méthode `FormsMaps.Init("AUTHORIZATION_TOKEN")`, pour authentifier l’application avec Bing Maps.

En outre, si votre application doit accéder à l’emplacement de l’utilisateur, vous devez activer la fonctionnalité d’emplacement dans le manifeste du package. Cela peut être accompli de la façon suivante :

1. Dans **Explorateur de solutions**, double-cliquez sur **Package. appxmanifest** et sélectionnez l’onglet **capacités** .
1. Dans la liste des **fonctionnalités** , activez la case à cocher **emplacement**. Cela ajoute la fonctionnalité d’appareil `location` au fichier manifeste du package.

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Épingles Xamarin. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API Cartes](xref:Xamarin.Forms.Maps)
- [Convertisseur personnalisé de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
