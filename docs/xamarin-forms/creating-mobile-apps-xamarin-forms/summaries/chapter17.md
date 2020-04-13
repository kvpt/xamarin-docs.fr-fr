---
title: Résumé du chapitre 17. Maîtrise de la grille
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 17. Maîtrise de la grille'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760626"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Résumé du chapitre 17. Maîtrise de la grille

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

Il [`Grid`](xref:Xamarin.Forms.Grid) s’agit d’un puissant mécanisme de mise en page qui arrange ses enfants en rangées et colonnes de cellules. Contrairement à `table` l’élément `Grid` HTML similaire, le est uniquement à des fins de mise en page plutôt que de présentation.

## <a name="the-basic-grid"></a>La grille de base

`Grid`dérive de [`Layout<View>`](xref:Xamarin.Forms.Layout`1), qui [`Children`](xref:Xamarin.Forms.Layout`1.Children) définit `Grid` une propriété qui hérite. Vous pouvez remplir cette collection en XAML ou en code.

### <a name="the-grid-in-xaml"></a>La grille dans XAML

La définition `Grid` d’un dans XAML [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) commence généralement `Grid` [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) par [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) remplir le et les collections de la avec et des objets. C’est ainsi que vous établissez le `Grid`nombre de lignes et de colonnes de la , et leurs propriétés.

`RowDefinition`a [`Height`](xref:Xamarin.Forms.RowDefinition.Height) une `ColumnDefinition` propriété [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) et a une [`GridLength`](xref:Xamarin.Forms.GridLength)propriété, à la fois de type , une structure.

Dans XAML, [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) le convertit les `GridLength` chaînes de texte simples en valeurs. Dans les coulisses, le `GridLength` [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) [ `GridLength` constructeur](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) crée la valeur basée sur un nombre et une valeur de type, un recensement avec trois membres:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash; la largeur ou la hauteur est spécifiée dans les unités indépendantes de l’appareil (un numéro dans XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash; la hauteur ou la largeur est autosized en fonction du contenu cellulaire ("Auto" dans XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash; restes de hauteur ou de largeur est\*alloué proportionnellement (un nombre avec " ", appelé *étoile*, dans XAML)

Chaque enfant `Grid` de la doit également être attribué une rangée et une colonne (explicitement ou implicitement). Les travées de ligne et les travées de colonne sont facultatives. Ceux-ci sont tous spécifiés en utilisant des propriétés liantes attachées &mdash; qui sont définies par les `Grid` enfants mais fixés sur les enfants de la `Grid`. `Grid`définit quatre propriétés liantes attachées statiques :

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash; la ligne zéro; par défaut est de 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash; la colonne zéro; par défaut est de 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash; le nombre de rangées que l’enfant s’étend; par défaut est de 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash; le nombre de colonnes que l’enfant s’étend; par défaut est de 1

En code, un programme peut utiliser huit méthodes statiques pour définir et obtenir ces valeurs :

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32))Et[`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32))Et[`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32))Et[`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32))Et[`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

Dans XAML, vous utilisez les attributs suivants pour définir ces valeurs :

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[**L’échantillon SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) démontre la `Grid` création et l’initialisation d’un XAML.

Le `Grid` hérite [`Padding`](xref:Xamarin.Forms.Layout.Padding) de `Layout` la propriété et définit deux propriétés supplémentaires qui fournissent l’espacement entre les rangées et les colonnes:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)a une valeur par défaut de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)a une valeur par défaut de 6

Les `RowDefinitions` `ColumnDefinitions` collections et les collections ne sont pas strictement requises. En cas `Grid` d’absence, le `Grid` crée des lignes et `GridLength` des\*colonnes pour les enfants et leur donne à tous un défaut de " (étoile).

### <a name="the-grid-in-code"></a>La grille dans le code

[**L’échantillon GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) montre comment créer `Grid` et remplir un code. Vous pouvez définir les propriétés ci-jointes pour `Add` chaque [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) enfant directement, ou indirectement en appelant des méthodes supplémentaires telles que définies par l’interface [Grid.IGridList.<T> ](xref:Xamarin.Forms.Grid.IGridList`1)

### <a name="the-grid-bar-chart"></a>Le graphique à barres Grid

[**L’échantillon GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) montre `BoxView` comment ajouter `Grid` plusieurs [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) éléments à une méthode en vrac. Par défaut, `BoxView` ces éléments ont une largeur égale. La hauteur `BoxView` de chacun peut alors être contrôlée pour ressembler à un graphique à barres.

L’échantillon `Grid` de **GridBarChart** partage un `AbsoluteLayout` `Frame`parent avec un initialement invisible . Le programme définit `TapGestureRecognizer` également `BoxView` un `Frame` sur chacun pour utiliser les informations pour afficher des informations sur la barre sur écoute.

### <a name="alignment-in-the-grid"></a>Alignement dans la grille

[**L’échantillon GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) montre comment `VerticalOptions` `HorizontalOptions` utiliser les propriétés `Grid` et les propriétés pour aligner les enfants dans une cellule.

Les [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) échantillonnent également des éléments d’espace `Button` centrés dans `Grid` les cellules.

### <a name="cell-dividers-and-borders"></a>Diviseurs et frontières cellulaires

Il `Grid` ne s’agit pas d’une caractéristique qui attire les diviseurs cellulaires ou les frontières. Cependant, vous pouvez faire votre propre.

Les [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) démontrent comment définir des lignes et `BoxView` des colonnes supplémentaires spécifiquement pour les éléments minces pour imiter les lignes de démarcation.

Le programme [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) ne crée pas de `BoxView` cellules supplémentaires, mais aligne plutôt des éléments dans chaque cellule pour imiter une bordure cellulaire.

## <a name="almost-real-life-grid-examples"></a>Presque des exemples de grille de la vie réelle

[**L’échantillon KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) utilise un `Grid` clavier pour afficher un clavier :

[![Triple capture d’écran de Keypad Grid](images/ch17fg12-small.png "Grille Keypad")](images/ch17fg12-large.png#lightbox "Grille Keypad")

### <a name="responding-to-orientation-changes"></a>Répondre aux changements d’orientation

Le `Grid` programme peut aider à structurer un programme pour répondre aux changements d’orientation. [**L’échantillon GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) démontre une technique qui déplace un élément entre une deuxième rangée d’un téléphone orienté portrait et la deuxième colonne d’un téléphone orienté paysage.

Le programme initialise les `Slider` éléments à une gamme de 0 à 255, et utilise des liaisons de données pour afficher la valeur des curseurs dans l’hexadecimal. Parce `Slider` que les valeurs sont point flottant, et la chaîne de formatage .NET [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) pour hexadecimal ne fonctionne qu’avec des intégristes, une classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) aide.

## <a name="related-links"></a>Liens connexes

- [Chapitre 17 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Échantillons du chapitre 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grille](~/xamarin-forms/user-interface/layouts/grid.md)
