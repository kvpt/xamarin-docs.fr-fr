---
title : " Xamarin.Forms Description des codes confidentiels" : "cet article explique comment créer des codes confidentiels sur une Xamarin.Forms carte."
ms. Prod : xamarin ms. AssetID : F8FC081B-A811-4FBB-B8F8-30D6FD36BD40 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/23/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map-pins"></a>Xamarin.FormsÉpingler les broches

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Le Xamarin.Forms [`Map`](xref:Xamarin.Forms.Maps.Map) contrôle permet aux emplacements d’être marqués avec des [`Pin`](xref:Xamarin.Forms.Maps.Pin) objets. Un `Pin` est un marqueur de carte qui ouvre une fenêtre d’informations lorsqu’il est frappé :

[![Capture d’écran d’un code confidentiel de carte et de sa fenêtre d’informations, sur iOS et Android](pins-images/pin-and-information-window.png "Mapper le code confidentiel avec la fenêtre d’informations")](pins-images/pin-and-information-window-large.png#lightbox "Mapper le code confidentiel avec la fenêtre d’informations")

Lorsqu’un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objet est ajouté à la [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) collection, le code PIN est rendu sur la carte.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe a les propriétés suivantes :

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), de type `string` , qui représente généralement l’adresse de l’emplacement du code confidentiel. Toutefois, il peut s’agir de n’importe quel `string` contenu, pas seulement d’une adresse.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), de type `string` , qui représente généralement le titre du pin.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), de type [`Position`](xref:Xamarin.Forms.Maps.Position) , qui représente la latitude et la longitude du code confidentiel.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), de type [`PinType`](xref:Xamarin.Forms.Maps.PinType) , qui représente le type de pin.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’un `Pin` peut être la cible des liaisons de données. Pour plus d’informations sur les objets de liaison de données `Pin` , consultez [afficher une collection de codes confidentiels](#display-a-pin-collection).

En outre, la [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe définit `MarkerClicked` les `InfoWindowClicked` événements et. L' `MarkerClicked` événement est déclenché lorsqu’un code PIN est activé et que l' `InfoWindowClicked` événement est déclenché lorsque la fenêtre d’informations est activée. L' `PinClickedEventArgs` objet qui accompagne les deux événements a une `HideInfoWindow` propriété unique, de type `bool` .

## <a name="display-a-pin"></a>Afficher un code confidentiel

Un [`Pin`](xref:Xamarin.Forms.Maps.Pin) peut être ajouté à un [`Map`](xref:Xamarin.Forms.Maps.Map) en XAML :

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False">
         <x:Arguments>
             <maps:MapSpan>
                 <x:Arguments>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     <x:Double>0.01</x:Double>
                     <x:Double>0.01</x:Double>
                 </x:Arguments>
             </maps:MapSpan>
         </x:Arguments>
         <maps:Map.Pins>
             <maps:Pin Label="Santa Cruz"
                       Address="The city with a boardwalk"
                       Type="Place">
                 <maps:Pin.Position>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                 </maps:Pin.Position>
             </maps:Pin>
         </maps:Map.Pins>
     </maps:Map>
</ContentPage>
```

Ce code XAML crée un [`Map`](xref:Xamarin.Forms.Maps.Map) objet qui affiche la zone spécifiée par l' [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) objet. L' `MapSpan` objet est centré sur la latitude et la longitude représentées par un [`Position`](xref:Xamarin.Forms.Maps.Position) objet, qui étend 0,01 de latitude et des degrés de longitude. Un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objet est ajouté à la [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) collection et dessiné à l' `Map` emplacement spécifié par sa [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) propriété. Pour plus d’informations sur la [`Position`](xref:Xamarin.Forms.Maps.Position) structure, consultez [position et distance](position-distance.md)de la carte. Pour plus d’informations sur le passage d’arguments en XAML aux objets qui n’ont pas de constructeurs par défaut, consultez [passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md).

Le code C# équivalent est :

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
Pin pin = new Pin
{
  Label = "Santa Cruz",
  Address = "The city with a boardwalk",
  Type = PinType.Place,
  Position = new Position(36.9628066, -122.0194722)
};
map.Pins.Add(pin);
```

> [!WARNING]
> [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label)Si vous ne définissez pas la propriété, une `ArgumentException` exception est levée lorsque le [`Pin`](xref:Xamarin.Forms.Maps.Pin) est ajouté à un [`Map`](xref:Xamarin.Forms.Maps.Map) .

Cet exemple de code entraîne le rendu d’un seul code confidentiel sur une carte :

[![Capture d’écran d’un code confidentiel de carte, sur iOS et Android](pins-images/pin-only.png "Code confidentiel de la carte")](pins-images/pin-only-large.png#lightbox "Code confidentiel de la carte")

## <a name="interact-with-a-pin"></a>Interagir avec un code confidentiel

Par défaut, lorsqu’un [`Pin`](xref:Xamarin.Forms.Maps.Pin) est utilisé, sa fenêtre d’informations s’affiche :

[![Capture d’écran d’un code confidentiel de carte et de sa fenêtre d’informations, sur iOS et Android](pins-images/pin-and-information-window.png "Mapper le code confidentiel avec la fenêtre d’informations")](pins-images/pin-and-information-window-large.png#lightbox "Mapper le code confidentiel avec la fenêtre d’informations")

En appuyant ailleurs sur la carte, vous fermez la fenêtre d’informations.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe définit un `MarkerClicked` événement, qui est déclenché lorsqu’un `Pin` est frappé. Il n’est pas nécessaire de gérer cet événement pour afficher la fenêtre d’informations. Au lieu de cela, cet événement doit être traité lorsqu’il est nécessaire de notifier qu’un code confidentiel spécifique a été frappé.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe définit également un `InfoWindowClicked` événement qui est déclenché lorsqu’une fenêtre d’informations est frappée. Cet événement doit être traité lorsqu’il est nécessaire de notifier qu’une fenêtre d’informations spécifique a été exploitée.

Le code suivant montre un exemple de gestion de ces événements :

```csharp
using Xamarin.Forms.Maps;
// ...
Pin boardwalkPin = new Pin
{
    Position = new Position(36.9641949, -122.0177232),
    Label = "Boardwalk",
    Address = "Santa Cruz",
    Type = PinType.Place
};
boardwalkPin.MarkerClicked += async (s, args) =>
{
    args.HideInfoWindow = true;
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
};

Pin wharfPin = new Pin
{
    Position = new Position(36.9571571, -122.0173544),
    Label = "Wharf",
    Address = "Santa Cruz",
    Type = PinType.Place
};
wharfPin.InfoWindowClicked += async (s, args) =>
{
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
};
```

L' `PinClickedEventArgs` objet qui accompagne les deux événements a une `HideInfoWindow` propriété unique, de type `bool` . Quand cette propriété a la valeur à `true` l’intérieur d’un gestionnaire d’événements, la fenêtre d’informations est masquée.

## <a name="pin-types"></a>Types de code confidentiel

[`Pin`](xref:Xamarin.Forms.Maps.Pin)les objets incluent une [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) propriété, de type [`PinType`](xref:Xamarin.Forms.Maps.PinType) , qui représente le type de pin. L’énumération `PinType` définit les membres suivants :

- `Generic`, représente un code confidentiel générique.
- `Place`, représente un code confidentiel pour un lieu.
- `SavedPin`, représente un code confidentiel pour un emplacement enregistré.
- `SearchResult`, représente un code confidentiel pour un résultat de recherche.

Toutefois, le fait d’affecter [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) à la propriété un [`PinType`](xref:Xamarin.Forms.Maps.PinType) membre n’a pas pour effet de modifier l’apparence du code PIN rendu. Au lieu de cela, vous devez créer un convertisseur personnalisé pour personnaliser l’apparence du pin. Pour plus d’informations, consultez [Personnalisation d’un code confidentiel de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

## <a name="display-a-pin-collection"></a>Afficher une collection d’épingles

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe définit les propriétés suivantes :

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), de type `IEnumerable` , qui spécifie la collection d' `IEnumerable` éléments à afficher.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément de la collection d’éléments affichés.
- `ItemTemplateSelector`, de type [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) , qui spécifie le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.

> [!IMPORTANT]
> La [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) propriété est prioritaire lorsque les `ItemTemplate` Propriétés et `ItemTemplateSelector` sont définies.

Une [`Map`](xref:Xamarin.Forms.Maps.Map) peut être remplie avec des codes confidentiels à l’aide d’une liaison de données pour lier sa [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) propriété à une `IEnumerable` collection :

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

Les [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) données de propriété sont liées à la `Locations` propriété du ViewModel connecté, qui retourne un `ObservableCollection` `Location` objet d’objets, qui est un type personnalisé. Chaque `Location` objet définit `Address` les `Description` Propriétés et, de type `string` et une `Position` propriété, de type [`Position`](xref:Xamarin.Forms.Maps.Position) .

L’apparence de chaque élément de la `IEnumerable` collection est définie en affectant [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) à la propriété une valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui contient un [`Pin`](xref:Xamarin.Forms.Maps.Pin) objet que les données lient aux propriétés appropriées.

Les captures d’écran suivantes montrent comment afficher [`Map`](xref:Xamarin.Forms.Maps.Map) une [`Pin`](xref:Xamarin.Forms.Maps.Pin) collection à l’aide de la liaison de données :

[![Capture d’écran de la carte avec des broches liées aux données, sur iOS et Android](pins-images/pins-itemsource.png "Mapper avec des codes confidentiels liés aux données")](pins-images/pins-itemsource-large.png#lightbox "Mapper avec des codes confidentiels liés aux données")

### <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément de la `IEnumerable` collection peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant `ItemTemplateSelector` à la propriété [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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
                    <!-- Change the property values, or the properties that are bound to. -->
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

L’exemple suivant illustre la `MapItemTemplateSelector` classe :

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

La `MapItemTemplateSelector` classe définit `DefaultTemplate` et les `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriétés qui sont définies sur des modèles de données différents. La `OnSelectTemplate` méthode retourne le `XamarinTemplate` , qui affiche « Xamarin » comme étiquette lorsqu’un `Pin` est frappé, lorsque l’élément a une adresse qui contient « San Francisco ». Lorsque l’élément n’a pas d’adresse contenant « San Francisco », la `OnSelectTemplate` méthode retourne `DefaultTemplate` .

> [!NOTE]
> Un cas d’usage de cette fonctionnalité consiste à lier des propriétés d’objets sous-classées [`Pin`](xref:Xamarin.Forms.Maps.Pin) à des propriétés différentes, en fonction du `Pin` sous-type.

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Convertisseur personnalisé de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Passage d’arguments en XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Création d’un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
