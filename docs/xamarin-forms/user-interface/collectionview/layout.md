---
title: Xamarin.Forms Disposition CollectionView
description: Par défaut, un CollectionView affiche ses éléments dans une liste verticale. Toutefois, les listes et grilles verticales et horizontales peuvent être spécifiées.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dcff583f929e0ce8cda9fe81dc88452c6056ddf1
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371480"
---
# <a name="no-locxamarinforms-collectionview-layout"></a>Xamarin.Forms Disposition CollectionView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes qui contrôlent la disposition :

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de type [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout) , spécifie la disposition à utiliser.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy), de type [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) , spécifie la stratégie de mesure d’élément à utiliser.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Par défaut, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche ses éléments dans une liste verticale. Toutefois, les dispositions suivantes peuvent être utilisées :

- Liste verticale : liste de colonnes unique qui s’agrandit verticalement au fur et à mesure que de nouveaux éléments sont ajoutés.
- Liste horizontale : liste de lignes unique qui s’agrandit horizontalement au fur et à mesure que de nouveaux éléments sont ajoutés.
- Grille verticale : grille à plusieurs colonnes qui se développe verticalement au fur et à mesure que de nouveaux éléments sont ajoutés.
- Grille horizontale : grille à plusieurs lignes qui s’agrandit horizontalement au fur et à mesure que de nouveaux éléments sont ajoutés.

Ces dispositions peuvent être spécifiées en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à la propriété la classe qui dérive de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe. Cette classe définit les propriétés suivantes :

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de type [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) , spécifie la direction dans laquelle le [`CollectionView`](xref:Xamarin.Forms.CollectionView) s’étend à mesure que des éléments sont ajoutés.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de type [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , spécifie la manière dont les points d’alignement sont alignés avec les éléments.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de type [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , spécifie le comportement des points d’alignement lors du défilement.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données. Pour plus d’informations sur les points d’alignement, consultez [points d’ancrage](scrolling.md#snap-points) dans le Guide de [ Xamarin.Forms défilement CollectionView](scrolling.md) .

L' [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération définit les membres suivants :

- `Vertical` indique que le [`CollectionView`](xref:Xamarin.Forms.CollectionView) s’étend verticalement au fur et à mesure que des éléments sont ajoutés.
- `Horizontal` indique que le [`CollectionView`](xref:Xamarin.Forms.CollectionView) s’étend horizontalement au fur et à mesure que des éléments sont ajoutés.

La `LinearItemsLayout` classe hérite de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe et définit une `ItemSpacing` propriété, de type `double` , qui représente l’espace vide autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0. La `LinearItemsLayout` classe définit également `Vertical` des membres et statiques `Horizontal` . Ces membres peuvent être utilisés pour créer des listes verticales ou horizontales, respectivement. Vous `LinearItemsLayout` pouvez également créer un objet, en spécifiant un [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membre d’énumération en tant qu’argument.

La [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) classe hérite de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe et définit les propriétés suivantes :

- `VerticalItemSpacing`, de type `double` , qui représente l’espace vide vertical autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0.
- `HorizontalItemSpacing`, de type `double` , qui représente l’espace vide horizontal autour de chaque élément. La valeur par défaut de cette propriété est 0, et sa valeur doit toujours être supérieure ou égale à 0.
- `Span`, de type `int` , qui représente le nombre de colonnes ou de lignes à afficher dans la grille. La valeur par défaut de cette propriété est 1, et sa valeur doit toujours être supérieure ou égale à 1.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilise les moteurs de disposition natifs pour effectuer la mise en page.

## <a name="vertical-list"></a>Liste verticale

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche ses éléments dans une disposition de liste verticale. Par conséquent, il n’est pas nécessaire de définir la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) propriété pour utiliser cette disposition :

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

Toutefois, en cas d’exhaustivité, en XAML, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être configuré pour afficher ses éléments dans une liste verticale en affectant à sa propriété la valeur [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `VerticalList` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Cela peut également être accompli en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à la propriété un `LinearItemsLayout` objet, en spécifiant le membre de l' `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération en tant que `Orientation` valeur de propriété :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

Il en résulte une liste de colonnes unique, qui s’étend verticalement à mesure que de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de liste verticale CollectionView, sur iOS et Android](layout-images/vertical-list.png "Disposition de la liste verticale CollectionView")](layout-images/vertical-list-large.png#lightbox "Disposition de la liste verticale CollectionView")

## <a name="horizontal-list"></a>Liste horizontale

En XAML, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut afficher ses éléments dans une liste horizontale en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à sa propriété la valeur `HorizontalList` :

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

Cette disposition peut également être obtenue en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à la propriété un `LinearItemsLayout` objet, en spécifiant le membre de l' `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) énumération en tant que valeur de la `Orientation` propriété :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

Il en résulte une liste de lignes unique, qui s’agrandit horizontalement au fur et à mesure que de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de liste horizontale CollectionView, sur iOS et Android](layout-images/horizontal-list.png "Disposition de liste horizontale CollectionView")](layout-images/horizontal-list-large.png#lightbox "Disposition de liste horizontale CollectionView")

## <a name="vertical-grid"></a>Grille verticale

En XAML, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut afficher ses éléments dans une grille verticale en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à sa propriété la valeur `VerticalGrid` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalGrid, 2">
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

Cette disposition peut également être obtenue en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à la propriété un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objet dont la [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) propriété a la valeur `Vertical` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    ...
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

Par défaut, un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) élément vertical affiche les éléments dans une seule colonne. Toutefois, cet exemple affecte `GridItemsLayout.Span` à la propriété la valeur 2. Il en résulte une grille à deux colonnes, qui se développe verticalement à mesure que de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de grille verticale CollectionView, sur iOS et Android](layout-images/vertical-grid.png "Disposition de grille verticale CollectionView")](layout-images/vertical-grid-large.png#lightbox "Disposition de grille verticale CollectionView")

## <a name="horizontal-grid"></a>Grille horizontale

En XAML, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut afficher ses éléments dans une grille horizontale en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à sa propriété la valeur `HorizontalGrid` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalGrid, 4">
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

Cette disposition peut également être obtenue en affectant [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) à la propriété un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objet dont la [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) propriété a la valeur `Horizontal` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    ...
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

Par défaut, un horizontal [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) affiche des éléments sur une seule ligne. Toutefois, cet exemple affecte `GridItemsLayout.Span` à la propriété la valeur 4. Il en résulte une grille de quatre lignes, qui s’agrandit horizontalement à mesure que de nouveaux éléments sont ajoutés :

[![Capture d’écran d’une disposition de grille horizontale CollectionView, sur iOS et Android](layout-images/horizontal-grid.png "Disposition de grille horizontale CollectionView")](layout-images/horizontal-grid-large.png#lightbox "Disposition de grille horizontale CollectionView")

## <a name="headers-and-footers"></a>En-têtes et pieds de page

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut présenter un en-tête et un pied de page qui défilent avec les éléments de la liste. L’en-tête et le pied de page peuvent être des chaînes, des vues ou des [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit les propriétés suivantes pour spécifier l’en-tête et le pied de page :

- `Header`, de type `object` , spécifie la chaîne, la liaison ou la vue qui sera affichée au début de la liste.
- `HeaderTemplate`, de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , spécifie le `DataTemplate` à utiliser pour mettre en forme `Header` .
- `Footer`, de type `object` , spécifie la chaîne, la liaison ou la vue qui sera affichée à la fin de la liste.
- `FooterTemplate`, de type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , spécifie le `DataTemplate` à utiliser pour mettre en forme `Footer` .

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

Lorsqu’un en-tête est ajouté à une disposition qui augmente horizontalement, de gauche à droite, l’en-tête est affiché à gauche de la liste. De même, lorsqu’un pied de page est ajouté à une disposition qui augmente horizontalement, de gauche à droite, le pied de page est affiché à droite de la liste.

### <a name="display-strings-in-the-header-and-footer"></a>Afficher des chaînes dans l’en-tête et le pied de page

Les `Header` `Footer` Propriétés et peuvent être définies sur des `string` valeurs, comme indiqué dans l’exemple suivant :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Ce code génère les captures d’écran suivantes, avec l’en-tête illustré dans la capture d’écran iOS et le pied de page affiché dans la capture d’écran Android :

[![Capture d’écran d’un en-tête et d’un pied de page de chaîne CollectionView sur iOS et Android](layout-images/header-footer-string.png "En-tête et pied de page de chaîne CollectionView")](layout-images/header-footer-string-large.png#lightbox "En-tête et pied de page de chaîne CollectionView")

### <a name="display-views-in-the-header-and-footer"></a>Afficher les affichages dans l’en-tête et le pied de page

Les `Header` `Footer` Propriétés et peuvent toutes être définies sur une vue. Il peut s’agir d’une vue unique ou d’une vue contenant plusieurs vues enfants. L’exemple suivant montre les `Header` `Footer` Propriétés et dont chacune a pour valeur un [`StackLayout`](xref:Xamarin.Forms.StackLayout) objet qui contient un [`Label`](xref:Xamarin.Forms.Label) objet :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Ce code génère les captures d’écran suivantes, avec l’en-tête illustré dans la capture d’écran iOS et le pied de page affiché dans la capture d’écran Android :

[![Capture d’écran d’un en-tête et d’un pied de page CollectionView à l’aide de vues, sur iOS et Android](layout-images/header-footer-view.png "En-tête et pied de page de l’affichage CollectionView")](layout-images/header-footer-view-large.png#lightbox "En-tête et pied de page de l’affichage CollectionView")

### <a name="display-a-templated-header-and-footer"></a>Afficher un en-tête et un pied de page basés sur un modèle

Les `HeaderTemplate` `FooterTemplate` Propriétés et peuvent être définies sur des [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets utilisés pour mettre en forme l’en-tête et le pied de page. Dans ce scénario, les `Header` `Footer` Propriétés et doivent être liées à la source actuelle pour que les modèles soient appliqués, comme indiqué dans l’exemple suivant :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Ce code génère les captures d’écran suivantes, avec l’en-tête illustré dans la capture d’écran iOS et le pied de page affiché dans la capture d’écran Android :

[![Capture d’écran d’un en-tête et d’un pied de page CollectionView à l’aide de modèles, sur iOS et Android](layout-images/header-footer-template.png "En-tête et pied de page du modèle CollectionView")](layout-images/header-footer-template-large.png#lightbox "En-tête et pied de page du modèle CollectionView")

## <a name="item-spacing"></a>Espacement d’éléments

Par défaut, il n’y a pas d’espace entre chaque élément dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Ce comportement peut être modifié en définissant des propriétés sur la disposition des éléments utilisée par le `CollectionView` .

Quand un [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit sa [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) propriété sur un `LinearItemsLayout` objet, la `LinearItemsLayout.ItemSpacing` propriété peut être définie sur une `double` valeur qui représente l’espace entre les éléments :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> La `LinearItemsLayout.ItemSpacing` propriété a un jeu de rappels de validation, ce qui garantit que la valeur de la propriété est toujours supérieure ou égale à 0.

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Ce code génère une liste à une seule colonne verticale, dont l’espacement est de 20 entre les éléments :

[![Capture d’écran d’un CollectionView avec l’espacement des éléments, sur iOS et Android](layout-images/vertical-list-spacing.png "Espacement des éléments CollectionView")](layout-images/vertical-list-spacing-large.png#lightbox "Espacement des éléments CollectionView")

Quand un [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit sa [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) propriété sur un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objet, les `GridItemsLayout.VerticalItemSpacing` `GridItemsLayout.HorizontalItemSpacing` Propriétés et peuvent être définies sur des `double` valeurs qui représentent l’espace vide verticalement et horizontalement entre les éléments :

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
> Les `GridItemsLayout.VerticalItemSpacing` `GridItemsLayout.HorizontalItemSpacing` Propriétés et ont des rappels de validation définis, ce qui garantit que les valeurs des propriétés sont toujours supérieures ou égales à 0.

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

Ce code génère une grille verticale à deux colonnes, dont l’espacement vertical est de 20 entre les éléments et un espacement horizontal de 30 entre les éléments :

[![Capture d’écran d’un CollectionView avec l’espacement des éléments, sur Android](layout-images/vertical-grid-spacing.png "Espacement des éléments CollectionView")](layout-images/vertical-grid-spacing-large.png#lightbox "Espacement des éléments CollectionView")

## <a name="item-sizing"></a>Dimensionnement des éléments

Par défaut, chaque élément d’un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est mesuré et dimensionné individuellement, à condition que les éléments d’interface utilisateur dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ne spécifient pas de tailles fixes. Ce comportement, qui peut être modifié, est spécifié par la [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) valeur de la propriété. Cette valeur de propriété peut être définie sur l’un des membres de l' [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) énumération :

- `MeasureAllItems` : chaque élément est mesuré individuellement. Il s’agit de la valeur par défaut.
- `MeasureFirstItem` : seul le premier élément est mesuré, avec tous les éléments suivants ayant la même taille que le premier élément.

> [!IMPORTANT]
> La `MeasureFirstItem` stratégie de dimensionnement entraîne une augmentation des performances lorsqu’elle est utilisée dans les situations où la taille de l’élément est destinée à être uniforme pour tous les éléments.

L’exemple de code suivant illustre la définition de la [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) propriété :

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

## <a name="dynamic-resizing-of-items"></a>Redimensionnement dynamique des éléments

Les éléments d’un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peuvent être redimensionnés dynamiquement au moment de l’exécution en modifiant les propriétés relatives à la disposition des éléments dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Par exemple, l’exemple de code suivant modifie [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) les [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriétés et d’un [`Image`](xref:Xamarin.Forms.Image) objet :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

Le `OnImageTapped` Gestionnaire d’événements est exécuté en réponse à un [`Image`](xref:Xamarin.Forms.Image) objet qui est taraudé, et modifie les dimensions de l’image pour qu’elle soit plus facilement visible :

[![Capture d’écran d’un CollectionView avec le dimensionnement d’un élément dynamique sur iOS et Android](layout-images/runtime-resizing.png "Dimensionnement de l’élément dynamique CollectionView")](layout-images/runtime-resizing-large.png#lightbox "Dimensionnement de l’élément dynamique CollectionView")

## <a name="right-to-left-layout"></a>Disposition de droite à gauche

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut mettre en forme son contenu dans un sens de déroulement de droite à gauche en affectant à sa propriété la valeur [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) . Toutefois, la `FlowDirection` propriété doit idéalement être définie sur une page ou une disposition racine, ce qui entraîne la réponse de tous les éléments de la page, ou de la disposition racine, au sens du déroulement :

```xaml
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

La valeur par défaut [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) d’un élément avec un parent est [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . Par conséquent, le [`CollectionView`](xref:Xamarin.Forms.CollectionView) hérite de la `FlowDirection` valeur de la propriété de [`StackLayout`](xref:Xamarin.Forms.StackLayout) , qui à son tour hérite de la `FlowDirection` valeur de la propriété de [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Cela aboutit à une mise en page de droite à gauche illustrée dans les captures d’écran suivantes :

[![Capture d’écran d’une disposition de liste verticale de droite à gauche CollectionView, sur iOS et Android](layout-images/vertical-list-rtl.png "Disposition de liste verticale de droite à gauche CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "Disposition de liste verticale de droite à gauche CollectionView")

Pour plus d’informations sur le sens du déroulement, consultez [localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localisation de droite à gauche](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.Forms Défilement CollectionView](scrolling.md)