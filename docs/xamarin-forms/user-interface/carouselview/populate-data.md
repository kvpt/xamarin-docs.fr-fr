---
title: Xamarin.FormsDonnées CarouselView
description: Un CarouselView est rempli avec des données en affectant à sa propriété ItemsSource une collection qui implémente IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/29/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8898120ae234c13c6650a7c818796fd25fa1101
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929111"
---
# <a name="xamarinforms-carouselview-data"></a>Xamarin.FormsDonnées CarouselView

![API de la version préliminaire](~/media/shared/preview.png "Cette API est actuellement en préversion.")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)comprend les propriétés suivantes qui définissent les données à afficher et leur apparence :

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de type `IEnumerable` , spécifie la collection d’éléments à afficher et a une valeur par défaut de `null` .
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , spécifie le modèle à appliquer à chaque élément de la collection d’éléments à afficher.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)définit une `ItemsUpdatingScrollMode` propriété qui représente le comportement de défilement de `CarouselView` lorsque de nouveaux éléments y sont ajoutés. Pour plus d’informations sur cette propriété, consultez [contrôler la position de défilement quand de nouveaux éléments sont ajoutés](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView)prend en charge la virtualisation incrémentielle des données au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Remplir un CarouselView avec des données

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) est rempli avec des données en affectant [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) à sa propriété une collection qui implémente `IEnumerable` . Les éléments peuvent être ajoutés en XAML en initialisant la `ItemsSource` propriété à partir d’un tableau de chaînes :

```xaml
<CarouselView>
    <CarouselView.ItemsSource>
        <x:Array Type="{x:Type x:String}">
            <x:String>Baboon</x:String>
            <x:String>Capuchin Monkey</x:String>
            <x:String>Blue Monkey</x:String>
            <x:String>Squirrel Monkey</x:String>
            <x:String>Golden Lion Tamarin</x:String>
            <x:String>Howler Monkey</x:String>
            <x:String>Japanese Macaque</x:String>
        </x:Array>
    </CarouselView.ItemsSource>
</CarouselView>
```

> [!NOTE]
> Notez que l’élément `x:Array` nécessite un attribut `Type` qui indique le type des éléments du tableau.

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!IMPORTANT]
> Si [`CarouselView`](xref:Xamarin.Forms.CarouselView) est requis pour l’actualisation à mesure que des éléments sont ajoutés, supprimés ou modifiés dans la collection sous-jacente, la collection sous-jacente doit être une `IEnumerable` collection qui envoie des notifications de modification de propriété, telles que `ObservableCollection` .

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche les éléments horizontalement. Les captures d’écran suivantes montrent un `CarouselView` affichage de différents éléments de chaîne sur iOS et Android :

[![Capture d’écran de CarouselView contenant des éléments de texte, sur iOS et Android](populate-data-images/text.png "Éléments de texte dans un CarouselView")](populate-data-images/text-large.png#lightbox "Éléments de texte dans un CarouselView")

Pour plus d’informations sur la modification de l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) orientation, consultez [ Xamarin.Forms disposition CarouselView](layout.md). Pour plus d’informations sur la façon de définir l’apparence de chaque élément dans `CarouselView` , consultez [définir l’apparence des éléments](#define-item-appearance).

### <a name="data-binding"></a>Liaison de données

[`CarouselView`](xref:Xamarin.Forms.CarouselView)peut être rempli de données à l’aide d’une liaison de données pour lier sa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété à une `IEnumerable` collection. En XAML, cela est accompli avec l' `Binding` extension de balisage :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Dans cet exemple, les [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) données de propriété sont liées à la `Monkeys` propriété du ViewModel connecté.

> [!NOTE]
> Les liaisons compilées peuvent être activées pour améliorer les performances de liaison de données dans les Xamarin.Forms applications. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Pour plus d’informations sur la liaison de données, consultez [ Xamarin.Forms liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être définie en affectant à la propriété la valeur [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    Label nameLabel = new Label { ... };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Image image = new Image { ... };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label locationLabel = new Label { ... };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Label detailsLabel = new Label { ... };
    detailsLabel.SetBinding(Label.TextProperty, "Details");

    StackLayout stackLayout = new StackLayout
    {
        Children = { nameLabel, image, locationLabel, detailsLabel }
    };

    Frame frame = new Frame { ... };
    StackLayout rootStackLayout = new StackLayout
    {
        Children = { frame }
    };

    return rootStackLayout;
});
```

Les éléments spécifiés dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque élément dans `CarouselView` . Dans l’exemple, la disposition dans le `DataTemplate` est gérée par un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , et les données sont affichées avec un [`Image`](xref:Xamarin.Forms.Image) objet, et trois [`Label`](xref:Xamarin.Forms.Label) objets, qui sont tous liés aux propriétés de la `Monkey` classe :

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Les captures d’écran suivantes montrent le résultat de la création de modèles pour chaque élément :

[![Capture d’écran de CarouselView où chaque élément est basé sur un modèle, sur iOS et Android](populate-data-images/datatemplate.png "Éléments basés sur un modèle dans un CarouselView")](populate-data-images/datatemplate-large.png#lightbox "Éléments basés sur un modèle dans un CarouselView")

Pour plus d’informations sur les modèles de données, consultez [ Xamarin.Forms modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) à la propriété un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objet :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls"
             x:Class="CarouselViewDemos.Views.HorizontalLayoutDataTemplateSelectorPage">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CarouselView ItemsSource="{Binding Monkeys}"
                  ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriété est définie sur un `MonkeyDataTemplateSelector` objet. L’exemple suivant illustre la `MonkeyDataTemplateSelector` classe :

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

La `MonkeyDataTemplateSelector` classe définit `AmericanMonkey` et les `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriétés qui sont définies sur des modèles de données différents. La `OnSelectTemplate` substitution retourne le `AmericanMonkey` modèle lorsque le nom du singe contient « America ». Lorsque le nom du singe ne contient pas « America », le `OnSelectTemplate` remplacement retourne le `OtherMonkey` modèle, qui affiche ses données grisées :

[![Capture d’écran de la sélection du modèle d’élément d’exécution CarouselView, sur iOS et Android](populate-data-images/datatemplateselector.png "Sélection du modèle d’élément d’exécution dans un CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Sélection du modèle d’élément d’exécution dans un CarouselView")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Lorsque [`CarouselView`](xref:Xamarin.Forms.CarouselView) vous utilisez, ne définissez jamais l’élément racine de vos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets sur un `ViewCell` . Cela entraînera la levée d’une exception, car n' `CarouselView` a pas de concept de cellules.

## <a name="display-indicators"></a>Indicateurs d’affichage

Les indicateurs, qui représentent le nombre d’éléments et la position actuelle dans un `CarouselView` , peuvent être affichés en regard de `CarouselView` . Pour ce faire, vous pouvez utiliser le `IndicatorView` contrôle :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

Dans cet exemple, le `IndicatorView` est rendu sous le `CarouselView` , avec un indicateur pour chaque élément dans `CarouselView` . Le `IndicatorView` est rempli avec les données en affectant `CarouselView.IndicatorView` à la propriété l' `IndicatorView` objet. Chaque indicateur est un cercle gris clair, tandis que l’indicateur qui représente l’élément actuel dans la `CarouselView` est gris foncé :

[![Capture d’écran d’un CarouselView et d’un IndicatorView, sur iOS et Android](populate-data-images/indicators.png "Cercles IndicatorView")](populate-data-images/indicators-large.png#lightbox "Cercles IndicatorView")

> [!IMPORTANT]
> La définition de la propriété entraîne la liaison de la propriété `CarouselView.IndicatorView` `IndicatorView.Position` à la propriété `CarouselView.Position` et la `IndicatorView.ItemsSource` liaison de la propriété à la `CarouselView.ItemsSource` propriété.

Pour plus d’informations sur les indicateurs, consultez [ Xamarin.Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md).

## <a name="context-menus"></a>Menu contextuels

[`CarouselView`](xref:Xamarin.Forms.CarouselView)prend en charge les menus contextuels pour les éléments de données via `SwipeView` , qui révèle le menu contextuel avec un mouvement de balayage. Le `SwipeView` est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel pour cet élément de contenu. Par conséquent, les menus contextuels sont implémentés pour un `CarouselView` en créant un `SwipeView` qui définit le contenu que le `SwipeView` encapsule et les éléments de menu contextuel qui sont révélés par le mouvement de balayage. Pour cela, `SwipeView` vous devez ajouter un au [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence de chaque élément de données dans le `CarouselView` :

```xaml
<CarouselView x:Name="carouselView"
              ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                    <Frame HasShadow="True"
                           BorderColor="DarkGray"
                           CornerRadius="5"
                           Margin="20"
                           HeightRequest="300"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand">
                        <SwipeView>
                            <SwipeView.TopItems>
                                <SwipeItems>
                                    <SwipeItem Text="Favorite"
                                               IconImageSource="favorite.png"
                                               BackgroundColor="LightGreen"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.FavoriteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.TopItems>
                            <SwipeView.BottomItems>
                                <SwipeItems>
                                    <SwipeItem Text="Delete"
                                               IconImageSource="delete.png"
                                               BackgroundColor="LightPink"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.DeleteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.BottomItems>
                            <StackLayout>
                                <!-- Define item appearance -->
                            </StackLayout>
                        </SwipeView>
                    </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    StackLayout stackLayout = new StackLayout();
    Frame frame = new Frame { ... };

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: carouselView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: carouselView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.TopItems = new SwipeItems { favoriteSwipeItem };
    swipeView.BottomItems = new SwipeItems { deleteSwipeItem };

    StackLayout swipeViewStackLayout = new StackLayout { ... };
    swipeView.Content = swipeViewStackLayout;
    frame.Content = swipeView;
    stackLayout.Children.Add(frame);

    return stackLayout;
});
```

Dans cet exemple, le `SwipeView` contenu est un [`StackLayout`](xref:Xamarin.Forms.StackLayout) qui définit l’apparence de chaque élément entouré par un [`Frame`](xref:Xamarin.Forms.Frame) dans le [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Les éléments de balayage sont utilisés pour effectuer des actions sur le `SwipeView` contenu et sont révélés lorsque le contrôle est balayé à partir du haut et du bas :

[![Capture d’écran de l’élément de menu contextuel CarouselView Bottom, sur iOS et Android](populate-data-images/swipeview-bottom.png "CarouselView avec l’élément de menu contextuel SwipeView inférieur")](populate-data-images/swipeview-bottom-large.png#lightbox "CarouselView avec l’élément de menu contextuel SwipeView inférieur") 
 [ ![Capture d’écran de l’élément de menu supérieur CarouselView, sur iOS et Android](populate-data-images/swipeview-top.png "CarouselView avec l’élément de menu contextuel SwipeView Top")](populate-data-images/swipeview-top-large.png#lightbox "CarouselView avec l’élément de menu contextuel SwipeView Top")

`SwipeView`prend en charge quatre directions de balayage différentes, la direction de balayage étant définie par la `SwipeItems` collection directionnelle à laquelle les `SwipeItems` objets sont ajoutés. Par défaut, un élément balayer est exécuté lorsqu’il est appuyé par l’utilisateur. En outre, une fois qu’un élément balayer a été exécuté, les éléments de balayage sont masqués et le `SwipeView` contenu est à nouveau affiché. Toutefois, ces comportements peuvent être modifiés.

Pour plus d’informations sur le `SwipeView` contrôle, consultez [ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Tirer pour actualiser

[`CarouselView`](xref:Xamarin.Forms.CarouselView)prend en charge la fonctionnalité d’actualisation par extraction via le `RefreshView` , qui permet l’actualisation des données affichées en extrayant les éléments. Le `RefreshView` est un contrôle conteneur qui fournit la fonctionnalité d’actualisation par extraction à son enfant, à condition que l’enfant prenne en charge le contenu défilant. Par conséquent, l’extraction vers l’actualisation est implémentée pour un `CarouselView` en le définissant comme enfant d’un `RefreshView` :

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CarouselView ItemsSource="{Binding Animals}">
        ...
    </CarouselView>
</RefreshView>
```

Le code C# équivalent est :

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = carouselView;
// ...
```

Lorsque l’utilisateur lance une actualisation, le `ICommand` défini par la `Command` propriété est exécuté, ce qui doit actualiser les éléments affichés. Une visualisation d’actualisation s’affiche pendant l’actualisation, qui se compose d’un cercle de progression animé :

[![Capture d’écran de l’extraction CarouselView pour iOS et Android](populate-data-images/pull-to-refresh.png "CarouselView extraction à l’actualisation")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView extraction à l’actualisation")

La valeur de la `RefreshView.IsRefreshing` propriété indique l’état actuel de `RefreshView` . Lorsqu’une actualisation est déclenchée par l’utilisateur, cette propriété effectue automatiquement la transition vers `true` . Une fois l’actualisation terminée, vous devez réinitialiser la propriété à `false` .

Pour plus d’informations sur `RefreshView` , consultez [ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Charger les données de façon incrémentielle

[`CarouselView`](xref:Xamarin.Forms.CarouselView)prend en charge la virtualisation incrémentielle des données au fur et à mesure que l’utilisateur fait défiler. Cela permet des scénarios tels que le chargement asynchrone d’une page de données à partir d’un service Web, lorsque l’utilisateur fait défiler. En outre, le point auquel des données supplémentaires sont chargées est configurable afin que les utilisateurs ne voient pas d’espace vide ou ne soient plus défilant.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)définit les propriétés suivantes pour contrôler le chargement incrémentiel des données :

- `RemainingItemsThreshold`, de type `int` , le seuil d’éléments qui ne sont pas encore visibles dans la liste à laquelle l' `RemainingItemsThresholdReached` événement est déclenché.
- `RemainingItemsThresholdReachedCommand`, de type `ICommand` , qui est exécuté lorsque `RemainingItemsThreshold` est atteint.
- `RemainingItemsThresholdReachedCommandParameter`, de type `object` : paramètre passé à la commande `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)définit également un `RemainingItemsThresholdReached` événement qui est déclenché lorsque le `CarouselView` est défilé suffisamment loin pour que les `RemainingItemsThreshold` éléments n’aient pas été affichés. Cet événement peut être géré pour charger plus d’éléments. En outre, lorsque l' `RemainingItemsThresholdReached` événement est déclenché, le `RemainingItemsThresholdReachedCommand` est exécuté, ce qui permet le chargement incrémentiel des données dans un ViewModel.

La valeur par défaut de la `RemainingItemsThreshold` propriété est-1, ce qui indique que l' `RemainingItemsThresholdReached` événement ne sera jamais déclenché. Lorsque la valeur de la propriété est 0, l' `RemainingItemsThresholdReached` événement est déclenché lorsque l’élément final du [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est affiché. Pour les valeurs supérieures à 0, l' `RemainingItemsThresholdReached` événement est déclenché quand le `ItemsSource` contient ce nombre d’éléments qui n’ont pas encore fait l’objet d’un défilement.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)valide la `RemainingItemsThreshold` propriété afin que sa valeur soit toujours supérieure ou égale à-1.

L’exemple de code XAML suivant montre un [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui charge des données de façon incrémentielle :

```xaml
<CarouselView ItemsSource="{Binding Animals}"
              RemainingItemsThreshold="2"
              RemainingItemsThresholdReached="OnCarouselViewRemainingItemsThresholdReached"
              RemainingItemsThresholdReachedCommand="{Binding LoadMoreDataCommand}">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    RemainingItemsThreshold = 2
};
carouselView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

Dans cet exemple de code, l' `RemainingItemsThresholdReached` événement se déclenche lorsque 2 éléments n’ont pas encore fait l’objet d’un défilement, et en réponse exécute le `OnCollectionViewRemainingItemsThresholdReached` Gestionnaire d’événements :

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Les données peuvent également être chargées de façon incrémentielle en liant le `RemainingItemsThresholdReachedCommand` à une `ICommand` implémentation dans le ViewModel.

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.FormsIndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Xamarin.FormsRefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.FormsLiaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.FormsModèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Créer un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
