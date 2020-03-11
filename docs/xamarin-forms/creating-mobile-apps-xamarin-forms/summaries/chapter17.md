---
title: Résumé du chapitre 17. Maîtrise de la grille
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 17. Maîtrise de la grille'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760626"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Résumé du chapitre 17. Maîtrise de la grille

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

Le [`Grid`](xref:Xamarin.Forms.Grid) est un mécanisme de disposition puissant qui réorganise ses enfants en lignes et colonnes de cellules. Contrairement à l’élément de `table` HTML similaire, le `Grid` est uniquement destiné à la disposition plutôt qu’à la présentation.

## <a name="the-basic-grid"></a>La grille de base

`Grid` dérive de [`Layout<View>`](xref:Xamarin.Forms.Layout`1), qui définit une propriété [`Children`](xref:Xamarin.Forms.Layout`1.Children) qui `Grid` hérite. Vous pouvez remplir cette collection dans XAML ou de code.

### <a name="the-grid-in-xaml"></a>La grille dans XAML

La définition d’une `Grid` en XAML commence généralement par remplir le [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) et [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) collections du `Grid` avec des objets [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) et [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) . C’est ainsi que vous définissez le nombre de lignes et de colonnes du `Grid`, ainsi que leurs propriétés.

`RowDefinition` a une propriété [`Height`](xref:Xamarin.Forms.RowDefinition.Height) et `ColumnDefinition` a une propriété [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) , de type [`GridLength`](xref:Xamarin.Forms.GridLength), une structure.

En XAML, le [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) convertit des chaînes de texte simples en valeurs `GridLength`. En arrière-plan, le [constructeur`GridLength`](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) crée la valeur `GridLength` en fonction d’un nombre et d’une valeur de type [`GridUnitType`](xref:Xamarin.Forms.GridUnitType), une énumération avec trois membres :

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; la largeur ou la hauteur est spécifiée en unités indépendantes du périphérique (un nombre en XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; la hauteur ou la largeur est redimensionnée automatiquement en fonction du contenu de la cellule (« auto » en XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; la hauteur ou la largeur restante est allouée proportionnellement (un nombre avec «\*», appelé *Star*, en XAML)

Chaque enfant du `Grid` doit également recevoir une ligne et une colonne (explicitement ou implicitement). Ligne s’étend et étendues de colonne sont facultatifs. Ils sont tous spécifiés à l’aide des propriétés pouvant être liées, &mdash; les propriétés définies par le `Grid` mais définies sur les enfants de l' `Grid`. `Grid` définit quatre propriétés pouvant être liées statiquement attachées :

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; la ligne de base zéro ; la valeur par défaut est 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; la colonne de base zéro ; la valeur par défaut est 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; le nombre de lignes sur lesquelles s’étend l’enfant ; la valeur par défaut est 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; le nombre de colonnes sur lesquelles s’étend l’enfant ; la valeur par défaut est 1

Dans le code, un programme peut utiliser les huit méthodes statiques pour définir et obtenir ces valeurs :

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) et [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) et [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) et [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) et [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

Dans XAML, vous utilisez les attributs suivants pour la définition de ces valeurs :

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

L’exemple [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) montre comment créer et initialiser un `Grid` en XAML.

Le `Grid` hérite de la propriété [`Padding`](xref:Xamarin.Forms.Layout.Padding) de `Layout` et définit deux propriétés supplémentaires qui fournissent l’espacement entre les lignes et les colonnes :

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) a une valeur par défaut de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) a une valeur par défaut de 6

Les collections `RowDefinitions` et `ColumnDefinitions` ne sont pas strictement nécessaires. Si elle est absente, le `Grid` crée des lignes et des colonnes pour les enfants `Grid` et leur donne une `GridLength` par défaut «\*» (Star).

### <a name="the-grid-in-code"></a>La grille dans le code

L’exemple [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) montre comment créer et remplir un `Grid` dans le code. Vous pouvez définir les propriétés jointes pour chaque enfant directement, ou indirectement en appelant des méthodes `Add` supplémentaires telles que [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) définies par l’interface [<T>Grid. IGridList](xref:Xamarin.Forms.Grid.IGridList`1) .

### <a name="the-grid-bar-chart"></a>Le graphique à barres grille

L’exemple [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) montre comment ajouter plusieurs éléments `BoxView` à un `Grid` à l’aide de la méthode Bulk [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) . Par défaut, ces `BoxView` éléments ont la même largeur. La hauteur de chaque `BoxView` peut ensuite être contrôlée pour ressembler à un graphique à barres.

L' `Grid` de l’exemple **GridBarChart** partage un parent `AbsoluteLayout` avec un `Frame`invisible initialement. Le programme définit également un `TapGestureRecognizer` sur chaque `BoxView` pour utiliser le `Frame` pour afficher des informations sur la barre taraudé.

### <a name="alignment-in-the-grid"></a>Alignement de la grille

L’exemple [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) montre comment utiliser les propriétés `VerticalOptions` et `HorizontalOptions` pour aligner des enfants dans une cellule `Grid`.

L’exemple [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) espace `Button` éléments centrés dans `Grid` cellules.

### <a name="cell-dividers-and-borders"></a>Les bordures et les séparateurs de cellule

La `Grid` n’inclut pas de fonctionnalité qui dessine des séparateurs de cellules ou des bordures. Toutefois, vous pouvez créer vos propres.

Le [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) montre comment définir des lignes et des colonnes supplémentaires spécifiquement pour les éléments de `BoxView` minces pour imiter les lignes de séparation.

Le programme [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) ne crée pas de cellules supplémentaires mais aligne à la place `BoxView` éléments dans chaque cellule pour imiter une bordure de cellule.

## <a name="almost-real-life-grid-examples"></a>Exemples de grille presque réel

L’exemple [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) utilise un `Grid` pour afficher un clavier :

[![Capture d’écran triple de la grille du pavé numérique](images/ch17fg12-small.png "Grille du pavé numérique")](images/ch17fg12-large.png#lightbox "Grille du pavé numérique")

### <a name="responding-to-orientation-changes"></a>Répondre aux changements d’orientation

La `Grid` peut aider à structurer un programme pour répondre aux changements d’orientation. L’exemple [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) illustre une technique qui déplace un élément entre une deuxième ligne d’un téléphone orienté portrait et la deuxième colonne d’un téléphone orienté paysage.

Le programme Initialise `Slider` éléments dans une plage comprise entre 0 et 255 et utilise des liaisons de données pour afficher la valeur des curseurs au format hexadécimal. Étant donné que les valeurs `Slider` sont à virgule flottante et que la chaîne de mise en forme .NET pour hexadécimale ne fonctionne qu’avec des entiers, une classe [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) vous aide.

## <a name="related-links"></a>Liens connexes

- [Chapitre 17 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Chapitre 17 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
