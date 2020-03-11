---
title: Résumé du chapitre 19. Vues de collection
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 19. Vues de collection'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032863"
---
# <a name="summary-of-chapter-19-collection-views"></a>Résumé du chapitre 19. Vues de collection

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Xamarin.Forms définit trois vues qui maintiennent des collections et affichent leurs éléments :

- [`Picker`](xref:Xamarin.Forms.Picker) est une liste relativement brève d’éléments de chaîne qui permet à l’utilisateur d’en choisir un
- [`ListView`](xref:Xamarin.Forms.ListView) est souvent une longue liste d’éléments généralement du même type et de la même mise en forme, ce qui permet également à l’utilisateur d’en choisir un.
- [`TableView`](xref:Xamarin.Forms.TableView) est une collection de *cellules* (généralement de différents types et apparences) pour afficher des données ou gérer une entrée d’utilisateur

Il est courant pour les applications MVVM d’utiliser le `ListView` pour afficher une collection d’objets sélectionnables.

## <a name="program-options-with-picker"></a>Options du programme avec le sélecteur

Le [`Picker`](xref:Xamarin.Forms.Picker) est un bon choix lorsque vous devez autoriser l’utilisateur à choisir une option parmi une liste relativement brève d’éléments de `string`.

### <a name="the-picker-and-event-handling"></a>Le sélecteur et la gestion des événements

L’exemple [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) montre comment utiliser XAML pour définir la `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) propriété et ajouter des éléments `string` à la collection [`Items`](xref:Xamarin.Forms.Picker.Items) . Lorsque l’utilisateur sélectionne la `Picker`, il affiche les éléments dans la collection `Items` selon une méthode dépendante de la plateforme.

L’événement [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indique quand l’utilisateur a sélectionné un élément. La propriété de [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) de base zéro indique alors l’élément sélectionné. Si aucun élément n’est sélectionné, `SelectedIndex` est égal à &ndash;1.

Vous pouvez également utiliser `SelectedIndex` pour initialiser l’élément sélectionné, mais il doit être défini une fois que la collection de `Items` est remplie. En XAML, cela signifie que vous utiliserez probablement un élément de propriété pour définir `SelectedIndex`.

### <a name="data-binding-the-picker"></a>Le sélecteur de liaison de données

La propriété `SelectedIndex` est stockée par une propriété pouvant être liée, mais `Items` n’est pas, l’utilisation de la liaison de données avec un `Picker` est difficile. Une solution consiste à utiliser le `Picker` en association avec un [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) tel que celui de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Le [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) montre comment cela fonctionne.

> [!NOTE] 
> L' `Picker` Xamarin. Forms comprend désormais des propriétés `ItemsSource` et `SelectedItem` qui prennent en charge la liaison de données. Consultez [Sélecteur](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Rendu des données avec ListView

La [`ListView`](xref:Xamarin.Forms.ListView) est la seule classe qui dérive de [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) à partir de laquelle elle hérite des propriétés [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) et [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) .

`ItemsSource` est de type `IEnumerable`, mais il est `null` par défaut et doit être explicitement initialisé ou (plus communément) défini sur une collection par le biais d’une liaison de données. Les éléments de cette collection peuvent être de n’importe quel type.

`ListView` définit une propriété [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) qui est définie sur l’un des éléments de la collection `ItemsSource` ou `null` si aucun élément n’est sélectionné. `ListView` déclenche l’événement [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) lorsqu’un nouvel élément est sélectionné.

### <a name="collections-and-selections"></a>Collections et les sélections

L’exemple [**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) remplit une `ListView` avec 17 valeurs de `Color` dans une collection de `List<Color>`. Les éléments peuvent être sélectionnés mais, par défaut, ils sont affichés avec leurs représentations `ToString` non attrayantes. Plusieurs exemples dans ce chapitre vous montrent comment corriger qui affichent et rendre aussi attractif comme vous le souhaitez.

### <a name="the-row-separator"></a>Le séparateur de ligne

Sur iOS et Android affiche, une fine ligne sépare les lignes. Vous pouvez contrôler cela avec les propriétés [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) et [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) . `SeparatorVisibility` propriété est de type [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility), une énumération avec deux membres :

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), le paramètre par défaut
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>L’élément sélectionné de liaison de données

La propriété `SelectedItem` est stockée par une propriété pouvant être liée. il peut s’agir de la source ou de la cible d’une liaison de données. Son `BindingMode` par défaut est `OneWayToSource`, mais il s’agit généralement de la cible d’une liaison de données bidirectionnelle, en particulier dans les scénarios MVVM. L’exemple [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) illustre ce type de liaison.

### <a name="the-observablecollection-difference"></a>La différence de ObservableCollection

L’exemple [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) définit la propriété `ItemsSource` d’un `ListView` sur une collection `List<DateTime>`, puis ajoute progressivement un nouvel objet `DateTime` à la collection chaque seconde à l’aide d’un minuteur.

Toutefois, le `ListView` ne se met pas automatiquement à jour, car la collection de `List<T>` n’a pas de mécanisme de notification pour indiquer quand des éléments sont ajoutés ou supprimés de la collection.

Une classe bien meilleure à utiliser dans ces scénarios est [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) définie dans l’espace de noms `System.Collections.ObjectModel`. Cette classe implémente l’interface [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) et, par conséquent, déclenche un événement [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) lorsque des éléments sont ajoutés ou supprimés de la collection, ou lorsqu’ils sont remplacés ou déplacés dans la collection. Lorsque l' `ListView` détecte en interne qu’une classe qui implémente `INotifyCollectionChanged` a été définie sur sa propriété `ItemsSource`, elle joint un gestionnaire à l’événement `CollectionChanged` et met à jour son affichage lorsque la collection est modifiée.

L’exemple [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) illustre l’utilisation de `ObservableCollection`.

### <a name="templates-and-cells"></a>Modèles et des cellules

Par défaut, un `ListView` affiche des éléments dans sa collection à l’aide de la méthode `ToString` de chaque élément. Une meilleure approche consiste à définir un modèle pour afficher les éléments.

Pour expérimenter cette fonctionnalité, vous pouvez utiliser la classe [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Cette classe définit une propriété `All` statique de type `IList<NamedColor>` qui contient 141 `NamedColor` objets correspondant aux champs publics de la structure `Color`.

L’exemple [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) définit la `ItemsSource` d’une `ListView` sur cette propriété `NamedColor.All`, mais seuls les noms de classes qualifiés complets des objets `NamedColor` sont affichés.

`ListView` a besoin d’un modèle pour afficher ces éléments. Dans le code, vous pouvez définir la propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) définie par `ItemsView<TVisual>` sur un objet [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) à l’aide du [constructeur`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) qui référence une dérivée de la classe [`Cell`](xref:Xamarin.Forms.Cell) . `Cell` a cinq dérivées :

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; contient deux vues `Label` (conceptuellement parlant)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; ajoute une vue `Image` à `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; contient une vue de `Entry` avec un `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; contient un `Switch` avec un `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; peut être n’importe quel `View` (probablement avec les enfants)

Appelez ensuite [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) et [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) sur l’objet `DataTemplate` pour associer des valeurs aux propriétés de `Cell`, ou pour définir des liaisons de données sur les propriétés de `Cell` référençant les propriétés des éléments de la collection `ItemsSource`. Cela est illustré dans l’exemple [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

À mesure que chaque élément est affiché par le `ListView`, une petite arborescence d’éléments visuels est construite à partir du modèle, et les liaisons de données sont établies entre l’élément et les propriétés des éléments de cette arborescence d’éléments visuels. Vous pouvez obtenir une idée de ce processus en installant des gestionnaires pour les événements [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) et [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) de l' `ListView`, ou en utilisant un autre [constructeur de`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) qui utilise une fonction qui est appelée chaque fois que l’arborescence d’éléments visuels d’un élément doit être créée.

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) affiche un programme identique de manière fonctionnelle, entièrement en XAML. Une balise `DataTemplate` est définie sur la propriété `ItemTemplate` de l' `ListView`, puis la `TextCell` est définie sur la `DataTemplate`. Les liaisons aux propriétés des éléments de la collection sont définies directement sur les propriétés [`Text`](xref:Xamarin.Forms.TextCell.Text) et [`Detail`](xref:Xamarin.Forms.TextCell.Detail) de la `TextCell`.

### <a name="custom-cells"></a>Cellules personnalisées

En XAML, il est possible de définir un [`ViewCell`](xref:Xamarin.Forms.ViewCell) sur le `DataTemplate` puis de définir une arborescence d’éléments visuels personnalisée comme [`View`](xref:Xamarin.Forms.ViewCell.View) propriété de `ViewCell`. (`View` est la propriété de contenu de `ViewCell` donc les balises `ViewCell.View` ne sont pas requises.) L’exemple [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) illustre cette technique :

[![Capture d’écran triple de la liste de couleurs nommées personnalisée](images/ch19fg11-small.png "Liste de couleurs nommées personnalisées")](images/ch19fg11-large.png#lightbox "Liste de couleurs nommées personnalisées")

Obtenir la taille adaptées à toutes les plateformes peut être difficile. La propriété [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) est utile, mais dans certains cas, vous souhaiterez peut-être recourir à la propriété [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) , qui est moins efficace, mais qui force la `ListView` à dimensionner les lignes. Pour iOS et Android, vous devez utiliser une de ces deux propriétés pour obtenir le dimensionnement de la ligne appropriée.

### <a name="grouping-the-listview-items"></a>Grouper les éléments de ListView

`ListView` prend en charge le regroupement d’éléments et la navigation entre ces groupes. La propriété `ItemsSource` doit être définie sur une collection de collections : l’objet dont `ItemsSource` a la valeur doit implémenter `IEnumerable`, et chaque élément de la collection doit également implémenter `IEnumerable`. Chaque groupe doit inclure deux propriétés : une description du groupe et une abréviation à trois lettres.

La classe [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crée sept groupes d’objets `NamedColor`. L’exemple [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) montre comment utiliser ces groupes avec la propriété [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) de `ListView` définie sur `true`, et les propriétés [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) et [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) liées aux propriétés dans chaque groupe.

### <a name="custom-group-headers"></a>En-têtes de groupe personnalisé

Il est possible de créer des en-têtes personnalisés pour les groupes de `ListView` en remplaçant la propriété `GroupDisplayBinding` par le [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) définissant un modèle pour les en-têtes.

### <a name="listview-and-interactivity"></a>ListView et l’interactivité

En général, une application obtient l’interaction de l’utilisateur avec un `ListView` en attachant un gestionnaire à l’événement `ItemSelected` ou [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , ou en définissant une liaison de données sur la propriété `SelectedItem`. Toutefois, certains types de cellules (`EntryCell` et `SwitchCell`) autorisent l’interaction de l’utilisateur, et il est également possible de créer des cellules personnalisées qui interagissent elles-mêmes avec l’utilisateur. [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crée 100 instances de [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) et permet à l’utilisateur de modifier chaque couleur à l’aide d’un trio d’éléments `Slider`. Le programme utilise également les [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) dans [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView et MVVM

`ListView` joue un rôle important dans les scénarios MVVM. Lorsqu’une collection de `IEnumerable` existe dans un ViewModel, elle est souvent liée à une `ListView`. En outre, les éléments de la collection implémentent souvent `INotifyPropertyChanged` pour établir une liaison avec les propriétés dans un modèle.

### <a name="a-collection-of-viewmodels"></a>Une collection de ViewModel

Pour l’Explorer, la bibliothèque [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crée plusieurs classes basées sur un [fichier de données XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) et des images d’étudiants fictifs dans cette école fictive.

La classe [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) dérive de [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs). La classe [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) est une collection d’objets `Student` et dérive également de `ViewModelBase`. Le [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) télécharge le fichier XML et assemble tous les objets.

Le programme [**studentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) utilise un `ImageCell` pour afficher les étudiants et leurs images dans un `ListView`:

[![Capture d’écran triple de la liste des étudiants](images/ch19fg18-small.png "Liste des étudiants")](images/ch19fg18-large.png#lightbox "Liste des étudiants")

L’exemple [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) ajoute une propriété [`Header`](xref:Xamarin.Forms.ListView.Header) , mais elle s’affiche uniquement sur Android.

### <a name="selection-and-the-binding-context"></a>Sélection et le contexte de liaison

Le programme [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) lie la `BindingContext` d’un `StackLayout` à la propriété `SelectedItem` de l' `ListView`. Cela permet au programme afficher des informations détaillées sur l’étudiant sélectionné.

### <a name="context-menus"></a>Menu contextuels

Une cellule peut définir un menu contextuel qui est implémenté de manière spécifique à la plateforme. Pour créer ce menu, ajoutez [`MenuItem`](xref:Xamarin.Forms.MenuItem) objets à la propriété [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) de l' `Cell`.

`MenuItem` définit cinq propriétés :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) de type `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de type `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) de type `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) de type `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) de type `object`

Les propriétés `Command` et `CommandParameter` impliquent que le ViewModel de chaque élément contient des méthodes pour exécuter les commandes de menu souhaitées. Dans les scénarios non-MVVM, `MenuItem` définit également un événement [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) .

Le [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) illustre cette technique. La propriété `Command` de chaque `MenuItem` est liée à une propriété de type `ICommand` dans la classe `Student`. Affectez à la propriété `IsDestructive` la valeur `true` pour un `MenuItem` qui supprime ou supprime l’objet sélectionné.

### <a name="varying-the-visuals"></a>Faire varier les éléments visuels

Parfois, vous souhaiterez obtenir de légères variations dans les visuels des éléments de l' `ListView` en fonction d’une propriété. Par exemple, lorsque la moyenne des points de niveau d’un étudiant tombe en dessous de 2,0, l’exemple [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) affiche le nom de l’étudiant en rouge.
Pour ce faire, utilisez un convertisseur de valeur de liaison, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs), dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>L’actualisation du contenu

Le `ListView` prend en charge un geste d’extraction pour actualiser ses données. Pour activer cette propriété, le programme doit définir la propriété [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) sur `true`. Le `ListView` répond au geste de la liste déroulante en affectant à sa propriété [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) la valeur `true`et en déclenchant l’événement [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) et (pour les scénarios MVVM) appelant la méthode `Execute` de sa propriété [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) .

Le code qui gère l’événement `Refresh` ou le `RefreshCommand` peut mettre à jour les données affichées par le `ListView` et redéfinit `IsRefreshing` sur `false`.

L’exemple [**rssfeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) illustre l’utilisation d’une [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) qui implémente les propriétés `RefreshCommand` et `IsRefreshing` pour la liaison de données.

## <a name="the-tableview-and-its-intents"></a>Le TableView et ses intentions

Tandis que le `ListView` affiche généralement plusieurs instances du même type, la [`TableView`](xref:Xamarin.Forms.TableView) est généralement axée sur la fourniture d’une interface utilisateur pour plusieurs propriétés de différents types. Chaque élément est associé à sa propre [`Cell`](xref:Xamarin.Forms.Cell) dérivée pour afficher la propriété ou lui fournir une interface utilisateur.

### <a name="properties-and-hierarchies"></a>Propriétés et des hiérarchies

`TableView` ne définit que quatre propriétés :

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) de type [`TableIntent`](xref:Xamarin.Forms.TableIntent), une énumération
- [`Root`](xref:Xamarin.Forms.TableView.Root) de type [`TableRoot`](xref:Xamarin.Forms.TableRoot), la propriété de contenu de `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) de type `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) de type `bool`

L’énumération `TableIntent` indique comment vous avez l’intention d’utiliser le `TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Ces membres suggèrent également des utilisations du `TableView`.

Plusieurs autres classes sont impliqués dans la définition d’une table :

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) est une classe abstraite qui dérive de `BindableObject` et définit une propriété [`Title`](xref:Xamarin.Forms.TableSectionBase.Title)

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) est une classe abstraite qui dérive de `TableSectionBase` et implémente `IList<T>` et `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) dérive de `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) dérive de `TableSectionBase<TableSection>`

En résumé, `TableView` a une propriété `Root` que vous définissez sur un objet `TableRoot`, qui est une collection d’objets `TableSection`, chacun d’entre eux étant une collection d’objets `Cell`. Une table comporte plusieurs sections, et chaque section comporte plusieurs cellules. La table elle-même peut avoir un titre, et chaque section peut contenir un titre. Bien que `TableView` utilise des dérivés `Cell`, il n’utilise pas de `DataTemplate`.

### <a name="a-prosaic-form"></a>Un formulaire prosaïque

L’exemple [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) définit un modèle de vue de [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) , une instance de qui devient la `BindingContext` du `TableView`. Chaque `Cell` dérivé dans son `TableSection` peut ensuite avoir des liaisons aux propriétés de la classe `PersonalInformation`.

### <a name="custom-cells"></a>Cellules personnalisées

L’exemple [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) se développe sur **EntryForm**. La classe [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) comprend une propriété booléenne qui régit l’applicabilité de deux propriétés supplémentaires. Pour ces deux propriétés supplémentaires, le programme utilise un `PickerCell` personnalisé basé sur [PickerCell. Xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) et [PickerCell.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Bien que les propriétés `IsEnabled` des deux éléments `PickerCell` soient liées à la propriété booléenne dans `ProgrammerInformation`, cette technique ne semble pas fonctionner, qui invite l’exemple suivant.

### <a name="conditional-sections"></a>Sections conditionnelles

L’exemple [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) place les deux éléments qui sont conditionnels sur la sélection de l’élément booléen dans une `TableSection`distincte. Le fichier code-behind supprime cette section de la `TableView` ou la rajoute en fonction de la propriété booléenne.

### <a name="a-tableview-menu"></a>Un menu TableView

Une autre utilisation d’un `TableView` est un menu. L’exemple [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) illustre un menu qui vous permet de déplacer un petit `BoxView` à l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 19 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Exemples du chapitre 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Picker](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
