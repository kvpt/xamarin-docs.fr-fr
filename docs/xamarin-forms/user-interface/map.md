---
title: Mappage de Xamarin.Forms
description: Cet article explique comment utiliser la classe Map de Xamarin.Forms à utiliser l’API de mappage natif sur chaque plateforme pour fournir qu'un familier mappe l’expérience des utilisateurs.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2019
ms.openlocfilehash: 60d78797406f2e69c435fb597e36775d906852f9
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513110"
---
# <a name="xamarinforms-map"></a>Mappage de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)

_Xamarin.Forms utilise l’API de mappage natif sur chaque plateforme._

Xamarin.Forms.Maps utilise l’API de mappage natif sur chaque plateforme. Cela offre une expérience de cartes rapide, familière pour les utilisateurs, mais signifie que certaines étapes de configuration sont nécessaires pour respecter chaque conditions d’API de plateformes.
Une fois configuré, le `Map` contrôler fonctionne exactement comme tout autre élément de Xamarin.Forms dans le code commun.

Le contrôle de carte a été utilisé dans le [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/) exemple, qui est indiqué ci-dessous.

 [![Cartes dans l’exemple MobileCRM](map-images/maps-zoom-sml.png "exemple de contrôle de carte")](map-images/maps-zoom.png#lightbox "exemple de contrôle de carte")

Fonctionnalité de carte peut encore être améliorée en créant un [mapper convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Initialisation du plan

Lorsque vous ajoutez des mappages à une application Xamarin.Forms, **Xamarin.Forms.Maps** est un package NuGet distinct, vous devez ajouter à chaque projet dans la solution.
Sur Android, cela a également une dépendance sur GooglePlayServices (un autre NuGet) qui est téléchargé automatiquement lorsque vous ajoutez Xamarin.Forms.Maps.

Après avoir installé le package NuGet, du code d’initialisation est requis dans chaque projet d’application, *après* le `Xamarin.Forms.Forms.Init` appel de méthode. Pour iOS, utilisez le code suivant :

```csharp
Xamarin.FormsMaps.Init();
```

Sur Android, vous devez passer les mêmes paramètres que `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Pour la plateforme Windows universelle (UWP) utilisez le code suivant :

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Ajoutez cet appel dans les fichiers suivants pour chaque plateforme :

-  **iOS** -fichier AppDelegate.cs, dans le `FinishedLaunching` (méthode).
-  **Android** -MainActivity.cs de fichiers, dans le `OnCreate` (méthode).
-  **UWP** -fichier MainPage.xaml.cs, dans le `MainPage` constructeur.

Une fois que le package NuGet a été ajouté et la méthode d’initialisation appelée à l’intérieur de chaque application, `Xamarin.Forms.Maps` API peuvent être utilisées dans le projet de bibliothèque .NET Standard commun ou le code de projet partagé.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configuration de la plateforme

Étapes de configuration supplémentaires sont requises sur certaines plateformes avant que la carte s’affiche.

### <a name="ios"></a>iOS

Pour accéder aux services de localisation sur iOS, vous devez définir les clés suivantes dans **Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – pour l’utilisation des services de localisation lors de l’application est en cours d’utilisation
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – pour l’utilisation des services de localisation à tout moment
- iOS 10 et versions antérieures
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – pour l’utilisation des services de localisation lors de l’application est en cours d’utilisation
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – pour l’utilisation des services de localisation à tout moment    

Pour prendre en charge iOS 11 et versions antérieures, vous pouvez inclure toutes les trois clés : `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, et `NSLocationAlwaysUsageDescription`.

La représentation XML de ces clés dans **Info.plist** est indiqué ci-dessous. Vous devez mettre à jour le `string` valeurs afin de refléter la façon dont votre application utilise les informations d’emplacement :

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Le **Info.plist** entrées peuvent également être ajoutées dans **Source** affichage lors de la modification la **Info.plist** fichier :

![Info.plist pour iOS 8](map-images/ios8-map-permissions.png "iOS 8 des entrées Info.plist requises")

### <a name="android"></a>Android

Pour utiliser le [v2 de l’API Google Maps](https://developers.google.com/maps/documentation/android/) sur Android, vous devez générer une clé d’API et ajoutez-le à votre projet Android.
Suivez les instructions de la documentation de Xamarin [obtention d’une clé de v2 de l’API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Après avoir suivi ces instructions, collez la clé d’API dans le **Properties/Androidmanifest.XML** fichier (afficher la source et find/mettre à jour l’élément suivant) :

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Sans une clé API valide, le contrôle maps affichera qu’une zone grise sur Android.

> [!NOTE]
> Notez que, afin que votre APK accéder à Google Maps, vous devez inclure les empreintes digitales de SHA-1 et empaqueter des noms pour chaque magasin de clés (debug et release) que vous utilisez pour signer votre APK. Par exemple, si vous utilisez un ordinateur pour le débogage et un autre ordinateur pour générer le fichier APK de mise en production, vous devez inclure l’empreinte de certificat SHA-1 à partir du magasin de clés de débogage du premier ordinateur et l’empreinte de certificat SHA-1 à partir du magasin de clés de version de le deuxième ordinateur. Pensez également à modifier les informations d’identification clées si l’application **nom_package** modifications. Consultez [obtention d’une clé de v2 de l’API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Vous devez également activer les autorisations appropriées en effectuant un clic droit sur le projet Android et en sélectionnant **Options > Générer > Application Android** et déjà les éléments suivants :

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Certaines d'entre elles sont présentées dans la capture d’écran ci-dessous :

![Autorisations requises pour Android](map-images/android-map-permissions.png "les autorisations requises pour Android")

Les deux derniers sont requis, car les applications nécessitent une connexion réseau pour télécharger les données cartographiques. En savoir plus sur Android [autorisations](https://developer.android.com/reference/android/Manifest.permission.html) pour en savoir plus.

En outre, 9 Android a supprimé la bibliothèque de client HTTP Apache à partir de la bootclasspath, et par conséquent, il n’est pas disponible pour les applications qui ciblent des API 28 ou une version ultérieure. La ligne suivante doit être ajoutée à la `application` nœud de votre **AndroidManifest.xml** fichier pour continuer à utiliser le client HTTP Apache dans les applications qui ciblent des API 28 ou une version ultérieure :

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Pour utiliser des cartes sur la plateforme Windows universelle, vous devez générer un jeton d’autorisation. Pour plus d’informations, consultez [demander une clé d’authentification maps](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) sur MSDN.

Le jeton d’authentification doit ensuite être spécifié dans le `FormsMaps.Init("AUTHORIZATION_TOKEN")` appel de méthode, pour authentifier l’application avec Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configuration de la carte

Consultez le [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) dans l’exemple MobileCRM pour obtenir un exemple de comment le contrôle de carte peut être utilisé dans le code. Une simple `MapPage` classe peut ressembler à cet - avis qui un nouveau `MapSpan` est créé pour positionner les vue de la carte :

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

### <a name="map-type"></a>Type de carte

Le contenu de la carte peut également être modifié en définissant le `MapType` propriété, pour afficher une carte postale régulière (la valeur par défaut), les images satellite ou une combinaison des deux.

```csharp
map.MapType == MapType.Street;
```

Valide `MapType` les valeurs sont :

-  Hybride
-  Satellite
-  Rue (la valeur par défaut)

### <a name="map-region-and-mapspan"></a>Région de carte et MapSpan

Comme indiqué dans l’extrait de code ci-dessus, en fournissant un `MapSpan` instance à un constructeur de la carte définit la vue initiale (point central et un niveau de zoom) de la carte lorsqu’il est chargé. Le `MoveToRegion` méthode sur la classe map peut ensuite être utilisée pour modifier le niveau de zoom ou de la position de la carte. Il existe deux façons de créer un nouveau `MapSpan` instance :

-  **MapSpan.FromCenterAndRadius()** -méthode statique pour créer une étendue d’un `Position` et en spécifiant un `Distance` .
-  **nouvelle MapSpan ()** -constructeur qui utilise un `Position` et les degrés de latitude et longitude à afficher.


Pour modifier le niveau de zoom de la carte sans modifier l’emplacement, créez un nouveau `MapSpan` à l’aide de l’emplacement actuel à partir de la `VisibleRegion.Center` propriété du contrôle de carte. Un `Slider` peut servir à contrôler le zoom de mappage comme suit (mais le zoom directement dans le contrôle de carte actuellement ne peut pas mettre à jour la valeur du curseur) :

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Mappages avec zoom](map-images/maps-zoom-sml.png "effectuer un zoom avant du contrôle de carte")](map-images/maps-zoom.png#lightbox "effectuer un zoom avant du contrôle de carte")

### <a name="map-pins"></a>Codes confidentiels de carte

Emplacements peuvent être marquées sur la carte avec `Pin` objets.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

`PinType` peut être définie à une des valeurs suivantes, qui peuvent affecter la façon dont le code confidentiel est rendu (selon la plateforme) :

-  Generic
-  Sur place
-  SavedPin
-  Résultats de la recherche

### <a name="map-clicks"></a>Mapper des clics

`Map` définit un `MapClicked` événement est déclenché lors de l’appui sur la carte. Le `MapClickedEventArgs` objet qui accompagne le `MapClicked` événement a une propriété unique nommée `Position`, de type `Position`. Lorsque l’événement est déclenché, la valeur de la `Position` propriété est définie sur l’emplacement de la carte qui a été activé par un clic.

L’exemple de code suivant montre un gestionnaire d’événements pour le `MapClicked` événement :

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

Dans cet exemple, le `OnMapClicked` Gestionnaire d’événements génère la latitude et la longitude qui représente l’emplacement de la carte drainées.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Créer une carte dans XAML

Maps peuvent également être créés dans XAML, comme illustré dans cet exemple :

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
> Un autre `xmlns` définition de l’espace de noms est obligatoire pour référencer les contrôles Xamarin.Forms.Maps.

Le `MapRegion` et `Pins` peuvent être définies dans le code à l’aide de la référence nommée pour le `Map`:

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>Remplir un mappage avec des données à l’aide de la liaison de données

Le [ `Map` ](xref:Xamarin.Forms.Maps.Map) classe expose également les propriétés suivantes :

- `ItemsSource` : Spécifie la collection de `IEnumerable` éléments à afficher.
- `ItemTemplate` – Spécifie le [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément dans la collection d’éléments affichés.
- `ItemTemplateSelector` – Spécifie le [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) pour un élément lors de l’exécution.

> [!NOTE]
> Le `ItemTemplate` propriété est prioritaire lorsqu’à la fois le `ItemTemplate` et `ItemTemplateSelector` propriétés sont définies.

Un [ `Map` ](xref:Xamarin.Forms.Maps.Map) peut être rempli de données à l’aide de la liaison de données à lier son `ItemsSource` propriété un `IEnumerable` collection :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

Le `ItemsSource` lie les données de propriété pour le `Locations` propriété du modèle de vue connectée, qui retourne un `ObservableCollection` de `Location` objets, qui est un type personnalisé. Chaque `Location` objet définit `Address` et `Description` propriétés, de type `string`et un `Position` propriété, de type [ `Position` ](xref:Xamarin.Forms.Maps.Position).

L’apparence de chaque élément dans le `IEnumerable` collection est définie en configurant le `ItemTemplate` propriété à un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) qui contient un [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) que les données est liée à l’objet propriétés appropriées.

Le montrent les captures d’écran suivants un [ `Map` ](xref:Xamarin.Forms.Maps.Map) affichant un [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) collection à l’aide de la liaison de données :

[![Capture d’écran de données carte liée quilles, iOS et Android](map-images/pins-itemssource.png "épingle avec des données liées")](map-images/pins-itemssource-large.png#lightbox "épingle avec des données liées")

### <a name="choose-item-appearance-at-runtime"></a>Choisissez l’apparence de l’élément lors de l’exécution

L’apparence de chaque élément dans le `IEnumerable` collection peut être choisie lors de l’exécution, selon la valeur de l’élément, en définissant le `ItemTemplateSelector` propriété à un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

L’exemple suivant montre la `MapItemTemplateSelector` classe :

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

Le `MapItemTemplateSelector` classe définit `DefaultTemplate` et `XamarinTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) propriétés qui sont définies sur les différents modèles de données. Le `OnSelectTemplate` méthode retourne le `XamarinTemplate`, qui affiche « Xamarin » en tant qu’étiquette quand un `Pin` est activé par un clic, lorsque l’élément a une adresse qui contient « San Francisco ». Lorsque l’élément n’a pas une adresse qui contient « San Francisco », le `OnSelectTemplate` méthode retourne le `DefaultTemplate`.

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Liens connexes

- [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)
- [Mapper le convertisseur personnalisé](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Exemples Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Création d’un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
