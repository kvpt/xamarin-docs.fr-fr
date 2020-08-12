---
title: Choisir une Xamarin.Forms disposition
description: Xamarin.Formsles classes de disposition vous permettent d’organiser et de regrouper des contrôles d’interface utilisateur dans votre application.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a097d6dad067f209d3aad27bde382f28910324a
ms.sourcegitcommit: c3329ab25d377907d8804cdd5e26dc84a274f39c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130927"
---
# <a name="choose-a-no-locxamarinforms-layout"></a>Choisir une Xamarin.Forms disposition

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Formsles classes de disposition vous permettent d’organiser et de regrouper des contrôles d’interface utilisateur dans votre application. Le choix d’une classe de disposition requiert une connaissance de la façon dont la disposition positionne ses éléments enfants, et de la façon dont la disposition dimensionne ses éléments enfants. En outre, il peut être nécessaire d’imbriquer des dispositions pour créer la disposition souhaitée.

L’illustration suivante montre les dispositions typiques qui peuvent être obtenues avec les Xamarin.Forms classes de mise en page principales :

[![Les classes de disposition principales dans ::: No-Loc (Xamarin. Forms) :::](images/layouts.png "::: No-Loc (Xamarin. Forms) ::: Layout, classes")](images/layouts-large.png#lightbox "::: No-Loc (Xamarin. Forms) ::: Layout, classes")

## <a name="stacklayout"></a>StackLayout

[`StackLayout`](xref:Xamarin.Forms.StackLayout)Organise les éléments d’une pile unidimensionnelle, horizontalement ou verticalement. La [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriété spécifie la direction des éléments, et l’orientation par défaut est [`Vertical`](xref:Xamarin.Forms.StackOrientation) . `StackLayout`est généralement utilisé pour réorganiser une sous-section de l’interface utilisateur sur une page.

Le code XAML suivant montre comment créer un vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) contenant trois [`Label`](xref:Xamarin.Forms.Label) objets :

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

Dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , si la taille d’un élément n’est pas définie explicitement, il se développe pour remplir la largeur disponible ou la hauteur si la [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriété a la valeur [`Horizontal`](xref:Xamarin.Forms.StackOrientation) .

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) est souvent utilisé comme une disposition parente, qui contient d’autres dispositions enfants. Toutefois, un ne `StackLayout` doit pas être utilisé pour reproduire une [`Grid`](xref:Xamarin.Forms.Grid) disposition à l’aide d’une combinaison d' `StackLayout` objets. Le code suivant montre un exemple de cette mauvaise pratique :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Cela ne sert à rien, car des calculs de dispositions inutiles sont effectués. Au lieu de cela, la disposition souhaitée peut être mieux obtenue à l’aide d’un [`Grid`](xref:Xamarin.Forms.Grid) .

> [!TIP]
> Quand vous utilisez un [`StackLayout`](xref:Xamarin.Forms.StackLayout) , assurez-vous qu’un seul élément enfant a la valeur [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Cette propriété permet de garantir que l’enfant spécifié occupe le plus grand espace que l’élément `StackLayout` peut lui donner, et il est inutile d’effectuer ces calculs plusieurs fois.

Pour plus d’informations, consultez [ Xamarin.Forms StackLayout](stacklayout.md).

## <a name="grid"></a>Grille

Un [`Grid`](xref:Xamarin.Forms.Grid) est utilisé pour afficher des éléments dans les lignes et les colonnes, qui peuvent avoir des tailles proportionnelles ou absolues. Les lignes et les colonnes d’une grille sont spécifiées avec les [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) Propriétés et.

Pour positionner des éléments dans des [`Grid`](xref:Xamarin.Forms.Grid) cellules spécifiques, utilisez les [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) propriétés jointes et. Pour que les éléments s’étendent sur plusieurs lignes et colonnes, utilisez les [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) propriétés jointes et.

> [!NOTE]
> Une [`Grid`](xref:Xamarin.Forms.Grid) disposition ne doit pas être confondue avec des tables et n’est pas destinée à présenter des données tabulaires. Contrairement aux tableaux HTML, un `Grid` est destiné à la disposition du contenu. Pour afficher des données tabulaires, envisagez d’utiliser un [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)ou [TableView](~/xamarin-forms/user-interface/tableview.md).

Le code XAML suivant montre comment créer un [`Grid`](xref:Xamarin.Forms.Grid) avec deux lignes et deux colonnes :

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition />
    </Grid.ColumnDefinitions>    
    <Label Text="Column 0, Row 0"
           WidthRequest="200" />
    <Label Grid.Column="1"
           Text="Column 1, Row 0" />
    <Label Grid.Row="1"
           Text="Column 0, Row 1" />
    <Label Grid.Column="1"
           Grid.Row="1"
           Text="Column 1, Row 1" />
</Grid>
```

Dans cet exemple, le dimensionnement fonctionne comme suit :

- Chaque ligne a une hauteur explicite de 50 unités indépendantes du périphérique.
- La largeur de la première colonne est définie sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto) et est donc aussi large que nécessaire pour ses enfants. Dans ce cas, il s’agit d’unités indépendantes de l’appareil 200 de large pour tenir compte de la largeur du premier [`Label`](xref:Xamarin.Forms.Label) .

L’espace peut être distribué au sein d’une colonne ou d’une ligne à l’aide du dimensionnement automatique, ce qui permet à des colonnes et des lignes de s’adapter à leur contenu. Pour ce faire, affectez à la hauteur d’un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) , ou à la largeur d’un, la valeur [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) [`Auto`](xref:Xamarin.Forms.GridLength.Auto) . Le dimensionnement proportionnel peut également être utilisé pour distribuer l’espace disponible parmi les lignes et les colonnes de la grille par proportions pondérées. Pour ce faire, affectez à la hauteur d’un `RowDefinition` , ou à la largeur d’un `ColumnDefinition` , une valeur qui utilise l' `*` opérateur.

> [!CAUTION]
> Essayez de vous assurer que le moins de lignes et de colonnes possibles est définie sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto) Size. Pour chaque ligne ou colonne dimensionnée automatiquement, le moteur de disposition effectue des calculs de dispositions supplémentaires. Utilisez plutôt des lignes et colonnes de taille fixe si possible. Vous pouvez également définir des lignes et des colonnes pour occuper une quantité proportionnelle d’espace avec la [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valeur d’énumération.

Pour plus d’informations, consultez [ Xamarin.Forms Grid](grid.md).

## <a name="flexlayout"></a>FlexLayout

Un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) est semblable à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) dans le sens où il affiche des éléments enfants horizontalement ou verticalement dans une pile. Toutefois, un `FlexLayout` peut également encapsuler ses enfants s’il y a trop d’éléments pour tenir sur une seule ligne ou colonne, et permet également un contrôle plus granulaire de la taille, de l’orientation et de l’alignement de ses éléments enfants.

Le code XAML suivant montre comment créer un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) qui affiche ses vues dans une seule colonne :

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Dans cet exemple, la disposition fonctionne comme suit :

- La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propriété a la valeur `Column` , ce qui a pour effet que les enfants du `FlexLayout` soient disposés dans une seule colonne d’éléments.
- La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propriété est définie sur `Center` , ce qui entraîne le centrage horizontal de chaque élément.
- La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriété a la valeur `SpaceEvenly` , qui alloue tout l’espace vertical restant de manière égale entre tous les éléments, et au-dessus du premier élément, et sous le dernier élément.

Pour plus d’informations, consultez [ Xamarin.Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) est utilisé pour positionner et dimensionner des éléments par rapport aux propriétés des éléments de disposition ou frères. Par défaut, un élément est positionné dans le coin supérieur gauche de la disposition. Un `RelativeLayout` peut être utilisé pour créer des interfaces utilisateur qui évoluent proportionnellement entre les tailles des appareils.

Dans [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) , les positions et les tailles sont spécifiées en tant que contraintes. Les contraintes ont des [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) Propriétés et, qui peuvent être utilisées pour définir des positions et des tailles en tant que multiples (ou fractions) de propriétés d’autres objets, plus une constante. En outre, les constantes peuvent être négatives.

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)Prend en charge le positionnement des éléments en dehors de ses propres limites.

Le code XAML suivant montre comment organiser les éléments dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) :

```xaml
<RelativeLayout>
    <BoxView Color="Blue"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView Color="Red"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.85}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView x:Name="pole"
             Color="Gray"
             WidthRequest="15"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.45}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.25}" />
    <BoxView Color="Green"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
             RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.2, Constant=20}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Dans cet exemple, la disposition fonctionne comme suit :

- Le bleu [`BoxView`](xref:Xamarin.Forms.BoxView) reçoit une taille explicite d’unités indépendantes du périphérique 50x50. Elle est placée dans le coin supérieur gauche de la disposition, qui est la position par défaut.
- Le rouge reçoit [`BoxView`](xref:Xamarin.Forms.BoxView) une taille explicite d’unités indépendantes du périphérique 50x50. Elle est placée dans le coin supérieur droit de la disposition.
- Le gris [`BoxView`](xref:Xamarin.Forms.BoxView) reçoit une largeur explicite de 15 unités indépendantes du périphérique, et la hauteur est définie sur 75% de la hauteur de son parent.
- Le vert [`BoxView`](xref:Xamarin.Forms.BoxView) n’a pas de taille explicite. Sa position est définie par rapport au `BoxView` nommé `pole` .

> [!WARNING]
> Évitez d’utiliser un élément `RelativeLayout` autant que possible. Le processeur aurait considérablement plus de travail à effectuer.

Pour plus d’informations, consultez [ Xamarin.Forms RelativeLayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) est utilisé pour positionner et dimensionner des éléments à l’aide de valeurs explicites, ou de valeurs relatives à la taille de la disposition. La position est spécifiée par l’angle supérieur gauche de l’enfant par rapport au coin supérieur gauche de `AbsoluteLayout` .

Une [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) doit être considérée comme une disposition spéciale à utiliser uniquement lorsque vous pouvez imposer une taille aux enfants ou lorsque la taille de l’élément n’affecte pas le positionnement d’autres enfants. Une utilisation standard de cette disposition consiste à créer une superposition qui couvre la page avec d’autres contrôles, peut-être pour empêcher l’utilisateur d’interagir avec les contrôles normaux sur la page.

> [!IMPORTANT]
> Les `HorizontalOptions` Propriétés et n' `VerticalOptions` ont aucun effet sur les enfants d’un `AbsoluteLayout` .

Dans un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe est utilisée pour spécifier la position horizontale, la position verticale, la largeur et la hauteur d’un élément. De plus, la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propriété jointe spécifie la manière dont les limites de disposition sont interprétées.

Le code XAML suivant montre comment organiser les éléments dans un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) :

```xaml
<AbsoluteLayout Margin="40">
    <BoxView Color="Red"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="30" />
    <BoxView Color="Green"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="60" />
    <BoxView Color="Blue"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" />
</AbsoluteLayout>
```

Dans cet exemple, la disposition fonctionne comme suit :

- Chacun [`BoxView`](xref:Xamarin.Forms.BoxView) reçoit une taille explicite de 100x100 et s’affiche à la même position, centrée horizontalement.
- Le rouge [`BoxView`](xref:Xamarin.Forms.BoxView) est pivoté de 30 degrés et le vert `BoxView` est pivoté de 60 degrés.
- Sur chaque [`BoxView`](xref:Xamarin.Forms.BoxView) , la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propriété jointe a la valeur, ce qui `PositionProportional` indique que la position est proportionnelle à l’espace restant après la largeur et la hauteur.

> [!CAUTION]
> Évitez d’utiliser la [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propriété chaque fois que cela est possible, car le moteur de présentation effectuera des calculs de disposition supplémentaires.

Pour plus d’informations, consultez [ Xamarin.Forms AbsoluteLayout](absolutelayout.md).

## <a name="input-transparency"></a>Transparence de l’entrée

Chaque élément visuel a une [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) propriété qui est utilisée pour définir si l’élément reçoit l’entrée. Sa valeur par défaut est `false` , garantissant que l’élément reçoit l’entrée.

Quand cette propriété est définie sur une classe de disposition, sa valeur est transférée aux éléments enfants. Par conséquent, l’affectation de la valeur à la [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) propriété `true` sur une classe Layout entraîne la non-réception de tous les éléments dans la disposition.

## <a name="layout-performance"></a>Performances de disposition

Pour obtenir les meilleures performances possibles en matière de disposition, suivez les instructions fournies dans [optimiser les performances](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)de la disposition.

En outre, les performances de rendu de page peuvent également être améliorées à l’aide de la compression de disposition, qui supprime les dispositions spécifiées de l’arborescence d’éléments visuels. Pour plus d’informations, consultez compression de la [disposition](layout-compression.md).

## <a name="related-links"></a>Liens connexes

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Xamarin.FormsDispositions (vidéo)](https://youtu.be/4HlLjTZQzjM)
- [Xamarin.FormsStackLayout](stacklayout.md)
- [Xamarin.FormsBoutons](grid.md)
- [Xamarin.FormsFlexLayout](flex-layout.md)
- [Xamarin.FormsAbsoluteLayout](absolutelayout.md)
- [Xamarin.FormsRelativeLayout](relative-layout.md)
- [Optimiser les performances de disposition](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compression de disposition](layout-compression.md)
