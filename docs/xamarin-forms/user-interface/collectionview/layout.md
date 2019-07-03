---
title: Xamarin.Forms CollectionView de disposition
description: Par défaut, un CollectionView affiche ses éléments dans une liste verticale. Toutefois, grilles et répertorie vertical et horizontal peuvent être spécifiés.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 786cea04718022847bba2ecffed8f377dd49bd8b
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512808"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin.Forms CollectionView de disposition

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes qui contrôlent la mise en page :

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de type [ `IItemsLayout` ](xref:Xamarin.Forms.IItemsLayout), spécifie la disposition à utiliser.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), de type [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy), spécifie la stratégie de mesures élément à utiliser.

Ces propriétés sont secondées par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Par défaut, un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) affiche ses éléments dans une liste verticale. Toutefois, chacun des dispositions suivantes peut être utilisé :

- Liste verticale : une liste de colonne unique qui s’agrandit verticalement comme de nouveaux éléments sont ajoutés.
- Liste horizontale : une liste de ligne unique qui se développe horizontalement comme de nouveaux éléments sont ajoutés.
- Quadrillage vertical – une grille de plusieurs colonne qui s’agrandit verticalement comme de nouveaux éléments sont ajoutés.
- Grille horizontale – une grille à plusieurs ligne qui se développe horizontalement comme de nouveaux éléments sont ajoutés.

Ces mises en page peuvent être spécifiés en définissant le [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété de classe qui dérive de la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe. Cette classe définit les propriétés suivantes :

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de type [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation), spécifie la direction dans laquelle le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) augmente à mesure que les éléments sont ajoutés.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de type [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment), spécifie comment les points d’ancrage sont alignées avec les éléments.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de type [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType), spécifie le comportement des points d’ancrage lors du défilement.

Ces propriétés sont secondées par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données. Pour plus d’informations sur les points d’ancrage, consultez [points d’alignement](scrolling.md#snap-points) dans le [Xamarin.Forms CollectionView défilement](scrolling.md) guide.

Le [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération définit les membres suivants :

- `Vertical` Indique que le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) étendre verticalement comme les éléments sont ajoutés.
- `Horizontal` Indique que le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) se développe horizontalement, comme les éléments sont ajoutés.

Le [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) classe hérite de la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe et définit un `ItemSpacing` propriété, de type `double`, qui représente l’espace vide autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0. Le `ListItemsLayout` classe définit également statique `Vertical` et `Horizontal` membres. Ces membres peuvent être utilisés pour créer des listes verticales ou horizontales, respectivement. Vous pouvez également un `ListItemsLayout` objet peut être créé, en spécifiant un [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membre d’énumération en tant qu’argument.

Le [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) classe hérite de la [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe et définit les propriétés suivantes :

- `VerticalItemSpacing`, de type `double`, qui représente l’espace vide verticale autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0.
- `HorizontalItemSpacing`, de type `double`, qui représente l’espace vide horizontale autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0.
- `Span`, de type `int`, qui représente le nombre de colonnes ou lignes à afficher dans la grille. La valeur par défaut de cette propriété est 1, et sa valeur doit toujours être supérieure ou égale à 1.

Ces propriétés sont secondées par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilise les moteurs de présentation natif pour effectuer la mise en page.

## <a name="vertical-list"></a>Liste verticale

Par défaut, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) affiche ses éléments dans une disposition Liste verticale. Par conséquent, il n’est pas nécessaire de définir le [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété à utiliser cette disposition :

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
</CollectionView>
```

Toutefois, par souci d’exhaustivité, un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peut être définie pour afficher ses éléments dans une liste verticale en définissant son [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété `VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Ou bien, pouvez également le faire en définissant le [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété vers un objet de la [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) classe, en spécifiant le `Vertical` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membre d’énumération en tant qu’argument :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Vertical
};
```

Il en résulte dans une liste de colonne unique, qui s’agrandit verticalement comme de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de liste verticale CollectionView, sur iOS et Android](layout-images/vertical-list.png "mise en page de la liste verticale CollectionView")](layout-images/vertical-list-large.png#lightbox "mise en page de la liste verticale CollectionView")

## <a name="horizontal-list"></a>Liste horizontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut afficher ses éléments dans une liste horizontale en définissant son [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété `HorizontalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Ou bien, pouvez également le faire en définissant le [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété à un [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) de l’objet, en spécifiant le `Horizontal` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membre d’énumération en tant qu’argument :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Horizontal
};
```

Il en résulte dans une liste de ligne unique, ce qui se développe horizontalement comme de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de liste horizontale CollectionView, sur iOS et Android](layout-images/horizontal-list.png "mise en page de liste horizontale CollectionView")](layout-images/horizontal-list-large.png#lightbox "mise en page de liste horizontale CollectionView")

## <a name="vertical-grid"></a>Grille verticale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut afficher ses éléments dans une grille verticale en définissant son [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété à un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) de l’objet dont la propriété [ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) propriété est définie sur `Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

Par défaut, un vertical [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) affichera les éléments dans une seule colonne. Toutefois, cet exemple définit le `GridItemsLayout.Span` propriété à 2. Il en résulte dans une grille à deux colonnes, qui s’agrandit verticalement comme de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de grille verticale CollectionView, sur iOS et Android](layout-images/vertical-grid.png "mise en page de grille verticale CollectionView")](layout-images/vertical-grid-large.png#lightbox "mise en page de grille verticale CollectionView")

## <a name="horizontal-grid"></a>Grille horizontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut afficher ses éléments dans une grille horizontale en définissant son [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété à un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) de l’objet dont la propriété[ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) propriété est définie sur `Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

Par défaut, un horizontal [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) affichera les éléments dans une seule ligne. Toutefois, cet exemple définit le `GridItemsLayout.Span` propriété à 4. Il en résulte dans une grille de quatre lignes, ce qui se développe horizontalement comme de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de grille horizontale CollectionView, sur iOS et Android](layout-images/horizontal-grid.png "mise en page de grille horizontale CollectionView")](layout-images/horizontal-grid-large.png#lightbox "mise en page de grille horizontale CollectionView")

## <a name="item-spacing"></a>Espacement d’élément

Par défaut, chaque élément dans un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) n’a pas un espace vide autour d’elle. Ce comportement peut être modifié en définissant des propriétés sur la disposition d’éléments utilisée par le `CollectionView`.

Lorsqu’un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) définit son [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété à un [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) objet, le `ListItemsLayout.ItemSpacing` propriété peut être définie sur une `double` valeur qui représente l’espace vide autour de chaque élément :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout ItemSpacing="20">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> Le `ListItemsLayout.ItemSpacing` propriété possède un ensemble de rappel de validation, ce qui garantit que la valeur de la propriété est toujours supérieure ou égale à 0.

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Ce code génère la liste verticale de colonne unique, qui a un espacement de 20 autour de chaque élément :

[![Capture d’écran d’un CollectionView d’espacement d’élément, sur iOS et Android](layout-images/vertical-list-spacing.png "CollectionView élément espacement")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView élément espacement")

Quand un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) définit son [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) propriété à un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) objet, le `GridItemsLayout.VerticalItemSpacing` et `GridItemsLayout.HorizontalItemSpacing` propriétés peuvent être la valeur `double` valeurs qui représentent l’espace vide verticalement et horizontalement autour de chaque élément :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> Le `GridItemsLayout.VerticalItemSpacing` et `GridItemsLayout.HorizontalItemSpacing` propriétés ont des rappels de validation définie, qui garantissent que les valeurs des propriétés sont toujours supérieure ou égale à 0.

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

Ce code génère un quadrillage vertical de deux colonnes, ce qui a un espacement vertical de 20 autour de chaque élément et un espacement horizontal de 30 autour de chaque élément :

[![Capture d’écran d’un CollectionView d’espacement d’élément, sur iOS et Android](layout-images/vertical-grid-spacing.png "CollectionView élément espacement")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView élément espacement")

## <a name="item-sizing"></a>Dimensionnement de l’élément

Par défaut, chaque élément dans un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) individuellement mesuré et en taille réelle, à condition que les éléments d’interface utilisateur dans le [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) ne spécifiez des tailles fixes. Ce comportement, ce qui peut être modifié, est spécifié par le [ `CollectionView.ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) valeur de propriété. Cette valeur de propriété peut être définie à une de la [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy) membres de l’énumération :

- `MeasureAllItems` : chaque élément est mesurée individuellement. Valeur par défaut.
- `MeasureFirstItem` : seul le premier élément est mesuré, avec tous les articles suivants étant données la même taille que le premier élément.

> [!IMPORTANT]
> Le `MeasureFirstItem` stratégie de dimensionnement entraîne de meilleures performances lorsqu’il est utilisé dans les situations où la taille de l’élément est destinée à être uniforme sur tous les éléments.

L’exemple de code suivant montre le paramètre le [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) propriété :

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>Redimensionnement d’éléments

Les éléments dans un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peut être redimensionné dynamiquement lors de l’exécution en modifiant disposition liés des propriétés d’éléments dans le [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Par exemple, le code suivant exemple modifie le [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) et [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) propriétés d’un [ `Image` ](xref:Xamarin.Forms.Image) objet :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

Le `OnImageTapped` Gestionnaire d’événements est exécuté en réponse à une [ `Image` ](xref:Xamarin.Forms.Image) de l’objet qui est activé par un clic et modifie les dimensions de l’image afin qu’il est plus faciles à afficher :

[![Capture d’écran d’un CollectionView de dimensionnement de l’élément dynamique, sur iOS et Android](layout-images/runtime-resizing.png "CollectionView élément dynamique dimensionnement")](layout-images/runtime-resizing-large.png#lightbox "CollectionView élément dynamique dimensionnement")

## <a name="right-to-left-layout"></a>Disposition de droite à gauche

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pouvez disposer son contenu dans un sens de déroulement de droite à gauche en définissant son [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriété [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). Toutefois, le `FlowDirection` propriété doit être définie dans l’idéal, sur une disposition de page ou racine, ce qui entraîne tous les éléments dans la page, ou la disposition de la racine, pour répondre à la direction du flux :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

La valeur par défaut [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) pour un élément avec un parent est [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Par conséquent, le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) hérite le `FlowDirection` valeur de propriété à partir de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), qui à son tour hérite le `FlowDirection` valeur de propriété à partir de la [ `ContentPage`](xref:Xamarin.Forms.ContentPage). Il en résulte dans la disposition de droite à gauche indiquée dans les captures d’écran suivante :

[![Capture d’écran d’une disposition de droite à gauche de liste verticale CollectionView, sur iOS et Android](layout-images/vertical-list-rtl.png "disposition Liste verticale de droite à gauche de CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "vertical de droite à gauche CollectionView disposition de liste")

Pour plus d’informations sur la direction du flux, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.Forms CollectionView le défilement](scrolling.md)
