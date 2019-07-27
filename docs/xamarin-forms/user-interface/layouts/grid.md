---
title: Grille de Xamarin.Forms
description: Cet article explique comment utiliser la classe de grille de Xamarin.Forms pour présenter des vues dans les grilles, qui possèdent des lignes et colonnes.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 78a84fd8d4f380739a9f080a1a5aad7c5942d0c9
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511878"
---
# <a name="xamarinforms-grid"></a>Grille de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

[`Grid`](xref:Xamarin.Forms.Grid) prend en charge la réorganisation des vues en lignes et colonnes. Lignes et colonnes peuvent être définis pour avoir des tailles proportionnelles ou tailles absolues. Le `Grid` disposition ne doit pas être confondue avec des tables traditionnelles et n’est pas destinée à présenter les données tabulaires. `Grid` n’a pas le concept de ligne, colonne ou cellule mise en forme. Contrairement aux tables HTML, `Grid` est purement destinés à disposition du contenu.

[![](grid-images/layouts-sml.png "Dispositions Xamarin.Forms")](grid-images/layouts.png#lightbox "dispositions Xamarin.Forms")

Cet article couvre :

- **[Objectif](#purpose)**  &ndash; utilisations courantes pour `Grid`.
- **[Utilisation](#usage)**  &ndash; comment utiliser `Grid` pour atteindre votre conception de votre choix.
  - **[Lignes et colonnes](#rows-and-columns)**  &ndash; spécifier les lignes et colonnes pour le `Grid`.
  - **[Placer des vues](#placing-views-in-a-grid)**  &ndash; ajouter des vues à la grille à des lignes et colonnes spécifiques.
  - **[Espacement](#spacing)**  &ndash; configurer les espaces entre les lignes et colonnes.
  - **[Étendues](#spans)**  &ndash; configurer les éléments à s’étendre sur plusieurs lignes ou colonnes.

![](grid-images/grid.png "Exploration de la grille")

## <a name="purpose"></a>Objectif

`Grid` peut être utilisé pour organiser des vues dans une grille. Cela est utile dans plusieurs cas :

- Réorganiser les boutons dans une application de calculatrice
- Organisation de boutons/choix dans une grille, comme l’iOS ou Android écrans d’accueil
- Réorganisation des vues afin qu’ils soient de taille égale dans une dimension (comme dans certaines barres d’outils)

## <a name="usage"></a>Utilisation

Contrairement aux tables traditionnelles, `Grid` ne déduit pas le nombre et la taille des lignes et des colonnes à partir du contenu. Au lieu de cela, `Grid` a `RowDefinitions` et `ColumnDefinitions` collections. Ces derniers contiennent les définitions de combien de lignes et de colonnes seront disposés. Les vues sont ajoutés à `Grid` avec la ligne spécifiée et les index de colonne, qui identifient quels ligne et la colonne d’une vue doit être placée dans.

### <a name="rows-and-columns"></a>Lignes et colonnes

Les informations de ligne et de colonne sont stockées dans `Grid`de `RowDefinitions`  &  `ColumnDefinitions` propriétés, qui sont des collections de chaque de [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) et [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)des objets, respectivement. `RowDefinition` a une propriété unique, `Height`, et `ColumnDefinition` a une propriété unique, `Width`. Les options pour la hauteur et la largeur sont les suivantes :

- **Auto** &ndash; automatiquement les tailles en fonction du contenu dans la ligne ou colonne. Spécifié en tant que [ `GridUnitType.Auto` ](xref:Xamarin.Forms.GridUnitType) en C# ou en tant que `Auto` dans XAML.
- **Proportional(*)** &ndash; tailles des colonnes et des lignes sous forme de proportion de l’espace restant. Spécifié comme valeur et `GridUnitType.Star` en C# et en tant que `#*` dans XAML, avec `#` en cours de la valeur souhaitée. Spécification d’une ligne/colonne avec `*` entraîne remplir l’espace disponible.
- **Absolu** &ndash; tailles des colonnes et des lignes avec des valeurs de hauteur et largeur fixes, spécifiques. Spécifié comme valeur et `GridUnitType.Absolute` en C# et en tant que `#` dans XAML, avec `#` en cours de la valeur souhaitée.

> [!NOTE]
> Les valeurs de largeur pour les colonnes sont `*` définies par défaut dans Xamarin. Forms, ce qui garantit que la colonne remplira l’espace disponible. Les valeurs de hauteur pour les lignes sont également `*` définies par défaut.

Imaginez une application qui a besoin de trois lignes et deux colonnes. La ligne inférieure doit être exactement les 200px en hauteur et la ligne supérieure doit être deux fois la hauteur de la ligne du milieu. La colonne de gauche doit être suffisamment large pour s’ajuster le contenu et la colonne de droite doit remplir l’espace restant.

Dans XAML :

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

En C# :

```csharp
Grid grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Auto) });
```

### <a name="placing-views-in-a-grid"></a>Placer des vues dans une grille

Placement des vues dans un `Grid` vous devrez les ajouter en tant qu’enfants à la grille, puis spécifiez les lignes et colonnes qu’ils appartiennent dans.

Dans XAML, utilisez `Grid.Row` et `Grid.Column` sur chaque vue individuelle pour spécifier la sélection élective. Notez que `Grid.Row` et `Grid.Column` spécifier l’emplacement basé sur les listes de lignes et colonnes de base zéro. Cela signifie que dans une 4x4 grille, la cellule supérieure gauche est (0,0) et la cellule inférieure droite est (3,3).

Le `Grid` illustré ci-dessous contient quatre cellules :

![](grid-images/label-grid.png "Grille avec quatre affichages")

Dans XAML :

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

En C# :

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);
```

Le code ci-dessus crée une grille avec quatre étiquettes, deux colonnes et deux lignes. Notez que chaque étiquette aura la même taille et que les lignes seront développe pour utiliser tout l’espace disponible.

Dans l’exemple ci-dessus, les vues sont ajoutés à la [ `Grid.Children` ](xref:Xamarin.Forms.Grid.Children) à l’aide de la collection le [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) surcharge qui spécifie les arguments de gauche et supérieure. Lorsque vous utilisez le [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) surcharge qui spécifie de gauche, droite, haut et arguments du bas, tandis que la gauche et supérieurs arguments fait toujours référence à des cellules dans le [ `Grid` ](xref:Xamarin.Forms.Grid), droite et les arguments bas peuvent apparaître pour faire référence aux cellules qui sont en dehors de la `Grid`. Il s’agit, car l’argument de droite doit toujours être supérieure à l’argument de gauche, et l’argument bas doit toujours être supérieur à l’argument supérieur. L’exemple suivant montre le code équivalent à l’aide de deux `Add` surcharges :

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>Espacement

`Grid` possède des propriétés pour contrôler l’espacement entre les lignes et colonnes. Les propriétés suivantes sont disponibles pour la personnalisation de la `Grid`:

- **Espacement** &ndash; la quantité d’espace entre les colonnes. La valeur par défaut de cette propriété est 6.
- **RowSpacing** &ndash; la quantité d’espace entre les lignes. La valeur par défaut de cette propriété est 6.

Le XAML suivant spécifie un `Grid` avec deux colonnes, une ligne et 5 px d’espacement entre les colonnes :

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

En C# :

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>Étendues

Souvent lorsque vous travaillez avec une grille, il est un élément qui doit occuper plus d’une ligne ou colonne. Imaginez une application de calculatrice simple :

![](grid-images/calculator.png "Application de Calulator")

Notez que le bouton 0 s’étend sur deux colonnes, tout comme sur les calculatrices intégrés pour chaque plateforme. Pour cela, à l’aide de la `ColumnSpan` propriété qui spécifie le nombre de colonnes un élément doit occuper. Le XAML de ce bouton :

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

Et en C# :

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

Notez que dans le code, des méthodes statiques de la `Grid` classe sont utilisés pour effectuer des modifications de positionnement, y compris les modifications apportées aux `ColumnSpan` et `RowSpan`. Notez que, contrairement aux autres propriétés qui peuvent être définies à tout moment, les propriétés définies à l’aide de méthodes statiques doivent déjà être également dans la grille avant leur modification.

Le XAML complet pour l’application de calculatrice ci-dessus est la suivante :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Notez qu’à la fois l’étiquette en haut de la grille et le bouton zéro sont occuping et plusieurs colonnes. Bien qu’une disposition semblable peut être effectuée à l’aide de grilles imbriquées, le `ColumnSpan`  &  `RowSpan` approche est plus simple.

L’implémentation C# :

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```


## <a name="related-links"></a>Liens associés

- [Création d’applications mobiles avec Xamarin.Forms, chapitre 17](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [Grid](xref:Xamarin.Forms.Grid)
- [Disposition (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemple BusinessTumble (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
