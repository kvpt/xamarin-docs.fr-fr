---
title: Xamarin. Forms, épingles
description: Cet article explique comment créer des codes confidentiels sur une carte Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/23/2019
ms.openlocfilehash: 76535f9c31a9dc138e132a3e582b986daf89bdb0
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697338"
---
# <a name="xamarinforms-map-pins"></a>Xamarin. Forms, épingles

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Le contrôle Xamarin. Forms `Maps` permet aux emplacements d’être marqués avec des objets `Pin`. Un `Pin` est un marqueur de carte qui ouvre une fenêtre d’informations lorsque l’utilisateur clique dessus ou appuie dessus.

La classe `Pin` a les propriétés suivantes:

- `Type` est une valeur d’énumération `PinType` : Generic, place, SavedPin ou SearchResult.
- `Position` est une `Position` instance contenant la latitude et la longitude du code confidentiel.
- `Label` est un `string` généralement affiché en tant que titre du pin.
- `Address` est un `string` qui s’affiche dans la fenêtre info. Il peut s’agir de n’importe quel contenu `string`, pas seulement d’une adresse.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les `Pin` peuvent être la cible des liaisons de données. Pour plus d’informations, consultez [créer des broches avec la liaison de données](#create-pins-with-data-binding).

## <a name="create-map-pins"></a>Créer des codes confidentiels de carte

Une instance de `Pin` peut être créée dans le code et ajoutée à un mappage :

```csharp
Pin pin1 = new Pin
{
    Type = PinType.Place,
    Position = new Position(47.6368678, -122.137305),
    Label = "Example Pin 1",
    Address = "Example custom details..."
};
map.Pins.Add(pin1);
```

> [!NOTE]
> La valeur `PinType` affecte le mode de rendu des codes confidentiels en fonction de la plateforme. Pour personnaliser l’apparence d’un code confidentiel, vous devez créer un convertisseur personnalisé. Pour plus d’informations, consultez [Personnalisation d’un code confidentiel de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="create-pins-with-data-binding"></a>Créer des broches avec la liaison de données

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) expose les propriétés suivantes :

- `ItemsSource` : spécifie la collection d’éléments de `IEnumerable` à afficher.
- `ItemTemplate` : spécifie le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à appliquer à chaque élément de la collection d’éléments affichés.
- `ItemTemplateSelector` : spécifie le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) qui sera utilisé pour choisir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) pour un élément au moment de l’exécution.

> [!NOTE]
> La propriété `ItemTemplate` est prioritaire lorsque les propriétés `ItemTemplate` et `ItemTemplateSelector` sont définies.

Une [`Map`](xref:Xamarin.Forms.Maps.Map) peut être remplie avec des données à l’aide de la liaison de données pour lier sa propriété `ItemsSource` à une collection `IEnumerable` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
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

Les données de la propriété `ItemsSource` sont liées à la propriété `Locations` du modèle de vue connecté, qui retourne un `ObservableCollection` d' `Location` objets, qui est un type personnalisé. Chaque objet `Location` définit des propriétés `Address` et `Description`, de type `string` et une propriété `Position`, de type [`Position`](xref:Xamarin.Forms.Maps.Position).

L’apparence de chaque élément de la collection `IEnumerable` est définie en affectant à la propriété `ItemTemplate` la valeur d’un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui contient un objet [`Pin`](xref:Xamarin.Forms.Maps.Pin) dont les données sont liées aux propriétés appropriées.

Les captures d’écran suivantes montrent un [`Map`](xref:Xamarin.Forms.Maps.Map) affichant une collection [`Pin`](xref:Xamarin.Forms.Maps.Pin) à l’aide de la liaison de données :

[![Capture d’écran de la carte avec des broches liées aux données, sur iOS et Android](map-images/pins-itemssource.png "Mapper avec des codes confidentiels liés aux données")](map-images/pins-itemssource-large.png#lightbox "Mapper avec des codes confidentiels liés aux données")

### <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément de la collection `IEnumerable` peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant à la propriété `ItemTemplateSelector` la valeur d’un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

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

L’exemple suivant illustre la classe `MapItemTemplateSelector` :

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

La classe `MapItemTemplateSelector` définit les propriétés de `DefaultTemplate` et de `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui sont définies sur des modèles de données différents. La méthode `OnSelectTemplate` retourne le `XamarinTemplate`, qui affiche « Xamarin » comme étiquette lorsqu’un `Pin` est frappé, lorsque l’élément a une adresse qui contient « San Francisco ». Lorsque l’élément n’a pas d’adresse contenant « San Francisco », la méthode `OnSelectTemplate` retourne le `DefaultTemplate`.

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="pin-events"></a>Épingler les événements

La classe `Pin` fournit deux événements :

- `MarkerClicked` est déclenché lorsque l’utilisateur clique ou appuie sur le code PIN.
- `InfoWindowClicked` est déclenché lors d’un clic ou d’un appui sur la fenêtre info.

Les gestionnaires d’événements peuvent être attachés à un code confidentiel dans le code :

```csharp
public class PinEvents: ContentPage
{
    public PinEvents()
    {
        // ...

        pin1.MarkerClicked += OnMarkerClickedAsync;
        pin1.InfoWindowClicked += OnInfoWindowClickedAsync;
    }

    async void OnMarkerClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
    }

    async void OnInfoWindowClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
    }
}
```

## <a name="related-links"></a>Liens connexes

- [Exemple Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Convertisseur personnalisé de carte](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Création d’un Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
