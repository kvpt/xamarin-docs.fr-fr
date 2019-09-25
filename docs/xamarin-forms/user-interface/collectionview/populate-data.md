---
title: Données CollectionView Xamarin. Forms
description: Un CollectionView est rempli avec des données en affectant à sa propriété ItemsSource une collection qui implémente IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 6942baed6af2a2e9b2c713a8fe08cf4c8ed4416b
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "69888542"
---
# <a name="xamarinforms-collectionview-data"></a>Données CollectionView Xamarin. Forms

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit les propriétés suivantes qui définissent les données à afficher et leur apparence :

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de type `IEnumerable`, spécifie la collection d’éléments à afficher et a une valeur par défaut de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), spécifie le modèle à appliquer à chaque élément de la collection d’éléments à afficher.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)définit une `ItemsUpdatingScrollMode` propriété qui représente le comportement `CollectionView` de défilement de lorsque de nouveaux éléments y sont ajoutés. Pour plus d’informations sur cette propriété, consultez [contrôler la position de défilement quand de nouveaux éléments sont ajoutés](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)peut également charger des données de façon incrémentielle au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Remplir un CollectionView avec des données

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est rempli avec des données en affectant à sa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété `IEnumerable`une collection qui implémente. Les éléments peuvent être ajoutés en XAML en initialisant la `ItemsSource` propriété à partir d’un tableau de chaînes :

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

> [!IMPORTANT]
> Si est [`CollectionView`](xref:Xamarin.Forms.CollectionView) requis pour l’actualisation à mesure que des éléments sont ajoutés, supprimés ou modifiés dans la collection sous-jacente, la collection `IEnumerable` sous-jacente doit être une collection qui envoie `ObservableCollection`des notifications de modification de propriété, telles que.

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche les éléments dans une liste verticale, comme illustré dans les captures d’écran suivantes :

[![Capture d’écran de CollectionView contenant des éléments de texte, sur iOS et Android](populate-data-images/text.png "Éléments de texte dans un CollectionView")](populate-data-images/text-large.png#lightbox "Éléments de texte dans un CollectionView")

Pour plus d’informations sur la façon [`CollectionView`](xref:Xamarin.Forms.CollectionView) de modifier la disposition, consultez [spécifier une disposition](layout.md). Pour plus d’informations sur la façon de définir l’apparence de chaque `CollectionView`élément dans, consultez [définir l’apparence des éléments](#define-item-appearance).

### <a name="data-binding"></a>Liaison de données

[`CollectionView`](xref:Xamarin.Forms.CollectionView)peut être rempli de données à l’aide d’une liaison de [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) données pour lier `IEnumerable` sa propriété à une collection. En XAML, cela est accompli avec l' `Binding` extension de balisage :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Dans cet exemple, les [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) données de propriété sont liées à `Monkeys` la propriété du ViewModel connecté.

> [!NOTE]
> Les liaisons compilées peuvent être activées pour améliorer les performances de liaison de données dans les applications Xamarin. Forms. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être définie en affectant à [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)la propriété la valeur :

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

Les éléments spécifiés dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque élément de la liste. Dans l’exemple, la disposition dans `DataTemplate` le est gérée [`Grid`](xref:Xamarin.Forms.Grid)par un. Le `Grid` contient un [`Image`](xref:Xamarin.Forms.Image) objet, et deux [`Label`](xref:Xamarin.Forms.Label) objets, qui sont tous liés aux propriétés de `Monkey` la classe :

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

L’apparence de chaque élément dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant à la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriété un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) objet :

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

La [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propriété est définie sur un `MonkeyDataTemplateSelector` objet. L’exemple suivant illustre la `MonkeyDataTemplateSelector` classe:

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

La `MonkeyDataTemplateSelector` classe définit `AmericanMonkey` et `OtherMonkey` [lespropriétésquisontdéfiniessurdesmodèles`DataTemplate`](xref:Xamarin.Forms.DataTemplate) de données différents. La `OnSelectTemplate` substitution retourne le `AmericanMonkey` modèle, qui affiche le nom et l’emplacement du singe en bleu vert, lorsque le nom du singe contient « America ». Lorsque le nom du singe ne contient pas « America » `OnSelectTemplate` , le remplacement retourne `OtherMonkey` le modèle, qui affiche le nom du singe et l’emplacement en argent :

[![Capture d’écran de la sélection du modèle d’élément d’exécution CollectionView, sur iOS et Android](populate-data-images/datatemplateselector.png "Sélection du modèle d’élément d’exécution dans un CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Sélection du modèle d’élément d’exécution dans un CollectionView")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Lorsque vous [`CollectionView`](xref:Xamarin.Forms.CollectionView)utilisez, ne définissez jamais l’élément racine [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) de vos objets `ViewCell`sur un. Cela entraînera la levée d’une exception, `CollectionView` car n’a pas de concept de cellules.

## <a name="load-data-incrementally"></a>Charger les données de façon incrémentielle

[`CollectionView`](xref:Xamarin.Forms.CollectionView)prend en charge le chargement incrémentiel des données au fur et à mesure que les utilisateurs parcourent les éléments. Cela permet des scénarios tels que le chargement asynchrone d’une page de données à partir d’un service Web, lorsque l’utilisateur fait défiler. En outre, le point auquel des données supplémentaires sont chargées est configurable afin que les utilisateurs ne voient pas d’espace vide ou ne soient plus défilant.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit les propriétés suivantes pour contrôler le chargement incrémentiel des données :

- `RemainingItemsThreshold`, de type `int`, le seuil d’éléments qui ne sont pas encore visibles dans la liste `RemainingItemsThresholdReached` à laquelle l’événement est déclenché.
- `RemainingItemsThresholdReachedCommand`, de type `ICommand`, qui est exécuté `RemainingItemsThreshold` lorsque est atteint.
- `RemainingItemsThresholdReachedCommandParameter`, de type `object` : paramètre passé à la commande `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)définit également un `RemainingItemsThresholdReached` événement qui est déclenché lorsque le `CollectionView` est défilé suffisamment loin pour que `RemainingItemsThreshold` les éléments n’aient pas été affichés. Cet événement peut être géré pour charger plus d’éléments. En outre, lorsque l' `RemainingItemsThresholdReached` événement est déclenché, le `RemainingItemsThresholdReachedCommand` est exécuté, ce qui permet le chargement incrémentiel des données dans un ViewModel.

La valeur par défaut de `RemainingItemsThreshold` la propriété est-1, ce qui indique `RemainingItemsThresholdReached` que l’événement ne sera jamais déclenché. Lorsque la valeur de la propriété est 0 `RemainingItemsThresholdReached` , l’événement est déclenché lorsque l’élément [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) final du est affiché. Pour les valeurs supérieures à 0, `RemainingItemsThresholdReached` l’événement est déclenché quand le `ItemsSource` contient ce nombre d’éléments qui n’ont pas encore fait l’objet d’un défilement.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)valide la `RemainingItemsThreshold` propriété afin que sa valeur soit toujours supérieure ou égale à-1.

L’exemple de code XAML suivant [`CollectionView`](xref:Xamarin.Forms.CollectionView) montre un qui charge des données de façon incrémentielle :

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

Dans cet exemple de code, `RemainingItemsThresholdReached` l’événement se déclenche lorsque 5 éléments n’ont pas encore fait l’objet d’un défilement, et `OnCollectionViewRemainingItemsThresholdReached` en réponse exécute le gestionnaire d’événements :

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Les données peuvent également être chargées de façon incrémentielle `RemainingItemsThresholdReachedCommand` en liant `ICommand` le à une implémentation dans le ViewModel.

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Créer un DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
