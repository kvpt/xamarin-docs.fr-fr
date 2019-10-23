---
title: Initialisation et configuration de la carte Xamarin. Forms
description: Cet article explique comment utiliser la classe Xamarin. Forms Map pour utiliser les API Map natives sur chaque plateforme afin de fournir une expérience de mappage familière aux utilisateurs.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/07/2019
ms.openlocfilehash: d9b1b93b0667415281bb04e2c4264f03be19bd83
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697387"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Initialisation et configuration de la carte Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin. Forms utilise les API de mappage natives sur chaque plateforme._

Xamarin. Forms. Maps utilise les API de mappage natives sur chaque plateforme. Cela offre une expérience de cartes rapide et familière pour les utilisateurs, mais signifie que certaines étapes de configuration sont nécessaires pour respecter les exigences de chaque API de plateformes.
Une fois configuré, le contrôle `Map` fonctionne comme tout autre élément Xamarin. Forms dans le code commun.

Le contrôle de carte a été utilisé dans l’exemple [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps) , comme indiqué ci-dessous.

 [![Mappages dans l’exemple MobileCRM](map-images/maps-zoom-sml.png "Exemple de Map Control")](map-images/maps-zoom.png#lightbox "Exemple de Map Control")

La fonctionnalité de mappage peut être améliorée davantage en créant un [convertisseur personnalisé de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Initialisation de la carte

Lors de l’ajout de mappages à une application Xamarin. Forms, **Xamarin. Forms. Maps** est un package NuGet distinct que vous devez ajouter à chaque projet de la solution.
Sur Android, cela a également une dépendance sur GooglePlayServices (un autre NuGet) qui est téléchargé automatiquement lorsque vous ajoutez Xamarin. Forms. Maps.

Après l’installation du package NuGet, un code d’initialisation est requis dans chaque projet d’application, *après* l’appel de la méthode `Xamarin.Forms.Forms.Init`. Pour iOS, utilisez le code suivant :

```csharp
Xamarin.FormsMaps.Init();
```

Sur Android, vous devez transmettre les mêmes paramètres que `Forms.Init` :

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Pour la plateforme Windows universelle (UWP), utilisez le code suivant :

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Ajoutez cet appel dans les fichiers suivants pour chaque plateforme :

- fichier **iOS** -AppDelegate.cs, dans la méthode `FinishedLaunching`.
- Fichier **Android** -MainActivity.cs, dans la méthode `OnCreate`.
- Fichier **UWP** -MainPage.Xaml.cs, dans le constructeur `MainPage`.

Une fois que le package NuGet a été ajouté et la méthode d’initialisation appelée à l’intérieur de chaque application, `Xamarin.Forms.Maps` API peuvent être utilisées dans le projet Common .NET Standard Library ou dans le code de projet partagé.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuration de la plateforme

Des étapes de configuration supplémentaires sont requises sur certaines plateformes pour que la carte s’affiche.

### <a name="ios"></a>iOS

Pour accéder aux services d’emplacement sur iOS, vous devez définir les clés suivantes dans **info. plist**:

- iOS 11
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
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Vous pouvez également ajouter les entrées **info. plist** en mode **source** lors de la modification du fichier **info. plist** :

![Info. plist pour iOS 8](map-images/ios8-map-permissions.png "Entrées. plist des informations requises pour iOS 8")

### <a name="android"></a>Android

Pour utiliser l' [API Google Maps v2](https://developers.google.com/maps/documentation/android/) sur Android, vous devez générer une clé API et l’ajouter à votre projet Android.
Suivez les instructions dans le document Xamarin sur l' [obtention d’une clé API Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Après avoir effectué ces instructions, collez la clé API dans le fichier **Properties/fichier AndroidManifest. xml** (Affichez la source et recherchez/mettez à jour l’élément suivant) :

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Si vous n’avez pas de clé d’API valide, le contrôle Maps s’affichera sous la forme d’une zone grise sur Android.

> [!NOTE]
> Notez que, pour que vos APK accèdent à Google Maps, vous devez inclure les empreintes de l’algorithme SHA-1 et les noms des packages pour chaque magasin de clés (Debug et Release) que vous utilisez pour signer vos APK. Par exemple, si vous utilisez un ordinateur pour le débogage et un autre pour générer le APK de mise en production, vous devez inclure l’empreinte de certificat SHA-1 à partir du magasin de clés de débogage du premier ordinateur et l’empreinte de certificat SHA-1 du magasin de clés de version de deuxième ordinateur. N’oubliez pas également de modifier les informations d’identification de la clé si le **nom du package** de l’application est modifié. Consultez [obtention d’une clé de l’API Google Maps v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Vous devez également activer les autorisations appropriées en cliquant avec le bouton droit sur le projet Android, puis en sélectionnant **Options > générer > application Android** et en cochant les cases suivantes :

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

Certaines d’entre elles sont illustrées dans la capture d’écran ci-dessous :

![Autorisations requises pour Android](map-images/android-map-permissions.png "Autorisations requises pour Android")

Les deux derniers sont requis car les applications requièrent une connexion réseau pour télécharger les données cartographiques. Pour en savoir plus, consultez les informations sur les [autorisations](https://developer.android.com/reference/android/Manifest.permission.html) Android.

En outre, Android 9 a supprimé la bibliothèque cliente Apache HTTP du bootclasspath et n’est donc pas disponible pour les applications qui ciblent l’API 28 ou une version ultérieure. La ligne suivante doit être ajoutée au nœud `application` de votre fichier **fichier AndroidManifest. xml** pour continuer à utiliser le client HTTP Apache dans les applications qui ciblent l’API 28 ou une version ultérieure :

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Pour utiliser les mappages sur le plateforme Windows universelle vous devez générer un jeton d’autorisation. Pour plus d’informations, consultez [demander une clé d’authentification par mappages](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) sur MSDN.

Le jeton d’authentification doit ensuite être spécifié dans l’appel de méthode `FormsMaps.Init("AUTHORIZATION_TOKEN")` pour authentifier l’application avec Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configuration de la carte

Pour obtenir un exemple de la façon dont le contrôle Map peut être utilisé dans le code, consultez [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) dans l’exemple MobileCRM. Une classe de `MapPage` simple peut ressembler à ceci : Notez qu’une nouvelle `MapSpan` est créée pour positionner la vue de la carte :

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Type de mappage

Le contenu de la carte peut également être modifié en définissant la propriété `MapType`, afin d’afficher une carte postale standard (valeur par défaut), une image satellite ou une combinaison des deux.

```csharp
map.MapType = MapType.Street;
```

Les valeurs `MapType` valides sont les suivantes :

- `Hybrid`
- `Satellite`
- `Street` (valeur par défaut)

### <a name="map-region-and-mapspan"></a>Zone de mappage et MapSpan

Comme indiqué dans l’extrait de code ci-dessus, la spécification d’une instance de `MapSpan` à un constructeur de carte définit la vue initiale (point central et niveau de zoom) de la carte lorsqu’elle est chargée. Il existe deux façons de créer une instance de `MapSpan` :

- **MapSpan. FromCenterAndRadius ()** -méthode statique pour créer une étendue à partir d’une `Position` et en spécifiant une `Distance`.
- **New MapSpan ()** -constructeur qui utilise une `Position` et les degrés de latitude et de longitude à afficher.

La méthode `MoveToRegion` sur la classe Map peut ensuite être utilisée pour modifier le niveau de position ou de zoom de la carte. Pour modifier le niveau de zoom de la carte sans altérer l’emplacement, créez un nouveau `MapSpan` à l’aide de l’emplacement actuel de la propriété `VisibleRegion.Center` du contrôle de carte. Un `Slider` peut être utilisé pour contrôler le zoom de la carte comme suit (Toutefois, le zoom direct dans le contrôle Map ne peut pas mettre à jour actuellement la valeur du curseur) :

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![Cartes avec zoom](map-images/maps-zoom-sml.png "Zoom Map Control")](map-images/maps-zoom.png#lightbox "Zoom Map Control")

En outre, la classe [`Map`](xref:Xamarin.Forms.Maps.Map) a une propriété `MoveToLastRegionOnLayoutChange` de type `bool`, qui est stockée par une propriété pouvant être liée. Par défaut, cette propriété est `true`, ce qui indique que la zone de mappage affichée passe de sa région actuelle à sa zone définie précédemment quand une modification de disposition se produit, par exemple lors de la rotation de l’appareil. Quand cette propriété a la valeur `false`, la zone de mappage affichée reste centrée en cas de modification de la disposition. L’exemple suivant illustre la définition de cette propriété :

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-clicks"></a>Clics de la carte

`Map` définit un événement `MapClicked` qui est déclenché lorsque le mappage est activé. L’objet `MapClickedEventArgs` qui accompagne l’événement `MapClicked` a une propriété unique nommée `Position`, de type `Position`. Lorsque l’événement est déclenché, la valeur de la propriété `Position` est définie sur l’emplacement de la carte qui a été taraudé.

L’exemple de code suivant montre un gestionnaire d’événements pour l’événement `MapClicked` :

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Dans cet exemple, le gestionnaire d’événements `OnMapClicked` génère la latitude et la longitude qui représentent l’emplacement du mappage taraudé.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Créer un mappage en XAML

Les mappages peuvent également être créés en XAML, comme illustré dans cet exemple :

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Une définition d’espace de noms `xmlns` supplémentaire est requise pour référencer les contrôles Xamarin. Forms. Maps. Dans l’exemple précédent, l’espace de noms `Xamarin.Forms.Maps` est référencé via le mot clé `maps`.

La `MapRegion` peut être définie dans le code à l’aide de la référence nommée pour le `Map` :

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Épingles Xamarin. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API Cartes](xref:Xamarin.Forms.Maps)
- [Convertisseur personnalisé de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
