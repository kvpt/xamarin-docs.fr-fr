---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 19. Collection views''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0eafdeffb6783a0ed54fdf23e6d10de24e2b4c6f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136693"
---
# <a name="summary-of-chapter-19-collection-views"></a>Résumé du chapitre 19. Vues de collection

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

Xamarin.Formsdéfinit trois vues qui maintiennent les collections et affichent leurs éléments :

- [`Picker`](xref:Xamarin.Forms.Picker)est une liste relativement brève d’éléments de chaîne qui permet à l’utilisateur d’en choisir un
- [`ListView`](xref:Xamarin.Forms.ListView)est souvent une longue liste d’éléments du même type et de la même mise en forme, ce qui permet également à l’utilisateur d’en choisir un.
- [`TableView`](xref:Xamarin.Forms.TableView)est une collection de *cellules* (généralement de différents types et apparences) pour afficher des données ou gérer une entrée d’utilisateur

Il est courant pour les applications MVVM d’utiliser le `ListView` pour afficher une collection d’objets sélectionnables.

## <a name="program-options-with-picker"></a>Options de programme avec sélecteur

Le [`Picker`](xref:Xamarin.Forms.Picker) est un bon choix lorsque vous devez autoriser l’utilisateur à choisir une option parmi une liste relativement brève d' `string` éléments.

### <a name="the-picker-and-event-handling"></a>Le sélecteur et la gestion des événements

L’exemple [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) montre comment utiliser XAML pour définir la `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) propriété et ajouter des `string` éléments à la [`Items`](xref:Xamarin.Forms.Picker.Items) collection. Lorsque l’utilisateur sélectionne le `Picker` , il affiche les éléments de la `Items` collection d’une manière dépendante de la plateforme.

L' [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) événement indique que l’utilisateur a sélectionné un élément. La propriété de base zéro [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) indique alors l’élément sélectionné. Si aucun élément n’est sélectionné, est `SelectedIndex` égal à &ndash; 1.

Vous pouvez également utiliser `SelectedIndex` pour initialiser l’élément sélectionné, mais il doit être défini une fois que la `Items` collection est remplie. En XAML, cela signifie que vous utiliserez probablement un élément de propriété pour définir `SelectedIndex` .

### <a name="data-binding-the-picker"></a>Liaison de données du sélecteur

La `SelectedIndex` propriété est stockée par une propriété pouvant être liée, mais elle ne `Items` l’est pas, l’utilisation de la liaison de données avec un `Picker` est donc difficile. Une solution consiste à utiliser conjointement `Picker` avec un [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) tel que celui de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Le [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) montre comment cela fonctionne.

> [!NOTE] 
> Xamarin.Forms `Picker` Contient désormais les `ItemsSource` `SelectedItem` Propriétés et qui prennent en charge la liaison de données. Consultez [Sélecteur](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Rendu de données avec ListView

[`ListView`](xref:Xamarin.Forms.ListView)Est la seule classe qui dérive de [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) à partir de laquelle elle hérite [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) des [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) Propriétés et.

`ItemsSource`est de type, `IEnumerable` mais il est `null` par défaut et doit être explicitement initialisé ou (plus communément) défini sur une collection par le biais d’une liaison de données. Les éléments de cette collection peuvent être de n’importe quel type.

`ListView`définit une [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriété qui est définie sur l’un des éléments de la `ItemsSource` collection ou `null` si aucun élément n’est sélectionné. `ListView`déclenche l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) événement lorsqu’un nouvel élément est sélectionné.

### <a name="collections-and-selections"></a>Collections et sélections

L’exemple [**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) remplit un `ListView` avec 17 `Color` valeurs dans une `List<Color>` collection. Les éléments peuvent être sélectionnés mais, par défaut, ils sont affichés avec leurs représentations non attrayantes `ToString` . Plusieurs exemples de ce chapitre montrent comment corriger cet affichage et le rendre aussi attrayant que souhaité.

### <a name="the-row-separator"></a>Séparateur de lignes

Sur les écrans iOS et Android, une ligne fine sépare les lignes. Vous pouvez contrôler cela à l’aide des [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) Propriétés et. `SeparatorVisibility`la propriété est de type [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility) , une énumération avec deux membres :

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), le paramètre par défaut
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Liaison de données de l’élément sélectionné

La `SelectedItem` propriété est stockée par une propriété pouvant être liée. elle peut donc être la source ou la cible d’une liaison de données. Sa valeur par défaut `BindingMode` est `OneWayToSource` , mais elle est généralement la cible d’une liaison de données bidirectionnelle, en particulier dans les scénarios MVVM. L’exemple [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) illustre ce type de liaison.

### <a name="the-observablecollection-difference"></a>Différence ObservableCollection

L’exemple [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) définit la `ItemsSource` propriété d’un `ListView` objet sur une `List<DateTime>` collection, puis ajoute progressivement un nouvel `DateTime` objet à la collection chaque seconde à l’aide d’un minuteur.

Toutefois, le `ListView` n’est pas automatiquement mis à jour, car la `List<T>` collection n’a pas de mécanisme de notification pour indiquer quand des éléments sont ajoutés ou supprimés de la collection.

Une classe bien meilleure à utiliser dans ces scénarios est [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) définie dans l' `System.Collections.ObjectModel` espace de noms. Cette classe implémente l' [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) interface et, par conséquent [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) , déclenche un événement lorsque des éléments sont ajoutés ou supprimés de la collection, ou lorsqu’ils sont remplacés ou déplacés dans la collection. Lorsque le `ListView` détecte en interne qu’une classe implémentant `INotifyCollectionChanged` a été définie sur sa `ItemsSource` propriété, il attache un gestionnaire à l' `CollectionChanged` événement et met à jour son affichage lorsque la collection est modifiée.

L’exemple [**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) illustre l’utilisation de `ObservableCollection` .

### <a name="templates-and-cells"></a>Modèles et cellules

Par défaut, un `ListView` affiche des éléments dans sa collection à l’aide de la méthode de chaque élément `ToString` . Une meilleure approche consiste à définir un modèle pour afficher les éléments.

Pour expérimenter cette fonctionnalité, vous pouvez utiliser la [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . Cette classe définit une `All` propriété statique de type `IList<NamedColor>` qui contient 141 `NamedColor` objets correspondant aux champs publics de la `Color` structure.

L’exemple [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) définit le `ItemsSource` d’un `ListView` objet sur cette `NamedColor.All` propriété, mais seuls les noms de classes qualifiés complets des `NamedColor` objets sont affichés.

`ListView`a besoin d’un modèle pour afficher ces éléments. Dans le code, vous pouvez définir la [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) propriété définie par `ItemsView<TVisual>` sur un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objet à l’aide du [ `DataTemplate` constructeur](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) qui référence une dérivée de la [`Cell`](xref:Xamarin.Forms.Cell) classe. `Cell`a cinq dérivées :

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash;contient deux `Label` vues (conceptuellement parlant)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash;ajoute une `Image` vue à`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash;contient une `Entry` vue avec un`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash;contient une `Switch` avec un`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash;peut être Any `View` (probablement avec des enfants)

Appelez ensuite [ `SetValue` ] (XREF : Xamarin.Forms . DataTemplate. SetValue ( Xamarin.Forms . BindableProperty, System. Object)) et [ `SetBinding` ] (XREF : Xamarin.Forms . DataTemplate. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) sur l' `DataTemplate` objet pour associer des valeurs aux `Cell` Propriétés, ou pour définir des liaisons de données sur les `Cell` propriétés référençant les propriétés des éléments de la `ItemsSource` collection. Cela est illustré dans l’exemple [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode) .

À mesure que chaque élément est affiché par le `ListView` , une petite arborescence d’éléments visuels est construite à partir du modèle, et des liaisons de données sont établies entre l’élément et les propriétés des éléments de cette arborescence d’éléments visuels. Vous pouvez obtenir une idée de ce processus en installant des gestionnaires pour [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) les [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) événements et du `ListView` , ou à l’aide d’un autre [ `DataTemplate` constructeur](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) qui utilise une fonction qui est appelée chaque fois que l’arborescence d’éléments visuels d’un élément doit être créée.

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) affiche un programme identique de manière fonctionnelle, entièrement en XAML. Une `DataTemplate` balise est définie sur la `ItemTemplate` propriété du `ListView` , puis `TextCell` est défini sur `DataTemplate` . Les liaisons aux propriétés des éléments de la collection sont définies directement sur les [`Text`](xref:Xamarin.Forms.TextCell.Text) Propriétés et [`Detail`](xref:Xamarin.Forms.TextCell.Detail) de `TextCell` .

### <a name="custom-cells"></a>Cellules personnalisées

En XAML, il est possible de définir un [`ViewCell`](xref:Xamarin.Forms.ViewCell) sur `DataTemplate` , puis de définir une arborescence d’éléments visuels personnalisée comme [`View`](xref:Xamarin.Forms.ViewCell.View) propriété de `ViewCell` . ( `View` est la propriété de contenu de `ViewCell` , les `ViewCell.View` balises ne sont donc pas requises.) L’exemple [**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) illustre cette technique :

[![Capture d’écran triple de la liste de couleurs nommées personnalisée](images/ch19fg11-small.png "Liste de couleurs nommées personnalisées")](images/ch19fg11-large.png#lightbox "Liste de couleurs nommées personnalisées")

L’obtention du droit de dimensionnement pour toutes les plateformes peut être délicate. La [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) propriété est utile, mais dans certains cas, vous souhaiterez peut-être recourir à la [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) propriété, qui est moins efficace, mais force le `ListView` à dimensionner les lignes. Pour iOS et Android, vous devez utiliser l’une de ces deux propriétés pour obtenir le dimensionnement de ligne approprié.

### <a name="grouping-the-listview-items"></a>Regroupement des éléments ListView

`ListView`prend en charge le regroupement d’éléments et la navigation entre ces groupes. La `ItemsSource` propriété doit être définie sur une collection de collections : l’objet qui `ItemsSource` a la valeur doit implémenter `IEnumerable` , et chaque élément de la collection doit également implémenter `IEnumerable` . Chaque groupe doit inclure deux propriétés : une description textuelle du groupe et une abréviation à trois lettres.

La [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crée sept groupes d' `NamedColor` objets. L’exemple [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) montre comment utiliser ces groupes avec la [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) propriété de `ListView` définie sur `true` , et les [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) Propriétés et [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) liées aux propriétés dans chaque groupe.

### <a name="custom-group-headers"></a>En-têtes de groupe personnalisés

Il est possible de créer des en-têtes personnalisés pour les `ListView` groupes en remplaçant la `GroupDisplayBinding` propriété par la [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) définition d’un modèle pour les en-têtes.

### <a name="listview-and-interactivity"></a>ListView et interactivité

En général, une application obtient l’interaction de l’utilisateur avec un `ListView` en joignant un gestionnaire à l' `ItemSelected` [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) événement ou, ou en définissant une liaison de données sur la `SelectedItem` propriété. Toutefois, certains types de cellules ( `EntryCell` et `SwitchCell` ) autorisent l’interaction de l’utilisateur, et il est également possible de créer des cellules personnalisées qui interagissent elles-mêmes avec l’utilisateur. [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crée 100 instances de [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) et permet à l’utilisateur de modifier chaque couleur à l’aide d’un trio d' `Slider` éléments. Le programme utilise également le [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) dans le [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView et MVVM

`ListView`joue un rôle important dans les scénarios MVVM. Chaque fois qu’une `IEnumerable` collection existe dans un ViewModel, elle est souvent liée à un `ListView` . En outre, les éléments de la collection implémentent souvent `INotifyPropertyChanged` pour établir une liaison avec les propriétés d’un modèle.

### <a name="a-collection-of-viewmodels"></a>Collection de ViewModels

Pour l’Explorer, la bibliothèque [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crée plusieurs classes basées sur un [fichier de données XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) et des images d’étudiants fictifs dans cette école fictive.

La [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe dérive de [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs) . La [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe est une collection d' `Student` objets et dérive également de `ViewModelBase` . Le [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) télécharge le fichier XML et assemble tous les objets.

Le programme [**studentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) utilise un `ImageCell` pour afficher les étudiants et leurs images dans un `ListView` :

[![Capture d’écran triple de la liste des étudiants](images/ch19fg18-small.png "Liste des étudiants")](images/ch19fg18-large.png#lightbox "Liste des étudiants")

L’exemple [**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) ajoute une [`Header`](xref:Xamarin.Forms.ListView.Header) propriété, mais s’affiche uniquement sur Android.

### <a name="selection-and-the-binding-context"></a>Sélection et contexte de liaison

Le programme [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) lie le `BindingContext` d’un `StackLayout` à la `SelectedItem` propriété de `ListView` . Cela permet au programme d’afficher des informations détaillées sur l’étudiant sélectionné.

### <a name="context-menus"></a>Les menus contextuels :

Une cellule peut définir un menu contextuel qui est implémenté de manière spécifique à une plateforme. Pour créer ce menu, ajoutez [`MenuItem`](xref:Xamarin.Forms.MenuItem) des objets à la [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) propriété de `Cell` .

`MenuItem`définit cinq propriétés :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)de type`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)de type`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)de type`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)de type`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)de type`object`

Les `Command` `CommandParameter` Propriétés et impliquent que le ViewModel de chaque élément contient des méthodes pour exécuter les commandes de menu souhaitées. Dans les scénarios non-MVVM, `MenuItem` définit également un [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) événement.

Le [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) illustre cette technique. La `Command` propriété de chaque `MenuItem` est liée à une propriété de type `ICommand` dans la `Student` classe. Affectez `IsDestructive` à la propriété la valeur `true` pour un `MenuItem` qui supprime ou supprime l’objet sélectionné.

### <a name="varying-the-visuals"></a>Variation des visuels

Il peut arriver que vous souhaitiez obtenir de légères variations dans les visuels des éléments de en `ListView` fonction d’une propriété. Par exemple, lorsque la moyenne des points de niveau d’un étudiant tombe en dessous de 2,0, l’exemple [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) affiche le nom de l’étudiant en rouge.
Pour ce faire, utilisez un convertisseur de valeur de liaison, [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs) , dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="refreshing-the-content"></a>Actualisation du contenu

Le `ListView` prend en charge un mouvement d’extraction pour actualiser ses données. Le programme doit définir la [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) propriété sur `true` pour activer cette. Le `ListView` répond au geste de déroulement en affectant à sa [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) propriété la valeur `true` et en déclenchant l' [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) événement et (pour les scénarios MVVM) appelant la `Execute` méthode de sa [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) propriété.

Le code qui gère l' `Refresh` événement ou `RefreshCommand` met éventuellement à jour les données affichées par le `ListView` et affecte `IsRefreshing` à nouveau la valeur `false` .

L’exemple [**rssfeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) illustre l’utilisation d’un [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) qui implémente les `RefreshCommand` `IsRefreshing` Propriétés et pour la liaison de données.

## <a name="the-tableview-and-its-intents"></a>L’TableView et ses intentions

Bien que le `ListView` affiche généralement plusieurs instances du même type, le [`TableView`](xref:Xamarin.Forms.TableView) se concentre généralement sur la fourniture d’une interface utilisateur pour plusieurs propriétés de différents types. Chaque élément est associé à sa propre [`Cell`](xref:Xamarin.Forms.Cell) dérivée pour afficher la propriété ou lui fournir une interface utilisateur.

### <a name="properties-and-hierarchies"></a>Propriétés et hiérarchies

`TableView`définit uniquement quatre propriétés :

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)de type [`TableIntent`](xref:Xamarin.Forms.TableIntent) , une énumération
- [`Root`](xref:Xamarin.Forms.TableView.Root)de type [`TableRoot`](xref:Xamarin.Forms.TableRoot) , la propriété de contenu de`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)de type`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)de type`bool`

L' `TableIntent` énumération indique comment vous souhaitez utiliser `TableView` :

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Ces membres suggèrent également des utilisations de `TableView` .

Plusieurs autres classes sont impliquées dans la définition d’une table :

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)est une classe abstraite qui dérive de `BindableObject` et définit une [`Title`](xref:Xamarin.Forms.TableSectionBase.Title) propriété

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)est une classe abstraite qui dérive de `TableSectionBase` et implémente `IList<T>` et`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)dérive de`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)dérive de`TableSectionBase<TableSection>`

En résumé, `TableView` a une `Root` propriété que vous définissez sur un `TableRoot` objet, qui est une collection d' `TableSection` objets, chacun d’entre eux étant une collection d' `Cell` objets. Une table comporte plusieurs sections, et chaque section comporte plusieurs cellules. La table elle-même peut avoir un titre et chaque section peut avoir un titre. Bien que utilise `TableView` des `Cell` dérivés, il n’utilise pas `DataTemplate` .

### <a name="a-prosaic-form"></a>Formulaire prosaic

L’exemple [**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) définit un [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) modèle de vue, une instance de qui devient le `BindingContext` du `TableView` . Chaque `Cell` dérivé de son `TableSection` peut ensuite avoir des liaisons aux propriétés de la `PersonalInformation` classe.

### <a name="custom-cells"></a>Cellules personnalisées

L’exemple [**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) se développe sur **EntryForm**. La [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe comprend une propriété booléenne qui régit l’applicabilité de deux propriétés supplémentaires. Pour ces deux propriétés supplémentaires, le programme utilise un personnalisé `PickerCell` basé sur [PickerCell. xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) et [PickerCell.Xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Bien que les `IsEnabled` Propriétés des deux `PickerCell` éléments soient liées à la propriété booléenne dans `ProgrammerInformation` , cette technique ne semble pas fonctionner, qui invite l’exemple suivant.

### <a name="conditional-sections"></a>Sections conditionnelles

L’exemple [**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) place les deux éléments qui sont conditionnels sur la sélection de l’élément booléen dans un distinct `TableSection` . Le fichier code-behind supprime cette section du `TableView` ou le rajoute en fonction de la propriété booléenne.

### <a name="a-tableview-menu"></a>Un menu TableView

Une autre utilisation de `TableView` est un menu. L’exemple [**MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) illustre un menu qui vous permet de déplacer un peu `BoxView` autour de l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 19 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Exemples du chapitre 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Sélecteur](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
