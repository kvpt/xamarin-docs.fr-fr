---
title: Résumé du chapitre 19. Vues de collection
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 19. Vues de collection'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032863"
---
# <a name="summary-of-chapter-19-collection-views"></a>Résumé du chapitre 19. Vues de collection

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Xamarin.Forms définit trois vues qui maintiennent les collections et affichent leurs éléments :

- [`Picker`](xref:Xamarin.Forms.Picker)est une liste relativement courte d’éléments de chaîne qui permet à l’utilisateur de choisir un
- [`ListView`](xref:Xamarin.Forms.ListView)est souvent une longue liste d’éléments généralement du même type et le formatage, permettant également à l’utilisateur de choisir un
- [`TableView`](xref:Xamarin.Forms.TableView)est une collection de *cellules* (généralement de différents types et apparences) pour afficher des données ou gérer l’entrée de l’utilisateur

Il est courant pour les applications `ListView` MVVM d’utiliser la pour afficher une collection sélectionnable d’objets.

## <a name="program-options-with-picker"></a>Options de programme avec Picker

Le [`Picker`](xref:Xamarin.Forms.Picker) est un bon choix lorsque vous avez besoin de permettre à `string` l’utilisateur de choisir une option parmi une liste relativement courte d’éléments.

### <a name="the-picker-and-event-handling"></a>Le Picker et la tenue d’événements

L’échantillon [**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo) montre comment utiliser XAML `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) pour `string` définir [`Items`](xref:Xamarin.Forms.Picker.Items) la propriété et ajouter des éléments à la collection. Lorsque l’utilisateur `Picker`sélectionne le , il `Items` affiche les éléments de la collection d’une manière dépendante de la plate-forme.

L’événement [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) indique quand l’utilisateur a sélectionné un élément. La propriété [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) à base nulle indique alors l’article sélectionné. Si aucun élément `SelectedIndex` n’est sélectionné, équivaut à &ndash;1.

Vous pouvez `SelectedIndex` également utiliser pour initialiser l’élément sélectionné, mais il doit être défini une fois la `Items` collection remplie. Dans XAML, cela signifie que vous allez probablement `SelectedIndex`utiliser un élément de propriété pour définir .

### <a name="data-binding-the-picker"></a>Données liant le Picker

La `SelectedIndex` propriété est soutenue par `Items` une propriété liant mais `Picker` ne l’est pas, donc l’utilisation de la liaison de données avec un est difficile. Une solution est `Picker` d’utiliser [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) la combinaison avec un tel dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Le [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding) montre comment cela fonctionne.

> [!NOTE] 
> Le Xamarin.Forms `Picker` `ItemsSource` comprend `SelectedItem` maintenant et les propriétés qui prennent en charge la liaison des données. Voir [Picker](~/xamarin-forms/user-interface/picker/index.md).

## <a name="rendering-data-with-listview"></a>Rendre les données avec ListView

La [`ListView`](xref:Xamarin.Forms.ListView) seule classe qui dérive [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) de laquelle il [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) hérite de la et des propriétés.

`ItemsSource`est de `IEnumerable` type, `null` mais il est par défaut et doit être explicitement initialisé ou (plus généralement) réglé à une collecte par le biais d’une liaison de données. Les éléments de cette collection peuvent être de n’importe quel type.

`ListView`définit une [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriété qui est soit réglée à `ItemsSource` l’un des éléments de la collection ou `null` si aucun article n’est sélectionné. `ListView`déclenche [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) l’événement lorsqu’un nouvel élément est sélectionné.

### <a name="collections-and-selections"></a>Collections et sélections

[**L’échantillon ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList) `ListView` remplit un `Color` avec 17 valeurs dans une `List<Color>` collection. Les éléments sont sélectionnables mais par défaut `ToString` ils sont affichés avec leurs représentations peu attrayantes. Plusieurs exemples dans ce chapitre montrent comment fixer cet affichage et le rendre aussi attrayant que souhaité.

### <a name="the-row-separator"></a>Le séparateur de ligne

Sur les écrans iOS et Android, une ligne mince sépare les lignes. Vous pouvez contrôler [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) cela [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) avec le et les propriétés. `SeparatorVisibility`propriété est [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility)de type , un recensement avec deux membres:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default), le paramètre par défaut
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>Données liant l’élément sélectionné

La `SelectedItem` propriété est adossée à une propriété liant, de sorte qu’elle peut être soit la source ou la cible d’une liaison de données. Son `BindingMode` défaut `OneWayToSource`est, mais généralement il est la cible d’une liaison de données bidirectionnels, en particulier dans les scénarios MVVM. [**L’échantillon ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray) démontre ce type de liaison.

### <a name="the-observablecollection-difference"></a>La différence ObservableCollection

L’échantillon [**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger) `ItemsSource` définit `ListView` la `List<DateTime>` propriété d’une collection, puis ajoute progressivement un nouvel `DateTime` objet à la collection chaque seconde à l’aide d’une minuterie.

Toutefois, `ListView` le ne se met `List<T>` pas automatiquement à jour parce que la collection n’a pas de mécanisme de notification pour indiquer quand les éléments sont ajoutés ou supprimés de la collection.

Une bien meilleure classe à utiliser [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) dans `System.Collections.ObjectModel` de tels scénarios est définie dans l’espace nom. Cette classe implémente l’interface [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) et déclenche par conséquent un [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) événement lorsque des éléments sont ajoutés ou retirés de la collection, ou lorsqu’ils sont remplacés ou déplacés au sein de la collection. Lorsque `ListView` l’interne détecte qu’une mise en œuvre de `INotifyCollectionChanged` classe a été réglée à sa `ItemsSource` propriété, elle attache un gestionnaire à l’événement `CollectionChanged` et met à jour son affichage lorsque la collection change.

[**L’échantillon ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger) démontre `ObservableCollection`l’utilisation de .

### <a name="templates-and-cells"></a>Modèles et cellules

Par défaut, `ListView` un affichage des éléments de `ToString` sa collection en utilisant la méthode de chaque élément. Une meilleure approche consiste à définir un modèle pour afficher les éléments.

Pour expérimenter cette fonctionnalité, vous [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) pouvez utiliser la classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) Cette classe définit `All` une propriété `IList<NamedColor>` statique de type `NamedColor` qui contient 141 objets correspondant aux champs publics de la `Color` structure.

L’échantillon [**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList) `ItemsSource` définit `ListView` le `NamedColor.All` d’un à cette propriété, `NamedColor` mais seuls les noms de classe entièrement qualifiés des objets sont affichés.

`ListView`a besoin d’un modèle pour afficher ces éléments. Dans le code, [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) vous pouvez `ItemsView<TVisual>` définir [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) la propriété définie par un [`Cell`](xref:Xamarin.Forms.Cell) objet à l’aide du [ `DataTemplate` constructeur](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) qui fait référence à un dérivé de la classe. `Cell`a cinq dérivés :

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash; contient `Label` deux points de vue (conceptuellement parlant)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash; ajoute `Image` une vue à`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash; contient `Entry` une vue avec un`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash; contient `Switch` un avec un`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash; peut être `View` n’importe quel (probablement avec des enfants)

Ensuite, [`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object)) [`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) appelez `DataTemplate` et sur l’objet d’associer les valeurs avec les `Cell` propriétés, ou de définir des liaisons de données sur les `Cell` propriétés faisant référence aux propriétés des éléments de la `ItemsSource` collection. Ceci est démontré dans l’échantillon [**TextCellListCode.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)

Comme chaque élément est `ListView`affiché par le , un petit arbre visuel est construit à partir du modèle, et des liaisons de données sont établies entre l’élément et les propriétés des éléments de cet arbre visuel. Vous pouvez avoir une idée de ce processus [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) en installant des gestionnaires pour les et les événements de la `ListView`, ou en utilisant un [ `DataTemplate` constructeur](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object})) alternatif qui utilise une fonction qui est appelé chaque fois que l’arbre visuel d’un élément doit être créé.

Le [**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml) affiche un programme fonctionnellement identique entièrement dans XAML. Une `DataTemplate` étiquette est `ItemTemplate` réglée à `ListView`la propriété `TextCell` de la `DataTemplate`, et puis le est réglé à la . Les fixations aux propriétés des articles de [`Text`](xref:Xamarin.Forms.TextCell.Text) [`Detail`](xref:Xamarin.Forms.TextCell.Detail) la collection `TextCell`sont fixées directement sur le .

### <a name="custom-cells"></a>Cellules personnalisées

Dans XAML, il est [`ViewCell`](xref:Xamarin.Forms.ViewCell) possible `DataTemplate` de définir un à [`View`](xref:Xamarin.Forms.ViewCell.View) l’et ensuite définir un arbre visuel personnalisé comme la propriété de `ViewCell`. (`View` est la `ViewCell` propriété `ViewCell.View` de contenu de sorte que les balises ne sont pas nécessaires.) [**L’échantillon CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList) démontre cette technique :

[![Triple capture d’écran de Custom Named Color List](images/ch19fg11-small.png "Liste de couleurs nommées personnalisées")](images/ch19fg11-large.png#lightbox "Liste de couleurs nommées personnalisées")

Obtenir le bon dimensionnement pour toutes les plates-formes peut être difficile. La [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight) propriété est utile, mais dans certains cas, vous aurez envie de recourir à la [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) propriété, qui est moins efficace, mais force à la `ListView` taille des rangées. Pour iOS et Android, vous devez utiliser l’une de ces deux propriétés pour obtenir le dimensionnement de ligne approprié.

### <a name="grouping-the-listview-items"></a>Regrouper les éléments ListView

`ListView`soutient le regroupement d’articles et la navigation entre ces groupes. La `ItemsSource` propriété doit être réglée sur une `ItemsSource` collection de collections `IEnumerable`: l’objet qui doit `IEnumerable`être mis en œuvre, et chaque élément de la collection doit également être mis en œuvre. Chaque groupe doit inclure deux propriétés : une description textuelle du groupe et une abréviation de trois lettres.

La [`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) crée sept groupes d’objets. `NamedColor` L’échantillon [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList) montre comment utiliser [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) ces `ListView` groupes `true`avec [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) la [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) propriété de set to , et les propriétés et les propriétés liées aux propriétés dans chaque groupe.

### <a name="custom-group-headers"></a>En-têtes de groupe personnalisés

Il est possible de créer des `ListView` en-têtes `GroupDisplayBinding` personnalisés [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) pour les groupes en remplaçant la propriété par la définition d’un modèle pour les en-têtes.

### <a name="listview-and-interactivity"></a>ListView et interactivité

En général, une application `ListView` obtient l’interaction de `ItemSelected` [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) l’utilisateur avec un en `SelectedItem` attachant un gestionnaire à l’événement ou à l’événement, ou en définissant une liaison de données sur la propriété. Mais certains types`EntryCell` `SwitchCell`de cellules ( et ) permettent l’interaction avec l’utilisateur, et il est également possible de créer des cellules personnalisées qui interagissent elles-mêmes avec l’utilisateur. [**L’InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView) crée 100 instances et permet à l’utilisateur de [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) changer chaque couleur à l’aide d’un trio d’éléments. `Slider` Le programme fait également [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) usage de la dans le [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit).

## <a name="listview-and-mvvm"></a>ListView et MVVM

`ListView`joue un grand rôle dans les scénarios MVVM. Chaque `IEnumerable` fois qu’une collection existe dans un ViewModel, elle est souvent liée à un `ListView`. En outre, les éléments `INotifyPropertyChanged` de la collection mettent souvent en œuvre pour se lier avec les propriétés dans un modèle.

### <a name="a-collection-of-viewmodels"></a>Une collection de ViewModels

Pour explorer cette découverte, la bibliothèque [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) crée plusieurs classes basées sur un fichier de [données XML](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml) et des images d’élèves fictifs de cette école fictive.

La [`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs) classe dérive [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)de . La [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs) classe est `Student` une collection d’objets et dérive également de `ViewModelBase`. Le [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) télécharge le fichier XML et assemble tous les objets.

Le programme [**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList) utilise un `ImageCell` pour afficher `ListView`les étudiants et leurs images dans un :

[![Triple capture d’écran de la liste des étudiants](images/ch19fg18-small.png "Liste des étudiants")](images/ch19fg18-large.png#lightbox "Liste des étudiants")

[**L’échantillon ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader) ajoute une [`Header`](xref:Xamarin.Forms.ListView.Header) propriété, mais il ne s’affiche que sur Android.

### <a name="selection-and-the-binding-context"></a>Sélection et contexte contraignant

Le programme [**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail) `BindingContext` lie `StackLayout` le `SelectedItem` d’un `ListView`à la propriété de la . Cela permet au programme d’afficher des informations détaillées sur l’élève sélectionné.

### <a name="context-menus"></a>Menu contextuels

Une cellule peut définir un menu contextuelle qui est implémenté d’une manière spécifique à la plate-forme. Pour créer ce [`MenuItem`](xref:Xamarin.Forms.MenuItem) menu, [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) ajoutez des `Cell`objets à la propriété de la .

`MenuItem`définit cinq propriétés :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)de type`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)de type`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)de type`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)de type`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)de type`object`

Les `Command` `CommandParameter` propriétés et les propriétés impliquent que le ViewModel pour chaque élément contient des méthodes pour effectuer les commandes de menu souhaitées. Dans les scénarios non-MVVM, `MenuItem` [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) définit également un événement.

Le [**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu) démontre cette technique. La `Command` propriété `MenuItem` de chacun est liée `ICommand` à `Student` une propriété de type dans la classe. Définissez `IsDestructive` la `true` propriété `MenuItem` pour un qui supprime ou supprime l’objet sélectionné.

### <a name="varying-the-visuals"></a>Varier les visuels

Parfois, vous aurez envie de légères variations `ListView` dans les visuels des éléments dans la base d’une propriété. Par exemple, lorsque la moyenne des points de classe d’un élève est inférieure à 2,0, l’échantillon [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents) affiche le nom de cet élève en rouge.
Ceci est accompli par l’utilisation [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)d’un convertisseur de valeur contraignante, , dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

### <a name="refreshing-the-content"></a>Rafraîchir le contenu

Le `ListView` prend en charge un geste de traction vers le bas pour rafraîchir ses données. Le programme doit [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) définir `true` la propriété pour permettre cela. Le `ListView` répond au geste de traction [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) vers `true`le bas en [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) fixant sa propriété à , `Execute` et en [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) soulevant l’événement et (pour les scénarios MVVM) appelant la méthode de sa propriété.

Code traitant `Refresh` l’événement ou `RefreshCommand` les mises `ListView` à `IsRefreshing` jour `false`puis éventuellement les données affichées par le et se définit en arrière à .

[**L’échantillon RssFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed) démontre [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) l’utilisation `IsRefreshing` d’un qui implémente et des `RefreshCommand` propriétés pour la liaison des données.

## <a name="the-tableview-and-its-intents"></a>La TableView et ses intentions

Bien `ListView` que l’affichage général de [`TableView`](xref:Xamarin.Forms.TableView) plusieurs instances du même type, le est généralement axé sur la fourniture d’une interface utilisateur pour plusieurs propriétés de différents types. Chaque article est associé [`Cell`](xref:Xamarin.Forms.Cell) à son propre dérivé pour afficher la propriété ou lui fournir une interface utilisateur.

### <a name="properties-and-hierarchies"></a>Propriétés et hiérarchies

`TableView`ne définit que quatre propriétés :

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)de [`TableIntent`](xref:Xamarin.Forms.TableIntent)type, un recensement
- [`Root`](xref:Xamarin.Forms.TableView.Root)de [`TableRoot`](xref:Xamarin.Forms.TableRoot)type, la propriété de contenu de`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)de type`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)de type`bool`

L’énumération `TableIntent` indique comment vous `TableView`avez l’intention d’utiliser le :

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

Ces membres suggèrent également `TableView`quelques utilisations pour le .

Plusieurs autres classes sont impliquées dans la définition d’une table :

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)est une classe abstraite `BindableObject` qui dérive [`Title`](xref:Xamarin.Forms.TableSectionBase.Title) et définit une propriété

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)est une classe abstraite `TableSectionBase` qui `IList<T>` dérive et met en œuvre et`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)dérive de`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)dérive de`TableSectionBase<TableSection>`

En bref, `TableView` `Root` a une propriété `TableRoot` que vous définissez `TableSection` à un objet, qui `Cell` est une collection d’objets, dont chacun est une collection d’objets. Une table a plusieurs sections, et chaque section a plusieurs cellules. Le tableau lui-même peut avoir un titre, et chaque section peut avoir un titre. Bien `TableView` que `Cell` fait usage de dérivés, `DataTemplate`il ne fait pas usage de .

### <a name="a-prosaic-form"></a>Une forme prosaïque

[**L’échantillon EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm) [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) définit un modèle de vue, dont une instance devient la `BindingContext` `TableView`. Chaque `Cell` dérivé `TableSection` dans son peut alors avoir `PersonalInformation` des liaisons aux propriétés de la classe.

### <a name="custom-cells"></a>Cellules personnalisées

[**L’échantillon ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells) s’étend sur **EntryForm**. La [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) classe comprend une propriété Boolean qui régit l’applicabilité de deux propriétés supplémentaires. Pour ces deux propriétés supplémentaires, `PickerCell` le programme utilise une coutume basée sur un [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) et [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Bien `IsEnabled` que les `PickerCell` propriétés des deux éléments `ProgrammerInformation`soient liées à la propriété Boolean en , cette technique ne semble pas fonctionner, ce qui invite l’échantillon suivant.

### <a name="conditional-sections"></a>Sections conditionnelles

[**L’échantillon de conditionnels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection) met les deux éléments qui sont conditionnels à la sélection de l’article Boolean dans une séparation `TableSection`. Le fichier de code-derrière supprime `TableView` cette section de la ou l’ajoute en arrière en fonction de la propriété Boolean.

### <a name="a-tableview-menu"></a>Un menu TableView

Une autre `TableView` utilisation d’un est un menu. [**L’échantillon MenuCommands**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands) montre un menu qui `BoxView` vous permet de vous déplacer un peu autour de l’écran.

## <a name="related-links"></a>Liens connexes

- [Chapitre 19 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [Échantillons du chapitre 19](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Sélecteur](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView (en)](~/xamarin-forms/user-interface/tableview.md)
