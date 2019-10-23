---
title: Choisir une disposition Xamarin. Forms
description: Les classes de disposition Xamarin. Forms vous permettent d’organiser et de regrouper des contrôles d’interface utilisateur dans votre application.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: 161da8948f356fef997a411855598bc99d2f49b7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69893996"
---
# <a name="choose-a-xamarinforms-layout"></a>Choisir une disposition Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Les classes de disposition Xamarin. Forms vous permettent d’organiser et de regrouper des contrôles d’interface utilisateur dans votre application. Le choix d’une classe de disposition requiert une connaissance de la façon dont la disposition positionne ses éléments enfants, et de la façon dont la disposition dimensionne ses éléments enfants. En outre, il peut être nécessaire d’imbriquer des dispositions pour créer la disposition souhaitée.

L’illustration suivante montre les dispositions typiques qui peuvent être obtenues avec les classes de disposition Xamarin. Forms principales :

[![Les classes de disposition principales dans Xamarin. Forms](images/layouts.png "Classes de disposition Xamarin. Forms")](images/layouts-large.png#lightbox "Classes de disposition Xamarin. Forms")

## <a name="stacklayout"></a>StackLayout

Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) organise les éléments dans une pile unidimensionnelle, horizontalement ou verticalement. La propriété [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) spécifie la direction des éléments, et l’orientation par défaut est [`Vertical`](xref:Xamarin.Forms.StackOrientation). `StackLayout` est généralement utilisé pour réorganiser une sous-section de l’interface utilisateur sur une page.

Le code XAML suivant montre comment créer un [`StackLayout`](xref:Xamarin.Forms.StackLayout) vertical contenant trois objets [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

Dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout), si la taille d’un élément n’est pas explicitement définie, il se développe pour remplir la largeur disponible ou la hauteur si la propriété [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) est définie sur [`Horizontal`](xref:Xamarin.Forms.StackOrientation).

Une [`StackLayout`](xref:Xamarin.Forms.StackLayout) est souvent utilisée comme une disposition parente, qui contient d’autres dispositions enfants. Toutefois, un `StackLayout` ne doit pas être utilisé pour reproduire une disposition [`Grid`](xref:Xamarin.Forms.Grid) à l’aide d’une combinaison d’objets `StackLayout`. Le code suivant montre un exemple de cette mauvaise pratique :

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

Cela ne sert à rien, car des calculs de dispositions inutiles sont effectués. Au lieu de cela, la disposition souhaitée peut être mieux obtenue à l’aide d’un [`Grid`](xref:Xamarin.Forms.Grid).

> [!TIP]
> Lorsque vous utilisez un [`StackLayout`](xref:Xamarin.Forms.StackLayout), assurez-vous qu’un seul élément enfant est défini sur [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Cette propriété permet de garantir que l’enfant spécifié occupe le plus grand espace que l’élément `StackLayout` peut lui donner, et il est inutile d’effectuer ces calculs plusieurs fois.

Pour plus d’informations, consultez [Xamarin. Forms StackLayout](stack-layout.md).

## <a name="grid"></a>Grille

Un [`Grid`](xref:Xamarin.Forms.Grid) est utilisé pour afficher des éléments dans les lignes et les colonnes, qui peuvent avoir des tailles proportionnelles ou absolues. Les lignes et les colonnes d’une grille sont spécifiées avec les propriétés [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) et [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) .

Pour positionner des éléments dans des cellules [`Grid`](xref:Xamarin.Forms.Grid) spécifiques, utilisez les propriétés jointes [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) et [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) . Pour que les éléments s’étendent sur plusieurs lignes et colonnes, utilisez les propriétés jointes [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) et [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .

> [!NOTE]
> Une disposition de [`Grid`](xref:Xamarin.Forms.Grid) ne doit pas être confondue avec des tables et n’est pas destinée à présenter des données tabulaires. Contrairement aux tableaux HTML, un `Grid` est destiné à la disposition du contenu. Pour afficher des données tabulaires, envisagez d’utiliser un [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)ou [TableView](~/xamarin-forms/user-interface/tableview.md).

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
           Width="200" />
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
- La largeur de la première colonne est définie sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto)et est donc aussi large que nécessaire pour ses enfants. Dans ce cas, il s’agit d’unités indépendantes de l’appareil 200 de large pour tenir compte de la largeur du premier [`Label`](xref:Xamarin.Forms.Label).

L’espace peut être distribué au sein d’une colonne ou d’une ligne à l’aide du dimensionnement automatique, ce qui permet à des colonnes et des lignes de s’adapter à leur contenu. Pour ce faire, définissez la hauteur d’un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition)ou la largeur d’un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition)sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto). Le dimensionnement proportionnel peut également être utilisé pour distribuer l’espace disponible parmi les lignes et les colonnes de la grille par proportions pondérées. Pour ce faire, affectez à la hauteur d’un `RowDefinition`, ou à la largeur d’une `ColumnDefinition`, une valeur qui utilise l’opérateur `*`.

> [!CAUTION]
> Essayez de vous assurer que le moins de lignes et de colonnes possibles est définie sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto) taille. Pour chaque ligne ou colonne dimensionnée automatiquement, le moteur de disposition effectue des calculs de dispositions supplémentaires. Utilisez plutôt des lignes et colonnes de taille fixe si possible. Vous pouvez également définir des lignes et des colonnes pour occuper une quantité proportionnelle d’espace avec la valeur d’énumération [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) .

Pour plus d’informations, consultez [Xamarin. Forms Grid](grid.md).

## <a name="flexlayout"></a>FlexLayout

Un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) est semblable à un [`StackLayout`](xref:Xamarin.Forms.StackLayout) en ce sens qu’il affiche des éléments enfants horizontalement ou verticalement dans une pile. Toutefois, un `FlexLayout` peut également encapsuler ses enfants s’il y a trop d’éléments pour tenir sur une seule ligne ou colonne, et permet également un contrôle plus granulaire de la taille, de l’orientation et de l’alignement de ses éléments enfants.

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

- La propriété [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) est définie sur `Column`, ce qui entraîne la réorganisation des enfants de la `FlexLayout` dans une seule colonne d’éléments.
- La propriété [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) est définie sur `Center`, ce qui entraîne le centrage horizontal de chaque élément.
- La propriété [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) a la valeur `SpaceEvenly`, qui alloue tout l’espace vertical restant de manière égale entre tous les éléments, et au-dessus du premier élément, et sous le dernier élément.

Pour plus d’informations, consultez [Xamarin. Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) est utilisé pour positionner et dimensionner des éléments par rapport aux propriétés des éléments de disposition ou frères. Par défaut, un élément est positionné dans le coin supérieur gauche de la disposition. Un `RelativeLayout` peut être utilisé pour créer des interfaces utilisateur qui évoluent proportionnellement entre les tailles des appareils.

Dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), les positions et les tailles sont spécifiées en tant que contraintes. Les contraintes ont des propriétés [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) et [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) , qui peuvent être utilisées pour définir des positions et des tailles en tant que multiples (ou fractions) de propriétés d’autres objets, plus une constante. En outre, les constantes peuvent être négatives.

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) prend en charge le positionnement d’éléments en dehors de ses propres limites.

Le code XAML suivant montre comment organiser les éléments dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

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

- La [`BoxView`](xref:Xamarin.Forms.BoxView) bleue reçoit une taille explicite d’unités indépendantes du périphérique 50x50. Elle est placée dans le coin supérieur gauche de la disposition, qui est la position par défaut.
- La [`BoxView`](xref:Xamarin.Forms.BoxView) rouge reçoit une taille explicite d’unités indépendantes du périphérique 50x50. Elle est placée dans le coin supérieur droit de la disposition.
- La [`BoxView`](xref:Xamarin.Forms.BoxView) grise reçoit une largeur explicite de 15 unités indépendantes du périphérique, et la hauteur est définie sur 75% de la hauteur de son parent.
- La [`BoxView`](xref:Xamarin.Forms.BoxView) verte n’a pas de taille explicite. Sa position est définie par rapport au `BoxView` nommé `pole`.

> [!WARNING]
> Évitez d’utiliser un `RelativeLayout` dans la mesure du possible. Le processeur aurait considérablement plus de travail à effectuer.

Pour plus d’informations, consultez [Xamarin. Forms RelativeLayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) est utilisé pour positionner et dimensionner des éléments à l’aide de valeurs explicites, ou des valeurs relatives à la taille de la disposition. La position est spécifiée par l’angle supérieur gauche de l’enfant par rapport au coin supérieur gauche de la `AbsoluteLayout`.

Une [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) doit être considérée comme une disposition spéciale à utiliser uniquement lorsque vous pouvez imposer une taille aux enfants ou lorsque la taille de l’élément n’affecte pas la position des autres enfants. Une utilisation standard de cette disposition consiste à créer une superposition qui couvre la page avec d’autres contrôles, peut-être pour empêcher l’utilisateur d’interagir avec les contrôles normaux sur la page.

> [!IMPORTANT]
> Les propriétés `HorizontalOptions` et `VerticalOptions` n’ont aucun effet sur les enfants d’un `AbsoluteLayout`.

Dans un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), la propriété jointe [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) est utilisée pour spécifier la position horizontale, la position verticale, la largeur et la hauteur d’un élément. En outre, la propriété jointe [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) spécifie la manière dont les limites de disposition sont interprétées.

Le code XAML suivant montre comment organiser les éléments dans un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout):

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

- Chaque [`BoxView`](xref:Xamarin.Forms.BoxView) reçoit une taille explicite de 100x100 et s’affiche à la même position, centrée horizontalement.
- Le [`BoxView`](xref:Xamarin.Forms.BoxView) rouge est pivoté de 30 degrés et le `BoxView` vert est pivoté de 60 degrés.
- Sur chaque [`BoxView`](xref:Xamarin.Forms.BoxView), la propriété jointe [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) a la valeur `PositionProportional`, ce qui indique que la position est proportionnelle à l’espace restant après la largeur et la hauteur.

> [!CAUTION]
> Évitez d’utiliser la propriété [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) chaque fois que cela est possible, car elle permettra au moteur de disposition d’effectuer des calculs de disposition supplémentaires.

Pour plus d’informations, consultez [Xamarin. Forms AbsoluteLayout](absolute-layout.md).

## <a name="input-transparency"></a>Transparence de l’entrée

Chaque élément visuel a une propriété [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) utilisée pour définir si l’élément reçoit l’entrée. Sa valeur par défaut est `false`, garantissant que l’élément reçoit l’entrée.

Quand cette propriété est définie sur une classe de disposition, sa valeur est transférée aux éléments enfants. Par conséquent, la définition de la propriété [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) sur `true` sur une classe Layout entraînera la non-réception de tous les éléments dans la disposition.

## <a name="layout-performance"></a>Performances de disposition

Pour obtenir les meilleures performances possibles en matière de disposition, suivez les instructions fournies dans [optimiser les performances](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)de la disposition.

En outre, les performances de rendu de page peuvent également être améliorées à l’aide de la compression de disposition, qui supprime les dispositions spécifiées de l’arborescence d’éléments visuels. Pour plus d’informations, consultez compression de la [disposition](layout-compression.md).

## <a name="related-links"></a>Liens connexes

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Dispositions Xamarin. Forms (vidéo)](https://youtu.be/4HlLjTZQzjM)
- [Xamarin. Forms StackLayout](stack-layout.md)
- [Grille Xamarin. Forms](grid.md)
- [Xamarin. Forms FlexLayout](flex-layout.md)
- [Xamarin. Forms AbsoluteLayout](absolute-layout.md)
- [Xamarin. Forms RelativeLayout](relative-layout.md)
- [Optimiser les performances de disposition](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compression de disposition](layout-compression.md)
