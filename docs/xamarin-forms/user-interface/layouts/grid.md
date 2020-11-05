---
title: Xamarin.Forms Boutons
description: La Xamarin.Forms grille est une disposition qui organise ses enfants en lignes et colonnes de cellules.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 226c20acef7fc65cd0a4daf082c86e3c9c17bf54
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373773"
---
# <a name="no-locxamarinforms-grid"></a>Xamarin.Forms Boutons

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![::: No-Loc (Xamarin. Forms) ::: Grid](grid-images/layouts.png "::: No-Loc (Xamarin. Forms) ::: Grid")](grid-images/layouts-large.png#lightbox "::: No-Loc (Xamarin. Forms) ::: Grid")

[`Grid`](xref:Xamarin.Forms.Grid)Est une disposition qui organise ses enfants en lignes et en colonnes, qui peuvent avoir des tailles proportionnelles ou absolues. Par défaut, un `Grid` contient une ligne et une colonne. En outre, un `Grid` peut être utilisé comme une disposition parente qui contient d’autres dispositions enfants.

La [`Grid`](xref:Xamarin.Forms.Grid) disposition ne doit pas être confondue avec les tables et n’est pas destinée à présenter des données tabulaires. Contrairement aux tableaux HTML, un `Grid` est destiné à la disposition du contenu. Pour afficher des données tabulaires, envisagez d’utiliser un [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)ou [TableView](~/xamarin-forms/user-interface/tableview.md).

La [`Grid`](xref:Xamarin.Forms.Grid) classe définit les propriétés suivantes :

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), de type `int` , qui est une propriété jointe qui indique l’alignement de colonne d’une vue dans un parent `Grid` . La valeur par défaut de cette propriété est 0. Un rappel de validation garantit que lorsque la propriété est définie, sa valeur est supérieure ou égale à 0.
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions), de type [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) , est une liste d' [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objets qui définissent la largeur des colonnes de la grille.
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing), de type `double` , indique la distance entre les colonnes de la grille. La valeur par défaut de cette propriété est 6 unités indépendantes du périphérique.
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty), de type `int` , qui est une propriété jointe qui indique le nombre total de colonnes qu’une vue couvre dans un parent `Grid` . La valeur par défaut de cette propriété est 1. Un rappel de validation garantit que lorsque la propriété est définie, sa valeur est supérieure ou égale à 1.
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty), de type `int` , qui est une propriété jointe qui indique l’alignement de ligne d’une vue dans un parent `Grid` . La valeur par défaut de cette propriété est 0. Un rappel de validation garantit que lorsque la propriété est définie, sa valeur est supérieure ou égale à 0.
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), de type [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) , est une liste d' [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) objets qui définissent la hauteur des lignes de la grille.
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing), de type `double` , indique la distance entre les lignes de la grille. La valeur par défaut de cette propriété est 6 unités indépendantes du périphérique.
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty), de type `int` , qui est une propriété jointe qui indique le nombre total de lignes qu’une vue couvre dans un parent `Grid` . La valeur par défaut de cette propriété est 1. Un rappel de validation garantit que lorsque la propriété est définie, sa valeur est supérieure ou égale à 1.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données et un style.

La [`Grid`](xref:Xamarin.Forms.Grid) classe dérive de la `Layout<T>` classe, qui définit une `Children` propriété de type `IList<T>` . La `Children` propriété est du `ContentProperty` de la `Layout<T>` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

> [!TIP]
> Pour obtenir les meilleures performances possibles en matière de disposition, suivez les instructions fournies dans [optimiser les performances](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)de la disposition.

## <a name="rows-and-columns"></a>Lignes et colonnes

Par défaut, un [`Grid`](xref:Xamarin.Forms.Grid) contient une ligne et une colonne :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

Dans cet exemple, le [`Grid`](xref:Xamarin.Forms.Grid) contient un seul enfant [`Label`](xref:Xamarin.Forms.Label) qui est automatiquement positionné à un seul emplacement :

[![Capture d’écran d’une disposition de grille par défaut](grid-images/default.png "Disposition de grille par défaut")](grid-images/default-large.png#lightbox "Disposition de grille par défaut")

Le comportement de disposition d’un [`Grid`](xref:Xamarin.Forms.Grid) peut être défini avec [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) les [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) Propriétés et, qui sont respectivement des collections d' [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objets et. Ces collections définissent les caractéristiques de ligne et de colonne d’un `Grid` et doivent contenir un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objet pour chaque ligne du `Grid` , et un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objet pour chaque colonne dans le `Grid` .

La [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) classe définit une [`Height`](xref:Xamarin.Forms.RowDefinition.Height) propriété, de type [`GridLength`](xref:Xamarin.Forms.GridLength) , et la [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) classe définit une [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propriété, de type [`GridLength`](xref:Xamarin.Forms.GridLength) . Le [`GridLength`](xref:Xamarin.Forms.GridLength) struct spécifie une hauteur de ligne ou une largeur de colonne en termes d' [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) énumération, qui a trois membres :

- `Absolute` : la hauteur de ligne ou la largeur de colonne est une valeur dans les unités indépendantes du périphérique (un nombre en XAML).
- `Auto` : la hauteur de ligne ou la largeur de colonne est automatiquement redimensionnée en fonction du contenu de la cellule ( `Auto` en XAML).
- `Star` : la hauteur de ligne ou la largeur de colonne restante est allouée proportionnellement (nombre suivi `*` de en XAML).

Une [`Grid`](xref:Xamarin.Forms.Grid) ligne avec une `Height` propriété de `Auto` limite la hauteur des vues dans cette ligne de la même façon qu’une verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) . De même, une colonne avec une `Width` propriété de `Auto` fonctionne comme un horizontal `StackLayout` .

> [!CAUTION]
> Essayez de vous assurer que le moins de lignes et de colonnes possibles est définie sur [`Auto`](xref:Xamarin.Forms.GridLength.Auto) Size. Pour chaque ligne ou colonne dimensionnée automatiquement, le moteur de disposition effectue des calculs de dispositions supplémentaires. Utilisez plutôt des lignes et colonnes de taille fixe si possible. Vous pouvez également définir des lignes et des colonnes pour occuper une quantité proportionnelle d’espace avec la [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valeur d’énumération.

Le code XAML suivant montre comment créer un [`Grid`](xref:Xamarin.Forms.Grid) avec trois lignes et deux colonnes :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Dans cet exemple, [`Grid`](xref:Xamarin.Forms.Grid) a une hauteur globale qui correspond à la hauteur de la page. Le `Grid` sait que la hauteur de la troisième ligne est 100 unités indépendantes du périphérique. Il soustrait cette hauteur de sa propre hauteur et alloue la hauteur restante proportionnellement entre la première et la deuxième ligne en fonction du nombre avant l’étoile. Dans cet exemple, la hauteur de la première ligne est deux fois supérieure à celle de la deuxième ligne.

Les deux [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objets définissent [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) à `*` , qui est le même que `1*` , ce qui signifie que la largeur de l’écran est divisée de manière égale sous les deux colonnes.

> [!IMPORTANT]
> La valeur par défaut de la [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) propriété est `*` . De même, la valeur par défaut de la [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propriété est `*` . Par conséquent, il n’est pas nécessaire de définir ces propriétés dans les cas où ces valeurs par défaut sont acceptables.

Les vues enfants peuvent être positionnées dans des [`Grid`](xref:Xamarin.Forms.Grid) cellules spécifiques avec les [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) propriétés jointes et. En outre, pour que les vues enfants s’étendent sur plusieurs lignes et colonnes, utilisez les [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) propriétés jointes et.

Le code XAML suivant illustre la même [`Grid`](xref:Xamarin.Forms.Grid) définition et positionne également les vues enfants dans des `Grid` cellules spécifiques :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <BoxView Color="Green" />
        <Label Text="Row 0, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Column="1"
                 Color="Blue" />
        <Label Grid.Column="1"
               Text="Row 0, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Color="Teal" />
        <Label Grid.Row="1"
               Text="Row 1, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="Purple" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Row1, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Grid.ColumnSpan="2"
                 Color="Red" />
        <Label Grid.Row="2"
               Grid.ColumnSpan="2"
               Text="Row 2, Columns 0 and 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

> [!NOTE]
> Les `Grid.Row` `Grid.Column` Propriétés et sont toutes deux indexées à partir de 0. par conséquent, `Grid.Row="2"` fait référence à la troisième ligne alors que `Grid.Column="1"` fait référence à la deuxième colonne. En outre, ces deux propriétés ont une valeur par défaut de 0 et n’ont donc pas besoin d’être définies sur les vues enfants qui occupent la première ligne ou la première colonne d’un [`Grid`](xref:Xamarin.Forms.Grid) .

Dans cet exemple, les trois [`Grid`](xref:Xamarin.Forms.Grid) lignes sont occupées par [`BoxView`](xref:Xamarin.Forms.BoxView) des [`Label`](xref:Xamarin.Forms.Label) vues et. La troisième ligne est 100 unités indépendantes de l’appareil, les deux premières lignes occupant l’espace restant (la première ligne est deux fois plus élevée que la deuxième). Les deux colonnes sont égales en largeur et divisent le `Grid` en deux. Le `BoxView` de la troisième ligne s’étend sur les deux colonnes.

[![Capture d’écran d’une disposition de grille de base](grid-images/basic.png "Disposition de grille de base")](grid-images/basic-large.png#lightbox "Disposition de grille de base")

En outre, les vues enfants dans un [`Grid`](xref:Xamarin.Forms.Grid) peuvent partager des cellules. L’ordre dans lequel les enfants apparaissent dans le XAML est l’ordre dans lequel ils sont placés `Grid` . Dans l’exemple précédent, les [`Label`](xref:Xamarin.Forms.Label) objets sont visibles uniquement parce qu’ils sont affichés au-dessus des [`BoxView`](xref:Xamarin.Forms.BoxView) objets. Les `Label` objets ne sont pas visibles si les `BoxView` objets étaient affichés par-dessus.

Le code C# équivalent est :

```csharp
public class BasicGridPageCS : ContentPage
{
    public BasicGridPageCS()
    {
        Grid grid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition { Height = new GridLength(2, GridUnitType.Star) },
                new RowDefinition(),
                new RowDefinition { Height = new GridLength(100) }
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        // The BoxView and Label are in row 0 and column 0, and so only needs to be added to the
        // Grid.Children collection to get default row and column settings.
        grid.Children.Add(new BoxView
        {
            Color = Color.Green
        });
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        });

        // This BoxView and Label are in row 0 and column 1, which are specified as arguments
        // to the Add method.
        grid.Children.Add(new BoxView
        {
            Color = Color.Blue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 0);

        // Row 1
        // This BoxView and Label are in row 1 and column 0, which are specified as arguments
        // to the Add method overload.
        grid.Children.Add(new BoxView
        {
            Color = Color.Teal
        }, 0, 1, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row 1, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1, 1, 2); // These arguments indicate that that the child element goes in the column starting at 0 but ending before 1.
                        // They also indicate that the child element goes in the row starting at 1 but ending before 2.

        grid.Children.Add(new BoxView
        {
            Color = Color.Purple
        }, 1, 2, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row1, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 2, 1, 2);

        // Row 2
        // Alternatively, the BoxView and Label can be positioned in cells with the Grid.SetRow
        // and Grid.SetColumn methods.
        BoxView boxView = new BoxView { Color = Color.Red };
        Grid.SetRow(boxView, 2);
        Grid.SetColumnSpan(boxView, 2);
        Label label = new Label
        {
            Text = "Row 2, Column 0 and 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        };
        Grid.SetRow(label, 2);
        Grid.SetColumnSpan(label, 2);

        grid.Children.Add(boxView);
        grid.Children.Add(label);

        Title = "Basic Grid demo";
        Content = grid;
    }
}
```

Dans le code, pour spécifier la hauteur d’un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objet et la largeur d’un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objet, vous utilisez des valeurs de la [`GridLength`](xref:Xamarin.Forms.GridLength) structure, souvent en association avec l' [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) énumération.

L’exemple de code ci-dessus montre également plusieurs approches différentes pour l’ajout d’enfants au [`Grid`](xref:Xamarin.Forms.Grid) et la spécification des cellules dans lesquelles ils résident. Lors de l’utilisation de la `Add` surcharge qui spécifie les arguments *gauche* , *droite* , *haut* et *bas* , tandis que les arguments *gauche* et *supérieur* font toujours référence aux cellules dans le `Grid` , les arguments de *droite* et du *bas* semblent faire référence aux cellules qui se trouvent en dehors de `Grid` . En effet, l’argument de *droite* doit toujours être supérieur à l’argument de *gauche* , et l’argument *inférieur* doit toujours être supérieur à l’argument du *haut* . L’exemple suivant, qui suppose un 2x2 `Grid` , affiche le code équivalent à l’aide des deux `Add` surcharges :

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);           // first column, first row
grid.Children.Add(topRight, 1, 0);          // second column, first tow
grid.Children.Add(bottomLeft, 0, 1);        // first column, second row
grid.Children.Add(bottomRight, 1, 1);       // second column, second row

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);     // first column, first row
grid.Children.Add(topRight, 1, 2, 0, 1);    // second column, first tow
grid.Children.Add(bottomLeft, 0, 1, 1, 2);  // first column, second row
grid.Children.Add(bottomRight, 1, 2, 1, 2); // second column, second row
```

> [!NOTE]
> En outre, les vues enfants peuvent être ajoutées à un [`Grid`](xref:Xamarin.Forms.Grid) avec [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) les [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) méthodes et, qui ajoutent des enfants à une seule ligne ou colonne `Grid` . Le `Grid` s’étend ensuite dans des lignes ou des colonnes à mesure que ces appels sont effectués, ainsi que le positionnement automatique des enfants dans les cellules appropriées.

### <a name="simplify-row-and-column-definitions"></a>Simplifier les définitions de lignes et de colonnes

En XAML, les caractéristiques de ligne et de colonne d’un [`Grid`](xref:Xamarin.Forms.Grid) peuvent être spécifiées à l’aide d’une syntaxe simplifiée qui évite d’avoir à définir des [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objets et pour chaque ligne et colonne. Au lieu de cela, les [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) Propriétés et peuvent être définies sur des chaînes contenant des valeurs délimitées par des virgules [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) , à partir de laquelle les convertisseurs de type intégrés à la Xamarin.Forms création `RowDefinition` et aux `ColumnDefinition` objets :

```xaml
<Grid RowDefinitions="1*, Auto, 25, 14, 20"
      ColumnDefinitions="*, 2*, Auto, 300">
    ...
</Grid>
```

Dans cet exemple, [`Grid`](xref:Xamarin.Forms.Grid) a cinq lignes et quatre colonnes. Les troisième, quatrième et cinquième lignes sont définies sur des hauteurs absolues, la deuxième ligne étant automatiquement dimensionnée sur son contenu. La hauteur restante est ensuite allouée à la première ligne.

La colonne de sens est définie sur une largeur absolue, avec la troisième colonne de redimensionnement automatique à son contenu. La largeur restante est allouée proportionnellement entre la première et la deuxième colonne en fonction du nombre avant l’étoile. Dans cet exemple, la largeur de la deuxième colonne est deux fois supérieure à celle de la première colonne (car `*` est identique à `1*` ).

## <a name="space-between-rows-and-columns"></a>Espace entre les lignes et les colonnes

Par défaut, [`Grid`](xref:Xamarin.Forms.Grid) les lignes sont séparées par 6 unités d’espace indépendantes du périphérique. De même, `Grid` les colonnes sont séparées par 6 unités d’espace indépendantes du périphérique. Vous pouvez modifier ces valeurs par défaut en définissant les [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) Propriétés et, respectivement :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridSpacingPage"
             Title="Grid spacing demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        ..
    </Grid>
</ContentPage>
```

Cet exemple crée un [`Grid`](xref:Xamarin.Forms.Grid) qui n’a pas d’espace entre ses lignes et ses colonnes :

[![Capture d’écran de la grille sans espacement entre les cellules](grid-images/spacing.png "Grille sans espace entre les cellules")](grid-images/spacing-large.png#lightbox "Grille sans espace entre les cellules")

> [!TIP]
> Les [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) Propriétés et peuvent être définies sur des valeurs négatives pour que le contenu des cellules se chevauche.

Le code C# équivalent est :

```csharp
public GridSpacingPageCS()
{
    Grid grid = new Grid
    {
        RowSpacing = 0,
        ColumnSpacing = 0,
        // ...
    };
    // ...

    Content = grid;
}
```

## <a name="alignment"></a>Alignement

Les vues enfants dans un [`Grid`](xref:Xamarin.Forms.Grid) peuvent être positionnées dans leurs cellules par les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et. Ces propriétés peuvent être définies sur les champs suivants à partir de la [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) structure :

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> Les `AndExpands` champs du [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct s’appliquent uniquement aux [`StackLayout`](xref:Xamarin.Forms.StackLayout) objets.

Le code XAML suivant crée un [`Grid`](xref:Xamarin.Forms.Grid) avec neuf cellules de taille égale et place un [`Label`](xref:Xamarin.Forms.Label) dans chaque cellule avec un alignement différent :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridAlignmentPage"
             Title="Grid alignment demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <BoxView Color="AliceBlue" />
        <Label Text="Upper left"
               HorizontalOptions="Start"
               VerticalOptions="Start" />
        <BoxView Grid.Column="1"
                 Color="LightSkyBlue" />
        <Label Grid.Column="1"
               Text="Upper center"
               HorizontalOptions="Center"
               VerticalOptions="Start"/>
        <BoxView Grid.Column="2"
                 Color="CadetBlue" />
        <Label Grid.Column="2"
               Text="Upper right"
               HorizontalOptions="End"
               VerticalOptions="Start" />
        <BoxView Grid.Row="1"
                 Color="CornflowerBlue" />
        <Label Grid.Row="1"
               Text="Center left"
               HorizontalOptions="Start"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="DodgerBlue" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Center center"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="2"
                 Color="DarkSlateBlue" />
        <Label Grid.Row="1"
               Grid.Column="2"
               Text="Center right"
               HorizontalOptions="End"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Color="SteelBlue" />
        <Label Grid.Row="2"
               Text="Lower left"
               HorizontalOptions="Start"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="1"
                 Color="LightBlue" />
        <Label Grid.Row="2"
               Grid.Column="1"
               Text="Lower center"
               HorizontalOptions="Center"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="2"
                 Color="BlueViolet" />
        <Label Grid.Row="2"
               Grid.Column="2"
               Text="Lower right"
               HorizontalOptions="End"
               VerticalOptions="End" />
    </Grid>
</ContentPage>
```

Dans cet exemple, les [`Label`](xref:Xamarin.Forms.Label) objets de chaque ligne sont tous alignés de manière identique verticalement, mais utilisent des alignements horizontaux différents. Cela peut également être considéré comme les `Label` objets de chaque colonne qui sont alignés de façon identique horizontalement, mais avec différents alignements verticaux :

[![Capture d’écran de l’alignement des cellules dans une grille](grid-images/alignment.png "Alignement des cellules dans une grille")](grid-images/alignment-large.png#lightbox "Alignement des cellules dans une grille")

Le code C# équivalent est :

```csharp
public class GridAlignmentPageCS : ContentPage
{
    public GridAlignmentPageCS()
    {
        Grid grid = new Grid
        {
            RowSpacing = 0,
            ColumnSpacing = 0,
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        grid.Children.Add(new BoxView
        {
            Color = Color.AliceBlue
        });
        grid.Children.Add(new Label
        {
            Text = "Upper left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        });

        grid.Children.Add(new BoxView
        {
            Color = Color.LightSkyBlue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Start
        }, 1, 0);

        grid.Children.Add(new BoxView
        {
            Color = Color.CadetBlue
        }, 2, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Start
        }, 2, 0);

        // Row 1
        grid.Children.Add(new BoxView
        {
            Color = Color.CornflowerBlue
        }, 0, 1);
        grid.Children.Add(new Label
        {
            Text = "Center left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DodgerBlue
        }, 1, 1);
        grid.Children.Add(new Label
        {
            Text = "Center center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DarkSlateBlue
        }, 2, 1);
        grid.Children.Add(new Label
        {
            Text = "Center right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Center
        }, 2, 1);

        // Row 2
        grid.Children.Add(new BoxView
        {
            Color = Color.SteelBlue
        }, 0, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.End
        }, 0, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.LightBlue
        }, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.End
        }, 1, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.BlueViolet
        }, 2, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.End
        }, 2, 2);

        Title = "Grid alignment demo";
        Content = grid;
    }
}
```

## <a name="nested-grid-objects"></a>Objets de grille imbriquée

Un [`Grid`](xref:Xamarin.Forms.Grid) peut être utilisé comme une disposition parente qui contient des objets enfants imbriqués `Grid` ou d’autres dispositions enfants. Lors de l’imbrication `Grid` d’objets, les `Grid.Row` `Grid.Column` `Grid.RowSpan` `Grid.ColumnSpan` propriétés jointes,, et font toujours référence à la position des vues au sein de leur parent `Grid` .

Le code XAML suivant montre un exemple d’imbrication d' [`Grid`](xref:Xamarin.Forms.Grid) objets :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:converters="clr-namespace:GridDemos.Converters"
             x:Class="GridDemos.Views.ColorSlidersGridPage"
             Title="Nested Grids demo">

    <ContentPage.Resources>
        <converters:DoubleToIntConverter x:Key="doubleToInt" />

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment"
                    Value="Center" />
        </Style>
    </ContentPage.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <BoxView x:Name="boxView"
                 Color="Black" />
        <Grid Grid.Row="1"
              Margin="20">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Slider x:Name="redSlider"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="1"
                   Text="{Binding Source={x:Reference redSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0}'}" />
            <Slider x:Name="greenSlider"
                    Grid.Row="2"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="3"
                   Text="{Binding Source={x:Reference greenSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0}'}" />
            <Slider x:Name="blueSlider"
                    Grid.Row="4"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="5"
                   Text="{Binding Source={x:Reference blueSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Blue = {0}'}" />
        </Grid>
    </Grid>
</ContentPage>
```

Dans cet exemple, la [`Grid`](xref:Xamarin.Forms.Grid) disposition racine contient un [`BoxView`](xref:Xamarin.Forms.BoxView) dans sa première ligne et un enfant `Grid` dans sa deuxième ligne. L’enfant `Grid` contient [`Slider`](xref:Xamarin.Forms.Slider) des objets qui manipulent la couleur affichée par le `BoxView` , et les [`Label`](xref:Xamarin.Forms.Label) objets qui affichent la valeur de chacun `Slider` :

[![Capture d’écran des grilles imbriquées](grid-images/nesting.png "Objets de grille imbriquée")](grid-images/nesting-large.png#lightbox "Objets de grille imbriquée")

> [!IMPORTANT]
> Plus vous imbriquez des [`Grid`](xref:Xamarin.Forms.Grid) objets et d’autres dispositions, plus les dispositions imbriquées auront un impact sur les performances. Pour plus d’informations, consultez [choisir la disposition correcte](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

Le code C# équivalent est :

```csharp
public class ColorSlidersGridPageCS : ContentPage
{
    BoxView boxView;
    Slider redSlider;
    Slider greenSlider;
    Slider blueSlider;

    public ColorSlidersGridPageCS()
    {
        // Create an implicit style for the Labels
        Style labelStyle = new Style(typeof(Label))
        {
            Setters =
            {
                new Setter { Property = Label.HorizontalTextAlignmentProperty, Value = TextAlignment.Center }
            }
        };
        Resources.Add(labelStyle);

        // Root page layout
        Grid rootGrid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition()
            }
        };

        boxView = new BoxView { Color = Color.Black };
        rootGrid.Children.Add(boxView);

        // Child page layout
        Grid childGrid = new Grid
        {
            Margin = new Thickness(20),
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            }
        };

        DoubleToIntConverter doubleToInt = new DoubleToIntConverter();

        redSlider = new Slider();
        redSlider.ValueChanged += OnSliderValueChanged;
        childGrid.Children.Add(redSlider);

        Label redLabel = new Label();
        redLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Red = {0}", source: redSlider));
        Grid.SetRow(redLabel, 1);
        childGrid.Children.Add(redLabel);

        greenSlider = new Slider();
        greenSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(greenSlider, 2);
        childGrid.Children.Add(greenSlider);

        Label greenLabel = new Label();
        greenLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Green = {0}", source: greenSlider));
        Grid.SetRow(greenLabel, 3);
        childGrid.Children.Add(greenLabel);

        blueSlider = new Slider();
        blueSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(blueSlider, 4);
        childGrid.Children.Add(blueSlider);

        Label blueLabel = new Label();
        blueLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Blue = {0}", source: blueSlider));
        Grid.SetRow(blueLabel, 5);
        childGrid.Children.Add(blueLabel);

        // Place the child Grid in the root Grid
        rootGrid.Children.Add(childGrid, 0, 1);

        Title = "Nested Grids demo";
        Content = rootGrid;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        boxView.Color = new Color(redSlider.Value, greenSlider.Value, blueSlider.Value);
    }
}
```

## <a name="related-links"></a>Liens connexes

- [Démonstrations de grille (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [Options de disposition dans Xamarin.Forms](layout-options.md)
- [Choisir une Xamarin.Forms disposition](choose-layout.md)
- [Améliorer les Xamarin.Forms performances des applications](~/xamarin-forms/deploy-test/performance.md)