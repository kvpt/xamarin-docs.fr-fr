---
title: Xamarin.Forms Données CollectionView
description: Un CollectionView est rempli avec des données en affectant à sa propriété ItemsSource une collection qui implémente IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 77f47af2ed2cce787cf0f66e524c2314ef4c9452
ms.sourcegitcommit: 1550019cd1e858d4d13a4ae6dfb4a5947702f24b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897499"
---
# <a name="no-locxamarinforms-collectionview-data"></a>Xamarin.Forms Données CollectionView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) comprend les propriétés suivantes qui définissent les données à afficher et leur apparence :

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de type `IEnumerable` , spécifie la collection d’éléments à afficher et a une valeur par défaut de `null` .
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , spécifie le modèle à appliquer à chaque élément de la collection d’éléments à afficher.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit une `ItemsUpdatingScrollMode` propriété qui représente le comportement de défilement de `CollectionView` lorsque de nouveaux éléments y sont ajoutés. Pour plus d’informations sur cette propriété, consultez [contrôler la position de défilement quand de nouveaux éléments sont ajoutés](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge la virtualisation incrémentielle des données au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Remplir un CollectionView avec des données

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est rempli avec des données en affectant [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) à sa propriété une collection qui implémente `IEnumerable` . Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche les éléments dans une liste verticale.

> [!IMPORTANT]
> Si [`CollectionView`](xref:Xamarin.Forms.CollectionView) est requis pour l’actualisation à mesure que des éléments sont ajoutés, supprimés ou modifiés dans la collection sous-jacente, la collection sous-jacente doit être une `IEnumerable` collection qui envoie des notifications de modification de propriété, telles que `ObservableCollection` .

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être rempli de données à l’aide d’une liaison de données pour lier sa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété à une `IEnumerable` collection. En XAML, cela est accompli avec l' `Binding` extension de balisage :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Dans cet exemple, les [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) données de propriété sont liées à la `Monkeys` propriété du ViewModel connecté.

> [!NOTE]
> Les liaisons compilées peuvent être activées pour améliorer les performances de liaison de données dans les Xamarin.Forms applications. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Pour plus d’informations sur la modification de la [`CollectionView`](xref:Xamarin.Forms.CollectionView) disposition, consultez [ Xamarin.Forms disposition CollectionView](layout.md). Pour plus d’informations sur la façon de définir l’apparence de chaque élément dans `CollectionView` , consultez [définir l’apparence des éléments](#define-item-appearance). Pour plus d’informations sur la liaison de données, consultez [ Xamarin.Forms liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) lèvera une exception si son [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est mis à jour à partir du thread d’interface utilisateur.

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être définie en affectant à la propriété la valeur [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

Les éléments spécifiés dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque élément de la liste. Dans l’exemple, la disposition dans le `DataTemplate` est gérée par un [`Grid`](xref:Xamarin.Forms.Grid) . Le `Grid` contient un [`Image`](xref:Xamarin.Forms.Image) objet, et deux [`Label`](xref:Xamarin.Forms.Label) objets, qui sont tous liés aux propriétés de la `Monkey` classe :

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Les captures d’écran suivantes montrent le résultat de la création de modèles pour chaque élément de la liste :

[![Capture d’écran de CollectionView où chaque élément est basé sur un modèle, sur iOS et Android](populate-data-images/datatemplate.png "Éléments basés sur un modèle dans un CollectionView")](populate-data-images/datatemplate-large.png#lightbox "Éléments basés sur un modèle dans un CollectionView")

Pour plus d’informations sur les modèles de données, consultez [ Xamarin.Forms modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) à la propriété un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objet :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
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

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
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

La `MonkeyDataTemplateSelector` classe définit `AmericanMonkey` et les `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) propriétés qui sont définies sur des modèles de données différents. La `OnSelectTemplate` substitution retourne le `AmericanMonkey` modèle, qui affiche le nom et l’emplacement du singe en bleu vert, lorsque le nom du singe contient « America ». Lorsque le nom du singe ne contient pas « America », le `OnSelectTemplate` remplacement retourne le `OtherMonkey` modèle, qui affiche le nom du singe et l’emplacement en argent :

[![Capture d’écran de la sélection du modèle d’élément d’exécution CollectionView, sur iOS et Android](populate-data-images/datatemplateselector.png "Sélection du modèle d’élément d’exécution dans un CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Sélection du modèle d’élément d’exécution dans un CollectionView")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Lorsque [`CollectionView`](xref:Xamarin.Forms.CollectionView) vous utilisez, ne définissez jamais l’élément racine de vos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets sur un `ViewCell` . Cela entraînera la levée d’une exception, car n' `CollectionView` a pas de concept de cellules.

## <a name="context-menus"></a>Menu contextuels

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge les menus contextuels pour les éléments de données via `SwipeView` , qui révèle le menu contextuel avec un mouvement de balayage. Le `SwipeView` est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel pour cet élément de contenu. Par conséquent, les menus contextuels sont implémentés pour un `CollectionView` en créant un `SwipeView` qui définit le contenu que le `SwipeView` encapsule et les éléments de menu contextuel qui sont révélés par le mouvement de balayage. Pour cela, vous devez définir `SwipeView` en tant que vue racine dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence de chaque élément de données dans le `CollectionView` :

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <SwipeView>
                <SwipeView.LeftItems>
                    <SwipeItems>
                        <SwipeItem Text="Favorite"
                                   IconImageSource="favorite.png"
                                   BackgroundColor="LightGreen"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.FavoriteCommand}"
                                   CommandParameter="{Binding}" />
                        <SwipeItem Text="Delete"
                                   IconImageSource="delete.png"
                                   BackgroundColor="LightPink"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.DeleteCommand}"
                                   CommandParameter="{Binding}" />
                    </SwipeItems>
                </SwipeView.LeftItems>
                <Grid BackgroundColor="White"
                      Padding="10">
                    <!-- Define item appearance -->
                </Grid>
            </SwipeView>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    // Define item appearance
    Grid grid = new Grid { Padding = 10, BackgroundColor = Color.White };
    // ...

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: collectionView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: collectionView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.LeftItems = new SwipeItems { favoriteSwipeItem, deleteSwipeItem };
    swipeView.Content = grid;    
    return swipeView;
});
```

Dans cet exemple, le `SwipeView` contenu est un [`Grid`](xref:Xamarin.Forms.Grid) qui définit l’apparence de chaque élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Les éléments de balayage sont utilisés pour effectuer des actions sur le `SwipeView` contenu et sont révélés lorsque le contrôle est extrait du côté gauche :

[![Capture d’écran des éléments du menu contextuel CollectionView, sur iOS et Android](populate-data-images/swipeview.png "CollectionView avec les éléments de menu contextuel SwipeView")](populate-data-images/swipeview-large.png#lightbox "CollectionView avec les éléments de menu contextuel SwipeView")

`SwipeView` prend en charge quatre directions de balayage différentes, la direction de balayage étant définie par la `SwipeItems` collection directionnelle à laquelle les `SwipeItems` objets sont ajoutés. Par défaut, un élément balayer est exécuté lorsqu’il est appuyé par l’utilisateur. En outre, une fois qu’un élément balayer a été exécuté, les éléments de balayage sont masqués et le `SwipeView` contenu est à nouveau affiché. Toutefois, ces comportements peuvent être modifiés.

Pour plus d’informations sur le `SwipeView` contrôle, consultez [ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Tirer pour actualiser

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge la fonctionnalité d’actualisation par extraction via le `RefreshView` , qui permet l’actualisation des données affichées en extrayant la liste des éléments. Le `RefreshView` est un contrôle conteneur qui fournit la fonctionnalité d’actualisation par extraction à son enfant, à condition que l’enfant prenne en charge le contenu défilant. Par conséquent, l’extraction vers l’actualisation est implémentée pour un `CollectionView` en le définissant comme enfant d’un `RefreshView` :

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CollectionView ItemsSource="{Binding Animals}">
        ...
    </CollectionView>
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

CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = collectionView;
// ...
```

Lorsque l’utilisateur lance une actualisation, le `ICommand` défini par la `Command` propriété est exécuté, ce qui doit actualiser les éléments affichés. Une visualisation d’actualisation s’affiche pendant l’actualisation, qui se compose d’un cercle de progression animé :

[![Capture d’écran de l’extraction CollectionView pour iOS et Android](populate-data-images/pull-to-refresh.png "CollectionView extraction à l’actualisation")](populate-data-images/pull-to-refresh-large.png#lightbox "CollectionView extraction à l’actualisation")

La valeur de la `RefreshView.IsRefreshing` propriété indique l’état actuel de `RefreshView` . Lorsqu’une actualisation est déclenchée par l’utilisateur, cette propriété effectue automatiquement la transition vers `true` . Une fois l’actualisation terminée, vous devez réinitialiser la propriété à `false` .

Pour plus d’informations sur `RefreshView` , consultez [ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Charger les données de façon incrémentielle

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge la virtualisation incrémentielle des données au fur et à mesure que l’utilisateur fait défiler. Cela permet des scénarios tels que le chargement asynchrone d’une page de données à partir d’un service Web, lorsque l’utilisateur fait défiler. En outre, le point auquel des données supplémentaires sont chargées est configurable afin que les utilisateurs ne voient pas d’espace vide ou ne soient plus défilant.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes pour contrôler le chargement incrémentiel des données :

- `RemainingItemsThreshold`, de type `int` , le seuil d’éléments qui ne sont pas encore visibles dans la liste à laquelle l' `RemainingItemsThresholdReached` événement est déclenché.
- `RemainingItemsThresholdReachedCommand`, de type `ICommand` , qui est exécuté lorsque `RemainingItemsThreshold` est atteint.
- `RemainingItemsThresholdReachedCommandParameter`, de type `object` : paramètre passé à la commande `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit également un `RemainingItemsThresholdReached` événement qui est déclenché lorsque le `CollectionView` est défilé suffisamment loin pour que les `RemainingItemsThreshold` éléments n’aient pas été affichés. Cet événement peut être géré pour charger plus d’éléments. En outre, lorsque l' `RemainingItemsThresholdReached` événement est déclenché, le `RemainingItemsThresholdReachedCommand` est exécuté, ce qui permet le chargement incrémentiel des données dans un ViewModel.

La valeur par défaut de la `RemainingItemsThreshold` propriété est-1, ce qui indique que l' `RemainingItemsThresholdReached` événement ne sera jamais déclenché. Lorsque la valeur de la propriété est 0, l' `RemainingItemsThresholdReached` événement est déclenché lorsque l’élément final du [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est affiché. Pour les valeurs supérieures à 0, l' `RemainingItemsThresholdReached` événement est déclenché quand le `ItemsSource` contient ce nombre d’éléments qui n’ont pas encore fait l’objet d’un défilement.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) valide la `RemainingItemsThreshold` propriété afin que sa valeur soit toujours supérieure ou égale à-1.

L’exemple de code XAML suivant montre un [`CollectionView`](xref:Xamarin.Forms.CollectionView) qui charge des données de façon incrémentielle :

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

Dans cet exemple de code, l' `RemainingItemsThresholdReached` événement se déclenche lorsque 5 éléments n’ont pas encore fait l’objet d’un défilement, et en réponse exécute le `OnCollectionViewRemainingItemsThresholdReached` Gestionnaire d’événements :

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Les données peuvent également être chargées de façon incrémentielle en liant le `RemainingItemsThresholdReachedCommand` à une `ICommand` implémentation dans le ViewModel.

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Xamarin.Forms Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms Modèles de données](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Créer un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
