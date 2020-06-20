---
title: Résumé du chapitre 17. Maîtrise de la grille
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 17. Maîtrise de la grille'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6dd13c0f592831c6488afac6727bcac734e9136a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136719"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Résumé du chapitre 17. Maîtrise de la grille

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

[`Grid`](xref:Xamarin.Forms.Grid)Est un mécanisme de disposition puissant qui réorganise ses enfants en lignes et colonnes de cellules. Contrairement à l’élément HTML similaire `table` , le `Grid` est uniquement à des fins de mise en page plutôt que de présentation.

## <a name="the-basic-grid"></a>Grille de base

`Grid`dérive de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) , qui définit une [`Children`](xref:Xamarin.Forms.Layout`1.Children) propriété qui `Grid` hérite de. Vous pouvez remplir cette collection en XAML ou dans le code.

### <a name="the-grid-in-xaml"></a>Grille en XAML

La définition d’un `Grid` en XAML commence généralement par remplir les [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) collections et de `Grid` avec les [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objets et. C’est ainsi que vous définissez le nombre de lignes et de colonnes du `Grid` , ainsi que leurs propriétés.

`RowDefinition`a une [`Height`](xref:Xamarin.Forms.RowDefinition.Height) propriété et `ColumnDefinition` a une [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propriété, à la fois de type [`GridLength`](xref:Xamarin.Forms.GridLength) , d’une structure.

En XAML, le [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) convertit des chaînes de texte simples en `GridLength` valeurs. En arrière-plan, le [ `GridLength` constructeur] (XREF : Xamarin.Forms . GridLength .% 23ctor (System. double, Xamarin.Forms . GridUnitType)) crée la `GridLength` valeur en fonction d’un nombre et d’une valeur de type [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) , une énumération avec trois membres :

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash;la largeur ou la hauteur est spécifiée en unités indépendantes du périphérique (un nombre en XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash;la hauteur ou la largeur est redimensionnée automatiquement en fonction du contenu de la cellule (« auto » en XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash;la hauteur ou la largeur restante est allouée proportionnellement (un nombre avec « \* », appelé *étoile*, en XAML)

Chaque enfant du `Grid` doit également recevoir une ligne et une colonne (explicitement ou implicitement). Les étendues de ligne et de colonne sont facultatives. Ils sont tous spécifiés à l’aide des propriétés de propriétés pouvant être liées attachées &mdash; définies par, `Grid` mais définies sur les enfants de `Grid` . `Grid`définit quatre propriétés pouvant être liées statiquement attachées :

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash;ligne de base zéro ; la valeur par défaut est 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash;colonne de base zéro ; la valeur par défaut est 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash;nombre de lignes sur lesquelles s’étend l’enfant ; la valeur par défaut est 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash;nombre de colonnes sur lesquelles s’étend l’enfant ; la valeur par défaut est 1

Dans le code, un programme peut utiliser huit méthodes statiques pour définir et obtenir ces valeurs :

- [ `Grid.SetRow` ] (XREF : Xamarin.Forms . Grid. SetRow ( Xamarin.Forms . BindableObject, System. Int32)) et [ `Grid.GetRow` ] (XREF : Xamarin.Forms . Grid. GetRow ( Xamarin.Forms . BindableObject))
- [ `Grid.SetColumn` ] (XREF : Xamarin.Forms . Grid. SetColumn ( Xamarin.Forms . BindableObject, System. Int32)) et [ `Grid.GetColumn` ] (XREF : Xamarin.Forms . Grid. GetColumn ( Xamarin.Forms . BindableObject))
- [ `Grid.SetRowSpan` ] (XREF : Xamarin.Forms . Grid. SetRowSpan ( Xamarin.Forms . BindableObject, System. Int32)) et [ `Grid.GetRowSpan` ] (XREF : Xamarin.Forms . Grid. GetRowSpan ( Xamarin.Forms . BindableObject))
- [ `Grid.SetColumnSpan` ] (XREF : Xamarin.Forms . Grid. SetColumnSpan ( Xamarin.Forms . BindableObject, System. Int32)) et [ `Grid.GetColumnSpan` ] (XREF : Xamarin.Forms . Grid. GetColumnSpan ( Xamarin.Forms . BindableObject))

Dans XAML, vous utilisez les attributs suivants pour définir ces valeurs :

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

L’exemple [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) illustre la création et l’initialisation d’un `Grid` en XAML.

`Grid`Hérite de la [`Padding`](xref:Xamarin.Forms.Layout.Padding) propriété de `Layout` et définit deux propriétés supplémentaires qui fournissent l’espacement entre les lignes et les colonnes :

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)a une valeur par défaut de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)a une valeur par défaut de 6

Les `RowDefinitions` `ColumnDefinitions` collections et ne sont pas strictement nécessaires. Si elle est absente, `Grid` crée des lignes et des colonnes pour les `Grid` enfants et leur attribue la valeur par défaut `GridLength` « \* » (étoile).

### <a name="the-grid-in-code"></a>La grille dans le code

L’exemple [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) montre comment créer et remplir un `Grid` dans le code. Vous pouvez définir les propriétés jointes pour chaque enfant directement, ou indirectement en appelant des `Add` méthodes supplémentaires telles que [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) définies par l’interface [Grid. IGridList <T> ](xref:Xamarin.Forms.Grid.IGridList`1) .

### <a name="the-grid-bar-chart"></a>Graphique à barres de grille

L’exemple [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) montre comment ajouter plusieurs `BoxView` éléments à un `Grid` à l’aide de la méthode Bulk [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) . Par défaut, ces `BoxView` éléments ont la même largeur. La hauteur de chaque `BoxView` peut ensuite être contrôlée pour ressembler à un graphique à barres.

L' `Grid` de l’exemple **GridBarChart** partage un `AbsoluteLayout` parent avec un initialement invisible `Frame` . Le programme définit également un `TapGestureRecognizer` sur chaque `BoxView` afin d’utiliser le `Frame` pour afficher des informations sur la barre taraudé.

### <a name="alignment-in-the-grid"></a>Alignement dans la grille

L’exemple [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) montre comment utiliser les `VerticalOptions` Propriétés et `HorizontalOptions` pour aligner des enfants dans une `Grid` cellule.

L’exemple [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) espace également les `Button` éléments centrés dans les `Grid` cellules.

### <a name="cell-dividers-and-borders"></a>Divisions et bordures de cellule

Le `Grid` n’inclut pas de fonctionnalité qui dessine des séparateurs de cellules ou des bordures. Toutefois, vous pouvez créer votre propre.

Le [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) montre comment définir des lignes et des colonnes supplémentaires spécifiquement pour les `BoxView` éléments légers pour imiter les lignes de séparation.

Le programme [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) ne crée pas de cellules supplémentaires mais aligne `BoxView` à la place les éléments dans chaque cellule pour imiter une bordure de cellule.

## <a name="almost-real-life-grid-examples"></a>Exemples de grille presque réaliste

L’exemple [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) utilise un `Grid` pour afficher un clavier :

[![Capture d’écran triple de la grille du pavé numérique](images/ch17fg12-small.png "Grille du pavé numérique")](images/ch17fg12-large.png#lightbox "Grille du pavé numérique")

### <a name="responding-to-orientation-changes"></a>Réponse aux changements d’orientation

`Grid`Peut aider à structurer un programme pour répondre aux changements d’orientation. L’exemple [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) illustre une technique qui déplace un élément entre une deuxième ligne d’un téléphone orienté portrait et la deuxième colonne d’un téléphone orienté paysage.

Le programme initialise les `Slider` éléments dans une plage comprise entre 0 et 255 et utilise des liaisons de données pour afficher la valeur des curseurs au format hexadécimal. Étant donné que les `Slider` valeurs sont à virgule flottante et que la chaîne de mise en forme .net pour hexadécimale ne fonctionne qu’avec des entiers, une [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) est utile.

## <a name="related-links"></a>Liens connexes

- [Chapitre 17 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Chapitre 17 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
