---
title: Données CollectionView Xamarin. Forms
description: Un CollectionView est rempli avec des données en affectant à sa propriété ItemsSource une collection qui implémente IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 9442f7878d9290946fabb7bfc5dee77a828228c7
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78913048"
---
# <a name="xamarinforms-collectionview-data"></a>Données CollectionView Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) comprend les propriétés suivantes qui définissent les données à afficher et leur apparence :

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de type `IEnumerable`, spécifie la collection d’éléments à afficher et a une valeur par défaut de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), spécifie le modèle à appliquer à chaque élément de la collection d’éléments à afficher.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit une propriété `ItemsUpdatingScrollMode` qui représente le comportement de défilement de la `CollectionView` quand de nouveaux éléments y sont ajoutés. Pour plus d’informations sur cette propriété, consultez [contrôler la position de défilement quand de nouveaux éléments sont ajoutés](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pouvez également charger des données de façon incrémentielle au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Remplir un CollectionView avec des données

Une [`CollectionView`](xref:Xamarin.Forms.CollectionView) est remplie avec des données en définissant sa propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sur n’importe quelle collection qui implémente `IEnumerable`. Les éléments peuvent être ajoutés en XAML en initialisant la propriété `ItemsSource` à partir d’un tableau de chaînes :

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> Notez que l’élément `x:Array` nécessite un attribut `Type` qui indique le type des éléments du tableau.

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
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

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) lèvera une exception si son [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est mis à jour à partir du thread d’interface utilisateur.

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche les éléments dans une liste verticale, comme illustré dans les captures d’écran suivantes :

[![Capture d’écran de CollectionView contenant des éléments de texte, sur iOS et Android](populate-data-images/text.png "Éléments de texte dans un CollectionView")](populate-data-images/text-large.png#lightbox "Éléments de texte dans un CollectionView")

> [!IMPORTANT]
> Si la [`CollectionView`](xref:Xamarin.Forms.CollectionView) est requise pour l’actualisation à mesure que des éléments sont ajoutés, supprimés ou modifiés dans la collection sous-jacente, la collection sous-jacente doit être une collection `IEnumerable` qui envoie des notifications de modification de propriété, telles que `ObservableCollection`.

Pour plus d’informations sur la modification de la disposition de [`CollectionView`](xref:Xamarin.Forms.CollectionView) , consultez [disposition du CollectionView Xamarin. Forms](layout.md). Pour plus d’informations sur la façon de définir l’apparence de chaque élément dans le `CollectionView`, consultez [définir l’apparence des éléments](#define-item-appearance).

### <a name="data-binding"></a>Liaison de données

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pouvez remplir des données à l’aide de la liaison de données pour lier sa propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) à une collection `IEnumerable`. En XAML, cela est accompli avec l’extension de balisage `Binding` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Dans cet exemple, les données de la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sont liées à la propriété `Monkeys` du ViewModel connecté.

> [!NOTE]
> Les liaisons compilées peuvent être activées pour améliorer les performances de liaison de données dans les applications Xamarin. Forms. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans l' [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être définie en affectant à la propriété [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Les éléments spécifiés dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque élément de la liste. Dans l’exemple, la disposition dans le `DataTemplate` est gérée par un [`Grid`](xref:Xamarin.Forms.Grid). L' `Grid` contient un objet [`Image`](xref:Xamarin.Forms.Image) , et deux objets [`Label`](xref:Xamarin.Forms.Label) , qui sont tous liés aux propriétés de la classe `Monkey` :

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

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans le [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant à la propriété [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) la valeur d’un objet [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

La propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) est définie sur un objet `MonkeyDataTemplateSelector`. L’exemple suivant illustre la classe `MonkeyDataTemplateSelector` :

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

La classe `MonkeyDataTemplateSelector` définit les propriétés de `AmericanMonkey` et de `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui sont définies sur des modèles de données différents. Le `OnSelectTemplate` remplacement retourne le modèle de `AmericanMonkey`, qui affiche le nom et l’emplacement du singe en bleu vert, lorsque le nom du singe contient « America ». Lorsque le nom du singe ne contient pas « America », le `OnSelectTemplate` remplacement retourne le `OtherMonkey` modèle, qui affiche le nom du singe et l’emplacement en argent :

[![Capture d’écran de la sélection du modèle d’élément d’exécution CollectionView, sur iOS et Android](populate-data-images/datatemplateselector.png "Sélection du modèle d’élément d’exécution dans un CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Sélection du modèle d’élément d’exécution dans un CollectionView")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Lorsque vous utilisez [`CollectionView`](xref:Xamarin.Forms.CollectionView), ne définissez jamais l’élément racine de vos objets [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) sur un `ViewCell`. Cela entraîne la levée d’une exception, car `CollectionView` n’a pas de concept de cellule.

## <a name="context-menus"></a>Menu contextuels

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge les menus contextuels pour les éléments de données via le `SwipeView`, ce qui révèle le menu contextuel avec un mouvement de balayage. Le `SwipeView` est un contrôle conteneur qui encapsule un élément de contenu et fournit des éléments de menu contextuel pour cet élément de contenu. Par conséquent, les menus contextuels sont implémentés pour un `CollectionView` en créant un `SwipeView` qui définit le contenu que le `SwipeView` encapsule, et les éléments de menu contextuel qui sont révélés par le mouvement de balayage. Pour ce faire, définissez la `SwipeView` comme vue racine dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence de chaque élément de données dans le `CollectionView`:

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

Dans cet exemple, le contenu `SwipeView` est un [`Grid`](xref:Xamarin.Forms.Grid) qui définit l’apparence de chaque élément dans le [`CollectionView`](xref:Xamarin.Forms.CollectionView). Les éléments de balayage sont utilisés pour effectuer des actions sur le contenu `SwipeView` et sont révélés lorsque le contrôle est extrait du côté gauche :

[![Capture d’écran des éléments du menu contextuel CollectionView, sur iOS et Android](populate-data-images/swipeview.png "CollectionView avec les éléments de menu contextuel SwipeView")](populate-data-images/swipeview-large.png#lightbox "CollectionView avec les éléments de menu contextuel SwipeView")

`SwipeView` prend en charge quatre directions de balayage différentes, la direction de balayage étant définie par la collection de `SwipeItems` directionnelles, les objets `SwipeItems` sont ajoutés. Par défaut, un élément balayer est exécuté lorsqu’il est appuyé par l’utilisateur. En outre, une fois qu’un élément balayer a été exécuté, les éléments de balayage sont masqués et le contenu `SwipeView` s’affiche à nouveau. Toutefois, ces comportements peuvent être modifiés.

Pour plus d’informations sur le contrôle `SwipeView`, consultez [Xamarin. Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Extraire pour actualiser

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge la fonctionnalité d’actualisation de l’extraction par le biais du `RefreshView`, ce qui permet d’afficher les données affichées pour les actualiser en faisant défiler la liste des éléments. Le `RefreshView` est un contrôle conteneur qui fournit des fonctionnalités pull pour actualiser à son enfant, à condition que l’enfant prenne en charge le contenu défilant. Par conséquent, l’extraction vers l’actualisation est implémentée pour une `CollectionView` en la définissant comme enfant d’un `RefreshView`:

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

Lorsque l’utilisateur lance une actualisation, la `ICommand` définie par la propriété `Command` est exécutée, ce qui doit actualiser les éléments affichés. Une visualisation d’actualisation s’affiche pendant l’actualisation, qui se compose d’un cercle de progression animé :

[![Capture d’écran de l’extraction CollectionView pour iOS et Android](populate-data-images/pull-to-refresh.png "CollectionView extraction à l’actualisation")](populate-data-images/pull-to-refresh-large.png#lightbox "CollectionView extraction à l’actualisation")

La valeur de la propriété `RefreshView.IsRefreshing` indique l’état actuel du `RefreshView`. Lorsqu’une actualisation est déclenchée par l’utilisateur, cette propriété effectue automatiquement la transition vers `true`. Une fois l’actualisation terminée, vous devez réinitialiser la propriété à `false`.

Pour plus d’informations sur `RefreshView`, consultez [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Charger les données de façon incrémentielle

[`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge le chargement incrémentiel des données à mesure que les utilisateurs parcourent les éléments. Cela permet des scénarios tels que le chargement asynchrone d’une page de données à partir d’un service Web, lorsque l’utilisateur fait défiler. En outre, le point auquel des données supplémentaires sont chargées est configurable afin que les utilisateurs ne voient pas d’espace vide ou ne soient plus défilant.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes pour contrôler le chargement incrémentiel des données :

- `RemainingItemsThreshold`, de type `int`, le seuil d’éléments qui ne sont pas encore visibles dans la liste à laquelle l’événement `RemainingItemsThresholdReached` sera déclenché.
- `RemainingItemsThresholdReachedCommand`, de type `ICommand`, qui est exécuté lorsque le `RemainingItemsThreshold` est atteint.
- `RemainingItemsThresholdReachedCommandParameter`, de type `object` : paramètre passé à la commande `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit également un événement `RemainingItemsThresholdReached` qui est déclenché lorsque le `CollectionView` défile suffisamment loin pour que les éléments de `RemainingItemsThreshold` n’aient pas été affichés. Cet événement peut être géré pour charger plus d’éléments. En outre, lorsque l’événement `RemainingItemsThresholdReached` est déclenché, le `RemainingItemsThresholdReachedCommand` est exécuté, ce qui permet le chargement incrémentiel des données dans un ViewModel.

La valeur par défaut de la propriété `RemainingItemsThreshold` est-1, ce qui indique que l’événement `RemainingItemsThresholdReached` ne sera jamais déclenché. Lorsque la valeur de la propriété est 0, l’événement `RemainingItemsThresholdReached` est déclenché lorsque l’élément final du [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) s’affiche. Pour les valeurs supérieures à 0, l’événement `RemainingItemsThresholdReached` est déclenché lorsque le `ItemsSource` contient ce nombre d’éléments qui n’ont pas encore fait l’objet d’un défilement.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) valide la propriété `RemainingItemsThreshold` afin que sa valeur soit toujours supérieure ou égale à-1.

L’exemple de code XAML suivant montre une [`CollectionView`](xref:Xamarin.Forms.CollectionView) qui charge des données de façon incrémentielle :

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

Dans cet exemple de code, l’événement `RemainingItemsThresholdReached` se déclenche quand 5 éléments n’ont pas encore fait l’objet d’un défilement, et en réponse exécute le gestionnaire d’événements `OnCollectionViewRemainingItemsThresholdReached` :

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Les données peuvent également être chargées de façon incrémentielle en liant le `RemainingItemsThresholdReachedCommand` à une implémentation de `ICommand` dans le ViewModel.

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin. Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Créer un DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
