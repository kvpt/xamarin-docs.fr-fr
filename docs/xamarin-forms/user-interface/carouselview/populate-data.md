---
title: Données CarouselView Xamarin. Forms
description: Un CarouselView est rempli avec des données en affectant à sa propriété ItemsSource une collection qui implémente IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2019
ms.openlocfilehash: 0ad31bc6f84ae633a9a18592a00670703db19df9
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697611"
---
# <a name="xamarinforms-carouselview-data"></a>Données CarouselView Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) comprend les propriétés suivantes qui définissent les données à afficher et leur apparence :

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), de type `IEnumerable`, spécifie la collection d’éléments à afficher et a une valeur par défaut de `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), spécifie le modèle à appliquer à chaque élément de la collection d’éléments à afficher.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit une propriété `ItemsUpdatingScrollMode` qui représente le comportement de défilement de la `CarouselView` quand de nouveaux éléments y sont ajoutés. Pour plus d’informations sur cette propriété, consultez [contrôler la position de défilement quand de nouveaux éléments sont ajoutés](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pouvez également charger des données de façon incrémentielle au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Remplir un CarouselView avec des données

Une [`CarouselView`](xref:Xamarin.Forms.CarouselView) est remplie avec des données en définissant sa propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sur n’importe quelle collection qui implémente `IEnumerable`. Les éléments peuvent être ajoutés en XAML en initialisant la propriété `ItemsSource` à partir d’un tableau de chaînes :

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
> Si la [`CarouselView`](xref:Xamarin.Forms.CarouselView) est requise pour l’actualisation à mesure que des éléments sont ajoutés, supprimés ou modifiés dans la collection sous-jacente, la collection sous-jacente doit être une collection `IEnumerable` qui envoie des notifications de modification de propriété, telles que `ObservableCollection`.

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche les éléments horizontalement. Les captures d’écran suivantes montrent un `CarouselView` affichant différents éléments de chaîne sur iOS et Android :

[![Capture d’écran de CarouselView contenant des éléments de texte, sur iOS et Android](populate-data-images/text.png "Éléments de texte dans un CarouselView")](populate-data-images/text-large.png#lightbox "Éléments de texte dans un CarouselView")

Pour plus d’informations sur la modification de l’orientation de [`CarouselView`](xref:Xamarin.Forms.CarouselView) , consultez [disposition de CarouselView Xamarin. Forms](layout.md). Pour plus d’informations sur la façon de définir l’apparence de chaque élément dans le `CarouselView`, consultez [définir l’apparence des éléments](#define-item-appearance).

### <a name="data-binding"></a>Liaison de données

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pouvez remplir des données à l’aide de la liaison de données pour lier sa propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) à une collection `IEnumerable`. En XAML, cela est accompli avec l’extension de balisage `Binding` :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Dans cet exemple, les données de la propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sont liées à la propriété `Monkeys` du ViewModel connecté.

> [!NOTE]
> Les liaisons compilées peuvent être activées pour améliorer les performances de liaison de données dans les applications Xamarin. Forms. Pour plus d’informations, consultez [Liaisons compilées](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Définir l’apparence des éléments

L’apparence de chaque élément dans l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être définie en affectant à la propriété [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Les éléments spécifiés dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque élément du `CarouselView`. Dans l’exemple, la disposition dans le `DataTemplate` est gérée par un [`StackLayout`](xref:Xamarin.Forms.StackLayout), et les données sont affichées avec un objet [`Image`](xref:Xamarin.Forms.Image) , et trois objets [`Label`](xref:Xamarin.Forms.Label) , qui sont tous liés aux propriétés de la classe `Monkey` :

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

Pour plus d’informations sur les modèles de données, consultez [Modèles de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Choisir l’apparence des éléments au moment de l’exécution

L’apparence de chaque élément dans le [`CarouselView`](xref:Xamarin.Forms.CarouselView) peut être choisie au moment de l’exécution, en fonction de la valeur de l’élément, en affectant à la propriété [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) la valeur d’un objet [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

La classe `MonkeyDataTemplateSelector` définit les propriétés de `AmericanMonkey` et de `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui sont définies sur des modèles de données différents. La substitution `OnSelectTemplate` retourne le modèle de `AmericanMonkey` lorsque le nom du singe contient « America ». Lorsque le nom du singe ne contient pas « America », le `OnSelectTemplate` remplacement retourne le modèle de `OtherMonkey`, qui affiche ses données grisées :

[![Capture d’écran de la sélection du modèle d’élément d’exécution CarouselView, sur iOS et Android](populate-data-images/datatemplateselector.png "Sélection du modèle d’élément d’exécution dans un CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Sélection du modèle d’élément d’exécution dans un CarouselView")

Pour plus d’informations sur les sélecteurs de modèle de données, consultez [Create a Xamarin. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Lorsque vous utilisez [`CarouselView`](xref:Xamarin.Forms.CarouselView), ne définissez jamais l’élément racine de vos objets [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) sur un `ViewCell`. Cela entraîne la levée d’une exception, car `CarouselView` n’a pas de concept de cellule.

## <a name="pull-to-refresh"></a>Extraire pour actualiser

[`CarouselView`](xref:Xamarin.Forms.CarouselView) prend en charge la fonctionnalité d’actualisation par extraction via le `RefreshView`, ce qui permet aux données affichées d’être actualisées en extrayant les éléments. Le `RefreshView` est un contrôle conteneur qui fournit des fonctionnalités pull pour actualiser à son enfant, à condition que l’enfant prenne en charge le contenu défilant. Par conséquent, l’extraction vers l’actualisation est implémentée pour une `CarouselView` en la définissant comme enfant d’un `RefreshView` :

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

Lorsque l’utilisateur lance une actualisation, la `ICommand` définie par la propriété `Command` est exécutée, ce qui doit actualiser les éléments affichés. Une visualisation d’actualisation s’affiche pendant l’actualisation, qui se compose d’un cercle de progression animé :

[![Capture d’écran de l’extraction CarouselView pour iOS et Android](populate-data-images/pull-to-refresh.png "CarouselView extraction à l’actualisation")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView extraction à l’actualisation")

La valeur de la propriété `RefreshView.IsRefreshing` indique l’état actuel du `RefreshView`. Lorsqu’une actualisation est déclenchée par l’utilisateur, cette propriété effectue automatiquement la transition vers `true`. Une fois l’actualisation terminée, vous devez réinitialiser la propriété à `false`.

Pour plus d’informations sur `RefreshView`, consultez [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Charger les données de façon incrémentielle

[`CarouselView`](xref:Xamarin.Forms.CarouselView) prend en charge le chargement incrémentiel des données à mesure que les utilisateurs parcourent les éléments. Cela permet des scénarios tels que le chargement asynchrone d’une page de données à partir d’un service Web, lorsque l’utilisateur fait défiler. En outre, le point auquel des données supplémentaires sont chargées est configurable afin que les utilisateurs ne voient pas d’espace vide ou ne soient plus défilant.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes pour contrôler le chargement incrémentiel des données :

- `RemainingItemsThreshold`, de type `int`, le seuil d’éléments qui ne sont pas encore visibles dans la liste à laquelle l’événement `RemainingItemsThresholdReached` sera déclenché.
- `RemainingItemsThresholdReachedCommand`, de type `ICommand`, qui est exécuté lorsque le `RemainingItemsThreshold` est atteint.
- `RemainingItemsThresholdReachedCommandParameter`, de type `object` : paramètre passé à la commande `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit également un événement `RemainingItemsThresholdReached` qui est déclenché lorsque le `CarouselView` défile suffisamment loin pour que les éléments de `RemainingItemsThreshold` n’aient pas été affichés. Cet événement peut être géré pour charger plus d’éléments. En outre, lorsque l’événement `RemainingItemsThresholdReached` est déclenché, le `RemainingItemsThresholdReachedCommand` est exécuté, ce qui permet le chargement incrémentiel des données dans un ViewModel.

La valeur par défaut de la propriété `RemainingItemsThreshold` est-1, ce qui indique que l’événement `RemainingItemsThresholdReached` ne sera jamais déclenché. Lorsque la valeur de la propriété est 0, l’événement `RemainingItemsThresholdReached` est déclenché lorsque l’élément final du [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) s’affiche. Pour les valeurs supérieures à 0, l’événement `RemainingItemsThresholdReached` est déclenché lorsque le `ItemsSource` contient ce nombre d’éléments qui n’ont pas encore fait l’objet d’un défilement.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) valide la propriété `RemainingItemsThreshold` afin que sa valeur soit toujours supérieure ou égale à-1.

L’exemple de code XAML suivant montre une [`CarouselView`](xref:Xamarin.Forms.CarouselView) qui charge des données de façon incrémentielle :

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

Dans cet exemple de code, l’événement `RemainingItemsThresholdReached` se déclenche lorsqu’il y a 2 éléments qui n’ont pas encore fait l’objet d’un défilement, et en réponse exécute le gestionnaire d’événements `OnCollectionViewRemainingItemsThresholdReached` :

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> Les données peuvent également être chargées de façon incrémentielle en liant le `RemainingItemsThresholdReachedCommand` à une implémentation de `ICommand` dans le ViewModel.

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modèles de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Créer un DataTemplateSelector Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
