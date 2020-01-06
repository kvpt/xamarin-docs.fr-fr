---
title: Disposition Xamarin. Forms CarouselView
description: Par défaut, un CarouselView affiche ses éléments horizontalement. Toutefois, une orientation verticale est également possible.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: 0149a66fedd98a94f1c9d96bf8e7e57715d1b90b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488255"
---
# <a name="xamarinforms-carouselview-layout"></a>Disposition Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes qui contrôlent la disposition :

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de type `LinearItemsLayout`, spécifie la disposition à utiliser.
- `PeekAreaInsets`, de type [`Thickness`](xref:Xamarin.Forms.Thickness), spécifie le nombre de fois que les éléments adjacents sont partiellement visibles par.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Par défaut, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) affichera ses éléments dans une orientation horizontale. Un seul élément s’affiche à l’écran, avec des mouvements de balayage qui entraînent la navigation vers l’avant et vers l’arrière dans la collection d’éléments. Toutefois, une orientation verticale est également possible. Cela est dû au fait que la propriété [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) est de type `LinearItemsLayout`, qui hérite de la classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) . La classe `ItemsLayout` définit les propriétés suivantes :

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de type [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), spécifie la direction dans laquelle le [`CarouselView`](xref:Xamarin.Forms.CarouselView) se développe à mesure que des éléments sont ajoutés.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de type [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), spécifie la manière dont les points d’ancrage sont alignés avec les éléments.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de type [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), spécifie le comportement des points d’alignement lors du défilement.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les propriétés peuvent être des cibles de liaisons de données. Pour plus d’informations sur les points d’alignement, consultez [points d’ancrage](scrolling.md#snap-points) dans le Guide de [défilement Xamarin. Forms CollectionView](scrolling.md) .

L’énumération [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) définit les membres suivants :

- `Vertical` indique que le [`CarouselView`](xref:Xamarin.Forms.CarouselView) s’étend verticalement au fur et à mesure que des éléments sont ajoutés.
- `Horizontal` indique que le [`CarouselView`](xref:Xamarin.Forms.CarouselView) s’agrandit horizontalement au fur et à mesure que des éléments sont ajoutés.

La classe `LinearItemsLayout` hérite de la classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) et définit une propriété `ItemSpacing`, de type `double`, qui représente l’espace vide autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0. La classe `LinearItemsLayout` définit également des membres statiques `Vertical` et `Horizontal`. Ces membres peuvent être utilisés pour créer des listes verticales ou horizontales, respectivement. Vous pouvez également créer un objet `LinearItemsLayout`, en spécifiant un membre d’énumération [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) en tant qu’argument.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) utilise les moteurs de disposition natifs pour effectuer la mise en page.

## <a name="horizontal-layout"></a>Disposition horizontale

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche ses éléments horizontalement. Par conséquent, il n’est pas nécessaire de définir la propriété [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) pour utiliser cette disposition :

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

Cette disposition peut également être obtenue en affectant à la propriété [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la valeur d’un objet `LinearItemsLayout`, en spécifiant le `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membre de l’énumération comme valeur de propriété `Orientation` :

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

Cela a pour résultat une mise en page qui augmente horizontalement au fur et à mesure que de nouveaux éléments sont ajoutés.

## <a name="vertical-layout"></a>Disposition verticale

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pouvez afficher ses éléments verticalement en affectant à la propriété [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la valeur d’un objet `LinearItemsLayout`, en spécifiant le `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membre d’énumération en tant que valeur de propriété `Orientation` :

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

Cela aboutit à une mise en page qui se développe verticalement au fur et à mesure que de nouveaux éléments sont ajoutés.

## <a name="partially-visible-adjacent-items"></a>Éléments adjacents partiellement visibles

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche des éléments complets à la fois. Toutefois, ce comportement peut être modifié en affectant à la propriété `PeekAreaInsets` la valeur d' `Thickness` qui spécifie la proportion de rendre les éléments adjacents partiellement visibles par. Cela peut être utile pour indiquer aux utilisateurs qu’il existe des éléments supplémentaires à afficher. Le code XAML suivant montre un exemple de définition de cette propriété :

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

Le résultat est que les éléments adjacents sont partiellement exposés à l’écran.

## <a name="item-spacing"></a>Espacement d’éléments

Par défaut, chaque élément d’un [`CarouselView`](xref:Xamarin.Forms.CarouselView) n’a pas d’espace vide autour de lui. Ce comportement peut être modifié en définissant des propriétés sur la disposition des éléments utilisée par le `CarouselView`.

Lorsqu’un [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit sa propriété [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) sur un objet `LinearItemsLayout`, la propriété `LinearItemsLayout.ItemSpacing` peut être définie sur une valeur `double` qui représente l’espace vide autour de chaque élément :

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
> La propriété `LinearItemsLayout.ItemSpacing` a un jeu de rappels de validation, qui garantit que la valeur de la propriété est toujours supérieure ou égale à 0.

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

Ce code donne une disposition verticale, dont l’espacement est de 20 autour de chaque élément.

## <a name="dynamic-resizing-of-items"></a>Redimensionnement dynamique des éléments

Les éléments d’un [`CarouselView`](xref:Xamarin.Forms.CarouselView) peuvent être redimensionnés dynamiquement au moment de l’exécution en modifiant les propriétés relatives à la disposition des éléments au sein du [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Par exemple, l’exemple de code suivant modifie les propriétés [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) et [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) d’un objet [`Image`](xref:Xamarin.Forms.Image) , et la propriété `HeightRequest` de son [`Frame`](xref:Xamarin.Forms.Frame)parent :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

Le gestionnaire d’événements `OnImageTapped` est exécuté en réponse à un objet [`Image`](xref:Xamarin.Forms.Image) qui est taraudé, et modifie les dimensions de l’image (et de son cadre parent), afin qu’elle soit plus facilement visible.

## <a name="right-to-left-layout"></a>Disposition de droite à gauche

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pouvez mettre en forme son contenu dans un sens de déroulement de droite à gauche en affectant à sa propriété [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) la valeur [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft). Toutefois, la propriété `FlowDirection` doit idéalement être définie sur une page ou une disposition racine, ce qui entraîne la réponse de tous les éléments de la page, ou de la disposition racine, au sens du déroulement :

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

La [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) par défaut pour un élément avec un parent est [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Par conséquent, le [`CarouselView`](xref:Xamarin.Forms.CarouselView) hérite de la valeur de la propriété `FlowDirection` du [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin. Forms CarouselView défilement](scrolling.md)
