---
titre : « Xamarin.Forms CarouselView layout » Description : «par défaut, un CarouselView affichera ses éléments horizontalement. Toutefois, une orientation verticale est également possible.»
ms. Prod : xamarin ms. AssetID : fede0382-c972-4023-a4ea-fe5cadec91a6 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 01/28/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview-layout"></a>Xamarin.FormsDisposition CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)définit les propriétés suivantes qui contrôlent la disposition :

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de type `LinearItemsLayout` , spécifie la disposition à utiliser.
- `PeekAreaInsets`, de type [`Thickness`](xref:Xamarin.Forms.Thickness) , spécifie la proportion de rendre les éléments adjacents partiellement visibles par.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Par défaut, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche ses éléments dans une orientation horizontale. Un seul élément s’affiche à l’écran, avec des mouvements de balayage qui entraînent la navigation vers l’avant et vers l’arrière dans la collection d’éléments. Toutefois, une orientation verticale est également possible. Cela est dû au fait que la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) propriété est de type `LinearItemsLayout` , qui hérite de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe. La `ItemsLayout` classe définit les propriétés suivantes :

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de type [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) , spécifie la direction dans laquelle le [`CarouselView`](xref:Xamarin.Forms.CarouselView) s’étend à mesure que des éléments sont ajoutés.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de type [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , spécifie la manière dont les points d’alignement sont alignés avec les éléments.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de type [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , spécifie le comportement des points d’alignement lors du défilement.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données. Pour plus d’informations sur les points d’alignement, consultez [points d’ancrage](scrolling.md#snap-points) dans le Guide de [ Xamarin.Forms défilement CollectionView](scrolling.md) .

L' [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération définit les membres suivants :

- `Vertical`indique que le [`CarouselView`](xref:Xamarin.Forms.CarouselView) s’étend verticalement au fur et à mesure que des éléments sont ajoutés.
- `Horizontal`indique que le [`CarouselView`](xref:Xamarin.Forms.CarouselView) s’étend horizontalement au fur et à mesure que des éléments sont ajoutés.

La `LinearItemsLayout` classe hérite de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe et définit une `ItemSpacing` propriété, de type `double` , qui représente l’espace vide autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0. La `LinearItemsLayout` classe définit également `Vertical` des membres et statiques `Horizontal` . Ces membres peuvent être utilisés pour créer des listes verticales ou horizontales, respectivement. Vous `LinearItemsLayout` pouvez également créer un objet, en spécifiant un [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membre d’énumération en tant qu’argument.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)utilise les moteurs de disposition natifs pour effectuer la mise en page.

## <a name="horizontal-layout"></a>Disposition horizontale

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche ses éléments horizontalement. Par conséquent, il n’est pas nécessaire de définir la [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propriété pour utiliser cette disposition :

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

Cette disposition peut également être obtenue en affectant [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) à la propriété un `LinearItemsLayout` objet, en spécifiant le membre de l' `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération en tant que valeur de la `Orientation` propriété :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

Cela aboutit à une mise en page qui augmente horizontalement au fur et à mesure que de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition horizontale CarouselView, sur iOS et Android](layout-images/horizontal.png "Disposition horizontale CarouselView")](layout-images/horizontal-large.png#lightbox "Disposition horizontale CarouselView")

## <a name="vertical-layout"></a>Disposition verticale

[`CarouselView`](xref:Xamarin.Forms.CarouselView)peut afficher ses éléments verticalement en affectant [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) à la propriété un `LinearItemsLayout` objet, en spécifiant le membre de l' `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération comme valeur de la `Orientation` propriété :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CarouselView.ItemsLayout>
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
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

Cela aboutit à une mise en page qui se développe verticalement au fur et à mesure que de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition verticale CarouselView, sur iOS et Android](layout-images/vertical.png "Disposition verticale CarouselView")](layout-images/vertical-large.png#lightbox "Disposition verticale CarouselView")

## <a name="partially-visible-adjacent-items"></a>Éléments adjacents partiellement visibles

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche des éléments complets à la fois. Toutefois, ce comportement peut être modifié en affectant `PeekAreaInsets` à la propriété une `Thickness` valeur qui spécifie la proportion de rendre les éléments adjacents partiellement visibles par. Cela peut être utile pour indiquer aux utilisateurs qu’il existe des éléments supplémentaires à afficher. Le code XAML suivant montre un exemple de définition de cette propriété :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    PeekAreaInsets = new Thickness(100)
};
```

Le résultat est que les éléments adjacents sont partiellement exposés à l’écran :

[![Capture d’écran d’un CollectionView avec des éléments adjacents partiellement visibles, sur iOS et Android](layout-images/peek-items.png "Indéfinis de zone de lecture CarouselView")](layout-images/peek-items-large.png#lightbox "Indéfinis de la zone de pic CarouselView")

## <a name="item-spacing"></a>Espacement d’éléments

Par défaut, il n’y a pas d’espace entre chaque élément dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Ce comportement peut être modifié en définissant la `ItemSpacing` propriété sur la disposition des éléments utilisée par le `CarouselView` .

Quand un [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit sa [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propriété sur un `LinearItemsLayout` objet, la `LinearItemsLayout.ItemSpacing` propriété peut être définie sur une `double` valeur qui représente l’espace entre les éléments :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

> [!NOTE]
> La `LinearItemsLayout.ItemSpacing` propriété a un jeu de rappels de validation, ce qui garantit que la valeur de la propriété est toujours supérieure ou égale à 0.

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Ce code donne une disposition verticale, dont l’espacement est de 20 entre les éléments.

## <a name="dynamic-resizing-of-items"></a>Redimensionnement dynamique des éléments

Les éléments d’un [`CarouselView`](xref:Xamarin.Forms.CarouselView) peuvent être redimensionnés dynamiquement au moment de l’exécution en modifiant les propriétés relatives à la disposition des éléments dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Par exemple, l’exemple de code suivant modifie [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) les [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriétés et d’un [`Image`](xref:Xamarin.Forms.Image) objet, et la `HeightRequest` propriété de son parent [`Frame`](xref:Xamarin.Forms.Frame) :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

Le `OnImageTapped` Gestionnaire d’événements est exécuté en réponse à un [`Image`](xref:Xamarin.Forms.Image) objet qui est taraudé, et modifie les dimensions de l’image (et de son parent `Frame` ), afin qu’elle soit plus facilement visible :

[![Capture d’écran d’un CarouselView avec le dimensionnement d’un élément dynamique sur iOS et Android](layout-images/runtime-resizing.png "Dimensionnement de l’élément dynamique CarouselView")](layout-images/runtime-resizing-large.png#lightbox "Dimensionnement de l’élément dynamique CarouselView")

## <a name="right-to-left-layout"></a>Disposition de droite à gauche

[`CarouselView`](xref:Xamarin.Forms.CarouselView)peut mettre en forme son contenu dans un sens de déroulement de droite à gauche en affectant à sa propriété la valeur [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) . Toutefois, la `FlowDirection` propriété doit idéalement être définie sur une page ou une disposition racine, ce qui entraîne la réponse de tous les éléments de la page, ou de la disposition racine, au sens du déroulement :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CarouselViewDemos.Views.HorizontalTemplateLayoutRTLPage"
             Title="Horizontal layout (RTL FlowDirection)"
             FlowDirection="RightToLeft">    
    <CarouselView ItemsSource="{Binding Monkeys}">
        ...
    </CarouselView>
</ContentPage>
```

La valeur par défaut [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) d’un élément avec un parent est [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . Par conséquent, le [`CarouselView`](xref:Xamarin.Forms.CarouselView) hérite `FlowDirection` de la valeur de la propriété de [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.FormsDéfilement CarouselView](scrolling.md)
